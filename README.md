# 💬 Message Workflow V2 - Collibra SaaS向けメッセージワークフロー

A simple Q&A workflow for Collibra SaaS - ask questions, get answers, done! ✨

## 🎯 What Does It Do?

This workflow lets anyone in your organization:
- 📝 Post questions about data assets from Actions button
- 💬 Chat back and forth with data stewards (bidirectional messaging)
- 🔔 Get email notifications
- ⏰ Auto-escalate if no one responds
- 🆙 Manual escalation to different groups
- 📌 Save conversation history automatically on assets
- 📊 Track status: New → In Progress → Closed

## 🌟 主要機能 (Key Features)

| Feature | Description |
|---------|-------------|
| 💬 **双方向メッセージング** | 送信者⇔受信者グループ間で無制限に会話可能 |
| 🆙 **エスカレーション機能** | 受信者が別グループへ手動転送可能 |
| ⏰ **自動エスカレーション** | 7日間無応答で自動的に上位グループへ |
| 📧 **メール通知** | 全参加者へ自動通知 |
| 💾 **会話履歴の自動記録** | アセットコメントとして完全保存 |
| 📊 **ステータス管理** | New → In Progress → Closed の状態遷移 |
| 🎯 **Actionsボタン対応** | アセット詳細画面から直接起動可能 |

## ⚡ Quick Start

### 1️⃣ Upload to Collibra
```
Settings → Workflows → Definitions → Upload Workflow
```
Choose: `workflow/message-workflow-practical.bpmn`

**重要:** アップロード後、必ず **Enable** ボタンをクリックしてください！

### 2️⃣ Refresh Browser
ブラウザを完全にリフレッシュ:
- Windows: `Ctrl + Shift + R`
- Mac: `Cmd + Shift + R`

**Note:** リフレッシュしないとActionsボタンに表示されません

### 3️⃣ Start Using!

**方法1: Actionsボタンから (推奨)**
1. 任意のアセット詳細画面を開く
2. 右上の **Actions** ボタンをクリック
3. **Message Workflow** を選択

**方法2: Start Workflowから**
1. Click **Start Workflow** → Choose **Message Workflow**

Fill in:
- 📋 Title (タイトル)
- ✍️ Your question (質問内容)
- ⚡ Priority (High/Medium/Low)
- 🏷️ Category (Question/Issue/Other)
- 🎯 Related Asset (関連アセット - Actionsから起動時は自動入力)
- 👥 Recipient Group (受信者グループ)
- 🆙 Escalation Group (エスカレーション先グループ)

Done! 🎉

## 🎭 How It Works

```
┌─────────────┐
│ User Posts  │
│  Question   │
│ (Actions)   │
└──────┬──────┘
       │
       ↓
┌─────────────┐      ┌──────────────┐
│  Receiver   │ ←→   │    Sender    │
│   Group     │      │  (You!)      │
└──────┬──────┘      └──────────────┘
       │                     ↑
       ↓                     │
┌─────────────┐             │
│ Escalation  │─────────────┘
│   Group     │
└──────┬──────┘
       │
       ↓
┌─────────────┐
│  Resolved!  │
│   Closed    │
└─────────────┘
```

**💡 Tip:** If receiver doesn't respond in 7 days, workflow auto-escalates!

## ⚙️ 受信者グループの設定方法

### 方法1: BPMNファイルを編集 (推奨)

`workflow/message-workflow-practical.bpmn` の **Line 34** を編集:

```xml
<!-- Before -->
<activiti:formProperty id="recipientGroup" name="Recipient Group"
  type="string" required="true" default="Data_Stewards"/>

<!-- After (your group name) -->
<activiti:formProperty id="recipientGroup" name="Recipient Group"
  type="string" required="true" default="Your_Group_Name"/>
```

**重要:** グループ名は完全一致が必要（大文字小文字、アンダースコアなど）

### 方法2: ワークフロー開始時に指定

毎回フォームで受信者グループを選択できます（デフォルト値が入力されています）

## 🔧 エスカレーション設定

エスカレーション先グループもLine 35で設定可能:

```xml
<activiti:formProperty id="escalationGroup" name="Escalation Group"
  type="string" required="false" default="Data_Governance_Council"/>
```

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

**Sarah needs help (from Actions button):**
```
1. Opens "Customer Data" asset
2. Clicks Actions → Message Workflow
3. Title: "How do I update asset attributes?"
4. Message: "Can't find the edit button..."
5. Priority: Medium
→ Data Stewards get email
→ John replies: "You need Edit permission"
→ Sarah: "Thanks! It works now!"
→ Closed ✓
→ Complete conversation saved as asset comments
```

**Tom has urgent issue with escalation:**
```
Title: "Data quality issue - null emails"
Priority: High
→ Data Steward investigates
→ Can't solve alone → Escalates to DBA Team
→ DBA Team provides solution
→ Multiple messages back and forth
→ Solution found
→ Closed ✓
```

## 🛠️ Technical Specs

**Built With:**
- 🔧 BPMN 2.0
- 🌊 Activiti Engine (Collibra API v2)
- 🎯 Collibra SaaS (tested on 5.x)

**Contains:**
- 1 Start Event (with formKey="resource")
- 3 Script Tasks
- 10 Service Tasks
- 3 User Tasks
- 7 Gateways
- 33 Flows
- 1 End Event

**API Compatibility:**
- Uses `activiti` namespace (API v2 compatible)
- Supports resource-based workflow launch
- Full Collibra SaaS integration

## 🚀 Features at a Glance

✅ Launch from Actions button on any asset
✅ Bidirectional messaging (unlimited replies)
✅ Manual escalation to different groups
✅ Auto-escalation after 7 days
✅ Email notifications to all participants
✅ Complete conversation history on assets
✅ Status tracking (New/In Progress/Closed)
✅ Works with any asset type
✅ No training needed

## 💡 Use Cases

- ❓ "How do I...?" questions from asset context
- 🐛 Report data quality issues for specific assets
- 📋 Request access or changes with full context
- 💬 Discuss data governance topics
- 🤝 Collaborate on specific data assets
- 🆙 Escalate complex issues to appropriate teams

## 🎯 Who Is This For?

**Perfect for organizations that want:**
- Centralized Q&A system with asset context
- Better communication between users & data teams
- SLA-based escalation with manual override
- Complete audit trails on assets
- Simple workflow without complex setup
- Actions button integration for easy access

## 📦 Project Structure

```
collibra-message-workflow/
├── 📄 README.md                           ← You are here!
├── 📁 workflow/
│   ├── message-workflow-practical.bpmn   ← V2 (Recommended)
│   └── message-workflow.bpmn             ← V1 (Legacy)
├── 📁 docs/
│   ├── INSTALLATION.md                   ← Setup guide
│   ├── CONFIGURATION.md                  ← Config reference
│   └── ARCHITECTURE.md                   ← Technical docs
└── 📁 examples/
    ├── configuration-examples.md         ← Config scenarios
    └── usage-scenarios.md                ← How-to guides
```

## 🎓 Quick Tips

💡 **Launch from Actions button:**
- Context is automatically captured
- Related asset auto-populated
- Faster than Start Workflow menu

💡 **Choose the right priority:**
- 🔴 High: Blocking work, urgent
- 🟡 Medium: Important, not urgent
- 🟢 Low: Nice to know

💡 **Use escalation wisely:**
- Manual: When you need different expertise
- Auto: Safety net for forgotten questions

💡 **Use clear titles:**
- ✅ "How to assign data owners to this table?"
- ❌ "Help needed"

## 🤔 FAQ

**Q: Can anyone start this workflow?**
A: Yes! Any logged-in Collibra user (if workflow is enabled).

**Q: How do I make it appear in Actions button?**
A: Upload the workflow, click Enable, then refresh browser (Ctrl+Shift+R or Cmd+Shift+R).

**Q: What happens if no one responds?**
A: Auto-escalates after 7 days to escalation group.

**Q: Can I change the escalation time?**
A: Yes, edit the boundary timer in BPMN line 120 (default: `<timeDuration>P7D</timeDuration>`).

**Q: Can receiver send it to a different team?**
A: Yes! Use the "Escalate" action and specify escalation reason.

**Q: Can conversations go back and forth?**
A: Yes! Unlimited replies until someone closes it.

**Q: Where is conversation history saved?**
A: All messages are saved as comments on the related asset (if specified).

## ⚠️ 既知の問題 (Known Issues)

### API v1 Warning Message

**Issue:**
Collibra画面にAPI v1の警告が表示される場合があります
```
"Workflow uses deprecated API v1"
```

**Impact:**
- ⚠️ 警告メッセージのみ（機能には影響なし）
- ✅ ワークフローは正常に動作します
- ✅ すべての機能が利用可能です

**Reason:**
- このワークフローは`activiti` namespace（API v2対応）を使用
- Collibraの警告検出ロジックの誤検知の可能性
- または一部のレガシー機能との互換性維持のため

**Action Required:**
- 🟢 特に対応不要
- 動作に問題がある場合のみサポートに連絡

### Collibra Version Compatibility

**Tested:**
- ✅ Collibra SaaS 5.x (推奨)
- ✅ Collibra 2024.05+

**Not Tested:**
- ⚠️ Collibra 6.0以降の動作は未保証
- API仕様変更により動作しない可能性があります

**Future Support:**
- Collibra 6.0対応版は別途提供予定
- 必要に応じてBPMN更新を検討

### Other Known Limitations

- Email notifications require SMTP server configuration
- Group names must match exactly (case-sensitive)
- Browser refresh required after enabling workflow

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| Actions button に表示されない | Enable後、ブラウザをCtrl+Shift+Rでリフレッシュ |
| Can't find workflow | Check it's enabled in Settings → Workflows |
| No email notifications | Verify email server configured in Settings |
| Can't upload BPMN | Check Collibra version (needs 2024.05+ or SaaS 5.x) |
| Groups not working | Verify group names are exact match (case-sensitive) |
| API v1 warning appears | Ignore - workflow uses v2, warning is false positive |
| Workflow doesn't start | Check user has permission to start workflows |
| Related asset not populated | Make sure you launched from Actions button |

## 📞 Need Help?

Check the detailed guides in the `docs/` folder:
- 🆘 Installation issues → [INSTALLATION.md](docs/INSTALLATION.md)
- ⚙️ Configuration help → [CONFIGURATION.md](docs/CONFIGURATION.md)
- 🏗️ Technical questions → [ARCHITECTURE.md](docs/ARCHITECTURE.md)

## 🔄 Version History

**V2 (message-workflow-practical.bpmn) - Current**
- ✅ Actions button integration (formKey="resource")
- ✅ Activiti namespace (API v2)
- ✅ Default recipient group setting
- ✅ Complete escalation implementation
- ✅ Enhanced conversation history

**V1 (message-workflow.bpmn) - Legacy**
- Basic workflow functionality
- Start Workflow menu only
- Flowable namespace

## 🎉 That's It!

Simple, right? Upload, enable, refresh, and start chatting from any asset!

Questions? Click Actions on any asset and ask! 😄

---

**Version:** 2.0.0 (Practical)
**License:** For use with Collibra Data Intelligence Platform
**Compatibility:** Collibra SaaS 5.x, Collibra 2024.05+
**API:** Activiti (v2 compatible)

Made with ❤️ for better data collaboration
