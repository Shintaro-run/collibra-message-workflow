# Collibra Message Workflow

A comprehensive bidirectional messaging workflow for Collibra Data Intelligence Platform that enables users to post questions and issues about assets/domains with full conversation support.

## Overview

The Message Workflow provides a structured communication channel between users and designated receiver groups within Collibra. It supports full bidirectional messaging, automatic escalation, and complete conversation history tracking.

## Features

### Core Functionality
- **User-Initiated Questions**: Any logged-in user can post questions or issues
- **Bidirectional Messaging**: Full conversation support with loop-back pattern
- **Multiple Categories**: Question, Issue Report, or Other
- **Priority Levels**: High, Medium, Low
- **Asset Association**: Optional linking to specific Collibra assets
- **Conversation History**: Complete message thread tracking
- **Comment Integration**: All messages saved as asset comments (if asset provided)

### Workflow Management
- **Status Tracking**: New → In Progress → Closed
- **Receiver Actions**: Reply, Escalate, or Close
- **Sender Actions**: Reply or Accept Close
- **Auto-Escalation**: Automatic escalation after configurable timeout
- **Manual Escalation**: Receiver can escalate to higher-level group

### Notifications
- **Email Alerts**: Automatic notifications for all participants
- **Configurable Templates**: Customizable email subjects and content
- **Task Links**: Direct links to pending tasks in emails
- **Status Updates**: Notifications on workflow state changes

## Quick Start

### Prerequisites
- Collibra DGC version 2024.05 or higher
- Administrator access to Collibra Settings
- At least one user group configured as receiver
- Email server configured in Collibra (for notifications)

### Installation Steps

1. **Download the workflow file**
   ```
   workflow/message-workflow.bpmn
   ```

2. **Upload to Collibra**
   - Navigate to: Settings → Workflows → Definitions
   - Click "Upload Workflow"
   - Select `message-workflow.bpmn`
   - Click "Upload"

3. **Configure the workflow**
   - Find "Message Workflow" in the definitions list
   - Click "Edit Configuration"
   - Set `recipientGroup` to your receiver group name
   - Adjust other settings as needed (see Configuration section)

4. **Enable the workflow**
   - Navigate to: Settings → Workflows → Start Workflow
   - Find "Message Workflow"
   - Configure start permissions (who can initiate)
   - Enable the workflow

For detailed installation instructions, see [INSTALLATION.md](docs/INSTALLATION.md).

## Usage

### Starting a Workflow

1. Navigate to any Collibra page or asset
2. Click "Start Workflow" (or workflow menu)
3. Select "Message Workflow"
4. Fill in the form:
   - **Title**: Brief summary of your question/issue
   - **Message**: Detailed description
   - **Priority**: High, Medium, or Low
   - **Category**: Question, Issue Report, or Other
   - **Related Asset**: (Optional) Select associated asset
5. Click "Submit"

### Receiver Workflow

When a message is received:
1. Receiver group members get email notification
2. Open task from email link or Collibra task list
3. Review conversation history and original message
4. Choose action:
   - **Reply**: Send response back to sender
   - **Escalate**: Forward to higher-level group
   - **Close**: Complete the workflow
5. Provide message/notes based on chosen action

### Sender Response

When receiver replies:
1. Original sender receives email notification
2. Open task to view receiver's response
3. Choose action:
   - **Reply**: Send follow-up message (continues conversation)
   - **Accept and Close**: Close the workflow

### Escalation

**Automatic Escalation:**
- Triggers after 7 days (default) of no response
- Escalates to Data Governance Council (configurable)

**Manual Escalation:**
- Receiver can escalate at any time
- Requires escalation reason
- Notifies escalation group

## Workflow Status States

```
┌─────────────┐
│     New     │  Initial state when question posted
└──────┬──────┘
       │
       │ Receiver replies or starts handling
       v
┌─────────────┐
│ In Progress │  Active conversation ongoing
└──────┬──────┘
       │
       │ Receiver or sender closes workflow
       v
┌─────────────┐
│   Closed    │  Workflow completed
└─────────────┘
```

**Note:** Workflow status does NOT affect asset status. It only tracks the message workflow state.

## Architecture

### High-Level Flow

```
[User Posts Question]
        |
        v
[Notify Receiver Group]
        |
        v
[Receiver Reviews]
        |
        +----> [Reply] -----> [Sender Responds] --+
        |                                          |
        +----> [Escalate] --> [Higher Group] -----+
        |                                          |
        +----> [Close] --------------------------> [End]
        |                                          |
        +------ [Loop back after sender reply] <--+
```

### Key Components

- **Start Event**: User input form with validation
- **Service Tasks**: Comment saving, email notifications, status updates
- **User Tasks**: Receiver review, sender response, escalated handling
- **Gateways**: Action routing, conditional comment saving
- **Boundary Event**: Auto-escalation timer
- **End Event**: Workflow completion

For detailed architecture documentation, see [ARCHITECTURE.md](docs/ARCHITECTURE.md).

## Configuration

### Default Configuration Variables

| Variable | Default Value | Description |
|----------|---------------|-------------|
| `recipientGroup` | JP_Test_Group_For_Temporary | Primary receiver group |
| `emailNotificationEnabled` | true | Enable/disable email notifications |
| `escalationDuration` | P7D | Auto-escalation timeout (7 days) |
| `mailSubjectPrefix` | [Collibra Q&A] | Email subject line prefix |
| `escalationGroup` | Data_Governance_Council | Escalation target group |

### Customization

All configuration variables can be modified in:
**Settings → Workflows → Definitions → Message Workflow → Edit Configuration**

For detailed configuration guide, see [CONFIGURATION.md](docs/CONFIGURATION.md).

## Examples

### Example 1: Simple Question
```
Title: "How do I update asset attributes?"
Category: Question
Priority: Medium
Message: "I need to update multiple attributes for a data asset..."
Related Asset: (selected from picker)
```

### Example 2: Issue Report
```
Title: "Data quality issue in Customer table"
Category: Issue Report
Priority: High
Message: "Discovered null values in required email field..."
Related Asset: Customer Table
```

### Example 3: Escalated Scenario
```
1. User posts question (Priority: High)
2. Receiver doesn't respond within 7 days
3. Auto-escalation triggers
4. Data Governance Council receives notification
5. Council member reviews and responds
6. Conversation continues with sender
7. Council closes workflow when resolved
```

For more examples, see [examples/usage-scenarios.md](examples/usage-scenarios.md).

## Troubleshooting

### Common Issues

**Issue: Receiver group not receiving notifications**
- Solution: Verify email server configuration in Collibra
- Check that `recipientGroup` variable matches actual group name
- Ensure group members have valid email addresses

**Issue: Comments not appearing on assets**
- Solution: Verify user has permission to add comments to asset
- Check that `relatedAssetId` is valid
- Review logs for AddComment delegate errors

**Issue: Workflow not appearing in start menu**
- Solution: Verify workflow is enabled in Settings
- Check start permissions configuration
- Ensure user has permission to start workflows

**Issue: Auto-escalation not triggering**
- Solution: Verify `escalationDuration` format (ISO 8601 duration)
- Check that escalation group exists and is valid
- Review workflow engine logs

### Getting Help

For additional support:
1. Check [INSTALLATION.md](docs/INSTALLATION.md) for setup issues
2. Review [CONFIGURATION.md](docs/CONFIGURATION.md) for configuration problems
3. Consult [ARCHITECTURE.md](docs/ARCHITECTURE.md) for technical details
4. Contact your Collibra administrator

## Technical Details

### BPMN Elements Used
- Start Event with form properties
- Service Tasks (AddComment, MailSender, Script delegates)
- User Tasks with dynamic assignees
- Exclusive Gateways for routing
- Boundary Timer Event for escalation
- Sequence Flows with conditions

### Collibra Delegates
- `addCommentDelegate`: Save messages as asset comments
- `mailSenderDelegate`: Send email notifications
- `scriptDelegate`: Update workflow variables

### Variable Expressions
- `${startUser}`: Workflow initiator
- `${currentUser}`: Current task assignee
- `${group(groupName)}`: User group reference
- `${now()}`: Current timestamp

### Standards Compliance
- BPMN 2.0 specification
- Flowable workflow engine
- Collibra workflow namespace: `http://collibra.com/workflow`

## Contributing

### Reporting Issues
If you encounter bugs or have feature requests:
1. Document the issue with detailed steps to reproduce
2. Include Collibra version and workflow configuration
3. Provide relevant error messages or logs
4. Submit through your organization's Collibra support channel

### Suggesting Improvements
We welcome suggestions for:
- Additional workflow features
- Enhanced notification templates
- Improved user interface elements
- Integration with other Collibra workflows

## Versioning

This workflow follows semantic versioning:
- **MAJOR**: Breaking changes to workflow structure
- **MINOR**: New features and functionality
- **PATCH**: Bug fixes and minor improvements

Current Version: 1.0.0

## License

This workflow is provided as-is for use with Collibra Data Intelligence Platform. Usage is subject to your organization's Collibra license agreement.

## Acknowledgments

Designed for Collibra Data Intelligence Platform version 2024.05+, utilizing the Flowable BPMN engine and Collibra's workflow framework.

---

## File Structure

```
collibra-message-workflow/
├── README.md                           # This file
├── .gitignore                          # Git exclusions
├── workflow/
│   └── message-workflow.bpmn          # Main workflow definition
├── docs/
│   ├── INSTALLATION.md                # Detailed installation guide
│   ├── CONFIGURATION.md               # Configuration reference
│   └── ARCHITECTURE.md                # Technical architecture
└── examples/
    ├── configuration-examples.md      # Configuration examples
    └── usage-scenarios.md             # Usage scenarios and examples
```

## Next Steps

1. Read [INSTALLATION.md](docs/INSTALLATION.md) for detailed setup instructions
2. Review [CONFIGURATION.md](docs/CONFIGURATION.md) to customize settings
3. Explore [examples/usage-scenarios.md](examples/usage-scenarios.md) for use cases
4. Test the workflow in a development environment first
5. Deploy to production with appropriate receiver groups configured

---

**Need Help?** Consult the documentation in the `docs/` directory or contact your Collibra administrator.
