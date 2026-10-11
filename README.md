# 知识星空 · 卡片管理

浏览器端知识卡片管理与整理工具。支持领域树、卡片编辑、LaTeX 公式、标签、关联卡片、总库筛选、JSON 导入导出与快照备份。

## 目录

- [项目简介](#项目简介)
- [快速开始](#快速开始)
- [数据格式](#数据格式)
- [使用说明](#使用说明)
- [LaTeX 公式](#latex-公式)
- [注意事项](#注意事项)

---

## 项目简介

“知识星空 · 卡片管理”是“知识星空”的卡片管理端页面，用于创建、编辑、组织知识卡片，并按“顶层领域 → 子领域 → 卡片”的层级结构管理。

| 入口 | 文件 |
|------|------|
| 管理端 | `admin.html` |
| 首页 | `index.html` |

页面默认深色主题：

```html
<body data-theme="dark">
```

---

## 快速开始

### 手动运行

在项目根目录打开命令行：

python3 -m http.server 8000

访问 `http://localhost:8000/admin.html`。

### 使用 start.bat 自启动

在项目根目录新建 `start.bat`：

```bat
@echo off
chcp 65001 >nul
cd /d "%~dp0"
set PORT=8000
set PAGE=admin.html
set PYTHON=
where python >nul 2>nul && set PYTHON=python
if "%PYTHON%"=="" where py >nul 2>nul && set PYTHON=py
if "%PYTHON%"=="" (echo 未找到 Python & pause & exit /b 1)
start "" /b %PYTHON% -m http.server %PORT%
timeout /t 2 /nobreak >nul
start "" http://localhost:%PORT%/%PAGE%
pause
```

双击运行，自动启动服务并打开管理页。

### 开机自启动

1. 右键 `start.bat` → 创建快捷方式。
2. 按 `Win + R`，输入 `shell:startup`。
3. 将快捷方式放入打开的启动文件夹。

---

## 数据格式

### 普通 deck 文件

```json
{
  "version": "4.6",
  "updatedAt": "2026-10-08T06:55:07.557Z",
  "decks": [
    {
      "id": "deck-xxxxxx",
      "name": "领域名称",
      "color": "#f0a07d",
      "description": "",
      "parentId": null,
      "cards": []
    }
  ]
}
```

### 卡片对象字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | string | 卡片唯一 ID |
| `front` | string | 正面/问题 |
| `back` | string | 背面/答案 |
| `formula` | string | LaTeX 公式，可为空 |
| `tags` | string[] | 标签 |
| `links` | string[] | 关联卡片 ID |
| `hint` | string | 提示 |

完整备份使用 `_snapshot.json`。

---

## 使用说明

- **连接数据目录**：点击顶部「连接数据目录」，选择包含 `deck-*.json` 或 `_snapshot.json` 的文件夹。
- **新建领域**：点击「＋ 新建顶层领域」，子领域通过领域菜单操作。
- **新建卡片**：中间卡片栏点击「＋」。
- **编辑卡片**：选择卡片后，在右侧编辑器修改问题、答案、公式、标签、提示、关联。
- **删除/移动卡片**：编辑器右上角对应按钮。
- **关联卡片**：编辑器点击「＋ 添加关联…」搜索添加，`links` 存储关联卡片 ID。
- **总库模式**：顶部切换到「总库」，可按搜索、领域、标签、状态筛选。
- **导入导出**：顶部「导入」选择 JSON，「导出 JSON」下载数据；快照用于完整迁移。

---

## LaTeX 公式

行内：`$E = mc^2$`

行间：

$$
E = mc^2
$$

编辑器「实时预览」会渲染公式。

---

## 注意事项

- 修改 JSON 前先备份。
- 不要破坏 `id`、`parentId`、`links` 字段。
- `links` 引用的卡片 ID 必须存在。
- 「清空全部」不可恢复。
- 推荐使用 Chrome / Edge，通过 HTTP 访问，不要直接双击 `admin.html`。
- 当前数据版本：`4.6`。
