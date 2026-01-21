# Changelog

All notable changes to this project will be documented in this file.

## [3.0.19+1] - 2026-01-21

### Added

#### RFID Check Feature
- **New RFID Check Workflow**: Implemented complete RFID Check feature for validating product quantities using RFID scans
  - Scan Transfer or Package barcode to load move lines
  - Display only products with `has_rfid = true`
  - Scan EPCs using internal RFID reader with trigger control
  - Validate scanned quantities against required quantities
  - Display validation results with status (OK, INCOMPLETE, EXCESS)
  - Re-scanning logic that ignores previously synced EPCs

- **Backend Integration**:
  - Extended `stock.rfid.session` model with `rfid_check` session type
  - Added `rfid_check_picking_id` field to link sessions to pickings
  - Added `rfid_qty` field to `stock.move.line` to track RFID-scanned quantities
  - Added `has_rfid` computed field to `product.product`
  - New Odoo methods: `get_rfid_move_lines()` and `process_rfid_tags()`

- **SQLite Caching**:
  - Added `rfid_check_scanned_epcs` table for temporary caching of scanned EPCs
  - Cache is cleared when user exits the screen or after successful sync
  - Supports offline functionality and re-scanning logic

- **State Management**:
  - Created `RfidCheckProvider` for managing RFID Check workflow state
  - Integrated with existing RFID sessions to retrieve previously validated EPCs
  - Optimized UI performance using `Selector` widgets for fine-grained rebuilds

- **UI Components**:
  - Reused `ProductCard` widget for consistent product display
  - Added RFID power control settings dialog
  - Real-time tag count display during scanning
  - Validation results view with status indicators

### Fixed

#### ProductCard Widget Compatibility
- **Provider Dependency Issue**: Fixed `ProductCard` widget to work without `MoveLinesProvider`
  - Made `MoveLinesProvider` access optional with try-catch handling
  - Added fallback to use `moveLine` data directly when provider is unavailable
  - Fixed quantity display to use `rfid_qty` from moveLine data
  - Made undo functionality conditional (disabled when provider not available)
  - Fixed pack info chip to handle missing provider gracefully
  - Fixed image loading to work with both `MoveLinesProvider` and `RfidCheckProvider`

#### RFID Check Screen
- **Barcode Scanning Control**:
  - Disabled barcode scanning after successfully loading items
  - Only RFID trigger works after items are loaded
  - Barcode scanning re-enabled only on reset

- **Trigger-Controlled Scanning**:
  - Changed from 6-second auto-stop to trigger-controlled scanning
  - Press trigger to start scanning, release to stop
  - Real-time tag count display during scanning
  - "Check" button disabled while actively scanning

- **Dispose Error Fix**:
  - Fixed "provider used after being disposed" error
  - Store picking info in state variables
  - Clear cache directly using `TransferStorageService` instead of calling provider methods
  - Prevents errors when widget is disposed

#### Cycle Count Scan Screen
- **Dispose Error Fix**: Added `mounted` check before calling `setState()` in `_stopScanning()` method
  - Prevents errors when Timer callback fires after widget disposal
  - Ensures safe state updates in async callbacks

### Changed

#### State Management Optimizations
- Replaced `Consumer` widgets with `Selector` widgets in RFID Check screen
  - Only specific widgets rebuild when their dependent state changes
  - Prevents full widget tree rebuilds during rapid RFID scanning
  - Improved UI performance and eliminated lag

#### Navigation Service
- Improved logout navigation reliability
  - Store `StackRouter` instance directly instead of `BuildContext`
  - Capture router instance from widgets within router tree
  - Ensures valid router context for navigation operations

### Technical Details

#### Files Modified
- `lib/features/rfid/check/rfid_check_screen.dart` - Main RFID Check screen implementation
- `lib/providers/rfid_check_provider.dart` - State management for RFID Check
- `lib/features/operations/batch_transfer/widgets/product_card.dart` - Made provider-agnostic
- `lib/core/storage/transfer_storage_service.dart` - Added RFID Check EPC caching
- `lib/data/services/odoo_service.dart` - Added RFID Check API methods
- `lib/features/rfid/cycle_count/cycle_count_scan_screen.dart` - Fixed dispose error
- `lib/core/navigation/navigation_service.dart` - Improved router handling
- `lib/main.dart` - Router instance management

#### Backend Files (Odoo)
- `cds_rfid_check_mobile/models/stock_rfid_session.py` - Extended session model
- `cds_rfid_check_mobile/models/stock_move_line.py` - Added `rfid_qty` field
- `cds_rfid_check_mobile/models/stock_picking.py` - Added RFID Check methods
- `cds_rfid_check_mobile/models/product_product.py` - Added `has_rfid` field

---

## Previous Versions

[Previous changelog entries would go here]
