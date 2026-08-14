# Comprehensive AI Build Prompt: GridGlow

You are a senior product designer and front-end engineer. Build a complete, polished, responsive web application called **GridGlow**. GridGlow is a private, browser-based workspace for cleaning spreadsheets, comparing two datasets, and performing an easy-to-understand XLOOKUP-style operation without requiring users to know Excel.

Do not build a static mockup. Build a functional application whose core workflows work with real user-uploaded CSV, XLSX, and XLS files.

## Core product idea

GridGlow helps nontechnical users:

1. Upload and clean a spreadsheet through guided verification steps.
2. Preview every cleaning change before applying it.
3. Compare two spreadsheets by a shared identifier and see conflicts.
4. Perform an XLOOKUP-style match between two spreadsheets.
5. Choose which columns to return from the search spreadsheet.
6. Choose custom names for newly returned columns.
7. Preview matched and unmatched records.
8. Download the final result as a CSV.
9. Save work as multiple named projects organized into folders.
10. Switch between projects and resume saved work.

All file processing and project storage must happen locally in the browser. Do not upload spreadsheet contents to a server. Clearly communicate this privacy behavior in the interface.

## Deliverable and technology

Create a standalone, responsive website. A single `index.html` is acceptable and preferred when practical. Include a concise `README.md` with launch and feature instructions.

Use semantic HTML, CSS, and JavaScript. The app must work when `index.html` is opened in a modern browser or hosted through GitHub Pages. Use SheetJS/XLSX for CSV and Excel parsing and export. It may be loaded from:

```html
<script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
```

Use IndexedDB for saved project data because uploaded datasets may exceed localStorage limits. localStorage may be used for small folder metadata. Do not require a backend, build process, user account, database server, or API key.

## Product name and visual identity

The product name is **GridGlow**.

Use a compact sparkle-style logo mark and a clean, trustworthy visual identity. The product should feel professional, calm, modern, and suitable for business data—not playful or cartoonish.

Recommended visual direction:

- Warm off-white page background.
- White cards with subtle neutral borders.
- Deep forest-green primary color.
- Pale translucent green for selected states and newly added columns.
- Amber for warnings and unmatched records.
- Red for destructive validation errors.
- Dark charcoal-green text.
- Muted gray-green secondary text.
- Rounded corners around 10–16px.
- Restrained soft shadows.
- System sans-serif type for controls and body text.
- Optional restrained serif type for the large marketing heading.
- Avoid excessive gradients, glass effects, oversized icons, or dashboard clutter.

Suggested color tokens:

```css
--ink: #17231e;
--muted: #68756e;
--line: #dfe6e1;
--paper: #fbfcfa;
--card: #ffffff;
--green: #176b4d;
--green-soft: #e4f3eb;
--amber: #a86213;
--amber-soft: #fff3dc;
--red: #a33a38;
--red-soft: #fde9e7;
```

## Overall application layout

Use a desktop application shell with:

- A left sidebar approximately 240px wide.
- A top application bar.
- A large central content area with a maximum readable width.
- Responsive behavior that collapses or hides the sidebar on narrow screens.

The left sidebar should contain:

- GridGlow logo and product name.
- Navigation items:
  - Data workspace
  - Clean dataset
  - Compare files
  - Match & enrich
- A “Saved projects” section.
- A `+` control for creating a folder.
- Folder headings and projects nested below them.
- The currently open project highlighted.
- A privacy message explaining that files and projects stay in the browser.

The top bar should contain:

- Breadcrumb text such as `Workspace / New project` or `Folder / Project name`.
- A “Switch project…” dropdown listing all saved projects as `Folder / Project`.
- A `New` button.
- An `Update current` button, disabled when no saved project is open.
- A high-emphasis `Save as new` button.
- A small `Local processing` status indicator.

The main page introduction should include:

- Eyebrow: `Spreadsheet quality workspace`.
- Heading: `Turn messy data into trusted data.`
- A concise explanation of cleaning, comparing, and enriching records.
- Three top-level tabs: `Clean`, `Compare`, and `Lookup`.

## File upload behavior

Every upload area must support:

- Clicking a clearly labeled file-selection button.
- Drag and drop.
- Accepted formats: `.csv`, `.xlsx`, and `.xls`.
- A visible drag-over state.
- A clear confirmation after parsing.

After upload, display this information prominently inside the upload area:

```text
✓ Uploaded: filename.xlsx · 1,234 rows · 18 columns
```

Never leave users uncertain about which file was uploaded.

Read only the first worksheet unless a worksheet selector is intentionally added. Treat the first populated row as headers. Remove rows that are completely blank. Remove columns that are completely blank across all rows. This is particularly important because SheetJS may generate names such as `__EMPTY`, `__EMPTY_1`, and `__EMPTY_2` for empty spreadsheet regions. Do not remove a generated `__EMPTY` column if it actually contains meaningful data.

## Clean dataset workflow

The Clean tab accepts one spreadsheet and displays summary metrics:

- Total rows.
- Total columns.
- Empty cells.
- Exact duplicate rows.

Provide a vertical verification workflow on desktop and a horizontally scrollable step list on mobile:

1. Overview
2. Missing values
3. Duplicates
4. Text cleanup
5. Export

Each step must have a title, short explanation, preview table, and appropriate controls.

### Overview

- Display the first 100 rows.
- Show how many rows are visible out of the total.
- Provide a clear `Start verification` action.

### Missing values

- Highlight blank cells with a subtle red background.
- Display the number of blank cells.
- Provide these actions:
  - Keep blank.
  - Remove rows containing blanks.
  - Fill blank cells with a user-entered replacement such as `Unknown`.
- Preview the data before applying.

### Duplicates

- Identify exact duplicate rows.
- Mark repeated rows with an amber indicator.
- Default to keeping the first occurrence and removing later copies.
- Allow the user to disable duplicate removal.

### Text cleanup

- Allow trimming leading and trailing whitespace.
- Allow text case selection:
  - Keep original.
  - Title Case.
  - lowercase.
  - UPPERCASE.
- Highlight cells that would change in the preview.

### Undo and export

- Keep a history of applied changes.
- Provide an `Undo` control that restores the previous dataset state.
- Recalculate quality metrics after each applied change.
- Export the cleaned result as an XLSX file.

## Compare spreadsheets workflow

The Compare tab accepts two files:

- `Primary file`.
- `Comparison file`.

Display the uploaded filename, row count, and column count for both files.

After both files are uploaded:

- Find column names present in both files.
- Populate a `Unique identifier` selector with the shared columns.
- Keep the comparison button disabled if no common column exists.
- Explain that the selected column should uniquely identify each record.

When comparison runs:

- Match rows using the selected identifier.
- Classify records as:
  - Match.
  - Conflict.
  - Only in primary.
  - Only in comparison.
- For conflicts, list the column names whose values differ.
- Display summary badges showing the count of each category.
- Highlight conflicting rows.
- Show a scrollable preview table.
- Allow downloading an XLSX comparison report.

Use normalized identifier matching where appropriate, consistent with the lookup rules below.

## Lookup workflow: beginner-friendly XLOOKUP

This workflow is the most important part of the application. It must be understandable to someone who has never used Excel or XLOOKUP.

Do not describe the workflow as “updating” either original spreadsheet. Explain that GridGlow creates a new result file and leaves both uploaded files unchanged.

### Step 1: Starting spreadsheet

Heading: `1. Starting spreadsheet`.

Description:

> Upload the main list you want in your final result. For example, this may be a customer list that has customer IDs but is missing addresses.

Upload-area language:

- `Choose your main list`.
- `Every row from this file stays in the final CSV`.

This spreadsheet supplies the base rows and existing columns in the final output.

### Step 2: Search spreadsheet

Heading: `2. Search spreadsheet`.

Description:

> Upload the list containing the missing information. It needs a shared value—such as customer ID or email—to connect each record.

Upload-area language:

- `Choose the file to search`.
- `This file supplies the information to add`.

### Step 3: Connection setup

Heading: `3. Tell us how to connect the files`.

Supporting description:

> Select the shared values, then choose the information to add.

Display a pale-green educational explanation:

> Example: Choose “Customer ID” in both matching fields. Then choose “Address” as the column to bring over. Column names may differ, but their values must represent the same person or record.

Provide these four controls with explanatory helper text:

1. **Find each value from this main-file column**
   - Populated using columns from the starting spreadsheet.
   - Helper: `Choose an ID, email, account number, or other value in the starting file.`

2. **Look for it in this search-file column**
   - Populated using columns from the search spreadsheet.
   - Helper: `Choose the column containing the same values in the second file.`

3. **Bring over this search-file column**
   - Populated using columns from the search spreadsheet.
   - Helper: `Choose the missing information to add, such as address or email.`

4. **Call the new column**
   - A text input automatically prefilled with the selected source column name.
   - Users can enter any heading they want for the final CSV.
   - Helper: `This becomes the heading in the final CSV.`

Provide:

- `+ Add another column` to save the current source-column-to-output-name mapping.
- A visible mapping summary such as `STREET_LINE1 → Address 1`.
- A remove control on each saved mapping.
- Support for multiple returned columns.
- `Run lookup & preview` as the primary action.

If the user runs the lookup without first pressing `Add another column`, automatically use the currently selected return column and entered output name. Prevent lookup if the source column or output column name is missing.

### Lookup matching rules

Implement exact logical matching with light normalization to avoid false nonmatches caused by spreadsheet formatting.

For both lookup keys:

- Convert the value to a string.
- Trim leading and trailing whitespace.
- Convert to lowercase for matching.
- Collapse repeated internal whitespace to one space.
- Treat a purely numeric value like `123.0` as equivalent to `123`.
- Ignore empty lookup keys in the search file.

Do not aggressively remove punctuation or leading zeros because values such as `00123` may be meaningful identifiers.

If duplicate identifiers exist in the search file, document and consistently apply a rule. A simple implementation may use the final matching record, but a production-quality version should warn users about duplicate search identifiers and ideally let them inspect them.

### Lookup result construction

For every row in the starting spreadsheet:

1. Read the selected main-file match value.
2. Normalize it.
3. Search the normalized lookup-column values in the second spreadsheet.
4. If found, copy each selected return value into its user-defined output column.
5. If not found, retain the starting row and place blank values in the newly added columns.

The original spreadsheets must remain unchanged. The result is a new in-memory dataset and a downloadable CSV.

Preserve the meaningful starting-file column order. Append the newly returned columns after the starting-file columns. Do not include completely empty spreadsheet columns.

### Lookup preview and unmatched records

After running the lookup, show summary badges:

- Number matched.
- Number unmatched.
- Number of columns filled.

Provide a `Show results` filter with:

- All rows.
- Matched only.
- Unmatched only.

The filter affects only the preview. The downloaded CSV must always contain all starting-file rows.

Mark unmatched preview rows with an amber indicator on the left. Make unmatched rows easy to inspect rather than only showing an unmatched count.

Display at most the first 100 rows in the preview for performance. If the selected filter produces no rows, display `No rows match this view.`

### Highlight newly added columns

Every newly returned column must have a low-opacity green background across the entire preview column. Apply the highlight to:

- The column header.
- Every data cell in that column.

Use approximately `rgba(23, 107, 77, 0.10)` or an equivalent theme-aware translucent green. Give new-column headers green text and a subtle green underline or inset border. Include a visible legend reading `Highlighted columns are newly added`.

The highlight is for the on-screen preview only. Do not place styling metadata or extra status columns in the exported CSV.

### Lookup CSV export

The primary download action should say `Download XLOOKUP result` or `Download final CSV`.

Generate a CSV containing:

- Every meaningful column from the starting spreadsheet.
- Every user-selected lookup return column using its custom output name.
- Every starting-file row.
- Filled returned values for matches.
- Blank returned values for unmatched rows.

Use UTF-8 with a byte-order mark so the CSV opens cleanly in Excel. Generate a filename based on the starting spreadsheet, such as `enriched-customers.csv`.

## Project and folder system

GridGlow must support multiple distinct projects. Saving repeatedly must not accidentally overwrite the only project.

Use IndexedDB with a `projects` object store keyed by a unique project ID. A project record should contain:

```js
{
  id: crypto.randomUUID(),
  name: "Customer address lookup",
  folder: "Operations",
  updated: Date.now(),
  state: /* structured clone of current application state */
}
```

Save parsed dataset rows and relevant workflow state, including:

- Uploaded file names.
- Parsed starting and search datasets.
- Current cleaned dataset.
- Cleaning step and undo history if practical.
- Comparison inputs and results.
- Lookup mappings.
- Lookup match status.
- Enriched output rows.
- Preview column metadata.

### Folder behavior

- Provide a `+` control beside `Saved projects`.
- Ask for a folder name.
- Save folder names locally.
- Display projects grouped below their folder headings.
- Show an empty state for folders with no projects.

### Save and update behavior

Provide separate actions:

- **Save as new**: Always creates a new project with a new UUID, even if another project is already open.
- **Update current**: Updates only the currently open project and stays disabled when no saved project is open.
- **New**: Starts a blank project after warning the user to save current work if necessary.

When saving as new:

- Ask for a project name.
- Ask for or select a folder name.
- Create a fresh project ID.
- Update the breadcrumb.
- Add the new project to the sidebar and project picker.

### Switching projects

Allow project switching in two places:

- By clicking a project in the sidebar.
- Through the `Switch project…` dropdown in the top bar.

Switching must restore the selected project’s actual state—not just its name. Clear stale UI from the previously open project before rendering the next one. Restore visible uploaded-file confirmations, controls, mappings, previews, summaries, filters, and download availability as appropriate.

Highlight the active project in the sidebar and select it in the top picker.

Explain that saved projects exist only in the current browser and may be removed if the user clears browser/site data.

## Tables and previews

All data tables should:

- Use semantic `table`, `thead`, `tbody`, `th`, and `td` elements.
- Include a display-only row number column.
- Keep headers visible while scrolling vertically.
- Allow contained horizontal scrolling for wide spreadsheets.
- Use subtle horizontal dividers rather than heavy full-cell gridlines.
- Preserve readable font sizes.
- Escape all user-provided cell values and column names before inserting HTML.
- Limit previews to 100 rows while preserving the full data in memory and exports.

## Responsive behavior

Support desktop, tablet, and mobile widths.

At narrow widths:

- Collapse to a single-column shell.
- Hide or replace the fixed desktop sidebar.
- Stack upload cards.
- Stack comparison and lookup cards.
- Wrap top actions without overlap.
- Make the cleaning steps horizontally scrollable if needed.
- Keep tables horizontally scrollable inside their own container.
- Do not allow the page itself to overflow horizontally.

If the sidebar is hidden on mobile, ensure saved projects remain accessible through the top project picker.

## Accessibility

- Use real buttons, inputs, labels, selects, and file inputs.
- Associate labels with their controls where possible.
- Keep keyboard focus visible.
- Include `aria-label` text for icon-only buttons.
- Do not rely on color alone: use text labels, counts, and row indicators along with color.
- Maintain readable contrast.
- Use concise status messages or toasts after saving, opening, updating, undoing, and downloading.

## Privacy and reliability

- Process all data locally.
- Never send spreadsheet content through fetch, XHR, WebSocket, or analytics.
- Do not log uploaded cell data to the console.
- Sanitize values inserted into the DOM.
- Handle invalid or unreadable files with a clear error message.
- Disable actions until required files and selections are available.
- Revoke generated Blob URLs after CSV download.
- Use a UTF-8 BOM in exported CSV files.

## Important edge cases

Handle or clearly report:

- Empty files.
- Files with headers but no data rows.
- Blank rows and columns.
- Generated `__EMPTY` columns.
- Different capitalization in identifiers.
- Leading/trailing spaces in identifiers.
- Numeric identifiers displayed as `123` versus `123.0`.
- Identifiers with meaningful leading zeros.
- Missing match values.
- Duplicate identifiers in the search file.
- User-defined output names that already exist in the starting file.
- Multiple selected return columns.
- No matched rows.
- No unmatched rows.
- Very wide datasets.
- Project switching between different workflow types.
- Saving a new project while another project is open.
- Updating a current project without creating a duplicate.
- Browser storage errors or quota limitations.

For output-column name collisions, either ask the user to choose another name or use a clear suffix such as `Address (lookup)`. Never silently destroy an existing starting-file column.

## Acceptance tests

The finished app is complete only if all of these tests pass:

1. Uploading a CSV displays its exact name, row count, and meaningful column count.
2. Uploading an XLSX with blank trailing columns does not show useless `__EMPTY` columns if those columns contain no data.
3. Cleaning changes can be previewed, applied, and undone.
4. Missing-value and duplicate counts update after changes.
5. Two spreadsheets can be compared using a shared identifier.
6. Comparison results distinguish matches, conflicts, and records present in only one file.
7. A starting file with customer IDs can match a search file whose equivalent IDs contain capitalization or surrounding-space differences.
8. `123` matches `123.0`, while `00123` remains distinct from `123` unless the source data itself makes them identical.
9. Users can select a search-file column and give it a custom output-column name.
10. Users can add multiple returned columns.
11. Lookup results retain all starting rows.
12. Unmatched rows have blank returned values.
13. Users can filter the preview to unmatched-only records.
14. New columns are highlighted across headers and cells at low opacity.
15. Preview filtering does not change the downloaded CSV.
16. The final lookup download is a valid UTF-8 CSV with all original and returned columns.
17. `Save as new` creates multiple different projects rather than overwriting one.
18. `Update current` modifies only the open project.
19. Projects can be organized into folders.
20. Users can toggle among projects from both the sidebar and top picker.
21. Switching projects restores the selected project’s data and UI without showing stale content from the previous project.
22. The site works when hosted as a static GitHub Pages website.
23. The JavaScript parses without syntax errors and the major workflows function without console errors.

## Final handoff

Return:

- A working `index.html`.
- A `README.md` explaining how to open the site and host it on GitHub Pages.
- No placeholder-only interactions.
- No backend requirement.
- A concise summary of completed features and any known browser-storage limitations.

Before finishing, verify the page at desktop and mobile widths, test CSV and XLSX parsing, test a successful and unsuccessful lookup, test multiple returned columns, test the unmatched-only filter, test CSV export, create at least two projects, and switch between them.
