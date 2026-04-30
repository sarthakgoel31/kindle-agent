# Kindle Agent

**Send any document to your Kindle with one command. PDFs, transcripts, web articles -- cleaned and formatted for actual reading.**

## Why

Amazon's "Send to Kindle" flow is clunky: find the email address, compose a message, attach the file, hope the formatting is decent. Kindle Agent is a single command. It takes any file -- PDF, DOCX, plain text, raw transcript -- cleans it (strips page numbers, fixes hyphenation, detects headings), converts it to reflowable Kindle HTML, and emails it to your device. The reading experience is excellent because the file is processed before sending, not just forwarded as-is.

## How

1. Run `python3 send_to_kindle.py document.pdf`
2. The pipeline extracts text, cleans junk (page numbers, headers, decorative separators), detects structure (headings, lists, paragraphs), and converts to Kindle-optimized HTML
3. The processed file is sent via Gmail SMTP to your Kindle email
4. Document appears on your Kindle within minutes (WiFi required)

```bash
python3 tools/send_to_kindle.py paper.pdf                      # Process + send
python3 tools/send_to_kindle.py notes.txt --title "My Notes"   # With custom title
python3 tools/send_to_kindle.py native.epub --raw               # Skip processing
```

## Features

| Category | Feature | Detail |
|----------|---------|--------|
| Formats | PDF | Text extraction (pymupdf/pdfplumber), cleaned + structured HTML |
| Formats | DOCX / DOC | python-docx extraction, cleaned + structured HTML |
| Formats | Plain text (.txt) | Auto-converted to styled HTML with proper typography |
| Formats | HTML | Sent as-is (already Kindle-compatible) |
| Formats | MOBI / EPUB | Sent as-is (native Kindle formats) |
| Formats | Images (PNG/JPG/GIF) | Sent as-is |
| Formats | RTF | Calibre conversion if available, Amazon converts otherwise |
| Processing | Page number removal | Strips standalone `42`, `- 12 -`, `Page 5` patterns |
| Processing | Hyphenation fix | Rejoins `docu-\nment` across line breaks to `document` |
| Processing | Structure detection | ALL CAPS and numbered sections become headings; bullets become lists |
| Processing | Header/footer cleanup | Removes repeated decorative separators (`===`, `---`) |
| Premium | Calibre integration | If `ebook-convert` CLI is installed, produces highest-quality EPUB |
| Auth | macOS Keychain | Gmail App Password stored securely via `security` CLI |
| Options | `--title "Name"` | Set document title for Kindle library |
| Options | `--raw` | Skip all processing, send file exactly as-is |
| Options | `--no-convert` | Don't ask Amazon to convert the format |
| Safety | 50MB file size guard | Rejects files exceeding Kindle's limit |

## Tech

| Component | Technology |
|-----------|------------|
| Language | Python 3.10+ |
| Email delivery | Gmail SMTP (TLS, App Password auth) |
| PDF extraction | pymupdf (primary), pdfplumber (fallback) |
| DOCX extraction | python-docx |
| Premium conversion | Calibre `ebook-convert` CLI (optional) |
| Credential storage | macOS Keychain (`security` CLI) |
| Output format | Kindle-optimized HTML (Georgia font, 1.7 line height, semantic markup) |

## Architecture

```
kindle-agent/
├── kindle-agent.md               # Claude Code agent definition
├── tools/
│   └── send_to_kindle.py         # Processing + delivery pipeline (500 lines)
└── README.md                     # This file

Processing pipeline:
  Input file
    |
    +--> Format detection (.pdf / .docx / .txt / .html / .epub / etc.)
    |
    +--> [PDF/DOCX/RTF] Try Calibre ebook-convert --> EPUB
    |         |
    |         +--> Fallback: Python text extraction
    |                  |
    |                  +--> clean_extracted_text()  -- strip junk
    |                  +--> detect_structure()      -- headings, lists, paragraphs
    |                  +--> text_to_html()          -- Kindle-friendly HTML
    |
    +--> [TXT] Direct text_to_html() conversion
    |
    +--> [MOBI/EPUB/HTML/Images] Send as-is
    |
    +--> Gmail SMTP --> Kindle email address
    |
    +--> Auto-cleanup of temp files
```

## Setup

```bash
# Store Gmail App Password in macOS Keychain (one-time)
python3 tools/send_to_kindle.py --store-password 'xxxx xxxx xxxx xxxx'

# Optional: install extraction libraries for best results
pip install pymupdf       # PDF text extraction (preferred)
pip install pdfplumber    # PDF text extraction (fallback)
pip install python-docx   # DOCX text extraction
brew install calibre      # Premium ebook conversion
```

Ensure `sarthak.goel01@gmail.com` is added as an approved sender in your Amazon Kindle settings.

## Status

| Item | State |
|------|-------|
| PDF text extraction + cleaning | Live |
| DOCX text extraction | Live |
| TXT to styled HTML | Live |
| Calibre premium conversion | Live (optional) |
| macOS Keychain auth | Live |
| Structure detection (headings, lists) | Live |
| Hyphenation + page number cleanup | Live |
| 50MB file size guard | Live |
| Claude Code agent integration | Live |

---

Built by [Sarthak Goel](https://github.com/sarthakgoel31)
