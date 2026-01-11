# EPUB Fixer & Translator

A standalone Python tool to fix and translate Chinese EPUBs for reading on Google Play Books (or any e-reader).

**No Calibre required!** Uses proper lxml-based XHTML parsing like Calibre does internally.

```bash
python fixTranslate.py novel.epub
```

## What It Does

### EPUB Validation & Repair (like Calibre)

Uses **lxml** for proper XML/XHTML parsing and serialization:

- **Proper XHTML namespace handling** - Forces all content into correct XHTML namespace
- **Structure fixes** - Ensures `<head>`, `<body>`, `<title>` elements exist
- **Meta charset** - Adds proper `Content-Type: text/html; charset=utf-8`
- **Removes invalid elements** - `<script>`, `<embed>`, `<object>`, `<form>`, etc.
- **Converts deprecated tags** - `<center>` → `<div style="text-align:center">`, `<u>` → `<span>`, etc.
- **Removes empty tags** - Empty `<a>`, `<i>`, `<b>`, `<u>`, `<span>` without IDs
- **Fixes self-closing tags** - `<div/>` → `<div></div>` (critical for e-readers!)
- **Removes duplicate IDs** - Prevents EPUB validation errors
- **Cleans invisible characters** - Zero-width spaces, soft hyphens, etc.
- **Removes watermarks** - Chinese novel site ads and watermarks
- **Proper EPUB packaging** - `mimetype` first and uncompressed, NFC Unicode normalization

### Translation

- **Concurrent translation** - Up to 100 parallel requests (same speed as Calibre plugin)
- Same Google Translate API as [Calibre Ebook Translator plugin](https://github.com/bookfere/Ebook-Translator-Calibre-Plugin)
- Automatic retry with exponential backoff
- In-memory caching

## Requirements

- Python 3.7+
- `lxml` - For proper XML/XHTML parsing
- `requests` - For translation API

## Installation

```bash
pip install lxml requests
```

Download `fixTranslate.py` and you're ready to go.

## Usage

### Basic Usage

```bash
python fixTranslate.py novel.epub
```

Output: `novel_translated.epub`

### Fix Only (No Translation)

```bash
python fixTranslate.py novel.epub --no-translate
```

Output: `novel_fixed.epub`

### Custom Output

```bash
python fixTranslate.py novel.epub -o "My Book.epub"
```

### Control Concurrency

```bash
# Limit to 50 concurrent requests
python fixTranslate.py novel.epub --workers 50

# Add delay between requests
python fixTranslate.py novel.epub --interval 0.1
```

## Options

| Option | Description |
|--------|-------------|
| `-o, --output` | Custom output filename |
| `--no-translate` | Skip translation (fix only) |
| `--workers N` | Max concurrent requests (0=auto, up to 100) |
| `--interval SEC` | Delay between requests |
| `--add-watermark PAT` | Add custom watermark regex |
| `-q, --quiet` | Suppress output |

## Why This Works

Google Play Books and other e-readers are strict about EPUB validation. Common issues:

1. **Self-closing tags**: `<div/>` is valid XML but renders incorrectly in browsers/e-readers. Must be `<div></div>`.

2. **Missing structure**: EPUBs need proper `<head>`, `<body>`, `<title>`, and meta charset.

3. **Invalid elements**: `<script>`, `<form>`, etc. cause processing failures.

4. **Duplicate IDs**: Multiple elements with same `id` attribute fail validation.

5. **Invisible characters**: Zero-width spaces and soft hyphens cause rendering issues.

This script fixes all of these using the same lxml-based approach that Calibre uses internally.

## Troubleshooting

### Still getting "Processing failed" in Google Play Books?

The source EPUB may have deeper issues. Try:
1. Check the console output for parsing errors
2. Use `--no-translate` to isolate if it's a translation issue
3. Report the issue with details about the source

### Translation errors?

- Reduce workers: `--workers 20`
- Add delay: `--interval 0.1`
- Check internet connection

## Credits

- **XHTML processing logic** based on [Calibre](https://github.com/kovidgoyal/calibre) by Kovid Goyal (GPL-3.0)
- **Translation API** based on [Ebook Translator Calibre Plugin](https://github.com/bookfere/Ebook-Translator-Calibre-Plugin) by bookfere (GPL-3.0)
- **WebToEpub** for downloading web novels - https://github.com/dteviot/WebToEpub

### 🤖 AI-Assisted Development

This script was built with **Claude AI** (Anthropic) based on design, requirements, and ideas by **Joel**. The development involved iterative collaboration where Joel provided the use case, tested outputs, and guided implementation while Claude analyzed Calibre's source code and wrote the solution.

## License

MIT License
