---
name: multimodal
description: >
  Multi-modal document processing — OCR, speech-to-text, text-to-speech, image structure
  analysis. Trigger on OCR image, extract text from PDF, recognize speech, convert
  document images, or analyze image layout. cloud API + local PaddleOCR CPU + pure .NET
  ImageAnalyzer.
---

# MultiModal — Document Intelligence

Three independent capabilities, loaded on demand:

- **Cloud OCR** → load [ocr-cloud.md](references/ocr-cloud.md)
- **Local OCR** → load [ocr-local.md](references/ocr-local.md)
- **ImageAnalyzer** → load [image-analyzer.md](references/image-analyzer.md)

## Dependencies

- .NET SDK 8.0+ (`dotnet` command available)
- NuGet: `Angri450.Nong.MultiModal`

If .NET SDK 8.0+ is missing, stop immediately and tell the user to install it. Do not attempt to fix.

## Dispatch Logic

1. User mentions "API", "cloud", "PaddleOCR-VL", "online" → **load ocr-cloud.md**
2. User mentions "local", "offline", "CPU", "without network" → **load ocr-local.md**
3. User mentions "Word", "docx", "document formatting" → **load ocr-cloud.md** (Word output comes from cloud API pipeline)
4. User mentions "analyze", "structure", "layout", "white space", "空白", "布局分析", "ASCII map" → **load image-analyzer.md**
5. Both OCR modes (Cloud + Local) → cloud first, local as fallback

## Core Operations

### Cloud OCR (PaddleOCR-VL-1.6 API)

```csharp
using MultiModalCore;

// Token read from PADDLEOCR_TOKEN environment variable
var client = new PaddleOcrVlClient();
var outDir = Path.Combine(
    Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments),
    "GroundPA Toolkit Workplace", "output",
    $"<timestamp>+<project>+<seq>");
Directory.CreateDirectory(outDir);

// One-shot OCR → Markdown
var mdFiles = await client.ProcessAsync("scan.pdf", outDir);

// One-shot OCR → Word (layout-preserving)
var docxPath = await client.ProcessToWordAsync("scan.pdf",
    Path.Combine(outDir, "result.docx"));
```

### Local OCR (PaddleOCR CPU)

```csharp
var local = new LocalOcrClient(pythonExe: "python", lang: "ch");
var (ok, msg) = await local.CheckEnvironmentAsync();
if (ok) {
    var blocks = await local.RecognizeAsync("crop.png");
}
```

### ImageAnalyzer (纯 .NET 图像结构分析)

```csharp
using MultiModalCore;

var analyzer = new ImageAnalyzer();

// 分析图像布局
var layout = analyzer.Analyze("image.png", targetWidth: 50);

// 打印 ASCII 文本地图（直观查看布局）
Console.WriteLine(layout.AsciiMap);

// 空白比例
Console.WriteLine($"Whitespace: {layout.WhitespaceRatio:P0}");

// 检测到的内容区域
foreach (var region in layout.Regions)
    Console.WriteLine($"[{region.Type}] at ({region.X},{region.Y}) {region.Width}x{region.Height}");
```

零额外依赖（基于 SkiaSharp，已在 ThirdParty 中合并）。无需 Python、PaddleOCR 或网络。适用场景：作图质量验证、OCR 预处理、布局调试。

## Workspace

First use: scaffold a console project under the standard workspace:

```powershell
dotnet new console -n OcrTask -o <target-dir> --force
dotnet add <target-dir> package Angri450.Nong.MultiModal
```

Suggest `~/Documents/GroundPA Toolkit Workplace/multimodal/OcrTask/` as the working directory. After setup, each session only modifies `Program.cs`.

Write a `Program.cs` with the task. For OCR: see [ocr-cloud.md](references/ocr-cloud.md). For image analysis: see [image-analyzer.md](references/image-analyzer.md).

Output goes to `~/Documents/GroundPA Toolkit Workplace/output/<timestamp>+<project>+<seq>/`.

## Token Configuration

Set the `PADDLEOCR_TOKEN` environment variable, or pass it to the constructor:

```powershell
$env:PADDLEOCR_TOKEN = "your-token"
```
