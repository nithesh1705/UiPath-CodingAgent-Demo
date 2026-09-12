# Business Requirements Document (BRD)

## Sales Performance & Commission Reconciliation Automation

**Document Type:** Business Requirements Document  
**Automation Platform:** UiPath  
**Input Format:** Excel  
**Output Format:** Excel  
**Reporting Frequency:** Monthly

---

## 1. Business Objective

The Sales Operations team receives monthly sales information from multiple Excel files maintained by different teams.

Currently, the team manually combines the files, validates the data, filters eligible transactions, calculates employee performance, determines commission eligibility, and prepares management reports.

The objective is to build a UiPath automation that performs the complete process automatically and generates two professional, management-ready Excel reports.

The automation should reduce manual effort, improve calculation accuracy, and produce a consistent reporting format every month.

---

## 2. Input Files

The automation will receive the following four Excel files.

### 2.1 Employee_Master.xlsx

Contains employee/RM master information.

**Columns:**
- Employee ID
- Employee Name
- Region
- Branch
- Grade

### 2.2 Customer_Master.xlsx

Contains customer information.

**Columns:**
- Customer ID
- Customer Name
- Segment
- Risk Category

### 2.3 Sales_Transactions.xlsx

Contains monthly sales transactions.

**Columns:**
- Transaction ID
- Employee ID
- Customer ID
- Product
- Transaction Date
- Sales Amount
- Status

Possible transaction statuses include:
- Completed
- Cancelled
- Rejected
- Pending

### 2.4 Target_Master.xlsx

Contains monthly employee targets and commission rates.

**Columns:**
- Employee ID
- Monthly Target
- Commission %

---

## 3. Output Files

The automation must generate the following two Excel files.

### 3.1 Sales_Performance_Report.xlsx

The report should contain:

- Employee ID
- Employee Name
- Region
- Branch
- Grade
- Total Transactions
- Eligible Transactions
- Total Sales
- Monthly Target
- Achievement %
- Performance Category

It should also contain:

- Management summary
- Top 5 performing employees
- Bottom 5 performing employees
- Excluded transaction summary

### 3.2 Commission_Report.xlsx

The report should contain:

- Employee ID
- Employee Name
- Region
- Branch
- Total Eligible Sales
- Monthly Target
- Achievement %
- Commission %
- Commission Eligibility
- Commission Amount

---

## 4. Business Rules

### 4.1 Transaction Eligibility

A transaction is considered eligible only when:

1. Status is `Completed`
2. Transaction Date belongs to the reporting month
3. Sales Amount is greater than 0
4. Employee ID exists in Employee_Master.xlsx
5. Customer ID exists in Customer_Master.xlsx

Cancelled, rejected, pending, invalid, or incomplete transactions must not be included in sales calculations.

---

### 4.2 Data Enrichment

Sales transaction data must be enriched using the master files.

Using **Employee ID**, retrieve:

- Employee Name
- Region
- Branch
- Grade

Using **Customer ID**, retrieve:

- Customer Name
- Segment
- Risk Category

Using **Employee ID** from Target_Master.xlsx, retrieve:

- Monthly Target
- Commission %

---

### 4.3 Sales Calculation

For each employee:

**Total Sales**

`Total Sales = SUM(Sales Amount of all eligible transactions)`

**Achievement %**

`Achievement % = Total Sales / Monthly Target × 100`

---

### 4.4 Performance Classification

Classify each employee according to Achievement %:

| Achievement | Category |
|---|---|
| >= 100% | Excellent |
| >= 80% and < 100% | On Track |
| >= 60% and < 80% | Needs Improvement |
| < 60% | Below Target |

---

### 4.5 Commission Calculation

Commission is payable only when the employee achieves at least **80% of the monthly target**.

If:

`Achievement % >= 80%`

then:

`Commission Amount = Eligible Sales × Commission %`

Otherwise:

`Commission Amount = 0`

Commission Eligibility should be displayed as:

- Eligible
- Not Eligible

---

## 5. Data Validation Requirements

Before processing, the automation should validate that:

- All four input files are available.
- Required sheets exist.
- Required columns exist.
- Employee IDs are valid.
- Customer IDs are valid.
- Monthly Target is not blank or zero.
- Sales Amount contains valid numeric values.
- Transaction Date contains valid dates.
- Duplicate Transaction IDs are identified.
- Invalid records are excluded from calculations.

The automation should maintain an exception/exclusion collection containing the reason for exclusion.

Example exclusion reasons:

- Invalid Employee ID
- Invalid Customer ID
- Invalid Sales Amount
- Invalid Transaction Date
- Transaction not completed
- Transaction outside reporting month
- Duplicate Transaction ID

---

## 6. Excel Formatting Requirements

Both output reports must be professionally formatted.

### Header Formatting

- Bold headers
- Center alignment
- Appropriate header formatting
- Borders
- Wrapped text where required
- Freeze the header row
- Enable Excel AutoFilter

### Column Formatting

Automatically adjust column widths so that the report is readable.

### Currency Formatting

Sales and commission amounts should use:

`₹#,##0.00`

### Percentage Formatting

Achievement and commission rates should use:

`0.00%`

### Conditional Formatting

Achievement %:

- **>= 100%** → Green
- **80%–99.99%** → Yellow
- **< 80%** → Red

Commission Eligibility:

- **Eligible** → Green
- **Not Eligible** → Red

---

## 7. Sorting Requirements

The Sales Performance Report should be sorted in the following order:

1. Region — Ascending
2. Branch — Ascending
3. Achievement % — Descending

The highest-performing employees within each branch should appear first.

---

## 8. Management Summary

The Sales Performance Report should contain a summary section showing:

- Total Employees
- Total Transactions
- Eligible Transactions
- Excluded Transactions
- Total Sales
- Average Achievement %
- Employees Above Target
- Employees Below Target
- Total Commission Payable

The report should also identify:

### Top 5 Performers

Employees with the highest Achievement %.

### Bottom 5 Performers

Employees with the lowest Achievement %.

---

## 9. Exception Handling

The automation should handle unexpected situations gracefully.

Examples:

- Missing input file
- Missing Excel sheet
- Missing required column
- Invalid data
- Duplicate records
- Empty input file
- Excel file locked by another application

Errors should be logged with enough information to identify the failed step and reason.

The automation should not silently ignore data problems.

---

## 10. Expected UiPath Solution

The automation should be implemented as a UiPath RPA project.

The solution should include logical stages such as:

```text
Initialize
    ↓
Validate Input Files
    ↓
Read Employee Master
    ↓
Read Customer Master
    ↓
Read Sales Transactions
    ↓
Read Target Master
    ↓
Validate & Filter Transactions
    ↓
Enrich Transaction Data
    ↓
Calculate Employee Performance
    ↓
Calculate Commission
    ↓
Generate Sales Performance Report
    ↓
Generate Commission Report
    ↓
Apply Excel Formatting
    ↓
Generate Summary
    ↓
Complete
```

The implementation should use appropriate UiPath Excel activities and reusable workflow components wherever practical.

---

## 11. Final Expected Result

At the end of the automation, the user should receive:

### Output 1
`Sales_Performance_Report.xlsx`

A complete employee performance report containing calculations, classifications, summaries, sorting, filtering, and professional formatting.

### Output 2
`Commission_Report.xlsx`

A complete commission report containing eligibility calculations, commission amounts, and management-ready formatting.

The final reports should require **no manual calculations or formatting**.