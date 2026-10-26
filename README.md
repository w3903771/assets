# ttzg-assets

[ttzg.site](https://ttzg.site) 的大体积静态资源，通过 jsDelivr CDN 分发。

主站服务器在美国洛杉矶，国内访客走 443 口拉大文件只有 0.7~7.8 KB/s，
而 jsDelivr 实测 4.6 MB/s —— 所以把重资源挪到了这里。
站点的 HTML/CSS/JS 等小文件仍由自有服务器提供。

## 内容

| 文件 | 规格 | 体积 | 用途 |
|---|---|---|---|
| `bg-poster.jpg` | 2560×1080 JPEG | 173 KB | 背景打底图，也是 `<video>` 的 poster |
| `bg-av1.mp4` | 2560×1080 / 30fps / AV1 CRF38 | 9.96 MB | 背景视频，首选 |
| `bg-h264.mp4` | 2560×1080 / 30fps / H.264 CRF28 | 10.35 MB | 背景视频，Safari 等不支持 AV1 时回退 |

2560×1080 是按 `object-fit: cover` 算的：2.37:1 的视频要盖满 16:9 的 1080p 屏，
必须按高度放大，需要 2560 宽。压到 1920 会被再放大 1.33 倍，明显发虚。

## 引用方式

```html
<video class="bjimg" poster="https://cdn.jsdelivr.net/gh/w3903771/ttzg-assets@v1/bg-poster.jpg"
       autoplay loop muted playsinline preload="auto">
  <source src="https://cdn.jsdelivr.net/gh/w3903771/ttzg-assets@v1/bg-av1.mp4"
          type='video/mp4; codecs="av01.0.12M.08"'>
  <source src="https://cdn.jsdelivr.net/gh/w3903771/ttzg-assets@v1/bg-h264.mp4"
          type="video/mp4">
</video>
```

**务必带上 `@` 版本标签**。不带标签 jsDelivr 会走 latest，缓存策略不同，
而且内容变了会导致缓存不一致。换素材时打新标签（`@v2`），同时改引用。

## 原始件

原始 60.4 MB / 3412×1440 / 60fps 的母带不在本仓库，
在本地 `ttzgsite/assets/originals/background-3412x1440-60MB.mp4`。
重编码参数见 `ttzgsite/ops/runbooks/frontpage-perf.md`。
