---
uid: zero-count-tracking-user-guide
title: Zero Count Tracking - User Guide
---

# Zero Count Tracking - User Guide

**Version**: 27.3.0.0  
**Publisher**: Technology Management  
**Application**: Business Central WMS Device Enhancement  
**Last Updated**: February 2026

---

## Table of Contents

1. [Overview](#overview)
2. [Getting Started](#getting-started)
3. [System Setup](#system-setup)
4. [Main Features](#main-features)
5. [Working with Empty Bin Checks](#working-with-empty-bin-checks)
6. [Using the Handheld Device](#using-the-handheld-device)
7. [Reporting and Monitoring](#reporting-and-monitoring)
8. [Bin Blocking and Investigation](#bin-blocking-and-investigation)
9. [Best Practices](#best-practices)
10. [Troubleshooting](#troubleshooting)

---

## Overview

### Purpose

The Zero Count Tracking app enables warehouse managers to accurately track, confirm, and audit empty bins within your Business Central environment. This ensures reliable inventory data for reporting in Power BI and Business Central by systematically logging empty bin confirmations and identifying discrepancies that require investigation.

### Key Benefits

- **Accurate Inventory Visibility**: Confirm empty bins to ensure reliable stock data
- **Audit Trail**: Complete ledger of all empty bin confirmations with timestamps
- **Exception Management**: Flag bins requiring investigation and automatically block them
- **WMS Integration**: Seamlessly integrated with handheld devices for warehouse operations
- **Flexible Filtering**: Location and zone-specific functionality with exclusion options

### Target Users

- Warehouse Managers
- Warehouse Operatives
- Stock Controllers
- Inventory Analysts

---

## Getting Started

### Prerequisites

- Business Central version 27.3 or higher
- Clever Device Framework (version 8.0.2 or higher)
- Clever WMS Devices (version 8.0.3 or higher)
- Appropriate user permissions for warehouse management
- Access to handheld WMS devices (for mobile operations)

### Required Permissions

The Zero Count Tracking permission set provides access to:
- Empty Bin Check Journal
- Empty Bin Check Ledger
- Empty Bin Check configuration settings
- Bin blocking functionality
- Related reports and statistics

### Accessing the Application

1. **From the Role Center**:
   - Navigate to **Tasks** → **Empty Bin Check List**

2. **From Inventory Setup**:
   - Search for **Inventory Setup**
   - Navigate to **Empty Check Administration** → **Empty Bin Check Journal**

3. **From Bin List**:
   - Open any bin list page
   - Use the **Empty Bin Check** action in the ribbon

---

## System Setup

### Initial Configuration

Before using the Zero Count Tracking features, configure the system through the Inventory Setup page.

#### Step 1: Open Inventory Setup

1. Search for **Inventory Setup** in Business Central
2. Scroll down to the **Empty Bin Check** section

#### Step 2: Enable Empty Bin Check

Configure the following settings:

| Setting | Description | Recommended Value |
|---------|-------------|-------------------|
| **Empty Check Enabled** | Master switch to enable/disable the feature | ✓ Enabled |
| **Excluded Locations** | Filter of location codes to exclude (e.g., Quality, 3PL locations) | Set as per business requirements |
| **Auto-Block on Check Required** | Automatically block bins when flagged for investigation | ✓ Enabled (recommended) |
| **Empty Check Batch Name** | Default batch name for Physical Inventory Journal integration | "EMPTY" |
| **Default Reason Code** | Reason code for empty bin transactions | Create specific code (e.g., "EMPTYBIN") |
| **Enforce Comment** | Require comments on all empty bin check transactions | ✓ Enabled (for audit purposes) |

#### Step 3: Configure Excluded Locations

To exclude specific locations (e.g., Quality Control, 3PL locations):

1. In **Excluded Locations Empty Check** field, enter location filter
2. Use standard filter syntax:
   - Single location: `QUAL`
   - Multiple locations: `QUAL|3PL|TEMP`
   - Pattern matching: `QC*` (excludes all locations starting with QC)
3. Use the **Assist Edit** button (three dots) to select from location list

#### Step 4: Set Up Reason Codes (Optional)

1. Navigate to **Reason Codes**
2. Create specific codes for empty bin operations:
   - `EMPTYBIN` - Standard empty bin confirmation
   - `EMPTYDISC` - Empty bin discrepancy  
   - `STOCKFOUND` - Stock found in supposedly empty bin

---

## Main Features

### 1. Empty Bin Check Journal

The journal is your primary workspace for managing empty bin checks.

**Key Functions**:
- Generate list of empty bins
- Confirm bins are empty
- Flag bins for investigation
- Add comments and reason codes
- Post confirmations to ledger

### 2. Empty Bin Check Ledger

Historical record of all posted empty bin confirmations.

**Features**:
- Complete audit trail with timestamps
- Filter by location, bin, user, or date
- View confirmation history for specific bins
- Export for external reporting

### 3. Bin Blocking Integration

Automatically blocks bins flagged for investigation within WMS.

**Capabilities**:
- Prevents warehouse operations on flagged bins
- Visible in bin lists and handheld devices
- Tracks who blocked bin and when
- Requires resolution before unblocking

### 4. Handheld Device Integration

Full integration with WMS handheld devices for warehouse floor operations.

---

## Working with Empty Bin Checks

### Creating an Empty Bin Check List

#### Step 1: Open the Journal

1. Navigate to **Empty Bin Check List** page
2. The journal will be empty initially

#### Step 2: Get Empty Bins

1. Click **Get Empty Bins** action in the ribbon
2. In the dialog, set filters:

| Filter | Description | Example |
|--------|-------------|---------|
| **Location Code** | Select location(s) to check | MAIN |
| **Zone Code** | (Optional) Specific zone(s) | BULK, PICK |
| **Include Non-Empty Bins** | Check bins with stock to clear them from system | Leave unchecked normally |
| **Bin Type Filter** | Filter by bin type | Leave blank for all types |

3. Click **OK** to generate the list
4. The journal populates with all empty bins matching your criteria

#### Step 3: Review the List

Review the generated lines:

- **Entry No.**: Unique identifier for each line
- **Location Code**: Where the bin is located
- **Bin Code**: The specific bin identifier
- **User ID**: Your user ID (auto-populated)
- **Action**: Select appropriate action (initially blank)
- **Created Date Time**: When the entry was created
- **Status**: Pending (until posted)
- **Reason Code**: Optional reason code
- **Comments**: Add notes as required

### Confirming Empty Bins

When you've verified a bin is truly empty:

#### Method 1: Individual Confirmation

1. Select the line for the bin
2. In the **Action** field, select **Confirm Empty**
3. Add **Comments** if required (mandatory if enforced in setup)
4. Set **Reason Code** if applicable
5. Click **Post** action to post to ledger

#### Method 2: Bulk Confirmation

1. Select multiple lines (Ctrl+Click or Shift+Click)
2. Click **Confirm Empty** action in the ribbon
3. All selected lines will have Action set to "Confirm Empty"
4. Add comments on individual lines as needed
5. Click **Post** to post all pending lines

### Flagging Bins for Investigation

When a bin should be empty but you suspect or find stock:

1. Select the bin line
2. In the **Action** field, select **Check Required**
3. Enter **Reason Code** (mandatory)
4. Add detailed **Comments** explaining the issue
5. The bin will be automatically blocked (if configured)
6. Click **Post** to post to ledger

**Note**: Once marked as "Check Required", the bin is blocked in WMS and cannot be used for warehouse operations until resolved.

### Posting Journal Entries

#### Post All Pending

1. Click **Post** action in the ribbon
2. Confirm the posting action
3. All lines with status "Pending" will be:
   - Posted to the Empty Bin Check Ledger
   - Status updated to "Posted"
   - Bin table updated with last check date and user

#### Post Selected Lines

1. Select specific lines to post
2. Click **Post Selected** action
3. Only selected lines will be posted

#### View Posted Entries

1. After posting, click **View Ledger** action
2. Navigate to the Empty Bin Check Ledger page
3. Review posted entries with timestamps

### Clearing the Journal

To remove posted entries from the journal:

1. Click **Clear Posted Lines** action
2. Confirm the deletion
3. Only lines with Status = "Posted" will be deleted
4. Original ledger entries remain intact

---

## Using the Handheld Device

### Overview

Warehouse operatives can perform empty bin checks directly from handheld WMS devices during spot checks and stock takes.

### Accessing Empty Bin Function

1. From the handheld device main menu:
   - Navigate to **Stock Take** → **Spot Check**
   - Or select **Empty Bin Check** (depending on device configuration)

2. The device displays list of empty bins for your location

### Confirming Empty Bins on Device

#### Scanning Method

1. Scan the bin barcode
2. Device displays bin information
3. Select **Empty** option
4. Add comment if required (use voice-to-text or on-screen keyboard)
5. Confirm action
6. Updates sync immediately to Business Central

#### Browse Method

1. Browse list of empty bins on device
2. Select bin from list
3. Choose **Confirm Empty**
4. Add comment if required
5. Confirm action

### Flagging for Investigation on Device

1. Scan or select the bin
2. If stock is found:
   - Select **Check Required** option
   - Select appropriate reason code
   - Add detailed comment
   - Device will show bin as blocked
3. Bin is immediately blocked in system

### Recording Stock Found in "Empty" Bins

If you find stock in a bin that was flagged as empty:

1. Scan the bin
2. Instead of selecting "Empty", proceed with normal stock take:
   - Scan item barcode(s)
   - Enter quantity
   - Confirm
3. Entries are created in Physical Inventory Journal under batch name "EMPTY"
4. These can be processed separately to update stock levels
5. The bin will be removed from empty bin list after posting

### Sync and Connectivity

- Changes made on handheld devices sync in real-time
- If offline, changes queue and sync when connection restored
- Check device sync indicator to confirm updates are posted
- Refresh list periodically to see updates from other users

---

## Reporting and Monitoring

### Empty Bin Check Ledger

**Purpose**: Complete audit trail of all empty bin confirmations and checks.

**Access**: 
- Navigate to **Empty Bin Check Ledger**
- Or from journal, click **View Ledger**

**Key Information**:
- Ledger Entry No.
- Original Journal Entry No.
- Location and Bin codes
- Action taken (Confirmed Empty / Check Required)
- User who performed action
- Created and Posted timestamps
- Bin blocking status
- Comments and reason codes

**Filtering Options**:
- Filter by date range
- Filter by location or specific bins
- Filter by user
- Filter by action type
- Filter for currently blocked bins only

### Empty Bin Check Statistics

**Purpose**: Dashboard view of empty bin check activity and trends.

**Access**: Navigate to **Empty Bin Check Stats** page

**Metrics Displayed**:
- Total bins checked (by period)
- Confirmed empty vs. checks required ratio
- Current blocked bins count
- Checks by location
- Checks by user
- Trend analysis over time

### Empty Bin Check Report

**Purpose**: Printable report for management review and audits.

**Access**: 
- Run **Empty Bin Check Report** (Report 50057)
- Available in Excel and PDF formats

**Report Sections**:
1. **Summary**: Overview statistics for selected period
2. **Confirmed Empty Bins**: List of all confirmations
3. **Bins Requiring Check**: Current exceptions
4. **Blocked Bins**: Currently blocked bins and resolution status
5. **User Activity**: Breakdown by user

**Parameters**:
- Date Range: Filter by created or posted date
- Location Filter: Specific location(s)
- User Filter: Specific user(s)
- Include Posted Only: Exclude pending entries
- Include Resolved Blocks: Show resolved issues

### Viewing from Bin Pages

From any bin list or card:

1. **Last Empty Check Date**: Shows when bin was last confirmed empty
2. **Last Confirmed By**: User who performed last check
3. **Empty Check Required**: Flag indicating bin needs investigation
4. **Blocked for Empty Check**: Current blocking status
5. **Empty Check Comments**: Latest comments

**Actions Available**:
- View Empty Check History (opens ledger filtered for this bin)
- Perform Empty Check (opens journal with this bin)
- Clear Check Requirement (unblock bin after resolution)

---

## Bin Blocking and Investigation

### When Bins Get Blocked

Bins are automatically blocked when:
- Action set to "Check Required" in journal
- Check Required selected on handheld device
- Auto-Block on Check Required is enabled in setup

### Effects of Bin Blocking

A blocked bin:
- Cannot be used for new put-aways
- Cannot be used as source for picks
- Shows as blocked in all bin lists
- Displays warning on handheld devices
- Requires resolution before use

### Investigating Blocked Bins

#### Step 1: Review the Ledger Entry

1. Navigate to **Empty Bin Check Ledger**
2. Filter for **Blocked = Yes**
3. Review comments and reason code
4. Note who blocked it and when

#### Step 2: Physical Investigation

1. Go to the physical bin location
2. Verify if stock is present or bin is truly empty
3. If stock found:
   - Perform stock count using standard process
   - Record in Physical Inventory Journal
   - Post to correct system records

#### Step 3: Resolve the Blocking

**If Bin is Empty (False Alarm)**:
1. Navigate to the bin card
2. Click **Clear Check Requirement** action
3. Add comment explaining resolution
4. Bin is unblocked and available for use
5. New ledger entry recorded

**If Stock Was Found**:
1. Complete stock count and post adjustments
2. Navigate to bin card
3. Click **Clear Check Requirement**
4. Add comment: "Stock counted and corrected"
5. Bin is unblocked

### Monitoring Blocked Bins

**Daily Review**:
- Check **Blocked Bins Count** on statistics page
- Review aging of blocked bins
- Follow up on bins blocked >48 hours

**Weekly Review**:
- Generate **Empty Bin Check Report** 
- Filter for blocked bins
- Review resolution rates and trends
- Identify process improvement opportunities

---

## Best Practices

### Planning Empty Bin Checks

✅ **Do**:
- Schedule regular empty bin checks (e.g., weekly or monthly)
- Focus on high-traffic zones first (picking areas)
- Coordinate with warehouse quiet periods
- Assign specific users or teams to zones

❌ **Don't**:
- Perform during peak warehouse hours
- Mix with regular stock counts (separate processes)
- Check bins recently used in movements

### Using the Journal Effectively

✅ **Do**:
- Generate focused lists using location/zone filters
- Include "Include Non-Empty Bins" periodically to catch system errors
- Add meaningful comments for future reference
- Post regularly (at least daily) to keep journal manageable
- Clear posted lines to keep journal view clean

❌ **Don't**:
- Generate entire warehouse at once (too many lines)
- Leave journal entries unposted for extended periods
- Skip comments when flagging bins for check
- Delete unposted lines without investigation

### Handheld Device Best Practices

✅ **Do**:
- Train operatives on proper empty bin confirmation process
- Perform physical verification before confirming
- Use standard comments/reason codes for consistency
- Sync device regularly in areas with poor connectivity
- Report device issues immediately

❌ **Don't**:
- Confirm bins as empty without physical verification
- Skip comments when flagging exceptions
- Continue working if device doesn't sync properly
- Use device in airplane mode during checks

### Investigation and Resolution

✅ **Do**:
- Investigate flagged bins within 24 hours
- Document findings in comments
- Correct stock levels if discrepancies found
- Unblock bins promptly after resolution
- Track patterns in check requirements

❌ **Don't**:
- Assume checkflags are errors without investigation
- Unblock bins without verifying status
- Skip root cause analysis for repeated issues
- Leave bins blocked unnecessarily

### Data Quality

✅ **Do**:
- Review ledger entries weekly for patterns
- Monitor user activity for training needs
- Use consistent reason codes
- Maintain quality comments
- Archive old ledger data per retention policy

❌ **Don't**:
- Use generic reason codes for all situations
- Skip audits of empty bin check process
- Ignore trends in check requirements

---

## Troubleshooting

### Common Issues and Solutions

#### Issue: "Get Empty Bins" Returns No Results

**Possible Causes**:
- All bins have stock
- Location/zone filter too restrictive  
- Location is in excluded list

**Solutions**:
1. Check Inventory Setup → Excluded Locations filter
2. Verify location has bins defined
3. Temporarily enable "Include Non-Empty Bins" to see all bins
4. Check bin content in Bin Contents page

#### Issue: Cannot Post Journal Entries

**Possible Causes**:
- Action not set on lines
- Comments required but missing
- Reason code required but missing
- Record locked by another user

**Solutions**:
1. Ensure all lines have Action selected
2. Add comments if enforced in setup
3. Add reason codes for "Check Required" lines
4. Check if another user has journal open
5. Refresh page and retry

#### Issue: Bin Not Blocked After "Check Required"

**Possible Causes**:
- Auto-block disabled in setup
- Journal not posted yet
- Bin already blocked for other reason

**Solutions**:
1. Check Inventory Setup → Auto-Block on Check Required
2. Post the journal entry
3. Manually block bin from bin card if auto-block disabled
4. Check bin card for existing blocking reason

#### Issue: Handheld Device Not Showing Empty Bins

**Possible Causes**:
- Device not synced
- User doesn't have permissions
- Location filter on device
- No empty bins in location

**Solutions**:
1. Force sync on device (refresh/reconnect)
2. Check user permissions for empty bin check
3. Verify device location filter settings
4. Generate empty bin list from BC to confirm bins exist

#### Issue: Cannot Unblock Bin

**Possible Causes**:
- Missing permissions
- Bin blocked for multiple reasons
- Integration with WMS+ preventing unblock

**Solutions**:
1. Verify user has warehouse admin permissions
2. Check bin card for all blocking reasons
3. Clear each blocking reason separately
4. Contact system administrator if issue persists

#### Issue: Empty Bin Not Removed from List After Stock Found

**Possible Causes**:
- Physical Inventory Journal not posted
- Bin Contents not updated
- Cache/refresh needed

**Solutions**:
1. Post the Physical Inventory Journal entries
2. Manually verify Bin Contents page
3. Refresh empty bin check list
4. Regenerate empty bin list with current filters

---

## Support and Additional Resources

### Getting Help

- **Live Support**: https://www.tecman.co.uk/live-support
- **Documentation**: Refer to resource documents in app folder
- **Privacy Policy**: https://www.tecman.co.uk/legal-notices/your-privacy
- **EULA**: https://www.tecman.co.uk/eula

### Related Documentation

- Empty Bin Check Process Flow
- WMS Device User Guide
- Business Central Warehouse Management Guide
- Physical Inventory Counting Procedures

### Training Resources

Contact your system administrator or Technology Management for:
- User training sessions
- Advanced warehouse management training
- Handheld device operation training
- Report generation and analysis training

---

## Appendix

### Glossary

| Term | Definition |
|------|------------|
| **Empty Bin Check** | Process of verifying and confirming that a bin contains no stock |
| **Journal** | Temporary workspace for recording empty bin confirmations before posting |
| **Ledger** | Permanent historical record of all posted empty bin checks |
| **Check Required** | Status indicating a bin needs investigation (usually due to discrepancy) |
| **Bin Blocking** | Preventing warehouse operations on a specific bin |
| **Spot Check** | Random or scheduled verification of bin contents |
| **WMS** | Warehouse Management System |
| **Physical Inventory Journal** | BC standard journal for recording stock counts |

### Keyboard Shortcuts

| Action | Shortcut |
|--------|----------|
| New Line | Ctrl + N |
| Delete Line | Ctrl + Delete |
| Post | Ctrl + F11 |
| Search/Filter | Shift + F3 |
| Refresh | F5 |
| View Ledger | Ctrl + Shift + L |

### Quick Reference: Common Tasks

| Task | Quick Steps |
|------|------------|
| **Confirm empty bins** | Get Empty Bins → Select Action "Confirm Empty" → Post |
| **Flag for investigation** | Select line → Action "Check Required" → Add reason & comment → Post |
| **View history** | View Ledger → Filter by bin/location/date |
| **Unblock bin** | Find in Bin List → Clear Check Requirement → Confirm |
| **Generate report** | Run Report 50057 → Set filters → Export |

---

**Document Version**: 1.0  
**Application Version**: 27.3.0.0  
**Last Updated**: February 2026  
**Publisher**: Technology Management  

---

*This user guide is subject to updates as the application evolves. Always refer to the latest version available in your Business Central environment or from your system administrator.*
