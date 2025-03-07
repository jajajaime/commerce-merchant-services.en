---
title: Catalog Sync
description: Learn how to export product data from the [!DNL Commerce] server to [!DNL Commerce Services] on an ongoing basis to keep the services up to date.
exl-id: 19d29731-097c-4f5f-b8c0-12f9c91848ac
---
# Catalog Sync

Adobe Commerce and Magento Open Source use indexers to compile catalog data into tables. The process is automatically triggered by [events](https://experienceleague.adobe.com/docs/commerce-admin/systems/tools/index-management.html#events-that-trigger-full-reindexing) such as a change to a product price or inventory level.

The catalog sync process runs hourly to allow [!DNL Commerce] services to use catalog data. Catalog sync exports product data from the [!DNL Commerce] server to [!DNL Commerce] services on an ongoing basis to keep the services up to date. For example, [[!DNL Product Recommendations]](/help/product-recommendations/overview.md) needs current catalog information to accurately return recommendations with correct names, pricing, and availability. You can use the _Catalog Sync_ dashboard to observe and manage the synchronization process or the [command-line interface](#resynccmdline) to trigger catalog sync and reindex product data for consumption by [!DNL Commerce] services.

>[!NOTE]
>
> To use the _Catalog Sync_ dashboard or the command-line interface, you must have an [API key and a SaaS data space configured](saas.md).

## Accessing the Catalog Sync dashboard

>[!NOTE]
>
> The _Catalog Sync_ dashboard is only available when the _Product Recommendations_ modules are installed and reflects data projections related to that feature only. Support for other Commerce Services such as _Live Search_ and _Catalog Service_ are planned for the future.

To access the Catalog Sync dashboard, select **System** > _Data Transfer_ > **Catalog Sync**.

With the **Catalog Sync** dashboard you can:

- View the sync status (**In Progress**, **Success**, **Failed**)
- View the total number of products synced, if successful
- Search synced products to view their current state
- Search store catalog by name, SKU, and so on
- View synced product details in JSON to help diagnose a sync discrepancy
- Reinitiate the sync process

### Last sync

Reports a sync status of:

- **Success** - Displays the date and time that the sync was successful and the number of products updated
- **Failed** - Displays the date and time that the sync was attempted
- **In Progress** - Displays the date and time of the last successful sync

>[!NOTE]
>
> The catalog sync process automatically runs every hour. However, if you are not seeing products on your storefront, or if the products do not reflect recent changes you made, you can resolve [catalog sync issues](#resolvesync).

### Products synced

Displays the total number of products synced from your [!DNL Commerce] catalog. After the initial sync, you should expect only changed products to be synced.

## Resync {#resync}

If you must initiate a resync of your catalog before the hourly scheduled sync occurs, you can force a resync.

>[!NOTE]
>
> Forcing a resync triggers a resync of your entire product catalog, which can increase load on hardware resources.

1. From the _Catalog Sync_ dashboard, select **Settings**.

   The _Catalog Sync Settings_ page appears.

1. In the _Resync Data_ section, click [!UICONTROL Resync].

   [!DNL Commerce] syncs your catalog during the next scheduled sync window. Depending on the size of your catalog, this operation can take a long time.

## Synced catalog products

The **Synced catalog products** table displays the following information.

|Field|Description|
|---|---|
|ID | Unique identifier of the product|
|Name | Storefront name of the product|
|Type | Identifies the product type, such as simple, configurable, downloadable, and so on|
|Last Exported | Date the product was last successfully exported from your catalog|
|Last Modified | Date the product was last modified in your catalog|
|SKU | Displays the stock-keeping unit for the product|
|Price | Price of the product|
|Visibility | A product's visibility setting as defined in the [!DNL Commerce] catalog|

## Resolve catalog sync issues {#resolvesync}

When you trigger a data resync, it can take up to an hour for the data to update and be reflected in UI components, such as recommendation units. However, if after waiting for an hour you still notice discrepancies between your catalog and what appears on your storefront, or if the catalog sync failed, refer to the following:

### Data discrepancy

1. Display the detailed view of the product in question in the search results.
1. Copy the JSON output and verify that the content matches what you have in the [!DNL Commerce] catalog.
1. If the content does not match, make a minor change to the product in your catalog, such as adding a space or a period.
1. Wait for a resync or [trigger a manual resync](#resync).

### Sync not running

If the sync is not running on a schedule or nothing is synced, see the [KnowledgeBase](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/troubleshoot-product-recommendations-module-in-magento-commerce.html).

### Sync failed

If the catalog sync has a status of **Failed**, submit a [support ticket](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket).

## Command-line interface {#resynccmdline}

The `saas:resync` command is part of the `magento/saas-export` package. You can install this package using one of the [!DNL Commerce Services] products, such as [[!DNL Product Recommendations]](/help/product-recommendations/install-configure.md) or [[!DNL Live Search]](/help/live-search/install.md). 

>[!NOTE]
>
> When running a data sync for the first time, it is important to run the `productattributes` feed first, followed by `productoverrides`, before running the `products` feed.

Command options:

```bash
bin/magento saas:resync --feed <feed name> [no-reindex]
```

The following table describes the `saas:resync` parameters and descriptions.

|Parameter|Description|Required?|
|---| ---| ---|
|`feed`| Specifies which entity to resync, such as `products`|Yes|
|`no-reindex`| Resubmits the existing catalog data to [!DNL Commerce Services] without reindexing. When this parameter is not specified, the command runs a full reindex before syncing data.|No|

The feed name can be one of the following:

- `products`-- Products in your catalog
- `categories`-- Categories in your catalog
- `variants`-- Product variations of a configurable product, such as color and size
- `productattributes`-- Product attributes such as `activity`, `gender`, `tops`, `bottoms`, and so on
- `productoverrides`-- Customer-specific pricing and catalog visibility rules, such as those based on category permissions

When you trigger a data resync from the command line, it may take up to an hour for the data to update.

### Examples

The following example reindexes the product data from the [!DNL Commerce] catalog and resyncs it to Commerce services:

```bash
bin/magento saas:resync --feed products
```

If you do not want to run a full reindex of the products, you can instead sync the product data that has already been generated:

```bash
bin/magento saas:resync --feed products --no-reindex
```
