# Fortune Capital — PL Document Generator (static webpage)

100% client-side. No backend, no server, no database. Fills `template.docx`
in the visitor's browser and lets them preview, download Word, download PDF.

## Files
- `index.html` — the whole app (form + engine + preview)
- `template.docx` — master PL template (run-merged so `{{ field }}` tags are
  intact in single XML text nodes — do not re-save this file from Word,
  it will re-fragment the runs)
- `vendor/` — the JS libraries the app needs, bundled locally (not loaded
  from a CDN, so there's nothing to break or go offline): pizzip,
  docxtemplater, jszip, docx-preview, html2pdf.bundle, FileSaver.
  All MIT-licensed, pulled straight from their npm packages.

## Deploy to GitHub Pages

```bash
git init
git add index.html template.docx README.md vendor/
git commit -m "Fortune Capital PL generator"
git branch -M main
git remote add origin https://github.com/<you>/<repo>.git
git push -u origin main
```

Then: repo **Settings → Pages → Source: main branch, root** → Save.
Live at `https://<you>.github.io/<repo>/`.

No build step. No `npm install`. Just static files.

## How it works
- `template.docx` is fetched at runtime and filled with **docxtemplater**
  (`{{ }}` delimiters), which edits the DOCX's own XML — same placeholder
  engine logic as the original Python script, just running in-browser.
- Preview renders via **docx-preview**.
- "Download Word" saves the actual generated `.docx` (byte-accurate, opens
  in Microsoft Word, fully editable).
- "Download PDF" rasterizes the rendered preview via **html2pdf.js**. This
  is a screenshot-based PDF, not a native DOCX→PDF conversion (browsers
  can't run LibreOffice) — layout is close but not guaranteed
  pixel-identical. For a guaranteed-exact PDF, open the downloaded Word
  file and use Word's own "Export to PDF."

## Editing the template
Only edit `template.docx` in Word if you then re-run it through a run-merge
step before replacing this file — otherwise Word will split `{{ name }}`
across multiple runs and the browser engine won't find it. Safer: ask
Claude (or re-run `merge_runs.py` from the docx skill) on any new version
before dropping it in here.

## Privacy
Nothing leaves the browser. No upload, no analytics, no server logging —
appropriate for Aadhaar/KYC data since Version 1 has no OCR step (manual
entry only). If OCR is added later, keep it client-side or self-hosted to
preserve this property.
