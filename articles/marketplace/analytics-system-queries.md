---
title: Lista över system frågor
description: Lär dig mer om system frågor som du kan använda för att program mässigt få analys data om dina erbjudanden på Microsofts kommersiella marknads plats.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f2b5f7eb559e349947f88067a3d2ea53d99b7cbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584195"
---
# <a name="list-of-system-queries"></a>Lista över system frågor

Följande system frågor kan användas i [Skapa rapport-API](analytics-programmatic-access.md#create-report-api) direkt med en `QueryId` . System frågorna är som export rapporter i Partner Center för en beräknings period på sex månader (6 M).

Mer information om kolumn namn, attribut och beskrivning finns i följande artiklar:

- [Kund instrument panel på kommersiell Marketplace-analys](customer-dashboard.md#customer-details-table)
- [Analys av orderinstrumentpanelen på kommersiell marknadsplats](orders-dashboard.md#orders-details-table)
- [Instrument panelen för användning i kommersiell Marketplace-analys](usage-dashboard.md#usage-details-table)
- [Marketplace Insights-instrumentpanelen i Commersial Marketplace-analys](insights-dashboard.md#marketplace-insights-details-table)

I följande avsnitt finns rapport frågor för olika rapporter.

## <a name="customers-report-query"></a>Fråga om kunders rapport

**Rapport Beskrivning**: kund rapport för senaste 6 m

**Fråge**:  `b9df4929-073f-4795-b0cb-a2c81b11e28d`

**Rapport fråga**:

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>Fråga om order rapport

**Rapport Beskrivning**: order rapport för den senaste 6 m

**Fråge**:  `fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**Rapport fråga**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>Användnings rapport frågor

**Rapport Beskrivning**: normaliserad användnings rapport för virtuell dator för den senaste 6 m

**Fråge**:  `2c6f384b-ad52-4aed-965f-32bfa09b3778`

**Rapport fråga**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Rapport Beskrivning**: rapport för VM RAW-användning för senaste 6 m

**Fråge**:  `3f19fb95-5bc4-4ee0-872e-cedd22578512`

**Rapport fråga**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Rapport Beskrivning**: rapport med mätnings användning för senaste 6 m

**Fråge**:  `f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**Rapport fråga**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>Fråga om marknads insikts rapport

**Rapport Beskrivning**: marknads insikts rapport för den senaste 6 m

**Fråge**:  `6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**Rapport fråga**:

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>Nästa steg

- [API: er för åtkomst till kommersiella data för Marketplace-analys](analytics-available-apis.md)
- [Exempel program för åtkomst till kommersiella Marketplace Analytics-data](analytics-sample-application.md)
