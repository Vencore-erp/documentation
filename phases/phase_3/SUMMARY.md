# Phase 3: Quality Assurance & Logic Audits (IN PROGRESS)

## Objective
Deep dive into code quality, identify potential runtime errors, and fix logical bugs in critical paths.

## Key Fixes & Audits

### 1. Fixed Infinite Recursion (StackOverflowError)
**Severity:** Critical 🔴
**Description:** Bidirectional JPA relationships (Parent <-> Child) combined with Lombok's `@Data` annotation cause infinite recursion during `toString()` or JSON serialization.
**Affected Entities:**
*   `ProcurementRequest` (Parent)
*   `ProcurementItem` (Child)
*   `DeliveryDetail` (Child)
*   `AdditionalDocument` (Child)
**Solution Applied:**
*   Removed `@Data`.
*   Used `@Getter`, `@Setter`, and `@ToString(exclude = "...")`.
*   Explicitly excluded child lists from Parent's `toString()` and parent reference from Child's `toString()`.

### 2. Fixed Data Integrity in Update Logic
**Severity:** High 🟠
**Description:** `ProcurementRequestService.updateProcurementRequest` failed to correctly handle the `items` list update, potentially leaving orphaned data or throwing errors.
**Solution Applied:**
*   Refactored update logic to explicitly clear the old list.
*   Re-mapped DTOs to new Entity instances.
*   **Crucial:** Manually re-set the `procurementRequest` (Parent) reference on new Items before saving.

### 3. Cleaned Exception Handling
**Description:** Found duplicate `GlobalExceptionHandler` classes in `procurement-service`.
**Solution:** Deleted the incomplete/duplicate handler to prevent ambiguity in error responses.

## Future Steps
*   **Unit Tests:** Create tests for `ProcurementRequestService` to verify the rewrite.
*   **Integration Tests:** Verify flow from Gateway -> Auth -> Procurement.
