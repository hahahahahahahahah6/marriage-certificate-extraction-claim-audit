# Documentation claim auditor

An offline static auditor comparing documentation promises about the checked feature with Python source patterns. It reads root README.md and Markdown files with a docs path component. Source is parsed, never executed. No network or telemetry is used.

## Usage

```sh
marriage-certificate-extraction-claim-audit audit . --json
python3 -m marriage_certificate_extraction_claim_audit audit . --verbose
marriage-certificate-extraction-claim-audit audit . --fail-on medium --ignore vendor --ignore generated
marriage-certificate-extraction-claim-audit --version
```

The audit subcommand accepts an optional repository directory, defaulting to the current directory. Ignore options each take one relative path prefix. The default failure threshold is high; medium includes test-only evidence failures. Exit status is 0 for PASS, 2 for findings meeting the threshold, and 1 for usage or I/O errors. JSON includes verdict, findings with rule, severity, message and locations, production_evidence, and test_evidence. PASS can still contain a medium finding below the selected threshold.

## Matching

Paragraphs are separated by blank lines. Fences using backticks or tildes with up to three leading spaces are excluded; inline code counts. Matching lowercases text, expands contractions, replaces hyphens with spaces, and collapses whitespace. Exact phrases use word boundaries, longest first with alphabetical ties. Denial spans are removed before positive matching. One finding is emitted per family.

Only module-local listed calls count. Import aliases are resolved; local definitions and assignment or context-manager bindings shadow imports. Attribute assignments preserve imported names. Syntax-invalid files provide no evidence. Symlinked files and directories are skipped. Fixtures, docs and examples provide no source evidence; tests are tracked separately. A positive statement without source fails, while a denial without source passes.

```text
documented-marriage-certificate-extraction-claim-false HIGH (or MEDIUM with test-only evidence)
documented-no-marriage-certificate-extraction-claim-false HIGH
```

## Differentiation

The checked capability concerns structured spouse, document, issue and marriage fields. The invoice / receipt / id-document / bank-check / bank-statement / tax-form / pay-stub / health-insurance-card / business-card / credit-card / contract / mortgage / resume / bill-of-lading / delivery-note / carte-grise / energy-bill extraction families check other document types. OCR checks raw text recognition; table extraction checks table structure. Neither establishes the checked operation.

## Honest limitations

Static patterns are not semantic proof. Dead code counts, unresolved alias chains are missed, credentials and API success are not verified, and evidence cannot be combined across modules. Exact wording and the denial vocabulary limit coverage. The detailed matching hazards below describe false positives, unsupported vendors and missed wording. Quoted vocabulary is fenced so the auditor's own documentation does not assert the checked capability.

```text
- The bare phrases "marriage certificate extraction" / "extract
  marriage certificate" / "extract marriage certificates" are
  deliberately MARRIAGECERTIFICATEEXTRACTION phrases: in
  document-tool docs they almost always name marriage certificate
  extraction. But "marriage certificate" can also name a
  certificate the tool merely stores, displays, or manages (a
  marriage certificate archive, a marriage certificate template
  library) without extracting fields from a marriage certificate
  document: a doc that means storing, displaying, or templating
  marriage certificates, not extracting fields from them, is a
  false positive; the tool flags the claim, only the code decides.
- "marriage cert extraction" / "extract marriage cert(s)" /
  "marriage cert parsing" / "marriage cert parser" are deliberately
  phrases: "marriage cert" is the common abbreviation for a
  marriage certificate. But "cert" is also an abbreviation for
  other certificates: a doc that means extracting a different
  certificate (a birth cert, a death cert) is not caught, because
  the phrases require the word "marriage"; the tool flags the
  claim, only the code decides.
- "marriage certificate data extraction" / "extract marriage
  certificate data" / "marriage certificate data parsing" /
  "marriage certificate data parser" are deliberately phrases: a
  doc can say "marriage certificate data" about a tool that only
  stores marriage certificate data typed in by a user, or a
  "marriage certificate data parser" that is really a keyword
  highlighter over plain text. The tool flags the claim; only the
  code decides.
- "marriage certificate scanning" / "scan marriage certificate(s)"
  are deliberately phrases: "scanning" a marriage certificate can
  mean merely photographing it or OCRing it into raw text, with no
  structured field extraction. The tool flags the claim; only the
  code decides.
- "marriage license extraction" / "extract marriage licenses" /
  "marriage license parsing" / "marriage license parser" claims
  are deliberately NOT in the phrase lists: a marriage license is
  a different document from a marriage certificate (a license is
  permission to marry; a certificate proves the marriage
  happened). A doc that claims marriage license extraction is
  missed. Say so in the README.
- "marriage certificate verification" / "proof of identity
  extraction" claims are NOT in the phrase lists: verifying a
  marriage certificate as proof of identity or address is a
  different operation from extracting fields from it. Say so in
  the README.
- The French phrase "acte de mariage" (and its extraction /
  parsing / scanning variants) is deliberately NOT in the phrase
  lists: the matcher does exact phrases after a normalisation
  that does not strip apostrophes, so a French apostrophe phrase
  would match unreliably across docs that write it with a straight
  quote, a curly quote, or no apostrophe at all. A doc that claims
  extraction using only the French name is missed; the English
  "marriage certificate" and "marriage cert" phrases are the
  checked ones. Say so in the README.
- prebuilt-marriageCertificate.us carries the field set listed in
  the azure names section above (spouse 1 and spouse 2 name / age
  / birth date / address / birth place, document number, issue
  date / place, marriage date / place): it has NO officiant, NO
  witness, and NO seal / signature fields. A tool that extracts
  those does so through some other route; this family checks for
  the begin_analyze_document call with the marriage certificate
  model id, not for those fields. Say so in the README.
- prebuilt-marriageCertificate.us is English-language US documents
  only (en-US). A tool that extracts marriage certificates from
  other countries or languages does so through some other route.
  Say so in the README.
- A generic `extract_marriage_certificate(` /
  `parse_marriage_certificate(` call can name a string parser for
  text the user typed in rather than a document extractor: the tool
  counts the call, the claim plus the code decide — and a
  subprocess call invoking an external CLI is NOT evidence by
  design, so a thin wrapper that only calls subprocess fails.
- Azure `begin_analyze_document` with model id
  "prebuilt-marriageCertificate.us" counts as evidence: the model
  exists in Document Intelligence v4.0 (GA). The tool detects the
  call in source, not whether the call succeeds against a current
  API version. Say so in the README.
- Azure `begin_analyze_document` with any other model id is NOT
  detected: those are other document types. Say so in the README.
- Mindee `parse` with ANY product is NOT detected: mindee has no
  marriage certificate product (4.22.0 wheel inspected 2026-11-14
  — zero "marriage" hits in any .py file). Say so in the README.
- Veryfi calls of ANY kind are NOT detected: veryfi has no
  marriage certificate method (5.1.0 wheel inspected 2026-11-14 —
  zero "marriage" and zero "certificate" hits in any .py file),
  and its document APIs post to a generic documents endpoint shared
  across document types. Say so in the README.
- Google Document AI `process_document` / `batch_process_documents`
  with ANY processor is NOT detected: Document AI has no marriage
  certificate processor (the processors list page contains zero
  hits for "Marriage", verified 2026-11-14). Say so in the README.
- Eden AI universal-ai calls with ANY model id are NOT detected:
  Eden AI documents no marriage certificate parser model. Say so
  in the README.
- Affinda API calls are NOT detected: the marriage certificate
  parser is reached through a user-configured collection /
  workspace, so there is no fixed static fingerprint naming
  marriage certificates in Python source. Say so in the README.
- PDF4me AI-Marriage Certificate Parser calls are NOT detected:
  it is a Zapier / Make / n8n module, not a Python call. Say so
  in the README.
- LlamaParse calls are NOT detected: its parsing API is
  generative with no fixed static fingerprint naming marriage
  certificates. Say so in the README.
- Nanonets / Docsumo / ABBYY Vantage / Rossum / Klippa / Parseur /
  Extend calls are NOT detected: opaque model / skill / queue /
  template / workflow IDs, or a generic upload endpoint with the
  document type configured outside the call — no fixed fingerprint.
  Say so in the README.
- Textract `analyze_document` / `analyze_expense` / `analyze_id` /
  `start_document_analysis` / `get_expense_analysis` are NOT
  detected: generic document, expense, and identity analysis are
  not marriage certificate extraction, and Textract has no marriage
  certificate operation (AnalyzeID covers driver's licenses and
  passports). Say so in the README.
- A hand-rolled extraction from OCR text (`re.search` /
  `re.findall` on extracted text plus manual field assembly, no
  listed call) is NOT detected: the tool requires a listed call.
  Say so in the README.
- The Azure CLI and any other external marriage certificate tool
  invoked through subprocess are NOT detected: subprocess calls
  invoking an external CLI are NOT evidence by design. Say so in
  the README.
- Azure Document Intelligence, Mindee, and Veryfi are cloud APIs:
  the tool detects the client call in source, not whether
  credentials are configured or the call succeeds. Say so in the
  README.
- "extract marriage certificate text" / "extract text from marriage
  certificate" claims are NOT in the phrase lists: extracting plain
  text from a marriage certificate is text extraction, not marriage
  certificate field extraction. Say so in the README.
- "marriage certificate template" / "marriage certificate form"
  claims are NOT in the phrase lists: templating or form-filling a
  marriage certificate is creating one, not extracting fields from
  one. Say so in the README.
- "marriage certificate tracking" / "marriage certificate
  verification" claims are NOT in the phrase lists: tracking or
  verifying a marriage certificate is downstream of extraction, not
  extraction itself. Say so in the README.
- "marriage license extraction" / "extract marriage licenses" /
  "marriage license parsing" claims are NOT in the phrase lists:
  a marriage license is a different document from a marriage
  certificate. Say so in the README.
- Invoice extraction claims ("extract invoices", "invoice
  extraction"), receipt extraction claims ("extract receipts",
  "receipt extraction"), ID document extraction claims ("extract
  passports", "passport extraction"), bank check extraction claims
  ("extract bank checks", "check extraction"), bank statement
  extraction claims ("extract bank statements", "statement
  extraction"), tax form extraction claims ("extract tax forms",
  "extract w2 forms", "1099 extraction"), pay stub extraction
  claims ("extract pay stubs", "payslip extraction"), health
  insurance card extraction claims ("extract health insurance
  cards", "health insurance card extraction"), business card
  extraction claims ("extract business cards", "business card
  extraction"), credit card extraction claims ("extract credit
  cards", "credit card extraction"), contract extraction claims
  ("extract contracts", "contract extraction"), mortgage
  extraction claims ("extract mortgages", "mortgage extraction"),
  resume extraction claims ("extract resumes", "resume
  extraction"), bill of lading extraction claims ("extract
  bills of lading", "bill of lading extraction"), delivery
  note extraction claims ("extract delivery notes", "delivery
  note extraction"), carte grise extraction claims ("extract
  carte grises", "carte grise extraction"), and energy bill
  extraction claims ("extract energy bills", "energy bill
  extraction") are NOT in the phrase lists: this family covers
  marriage certificates only. Say so in the README.
- As with every family: static pattern matching, not semantic
  judgement; dead code counts; alias chains the tracer cannot follow
  are missed; exact phrases only, not semantics; denial handling
  depends on the denial phrase list; each route must sit in a single
  module.

```
