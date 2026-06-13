# Multimodal Skill

`multimodal` is exposed as a GroundPA skill for Nong OCR and image-structure QA commands.

Use `nong ocr analyze-image` for structure/blank/whitespace/content-region QA. Use `nong ocr cloud` and `nong ocr to-word` only when `PADDLEOCR_ACCESS_TOKEN` is available from `https://aistudio.baidu.com/account/accessToken`. Treat `ocr local` as stable only after `localDotNetPpOcrV5.status=ok` and a real image smoke test pass; dependency failures return E005.

Do not route GroundPA through Python OCR libraries or custom OCR wrappers.
