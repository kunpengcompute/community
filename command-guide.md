# `BoostKit` 社区机器人命令使用手册

| 命令 | 示例 |描述 |谁能使用|
|:-|:-----|:-|:-|
|        `/check-cla`       |             `/check-cla` <br> /cla cancel           | 强制重新检查一个`Pull Request`的CLA状态。 如果`Pull Request`的作者已经签署CLA， 这个`Pull Request`将会新增一个名为`boostkit-cla/yes`的标签， 反之将会新增一个名为`boostkit-cla/no`的标签。 |     任何人     |
|      `/lgtm`                |            `/lgtm` <br> `/lgtm cancel`               | 为一个`Pull Request`添加或者删除`lgtm`标签，这个标签将用于`Pull Request`合入判断。|    SIG成员    |
|      `/approve `         |     `/approve ` <br> `/approve  cancel`         |     为一个`Pull Request`添加或者删除`lgtm`标签，这个标签将用于`Pull Request`合入判断。      |    SIG成员    |
|      `/check-pr `        |                      `/check-pr`                                |     检车`Pull Request`是否满足合入条件。如果不满足，则给出原因      |    任何人    |
|         `/assign`          |    `/assign ` <br> `/assign @boostkit-bot`    |    给一个`Issue`分配负责人      |    任何人都能在一个`Issue`上触发这种命令， 但是目标负责人必须是这个组织的一个成员。 如果没有指定目标负责人，这表明这个`Issue`会分配给自己。    |
|         `/unassign`      |`/unassign ` <br> `/unassign @boostkit-bot`|     取消分配一个`Issue`负责人。      |    任何人都能在一个`Issue`上触发这种命令    |
|         `/close`           |                                  `/close`                           |    关闭一个Pull Request或者Issue。      |    作者和SIG成员   |
|         `/reopen`        |                                  `/reopen`                       |   重新打开一个Issue。                           |    作者和SIG成员   |
|         `/rebase`        |     `/rebase ` <br> `/rebase cancel`    |   PR 以 rebase 方式合入(或取消)                         |    SIG成员   |
|         `/squash`        |     `/squash ` <br> `/squash cancel`    |   PR 以 squash 方式合入(或取消)                         |    SIG成员   |