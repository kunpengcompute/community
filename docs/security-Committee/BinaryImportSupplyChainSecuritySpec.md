# BoostKit 仓库二进制引入与供应链安全约束规范

| 项目 | 内容 |
| --- | --- |
| 文档编号 | BK-SEC-BIN-001 |
| 版本 | 1.2（建议稿） |
| 制定日期 | 2026-11-26 |
| 适用范围 | `gitcode.com/boostkit/` 组织下全部仓库，含默认分支、Release、LFS、CI/CD |
| 依据 | 《BoostKit 二进制引用与供应链安全专项分析》（2026-11-24） |
| 规范性引用 | SLSA v1.2、SPDX 3.0.1、CycloneDX 1.7、Sigstore/cosign、in-toto、TUF、OpenChain ISO/IEC 18974、NIST SSDF SP 800-218 |
| 修订说明 | v1.1 按业界成熟模型重构 B/C/D 三类校验：以“补丁系列 / 上游增量 / 发布元数据”为校验单元，减少逐项人工检查，增加自动门禁与附录 F。v1.2 结合 124 仓实地验证优化可操作性：签名主路线改为 GPG（GitCode 无 OIDC 身份联合）、G-12 增加平台能力分级与兜底、C 类 Fork 改按需同步、新增 7.7 存量清单脚本化生成与附录 G 平台能力对照 |

---

## 0. 背景与目标

### 0.1 现状

专项分析覆盖 124 个仓库，结论如下：

- 87 个仓库含二进制文件（5,063 个、约 566.9 MB）；27 个仓库含可执行/制品类二进制（207 个、约 130.8 MB）；4 个仓库提交入库 15 个 ELF/ar（约 28.3 MB）。
- 12 个仓库在构建/部署/CI 中从外部下载预编译依赖；29 个仓库存在运行期 `dlopen`/JNI/`ctypes`/`LD_PRELOAD` 加载。
- **0 个仓库具备完备的防仿冒、防篡改、防投毒能力**：0 个 GPG/cosign 验签、0 个 SBOM、抽样 18 个仓库 0 个签名提交、仅 1 个自动 SHA256 校验。
- 典型缺陷：`Kbox-patches` 未验签 `.asc` 且校验失败继续安装；`CubeSandbox` 默认 latest 且不消费 manifest digest；`Redis` 明文 HTTP 下载源码并关闭 TLS 下载含 `.so` 的压缩包；`skills` 直接执行无来源 ELF；JNI/`dlopen` 普遍不校验库哈希，部分路径可由环境变量控制。

### 0.2 目标

1. **可控**：每个二进制都能回答“从哪来、谁负责、什么版本、如何验证、为什么需要”。
2. **可验**：所有引入、构建、分发、加载环节具备可执行的完整性与真实性校验，默认失败关闭。
3. **可追溯**：发布物具备 SBOM、构建溯源与签名，支持事件回溯与撤回。
4. **可落地**：按自研、补丁、Fork、发布仓分类给出差异化操作要求，避免“一刀切”。

### 0.3 规范性用语

- **必须 / 禁止**：强制要求，未满足不得合入或发布；例外须走第 8 章流程。
- **应 / 不应**：推荐要求，偏离须在评审中说明理由并登记。
- **可**：可选实践。

---

## 1. 术语与定义

| 术语 | 定义 |
| --- | --- |
| 二进制 | 不可直接审阅的制品，包括 ELF/`.so`/`.a`/`.ko`、PE/`.dll`/`.exe`、Mach-O、`.jar`/`.war`、安装包（deb/rpm）、压缩包、模型（onnx/gguf）、LFS 大对象等 |
| 提交入库二进制 | 通过 Git 提交或 LFS 指针进入仓库的二进制 |
| 外部二进制 | 构建、安装、运行期从仓库外获取的二进制或预编译依赖 |
| 发布制品 | 对外发布的 tarball、镜像、安装包、LFS 包及其校验/签名文件 |
| 不可变引用 | 精确版本号 + 内容摘要（commit SHA、SHA-256、OCI digest），不接受 `latest`、分支名、可移动 tag |
| 信任根 | 与制品分发渠道分离、用于验证发布者身份的密钥或身份体系（GPG 公钥、cosign OIDC 身份、TUF 根） |
| 失败关闭 | 校验不通过、网络异常、签名缺失时中止流程，不回退到“继续安装” |
| 清单 | 仓库根目录 `BINARIES.yaml`，登记全部二进制及其来源、版本、摘要、签名、责任人 |

### 1.1 适用边界

- 对 Fork 仓**上游自带、BoostKit 不构建、不分发、不加载**的二进制（如上游测试图片、示例模型、测试固件），可按“目录 + 目录清单哈希”汇总登记，不要求逐文件整改。
- 一旦二进制进入 BoostKit 的构建、安装、发布或运行期加载链路，即视为“BoostKit 引入”，必须满足本规范全部适用条款。
- 对无法逐一列举的历史存量，允许先以目录级清单登记并制定逐文件补全计划，但必须在 90 天内完成可执行制品的逐项登记。

---

## 2. 仓库分类与适用规则

### 2.1 分类

| 类型 | 代号 | 判定 | 典型仓库 |
| --- | --- | --- | --- |
| 自研仓 | A | 主体代码为华为/BoostKit 自研，第三方仅为片段或依赖 | `skills`、`KADD`、`kadd-open`、`kpglibc`、`kvecturbo`、`Megascan`、`KAE`（含内嵌） |
| 补丁仓 | B | 以上游源码为基线，通过 patch 系列构建 | `Redis`、`vsag`、`Kbox-patches`、`ceph_BK` |
| Fork 仓 | C | 完整上游仓库副本 + BoostKit 修改 | `CubeSandbox`、`mooncake`、`overlaybd`、`brpc`、`dpdk`、`velox` |
| 发布/文档仓 | D | 主要承载发布制品、LFS 包、文档与配置 | `kqmalloc-release`、`boostcph`、`boostcore`、`release-management` |

> 一个仓库可同时具有多种属性。按“**最严格适用**”原则执行：例如 Fork 仓自制发布包，同时适用 C 与 D。

### 2.2 适用矩阵

| 要求 | A 自研 | B 补丁 | C Fork | D 发布 |
| --- | --- | --- | --- | --- |
| 二进制清单 `BINARIES.yaml` | 必须 | 必须 | 必须 | 必须 |
| 源码优先/禁止新增无理由二进制 | 必须 | 必须 | 应 | 必须 |
| 版本固定 + SHA-256 | 必须 | 必须 | 必须（BoostKit 增量部分） | 必须 |
| 禁止关闭 TLS | 必须 | 必须 | 必须（BoostKit 脚本） | 必须 |
| 发布物签名 + SBOM | 必须（有发布物时） | 必须（有发布物时） | 必须（BoostKit 发布物） | 必须 |
| 运行期加载校验 | 必须 | 必须 | 应（BoostKit 新增加载点） | 不适用 |
| 签名提交/标签 | 应 | 应 | 可（跟随上游） | 必须（发布 tag） |
| 目标合规等级 | L3 | L2（发布 L3） | L2（发布 L3） | L3 |

### 2.3 三类仓库操作流程对比

| 环节 | A 自研仓 | B 补丁仓 | C Fork 仓 |
| --- | --- | --- | --- |
| 1. 引入前 | 填《二进制引入申请》，证明不可源码化 | 记录上游项目、基线版本、基线 commit、补丁系列 | 记录上游地址、基线 commit/tag、BoostKit 增量清单 |
| 2. 入库 | 二进制放 `prebuilt/<name>/<version>/`，附 LICENSE、SHA256SUMS、PROVENANCE | 只提交文本 patch 与 `series`；禁止二进制补丁 | 增量集中在 `boostkit/`；新增二进制登记清单 |
| 3. 构建 | 优先源码构建；下载走 `tools/fetch` | 固定基线 + 补丁在 CI 构建；禁止提交构建产物 | 从源码构建；不得削弱上游校验 |
| 4. 校验 | 入库哈希 + 下载哈希 + 加载前哈希 | **校验单元：补丁系列**；补丁头 + `series` + 干净基线构建 | **校验单元：上游 pin + 增量清单**；固定 commit + delta + lockfile/vendor |
| 5. 发布 | cosign/GPG + SBOM + provenance | 同左，注明上游版本与补丁列表 | 同左，注明“基于上游 X + BoostKit 增量” |
| 6. 发布校验 | 制品签名 + SBOM + provenance | 同左 + 补丁列表/上游状态 | 同左 + 上游基线 + delta 清单 |
| 7. 维护 | 季度复审；版本升级重走评审 | 跟踪上游安全修复并 rebase 补丁 | 按需同步上游（触发条件见 C-01）；同步 PR 审查二进制差异 |
| 8. 红线 | 禁止无来源 ELF、`curl` 管道执行、关闭 TLS | 禁止二进制 patch、补丁脚本联网、关闭 TLS | 禁止关闭上游 TLS/校验、未登记二进制随同步进入 |

---

## 3. 通用底线要求（G）

以下要求适用于所有仓库、所有分支和所有发布物。

### G-01 最小化

- 新增二进制前必须证明“无法从源码构建”或“源码构建成本/风险不可接受”。
- 优先顺序：**源码构建 > 内部可信构建 > 上游签名制品 > 上游无签名制品（仅例外）**。
- 禁止仅为“方便”提交预编译产物；构建输出不得入库（`build/`、`dist/`、`target/`、`out/` 必须被 `.gitignore` 覆盖）。

**验证**：引入 PR 必须附《二进制引入申请》，说明不可源码化的原因与替代方案。

### G-02 清单

- 仓库根目录必须存在 `BINARIES.yaml`（格式见 7.1 与 `specs/binary-manifest.template.yaml`）。
- 清单必须覆盖：提交入库二进制、LFS 对象、构建/安装期下载、运行期加载、发布制品。
- 每条记录必须包含：`id`、`kind`、`path`/`url`、`version`、`sha256`、`source`、`license`、`reason`、`owner`、`verification`。
- 清单变更必须与二进制变更在同一 PR 中提交；CI 必须校验“仓库内每个二进制都能在清单中找到”。

**验证**：CI 运行清单 schema 校验 + 文件覆盖比对；缺失即失败。

### G-03 来源与责任人

- 必须记录发布者（组织/项目）、上游地址、许可证、引入理由、使用位置。
- 每条记录必须有具名责任人（团队而非个人亦可）和维护期限。
- 来源不明、许可证不明、无上游签名的二进制禁止新增。

**验证**：清单字段完整性检查；评审时核对上游页面与许可证。

### G-04 版本不可变

- 必须固定精确版本与内容摘要：Git commit SHA、release 版本号 + SHA-256、OCI image digest。
- 禁止：`latest`、`main`/`master`、`stable`、可移动 tag、未固定 commit 的 `git clone`、未固定 digest 的镜像。
- Git submodule 必须固定 commit；LFS 指针必须与签名清单绑定。

**验证**：CI 正则拦截 `latest`、默认分支 clone、无 digest 的 `FROM`；人工复核发布 tag 不可移动。

### G-05 传输安全

- 外部获取必须使用 HTTPS，TLS 1.2 及以上，使用系统/镜像内置 CA 校验。
- 禁止：`http://` 下载、`curl -k`、`wget --no-check-certificate`、`GIT_SSL_NO_VERIFY`、`npm config set strict-ssl false`、`pip --trusted-host`。
- 构建环境缺 CA 时，必须修复镜像或使用内部 HTTPS 镜像，不得关闭校验。

**验证**：CI 禁止模式扫描（附录 C）；构建镜像 CA 自检。

### G-06 完整性

- 所有外部二进制在使用前必须校验 SHA-256；摘要必须来自仓库内清单或签名清单，不得从同一未认证响应中“边下边取”。
- 校验失败必须 `exit 1`，禁止“删除后重下并继续”。
- 压缩包必须校验压缩包本身，不得只校验解压后的部分文件。
- 提交入库二进制必须在 CI 中校验其 SHA-256 与清单一致。

**验证**：CI 对清单中的每个 `sha256` 复算；安装器单元测试覆盖“哈希错误必须失败”。

### G-07 真实性

- 对外发布制品必须使用独立信任根签名。**主路线为 GPG**：发布专用密钥，公钥指纹写入组织级公告（仓库外独立渠道）与安装器。依据：GitCode 已支持 GPG 验证，但无 OIDC 身份联合，cosign keyless 不可用。
- Sigstore cosign（keyless/OIDC）为远期可选，待平台提供 OIDC 联合或自建 Fulcio/Rekor 后启用；已按 GPG 落地的仓库无须迁移。
- 发布目录必须同时提供 `SHA256SUMS`、`SHA256SUMS.sig`（或 cosign bundle）、SBOM、构建溯源。
- 安装器必须验签后再校验哈希；签名缺失即失败。
- 上游二进制应优先验证上游签名/校验和；无法验证时必须走例外流程。

**验证**：发布流水线强制签名；安装器集成测试验证“无签名/错签名必须失败”。

### G-08 安全加载

- `dlopen`/`LoadLibrary`/JNI/`ctypes`/`LD_PRELOAD` 加载的库必须来自固定安装前缀或受控目录，路径不得由环境变量、命令行、可写配置直接决定。
- 加载前必须：`realpath`、检查普通文件、属主为 root 或当前用户、文件及父目录组/其他用户不可写、校验 SHA-256。
- JNI 从 jar 释放库时：使用随机临时目录（0700）、校验 jar 内清单哈希、`System.load` 绝对路径、用后删除；禁止在调用方提供的目录写固定文件名。
- 禁止 `RTLD_GLOBAL`（除非有评审结论）；插件机制必须有签名插件清单。

**验证**：代码评审 + 静态扫描（附录 C）；加载器单元测试覆盖路径替换、哈希错误、权限异常。

### G-09 可追溯

- 每个发布版本必须生成 SBOM（SPDX 或 CycloneDX），包含全部二进制、版本、哈希、许可证。
- 发布制品应生成 SLSA provenance / in-toto 证明，记录源码 commit、构建参数、构建环境 digest。
- 构建必须可复现或在同等输入下可审计；至少记录输入清单与输出哈希。

**验证**：发布流水线产出 SBOM/provenance 并归档；抽样重建比对哈希。

### G-10 构建隔离

- 构建必须在干净环境执行，不携带个人凭证；CI 使用短期令牌。
- 网络出口使用允许列表；依赖下载必须经内部镜像或带摘要的 fetch 工具。
- 构建缓存必须以内容摘要为键，禁止使用可被覆盖的“最新”缓存。

**验证**：CI 配置审计；网络策略与缓存策略检查。

### G-11 许可证与合规

- 每个二进制必须记录许可证；禁止引入许可证不明或与分发方式冲突的二进制。
- 必须随附上游 `LICENSE`/`NOTICE`；修改过的 Fork 必须保留原版权声明并补充修改说明。
- GPL/LGPL/AGPL 二进制必须单独评审分发方式与义务。

**验证**：许可证扫描 + 法务评审记录。

### G-12 CI 强制

- 下列检查必须作为合入门禁，失败即阻断：
  1. `BINARIES.yaml` schema 与覆盖校验；
  2. 禁止模式扫描（附录 C）；
  3. 提交入库二进制哈希复算；
  4. 新二进制/新下载 URL 的人工审批标签；
  5. 发布流水线签名与 SBOM 生成。
- 门禁实现按平台能力分级（能力现状见附录 G）：
  1. **强制阻断（主路线）**：MR 设置“必须通过的检查项” + 分支保护，检查集以 `.workflow/` 流水线实现（boostkit 已在使用该格式）；
  2. **检测兜底**：平台暂不支持强制阻断时，必须每日定时对默认分支运行同一检查集，违规写入组织看板并要求 24 小时内整改；同时主分支至少启用双人评审 + CodeOwners 作为补偿；
  3. 禁止以“平台不支持”为由完全不运行检查。
- 禁止通过管理员绕过、临时禁用检查或使用“skip-ci”合入二进制变更。

**验证**：分支保护规则；CI 审计日志；兜底模式下定时任务运行记录与违规看板。

### G-13 例外管理

- 不满足 G-01～G-12 的存量或新增项，必须申请例外；例外必须有时限（≤ 90 天）、补偿措施、责任人和复审日期。
- 例外不得用于绕过签名要求；签名缺失只能作为“限时迁移”例外，且发布页必须显著标注。
- 例外到期未整改，CI 必须阻断发布。

### G-14 发布不可变

- 已发布版本禁止覆盖；修复必须发新版本。
- 撤回时必须发布公告、更新签名清单、提供受影响哈希列表与替代版本。
- 发布 tag 必须签名（D 类强制，A/B/C 类应）。

### G-15 定期复审

- 外部二进制至少每季度复审一次；上游停止维护、出现高危漏洞、许可证变更时立即复审。
- 复审内容包括：版本、哈希、签名、上游状态、替代方案、是否可源码化。

### G-16 事件响应

- 发现发布源失陷、哈希不匹配、恶意二进制时，必须：停止分发 → 撤回制品 → 公告 → 提供自查脚本 → 根因分析 → 更新规范。
- 必须保留发布、下载、校验日志不少于 180 天。

---

## 4. 自研仓库（A 类）操作规范

### 4.1 引入决策树（A-01）

新增二进制前依次回答：

1. 能否从源码构建？能 → 源码构建，禁止提交二进制。
2. 能否在内部可信环境构建并签名？能 → 内部构建 + 发布签名。
3. 是否为闭源硬件/厂商 SDK？是 → 允许引入，但必须固定版本、校验哈希、记录许可证，并优先验证厂商签名。
4. 是否为预编译性能/部署工具？是 → 允许引入，但必须提供来源、哈希、签名；无法签名时走例外。
5. 以上皆否 → 禁止引入。

### 4.2 提交入库二进制（A-02）

- 存放路径：`prebuilt/<name>/<version>/`；每个目录必须含：
  - 二进制文件；
  - `LICENSE`、`NOTICE`（如上游提供）；
  - `SHA256SUMS`；
  - `PROVENANCE.md`（来源 URL、版本、获取日期、校验方式、责任人）。
- 文件权限：默认 `0644`；仅确需直接执行时设 `0755`，并在清单 `reason` 中说明。
- 禁止提交构建输出、调试符号、测试生成的临时二进制。
- 同一二进制不得在多处重复存放；副本必须用符号链接或在清单中标注“副本”。

### 4.3 构建期下载（A-03）

- 必须通过统一 `tools/fetch`（参考实现见 7.3）读取 `BINARIES.yaml` 下载，禁止脚本内散落 `curl`/`wget`。
- 下载 URL 必须是 HTTPS + 精确版本；禁止变量拼接出 `latest`。
- 摘要必须写在清单中；禁止“下载后从响应头/同页文本取摘要”。
- 优先使用内部镜像；镜像必须按摘要缓存，不得透明替换内容。
- 安装/解压前必须校验；校验失败立即退出。

### 4.4 运行期加载（A-04）

**C/C++**

- 路径来自编译期固定前缀或受控配置文件，禁止 `getenv("..._LIB_DIR")` 直接拼路径。
- 加载前执行：`realpath` → `stat` 普通文件 → 属主 root/euid → 文件与父目录无组/其他写权限 → SHA-256 校验 → `dlopen(path, RTLD_NOW | RTLD_LOCAL)`。
- 禁止 `RTLD_GLOBAL`、禁止从当前工作目录或 `/tmp` 加载。
- 可选插件必须使用签名清单，逐插件校验。

**Java/JNI**

- 库文件放入 jar 的 `META-INF/native/<os>-<arch>/`，同目录提供 `.sha256`。
- 释放到 `Files.createTempDirectory` 创建的 0700 目录，文件名随机；校验哈希后 `System.load(absolutePath)`；`deleteOnExit`。
- 禁止在用户传入目录写固定文件名；禁止回退到无校验的 `System.loadLibrary`（除非该库由系统包管理器提供并在清单登记）。

**Python/ctypes**

- `ctypes.CDLL` 必须使用绝对路径；加载前校验哈希；禁止从 `sys.path`/cwd 隐式查找。
- 禁止 `LD_PRELOAD` 指向用户可写目录；测试场景必须在隔离环境执行并标注。

### 4.5 发布与签名（A-05）

发布流水线必须产出：

```
release/
  <artifact>.tar.gz
  SHA256SUMS
  SHA256SUMS.sig          # gpg --detach-sign（主路线）；cosign sign-blob（远期可选）
  <artifact>.sbom.json    # SPDX/CycloneDX
  provenance.intoto.jsonl # SLSA（建议）
  verify.sh               # 用户侧校验脚本
```

- 签名身份固定：GPG 使用发布专用密钥，公钥指纹写入 README 与安装器（主路线）；cosign 待平台 OIDC 身份联合可用后再启用。
- 发布 tag 必须签名且不可移动；Release Notes 必须列出二进制清单与校验命令。
- `verify.sh` 必须示例：导入公钥 → 验签 → `sha256sum -c` → 失败退出。

### 4.6 CI 配置（A-06）

- 合入门禁：清单校验、禁止模式、二进制哈希、许可证、新二进制审批。
- 发布门禁：签名、SBOM、provenance、签名 tag 校验。
- 每季度运行一次全量二进制盘点，输出与 `BINARIES.yaml` 的差异报告。

### 4.7 自研仓禁止事项（A-07）

1. 禁止提交无法说明来源的 ELF/`.so`。
2. 禁止脚本 `chmod +x` 后直接执行入库二进制而不校验。
3. 禁止 `curl ... | bash` 引导安装。
4. 禁止在构建脚本中关闭 TLS 校验。
5. 禁止从可写目录或环境变量路径 `dlopen`。
6. 禁止发布未签名制品（例外除外）。

---

## 5. 开源参与仓库操作规范

### 5.1 补丁仓（B 类）

补丁仓的原则：**上游源码 + 可审阅补丁 + 可复现构建**。二进制只能出现在最终发布物，不得成为构建输入的黑盒。

**校验单元是“补丁系列”，不是整棵上游树。** 业界成熟做法可归纳为：固定上游基线 → 补丁自描述 → 系列可应用 → CI 从源码重建 → 发布物签名。参考 Debian DEP-3 补丁头、`quilt`/`series`、Yocto `Upstream-Status`、Fedora/openSUSE 的 upstream-first、Linux stable 的 backport 规则（见附录 F）。

#### 5.1.1 基线与补丁（B-01）

- 必须记录上游项目、基线版本、基线 commit/tag、补丁系列顺序（`series` 文件）。
- 每个补丁必须带 DEP-3 风格头：`Description`、`Origin`、`Forwarded`、`Last-Update`、`Upstream-Status`。BoostKit 自有补丁标 `Origin: BoostKit`；`Forwarded: no` 必须附不上游化的理由。
- `Upstream-Status` 取值限定为 `Pending`、`Submitted`、`Accepted`、`Denied`、`Inappropriate [理由]`；`Inappropriate` 必须写明替代方案。
- 补丁必须能应用到固定基线；CI 必须执行 `quilt push -a` 或 `git am --3way`，并验证 `patch -p1 --dry-run` 全部通过。
- 补丁必须是文本 diff；禁止二进制补丁、禁止大段 base64/不可审阅 blob。
- 补丁脚本禁止联网下载；构建依赖必须走 `BINARIES.yaml` + `tools/fetch`。
- 补丁修改构建脚本时，若涉及 TLS、校验、下载地址，必须安全评审并保留原校验逻辑。

补丁头最小示例：

```text
Description: fix build with newer glibc
Origin: upstream, https://github.com/example/project/commit/abc123
Bug: https://github.com/example/project/issues/456
Forwarded: https://github.com/example/project/pull/789
Last-Update: 2026-11-26
Upstream-Status: Submitted
```

#### 5.1.2 上游源码获取（B-02）

- 必须使用 HTTPS + 固定版本 + SHA-256；优先官方 release tarball 或固定 commit 的源码归档。
- 禁止 `git clone` 默认分支后直接构建；必须 `git checkout <commit>` 并校验 commit SHA。
- 使用镜像时，摘要必须与上游一致；镜像不得透明替换。
- 源码包解压后应校验关键文件或使用签名 tag 验证。

#### 5.1.3 构建与产物（B-03）

- 必须从“上游源码 + 补丁”在 CI 中构建；禁止把构建产物提交回仓库。
- 构建输入（源码哈希、补丁哈希、工具链版本、环境镜像 digest）必须记录，并生成 provenance。
- 发布物必须签名并附 SBOM；SBOM 必须区分上游组件与 BoostKit 补丁。
- 若必须引入上游预编译依赖（如 onnxruntime），按 G-04～G-07 固定版本、校验、记录；禁止关闭 TLS。
- 应支持可复现构建：同一源码 + 补丁 + 工具链重复构建应得到相同制品哈希；暂不具备时须记录差异原因。

#### 5.1.4 发布（B-04）

- Release Notes 必须列出：上游版本、补丁列表及 `Upstream-Status`、二进制依赖及哈希、校验命令。
- 发布 tag 必须签名；发布资产不得覆盖。
- 发布元数据按 D-01 执行“四件套”，撤回流程与 A 类一致。

#### 5.1.5 补丁仓禁止事项（B-05）

1. 禁止在补丁中新增预编译二进制（例外除外）。
2. 禁止补丁脚本 `wget --no-check-certificate`、明文 HTTP。
3. 禁止 `rpm -ivh`/`dpkg -i` 安装未校验的本地包。
4. 禁止把上游 `latest` 或默认分支作为构建输入。
5. 禁止在补丁中删除上游的签名/校验逻辑。
6. 禁止无头补丁（缺少 `Origin`/`Upstream-Status`）进入 `series`。

#### 5.1.6 B 类自动门禁（最小集）

| 门禁 | 检查内容 | 参考工具 |
| --- | --- | --- |
| 补丁头完整 | 每个 patch 含 `Origin`、`Upstream-Status`、`Forwarded` | 仓库脚本 / grep |
| 系列可应用 | 在干净基线执行 `quilt push -a` 或 `git am --3way`，全量通过 | quilt / git |
| 基线固定 | 源码 commit/tag 与 SHA-256 固定，非默认分支 | git / sha256sum |
| 重建通过 | 从“上游源码 + 补丁”完整构建，产物不入库 | CI |
| 发布可验 | 签名 + provenance + SBOM + 哈希清单 | gpg（或 cosign）/ in-toto |
| 增量审查 | rebase 后用 `git range-diff` 审查补丁变化 | git |

满足前五项即达到 B 类 L2；发布物同时满足 D-01 时达到 L3。

### 5.2 Fork 仓（C 类）

Fork 仓的原则：**保留上游、隔离增量、不削弱上游安全能力**。

**校验单元是“上游固定点 + BoostKit 增量清单”，不是整棵 fork 树。** 业界成熟做法是元数据清单 + 版本固定 + 增量隔离：Chromium 的 `DEPS`/`third_party/README.chromium`、AOSP 的 `METADATA`、Mozilla 的 `moz.yaml`、Rust/Go 的 vendor 清单（见附录 F）。

#### 5.2.1 上游关系（C-01）

- 仓库根目录必须维护 `UPSTREAM.yaml`（可另附 `UPSTREAM.md` 说明），至少包含：上游地址、固定 commit/tag、许可证、安全联系人、同步周期、BoostKit 增量清单（分支/提交/补丁）、CVE 回移 SLA。
- 上游组件如需逐项登记，可参考 `README.chromium` 字段：`Name`、`URL`、`Version`、`Revision`、`License`、`Security Critical`。
- `UPSTREAM.yaml` 必须以 commit SHA 记录上游基线；tag 仅作辅助信息（实地验证：部分 Fork 导入 GitCode 时未带入上游 tag，如 `mooncake` 为 0 tag，无法以 tag 锚定基线）。
- **按需同步，不承诺固定节奏**（24 个 Fork 全部跟随上游节奏不可持续）。出现以下任一情况必须同步：
  1. 上游发布安全修复（按 CVE 回移 SLA 执行）；
  2. BoostKit 需要的上游特性或修复；
  3. 上游大版本进入停止维护窗口。
- 同步 PR 必须包含：更新后的 `UPSTREAM.yaml`（旧/新 commit SHA）、`git range-diff` 增量报告、二进制清单差异、CI 重建结果。
- 上游许可证、NOTICE、版权声明必须保留。

`UPSTREAM.yaml` 最小示例：

```yaml
upstream:
  url: https://github.com/example/project
  commit: 0123456789abcdef0123456789abcdef01234567
  license: Apache-2.0
  security_contact: security@example.org
sync:
  mode: on-demand            # on-demand（默认）| fixed
  triggers:
    - upstream-security-fix
    - boostkit-feature-need
  last_synced_at: 2026-11-01
  last_synced_upstream_commit: 0123456789abcdef0123456789abcdef01234567
delta:
  branch: boostkit/main
  patches:
    - boostkit/patches/0001-adapt-kunpeng.patch
security:
  cve_backport_sla_days:
    critical: 7
    high: 30
```

#### 5.2.2 增量隔离（C-02）

- BoostKit 专属修改应集中在 `boostkit/` 目录，或使用清晰的提交前缀/标签；每个增量必须能映射到 `UPSTREAM.yaml` 的增量清单。
- 新增二进制、下载脚本、加载点必须登记到 `BINARIES.yaml`，并在 `UPSTREAM.yaml` 标注“BoostKit 增量”。
- 禁止静默修改上游的安全校验、TLS 配置、哈希校验；如必须修改，走例外并说明补偿措施。
- 必须能用 `git range-diff upstream/main...HEAD` 或等价命令生成增量清单；评审以增量为单位，不要求逐行审阅上游代码。

#### 5.2.3 上游二进制与脚本（C-03）

- 优先从源码构建上游组件；确需使用上游预编译包时，必须验证上游签名/校验和，并记录到清单。
- 上游脚本关闭 TLS 或使用 `latest` 时，BoostKit 不得直接复用；必须在 `boostkit/ci/` 提供加固版本，或在上游合并修复。
- 不得把上游二进制重新签名为 BoostKit 制品；应保留上游来源与签名。

#### 5.2.4 CI 与发布（C-04）

- Fork 仓的 BoostKit 发布物必须签名、生成 SBOM 与 provenance，并注明“基于上游 X 版本 + BoostKit 补丁”。
- CI 必须运行通用门禁；上游测试与 BoostKit 测试均需通过；lockfile/vendor 校验和必须一致。
- 应运行 OpenSSF Scorecard 的 `Binary-Artifacts`、`Pinned-Dependencies`、`Signed-Releases` 检查，作为基线而非最终结论。
- 发布物中的二进制必须能追溯到上游版本或 BoostKit 构建记录。

#### 5.2.5 Fork 仓禁止事项（C-05）

1. 禁止在 Fork 中关闭上游的证书校验或哈希校验。
2. 禁止把上游 `latest`/默认分支作为 BoostKit 发布输入。
3. 禁止未登记的新增二进制随 Fork 同步进入仓库。
4. 禁止删除上游 LICENSE/NOTICE。
5. 禁止将上游未签名二进制包装成“BoostKit 官方签名制品”。

#### 5.2.6 C 类自动门禁（最小集）

| 门禁 | 检查内容 | 参考工具 |
| --- | --- | --- |
| 上游固定 | `UPSTREAM.yaml` 中 commit/tag 非默认分支、非 `latest` | 仓库脚本 |
| 增量可审 | `git range-diff` 生成增量清单并与 `UPSTREAM.yaml` 一致 | git |
| 依赖校验 | lockfile/vendor 校验和一致；无未登记二进制 | npm/cargo/go、syft |
| 安全不降级 | diff 中无关闭 TLS/校验、无 `--no-check-certificate` | CI 扫描 |
| 仓库健康 | Scorecard 三项关键检查达到阈值 | Scorecard / Allstar |
| 发布可验 | 签名 + provenance + SBOM | gpg（或 cosign）/ in-toto |

满足前四项即达到 C 类 L2；发布物同时满足 D-01 时达到 L3。

### 5.3 内嵌第三方片段（A 类补充，E-01）

- 内嵌代码必须保留原始版权头与许可证；`vendor/`、`third_party/` 目录必须有 `README` 说明来源、版本、许可证、修改点。
- 禁止以内嵌源码为名提交预编译二进制；如确需，按 4.2 管理。
- 内嵌代码升级必须记录上游版本与差异；GPL/LGPL 片段按 G-11 评审。

---

## 6. 发布/文档仓（D 类）专项

适用于 `kqmalloc-release`、`boostcph` 等以发布制品为主的仓库。

**校验单元是“发布元数据”，不是对每个文件做人工检查。** 业界成熟做法是构建溯源 + 签名 + 软件物料清单 + 不可变发布：SLSA provenance、in-toto、Sigstore/cosign、TUF、SPDX/CycloneDX、OpenVEX/OSV、Reproducible Builds（见附录 F）。落地为“四件套 + `verify.sh`”：

1. 制品（tarball/镜像/安装包）；
2. `SHA256SUMS` 与独立签名（`SHA256SUMS.sig` 或 cosign bundle）；
3. SLSA/in-toto provenance；
4. SBOM（SPDX 或 CycloneDX）；
5. `verify.sh`（验签 → 校验哈希 → 失败退出）。

- **D-01** 每个发布版本必须提供签名清单：`SHA256SUMS` + `SHA256SUMS.sig` + SBOM + provenance，并附可执行的 `verify.sh`。目标 SLSA Build L2；关键制品争取 L3。签名身份/公钥必须通过独立渠道公告，不得只放在同一 Release 页面。
- **D-02** 禁止发布未签名二进制；LFS 包必须附签名清单，部署脚本必须验签后再校验哈希。LFS/自动更新通道应提供 TUF 式元数据（root/timestamp/snapshot/targets、阈值签名、过期时间），至少也要有带版本号与过期时间的签名清单，以抵御回滚和冻结攻击。
- **D-03** 发布目录只读、版本不可覆盖；撤回必须公告并提供替代版本。发布操作双人复核；发布仓库应启用不可变策略，禁止用同名制品覆盖已发布版本。
- 平台不支持发布不可变时的降级方案（能力现状见附录 G）：版本目录只增不改；`SHA256SUMS` 文件名携带版本号（如 `SHA256SUMS-v0.22.0`）并整体签名，任何覆盖或篡改都会破坏签名一致性；CI 每日复算已发布版本哈希并与签名清单比对，发现不一致立即触发 G-16 撤回流程。
- **D-04** Release Notes 必须包含：版本、组件清单、哈希、签名验证步骤、SBOM/VEX 摘要、已知问题、许可证、支持期限。
- **D-05** LFS 指针变更必须经过发布评审；禁止直接替换指针指向未登记对象。变更 PR 必须同时更新 `BINARIES.yaml` 与签名清单，并记录旧/新 OID。
- **D-06** 文档中的安装示例必须使用“下载 → 验签 → 校验 → 安装”，禁止示例 `curl | bash` 或无校验安装；`verify.sh` 必须可重复执行且失败关闭。

### 6.1 D 类自动门禁（最小集）

| 门禁 | 检查内容 | 参考工具 |
| --- | --- | --- |
| 验签 | cosign/GPG 验签通过，证书身份/公钥符合公告 | cosign / gpg |
| 验溯源 | provenance 中的源码 commit、构建平台、参数与发布物一致 | slsa-verifier / in-toto |
| 验哈希 | `sha256sum -c` 全部通过，失败即中止 | sha256sum |
| SBOM 完整 | SPDX/CycloneDX 存在且覆盖主要组件与版本 | syft / trivy |
| 漏洞状态 | 高危漏洞有修复或 VEX 说明 | osv-scanner / Dependency-Track |
| 发布不可变 | 版本目录只读、同名制品不可覆盖 | 制品库策略 |
| 文档安全 | 安装示例不含 `curl` 管道执行、明文 HTTP、跳过校验 | CI 扫描 |

满足“验签 + 验溯源 + 验哈希 + SBOM”即达到 D 类 L3 的核心要求；L4 还需 TUF 式更新元数据或可复现构建（至少一项）、签名提交/标签和双人发布审计。

---

## 7. 技术实现标准

### 7.1 `BINARIES.yaml` 清单

完整模板见 `specs/binary-manifest.template.yaml`。字段要求：

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| `schema_version` | 是 | 固定 `1` |
| `repo` / `owner` / `reviewed_at` | 是 | 仓库、责任团队、复审日期 |
| `artifacts[].id` | 是 | 仓库内唯一，建议 `<kind>-<name>-<version>` |
| `artifacts[].kind` | 是 | `committed`/`lfs`/`download`/`runtime`/`release` |
| `artifacts[].path` / `url` | 视类型 | 仓库相对路径或 HTTPS URL，二者至少一个 |
| `artifacts[].version` | 是 | 精确版本；禁止 `latest` |
| `artifacts[].sha256` | 是 | 64 位小写十六进制；LFS 可用 OID，但发布物必须另附清单 |
| `artifacts[].signature` | 发布物必填 | cosign bundle / GPG 签名路径或 URL |
| `artifacts[].sbom` | 发布物必填 | SPDX 3.0.1 / CycloneDX 1.7 文件路径或 URL |
| `artifacts[].provenance` | 发布物必填 | SLSA/in-toto 溯源文件路径或 URL |
| `artifacts[].source` | 是 | 上游项目、发布者、许可证 |
| `artifacts[].reason` | 是 | 不可源码化的理由与用途 |
| `artifacts[].used_by` | 是 | 引用文件/脚本/模块 |
| `artifacts[].verification` | 是 | `auto-sha256`/`auto-signature+sha256`/`manual`/`lfs-oid` |
| `artifacts[].exception` | 否 | 例外编号与到期日 |

校验规则：

- `sha256` 必须匹配 `^[a-f0-9]{64}$`；
- `url` 必须 `https://`；
- `kind=committed|lfs` 必须有 `path` 且文件存在；
- `kind=download` 必须有 `https://` 的 `url`；
- `kind=runtime` 必须有固定安装 `path`；
- `kind=release` 必须有 `path`、`signature`、`sbom`、`provenance`，且四者不得为 `null`。

### 7.2 哈希与签名

- 哈希：SHA-256；禁止 MD5/SHA-1。
- 签名（主路线 GPG，与 G-07 一致）：
  - GPG 签名：`gpg --detach-sign --armor SHA256SUMS`；公钥指纹固定公告；
  - GPG 验证：`gpg --verify SHA256SUMS.asc SHA256SUMS`，失败即中止；
  - cosign（远期可选）：`cosign sign-blob --bundle artifact.sig.json artifact.tar.gz`；验证：`cosign verify-blob --bundle artifact.sig.json --certificate-identity <id> --certificate-oidc-issuer <issuer> artifact.tar.gz`。
- 发布目录至少包含：制品、`SHA256SUMS`、`SHA256SUMS.sig`、SBOM、provenance、`verify.sh`。

### 7.3 统一下载器（`tools/fetch`）参考实现

要求：

1. 只接受清单中存在的 `id`；
2. URL 必须 HTTPS 且主机在允许列表；
3. 下载到临时文件，限制大小与超时；
4. 计算 SHA-256 并与清单比对，不一致删除并退出非零；
5. 发布物校验签名（如配置）；
6. 原子移动到缓存目录，缓存键为 `sha256`；
7. 全程日志记录 URL、哈希、校验结果。

参考伪代码：

```bash
fetch() {
  id="$1"
  url="$(manifest_get "$id" url)"
  want="$(manifest_get "$id" sha256)"
  case "$url" in https://*) ;; *) die "non-HTTPS URL: $url" ;; esac
  tmp="$(mktemp)"
  curl -fsSL --proto '=https' --tlsv1.2 --max-time 300 -o "$tmp" "$url" || die "download failed"
  got="$(sha256sum "$tmp" | awk '{print $1}')"
  [ "$got" = "$want" ] || { rm -f "$tmp"; die "sha256 mismatch for $id"; }
  mv "$tmp" "$CACHE/$want"
}
```

### 7.4 安全加载参考实现

**C/C++**

```c
static void *load_verified(const char *path, const char *want_sha256) {
    char real[PATH_MAX];
    if (!realpath(path, real)) return NULL;
    struct stat st;
    if (stat(real, &st) || !S_ISREG(st.st_mode)) return NULL;
    if (st.st_uid != 0 && st.st_uid != geteuid()) return NULL;
    if (st.st_mode & (S_IWGRP | S_IWOTH)) return NULL;
    if (!sha256_file_equals(real, want_sha256)) return NULL;
    return dlopen(real, RTLD_NOW | RTLD_LOCAL);
}
```

**Java/JNI**

```java
Path dir = Files.createTempDirectory("boostkit-native");
try {
    Path lib = extractResource("/META-INF/native/linux-aarch64/libfoo.so", dir);
    verifySha256(lib, readExpectedSha256("/META-INF/native/linux-aarch64/libfoo.so.sha256"));
    System.load(lib.toAbsolutePath().toString());
} finally {
    deleteRecursively(dir);
}
```

### 7.5 CI 检查规则

必须包含以下检查（参考正则见附录 C）：

1. **禁止模式**：`--no-check-certificate`、`curl -k`、`http://` 下载、`curl|bash`、`wget ... | sh`、`GIT_SSL_NO_VERIFY`、`latest`、未固定 digest 的 `FROM`。
2. **清单覆盖**：`git ls-files` 中的二进制扩展名/LFS 指针必须全部出现在 `BINARIES.yaml`。
3. **哈希复算**：对 `kind=committed|lfs` 的文件复算 SHA-256。
4. **新增审批**：新增二进制或新增下载 URL 的 PR 必须带 `binary-approved` 标签与评审记录。
5. **发布门禁**：签名验证、SBOM 生成、provenance 归档、签名 tag 校验。
6. **许可证**：扫描并阻断未知/冲突许可证。
7. **B 类门禁**：补丁头完整性、`series` 可应用、干净基线重建。
8. **C 类门禁**：`UPSTREAM.yaml` 存在、上游 commit 固定、`range-diff` 增量清单一致。
9. **D 类门禁**：发布“四件套”齐全、`verify.sh` 可执行、发布目录不可变。

### 7.6 SBOM 与溯源

- SBOM 格式：SPDX 3.0.1 JSON 或 CycloneDX 1.7 JSON；
- 内容必须包含：组件名、版本、供应商、哈希、许可证、依赖关系；
- 发布物与 SBOM 必须同版本归档；
- 建议使用 `syft`/`trivy` 生成，CI 自动附加到 Release；
- 漏洞响应可用 OpenVEX/CSAF 表达“不受影响/已修复”，与 OSV 扫描结果一并归档。

### 7.7 存量清单脚本化生成

87 个仓库 5,000+ 个存量二进制，禁止逐个人工登记。统一按以下流程批量生成初稿：

1. **扫描**：仓库扫描脚本（参照 `analysis/scan.py`：遍历 `git ls-files`，按文件魔数识别 ELF/Mach-O/PE/ar/jar/压缩包，识别 LFS 指针）自动生成 `BINARIES.yaml` 初稿，自动填充 `id`、`kind`、`path`、`sha256`；
2. **目录级归并**：同目录下同来源的批量文件（测试固件、示例模型、文档图片等）合并为一条记录，`path` 写目录、`sha256` 写目录文件清单哈希（`sha256sum <dir>/* | sha256sum`），对应 1.1 条“目录 + 目录清单哈希”登记方式；
3. **人工补齐**：只补四个字段——`source`、`reason`、`owner`、`license`；`version` 从路径或上游 release 推断，取不到写 `unknown` 并登记例外；
4. **转入维护**：初稿通过 schema 校验（`specs/binary-manifest.schema.json`）与 CI 覆盖比对后，存量条目不再变动；此后只有新增/删除二进制时才改清单，维护成本收敛到增量。

分批期限：可执行/制品类（ELF、jar、安装包等 kind=committed）90 天内逐项登记；其余 LFS、文档类资源 180 天内完成目录级登记。

---

## 8. 评审、例外与治理

### 8.1 角色

| 角色 | 职责 |
| --- | --- |
| 仓库责任人 | 维护 `BINARIES.yaml`，发起引入/例外申请，执行复审 |
| 安全评审人 | 审核来源、签名、加载路径、威胁模型 |
| 发布经理 | 校验签名、SBOM、provenance，执行撤回 |
| 法务/开源合规 | 审核许可证与分发义务 |

### 8.2 引入流程

1. 提交《二进制引入申请》（模板见附录 B）；
2. 安全评审（≥ 1 名）与许可证评审；
3. 在 PR 中更新 `BINARIES.yaml`、签名/哈希文件；
4. CI 门禁通过；
5. 合并；发布时执行签名与 SBOM。

### 8.3 例外申请

必须包含：

- 制品标识、版本、哈希；
- 不满足的条款（G-xx/A-xx/B-xx/C-xx）；
- 风险说明与攻击面；
- 补偿措施（网络隔离、只读挂载、额外监控等）；
- 责任人、到期日（≤ 90 天）、整改计划。

例外由安全评审人 + 仓库责任人共同批准；到期未整改，CI 阻断发布。

到期阻断以脚本实现：CI 定时将清单中 `exception` 的到期日与当前日期比对，超期即向发布流水线注入失败步骤，不依赖平台特性。例外总量控制：组织级在役例外不超过 50 条；超限后新例外按“先销一条再进一条”申请，防止例外常态化掏空规范。

### 8.4 审计

- 每季度抽样 10% 仓库，核对清单、哈希、签名、CI 记录；
- 每半年全量扫描禁止模式与未登记二进制；
- 审计结果纳入仓库健康度评分。

---

## 9. 合规等级、度量与迁移

### 9.1 等级

| 等级 | 要求 |
| --- | --- |
| L0 未管理 | 无清单、无固定版本、无校验 |
| L1 清单化 | `BINARIES.yaml` 覆盖全部二进制，来源/责任人明确 |
| L2 可验证 | L1 + 版本固定 + TLS + 自动 SHA-256 失败关闭 + B/C 类基础门禁（补丁头/`series` 或 `UPSTREAM.yaml`/增量清单） |
| L3 可信任 | L2 + 发布物签名 + SBOM + provenance + 安装器/部署端验签 |
| L4 可追溯 | L3 + TUF 式更新元数据或可复现构建 + 签名提交/标签 + 定期复审 |

### 9.2 目标

| 仓库类型 | 30 天 | 90 天 | 180 天 |
| --- | --- | --- | --- |
| A 自研（有发布） | L1 | L3 | L4 |
| A 自研（无发布） | L1 | L2 | L3 |
| B 补丁仓 | L1 | L2 | L3（发布） |
| C Fork 仓 | L1 | L2 | L3（BoostKit 发布） |
| D 发布仓 | L2 | L3 | L4 |

### 9.3 度量指标

- 有 `BINARIES.yaml` 且通过校验的仓库比例；
- 已签名发布物比例；
- 关闭 TLS 校验的脚本数量（目标 0）；
- 未登记二进制数量（目标 0）；
- 未签名入库 ELF 数量（目标 0）；
- B 类补丁头完整率、`series` 可应用率、干净基线重建通过率；
- C 类 `UPSTREAM.yaml` 覆盖率、增量清单一致率、CVE 回移及时率；
- D 类“四件套”覆盖率、`verify.sh` 通过率、发布目录不可变符合率；
- 例外数量与超期率；
- 平均整改周期。

### 9.4 迁移计划

**第 1 阶段（0–30 天）：止血**

1. 冻结新增二进制（例外审批）；
2. 全量盘点，按 7.7 脚本化生成 `BINARIES.yaml` 初稿（可执行制品 90 天内逐项登记）；
3. 移除全部 `--no-check-certificate` 与明文 HTTP；
4. 修复 `Kbox-patches` 校验逻辑（失败即中止）、`CubeSandbox` 固定版本与 bundle 校验；
5. 对 `kqmalloc-release`、`boostcph` 发布物补 GPG 签名清单；
6. 对 `skills` 入库 ELF 补来源/哈希或下架；
7. 向 GitCode 组织管理员实测确认四项能力：MR 强制门禁、发布不可覆盖、保护 tag、审计日志，结果记入附录 G；未确认前相关条款按各自降级方案执行；
8. 在附录 D 点名仓中选择 `Redis`、`skills`、`kqmalloc-release`、`Kbox-patches` 试点“扫描 → 清单 → 门禁 → 签名”闭环，验证后再推广至全部 87 仓。

**第 2 阶段（30–90 天）：建体系**

1. 上线 `tools/fetch` 与清单 schema；
2. CI 接入禁止模式、清单覆盖、哈希复算、审批标签；
3. JNI/`dlopen` 加载点加固（`omnistatestore`、`omnioperator`、`OmniAdaptor`、`mooncake`、`overlaybd`、`KAE`）；
4. 发布流水线接入 cosign/GPG 签名、SBOM 与 provenance；
5. 补丁仓统一“固定基线 + 补丁头 + `series` + 源码重建 + 签名发布”；
6. Fork 仓统一 `UPSTREAM.yaml`（含上游 commit SHA）+ 增量清单 + `range-diff` 审查，同步模式按 C-01 执行按需同步。

**第 3 阶段（90–180 天）：可追溯**

1. 引入 SLSA provenance 与可复现构建；
2. 签名提交/标签，保护分支与发布权限；
3. 季度复审机制与审计看板；
4. 完成 Fork 仓增量隔离与 `UPSTREAM.yaml` 全覆盖；
5. D 类发布仓引入 TUF 式更新元数据，部署端强制执行验签/验哈希。

---

## 附录 A：`BINARIES.yaml` 模板

见 `specs/binary-manifest.template.yaml`。

## 附录 B：二进制引入/例外申请检查表

| 检查项 | 是/否 | 说明 |
| --- | --- | --- |
| 已证明无法源码构建 | | |
| 上游来源、版本、许可证明确 | | |
| 已固定 commit/tag/digest | | |
| 已提供 SHA-256 且来源独立 | | |
| 已提供上游或 BoostKit 签名 | | |
| 已评估加载路径与权限 | | |
| 已评估许可证义务 | | |
| 已登记 `BINARIES.yaml` | | |
| 已配置 CI 校验 | | |
| 已安排复审日期 | | |

面向流水线的逐条落地检查项见 `specs/二进制约束落地检查表.csv`（39 条，含适用类型、验证方法与不通过处理）。

## 附录 C：禁止模式清单（CI 扫描）

```text
--no-check-certificate
curl[^\n]*\s-k\s
wget[^\n]*--no-check-certificate
http://[^\s"']+\.(tar\.gz|tgz|zip|so|a|dll|jar|rpm|deb|bin)
(latest|master|main)(/|"|'|\s|$)
curl[^\n]*\|[^\n]*(ba)?sh
wget[^\n]*\|[^\n]*(ba)?sh
GIT_SSL_NO_VERIFY
npm[^\n]*strict-ssl[^\n]*false
pip[^\n]*--trusted-host
dlopen\s*\(\s*(getenv|argv|config->)
System\.loadLibrary\s*\(
Base64\.decode|b64decode
```

> 说明：`System.loadLibrary`、`b64decode` 等需结合上下文判定，命中后转人工评审，不直接判定违规。

## 附录 D：存量重点仓库整改对照

| 仓库 | 类型 | 关键问题 | 适用条款 | 优先动作 |
| --- | --- | --- | --- | --- |
| `Kbox-patches` | B | 未验签 `.asc`、失败重下继续、关闭 TLS | G-05/G-06/G-07、B-02 | 修校验逻辑、GPG 验签、失败中止 |
| `CubeSandbox` | C | latest、bundle 无校验、digest 不消费 | G-04/G-06/G-07、C-03 | 固定版本、校验 bundle、消费 manifest digest |
| `Redis` | B | 明文 HTTP、关闭 TLS、零校验 | G-05/G-06、B-02 | 改 HTTPS、固定版本、加哈希 |
| `KTAG`/`KADD` | A | 关闭 TLS 下载预编译依赖 | G-05/G-06、A-03 | 修 CA、加哈希、内部镜像 |
| `omnistream` | B/A | 下载无校验；UDF 路径防护较好 | G-05/G-06、G-08 | 下载加校验；保留并推广路径防护 |
| `skills` | A | 6 个无来源 ELF 直接执行 | G-02/G-06/G-07、A-02 | 补来源/哈希/签名或下架 |
| `omnistatestore` | A | 可预测临时路径 + 无哈希 | G-08、A-04 | 随机 0700 目录、哈希校验 |
| `omnioperator`/`OmniAdaptor` | A | jar 释放 `.so` 无哈希 | G-08、A-04 | jar 内清单哈希、绝对路径加载 |
| `mooncake`/`overlaybd` | C | 环境变量控制库路径 | G-08 | 固定路径、校验哈希、生产禁用覆盖 |
| `KAE` | A | 环境变量/配置控制驱动库路径 | G-08 | 固定路径、校验哈希、生产禁用覆盖 |
| `kqmalloc-release` | D | 手工校验、无签名 | G-07、D-01 | 发布签名 + SBOM + 自动校验 |
| `boostcph` | D | LFS 无签名清单 | G-07、D-02 | 签名 `SHA256SUMS`、部署验签 |
| `AgentENV` | C | 自动哈希但摘要同源、引导脚本 `curl` 管道执行 | G-07、C-03 | 发布签名、独立摘要、改引导方式 |

---

## 附录 E：与现有分析报告的对应关系

| 本规范条款 | 对应分析结论 |
| --- | --- |
| G-05 | 12 个仓库关闭 TLS、Redis 明文 HTTP |
| G-06/G-07 | 仅 1 个自动 SHA256、0 个验签 |
| G-08 | 29 个仓库运行期加载、JNI 临时路径问题 |
| G-09 | 0 个 SBOM |
| 第 5 章 | 补丁/Fork 仓的下载与校验缺陷 |
| 第 6 章 | 发布仓无签名清单 |

---

## 附录 F：业界参考模型与工具链

### F.1 三类仓库的校验单元

| 类型 | 校验单元 | 业界机制 | 代表项目/标准 | 本规范条款 |
| --- | --- | --- | --- | --- |
| B 补丁 | 补丁系列 | DEP-3 补丁头 + `quilt`/`series`；`Upstream-Status` 生命周期；upstream-first；stable backport 规则 | Debian、Yocto/OpenEmbedded、Fedora/openSUSE、Linux stable | B-01～B-05、5.1.6 |
| C Fork | 上游固定点 + 增量清单 | `DEPS` + `README.chromium`；`METADATA`；`moz.yaml`；Rust/Go vendor 清单 | Chromium、AOSP、Mozilla、Rust/Go 生态 | C-01～C-05、5.2.6 |
| D 发布 | 发布元数据 | SLSA provenance；in-toto；Sigstore/cosign；TUF；SPDX/CycloneDX；VEX | SLSA、Sigstore、CNCF TUF、SPDX、CycloneDX、OpenVEX/CSAF | D-01～D-06、6.1 |

### F.2 管理机制

| 标准/机制 | 作用 | 与 B/C/D 的关系 |
| --- | --- | --- |
| OpenChain ISO/IEC 5230 | 开源许可证合规管理体系 | B/C 的许可证与 NOTICE 义务 |
| OpenChain ISO/IEC 18974:2023 | 开源安全保证体系，含 SBOM、漏洞检测/响应、CVD；11 条款、25 项验证材料 | B/C/D 的程序化外壳 |
| NIST SSDF SP 800-218 | 安全软件开发框架（PO/PS/PW/RV） | 全流程 |
| EU CRA (EU) 2024/2847 | 2026-09-11 起漏洞/事件报告；2027-12-11 起全面适用，含 SBOM | 对欧交付的 D 类 |
| ISO/IEC 29147 / 30111 | 漏洞披露与处置 | B/C/D 的 PSIRT/CVD |
| OpenSSF Scorecard / Allstar | 仓库健康度与策略执行 | C 类基线、D 类发布检查 |
| OpenChain Telco SBOM Guide | 电信行业 SBOM 实践 | 运营商交付场景 |

### F.3 推荐工具链

| 环节 | 工具 |
| --- | --- |
| B 补丁 | `quilt`、`git format-patch`/`git am`、`git range-diff`、`patch`、Yocto `devtool`/`cve-check` |
| C Fork | `repo`、`git subtree`、`cargo vendor`、`go mod vendor`、`npm ci`、Renovate/Dependabot（含 cooldown）、`osv-scanner`、`syft` |
| D 发布 | `cosign`、`slsa-verifier`、`in-toto`、`python-tuf`/`go-tuf`、`oras`/`notation`、`diffoscope`/`reprotest`、`syft`/`trivy`、Dependency-Track |
| 通用 | OpenSSF Scorecard、Allstar、GUAC（可选） |

### F.4 版本基线（2026-11）

- SLSA v1.2，Build track L1–L3；Source track 逐步采用。
- SPDX 3.0.1 为当前稳定版，3.1 RC1 已发布；CycloneDX 1.7（ECMA-424 第 2 版），2.0 在途。
- TUF、Sigstore、in-toto 作为发布与更新安全的默认组合。
- OpenChain ISO/IEC 18974:2023 作为安全保证体系参考。
- EU CRA：2026-09-11 报告义务生效；2027-12-11 全面适用（含 SBOM）。

### F.5 参考链接

- [SLSA v1.2 规范](https://slsa.dev/spec/v1.2/zonepage)
- [Debian DEP-3 补丁头规范](https://dep-team.pages.debian.net/deps/dep3/)
- [Yocto 补丁与 Upstream-Status](https://docs.yoctoproject.org/dev-manual/common-tasks.html)
- [Chromium third_party/README.chromium](https://chromium.googlesource.com/chromium/src/+/main/third_party/README.chromium)
- [AOSP METADATA](https://source.android.com/docs/setup/about/metadata)
- [The Update Framework](https://theupdateframework.io/)
- [Sigstore](https://www.sigstore.dev/)
- [in-toto](https://in-toto.io/)
- [SPDX 3.1 RC 公告](https://spdx.dev/spdx-3-1-ontology-and-schema-available-for-review/)
- [CycloneDX 规范](https://cyclonedx.org/specification/overview/)
- [OpenChain ISO/IEC 18974 符合性指南](https://openchain-project.github.io/OpenChain-KWG/en/guide/iso18974_guide/)
- [NIST SSDF](https://csrc.nist.gov/projects/ssdf)
- [EU Cyber Resilience Act](https://digital-strategy.ec.europa.eu/en/policies/cyber-resilience-act)
- [OpenSSF Scorecard](https://github.com/ossf/scorecard)
- [OSV](https://osv.dev/)
- [Reproducible Builds](https://reproducible-builds.org/)

---

## 附录 G：GitCode 平台能力对照（2026-11 基线）

| 能力 | 状态 | 对本规范的影响 |
| --- | --- | --- |
| 分支保护、CodeOwners、组织级权限 | 已确认支持 | G-12 主路线可用 |
| `.workflow/` CI 流水线 | 已确认支持（`cloud-native` 等仓在用） | 全部自动门禁的运行载体 |
| GPG 签名验证 | 已确认支持 | G-07 GPG 主路线可用 |
| Git LFS | 已确认支持 | 发布仓需确认组织级配额（免费版单仓 1 GB） |
| cosign keyless（OIDC 身份联合） | 不可用 | G-07 改 GPG-only，cosign 列为远期 |
| MR 检查结果强制阻断合并 | 待组织管理员实测 | 不可用时 G-12 走每日扫描兜底 |
| 发布不可覆盖（Release 不可变） | 待组织管理员实测 | 不可用时 D-03 走版本化清单降级方案 |
| 保护 tag / 签名 tag 校验 | 待组织管理员实测 | 影响 G-14、D-01 的 tag 强制项 |
| 审计日志留存 | 属企业版能力 | 不足时以 CI 归档日志替代（G-16 要求 180 天） |

> 实测责任：GitCode 组织管理员；实测结果直接更新本表。“待实测”项落定前，相关条款按各自降级方案执行，不阻塞其余条款落地。

---

**附：规范落地的最小可执行集合**

若资源有限，优先完成以下 8 项，可消除大部分高风险（全部可用 CI 脚本实现，不依赖附录 G 中“待实测”的平台特性）：

1. 全仓 `BINARIES.yaml`（按 7.7 脚本化生成初稿，人工只补 source/reason/owner/license）；
2. 禁止 `--no-check-certificate` 与明文 HTTP；
3. 所有下载物 SHA-256 失败关闭；
4. 发布物 GPG 签名 + `verify.sh` 校验脚本；
5. JNI/`dlopen` 加载前哈希校验；
6. 新增二进制审批门禁（二进制变更缺清单更新即流水线失败）；
7. B 类补丁头（`Origin`/`Upstream-Status`）+ `series` + CI 重建；
8. C 类 `UPSTREAM.yaml`（含上游 commit SHA）+ 增量清单 + `range-diff` 审查，按需同步。
