# 💬 Message Workflow V3 - Collibra SaaS向けメッセージワークフロー

A simple Q&A workflow for Collibra SaaS with Resource Role-based assignment - ask questions, get answers, done! ✨

## 🎯 What Does It Do?

This workflow lets anyone in your organization:
- 📝 Post questions about data assets from Actions button
- 💬 Chat back and forth with data stewards (bidirectional messaging)
- 🔔 Get email notifications
- ⏰ Auto-escalate if no one responds
- 🆙 Manual escalation to different groups
- 📌 Save conversation history in workflow variables
- 📊 Track status: New → In Progress → Closed

## ⚠️ 前提条件 (Prerequisites)

### 必須設定 (Required Setup)

**Collibra SaaS でワークフローを実行するには、以下の設定が必須です:**

#### 1️⃣ Resource Role の作成
```
Settings > Governance > Operating Model > Resource Roles
```

新しいResource Roleを作成:
- **Role名**: `Message Receiver` (または任意の名前)
- **説明**: ワークフローのメッセージ受信者ロール

#### 2️⃣ Resource Permission の設定
```
Settings > Security > Permissions > Resource Permissions
```

作成したResource Role (`Message Receiver`) に以下の権限を付与:
- ✅ **Manage Workflow** (ワークフロータスクの表示・完了に必須)
- ✅ **Add Comment** (コメント追加が必要な場合)
- ✅ **View Resource** (アセット閲覧権限)

#### 3️⃣ Responsibilities の割り当て

**重要:** 送信者・受信者を問わず、ワークフローに参加する**全ユーザーまたはグループ**に対して:

```
対象アセットの詳細画面 > Responsibilities タブ
```

1. **+ Add Responsibility** をクリック
2. Role: `Message Receiver` を選択
3. User/Group: 参加者を選択
4. Save

**例:**
```
Asset: Customer Table
Responsibilities:
  - Message Receiver: John Smith (送信者)
  - Message Receiver: Data Stewards Group (受信者グループ)
  - Message Receiver: DG Council (エスカレーション先)
```

### ⚠️ 設定しないとどうなる？

- ❌ ワークフロータスクが表示されない
- ❌ "Access Denied" エラーが発生
- ❌ ワークフローが正常に動作しない

**必ずすべての参加者にResponsibilityを設定してください！**

## 🌟 主要機能 (Key Features)

| Feature | Description |
|---------|-------------|
| 💬 **双方向メッセージング** | 送信者⇔受信者グループ間で無制限に会話可能 |
| 🆙 **エスカレーション機能** | 受信者が別グループへ手動転送可能 |
| ⏰ **自動エスカレーション** | 7日間無応答で自動的に上位グループへ |
| 📧 **メール通知** | 全参加者へ自動通知 |
| 💾 **会話履歴の記録** | ワークフロー変数内に完全保存 |
| 📊 **ステータス管理** | New → In Progress → Closed の状態遷移 |
| 🎯 **Actionsボタン対応** | アセット詳細画面から直接起動可能 |
| 👥 **Resource Role対応** | Collibra SaaS標準のロールベース割り当て |

## ⚡ Quick Start

### 1️⃣ 前提条件の確認

上記「前提条件」セクションの設定を完了してください:
- ✅ Resource Role `Message Receiver` 作成済み
- ✅ Manage Workflow 権限付与済み
- ✅ 対象アセットのResponsibilities設定済み

### 2️⃣ Upload to Collibra
```
Settings → Workflows → Definitions → Upload Workflow
```
Choose: `workflow/message-workflow-v3-role-based.bpmn`

**重要:** アップロード後、必ず **Enable** ボタンをクリックしてください！

### 3️⃣ Refresh Browser
ブラウザを完全にリフレッシュ:
- Windows: `Ctrl + Shift + R`
- Mac: `Cmd + Shift + R`

**Note:** リフレッシュしないとActionsボタンに表示されません

### 4️⃣ Start Using!

**方法1: Actionsボタンから (推奨)**
1. Responsibilitiesが設定された任意のアセット詳細画面を開く
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
- 👥 Receiver Role (受信者ロール名 - デフォルト: Message Receiver)
- 🆙 Escalation Role (エスカレーション先ロール名)

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
│   Role      │      │  (You!)      │
└──────┬──────┘      └──────────────┘
       │                     ↑
       ↓                     │
┌─────────────┐             │
│ Escalation  │─────────────┘
│   Role      │
└──────┬──────┘
       │
       ↓
┌─────────────┐
│  Resolved!  │
│   Closed    │
└─────────────┘
```

**💡 Tip:** All participants must have the Resource Role assigned via Responsibilities!

## 🔧 技術的制約 (Technical Constraints)

### Collibra SaaS BPMNワークフローの制限

Collibra SaaSでは、以下の割り当て方法は**使用できません**:

❌ **使用不可:**
- Email での直接割り当て: `flowable:assignee="${user.email}"`
- Username での直接割り当て: `flowable:assignee="${user.userName}"`
- User ID での直接割り当て: `flowable:candidateUsers="${userId}"`
- グループ名での直接割り当て: `flowable:candidateGroups="${groupName}"`

✅ **使用可能:**
- **Resource Role**: `flowable:candidateUsers="{role(Message Receiver)}"`
- **Resource Role (レベル指定)**: `flowable:candidateUsers="{role(Message Receiver;0)}"`
- **Global Role**: `flowable:candidateUsers="{role(Admin)}"`

### 正しい構文

```xml
<!-- Resource Roleを使用（推奨） -->
<userTask id="receiverTask" name="Review and Respond"
  activiti:candidateUsers="{role(Message Receiver)}">
</userTask>

<!-- レベル指定も可能 -->
<userTask id="escalatedTask" name="Handle Escalation"
  activiti:candidateUsers="{role(Message Receiver;1)}">
</userTask>
```

### なぜ Resource Role が必要か

Collibra SaaS のセキュリティモデルでは:
1. ワークフロータスクはアセットに紐づく
2. アセットへのアクセスはResponsibilityで制御
3. Responsibilityは Resource Role を通じて付与
4. Resource Role に必要な Permission を設定

**つまり:** Resource Role = アクセス権限のコンテナ

## ⚙️ ロール名の設定方法

### 方法1: BPMNファイルを編集 (推奨)

`workflow/message-workflow-v3-role-based.bpmn` を編集:

**受信者ロール (Line 34付近):**
```xml
<!-- Before -->
<activiti:formProperty id="receiverRole" name="Receiver Role"
  type="string" required="true" default="Message Receiver"/>

<!-- After (your role name) -->
<activiti:formProperty id="receiverRole" name="Receiver Role"
  type="string" required="true" default="Your_Custom_Role"/>
```

**エスカレーションロール (Line 35付近):**
```xml
<activiti:formProperty id="escalationRole" name="Escalation Role"
  type="string" required="false" default="Message Receiver"/>
```

**重要:** ロール名は完全一致が必要（大文字小文字、スペース、記号など）

### 方法2: ワークフロー開始時に指定

毎回フォームでロール名を入力できます（デフォルト値が入力されています）

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
1. Admin sets up:
   - Resource Role: "Message Receiver"
   - Assigns to: Sarah, Data Stewards Group
   - On asset: "Customer Data"

2. Sarah opens "Customer Data" asset
3. Clicks Actions → Message Workflow
4. Title: "How do I update asset attributes?"
5. Message: "Can't find the edit button..."
6. Priority: Medium
7. Receiver Role: Message Receiver (default)
→ Data Stewards (with Message Receiver role) get email
→ John (Data Steward) replies: "You need Edit permission"
→ Sarah: "Thanks! It works now!"
→ Closed ✓
→ Complete conversation saved in workflow variables
```

**Tom has urgent issue with escalation:**
```
1. Admin sets up roles on "Email Database" asset:
   - Message Receiver: Tom, Data Stewards, DBA Team

2. Tom: "Data quality issue - null emails"
   Priority: High
   Receiver Role: Message Receiver
→ Data Steward investigates
→ Can't solve alone → Escalates
→ DBA Team (with Message Receiver role) receives
→ DBA Team provides solution
→ Closed ✓
```

## 🛠️ Technical Specs

**Built With:**
- 🔧 BPMN 2.0
- 🌊 Activiti Engine (Collibra API v2)
- 🎯 Collibra SaaS (tested on 5.x)
- 👥 Resource Role-based assignment

**Contains:**
- 1 Start Event (with formKey="resource")
- 3 Script Tasks (Groovy)
- 10 Service Tasks
- 3 User Tasks (Role-based)
- 7 Gateways
- 33 Flows
- 1 End Event

**API Compatibility:**
- Uses `activiti` namespace (API v2 compatible)
- Supports resource-based workflow launch
- Full Collibra SaaS integration
- Resource Role syntax: `{role(RoleName)}`

## 🚀 Features at a Glance

✅ Launch from Actions button on any asset
✅ Resource Role-based assignment (Collibra SaaS standard)
✅ Bidirectional messaging (unlimited replies)
✅ Manual escalation to different roles
✅ Auto-escalation after 7 days
✅ Email notifications to all participants
✅ Conversation history in workflow variables
✅ Status tracking (New/In Progress/Closed)
✅ Works with any asset type
✅ Proper permission management via Responsibilities

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
- Proper access control via Resource Roles
- Collibra SaaS-compatible workflows
- Simple workflow without complex setup
- Actions button integration for easy access

## 📦 Project Structure

```
collibra-message-workflow/
├── 📄 README.md                              ← You are here!
├── 📁 workflow/
│   ├── message-workflow-v3-role-based.bpmn  ← V3 (Current - Recommended)
│   ├── message-workflow-practical.bpmn      ← V2 (Deprecated)
│   └── message-workflow.bpmn                ← V1 (Legacy)
├── 📁 docs/
│   ├── INSTALLATION.md                      ← Setup guide
│   ├── CONFIGURATION.md                     ← Config reference
│   └── ARCHITECTURE.md                      ← Technical docs
└── 📁 examples/
    ├── configuration-examples.md            ← Config scenarios
    └── usage-scenarios.md                   ← How-to guides
```

## 🎓 Quick Tips

💡 **Resource Role は必須:**
- 全参加者にResponsibilityでロール割り当て
- 忘れるとタスクが表示されない
- アセットごとに設定が必要

💡 **Launch from Actions button:**
- Context is automatically captured
- Related asset auto-populated
- Faster than Start Workflow menu

💡 **Choose the right priority:**
- 🔴 High: Blocking work, urgent
- 🟡 Medium: Important, not urgent
- 🟢 Low: Nice to know

💡 **Use clear titles:**
- ✅ "How to assign data owners to this table?"
- ❌ "Help needed"

## 🤔 FAQ

**Q: タスクが表示されません (Tasks not appearing)**
A: 対象アセットのResponsibilitiesで、あなたに対してResource Role (Message Receiver) が割り当てられているか確認してください。

**Q: "Access Denied" エラーが出ます**
A: Resource Role に "Manage Workflow" 権限が付与されているか確認してください (Settings > Security > Permissions)。

**Q: Can anyone start this workflow?**
A: Yes, but they must have the Resource Role assigned via Responsibilities on the target asset.

**Q: How do I make it appear in Actions button?**
A: Upload the workflow, click Enable, then refresh browser (Ctrl+Shift+R or Cmd+Shift+R).

**Q: What happens if no one responds?**
A: Auto-escalates after 7 days to users with the escalation role.

**Q: Can I change the escalation time?**
A: Yes, edit the boundary timer in BPMN (default: `<timeDuration>P7D</timeDuration>`).

**Q: Where is conversation history saved?**
A: In workflow variables (not in asset comments). View in workflow instance details.

**Q: Why not use Groups instead of Roles?**
A: Collibra SaaS requires Resource Roles for workflow task assignment. Groups alone don't work.

**Q: Can I use the same role for multiple assets?**
A: Yes! Assign the same Resource Role to different users on different assets.

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
- Groovy ScriptTask を使用しているため
- Collibra の警告検出ロジックがGroovyを古いAPIと誤認識
- 実際は activiti namespace (API v2) を使用

**Action Required:**
- 🟢 特に対応不要
- 動作に問題がある場合のみサポートに連絡

### 会話履歴の保存場所

**制限事項:**
- 会話履歴はワークフロー変数内のみに保存
- Collibraのアセットコメント欄には**保存されません**

**理由:**
- Resource Role-based 割り当てではコメント保存の権限管理が複雑
- ワークフロー変数の方が安全で確実

**代替方法:**
- ワークフローインスタンスの詳細から会話履歴を確認
- 必要に応じて手動でコメントを追加

### Collibra Version Compatibility

**Tested:**
- ✅ Collibra SaaS 5.x (推奨)
- ✅ Resource Role 機能が有効な環境

**Not Tested:**
- ⚠️ Collibra 6.0以降の動作は未保証
- ⚠️ On-Premise版での動作は未確認

**Future Support:**
- Collibra 6.0対応版は別途提供予定
- 必要に応じてBPMN更新を検討

### Other Known Limitations

- Email notifications require SMTP server configuration
- Role names must match exactly (case-sensitive)
- Browser refresh required after enabling workflow
- All participants must have Resource Role assigned via Responsibilities

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| タスクが表示されない | Responsibilitiesで全参加者にResource Role割り当て確認 |
| Access Denied エラー | Resource RoleにManage Workflow権限付与確認 |
| Actions button に表示されない | Enable後、ブラウザをCtrl+Shift+Rでリフレッシュ |
| Can't find workflow | Check it's enabled in Settings → Workflows |
| No email notifications | Verify email server configured in Settings |
| Can't upload BPMN | Check Collibra version (needs SaaS 5.x or 2024.05+) |
| Role not working | Verify role name exact match (case-sensitive) |
| API v1 warning appears | Ignore - workflow uses v2, Groovy causes false positive |
| Workflow doesn't start | Check user has Resource Role on target asset |
| Related asset not populated | Make sure you launched from Actions button |

## 📞 Need Help?

Check the detailed guides in the `docs/` folder:
- 🆘 Installation issues → [INSTALLATION.md](docs/INSTALLATION.md)
- ⚙️ Configuration help → [CONFIGURATION.md](docs/CONFIGURATION.md)
- 🏗️ Technical questions → [ARCHITECTURE.md](docs/ARCHITECTURE.md)

## 🔄 Version History

**V3 (message-workflow-v3-role-based.bpmn) - Current ✅**
- ✅ Resource Role-based assignment (Collibra SaaS compatible)
- ✅ Actions button integration (formKey="resource")
- ✅ Activiti namespace (API v2)
- ✅ Proper permission management
- ✅ Groovy script for variable management
- ✅ Conversation history in workflow variables
- ⚠️ Requires Responsibilities setup on assets

**V2 (message-workflow-practical.bpmn) - Deprecated**
- Actions button integration
- Group-based assignment (doesn't work in SaaS)
- Missing Responsibilities requirement

**V1 (message-workflow.bpmn) - Legacy**
- Basic workflow functionality
- Start Workflow menu only
- Flowable namespace

## 🎉 That's It!

Setup is simple:
1. Create Resource Role
2. Assign permissions
3. Set Responsibilities
4. Upload & enable workflow
5. Refresh browser
6. Start chatting! 😄

---

**Version:** 3.0.0 (Role-Based)
**License:** For use with Collibra Data Intelligence Platform
**Compatibility:** Collibra SaaS 5.x (Resource Role required)
**API:** Activiti (v2 compatible)
**Assignment:** Resource Role-based (Collibra SaaS standard)

Made with ❤️ for better data collaboration
