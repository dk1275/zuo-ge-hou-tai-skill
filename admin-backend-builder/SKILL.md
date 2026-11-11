---
name: admin-backend-builder
description: Build or repair a production-ready content-management backend with shared schemas, rich-text editing, server-enforced permissions, safe save/publish workflows, previews, media handling, and deployment verification. Use for admin portals that manage user-generated or editorial content; do not use for a simple static dashboard.
---

# 做个后台

把后台当成一条可审计的内容生产链，而不是一组 CRUD 页面。开始前读取项目地图和交接记录，只定位当前业务涉及的页面、数据集合、接口和部署目标。

## 工作方法

1. 先画出内容生命周期：草稿 → 待审 → 需要补充/驳回 → 已发布 → 下线；列出谁能读、写、审核、发布、删除。
2. 建立单一 schema：表单、回填、预览、payload、服务端白名单和发布校验必须来自同一份字段定义。禁止为手机端、预览端或 AI 端复制第二套保存逻辑。
3. 先实现服务端权限和数据校验，再接前端按钮。客户端隐藏按钮不是权限控制。
4. 对编辑保存做幂等和竞态保护：真实记录必须先读取；关键身份字段空值不覆盖；保存期间禁用按钮并使用请求锁；按 ID 替换本地缓存，不追加重复行。
5. 富文本和结构化模块采用可逆模型：保留原始结构、稳定模块 ID、顺序和未知字段策略；编辑器异常时保留草稿，不能静默清空。
6. H5/移动端实时预览必须直接消费规范化后的编辑态 payload，而不是复制一套字段或等待保存后再请求；输入、选择、模块排序和本地图片变化都应立即反映到预览。
7. 预览、H5、微信小程序和正式详情页共享渲染契约；图片用稳定媒体引用，未上传图片允许使用受控的本地临时预览，但不能写入正式数据。
8. 发布必须是显式动作，并在服务端再次校验标题、关联对象、正文/模块、配图、状态和时间；审核通过、正式内容和审核日志要原子更新或有补偿策略。
9. 部署后以 CLI/API 读取线上实际配置和版本摘要验收；“配置更新成功”不等于运行时、代码和权限已生效。

## 必读参考

- 需要设计内容模型、富文本、预览或回填时，读 [references/content-and-rich-text.md](references/content-and-rich-text.md)。
- 需要登录、角色、接口鉴权或 UGC 审核时，读 [references/permissions-and-review.md](references/permissions-and-review.md)。
- 需要保存、发布、上传、部署或回滚时，读 [references/reliability-and-release.md](references/reliability-and-release.md)。
- 交付前按 [references/acceptance-checklist.md](references/acceptance-checklist.md) 做最小验收，并区分“已实现、已验证、已部署、待人工验收”。

## 输出要求

交付时说明：改动文件、数据/接口边界、已验证项、已部署目标、尚未人工验收的项、回滚依据和剩余风险。不要把本地测试、云端部署或用户验收混写成同一个“完成”。
