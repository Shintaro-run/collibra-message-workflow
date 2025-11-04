# Installation Guide - Collibra Message Workflow

This guide provides step-by-step instructions for installing and configuring the Message Workflow in your Collibra environment.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Pre-Installation Checklist](#pre-installation-checklist)
- [Installation Steps](#installation-steps)
- [Initial Configuration](#initial-configuration)
- [Enabling the Workflow](#enabling-the-workflow)
- [User Group Setup](#user-group-setup)
- [Permission Configuration](#permission-configuration)
- [Testing the Installation](#testing-the-installation)
- [Troubleshooting](#troubleshooting)

## Prerequisites

### System Requirements
- **Collibra DGC Version**: 2024.05 or higher
- **Access Level**: System Administrator or Workflow Administrator role
- **Email Configuration**: SMTP server configured in Collibra (for notifications)
- **Browser**: Modern browser (Chrome, Firefox, Edge, Safari latest versions)

### Required Components
- Flowable workflow engine (included in Collibra)
- BPMN 2.0 support (included in Collibra)
- Collibra workflow delegates:
  - AddComment delegate
  - MailSender delegate
  - Script delegate

### User Requirements
- At least one user group designated as receiver
- Users in receiver group must have valid email addresses
- Users must have appropriate permissions (see Permission Configuration)

## Pre-Installation Checklist

Before installing the workflow, verify the following:

- [ ] You have System Administrator or Workflow Administrator access
- [ ] Email server is configured and working in Collibra
- [ ] At least one user group exists to act as receiver
- [ ] Users in receiver group have valid email addresses
- [ ] You have downloaded the `message-workflow.bpmn` file
- [ ] You have reviewed the default configuration variables
- [ ] You have identified an escalation group (optional but recommended)
- [ ] You have tested email functionality in Collibra
- [ ] You have backup of current workflow configurations (if applicable)

## Installation Steps

### Step 1: Access Workflow Settings

1. Log in to Collibra as Administrator
2. Click on the gear icon (⚙️) in the top-right corner
3. Select **Workflows** from the left sidebar
4. Click on **Definitions** tab

### Step 2: Upload Workflow Definition

1. In the Workflow Definitions page, click **"Upload Workflow"** button
2. Click **"Choose File"** or drag-and-drop
3. Navigate to and select `message-workflow.bpmn`
4. Wait for upload confirmation message
5. Verify "Message Workflow" appears in the definitions list

**Expected Result:**
```
Workflow Name: Message Workflow
Process ID: messageWorkflow
Status: Inactive (not yet enabled)
```

### Step 3: Verify Upload

1. Locate "Message Workflow" in the definitions list
2. Click on the workflow name to view details
3. Verify the following information:
   - **Name**: Message Workflow
   - **Process ID**: messageWorkflow
   - **BPMN Version**: 2.0
   - **Status**: Uploaded successfully

### Step 4: Review Workflow Structure

1. Click **"View Diagram"** (if available) to see visual representation
2. Verify key elements are present:
   - Start event with form
   - Service tasks for notifications
   - User tasks for receiver and sender
   - Gateways for routing
   - End event
3. Close diagram view

## Initial Configuration

### Step 5: Configure Workflow Variables

1. In Workflow Definitions, find "Message Workflow"
2. Click **"Edit Configuration"** or **"Configure"** button
3. You will see the configuration variables interface

### Step 6: Set Receiver Group

**CRITICAL:** This is the most important configuration setting.

1. Locate variable: `recipientGroup`
2. Current default: `JP_Test_Group_For_Temporary`
3. Change to your actual receiver group name (exact match required)
4. Example: `Data_Stewards` or `IT_Support_Team`

**Important Notes:**
- Group name must match exactly (case-sensitive)
- Use underscore (_) not space in group names
- To find available groups:
  - Navigate to Settings → Users & Groups → Groups
  - Copy the exact group name from the list

### Step 7: Configure Email Settings

1. Locate variable: `emailNotificationEnabled`
   - Set to `true` to enable email notifications
   - Set to `false` to disable (not recommended)

2. Locate variable: `mailSubjectPrefix`
   - Default: `[Collibra Q&A]`
   - Customize to your preference
   - Example: `[Data Governance]` or `[Support]`

### Step 8: Configure Escalation Settings

1. Locate variable: `escalationDuration`
   - Default: `P7D` (7 days)
   - Format: ISO 8601 duration
   - Examples:
     - `P1D` = 1 day
     - `P3D` = 3 days
     - `P14D` = 14 days
     - `PT24H` = 24 hours

2. Locate variable: `escalationGroup`
   - Default: `Data_Governance_Council`
   - Change to your escalation group name
   - Must be a valid, existing group

### Step 9: Save Configuration

1. Review all configuration variables
2. Click **"Save"** or **"Apply"** button
3. Wait for confirmation message
4. Verify changes are saved

**Configuration Summary:**
```
recipientGroup: [Your Receiver Group Name]
emailNotificationEnabled: true
escalationDuration: P7D
mailSubjectPrefix: [Collibra Q&A]
escalationGroup: [Your Escalation Group Name]
```

## Enabling the Workflow

### Step 10: Set Start Permissions

1. Navigate to: Settings → Workflows → **Start Workflow** tab
2. Locate "Message Workflow" in the list
3. Click **"Configure"** or **"Edit Permissions"**
4. Configure who can start the workflow:

**Option A: All Authenticated Users (Recommended)**
- Select: "All authenticated users"
- This allows any logged-in user to post questions

**Option B: Specific Roles**
- Select specific roles that can start workflow
- Example: User, Data Steward, Business User

**Option C: Specific Users**
- Select individual users (not recommended for general use)

5. Click **"Save"**

### Step 11: Enable the Workflow

1. In the Start Workflow configuration page
2. Find the **"Status"** or **"Enabled"** toggle
3. Switch to **"Enabled"** or **"Active"**
4. Confirm the activation

**Expected Result:**
```
Status: Enabled
Available to: [Configured users/roles]
```

### Step 12: Set Workflow Visibility

1. Optionally configure workflow categories/tags
2. Set visibility in workflow start menu
3. Configure any additional access controls

## User Group Setup

### Step 13: Verify Receiver Group

1. Navigate to: Settings → Users & Groups → **Groups**
2. Search for your configured receiver group
3. Click on the group to view details
4. Verify:
   - Group has at least one member
   - Members have valid email addresses
   - Members have appropriate permissions

### Step 14: Verify Escalation Group

1. Navigate to: Settings → Users & Groups → **Groups**
2. Search for your configured escalation group
3. Click on the group to view details
4. Verify:
   - Group exists and is active
   - Group has at least one member
   - Members have higher authority than receiver group

### Step 15: Configure Group Permissions

Receiver group members need:
- **Read** permission on assets they'll receive questions about
- **Comment** permission to add comments (if using asset linking)
- **Workflow** task completion permissions

To set permissions:
1. Navigate to asset type or domain
2. Configure permissions for receiver group
3. Grant necessary access levels

## Permission Configuration

### Required Permissions for Users

**For Workflow Initiators (Senders):**
- Start Workflow permission
- Read access to assets (if linking to assets)
- Basic user role in Collibra

**For Receiver Group Members:**
- Complete Workflow Tasks permission
- Comment on Assets permission (if using asset linking)
- Read/Write access to relevant assets
- Email address configured in profile

**For Escalation Group Members:**
- Complete Workflow Tasks permission
- Comment on Assets permission
- Higher-level data governance authority
- Email address configured in profile

### Granting Permissions

1. Navigate to: Settings → Security → **Roles**
2. Select the relevant role
3. Ensure the following permissions are enabled:
   - Workflow → Complete Tasks
   - Resources → Add Comment (for asset linking)
   - Resources → View (read access)

## Testing the Installation

### Step 16: Test Workflow Start

1. Log out and log back in (to refresh permissions)
2. Navigate to any Collibra page
3. Click **"Start Workflow"** menu
4. Verify "Message Workflow" appears in the list
5. Click on "Message Workflow"

**Expected Result:**
You should see a form with:
- Title (text input)
- Message (text area)
- Priority (dropdown: High/Medium/Low)
- Category (dropdown: Question/Issue Report/Other)
- Related Asset (asset picker)

### Step 17: Submit Test Message

1. Fill in the form:
   - Title: "Test Message - Installation Verification"
   - Message: "This is a test message to verify workflow installation."
   - Priority: Medium
   - Category: Question
   - Related Asset: (select any asset, optional)
2. Click **"Submit"** or **"Start Workflow"**
3. Wait for confirmation

**Expected Result:**
- "Workflow started successfully" message
- Workflow instance created

### Step 18: Verify Receiver Notification

1. Check email of receiver group members
2. Verify they received notification email with:
   - Subject: `[Collibra Q&A] [Medium] Test Message - Installation Verification`
   - Body containing the test message
   - Link to task in Collibra

3. Log in as receiver group member
4. Navigate to: **Tasks** (bell icon or tasks menu)
5. Verify task appears: "Review and Respond"

### Step 19: Test Receiver Response

As receiver group member:
1. Open the task "Review and Respond"
2. Review the form fields:
   - Conversation History (read-only)
   - Original message details (read-only)
   - Action dropdown (Reply/Escalate/Close)
3. Select Action: **Reply**
4. Enter message: "Test reply from receiver"
5. Click **"Complete"** or **"Submit"**

**Expected Result:**
- Task completed successfully
- Sender receives email notification
- New task appears for sender

### Step 20: Test Sender Response

As original sender:
1. Check email for reply notification
2. Navigate to Tasks in Collibra
3. Open task "Review Response"
4. Verify receiver's reply is visible
5. Select Action: **Accept and Close**
6. Click **"Complete"**

**Expected Result:**
- Workflow completes successfully
- Closure notification sent
- Task removed from task list
- If asset was linked, comments appear on asset

### Step 21: Verify Comments on Asset

If you linked an asset:
1. Navigate to the asset you selected
2. Click on **Comments** tab
3. Verify comments are present:
   - Initial message from sender
   - Reply from receiver
   - Closure note

**Expected Format:**
```
[Sender Name] (username) - [timestamp]: [Question] Test Message
This is a test message...

[Receiver Name] (Receiver) - [timestamp]:
Test reply from receiver

[Receiver Name] (Receiver) - [timestamp]:
[CLOSED] Workflow closed by receiver.
```

## Troubleshooting

### Issue: Workflow not appearing in upload list after upload

**Possible Causes:**
- BPMN file is corrupted
- Invalid XML syntax
- Unsupported BPMN elements

**Solutions:**
1. Verify BPMN file is valid XML (open in text editor)
2. Check for XML parsing errors in Collibra logs
3. Re-download workflow file and try again
4. Contact Collibra support with error logs

### Issue: Configuration variables not showing

**Possible Causes:**
- Workflow not properly registered
- Cache issue in Collibra
- Incorrect BPMN namespace

**Solutions:**
1. Refresh browser (Ctrl+F5 or Cmd+Shift+R)
2. Log out and log back in
3. Check BPMN file uses correct namespace: `http://collibra.com/workflow`
4. Restart workflow engine (requires admin access)

### Issue: Emails not being sent

**Possible Causes:**
- Email server not configured
- Invalid email addresses
- MailSender delegate not available
- Email notifications disabled

**Solutions:**
1. Verify email server configuration: Settings → System → Email Server
2. Test email functionality: Send test email from Collibra
3. Check user profiles have valid email addresses
4. Verify `emailNotificationEnabled` is set to `true`
5. Check Collibra logs for MailSender errors

### Issue: Receiver group not receiving tasks

**Possible Causes:**
- Group name mismatch (case-sensitive)
- Group doesn't exist
- Group has no members
- Incorrect group assignment syntax

**Solutions:**
1. Verify exact group name: Settings → Users & Groups → Groups
2. Ensure group has active members
3. Check configuration variable `recipientGroup` matches exactly
4. Test with `${group(recipientGroup)}` syntax in BPMN

### Issue: Comments not appearing on assets

**Possible Causes:**
- User lacks comment permission
- Asset ID is invalid
- AddComment delegate error
- Asset not selected in workflow

**Solutions:**
1. Verify users have "Add Comment" permission
2. Check asset exists and is accessible
3. Review Collibra logs for AddComment delegate errors
4. Ensure `relatedAssetId` is properly passed in workflow

### Issue: Auto-escalation not triggering

**Possible Causes:**
- Invalid duration format
- Escalation group doesn't exist
- Timer event not configured correctly
- Workflow engine issue

**Solutions:**
1. Verify duration format is ISO 8601: `P7D` (not `7 days`)
2. Check escalation group exists and is valid
3. Test with shorter duration (e.g., `PT1H` = 1 hour) for testing
4. Review workflow engine logs

### Issue: Workflow stuck in "In Progress"

**Possible Causes:**
- Task assigned to wrong user/group
- User doesn't have task completion permission
- Workflow path deadlock

**Solutions:**
1. Check task assignment in workflow instance details
2. Verify user has workflow task permissions
3. Manually reassign task if needed (admin capability)
4. Review workflow logs for errors

### Getting Additional Help

If issues persist:
1. Check Collibra system logs: Settings → System → Logs
2. Enable debug logging for workflow engine
3. Contact Collibra Support with:
   - Error messages
   - Workflow instance ID
   - Configuration details
   - Log excerpts
4. Review Collibra community forums for similar issues

## Post-Installation Steps

After successful installation and testing:

1. **Document Configuration**
   - Record all configuration variables
   - Document receiver and escalation groups
   - Note any customizations made

2. **Train Users**
   - Conduct training for receiver group members
   - Create user guides for workflow initiators
   - Share usage scenarios and best practices

3. **Monitor Initial Usage**
   - Review first few workflow instances
   - Collect feedback from users
   - Adjust configuration if needed

4. **Regular Maintenance**
   - Review workflow performance monthly
   - Update receiver groups as needed
   - Monitor escalation patterns
   - Adjust timeout durations based on usage

## Next Steps

- Read [CONFIGURATION.md](CONFIGURATION.md) for advanced configuration options
- Review [ARCHITECTURE.md](ARCHITECTURE.md) for technical details
- Explore [configuration-examples.md](../examples/configuration-examples.md) for different scenarios
- Check [usage-scenarios.md](../examples/usage-scenarios.md) for best practices

## Installation Checklist

Use this checklist to track your installation progress:

- [ ] Prerequisites verified
- [ ] BPMN file uploaded successfully
- [ ] Workflow appears in definitions list
- [ ] Receiver group configured
- [ ] Email settings configured
- [ ] Escalation settings configured
- [ ] Configuration saved
- [ ] Start permissions set
- [ ] Workflow enabled
- [ ] Receiver group verified
- [ ] Escalation group verified
- [ ] Permissions granted
- [ ] Test workflow started successfully
- [ ] Receiver notification received
- [ ] Receiver response tested
- [ ] Sender response tested
- [ ] Comments verified on asset
- [ ] Documentation completed
- [ ] Users trained

---

**Installation Complete!** Your Message Workflow is now ready for production use.
