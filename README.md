# Repeat Defects Timeline

Single-file browser tool (`repeat-defects-timeline.html`) that finds repeat defects and shows how each one was closed, as a timeline, grouped by depot.

## How to use

1. Open `repeat-defects-timeline.html` in Chrome or Edge (double-click it, no install needed).
2. Load the CSV exports:
   - **HxGN / EAM driver defect (EDVC) export** (required): OpCo, Depot, Equipment, Date Reported, Type, Driver Defect WOs, Description, Driver Comments, Roadworthiness Defect, Status, Priority, Sched. Start Date, Assigned To Name, Problem Description, Closing Comments, Action Description, Cleared Date. Windows-1252 or UTF-8 both work.
   - **Call log** (optional in defect mode; required in call log mode): Status, Location, Call D/T, Reason, Bus No … Notes.
3. Pick how to find repeats, adjust the settings, then click **Build timeline**.

All processing happens in the browser. No data is uploaded anywhere.

## Repeat modes

### Same defect on the same bus (default)
- **Repeat** = the same bus (Equipment) with the same defect reported at least the minimum number of times (default 2). Optionally the reports must be within N days of each other.
- The defect is the item at the start of **Driver Comments (column H)**, before " - Note 1 (…)", e.g. "Coolant", "Bell Push". If column H is empty (mostly breakdowns), the item part of Description is used.
- So low coolant followed by a broken bell push is **not** a repeat; low coolant followed by low coolant **is**.
- **Depot**: the call log's Location for that bus if the call log is loaded, otherwise the EAM depot code and OpCo.
- **Timeline**: each EDVC in date order with WO number, the driver's notes, the closing comment, action, problem, fitter and cleared date, and the time between repeats.

### Call log: same bus + reason
- **Repeat** = the same Bus No with the same call log Reason. Each call shows the EDVCs for that bus reported from the start of the call day up to the "days after each call" window. "No driver submitted EDVC found" marks calls with none.

## Data handling
- The exact report time comes from the first driver note timestamp (UTC, shown in local time). Rows without notes use the Date Reported.
- The EAM export mixes date formats (1st–12th of the month come out as mm/dd/yyyy). Ambiguous dates are resolved against the driver note timestamps.
- Driver notes have FaultIDs and duplicate "Ticketer Portal update" copies removed.
- Colours: green = EDVC cleared, amber = no fault found / ok for service, red outline = not cleared yet, red = call log call.

## Filters and outputs
- **Defects / Reasons dropdown**: untick what you don't need (with a find box). The selection is remembered in your browser and saved into the shareable HTML.
- **Print / PDF**: prints the current view (selected depot, search and filters) with every timeline expanded, one depot per page.
- **🖨 PDF on each repeat**: prints or saves just that one repeat. The file name defaults to the bus, defect and date.
- **Save shareable HTML**: downloads a self-contained snapshot. Recipients open it in a browser without the CSVs, and can still filter by depot, search and print.

## Column detection
Columns are matched by header name, so column order can change. If a header isn't recognised, it falls back to the standard column letter. The detected mapping appears under the upload boxes.
