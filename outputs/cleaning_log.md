# Cleaning Log

## Files Created
- `data/raw_orders.xlsx` — copied from the uploaded raw workbook and left unchanged.
- `data/cleaned_orders.xlsx` — cleaned, standardized, validated, and analysis-ready workbook.
- `outputs/data_quality_report.xlsx` — detailed data quality report.
- `outputs/pivot_summary.xlsx` — pivot-style business summaries.
- `screenshots/*.png` — required visual previews.

## Issues Found
- Raw rows reviewed: **932**
- Exact duplicate rows found and removed: **20**
- Duplicate `order_id` values in raw data: **31**
- Conflicting duplicate `order_id` values retained after exact duplicate removal: **12**
- Records flagged for duplicate review: **24**
- Missing regions filled as `Unknown`: **25**
- Missing ship modes filled as `Unknown`: **21**
- Missing discounts treated as 0: **18**
- Negative discounts flagged invalid: **15**
- Discounts above allowed range flagged invalid: **15**
- Ship dates earlier than order dates: **21**
- Sales calculation mismatches: **64**
- Profit calculation mismatches: **64**
- Cancelled orders excluded from completed sales summary: **145**
- Failed payments excluded from completed sales summary: **69**
- Refunded/returned orders summarized separately: **200**

## Cleaning Actions Performed
1. Preserved the raw workbook by copying it to `data/raw_orders.xlsx`.
2. Created a separate cleaned workbook at `data/cleaned_orders.xlsx`.
3. Standardized text fields using TRIM-style cleanup:
   - Removed leading/trailing spaces.
   - Collapsed repeated internal spaces.
   - Standardized case and common category/status variants.
   - Removed unwanted special characters where applicable.
4. Standardized date fields:
   - Converted valid date strings to Excel date values.
   - Supported `yyyy-mm-dd`, `dd-mm-yyyy`, `mm/dd/yyyy`, and `dd Mon yyyy` formats.
   - Flagged missing, invalid, and logically incorrect dates.
5. Removed exact duplicate rows only after keeping the first occurrence.
6. Retained conflicting duplicate `order_id` records and flagged them for review.
7. Added calculated columns:
   - `cleaned_discount`
   - `calculated_sales`
   - `calculated_profit`
   - `profit_margin`
   - `shipping_delay_days`
   - `order_month`
   - `order_year`
   - `completed_sales_included`
   - `data_quality_flag`
   - `issue_details`

## Business Rules Applied
| Rule Area | Decision |
|---|---|
| Missing region | Filled as `Unknown` and flagged. |
| Missing ship_mode | Filled as `Unknown` and flagged. |
| Missing discount | Treated as `0` only when quantity, unit price, sales, cost, and profit were valid numeric values. |
| Negative discount | Flagged as invalid. |
| Discount above allowed range | Flagged as invalid. The assumed maximum allowed discount was 50%. |
| Cancelled orders | Excluded from final completed sales summary. |
| Failed payments | Excluded from final completed sales summary. |
| Refunded/returned orders | Summarized separately from completed sales. |
| Ship date before order date | Flagged as invalid shipping record. |
| Duplicate order_id conflicts | Retained and flagged for review, not silently deleted. |
| Final completed sales | Includes only `Paid` + `Completed` records without invalid flags or duplicate order conflict flags. |

## Assumptions Made
- Slash dates such as `04/10/2025` were interpreted as `mm/dd/yyyy`.
- Hyphen dates such as `26-01-2025` were interpreted as `dd-mm-yyyy`.
- The maximum allowed discount was assumed to be **50%** because the source prompt did not specify a threshold.
- Percentage discount strings such as `70%` were converted to decimal form before validation.
- Calculation mismatch tolerance was set to **0.05** to allow minor rounding differences.
- Exact duplicate rows were removed; conflicting duplicate order IDs were retained and flagged.

## Records Removed
- **20** exact duplicate rows were removed from the cleaned data.
- No conflicting duplicate order IDs were deleted.

## Records Flagged
- Clean records: **505**
- Warning records: **356**
- Invalid records: **51**
- Total retained cleaned records: **912**

## Limitations
- The workbook uses rule-based cleaning and validation; unusual text values outside the mapped categories may require manual business review.
- Discount validation uses an assumed 50% maximum because no explicit allowed discount range was provided.
- Pivot summaries exclude invalid and duplicate-conflict records from completed-sales metrics to avoid overstating sales.
- Records with warnings may still require business review before final reporting.
