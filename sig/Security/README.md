# Security SIG

致力于提升BoostKit开源社区的安全质量与竞争力，并持续做好安全应急响应。

## 工作目标

1. 漏洞响应：制定BoostKit社区漏洞处理流程，响应上报BoostKit相关的安全问题，跟踪安全问题的处理进展，并遵循安全问题披露策略对安全问题在社区内进行披露和公告。
2. 协助漏洞修复：确保及时修复已知漏洞，开展漏洞分析、评估和修复建议等工作，包括提供相关漏洞检测工具。
3. 构建社区安全能力：结合业界和社区的优秀实践，制定BoostKit社区安全设计和安全编码等规范和指导，安全团队会尝试回答在开发和使用过程中遇到的任何问题。
4. 提供安全工具解决方案：和基础设施团队一起，在社区基础设施上构建安全领域的工具解决方案，帮助开发团队避免软件开发过程中的常见陷阱。
5. 安全合规治理：推动社区开源许可证合规、安全编译选项检查、敏感信息泄露扫描等合规治理工作，确保社区项目满足法务与安全合规要求。

## 职责与范围

本SIG主要负责以下安全领域的工作：

- **规范制定**
  - 制定和维护C/C++、Java、Python等语言的安全编码规范
  - 推广安全编码最佳实践，协助开发团队规避常见安全漏洞
  - 制定C/C++安全编译选项规范（如PIE、Stack Protector、FORTIFY_SOURCE等）
  - 检查和验证社区项目的编译安全配置
  - 制定安全架构设计指南和威胁建模方法论
  - 审视社区项目的安全设计方案
- **漏洞管理与响应**
  - 建立社区漏洞上报和响应流程
  - 协调CVE申请和安全公告（SA）发布
  - 跟踪和推动漏洞修复
- **安全合规治理**
  - 开源许可证合规检查与冲突检测
  - 敏感信息泄露扫描
  - 路径穿越等常见Web安全漏洞检测
  - 社区治理内容合规检查（CLA、行为准则等）

## 成员

### Maintainer列表

| 姓名 | GitCode ID | 邮箱 |
|------|-----------|------|
| 张春丽 | [@Shunrei](https://gitcode.com/Shunrei) | zhangchunli9@huawei.com |
| 陈启军 | [@Endfromhere](https://gitcode.com/Endfromhere) | Endfromhere@noreply.gitcode.com |
| 阮定 | [@RuanDing](https://gitcode.com/RuanDing) | ruanding@huawei.com |

### Committer列表

| 姓名 | GitCode ID | 邮箱 |
|------|-----------|------|
| 鲍鹏 | [@coolsunss](https://gitcode.com/coolsunss) | coolsunss@noreply.gitcode.com |
| 李海 | [@lh-11](https://gitcode.com/lh-11) | lihai44@huawei.com |
| 汪恒生 | [@whshahaha](https://gitcode.com/whshahaha) | wanghengsheng4@huawei.com |
| 李锦威 | [@ljw_kent](https://gitcode.com/ljw_kent) | 470921944@qq.com |
| 韦安琪 | [@weiaq](https://gitcode.com/weiaq) | weianqi1@huawei.com |
| 冯上清 | [@Excef](https://gitcode.com/Excef) | 1218424633@qq.com |
| 陈海涛 | [@GOD_CHT](https://gitcode.com/GOD_CHT) | 765917163@qq.com |
| 廖思睿 | [@Sirus_L](https://gitcode.com/Sirus_L) | liaosirui@outlook.com |
| 黄文杰 | [@huang-wen-jie](https://gitcode.com/huang-wen-jie) | huangwenjie16@huawei.com |
| 陈嘉珣 | [@codesheepchen](https://gitcode.com/codesheepchen) | chenjiaxun2@163.com |
| 包旻晨 | [@autumn_94](https://gitcode.com/autumn_94) | minchen_bao@foxmail.com |
| 付炬 | [@nonces](https://gitcode.com/nonces) | 2774337358@qq.com |
| 黄浩纯 | [@lindv](https://gitcode.com/lindv) | huanghaochun@huawei.com |

## 角色和组织管理

- **安全规范和流程专员**：基于开源社区优秀实践和社区实际情况，建立并持续完善社区的安全规范和漏洞管理流程，构建社区漏洞管理、设计&代码安全和基础设施安全等方面的安全能力。同时负责安全规范和流程在社区的推广，通过各种方式落地安全规范和流程。
  - 张春丽[@Shunrei](https://gitcode.com/Shunrei), *zhangchunli9@huawei.com*

- **安全合规专员**：负责社区知识产权审查、开源许可证合规、安全编译选项检查、敏感信息泄露扫描等合规治理工作，确保社区项目满足法务与安全合规要求。协助包管理团队在软件包引入阶段开展安全检查，确保引入的软件包合规。
  - 阮定[@RuanDing](https://gitcode.com/RuanDing), *ruanding@huawei.com*

- **漏洞管理专员**：负责社区漏洞管理和流程的落地和执行，包括构建社区的漏洞接收、评估、修复、披露和奖励计划等各项能力，通过各种方式提升社区漏洞处理效率。协调与其他SIG的合作，确保漏洞得到及时响应和修复。
  - 张强[@gongchangsui](https://gitcode.com/gongchangsui), *zhangq@huawei.com*

- **基础设施安全专员**：协助基础设施团队构建社区安全工具解决方案，通过工具手段落地各项安全检查，持续提升社区安全工程能力，帮助社区开发者快速检查和识别安全缺陷和风险，从而提升社区安全能力。
  - 唐鑫意[@tangxinyi](https://gitcode.com/tangxinyi), *tangxinyi@huawei.com*

## 社区运作

### 会议组织

- 公开的会议时间：北京时间，按需召开，提前三天发布会议通知。
- 会议时间初步定在双周周五上午10:00-12:00，遇节假日、紧急事务共同讨论进行调整。