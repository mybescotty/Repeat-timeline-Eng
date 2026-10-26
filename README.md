# Repeat Defects Timeline

Single-file browser tool (`repeat-defects-timeline.html`) that finds repeat defects and shows how each one was closed, as a timeline, grouped by depot.

## How to use

1. Open `repeat-defects-timeline.html` in Chrome or Edge (double-click it, no install needed).
2. Load the CSV exports:
   - **HxGN / EAM driver defect (EDVC) export**: OpCo, Depot, Equipment, Date Reported, Type, Driver Defect WOs, Description, Driver Comments, Roadworthiness Defect, Status, Priority, Sched. Start Date, Assigned To Name, Problem Description, Closing Comments, Action Description, Cleared Date. Windows-1252 or UTF-8 both work.
   - **Call log** (required for the default mode; optional for HxGN-only mode): Status, Location, Call D/T, Reason, Bus No, VEHICLE_TYPE, Driver, Incident Date, Route Code(s), Notes, BUS_BOARD, START_TIME (L), END_TIME, VAR_NOTES, Lost Mileage Reason Description (O).
3. Pick how to find repeats, adjust the settings, then click **Build timeline**.

All processing happens in the browser. No data is uploaded anywhere.

## Repeat modes

### Call log + HxGN repairs in between (default)
- **Repeat** = the same Bus No with the same **lost mileage category (call log column O, "Lost Mileage Reason Description")** logged at least the minimum number of times (default 2). Optionally the calls must be within N days of each other.
  - Engine Failure followed by Engine Failure is a repeat, even if the Reason (column D) differs. Engine Failure followed by Electrical Defect is not.
  - Calls with column O blank are left out. Older call logs with no column O are grouped by Reason instead.
- Each call is the moment the driver raised it. Under it are the HxGN EDVCs for that bus from the start of the call day up to the next call (or up to N days, if set). These are the repairs in between.
- **HxGN repairs shown under each call**:
  - *Only EDVCs related to the call's category* (default): each lost mileage category has a set of related words, matched against the EDVC description, column H defect, driver notes, problem and closing comment. For example, Fluid/Air Leaks matches coolant, water, oil, fuel, AdBlue and air leaks, low coolant lights and top-ups; Engine Failure matches EML/ECU, derate/lim, non-start, cutting out, DPF/regen, turbo and so on. Categories with no word list match on the words in the category name.
  - EDVCs on the bus that aren't related (e.g. Greenroad/CCTV under a coolant breakdown) are counted as "+ N other EDVCs on this bus, not related to …" and can be clicked open. Print shows the count only.
  - *All EDVCs on the bus*: shows everything.
- Related EDVC defects that come up more than once are tagged (e.g. "Coolant ×2") and listed in the repeat header. "No related EDVC found" marks calls where the bus has EDVCs but none relate; "No driver submitted EDVC found" marks calls with none at all.

### HxGN only: same defect on the same bus
- **Repeat** = the same Equipment with the same column H defect reported more than once. No call log is needed.
- **Depot names**: HxGN only has a depot code (kept separate per company, since codes repeat across OpCos). When the call log is loaded, each code is named after the call log Location most often seen for buses with that code. The setup screen lists every code in a **Depot names** table where you can type or correct names; these are remembered on that computer. Codes with no name show as "Depot code · company".

## Data handling
- The exact report time comes from the first driver note timestamp (UTC, shown in local time). Rows without notes use the Date Reported.
- The EAM export mixes date formats (1st–12th of the month come out as mm/dd/yyyy). Ambiguous dates are resolved against the driver note timestamps.
- **Call times**: the breakdown time comes from **START_TIME** (call log column L), combined with the call date (Call D/T, or Incident Date if there's no Call D/T). It accepts `14:35`, `14:35:00`, `2:35 pm`, Excel time fractions, `1899-12-30T14:35:00` (Excel time-only) and full date/times. A START_TIME that is blank, 00:00 / `1899-12-30T00:00:00`, not a time, or dated a different day from the call is ignored. The call then shows the date only, and gaps between calls are counted in whole days. A note under the upload boxes says how many calls have a time.
- **Call notes**: the repeat header and each call use **Notes**. VAR_NOTES, if present and different, is shown under it.
- Driver notes have FaultIDs and duplicate "Ticketer Portal update" copies removed.
- Colours: green = EDVC cleared, amber = no fault found / ok for service, red outline = not cleared yet, red = call log call.

## Filters and outputs
- **Driver / Engineer dropdowns**: filled from the loaded data with how many repeats each appears in. Drivers come from the call log Driver column and the driver numbers in the HxGN driver notes, matched on driver number. Engineers come from HxGN Assigned To Name on the EDVCs shown in a timeline. Picking one keeps only repeats involving them and outlines their calls/EDVCs in pink. The choice is listed on prints.
- **Category groups**: inside each depot, repeats sit in collapsible groups by lost mileage category (e.g. Fluid/Air Leaks, Engine Failure), largest first. Each group header shows its repeat count and how many have no related EDVC. Click a group to open it. Searching opens the groups with matches, Expand all opens everything, and printing shows every group open. In HxGN-only mode, groups are by defect area (e.g. Driver Warning Lights).
- **Defects / Reasons dropdown**: untick what you don't need (with a find box). The selection is remembered in your browser and saved into the shareable HTML.
- **Print / PDF**: prints the current view (selected depot, search and filters) with every timeline expanded, one depot per page.
- **✉ Email on each repeat**: copies that repeat's timeline as formatted content (tables and inline colours that Outlook keeps) and opens a new blank email with the subject filled in (bus, category, depot, count, dates). Click in the body and press Ctrl+V. Browsers can't attach files to an email from a web page; to send the PDF as well, save it with 🖨 PDF and attach it.
- **🖨 PDF on each repeat**: prints or saves just that one repeat. The file name defaults to the bus, defect and date.
- **Save shareable HTML**: downloads a self-contained snapshot. Recipients open it in a browser without the CSVs, and can still filter by depot, search and print.

## Column detection
Columns are matched by header name, so column order can change. Only fields whose header isn't recognised fall back to the standard column letter, and never onto a column another field is named in. The detected mapping appears under the upload boxes.
