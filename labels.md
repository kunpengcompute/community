## BoostKit 开源社区标签

BoostKit 开源社区所有的项目都有很多标签。
这些标签给予了 Issue 和 Pull Request 某些特定的含义。
这些标签包括：

### CLA

* boostkit-cla/yes: CLA检查通过，PR合入必要条件，表示此PR贡献者已经签署CLA协议
* boostkit-cla/no: CLA检查未通过，可以通过 `/check-cla` 命令再次检查是否满足CLA协议条件


### merge
* stats/needs-squash: 提示类型标签，提示SIG成员是否需要通过 squash 方式合入
* merge/squash: PR以squash方式合入，可以通过 `/squash` 添加 或 `/squash cancel`移除该标签
* merge/rebase: PR以rebase方式合入，可以通过 `/rebase` 添加 或 `/rebase cancel`移除该标签


### Sig

sig标签，表示该PR或者issue分属那个SIG组
* sig/infra
* sig/BoostCPH
* sig/boostrsa
* sig/boostMedia
* ...

### CI

* lgtm："Look good to me", PR合入必要条件, 表示SIG组成员检视通过, SIG组成员可以通过 `/lgtm` 添加 或 `/lgtm cancel`移除该标签
* approved:  PR合入必要条件, 表示SIG组成员检视通过, SIG组成员可以通过 `/approve ` 添加 或 `/approve cancel`移除该标签
* ci_successful: 门禁运行成功，部分仓PR合入必要条件(仓库名单可配中)
* ci_processing: 门禁运行中
* ci_failed: 门禁运行失败

