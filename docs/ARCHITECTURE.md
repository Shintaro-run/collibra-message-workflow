# Architecture Documentation - Collibra Message Workflow

This document provides comprehensive technical details about the Message Workflow architecture, design decisions, and implementation details.

## Table of Contents
- [Architecture Overview](#architecture-overview)
- [BPMN Structure](#bpmn-structure)
- [Workflow States and Transitions](#workflow-states-and-transitions)
- [Message Flow Sequence](#message-flow-sequence)
- [Technical Components](#technical-components)
- [Data Model](#data-model)
- [Integration Points](#integration-points)
- [Design Decisions and Rationale](#design-decisions-and-rationale)
- [Error Handling](#error-handling)
- [Performance Considerations](#performance-considerations)

## Architecture Overview

### System Context

The Message Workflow operates within the Collibra Data Intelligence Platform, leveraging the Flowable BPMN engine and Collibra's workflow framework.

```
┌─────────────────────────────────────────────────────────────┐
│                    Collibra Platform                        │
│  ┌───────────────────────────────────────────────────────┐  │
│  │              Flowable Workflow Engine                  │  │
│  │  ┌─────────────────────────────────────────────────┐  │  │
│  │  │         Message Workflow (BPMN Process)         │  │  │
│  │  │                                                 │  │  │
│  │  │  [Start] → [Service Tasks] → [User Tasks]     │  │  │
│  │  │     ↓          ↓                ↓              │  │  │
│  │  │  [Gateways] ← [Loop Back] ← [End]            │  │  │
│  │  └─────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────┘  │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐    │
│  │   Comments   │  │    Email     │  │    Users     │    │
│  │   Service    │  │   Service    │  │   & Groups   │    │
│  └──────────────┘  └──────────────┘  └──────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

### Design Principles

1. **Bidirectional Communication**: Full loop-back pattern for ongoing conversations
2. **Asynchronous Messaging**: Non-blocking message exchange via user tasks
3. **State Management**: Explicit workflow status tracking (New/InProgress/Closed)
4. **Scalability**: Stateless service tasks, minimal shared state
5. **Auditability**: Complete conversation history preserved
6. **Flexibility**: Configuration-driven behavior without code changes

### Key Design Patterns

- **Loop-Back Pattern**: Messages cycle between sender and receiver
- **Escalation Pattern**: Automatic and manual escalation support
- **Gateway Pattern**: Conditional routing based on user actions
- **Delegate Pattern**: Reusable service task delegates
- **Observer Pattern**: Email notifications on state changes

## BPMN Structure

### Process Hierarchy

```
messageWorkflow (Process)
├── Configuration Variables (5)
├── Start Event (1)
├── Service Tasks (15)
├── User Tasks (3)
├── Exclusive Gateways (7)
├── Boundary Event (1)
└── End Event (1)
```

### Visual Flow Diagram

```
                    [Start: User Posts Question]
                              |
                              v
                    [Initialize Workflow]
                       (Status = New)
                              |
                              v
                   [Asset Provided? Gateway]
                      /              \
                   Yes                No
                    /                  \
                   v                    v
        [Save Initial Comment]    [Skip Comment]
                   |                    |
                   +--------------------+
                              |
                              v
                   [Notify Receiver Group]
                       (Send Email)
                              |
                              v
                   [Receiver Task: Review]
                   (Boundary: Timer → Auto-Escalate)
                              |
                              v
                   [Receiver Action Gateway]
                      /       |        \
                 Reply    Escalate    Close
                   /         |          \
                  v          v           v
    [Update Status]  [Manual Escalation]  [Close Path]
     (InProgress)     → [Escalated Task]   (Status=Closed)
          |                   |                  |
          v                   v                  v
    [Save Reply]       [Council Reply]    [Save Closure]
          |                   |                  |
          v                   +------------------+
    [Notify Sender]                              |
          |                                      v
          v                           [Notify Sender: Closed]
    [Sender Task: Reply]                         |
          |                                      v
          v                                  [End Event]
    [Sender Action Gateway]
      /              \
   Reply          Accept Close
    /                  \
   v                    v
[Save Sender Reply]  [Close Path]
   |
   v
[Notify Receiver]
   |
   v
[Loop back to Receiver Task] ←─┐
                                │
                                │ (Bidirectional
                                │  Message Loop)
                                │
                                └─────────────┘
```

### Component Breakdown

#### 1. Start Event
**ID:** `startEvent`
**Type:** None Start Event
**Purpose:** Workflow initiation point

**Form Properties:**
- `title` (string, required)
- `messageBody` (textarea, required)
- `priority` (enum: High/Medium/Low, required)
- `category` (enum: Question/IssueReport/Other, required)
- `relatedAssetId` (asset picker, optional)

**Validation:**
- Title: Non-empty, max 255 characters
- Message: Non-empty, max 4000 characters
- Priority: Must be one of enum values
- Category: Must be one of enum values

#### 2. Service Tasks

**Initialize Workflow** (`initializeWorkflow`)
- **Delegate:** `initializeWorkflowDelegate`
- **Purpose:** Set initial workflow status
- **Output:** `workflowStatus = "New"`

**Save Initial Comment** (`saveInitialComment`)
- **Delegate:** `addCommentDelegate`
- **Purpose:** Save initial message as asset comment
- **Condition:** Only if `relatedAssetId` provided
- **Fields:**
  - `resourceId`: Asset to comment on
  - `content`: Formatted message with metadata

**Notify Receiver** (`notifyReceiver`)
- **Delegate:** `mailSenderDelegate`
- **Purpose:** Send email to receiver group
- **Fields:**
  - `to`: Group members' emails
  - `subject`: Formatted subject with priority
  - `body`: Full message with context and link

**Update Status In Progress** (`updateStatusInProgress`)
- **Delegate:** `scriptDelegate`
- **Purpose:** Change workflow status to InProgress
- **Script:** `execution.setVariable("workflowStatus", "InProgress");`

**Save Receiver Reply** (`saveReceiverReply`)
- **Delegate:** `addCommentDelegate`
- **Purpose:** Save receiver's reply as comment
- **Condition:** Only if `relatedAssetId` provided

**Notify Sender Reply** (`notifySenderReply`)
- **Delegate:** `mailSenderDelegate`
- **Purpose:** Email sender about receiver's reply

**Save Sender Reply** (`saveSenderReply`)
- **Delegate:** `addCommentDelegate`
- **Purpose:** Save sender's follow-up as comment

**Notify Receiver Sender Reply** (`notifyReceiverSenderReply`)
- **Delegate:** `mailSenderDelegate`
- **Purpose:** Email receiver about sender's follow-up

**Handle Manual Escalation** (`handleManualEscalation`)
- **Delegate:** `mailSenderDelegate`
- **Purpose:** Email escalation group with reason

**Auto Escalate** (`autoEscalate`)
- **Delegate:** `mailSenderDelegate`
- **Purpose:** Email escalation group on timeout

**Update Status Closed** (`updateStatusClosed`)
- **Delegate:** `scriptDelegate`
- **Purpose:** Change workflow status to Closed
- **Script:** `execution.setVariable("workflowStatus", "Closed");`

**Save Closure Notes** (`saveClosureNotes`)
- **Delegate:** `addCommentDelegate`
- **Purpose:** Save closure message as comment

**Notify Sender Closure** (`notifySenderClosure`)
- **Delegate:** `mailSenderDelegate`
- **Purpose:** Email sender about workflow closure

#### 3. User Tasks

**Receiver Task** (`receiverTask`)
- **Name:** "Review and Respond"
- **Assignee:** `${group(recipientGroup)}`
- **Form Properties:**
  - `conversationHistory` (textarea, read-only)
  - `originalTitle` (string, read-only)
  - `originalPriority` (string, read-only)
  - `originalCategory` (string, read-only)
  - `originalSender` (string, read-only)
  - `receiverAction` (enum: Reply/Escalate/Close, required)
  - `receiverMessage` (textarea, conditional)
  - `escalationReason` (textarea, conditional)
  - `closureNotes` (textarea, conditional)

**Sender Reply Task** (`senderReplyTask`)
- **Name:** "Review Response"
- **Assignee:** `${startUser.userName}`
- **Form Properties:**
  - `conversationHistory` (textarea, read-only)
  - `receiverLastMessage` (textarea, read-only)
  - `senderAction` (enum: Reply/AcceptClose, required)
  - `senderMessage` (textarea, conditional)

**Escalated Task** (`escalatedTask`)
- **Name:** "Handle Escalated Issue"
- **Assignee:** `${group(escalationGroup)}`
- **Form Properties:**
  - `conversationHistory` (textarea, read-only)
  - `originalTitle` (string, read-only)
  - `originalPriority` (string, read-only)
  - `escalatedMessage` (textarea, required)
  - `escalatedAction` (enum: Reply/Close, required)

#### 4. Exclusive Gateways

**Check Asset Provided** (`checkAssetProvided`)
- **Purpose:** Route to comment saving if asset linked
- **Condition:** `${relatedAssetId != null && relatedAssetId != ''}`

**Receiver Action Gateway** (`receiverActionGateway`)
- **Purpose:** Route based on receiver's chosen action
- **Paths:**
  - Reply: `${receiverAction == 'Reply'}`
  - Escalate: `${receiverAction == 'Escalate'}`
  - Close: `${receiverAction == 'Close'}`

**Sender Action Gateway** (`senderActionGateway`)
- **Purpose:** Route based on sender's chosen action
- **Paths:**
  - Reply: `${senderAction == 'Reply'}`
  - Accept Close: `${senderAction == 'AcceptClose'}`

**Escalated Action Gateway** (`escalatedActionGateway`)
- **Purpose:** Route based on escalation handler's action
- **Paths:**
  - Reply: `${escalatedAction == 'Reply'}`
  - Close: `${escalatedAction == 'Close'}`

**Check Asset For Reply** (`checkAssetForReply`)
- **Purpose:** Conditional comment saving on reply

**Check Asset For Sender Reply** (`checkAssetForSenderReply`)
- **Purpose:** Conditional comment saving on sender reply

**Check Asset For Closure** (`checkAssetForClosure`)
- **Purpose:** Conditional comment saving on closure

#### 5. Boundary Event

**Escalation Timer** (`escalationTimer`)
- **Type:** Timer Boundary Event (Interrupting)
- **Attached To:** `receiverTask`
- **Duration:** `${escalationDuration}` (default: P7D)
- **Action:** Triggers auto-escalation to escalation group
- **Cancels:** Receiver task (user loses ability to respond)

#### 6. End Event

**End Event** (`endEvent`)
- **Type:** None End Event
- **Purpose:** Workflow completion point
- **Reached By:**
  - Normal closure after sender accepts
  - Receiver closes directly
  - Escalation handler closes

## Workflow States and Transitions

### State Diagram

```
┌──────────────────┐
│                  │
│       NEW        │  Initial state
│                  │
└────────┬─────────┘
         │
         │ Receiver replies OR
         │ Escalation occurs
         v
┌──────────────────┐
│                  │
│   IN PROGRESS    │  Active conversation
│                  │
└────────┬─────────┘
         │
         │ Receiver closes OR
         │ Sender accepts close OR
         │ Escalation handler closes
         v
┌──────────────────┐
│                  │
│     CLOSED       │  Terminal state
│                  │
└──────────────────┘
```

### State Transition Rules

| Current State | Trigger | Next State | Actor |
|---------------|---------|------------|-------|
| NEW | Receiver replies | IN PROGRESS | Receiver |
| NEW | Manual escalation | IN PROGRESS | Receiver |
| NEW | Auto-escalation | IN PROGRESS | System |
| NEW | Receiver closes | CLOSED | Receiver |
| IN PROGRESS | Sender replies | IN PROGRESS | Sender |
| IN PROGRESS | Receiver replies | IN PROGRESS | Receiver |
| IN PROGRESS | Receiver closes | CLOSED | Receiver |
| IN PROGRESS | Sender accepts close | CLOSED | Sender |
| IN PROGRESS | Escalated handler closes | CLOSED | Escalation Group |
| CLOSED | - | - | (Terminal) |

### Variable Tracking

**Workflow Variables:**
```javascript
{
  // User input (immutable)
  title: "How do I update attributes?",
  messageBody: "I need to update...",
  priority: "High",
  category: "Question",
  relatedAssetId: "asset-uuid-123",

  // Workflow state
  workflowStatus: "New" | "InProgress" | "Closed",

  // Conversation tracking
  conversationHistory: "Full message thread...",

  // User actions
  receiverAction: "Reply" | "Escalate" | "Close",
  receiverMessage: "Here's the answer...",
  senderAction: "Reply" | "AcceptClose",
  senderMessage: "Thanks, follow-up question...",
  escalationReason: "Requires policy decision",
  closureNotes: "Issue resolved",

  // Configuration
  recipientGroup: "Data_Stewards",
  escalationGroup: "DG_Council",
  escalationDuration: "P7D",
  emailNotificationEnabled: true,
  mailSubjectPrefix: "[Collibra Q&A]",

  // System variables
  startUser: { firstName, lastName, userName, email },
  currentUser: { firstName, lastName, userName, email }
}
```

## Message Flow Sequence

### Sequence 1: Simple Question and Answer

```
1. User starts workflow
   → POST /workflows/start/messageWorkflow
   → Variables: {title, messageBody, priority, category}

2. Initialize workflow
   → SET workflowStatus = "New"

3. Save comment (if asset provided)
   → POST /assets/{id}/comments
   → Body: "[User] [timestamp]: [Question] {title}\n{messageBody}"

4. Send email to receivers
   → POST /mail/send
   → To: group(recipientGroup)
   → Subject: "[Collibra Q&A] [High] {title}"

5. Create receiver task
   → POST /tasks/create
   → Assignee: group(recipientGroup)

6. [Receiver opens task]
   → GET /tasks/{taskId}

7. Receiver submits reply
   → POST /tasks/{taskId}/complete
   → Variables: {receiverAction: "Reply", receiverMessage: "Answer..."}

8. Update status
   → SET workflowStatus = "InProgress"

9. Save reply comment
   → POST /assets/{id}/comments

10. Send email to sender
    → POST /mail/send

11. Create sender task
    → POST /tasks/create
    → Assignee: startUser

12. [Sender opens task]
    → GET /tasks/{taskId}

13. Sender accepts close
    → POST /tasks/{taskId}/complete
    → Variables: {senderAction: "AcceptClose"}

14. Update status
    → SET workflowStatus = "Closed"

15. Send closure email
    → POST /mail/send

16. End workflow
    → Workflow instance complete
```

### Sequence 2: Multi-Round Conversation

```
Steps 1-13 same as above, but step 13:

13. Sender replies with follow-up
    → POST /tasks/{taskId}/complete
    → Variables: {senderAction: "Reply", senderMessage: "Follow-up..."}

14. Save sender reply comment
    → POST /assets/{id}/comments

15. Send email to receiver
    → POST /mail/send

16. Create receiver task (loop back)
    → POST /tasks/create
    → Assignee: group(recipientGroup)

17. [Cycle repeats from step 6]
    [Can continue indefinitely until closure]
```

### Sequence 3: Auto-Escalation

```
Steps 1-5 same as Sequence 1

6. [Receiver does NOT open task within escalationDuration]

7. Timer boundary event fires
   → After P7D (default)

8. Cancel receiver task
   → DELETE /tasks/{taskId}

9. Send escalation email
   → POST /mail/send
   → To: group(escalationGroup)
   → Subject: "[Collibra Q&A] [ESCALATED] {title}"

10. Create escalated task
    → POST /tasks/create
    → Assignee: group(escalationGroup)

11. [Escalation handler opens task]
    → GET /tasks/{taskId}

12. Handler submits response
    → POST /tasks/{taskId}/complete
    → Variables: {escalatedAction: "Reply", escalatedMessage: "..."}

13. Update status
    → SET workflowStatus = "InProgress"

14. Send email to sender
    → POST /mail/send

15. [Continue as normal conversation]
```

## Technical Components

### Collibra Delegates

#### AddComment Delegate
**Purpose:** Save messages as asset comments
**Interface:**
```java
public interface AddCommentDelegate extends JavaDelegate {
  void execute(DelegateExecution execution);
}
```

**Input Parameters:**
- `resourceId` (String): Asset UUID
- `content` (String): Comment text

**Behavior:**
- Creates comment on specified asset
- Timestamp automatically added
- User attribution from currentUser
- Visible in asset comment history

**Error Handling:**
- Asset not found: Log warning, continue workflow
- Permission denied: Log error, continue workflow
- Network error: Retry (if configured), continue

#### MailSender Delegate
**Purpose:** Send email notifications
**Interface:**
```java
public interface MailSenderDelegate extends JavaDelegate {
  void execute(DelegateExecution execution);
}
```

**Input Parameters:**
- `to` (String): Recipient email(s), can be expression
- `subject` (String): Email subject, can include variables
- `body` (String): Email body, can include variables

**Behavior:**
- Resolves expressions (e.g., `${group(groupName)}`)
- Sends email via configured SMTP server
- Asynchronous (non-blocking)
- Retry on failure (if configured)

**Error Handling:**
- Email server unavailable: Log error, continue
- Invalid recipient: Log warning, continue
- Quota exceeded: Log error, continue

#### Script Delegate
**Purpose:** Execute inline JavaScript for variable manipulation
**Interface:**
```java
public interface ScriptDelegate extends JavaDelegate {
  void execute(DelegateExecution execution);
}
```

**Input Parameters:**
- `script` (String): JavaScript code
- `language` (String): "javascript"

**Behavior:**
- Executes script in sandboxed environment
- Access to `execution` object
- Can set/get workflow variables

**Example:**
```javascript
execution.setVariable("workflowStatus", "InProgress");
```

### Expression Language

**Flowable uses Unified Expression Language (UEL)**

**Variable Access:**
```
${variableName}
${startUser.firstName}
${group(recipientGroup)}
```

**Conditional Expressions:**
```
${priority == 'High' ? 'PT8H' : 'P7D'}
${relatedAssetId != null && relatedAssetId != ''}
${receiverAction == 'Reply'}
```

**Function Calls:**
```
${now()}
${group(groupName)}
${user(userName)}
```

### Task Assignment

**Group Assignment:**
```xml
<userTask id="receiverTask" flowable:assignee="${group(recipientGroup)}">
```

**Behavior:**
- Task appears in task list for all group members
- Any member can claim and complete
- First to claim "locks" task to themselves

**Individual Assignment:**
```xml
<userTask id="senderReplyTask" flowable:assignee="${startUser.userName}">
```

**Behavior:**
- Task assigned to specific user
- Only that user can complete
- Appears only in their task list

## Data Model

### Workflow Instance

```json
{
  "processDefinitionId": "messageWorkflow:1:12345",
  "processInstanceId": "67890",
  "businessKey": null,
  "startTime": "2024-05-15T10:30:00Z",
  "endTime": null,
  "startUserId": "user123",
  "variables": {
    "title": "How do I update attributes?",
    "messageBody": "I need to update...",
    "priority": "High",
    "category": "Question",
    "relatedAssetId": "asset-uuid-123",
    "workflowStatus": "InProgress",
    "recipientGroup": "Data_Stewards",
    "escalationGroup": "DG_Council",
    "escalationDuration": "P7D",
    "emailNotificationEnabled": true,
    "mailSubjectPrefix": "[Collibra Q&A]"
  }
}
```

### Task Instance

```json
{
  "id": "task-98765",
  "name": "Review and Respond",
  "processInstanceId": "67890",
  "assignee": null,
  "candidateGroups": ["Data_Stewards"],
  "createTime": "2024-05-15T10:30:05Z",
  "dueDate": null,
  "formKey": null,
  "taskVariables": {
    "conversationHistory": "...",
    "originalTitle": "How do I update attributes?",
    "originalPriority": "High"
  }
}
```

### Comment (Asset)

```json
{
  "id": "comment-11111",
  "resourceId": "asset-uuid-123",
  "content": "John Doe (jdoe) - 2024-05-15T10:30:00Z: [Question] How do I update attributes?\nI need to update...",
  "createdBy": "user123",
  "createdOn": "2024-05-15T10:30:00Z"
}
```

## Integration Points

### Collibra APIs Used

1. **Workflow API**
   - Start workflow
   - Complete task
   - Query workflow instances

2. **Comment API**
   - Add comment to asset
   - View comment history

3. **Email API**
   - Send email notification
   - Resolve group emails

4. **User/Group API**
   - Resolve group members
   - Get user details

### External System Integration

**Potential Integrations:**
- **Ticketing Systems** (Jira, ServiceNow): Create ticket on escalation
- **Analytics Platforms**: Track metrics on workflow usage
- **Monitoring Systems**: Alert on SLA breaches
- **Chat Platforms** (Slack, Teams): Notify on new messages

**Integration Methods:**
- REST API calls from service tasks
- Webhook notifications
- Custom delegates
- Event listeners

## Design Decisions and Rationale

### Decision 1: Loop-Back vs. Sub-Process for Conversations

**Options Considered:**
A. Loop-back: Sender task → Receiver task → Sender task (chosen)
B. Sub-process: Spawn new conversation sub-process for each reply
C. Multi-instance: Parallel sender/receiver tasks

**Decision:** Loop-back pattern (Option A)

**Rationale:**
- Simpler BPMN structure
- Single workflow instance = single conversation
- Easier to track conversation history
- Lower complexity in debugging

**Trade-offs:**
- Potentially long-running workflow instances
- More sequence flows in BPMN

### Decision 2: Workflow Status Separate from Asset Status

**Options Considered:**
A. Workflow status independent of asset status (chosen)
B. Workflow status updates asset status
C. Asset status drives workflow status

**Decision:** Independent statuses (Option A)

**Rationale:**
- Workflow about conversation, not asset lifecycle
- Multiple workflows can discuss same asset
- Asset may not always be provided
- Clear separation of concerns

**Trade-offs:**
- Users must understand two status concepts
- Cannot infer asset status from workflow status

### Decision 3: Conditional Comment Saving

**Options Considered:**
A. Conditional based on relatedAssetId (chosen)
B. Always require asset, always save comments
C. Never save comments, use workflow variables only

**Decision:** Conditional comment saving (Option A)

**Rationale:**
- Flexibility: Not all questions relate to specific assets
- Audit trail: Comments visible in asset context when relevant
- Optional feature: Users can skip if not needed

**Trade-offs:**
- Additional gateways in BPMN (complexity)
- Conversation history may be split (workflow + asset)

### Decision 4: Single Escalation Level vs. Multi-Level

**Options Considered:**
A. Single escalation level (chosen)
B. Multi-level escalation (Tier 1 → Tier 2 → Tier 3)

**Decision:** Single escalation level (Option A)

**Rationale:**
- Simpler implementation
- Most organizations have two-tier support
- Can deploy multiple workflow instances for multi-tier
- Reduces BPMN complexity

**Trade-offs:**
- Organizations with >2 tiers need multiple workflows
- No built-in "escalate again" feature

### Decision 5: Email Notifications Always On vs. Optional

**Options Considered:**
A. Optional via configuration variable (chosen)
B. Always enabled
C. Per-user preference

**Decision:** Optional configuration (Option A)

**Rationale:**
- Flexibility for testing environments
- Support for organizations without email
- Can disable temporarily for troubleshooting

**Trade-offs:**
- Users may disable and forget to re-enable
- Testing without emails may miss issues

## Error Handling

### Service Task Failures

**Behavior:**
- Service tasks are fault-tolerant
- Failures logged but don't block workflow
- Workflow continues to next step

**Example:**
```
AddComment fails (asset not found)
  → Log warning
  → Continue to Notify Receiver
```

**Rationale:**
- Email notification more critical than comment
- Don't block conversation due to asset issues
- Users can manually add comments later

### Email Failures

**Behavior:**
- Email send failures logged
- Workflow continues
- Users can still access tasks via Collibra UI

**Mitigation:**
- Users check tasks regularly in UI
- Email is notification, not requirement
- Escalation provides backup mechanism

### User Task Timeout

**Behavior:**
- Auto-escalation after configured duration
- Original task cancelled
- New task created for escalation group

**Edge Case:**
- Receiver completes task right as timer fires
- Flowable handles race condition (timer wins or task wins)
- If timer wins: Escalation occurs, receiver completion ignored
- If task wins: Timer cancelled, no escalation

### Gateway Condition Errors

**Behavior:**
- Invalid expressions cause workflow error
- Workflow enters error state
- Admin intervention required

**Prevention:**
- Test all gateway conditions
- Validate expressions during deployment
- Use simple boolean expressions

## Performance Considerations

### Scalability

**Concurrent Workflows:**
- Design supports hundreds of concurrent instances
- Each instance independent
- No shared state between instances

**Database Impact:**
- Each workflow instance creates DB records
- Long-running workflows accumulate task history
- Periodic cleanup recommended (archive old workflows)

**Recommendations:**
- Archive workflows closed >90 days ago
- Monitor workflow instance count
- Set reasonable escalation durations (not too short)

### Response Time

**Task Completion:**
- Synchronous: User task completion immediate
- Asynchronous: Service tasks (email, comment) may lag

**Optimization:**
- Service tasks run asynchronously
- Don't block user task completion
- Email sends in background

### Resource Usage

**Memory:**
- Each workflow instance: ~10-50 KB
- Conversation history stored in variables
- Large messages increase memory footprint

**Network:**
- Email notifications: 1-5 KB per email
- Comment API calls: 1-10 KB per call
- Minimal network impact

**Recommendations:**
- Limit message body size (4000 characters)
- Encourage concise messages
- Consider archiving long conversations

---

## Summary

The Message Workflow architecture provides a robust, scalable, and flexible solution for bidirectional messaging within Collibra. Key architectural strengths:

1. **Bidirectional Loop**: Natural conversation flow
2. **State Management**: Clear workflow status tracking
3. **Escalation Support**: Automatic and manual escalation paths
4. **Audit Trail**: Complete history in comments and workflow
5. **Configuration-Driven**: Behavior customizable without code changes
6. **Fault Tolerant**: Failures don't block conversations
7. **Scalable**: Supports many concurrent conversations

This architecture balances simplicity, flexibility, and robustness for enterprise data governance communication needs.

---

**For more information:**
- [INSTALLATION.md](INSTALLATION.md) - Setup instructions
- [CONFIGURATION.md](CONFIGURATION.md) - Configuration details
- [configuration-examples.md](../examples/configuration-examples.md) - Configuration scenarios
- [usage-scenarios.md](../examples/usage-scenarios.md) - Usage examples
