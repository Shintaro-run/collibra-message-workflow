# 💬 Collibra Message Workflow

A simple Q&A workflow for Collibra - ask questions, get answers, done! ✨

## 🎯 What Does It Do?

This workflow lets anyone in your organization:
- 📝 Post questions about data assets
- 💬 Chat back and forth with data stewards
- 🔔 Get email notifications
- ⏰ Auto-escalate if no one responds
- 📌 Save conversation history on assets

## ⚡ Quick Start

### 1️⃣ Upload to Collibra
```
Settings → Workflows → Definitions → Upload Workflow
```
Choose: `workflow/message-workflow.bpmn`

### 2️⃣ Enable It
```
Settings → Workflows → Start Workflow → Enable "Message Workflow"
```

### 3️⃣ Start Using!
Click **Start Workflow** → Choose **Message Workflow**

Fill in:
- 📋 Title
- ✍️ Your question
- ⚡ Priority (High/Medium/Low)
- 🏷️ Category (Question/Issue/Other)
- 🎯 Related Asset (optional)
- 👥 Recipient Group
- 🆙 Escalation Group

Done! 🎉

## 🌟 Features

| Feature | Description |
|---------|-------------|
| 💬 **Bidirectional Chat** | Full conversation between sender & receiver |
| ⏰ **Auto-Escalation** | Escalates after 7 days if no response |
| 🆙 **Manual Escalation** | Receiver can escalate anytime |
| 📧 **Email Alerts** | Everyone gets notified |
| 💾 **Audit Trail** | Comments saved on assets |
| 📊 **Status Tracking** | New → In Progress → Closed |

## 🎭 How It Works

```
┌─────────────┐
│ User Posts  │
│  Question   │
└──────┬──────┘
       │
       ↓
┌─────────────┐      ┌──────────────┐
│  Receiver   │ ←→   │    Sender    │
│   Group     │      │  (You!)      │
└──────┬──────┘      └──────────────┘
       │
       ↓
┌─────────────┐
│  Resolved!  │
│   Closed    │
└─────────────┘
```

**💡 Tip:** If receiver doesn't respond in 7 days, workflow auto-escalates!

## 📚 Documentation

Need more details? Check these guides:

| Document | What's Inside |
|----------|---------------|
| 📖 [Installation Guide](docs/INSTALLATION.md) | Step-by-step setup |
| ⚙️ [Configuration Guide](docs/CONFIGURATION.md) | Customize settings |
| 🏗️ [Architecture Guide](docs/ARCHITECTURE.md) | Technical details |
| 📝 [Usage Scenarios](examples/usage-scenarios.md) | How to use it |
| 🎨 [Config Examples](examples/configuration-examples.md) | Real-world setups |

## 🎮 Example Usage

**Sarah needs help:**
```
Title: "How do I update asset attributes?"
Message: "Can't find the edit button..."
Priority: Medium
→ Data Stewards get email
→ John replies: "You need Edit permission"
→ Sarah: "Thanks! It works now!"
→ Closed ✓
```

**Tom has urgent issue:**
```
Title: "Data quality issue - null emails"
Priority: High
→ Data Steward investigates
→ Multiple messages back and forth
→ Solution found
→ Closed ✓
```

## 🛠️ Technical Specs

**Built With:**
- 🔧 BPMN 2.0
- 🌊 Flowable Engine
- 🎯 Collibra 2024.05+

**Contains:**
- 1 Start Event
- 3 Script Tasks
- 10 Service Tasks
- 3 User Tasks
- 7 Gateways
- 33 Flows
- 1 End Event

## 🎨 Workflow Components

### For Users 👤
- Post questions anytime
- Get expert answers
- Continue conversation until resolved

### For Data Stewards 👥
- Review questions
- Reply, Escalate, or Close
- Manage team workload

### For Admins ⚙️
- Email notifications
- Auto-escalation
- Complete audit trail
- Flexible configuration

## 🚀 Features at a Glance

✅ Simple to use
✅ No training needed
✅ Email notifications
✅ Auto-escalation (no question forgotten!)
✅ Complete conversation history
✅ Works with any asset type
✅ Configurable groups
✅ Status tracking

## 💡 Use Cases

- ❓ "How do I...?" questions
- 🐛 Report data quality issues
- 📋 Request access or changes
- 💬 Discuss data governance topics
- 🤝 Collaborate on data assets

## 🎯 Who Is This For?

**Perfect for organizations that want:**
- Centralized Q&A system
- Better communication between users & data teams
- SLA-based escalation
- Complete audit trails
- Simple workflow without complex setup

## 📦 Project Structure

```
collibra-message-workflow/
├── 📄 README.md                    ← You are here!
├── 📁 workflow/
│   └── message-workflow.bpmn      ← The workflow file
├── 📁 docs/
│   ├── INSTALLATION.md            ← Setup guide
│   ├── CONFIGURATION.md           ← Config reference
│   └── ARCHITECTURE.md            ← Technical docs
└── 📁 examples/
    ├── configuration-examples.md  ← Config scenarios
    └── usage-scenarios.md         ← How-to guides
```

## 🎓 Quick Tips

💡 **Choose the right priority:**
- 🔴 High: Blocking work, urgent
- 🟡 Medium: Important, not urgent
- 🟢 Low: Nice to know

💡 **Link to assets:**
- Adds conversation to asset comments
- Provides context for receivers
- Creates audit trail

💡 **Use clear titles:**
- ✅ "How to assign data owners?"
- ❌ "Help needed"

## 🤔 FAQ

**Q: Can anyone start this workflow?**
A: Yes! Any logged-in Collibra user (if enabled).

**Q: What happens if no one responds?**
A: Auto-escalates after 7 days to escalation group.

**Q: Can I customize the escalation time?**
A: Yes, edit the boundary timer in the BPMN (default: 7 days).

**Q: Do I need to link an asset?**
A: No, it's optional. But linking helps provide context.

**Q: Can conversations go back and forth?**
A: Yes! Unlimited replies until someone closes it.

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| Can't find workflow | Check it's enabled in Settings |
| No email notifications | Verify email server configured |
| Can't upload BPMN | Check Collibra version (needs 2024.05+) |
| Groups not working | Verify group names are exact match |

## 📞 Need Help?

Check the detailed guides in the `docs/` folder:
- 🆘 Installation issues → [INSTALLATION.md](docs/INSTALLATION.md)
- ⚙️ Configuration help → [CONFIGURATION.md](docs/CONFIGURATION.md)
- 🏗️ Technical questions → [ARCHITECTURE.md](docs/ARCHITECTURE.md)

## 🎉 That's It!

Simple, right? Upload, enable, and start chatting!

Questions? Start the workflow and ask! 😄

---

**Version:** 1.0.0
**License:** For use with Collibra Data Intelligence Platform
**Compatibility:** Collibra 2024.05+

Made with ❤️ for better data collaboration
