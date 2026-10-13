# dsh-wallpaper-engine 社区 PR 批量整合报告

- 时间：2026-10-13（Asia/Shanghai）
- 仓库：`elysia395/dsh-wallpaper-engine`
- 整合分支：`integration/pr-batch`，**HEAD `9c14480`**（= `623fe1f` + 三笔 CI 跨平台修复 + 一笔 main 合并，见 4.6），基于本地线 `feat/webwallgl-npm-3.0.0` @ `877952e`（领先 `origin/main` 113 笔，落后 0 笔）
- 本轮范围：GitHub 上全部 **7 个 open PR**（#196 / #199 / #201 / #202 / #203 / #204 / #206）
- 结论：**6 个已本地合并（移植）进整合分支并全链验收；#196 已是本地线祖先，本地无需动作、只需决定何时合入 main**。评审发现并已在本线修掉 **4 条 P1**（#202 AVIF 尺寸探针、#203 对比度红线、#204 选择器截断、#199 判据缺牙 + 404 文案因果），修复批次另跑一轮全链验收。
- 交付物：本报告、`GITHUB-HANDOFF.md`（回灌文稿：7 条 PR 评论 + 2 条 issue + 命令备忘）、`patches/`（8 个可直接 `git apply` 的补丁）、以及仓库既有的 `ENGINEERING-REPORT.md`
- **已回灌（2026-10-13）**：`integration/pr-batch`（PR [#207](https://github.com/elysia395/dsh-wallpaper-engine/pull/207)）与 `integration/handoff-docs`（本报告 + 补丁）已 push；7 个 PR 各发一条评论；issue [#208](https://github.com/elysia395/dsh-wallpaper-engine/issues/208) / [#209](https://github.com/elysia395/dsh-wallpaper-engine/issues/209) 已开；**`main` 已合并（2026-10-13）**：PR [#207](https://github.com/elysia395/dsh-wallpaper-engine/pull/207) 已 merge 进 `main`，merge commit `9acdf0101e596ab8525d9dd8817ad0af978fbf00`（`mergedAt 2026-10-13T15:48:55Z` = 北京时间 23:48）。合并后证据：main push CI run `38425270513` **ubuntu-latest + windows-latest 全绿**；在 `origin/main` 的工作树里复跑 `node test/run-guards.mjs all` = **70 步 EXIT=0**（0 条 ✗、无软档降级）、`node test/verify-static.mjs` = `ALL STATIC GATE CHECKS PASSED`。
- **收口（2026-10-14 00:1x 北京时间）**：#210 的 flaky 已修 —— PR [#211](https://github.com/elysia395/dsh-wallpaper-engine/pull/211)（单笔 `51f3b32`，只改 `test/verify-client.mjs`）merge 进 `main`，merge commit `8244035`；main push CI run `38428381243` 双平台绿；在 `origin/main` 工作树复跑 `node test/run-guards.mjs all` = **70 步 EXIT=0**（3516 条 ✓ / 0 条 ✗ / 无软档降级）；issue [#210](https://github.com/elysia395/dsh-wallpaper-engine/issues/210) 已关。四个 GitHub 不会自动标 merged 的 open PR（#202 / #203 / #204 是 fork PR，#206 是跨分支）已各发一条「内容已随 #207 进 `main`，按取代关闭」的说明评论并关闭（评论 id `6149655450` / `6149656126` / `6149656838` / `6149657634`）；#196 / #199 / #201 因头提交成为 `main` 祖先被 GitHub 自动标为 MERGED。至此**仓库 0 个 open PR**（`patches/` 仍留档，供作者往自己分支回灌）。

---

## 0. 速览

| PR | 作者 | 主题 | 值不值得 | 是否达标 | 本地处置 |
|---|---|---|---|---|---|
| [#196](https://github.com/elysia395/dsh-wallpaper-engine/pull/196) | @oneincase | WebWallGL 引擎改 npm 运行期依赖，移除 vendored 副本 | **值得** | **达标**（仅 3 处文档漂移） | 已是本地线祖先，无需动作 |
| [#199](https://github.com/elysia395/dsh-wallpaper-engine/pull/199) | @elysia395 | #188 升级窗口期「作者自用」弹窗必败 | **值得** | 达标（缺「先红后绿」证据 → **本线已补**） | ✅ `09deacc`（+ 后续 `6200461` `ff3df77`） |
| [#201](https://github.com/elysia395/dsh-wallpaper-engine/pull/201) | @jassdingk | 思考块行内代码跨行折断不再糊成整列霜釉 | **值得** | 条件达标（fork 无 CI run） | ✅ `9f4ea82` |
| [#203](https://github.com/elysia395/dsh-wallpaper-engine/pull/203) | @jassdingk | 原生 select 弹层行底与对比墨（#164） | 有条件值得 → **本线修完 P1 后值得** | 条件达标（**P1 对比度不达标 → 已修**） | ✅ `6b378a0`（+ `40a97fb`） |
| [#204](https://github.com/elysia395/dsh-wallpaper-engine/pull/204) | @tangdogdaihuman | 额外补雾化选择器（#200） | 有条件值得 → **本线修完 P1 后值得** | 条件达标（**P1 400 字静默截断 → 已修**） | ✅ `dc7bb3c`（+ `7f85a93` `eb1aa14`） |
| [#202](https://github.com/elysia395/dsh-wallpaper-engine/pull/202) | @Fish-under-sea | 头像 SVG 等格式导入 + 两道闸 + 光效合批 + 三档重置（取代 #198） | **值得** | **达标**（**P1 AVIF 边长闸失效 → 已修**） | ✅ `dd25f1c` → `30541ce`（+ `cda8149` `176a90d`） |
| [#206](https://github.com/elysia395/dsh-wallpaper-engine/pull/206) | @yuxilao | GPU 抓帧缓存改单主帧 + 面积升级制 | **值得** | 达标，但**不能直接 merge** | ✅ `f788a33` |

**最终验收（`623fe1f` 本地链 / `9c14480` 加上 CI）**

```
▶ all —— 70 步（硬档 63 / 软档 7）
✓ all 全部 70 步执行完毕                                    EXIT=0      （3444 条 ✓，0 条 ✗，0 条软档降级）
✓ 提交物与重建结果一致（按提交形态 LF 比较） — 4540090 B
ALL STATIC GATE CHECKS PASSED                               （npx eslint src test lib → 0 error）
```

CI（首次跑通，见 4.6）：PR [#207](https://github.com/elysia395/dsh-wallpaper-engine/pull/207) 的 `verify` 在 **ubuntu-latest + windows-latest 全部 success**（run `38423298899`），PR 状态 `MERGEABLE` / `CLEAN`。

基线 `877952e` 为 **68 步全绿**；`70 − 68 = 2` 正是 #206 新增守卫及其派生步。整链日志：`/tmp/chain-accept.log`（另有两轮：`/tmp/chain-final.log` @`176a90d`、`/tmp/lead-final-all.log` @`419b6fe`）。

---

## 1. 本地合并顺序（问题 1）

### 执行的顺序与理由

| 序 | PR | 提交 | 为什么排这里 |
|---|---|---|---|
| 1 | #199 | `09deacc` | 面最小：1 个真冲突（`docs/ROUTE-INDEX.md`，工具重生）+ 1 个派生文档，先落地把整合成本压到最低 |
| 2 | #201 | `9f4ea82` | 纯样式 + 守卫 + CHANGELOG；把「样式域模块移植」这件事在该族最早做掉 |
| 3 | #203 | `6b378a0` | 样式 + effects，紧跟 #201：`docs/TOKEN-CONTRACT.md` 连着重生两次，而不是与别的族交叉重生 |
| 4 | #204 | `dc7bb3c` | 新增模块并改守卫计数（GUARD-MAP 127/71 → 128/72）与 `test/run-guards.mjs` 注册面，放在样式族之后一次性重算 |
| 5 | #202 | `dd25f1c` → `30541ce` | 面最大（28 文件、+3842/−191）。**硬约束**：它带来的跨 PR 覆盖面守卫要求「每个可重置 / 可配键都被消费」，必须等 #203 / #204 把新令牌与新选择器加进来之后才合，否则守卫必红 |
| 6 | #206 | `f788a33` | 最新开、最「热」的一笔，且要改 `test/run-guards.mjs` 注册与重冻结链快照，放最后避免在别人底下反复重冻结 |
| 7 | P1 修复批次 | `cda8149` … `623fe1f`（9 笔） | 独立成段落在 6 个 PR 之后（见 4.4），避免与移植笔混在一起、也避免再动一次链快照 |

补充说明：

- 硬约束只有两条：**#202 必须在 #203 / #204 之后**（覆盖面守卫）；**#206 最后**（链快照与守卫计数的重冻结成本）。其余顺序是成本优化，不是正确性依赖——评审已用「合体树」验证过 #199+#201 互不干扰、三方互不干扰。
- 为什么不是 `git merge` 上游分支而是**逐个移植**：本地线已把巨石拆成域模块（`src/styles.js` 只剩 31 行组装点，`src/client.js` / `src/i18n-copy.js` 同理），而 6 个 PR 全部基于巨石。直接 `git merge` 要么冲突（#206 实测 8 个文件冲突），要么会静默把巨石复活、或悄悄放宽本地棘轮（#206 会放宽 `test/verify-scene.mjs` 的 P4-16、回流 `dir` 死字段）。
- 若改在 GitHub 上直接合（不经过本地线），顺序建议：#196 独立（对 main 是 fast-forward）→ #199 → #201 → #203 → #204 → #202 → #206；#203 与 #204 可互换，但 #202 与 #206 仍应压在最后。
- 顺序敏感性的实测数据（A 先合再合 B 的冲突信息行数）：#202 应尽量先合（#199→#202 = 49 行 vs #202→#199 = 9 行；#203→#202 = 45 vs #202→#203 = 11）；#204 早于 #201/#203 更省（#204→#201 = 15 vs #201→#204 = 26）。这两条与上面两条硬约束并不冲突：在本线，#202 的落点已被域模块化吸收，实际冲突只有 4 处（见 4.1）。

---

## 2. 值不值得合并（问题 2）

### ✅ 直接值得

- **#196**：真问题——删掉 6158 行 vendored WebWallGL 副本、改 `webwallgl@^3.0.0` + `x11@^4.3.1` 运行期依赖；非 Linux 环境不加载 x11（darwin 实测 snapshot `{"ok":1,"backend":"none"}`，只打一行日志不抛）。无阻塞级缺陷。
- **#199**：问题真实（升级窗口期「作者自用」弹窗必败），机制已在活宿主用 curl 复核（`404 {"error":"unknown preset"}`）。
- **#201**：三处变异各自判红（删语义锚 → F2d3b3 红；降级选择器 → F2d3b2 红；整条规则删 → F2d3b 红）；两个承重锚点都在安装版宿主里实测存在（`data-variant: "think"`、`thinkBody: "HiSsxa_thinkBody"`、compact 变体自带 `box-decoration-break: clone`）。
- **#202**：功能面最大且带三组行为级新守卫；**它的跨 PR 覆盖面守卫正是本轮发现 #203 / #204 覆盖缺口的工具**——这一点本身就值它占的位置。行为级证据：光效层一帧绘制调用 **109 → 4**、真浏览器帧间隔 p95 **16.70 ms → 8.50 ms**。
- **#206**：真问题（多窗口互踢、resize 写盘），带行为级新守卫（18 条）。

### ⚠️ 有条件值得（原判：修掉 P1 才建议落 main）

- **#203**：判据质量在本批里最高（F8 四作用域 + 6 条负对照、F9/F10 对比度网格，产物与派生文档都能复现），但**对比度红线实际不达标**：`src/effects.js:157` 的 `weCheckedRowInk(hex)` 注释与 F10 声称「对比度取大 ⇒ ≥4.5」，实测 accent `#de3c42` 配 `#0f1115` 只有 **4.347:1**；256 档灰中有 **5 档 < 4.5**（最差 `#797979` → `#ffffff` = 4.353:1）；同一 accent 换纯 `#000000` 可得 **4.831:1** ⇒ 不是无法达标，是墨臂挑得不对（`#0f1115` 把全局最差锁死）。F10a 只采样 9 档（最差 4.72）所以全绿 —— 判据缺口。**→ 已在本线修掉：`40a97fb`（见 4.4）。**
- **#204**：方向正确（消毒贯通、金夹具与派生文档可复现、两次突变都能断言式判红），但 `lib/settings-schema.js:139` 的 `.slice(0, 400)` 会**静默产出半截选择器**：合法上限是 8×120+14 = **974**，实测 8 条各 120 字 → 输出 400 B / 4 条，第 4 条被切断，与 `README.md:193`「最多 8 条」自相矛盾；且该 PR 直接并进本地线还会踩两条本地硬门（ESLint 未用变量、settings-schema 零消费者导出）。**→ 已在本线修掉：`7f85a93` `eb1aa14`（见 4.4）。**

### ❌ 现状不值得（修完才值得）

- 无（本批 7 个 PR 方向上没有一个是「不该合」的）。#204 是本批唯一一个「现状不能要、修完可以要」的（P1 截断 + 两条本地硬门）。

---

## 3. 是否达到合并要求（问题 3）

判据口径：GitHub CI 有证据 / 新增判据「先红后绿」/ `npm run build` 产物逐字节同步 / `verify`+`docs`+`smoke` 三组绿 / 派生文档同步 / CHANGELOG 与版本约定 / 无 P0。

| PR | GitHub CI | 先红后绿 | 产物同步 | verify+docs+smoke | 派生文档 | CHANGELOG | 裁定 |
|---|---|---|---|---|---|---|---|
| #196 | **4/4 SUCCESS**，`MERGEABLE/CLEAN` | 线内（n/a） | ✅ | ✅ | ✅ | ✅ | **达标**，仅 3 处文档漂移 |
| #199 | **4/4 SUCCESS** | ❌ **无证据** | ✅ | ✅ | ✅ | ❌ 未加 | 达标，缺一档证据（**本线已补齐**） |
| #201 | ❌ 0 check-run（fork） | ✅ | ✅ | ✅ | ⚠️ 需重生 | ✅ | 条件达标 |
| #202 | **2/2 SUCCESS** | ✅ | ✅ | ✅ | ✅ | ✅ | **达标** |
| #203 | ❌ 0 check-run（fork） | ✅ | ✅ | ✅（须短路径） | ✅ | ✅（PR 自带） | 条件达标（**P1 已修**） |
| #204 | ❌ 0 check-run（fork） | ✅ | ✅ | ✅ | ✅ | ❌ 未加 | 条件达标（+ 两处本地硬门，**均已修**） |
| #206 | **4/4 SUCCESS** | ✅ | ✅ | ✅ | ⚠️ 需重生 | ✅ | 达标，但**不能直接 merge** |

要点：

- **没有任何 PR 有 review 记录**（`reviewDecision` 全空）；`origin/main` 也没有分支保护（`branches/main/protection` → 404），所以 GitHub 侧「可合」只由 CI 与 mergeable 决定。
- **#201 / #203 / #204 是 fork PR，GitHub 上零 workflow run**（`statusCheckRollup` 为空、`mergeStateStatus=UNSTABLE`）。这三个的「达标」完全靠本地补证：#201 靠三处突变、#203 靠 F8/F9/F10 突变格、#204 靠两次突变成断言式红 + 消毒恒等突变。
- #199 的缺口是**缺「先红后绿」证据**：回退它的源码后重建并重生 ROUTE-INDEX，`verify` 仍 55 步全绿 ⇒ 原守卫抓不到它修的 bug。本线已补 `test/verify-presets.mjs` 三条判据（`6200461`，先红后绿证据见 4.4）。
- #204 的 `package.json` 改动在本地线**不合法**：本地 `P5` 硬断言 `scripts.verify` 的值恰为 `node test/run-guards.mjs <group>`，所以它的长 `&&` 链插入改走 `test/run-guards.mjs` 的 MANIFEST 注册 + 重冻结 `test/fixtures/verify-chain.json`。同理 #206。
- #206 的 `docs/en/CHANGELOG.md | 318 +------` 是**底座漂移而非有意删除**：main 1931 行 / PR 1615 / 其父 `0e7c806^` 1896；删掉的正是 main 那笔 release 提交 `0e7c806`（v1.3.1 published）加的标题 + 41 条英文条目；对 main 历史 13 个提交逐一比对，没有任何版本等于 PR 那份文件。处置：**拒删**，以 HEAD 版为底只插那 1 行新条目（落地即 `+2/−0`）。
- 逐 PR 的「可合 / 不可合」三级判词与 7 条评论草稿见 `GITHUB-HANDOFF.md`。

---

## 4. 本地合并后测试（问题 4）

### 4.1 整合提交序列

**第一批：6 个 PR（`877952e..419b6fe`，8 笔，`65 files changed, +6020/−707`）**

```
419b6fe fix(static): 修 #202 移植（dd25f1c）引入的 19 条静态门禁违规 —— verify-static 转绿
f788a33 merge(#206): GPU 抓帧缓存改「单主帧 + 面积升级制」
30541ce merge(#202): 头像导入两道闸 + SVG 等格式、三档重置、光效合批、上传裁切与头像分组
dd25f1c feat(client): 移植 PR #202（@6a7fc98）
dc7bb3c feat(glass): 移植 PR #204（@404ff40）—— 额外补雾化的选择器（#200）
6b378a0 feat(styles): 移植 PR #203（@4c864b7）—— 原生 select 弹层行配对 + 选中行 accent-src/对比墨
9f4ea82 merge(#201): 思考态代码块行内码玻璃填充收口
09deacc merge(#199): 预设路由探针 404 语义 + 版本过期提示
```

逐笔：`+95/−7`（#199）、`+155/−33`（#201）、`+469/−43`（#203）、`+674/−55`（#204）、`+3842/−191`（#202）、`+829/−382`（#206）、`+10/−54`（静态门禁修复）。
冲突处置：#199 只 `docs/ROUTE-INDEX.md` 冲突（`host-route-index.mjs --write` 重生）；#201 冲突 `docs/TOKEN-CONTRACT.md` + `src/styles.js`（取本地 31 行组装点，把 21 行规则**移植**进 `src/styles-shell.js`；TOKEN-CONTRACT 由 `token-contract.mjs --write` 重生）；#203 四条 option/optgroup 规则移植进 `src/styles-settings.js`、两条新令牌进 `src/styles-base.js`、`src/effects.js` 三方合并自动成功；#204 `lib/settings-schema.js` 与 `src/i18n-copy.js` 冲突（后者改走域词表 `src/i18n-copy-glass.js`，`package.json` 一字未改）；#202 4 处冲突（`docs/GUARD-MAP.md`、`lib/client.js`、`src/glass-panel.js` 与 `src/quick-panel.js` 的 add/add 取并集）。逐 PR 落点表见 `GITHUB-HANDOFF.md` 与评审记录。

**第二批：P1 修复（`419b6fe..623fe1f`，9 笔，`18 files changed, +1093/−84`）**

```
623fe1f docs(changelog): 同步 P1 修复口径（AVIF 读头 / #164 墨臂与 F10）+ 补 #204 额外补雾化条目
176a90d fix(avatar): 残留洞 A —— 256 KB 读头让 mdat 在前的 AVIF 绕过边长闸
eb1aa14 #204 面板提示补齐 120 字/整条丢弃口径
bb6f9ba chore(guard-map): 重算 #199 新判据带来的 i18n 词表覆盖（6200461 的派生产物）
ff3df77 fix(presets): #199 后续 —— 404 的因果两分：路由在、预设真没了别再叫人重启 DSH
6200461 test(presets): 给 #199 补「先红后绿」判据 —— 随包层 422 / 404 reason / 弹窗探针门控
7f85a93 fix(glass): #204 选择器总量上限不再截断成半截选择器 —— 修复 PR #204 P1
40a97fb fix(styles): #203 选中行墨色按实算对比度取大 + F10 连续网格判据 —— 修复 PR #203 P1
cda8149 fix(avatar): AVIF 尺寸探针（ISOBMFF/iprp/ipco/ispe）补全 —— 修复 PR #202 P1
```

全量：`877952e..623fe1f` = **66 files changed, +7038/−716**（含两笔 PR head 对象 `cb1253a` / `d0ac039` 随合并进入本地历史）。

### 4.2 验收结果

| 验收项 | 命令 | 结果 |
|---|---|---|
| 基线（合并前） | `node test/run-guards.mjs all` @`877952e` | 68 步全绿，EXIT=0，红集为空 |
| 第一批后全链 | `node test/run-guards.mjs all` @`419b6fe` | 70 步（硬档 63 / 软档 7）全绿，EXIT=0 |
| **最终全链** | `node test/run-guards.mjs all` @`623fe1f` | **70 步全绿，EXIT=0；3444 条 ✓ / 0 条 ✗ / 0 条软档降级** |
| 产物同步 | 链内 `[1/70] npm run build` + `[2/70] verify-client-sync` | `lib/client.js` **4,540,090 B**，blob `cd7fec9a047c491f902e6f60c38efce884add40d`，与重建逐字节一致（含「差一个字节判红」负对照） |
| 静态门禁 | `node test/verify-static.mjs` / `npx eslint src test lib` | `ALL STATIC GATE CHECKS PASSED` / 0 error |
| 关键守卫抽样 | 各 PR 定向 | `verify-scene-frame-master` 18；`verify-scene` 132 passed；`verify-scene-live` **679**（原 670）；`verify-client` 全绿；`client-sync` 5；`contracts` 全绿；`settings-schema-exports` 15；`verify-presets` **225**（其中 11 条在修复前判红，修复后全绿）；`verify-extra-glass` 47；`verify-i18n` 44 |
| 派生文档 | 各自 `--write` 后 `git status` | `docs/GUARD-MAP.md`（130 模块 / 73 守卫 / 零覆盖 0）、`docs/ROUTE-INDEX.md`、`docs/TOKEN-CONTRACT.md`、`test/fixtures/verify-chain.json`（verify 57 步）均与工具输出零漂移 |

### 4.3 第一批过程中发现并修掉的问题

1. **`verify-static` 被 #202 移植弄红（19 条），已修**（`419b6fe`，rev-c 执行、Lead 复核）。
   Lead 用 /tmp worktree 二分证伪了「这是既有红」的判断：`9f4ea82`（#202 之前）= `ALL STATIC GATE CHECKS PASSED`，`dd25f1c` 与 `30541ce` = `超限=[no-useless-assignment 5 · no-dupe-keys 10 · no-promise-executor-return 1 · no-unused-vars 2 · object-shorthand 1]` ⇒ 是 #202 移植引入。根因是 `src/client.js` 的状态块（吉祥物导入态 / 上传目录编辑器 / 缓存位置编辑器，含注释）被整段重复插入；处置为删第二份 + 去无用初值 + 删死变量，全部等价改动，**没有用 `--write-baseline` 把违规冻成基线**。
2. **丢失更新事故（已恢复）**：并行移植期间一次 `git checkout -- <file>` 把主树里刚落的 #203 文件改动抹掉。恢复方式：从 rev-b 的 worktree 分支取回文件并 `git add` 重新提交。防复发纪律已改为「**每落一批先 `git add` 暂存再跑守卫**」，且此后主树只允许一个写者。
3. **环境陷阱：长 checkout 路径会触发 ENAMETOOLONG 假红**。`verify-scene` 的 GPU 缓存名形如 `lf1_<base64(abs path)>.png`，深目录下超过 NAME_MAX 255（实测 258 B）⇒ PUT 落盘 500，18 条与代码无关的红；同一份代码在 `/tmp` 短路径下 130 passed / 0 failed。**最终验收一律在短路径主树（56 字符）上跑**；这本身是仓库的健壮性问题，建议单开 issue（缓存键改用短哈希，草稿见 `GITHUB-HANDOFF.md` 的 issue-A）。

### 4.4 第二批：P1 修复（每条都带「先红后绿」证据）

| 修复 | 提交 | 改了什么 | 先红后绿证据 |
|---|---|---|---|
| #202 AVIF 尺寸探针 | `cda8149` | `src/avatar-import.js` 新增 ISOBMFF 解析（`ftyp` 品牌门 → `meta` → `iprp` → `ipco` → `ispe`，多图认 `ipma` 主图），接进 `probeImageSize`；`test/verify-scene-live.mjs` 新增 9 条（含三喂负对照与「ispe 宽高互换」变异） | 红：未修树上 `7 failed, 671 passed`（`4096×2048` 被读成 `2048×4096` 等）；绿：`ALL SCENE-LIVE CHECKS PASSED (679)`。另用真 ffmpeg AVIF 样本（354 B、声明 2000×1200）验证过闸 |
| #202 残留洞 A（审查中发现） | `176a90d` | 位图族仍读 **256 KB**；**AVIF/HEIF 族**在 256 KB 量不出尺寸时按 `min(file.size, 4 MiB)` 重读一次（`meta` 允许排在 `mdat` 之后），仍只读字节不解码 | 红：`ftyp + 3.4 MB mdat + meta`（3.24 MiB < 体积闸）在修复前 `probe=null`、`{"ok":true}` 放行；修复后边长闸 `overEdge` 拒绝。独立复核（rev-d 自造夹具 3,565,238 B、非方形 2048×1536）：pre/post 全档 diff 只有 2 行，非族（`mp42`）与「PNG 签名 + 垃圾字节」逐字节相同，`overBytes` 语义未动 |
| #203 对比度红线 | `40a97fb` | `weCheckedRowInk` 墨臂改为纯 `#000000` / `#ffffff` 择优；CSS 兜底墨同步为 `#000000`；F10 扩成连续网格（256 灰阶 + 16³ 采样 + 最差点邻域细化 = **4388 档**）+ 新增 F10c（兜底墨必须等于 JS 判决）+ 三条负对照 | 红：连续网格上最差 `#ee0660` = 4.347:1（< 4.5，177 档不合格）；绿：最差 **4.5826:1**（`#927030` → 白墨）、256 灰阶最差 **4.6075:1**、低于 4.5 的档 **0**；`#de3c42` 4.347 → **4.831**。Lead 另从**产物**里抽出函数源码独立复算（32³ 立方 + 30 万随机点均收敛到 4.5826） |
| #204 选择器总量上限 | `7f85a93` | `EXTRA_GLASS_SELECTORS_MAX` = 8×120+14 = **974**，删掉 `.slice(0, 400)`，改为「逐条白名单校验 → 到第 8 条即停 → 整条丢弃、绝不截断」；README 中英同步；§②b 加 5 条断言（含 300 组随机输入 violations=0） | 红：8 条各 120 字 → 输出 400 B / 4 条、第 4 条半截；绿：8 条互异 × 120 字 → 8 部分 / 974 B；9 条与 12 条 → 仍 8 部分；单条 121 字 → 整条丢弃；坏条只丢自己 |
| #204 面板提示 | `eb1aa14` | `src/i18n-copy-glass.js` 中英键 + `src/glass-panel.js:731` 文案补齐「最多 8 条 / 每条 ≤120 字 / 整条丢弃不截断」 | 复核确认 i18n 键与 `weT()` 字面量**逐字节相同**（英文不会掉回中文），产物 blob 与 HEAD 同 hash |
| #199「先红后绿」判据 | `6200461` | `test/verify-presets.mjs` **+166 行**：③e 随包层旧 tag ⇒ 422 `version-obsolete`（非 404）、④c 真实调用 `glassPresetFailureReason` 桩、`PresetIntroDialog` 探针门控；CHANGELOG 中英各一条 | 红：回退 `lib/routes/presets.js` + `src/client.js` + `src/preset-store.js` 并重建 ⇒ **8 ✗**，链在 `[43/57]` 短路；绿：**225 passed**，`verify` 57 步 / `docs` 6 步 / `smoke` 6 步全绿 |
| #199 404 文案因果 | `ff3df77` | `src/preset-store.js` 按 `reason` 两分：`missing`/`tombstone` ⇒「该预设不存在 / 已删除」；**无** `reason`（= 旧宿主 = 升级窗口期）才提示「重启 DSH 后再试」 | 与 `lib/routes/presets.js:711-713` 的 `reason` 字段对齐；活宿主 curl 复核 |
| 派生产物重算 | `bb6f9ba` | `6200461` 的新判据会读 `src/i18n-copy*` 词表族 ⇒ `docs/GUARD-MAP.md` 的覆盖列变化，工具重生（130 模块 / 73 守卫） | 红：新判据落地后 guard-map 报「逐字一致但内容不同」（**软档**，链 EXIT 仍 0）；绿：重生后 `verify-guard-map` zero-diff |
| CHANGELOG 口径同步 | `623fe1f` | 头像读头（256 KB / AVIF 族 ≤4 MiB）、AVIF 尺寸来源、#164 墨臂与 F10 新口径、新增 #204「额外补雾化」条目 | `node test/run-guards.mjs docs` = `✓ docs 全部 6 步执行完毕`；rev-b 自行复算全部对比度数字（非照抄） |

**独立复核（rev-d，三轮，主树零写入）**

- 一轮：逐条复现 4 条 P1 的「红」，确认均非伪修；顺带发现**残留洞 A**（256 KB 读头 ⇒ `mdat` 在前的 AVIF 绕过边长闸）。
- 二轮：用自己的夹具对抗性验收 `ff3df77`（修复前放行 → 修复后拒绝）、突变验证判据有牙（把 `AVATAR_AVIF_HEAD_BYTES` 改回 256 KB 并重建 ⇒ `1 failed, 678 passed`）。
- 三轮（`176a90d`）：确认残留洞 A 真关闭、无伪修、`fail-open` 面未扩大（族门只看 `ftyp` 品牌不看尺寸；新路径失败仍返回 `null`；`avatarImportLimit` / `overBytes` 一字未改）；总判 **可定稿**。

**本轮修正的一处评审误判**：原报告 §5.2 写「#203 的修法是『改墨色择优**或**升级网格』」——独立复核实测表明**两者都要**：只改网格不改墨臂（`#0f1115`）时连续色域最差仍是 4.347:1，只改墨臂不改网格则判据缺口仍在（F10a 九档采样抓不到）。另注：4.831 是「同一 accent 换纯黑」的单点值，不是全局保证——全局保证来自 `40a97fb` 的连续网格实测（最差 4.5826:1）。

### 4.5 回灌材料

- `patches/`（8 个 `git apply` 补丁，均已实测适用性）：
  - 直接适用于对应 PR 分支：`202-avif-size-probe.patch`（@`refs/pr/202`）、`204-selector-limit.patch`（@`refs/pr/204`）、`203-contrast-ink-grid.pr203-subset.patch`（@`refs/pr/203` 新 head `4c864b7`）、`199-red-green-guard.pr199-subset.patch`、`199-p2-404-reason.pr199-subset.patch`（@`refs/pr/199`）——后两个另附逐文件子补丁（`src/effects.js`、`test/verify-readability.mjs`、`src/preset-store.js`、`test/verify-presets.mjs` 单独可用）。
  - 仅适用于本地整合线（含本地专属文件）：`203-contrast-ink-grid.patch`、`199-red-green-guard.patch`、`199-p2-404-reason.patch`。
- `GITHUB-HANDOFF.md`：7 条 PR 评论草稿（逐条给「值得合 / 有条件 / 现状不该合 + 具体修法」）、2 条 issue 草稿（ENAMETOOLONG；本地模块拆分带来的移植负担）、回灌顺序与命令备忘。文中的 4 处自相矛盾（提交数 102 vs 92、#196「祖先」表述、#202 文件数 28 vs 26、`verify-scene` 132 vs 130）已逐一修正。

### 4.6 CI 首次跑绿：三个跨平台问题 + 一个 flaky

这条工程线此前**从未在 GitHub CI 上跑过**（#196 的 4/4 绿灯停在 `227b848`，即巨石拆分重构之前），所以 PR #207 第一次运行（run `38421719079`）是两个平台同时判红、卡点还各不相同 —— 它们都不是社区 PR 的问题，而是本地重构线自己的守卫平台假设：

| 平台 | 卡点 | 根因 | 修法 |
|---|---|---|---|
| windows-latest | `[4/57]` `test/verify-transcode-state.mjs` 负对照 | 负对照按行锚定源码文本（`…onEntry(entry);\n`），Windows 检出是 CRLF ⇒ 锚点不命中 ⇒ 变异体没删成 ⇒ 反而判成「这条判据没有牙」。仓库当时没有 `.gitattributes` | 新增 `.gitattributes`（`* text=auto eol=lf` + 二进制钉住），把「检出即 LF」变成仓库契约；该处正则同时容 `\r?\n` 兜底 |
| ubuntu-latest | `[51/57]` `test/verify-steam-discovery.mjs` D3 | 夹具用 `D:\SteamLibrary`，而 `lib/steam-discovery.js:163-168` 的 `wslPath()` 在 linux 上把 `D:\x` 与未反转义的 `D:\\x` 都翻译成同一个 `/mnt/d/x` ⇒ 正向期望值错，负对照也同时失去牙 | D3 改用不带盘符的 UNC 夹具（`wslPath` 恒等），反转义重新是唯一变量 |
| windows-latest | `[56/57]` `test/verify-asset-paths.mjs` E1 | 期望值硬写 posix 字面量（`'/cfg/we'`、`endsWith('/')`），Windows 的 `normalize()` 产出 `\cfg\we` / 尾字符 `\` | 期望值改由 `normalize()` 求得，尾分隔符按 `/[\\/]$/` 判定（语义与分隔符无关） |

同一时间 `origin/main` 前进一笔（`610ccd8`：中文 README 整篇重写 + `assets/about/sponsor-fish.png`），冲突只有 `README.md` 一处 —— 本地线在旧表里加的 #204 行在新版结构里没有对应位置。处置：`README.md` 取 main 新版，把 #204 的说明按新版结构写成「液态玻璃、字体与预设」下的一条 bullet（8 条 / 120 字 / 整条丢弃 / 目标需自定位 / 载体在 `::before` / issue #200）；`README.en.md` main 未改动，本地那行自动合并保留。

三处修完后 CI 转绿：**PR 事件 run `38423298899` 的 ubuntu-latest 与 windows-latest 全部 success**，PR #207 状态 `MERGEABLE` / `CLEAN`。本地整链复跑仍 `node test/run-guards.mjs all` 70 步 EXIT=0。

**已知 flaky（已修 —— PR [#211](https://github.com/elysia395/dsh-wallpaper-engine/pull/211) `51f3b32` / merge `8244035`，issue [#210](https://github.com/elysia395/dsh-wallpaper-engine/issues/210) 已关；下面是当时的诊断与建议修法，实际修法与之一致）**：同一 commit 的 **push 事件** run（`38423291533`）在 windows-latest `[2/57]` `test/verify-client.mjs` 的 `A3-F2` 判红一次 —— `窗口内重复 focusout 不立刻补交、每次跳过都记账，实测 {"handbacks":1,"skipped":4}`（期望 `skipped === 3`）；同 sha 的 PR 事件 run 同一步通过 ⇒ 与 runner 负载相关的时序抖动（该判据与 `src/focus-handback.js` 的实现都用真实时钟：手势窗口 1000 ms / `MIN_GAP_MS` 300 ms / 补交 `setTimeout`）。它落在硬档且是 `verify` 链第 2 步，会随机卡住合并门。处置：已开 issue [#210](https://github.com/elysia395/dsh-wallpaper-engine/issues/210)，并由 PR [#211](https://github.com/elysia395/dsh-wallpaper-engine/pull/211) 修掉 —— `test/verify-client.mjs:4618-4645` 的 `buildFh` 台架注入虚拟时钟（`Date`/`setTimeout`/`clearTimeout`），A3-F2 与 A3-F1/F5 的 `sleep(320/1150)` 全换成 `advance(ms)`，负对照扩成「到点必须补交 / 999ms 未到点不许补交 / 拆掉 `retryTimer` 后队列必须为空」并对三份变异源码判红；本地 20× 稳定性循环 20/20 绿，CI run `38427833152`（push）/ `38427844889`（PR）双平台全绿。

---

## 5. 未闭合项与建议

### 5.1 已回灌（分支 / PR / 评论 / issue 均已发出）

整合分支 `integration/pr-batch` @ `9c14480` 已 push，并在 PR [#207](https://github.com/elysia395/dsh-wallpaper-engine/pull/207) 上向 `main` 发起整合（CI 双平台已转绿，见 4.6）；本报告与补丁在 `integration/handoff-docs` 分支；7 个 PR（#196 / #199 / #201 / #202 / #203 / #204 / #206）各收到一条评审评论；[issue #208](https://github.com/elysia395/dsh-wallpaper-engine/issues/208)（GPU 抓帧缓存名逼近 NAME_MAX）与 [issue #209](https://github.com/elysia395/dsh-wallpaper-engine/issues/209)（模块拆分带来的移植负担）已开。**`main` 已合并**：PR [#207](https://github.com/elysia395/dsh-wallpaper-engine/pull/207) 已 merge（commit `9acdf01`，2026-10-13 23:48 +08:00）；合并后 main 上 `run-guards all` 70 步 EXIT=0、`verify-static` 绿、push CI 双平台绿。`#196` 是本地线祖先，随本次合并一并进入 `main`。

### 5.2 遗留问题清单

**本线已修（原 P1，见 4.4）**：#202 AVIF 尺寸探针 + 残留洞 A；#203 对比度红线；#204 选择器截断 + 两处本地硬门；#199 判据缺牙 + 404 文案因果；#199 / #204 的 CHANGELOG 条目；`docs/GUARD-MAP.md` 派生重算。

**待回灌（把 4.4 的修复搬到各 PR 自己的分支上；补丁已就绪）**：即 `patches/` 的五个 PR 子集补丁。注意 #202 的补丁需拆两笔（`cda8149` + `176a90d`），#203 / #199 的补丁依赖 PR 分支上尚不存在的本地文件时改用子补丁。

**P2（可择机，均不影响本线验收）**

- #196：`CONTRIBUTING.md:130`、`docs/DEV-GUIDE.md:32`、`test/verify-package-files.mjs:296` 仍写「CI 故意不执行 npm ci」——与 227b848 起加上的 `npm ci --omit=peer` 漂移；`^3.0.0` 浮动 + `npm ci` 吃 lock ⇒ 上游 3.x 破坏 shim 形态时 CI 不会红，建议钉 `3.0.0` 或加定期 lock 刷新；mediabunny 11 MB 死重、离线安装需 registry（CHANGELOG 无引擎切换条目）。
- #199：墓碑（tombstone）之后 GET 仍有英文漏出（`lib/routes/presets.js:716-722` 只本地化了 `version-obsolete`）。
- #201：CHANGELOG 称「三条判据各配一条负对照」但实测只有 2 条；F2f 记述错位；`[data-vcp-reasoning*]` 在当前宿主查无此物（无害死选择器）；建议给收口规则补 `box-decoration-break: clone !important`（当前依赖宿主 compact 变体自带）。
- #204：目标元素为 `static` 时伪元素按最近已定位祖先定位（README 已声明前提）；用户选择器可命中宿主自身 DOM（`*/body` 与宿主 `::before` 互斥）；gate 只作用于 `body[data-we-glass-page]` 文档。
- #206：面积制忽略宽高比（16:9 主帧在 21:9 画布 cover 裁 ~25%，作者已明确接受）；客户端不再读 `removed:false`；409 分支不释放旧帧字节。
- 代码注释不全：`src/client.js:3308` 仍只写「最多 8 条」，未写「每条 ≤120 字 / 整条丢弃」（本轮已改面板文案与 CHANGELOG，注释留作顺手项）。
- 顺带发现的**既有独立课题**：`parsePngSize` 按固定偏移读 IHDR、不校验块类型，PNG 签名 + 垃圾字节会被当成尺寸（本例恰好被边长闸拒）。建议单独开 issue，与本轮无关。

**始终未核验**：真实宿主 + 真浏览器的人工端到端过一遍（本轮的证据全部是守卫链 + 活宿主 curl + 解析器级夹具）。

### 5.3 下一步（本轮已执行的部分）

1. **本地保留** ✅：`integration/pr-batch` @ `9c14480` 即「6 个 PR + 4 条 P1 修复在本地线跑通」的成品；复验只需在短路径 checkout 里跑 `node test/run-guards.mjs all`（本轮已跑三次，均 EXIT=0）。
2. **回灌 GitHub** ✅（2026-10-13 执行）：分支已 push、[PR #207](https://github.com/elysia395/dsh-wallpaper-engine/pull/207) 已开并**合并进 `main`**（merge commit `9acdf01`）；7 个 PR 各收到评审评论；`patches/` 里与各 PR 对应的子补丁仍待**作者自己**落到各自分支（本仓侧的回灌已闭环）。
3. **要长期跑这条线** ⏳：把 5.2 的 P2 与两条 issue（`GITHUB-HANDOFF.md` 的 issue-A / issue-B）开成仓库 issue；`ENAMETOOLONG` 建议优先级最高（它会制造与代码无关的假红）。
4. **收口** ✅（2026-10-14 00:1x）：修掉 #210 的 flaky（PR [#211](https://github.com/elysia395/dsh-wallpaper-engine/pull/211)，merge `8244035`，只改 `test/verify-client.mjs`；本地 20× 稳定绿 + 双平台 CI 绿 + `origin/main` 复跑 `all` 70 步 EXIT=0），关闭该 issue；把四个不会被 GitHub 自动标 merged 的 PR（#202 / #203 / #204 / #206）各发一条「已并入 main」说明评论后关闭 ⇒ **仓库 0 个 open PR**。`patches/` 与 `GITHUB-HANDOFF.md` 继续留档，作者若想同步自己分支可直接取用。
