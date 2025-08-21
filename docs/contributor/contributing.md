## 了解行为准则

在参与贡献前，请了解[BoostKit开放项目行为准则](https://gitcode.com/BoostKit/community/blob/master/docs/contributor/code-of-conduct.md)，后续您在BoostKit开放项目的活动（包括但不限于发表评论、提交Issue、发表wiki等）都请遵循此行为准则。

## 签署CLA

在参与项目贡献前，您需要签署CANN开放项目贡献者许可协议（CLA）。

请根据您的参与身份，选择签署法人CLA、法人贡献者CLA、个人CLA 或企业管理员CLA，请点击[这里](待补充)签署。

- 法人CLA：以企业身份参与贡献，代表企业签署CAL，一般由企业管理人员进行签署。
- 法人贡献者CLA：如果您属于签署了法人CAL的企业员工，请申请签署法人贡献者CLA，在申请页面选择您所属的企业，申请后将由企业管理员进行审批，审批完成即可参与贡献。
- 个人CLA：以非企业员工的个人身份参与贡献，请签署个人CLA。
- 企业管理员：以企业管理员的身份参与贡献，请签署企业管理员CLA，企业管理员有权对签署法人贡献者CLA的申请进行审批和人员管理。

## 参与贡献

在签署了CLA协议、找到了您想参与的开放项目后，就可以开始您的贡献之旅啦！贡献的方式有很多种，每一种贡献都将受到欢迎和重视。

所有您发现的问题或想贡献的新想法都可以通过[Issue](https://gitcode.com/BoostKit/community/blob/master/docs/contributor/issue-submit.md)进行反馈、讨论和跟踪，并在后续[贡献编码](#贡献编码) PR 合入后关闭关联Issue。

> 📝 **提示**
> 
> - 如果您是首次使用GitCode平台进行代码贡献，详细贡献流程和常用操作可以阅读[GitCode工作流说明](https://gitcode.com/BoostKit/community/blob/master/docs/contributor/gitcode-workflow.md)进行学习。
> - 如果您在提交PR过程中遇到问题，常见问题的解决方法可参见[FAQs](https://gitcode.com/BoostKit/community/blob/master/docs/contributor/infra-faqs.md)。

### 贡献分类

- bug修复
  
  如果您在本社区下的仓库中发现了某些Bug，并想对其进行修复，欢迎您在仓库中新建Issue进行反馈和跟踪处理。
      
   您可以按照下方[提交Issue/处理Issue任务](https://gitcode.com/BoostKit/community/blob/master/docs/contributor/issue-submit.md)指引新建 `Bug-Report|缺陷反馈` 类Issue对Bug进行描述，然后在评论框中输入“/assign”或“/assign @yourself”将该Issue分配给您进行处理。
- 算子优化
  
  如果您对本社区下的仓库中某些特性实现有泛化性增强/性能优化思路，并想着手实现这些优化点，欢迎您对特性进行优化贡献。
  您可以按照下方[提交Issue/处理Issue任务](https://gitcode.com/BoostKit/community/blob/master/docs/contributor/issue-submit.md)指引新建 `Requirement|需求建议` 类Issue对优化点进行说明，并提供您的设计方案，然后在评论框中输入“/assign”或“/assign @yourself”将该Issue分配给您进行跟踪优化。
 - 贡献新特性

   如果您有全新的特性想基于鲲鹏芯片进行设计实现，欢迎您在Issue中提出新的想法和设计，并与鲲鹏团队成员进行交流讨论。您可以按照下方[提交Issue/处理Issue任务](https://gitcode.com/BoostKit/community/blob/master/docs/contributor/issue-submit.md)指引新建 `Requirement|需求建议` 类Issue提供您的新特性说明和设计方案，鲲鹏团队成员会与您进行沟通确认，并为您的特性提供一个合适的`contrib`目录分类，您可以将您的新特性贡献到对应分类目录下。同时，您需要在提交的Issue中评论“/assign”或“/assign @yourself”，认领该Issue并在后续完成新特性上库。
                                    
 - 文档纠错
                                      
   如果您在社区下的仓库中发现某些特性文档描述错误，欢迎您在仓库中新建Issue进行反馈和修复。您可以按照下方[提交Issue/处理Issue任务](https://gitcode.com/BoostKit/community/blob/master/docs/contributor/issue-submit.md)指引新建 `Documentation|文档反馈` 类Issue指出对应文档中的问题，然后在评论框中输入“/assign”或“/assign @yourself”将该Issue分配给您纠正对应文档描述。
  - 帮助解决他人Issue
                                                
    如果社区中他人遇到的问题您有合适的解决方法，欢迎您在Issue中发表评论交流，帮助他人解决问题和痛点，共同优化易用性。如果对应Issue需要进行代码修改，您可以在Issue评论框中输入“/assign”或“/assign @yourself”将该Issue分配给您，跟踪协助解决问题。
                                                      
### 提交Issue/处理Issue任务
                                                      
- 找到Issue列表：
                                                        
  在您感兴趣的BoostKit开放项目GitCode 主页内，点击“Issues”，即可找到 Issue 列表。
- 提交Issue
                                                            
  如果您准备向社区上报Bug或者提交需求，或者为社区贡献自己的意见或建议，请在BoostKit开放项目对应的仓库上提交Issue。提交Issue请参考 [Issue 提交指南](https://gitcode.com/BoostKit/community/blob/master/docs/contributor/issue-submit.md)。
 - 参与Issue讨论
                                                                    
   每个Issue下面都支持开发者们交流讨论，如果您感兴趣，可以在评论区中发表自己的意见。
  - 找到愿意处理的Issue
                                                                        
    如果您愿意处理其中的一个 issue，可以将它分配给自己。只需要在评论框内输入“/assign”或 “/assign @yourself”，机器人就会将问题分配给您，您的名字将显示在负责人列表里。
                                                                          
 ### 贡献编码
                                                                          
1. 准备开发环境
                                                                             
   如果您想参与编码贡献，需要准备开发环境，请参考每个开放项目的README.md，了解环境准备。
2. 了解开放项目内的开发注意事项
                                                                                   
   1）每个开放项目使用的编码语言、开发编译环境等都可能存在差异，请参考每个开放项目中的README.md，了解编码贡献的一些要求。
3. 代码下载与贡献流程
                                                                                                                                                                     
   1）进行代码开发前，请先将需要参与开发的仓库fork到个人仓，然后将个人仓下载到本地。并在本地分支进行代码修改。
   
   2）参考每个开放项目的说明文档，进行本地构建与验证。
   
   3）代码验证满足贡献要求后，提交Pull-Request，将代码贡献到相应的开放项目。
 4. 请注意查看门禁测试结果，若未通过，请根据问题提示进行本地代码修改；若通过，此PR会被分配给commiter检视，请关注commiter的检视意见。
 5. 当您的PR检视通过后，代码会合入相应的开放项目。
 
                                                                                                              
 > 📝 **提示**
 > 
> - 关于GitCode工作流的详细操作可参见[GitCode工作流说明](https://gitcode.com/BoostKit/community/blob/master/docs/contributor/gitcode-workflow.md)。
> - 当您在提交PR过程中遇到问题，常见问题的解决方法可参见[FAQs](https://gitcode.com/BoostKit/community/blob/master/docs/contributor/infra-faqs.md)。
                                                                                                              
                                                                                                              
                                                                                                              