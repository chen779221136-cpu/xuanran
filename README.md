# 包小盒 3D 云渲染控制台 — 页面完整抓取

- 源页面: `https://www.baoxiaohe.com/render-v3/console?render_id=irdj967yds`
- 页面标题: 包小盒·3D云渲染 - 15107540674 的 未命名的渲染 - 营销提案效果图在线设计
- 抓取方式: 接管已登录的 Chrome（CDP :9222），执行页面内 JS 取数 + 原样下载资源
- 抓取时间: 2026-09-23
- 合计: 290.2 MB

## 一、页面结构

单页应用（Vue3 + Three.js），路由仅 `/console`、`/video`，所有数据经 `/api/v2/*` 拉取，未登录返回 `{"code":401,"msg":"请登录"}`。

布局：左侧工具栏（场景 / 素材 / 材质 / 灯光 / 我的 / 帮助）→ 二级资源面板 → 中间 3D 视口（WebGL canvas）→ 右侧属性面板（场景 / 模型 / 光影）。

## 二、当前渲染方案

| 项 | 值 |
|---|---|
| 名称 | 未命名的渲染 |
| render_id | `irdj967yds` |
| 内部 id | 6295533 |
| 配置版本 | 5.0.0 |
| 构图比例 | 4:3 |
| 创建 / 更新 | 2026-09-17T10:18:39.000Z / 2026-09-17T15:06:45.000Z |
| 模型 | 1 个 gltf（未命名设计_01） |
| 材质 | 3 个 |
| 灯光 | 2 盏（环境光 + 太阳光） |
| 相机 | fov 45，perspective |
| 结构节点 | 4 个（side / inner / outer / collapse） |
| 分享状态 | isPublic=1，访问量 0 |

## 三、离线网页版（双击即可打开）

抓下来的 `02_dom.html` 是**带 JS 的原始快照**，直接双击会白屏——脚本会重新接管页面、
再次请求接口，请求失败就把已渲染的内容清空。所以额外做了一份剥离脚本的静态版：

```
offline/index.html      ← 双击这个，纯离线可看
offline/preview.png     ← 离线版渲染效果核对图
offline/assets/         ← 15 个 CSS + 图片，全部本地化
```

处理内容：

- 剥离全部 `<script>` / `<iframe>` / 第三方组件（客服、统计、埋点）
- 15 个 CSS 全部下载，并把 CSS 内的 `url()` 按**相对 CSS 文件自身**改写
- 全部图片本地化（69 个文件），`index.html` 里**残留绝对 URL = 0**
- three.js 的 WebGL canvas 离线不会重绘，替换为抓取时导出的视口快照
- 清掉原站自带的 21 处坏图占位（`src=""`、隐藏的图标回退 `img`）

已实机核对：82 张图片 **0 张坏图**，18 个样式表全部加载，版面与线上一致。

> 说明：`//yun.baoxiaohe.com//scene/color.png` 上游本身 404（双斜杠），**原页面同样加载不到**，不算遗漏。

## 四、文件清单

| 文件 | 说明 |
|---|---|
| `02_dom.html` | 渲染完成后的完整 DOM（175 KB） |
| `03_screenshot.png` | 页面整屏截图（含左中右三栏） |
| `04_canvas_snapshot.png` | 3D 视口 canvas 导出图 |
| `01_page_text.txt` | 页面可见文字 |
| `06_ui_dump.json` | UI 区块 / 图片 / 控件结构化清单 |
| `10_render_config.json` | **核心**：完整 3D 场景配置（相机、灯光、材质、贴图、模型、节点变换） |
| `11_share_info.json` | 分享信息 |
| `12_album_list.json` | 渲染图册列表（20 个方案） |
| `20_storage.json` | localStorage / sessionStorage 全量 |
| `05_libraries_raw.json` + `libraries/` | 各资源库接口原始响应（场景 / 材质 / 模型 / 灯光 / 模板 / 我的设计） |
| `manifest.json` | 全部下载资源的 URL ↔ 本地文件对照表 |
| `assets/` | 本渲染实际引用的资源（gltf 模型、贴图、HDR 环境、缩略图） |
| `album/` | 渲染图册输出图（20 张，高清 PNG） |
| `designs/` | 「我的设计」缩略图（11 个方案） |
| `offline/index.html` | **离线静态版网页**，双击直接看（详见第三节） |
| `library_materials/` `library_scenes/` `library_templates/` `library_lights/` `library_meshes/` | 素材库 / 场景库 / 模板库 / 灯光库缩略图 |

## 五、本渲染引用的核心资源

- `https://oss.baoxiaohe.com/renderer/i/3391aa59b39fcc6d1ebd235ed26d98ffc905c2d4.png`
- `https://oss.baoxiaohe.com/renderer/i/5b474a31f3c6c69f9714a911a9e204f2bc892e4c.jpeg`
- `https://oss.baoxiaohe.com/renderer/i/6c63a8618942db05bb3ec5eeb69b7935df0b8b6d.png`
- `https://oss.baoxiaohe.com/renderer/m/167aa17beedefc246d1a659e12e29f55b1c3691a.gltf`
- `https://yun.baoxiaohe.com/admin-materials/f22065d9-22be-4c1d-a87e-335f24cdf95f.png`
- `https://yun.baoxiaohe.com/render-hdr/8e43a41e-035d-4588-b080-c5750564f885.hdr`

## 六、资源库接口状态

| 接口 | 结果 |
|---|---|
| `/api/v2/...my_designs_configs` | list[11] |
| `/api/v2/...scene_collections` | code=422 |
| `/api/v2/...scene_bgimage` | list[15] |
| `/api/v2/...material_cates` | dict(2 keys) |
| `/api/v2/...material_list` | list[20] |
| `/api/v2/...model_cates` | dict(4 keys) |
| `/api/v2/...premium_material` | code=422 |
| `/api/v2/...premium_light` | code=422 |
| `/api/v2/...official_templates` | list[20] |
| `/api/v2/...official_template_ctree` | list[6] |
| `/api/v2/...official_lighttemplate_normals` | list[6] |
| `/api/v2/...render_collections` | code=404 |
| `/api/v2/...render_collections_model_list` | list[0] |
| `/api/v2/...new_mesh_list` | list[20] |
| `/api/v2/...view_image_albums` | list[20] |
| `/api/v2/...render_limit` | list[5] |
| `/api/v2/...user_info` | code=401 |

## 七、备注

- 站点为纯前端 SPA，`curl` 只能拿到空壳 HTML，正文完全依赖 JS 渲染 + 登录态。
- 页面与全部 `/api/v2/*` 接口均要求登录；未登录返回 401 `请登录`。
- 拿到的 `10_render_config.json` 可直接用于重建场景：`data.config.config` 内含 scene / models / materials / nodeTransform / operations。
- 模型 `167aa17b…gltf` 自带 base64 内嵌 buffer，可独立使用，无外部依赖。