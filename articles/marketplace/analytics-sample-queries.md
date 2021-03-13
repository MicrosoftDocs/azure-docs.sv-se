---
title: Exempel frågor för program analys
description: Använd dessa exempel frågor för att program mässigt få åtkomst till Microsofts affärs plats analys data.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7d788448fb3f8a849f79e43fcb0737898f4c9e15
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584198"
---
# <a name="sample-queries-for-programmatic-analytics"></a>Exempel frågor för program analys

Den här artikeln innehåller exempel frågor för Microsoft Commercial Marketplace-beställningar, användnings-och kund rapporter. Du kan använda dessa frågor genom att anropa slut punkten för att [Skapa rapport frågor](analytics-programmatic-access.md#create-report-query-api) API. Om det behövs kan du ändra API-anropet [Skapa rapport fråga](analytics-programmatic-access.md#create-report-query-api) för att lägga till fler kolumner, justera beräknings perioden och lägga till filter villkor. Tids perioderna som stöds är sex månader (6 M), 12 månader (12M) och anpassad tids period.

Mer information om kolumn namn, attribut och beskrivningar finns i följande tabeller:

- [Tabellen Kund information](customer-dashboard.md#customer-details-table)
- [Tabellen Order Detaljer](orders-dashboard.md#orders-details-table)
- [Tabellen användnings information](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>Kunder rapporterar frågor

Dessa exempel frågor gäller för rapporten kunder.

| **Beskrivning av fråga** | **Exempel fråga** |
| --- | --- |
| Aktiva kunder av partnern fram till det datum du väljer | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| Omsättnings bara kunder för partnern fram till det datum du väljer | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| Lista över nya kunder från ett särskilt geografiskt område under de senaste sex månaderna | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>Användnings rapport frågor

Dessa exempel frågor gäller användnings rapporten.

| **Beskrivning av fråga** | **Exempel fråga** |
| --- | --- |
| Normaliserad användning för den virtuella datorn (VM) för "debiterad via Azure"-licens typen för den senaste 6 M | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| VM RAW-användning för "debiterad via Azure"-licens typen för den senaste 12M | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| Normaliserad VM-användning för "ta med din egen licens" Marketplace-licens typ för den senaste 6 M | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| VM RAW-användning för Marketplace-licens typen "ta med din egen licens" för den senaste 6 M | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Baserat på användnings datum, daglig total normaliserad användning och uppskattade utökade avgifter (PC/CC) för betalda planer för den senaste månaden | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| Baserat på användnings datum, daglig total obehandlad användning och uppskattade utökade avgifter (PC/CC) för betalda planer för den senaste månaden | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| För ett enskilt erbjudande namn, normaliserad VM-användning för "debiterad via Azure" Marketplace-licens typen för den senaste 6 M | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| För ett bestämt erbjudande namn, mätnings användning för senaste 6 M | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>Order rapport frågor

Dessa exempel frågor gäller för rapporten Orders.

| **Beskrivning av fråga** | **Exempel fråga** |
| --- | --- |
| Rapporten order för Azure-licens typen "företag" för den senaste 6 M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| Rapporten order för Azure-licens typen "betala per användning" för den senaste 6 M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| Rapporten order för det angivna erbjudande namnet för den senaste 6 M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Order rapport för aktiva beställningar för de senaste 6 M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Rapporten order för annullerade order för den senaste 6 M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>Nästa steg

- [API: er för åtkomst till kommersiella data för Marketplace-analys](analytics-available-apis.md)
