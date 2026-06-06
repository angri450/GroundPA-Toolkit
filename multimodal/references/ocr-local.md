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

This reports the .NET/NuGet deployment plan. Nong bundles the managed PP-OCRv5 model metadata; `install-model` deploys the current platform `Angri450.Nong.OcrRuntime.*` native runtime bundle into the Nong runtime cache from the Huawei NuGet source or a local NuGet cache. Upstream Sdcb/OpenCvSharp fallback is disabled by default and requires explicit `--allow-upstream-fallback`. A successful non-dry-run install/check cleans the temporary `runtimeCache\downloads` directory and reports `downloadCleanup`.

Preferred install command:

```powershell
nong ocr install-model pp-ocrv5-mobile --source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json --json
```

## Smoke Test

Only describe local OCR as available after a real image command exits 0:

```powershell
nong ocr local scan.png --json
```

If it returns E005, report the missing .NET native runtime dependency and run the Huawei-source `install-model` command above. If the first-party bundle is unavailable, report a NuGet publish/mirror-sync issue instead of silently falling back. Use `--allow-upstream-fallback` only when the user explicitly accepts downloading upstream Sdcb/OpenCvSharp native packages. If the command is unavailable or returns E009, update/reinstall `Angri450.Nong.Cli` from the Huawei NuGet source before retrying.

## GroundPA Boundary

- Do not install or invoke OCR through Python from GroundPA.
- Do not add alternate local OCR scripts.
- Do not translate dependency or not-implemented errors into success.
- Do not prefer local OCR over cloud OCR unless the local gate has passed in this environment.
