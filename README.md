# Elevated Drawing Review

Private structural PDF extraction and interactive source review. The production audience is owner-only through Sites access control.

## Use

Upload one or more PDFs, then review Strengths, Lap lengths, or Covers. Each item opens a linked source crop and full PDF page. FO and schedule values have independent labels; the source filter separates them. Verify, edit, dismiss, or manually add a missed item. A lap schedule is retained as rows with its original image rather than assigning a table lookup strength to an element. All items start unverified.

CSV exports contain the active extraction, source, document type, page, method, review status, original text and table rows. **Save review** downloads JSON with edits, statuses, coverage and references. Re-upload the matching PDFs and use **Restore** to resume. This workspace is kept in the current browser tab; PDFs are not stored on a server.

**Try example drawing** opens an explicitly fictional three-page PDF containing notes, a foundation lap table, and an image-only scanned page. No real project drawing is bundled in the deployment.

## Extraction

PDF.js 5.4.296 reads native PDF text and page transforms. The extractor assembles spatial rows, recognizes concrete sections, preserves written numbers and units, separates non-concrete material strengths, associates element and floor labels, reads cover lists including column continuation, and keeps lap schedules with their headers and numeric rows. Repeated items retain all page references. Distinct documents and differing values remain separate.

Tesseract.js 6.0.1 with locally hosted English data handles low-text pages automatically. **OCR this page** handles image annotations or poor PDF text layers on demand. All PDF, font, OCR worker, WASM and language assets are served from this same private site. Document bytes and extracted content never go to a remote extraction API.

## Validation on 2026-11-10

Six real PDFs / 131 pages / three projects:

- 202 E 120th St foundation and superstructure (10 + 33 pages).
- 608 Union St foundation and superstructure (15 + 38 pages).
- 114 Bruckner Blvd foundation and superstructure (11 + 24 pages).

88 selected reference checks passed: assigned concrete values, parent element and floor labels, all seven general cover rows in each PDF, lap schedule #3 and #11 endpoints, and concrete versus non-concrete material separation. These are targeted checks, not an exhaustive precision/recall score. `tests/validation-summary.json` records the results. Source PDFs stay outside this repository.

Eight portable regression tests cover alternative units, adjacent columns, cross-column cover continuation, FO/structural differences, table headings, stacked fractions, and direct cover/lap callouts. Run `node --test tests/extractor.test.mjs`. Browser QA also exercises the fictional PDF's text and image-only pages and the review controls.

## Practical limits

No extractor can establish completeness for arbitrary drawings. Unusual schedules, graphical fractions, very small scans, handwriting, and missing units require source review. The page coverage panel shows unreadable, failed and unprocessed pages. OCR text remains marked as OCR and unverified; it can contain recognition errors. Values are transcribed rather than calculated, converted, selected for design, or reconciled between conflicting documents. Password-protected PDFs require an unlocked copy. Maximum input size is 150 MB per PDF; large scanned sets depend on the browser's available memory.

## Development

The UI uses React with the provided Vinext/Vite integration. `public/extractor.mjs` is the independent extraction engine, `app/page.tsx` manages local documents and review state, and `public/load-libs.mjs` initializes same-origin PDF libraries. `scripts/create-example.py` reproduces the fictional example using ReportLab and Pillow. No runtime secrets or external model keys are required.

Run the provided dependency and build helpers; `npx tsc --noEmit` checks UI types. Sites deployment uses the project ID in `.openai/hosting.json` and must preserve its private audience.
