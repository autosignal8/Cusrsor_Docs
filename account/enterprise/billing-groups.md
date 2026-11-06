> 原文：https://cursor.com/docs/account/enterprise/billing-groups

# Billing Groups

[Billing groups](https://cursor.com/dashboard/members?subtab=billing-groups) 让 Enterprise 管理员理解并管理用户分组的支出。此功能适用于报告、内部费用分摊和预算。

对于跨已关联团队的组织级队列，参见[组织组](https://cursor.com/docs/enterprise/organization-groups.md)。对于 [Team directory groups](https://cursor.com/docs/account/teams/admin-api.md#team-directory-groups)，使用 Team Admin API 的 `/teams/directory-groups`，ID 为 `team_group_…`。Billing Group 路由仍在 `/teams/groups`，并使用 `group_…` ID。

## Billing group 架构

管理员可以把每个成员分配到一个 billing group。成员一次只能属于一个 billing group。没有被主动分配到任何其他 billing group 的成员，会被放入保留的 `Unassigned` 组。

所有用量都归属于发生当时用户所在的组。用户在组之间移动时，历史数据不会改变；只有在删除某个组时才能重新分配。在那种情况下，该组的全部用量会移到 Unassigned 组。

## 查看 billing groups

Enterprise 管理员可以在 Web 仪表板的 `Members & Groups` 选项卡中查看 billing groups。该表显示每个组、它的配置方式、其中的成员数量，以及该时段的支出。

![](/docs-static/images/account/enterprise/billing-groups/billing-groups-view.png)

## 创建 billing group 并添加成员

管理员点击 `Create Group` 创建 billing groups。为组命名后，有四种方式把成员分配到该组：

1. **SCIM**：把 billing group 与现有的 [SCIM 组](https://cursor.com/docs/account/teams/scim.md)同步。

2. **API**：通过 [Admin API](https://cursor.com/docs/account/teams/admin-api.md#billing-groups) 以编程方式创建组并添加成员。

3. **CSV**：上传包含组名和成员电子邮件地址的 CSV。

4. **手动**：点击 `Add Members`，手动选择要添加的 `Unassigned` 成员。

与 SCIM 同步的 billing groups 不能通过 CSV、API 或手动界面更改来编辑。SCIM 同步组的所有成员分配都必须通过 SCIM 处理。

## 在 billing groups 之间移动成员

管理员可以点击手动 billing group 并选择 `Move`，以移动其中的成员。

- **SCIM**：当成员在身份提供商中的 SCIM 组之间移动时，billing group 会自动跟随这些更改。
- **API**：使用[添加成员](https://cursor.com/docs/account/teams/admin-api.md#add-members-to-group)和[移除成员](https://cursor.com/docs/account/teams/admin-api.md#remove-members-from-group)端点，以编程方式移动成员。

## 重命名 billing group

可以在主菜单上点击齿轮按钮来重命名 billing groups，或在该 billing group 的页面上点击 `Rename`。

- **API**：使用[更新组](https://cursor.com/docs/account/teams/admin-api.md#update-group)端点以编程方式重命名组。

## 删除 billing group

可以在主菜单上点击齿轮按钮来删除 billing groups，或在该 billing group 的页面上点击 `Delete`。

- **API**：使用[删除组](https://cursor.com/docs/account/teams/admin-api.md#delete-group)端点以编程方式删除组。

删除 billing group 是破坏性操作；数据无法恢复。已删除组的全部历史用量会被追溯分配到 `Unassigned` 组。

### Billing groups 在 Enterprise 方案上可用

联系我们的团队，了解支出管理与报告。

---

## 站点地图

[所有文档页面概览](/llms.txt)
