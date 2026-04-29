# Kindle Agent

**Send anything to your Kindle from the command line -- documents, transcripts, raw text, PDFs -- formatted for actual reading.**

## What It Does

This Claude Code agent takes any document or text content and delivers it to your Kindle in a clean, reflowable format. It does not just email a file attachment. It extracts text, strips junk (page numbers, headers, hyphenation artifacts), detects structure (headings, lists, paragraphs), converts everything to reflowable HTML, and sends it via Gmail SMTP to your Kindle email address. The result is content that reflows properly on any Kindle screen size, not a fixed-layout PDF that requires constant zooming.

## Capabilities

- **PDF to readable Kindle** -- Extracts text from PDFs using pymupdf or pdfplumber, cleans it up, and converts to reflowable HTML. No more pinch-zooming through academic papers on a Kindle.
- **DOCX, RTF, TXT support** -- Handles Word documents, rich text, and plain text with automatic structure detection and clean formatting.
- **Raw text and transcripts** -- Paste any text content and it gets formatted into a properly structured HTML document with Georgia font, good line height, and semantic headings.
- **MOBI, EPUB, HTML passthrough** -- Native Kindle formats are sent as-is without unnecessary conversion.
- **Smart text cleaning** -- Strips page numbers, headers/footers, and decorative separators. Fixes hyphenated line breaks (`docu-\nment` becomes `document`). Detects ALL CAPS headings and numbered sections.
- **Calibre integration** -- If Calibre CLI (`ebook-convert`) is installed, uses it for highest-quality EPUB conversion. Falls back gracefully to Python-based extraction.
- **Secure credential storage** -- Gmail App Password stored in macOS Keychain, not in config files.

## How to Use

1. Copy `kindle-agent.md` into your `.claude/agents/` directory.
2. Copy `tools/send_to_kindle.py` into `.claude/agents/tools/`.
3. On first use, the agent will walk you through Gmail App Password setup and Kindle approved sender configuration.

Then in Claude Code:

```
"Send this PDF to my Kindle: /path/to/paper.pdf"
"Send this transcript to my Kindle"
"Format this text for Kindle and send it"
```

## Architecture

```
User input (file path, raw text, or URL content)
    |
    v
[kindle-agent.md]  -- Claude Code agent, determines input type
    |
    v
[send_to_kindle.py]  -- processing + delivery tool
    |
    |-- Input detection (PDF / DOCX / TXT / raw text / native format)
    |-- Text extraction (pymupdf, pdfplumber, python-docx)
    |-- Cleaning pipeline (strip junk, fix hyphenation, detect structure)
    |-- HTML conversion (reflowable, serif font, proper typography)
    |-- Calibre path (optional, highest quality EPUB)
    |
    v
Gmail SMTP --> Kindle email address
```

The `send_to_kindle.py` tool handles the entire processing pipeline: format detection, text extraction, cleaning, HTML conversion, and email delivery. The agent orchestrates when to use it and handles edge cases like missing files or first-time setup.

**Options:**
- `--title "Document Name"` -- Set the document title on Kindle
- `--no-convert` -- Skip Amazon's server-side format conversion
- `--raw` -- Skip all processing, send the file exactly as-is

## Requirements

- Python 3.10+
- Gmail account with App Password (2FA required)
- Kindle email address added to Amazon approved senders

**Optional (for best quality):**
- `pip install pymupdf` -- PDF text extraction
- `pip install pdfplumber` -- PDF fallback
- `pip install python-docx` -- DOCX support
- `brew install calibre` -- Premium ebook conversion

## Built with Claude Code
