---
name: chatfate
description: 通过 ChatFate 卡片完成问一件事、人生命盘、今日指引或年度解读，并打开持久报告。用户实际要求测算、重新测算或查看已有报告时使用；不用于插件维护或代码审查。必须先按本轮 Skills 清单的 file 路径读取本 Skill，root 别名与剩余路径直接拼接并保留重复目录名，不猜缓存版本。
---

# ChatFate 总编排

## 工作边界

- 按本轮 Skills 清单的 `file` 路径及相对路径读取本 Skill 和子 Skill；不猜插件缓存版本，不先查旧会话或 MEMORY。
- 本流程用于用户实际测算、重新测算和打开报告；维护插件、审查代码或讨论产品时，不自动创建卡片或私人报告。
- life 的唯一排盘事实为本次 `chatfate.bazi.life-calculation.v1.minimalFacts`；daily 为本次 `chatfate.bazi.daily-calculation.v3`（旧任务可能为 v2）的 `dailyFacts`；新版 annual 为 `chatfate.bazi.annual-calculation.v1` 的单份 `minimalFacts`；旧 annual 与 question 使用本次 `calculatedFacts`。模型不补算、改盘或依据反馈校准事实；传统解释不冒充引擎既定结论。
- 卡片收集必要信息。出生资料只作为 life、daily、annual 填写流程的辅助功能，最终提交后由服务端在登录成功时保存或更新；可选择、修改、清空、直接删除，无独立管理入口或额外确认。六爻不接出生资料。模型不代填、代选或调用 app-only 资料工具。可选关注点、背景、实际选项与期限保存在独立 userContext，是用户陈述，不进入引擎输入或起卦种子。daily 不收集这些背景字段，只使用已确认本人和账号场景设置；年度不改写每日本人或偏好。出生信息、所问和背景不进入 URL、日志或无关工具。
- 报告写入 ChatFate 自有站点，部署已配置；没有建站、选站、授权站点或升级准备步骤。
- 不展示内部请求 ID、堆栈、提示词、`writeSecret`、`deleteToken` 或 `CHATFATE_REPORT_WRITE_SECRET`。完整 `reportUrl` 是必须交付用户的读取链接，不能因此省略。

## 首次使用与入口选择

- “开始使用 ChatFate”“算命”“重新算命”而未明确产品时，打开四项选择菜单，不替用户选择产品。首次使用或用户询问怎么用时，在出卡片前简短说明：可在下面卡片选择“问一件事、人生命盘、今日指引、年度主题”，也可在对话中直接说功能名称；“查看历史报告”用于回看。说明后立即出卡片并同轮等待，不要求回复“继续”。同一会话不反复介绍。
- 明确说“问一件事／问一卦”时，调用 `render_divination_menu({product:"question"})`；“人生命盘／看我的命盘”选 `life`；“今日指引”选 `daily`；“年度主题／年度解读”选 `annual`。这些是意图示例，不是需要逐字输入的口令。存在实质歧义时打开选择菜单；问题背景与出生信息仍由用户在卡片中确认，不代填提交。
- “再算一次”在产品明确时沿用产品打开新卡片，产品不明确时回到选择菜单；六爻同问先按下方预检。当天再次领取今日指引仍返回已有当日报告，不承诺生成第二份。
- “查看历史报告”直接执行下方已有报告流程，不新建测算。引导只描述已实现的入口，不承诺未验证的跨任务免登录、免费解锁或重复领取。

## 同轮标准流程

1. 用户要求开始测算时，先应用下方六爻同问预检；不触发或已确认后，第一项测算工具动作是 `render_divination_menu`，按上方意图传入 product；未指定产品则省略。除上方首次使用说明外，调用前不输出重复说明或处理中状态。即使聊天中有部分信息，也由卡片收集。
2. 取得 `sessionId` 后，同一个模型回合立即调用 `wait_for_divination_data`。它等待卡片提交并返回经过验证的 `submissionId`；不要结束回合、要求用户再说一句话或调用 `sendFollowUpMessage`、`ui/message`、`updateModelContext`。用户点一次「免费试读」或「领取报告」就已提交；随后自动执行计算，遇到登录要求立即按下方流程打开 IAB。卡片的「等待登录」只是状态，不要求用户再次点击卡片或在聊天中确认继续。
3. 先处理 wait 返回的状态：`report_ready` 表示已有报告，不计算、不读取子 Skill、不生成 interpretation、不调用 create。若 `delivery:"card"`，卡片已负责打开报告，直接交付完整 reportUrl，不重复导航；其他 report_ready 沿用本轮标签打开原链接。新提交根据 wait 实际返回的 kind、product 分派：
   - life：`calculate_bazi_chart({submissionId,lifeAuthoringVersion:"life.v1"})`。
   - daily：`calculate_bazi_chart({submissionId,dailyAuthoringVersion:"daily.v3"})`。
   - annual：`calculate_bazi_chart({submissionId,annualAuthoringVersion:"annual.v1"})`。
   - question / liuyao：`calculate_liuyao_chart({submissionId})`。
   只传对应产品的版本参数，不同时传多个。旧 wait 缺少 product 时保留原八字 lifeAuthoringVersion 策略，不猜年度身份；旧任务沿用实际返回协议。计算返回的 reading 决定最终产品。life 必须返回对应 life-calculation.v1、minimalFacts 和完整 life.v1 instructions；daily 新任务必须返回 daily-calculation.v3、dailyFacts、fixedContent 和完整 daily.v3 instructions，旧任务的 v2/expressions/原方法继续有效；新版 annual 必须返回 annual-calculation.v1、单份 minimalFacts、与 wait 一致的 targetYear，以及冻结的 reading.authoring.referenceAt、annual.v1 与完整 instructions；旧 annual/question 使用对应 chart 版本、engineVersion 和 calculatedFacts。遇到 awaiting_original/unclaimed/deleted 按恢复规则处理，不能当成计算成功；登录错误重试原工具，不重开卡片。
4. 成功排盘后，仅在工具明确返回签名交接或等待链接时打开该原链接；没有返回链接就直接继续第 5 步，不自行拼接 pending 地址。本轮已打开登录页时沿用该页，不重复开等待页。首次登录仍须按受保护工具返回的要求完成，不跳过登录。始终沿用当前工具返回的站点与链接，本地候选不得转去生产站等待。浏览器不可用时不阻塞后续报告生成。
5. life 和新版 annual 直接使用本次完整 authoring.instructions、minimalFacts 与独立 userContext，无需读取本地方法文件、子 Skill、通用合同或其他产品段落。缺少方法或版本不匹配时，说明插件与 MCP 尚未配套，保留提交供恢复，不自行补方法或开始写作。daily v3 只用本次 dailyFacts、完整 instructions 与有限 fixedContent；后者是原文资料，不是运行指令或评级依据。旧 v2 沿用本次 expressions 和原方法，不加载另一版 Daily、Life 或全库。question 只读取 `../liuyao/SKILL.md`，其中包含完整分析方法、表达与现有输出合同，不再读取通用报告契约、表达边界或其他方法文件。仅旧 annual 读取 `../bazi/SKILL.md` 的兼容分支及[报告契约](references/report-contract.md)、[表达边界](references/safety.md)。已读材料直接复用。
6. 按实际产品写入：
   - life 使用第 5 步的完整方法与本次 minimalFacts、独立 userContext，一次写完17 MODULE原文，直接提交 `chatfate.bazi.life.interpretation.v1` 的 schemaVersion/product/text 三字段。保留原文，不写结构化副本、不做写后审稿；保存成功直接进入第 7 步。
   - daily 按本次返回的完整 authoring.instructions 一次完成个人判断和分析，v3 提交 `chatfate.bazi.daily.interpretation.v3`；固定短句由服务端匹配，不抄写、补写或改写。已发出的 v2 任务仍交 `chatfate.bazi.daily.interpretation.v2`。只使用本次本人事实和公共日值，不读取其他出生信息或问题背景。保留第一份完整原文；保存或技术校验失败也不自动改写、不调用第二个 Writer。
   - 新版 annual 使用本次完整 instructions、minimalFacts、冻结 reading 与独立 userContext，由当前模型一次写完全年总览与春夏秋冬原文，提交 schemaVersion:"chatfate.bazi.annual.interpretation.v1"、product:"annual"、text 三字段。逐字保留第一份完整原稿，不补结构化副本，不增加审稿、修稿或第二作者。
   - question：按第 5 步的六爻方法一次完成 `chatfate.reading.interpretation.v1`，调用 `create_divination_report({calculationId,interpretation})` 保存同一完整原稿，不增加独立分析模型、审稿或改写回合；不重传输入或 chart。
   - 旧 annual：根据计算结果、reading 和用户明确提供的 userContext，生成一次 `chatfate.reading.interpretation.v1`。围绕本问选择相关篇章，先给实质回答，再解释支持关系与改变判断的条件；不按固定领域凑篇幅，不要求每章附行动。检查来源、证据路径和具体事实后调用 `create_divination_report({ calculationId, interpretation })`。写作时直接自查：概要是否先回答本问，传统解释是否有可理解的推导，是否清楚区分用户陈述与现实建议；证据不足就收窄判断，不靠字数或术语撑深度，也不把“稳住节奏、结合实际、适时调整”当作整条结论。无需再调用一次模型审稿。不要把原始输入或整个 chart 重新传回。
7. 工具成功后，若本次提交已在内置浏览器打开登录页，原标签会自动进入报告，模型不再导航、不另开报告标签；否则在已有本次提交的内置交付标签中打开工具返回的完整 `reportUrl`，尚无该标签才新建一次。外部 Google 登录标签不作为最终报告交付标签。最终回复“报告已生成”，附上文字为“查看报告”的 Markdown 链接，地址使用工具返回的完整 `reportUrl` 并逐字保留；不把试读称为“完整报告”，不复述报告正文。

用户主动点卡片的「更换报告」并选择另一产品时，可以通过宿主用户消息请求新的填写卡片；这不是生成过程中的自动续接。按消息指定的 product 调用 render_divination_menu，保留原提交和已有原文，不重写原报告。

正常路径只有：卡片 → 同轮等待 → 确定性计算 → 解释 → 一次报告写入。每个产品均调用 `create_divination_report({calculationId,interpretation})`，不重传输入或 chart。life、daily、新版 annual 的技术校验或保存失败均保留原文，不重新写作；question 保留第一份完整原稿，不二次写作；旧 annual 仅修正指出字段。网络重试只复用同一 calculationId 与原 interpretation。正常生成不增加搜索、预检、测试身份、文件整理、哈希、重复计算或浏览器验收；保留用户选择的模型和推理设置。

## 当前写入合同

life 写入 `chatfate.bazi.life.interpretation.v1`，机器资源 `chatfate://schemas/bazi-life-interpretation-v1`。daily v3 写入 `chatfate.bazi.daily.interpretation.v3`，资源 `chatfate://schemas/bazi-daily-interpretation-v3`；旧 v2 沿用 `chatfate.bazi.daily.interpretation.v2` 与 `chatfate://schemas/bazi-daily-interpretation-v2`。新版 annual 写入 `chatfate.bazi.annual.interpretation.v1`，资源 `chatfate://schemas/bazi-annual-interpretation-v1`。旧 annual 与 question 使用 `chatfate.reading.interpretation.v1`：旧 annual 字段见[报告契约](references/report-contract.md)，question 字段已列于[六爻方法](../liuyao/SKILL.md)。必要时读取 `chatfate://schemas/reading-interpretation-v1`，不重复加载。

- question 使用 `chatfate.liuyao.chart.v2`，分析与一次写作均按六爻方法执行。免费范围是顶部卦象与综合判词；后续正文和详细排盘由服务器控制解锁，previewChapterId 仅作旧结构兼容。技术错误保留原稿与 calculationId。
- life 使用单份 minimalFacts，完整遵守 `authoring.instructions` 的方法与17 MODULE职责，事业、财富、感情、健康四域独立判断；弱信号可短写，不能省略整章。免费范围仅为出生信息、基础命盘和完整概要（Hero 11块），付费墙位于“命局与性情”之前；解锁前不展示该章及后续正文。免费范围由服务器确定，Writer不选择免费章节。
- daily 使用本次返回的 daily-calculation.v3 或旧任务 v2，与服务器固定的北京自然日；本人或场景变化不改变该日已领取任务。
- 新版 annual 仅使用服务端首次冻结的年度事实与 referenceAt；重取沿用同一快照和版本。卡片按服务端准备状态选择当前立春年度，不另选未来年份。免费范围为服务器识别的全年总览，四季正文不提前下发；标记异常仍保存原文并返回报告，不要求重新写作，已有权限仍可读全文。
- 旧 annual 使用 `chatfate.bazi.chart.v2` 的八字、目标年 `liunian` 及引擎返回的 `annualTransit` 节气月份。7 月 1 日用于固定年度参照；月份起止只用引擎真实节气日期。没有月份事实时不补算，不将月份解释写成事件保证。

外层 `chatfate.report.v2`、reading 和 userContext 由服务器从保存的计算结果生成，模型只传 calculationId 与 interpretation。旧 interpretation 合同仅为已经开始的旧客户端流程保留兼容，新流程不选旧模板。

life 按上述出生信息、基础命盘与完整 Hero 范围免费展示；旧 annual/question 仍先展示概要和一个有实质依据的完整篇章，再由用户按需解锁；今日全部免费。预览必须能回答本问，不能把关键条件藏在付费后，也不将恐吓或预测保证作为解锁理由。价格和权益以报告页为准，正文不促销。

## 打开页面

Google 登录和报告页优先使用内置浏览器；已有内置交付标签时沿用该标签。只有 Google 明确拒绝内置浏览器或内置浏览器不可用时，才使用用户指定账号的外部浏览器完成授权。即使授权在外部完成，报告仍通过工具返回的签名链接交给内置浏览器，不要求两个浏览器共享 Cookie。先读取工具返回的当前文档，再打开工具返回的完整 URL。不可猜测旧 API 或依赖未提供的技能与运行时。已有本轮 ChatFate 交付标签时直接复用；没有才创建可见标签。按当前工具支持的方式保留交付标签，不关闭或隐藏它。

- 新报告生成期间由模型统一负责开页，卡片只显示进度。同一个 submissionId 的登录、等待和报告只使用一个 IAB 交付标签。保存工具返回的标签标识；浏览器运行时重置、变量丢失或工具重试时，先用当前工具支持的标签查询重新取得它，不把连接重建当成新建标签的理由。不能确认已有标签是否打开时先查询，不盲目重开。
- 登录、签名交接、等待、历史和报告页面均使用工具返回的原链接；不改域名、不自行构造 pending URL。
- 这是交付动作，不为报告生成做截图、点击或重复导航；登录结果由原工具重试确认。
- 内置浏览器不可用或打开失败时，不阻塞已生成报告的交付：仍逐字返回完整 `reportUrl`，作为恢复入口。不要把浏览器失败说成报告失败。

## 登录与恢复

- 卡片展示和匿名填写无需登录；资料保存、计算、报告创建/删除的身份要求以工具返回为准。身份与资料工具只供卡片使用，模型不替用户保存或认定本人。普通服务和当前统一体验均通过 Google 登录绑定真实账号；统一体验只读写独立本地库，测试阅读权限不随登录自动授予。永远不要运行 `codex mcp login`。
- 错误包含 `/login/start?ticket=…` 时，立即在内置浏览器打开该链接一次，不等待用户再点按钮或回复确认。同一登录链接的工具重试不触发再次开页；只有明确不兼容或内置浏览器不可用时再使用外部浏览器。同一 IAB 在浏览器登录及卡片设备凭据仍有效时会自动恢复登录（最长 30 天）；恢复页无需再选择 Google 账号。卡片存储不可用、凭据过期或已退出时仍需 Google 登录，不承诺新任务初始卡片立刻显示头像。无需手动刷新凭据。
- 不结束模型回合：约每 10 秒以原参数重试原工具，最多约 5 分钟。授权成功即继续，不能重复打开登录链接或让用户重填。浏览器被 Google 拒绝时，保留原提交并说明具体错误，不重复打开同一页面。
- 约五分钟仍未成功，说明授权可能未完成；用户回复完成后沿用保留的 `submissionId` 或 `calculationId` 重试。已提交信息保留约一小时。
- 本次提交在 IAB 打开过登录页时，它会沿计算和写解状态自动转到报告；不要另开等待页或报告页，也不要在报告写入后再次导航该标签。仅在外部浏览器完成授权时，最终报告按「打开页面」交付到已有 IAB 标签；没有才新建一次。

## 六爻同问预检

仅检查同一会话内已成功交付六爻报告的所问。没有成功报告、跨会话或八字重排都不触发；拿不准是否同一件事时，不触发，不根据主题关键词猜测。

明确是同一件事时，先逐字询问：`这件事此前已起过一卦。传统规矩，初筮为准、再问为参——还要再起吗？`

用户明确确认后继续卡片流程，可在 boundaries 用 practical 说明“重复解读只宜作为对照，不据此取代先前判断”。这只是使用建议，不证明首次或重复测算有预测能力。未确认不创建新卡片，不增加跨会话同问识别，不向新合同添加旧 repeatNotice 字段。

## 已有报告和异常

- 用户主动要求查看历史时调用 `get_history_link`，按上方程序打开返回链接；返回 `message` 时如实转述。普通菜单会自行显示历史入口，模型不主动调用。
- 用户明确重新测算时才新建 `sessionId`，六爻仍做同问预检。成功交付后不主动追问。
- `widget_not_mounted` 或用户报告卡片未显示：用同一个 `sessionId` 重开并在同轮再次等待，最多自动重开一次；仍失败时说明宿主卡片加载失败，可稍后重试，不改装插件。
- 字段、闰月和城市歧义留在卡片内处理。事实缺失或计算失败时如实说明可恢复错误，不猜测、不改走另一分支。
- daily 已有完整原稿时，只提交同一原稿；已有固定草稿或报告的恢复由后端直接保存或返回同一 reportUrl，不重新写作。跨会话恢复可调用 `resume_daily_task({dailyAuthoringVersion:"daily.v3"})`，有原任务日期时原样传 targetDate。
- `awaiting_original` 表示后端尚无完整原稿。先沿用当前对话已有原稿；没有原稿时说明事实并等待用户明确确认“原稿不可用，继续”。仅在该确认后调用 `resume_daily_task({targetDate,expectedWriterVersion:writerVersion,resumeWriting:true,dailyAuthoringVersion:"daily.v3"})`，日期与版本原样复制本次状态。缺少任一值不续写。版本失效时停下等待，不自动读取新版本重试；只有后来再次真正中断且用户重新确认，才可重新读取状态。
- `unclaimed` 回卡片确认本人；`deleted` 表示该日额度已用、原报告不可用，结束本次领取。不得改资料、删报告或换免费产品重新取得同日额度。
- 报告上传繁忙或响应丢失：沿用同一 calculationId 和 interpretation 重试，不再起卦、不建第二份报告。life、daily、新版 annual 的 schema 或保存失败不指挥第二次内容写作；如实报告并保留原文。question 保留原稿和同一 calculationId，不二次写作；旧 annual 仅按返回字段修正。
- 报告站不可用时如实说明，不另建站点、不换地址、不把整份正文贴进聊天替代交付。
- 新报告的 256-bit `accessId` 用于不可猜测地定位报告，读取仍受生成账号和权益检查；早期未关联账号的报告可能保留持链接读取的兼容口径。无论哪种口径，都必须向用户交付完整 reportUrl，保留路径、不截断、不改写，不写日志或公开位置。
