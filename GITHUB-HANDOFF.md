# GITHUB-HANDOFF — 社区 PR 批量整合回灌文稿

- 仓库：`elysia395/dsh-wallpaper-engine`
- **已回灌（2026-10-13）**：代码分支 `integration/pr-batch`（PR [#207](https://github.com/elysia395/dsh-wallpaper-engine/pull/207)）与本文件所在的 `integration/handoff-docs` 分支均已 push；7 个 PR 各发一条评论；issue [#208](https://github.com/elysia395/dsh-wallpaper-engine/issues/208) / [#209](https://github.com/elysia395/dsh-wallpaper-engine/issues/209) 已开。**未合并 `main`**（留给评审）。
- 所有实测数字来自 `PR-BATCH-INTEGRATION-REPORT.md`（同仓库，169 行），并已在本地整合线复核。
- 复核分支：`integration/pr-batch` @ `623fe1f`（基于 `877952e` 的**拆分后模块布局**，即 `src/styles.js` 已只剩 31 行组装点的那条线）。

---

## 0. 使用说明

本文件里的文稿已于 2026-10-13 回灌：分支与 PR 已 push / 开，7 条评论已发，2 个 issue 已开；`main` 未合并。

**给 PR 作者（@jassdingk / @tangdogdaihuman / @Fish-under-sea / @yuxilao / @elysia395 / @oneincase）**
直接取 `## 1. 每个 PR 一条评论稿` 里属于你的那一节，全文粘到对应 PR 的评论框。里面写清了：本 PR 在本地整合线跑过的判据、值不值得合、以及（如果有）必须修掉的 P1。P1 的定位与实测数字都给了，按它改即可。

补充：本地整合线**已经把 4 条 P1 全部修完并跑通全链验收**（`#202` AVIF 尺寸探针 + 残留洞 A、`#203` 对比度红线、`#204` 选择器截断、`#199` 判据缺牙 + 404 文案因果），每条都带「先红后绿」证据与独立复核。作者不必从零推导，可直接取 `patches/` 里对应子补丁；改法说明、实测数字与三轮复核记录见 `PR-BATCH-INTEGRATION-REPORT.md` 的 §4.4 / §4.5。

**给仓库维护者（合 main 的人）**
先读 `## 2. Issue 草稿` 的两条（长路径假红 + 模块拆分的移植负担），它们决定"直接在 GitHub 上合"这一步顺不顺；再读 `## 3. 回灌顺序与命令备忘` 拿顺序与命令。第 3 节末尾列了本轮**未执行**的远端步骤。

**给 fork 作者**
你的 fork PR（#201 / #203 / #204）在 GitHub 上**零 workflow run**（`statusCheckRollup` 为空、`mergeStateStatus=UNSTABLE`），所以"CI 绿"这一档在 GitHub 侧没有证据。本地整合线替你补齐了突变/负对照证据，#203 与 #204 另各有一条 P1；不修 P1 时，建议先别请求合 main。

**CI 状态（2026-10-13 晚更新，给所有评审者）**
整合 PR [#207](https://github.com/elysia395/dsh-wallpaper-engine/pull/207) 第一次跑 CI 时两个平台同时判红，卡点不同 —— 都是**本地重构线自己的守卫平台假设**，不是社区 PR 的问题：
1. windows-latest `[4/57]` `test/verify-transcode-state.mjs`：负对照按行锚定源码文本，Windows 检出是 CRLF ⇒ 仓库补 `.gitattributes`（`* text=auto eol=lf`）；
2. ubuntu-latest `[51/57]` `test/verify-steam-discovery.mjs` D3：夹具用 `D:\SteamLibrary`，被 linux 的 `wslPath()` 翻译掩盖 ⇒ 换 UNC 夹具；
3. windows-latest `[56/57]` `test/verify-asset-paths.mjs` E1：期望值硬写 posix 字面量 ⇒ 改由 `normalize()` 求。
三处修完 + 并入 `origin/main`（`610ccd8` README 重写，冲突只 `README.md` 一处，已按新版结构重写 #204 那行）后，**ubuntu-latest 与 windows-latest 全绿**，PR 状态 `MERGEABLE` / `CLEAN`。明细见 `PR-BATCH-INTEGRATION-REPORT.md` §4.6。
另有一处**已知 flaky**：同 commit 的 push 事件 run 在 windows-latest `[2/57]` `test/verify-client.mjs` 的 `A3-F2` 判红过一次（`skipped` 实测 4、期望 3），同 sha 的 PR 事件 run 通过 ⇒ 与 runner 负载相关的时序抖动（判据与 `src/focus-handback.js` 都用真实时钟）。它落在硬档，会随机卡住合并门，建议单独开 issue 把该计量改成注入时钟。

---

## 1. 每个 PR 一条评论稿

### PR #196 refactor(渲染): WebWallGL 引擎改为 npm 运行期依赖 webwallgl@^3.0.0，移除 vendored 副本

**结论：值得合；它是本批里唯一对 main 可以 fast-forward 的一笔（本轮未替你执行）。**

**「合并要求」核对结果**
- GitHub CI：**4/4 SUCCESS**，`MERGEABLE/CLEAN`
- 先红后绿：线内（不适用；它是本地线的祖先，不是移植进来的）
- 产物同步：✅
- `verify` + `docs` + `smoke`：✅
- 派生文档：✅
- CHANGELOG：✅
- 裁定：**达标**，仅 3 处文档漂移（见 P2）

**P1 清单**
- 无 P1

**P2 摘要**
1. 文档漂移 3 处：`CONTRIBUTING.md:130`、`docs/DEV-GUIDE.md:32`、`test/verify-package-files.mjs:296` 仍写「CI 故意不执行 npm ci」——与 `227b848` 起加上的 `npm ci --omit=peer` 不符。
2. `webwallgl@^3.0.0` 浮动 + `npm ci` 吃 lock ⇒ 上游 3.x 破坏 shim 形态时 CI 不会红；建议钉 `3.0.0` 或加定期 lock 刷新。
3. 其它：mediabunny 11 MB 死重；离线安装需 registry；CHANGELOG 无引擎切换条目。另外非 Linux 环境不加载 x11（darwin 实测 snapshot `{"ok":1,"backend":"none"}`，只打一行日志不抛）。

本地修复补丁：**本批未生成**（P2 文档口径，未落代码）。精确落点与建议措辞：`CONTRIBUTING.md:130`、`docs/DEV-GUIDE.md:32` 把「CI 故意不执行 npm ci / 整条链对 registry 与 peer 解析完全免疫」改为「CI 执行 `npm ci --omit=peer --ignore-scripts --no-audit --no-fund`；链对 peer 解析免疫，但不再免疫 registry」；`test/verify-package-files.mjs:296` 的 check 标签 `(CI installs nothing)` 同步更正。

以上由本地整合线（integration/pr-batch，基于 877952e 的拆分后模块布局）复核实测得出。

### PR #199 fix(预设): #188 升级窗口期「作者自用」弹窗必败 —— 探针门控 + 随包层保留读失败原因 + 404 已知错误本地化

**结论：值得合；缺一档「先红后绿」证据，补上再落 main 更稳。**

**「合并要求」核对结果**
- GitHub CI：**4/4 SUCCESS**
- 先红后绿：❌ **无证据**
- 产物同步：✅
- `verify` + `docs` + `smoke`：✅
- 派生文档：✅
- CHANGELOG：❌ 未加
- 裁定：达标，缺一档证据

**P1 清单**
1. 缺「先红后绿」守卫证据：回退本 PR 源码后重建并重生 ROUTE-INDEX，`verify` 仍 55 步全绿 ⇒ 现有守卫抓不到它修的 bug。建议补 `test/verify-presets.mjs`（随包旧 tag ⇒ 422 version-obsolete 而非 404；新文案在词表里）。
2. CHANGELOG 未加（在报告 5.2 的 P1 表里与 #203 / #204 并列）。

机制本身已在活宿主用 curl 复核：`404 {"error":"unknown preset"}`。

**P2 摘要**
1. 404 文案因果挂错：`src/preset-store.js:85` 只看 `404 + error==='unknown preset'`，而 `lib/routes/presets.js:711-713` 给同一 404 带 `reason:'missing'|'tombstone'` ⇒「路由在、预设真没了」也会提示「重启 DSH」（重启无用）。
2. 墓碑后 GET 仍有英文漏出。

本地修复补丁（两笔，均在本地线已落地并验收）：
- `patches/199-red-green-guard.pr199-subset.patch` —— **可直接 `git apply` 到 `refs/pr/199`**（只含 `test/verify-presets.mjs`；CHANGELOG 那两笔因 main 版本行号不同未收入，需作者手加）。
- `patches/199-p2-404-reason.pr199-subset.patch` —— **可直接 `git apply` 到 `refs/pr/199`**（只含 `src/preset-store.js`；两条词表键需手改到 PR 分支的 `src/i18n-copy.js`，本地线在 `src/i18n-copy-glass.js`）。
- 本地线完整版（含 CHANGELOG 与域词表）：`patches/199-red-green-guard.patch`、`patches/199-p2-404-reason.patch`。

以上由本地整合线（integration/pr-batch，基于 877952e 的拆分后模块布局）复核实测得出。

### PR #201 fix(styles): 思考块正文行内代码跨行折断时不再糊成整列霜釉底板（#197）

**结论：值得合；判据质量在样式族里是好的，但派生文档要重生、CHANGELOG 要更正一处记述。**

**「合并要求」核对结果**
- GitHub CI：❌ 0 check-run（fork PR，`statusCheckRollup` 为空、`mergeStateStatus=UNSTABLE`）
- 先红后绿：✅（三处突变各自判红：删语义锚 → F2d3b3 红；降级选择器 → F2d3b2 红；整条规则删 → F2d3b 红）
- 产物同步：✅
- `verify` + `docs` + `smoke`：✅
- 派生文档：⚠️ 需重生
- CHANGELOG：✅
- 裁定：条件达标（"达标"完全靠本地补证，因为 fork PR 在 GitHub 上没有 workflow run）

**P1 清单**
- 无 P1

**P2 摘要**
1. CHANGELOG 称「三条判据各配一条负对照」但实测只有 2 条；F2f 记述错位。
2. `[data-vcp-reasoning*]` 在当前宿主查无此物（无害死选择器）。
3. 两个承重锚点都在安装版宿主里实测存在：`data-variant: "think"`、`thinkBody: "HiSsxa_thinkBody"`，compact 变体自带 `box-decoration-break: clone`。

本地修复补丁：**本批未生成**（P2 文档口径，未落代码）。建议：`docs/CHANGELOG.md` / `docs/en/CHANGELOG.md` 该条把「三条判据各配一条负对照」改为「三条判据 + 两条具名负对照 + 一条正向判据（撤掉语义锚由 F2d3b3 抓）」；同段把「推理面清底+无霜」的判据名从 `F2f` 改回 `F2d2/F2d2b`（`F2f` 实际是代码块内画布透明）。

以上由本地整合线（integration/pr-batch，基于 877952e 的拆分后模块布局）复核实测得出。

### PR #202 feat(头像): 支持 SVG 等格式（#185）+ 导入两道闸与自动压缩；修复 #198 已知问题（取代 #198）

**结论：值得合；功能面最大，且它的跨 PR 覆盖面守卫正是本轮发现 #203 / #204 覆盖缺口的工具——但必须先修掉 AVIF 边长闸缺口。**

**「合并要求」核对结果**
- GitHub CI：**2/2 SUCCESS**
- 先红后绿：✅
- 产物同步：✅
- `verify` + `docs` + `smoke`：✅
- 派生文档：✅
- CHANGELOG：✅
- 裁定：**达标**

**P1 清单**
1. AVIF 尺寸解析缺口：`probeImageSize` 只覆盖 SVG 与 `parseBitmapHeaderSize` 的位图头，AVIF（ISOBMFF `ftyp`→`meta`→`ispe`）读不出尺寸 ⇒ 返回 null ⇒ `avatarImportLimit` 只按 4 MiB 体积判，**边长闸（1024×1024）对 .avif 失效**。建议补 ISOBMFF/`ispe` 解析后再判边长。

**P2 摘要**
1. 顺序硬约束：它带来的「每个可重置 / 可配键都被消费」守卫必须等 #203 / #204 把新令牌与新选择器加进来之后才能合，否则守卫必红。
2. 本地落地口径与 PR 元数据不同：PR 为 26 文件 / +3820−187；本地拆成 `dd25f1c`（28 文件 / +3842−191，移植）+ `30541ce`（28 文件 / +3846−191，合并修补）两笔。
3. 移植过程中曾把 `src/client.js` 状态块（吉祥物导入态 / 上传目录编辑器 / 缓存位置编辑器）整段重复插入，导致 `verify-static` 19 条红（`no-useless-assignment 5 · no-dupe-keys 10 · no-promise-executor-return 1 · no-unused-vars 2 · object-shorthand 1`），已用等价改动修掉（`419b6fe`），**没有** `--write-baseline`。

本地修复补丁：`patches/202-avif-size-probe.patch` —— 已用 `git archive refs/pr/202` 解出 PR 树实测 `git apply --check` **通过**（该文件在 PR 分支上同名同路径）。补丁不含 `lib/client.js`，落地后请 `node scripts/build-client.mjs` 重建产物。

以上由本地整合线（integration/pr-batch，基于 877952e 的拆分后模块布局）复核实测得出。

### PR #203 fix(styles): 原生 select 弹层按有效墨色派生行底与对比墨（#164）

**结论：有条件值得；判据质量本批最高，但对比度红线实际不达标，修掉 P1 再落 main。**

**「合并要求」核对结果**
- GitHub CI：❌ 0 check-run（fork PR，`statusCheckRollup` 为空、`mergeStateStatus=UNSTABLE`）
- 先红后绿：✅（F8 四作用域 + 6 条负对照；F9/F10 对比度网格）
- 产物同步：✅
- `verify` + `docs` + `smoke`：✅（须短路径 checkout，见 issue-A）
- 派生文档：✅
- CHANGELOG：❌ 未加
- 裁定：条件达标（**P1 对比度不达标**）

**P1 清单**
1. 对比度红线不达标：`src/effects.js:157` 的 `weCheckedRowInk(hex)` 注释与 F10 声称「对比度取大 ⇒ ≥4.5」，实测 accent `#de3c42` 配 `#0f1115` 只有 **4.347:1**；256 档灰中有 **5 档 < 4.5**（最差 `#797979` → `#ffffff` = **4.353:1**）；同一 accent 换纯 `#000000` 可得 **4.831:1** ⇒ 不是无法达标，是墨臂挑得不对。F10a 只采样 9 档（最差 4.72）所以全绿——判据缺口。建议改墨色择优，或把 F10 升级为连续 / 自适应网格。
2. CHANGELOG 未加（报告 5.2 的 P1 表并列项）。

**P2 摘要**
1. 本地移植口径：`6b378a0` 为 7 文件 / +469−43（PR 元数据 7 文件 / +524−98）。
2. 过程中一次 `git checkout -- <file>` 曾把主树里刚落的本 PR 改动抹掉（丢失更新事故），已从 worktree 分支取回并重新提交；纪律已改为「每落一批先 `git add` 暂存再跑守卫」。

本地修复补丁：
- `patches/203-contrast-ink-grid.pr203-subset.patch` —— **可直接 `git apply` 到 `refs/pr/203-new`（@`4c864b7`）**（含 `src/effects.js` + `test/verify-readability.mjs`）。
- 另一半（CSS 兜底墨 `#0f1115` → `#000000`，本地线在 `src/styles-settings.js`）在 PR 分支上应改 `src/styles.js` 的同名兜底声明，故未收入 subset，需作者手改。
- 本地线完整版：`patches/203-contrast-ink-grid.patch`。

以上由本地整合线（integration/pr-batch，基于 877952e 的拆分后模块布局）复核实测得出。

### PR #204 feat(glass): 额外补雾化的选择器 —— 给第三方插件面板补毛玻璃载体（#200）

**结论：有条件值得；方向正确，但 `.slice(0, 400)` 会静默产出半截选择器，修掉 P1 再落 main。**

**「合并要求」核对结果**
- GitHub CI：❌ 0 check-run（fork PR，`statusCheckRollup` 为空、`mergeStateStatus=UNSTABLE`）
- 先红后绿：✅（两次突变都能断言式判红 + 消毒恒等突变）
- 产物同步：✅
- `verify` + `docs` + `smoke`：✅
- 派生文档：✅
- CHANGELOG：❌ 未加
- 裁定：条件达标（另有报告 3 节的「两处本地硬门」，见 P2）

**P1 清单**
1. `lib/settings-schema.js:139` 的 `.slice(0, 400)` 会**静默产出半截选择器**：合法上限是 8×120+14 = **974**，实测 8 条各 120 字 → 输出 400 B / 4 条，第 4 条被切断，与 `README.md:193`「最多 8 条」自相矛盾。建议改 974，或超限整体拒绝。
2. CHANGELOG 未加（报告 5.2 的 P1 表并列项）。

**P2 摘要**
1. 本地硬门：`package.json` 改动在本地线不合法（本地 P5 硬断言 `scripts.verify` 的值恰为 `node test/run-guards.mjs <group>`），所以长 `&&` 链插入改走 `test/run-guards.mjs` 的 MANIFEST 注册 + 重冻结 `test/fixtures/verify-chain.json`；它还把 GUARD-MAP 127/71 → 128/72。
2. 目标元素为 `static` 时伪元素按最近已定位祖先定位（README 已声明前提）。
3. 用户选择器可命中宿主自身 DOM（`*/body` 与宿主 `::before` 互斥）；gate 只作用于 `body[data-we-glass-page]` 文档。

本地修复补丁：`patches/204-selector-limit.patch` —— 已用 `git archive refs/pr/204` 解出 PR 树实测 `git apply --check` **通过**（`lib/settings-schema.js` / `test/verify-extra-glass.mjs` / `README.md` / `README.en.md` 在 PR 分支同名同路径）。补丁不含 `lib/client.js`，落地后请重建产物。

以上由本地整合线（integration/pr-batch，基于 877952e 的拆分后模块布局）复核实测得出。

### PR #206 fix(cache): GPU 抓帧缓存改单主帧 + 面积升级制 —— 多窗口不再互踢，resize 不再写盘

**结论：值得合，但**不能直接 merge**：直接 `git merge` 实测 8 个文件冲突，且会静默复活巨石 / 放宽本地棘轮。**

**「合并要求」核对结果**
- GitHub CI：**4/4 SUCCESS**
- 先红后绿：✅
- 产物同步：✅
- `verify` + `docs` + `smoke`：✅
- 派生文档：⚠️ 需重生
- CHANGELOG：✅（但 `docs/en/CHANGELOG.md | 318 +------` 是底座漂移，见 P2-1）
- 裁定：达标，但**不能直接 merge**

**P1 清单**
- 无 P1

**P2 摘要**
1. `docs/en/CHANGELOG.md | 318 +------` 是**底座漂移而非有意删除**：main 1931 行 / PR 1615 / 其父 `0e7c806^` 1896；删掉的正是 main 那笔 release 提交 `0e7c806`（v1.3.1 published）加的标题 + 41 条英文条目；对 main 历史 13 个提交逐一比对，没有任何版本等于 PR 那份文件。处置：**拒删**，以 HEAD 版为底只插那 1 行新条目（落地即 `+2/−0`）。
2. 直接 merge 的三重风险：8 文件冲突；静默把巨石复活；悄悄放宽本地棘轮（会放宽 `test/verify-scene.mjs` 的 P4-16、回流 `dir` 死字段）。
3. 面积制忽略宽高比（16:9 主帧在 21:9 画布 cover 裁 ~25%，作者已明确接受）；客户端不再读 `removed:false`；409 分支不释放旧帧字节。本 PR 在本地为 `f788a33`（21 文件 / +829−382），并新增 `verify-scene-frame-master` 18 条守卫，是 `68 步 → 70 步` 的那 2 步来源。

本地修复补丁：**本批未生成**。P0-1 是「底座漂移」而非可机械套用的 diff：正确处置是**以 main 版 `docs/en/CHANGELOG.md` 为底**，只插入本 PR 那 1 条新条目（本地线落地即 `+2/−0`，见 P2-1）；作者侧只需把 PR 分支 rebase 到最新 main 后重做这一处，无补丁可套。

以上由本地整合线（integration/pr-batch，基于 877952e 的拆分后模块布局）复核实测得出。

---

## 2. Issue 草稿

### issue-A：verify-scene 的 GPU 抓帧缓存文件名逼近 NAME_MAX（疑似与 checkout 路径长度耦合，未稳定复现）

**复现状态（先看这条）**
- 本轮**观测到一次**：某个深目录 checkout 里 `verify-scene` 出现 18 条与代码无关的红，错误为 `ENAMETOOLONG`，文件名 **258 B > 255**。
- 但用**符号链接**构造的 149 字符与 277 字符 checkout **未能复现**（verify-scene 130 全绿、verify-scene-live 670 全绿）——符号链接很可能被 realpath 解析成短路径，所以「长 checkout ⇒ 必红」这个因果**尚未证实**。
- 因此本 issue 的口径是「观测记录 + 待定机制」，不是「必然复现的 bug」。要立项修之前，请先补一个稳定复现（例如把 checkout 放在**真实深目录**而非符号链接下）。

**现象**
`verify-scene` 里以绝对路径为输入构造的 GPU 抓帧缓存名形如 `lf1_<base64(abs path)>.png`，在深目录下超过 `NAME_MAX` 255：

- 实测文件名 **258 B > 255** ⇒ PUT 落盘 500 ⇒ `verify-scene` **18 条与代码无关的红**。
- 错误形态：`ENAMETOOLONG … path=…/lf1_<base64(abs path)>.png`。
- 同一份代码在 `/tmp` 短路径下 **130 passed / 0 failed**。
- 最终验收一律在短路径主树（**56 字符**）上跑；短路径下 `verify-scene` 实测 130 passed，整合线最终为 132 passed（基线 130 + #206 新增）。

**疑似触发条件（待证实）**
缓存名 = `lf1_` + base64url(绝对路径) + mtime + `_gpu.png`；若真实 checkout 路径够长，该名字可能超 `NAME_MAX`。观测到的 258 B 与这一算式相容，但符号链接构造的长路径没有复现，所以**尚未定位到确定触发条件**（也可能与并发/临时目录/文件系统有关）。与代码正确性无关，是环境耦合。

**影响**
- 本地验收假红，容易被误读成回归（本轮就踩过：一度以为静态门禁的红是既有问题）。
- CI 不受影响（CI 的 checkout 路径短）。

**建议修法**
缓存键改用**短哈希**（如 `sha256(abs)` 前若干位），或截断 + 去重；两条都能把文件名压在 `NAME_MAX` 以下而不损失唯一性。

**复现尝试记录**
```bash
# 观测到红的那次：在深目录 checkout 里跑（真实深目录；符号链接构造的 149/277 字符路径复现失败）
node test/verify-scene.mjs
# 对照绿：同一份代码在 /tmp 短路径 checkout 里
cd /tmp/<短路径 worktree> && node test/verify-scene.mjs
# 整合线整链（一律在短路径 checkout 里跑，规避不确定性）
node test/run-guards.mjs all
```

### issue-B：本地整合线相对 main 的模块拆分带来的移植负担

**背景**
`origin/main` 是巨石布局：

- `lib/client.js` 4,360,451 B
- `src/styles.js` 266,874 B
- `src/i18n-copy.js` 103,961 B
- `lib/index.js` 156,512 B

本地整合线已按接缝拆成域模块：`src/styles.js` 只剩 **31 行**组装点（8 个视觉域模板）、`src/i18n-copy.js` 76 行（7 个域词表）、`lib/client.js` 只留 5,256 行正文（6 个域模块 + 立绘载荷外移），宿主 `lib/index.js` 3,280 → 1,447 行（11 个套件族）。于是**6 个基于巨石的社区 PR 全部无法直接 `git merge`**：

- `#206` 实测 **8 个文件冲突**（其体积改动最大的 `#202` 同理：本地为 28 文件口径，PR 为 26 文件口径）；
- 直接合并还会**静默把巨石复活**，或**悄悄放宽本地棘轮**（`#206` 会放宽 `test/verify-scene.mjs` 的 P4-16、回流 `dir` 死字段）；
- 因此本地只能**逐个移植**（`09deacc` `9f4ea82` `6b378a0` `dc7bb3c` `dd25f1c` `30541ce` `f788a33`），合计 `877952e..419b6fe` = **65 files changed, +6020/−707**。

**建议**
1. 在 `CONTRIBUTING.md` 里加一句：「样式 / 词表改动请落到对应域模块」，让新 PR 按新布局写；
2. 或者先把本地这条拆分线合进 `main`，之后社区 PR 只需按域模块 rebase，不必再由维护者逐个移植。

---

## 3. 回灌顺序与命令备忘

### 顺序（摘自报告第 1 节）

| 序 | PR | 提交（本地） | 为什么排这里 |
|---|---|---|---|
| 1 | #199 | `09deacc` | 面最小：1 个真冲突（`docs/ROUTE-INDEX.md`，工具重生）+ 1 个派生文档，先落地把整合成本压到最低 |
| 2 | #201 | `9f4ea82` | 纯样式 + 守卫 + CHANGELOG；把「样式域模块移植」在该族最早做掉 |
| 3 | #203 | `6b378a0` | 样式 + effects，紧跟 #201：`docs/TOKEN-CONTRACT.md` 连着重生两次，而不是与别的族交叉重生 |
| 4 | #204 | `dc7bb3c` | 新增模块并改守卫计数（GUARD-MAP 127/71 → 128/72）与注册面，放在样式族之后一次性重算 |
| 5 | #202 | `dd25f1c` → `30541ce` | 面最大。**硬约束**：跨 PR 覆盖面守卫要求「每个可重置 / 可配键都被消费」，必须等 #203 / #204 之后才合，否则守卫必红 |
| 6 | #206 | `f788a33` | 最新开、最「热」，要改注册与重冻结链快照，放最后避免在别人底下反复重冻结 |

硬约束只有两条：**#202 必须在 #203 / #204 之后**（覆盖面守卫）；**#206 最后**（链快照与守卫计数的重冻结成本）。其余是成本优化。若改在 GitHub 上直接合：**#196 独立**（对 main 是 fast-forward）→ #199 → #201 → #203 → #204 → #202 → #206；#203 与 #204 可互换，#202 与 #206 仍应压在最后。

### 命令序列（本地，可复制）

```bash
# 0) 一律在短路径 checkout 里操作（见 issue-A；本地验收用的主树路径为 56 字符）
cd /tmp/<短路径 worktree>

# 1) 每个 PR 补掉它自己的 P1，再逐笔落（顺序见上表）
git checkout integration/pr-batch
git add <改动的文件>                       # 纪律：每落一批先暂存，再跑守卫
git commit -m "fix(<scope>): 补 PR #<N> 的 P1 —— <一句话>"

# 2) 每落一笔后的定向验证
node test/verify-static.mjs
npx eslint src test lib

# 3) 整链验收（报告 4.2 的口径：70 步，硬档 63 / 软档 7）
node test/run-guards.mjs all
# 期望：✓ all 全部 70 步执行完毕   EXIT=0
#       ALL STATIC GATE CHECKS PASSED

# 4) 产物同步（链内已有 [1/70] npm run build + [2/70] verify-client-sync）
npm run build && git diff --stat lib/client.js

# 5) 回灌时的远端动作
git push origin integration/pr-batch        # 代码线
git push origin integration/handoff-docs    # 报告 + 补丁（本文件所在分支）
gh pr create --base main --head integration/pr-batch
gh pr comment <N> --body-file /tmp/gh/pr<N>.md
gh issue create --title ... --body-file /tmp/gh/issue-A.md
```

### 远端步骤执行状态（2026-10-13 已回灌）

| 步骤 | 状态 | 结果 |
|---|---|---|
| `git push` 代码分支 | **已执行** | `integration/pr-batch` @ `623fe1f`（相对 `main` 111 笔；其中 `877952e..623fe1f` 为本次 19 笔） |
| `git push` 报告与补丁 | **已执行** | `integration/handoff-docs` @ `7291a70`（含本文件、报告、`patches/` 8 个补丁） |
| 开整合 PR | **已执行** | [#207](https://github.com/elysia395/dsh-wallpaper-engine/pull/207)（base `main`） |
| 7 个 PR 各发一条评论 | **已执行** | [#196](https://github.com/elysia395/dsh-wallpaper-engine/pull/196#issuecomment-6149007848) / [#199](https://github.com/elysia395/dsh-wallpaper-engine/pull/199#issuecomment-6149008645) / [#201](https://github.com/elysia395/dsh-wallpaper-engine/pull/201#issuecomment-6149009651) / [#202](https://github.com/elysia395/dsh-wallpaper-engine/pull/202#issuecomment-6149010490) / [#203](https://github.com/elysia395/dsh-wallpaper-engine/pull/203#issuecomment-6149011306) / [#204](https://github.com/elysia395/dsh-wallpaper-engine/pull/204#issuecomment-6149012030) / [#206](https://github.com/elysia395/dsh-wallpaper-engine/pull/206#issuecomment-6149012704) |
| 开 issue | **已执行** | [issue #208](https://github.com/elysia395/dsh-wallpaper-engine/issues/208)（NAME_MAX）/ [issue #209](https://github.com/elysia395/dsh-wallpaper-engine/issues/209)（拆分移植负担） |
| 合并进 `main` | **未执行（有意）** | 留给评审：`origin/main` 无分支保护（`branches/main/protection` → 404），且**没有任何 PR 有 review 记录**（`reviewDecision` 全空），所以 GitHub 侧「可合」只由 CI 与 mergeable 决定；`#196` 仍可单独 fast-forward |

### 判定口径备注（维护者用）

- `#201` / `#203` / `#204` 是 fork PR，GitHub 零 workflow run，达标靠本地补证。
- `#199` 的缺口是「先红后绿」证据，不是功能问题。
- `#202` / `#206` 的 `docs/en/CHANGELOG.md` 存在底座漂移，落地时以 HEAD 版为底插入条目，不要接受删除。
