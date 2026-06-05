# Local OCR Reference

Local OCR is a Nong CLI path through Nong's pure .NET PP-OCRv5 runtime:

```powershell
nong ocr local scan.png --json
```

It does not require Python, pip, or external OCR executables. Do not bypass Nong by calling OCR libraries directly from GroundPA.

## Required Gate

Run environment checks first:

```powershell
nong ocr check-env --json
nong ocr models --json
```

Expect `localDotNetPpOcrV5.status` to be `ok` and model entries to include `noPython: true`.

For deployment planning on a new client machine:

```powershell
nong ocr install-model pp-ocrv5-mobile --dry-run --json
```

This reports the .NET/NuGet deployment plan and domestic NuGet mirror options. Nong bundles the managed PP-OCRv5 model metadata; `install-model` deploys the heavy Windows x64 native runtime DLLs into the Nong runtime cache from a NuGet mirror or local NuGet cache.

## Smoke Test

Only describe local OCR as available after a real image command exits 0:

```powershell
nong ocr local scan.png --json
```

If it returns E005, report the missing .NET native runtime dependency and run `nong ocr install-model pp-ocrv5-mobile --source <domestic-nuget-v3-source> --json`; if the command is unavailable or returns E009, update/reinstall `Angri450.Nong.Cli` from a domestic NuGet mirror before retrying.

## GroundPA Boundary

- Do not install or invoke OCR through Python from GroundPA.
- Do not add alternate local OCR scripts.
- Do not translate dependency or not-implemented errors into success.
- Do not prefer local OCR over cloud OCR unless the local gate has passed in this environment.
