# ImageAnalyzer — 纯 .NET 图像结构分析

零额外依赖（基于 SkiaSharp，已在 `Angri450.Nong.ThirdParty` 中合并）。无需 Python、无需 PaddleOCR、无需网络。

## 适用场景

1. **作图质量验证**：检查 Chart/Diagram 生成的图表是否包含留白过多、内容偏移等问题
2. **OCR 预处理**：识别文字区域坐标，为 OCR 引擎提供候选区域
3. **布局调试**：可视化查看图像中文字、图形、边框的空间分布

## 快速开始

```csharp
using MultiModalCore;

var analyzer = new ImageAnalyzer();
var layout = analyzer.Analyze("chart.png", targetWidth: 50);

// ASCII 文本地图 — 每个字符代表 targetWidth 像素宽的列
Console.WriteLine(layout.AsciiMap);

// 空白比例
Console.WriteLine($"Whitespace ratio: {layout.WhitespaceRatio:P0}");
```

## API 参考

### ImageAnalyzer

```csharp
public class ImageAnalyzer
{
    public ImageLayout Analyze(string filePath, int targetWidth = 50);
    public ImageLayout Analyze(byte[] imageBytes, int targetWidth = 50);
    public ImageLayout Analyze(SKBitmap bitmap, int targetWidth = 50);
}
```

`targetWidth` 控制 ASCII 地图的字符宽度（列数），不影响像素级分析精度。

### ImageLayout

| 属性 | 类型 | 说明 |
|------|------|------|
| `AsciiMap` | `string` | ASCII 文本地图（多行网格），每行对应图像的一行像素带，每个字符代表 `targetWidth` 像素宽的列 |
| `WhitespaceRatio` | `double` | 空白像素占图像总像素的比例 |
| `Regions` | `List<ContentRegion>` | 检测到的内容区域列表 |
| `ContentWidth` | `int` | 内容边界框宽度（px） |
| `ContentHeight` | `int` | 内容边界框高度（px） |
| `ContentMinX` | `int` | 内容边界框最小 X 坐标 |
| `ContentMinY` | `int` | 内容边界框最小 Y 坐标 |
| `BlackPixelCount` | `int` | 黑色像素（文字）数量 |
| `GraphicPixelCount` | `int` | 彩色像素（图形）数量 |
| `EdgePixelCount` | `int` | 暗灰色像素（边框/线条）数量 |

### ContentRegion

| 属性 | 类型 | 说明 |
|------|------|------|
| `X` | `int` | 区域左上角 X 坐标 |
| `Y` | `int` | 区域左上角 Y 坐标 |
| `Width` | `int` | 区域宽度（px） |
| `Height` | `int` | 区域高度（px） |
| `Type` | `RegionType` | 区域类型：`Text` / `Graphic` / `Edge` |
| `PixelCount` | `int` | 区域内像素数量 |

## 像素分类逻辑

分析器将每个像素分为四类（按表格顺序判定，命中即停止）：

| 分类 | 判断条件 | ASCII 字符 | 典型含义 |
|------|----------|-----------|---------|
| 白色（空白） | 亮度 > 240 | 空格 | 背景、留白 |
| 黑色（文字） | 亮度 < 40 | `#` | 文本、坐标轴标注 |
| 彩色（图形） | 饱和度 > 30 | `@` | 柱形图填充、折线 |
| 暗灰（边框） | 亮度 40–100 | `.` | 坐标轴、网格线 |

未命中以上四类的像素（如亮度 101–240 且饱和度 ≤ 30 的浅灰区域）归为空白处理。

## 内容区域检测

使用泛洪填充算法检测连通的内容区域（非空白像素）。每个连通区域作为一个 `ContentRegion`，按像素优势分类判断其类型。

## 完整示例

```csharp
using MultiModalCore;

var analyzer = new ImageAnalyzer();
var layout = analyzer.Analyze("output.png", targetWidth: 60);

// 1. 查看 ASCII 地图
Console.WriteLine("=== ASCII Map ===");
Console.WriteLine(layout.AsciiMap);

// 2. 空白检查
Console.WriteLine($"\nWhitespace: {layout.WhitespaceRatio:P0}");
if (layout.WhitespaceRatio > 0.80)
    Console.WriteLine("WARNING: 图像空白比例过高，可能存在布局问题");

// 3. 内容区域详情
Console.WriteLine($"\nContent bounding box: ({layout.ContentMinX},{layout.ContentMinY}) → " +
    $"({layout.ContentMinX + layout.ContentWidth},{layout.ContentMinY + layout.ContentHeight})");

Console.WriteLine($"\nDetected {layout.Regions.Count} content regions:");
foreach (var region in layout.Regions)
{
    Console.WriteLine($"  [{region.Type}] at ({region.X},{region.Y}) " +
        $"{region.Width}x{region.Height}, pixels={region.PixelCount}");
}

// 4. 像素统计
Console.WriteLine($"\nPixels: text={layout.BlackPixelCount} " +
    $"graphic={layout.GraphicPixelCount} edge={layout.EdgePixelCount}");
```
