# Configuration Guide - Collibra Message Workflow

This guide provides comprehensive information about configuring the Message Workflow for your specific needs.

## Table of Contents
- [Configuration Overview](#configuration-overview)
- [Configuration Variables Reference](#configuration-variables-reference)
- [Email Template Customization](#email-template-customization)
- [Group Assignment Best Practices](#group-assignment-best-practices)
- [Advanced Configuration Scenarios](#advanced-configuration-scenarios)
- [Performance Tuning](#performance-tuning)
- [Security Considerations](#security-considerations)
- [Integration with Other Workflows](#integration-with-other-workflows)

## Configuration Overview

The Message Workflow uses configuration variables to customize behavior without modifying the BPMN definition. All variables are configurable through the Collibra web interface.

### Accessing Configuration

**Path:** Settings → Workflows → Definitions → Message Workflow → Edit Configuration

### Configuration Principles

1. **Default Values**: All variables have sensible defaults
2. **Non-Breaking**: Changes don't require workflow restart
3. **Instance-Level**: Active workflows use configuration from start time
4. **Validation**: Collibra validates values on save

## Configuration Variables Reference

### 1. recipientGroup

**Type:** String
**Default:** `JP_Test_Group_For_Temporary`
**Required:** Yes
**Description:** Primary user group that receives and handles incoming messages.

**Configuration:**
```
Variable Name: recipientGroup
Type: string
Value: [Your Group Name]
```

**Valid Values:**
- Any existing Collibra user group name
- Must be exact match (case-sensitive)
- Use underscore (_) not space
- Group must have at least one active member

**Examples:**
```
Data_Stewards
IT_Support_Team
Data_Governance_Team
Business_Analysts
Customer_Support
```

**Usage in Workflow:**
- Assigns tasks to this group
- Sends email notifications to group members
- Used in `${group(recipientGroup)}` expressions

**Best Practices:**
- Choose group with appropriate expertise
- Ensure group has 24/7 coverage if needed
- Size group appropriately (3-10 members ideal)
- Rotate responsibilities within group

**Common Errors:**
- `Group not found`: Group name mismatch or doesn't exist
- `No assignees`: Group has no active members
- `Invalid characters`: Space instead of underscore

---

### 2. emailNotificationEnabled

**Type:** Boolean
**Default:** `true`
**Required:** No
**Description:** Enable or disable email notifications for all participants.

**Configuration:**
```
Variable Name: emailNotificationEnabled
Type: boolean
Value: true | false
```

**Valid Values:**
- `true`: Enable email notifications (recommended)
- `false`: Disable email notifications

**When to Disable:**
- Testing environment
- Users prefer in-app notifications only
- Email server unavailable
- Troubleshooting email issues

**Impact When Disabled:**
- No email notifications sent
- Users must check Collibra tasks manually
- Reduces network traffic
- May decrease response time

**Best Practices:**
- Keep enabled in production
- Disable only temporarily for testing
- Document when disabled
- Re-enable after testing complete

---

### 3. escalationDuration

**Type:** String (ISO 8601 Duration)
**Default:** `P7D` (7 days)
**Required:** No
**Description:** Time period after which unanswered messages are automatically escalated.

**Configuration:**
```
Variable Name: escalationDuration
Type: string
Value: [ISO 8601 Duration]
```

**Format:** ISO 8601 Duration Standard
```
P[n]Y[n]M[n]DT[n]H[n]M[n]S

Where:
P = Period designator (required)
Y = Years
M = Months (after P)
D = Days
T = Time designator (required for time components)
H = Hours
M = Minutes (after T)
S = Seconds
```

**Common Values:**
```
P1D   = 1 day
P3D   = 3 days
P7D   = 7 days (default)
P14D  = 14 days
PT24H = 24 hours
PT8H  = 8 hours
PT1H  = 1 hour (testing only)
P1M   = 1 month (not recommended)
```

**Examples by Use Case:**

**Critical Support (Same-Day Response):**
```
escalationDuration: PT8H
```

**Standard Support (3-Day Response):**
```
escalationDuration: P3D
```

**Low Priority (2-Week Response):**
```
escalationDuration: P14D
```

**Testing (1 Hour):**
```
escalationDuration: PT1H
```

**Best Practices:**
- Align with SLA commitments
- Consider business hours vs. 24/7
- Shorter for high-priority workflows
- Longer for advisory/non-urgent questions
- Test with short duration first

**Common Errors:**
- `Invalid format`: Missing P prefix or incorrect syntax
- `Too short`: Less than 1 hour (may cause excessive escalations)
- `Too long`: More than 30 days (poor user experience)

---

### 4. mailSubjectPrefix

**Type:** String
**Default:** `[Collibra Q&A]`
**Required:** No
**Description:** Prefix added to all email subject lines for easy identification and filtering.

**Configuration:**
```
Variable Name: mailSubjectPrefix
Type: string
Value: [Your Prefix]
```

**Valid Values:**
- Any string up to 50 characters
- Typically enclosed in brackets []
- Should be distinctive and recognizable

**Examples:**
```
[Collibra Q&A]
[Data Governance]
[Support Request]
[DG Question]
[Asset Inquiry]
[Data Steward]
[Collibra Help]
```

**Email Subject Examples:**

**With default prefix:**
```
[Collibra Q&A] [High] How do I update asset attributes?
[Collibra Q&A] Reply to: Data quality issue
[Collibra Q&A] [ESCALATED] Customer table issues
[Collibra Q&A] Closed: How do I update asset attributes?
```

**With custom prefix:**
```
[Data Governance] [High] How do I update asset attributes?
[Support] Reply to: Data quality issue
```

**Best Practices:**
- Keep short and distinctive
- Use for email filtering rules
- Coordinate with email system admins
- Consider multi-language environments
- Avoid special characters that break email

**Email Filtering:**
Users can create email rules:
```
If subject contains "[Collibra Q&A]"
  Then: Move to folder "Collibra Workflows"
  And: Mark as important
```

---

### 5. escalationGroup

**Type:** String
**Default:** `Data_Governance_Council`
**Required:** Yes
**Description:** Higher-level user group that receives escalated messages.

**Configuration:**
```
Variable Name: escalationGroup
Type: string
Value: [Your Escalation Group]
```

**Valid Values:**
- Any existing Collibra user group name
- Must be exact match (case-sensitive)
- Should have higher authority than recipientGroup
- Group must have at least one active member

**Examples:**
```
Data_Governance_Council
Senior_Data_Stewards
IT_Management
Data_Leadership_Team
Chief_Data_Office
Executive_Committee
```

**Escalation Scenarios:**

**Automatic Escalation:**
- Triggered after escalationDuration passes
- No response from recipientGroup
- Notifies escalationGroup with full context

**Manual Escalation:**
- Receiver chooses "Escalate" action
- Provides escalation reason
- Immediately routes to escalationGroup

**Best Practices:**
- Choose group with decision-making authority
- Ensure group has bandwidth for escalations
- Different from recipientGroup (no overlap ideal)
- Monitor escalation patterns
- Adjust if too many/few escalations occur

**Escalation Hierarchy Example:**
```
Level 1: Data_Stewards (recipientGroup)
Level 2: Senior_Data_Stewards (escalationGroup)
Level 3: (manual process if still unresolved)
```

**Common Errors:**
- `Group not found`: Group name mismatch
- `Same as recipient`: No meaningful escalation
- `No authority`: Escalation group lacks decision power

---

## Email Template Customization

### Email Notification Points

The workflow sends emails at these points:

1. **New Message Notification** (to receiver)
2. **Reply Notification** (to sender)
3. **Escalation Notification** (to escalation group)
4. **Closure Notification** (to sender)

### Email Template Structure

Email templates are defined in the BPMN file using Flowable expressions. To customize:

**Option 1: Modify BPMN File (Advanced)**

Edit the `mailSenderDelegate` service tasks in the BPMN XML:

```xml
<flowable:field name="body">
  <flowable:expression>
    Your custom email body here...

    Variables available:
    ${startUser.firstName} ${startUser.lastName}
    ${title}
    ${priority}
    ${category}
    ${messageBody}
    ${taskLink}
  </flowable:expression>
</flowable:field>
```

**Option 2: Use Configuration Variables**

Create additional configuration variables for email templates:

```
emailBodyTemplate: Your template text...
emailFooter: Custom footer text...
```

### Available Template Variables

**User Variables:**
```
${startUser.firstName}      # Sender's first name
${startUser.lastName}       # Sender's last name
${startUser.userName}       # Sender's username
${startUser.email}          # Sender's email
${currentUser.firstName}    # Current task assignee's first name
${currentUser.lastName}     # Current task assignee's last name
```

**Workflow Variables:**
```
${title}                    # Message title
${messageBody}              # Original message content
${priority}                 # High/Medium/Low
${category}                 # Question/IssueReport/Other
${relatedAssetId}           # Asset ID (if provided)
${receiverMessage}          # Receiver's reply
${senderMessage}            # Sender's follow-up
${escalationReason}         # Reason for escalation
${closureNotes}             # Notes on closure
${conversationHistory}      # Full conversation thread
${workflowStatus}           # New/InProgress/Closed
```

**System Variables:**
```
${taskLink}                 # Link to task in Collibra
${now()}                    # Current timestamp
```

### Example Email Templates

**New Message Notification Template:**
```
Hello Data Steward,

A new ${category} has been posted by ${startUser.firstName} ${startUser.lastName}.

DETAILS:
Title: ${title}
Priority: ${priority}
Category: ${category}

MESSAGE:
${messageBody}

${relatedAssetId != null ? 'Related Asset: ' + relatedAssetId : 'No asset specified'}

ACTION REQUIRED:
Please review and respond to this message within ${escalationDuration}.

Click here to respond: ${taskLink}

---
This is an automated notification from Collibra Message Workflow.
If you have questions about this workflow, contact your administrator.
```

**Reply Notification Template:**
```
Hi ${startUser.firstName},

You have received a response to your question: "${title}"

RESPONSE:
${receiverMessage}

Next Steps:
- Review the response by clicking: ${taskLink}
- Reply if you have follow-up questions
- Accept and close if your question is resolved

---
Thank you for using Collibra Message Workflow.
```

**Escalation Template:**
```
ESCALATED ISSUE - Attention Required

This message has been escalated to your group for resolution.

ESCALATION REASON:
${escalationReason != null ? escalationReason : 'No response within ' + escalationDuration}

ORIGINAL REQUEST:
From: ${startUser.firstName} ${startUser.lastName}
Title: ${title}
Priority: ${priority}
Category: ${category}

MESSAGE:
${messageBody}

CONVERSATION HISTORY:
${conversationHistory}

IMMEDIATE ACTION REQUIRED:
Please review and respond: ${taskLink}

---
This escalation requires prompt attention from your team.
```

### Multi-Language Support

To support multiple languages, use configuration variables:

```
emailLanguage: en | jp | de | fr
```

Then use conditional expressions in email bodies:

```xml
<flowable:expression>
${emailLanguage == 'jp' ?
  '新しいメッセージが投稿されました' :
  'A new message has been posted'}

${emailLanguage == 'jp' ?
  'タイトル: ' :
  'Title: '}${title}
</flowable:expression>
```

### HTML Email Support

If Collibra supports HTML emails, enhance templates:

```xml
<flowable:field name="htmlBody">
  <flowable:expression><![CDATA[
    <html>
      <body style="font-family: Arial, sans-serif;">
        <h2 style="color: #0066cc;">New Message</h2>
        <p>A new ${category} has been posted.</p>
        <table style="border-collapse: collapse;">
          <tr>
            <td style="font-weight: bold;">Title:</td>
            <td>${title}</td>
          </tr>
          <tr>
            <td style="font-weight: bold;">Priority:</td>
            <td>${priority}</td>
          </tr>
        </table>
        <p><a href="${taskLink}" style="background-color: #0066cc; color: white; padding: 10px 20px; text-decoration: none;">View Message</a></p>
      </body>
    </html>
  ]]></flowable:expression>
</flowable:field>
```

---

## Group Assignment Best Practices

### Choosing Receiver Groups

**Considerations:**
- **Expertise**: Group has knowledge to answer questions
- **Availability**: Members available during business hours
- **Size**: 3-10 members ideal (balance coverage vs. notification overload)
- **Authority**: Can make decisions or escalate appropriately

**Anti-Patterns:**
- ❌ Too small (1-2 members): No coverage during absences
- ❌ Too large (20+ members): Notification fatigue
- ❌ Wrong expertise: Can't answer questions
- ❌ No clear ownership: Tasks sit unassigned

### Choosing Escalation Groups

**Considerations:**
- **Higher Authority**: Can make final decisions
- **Different Members**: Avoid overlap with receiver group
- **Executive Support**: Has organizational influence
- **Escalation Process**: Clear process for handling escalations

**Examples of Good Escalation Hierarchies:**

**Example 1: Data Governance**
```
Level 1 (recipientGroup): Data_Stewards
  - Day-to-day data questions
  - Asset management support
  - Standard inquiries

Level 2 (escalationGroup): Data_Governance_Council
  - Policy decisions
  - Complex issues
  - Cross-domain conflicts
```

**Example 2: IT Support**
```
Level 1 (recipientGroup): IT_Support_Tier1
  - Technical questions
  - Access requests
  - How-to inquiries

Level 2 (escalationGroup): IT_Support_Management
  - Resource allocation
  - Priority conflicts
  - System-level issues
```

### Managing Multiple Receiver Groups

For organizations with multiple teams, consider:

**Option 1: Multiple Workflow Instances**
- Deploy workflow multiple times with different configurations
- Each instance has different recipientGroup
- Clear separation of concerns

**Option 2: Dynamic Assignment (Advanced)**
- Use workflow variables to determine receiver
- Add routing logic in BPMN
- More complex but more flexible

**Option 3: Category-Based Routing**
- Different groups for different categories
- Requires BPMN modification
- Example: Technical questions → IT, Data questions → Data Stewards

---

## Advanced Configuration Scenarios

### Scenario 1: High-Priority Fast Track

**Goal:** High-priority messages get faster response

**Configuration:**
```
# Use shorter escalation for high priority
escalationDuration: PT8H  # 8 hours instead of 7 days
```

**BPMN Modification (Advanced):**
Add conditional escalation based on priority:
```xml
<timerEventDefinition>
  <timeDuration>${priority == 'High' ? 'PT8H' : 'P7D'}</timeDuration>
</timerEventDefinition>
```

### Scenario 2: Business Hours Only

**Goal:** Only escalate during business hours

**Configuration:**
```
escalationDuration: P3BD  # 3 business days (if supported)
```

**Alternative:** Use longer duration to account for weekends
```
escalationDuration: P5D  # 5 calendar days ≈ 3 business days
```

### Scenario 3: Multi-Tier Escalation

**Goal:** Three levels of escalation

**Setup:**
1. Deploy workflow twice:
   - Instance 1: Tier 1 → Tier 2 escalation
   - Instance 2: Tier 2 → Tier 3 escalation

2. Configure Instance 1:
```
recipientGroup: Support_Tier1
escalationGroup: Support_Tier2
escalationDuration: P1D
```

3. Configure Instance 2:
```
recipientGroup: Support_Tier2
escalationGroup: Executive_Team
escalationDuration: P3D
```

### Scenario 4: Region-Specific Groups

**Goal:** Route to different groups by region

**Configuration:**
Create multiple workflow instances:

**APAC Instance:**
```
recipientGroup: Data_Stewards_APAC
escalationGroup: DG_Council_APAC
mailSubjectPrefix: [Collibra APAC]
```

**EMEA Instance:**
```
recipientGroup: Data_Stewards_EMEA
escalationGroup: DG_Council_EMEA
mailSubjectPrefix: [Collibra EMEA]
```

**Americas Instance:**
```
recipientGroup: Data_Stewards_Americas
escalationGroup: DG_Council_Americas
mailSubjectPrefix: [Collibra Americas]
```

### Scenario 5: Testing vs. Production

**Testing Environment:**
```
recipientGroup: Test_Group
emailNotificationEnabled: false
escalationDuration: PT1H
mailSubjectPrefix: [TEST - Collibra]
```

**Production Environment:**
```
recipientGroup: Data_Stewards_Prod
emailNotificationEnabled: true
escalationDuration: P7D
mailSubjectPrefix: [Collibra Q&A]
```

---

## Performance Tuning

### Email Performance

**Issue:** Too many emails overwhelming users

**Solutions:**
1. Increase escalation duration (fewer escalation emails)
2. Configure email digest (if Collibra supports)
3. Use in-app notifications instead
4. Train users to use email filters

**Optimal Configuration:**
```
escalationDuration: P7D  # Weekly escalations
emailNotificationEnabled: true  # But with user filtering
```

### Task Assignment Performance

**Issue:** Tasks sitting unassigned in group queue

**Solutions:**
1. Smaller, more focused receiver groups
2. Clear assignment process within group
3. Monitoring and alerts for unassigned tasks

**Recommendations:**
- Group size: 5-7 members ideal
- Rotate assignment responsibility
- Daily task review by group lead

### Workflow Instance Management

**Issue:** Too many active workflow instances

**Solutions:**
1. Encourage users to close resolved workflows
2. Periodic cleanup of old workflows
3. Auto-close after extended inactivity (requires BPMN modification)

---

## Security Considerations

### Data Privacy

**Question:** Are messages visible to all group members?

**Answer:** Yes, all members of recipientGroup can view tasks and messages.

**Implications:**
- Don't include PII or sensitive data in public messages
- Use private channels for confidential matters
- Consider separate workflow for sensitive topics

### Access Control

**Best Practices:**
- Limit workflow start permissions to authenticated users only
- Restrict receiver group membership to trained personnel
- Escalation group should be senior/trusted members
- Regular audit of group memberships

### Audit Trail

**What's Tracked:**
- All workflow instances
- All user actions (start, reply, close)
- All comments on assets
- All email notifications sent

**Audit Queries:**
- Who started which workflows
- Response times by receiver group
- Escalation frequency
- Closure rates

---

## Integration with Other Workflows

### Triggering Message Workflow from Other Workflows

Use sub-process call or signal event to start Message Workflow from another workflow when specific conditions met.

### Linking to Asset Workflows

If Message Workflow discusses an asset undergoing approval:
- Both workflows can add comments to same asset
- Conversation visible in both contexts
- Coordinate closure of both workflows

### Workflow Orchestration

Create parent orchestration workflow that:
1. Receives initial request
2. Routes to appropriate workflow (Message, Approval, Review)
3. Monitors progress
4. Escalates if needed

---

## Configuration Change Management

### Making Configuration Changes

**Best Practices:**
1. **Test in Non-Prod First**: Always test configuration changes in test environment
2. **Document Changes**: Record what changed, when, and why
3. **Communicate**: Inform users of configuration changes
4. **Monitor Impact**: Watch metrics after changes

### Rollback Plan

If configuration change causes issues:
1. Navigate to workflow configuration
2. Restore previous values
3. Save configuration
4. Active workflows continue with new settings
5. Document the rollback

### Configuration Versioning

**Recommendation:** Maintain configuration documentation:

```markdown
## Configuration History

### 2024-05-15: Initial Deployment
- recipientGroup: JP_Test_Group_For_Temporary
- escalationDuration: P7D

### 2024-06-01: Updated Receiver Group
- recipientGroup: Data_Stewards_Production
- Reason: Moved from test to production

### 2024-07-10: Shortened Escalation
- escalationDuration: P3D (was P7D)
- Reason: Too many overdue responses
```

---

## Configuration Checklist

Use this checklist when configuring the workflow:

- [ ] recipientGroup set to valid, active group
- [ ] escalationGroup set to valid, higher-authority group
- [ ] escalationDuration appropriate for your SLA
- [ ] mailSubjectPrefix distinctive and clear
- [ ] emailNotificationEnabled set to true (unless testing)
- [ ] Receiver group has 3+ active members with valid emails
- [ ] Escalation group has 2+ active members
- [ ] Groups have appropriate permissions
- [ ] Configuration tested with sample workflow
- [ ] Users notified of configuration
- [ ] Configuration documented
- [ ] Monitoring in place for workflow performance

---

For configuration examples, see [configuration-examples.md](../examples/configuration-examples.md).

For implementation details, see [ARCHITECTURE.md](ARCHITECTURE.md).
