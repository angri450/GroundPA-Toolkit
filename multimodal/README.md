# MultiModal Skill Disabled in 2.0.0

`multimodal` is not exposed as a GroundPA skill in 2.0.0 because the current `nong` CLI only marks OCR commands as stubs.

Cloud OCR should return as a .NET/nong command when it is implemented and verified. Do not route users to Python PaddleOCR or ad hoc cloud OCR wrappers from this skill layer.
