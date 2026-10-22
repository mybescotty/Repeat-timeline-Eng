# Repeat Defects Timeline

Single-file browser tool (`repeat-defects-timeline.html`) that finds repeat defects and shows how each one was closed, as a timeline, grouped by depot.

## How to use

1. Open `repeat-defects-timeline.html` in Chrome or Edge (double-click it, no install needed).
2. Load the CSV exports:
   - **HxGN / EAM driver defect (EDVC) export**: OpCo, Depot, Equipment, Date Reported, Type, Driver Defect WOs, Description, Driver Comments, Roadworthiness Defect, Status, Priority, Sched. Start Date, Assigned To Name, Problem Description, Closing Comments, Action Description, Cleared Date. Windows-1252 or UTF-8 both work.
   - **Call log** (required for the default mode; optional for HxGN-only mode): Status, Location, Call D/T, Reason, Bus No … Notes.
3. Pick how to find repeats, adjust the settings, then click **Build timeline**.

All processing happens in the browser. No data is uploaded anywhere.

## Repeat modes

### Call log + HxGN repairs in between (default)
- **Repeat** = the same Bus No with the same call log Reason logged at least the minimum number of times (default 2). Optionally the calls must be within N days of each other.
- Each call is the moment the driver raised it. Under it are the HxGN EDVCs for that bus from the start of the call day up to the next call (or up to N days, if set). These are the repairs in between.
- **HxGN repairs shown under each call**:
  - *Only defects raised 2+ times in the timeline* (default): uses the defect at the start of Driver Comments (column H). Low coolant followed by low coolant is shown and tagged "Coolant ×2". A one-off bell push is hidden and counted in a "+ N other EDVCs" line.
  - *All EDVCs on the bus*: shows everything.
- The repeat header lists the repeating defects. "No driver submitted EDVC found" marks calls with no EDVC at all.

### HxGN only: same defect on the same bus
- **Repeat** = the same Equipment with the same column H defect reported more than once. There's no call log needed; if one is loaded it names the depots, otherwise EAM depot codes are used.

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
