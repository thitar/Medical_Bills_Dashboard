# Medical Bill Tracker

A simple dashboard to track medical bills through the CNS and DKV reimbursement process. It connects to Paperless-NGX and shows you exactly where each bill is in the pipeline.

## What does this do?

When you go to the doctor in Luxembourg, getting your money back is a multi-step process involving two insurers: **CNS** (government) and **DKV** (private). This dashboard shows you **which documents need action** and **what to do next** — all in one screen, plus a live search, a reconciliation audit, and automatic cleanup of old completed bills.

## The workflow

There are five document types, each with its own path through the pipeline.

### Classic bills (`type:classic`)

You paid the full amount at the doctor and submit it to CNS yourself.

```
You paid → Send to CNS → Wait for CNS → CNS report received → Done ✓
```

The bill's job ends once CNS's report arrives — **the report itself is a
separate document** (see "CNS Reports" below), and it's the report — not
the original bill — that gets forwarded to DKV. This matters because a
single CNS report often groups several bills together, so DKV tracking
only ever needs to happen once, on the report, not per bill.

### PID bills (`type:pid`, Paiement Immédiat Direct)

You only paid the residual amount. CNS pays the doctor directly — you
never submit anything to CNS yourself, and CNS never sends *you* money
for a PID bill either. You just wait for their report.

```
You paid residual → Wait for CNS report → Done ✓
```

Same principle as Classic: once the report arrives, this bill's part is
done. The DKV forwarding happens on the report document, not this one.

### Non-CNS bills (`type:non-cns`)

Bills that CNS doesn't cover (alternative medicine, contact lenses, etc.).
These go directly to DKV — there's no CNS report to wait for.

```
You paid → Send to DKV → Wait for DKV → DKV reimbursed → Done ✓
```

### CNS Reports (`type:cns-report`)

The reimbursement statements CNS sends back, covering one or more Classic
or PID bills. This is the document that actually gets forwarded to DKV.

```
Report received → Send to DKV → Wait for DKV → DKV reimbursed → Done ✓
```

### DKV Reports (`type:dkv-report`)

DKV's own final reimbursement statement — nothing left to send anywhere,
just a quick review.

```
DKV reimbursed (on arrival) → Done ✓
```

## Understanding the dashboard

### Summary cards (top of the page)

Each card is clickable — it narrows the pipeline below to just the
matching column(s). Click the same card again (or "Total Bills") to
clear the filter.

| Card | What it means |
|------|--------------|
| **Total Bills** | How many documents are being tracked in total |
| **Action Needed** | Documents that need you to send something (to CNS or DKV) |
| **With CNS** | Documents currently with CNS (sent or waiting) |
| **With DKV** | Documents currently with DKV (sent or waiting) |
| **Complete** | Documents fully processed in the last 15 days — nothing left to do |

### Search

The search box filters the pipeline live as you type. It matches, in
order: the Paperless document ID, the title, the correspondent, and the
full OCR'd content of the document — with a highlighted snippet shown
when the match comes from inside the document text.

### Pipeline columns

A kanban board with one column per workflow stage:

1. **CNS — To Send** → submit this to CNS
2. **CNS — Pending** → sent (or automatic for PID), waiting for CNS's report
3. **DKV — To Send** → ready to scan/photo in the DKV app
4. **DKV — Pending** → sent to DKV, waiting for their response
5. **DKV — Reimbursed** → DKV has reimbursed
6. **Complete ✓ (last 15 days)** → fully done; older completions age out of view automatically (see "Completed Date" below) so this column doesn't grow forever

### Document cards

Each card shows the Paperless document ID, title, correspondent (if set),
type badge (Classic/PID/Non-CNS/CNS Report/DKV Report), creation date,
and a **→ button** to advance it to the next step. Click anywhere else on
the card to open the original document in Paperless-NGX.

A card in the Complete column with no **Completed Date** shows an amber
**"⚠ No Completed Date set"** warning instead of being hidden — that's
your signal to check it (usually an older document from before this
field existed, or a one-off write failure).

### Audit panel

Below the pipeline, a reconciliation check against the **Related
Document** custom field, across four categories:

- CNS Reports
- Non-CNS bills
- DKV Reports
- Classic/PID bills already marked Complete

Each row shows a green **"✓ all linked"**, or an amber count with a
clickable chip per document that's missing a link — click a chip to open
that document directly in Paperless. This is informational only; it
never blocks the → advance button.

## Tags in Paperless-NGX

Documents need a **type tag** and a **status tag**:

**Type tags:**
- `type:classic`
- `type:pid`
- `type:non-cns`
- `type:cns-report`
- `type:dkv-report`

**Status tags:**
- `status:cns-to-send`
- `status:cns-pending`
- `status:dkv-to-send`
- `status:dkv-pending`
- `status:dkv-reimbursed`
- `status:complete`

The Tag Reference section at the bottom of the dashboard shows which tags
(and custom fields) exist, and warns with ⚠ if any are missing.

## Custom fields in Paperless-NGX

| Field name | Type | Purpose |
|---|---|---|
| `Completed Date` | Date | Auto-written when a document is advanced to Complete. Drives the 15-day retention on the Complete column. |
| `Related Document` | Document Link | You set this manually to link a CNS/DKV report back to the bill(s) it covers (or a non-CNS bill to whatever it should point at). The Audit panel checks this field. |

## Quick reference: what to do when...

| Situation | Action |
|-----------|--------|
| New bill from the doctor (you paid full price) | Scan it, tag as `type:classic` + `status:cns-to-send` |
| New bill from the doctor (PID, you paid residual only) | Scan it, tag as `type:pid` + `status:cns-pending` |
| New bill not covered by CNS | Scan it, tag as `type:non-cns` + `status:dkv-to-send` |
| You mailed a bill to CNS | Click **→ cns-pending** on the dashboard |
| CNS reimbursement report arrives | Scan it, tag as `type:cns-report` + `status:dkv-to-send`, link it to the bill(s) it covers via `Related Document`, then click **→ complete** on the original bill(s) |
| You scanned a bill/report in the DKV app | Click **→ dkv-pending** on the dashboard |
| Money from DKV appeared on the bank account | Click **→ dkv-reimbursed** on the dashboard |
| DKV's own final statement arrives | Scan it, tag as `type:dkv-report` + `status:dkv-reimbursed`, then click **→ complete** |
| A document is fully processed | Click **→ complete** on the dashboard |
=======
# Medical_Bills_Dashboard
