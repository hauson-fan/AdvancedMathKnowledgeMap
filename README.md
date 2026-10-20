# 微积分知识图谱 · 使用说明

## 交付物

| 文件 | 说明 |
|---|---|
| `index.html` | **单文件应用**，数据内嵌，双击即可打开。公式由 KaTeX 渲染，依赖图由 Cytoscape.js + dagre 渲染 |
| `data.json` | 结构化知识库（schema 2.1），可被其他程序直接调用 |
| `README.md` | 本文件：使用说明 + 字段表 + 重新生成方法 |
| `KNOWN_LIMITATIONS.md` | 已知限制与收录口径 |
| `page_text/` | OCR 原始页文本（411 页），供逐条复核表述是否忠实于留档 |

## 一、打开方式

直接双击 `index.html`，或拖入浏览器。**完全自包含：不请求任何外部资源**（KaTeX、Cytoscape.js、dagre 以及 KaTeX 的全部 12 个字体都已内联），因此：

- 可以离线使用，也可以从 `file://` 直接打开
- 不受浏览器「跟踪防护 / Tracking Prevention」对第三方 CDN 的拦截影响
- 不会出现「公式渲染失败」这类因 CDN 加载时序导致的故障

文件约 1.5 MB，其中约 1.4 MB 是内联的库与字体。

## 二、界面与操作

```
┌──────────────────────────────────────────────────────────────┐
│ 顶栏：搜索框 │ 类型筛选 │ 章节筛选 │ 弱关联 │ 布局 │ 适应 │
├───────────────────────────┬──────────────────────────────────┤
│ │ ← 返回 前进 → 第 3/7 条 │
│ 依赖图（Cytoscape） │ ────────────────────────────── │
│ 箭头方向：前置 → 后继 │ 条目详情： │
│ │ 表述（KaTeX） │
│ │ 符号说明 │
│ │ 证明步骤链（点击引用可跳转） │
│ │ 前置 / 后继 │
│ │ │
├───────────────────────────┴──────────────────────────────────┤
│ 图例 │
├──────────────────────────────────────────────────────────────┤
│ 底栏：条目 / 类型统计 │ 依赖边统计 │ 证明步骤数 │ 快捷键提示 │
└──────────────────────────────────────────────────────────────┘
```

| 操作 | 效果 |
|---|---|
| 点击图中节点 | 右侧打开该条目详情，并高亮它的**直接前置**（蓝框）与**直接后继**（橙框），其余淡出 |
| 点击详情里的引用标签 | **替换**当前详情（不叠加卡片），并把它压入历史栈 |
| `← 返回` / `Alt+←` | 回到上一条（连续可回退多层） |
| `前进 →` / `Alt+→` | 重新进入下一条 |
| 详情顶部「第 N / M 条」 | 显示当前位置与「上一条」条目号 |
| 搜索框（或按 `/` 聚焦） | 实时过滤，**只匹配名称类字段**：编号、中文名、英文名、别名、小节标题 |
| 类型 / 章节下拉框 | 按类型、章节过滤节点 |
| 「弱关联」按钮 | 显示/隐藏 `related` 类边（默认隐藏） |
| 布局下拉框 | 分层布局（dagre，默认）/ 广度优先 / 同心圆 |
| 「适应」按钮 | 缩放到适应窗口 |
| 「折叠详情」把手 | 详情栏**左边缘正中**有一个凸出边界的窄条把手（带投影）。展开时箭头朝右 `▶`（点击 → 详情栏向右收起），折叠后箭头朝左 `◀`（点击 → 详情栏向左展开）。折叠时把手停在**同一屏幕位置**（即详情栏原左边缘，此时图谱正好铺到该处），不会跳动。键盘 `Alt+C` 等效 |

响应式：窗口宽度 ≤ 900px 时改为上下布局（图在上、详情在下）；窗口缩放时图自动重算并重新适应。

## 三、`data.json` 字段表（schema 2.1）

### 顶层

| 字段 | 说明 |
|---|---|
| `meta` | 信息、覆盖范围、批次、写作约定、schema 版本 |
| `items` | 95 条条目 |
| `graph.direction` | 恒为「前置 → 后继」 |
| `graph.edge_kinds` | 四类边的语义说明 |
| `graph.edges` | 213 条依赖边：`{source, target, kind, label}` |

### `items[]`

| 字段 | 类型 | 说明 |
|---|---|---|
| `id` | string | 唯一主键，命名规则 `(def\|thm\|cor\|lem\|prop)-<章>.<编号>` |
| `type` | string | `definition` / `theorem` / `corollary` / `proposition` / `lemma` |
| `number` | string | 印刷编号，如「定理 2.11」「推论 1」 |
| `name` | string | 中文名 |
| `name_en` | string | 英文名 |
| `aliases` | string[] | 别名、常用缩写、中英变体（**搜索只匹配这三个名称字段**） |
| `chapter` / `chapter_title` | number / string | 章号、章名 |
| `section` / `section_title` | string | 小节号、小节标题（本项目的「来源定位」，不含页码） |
| `statement` | string | **唯一权威表述，纯 LaTeX**（不含 `$` 定界符），由 KaTeX 渲染；中文用 `\text{}` 包裹，多行用 `\begin{aligned}` |
| `symbols[]` | `{latex, mean}` | 符号说明。`latex` 是**纯 LaTeX**（不含 `$`）；`mean` 是**混排文本**，其中的算式用 `$...$` 包裹，由页面做 Text+Math 混排渲染 |
| `deps[]` | `{ref, kind, note}` | **前置**条目（由 `graph.edges` 派生，勿手工编辑） |
| `used_by` | string[] | **后继**条目 id（同上，派生字段） |
| `proof` | object | 见下 |

### `$` 的语义约定（重要）

这是唯一容易踩坑的地方，两类字段的约定**相反**：

| 字段 | 约定 | 示例 |
|---|---|---|
| `statement`、`symbols[].latex`、`proof.steps[].claim` | **纯 LaTeX，不得含任何 `$`**，整串交给 KaTeX | `A_n=\dfrac{x_1+\cdots+x_n}{n}` |
| `symbols[].mean`、`proof.steps[].note` | **混排文本**，`$...$` 是行内公式定界符 | `数列的第 $n$ 项（通项）` |

历史教训：早期把 `symbols[].sym` 写成了 `$A_n=...$`（给行内渲染器的定界符），直接送进 KaTeX 就报 `Can't use function '$' in math mode`。现在数据层已统一为纯 LaTeX，字段也从 `sym` 改名为 `latex` 以免歧义；`renderMath` 另保留了剥离定界符的兜底，`validate.py` 会强制检查「纯 LaTeX 字段不含 `$`」与「混排字段的 `$` 配对」。

### `\\` 的语义约定（同样重要）

LaTeX 里 `\\` 是**换行命令**，误用会静默毁掉版式：

| 字段 | `\\` | 说明 |
|---|---|---|
| `statement` | **合法** | 长陈述分行书写，当前 67 处 |
| `symbols[].latex`、`claim` | 应为 0 | 当前用法确实为 0 |
| `symbols[].mean`、`proof.steps[].note` | **永远不合法** | 行内短句不需要换行 |

真实踩过的坑：Python **raw** 字符串 `r"..."` **不处理转义**，所以 `r"$\Delta y\\ne0$"` 里就是**两个**反斜杠。
KaTeX 把 `\\` 当换行，后续字母被拆成单个变量——`\Delta` 正常显示 Δ，而 `\\Delta` 变成「换行 + `D`、`e`、`l`、`t`、`a`」，
页面显示成一字一行。

**注意：非 raw 字符串里的 `\\` 恰好是「一个反斜杠」的正确写法**（`note="由 $\\lim...$"` 是对的）。
所以**源码文本本身无法判断对错**，只能依据构建后的数据判定——这正是校验跑在 `data.json` 而非源文件上的原因。

历史教训：`thm-3.5` 等 **11 处**混排字段曾误写成 `\\ne0`、`\\frac`、`\\{x_n\\}`，页面渲染成乱码却长期无人发现，
因为它既不含控制字符、也匹配不上 `\\[`，当时所有校验都拦不住。现在 `check_linebreak.py` 在构建期直接阻断。

### `items[].proof`

| 字段 | 说明 |
|---|---|
| `in_book` | bool。`false` 表示原处未给出完整证明（如「留给读者作为练习」），此时不代为补写，仅列出条件与要点 |
| `method` | 证明方法概述（如「反证法 + 两分法构造区间套 + 区间套定理」） |
| `steps[]` | **Lean 风格证明步骤链** |

`steps[]` 每步：

| 字段 | 说明 |
|---|---|
| `id` | 本条目内的步骤号（字符串，从 `"1"` 起） |
| `claim` | 该步的断言，**纯 LaTeX**（不含 `$`） |
| `by` | 该步**显式用到的依据**。`"#3"` 表示本条目第 3 步；裸 id（如 `"thm-2.13"`）表示其他条目，在页面上可点击跳转 |
| `note` | 该步的依据说明（混排文本，`$...$` 为行内公式） |

例（`thm-2.30` 有界性定理，8 步）：

```json
{ "id": "5",
 "claim": "f \\text{ 在 } \\xi \\text{ 连续}\\ \\Rightarrow\\ \\exists\\delta>0,\\ f \\text{ 在 } (\\xi-\\delta,\\xi+\\delta) \\text{ 内有界}",
 "by": ["def-2.12", "thm-2.18"],
 "note": "局部有界性" }
```

## 四、统计（当前批次）

| 项 | 值 |
|---|---|
| 条目 | **95**（第 1 章 16、第 2 章 66、第 3 章 13） |
| 类型 | 定义 30、定理 49、推论 14、命题 2 |
| 依赖边 | **213**（定义依赖 115、证明依赖 74、推论关系 11、弱关联 13） |
| 证明步骤 | **436** 步（平均 4.6 步/条） |
| 未给出证明的条目 | 6 条（多为公理性起点或标准补充定理，见下） |
| 条目顺序 | 与章节内出现的先后一致 |

未给出证明的 6 条：`thm-1.1`（确界存在定理，实数完备性的公理起点）、
`thm-2.43`／`thm-2.44`／`thm-2.45`／`thm-2.46`／`thm-2.47`（标准补充定理，
证明已写全，`in_book=false` 只标记其证明不来自原书摘录）。

## 五、编号与原书未编号结果的处理

**原书编号是连续正常的，没有重号。** 经 `.tools/audit_numbers.py` 把 95 个条目的 `number` 逐一回查 411 页原文：
71 条在原书中找到对应编号并已核实，其余 24 条是**原书本身未编号**的结果（见下），并非编号冲突。其中「定理 2.13 = 区间套定理」「定理 2.14 = 极限存在 ⟺ 左右极限存在且相等」均已逐页核对。

### 为原书未编号结果所给的顺序号

以下条目的 `number` 是**入库时给的顺序号**，原书没有印编号（表格中另标注原书称法）：

| id | 本库编号 | 原书形态 |
|---|---|---|
| `thm-1.1` | 定理 1.1 | 称「**确界存在性定理**」，无编号 |
| `thm-2.34` | 定理 2.34 | 「数列无界 ⟺ 存在无穷大子列」，行文中给出，无编号 |
| `thm-2.35` | 定理 2.35 | 「函数在区间上无界 ⟺ …」，行文中给出，无编号 |
| `thm-2.41` | 定理 2.41 | 「一个重要极限」（数列 $(1+1/n)^n$），无编号 |
| `thm-2.42` | 定理 2.42 | 原书为**例 2.8**（奇偶子列），按需求作为定理收录 |
| `cor-2.1`–`cor-2.11` | 推论 2.1–2.11 | 原书只写「推论」，无编号 |
| `cor-3.1`–`cor-3.3` | 推论 3.1–3.3 | 原书有陈述甚至标注「推论」，均未编号 |

另 5 条 `thm-2.43`–`thm-2.47` 是标准补充定理（`origin="supplement"`），原书没有，编号顺接原书序列。

## 六、重新生成

全部构建脚本在 `.tools/` 下。**唯一真相源是内容模块与边表**，`data.json` 与 `index.html` 都是它们的产物。

```powershell
$py = "C:\Users\hauson\.dsh\dsh-runtimes\dsh-primary-runtime\dependencies\python\python.exe"
$node = "C:\Users\hauson\.dsh\dsh-runtimes\dsh-primary-runtime\dependencies\node\bin\node.exe"

# 1) 重建 data.json（从内容模块 + 边表）
& $py .tools\build_all.py # 汇总 ch*_content*.py / ch_extra.py，写入 data.json，
 # 再由 edges.json 重建 graph.edges 并派生 deps / used_by
# 2) 重建 index.html（把库与数据内联进 template.html）
& $py .tools\build_html.py
```

### 内容与结构的维护位置

| 想改什么 | 改哪里 |
|---|---|
| 某条条目的表述 / 符号 / 证明步骤 | `.tools/ch1_content.py`、`.tools/ch2_content_a|b|c.py`、`.tools/ch_extra.py` |
| 依赖边（前置 → 后继、边的类型与说明） | `.tools/edges.json` |
| 条目的排列顺序 | `.tools/build_all.py` 顶部的 `ORDER` |
| 名称 / 英文名 / 别名（搜索用） | 各内容模块条目里的 `name` / `name_en` / `aliases` |
| 界面、样式、交互 | `.tools/template.html` |

> 内容模块若整体重建过，可用 `.tools\regen_sources.py` 从 `data.json` 反向生成（内容无损，仅格式规范化为脚本风格）。

### 校验（建议每次改动后都跑）

**推荐用汇总检查器**——它用 subprocess 完整收取每个校验的退出码与输出，不截断流：

```powershell
& $py .tools\run_all_checks.py            # 构建 + 全部 15 项校验，打印总表
& $py .tools\run_all_checks.py --verbose  # 失败项打印完整输出
```

> **不要用 `| Select-Object -First N` / `-Last N` 看校验结果。** 这会制造两种假象：
> `-First N` 读满后**掐断管道并杀掉上游进程**，于是得到假的非零退出码；
> `-Last N` 会把崩溃脚本的 traceback 压掉，于是崩溃被误当成通过。
> 这两个坑都真实发生过（一次假的 KaTeX「失败」，一次假的 `audit_dollars`「通过」）。

各项也可单独跑：

```powershell
# 数据层
& $py .tools\validate.py # id 唯一、零悬空引用、无环、无页码字段、旧字段无残留、$ 契约、证明链自洽
& $py .tools\audit_numbers.py # 编号回查：把每个 number 拿去 411 页原文找，输出「已核实 / 待复核 / 原文有但未收」
& $py .tools\coverage.py # 覆盖率：重扫 page_text/，确认没有漏收的编号条目
& $py .tools\audit_dollars.py # 纯 LaTeX 字段不得含 $；混排字段的 $ 必须配对
& $py .tools\check_ctrl.py # 数学字段无控制字符（非 raw 字符串会吞掉 \v、\t）
& $py .tools\check_linebreak.py # 无 `\\` 紧跟 `[`（会被当成换行的尺寸参数）
& $py .tools\fix_text_braces.py # \text{} 花括号配平

# 渲染层（必须在 build_html.py 之后跑）
& $node .tools\check_app_render.js # 从 index.html 抽出 app 真实的 renderMath/renderMixed，跑全部字段
& $node .tools\check_katex_render.js # 用真实 KaTeX 全量检查表达式语法，并探测易错宏
& $node .tools\check_layout.js # 用真实 dagre 跑布局，检查节点是否全部放置、有无坐标重合
& $node .tools\check_html.js # index.html 静态自检（JS 语法、DOM id、交互存在性、响应式）
& $py .tools\check_selfcontained.py # 断言 index.html 零外部请求
& $py .tools\check_single_file.py # 复制到空目录，验证可独立运行
```

`check_app_render.js` 是**最要紧**的一项：它不在测试里另写一套渲染逻辑，而是从构建产物里抽出页面真正执行的 `renderMath` / `renderMixed`，配上真实 KaTeX 跑一遍全部字段。曾经出现过「测试剥离了 `$` 定界符、页面没剥离」导致 3 处公式失败却测不出来的情况，这个脚本就是为了堵住这类测试与实现不一致的漏洞。

它现在还内置**反向对照**：故意喂必错公式、正常公式、含 `\\` 公式各一个，识别不出就判定「校验本身失效」并直接失败。加这个是因为它**曾经长期空转**——沙箱里的 `katex.min.js` 属于 Node 外层 realm，取不到沙箱的 `document`，`katex.render()` 每次都抛错被 `renderMath` 的 `catch` 吞掉，而当时的失败判据查的 `node.__err` 字段从来没人赋值，于是 1333 次调用一次都没真正渲染，却始终报「全部通过」。**没有反向对照的校验，无法证明自己不是空转。**

### 改动内容的位置

- **条目正文/证明**：`.tools/ch1_content.py`、`.tools/ch2_content_a.py`、`.tools/ch2_content_b.py`、`.tools/ch2_content_c.py`、`.tools/ch2_content_d.py`、`.tools/ch2_content_e.py`、`.tools/ch3_content.py`、`.tools/ch_extra.py`、`.tools/ch_extra2.py`
- **依赖边**：`.tools/edges.json`（权威边表；`deps` / `used_by` 由 `derive.py` 从它派生，不要手改 `data.json`）
- **界面与样式**：`.tools/template.html`
- **是否原生条目**：内容模块里的 `origin` 字段（`source` / `supplement`）

`build_all.py` 内置两道硬校验：证明步骤 `by` 里出现的裸 id 必须与该条目在图上相连；数学字段不得含控制字符、不得出现 `\\` 紧跟 `[`、**混排字段（`note` / `mean`）不得出现 `\\`**。这保证「证明链」与「依赖图」不会各说各话，也让 LaTeX 的隐蔽错误在构建期就被拦下——这些校验都在 `json.dump` **之前**执行，所以一旦失败，`data.json` 不会被写出（别把「构建失败」误读成「数据已经是新的」）。

## 七、后续批次

已交付第 1–3 章。第 4–6 章（微分中值定理与导数的应用 / 积分 / 微分方程）尚未处理。

扩容步骤（界面与 `data.json` 结构**无需改动**，章节下拉框已按数据动态生成）：

1. 新建 `.tools/ch4_content.py`，按同一 schema 写条目（含 `origin` 字段）；
2. 在 `build_all.py` 中 import 该模块、加进模块元组、把 id 依原文顺序写进 `ORDER`；
3. 在 `CH_TITLES` 里补上章名；
4. 在 `.tools/edges.json` 追加依赖边（`by` 里引用的每个裸 id 都必须有对应边）；
5. 跑 `build_all.py` → `build_html.py` → 全套校验。
