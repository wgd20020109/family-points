# Prototype: family-points

版本：v0.1 草案

## 1. 项目概述

本项目为面向中小学生家庭的积分兑换亲子协作应用，目标帮助家长以合理、友爱的方式管理孩子的学习与娱乐，通过结构化家规、可审计积分台账与智能 Agent 辅助，实现家长与孩子双方对约定的严格执行与长期行为改善。

## 2. 目标用户与痛点
- 目标用户：中小学生家长 + 孩子（7–15 岁）。
- 痛点：家长难以持续、公平地兑现承诺；口头约定容易被遗忘或争议；缺乏长期行为数据以指导教育决策；担心机械式惩罚导致亲子关系紧张。

## 3. 核心价值
- 结构化约定、可审计兑现；
- 使用积分激励正向行为；
- AI Agent 协助验证、推荐与仲裁，减轻家长负担；
- 数据驱动持续优化奖励规则并保障孩子权益。

## 4. 关键功能模块
- 家庭仪表盘、孩子个人档案；
- 任务/约定创建（可签署电子家规）；
- 多模态任务提交（照片/语音/截图/成绩单）；
- 验证队列（家长 + Agent），可人工或自动处理；
- 积分台账与兑换商城；
- 分析/趋势与 Agent 改进建议；
- 隐私设置与合规模块（家长控制、数据导出、删除）。

## 5. 主要用户流程（示例）
- 流程 A：家长创建“日常作业”任务 → 孩子提交作业照片 → Agent 初审+家长确认 → 积分到账 → 孩子在商城兑换 30 分换 15 分钟手机时长 → 家长兑现记录。
- 流程 B：家长与孩子协商周末奖励规则并双方签署电子家规 → 规则生效并在仪表盘展示。
- 流程 C（纠纷）：孩子提交证据被拒 → 发起仲裁由 Agent 汇总证据、历史记录并输出可解释的调解建议与可信度评分 → 若双方仍不同意，生成可导出的仲裁报告以供第三方参考（例如教师）。

## 6. 数据模型（关键表）
- users (id, name, role[parent/child], email, created_at)
- families (id, name, created_at)
- family_members (family_id, user_id, role, joined_at)
- tasks (id, family_id, owner_id, title, type, points, repeat_rule, evidence_types, created_at, active)
- submissions (id, task_id, child_id, evidence_urls, submitted_at, status[pending/approved/rejected], verifier_id)
- points_ledger (id, family_id, user_id, delta, reason, related_submission_id, created_at)
- redemptions (id, user_id, item_id, points_cost, status, created_at)
- agreements (id, family_id, title, content, signatures, effective_from, expires_at)
- audit_logs (id, entity, action, actor_id, meta, timestamp)

## 7. 简要 API 草案
- POST /api/families — 创建家庭
- POST /api/families/{id}/tasks — 创建任务（含规则）
- GET /api/families/{id}/dashboard — 仪表盘数据
- POST /api/tasks/{id}/submissions — 上传证据（multipart + meta）
- POST /api/submissions/{id}/verify — 家长/Agent 验证（approve/reject + note）
- GET /api/users/{id}/ledger — 积分台账
- POST /api/redemptions — 下兑换单

## 8. Agent 架构与职责（高层）
- 验证 Agent（CV/文本/规则引擎）：初步判断提交证据是否合格（如照片中是否含作业页、成绩截图是否包含分数）。
- 推荐 Agent（个性化策略）：根据孩子历史数据与家庭偏好推荐任务积分和兑换比率，优化激励效果。
- 仲裁/调解 Agent：在争议发生时汇总证据、历史记录并输出可解释的调解建议与可信度评分。
- 通知/日程 Agent：按规则推送提醒、周期性总结与活动安排。
- 安全/合规 Agent（审计）：检测异常积分流动与潜在作弊行为并告警。

## 9. 隐私与合规（要点）
- 儿童数据最小化：只保存必要字段；敏感媒体可做短期存储并加密；家长可随时导出/删除数据。
- 本地化/混合部署选项：支持局部在家长设备加密存储关键证据；MiMo Desktop 若支持本地推理，可把敏感处理放在本地。
- 审计日志与可导出仲裁报告以满足监管与家长信任需求。

## 10. 测试与评估指标（KPI）
- 家长兑现率（兑现次数 / 应兑现次数）；
- 任务完成率；
- 孩子参与度（活跃天数）；
- 家庭满意度（周期性问卷）；
- 异常与争议率（被仲裁比例）；
- 模型判定准确率（人工标注对比）。

## 11. 开发计划与技术栈建议
- 前端：React Native / Flutter；后端：Node.js (Express) 或 Django；数据库：Postgres；缓存：Redis；媒体存储：S3 或私有对象存储；模型与 Agent：推理部署支持 OpenAI/HF/本地模型，CV 模型（轻量级物体/场景识别）用于初步验真。

## 12. 附：截图说明模板
请见 /screenshots/README.md，包含每张截图的文件名、说明文字与标注要点，便于直接用于内测申请佐证材料。
