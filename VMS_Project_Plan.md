# Vendor Management System (VMS) — Project Plan

**Platform:** Google Sheets / Google Workspace  
**Currency:** INR (₹)

## 1) Spreadsheet Architecture (Tabs/Sheets)

| Sheet Name | Purpose | Key Columns |
|---|---|---|
| `Vendor_Master` | Database of all registered vendors | Vendor ID, Name, Contact, GSTIN, PAN, Bank Details, Status (Active/Pending) |
| `Order_Generation` | Log of all Purchase Orders issued | PO Number, Date, Vendor ID, Item, Size, Unit (Kg, Pcs, etc.), Quantity, Rate, GST %, Total INR |
| `Billing_Log` | Tracking invoices and proof uploads | Invoice No, PO Reference, Bill Date, Amount, Drive Link (PDF proof), Approval Status |
| `Payments_Record` | Final payment tracking | Payment Date, Invoice Ref, Amount Paid (INR), UTR Number, Payment Mode |
| `Dashboard` | Visual overview | Total payables, pending payment totals, vendor-wise spend, monthly trend |

---

## 2) Features & Workflow

## A. Vendor Registration & Controlled Access

- Create a **Google Form** linked to `Vendor_Master` for onboarding.
- Form fields: Vendor Name, Contact, GSTIN, PAN, Bank details, document upload.
- Store uploaded PAN/GST files in Google Drive.
- Use **Protected ranges/sheets** for sensitive data.
- For vendor self-service:
  - Option 1: dedicated `Vendor_Update` sheet per vendor.
  - Option 2: filtered update view managed by admin.

## B. Order Generation (Units, Sizes, Amounts)

In `Order_Generation`:

- Use **Data Validation** for controlled entries.
  - Units list: `Nos`, `Kg`, `Mtr`, `Ltr`, `Box`, `Pkt`
  - Sizes list: `Small`, `Medium`, `Large`, `XL`, `Custom`
- Calculated fields:
  - Base Amount: `=Quantity * Rate`
  - GST Amount: `=Base_Amount * GST%`
  - Total INR: `=Base_Amount + GST_Amount`

## C. Billing & Proof Upload

- Invoice submission via:
  - Google Form (recommended), or
  - shared Drive folder with naming standard.
- In `Billing_Log`, keep a clickable PDF link with:
  - `=HYPERLINK("drive_link","View PDF")`
- Add `Approval Status` dropdown: `Pending`, `Approved`, `Rejected`, `On Hold`.

## D. Payment Follow-up (INR)

- Aging alert using **Conditional Formatting**:
  - Rule: unpaid bill older than 30 days → red highlight.
- Outstanding formula:
  - `Outstanding = Total Billed - Total Paid`
- Suggested helper columns in `Billing_Log`:
  - `Days Pending`
  - `Payment Status` (`Unpaid`, `Partially Paid`, `Paid`)

---

## 3) Automation with Apps Script

Use Google Apps Script for:

1. **Auto PO PDF + Email**
   - Trigger from new row or status change in `Order_Generation`.
   - Generate a formatted PO PDF and email vendor + admin copy.

2. **New Bill Notification**
   - On new billing entry, send email to Admin/Finance with invoice summary and Drive link.

3. **Weekly Payment Reminder**
   - Time-driven trigger (weekly) sends pending payments digest to Finance team.

4. **ID/Number Sequencing (Optional)**
   - Auto-generate:
     - Vendor ID (e.g., `VND-0001`)
     - PO Number (e.g., `PO-2026-0001`)

---

## 4) Dashboard (Visuals)

In `Dashboard`, add:

1. **Total Payables**
   - Big-number KPI (sum of current outstanding in INR).

2. **Vendor Spend Distribution**
   - Pie chart by vendor from paid or billed totals.

3. **Monthly Spend Trend (Last 6 Months)**
   - Line chart based on `Order_Generation` or `Payments_Record`.

4. **Optional KPIs**
   - Pending approvals count
   - Overdue invoices count (>30 days)
   - Average payment cycle days

---

## 5) Recommended Build Sequence

1. Create sheet tabs and column schema.
2. Configure data validations and formulas.
3. Set up Google Forms (Vendor + Billing intake).
4. Add protection rules and sharing model.
5. Build Dashboard pivots/charts.
6. Implement Apps Script automations.
7. Perform UAT with sample vendors and invoices.

---

## 6) Governance & Controls

- Keep **master dropdown lists** in a hidden config range/sheet.
- Lock formula columns to avoid accidental edits.
- Use standardized naming for Drive files:
  - `VendorName_InvoiceNo_YYYYMMDD.pdf`
- Weekly backup/export of critical tabs (CSV or XLSX snapshot).
