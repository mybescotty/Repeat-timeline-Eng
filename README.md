# Repeat Defects Timeline

Single-file browser tool (`repeat-defects-timeline.html`) that finds repeat defects and shows how each one was closed, as a timeline, grouped by depot.

## How to use

1. Open `repeat-defects-timeline.html` in Chrome or Edge (double-click it, no install needed).
2. Load the two CSV exports:
   - **Defect / call log**: Status, Location, Call D/T, Reason, Bus No … Notes (column N)
   - **Work orders**: Work Order, Fleet Num, Date Completed, Work Performed Comment
3. Adjust the settings if needed, then click **Build timeline**.

All processing happens in the browser. No data is uploaded anywhere.

## What it does

- **Repeat** = the same bus (Bus No, column E) with the same reason (column D) logged at least the minimum number of times (default 2). You can optionally require the repeats to be within N days of each other.
- **Depot** = the Location of the bus's most recent call. Depots are listed in the sidebar and each one starts its own section (and its own page when printed).
- **Header** of each repeat = the Notes (column N) from the most recent call.
- **Timeline**: each call in date order, with the work orders for that fleet number completed after the call. A work order is shown under the most recent call before it, up to the "days after each call" window (default 7).
  - Red = call reported, green = work order closed, amber = closed as no fault found / ok for service, dashed = "No driver submitted EDVC found" (no work order after the call).
- **Reasons dropdown**: untick reason codes you don't need. The selection is remembered in your browser, and saved into the shareable HTML.

## Outputs

- **Print / PDF**: prints the current view (selected depot, search and filters) with every timeline expanded. Choose "Save as PDF" in the print dialog.
- **Save shareable HTML**: downloads a self-contained snapshot of the results. Recipients open it in a browser without needing the CSVs, and can still filter by depot, search and print.

## Column detection

Columns are matched by header name, so column order can change. If a header isn't recognised, it falls back to the standard column letter. The detected mapping appears under the upload boxes.
