# Medical Bill Tracker

A simple dashboard to track medical bills through the CNS and DKV reimbursement process. It connects to Paperless-NGX and shows you exactly where each bill is in the pipeline.

## What does this do?

When you go to the doctor in Luxembourg, getting your money back is a multi-step process. This dashboard shows you **which bills need action** and **what to do next** — all in one screen.

## The workflow

Every medical bill follows one of three paths depending on how you paid:

### Classic bills

You paid the full amount at the doctor. The bill needs to go through both CNS and DKV.

```
You paid → Send to CNS → Wait for CNS → CNS reimbursed →
Send to DKV → Wait for DKV → DKV reimbursed → Done ✓
```

### PID bills (Paiement Immédiat Direct)

You only paid the small leftover amount. CNS pays the doctor directly, but you still need to wait for the CNS report and forward it to DKV.

```
You paid residual → Wait for CNS report → CNS report received →
Send to DKV → Wait for DKV → DKV reimbursed → Done ✓
```

### Non-CNS bills

Bills that CNS doesn't cover (alternative medicine, contact lenses, etc.). These go directly to DKV.

```
You paid → Send to DKV → Wait for DKV → DKV reimbursed → Done ✓
```

### CNS Reports

The reimbursement statements that CNS sends back. These need to be forwarded to DKV for additional reimbursement.

```
Report received → Send to DKV → Wait for DKV → DKV reimbursed → Done ✓
```

## Understanding the dashboard

### Summary cards (top of the page)

| Card | What it means |
|------|--------------|
| **Total Bills** | How many bills are being tracked in total |
| **Action Needed** | Bills that need you to do something (send to CNS or DKV) |
| **With CNS** | Bills currently with CNS (sent or waiting) |
| **With DKV** | Bills currently with DKV (sent or waiting) |
| **Complete** | Bills that are fully processed — nothing left to do |

### Pipeline columns

The main part of the screen shows columns, like a kanban board. Each column is a step in the process:

1. **CNS — To Send** → You need to mail or submit this bill to CNS
2. **CNS — Pending** → Sent to CNS, waiting for their response
3. **CNS — Reimbursed** → CNS has reimbursed, now ready for the next step
4. **DKV — To Send** → You need to scan/photo this in the DKV app
5. **DKV — Pending** → Sent to DKV, waiting for their response
6. **DKV — Reimbursed** → DKV has reimbursed
7. **Complete ✓** → Fully done, nothing more to do

### Bill cards

Each card in a column represents one bill. It shows:

- **Title** — the document name
- **Type badge** — colored label showing Classic (blue), PID (orange), Non-CNS (purple), or CNS Report (teal)
- **Date** — when the document was created
- **→ button** — click this to move the bill to the next step

### How to advance a bill

When you've completed a step (for example, you sent a bill to CNS), click the green **→** button on that bill's card. It will automatically move to the next column.

For example, if a bill is in "CNS — To Send" and you click **→ cns-pending**, it moves to "CNS — Pending" because you've sent it and are now waiting.

### Opening a document

Click anywhere on a bill card (not the → button) to open the original document in Paperless-NGX.

## Tags in Paperless-NGX

For the dashboard to work, documents in Paperless-NGX need two tags:

**A type tag** (which kind of bill is it):

- `type:classic`
- `type:pid`
- `type:non-cns`
- `type:cns-report`

**A status tag** (where is it in the process):

- `status:cns-to-send`
- `status:cns-pending`
- `status:cns-reimbursed`
- `status:dkv-to-send`
- `status:dkv-pending`
- `status:dkv-reimbursed`
- `status:complete`

The bottom of the dashboard has a **Tag Reference** section that shows which tags exist and warns you (with a ⚠ symbol) if any are missing from Paperless-NGX.

## Quick reference: what to do when...

| Situation | Action |
|-----------|--------|
| New bill from the doctor (you paid full price) | Scan it, tag as `type:classic` + `status:cns-to-send` |
| New bill from the doctor (PID, you paid residual only) | Scan it, tag as `type:pid` + `status:cns-pending` |
| New bill not covered by CNS | Scan it, tag as `type:non-cns` + `status:dkv-to-send` |
| You mailed a bill to CNS | Click **→ cns-pending** on the dashboard |
| CNS reimbursement report arrives | Scan the report, tag as `type:cns-report` + `status:dkv-to-send` |
| You scanned a bill/report in the DKV app | Click **→ dkv-pending** on the dashboard |
| Money from CNS appeared on the bank account | Click **→ cns-reimbursed** on the dashboard |
| Money from DKV appeared on the bank account | Click **→ dkv-reimbursed** on the dashboard |
| Bill is fully reimbursed by everyone | Click **→ complete** on the dashboard |
=======
# Medical_Bills_Dashboard
