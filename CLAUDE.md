# CLAUDE.md - AI Assistant Guide for Card Batch Inventory System

## Project Overview

**Project Name:** Card Batch Inventory System (cardbatchinvintory)
**Type:** Single-page HTML web application
**Purpose:** Trading card inventory management system for eBay sellers managing partnerships

This is a lightweight, self-contained inventory tracking system for managing trading card batches through the workflow of photography, listing, and shipping on eBay. It supports partnership workflows between an inventory provider and a shipping/listing partner.

## Repository Structure

```
cardbatchinvintory/
├── invintory.html          # Main application (single file, self-contained)
└── CLAUDE.md              # This file - AI assistant documentation
```

**Note:** This is intentionally a single-file application with no build process, no dependencies, and no package manager. Everything (HTML, CSS, JavaScript) is contained in `invintory.html`.

## Technology Stack

- **Frontend:** Pure HTML5, CSS3, JavaScript (ES6+)
- **Styling:** Inline CSS with gradient themes, responsive design
- **Data Storage:** Browser localStorage (client-side only)
- **No Dependencies:** Zero npm packages, no frameworks, no build tools
- **No Backend:** Entirely client-side application

## Application Architecture

### Single File Design
The application follows a monolithic single-file architecture where all code is embedded in one HTML file:
- **Lines 1-336:** HTML structure and inline CSS styles
- **Lines 337-564:** HTML markup for UI components
- **Lines 565-847:** JavaScript application logic

### Data Model

Each inventory item is a JavaScript object with the following schema:

```javascript
{
  timestamp: String,           // Auto-generated: "12/17/2025, 10:30:00 AM"
  sport: String,              // Baseball, Basketball, Football, Hockey, Soccer, Other Sports, Non-Sports, MIXED BULK
  condition: String,          // "Raw (ungraded)", "Graded (in slab)", "Mix of both"
  quantityType: String,       // "Single card", "Lot of 2-4 cards", "Lot of 5+ cards", "BULK LOT - NEEDS SORT"
  quantity: Number,           // Number of items (1 lot counts as 1 item)
  description: String,        // Brief description for identification
  status: String,             // "📸 Photos Taken", "📝 Need to Post", "✅ Listed on eBay"
  notes: String,              // Optional additional notes
  submittedBy: String,        // "Partner A (Shipping/Listing)" or "Partner B (Inventory Provider)"
  ebayItemNumber: String,     // eBay item number (added during editing)
  listPrice: String,          // List price on eBay (added during editing)
  shippingMethod: String,     // "eBay Standard Envelope", "USPS Ground", "UPS Ground", "FedEx Ground"
  uniqueId: String           // Auto-generated: "YYMMDD-SPO-###" (e.g., "251217-BAS-482")
}
```

### Key Functions

#### Core Application Functions (invintory.html:567-846)

1. **showTab(tabName, event)** - Tab navigation controller
   - Switches between intake, inventory, dashboard, and instructions tabs
   - Triggers appropriate render functions for each tab

2. **updateDashboard()** - Dashboard metrics calculator
   - Counts total items and items by status
   - Renders recent activity (last 10 entries)

3. **Form submission handler** (invintory.html:608-633)
   - Validates required fields
   - Creates new inventory item with timestamp
   - Saves to localStorage
   - Shows success message and clears form

4. **generateUniqueId()** - Unique ID generator
   - Format: `YYMMDD-SPO-###`
   - Example: `251217-BAS-482` (Dec 17, 2025, Baseball, random #482)

5. **renderInventory()** - Main inventory table renderer
   - Applies filters (status, sport)
   - Validates shipping rules
   - Shows shipping alerts for violations

6. **filterInventoryData()** - Inventory filter logic
   - Filters by status and/or sport
   - Returns filtered array for rendering

7. **editItem(index)** - Modal-based item editor
   - Creates dynamic modal overlay
   - Allows editing eBay item #, price, shipping, status
   - Does NOT allow editing core fields (sport, condition, description)

8. **saveEdit(index)** - Save edited item
   - Updates inventory array
   - Persists to localStorage
   - Closes modal and re-renders

9. **exportToCSV()** - CSV export function
   - Headers: Timestamp, Sport, Condition, Quantity Type, Quantity, Description, Status, Notes, Submitted By, eBay Item #, List Price, Shipping Method, Unique ID
   - Filename format: `card_inventory_YYYY-MM-DD.csv`

10. **importCSV(event)** - CSV import function
    - Reads CSV file with FileReader API
    - Parses and validates columns
    - Appends to existing inventory (does NOT replace)

11. **clearAllData()** - Nuclear option
    - Requires confirmation
    - Clears localStorage completely
    - Cannot be undone

### Business Rules & Validations

#### Shipping Rules (invintory.html:649-654)
The application enforces eBay shipping best practices:

1. **Graded cards CANNOT use eBay Standard Envelope**
   - Validation: `if (condition === 'Graded (in slab)' && shippingMethod === 'eBay Standard Envelope')`
   - Alert: "⚠️ CAN'T USE ENVELOPE"

2. **Lots of 5+ cards MUST use ground shipping**
   - Validation: `if (quantityType === 'Lot of 5+ cards' && shippingMethod !== 'USPS Ground')`
   - Alert: "⚠️ MUST USE GROUND"

#### Recommended Shipping by Value (documented in Instructions tab)
- Under $20 single raw card: eBay Standard Envelope ($0.69 cost, charge $1.25-1.99)
- Any graded card: USPS Ground minimum ($4.99)
- Lot of 5+ cards: USPS Ground minimum ($4.99)
- $20-99: USPS Ground ($4.99)
- $100+: Premium shipping options available

### Status Workflow

The application tracks three main statuses:

1. **📸 Photos Taken** - Cards photographed, ready for listing creation
2. **📝 Need to Post** - Photos done, needs eBay listing creation
3. **✅ Listed on eBay** - Live on eBay with item number

Status can be set during intake or changed during editing.

## Development Guidelines for AI Assistants

### When Making Changes

1. **Preserve the Single-File Architecture**
   - Do NOT split into multiple files unless explicitly requested
   - Do NOT add build tools, package.json, or dependencies
   - Keep all CSS inline in `<style>` tags
   - Keep all JavaScript inline in `<script>` tags

2. **Maintain localStorage Compatibility**
   - The key is always `'cardInventory'`
   - Data is stored as JSON string: `JSON.parse(localStorage.getItem('cardInventory'))`
   - Always validate that changes don't break existing user data
   - Consider migration logic if changing data schema

3. **Preserve Business Logic**
   - Shipping rules are critical for eBay compliance
   - Do NOT remove validation alerts
   - Do NOT change unique ID format without very good reason
   - Status emojis are part of the user experience

4. **Testing Considerations**
   - No automated tests exist (this is a single-file app)
   - Manual testing required for all changes
   - Test in browser with localStorage enabled
   - Test CSV import/export compatibility

### Common Modification Scenarios

#### Adding a New Form Field

1. Add to HTML form in intake section (lines 349-427)
2. Add to form submission handler data collection (lines 608-624)
3. Add to table header in inventory section (lines 459-473)
4. Add to renderInventory() row template (lines 655-672)
5. Add to exportToCSV() headers and data mapping (lines 742-760)
6. Add to importCSV() value mapping (lines 783-797)
7. Consider if it should be editable in editItem() modal (lines 693-729)

#### Adding a New Status

1. Add option to status select in intake form (lines 402-410)
2. Add option to status filter (lines 434-439)
3. Add CSS class in getStatusClass() if needed (lines 687-692)
4. Add to dashboard metrics if needed (lines 589-593)
5. Document in Instructions tab (lines 547-552)

#### Modifying Shipping Rules

1. Edit validation logic in renderInventory() (lines 649-654)
2. Update shipping rules documentation in Instructions tab (lines 536-544)
3. Update editItem() modal shipping options if needed (lines 703-709)

### File Naming Conventions

- Main file: `invintory.html` (note: intentional misspelling of "inventory")
- Exported CSV: `card_inventory_YYYY-MM-DD.csv` (uses ISO date format)

### Code Style Conventions

1. **JavaScript:**
   - Global `inventory` array (line 566)
   - Function declarations (no arrow functions for top-level)
   - Use `const` and `let`, avoid `var`
   - Event listeners in both HTML attributes and addEventListener
   - Template literals for HTML generation

2. **CSS:**
   - BEM-like naming: `.metric-card`, `.status-badge`, etc.
   - Responsive breakpoint at 768px (lines 307-334)
   - Gradient theme: `#667eea` to `#764ba2`
   - Status colors: photos=blue, needpost=orange, listed=green

3. **HTML:**
   - Semantic HTML5 tags
   - Inline event handlers (onclick, onchange, onsubmit)
   - Accessibility: labels, required attributes, help text

### LocalStorage Schema

```javascript
// Key: 'cardInventory'
// Value: JSON string of array of item objects
localStorage.getItem('cardInventory')
// Returns: "[{...}, {...}, ...]"

// Always access like this:
let inventory = JSON.parse(localStorage.getItem('cardInventory')) || [];

// Always save like this:
localStorage.setItem('cardInventory', JSON.stringify(inventory));
```

### Browser Compatibility

- Requires modern browser with ES6+ support
- Requires localStorage API
- Requires FileReader API (for CSV import)
- No IE11 support needed (uses template literals, let/const)

## Common User Workflows

### 1. Initial Card Intake
1. User sorts cards by sport physically
2. User fills out intake form with batch details
3. Form submits → item added to inventory → localStorage updated
4. Success message shown → form cleared → auto-navigate to inventory tab

### 2. Listing a Card on eBay
1. User navigates to inventory tab
2. User filters to "📸 Photos Taken" status
3. User clicks "Edit" on an item
4. User enters eBay item #, list price, shipping method
5. User changes status to "✅ Listed on eBay"
6. Modal saves → inventory updates → shipping alerts validate

### 3. Exporting for Backup
1. User navigates to inventory tab
2. User clicks "📥 Export to CSV"
3. Browser downloads `card_inventory_YYYY-MM-DD.csv`
4. User can open in Excel or Google Sheets

### 4. Importing Data
1. User prepares CSV with correct column headers
2. User clicks "📤 Import CSV"
3. File picker opens
4. JavaScript parses CSV and appends to inventory
5. Alert confirms success

## Error Handling

The application has minimal error handling:
- Form validation via HTML5 `required` attributes
- Confirmation dialogs for destructive actions (clearAllData)
- Try-catch is NOT used (errors will appear in console)
- CSV import assumes well-formed data

**When adding features:** Add proper error handling, especially for:
- localStorage quota exceeded
- Malformed CSV files
- Invalid data types in imported data

## Performance Considerations

- No pagination: All inventory items render at once
- Filter is client-side: Filters full array on every call
- No debouncing on filter changes
- Acceptable for small-to-medium datasets (< 1000 items)
- For larger datasets, consider virtual scrolling or pagination

## Security Considerations

- No user authentication (single-user, local-only)
- No server-side component (no XSS/CSRF concerns)
- CSV import could inject malicious content into DOM
- localStorage is domain-specific (safe from other sites)

**Note:** This is a trusted, local-only tool. Do NOT expose to internet without authentication.

## Deployment

**Current deployment model:** User opens `invintory.html` directly in browser

Options for deployment:
1. **File protocol:** `file:///path/to/invintory.html` (works offline)
2. **Simple HTTP server:** `python -m http.server` or `npx serve`
3. **GitHub Pages:** Upload and serve statically
4. **No compilation needed:** Just serve the HTML file as-is

## Git Workflow

- **Main branch:** Not specified in git status (appears to be new repo)
- **Current branch:** `claude/add-claude-documentation-Nm9Ta`
- **Push command:** `git push -u origin claude/add-claude-documentation-Nm9Ta`

### Branch Naming Convention
- Claude branches MUST start with `claude/` and end with matching session ID
- Example: `claude/add-claude-documentation-Nm9Ta`

## Future Enhancement Ideas

If users request improvements, consider:
- Dark mode toggle
- Bulk status updates (select multiple items)
- Sort columns in inventory table
- Search/filter by description text
- Photo attachment storage (base64 or external links)
- Profit calculation (list price - fees - shipping cost)
- Pagination for large inventories
- Undo/redo for edits
- Print view for physical inventory checks
- QR code generation for unique IDs
- Integration with eBay API for auto-listing

## Troubleshooting

### User reports data loss
- Check browser localStorage: `localStorage.getItem('cardInventory')`
- Verify user didn't clear browser data
- Check if user switched browsers/devices (localStorage is browser-specific)
- Recommend regular CSV exports as backups

### CSV export/import not working
- Check browser console for errors
- Verify CSV has correct headers (13 columns)
- Check for special characters causing parse errors
- Test with small sample file first

### Shipping alerts not showing
- Verify item has both condition and shippingMethod set
- Check renderInventory() validation logic (lines 649-654)
- Ensure CSS classes `.shipping-alert` exists

### Form won't submit
- Check browser console for JavaScript errors
- Verify all required fields are filled
- Check localStorage quota (rare but possible)

## Key Insights for AI Assistants

1. **Simplicity is intentional** - Do not over-engineer solutions
2. **Single-file design is a feature** - Users want portability
3. **No backend means no auth** - Assume single trusted user
4. **localStorage is the database** - Treat schema changes carefully
5. **Shipping rules are business-critical** - Validate thoroughly
6. **Partner workflow matters** - "Submitted By" field enables collaboration
7. **CSV is the backup strategy** - Export/import must always work
8. **Unique IDs are immutable** - Never change after creation
9. **Status workflow is linear** - Photos → Post → Listed (but can jump)
10. **Emojis are part of UX** - They make status instantly recognizable

## Questions to Ask Users Before Major Changes

1. "Do you want to maintain the single-file architecture, or should I split into separate files?"
2. "Do you have existing data in localStorage that I need to preserve?"
3. "Should new fields be editable after creation, or intake-only?"
4. "Do you want to keep CSV backward-compatible with old exports?"
5. "Should this change apply to existing items or new items only?"
6. "Do you need this to work offline, or is internet access okay?"

## Contact & Support

This is a self-contained project with no external dependencies or support channels. Users should:
- Keep regular CSV backups
- Test changes in a copy of the file first
- Use browser developer tools to debug issues
- Check localStorage directly if data appears missing

---

**Last Updated:** 2025-12-17
**Document Version:** 1.0
**Application Version:** Single file, no versioning
