---
name: frontend-reference-curator
description: Curate, capture, verify, and catalog real live frontend and interaction design cases in yangjh-xbmu/frontend-reference-library, or record user approval/rejection feedback into catalog.json, approved/README.md, and preferences.md. Use whenever the user shares a website URL to add to the frontend reference library, asks to batch-collect design/interaction cases, or gives approval/rejection feedback on existing cases (F01, F02, ...).
---

# Frontend Reference Curator（前端与交互设计参考库维护 Skill）

本 Skill 用于维护公开仓库 `yangjh-xbmu/frontend-reference-library`。当用户提供真实线上网址要求收录、要求按主题批量补录案例，或对已有案例给出认可/淘汰/偏好反馈时，严格按本流程执行。

## 一、硬性边界（不可违背）

1. **只收真实上线、可打开体验的网页**：必须真实访问并截屏核看；拒收无线上 URL 的 Dribbble/Behance 概念飞机稿、牺牲可读性的重 3D 炫技营销页、纯电商转化漏斗页。
2. **肉眼核看关卡**：每张入库截屏必须用图像查看工具实际检查，确认主体界面清晰可见。凡出现 `403/404`、服务器报错、登录/扫码墙、身份验证拦截、白屏，或无法绕过的全屏声明/活动弹窗遮挡，**一律不入库**，在汇报中说明被过滤原因即可。
3. **公开仓库版权边界**：禁止搬运第三方完整源码、字体文件或闭源商用素材包；只保留原始 URL、作者/机构署名、结构化交互拆解，以及等比例缩放（`1440×900` → `1280×800`，不裁剪、不改绘、不用 AI 生成图替代）的少量评述性截屏。
4. **候选与认可分离**：新入库条目默认 `status: "candidate"`；**只有宏哥明确认可后**才改为 `approved`，并完整保留认可范围（整站/单页/局部交互机制）、日期与宏哥原话。AI 的设计观察不代表宏哥偏好。
5. **不越界开发**：本仓库保持纯 Markdown + JSON + 静态图片的轻量形态，不引入前端展示应用、爬虫框架或重型构建流水线。

## 二、四条主线编码

每条案例归入最贴合的一条主线（`track` / `track_label`）：

| `track` | `track_label` | 典型场景与观察重点 |
| --- | --- | --- |
| `ai-workbench` | AI 工作台与人机协同 | 对话流与画布分栏、证据溯源与原文高亮、长任务状态可观测、审批确认控件、高密度降噪 |
| `data-journalism` | 数据新闻与滚动叙事 | Scrollytelling 滚动联动、筛选器与地图联动、中文数据排印、可引用数据口径与专题架构 |
| `explorable-humanities` | 可交互解释与数字人文 | Explorable Explanations、多维文物/典籍检索、时空叙事与全景漫游 HUD、教学与竞赛互动表达 |
| `portal-docs` | 教学门户与轻量化落地页 | 清晰信息架构、双栏对照文档、站内检索、克制的组件体系与首屏信息传达 |

## 三、流程 A：收录新案例（单条或批量）

### Step 1：同步仓库并查重定号

1. 进入仓库根目录，执行 `git status -sb`；若落后远端则 `git pull --ff-only`。
2. 读取 `catalog.json`（及 `AGENTS.md`）：
   - 按 URL / 域名 / 作品名查重，避免重复收录；
   - 取当前最大编号（如 `F16`），顺延分配新编号（`F17`、`F18`…），并确定简短小写连字符 `slug`（如 `dpm-digicol`），目录为 `references/<ID>-<slug>/`。

### Step 2：实测访问与桌面端截屏

1. 使用无头 Chrome（或本机等效 Chromium/Playwright，视口固定 `1440×900`，中文站点加 `--lang=zh-CN`）截取真实页面到临时目录（如 `/tmp/frontend-capture/`，不要把临时文件留在仓库内）：

```bash
google-chrome \
  --headless=new --no-sandbox --disable-gpu --disable-dev-shm-usage \
  --hide-scrollbars --lang=zh-CN --window-size=1440,900 \
  --virtual-time-budget=12000 \
  --screenshot=/tmp/frontend-capture/<slug>.png \
  "<URL>"
```

2. 使用 ImageMagick 等比例缩放到 `1280×800` 的 JPEG（保持原貌、不裁剪）：

```bash
convert /tmp/frontend-capture/<slug>.png \
  -resize 1280x800 -quality 85 \
  references/<ID>-<slug>/viewport-desktop.jpg
```

3. **必须用图像读取工具打开 `viewport-desktop.jpg` 肉眼核看**：确认页面正常渲染且无登录墙/报错/全屏遮挡弹窗；若不合格则删除该临时目录并在最终汇报中说明，不写入 `catalog.json`。
4. 计算实际文件大小（`bytes`）与 `sha256` 校验值。

### Step 3：写入逐例拆解与元数据

1. 创建 `references/<ID>-<slug>/README.md`，遵循既有结构：
   - 标题：`# <ID> <中文标题>`
   - 元信息：原作、作者或机构、状态（`候选，尚未经宏哥认可`）、主线、类型、原始线上链接
   - `## 实测界面截屏`：嵌入 `![<work> 桌面端实测截屏](viewport-desktop.jpg)`
   - `## 核心交互与设计机制`：3–5 条具体、可观察的交互/排版机制（避免空泛形容词）
   - `## 本轮筛选意见`：明确标注“以下为 AI 的交互与设计观察，等待宏哥判断”，列出“可观察之处 / 与宏哥工作的关联 / 局限与迁移边界 / 技术栈线索”
   - `## 认可记录`：默认写“等待宏哥指定整站、具体页面或局部交互机制。”
   - `## 来源与版权边界`：核看与截屏日期、视口与缩放说明、原作者权利保留声明
2. 在 `catalog.json` 的 `items` 末尾追加条目（更新顶层 `updated_on`），字段须与既有条目完全一致：
   `id`, `slug`, `title`, `work`, `creator`, `track`, `track_label`, `url`, `format`, `patterns`, `why`, `transfer`, `limit`, `tech_hints`, `status` (`"candidate"`), `user_feedback` (`null`), `approved_scope` (`[]`), `retrieved_on`, `folder`, `rights`, `screenshots`（含 `path`, `caption`, `viewport`, `bytes`, `sha256`）。
3. 在 `capture-manifest.json` 的 `items` 末尾追加条目（更新 `updated_on`）：
   `id`, `slug`, `url`, `path`, `viewport`, `scaled_to`, `bytes`, `sha256`, `captured_on`, `status` (`"captured"`).
4. 更新根目录 `README.md`：
   - 更新总案例数、已认可数与四条主线编号范围；
   - 在 `## 案例目录` 表格中追加新条目行。
5. 若来源已在 `discovery-sources.md` 中，将其“已入库编号”与“最近巡检”同步更新；若为值得长期跟踪的新来源，可按档位补充一行。
6. **批量收录（单次 ≥ 3 条）时**，使用 `montage` 生成批次总览拼图 `boards/<NN>-<batch-slug>.jpg`（如 `-tile 2x3 -geometry 640x400+16+16 -background "#f5f5f4" -quality 85`），并在根 `README.md` 的 `## 批次总览拼图` 顶部嵌入。

### Step 4：自动化校验、提交与推送

提交前运行校验脚本，确保全部通过：

```bash
python3 - <<'PY'
import hashlib, json, os
cat = json.load(open("catalog.json", encoding="utf-8"))
man = json.load(open("capture-manifest.json", encoding="utf-8"))
assert len(cat["items"]) == len(man["items"]), "catalog/manifest count mismatch"
man_by_id = {m["id"]: m for m in man["items"]}
for item in cat["items"]:
    assert os.path.exists(os.path.join(item["folder"], "README.md")), f"missing README: {item['id']}"
    for s in item["screenshots"]:
        p = s["path"]
        assert os.path.exists(p), f"missing screenshot: {p}"
        data = open(p, "rb").read()
        sha = hashlib.sha256(data).hexdigest()
        assert s["bytes"] == len(data) and s["sha256"] == sha, f"catalog checksum mismatch: {item['id']}"
        m = man_by_id[item["id"]]
        assert m["bytes"] == len(data) and m["sha256"] == sha, f"manifest checksum mismatch: {item['id']}"
print(f"OK: {len(cat['items'])} items verified.")
PY
```

确认 `git status -sb` 无临时文件残留后，按语义化提交信息（如 `feat: add <ID>-<slug> ...`）提交并推送到 `origin main`，然后向宏哥汇报新增编号、被过滤的候选及截屏/拼图预览。

## 四、流程 B：记录宏哥的认可、淘汰与偏好反馈

当宏哥明确表态（例如“认可 F13 的多维筛选和配色”“F01 整站进 approved”“F11 信息太杂，不收”）时，同步更新以下 5 处文件：

1. **`catalog.json`**：
   - 认可：`status` 改为 `"approved"`，`user_feedback` 填入宏哥原话，`approved_scope` 填入具体范围数组（如 `["整站"]` 或 `["左侧多维分类筛选树", "青绿配色克制感"]`），更新顶层 `updated_on`。
   - 淘汰/不认可：`status` 改为 `"rejected"`，`user_feedback` 填入宏哥原话（保留记录，不静默删除历史编号，避免后续重复踩坑）。
2. **`references/<ID>-<slug>/README.md`**：更新顶部状态行，并在 `## 认可记录` 中写入日期、认可/淘汰范围与宏哥原话。
3. **`approved/README.md`**：若为认可，新增一行记录（日期、案例链接、认可范围、宏哥原话）并更新顶部计数。
4. **`preferences.md`**：在 `## 反馈台账` 追加记录（日期、条目、范围、宏哥原话、状态）；若宏哥明确给出长期规则或审美原则，再同步沉淀到确认偏好区，不要替用户脑补未明说的偏好。
5. **根目录 `README.md`**：更新顶部已认可计数与 `## 案例目录` 对应行的状态列（`已认可` / `已淘汰`）。

完成后运行 Step 4 的 JSON 与路径校验，提交（如 `docs: record approval for F13`）并推送到 `origin main`。

## 五、日常触发方式示例

- 收编单个案例：“把这个网站收进前端案例库：`https://...`，我觉得它的时间轴联动做得很好”
- 按主题批量补录：“再补 4 个 AI 工作台证据溯源类案例”
- 记录认可与偏好：“我认可 F13 的多维筛选和 F16 的全屏 HUD，F12 暂不进 approved，记一下”
