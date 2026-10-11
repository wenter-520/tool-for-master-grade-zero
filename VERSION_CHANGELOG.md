# 知识星空 · 版本迭代与更新说明

> 依据以下文件注释整理：  
> `src/admin.css`、`src/admin.js`、`src/app.js`、`src/galaxy.js`、`src/style.css`、`src/datastore.js`、`admin.html`、`index.html`  
> 注意：当前资料中存在同名文件的多份版本，部署时请确认实际覆盖关系。

---

## 1. 版本总览

| 模块 | 文件 | 最新 / 推荐版本 | 旧版 / 备选版本 | 关键差异 |
|---|---|---|---|---|
| 卡片管理 | `admin.css` / `admin.js` | v4.9.0 | 无明确旧版 | 聚合计数、空状态引导、关联卡片多分组选择器 |
| 学习主应用 | `app.js` | v5.6 | v4.7 | v5.6 增加星空右侧领域筛选面板；v4.7 为旧版宇宙/银河/星云布局 |
| 星空引擎 | `galaxy.js` | v6.1 | v3.3 | v6.1 为长方体分层 + 流形曲面 + 高亮 L0 层；v3.3 为旧版 3D 旋转/右键滚转 |
| 全局样式 | `style.css` | v5.1，含 v5.5 覆盖 | v4.5 / v4.6 | v5.1 增加搜索、标签筛选、领域抽屉；v4.6 增加知识索引与领域树 |
| 持久层 | `datastore.js` | 容错版 | 标准版 | 容错版 IndexedDB 失败时降级内存；标准版直接 reject |
| 管理页面 | `admin.html` | v4.9.0 配套 | — | 管理模式 / 总库模式 |
| 学习页面 | `index.html` | v5.6 配套 | 旧版 index | v5.6 星空视图包含 `#gfPanel` 领域筛选面板 |

---

## 2. 卡片管理页：admin v4.9.0

### 2.1 新增与修复

- 关联卡片选择器改为“多分组完整列表”：
  - 分组 1：标签匹配，跨所有领域，按命中标签数排序。
  - 分组 2：同第三分级，与当前卡片属于同一 L3 领域。
  - 分组 3+：第三分级下的各分级，L4、L5、L6... 逐层分组。
  - 分组 N：其他层级，兜底。
  - 每组完整展示，不截断。
- 领域树卡片计数向上聚合：
  - 父领域显示“含全部子领域”的卡片总数。
  - `.num.agg` 表示聚合计数。
  - `.num.zero` 表示零卡片弱化。
- 空状态智能提示：
  - 当前领域无卡片但子领域有卡片时，提示子领域卡片数量。
  - 提供 `.empty-btn` 引导按钮“包含子领域”。
- 切换按钮文案更明确：
  - 仅当前领域 / 含子领域。
- 关联选择器搜索支持正面、背面、标签、领域名。

### 2.2 行为变化

- 领域树计数不再是“仅本领域直接卡片数”，而是“含全部后代领域”。
- 空领域不再只显示“没有卡片”，会引导用户切换为包含子领域。
- 关联卡片选择器不再截断列表，按优先级分组展示。

### 2.3 兼容性

- 需要 `admin.html` 中存在对应 DOM：
  - `#deckList`、`#cardList`、`#editor`、`#edLinks`、`#edLinkPanel` 等。
- 推荐搭配容错版 `datastore.js`，避免 IndexedDB 不可用时阻断管理页。
- 可独立使用，不依赖 `app.js` / `galaxy.js`。

---

## 3. 学习主应用：app v5.6 vs v4.7

### 3.1 app v5.6 新增

- 星空视图右侧新增“领域筛选”浮动面板，可折叠。
- “按领域”模式：
  - 多选领域。
  - 单独或组合查看。
  - 支持搜索领域。
  - 每个领域支持“隐藏 / 恢复”“聚焦”。
- “按分级”模式：
  - 一键选择一级 / 二级 / 三级 / 全部层级。
  - 显示各层级领域数量。
- 底部操作：
  - 全选
  - 清空
  - 适应视野
- 状态新增：
  - `state.galaxyFilter`
  - `mode`：`deck` / `level`
  - `decks`：选中的领域 id 集合
  - `level`：分级层级
  - `search`：领域搜索词
- 渲染函数：
  - `gfRender()`
  - `gfRenderTree()`
  - `gfRenderLevels()`
  - `gfComputeVisibleIds()`
  - `gfComputeCounts()`
- `rebuildScene(opts)` 支持 `refit`，筛选后自动适应视野。
- 星空节点过滤会同时过滤卡片和 `deckTree`。
- 依赖 `galaxy.js v6.1` 的 `highlightLayers` 能力。

### 3.2 app v4.7 特点

- 星空布局为旧版层级：
  - 宇宙 L0 → 银河 L1 → 星云 L2 → 星团 L3+。
- 卡片排布为“四尺度混合流形”：
  - 宏观：鞍面 / 球谐片段。
  - 中观：双频波纹。
  - 微观：分形噪声 fbm。
  - 特征：局部高斯凸起 / 凹陷。
- 学习视图左侧为“领域目录树”，可折叠、点击筛选。
- 顶栏有 `chips` 领域筛选条。
- 星空交互：
  - 左键拖拽 3D 轨道旋转。
  - 右键拖拽绕 Z 轴滚转。
  - 滚轮缩放。
  - 点击选中。
  - 分组自转。
- 无 `#gfPanel` 领域筛选浮动面板。

### 3.3 迁移注意

- 不要混用 `app.js v5.6` 与 `galaxy.js v3.3`：
  - v5.6 需要 `setData(nodes, links, deckTree, highlightLayers)` 第四参数。
  - v5.6 的领域筛选高亮依赖 v6.1 的层高亮/淡化。
- 不要混用 `index.html v5.6` 与 `app.js v4.7`：
  - v5.6 页面包含 `#gfPanel`，但 v4.7 没有 `gfBindEvents()`。
- v4.7 更适合搭配旧版 `index.html`、`galaxy.js v3.3`、`style.css v4.5/v4.6`。

---

## 4. 星空引擎：galaxy v6.1 vs v3.3

### 4.1 galaxy v6.1 新增

- 布局改为：
  - 3D 长方体分层 + 流形曲面 + 曲面块。
- L0 顶层领域：
  - 竖直堆叠的长方体层。
  - 每层独立自转。
- L1 子领域：
  - 层内流形曲面上的点。
- L2+ 子领域：
  - L1 周围的小曲面块。
- 卡片：
  - 曲面块内网格散布 + 上下起伏。
- `setData` 支持 `highlightLayers`：
  - 高亮选中的 L0 层。
  - 未选中层淡化。
- `_renderLayerBoxes`：
  - 支持高亮 / 淡化。
  - 顶面光晕仅在高亮层显示。
- `_renderDeckLinks`：
  - 若筛选生效，非选中 L0 层内连线极度淡化。
- 指针交互：
  - 左键拖拽平移。
  - `WASD` 旋转。
  - `Q/E` 滚转。
  - 滚轮缩放。
  - 点击节点选中。
- 适合 `app.js v5.6` 的领域筛选面板。

### 4.2 galaxy v3.3 特点

- 旧版三维知识星空引擎。
- 修复右键拖拽：
  - `pointermove` / `pointerup` 挂到 `document`。
  - 鼠标移出 canvas 也不丢事件。
  - 右键 `pointerdown` / `mousedown` 阻止默认菜单。
- 交互：
  - 左键拖拽：3D 轨道旋转。
  - 右键拖拽：绕 Z 轴滚转。
  - 滚轮：缩放。
  - 左键点击：选中节点。
  - 分组自转：每个领域组围绕自身中心沿 Y 轴自转。
- 可见性：
  - 未复习卡片保留基础亮度、基础光晕、基础描边。
  - 掌握度作为增亮系数，不是可见性开关。
- 适合 `app.js v4.7`。

### 4.3 迁移注意

- `app.js v5.6` 必须使用 `galaxy.js v6.1`。
- `app.js v4.7` 可使用 `galaxy.js v3.3`。
- v6.1 的 `setData` 第四个参数 `highlightLayers` 是 v5.6 筛选高亮的关键。
- v3.3 没有 `highlightLayers`、`_renderLayerBoxes`、`focusOn` 等新版能力。

---

## 5. 样式：style v5.1 / v5.5 / v4.6 / v4.5

### 5.1 style v5.1

- 学习视图三栏布局：
  - 左：进度、统计、领域目录。
  - 中：卡片学习区。
  - 右：卡片信息、相关卡片、快捷键。
- 公式使用 KaTeX。
- 背面支持“答案 / 公式”Tab 切换。
- 新增：
  - 搜索框
  - 标签筛选
  - 领域筛选抽屉
- 响应式适配：
  - 1024px、900px、768px、620px、低高度屏幕。

### 5.2 style v5.5 覆盖

- 增加 Galaxy 领域筛选浮动面板样式 `.gfp`。
- 注意：`index.html v5.6` 中实际使用的是内联 `.gf-panel`，与 `.gfp` 不是同一套类名。
- 如果使用 `app.js v5.6`，优先确认 `index.html` 中的 `#gfPanel` 与内联样式是否完整。

### 5.3 style v4.6

- 增加知识索引视图样式：
  - `.view-index`
  - `.idx-side`
  - `.idx-grid`
  - `.idx-card`
  - `.idx-deck-row`
  - `.idx-deck-item`
  - `.idx-tag-chip`
  - `.idx-state-chip`
- 增加学习视图领域目录树：
  - `.deck-tree`
  - `.dt-all`
  - `.dt-row`
  - `.dt-fold`
  - `.dt-item`
- 增加针对性集合标记 `.sb-collection`。

### 5.4 style v4.5

- 基础三栏学习视图。
- 进度环、统计卡片、卡片翻转、评分按钮、编辑模式。
- 星空侧栏、弹窗、Toast。
- 标签筛选样式保留，但旧版可能不参与渲染。

---

## 6. 持久层：datastore.js 容错版 vs 标准版

### 6.1 容错版

- IndexedDB 不可用时降级到内存存储 `_memHandles`。
- 所有 IDB 操作静默失败，不阻断应用。
- 适用于：
  - 浏览器隐私模式。
  - IndexedDB 被禁用。
  - 配额满、磁盘满。
  - 企业策略限制。
- 刷新后内存句柄丢失，需要重新选择文件夹。
- 其他 API 与标准版一致：
  - `isSupported`
  - `pickDirectory`
  - `verifyPermission`
  - `getSavedHandle`
  - `saveHandle`
  - `clearSavedHandle`
  - `readJSON`
  - `writeJSON`
  - `deleteEntry`
  - `getSubdir`
  - `listJSONFiles`

### 6.2 标准版

- 直接使用 `indexedDB.open`。
- 失败时 reject。
- 适合桌面 Chrome / Edge 等完整支持 File System Access API 与 IndexedDB 的环境。

### 6.3 推荐

- 生产环境推荐容错版。
- 若只面向 Chrome / Edge 桌面且可保证 IndexedDB 可用，标准版也可。

---

## 7. 页面：index.html v5.6 vs 旧版

### 7.1 index.html v5.6

- 顶栏：
  - 学习 / 星空 / 知识索引。
- 星空视图：
  - `.sky-wrap`
  - `#sky`
  - `#gfPanel` 领域筛选面板。
- 领域筛选面板包含：
  - 模式：按领域 / 按分级。
  - 搜索。
  - 领域树。
  - 分级按钮。
  - 全选 / 清空 / 适应视野。
- 引入：
  - `src/galaxy.js` v6.1
  - `src/app.js` v5.6

### 7.2 旧版 index.html

- 星空视图无 `#gfPanel`。
- 顶栏有 `#chips`，由 `app.js v4.7` 渲染领域筛选。
- 引入：
  - `src/galaxy.js` v3.3
  - `src/app.js` v4.7

### 7.3 迁移注意

- 使用 v5.6 页面时，必须同时使用 v5.6 `app.js` 和 v6.1 `galaxy.js`。
- 使用旧版页面时，不要加载 v5.6 的 `gfBindEvents` 相关代码。
- `admin.html` 与学习页版本相对独立，可共用数据。

---

## 8. 数据与存储键

| 键 / 路径 | 用途 |
|---|---|
| `kstar.data.v1` | 卡片与领域数据，localStorage |
| `kstar.progress.v1` | 学习进度、统计、设置 |
| `kstar.view.v1` | 当前视图：study / galaxy / index |
| `decks/<顶层领域 id>.json` | 连接文件夹后，每个顶层领域一个文件 |
| `_snapshot.json` | 导出快照 |

---

## 9. 总体迁移建议

1. 若使用最新功能：
   - `admin v4.9.0`
   - `index.html v5.6`
   - `app.js v5.6`
   - `galaxy.js v6.1`
   - `style.css v5.1`
   - `datastore.js` 容错版
2. 若使用旧版宇宙/银河/星云布局：
   - `index.html` 旧版
   - `app.js v4.7`
   - `galaxy.js v3.3`
   - `style.css v4.5/v4.6`
3. 不要跨大版本混用：
   - `app v5.6` + `galaxy v3.3` 不兼容。
   - `index v5.6` + `app v4.7` 不兼容。
   - `app v4.7` + `index v5.6` 会出现 `#gfPanel` 无事件绑定。
4. 管理页 `admin v4.9.0` 可独立运行，但建议搭配容错版 `datastore.js`。
