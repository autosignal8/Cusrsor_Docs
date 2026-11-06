> 原文：https://cursor.com/docs/agent/design-mode

# Design Mode

Design Mode 让你通过在正在运行的应用上留下批注来指挥 Agent。在 [Agents Window](https://cursor.com/docs/agent/agents-window.md) 中打开浏览器，点击 **Annotate**，然后指向你想改的东西：一个元素、一块区域，或页面上的文字。Cursor 会把每条批注连同所需上下文发给 Agent，Agent 编辑代码，而你可以继续做下一处改动。

界面工作往往是空间性的。与其用一句话描述改动，你可以直接指向它。每条批注都包含你所选内容的细节和一张截图，因此 Agent 能找到正确的代码。

在正在运行的应用中点击一个元素，针对所选元素写提示，并让 Agent 编辑代码。

## 打开 Design Mode

Design Mode 位于 Agents Window 内的浏览器中。在浏览器工具栏点击 **Annotate**，或聚焦浏览器后按 Cmd + Shift + D。命令面板中的同一操作名为 **Toggle Design Mode**。

在你第一次打开 Design Mode 之前，**Annotate** 按钮会显示带有简短演示的 **Try Annotate** 预览。在你留下超过五条批注之后，按钮只显示图标。

页面顶部的提示行会显示每一步的手势。要退出，点击提示行中的退出按钮，再次按快捷键，或按 Esc。Esc 会先取消一笔标记、一次文字编辑或一条打开的批注，直到没有其他东西打开时才退出。

Annotate 在 Cursor 3.24.18 或更高版本中可用。更早的版本在浏览器工具栏中显示一支标为 **Design Mode** 的钢笔图标。

## 指挥 Agent 的方式

每条批注都从你在页面上选取的东西开始。

### 选择一个元素

点击任意元素以选中它并打开一条批注，然后输入改动。点击之前，按 Arrow Up 或 Arrow Down 把高亮移到该元素的父级或子级。

### 选择多个元素

按住 Shift 并点击更多元素，把它们加入同一条批注。当改动取决于元素之间的关系时，多选会有帮助。引用两个组件，并让 Agent 使其中一个与另一个一致、去掉重复内容，或一起调整一组元素。

选择多个元素，并描述它们应如何一起变化。

### 标记一块区域

在页面上拖动以框出一块区域，然后描述改动。按住 Shift 可将拖动锁定在一个轴上，或按 Option 从中心绘制方框。

标记区域时，提示行会显示标记工具 **Draw**、**Ellipse**、**Rectangle** 和 **Arrow**。圈出拥挤的部分，或指向需要处理的地方。按 Cmd + Z 撤销标记。

### 就地编辑文字

双击文字元素以在页面上编辑其文字。按 Return 保存编辑，或按 Esc 还原。文字编辑始终等待 [Send](https://cursor.com/docs/agent/design-mode.md#send-notes)。

### 编辑 CSS 属性

在针对单个元素的批注上，点击 **Edit CSS properties** 打开该元素的 CSS 检查器。筛选列表以找到属性并更改其值。Cursor 会把你的 CSS 编辑与批注一起发送。

### 口述批注

用说话代替打字来写批注。一旦批注有了文字或 CSS 编辑，它会显示 **Dictate** 按钮。点击它，或按住它说话。

把语音输入和绘图一起用来描述一处改动。

## 发送批注

批注卡片中的 **Batch edits** 开关控制每条批注是否单独发给 Agent。它默认关闭。

- **Batch edits 关闭。** 在元素或区域批注中按 Return，会立刻把它发给 Agent。按钮文字为 **Send note**。
- **Batch edits 开启。** 批注以带编号的图钉收集在页面上，浏览器工具栏中的 **Annotate** 会被带有计数的 **Send** 按钮替换。一次点击把所有待发送批注作为一条消息发送。

无论开关如何设置，文字编辑都始终等待 **Send**。打开 **Send** 旁边的列表以查看待发送批注，或用 **Clear All** 全部移除。发送所有待发送批注也会退出 Design Mode。如果你在仍有待发送批注时退出，**Send** 会留在工具栏中，直到你发送或清除它们。

在批注或文字编辑中按 Cmd + L，把它的内容放进聊天输入框，而不是发送它或留待 **Send**。

按住 Option 并点击元素，可把它直接加入聊天输入框，而不打开批注。在批注打开时，Option + 点击会把元素加入该批注。

## 键盘快捷键

| 操作 | 快捷键 |
| :---------------------------------------- | :--------------------- |
| Toggle Design Mode | Cmd + Shift + D |
| 把元素加入批注 | Shift + click |
| 高亮父元素或子元素 | Arrow Up 或 Arrow Down |
| 标记一块区域 | Drag |
| 在页面上编辑文字 | Double-click |
| 发送或保存批注 | Return |
| 把批注或文字编辑加入聊天输入框 | Cmd + L |
| 把元素加入聊天输入框 | Option + click |
| 撤销标记 | Cmd + Z |
| 取消当前步骤或退出 | Esc |

## Agent 会看到什么

你发送的每条批注都会给 Agent 你的文字和两类上下文：

- **元素细节。** 对每个选中的元素，Agent 会得到标签、id、类、属性、一条 CSS 风格的选择器路径、可见文字的前 200 个字符，以及元素的尺寸和位置。它还会得到六项计算样式：color、background color、font size、font family、display 和 position。在 React、Vue 和 Svelte 应用中，细节包括组件名和组件栈，因此 Agent 能找到源码。
- **一张截图。** 保存批注时，Cursor 会把截图裁剪到该元素，或裁剪到画有标记的区域。

## Canvas 选项卡

[Canvas](https://cursor.com/docs/agent/tools/canvas.md) 选项卡保留较旧的钢笔图标 **Design Mode** 开关。在那里，批注直接进入聊天，**Batch edits** 不适用，也不能就地编辑文字。拖动可自由绘制，或按住 Shift 并拖动以绘制方框。

## 在流程中工作

细化界面时，一处编辑通常会引出下一处。你调整一个组件，注意到它周围的间距，然后看到另一个组件应该如何与之匹配。

Design Mode 让你在注意到这些编辑时就把它们发出去。指向一个元素，描述改动，移到页面的另一部分，并在第一条完成之前再发一条批注。Agent 工作时发来的批注，Cursor 会像处理[你输入的后续消息](https://cursor.com/docs/agent/overview.md#queued-messages)一样处理。要收集一轮批注并一起发送，打开 **Batch edits**。如果你的开发服务器会热重载，改动会在 Agent 完成时出现在正在运行的应用中。

这个流程最适合速度快、又擅长界面工作的模型。我们推荐 [Composer 2.5](/blog/composer-2-5)。

## 相关

- [Agents Window](https://cursor.com/docs/agent/agents-window.md)
- [浏览器](https://cursor.com/docs/agent/tools/browser.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
