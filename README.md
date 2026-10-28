# 夜澜 NOCTURNE

一个会听音乐的房间。音乐的高音落下，墙面上就泛起水波焦散；低音让房间呼吸，每一拍激起一圈大涟漪。

纯前端单文件，WebGL2 + Web Audio，无依赖、无外部资源，双击 `index.html` 就能跑。

## 直接打开

双击 `index.html`。需要支持 WebGL2 的浏览器（Chrome / Edge / Firefox / Safari 16+）。

第一次点击「进入」才会启动音频——这是浏览器的自动播放限制，不是 bug。

## 操作

| 操作 | 效果 |
| --- | --- |
| 移动鼠标 | 环顾房间（方向与鼠标反向，像拖住场景转） |
| 滚轮 | 推进 / 拉远，可以走进房间内部 |
| 点击墙面 | 在该处激起涟漪 |
| 空格 | 暂停 / 继续 |
| H | 隐藏 / 显示界面 |
| 右下角「选择本地音乐」 | 换成你电脑里的歌（mp3 / wav / m4a / flac / ogg） |
| 把音频文件拖进窗口 | 同上 |

不选本地音乐时，页面会实时生成一首 68 BPM、D 利底亚调式的曲子，约 68 秒循环，
段落为 引子 → 绽放 → 高潮 → 平静 → 回升。

## 音画映射

| 声音 | 画面 |
| --- | --- |
| 高频（4.5–12.5 kHz） | 波源数量、焦散亮度、粒子闪烁 |
| 低频（30–160 Hz） | 房间呼吸、墙面明暗、棱线流光 |
| 节拍（低频瞬态） | 一圈大涟漪 |
| 整体能量 | 焦散与辉光强度 |

## 技术说明

- 房间用光线求交在片元着色器里实时渲染，五个面展开成一张「十字形」网格
- 网格上跑波动方程（ping-pong 浮点 FBO，固定 120 Hz 步长）
- 焦散分两层：大尺度曲率给出蓬松光云，雅可比行列式 `det(I + k·Hessian(h))` 在 det→0 处给出真实焦散尖角；三通道用不同 k 做色散
- 有景深、辉光、边缘色差、胶片颗粒
- 帧率不足时自动降低渲染分辨率（渲染尺寸上限约 260 万像素）

## 部署到 GitHub Pages

1. 在 GitHub 新建仓库，比如 `Music-Visual`，公开
2. 把本文件夹里的内容推到仓库根目录（`index.html` 必须在根目录）
3. 仓库 Settings → Pages → Source 选 `Deploy from a branch`，分支 `main`，目录 `/ (root)`
4. 一两分钟后访问 `https://<你的用户名>.github.io/Music-Visual/`

命令行版本：

```bash
git init
git add index.html assets README.md
git commit -m "Music Visual: nocturne"
git branch -M main
git remote add origin https://github.com/<用户名>/Music-Visual.git
git push -u origin main
```

## 目录

```
index.html            页面本体，单个文件，可直接部署
assets/poster.jpg     竖版海报 / 视频封面用图
assets/theme-30s.wav  离线渲染的 30 秒配乐（可选，页面不依赖它）
dev/capture.html      录制用版本：虚拟时钟 + 固定随机种子，逐帧渲染视频用
```

`dev/capture.html` 不是给访客用的，它把时间冻结成可控的帧步进，用来导出逐帧画面。
