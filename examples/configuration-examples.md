# Configuration Examples - Collibra Message Workflow

This document provides real-world configuration examples for different organizational scenarios and use cases.

## Table of Contents
- [Basic Configurations](#basic-configurations)
- [Use Case Scenarios](#use-case-scenarios)
- [Multi-Environment Setup](#multi-environment-setup)
- [Advanced Configurations](#advanced-configurations)
- [Troubleshooting Configurations](#troubleshooting-configurations)

## Basic Configurations

### Example 1: Standard Data Governance Team

**Scenario:** Single data governance team handling all data-related questions

**Configuration:**
```
recipientGroup: Data_Governance_Team
emailNotificationEnabled: true
escalationDuration: P7D
mailSubjectPrefix: [Data Governance]
escalationGroup: Chief_Data_Office
```

**Characteristics:**
- One-week response time expectation
- Escalates to CDO after one week
- Standard email notifications
- Clear subject line for filtering

**Best For:**
- Mid-sized organizations (100-500 users)
- Centralized data governance
- Standard response time expectations

---

### Example 2: IT Support with Fast Response

**Scenario:** IT support team with 24-hour response SLA

**Configuration:**
```
recipientGroup: IT_Support_L1
emailNotificationEnabled: true
escalationDuration: PT24H
mailSubjectPrefix: [IT Support]
escalationGroup: IT_Support_L2
```

**Characteristics:**
- 24-hour escalation (one business day)
- Two-tier support structure
- Fast turnaround expectation

**Best For:**
- Technical support teams
- Time-sensitive requests
- Organizations with 24/7 support

---

### Example 3: Business Analyst Community

**Scenario:** Collaborative community of business analysts answering user questions

**Configuration:**
```
recipientGroup: Business_Analysts
emailNotificationEnabled: true
escalationDuration: P14D
mailSubjectPrefix: [BA Community]
escalationGroup: Senior_Business_Analysts
```

**Characteristics:**
- Longer response window (2 weeks)
- Community-based support
- Escalation to senior members

**Best For:**
- Advisory/consulting model
- Non-urgent questions
- Knowledge-sharing communities

---

## Use Case Scenarios

### Scenario 1: Multi-Region Organization

**Context:**
- Global organization with teams in APAC, EMEA, and Americas
- Each region has its own data steward team
- Need region-specific workflows

**Solution:** Deploy three workflow instances

**APAC Configuration:**
```
recipientGroup: Data_Stewards_APAC
emailNotificationEnabled: true
escalationDuration: P5D
mailSubjectPrefix: [Collibra APAC]
escalationGroup: DG_Council_APAC
```

**EMEA Configuration:**
```
recipientGroup: Data_Stewards_EMEA
emailNotificationEnabled: true
escalationDuration: P5D
mailSubjectPrefix: [Collibra EMEA]
escalationGroup: DG_Council_EMEA
```

**Americas Configuration:**
```
recipientGroup: Data_Stewards_Americas
emailNotificationEnabled: true
escalationDuration: P5D
mailSubjectPrefix: [Collibra Americas]
escalationGroup: DG_Council_Americas
```

**Implementation:**
1. Upload workflow three times (or use workflow key variants)
2. Configure each instance separately
3. Name workflows: "Message Workflow - APAC", "Message Workflow - EMEA", etc.
4. Users select appropriate workflow when starting

**Benefits:**
- Region-appropriate response times
- Localized escalation paths
- Regional accountability
- Email subjects indicate region

---

### Scenario 2: Priority-Based Workflows

**Context:**
- Organization wants different response times based on priority
- High priority: 1 day, Medium: 3 days, Low: 7 days

**Solution:** Deploy three workflow instances by priority

**High Priority Workflow:**
```
recipientGroup: Data_Stewards
emailNotificationEnabled: true
escalationDuration: PT24H
mailSubjectPrefix: [URGENT - Data Governance]
escalationGroup: Senior_Data_Stewards
```
**Workflow Name:** "Message Workflow - High Priority"

**Medium Priority Workflow:**
```
recipientGroup: Data_Stewards
emailNotificationEnabled: true
escalationDuration: P3D
mailSubjectPrefix: [Data Governance]
escalationGroup: Senior_Data_Stewards
```
**Workflow Name:** "Message Workflow - Medium Priority"

**Low Priority Workflow:**
```
recipientGroup: Data_Stewards
emailNotificationEnabled: true
escalationDuration: P7D
mailSubjectPrefix: [Data Governance - Low Priority]
escalationGroup: Senior_Data_Stewards
```
**Workflow Name:** "Message Workflow - Low Priority"

**User Guidance:**
- Provide clear criteria for selecting priority level
- Train users on appropriate usage
- Monitor for priority inflation over time

**Alternative:** Single workflow with dynamic escalation (requires BPMN modification)

---

### Scenario 3: Domain-Specific Support

**Context:**
- Different data domains have specialized support teams
- Finance Data, Customer Data, Product Data, etc.

**Solution:** Multiple workflow instances per domain

**Finance Data Support:**
```
recipientGroup: Finance_Data_Stewards
emailNotificationEnabled: true
escalationDuration: P3D
mailSubjectPrefix: [Finance Data]
escalationGroup: Finance_Data_Council
```

**Customer Data Support:**
```
recipientGroup: Customer_Data_Stewards
emailNotificationEnabled: true
escalationDuration: P5D
mailSubjectPrefix: [Customer Data]
escalationGroup: Customer_Data_Council
```

**Product Data Support:**
```
recipientGroup: Product_Data_Stewards
emailNotificationEnabled: true
escalationDuration: P7D
mailSubjectPrefix: [Product Data]
escalationGroup: Product_Data_Council
```

**Benefits:**
- Domain expertise matched to questions
- Appropriate escalation paths
- Clear accountability per domain
- Domain-specific SLAs

---

### Scenario 4: Test Environment Configuration

**Context:**
- Need to test workflow without sending emails to production users
- Want fast escalation for testing purposes

**Test Environment Configuration:**
```
recipientGroup: Test_Data_Stewards
emailNotificationEnabled: false
escalationDuration: PT1H
mailSubjectPrefix: [TEST - Collibra]
escalationGroup: Test_Escalation_Group
```

**Additional Test Setup:**
1. Create test user groups with test accounts only
2. Use test email addresses (e.g., test@example.com)
3. Short escalation duration for fast testing
4. Clear [TEST] prefix to avoid confusion
5. Disable emails or use test SMTP server

**Testing Checklist:**
- [ ] Test workflow start
- [ ] Test receiver receives task
- [ ] Test receiver reply flow
- [ ] Test sender response flow
- [ ] Test escalation (wait 1 hour or adjust duration)
- [ ] Test closure path
- [ ] Test comment saving (if using test assets)
- [ ] Verify all emails sent (if enabled)

---

## Multi-Environment Setup

### Development Environment

**Purpose:** Developer testing, frequent changes

**Configuration:**
```
recipientGroup: Dev_Test_Group
emailNotificationEnabled: false
escalationDuration: PT15M
mailSubjectPrefix: [DEV - Collibra]
escalationGroup: Dev_Test_Group
```

**Characteristics:**
- Very short escalation (15 minutes)
- Emails disabled (reduces noise)
- Same group for escalation (simplified)
- Clear DEV prefix

---

### QA/Staging Environment

**Purpose:** User acceptance testing, pre-production validation

**Configuration:**
```
recipientGroup: QA_Data_Stewards
emailNotificationEnabled: true
escalationDuration: PT2H
mailSubjectPrefix: [QA - Collibra]
escalationGroup: QA_Escalation_Group
```

**Characteristics:**
- Short escalation (2 hours for testing)
- Emails enabled (validate email functionality)
- Separate escalation group
- Clear QA prefix

---

### Production Environment

**Purpose:** Live production use

**Configuration:**
```
recipientGroup: Data_Stewards_Prod
emailNotificationEnabled: true
escalationDuration: P7D
mailSubjectPrefix: [Collibra Q&A]
escalationGroup: Data_Governance_Council
```

**Characteristics:**
- Production-appropriate escalation timing
- Emails fully enabled
- Production groups
- User-friendly subject prefix

---

## Advanced Configurations

### Configuration 1: After-Hours Support

**Context:**
- Organization has 24/7 operations
- Different support teams for business hours vs. after-hours

**Solution:** Two workflow instances

**Business Hours Workflow:**
```
recipientGroup: Data_Stewards_Day_Shift
emailNotificationEnabled: true
escalationDuration: PT8H
mailSubjectPrefix: [Collibra - Business Hours]
escalationGroup: Senior_Data_Stewards
```
**Availability:** Mon-Fri, 8 AM - 5 PM

**After-Hours Workflow:**
```
recipientGroup: Data_Stewards_On_Call
emailNotificationEnabled: true
escalationDuration: PT2H
mailSubjectPrefix: [Collibra - URGENT]
escalationGroup: Data_Operations_Manager
```
**Availability:** Evenings, Weekends, Holidays

**Implementation:**
- Publish both workflows
- Document when to use each
- Consider workflow naming: "Data Question (Business Hours)" vs. "Data Question (Urgent - After Hours)"

---

### Configuration 2: Seasonal Adjustment

**Context:**
- Response capacity varies by season (e.g., slower during holiday season)

**Summer Configuration (Full Capacity):**
```
recipientGroup: Data_Stewards
emailNotificationEnabled: true
escalationDuration: P5D
mailSubjectPrefix: [Data Governance]
escalationGroup: DG_Council
```

**Holiday Season Configuration (Reduced Capacity):**
```
recipientGroup: Data_Stewards
emailNotificationEnabled: true
escalationDuration: P14D
mailSubjectPrefix: [Data Governance]
escalationGroup: DG_Council
```

**Process:**
1. Update escalationDuration in Settings → Workflows → Definitions
2. Communicate to users about expected response times
3. Adjust back after holiday season

---

### Configuration 3: Incident Management Integration

**Context:**
- High-priority issues should create tickets in external system
- Requires integration setup

**Configuration:**
```
recipientGroup: Data_Operations
emailNotificationEnabled: true
escalationDuration: PT4H
mailSubjectPrefix: [Data Incident]
escalationGroup: Incident_Management_Team
```

**Additional Setup:**
1. Configure webhook or API integration in BPMN
2. Service task to call incident management API
3. Trigger on High priority messages or escalations
4. Include workflow link in incident ticket

**Example Integration Point:**
- After "Notify Receiver" service task
- Add service task: "Create Incident Ticket" (conditional on priority == 'High')
- Use REST API delegate to call ServiceNow/Jira API

---

### Configuration 4: Multi-Language Support

**Context:**
- Global organization with multiple languages
- Email notifications should be localized

**Japanese Environment:**
```
recipientGroup: Data_Stewards_JP
emailNotificationEnabled: true
escalationDuration: P7D
mailSubjectPrefix: [コリブラQ&A]
escalationGroup: DG_Council_JP
```

**German Environment:**
```
recipientGroup: Data_Stewards_DE
emailNotificationEnabled: true
escalationDuration: P7D
mailSubjectPrefix: [Collibra Fragen]
escalationGroup: DG_Council_DE
```

**Note:** Email body templates need to be customized in BPMN file for full localization.

---

## Troubleshooting Configurations

### Troubleshooting 1: Email Overload

**Problem:** Receiver group complaining about too many emails

**Diagnostic Configuration:**
```
# Temporarily disable emails to test workflow without notifications
emailNotificationEnabled: false
```

**Alternative Solutions:**
1. Reduce number of email notification points (BPMN modification)
2. Implement email digest (daily summary instead of per-message)
3. Train users to use email filters
4. Rely more on in-app notifications

**Recommended Long-Term Fix:**
```
emailNotificationEnabled: true
# But educate users on email filtering:
# - Create rule: If subject contains "[Collibra Q&A]", move to folder
# - Set up digest view in email client
```

---

### Troubleshooting 2: Excessive Escalations

**Problem:** Too many workflows escalating, overwhelming escalation group

**Analysis:** Check if escalationDuration is too short

**Diagnostic Configuration:**
```
# Temporarily extend escalation duration
escalationDuration: P14D
```

**Monitor:**
- Track escalation rate over 2 weeks
- If rate decreases, duration was too short
- If rate stays high, issue is receiver group capacity/responsiveness

**Possible Fixes:**
1. Extend escalation duration permanently
2. Increase receiver group size
3. Provide training to receiver group
4. Improve knowledge base to reduce questions

---

### Troubleshooting 3: Wrong Group Receiving Tasks

**Problem:** Tasks not appearing for receiver group

**Check Current Configuration:**
```
recipientGroup: Data_Stewards
# Verify exact spelling, underscores, case
```

**Diagnostic Steps:**
1. Navigate to: Settings → Users & Groups → Groups
2. Copy exact group name from list
3. Update configuration with exact name

**Common Errors:**
```
# WRONG:
recipientGroup: data stewards  # Spaces instead of underscore
recipientGroup: data_stewards  # Lowercase (if actual group is Data_Stewards)
recipientGroup: DataStewards   # No underscore

# CORRECT:
recipientGroup: Data_Stewards  # Exact match
```

---

### Troubleshooting 4: Comments Not Saving

**Problem:** Comments not appearing on assets

**Check Configuration:**
```
# This workflow doesn't have config for comments,
# but verify in BPMN that addCommentDelegate is correct
```

**Diagnostic Steps:**
1. Check Collibra logs for AddComment errors
2. Verify user has permission to comment on asset
3. Verify asset ID is valid
4. Test with different asset types

**Temporary Workaround:**
- Workflow continues without comment saving
- Users can manually add comments
- Fix permission or configuration issue later

---

### Troubleshooting 5: Testing Escalation

**Problem:** Need to test escalation without waiting 7 days

**Test Configuration:**
```
# Set very short escalation for testing
escalationDuration: PT5M  # 5 minutes
```

**Testing Procedure:**
1. Start workflow
2. Wait 5 minutes without receiver responding
3. Verify escalation email sent
4. Verify escalated task created
5. After testing, restore production value:
   ```
   escalationDuration: P7D
   ```

**Alternative:** Use separate test workflow instance with short duration

---

## Configuration Templates

### Template 1: Small Organization

```
# Small org (< 100 users), single team
recipientGroup: Data_Team
emailNotificationEnabled: true
escalationDuration: P5D
mailSubjectPrefix: [Collibra Help]
escalationGroup: Data_Team_Lead
```

---

### Template 2: Medium Organization

```
# Medium org (100-1000 users), dedicated teams
recipientGroup: Data_Stewards
emailNotificationEnabled: true
escalationDuration: P7D
mailSubjectPrefix: [Data Governance]
escalationGroup: Data_Governance_Council
```

---

### Template 3: Large Enterprise

```
# Large enterprise (1000+ users), multiple regions
# Deploy per region with regional groups

# Example: Americas
recipientGroup: Data_Stewards_Americas
emailNotificationEnabled: true
escalationDuration: P7D
mailSubjectPrefix: [Collibra Americas]
escalationGroup: DG_Council_Americas
```

---

### Template 4: Agile/DevOps Team

```
# Fast-paced tech team with quick turnaround
recipientGroup: DevOps_Team
emailNotificationEnabled: true
escalationDuration: PT8H
mailSubjectPrefix: [DevOps Support]
escalationGroup: DevOps_Manager
```

---

## Configuration Decision Matrix

Use this matrix to choose appropriate configuration values:

| Organization Size | Response SLA | escalationDuration | Group Size |
|-------------------|--------------|-------------------|------------|
| Small (< 100) | 1 week | P7D | 3-5 |
| Small | 3 days | P3D | 3-5 |
| Medium (100-1000) | 1 week | P7D | 5-10 |
| Medium | 3 days | P3D | 5-10 |
| Large (1000+) | 1 week | P7D | 10-15 |
| Large | 1 day | PT24H | 10-15 |
| 24/7 Operations | 4 hours | PT4H | 15-20 (shifts) |
| Advisory/Non-urgent | 2 weeks | P14D | 3-5 |

| Question Type | Recommended escalationDuration |
|---------------|-------------------------------|
| Technical Support | PT8H - PT24H |
| Data Governance | P5D - P7D |
| Business Advisory | P7D - P14D |
| Urgent/Production Issues | PT2H - PT4H |
| Training/How-To | P7D - P14D |

---

## Best Practices Summary

1. **Start Conservative:** Begin with longer escalation durations, shorten if needed
2. **Test First:** Always test configuration in non-production environment
3. **Document Changes:** Keep record of configuration changes over time
4. **Monitor Metrics:** Track escalation rates, response times, closure rates
5. **User Communication:** Inform users of expected response times
6. **Regular Review:** Review configuration quarterly, adjust based on performance
7. **Consistency:** Use consistent naming and prefixes across workflows
8. **Group Maintenance:** Regularly review group memberships and permissions

---

For more information:
- [CONFIGURATION.md](../docs/CONFIGURATION.md) - Detailed configuration reference
- [INSTALLATION.md](../docs/INSTALLATION.md) - Installation guide
- [usage-scenarios.md](usage-scenarios.md) - Usage examples
