# Usage Scenarios - Collibra Message Workflow

This document provides detailed usage scenarios, step-by-step guides, and expected behaviors for the Message Workflow.

## Table of Contents
- [Common Scenarios](#common-scenarios)
- [Step-by-Step User Guides](#step-by-step-user-guides)
- [Best Practices](#best-practices)
- [Workflow Behavior Examples](#workflow-behavior-examples)
- [Edge Cases and Special Situations](#edge-cases-and-special-situations)

## Common Scenarios

### Scenario 1: Simple Question and Answer

**Context:**
A business user has a question about how to update an asset's attributes.

**Participants:**
- **Sender:** Jane Doe (Business Analyst)
- **Receiver:** Data Stewards group

**Timeline:**

**Day 1, 10:00 AM - Jane starts workflow:**
1. Navigates to Collibra
2. Clicks "Start Workflow" → "Message Workflow"
3. Fills form:
   - Title: "How do I update custom attributes?"
   - Message: "I need to update the 'Data Owner' attribute for several assets but don't see the edit option."
   - Priority: Medium
   - Category: Question
   - Related Asset: [Selects Customer Data asset]
4. Clicks Submit

**Day 1, 10:00 AM - System processes:**
- Workflow starts
- Status set to "New"
- Comment saved on Customer Data asset
- Email sent to Data Stewards group

**Day 1, 2:00 PM - Data Steward responds:**
1. John Smith (Data Steward) receives email notification
2. Opens task from email link
3. Reviews Jane's question
4. Selects Action: "Reply"
5. Enters message: "You need 'Edit' permission on the asset. I've granted you the permission. Try refreshing and you should see the edit button."
6. Clicks Complete

**Day 1, 2:01 PM - System processes:**
- Status changes to "In Progress"
- Reply saved as comment
- Email sent to Jane

**Day 1, 3:00 PM - Jane closes workflow:**
1. Jane receives email notification
2. Opens task
3. Reads John's reply
4. Selects Action: "Accept and Close"
5. Clicks Complete

**Day 1, 3:01 PM - System processes:**
- Status changes to "Closed"
- Closure email sent to Jane
- Workflow completes

**Expected Outcome:**
- Question answered same day
- Complete conversation history on asset
- All participants notified
- Workflow closed successfully

---

### Scenario 2: Multi-Round Conversation

**Context:**
A data analyst has a complex question requiring multiple exchanges.

**Participants:**
- **Sender:** Mike Chen (Data Analyst)
- **Receiver:** Data Stewards group

**Timeline:**

**Monday 9:00 AM - Mike starts workflow:**
- Title: "Data quality issue in Customer table"
- Message: "I'm seeing null values in the email field for customers. Should these be populated?"
- Priority: High
- Category: Issue Report
- Related Asset: Customer Table

**Monday 11:00 AM - Data Steward replies:**
- Reply: "Email is required for customers. Can you provide some customer IDs with null emails?"

**Monday 2:00 PM - Mike follows up:**
- Action: Reply
- Message: "Sure, IDs: 12345, 12389, 12401 all have null emails."

**Tuesday 10:00 AM - Data Steward investigates:**
- Reply: "Thanks! I checked and these are legacy customers from pre-2020 migration. We have a process to backfill emails. I'll create a task for the data team."

**Tuesday 11:00 AM - Mike confirms:**
- Action: Reply
- Message: "Perfect! Should I exclude these null values in my analysis for now?"

**Tuesday 2:00 PM - Data Steward advises:**
- Reply: "Yes, please exclude nulls for now. The backfill will take 2 weeks. I'll notify you when complete."

**Tuesday 3:00 PM - Mike closes:**
- Action: Accept and Close

**Expected Outcome:**
- Issue identified and solution provided
- 6 messages exchanged over 2 days
- Complete audit trail on Customer Table asset
- Mike has clear action items

---

### Scenario 3: Auto-Escalation

**Context:**
A question is posted but receiver group doesn't respond in time.

**Participants:**
- **Sender:** Sarah Johnson (Business User)
- **Receiver:** Data Stewards group (no response)
- **Escalation:** Data Governance Council

**Timeline:**

**Day 1 - Sarah starts workflow:**
- Title: "Policy question: Can we share customer data with vendors?"
- Priority: High
- Category: Question
- No related asset

**Day 1-7 - No response:**
- Data Stewards receive email but don't respond
- Task sits in their queue

**Day 8, 9:00 AM - Auto-escalation triggers:**
- System: Timer boundary event fires after 7 days (P7D)
- Original receiver task cancelled
- Email sent to Data Governance Council
- New task created for Council

**Day 8, 11:00 AM - Council member responds:**
1. Council member Maria Lopez receives escalation email
2. Opens task (shows "[ESCALATED]" in subject)
3. Reviews original question and lack of response
4. Selects Action: Reply
5. Message: "This requires legal review. Sharing customer data with vendors requires explicit consent and contract terms. I'll set up a meeting with Legal and you."
6. Clicks Complete

**Day 8, 11:01 AM - System processes:**
- Status: In Progress
- Reply sent to Sarah
- Sarah can now respond or close

**Day 8, 2:00 PM - Sarah responds:**
- Action: Reply
- Message: "Thank you! Looking forward to the meeting."

**Day 9, 10:00 AM - Maria closes:**
- Action: Close
- Closure Notes: "Meeting scheduled for next week. Follow-up via separate channels."

**Expected Outcome:**
- Question didn't get lost
- Escalated to appropriate authority
- Meeting scheduled to address complex issue
- Escalation path validated

**Lessons Learned:**
- Data Stewards need training on checking tasks daily
- Policy questions should go directly to Council
- Consider separate workflow for policy questions

---

### Scenario 4: Manual Escalation

**Context:**
Receiver realizes question is beyond their expertise and escalates immediately.

**Participants:**
- **Sender:** Tom Wilson (Product Manager)
- **Receiver:** Data Steward (immediate escalation)
- **Escalation:** Data Governance Council

**Timeline:**

**Day 1, 9:00 AM - Tom starts workflow:**
- Title: "New regulatory requirement: GDPR right to deletion"
- Message: "New EU regulation requires we implement 'right to be forgotten'. How do we handle this in Collibra?"
- Priority: High
- Category: Question

**Day 1, 10:00 AM - Data Steward escalates:**
1. Data Steward Lisa Brown opens task
2. Reads question
3. Realizes this is regulatory/legal matter
4. Selects Action: "Escalate"
5. Escalation Reason: "This involves legal compliance and regulatory requirements. Requires Council review and likely legal consultation."
6. Clicks Complete

**Day 1, 10:01 AM - System processes:**
- Email sent to Data Governance Council
- Task created for Council
- Status: In Progress

**Day 1, 2:00 PM - Council responds:**
1. Council member reviews escalation
2. Action: Reply
3. Message: "Good question. We have a GDPR compliance workflow separate from this. I'll connect you with our Data Privacy Officer and provide the workflow link."
4. Completes task

**Day 1, 3:00 PM - Tom closes:**
- Action: Accept and Close

**Expected Outcome:**
- Quick recognition that question requires escalation
- Proper escalation with clear reason
- Tom connected to right resources
- Efficient use of everyone's time

**Best Practice Demonstrated:**
- Receiver knows when to escalate
- Clear escalation reason provided
- Appropriate routing to expert resources

---

## Step-by-Step User Guides

### Guide 1: For Workflow Initiators (Senders)

**How to Post a Question:**

**Step 1: Access Workflow**
- Click "Start Workflow" (usually in top navigation or workflow menu)
- Select "Message Workflow" from list

**Step 2: Fill Out Form**
```
Title: [Brief summary of your question, 1 sentence]
  ✓ Good: "How do I assign data owners to assets?"
  ✗ Bad: "Question" or "Help needed"

Message: [Detailed question with context]
  ✓ Good: "I'm working on the Customer Data domain and need to assign data owners to 50+ assets. Is there a bulk assignment feature?"
  ✗ Bad: "How do I do this?"

Priority:
  - High: Blocking your work, urgent decision needed
  - Medium: Important but not blocking
  - Low: Nice to know, advisory question

Category:
  - Question: How-to, advisory, information request
  - Issue Report: Something broken, data quality issue
  - Other: Doesn't fit above categories

Related Asset: [Optional]
  - Select if question relates to specific asset
  - Helps provide context
  - Conversation will be saved on asset
```

**Step 3: Submit**
- Review your question
- Click "Submit" or "Start Workflow"
- You'll receive confirmation message

**Step 4: Wait for Response**
- Receiver group will receive email notification
- Response time depends on priority and configuration
- You'll receive email when receiver responds

**Step 5: Respond to Reply**
When receiver responds:
- Check your email for notification
- Click link to view response in Collibra
- Choose action:
  - **Reply**: If you have follow-up questions
  - **Accept and Close**: If your question is answered

**Step 6: Closure**
- Workflow closes when you accept close or receiver closes
- You'll receive final confirmation email
- All messages saved (on asset if you linked one)

---

### Guide 2: For Receivers (Data Stewards)

**How to Handle Incoming Questions:**

**Step 1: Receive Notification**
- Email notification arrives with question details
- Subject: `[Collibra Q&A] [Priority] Title`
- Email includes message body and link to task

**Step 2: Review Question**
- Click link in email or navigate to Tasks in Collibra
- Open task "Review and Respond"
- Read conversation history, original message, priority

**Step 3: Determine Action**
Choose appropriate action:

**Action: Reply**
- When: You can answer the question
- Enter your response in "Your Message" field
- Provide clear, complete answer
- Include any relevant links or references
- Click Complete

**Action: Escalate**
- When: Question requires higher authority, specialized expertise, or policy decision
- Enter clear "Escalation Reason"
- Explain why escalation needed
- Escalation group will receive notification
- Click Complete

**Action: Close**
- When: Question is already answered, no longer relevant, or duplicate
- Enter "Closure Notes" explaining why closing
- Sender will be notified
- Click Complete

**Step 4: Follow-Up (if sender replies)**
- If sender replies with follow-up, you'll receive new task
- Repeat steps 2-3 until question fully resolved
- Either you or sender can close workflow

**Best Practices for Receivers:**
- Respond within SLA (check escalation duration)
- Provide complete, clear answers
- Include examples when helpful
- Escalate early if unsure
- Close only when truly resolved

---

### Guide 3: For Escalation Handlers

**How to Handle Escalated Issues:**

**Step 1: Receive Escalation**
- Email subject includes "[ESCALATED]"
- Email shows escalation reason (if manual) or timeout notice (if auto)

**Step 2: Review Context**
- Open task "Handle Escalated Issue"
- Read full conversation history
- Understand why escalated
- Review original question, priority, category

**Step 3: Take Action**

**Option A: Reply to Sender**
- Provide answer or guidance
- May require investigation, meetings, decisions
- Send complete response
- Conversation continues with sender

**Option B: Close Immediately**
- If question already answered elsewhere
- If issue resolved through other channels
- If duplicate or no longer relevant
- Provide closure notes

**Step 4: Follow Through**
- If you promised action (meeting, investigation, etc.), follow through
- May continue conversation if sender has follow-ups
- Close when issue fully resolved

**Escalation Handler Responsibilities:**
- Treat escalations as high priority
- Understand why escalation occurred
- Provide authoritative response
- Consider improving processes to prevent future escalations

---

## Best Practices

### For Senders

**DO:**
- ✅ Provide clear, detailed questions
- ✅ Include relevant context
- ✅ Link to related asset when applicable
- ✅ Choose appropriate priority
- ✅ Respond promptly when receiver replies
- ✅ Close workflow when question answered

**DON'T:**
- ❌ Post overly vague questions
- ❌ Use High priority for non-urgent questions
- ❌ Start multiple workflows for same question
- ❌ Leave conversations hanging without response
- ❌ Include sensitive/confidential data in messages

---

### For Receivers

**DO:**
- ✅ Check tasks daily
- ✅ Respond within SLA
- ✅ Provide complete, actionable answers
- ✅ Escalate when appropriate
- ✅ Ask clarifying questions if needed
- ✅ Close only when truly resolved

**DON'T:**
- ❌ Let tasks sit unresponded
- ❌ Provide incomplete answers
- ❌ Close without proper resolution
- ❌ Ignore escalation-worthy questions
- ❌ Reply without fully understanding question

---

### For Organizations

**DO:**
- ✅ Train users on when to use workflow
- ✅ Set clear SLA expectations
- ✅ Monitor escalation rates
- ✅ Review workflow metrics regularly
- ✅ Maintain appropriate group sizes
- ✅ Document common Q&A for self-service

**DON'T:**
- ❌ Set unrealistic escalation durations
- ❌ Overload receiver groups
- ❌ Ignore escalation patterns
- ❌ Use as replacement for documentation
- ❌ Allow duplicate workflows

---

## Workflow Behavior Examples

### Behavior 1: Conversation Loop

**Question:** How many times can sender and receiver exchange messages?

**Answer:** Unlimited. The workflow loops between sender and receiver indefinitely until one party closes.

**Example:**
```
1. Sender posts question
2. Receiver replies
3. Sender replies (loop back to receiver)
4. Receiver replies (loop back to sender)
5. Sender replies (loop back to receiver)
... continues until closure
```

**Best Practice:** Keep conversations focused. If more than 5-6 exchanges, consider meeting or phone call.

---

### Behavior 2: Status Transitions

**Question:** What causes workflow status to change?

**Answer:**
```
NEW → IN PROGRESS:
- Receiver replies for first time
- Receiver escalates (manual)
- Auto-escalation triggers
- Escalation handler replies

IN PROGRESS → CLOSED:
- Receiver selects Close action
- Sender selects Accept and Close
- Escalation handler selects Close
```

**Note:** Status does NOT affect related asset status. They are independent.

---

### Behavior 3: Email Notifications

**Question:** When are emails sent?

**Answer:**
- When workflow starts (to receiver group)
- When receiver replies (to sender)
- When sender replies (to receiver group)
- When escalation occurs (to escalation group)
- When workflow closes (to sender)

**Frequency:** One email per event, no batching or digest.

**Opt-Out:** Users can create email filters or configure preferences (check Collibra settings).

---

### Behavior 4: Comment Saving

**Question:** When are comments saved on assets?

**Answer:** Comments saved at these points (only if asset was linked):
- Initial message posted
- Receiver replies
- Sender replies
- Closure notes

**Format:**
```
[Sender Name] (username) - [timestamp]: [Category] Title
Message body

[Receiver Name] (Receiver) - [timestamp]:
Reply message

[CLOSED] Closure notes
```

**Access:** Anyone with read access to asset can view comments.

---

### Behavior 5: Task Assignment

**Question:** Who sees the tasks?

**Answer:**
- **Receiver Tasks:** All members of receiver group see task in their task list
- **Sender Tasks:** Only original sender sees their tasks
- **Escalated Tasks:** All members of escalation group see task

**Claiming:** For group tasks, first person to open can claim. Some Collibra configurations allow any group member to complete.

---

## Edge Cases and Special Situations

### Edge Case 1: Receiver Closes Immediately

**Scenario:** Receiver closes workflow without replying.

**Behavior:**
- Workflow goes from NEW → CLOSED (skips IN PROGRESS)
- Sender receives closure notification
- No reply message sent
- Closure notes should explain why closed

**When Appropriate:**
- Duplicate question
- Question already answered elsewhere
- Issue resolved through other means
- Invalid/spam workflow

---

### Edge Case 2: Sender Doesn't Respond

**Scenario:** Receiver replies but sender never responds.

**Behavior:**
- Task sits in sender's task list indefinitely
- No automatic closure
- Workflow stays IN PROGRESS

**Recommendations:**
- Organizational policy: Auto-close after 30 days of inactivity (requires BPMN modification)
- Receiver can close workflow with notes like "No response from sender, closing as resolved"
- Monitor for stale workflows

---

### Edge Case 3: Concurrent Replies

**Scenario:** Receiver replies while sender is also replying.

**Behavior:**
- Flowable handles concurrency
- One task will complete first (locks workflow)
- Second task may become invalid
- User receives error: "Task no longer available"

**Resolution:**
- User who got error should refresh and find new task
- Their message not saved, need to re-enter
- Rare occurrence in practice

---

### Edge Case 4: User Leaves Organization

**Scenario:** Sender or receiver leaves organization mid-conversation.

**Sender Leaves:**
- Their tasks assigned to them become orphaned
- Receiver can still close workflow
- Consider admin reassignment of tasks

**Receiver Leaves:**
- Other group members can complete task
- Group-based assignment prevents orphan tasks

**Best Practice:** Deactivate users properly in Collibra, reassign tasks before departure.

---

### Edge Case 5: Related Asset Deleted

**Scenario:** Asset linked in workflow is deleted.

**Behavior:**
- Workflow continues normally
- Comments no longer visible (asset gone)
- No error in workflow
- relatedAssetId still stored but references non-existent asset

**Prevention:** Consider asset retention policies.

---

### Edge Case 6: Group Membership Changes

**Scenario:** User removed from receiver group while task assigned.

**Behavior:**
- User loses access to group tasks
- Other group members can still complete
- New group members see all existing group tasks

**Best Practice:** Review group memberships regularly.

---

### Edge Case 7: Multiple Workflows on Same Asset

**Scenario:** Two users start workflows about same asset.

**Behavior:**
- Both workflows are independent
- Both conversations appear in asset comments
- No conflict or merging
- Each tracks separately

**When Appropriate:**
- Different questions about same asset
- Different timeframes

**When Inappropriate:**
- Duplicate questions
- Second sender should search comments first

**Best Practice:** Search asset comments before starting new workflow on same topic.

---

## Real-World Scenarios

### Real-World 1: Data Quality Investigation

**Participants:** Business Analyst, Data Steward, Data Engineer

**Flow:**
1. Analyst finds data quality issue
2. Posts workflow with HIGH priority
3. Data Steward reviews, asks for details
4. Analyst provides specific records
5. Data Steward investigates, identifies root cause
6. Data Steward escalates to Data Engineer (via separate channel)
7. Data Steward updates Analyst via workflow reply
8. Fix deployed
9. Analyst verifies fix
10. Workflow closed

**Duration:** 3 days
**Messages:** 6
**Outcome:** Issue resolved, complete audit trail

---

### Real-World 2: New User Onboarding Questions

**Participants:** New user, Data Steward

**Flow:**
1. New user has 5 questions about Collibra usage
2. Posts first question
3. Data Steward answers
4. User posts follow-up
5. Data Steward provides documentation links
6. User satisfied, closes workflow

**Note:** Instead of 5 separate workflows, one conversation covered multiple related questions.

**Best Practice:** Related questions can be in one workflow, unrelated questions should be separate workflows.

---

### Real-World 3: Policy Clarification

**Participants:** Business User, Data Steward, Data Governance Council, Legal

**Flow:**
1. User asks about data sharing policy
2. Data Steward not sure, escalates
3. Council reviews, determines legal question
4. Council replies: "This requires legal review"
5. Legal consulted offline
6. Council updates workflow with legal guidance
7. User receives official policy statement
8. Workflow closed

**Duration:** 2 weeks (legal review time)
**Outcome:** Official policy documented in workflow history

---

## Summary

The Message Workflow supports a wide variety of usage scenarios, from simple Q&A to complex multi-party discussions. Key success factors:

1. **Clear Communication:** Well-written questions and answers
2. **Appropriate Escalation:** Know when to escalate
3. **Timely Responses:** Respect SLAs
4. **Proper Closure:** Don't leave conversations hanging
5. **Asset Linking:** Connect conversations to relevant assets

For additional support, refer to:
- [README.md](../README.md) - Overview and quick start
- [INSTALLATION.md](../docs/INSTALLATION.md) - Setup guide
- [CONFIGURATION.md](../docs/CONFIGURATION.md) - Configuration details
- [configuration-examples.md](configuration-examples.md) - Configuration scenarios
