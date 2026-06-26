---
uid: pcg-api-integration-guide
title: PCG API — Integration Guide
---

# PCG API — Integration Guide

**App**: PCG API  
**Publisher**: Technology Management  
**Version**: 27.3.x  
**BC Platform**: 27.3 (Cloud, Runtime 16.0)  
**Description**: A collection of API endpoints for reading and writing data in Business Central as part of the PCG integration.

---

## Table of Contents

1. [API Endpoint Reference](#api-endpoint-reference)
2. [Functional Areas](#functional-areas)
   - [Common / Setup](#common--setup)
   - [P5 — Order Cancellation](#p5--order-cancellation)
   - [P6 — Sales Order Line Dispatch](#p6--sales-order-line-dispatch)
   - [P7 — Palletised Load Build](#p7--palletised-load-build)
   - [P8 — Freight Shipping Info](#p8--freight-shipping-info)
   - [P9 — RTB Returns](#p9--rtb-returns)
   - [P10 — Purchase Order](#p10--purchase-order)
   - [Sales Return Orders](#sales-return-orders)
3. [Service-Enabled Procedures](#service-enabled-procedures)
4. [Caller Workflows](#caller-workflows)
   - [P7 — Palletised Load Build](#p7--palletised-load-build)
   - [P8 — Freight Shipping Info](#p8--freight-shipping-info)
   - [Sales Return Orders — PATCH](#sales-return-orders--patch)
5. [Outbound JSON Payloads](#outbound-json-payloads)
6. [Data Model](#data-model)
   - [Custom Tables](#custom-tables)
   - [Table Extensions](#table-extensions)
7. [Internal Components](#internal-components)
8. [Permission Set](#permission-set)
9. [Dependencies](#dependencies)

---

## API Endpoint Reference

All endpoints are OData v4 and follow the pattern:

```
/api/{publisher}/{group}/{version}/{entitySetName}
```

Base: `/api/technologyManagement/integrations/v2.0/`

| Page # | Entity Set Name | Entity Name | HTTP Methods | Notes |
| ------ | --------------- | ----------- | ------------ | ----- |
| 50048 | `items` | `item` | GET, PATCH | OData key: `SystemId` |
| 50049 | `itemInventory` | `itemInventory` | GET | Read-only; `locationFilter` is a FlowFilter — pass as `$filter=locationFilter eq 'MAIN'`; not stored, not returned in responses |
| 50050 | `itemReferences` | `itemReference` | GET, POST, PATCH | POST will reject duplicates — use PATCH to update existing records. PATCH will rename the record if any key field is changed (`itemNo`, `variantCode`, `unitOfMeasure`, `referenceType`, `referenceTypeNo`, `referenceNo`) |
| 50085 | `itemUnitOfMeasure` | `itemUnitOfMeasure` | GET, POST, PATCH | |
| 50051 | `salesOrders` | `salesOrder` | GET, PATCH | Includes `salesLines` sub-page |
| 50052 | `salesLines` | `salesLine` | GET, POST, PATCH | Sub-page of `salesOrders` |
| 50162 | `salesOrderCancellations` | `salesOrderCancellation` | POST | Insert-only; includes `salesOrderCancellationLines` sub-page |
| 50165 | `salesOrderCancellationLines` | `salesOrderCancellationLine` | POST | Sub-page of `salesOrderCancellations` |
| 50087 | `purchaseOrders` | `purchaseOrder` | GET | Read-only; includes `purchaseOrderLines` sub-page |
| 50181 | `purchaseOrderLines` | `purchaseOrderLine` | GET | Read-only; sub-page of `purchaseOrders` |
| 50229 | `purchaseOrderCancellations` | `purchaseOrderCancellation` | POST | Insert-only; includes `purchaseOrderCancellationLines` sub-page |
| 50230 | `purchaseOrderCancellationLines` | `purchaseOrderCancellationLine` | POST | Sub-page of `purchaseOrderCancellations` |
| 50164 | `pcgPalletisedLoadBuilds` | `pcgPalletisedLoadBuild` | POST, PATCH | Includes `pcgPalletisedLoadLines` sub-page |
| 50163 | `pcgPalletisedLoadLines` | `pcgPalletisedLoadLine` | GET, POST, PATCH | Sub-page of `pcgPalletisedLoadBuilds` |
| 50231 | `salesReturnOrders` | `salesReturnOrder` | GET, POST, PATCH, DELETE | Full Sales Return Order; includes `salesReturnLines` sub-page |
| 50232 | `salesReturnLines` | `salesReturnLine` | GET, POST, PATCH | Sub-page of `salesReturnOrders` |
| 50233 | `salesReturnOrdersExt` | `salesReturnOrderExt` | GET, POST, PATCH | Slim variant — custom fields only; includes `salesReturnLinesExt` sub-page |
| 50234 | `salesReturnLinesExt` | `salesReturnLineExt` | GET, POST, PATCH | Sub-page of `salesReturnOrdersExt` |
| 50167 | `warehouseReceiptFreight` | `warehouseReceiptFreight` | GET, PATCH | Includes `warehouseReceiptFreightLines` sub-page |
| 50169 | `warehouseReceiptFreightLines` | `warehouseReceiptFreightLine` | GET | Sub-page of `warehouseReceiptFreight` |

### Field Reference by Endpoint

#### `items` (PAG50048)

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `number` | Item No. | Code[20] | No |
| `number2` | Item No. 2 | Code[20] | Yes |
| `displayName` | Description | Text[100] | Yes |
| `subCategory` | Sub Category | Code | Yes |
| `warrantyCategory` | Warranty Category | Code | Yes |
| `supplyType` | Supply Type | Option | Yes |
| `customerProductCode` | Customer Product Code | Code | Yes |
| `storageWarehouse` | Storage Warehouse | Code | Yes |
| `boxType` | Box Type | Code | Yes |
| `productHeight` | Product Height | Decimal | Yes |
| `productWidth` | Product Width | Decimal | Yes |
| `productDepth` | Product Depth | Decimal | Yes |
| `outerCartonUnits` | Number of Units per Outer Carton | Decimal | Yes |
| `container20Qty` | 20ft Container Qty. | Decimal | Yes |
| `container40Qty` | 40ft Container Qty. | Decimal | Yes |
| `container40HQQty` | 40ft HQ Container Qty. | Decimal | Yes |
| `container45Qty` | 45ft Container Qty. | Decimal | Yes |
| `bundle` | Bundle | Text | Yes |
| `holdQuarantineQty` | Hold / Quarantine Qty. | Decimal | Yes |
| `nextPOQuantity` | Next PO Quantity | Decimal | Yes |
| `nextPODate` | Next PO Date | Date | Yes |
| `vendorNo` | Vendor No. | Code | Yes |
| `vendorItemNo` | Vendor Item No. | Code | Yes |
| `leadTimeCalculation` | Lead Time Calculation | Duration | Yes |
| `reorderQuantity` | Reorder Quantity | Decimal | Yes |
| `grossWeight` | Gross Weight | Decimal | Yes |
| `netWeight` | Net Weight | Decimal | Yes |
| `tariffNo` | Tariff No. | Code | Yes |
| `countryRegionPurchasedCode` | Country/Region Purchased Code | Code | Yes |
| `vatProdPostingGroup` | VAT Prod. Posting Group | Code | Yes |
| `minimumOrderQuantity` | Minimum Order Quantity | Decimal | Yes |
| `maximumOrderQuantity` | Maximum Order Quantity | Decimal | Yes |
| `dutyDue` | Duty Due % | Decimal | Yes |
| `unitVolume` | Unit Volume | Decimal | Yes |
| `endOfLifeDisposalReq` | End-of-Life Disposal Req. | Option | Yes |
| `salesBlocked` | Sales Blocked | Option | Yes |
| `purchasingBlocked` | Purchase Blocked | Option | Yes |
| `itemCategoryCode` | Item Category Code | Code | Yes |
| `itemCategoryParentCode` | Item Category Parent Code | Code | No |
| `itemStatus` | Item Status | Option | No |

> **Data Setup Notes:** `subCategory` requires a Sub Category record; `warrantyCategory` requires a Warranty Category record; `storageWarehouse` requires a **Location** record; `boxType` requires a Box Type record; `vatProdPostingGroup` requires a **VAT Product Posting Group** record; `itemCategoryCode` requires an **Item Category** record.

#### `itemInventory` (PAG50049)

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `number` | Item No. | Code[20] | No |
| `displayName` | Description | Text | No |
| `inventory` | Inventory (at Location) | Decimal | No |
| `locationFilter` | Location Filter | Code | Yes |
| `holdQuarantineQty` | Hold / Quarantine Qty. | Decimal | No |
| `nextPOQuantity` | Next PO Quantity | Decimal | No |
| `nextPODate` | Next PO Date | Date | No |
| `onSalesOrder` | Qty. on Sales Order | Decimal | No |
| `onAssComponent` | Qty. on Assembly Component | Decimal | No |
| `lastModifiedDateTime` | Last Modified Date | DateTime | No |

> **`locationFilter` — Behaviour**
>
> **Functional behaviour**
>
> Applying `$filter=locationFilter eq 'MAIN'` does **not** reduce the number of records returned. Every item in the catalogue is always included in the response regardless of whether it has any stock at the specified location.
>
> What the filter *does* change is the value of the calculated inventory fields on each record. When a location filter is active, `inventory`, `onSalesOrder`, and `onAssComponent` are each scoped to that location only — they reflect quantities at the filtered location rather than the company-wide total. An item with no stock at the specified location will still appear in the response, but with `inventory: 0`.
>
> | Scenario | Records returned | `inventory` value |
> | -------- | ---------------- | ----------------- |
> | No `locationFilter` applied | All items | Company-wide total across all locations |
> | `$filter=locationFilter eq 'MAIN'` | All items | Stock at MAIN only (0 if none held there) |
>
> **Example**
>
> Item `WIDGET-001` has 50 units at `MAIN` and 20 units at `SECONDARY`. Item `GADGET-002` has no stock at `MAIN` but 10 units at `SECONDARY`.
>
> Without filter:
>
> ```json
> { "number": "WIDGET-001", "inventory": 70 }
> { "number": "GADGET-002", "inventory": 10 }
> ```
>
> With `$filter=locationFilter eq 'MAIN'`:
>
> ```json
> { "number": "WIDGET-001", "inventory": 50 }
> { "number": "GADGET-002", "inventory": 0 }
> ```
>
> Both items are returned in both cases. Only the `inventory` value changes.
>
> **Technical detail**
>
> `locationFilter` is a Business Central **FlowFilter** field. FlowFilters are not stored values — they are filter parameters that change how **FlowField** calculations are evaluated at runtime. When BC processes an OData `$filter` clause against a FlowFilter field, it does not apply a row-level predicate to the result set; instead it sets the filter context used when computing the dependent FlowField values (`inventory`, `onSalesOrder`, `onAssComponent`). The field is returned in the response body, but its value will reflect the filter that was applied — if no filter was sent, it will be returned as blank.

#### `itemReferences` (PAG50050)

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `itemNo` | Item No. | Code[20] | Yes |
| `variantCode` | Variant Code | Code[10] | Yes |
| `unitOfMeasure` | Unit of Measure | Code[10] | Yes |
| `referenceType` | Reference Type | Option | Yes |
| `referenceTypeNo` | Reference Type No. | Code[30] | Yes |
| `referenceNo` | Reference No. | Code[50] | Yes |
| `description` | Description | Text[100] | Yes |
| `description2` | Description 2 | Text[100] | Yes |
| `startingDate` | Starting Date | Date | Yes |
| `endingDate` | Ending Date | Date | Yes |
| `supplierRefEntryNo` | Supplier Ref. Entry No. | Integer | No |

> **Note:** POST will return an error if the item reference already exists (matched by `itemNo`, `variantCode`, `unitOfMeasure`, `referenceType`, `referenceTypeNo`, `referenceNo`). Use PATCH to update existing records. PATCH will rename the record if any key field is changed.

#### `itemUnitOfMeasure` (PAG50085)

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `itemNo` | Item No. | Code[20] | Yes |
| `code` | Unit of Measure Code | Code[10] | Yes |
| `qtyPerUnitOfMeasure` | Qty. per Unit of Measure | Decimal | Yes |
| `length` | Length | Decimal | Yes |
| `width` | Width | Decimal | Yes |
| `height` | Height | Decimal | Yes |
| `cubage` | Cubage | Decimal | Yes |
| `weight` | Weight | Decimal | Yes |

> **Data Setup Notes:** `itemNo` requires an existing **Item** record; `code` requires an existing **Unit of Measure** record.

#### `salesOrders` (PAG50051)

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `systemId` | System identifier | Guid | No |
| `number` | Sales Order No. | Code[20] | No |
| `customerNo` | Customer No. | Code[20] | No |
| `shipToEMailAddress` | Ship-to E-mail Address | Text | Yes |
| `miscReference1` | Miscellaneous Reference 1 | Text | Yes |
| `miscReference2` | Miscellaneous Reference 2 | Text | Yes |
| `miscReference3` | Miscellaneous Reference 3 | Text | Yes |
| `externalRetailerName` | External Retailer Name | Text | Yes |
| `externalOrderReferences` | External Order References | Text | Yes |
| `invoiceName` | Invoice Name | Text | Yes |
| `invoiceAdd1` | Invoice Address Line 1 | Text | Yes |
| `invoiceAdd2` | Invoice Address Line 2 | Text | Yes |
| `invoiceAdd3` | Invoice Address Line 3 | Text | Yes |
| `invoiceAdd4` | Invoice Address Line 4 | Text | Yes |
| `invoicePostcode` | Invoice Postcode | Code | Yes |
| `transportMethod` | Transport Method | Code | Yes |
| `shipmentMethodCode` | Shipment Method Code | Code | Yes |
| `shipToPhoneNo` | Ship-to Phone No. | Text | Yes |
| `shipToPhoneNo2` | Ship-to Phone No. 2 | Text | Yes |
| `shippingAgentServiceCode` | Shipping Agent Service Code | Code | Yes |
| `systemCreatedAt` | Created date/time | DateTime | No |
| `systemCreatedBy` | Created by | Guid | No |
| `systemModifiedAt` | Last modified date/time | DateTime | No |
| `systemModifiedBy` | Last modified by | Guid | No |

> **Data Setup Notes:** `transportMethod` requires a **Transport Method** record; `shipmentMethodCode` requires a **Shipment Method** record; `shippingAgentServiceCode` requires a **Shipping Agent** record and a corresponding **Shipping Agent Service** record.

> **Known Issues:** `shippingAgentServiceCode` is exposed without `shippingAgentCode`. If the Sales Order does not already have a Shipping Agent Code set, sending a service code alone will fail BC validation — the service code is a child of the agent code. Ensure the order has a Shipping Agent Code before patching this field.

#### `salesLines` (PAG50052) — sub-page of `salesOrders`

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `documentNo` | Sales Order No. | Code[20] | No |
| `lineNo` | Line No. | Integer | No |
| `type` | Line Type | Option | Yes |
| `no` | Item / Account No. | Code[20] | Yes |
| `description` | Description | Text | Yes |
| `quantity` | Quantity | Decimal | Yes |
| `unitPrice` | Unit Price | Decimal | Yes |
| `lineAmount` | Line Amount | Decimal | No |
| `miscLineReference1` | Miscellaneous Line Reference 1 | Text | Yes |
| `pcgUniqueLineReference` | PCG Unique Line Reference | Text | Yes |
| `serialNo` | Serial No. | Code | Yes |
| `locationCode` | Location Code | Code | Yes |
| `shippingAgentCode` | Shipping Agent Code | Code | Yes |
| `shippingAgentServiceCode` | Shipping Agent Service Code | Code | Yes |
| `vehicleRegistration` | Vehicle Registration | Text | Yes |
| `dropNumber` | Drop Number | Text | Yes |
| `height` | Height | Decimal | Yes |
| `width` | Width | Decimal | Yes |
| `grossWeight` | Gross Weight | Decimal | Yes |
| `depth` | Depth | Decimal | Yes |
| `commodityCode` | Commodity Code / Tariff No. | Code[20] | Yes |
| `attachToInventoryLine` | Attach To Line No. | Integer | Yes |

> **Data Setup Notes:** `no` requires an **Item** record when `type` is `Item`, or a **G/L Account** record when `type` is `G/L Account`; `locationCode` requires a **Location** record; `shippingAgentCode` requires a **Shipping Agent** record; `shippingAgentServiceCode` requires a corresponding **Shipping Agent Service** record.

> **Customer Item Reference Resolution:** When posting or patching a `no` value on a Sales line, if the supplied value does not match a direct Item No., the system will attempt to resolve it via the customer's item cross-references (`Item Reference` table, filtered to the order's Sell-to Customer No.). If a matching cross-reference is found, the Item No. is substituted automatically before the line is written. This allows callers to send customer-specific part numbers rather than internal item numbers.

> **Serial No. Tracking:** On insert (POST), if a `serialNo` is provided, a surplus Reservation Entry is automatically created to register serial number item tracking. On modify (PATCH), if the serial no. changes, the existing tracking entry is replaced with the new value.

#### `salesOrderCancellations` (PAG50162)

| JSON Property | Description | Type | Notes |
| ------------- | ----------- | ---- | ----- |
| `id` | System identifier | Guid | Auto-generated |
| `cancellationType` | Cancellation Type | Enum | Required. `Cancel` or `Modify` |
| `customerCode` | Customer No. | Code[20] | Required — must exist in **Customer** table |
| `orderRef` | Order Reference (Your Reference) | Code | Required |
| `requestedDeliveryDate` | Requested Delivery Date | Date | |
| `dueDate` | Due Date | Date | |
| `shippingAgentCode` | Shipping Agent Code | Code | Requires **Shipping Agent** record |
| `locationCode` | Location Code | Code | Requires **Location** record |
| `vehicleRegistration` | Vehicle Registration | Text | |
| `shipToName` | Ship-to Name | Text | |
| `shipToName2` | Ship-to Name 2 | Text | |
| `shipToAddress` | Ship-to Address | Text | |
| `shipToAddress2` | Ship-to Address 2 | Text | |
| `shipToCity` | Ship-to City | Text | |
| `shipToCounty` | Ship-to County | Text | |
| `shipToPostCode` | Ship-to Post Code | Code | |
| `shipToContact` | Ship-to Contact | Text | |
| `shipToEmail` | Ship-to E-mail | Text | |
| `miscReference1` | Miscellaneous Reference 1 | Text | |
| `miscReference2` | Miscellaneous Reference 2 | Text | |
| `responseText` | Response JSON | Text | Populated by system after processing |

> **Known Issues:** Processing errors (e.g. order not found, active warehouse picks) are returned as a JSON string in `responseText`, **not** as a 4xx HTTP status. An `HTTP 200` response does not indicate success — always inspect `responseText` for failure indicators. Avoid sending concurrent requests; the entry number allocation and line table cleanup do not isolate per-request, which can cause collisions under concurrent load.

#### `salesOrderCancellationLines` (PAG50165) — sub-page

| JSON Property | Description | Type |
| ------------- | ----------- | ---- |
| `gNumber` | G Number (Item No.) | Code |
| `cancellationReason` | Cancellation Reason | Text |
| `cancellationDate` | Cancellation Date | Date |
| `qty` | Quantity | Decimal |

#### `purchaseOrders` (PAG50087)

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `orderNumber` | Purchase Order No. | Code[20] | No |
| `orderDate` | Order Date | Date | No |
| `supplierCode` | Vendor No. | Code[20] | No |
| `orderType` | Order Type | Code | No |
| `requiredByDate` | Expected Receipt Date | Date | No |
| `destinationWarehouse` | Location Code | Code | No |
| `containerType` | Container Type | Code | No |
| `status` | Status | Option | No |
| `currencyCode` | Currency Code | Code | No |

#### `purchaseOrderLines` (PAG50181) — sub-page of `purchaseOrders`

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `itemNo` | Item No. | Code[20] | No |
| `description` | Description | Text | No |
| `quantityOrdered` | Quantity Ordered | Decimal | No |
| `directUnitCost` | Direct Unit Cost | Decimal | No |
| `quantityReceived` | Quantity Received | Decimal | No |
| `quantityInvoiced` | Quantity Invoiced | Decimal | No |

#### `purchaseOrderCancellations` (PAG50229)

| JSON Property | Description | Type | Notes |
| ------------- | ----------- | ---- | ----- |
| `id` | System identifier | Guid | Auto-generated |
| `cancellationType` | Cancellation Type | Enum | Required. `Cancel` or `Modify` |
| `customerCode` | Vendor No. | Code[20] | Required — must exist in **Vendor** table |
| `orderRef` | Order Reference | Code | Required |
| `locationCode` | Location Code | Code | Requires **Location** record |
| `responseText` | Response JSON | Text | Populated by system after processing |

> **Known Issues:** Same response pattern as `salesOrderCancellations` — inspect `responseText` for failure, not only HTTP status. Note also that the `customerCode` JSON property maps to the **Vendor Code** field internally; BC error messages will reference "Vendor Code", not "Customer Code". Concurrent requests carry the same collision risks as `salesOrderCancellations`.

#### `purchaseOrderCancellationLines` (PAG50230) — sub-page

| JSON Property | Description | Type |
| ------------- | ----------- | ---- |
| `gNumber` | G Number (Item No.) | Code |
| `cancellationReason` | Cancellation Reason | Text |
| `cancellationDate` | Cancellation Date | Date |
| `qty` | Quantity | Decimal |

#### `pcgPalletisedLoadBuilds` (PAG50164)

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `systemId` | System identifier | Guid | No |
| `salesOrderNo` | Sales Order No. | Code[20] | Yes |
| `collectionReference` | Collection Reference | Text | Yes |
| `loadReference` | Load Reference | Text | Yes |
| `collectionDate` | Collection Date | Date | Yes |
| `deliveryDate` | Delivery Date | Date | Yes |
| `status` | Sales Order Status | Option | No |

> **Data Setup Notes:** `salesOrderNo` must reference an existing open **Sales Order** (Sales Header table). The order must already exist before posting this record.

> **Known Issues:** Sales Order must be in **Open** status; a Released order will be rejected, returning `HTTP 500` with an error message in the body.

**Bound Action — `submit`**

```
POST /api/technologyManagement/integrations/v2.0/pcgPalletisedLoadBuilds({id})/Microsoft.NAV.submit
```

Triggers palletised load processing for the identified Sales Order. Validates the payload, processes all pallet lines, and creates Warehouse Shipments. Returns the updated entity.

| Detail | Value |
| ------ | ----- |
| Parameters | None — entity resolved from `{id}` in the URL |
| Resolves order by | `id` (SystemId) or `salesOrderNo` |
| On success | Warehouse Shipments created; returns updated entity |
| On failure | HTTP error with processing error message |

#### `pcgPalletisedLoadLines` (PAG50163) — sub-page of `pcgPalletisedLoadBuilds`

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `documentNo` | Sales Order No. | Code[20] | No |
| `lineNo` | Line No. | Integer | No |
| `number` | Item No. | Code[20] | Yes |
| `itemReferenceNumber` | Item Reference No. | Code[50] | Yes |
| `quantity` | Quantity on Sales Line | Decimal | No |
| `quantityOnLoad` | Quantity on Load | Decimal | Yes |
| `quantityToCancel` | Quantity to Cancel | Decimal | Yes |
| `palletQuantityOnLoad` | Pallet Quantity on Load | Decimal | Yes |
| `weightOnLoad` | Weight on Load | Decimal | Yes |
| `unitsOnLoad` | Units on Load | Decimal | Yes |
| `palletRequirements` | Pallet Requirements | Text | Yes |
| `shipmentTag` | Shipment Tag | Text | Yes |
| `shipmentDate` | Shipment Date | Date | Yes |

#### `salesReturnOrders` (PAG50231)

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `number` | Document No. | Code[20] | Yes |
| `externalDocumentNumber` | External Document No. | Text | Yes |
| `orderDate` | Document Date | Date | Yes |
| `postingDate` | Posting Date | Date | Yes |
| `customerId` | Customer Id (Sell-to) | Guid | No |
| `customerNumber` | Customer No. | Code[20] | Yes |
| `customerName` | Customer Name | Text | No |
| `billToName` | Bill-to Name | Text | No |
| `billToCustomerId` | Bill-to Customer Id | Guid | No |
| `billToCustomerNumber` | Bill-to Customer No. | Code[20] | Yes |
| `shipToName` | Ship-to Name | Text | Yes |
| `shipToContact` | Ship-to Contact | Text | Yes |
| `sellToAddressLine1` | Sell-to Address | Text | Yes |
| `sellToAddressLine2` | Sell-to Address 2 | Text | Yes |
| `sellToCity` | Sell-to City | Text | Yes |
| `sellToCountry` | Sell-to Country/Region Code | Code | Yes |
| `sellToState` | Sell-to County | Text | Yes |
| `sellToPostCode` | Sell-to Post Code | Code | Yes |
| `billToAddressLine1` | Bill-to Address | Text | No |
| `billToAddressLine2` | Bill-to Address 2 | Text | No |
| `billToCity` | Bill-to City | Text | No |
| `billToCountry` | Bill-to Country/Region Code | Code | No |
| `billToState` | Bill-to County | Text | No |
| `billToPostCode` | Bill-to Post Code | Code | No |
| `shipToAddressLine1` | Ship-to Address | Text | Yes |
| `shipToAddressLine2` | Ship-to Address 2 | Text | Yes |
| `shipToCity` | Ship-to City | Text | Yes |
| `shipToCountry` | Ship-to Country/Region Code | Code | Yes |
| `shipToState` | Ship-to County | Text | Yes |
| `shipToPostCode` | Ship-to Post Code | Code | Yes |
| `shortcutDimension1Code` | Shortcut Dimension 1 Code | Code | Yes |
| `shortcutDimension2Code` | Shortcut Dimension 2 Code | Code | Yes |
| `currencyId` | Currency Id | Guid | No |
| `currencyCode` | Currency Code | Code | Yes |
| `pricesIncludeTax` | Prices Including VAT | Boolean | Yes |
| `paymentTermsId` | Payment Terms Id | Guid | Yes |
| `shipmentMethodId` | Shipment Method Id | Guid | Yes |
| `salesperson` | Salesperson Code | Code | Yes |
| `partialShipping` | Partial Shipping | Boolean | No |
| `requestedDeliveryDate` | Requested Delivery Date | Date | Yes |
| `discountAmount` | Invoice Discount Amount | Decimal | No |
| `discountAppliedBeforeTax` | Discount Applied Before Tax | Boolean | No |
| `totalAmountExcludingTax` | Total Amount Excluding Tax | Decimal | No |
| `totalTaxAmount` | Total Tax Amount | Decimal | No |
| `totalAmountIncludingTax` | Total Amount Including Tax | Decimal | No |
| `fullyShipped` | Fully Shipped | Boolean | No |
| `status` | Status | Option | No |
| `lastModifiedDateTime` | Last Modified Date Time | DateTime | No |
| `phoneNumber` | Sell-to Phone No. | Text | Yes |
| `email` | Sell-to E-Mail | Text | Yes |

> **Data Setup Notes:** `paymentTermsId` accepts a Guid — if the value does not match a Payment Terms record, an error is raised. `shipmentMethodId` likewise accepts a Guid matching a Shipment Method record. `customerId`, `billToCustomerId`, and `currencyId` are read-only Guids resolved from the Customer and Currency tables via `OnAfterGetRecord`.

#### `salesReturnLines` (PAG50232) — sub-page of `salesReturnOrders`

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `documentId` | Document Id | Text[20] | No |
| `sequence` | Line No. | Integer | No |
| `itemId` | Item Id | Text[20] | Yes |
| `accountId` | Account Id | Text[20] | Yes |
| `lineType` | Line Type | Option | Yes |
| `lineObjectNumber` | Item / Account No. | Code[20] | Yes |
| `description` | Description | Text | Yes |
| `description2` | Description 2 | Text | Yes |
| `unitOfMeasureId` | Unit of Measure Id | Guid | Yes |
| `unitOfMeasureCode` | Unit of Measure Code | Code[10] | Yes |
| `quantity` | Quantity | Decimal | Yes |
| `unitPrice` | Unit Price | Decimal | Yes |
| `discountAmount` | Line Discount Amount | Decimal | Yes |
| `discountPercent` | Line Discount % | Decimal | Yes |
| `discountAppliedBeforeTax` | Discount Applied Before Tax | Boolean | No |
| `amountExcludingTax` | Amount Excluding Tax | Decimal | No |
| `taxCode` | VAT Prod. Posting Group | Code | Yes |
| `taxPercent` | VAT % | Decimal | No |
| `totalTaxAmount` | Total Tax Amount | Decimal | No |
| `amountIncludingTax` | Amount Including Tax | Decimal | No |
| `invoiceDiscountAllocation` | Invoice Discount Allocation | Decimal | No |
| `netAmount` | Net Amount | Decimal | No |
| `netTaxAmount` | Net Tax Amount | Decimal | No |
| `netAmountIncludingTax` | Net Amount Including Tax | Decimal | No |
| `shipmentDate` | Shipment Date | Date | Yes |
| `shippedQuantity` | Return Qty. Received | Decimal | No |
| `invoicedQuantity` | Quantity Invoiced | Decimal | No |
| `invoiceQuantity` | Qty. to Invoice | Decimal | Yes |
| `shipQuantity` | Return Qty. to Receive | Decimal | Yes |
| `itemVariantId` | Item Variant Id | Guid | Yes |
| `locationId` | Location Id | Guid | Yes |

> **Customer Item Reference Resolution:** When posting or patching a `lineObjectNumber` value on a return line, if the supplied value does not match a direct Item No., the system will attempt to resolve it via the customer's item cross-references (`Item Reference` table, filtered to the return order's Customer No.). If a matching cross-reference is found, the Item No. is substituted automatically before the line is written. This allows callers to send customer-specific part numbers rather than internal item numbers.

#### `salesReturnOrdersExt` (PAG50233)

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `systemId` | System identifier | Guid | No |
| `number` | Document No. | Code[20] | Yes |
| `customerNo` | Customer No. | Code[20] | Yes |
| `shipToEMailAddress` | Ship-to E-mail Address | Text | Yes |
| `miscReference1` | Miscellaneous Reference 1 | Text | Yes |
| `miscReference2` | Miscellaneous Reference 2 | Text | Yes |
| `miscReference3` | Miscellaneous Reference 3 | Text | Yes |
| `externalRetailerName` | External Retailer Name | Text | Yes |
| `externalOrderReferences` | External Order References | Text | Yes |
| `invoiceName` | Invoice Name | Text | Yes |
| `invoiceAdd1` | Invoice Address Line 1 | Text | Yes |
| `invoiceAdd2` | Invoice Address Line 2 | Text | Yes |
| `invoiceAdd3` | Invoice Address Line 3 | Text | Yes |
| `invoiceAdd4` | Invoice Address Line 4 | Text | Yes |
| `invoicePostcode` | Invoice Postcode | Code | Yes |
| `transportMethod` | Transport Method | Code | Yes |
| `shipmentMethodCode` | Shipment Method Code | Code | Yes |
| `shipToPhoneNo` | Ship-to Phone No. | Text | Yes |
| `shipToPhoneNo2` | Ship-to Phone No. 2 | Text | Yes |
| `shippingAgentServiceCode` | Shipping Agent Service Code | Code | Yes |
| `shippingAgentCode` | Shipping Agent Code | Code | Yes |
| `systemCreatedAt` | Created date/time | DateTime | No |
| `systemCreatedBy` | Created by | Guid | No |
| `systemModifiedAt` | Last modified date/time | DateTime | No |
| `systemModifiedBy` | Last modified by | Guid | No |

> **Note:** `salesReturnOrdersExt` / `salesReturnOrdersExt` expose only the custom PCG/TM fields alongside the system identifier and document number. Use `salesReturnOrders` (PAG50231) when you need the full standard address and header fields.

#### `salesReturnLinesExt` (PAG50234) — sub-page of `salesReturnOrdersExt`

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `documentNo` | Document No. | Code[20] | No |
| `lineNo` | Line No. | Integer | No |
| `type` | Line Type | Option | Yes |
| `no` | Item / Account No. | Code[20] | Yes |
| `description` | Description | Text | Yes |
| `quantity` | Quantity | Decimal | Yes |
| `unitPrice` | Unit Price | Decimal | Yes |
| `lineAmount` | Line Amount | Decimal | No |
| `miscLineReference1` | Miscellaneous Line Reference 1 | Text | Yes |
| `pcgUniqueLineReference` | PCG Unique Line Reference | Text | Yes |
| `serialNo` | Serial No. | Code | Yes |
| `locationCode` | Location Code | Code | Yes |
| `shippingAgentServiceCode` | Shipping Agent Service Code | Code | Yes |
| `shippingAgentCode` | Shipping Agent Code | Code | Yes |
| `height` | Height | Decimal | No |
| `width` | Width | Decimal | Yes |
| `grossWeight` | Gross Weight | Decimal | No |
| `depth` | Depth | Decimal | No |
| `commodityCode` | Commodity Code / Tariff No. | Code[20] | No |
| `vehicleRegistration` | Vehicle Registration | Text | Yes |
| `dropNumber` | Drop Number | Text | Yes |
| `attachToInventoryLine` | Attach To Line No. | Integer | Yes |

> **Serial No. Tracking:** On insert, if a `serialNo` is provided, a surplus Reservation Entry is automatically created to register serial number item tracking. On modify, if the serial no. changes, the existing tracking entry is replaced with the new value. Fields `height`, `depth`, and `commodityCode` are read-only — they are populated from the Item card via `PopulateItemData`. `grossWeight` is read-only on PATCH but can be provided on POST via the insert handler.

#### `warehouseReceiptFreight` (PAG50167)

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `receiptNumber` | Warehouse Receipt No. | Code[20] | No |
| `locationCode` | Location Code | Code | No |
| `assignedUserId` | Assigned User ID | Code | No |
| `postingDate` | Posting Date | Date | No |
| `lastFreeDate` | Last Free Date | Date | Yes |
| `containerNumber` | Container Number | Code/Text | Yes |
| `vesselName` | Vessel Name | Text | Yes |
| `inboundBookingDate` | Inbound Booking Date | Date | Yes |
| `inboundBookingTime` | Inbound Booking Time | Time | Yes |
| `freightForwarder` | Freight Forwarder | Text | Yes |
| `portOfDischarge` | Port of Discharge | Text | Yes |
| `portOfLoading` | Port of Loading | Text | Yes |

#### `warehouseReceiptFreightLines` (PAG50169) — sub-page of `warehouseReceiptFreight`

| JSON Property | Description | Type | Editable |
| ------------- | ----------- | ---- | -------- |
| `id` | System identifier | Guid | No |
| `orderNo` | Source Order No. | Code | No |
| `itemNo` | Item No. | Code[20] | No |
| `description` | Description | Text | No |
| `quantity` | Quantity | Decimal | No |

> **Read-only:** All fields on `warehouseReceiptFreightLines` are read-only. This is a reporting sub-page that displays the line items received on the warehouse receipt.

---

## Service-Enabled Procedures

### `submit` — Palletised Load Build

**OData Bound Action**

```
POST /api/technologyManagement/integrations/v2.0/pcgPalletisedLoadBuilds({id})/Microsoft.NAV.submit
```

| Property | Detail |
| -------- | ------ |
| Defined in | `PAG50164 "PCG Pallet Load Build TMA2B9"` |
| Signature | `procedure submit(var ActionContext: WebServiceActionContext)` |
| Parameters | None in body — entity resolved via `{id}` in the URL |
| Returns | Updated `pcgPalletisedLoadBuild` entity |

**Behaviour**:

1. Resolves the real `Sales Header` record from the temp record's `SystemId` or `No.`.
2. Calls `PCGPalletLoadAPIHandlerTMA2B9.ProcessPayload()` to validate and process all pallet lines.
3. Builds Warehouse Shipment(s) via `PCGWhseShpmtBuilder`.
4. Reloads the temp record from the updated Sales Header.
5. Sets `ActionContext` result to `Updated`, returning the refreshed entity to the caller.

Errors are returned as HTTP errors with a descriptive message.

---

### `GetOrCreateWarehouseReceipt` — Warehouse Receipt Lookup

**OData / SOAP Function**

| Property | Detail |
| -------- | ------ |
| Defined in | `COD50086 "Whse. Receipt Lookup TMA2B9"` |
| Signature | `procedure GetOrCreateWarehouseReceipt(documentType: Text; documentNo: Code[20]): Code[20]` |
| Returns | Warehouse Receipt No. (`Code[20]`) |

**Parameters**:

| Parameter | Type | Required | Accepted Values |
| --------- | ---- | -------- | --------------- |
| `documentType` | Text | Yes | `"Purchase"` or `"Transfer"` (case-insensitive) |
| `documentNo` | Code[20] | Yes | Existing Purchase Order or Transfer Order No. |

**Behaviour**:

1. Validates `documentNo` is not blank.
2. Parses `documentType` to the internal `Warehouse Activity Source Document` enum — errors if unrecognised.
3. Verifies the source document exists (Purchase Order or Transfer Header).
4. Returns the existing Warehouse Receipt No. if one already references that source document.
5. If no receipt exists, creates a new Warehouse Receipt and returns its No.

This is Step 1 of the P8 Freight Shipping Info workflow. See [Caller Workflows — P8](#p8--freight-shipping-info-1) for the full sequence.

---

## Caller Workflows

This section describes the complete sequence of HTTP calls required for integrations that involve more than one request.

---

### P7 — Palletised Load Build

Two calls are required: one to write the pallet load data, then a second to trigger processing.

**Step 1 — Write pallet load assignments (deep insert)**

```http
POST .../pcgPalletisedLoadBuilds
Content-Type: application/json

{
  "salesOrderNo": "SO001234",
  "collectionReference": "COL-001",
  "loadReference": "LOAD-001",
  "collectionDate": "2026-05-01",
  "deliveryDate": "2026-05-02",
  "pcgPalletisedLoadLines": [
    { ... }
  ]
}
```

The response body contains the created record including its `id` (SystemId). Capture this for Step 2.

**Step 2 — Trigger processing**

```http
POST .../pcgPalletisedLoadBuilds({id})/Microsoft.NAV.submit
Content-Type: application/json

{}
```

On success, returns the updated `pcgPalletisedLoadBuild` entity (`HTTP 200`). On failure, returns an HTTP error with the processing error message.

| Step | Method | Endpoint | Purpose |
| ---- | ------ | -------- | ------- |
| 1 | POST | `pcgPalletisedLoadBuilds` | Write pallet load header + lines |
| 2 | POST | `pcgPalletisedLoadBuilds({id})/Microsoft.NAV.submit` | Validate, process lines, create Warehouse Shipments |

---

### P8 — Freight Shipping Info

Three calls are required: look up or create a Warehouse Receipt, find the API record by receipt number, then update the freight fields.

**Step 1 — Get or create Warehouse Receipt**

```http
POST .../ODataV4/Whse_Receipt_Lookup_TMA2B9_GetOrCreateWarehouseReceipt
Content-Type: application/json

{
  "documentType": "Purchase",
  "documentNo": "PO001234"
}
```

The response contains the Warehouse Receipt No. (e.g. `"WR001234"`). Capture this for Step 2.

Accepted values for `documentType`: `"Purchase"` or `"Transfer"` (case-insensitive).

**Step 2 — Locate the `warehouseReceiptFreight` record**

```http
GET .../warehouseReceiptFreight?$filter=receiptNumber eq 'WR001234'
```

The response returns the matching record. Capture its `id` for Step 3.

**Step 3 — Update freight fields**

```http
PATCH .../warehouseReceiptFreight({id})
Content-Type: application/json

{
  "lastFreeDate": "2026-05-10",
  "containerNumber": "CONT-XYZ",
  "vesselName": "MV Example",
  "inboundBookingDate": "2026-05-08",
  "inboundBookingTime": "09:00:00",
  "freightForwarder": "Acme Freight",
  "portOfDischarge": "Felixstowe",
  "portOfLoading": "Shanghai"
}
```

| Step | Method | Endpoint | Purpose |
| ---- | ------ | -------- | ------- |
| 1 | POST | `GetOrCreateWarehouseReceipt` web service | Get or create Warehouse Receipt; returns receipt number |
| 2 | GET | `warehouseReceiptFreight?$filter=receiptNumber eq '{no}'` | Locate the API record; returns `id` |
| 3 | PATCH | `warehouseReceiptFreight({id})` | Write freight fields |

---

### Sales Return Orders — PATCH

Two strategies are available depending on which fields need updating. Use the **full** endpoint pair when updating standard header or line fields; use the **slim** endpoint pair when updating only PCG-specific custom fields.

---

#### Full Order (`salesReturnOrders` / `salesReturnLines`)

**Step 1 — Locate the return order**

```http
GET .../salesReturnOrders?$filter=number eq 'SRO001234'
```

The response returns the matching record. Capture the `systemId` value for Step 2.

**Step 2 — Update header fields**

```http
PATCH .../salesReturnOrders({systemId})
Content-Type: application/json
If-Match: *

{
  "externalDocumentNumber": "CUST-REF-001",
  "requestedDeliveryDate": "2026-06-01",
  "shipToName": "Customer Warehouse",
  "miscReference1": "REF001"
}
```

Only include the fields that need to be changed — fields omitted from the body are left unchanged.

**Step 3 (optional) — Locate return lines**

To update a specific line, first retrieve the lines to obtain each line's `id`:

```http
GET .../salesReturnOrders({systemId})/salesReturnLines
```

**Step 4 (optional) — Update a return line**

```http
PATCH .../salesReturnOrders({systemId})/salesReturnLines({lineId})
Content-Type: application/json
If-Match: *

{
  "rtbCondition": "Used",
  "rtbRoutingCode": "ROUTE-A",
  "quantity": 2
}
```

| Step | Method | Endpoint | Purpose |
| ---- | ------ | -------- | ------- |
| 1 | GET | `salesReturnOrders?$filter=number eq '{no}'` | Locate return order; returns `systemId` |
| 2 | PATCH | `salesReturnOrders({systemId})` | Update header fields |
| 3 | GET | `salesReturnOrders({systemId})/salesReturnLines` | Retrieve lines; returns line `id` values |
| 4 | PATCH | `salesReturnOrders({systemId})/salesReturnLines({lineId})` | Update a specific return line |

> Steps 3 and 4 may be omitted when only header fields need updating.

---

#### Slim (`salesReturnOrdersExt` / `salesReturnLinesExt`)

Use `salesReturnOrdersExt` when only PCG-specific custom fields need updating. The slim endpoint exposes only custom fields alongside the system identifier and document number, so the request body can be kept minimal.

**Step 1 — Locate the return order**

```http
GET .../salesReturnOrdersExt?$filter=number eq 'SRO001234'
```

The response returns the matching record. Capture the `systemId` value for Step 2.

**Step 2 — Update custom fields**

```http
PATCH .../salesReturnOrdersExt({systemId})
Content-Type: application/json
If-Match: *

{
  "miscReference1": "REF001",
  "miscReference2": "REF002",
  "externalRetailerName": "Retailer Name",
  "externalOrderReferences": "EXT-ORDER-001"
}
```

| Step | Method | Endpoint | Purpose |
| ---- | ------ | -------- | ------- |
| 1 | GET | `salesReturnOrdersExt?$filter=number eq '{no}'` | Locate return order; returns `systemId` |
| 2 | PATCH | `salesReturnOrdersExt({systemId})` | Update PCG custom fields |

> **Choosing between Full and Slim:** If the caller only needs to write custom PCG fields (e.g. `miscReference1`, `externalRetailerName`), prefer `salesReturnOrdersExt` — the payload is smaller and the request will not inadvertently touch standard BC header fields. Use `salesReturnOrders` when standard fields (address, dates, shipping details, RTB line fields) also need to be written.

---

## Functional Areas

### Common / Setup

Central configuration and integration framework objects shared across all functional areas.

**Setup Page**: PAG50086 `PCG Setup TMA2B9` (Card page — not an API endpoint)

| Setup Field | Purpose |
| ----------- | ------- |
| Connector Code | TMXIF connection to PCG API |
| Auto-Send Manifest | Automatically send manifests on shipment posting |
| Manifest Batch Mode | Send manifests in batch |
| Auto-Release Pallet Load | Automatically release palletised load on creation |
| RTB Customer No. | Default customer for RTB return sales orders |
| RTB Location Code | Default location for RTB returns |
| Retry Count | Number of retries on manifest send failure |
| Failure Email | Email address for manifest send failure alerts |

**TMXIF Integration**: The app extends the `Int. Connector TMXIF` (51910) table and card with two additional fields:

| Field | Type | Purpose |
| ----- | ---- | ------- |
| PCG API Version | Code[10] | Target API version |
| OAuth Token Endpoint | Text[250] | OAuth2 token URL for authentication |

---

### P5 — Order Cancellation

Handles inbound cancellation or modification requests for both Sales Orders and Purchase Orders. The caller POSTs a cancellation request with a header and lines in a single call. The system validates the order, checks for active warehouse picks or posted receipts, and either removes the specified lines or deletes the order entirely.

The `responseText` field on the returned record contains a JSON object indicating success or failure. See the [API Endpoint Reference](#api-endpoint-reference) for request body examples.

**Flow**:

```
POST salesOrderCancellations  →  Sales Cancel Request + Lines inserted
  → OnInsert  →  SalesOrderCancelMgt.ProcessCancellation()
      → Find Sales Order  →  Check picks/shipments  →  Reopen if needed
      → Delete requested lines or full order  →  Archive
      → SalesCancelResponse.BuildSuccessResponse() / BuildErrorResponse()
      → responseText field populated on record

POST purchaseOrderCancellations  →  PO Cancel Request + Lines inserted
  → OnInsert  →  POCancelMgt.ProcessCancellation()
      → Find Purchase Order  →  Check receipts  →  Reopen if needed
      → Delete requested lines or full order  →  Archive
      → POCancelResponse.BuildSuccessResponse() / BuildErrorResponse()
      → responseText field populated on record
```

**Cancellation Type values:**

| Value | Meaning |
| ----- | ------- |
| `Cancel` | Remove the specified lines (or the entire order if all lines are cancelled) |
| `Modify` | Modify the order — lines are removed and the order is re-released with remaining lines |

---

### P6 — Sales Order Line Dispatch

Outbound only. When a Sales Shipment is posted in Business Central, the system automatically builds a JSON dispatch confirmation payload and sends it to the configured PCG endpoint. No inbound API call is required for this area.

**Flow**:

```
Sales Shipment Header  →  OnAfterInsert event
  →  DispConfOutboundMgt.CreateOutboundEntityChange()
      →  Resolves Connector Code from PCG Setup
      →  Inserts Outbound Entity Change TMXIF record

  [TMXIF Integration Framework processes pending changes]
  →  DispConfExportAPI.OnRun()  (TableNo = Int. Entity Interface TMXIF)
      →  ManifestJSONBuilder.BuildCompleteDispatchConfirmation()
      →  TMXIF communication library exports JSON payload
```

The payload is a JSON array — one flat object per shipment line. See [Outbound JSON Payloads — P6 Sales Order Line Dispatch](#p6-sales-order-line-dispatch) for the full structure.

---

### P7 — Palletised Load Build

Inbound integration — PCG POSTs palletised load assignments against existing Sales Orders. The app validates the payload, processes lines, creates Warehouse Shipments, and optionally releases the Sales Order.

**Flow**:

```
POST pcgPalletisedLoadBuilds  →  Sales Header (temp) + Lines inserted
  →  OnInsert  →  PCGPalletLoadAPIHandler.Handle()
      →  ValidateHeaderState()  →  ValidateHeaderFields()  →  ValidateLines()
      →  PCGPalletisedLineProcessor.ProcessLines()
          →  PCGWhseShpmtBuilder.BuildShipments()
      →  (optional) ReleaseSalesOrder()
```

**Buffer Table** (TAB50164 `Palletised Load Buffer TMA2B9`) — temporary, holds processing state including `Error Message` and `Result JSON`.

See [Caller Workflows — P7](#p7--palletised-load-build-1) for the full step-by-step request sequence.

---

### P8 — Freight Shipping Info

Two-step integration:

1. **Step 1 (Lookup)**: `WhseReceiptLookup.GetOrCreateWarehouseReceipt()` — given a document type and number, returns the Warehouse Receipt No. (creates a receipt if none exists).
2. **Step 2 (Update)**: `GET/PATCH warehouseReceiptFreight` — PCG reads or updates freight-specific fields on the Warehouse Receipt Header.

The lookup codeunit supports `Purchase Order` and `Transfer Order` document types.

See [Caller Workflows — P8](#p8--freight-shipping-info-1) for the full step-by-step request sequence.

---

### P9 — RTB Returns

Inbound Excel-based integration processed within the Business Central client. A warehouse operator uploads an RTB (Return To Base) Excel file via the **Sales Return Order List** action; the app parses, validates, and creates Sales Return Orders (SROs). There are no direct API calls for this area — the upload is handled through the BC UI.

**Flow**:

```
Action: "Import RTB Returns"  →  Upload Excel file stream
  →  PCGRTBExcelParser.ParseFile()  →  populates PCG RTB Import Line (temp table)
  →  PCGRTBManifestVal.ValidateLines()
  →  Show PCG RTB Import Preview (PAG50168)  →  operator reviews / blocks rows
  →  PCGRTBSROManager.ProcessImportLines()
      →  Group by Load Reference
      →  CreateSROForLoadRef()  →  CreateSROLine()
          →  PCGRTBItemResolver.ResolveItemNo()
              (tries: Vendor Reference → Item No. 2 → Barcode)
```

**RTB Condition Enum** (50052):

| Value | Caption |
| ----- | ------- |
| 0 | *(blank)* |
| 1 | New |
| 2 | Used |

**Sales Line extensions** added for RTB lines:

| Field | Type |
| ----- | ---- |
| RTB Condition TMA2B9 | Enum RTBCondition |
| RTB Routing Code TMA2B9 | Code |
| Original Case No. TMA2B9 | Text |
| Original Customer Postcode TMA2B9 | Code |
| RTB Tracking No. TMA2B9 | Code |

RTB-specific fields are readable via the `salesReturnLines` (PAG50232) sub-page:

| JSON Property | Description | Type |
| ------------- | ----------- | ---- |
| `rtbCondition` | RTB Condition (`New` or `Used`) | Option |
| `rtbRoutingCode` | RTB Routing Code | Code |
| `originalCaseNo` | Original Case No. | Text |
| `origCustomerPostcode` | Original Customer Postcode | Code |
| `rtbTrackingNo` | RTB Tracking No. | Code |

---

### P10 — Purchase Order

**Outbound** — When a Purchase Order is released in Business Central, the system automatically builds a JSON payload and sends it to the configured PCG endpoint. No inbound API call is required for this direction. See [Outbound JSON Payloads — P10 Purchase Order Export](#p10-purchase-order-export) for the full structure.

**Outbound flow**:

```
Purchase Order Released  →  OnAfterReleasePurchaseDoc (event)
  →  POReleaseMgt.CreateOutboundEntityChange()
      →  POJSONBuilder.BuildPurchaseOrderJSON()
      →  POExportCodeunitAPI.OnRun()  (TableNo = Int. Entity Interface TMXIF)
```

**Inbound (CSV Import)** — Operators can import Purchase Orders from a CSV file via the `PCG Purchase Import` list page (PAG50183).

**Inbound CSV Import flow**:

```
Action: Import CSV  →  stream
  →  PCGPurchaseImportMgt.ImportFromStream()  →  Purchase Import Buffer (temp)
  →  POImportValidation.ValidateBuffer()
  →  POImportCreate.CreatePurchaseOrders()
```

**Purchase Import Buffer fields** (TAB50182):

| Field | Type | Notes |
| ----- | ---- | ----- |
| Entry No. | Integer | Key |
| Order Number | Code | |
| Order Date | Date | |
| Supplier Code | Code[20] | Validated against Vendor |
| Buyer Code | Code[20] | |
| Order Type | Code | |
| Required By Date | Date | |
| Destination Warehouse | Code | Validated against Location |
| Part | Code[20] | Validated against Item |
| Description | Text | |
| Quantity Ordered | Decimal | |
| Supplier Quantity | Decimal | |
| Container Type | Code | |
| Import Status | Option | Blank / Error |
| Error Message | Text | |

**Inbound (read)** — The `purchaseOrders` and `purchaseOrderLines` endpoints are available read-only for PCG to query purchase order status.

---

### Sales Return Orders

Two API endpoint pairs are available for reading and writing Sales Return Orders:

| Pair | Header page | Lines sub-page | When to use |
| ---- | ----------- | -------------- | ----------- |
| Full | `salesReturnOrders` (PAG50231) | `salesReturnLines` (PAG50232) | Creating or reading return orders where full standard address and header fields are needed |
| Slim | `salesReturnOrdersExt` (PAG50233) | `salesReturnLinesExt` (PAG50234) | Patching only PCG-specific custom fields on an existing return order |

Both pairs support GET, POST, and PATCH. `salesReturnOrders` additionally supports DELETE.

See [Field Reference by Endpoint](#field-reference-by-endpoint) for complete field lists:

- [Full header fields (PAG50231)](#salesreturnorders-pag50231)
- [Full line fields (PAG50232)](#salesreturnlines-pag50232--sub-page-of-salesreturnorders)
- [Slim header fields (PAG50233)](#salesreturnordersext-pag50233)
- [Slim line fields (PAG50234)](#salesreturnlinesext-pag50234--sub-page-of-salesreturnordersext)

---

## Outbound JSON Payloads

### P6 Sales Order Line Dispatch

Sent automatically when a Sales Shipment is posted. The payload is a **JSON array** — one flat object per shipment line, with header fields repeated on each entry.

**Data Source**: All fields are populated from the Sales Shipment Header, Sales Shipment Lines, and the related Sales Order. The mapping of each JSON property to its source BC table/field is documented in the Field Reference table below.

```json
[
  {
    "shipmentNumber": "string",
    "salesOrderNumber": "string",
    "customerNumber": "string",
    "customerName": "string",
    "shipToName": "string",
    "shipToAddress": "string",
    "shipToAddress2": "string",
    "shipToCity": "string",
    "shipToPostCode": "string",
    "shipToCountryCode": "string",
    "shipmentDate": "2026-04-27",
    "shippingAgent": "string",
    "trackingNumber": "string",
    "externalRetailerName": "string",
    "salesLineItemSequenceNumber": "string",
    "lineObjectNumber": "string",
    "lineObjectNumber2": "string",
    "lineObjectCustomerReference": "string",
    "lineObjectDescription": "string",
    "quantity": 0,
    "externalDocumentNumber": "string",
    "vehicleRegistration": "string",
    "dropNumber": "string",
    "miscReference1": "string",
    "miscReference2": "string",
    "miscReference3": "string",
    "miscellaneousLineReference1": "string",
    "pcgUniqueLineReference": "string"
  }
]
```

> `dropNumber` is only included when it has a value.

**Field Reference**:

| JSON Property | Source Table | Source Field | Notes |
| ------------- | ----------- | ------------ | ----- |
| `shipmentNumber` | Sales Shipment Header | No. | |
| `salesOrderNumber` | Sales Header | No. | From the related Sales Order |
| `customerNumber` | Sales Shipment Header | Sell-to Customer No. | |
| `customerName` | Customer | Name | Resolved from Sell-to Customer No. |
| `shipToName` | Sales Shipment Header | Ship-to Name | |
| `shipToAddress` | Sales Shipment Header | Ship-to Address | |
| `shipToAddress2` | Sales Shipment Header | Ship-to Address 2 | |
| `shipToCity` | Sales Shipment Header | Ship-to City | |
| `shipToPostCode` | Sales Shipment Header | Ship-to Post Code | |
| `shipToCountryCode` | Sales Shipment Header | Ship-to Country/Region Code | |
| `shipmentDate` | Sales Shipment Header | Shipment Date | ISO 8601: `yyyy-MM-dd` |
| `shippingAgent` | Sales Shipment Header | Shipping Agent Code | |
| `trackingNumber` | Sales Shipment Header | Package Tracking No. | |
| `externalRetailerName` | Sales Header | External Retailer Name | Sourced from open Sales Order if available |
| `salesLineItemSequenceNumber` | Sales Shipment Line | Line No. | |
| `lineObjectNumber` | Sales Shipment Line | Item No. | |
| `lineObjectNumber2` | Item | No. 2 | SKU / Model No. |
| `lineObjectCustomerReference` | Item Reference | Reference No. | Customer cross-reference |
| `lineObjectDescription` | Sales Shipment Line | Description | |
| `quantity` | Sales Shipment Line | Quantity | Quantity shipped |
| `externalDocumentNumber` | Sales Header | External Document No. | Customer PO reference |
| `vehicleRegistration` | Sales Shipment Line | Vehicle Registration | |
| `dropNumber` | Sales Shipment Line | Drop Number | Omitted if blank |
| `miscReference1` | Sales Header | Miscellaneous Reference 1 | Header-level |
| `miscReference2` | Sales Header | Miscellaneous Reference 2 | Header-level |
| `miscReference3` | Sales Header | Miscellaneous Reference 3 | Header-level |
| `miscellaneousLineReference1` | Sales Shipment Line | Miscellaneous Line Reference 1 | Line-level |
| `pcgUniqueLineReference` | Sales Shipment Line | PCG Unique Line Reference | |

---

### P10 Purchase Order Export

Sent automatically when a Purchase Order is released. The payload is a single JSON object.

> **Note:** The `orderNumber` field in the outbound payload maps to `Purchase Header."Vendor Order No."` (the supplier's order reference), **not** `Purchase Header."No."` (the internal BC number). This differs from the `purchaseOrders` API endpoint (PAG50087) where `orderNumber` maps to `Purchase Header."No."`.

**Data Source**: Header fields are populated from the Purchase Header table. Line fields and item tracking information are populated from the Purchase Line and Item Tracking tables. The mapping of each JSON property to its source BC table/field is documented in the Field Reference tables below.

```json
{
  "orderNumber": "string",
  "orderDate": "2026-04-27T00:00:00",
  "supplierCode": "string",
  "requiredByDate": "2026-04-27T00:00:00",
  "destinationWarehouse": "string",
  "containerType": "string",
  "status": "string",
  "currencyCode": "string",
  "lines": [
    {
      "itemNo": "string",
      "description": "string",
      "quantityOrdered": 0,
      "directUnitCost": 0,
      "quantityReceived": 0,
      "quantityInvoiced": 0,
      "serialNumbers": [
        {
          "serialNo": "string",
          "quantity": 0
        }
      ]
    }
  ]
}
```

> `serialNumbers` is only included on lines where the item has item tracking enabled.

**Header Field Reference**:

| JSON Property | Source Table | Source Field | Notes |
| ------------- | ----------- | ------------ | ----- |
| `orderNumber` | Purchase Header | Vendor Order No. | Supplier's order reference |
| `orderDate` | Purchase Header | Order Date | ISO 8601 with time |
| `supplierCode` | Purchase Header | Buy-from Vendor No. | |
| `requiredByDate` | Purchase Header | Expected Receipt Date | ISO 8601 with time |
| `destinationWarehouse` | Purchase Header | Location Code | |
| `containerType` | Purchase Header | Container Type TM9FA3 | |
| `status` | Purchase Header | Status | Text value e.g. `Released` |
| `currencyCode` | Purchase Header | Currency Code | |

**Line Field Reference**:

| JSON Property | Source Table | Source Field | Notes |
| ------------- | ----------- | ------------ | ----- |
| `itemNo` | Purchase Line | No. | |
| `description` | Purchase Line | Description | |
| `quantityOrdered` | Purchase Line | Quantity | |
| `directUnitCost` | Purchase Line | Direct Unit Cost | |
| `quantityReceived` | Purchase Line | Quantity Received | |
| `quantityInvoiced` | Purchase Line | Quantity Invoiced | |
| `serialNo` | Item Tracking | Serial No. | Only included when item has tracking enabled |
| `quantity` | Item Tracking | Quantity | Only included when item has tracking enabled |

> **Note**: `orderType` is mapped in the JSON builder but currently commented out pending availability of dependency symbols (`TM9FA3` field).

---

## Permission Set

**Permission Set 50048 — `PCGAPIPerm TMA2B9`** (Assignable: Yes)

Grants `RIMD` (Read, Insert, Modify, Delete) on all app objects. Assign this permission set to any service or integration user account that needs to call the PCG API endpoints.

---

## Data Model

### Custom Tables

| Table # | Name | Type | Primary Key | Purpose |
| ------- | ---- | ---- | ----------- | ------- |
| 50048 | PO Cancel Request TMA2B9 | Permanent | Entry No. (Integer) | Staging: inbound PO cancellation header |
| 50049 | PO Cancel Req. Line TMA2B9 | Permanent | Entry No., Line No. | Staging: inbound PO cancellation lines |
| 50050 | Sales Cancel Request TMA2B9 | Permanent | Entry No. (Integer) | Staging: inbound Sales cancellation header |
| 50051 | Sales Cancel Req. Line TMA2B9 | Permanent | Entry No., Line No. | Staging: inbound Sales cancellation lines |
| 50162 | PCG Setup TMA2B9 | Permanent | Primary Key (Code[10]) | App configuration singleton |
| 50164 | Palletised Load Buffer TMA2B9 | Temporary | Entry No. | Processing buffer for pallet load API |
| 50167 | PCG RTB Import Line TMA2B9 | Temporary | Entry No. | Staging: parsed RTB Excel rows |
| 50182 | Purchase Import Buffer TMA2B9 | Temporary | Entry No. | Staging: parsed CSV purchase import rows |

### Table Extensions

| Extension # | Extended Table | Extended Table # | New Fields Summary |
| ----------- | -------------- | ---------------- | ------------------ |
| EXT50051 | Int. Connector TMXIF | 51910 | PCG API Version, OAuth Token Endpoint |
| EXT50048 | Sales Line | 37 | Serial No., Cancellation Date, Cancellation Reason, Cancel Parent Ref |
| EXT50049 | Sales Header | 36 | Cancellation Type, Cancellation Response |
| EXT50050 | Purchase Line | 39 | Cancellation Date, Cancellation Reason |
| EXT50052 | Sales Line (RTB) | 37 | RTB Condition, RTB Routing Code, Original Case No., Original Customer Postcode, RTB Tracking No. |
| EXT50212 | Sales Line (Item Data) | 37 | Height TMA2B9, Width TMA2B9, Depth TMA2B9, Commodity Code TMA2B9 |
| EXT50211 | Purchase Header | 38 | Cancellation Type |

> **Note**: Freight fields on Warehouse Receipt Header (7316) — `Last Free Date`, `Container Number`, `Vessel Name`, `Inbound Booking Date/Time`, `Freight Forwarder`, `Port of Discharge`, `Port of Loading` — are provided by the `PCG Config` dependency app (publisher: Technology Management, id: `14a38d9c-69ef-4c65-bc46-b5d449e1fd68`).

---

## Internal Components

### Codeunits

| Codeunit # | Name | Area | Role |
| ---------- | ---- | ---- | ---- |
| 50048 | Sales Order Cancel Mgt TMA2B9 | P5 | Orchestrates Sales Order cancellation/modification |
| 50049 | Sales Cancel Response TMA2B9 | P5 | Builds success/error JSON response for Sales cancellation |
| 50050 | TMXIF Event Handler TMA2B9 | Common | Implements TMXIF connection type interface (PCG) |
| 50051 | PCG RTB Excel Parser TMA2B9 | P9 | Parses RTB Excel upload into import buffer |
| 50052 | PCG RTB Manifest Val. TMA2B9 | P9 | Validates parsed RTB import lines |
| 50085 | PCG RTB Item Resolver TMA2B9 | P9 | Resolves item by vendor ref / Item No. 2 / barcode |
| 50086 | Whse. Receipt Lookup TMA2B9 | P8 | Finds or creates Warehouse Receipt for a document |
| 50087 | Cancel Line Buffer TMA2B9 | P5 | SingleInstance buffer: Sales cancellation lines |
| 50162 | PCG RTB SRO Manager TMA2B9 | P9 | Creates Sales Return Orders from RTB import lines |
| 50163 | Manifest JSON Builder TMA2B9 | P6 | Builds dispatch confirmation JSON from posted Sales Shipment |
| 50164 | Manifest Event Handler TMA2B9 | P6 | Test helper: builds dispatch confirmation JSON for test verification (no event subscribers) |
| 50165 | PCG Rest Client TMA2B9 | Common | REST HTTP client with OAuth2 authentication |
| 50166 | SalesLine Item Data Mgt TMA2B9 | Common | Event subscriber: resolves customer item references on APIV2 Sales Order Lines; auto-populates Height, Width, Depth, and Commodity Code on Sales Line validation |
| 50167 | Http Client Handler API TMA2B9 | Common | Mockable HTTP client handler (test injection point) |
| 50168 | PO Release Mgt TMA2B9 | P10 | Event subscriber: triggers PO export on release |
| 50169 | PO JSON Builder TMA2B9 | P10 | Builds Purchase Order JSON for outbound export |
| 50181 | PCG Purchase Import Mgt TMA2B9 | P10 | Parses CSV stream into Purchase Import Buffer |
| 50182 | PO Cancel Line Buf TMA2B9 | P5 | SingleInstance buffer: PO cancellation lines |
| 50183 | PO Cancel Mgt TMA2B9 | P5 | Orchestrates Purchase Order cancellation/modification |
| 50184 | PO Import Validation TMA2B9 | P10 | Validates Purchase Import Buffer rows |
| 50185 | PO Import Create TMA2B9 | P10 | Creates Purchase Orders from validated import buffer |
| 50186 | PO Export Codeunit API TMA2B9 | P10 | TMXIF export runner for Purchase Order outbound flow |
| 50211 | PCGPalletLoadAPIHandler TMA2B9 | P7 | Orchestrates palletised load API processing |
| 50212 | PCG Whse.Shpmt. Builder TMA2B9 | P7 | Creates Warehouse Shipment headers and lines |
| 50213 | PCGPalletisedLineProces TMA2B9 | P7 | Processes individual pallet load lines |
| 50229 | PO Cancel Response TMA2B9 | P5 | Builds success/error JSON response for PO cancellation |
| 50230 | Disp.Conf. Outbound Mgt TMA2B9 | P6 | Event subscriber: creates Outbound Entity Change on Sales Shipment posting |
| 50231 | Disp.Conf. Export API TMA2B9 | P6 | TMXIF export runner for dispatch confirmation outbound flow |

---

## Dependencies

| Publisher | App Name | Version | Purpose |
| --------- | -------- | ------- | ------- |
| Technology Management | Tecman X-Integration Framework Core | ≥ 18.1.0.0 | TMXIF connector and export interface base |
| Technology Management | PCG Config | ≥ 27.3.0.0 | Freight table extensions on Warehouse Receipt Header |
| Clever Dynamics | Clever Config | ≥ 1.15.1 | Configuration framework |
| Clever Dynamics | Clever Trade Plus | ≥ 2.0.5 | Trade extensions used by Sales/Item fields |
| Microsoft | Sustainability | ≥ 27.3.0.0 | Standard BC sustainability module |
| Microsoft | Subscription & Recurring Billing | ≥ 27.0.0.0 | Standard BC subscription billing module |
