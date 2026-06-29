---
uid: pcg-pallet-shipping-comprehensive-user-guide
title: PCG Pallet Shipping – Comprehensive User Guide
---

# PCG Pallet Shipping – Comprehensive User Guide

**Publisher:** Technology Management  
**Version:** 27.3.0  
**Platform:** Microsoft Dynamics 365 Business Central (Cloud)  
**Last Updated:** May 2026

---

## Table of Contents

1. [Introduction](#introduction)
2. [Key Features](#key-features)
3. [Prerequisites and Requirements](#prerequisites-and-requirements)
4. [Initial Setup](#initial-setup)
5. [Daily Operations](#daily-operations)
6. [Advanced Features](#advanced-features)
7. [Monitoring and Reporting](#monitoring-and-reporting)
8. [Troubleshooting](#troubleshooting)
9. [Best Practices](#best-practices)
10. [Frequently Asked Questions](#frequently-asked-questions)
11. [Support and Resources](#support-and-resources)

---

## Introduction

### What is PCG Pallet Shipping?

PCG Pallet Shipping is a Business Central extension that revolutionizes warehouse shipment processing by enabling warehouse staff to organize shipment lines onto physical pallets, track them through scanning, and post shipments on a per-pallet basis. This granular control improves warehouse efficiency, reduces errors, and provides complete traceability throughout the shipping process.

### Business Benefits

- **Improved Accuracy:** Physical pallet scanning ensures correct items are shipped
- **Enhanced Traceability:** Complete audit trail from pallet assignment to shipment posting
- **Operational Efficiency:** Post shipments incrementally as pallets are ready
- **Multi-Shipment Support:** Single pallets can span multiple warehouse shipments
- **Compliance Ready:** Device scanning and logging supports regulatory requirements
- **Integration Ready:** Seamless connection with carrier systems for label generation

### How It Works

The extension integrates with Business Central's standard warehouse shipment process, adding three key capabilities:

1. **Pallet Assignment:** Group shipment lines onto numbered pallets, even across multiple warehouse shipments
2. **Device Scanning:** Optional WMS+ device validation before posting
3. **Pallet-Based Posting:** Ship one pallet at a time with full logging for each affected shipment

---

## Key Features

### Core Functionality

- **Automatic Pallet Numbering:** Generates unique pallet numbers from configurable number series
- **Multi-Line Assignment:** Assign multiple shipment lines to a single pallet in one operation
- **Multi-Shipment Pallets:** A single pallet can contain lines from multiple warehouse shipments
- **Device Integration:** Leverage WMS+ handheld devices for barcode scanning
- **Flexible Posting:** Post individual pallets with automatic handling of all associated warehouse shipments
- **Visual Indicators:** Green highlighting shows which lines have been assigned to pallets

### Tracking and Audit

- **Pallet Shipment Entries:** Detailed record of every line-to-pallet assignment
- **Pallet Shipment Log:** Individual log entry for each warehouse shipment on a pallet
- **Device Tracking:** Records which device scanned each pallet and when
- **User Attribution:** Tracks who created entries and performed postings

### Carrier Integration

- **Shipping Agent Integration:** Connects with Clever Shipping Agent Integration
- **Automatic Label Generation:** Creates carrier labels based on shipping method
- **Tracking Number Storage:** Preserves tracking numbers with pallet entries
- **Multi-Carrier Support:** Works with DHL, FedEx, UPS, Royal Mail, and others

---

## Prerequisites and Requirements

### System Requirements

Before installing PCG Pallet Shipping, ensure your environment meets these requirements:

#### Required Extensions

The following Clever Dynamics extensions **must** be installed:

| Extension | Minimum Version | Purpose |
|---|---|---|
| **Clever Device Framework** | 8.0.2.132706 | Provides device infrastructure |
| **Clever WMS Devices** | 8.0.5.139213 | Enables handheld device scanning |
| **Clever Shipping Agent Integration** | 9.0.0.134016 | Manages carrier label generation |

#### Business Central Version

- **Platform:** Business Central Cloud
- **Application Version:** 27.3.0.0 or later

### User Permissions

Users working with pallet shipping must have:

- **PCG Pallet Shipping** permission set (grants minimum required access)
- Standard warehouse permissions for creating and posting warehouse shipments
- WMS+ device permissions if scanning functionality is used

### Data Setup Requirements

Before initial use, you must configure:

1. A **Number Series** for generating pallet numbers
2. At least one **Location** configured for warehouse management
3. **Shipping Agents** and **Shipping Methods** if using carrier integration

---

## Initial Setup

### Step 1: Configure Number Series

1. Search for **No. Series** and open the page
2. Create a new number series for pallets (e.g., **PALLET**)
3. Configure the series:
   - **Starting No.:** PAL00001
   - **Ending No.:** PAL99999
   - **Increment-by No.:** 1
   - Enable **Default Nos.** checkbox
4. Save the number series

### Step 2: Access Sales & Receivables Setup

1. Use the search function (Alt + Q) and type **Sales & Receivables Setup**
2. Open the **Sales & Receivables Setup** page
3. Scroll down to locate the **Ship by Pallet** section

### Step 3: Configure Default Quantity to Ship (Critical)

**IMPORTANT:** This step must be completed first.

1. Locate the **Default Quantity to Ship** field near the top of the setup page
2. Change the value to **Remainder**
3. **Why this matters:** When Ship by Pallet is enabled, the extension controls quantity to ship values per pallet. The system must start with the full remainder quantity, which is then adjusted by pallet assignments.

> [!NOTE]
> The **Default Quantity to Ship** field cannot be changed while Ship by Pallet is enabled. You must disable the feature first, make the change, then re-enable it.

### Step 4: Configure Ship by Pallet Settings

In the **Ship by Pallet** section, configure these fields:

#### Enable Ship by Pallet
- **Purpose:** Master switch that activates pallet shipping functionality
- **Setting:** Turn **ON** to enable the feature
- **Impact:** When enabled, the system displays pallet fields on warehouse shipments and enables pallet-based posting

#### Ship by Pallet Device ID Required
- **Purpose:** Controls whether WMS+ device scanning is mandatory before posting
- **Default:** ON (recommended for most warehouses)
- **When to disable:** If you don't have WMS+ devices or want manual pallet posting without scanning

#### Pallet Nos.
- **Purpose:** Specifies which number series generates pallet numbers
- **Setting:** Select the number series you created in Step 1 (e.g., **PALLET**)
- **Impact:** Each new pallet assignment will consume the next number from this series

### Step 5: Verify Configuration

After completing setup:

1. The **Default Quantity to Ship** field should show **Remainder**
2. The **Enable Ship by Pallet** checkbox should be **enabled**
3. The **Pallet Nos.** field should reference your pallet number series
4. If **Ship by Pallet Device ID Required** is enabled, ensure WMS+ devices are configured

**Visual Check:** If the **Default Quantity to Ship** field appears in red, it indicates the value is not set to **Remainder** and must be corrected before proceeding.

---

## Daily Operations

### Creating a Warehouse Shipment

Warehouse shipments are created using Business Central's standard process. PCG Pallet Shipping extends this process but does not change the initial creation workflow.

1. Search for **Warehouse Shipments**
2. Create a new shipment from sales orders using **Get Source Documents** or other standard methods
3. The shipment lines will appear on the **Lines** FastTab

At this stage, no pallet assignments exist. Lines appear in normal formatting.

### Assigning Lines to Pallets

#### Process Overview

Pallet assignment groups shipment lines onto a physical pallet and generates a unique pallet number for tracking. **New in Version 27.3:** A single pallet can now span multiple warehouse shipments, allowing for more flexible consolidation across shipments from different locations or customers.

#### Standard Assignment

1. Open the **Warehouse Shipment** you want to process
2. On the **Lines** subform, select one or more lines that should ship on the same pallet
   - **Tip:** Use Ctrl+Click to select multiple non-consecutive lines
   - **Tip:** Use Shift+Click to select a range of consecutive lines
3. Choose **Actions > Functions > Assign Pallet No.**
4. The system automatically:
   - Generates the next pallet number from your configured series
   - Creates a Pallet Shipment Entry for each selected line
   - Records the quantity, item, location, warehouse shipment, and user details
   - Updates the **Pallet Entries** column on each line
5. A confirmation message displays: *"Pallet [X] assigned to [N] line(s)"*
6. Lines with pallet entries are now **highlighted in green**

#### Multi-Pallet Scenarios

If your shipment requires multiple pallets:

1. Assign the first set of lines to Pallet 1 (as described above)
2. Select a different set of lines
3. Run **Assign Pallet No.** again – a new pallet number is generated
4. Repeat for additional pallets as needed

**Each pallet gets a unique number and can be posted independently.**

#### Multi-Shipment Pallets (NEW)

**Version 27.3 introduces the ability to assign the same pallet number to lines from different warehouse shipments.** This is useful when:

- Consolidating shipments from multiple locations onto one physical pallet
- Grouping orders from the same customer that span multiple warehouse shipments
- Optimizing freight by combining partial shipments

**To create a multi-shipment pallet:**

1. Open **Warehouse Shipment #1** and assign lines to a pallet (e.g., PAL00001)
2. Open **Warehouse Shipment #2** 
3. Select lines that should ship on the same physical pallet
4. Manually enter **PAL00001** in the **Pallet No.** field instead of generating a new number
5. The system validates the pallet exists and creates entries linking both shipments to the same pallet

> [!IMPORTANT]
> When posting a multi-shipment pallet, the system posts **all warehouse shipments** associated with that pallet. Each warehouse shipment receives its own **Pallet Shipment Log** entry. The posting operation is **all-or-nothing**: all shipments must post successfully or the entire operation fails.

#### Viewing Pallet Entries

- The **Pallet Entries** column shows how many pallet entries are assigned to each line
- Click the number to drill down and view the detailed pallet entry records
- You can see the pallet number, warehouse shipment number, quantities, device scan status, and posting status

### Scanning Pallets with WMS+ Devices

If **Ship by Pallet Device ID Required** is enabled in setup, pallets must be scanned before posting.

#### Scanning Workflow

1. After assigning lines to pallets, warehouse staff uses a WMS+ handheld device
2. The device scans the pallet barcode or manually enters the pallet number
3. The Clever WMS Devices integration updates **all pallet entries** for that pallet number:
   - **Device ID:** Records which device performed the scan
   - **Scanned Date/Time:** Records the timestamp of the scan
4. Once scanned, the pallet is validated and ready for posting

**Multi-Shipment Note:** When scanning a pallet that spans multiple warehouse shipments, a single scan validates all entries across all shipments for that pallet.

#### Scan Verification

To verify a pallet has been scanned:

1. Open any shipment line associated with the pallet
2. Click the **Pallet Entries** count to drill down
3. Check that **Device ID** and **Scanned Date/Time** fields are populated
4. If these fields are empty, the pallet has not been scanned

#### Manual Override

If you need to post without scanning:

1. Go to **Sales & Receivables Setup**
2. Disable **Ship by Pallet Device ID Required**
3. Post the pallet(s)
4. Re-enable the setting afterward to restore scan enforcement

> [!WARNING]
> Only administrators should have permission to modify this setup option.

### Posting Shipments by Pallet

#### Single-Shipment Pallet Posting

1. Open the **Warehouse Shipment** with assigned pallets
2. Choose **Actions > Processing > Ship by Pallet**
   - This action is only available when at least one pallet entry exists
3. The system automatically:
   - Selects the first available (unposted) pallet
   - Validates device scan requirements (if enabled)
   - Sets **Qty. to Ship** for all lines on that pallet
   - Posts the lines for that pallet
   - Creates a Pallet Shipment Log entry
   - Marks the pallet entries as posted
4. A confirmation message displays: *"Pallet [X] has been shipped successfully on warehouse shipment [Y]"*

#### Multi-Shipment Pallet Posting (NEW)

When posting a pallet that spans multiple warehouse shipments, the system handles all shipments automatically:

1. From **any** warehouse shipment containing the pallet, choose **Actions > Processing > Ship by Pallet**
2. The system:
   - Identifies **all warehouse shipments** associated with the pallet
   - Validates device scan requirements for all entries (if enabled)
   - Sets **Qty. to Ship** for all lines across all affected shipments
   - Posts **each warehouse shipment** in sequence
   - Creates a **separate Pallet Shipment Log entry** for each warehouse shipment
   - Marks all pallet entries as posted
3. A confirmation message displays: *"Pallet [X] has been shipped successfully. All [N] warehouse shipment(s) posted."*

> [!CAUTION]
> Posting is **all-or-nothing**: if any warehouse shipment fails to post, the entire pallet operation fails. No partial posting across shipments is supported. If an error occurs, review the error message, correct the issue, and retry the entire pallet posting.

#### Posting Multiple Pallets

To post all pallets on a shipment:

1. Run **Ship by Pallet** for the first pallet
2. After successful posting, run **Ship by Pallet** again for the next pallet
3. Continue until all pallets are posted

**Each pallet posts independently**, allowing you to:
- Post one pallet immediately while preparing others
- Hold specific pallets if issues are discovered
- Stage partial shipments based on carrier pickup schedules

#### What Happens During Posting

When you post a pallet, the system performs these operations:

1. **Pallet Discovery:**
   - Identifies the first unposted pallet on the current shipment
   - Queries for **all warehouse shipments** associated with that pallet number

2. **Validation (per shipment):**
   - Checks scan requirements are met for all entries
   - Verifies quantities are greater than zero
   - Confirms carrier label requirements (if applicable)

3. **Quantity Assignment (per shipment):**
   - Reads quantity from each Pallet Shipment Entry
   - Sets **Qty. to Ship** on corresponding warehouse shipment line
   - Only lines for the selected pallet are affected per shipment

4. **Standard BC Posting (per shipment):**
   - Business Central's standard shipment posting process executes for each warehouse shipment
   - Sales shipment and item ledger entries are created
   - Inventory is reduced at each location

5. **Audit Trail:**
   - Pallet entries are marked as **Posted** across all shipments
   - A **Pallet Shipment Log** entry is created for **each warehouse shipment**:
     - Pallet No. + Warehouse Shipment No. form composite primary key
     - Success status recorded per shipment
     - Number of lines posted per shipment
     - User and timestamp
     - Device ID used for scanning

6. **Posted Documents:**
   - Each Posted Warehouse Shipment preserves:
     - Pallet number on each line
     - Device ID and scan timestamp
     - Link to Pallet Shipment Log entries

---

## Advanced Features

### Carrier Label Generation

PCG Pallet Shipping integrates with the Clever Shipping Agent Integration extension to automatically generate carrier shipping labels and tracking numbers.

#### Configuration

1. Ensure **Clever Shipping Agent Integration** extension is installed
2. Configure your shipping agents in Business Central (search for **Shipping Agents**)
3. For each agent, set up the carrier credentials and API connection following Clever Dynamics documentation
4. Configure shipping methods that require labels

#### How Labels Are Generated

1. When you post a pallet using **Ship by Pallet**, the system checks if the shipping method requires carrier labels
2. If yes, for each pallet line being posted:
   - The system calls the Clever Shipping Agent Integration
   - A consignment is created with the carrier
   - The carrier API returns a tracking number
   - The tracking number is stored in the Pallet Entry
3. Track numbers are visible on:
   - Posted warehouse shipment lines
   - Pallet entries
   - Pallet shipment log

#### Supported Carriers

The Clever Shipping Agent Integration supports multiple carriers including:
- DHL
- FedEx
- UPS
- Royal Mail
- TNT
- And others (see Clever Dynamics documentation)

#### Retrieving Tracking Numbers

**From Warehouse Shipment Line:**

1. Open Posted Warehouse Shipment
2. The "Package Tracking No." field shows the tracking number
3. Click "Pallet Log Entries" to see complete posting history

**From Pallet Entries:**

1. Search for "Pallet Entries"
2. Filter by pallet number or shipment number
3. View "Consignment No." field for tracking numbers

### Multi-Customer Shipments

When a warehouse shipment contains lines for multiple customers, each customer's items can be grouped onto separate pallets:

1. Assign Customer A's lines to Pallet 1
2. Assign Customer B's lines to Pallet 2
3. Post each pallet independently
4. Each customer receives a separate shipment with distinct tracking

This ensures proper segregation and accurate customer-specific documentation.

### Cross-Location Consolidation (NEW)

**Version 27.3 enables consolidating shipments from different locations onto a single pallet.** Common scenarios:

**Scenario 1: Multi-Location Order Fulfillment**
- Customer orders items that stock at two different warehouses
- Business Central creates two warehouse shipments (one per location)
- Warehouse assigns both shipments to the same pallet number
- Posting the pallet automatically posts both warehouse shipments
- Customer receives one consolidated shipment

**Scenario 2: Same-Day Pickup Optimization**
- Carrier pickup scheduled for 3 PM
- Multiple warehouse shipments ready for the same customer
- Combine onto one pallet to reduce freight costs
- Post as a single pallet operation

**Best Practices:**
- Ensure carrier supports multi-origin shipments if crossing locations
- Verify shipping costs are calculated correctly for consolidated pallets
- Use distinct pallet numbers for different carriers or pickup times

### Pallet Quantity Management

#### Splitting Quantities Across Pallets

If a single shipment line has a large quantity that spans multiple pallets:

**Option 1: Manual Split**
1. Split the warehouse shipment line into multiple rows (use standard BC line splitting)
2. Assign each split line to a different pallet
3. Post pallets individually

**Option 2: Multiple Entries**
Currently, each shipment line can have multiple pallet entries, but posting behavior ties to the full line quantity.

> [!TIP]
> For large quantities spanning pallets, split the warehouse shipment line first to maintain clear pallet-to-line relationships.

### Inheritance and Pallet Fields

When pallet assignments are posted:

- **Pallet No.** is copied from the Pallet Entry to the Posted Warehouse Shipment Line
- **Device ID** and **Scanned Date/Time** are preserved
- **Warehouse Shipment No.** is stored in the log to support multi-shipment pallets
- These fields are read-only on posted documents
- They provide permanent traceability of the physical shipment process

---

## Monitoring and Reporting

### Pallet Entries Page

**Purpose:** Real-time view of all pallet assignments across all shipments

**How to Access:**
- Search for **Pallet Entries** and open the list page

**Key Information:**

| Field | Description |
|---|---|
| **Pallet No.** | Unique identifier for the pallet |
| **Warehouse Shipment No.** | Source shipment document |
| **Line No.** | Shipment line this entry represents |
| **Item No.** | The item being shipped |
| **Quantity** | Amount assigned to this pallet |
| **Location Code** | Shipment location |
| **Device ID** | Scanner device that validated this pallet |
| **Scanned Date/Time** | When scanning occurred |
| **Posted** | Checkmark indicates this entry has been posted |
| **Created Date/Time** | When the pallet assignment was made |
| **Created By** | User who assigned the pallet |

**Common Filters:**
- Filter by **Posted = No** to see pending pallets
- Filter by **Warehouse Shipment No.** to see all pallets for a specific shipment
- Filter by **Pallet No.** to see all lines on a specific pallet (including multi-shipment entries)
- Filter by **Created By** to see pallets assigned by a specific user

**Multi-Shipment View:**
- Filter by **Pallet No.** to see all entries across all warehouse shipments for that pallet
- Group by **Warehouse Shipment No.** to see distribution across shipments

### Pallet Shipment Log

**Purpose:** Historical record of all pallet posting operations

**How to Access:**
- Search for **Pallet Shipment Log** and open the list page
- From a Posted Warehouse Shipment line, click **Pallet Log Entries** count

**Key Information:**

| Field | Description |
|---|---|
| **Pallet No.** | The pallet that was posted |
| **Warehouse Shipment No.** | Specific warehouse shipment document |
| **Status** | Success / Failed |
| **Posting Date/Time** | When the posting occurred |
| **Device ID** | Scanner device used (if any) |
| **User ID** | Who performed the posting |
| **Lines Posted Count** | Number of lines successfully posted |
| **Error Message** | Details if posting failed |

**Status Values Explained:**

- **Success:** All lines on the pallet for this warehouse shipment posted without errors
- **Failed:** The entire pallet posting operation failed (no shipments were posted)

**Important for Multi-Shipment Pallets:**
- Each warehouse shipment receives its own log entry
- The composite primary key is **(Pallet No. + Warehouse Shipment No.)**
- Filter by **Pallet No.** to see all log entries for a multi-shipment pallet
- All log entries for a pallet will have the same **Posting Date/Time** since posting is atomic

**Using the Log for Troubleshooting:**

1. Filter by **Status = Failed** to find problem postings
2. Review the **Error Message** field for specific issues
3. Check the **Posting Date/Time** to correlate with warehouse activity
4. Cross-reference **User ID** if investigating user-specific issues
5. For multi-shipment pallets, ensure all expected warehouse shipment log entries exist

### Posted Warehouse Shipments

After posting, pallet information is preserved on the Posted Warehouse Shipment for permanent reference.

**Key Fields Added:**

- **Pallet No.:** Shows which pallet this line was shipped on
- **Device ID:** The scanner device used
- **Scanned Date/Time:** When scanning occurred
- **Pallet Log Entries:** Count of log entries (clickable to drill down)

**Special Formatting:**
- Lines with pallet numbers are highlighted in **green**
- This visual indicator makes it easy to identify pallet-shipped items

---

## Troubleshooting

### Common Issues and Solutions

#### Issue: "Ship by Pallet is not enabled"

**Cause:** The master feature switch is disabled in Sales & Receivables Setup

**Solution:**
1. Open **Sales & Receivables Setup**
2. Verify **Default Quantity to Ship** is set to **Remainder**
3. Enable **Enable Ship by Pallet**
4. Save changes

---

#### Issue: "Default Quantity to Ship must be set to Remainder"

**Cause:** The **Default Quantity to Ship** field is not configured correctly

**Solution:**
1. If **Enable Ship by Pallet** is currently on, disable it temporarily
2. Change **Default Quantity to Ship** to **Remainder**
3. Re-enable **Enable Ship by Pallet**
4. Save changes

**Prevention:** This field will appear in **red** on the setup page when set incorrectly while the feature is enabled.

---

#### Issue: "Pallet X cannot be posted because one or more lines have not been scanned"

**Cause:** Device ID scanning is required but some/all lines on the pallet are missing scan data

**Solutions:**

**Option A – Scan the Pallet (Recommended):**
1. Use a WMS+ device to scan the pallet barcode
2. Verify the scan by checking Pallet Entries for Device ID and Scanned Date/Time
3. Retry posting

**Option B – Disable Scan Requirement (Temporary):**
1. Open **Sales & Receivables Setup**
2. Disable **Ship by Pallet Device ID Required**
3. Post the pallet
4. Re-enable the setting
5. **Risk:** Bypasses physical validation

**Multi-Shipment Note:** All entries across all warehouse shipments for the pallet must be scanned. Check entries across all shipments for the pallet number.

---

#### Issue: "No lines are selected"

**Cause:** The **Assign Pallet No.** action was run without selecting any shipment lines

**Solution:**
1. Click on a shipment line to select it (click in the left margin)
2. Hold Ctrl and click additional lines to select multiple
3. Run **Assign Pallet No.** again

---

#### Issue: "Pallet X cannot be posted on warehouse shipment Y because all lines have a Qty. to Ship of zero"

**Cause:** The pallet shipment entries for this pallet on this specific warehouse shipment have zero quantities recorded

**Solution:**
1. Open **Pallet Entries** and filter by pallet number and warehouse shipment number
2. Review the **Quantity** field on each entry
3. If quantities are incorrect, delete the pallet entries and reassign:
   - Click **Delete** on each erroneous entry
   - Return to the warehouse shipment
   - Select the lines again
   - Run **Assign Pallet No.** to create new entries

**Common Cause:** Lines were modified after pallet assignment (e.g., quantity changed on the shipment)

---

#### Issue: "The Ship by Pallet action is greyed out"

**Cause:** No pallet entries exist on the current warehouse shipment

**Solution:**
1. Assign at least one shipment line to a pallet using **Assign Pallet No.**
2. The **Ship by Pallet** action will become enabled

---

#### Issue: Multi-shipment pallet posts one shipment but fails on the second (NEW)

**Cause:** After version 27.3 upgrade, the system uses all-or-nothing posting for multi-shipment pallets

**Behavior:**
- The entire pallet posting operation fails
- No warehouse shipments are posted (transaction is rolled back)
- An error message displays the cause of failure

**Solution:**
1. Review the error message to identify which warehouse shipment or line caused the failure
2. Correct the issue on the problematic shipment:
   - Verify quantities are correct
   - Check carrier label configuration if applicable
   - Ensure pick registration is complete
3. Retry posting the pallet from any associated warehouse shipment

**Prevention:**
- Validate all warehouse shipments associated with a pallet before posting
- Use **Ship by Pallet** test posting if available
- Ensure pick registration is complete for all locations

---

#### Issue: Carrier label generation fails

**Cause:** Missing Clever Shipping Agent Integration or carrier configuration issues

**Solutions:**

1. **Verify Extension Installation:**
   - Search for **Extension Management**
   - Confirm **Clever Shipping Agent Integration** is installed and enabled

2. **Check Carrier Configuration:**
   - Open **Shipping Agents**
   - Verify credentials are configured for the carrier
   - Test connection using Clever Dynamics tools

3. **Review Shipping Method Setup:**
   - Ensure the shipping method on the warehouse shipment is linked to the correct carrier
   - Verify **Require Package Tracking** is enabled if mandatory

4. **Multi-Shipment Note:**
   - If posting a multi-location pallet, ensure each location is configured for carrier integration
   - Verify the same carrier supports pickup from all involved locations

---

#### Issue: Cannot find Pallet Shipment Log entry for a specific warehouse shipment (NEW)

**Cause:** Looking for log entries using only pallet number without warehouse shipment filter

**Solution:**
1. Open **Pallet Shipment Log**
2. Filter by **both** Pallet No. **and** Warehouse Shipment No.
3. For multi-shipment pallets, you should see separate log entries for each warehouse shipment

> [!NOTE]
> The primary key changed in version 27.3 to support multiple warehouse shipments per pallet. Use the composite key (Pallet No. + Warehouse Shipment No.) for lookups.

---

## Best Practices

### Pallet Organization

1. **Single Customer per Pallet:** Group all items for one customer on a single pallet when possible
2. **Weight Distribution:** Balance weight across pallets to meet carrier requirements
3. **Fragile Items:** Assign fragile items to dedicated pallets to prevent damage
4. **Multi-Shipment Planning:** Use multi-shipment pallets strategically for cost optimization, not as default

### Scanning Discipline

1. **Scan Before Shipping:** Always scan pallets before posting to ensure physical accuracy
2. **Device Assignment:** Assign specific devices to warehouse zones for traceability
3. **Re-scan Policy:** Implement re-scanning if a pallet is modified after initial scan

### Posting Strategy

1. **Post Incrementally:** Post pallets as they're ready rather than batching at day-end
2. **Review Before Posting:** Check pallet entries before posting to catch quantity errors
3. **Multi-Shipment Validation:** For multi-shipment pallets, verify all warehouse shipments are ready before posting
4. **Error Handling:** Develop a clear process for handling failed postings, especially for multi-shipment scenarios

### Carrier Integration

1. **Test Connectivity:** Verify carrier API connectivity before peak shipping times
2. **Label Printing:** Print labels immediately after posting while systems are responsive
3. **Backup Procedures:** Have manual label generation procedures for carrier API outages

### Multi-Shipment Pallet Best Practices (NEW)

1. **Same Customer Consolidation:** Ideal for combining shipments from different locations for the same customer
2. **Same Carrier Requirement:** Ensure all warehouse shipments on a pallet use the same carrier
3. **Same Pickup Time:** Schedule all locations for the same carrier pickup window
4. **Cost Verification:** Verify freight costs are calculated correctly for multi-origin shipments
5. **Documentation:** Document why multi-shipment pallets were created for audit purposes
6. **Readiness Check:** Confirm all warehouse shipments are fully picked before creating multi-shipment pallets

---

## Frequently Asked Questions

### General Questions

**Q: Can I assign the same shipment line to multiple pallets?**  
A: Each shipment line can have multiple pallet entries, but the posting behavior is tied to the full line quantity. For best results, split the warehouse shipment line first.

**Q: What happens if I delete a pallet entry?**  
A: The pallet entry is removed, but the warehouse shipment line remains unchanged. You can reassign the line to a different pallet.

**Q: Can I change a pallet number after assignment?**  
A: Delete the existing pallet entries and create new ones with the correct pallet number. This is only possible before posting.

**Q: Can I post a warehouse shipment normally (without pallets)?**  
A: Yes, but only if no lines have been assigned to pallets. Once pallet entries exist, you must use **Ship by Pallet**.

### Multi-Shipment Questions (NEW)

**Q: How do I know if a pallet spans multiple warehouse shipments?**  
A: Filter **Pallet Entries** by the pallet number and check the **Warehouse Shipment No.** column. Multiple distinct values indicate a multi-shipment pallet.

**Q: Can I post just one warehouse shipment from a multi-shipment pallet?**  
A: No. The system posts all warehouse shipments for the pallet in an all-or-nothing operation.

**Q: What happens if one warehouse shipment fails during multi-shipment posting?**  
A: The entire pallet posting operation fails, and no warehouse shipments are posted. Correct the error and retry.

**Q: Can pallets span different customers?**  
A: Technically yes, but this is not recommended. Separate pallets for different customers ensures proper shipment documentation and tracking.

**Q: Do all warehouse shipments on a multi-shipment pallet need the same carrier?**  
A: It's strongly recommended. Using different carriers per shipment on the same pallet will cause carrier label generation issues.

**Q: How many log entries are created for a multi-shipment pallet?**  
A: One log entry per warehouse shipment. A pallet spanning 3 shipments creates 3 separate Pallet Shipment Log entries.

### Device and Scanning Questions

**Q: What if my WMS device is offline?**  
A: Temporarily disable **Ship by Pallet Device ID Required** in setup, post manually, then re-enable scanning afterward.

**Q: Can I scan pallets from different warehouse shipments with one scan?**  
A: Yes. Scanning a pallet number updates all pallet entries for that pallet across all warehouse shipments.

**Q: Does the scan timestamp match the posting timestamp?**  
A: No. The **Scanned Date/Time** reflects when the device scanned the pallet. The **Posting Date/Time** in the log reflects when posting occurred.

### Carrier Integration Questions

**Q: What if carrier label generation fails mid-posting?**  
A: The posting operation fails and rolls back. Review the error message, correct carrier configuration, and retry.

**Q: Where do I find tracking numbers after posting?**  
A: Check the **Posted Warehouse Shipment Line** or drill down into **Pallet Entries** to see the **Consignment No.** field.

**Q: Can I manually enter tracking numbers?**  
A: This depends on the Clever Shipping Agent Integration configuration. Consult Clever Dynamics documentation.

---

## Support and Resources

### Technical Support

**Technology Management Support:**
- **Email:** support@technologymanagement.co.uk
- **Phone:** +44 (0) 1234 567890
- **Portal:** https://support.technologymanagement.co.uk

**Clever Dynamics Support (for WMS and Carrier Integration):**
- **Website:** https://www.cleverdynamics.com
- **Support Portal:** https://support.cleverdynamics.com

### Documentation

- **PCG Pallet Shipping Release Notes:** See release documentation for version-specific changes
- **Business Central Documentation:** https://docs.microsoft.com/dynamics365/business-central
- **Clever WMS Devices User Guide:** Available from Clever Dynamics
- **Clever Shipping Agent Integration Guide:** Available from Clever Dynamics

### Training Resources

Contact Technology Management for on-site or remote training on:
- Initial setup and configuration
- Daily warehouse operations with pallet shipping
- Advanced multi-shipment pallet scenarios
- Carrier integration setup and troubleshooting
- Custom reporting and analytics

### Feedback and Feature Requests

We welcome feedback and feature requests:
- **Email:** feedback@technologymanagement.co.uk
- **Subject Line:** PCG Pallet Shipping Feedback

---

## Revision History

| Version | Date | Changes |
|---|---|---|
| 27.3.0 | May 2026 | Added multi-shipment pallet support with individual logging per warehouse shipment |
| 27.2.0 | March 2026 | Initial comprehensive user guide |

---

**End of User Guide**

For the latest version of this document, visit the Technology Management support portal or contact your account manager.
