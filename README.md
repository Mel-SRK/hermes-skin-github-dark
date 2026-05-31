# hermes-skin-github-dark

GitHub Dark 风格的 [Hermes Agent](https://github.com/NousResearch/hermes-agent) CLI 皮肤。

## 特性

- GitHub Dark 配色（#58a6ff 蓝、#ff8c00 橙、#3fb950 绿）
- 蓝色 HERMES AGENT ASCII art logo
- 彩色半块字符（▀）头像 ASCII 画
- 暗色透明终端友好
- 审批对话框醒目标橙色标题
- kaomoji 风格 spinner

## 安装

```bash
# 创建 skins 目录（如果不存在）
mkdir -p ~/.hermes/skins

# 下载 skin 文件
curl -o ~/.hermes/skins/github-dark.yaml \
  https://raw.githubusercontent.com/Mel-SRK/hermes-skin-github-dark/main/github-dark.yaml
```

## 激活

```bash
# 方式 1: 配置文件
hermes config set display.skin github-dark

# 方式 2: TUI 内切换
# /skin github-dark
```

重启 Hermes 生效。

## 自定义头像

默认头像来自 `~/Pictures/头像.png`。如需替换，在 `github-dark.yaml` 中修改 `banner_hero` 部分。

生成头像 ASCII 的脚本：

```python
from PIL import Image, ImageEnhance
import numpy as np

img = Image.open("your_image.png")
w, h = img.size
crop = img.crop((w//8, 0, w*7//8, h))

enhancer = ImageEnhance.Contrast(crop)
crop = enhancer.enhance(3.5)

arr = np.array(crop.convert("RGB"), dtype=np.float64) / 255.0
arr = np.power(arr, 0.55)  # gamma: lower = brighter
arr = (arr * 255).clip(0, 255).astype(np.uint8)
bright = Image.fromarray(arr)

width = 38
ratio = crop.height / crop.width
height_chars = int(ratio * width * 0.5)
height_pixels = height_chars * 2
img_resized = bright.resize((width, height_pixels))
img_rgb = img_resized.convert("RGB")

lines = []
for row in range(height_chars):
    parts = []
    y_top = row * 2
    y_bot = row * 2 + 1
    for x in range(width):
        r1, g1, b1 = img_rgb.getpixel((x, y_top))
        if y_bot < height_pixels:
            r2, g2, b2 = img_rgb.getpixel((x, y_bot))
        else:
            r2, g2, b2 = (0, 0, 0)
        def clean(v):
            return 0 if v < 10 else v
        r1, g1, b1 = clean(r1), clean(g1), clean(b1)
        r2, g2, b2 = clean(r2), clean(g2), clean(b2)
        fg = f"#{r1:02x}{g1:02x}{b1:02x}"
        bg = f"#{r2:02x}{g2:02x}{b2:02x}"
        parts.append(f'[{fg} on {bg}]▀[/]')
    lines.append("".join(parts))

for l in lines:
    print(f"  {l}")
```

## 截图

<!-- TODO: 添加截图 -->

## 配色参考

| 元素 | 颜色 | 用途 |
|------|------|------|
| `#58a6ff` | GitHub Blue | 标题、logo、选中项 |
| `#ff8c00` | Vibrant Orange | 警告、审批标题 |
| `#3fb950` | GitHub Green | 成功指示 |
| `#f85149` | GitHub Red | 错误指示 |
| `#bc8cff` | GitHub Purple | 小节标题 |
| `#c9d1d9` | GitHub Text | 正文 |
| `#6e7681` | GitHub Dim | 暗色文字 |
| `#30363d` | GitHub Border | 边框 |
| `#161b22` | GitHub Canvas | 状态栏背景 |

## License

MIT
