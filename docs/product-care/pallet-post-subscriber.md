---
uid: pallet-post-subscriber
title: Pallet Post Subscriber
---

# Pallet Post Subscriber

The **Pallet Post Subscriber** (Codeunit 50105) is a manual event subscriber that carries pallet tracking data through the warehouse shipment posting process. It ensures that pallet numbers and package tracking numbers flow from warehouse shipment lines to posted warehouse shipment lines and onward to sales shipment lines.

## Purpose

When a warehouse shipment line belongs to multiple pallets, the standard posting process cannot determine which pallet applies. This codeunit solves that by binding a scoped subscriber instance — tied to a specific pallet number — around each posting call, so the correct Pallet Shipment Entry is matched.

## How It Works

1. A caller creates an instance of this codeunit and calls `SetPalletNo` with the target pallet number.
2. The instance is bound as a manual event subscriber before the posting call begins.
3. During posting, the subscriber fires and looks up the **Pallet Shipment Entry** matching the current pallet number and warehouse shipment line.
4. It copies `Pallet No.` and `Package Tracking No.` to the posted warehouse shipment line.
5. A second subscriber then copies those same fields from the posted warehouse shipment line to the sales shipment line.

## Event Subscriptions

| Event Publisher | Event Name | Action |
|---|---|---|
| Codeunit "Whse.-Post Shipment" | `OnCreatePostedShptLineOnBeforePostedWhseShptLineInsert` | Copies Pallet No. and Package Tracking No. from Pallet Shipment Entry to the Posted Whse. Shipment Line. |
| Codeunit "Sales-Post" | `OnBeforeSalesShptLineInsert` | Copies Pallet No. and Package Tracking No. from Posted Whse. Shipment Line to the Sales Shipment Line. |

## Fields Transferred

| Field | Source | Destination |
|---|---|---|
| Pallet No. | Pallet Shipment Entry | Posted Whse. Shipment Line → Sales Shipment Line |
| Package Tracking No. | Pallet Shipment Entry | Posted Whse. Shipment Line → Sales Shipment Line |

## Usage Pattern

```al
var
    PalletPostSubscriber: Codeunit "Pallet Post Subscriber TMA1AA";
begin
    PalletPostSubscriber.SetPalletNo('PAL-001');
    BindSubscription(PalletPostSubscriber);

    // Perform warehouse shipment posting here

    UnbindSubscription(PalletPostSubscriber);
end;
```

> [!IMPORTANT]
> Always call `SetPalletNo` before `BindSubscription`. The subscriber uses the stored pallet number to filter Pallet Shipment Entries, so binding without setting it first will result in no match.

## Design Notes

- The codeunit uses `EventSubscriberInstance = Manual`, meaning it only fires when explicitly bound by calling code.
- This pattern allows multiple pallets on the same warehouse shipment to be posted correctly by binding a separate instance for each pallet's posting call.
