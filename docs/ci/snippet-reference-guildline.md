<span style="font-size:20px;">**开源代码片段引入操作指南**</span>

**<span style="font-size:18px;">1. 引言</span>**

**<span style="font-size:16px;">1.1 目的</span>**

本指南旨在规范项目中第三方开源代码片段的引入行为。

**<span style="font-size:18px;">1.2 适用范围</span>**

适用于所有需要在项目中引入第三方开源代码片段的开发者和贡献者。

<span style="font-size:18px;">**2. 基本原则**</span>

**2.1 风险认知**

引入第三方开源代码片段可能存在以下风险：

**合规风险**：可能产生许可证冲突问题

**安全风险**：不利于管理开源项目可能出现的漏洞

**维护风险**：上游项目修复功能性问题时，片段引入方式不利于同步更新

**2.2 核心原则**

不鼓励但允许第三方开源代码片段引用（推荐优先采用包管理器引用方式）

严格禁止引用第三方开源代码时变更其原始许可证和版权声明

严格禁止引入许可证与项目主许可证不兼容的代码片段

风险提示：在 Apache-2.0（宽松许可证）项目中引入 GPL-2.0-only 的代码片段，这将给社区下游用户带来严重的合规风险。

**3. 自动化检查流程**

**3.1 触发检查**

PR 合入时自动触发开源代码片段引入检查（SCA）

如 SCA 显示为"FAILED"，需点击上方任务链接进入数字化作业平台处理

![image.png](https://raw.gitcode.com/user-images/assets/9107584/a40b5971-81a4-4e1b-9bda-f9b5decabc16/image.png 'image.png')


**3.2 查看风险列表**

![image.png](https://raw.gitcode.com/user-images/assets/9107584/0121839b-87b7-4409-bf17-ac075e875268/image.png 'image.png')

**4. 合规整改指南**

确认存在开源代码片段引入后，贡献者需要在引入文件的头部保留原始License和Copyright，并在Third_Party_Open_Source_Software_Notice文件中进行引用声明。

- 确认引入代码的 License 和 Copyright

- 代码片段本身未声明 License 和 Copyright，则继承该片段所在文件的声明

- 所在文件未声明 License 和 Copyright，则继承其所在项目的声明