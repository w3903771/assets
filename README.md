# assets

个人跨项目的**大体积静态资源仓库**,通过 [jsDelivr](https://www.jsdelivr.com/) CDN 分发。

不绑定任何单个项目 —— 任何一个站点、笔记、Demo 需要放视频、大图、字体、
数据文件时都可以往这里加,按项目分子目录。

## 为什么要有这么个仓库

我的主站 [ttzg.site](https://ttzg.site) 跑在美国洛杉矶的小机器上。实测过:

| 路径 | 吞吐 |
|---|---|
| 服务器本机取文件 | 515 KB/s(顶满 nginx `limit_rate`) |
| scp 从服务器下载(端口 22) | 153~197 KB/s |
| **HTTPS 下载(端口 443)** | **0.7~7.8 KB/s** |
| **jsDelivr** | **1.8~43.9 MB/s** |

小文件(HTML/CSS/JS/图标)自有服务器完全够用,但**几 MB 以上的东西根本传不动**。
所以重资源放这里走 CDN,轻资源仍由自有服务器提供。

## 目录约定

```
<项目名>/<文件>
```

一个项目一个子目录,目录名直接用站点域名或项目名,别用缩写,半年后自己也能认出来。

| 目录 | 内容 |
|---|---|
| `ttzg.site/` | 主站首屏背景(poster + AV1/H.264 两版视频) |

## 怎么引用

**必须带 `@` 版本标签。**

```
https://cdn.jsdelivr.net/gh/w3903771/assets@v1/<项目>/<文件>
```

不带标签会走默认分支,jsDelivr 的缓存策略不同,而且内容一变就可能新旧混用。
换素材时**打新标签**(`v2`、`v3`…),同时改引用方,老标签的 URL 永远指向老内容不会断。

```bash
git add . && git commit -m "..."
git tag v2 && git push && git push origin v2
```

## 现有内容

### ttzg.site/ —— 主站首屏背景

| 文件 | 规格 | 体积 | SSIM |
|---|---|---|---|
| `bg-poster.jpg` | 2560×1080 JPEG q6 | 173 KB | — |
| `bg-av1.mp4` | 2560×1080 / 30fps / AV1 CRF38 | 9.96 MB | 0.98842 |
| `bg-h264.mp4` | 2560×1080 / 30fps / H.264 CRF28 | 10.35 MB | 0.98196 |

**2560×1080 是算出来的,不是拍脑袋**:页面用 `object-fit: cover`,
2.37:1 的视频要盖满 16:9 的 1080p 屏必须按高度放大,需要 2560 宽。
压到 1920 会被再放大 1.33 倍,肉眼明显发虚。

AV1 比 H.264 小 25% 且 SSIM 更高,但 Safari 要 17+ 且需硬件解码,
所以用 `<source>` 两条,AV1 在前、H.264 兜底。

```html
<video class="bjimg"
       poster="https://cdn.jsdelivr.net/gh/w3903771/assets@v1/ttzg.site/bg-poster.jpg"
       autoplay loop muted playsinline preload="auto">
  <source src="https://cdn.jsdelivr.net/gh/w3903771/assets@v1/ttzg.site/bg-av1.mp4"
          type='video/mp4; codecs="av01.0.12M.08"'>
  <source src="https://cdn.jsdelivr.net/gh/w3903771/assets@v1/ttzg.site/bg-h264.mp4"
          type="video/mp4">
</video>
```

原始 60.4 MB / 3412×1440 / 60fps 的母带不在本仓库,留在本地。
完整的链路实测与编码参数取舍见主站运维仓库的 `ops/runbooks/frontpage-perf.md`。

## 关于"会不会被别人盗用"

**这个仓库是公开的,这是设计使然** —— jsDelivr 只能分发公开的 GitHub 仓库。
所以要先想清楚两件事:

### 1. 别人直接引用你的 URL,你有损失吗?

**基本没有。** 流量是 jsDelivr 承担的,不走你的服务器,不产生任何费用。
jsDelivr 本身也没有 Referer 白名单之类的防盗链机制 —— 公共 CDN 的定位就是让人随便引。

真要防,只有换成带防盗链的托管(自有服务器 / 对象存储的 Referer 白名单),
但那样就回到"自有服务器传不动"的老问题了。**不值得。**

### 2. 你放进来的东西,你有权公开分发吗?

**这才是真正要注意的。** 放进公开仓库 = 公开再分发。

当前 `ttzg.site/` 里那几个背景素材是动漫风格的第三方作品
(主站头像上还带着 `ArtistryHub` 的水印),来源和授权我并不清楚。
自用和公开再分发在版权上不是一回事。这点我只做提示,你自己判断。

### 实际该守的几条

- **不要放任何私密内容**。这里的一切等同于公开,且 git 历史删不干净
- **不要放会变的配置/密钥**
- 自己的原创作品想保留署名,加个 `LICENSE` 文件说明
- 用版本标签,别让引用方被你的改动搞崩
