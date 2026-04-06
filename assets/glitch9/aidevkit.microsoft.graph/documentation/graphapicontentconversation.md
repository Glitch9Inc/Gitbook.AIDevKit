# ================================================================================ Graph API Content F

## CORE CONCEPT

Graph API supports server-side format conversion via ?format=txt parameter. Infrastructure already implemented - only needs format detection at call site.

## AUTO-CONVERTIBLE FORMATS

Graph API converts these to plain text: • PDF, DOCX, XLSX, PPTX → txt

Native text formats (no conversion): • TXT, MD, HTML

## OCR-EXTRACTIBLE FORMATS (Requires Additional Implementation)

Images can be converted to text via OCR (Azure Computer Vision API): • JPG, PNG, BMP, WEBP → OCR text extraction • PDF with images → OCR for scanned documents • GIF (first frame), SVG (embedded text)

Implementation notes:

* Requires Azure Computer Vision API integration
* Best for screenshots, scanned docs, diagrams with text
* Can extract text from charts, slides, handwritten notes
* Not implemented yet - future enhancement

## NON-CONVERTIBLE FORMATS

These cannot be converted to text and must be excluded from RAG indexing: • Videos: MP4, AVI, MOV, WMV, MKV • Audio: MP3, WAV, AAC, FLAC • Archives: ZIP, RAR, 7Z, TAR, GZ • Binaries: EXE, DLL, SO, DMG • Proprietary: PSD, AI, FIG, BLEND • CAD/Design: DWG, SKP, MAX

For these formats:

* Download metadata only (filename, size, modified date, author)
* Skip content extraction
* Optionally index filename/path for search

## USAGE PATTERN

var request = new DownloadFileRequest { DriveId = driveId, ItemId = itemId, Format = fileExtension switch { ".pdf" or ".docx" or ".xlsx" or ".pptx" => "txt", ".txt" or ".md" or ".html" => null, // No conversion needed \_ => null // Skip binary/media files } };

## WHY THIS APPROACH

✓ Zero dependencies (no PDF/Office parsing libraries) ✓ Offloads parsing to Microsoft servers ✓ Microsoft's authoritative conversion for their formats ✓ Single code path for all convertible types

## REMAINING WORK

1. Add file extension detection before download
2. Set Format="txt" for convertible files
3. Skip or metadata-only for non-convertible files

\================================================================================
