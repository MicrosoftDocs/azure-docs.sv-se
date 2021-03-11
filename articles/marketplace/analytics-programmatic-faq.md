---
title: Programmerings åtkomst till vanliga frågor om analys data
description: Vanliga frågor och svar om program mässig åtkomst till analys data i Partner Center för dina kommersiella Marketplace-erbjudanden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 393a718632138f4ffcf26e4875eea9ba3d886897
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584182"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>Programmerings åtkomst till vanliga frågor om analys data

Den här artikeln innehåller vanliga frågor och svar om hur du program mässigt får åtkomst till Analytics-data i Partner Center för dina kommersiella Marketplace-erbjudanden.

## <a name="api-responses"></a>API-svar

Vilka är de olika scenarier under vilka jag kan få ett API-svar annat än 200 (lyckades)?

I den här tabellen beskrivs API-svaren och vad du ska göra om du tar emot dem.

| Felbeskrivning | Felkod | Felsöka |
| ------------ | ------------- | ------------- |
| Behörighet saknas | 401 | Detta är ett undantag för autentisering. Kontrol lera att Azure Active Directory-token (Azure AD) är korrekt. Azure AD-token är giltig i 60 minuter, och sedan måste du återskapa Azure AD-token. |
| Ogiltigt tabell namn | 400 | Data uppsättningens namn är felaktigt. Kontrol lera data uppsättningens namn på nytt genom att anropa API: t "Hämta alla data uppsättningar". |
| Felaktigt kolumn namn | 400| Namnet på kolumnen i frågan är felaktigt. Kontrol lera kolumn namnet igen genom att anropa API: t "Hämta alla data uppsättningar" eller referera till kolumn namnen i följande tabeller:<br><ul><li>[Tabellen Order Detaljer](orders-dashboard.md#orders-details-table)</li><li>[Tabellen användnings information](usage-dashboard.md#usage-details-table)</li><li>[Tabellen Kund information](customer-dashboard.md#customer-details-table)</li><li>[Informations tabell för Marketplace](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| Värdet null eller saknas | 400 | Du kanske saknar obligatoriska parametrar som en del av begärans nytto last för API: et. |
| Ogiltiga rapport parametrar | 400 | Kontrol lera att rapport parametrarna är korrekta. Du kan till exempel ange ett värde som är mindre än 4 för `RecurrenceInterval` parametern. |
| Upprepnings intervallet måste vara mellan 4 och 90 | 400 | Kontrol lera att värdet för `RecurrenceInterval` parametern request är mellan 4 och 90. |
| Ogiltig fråge | 400 | Kontrol lera den genererade filen igen `QueryId` . |
| Ogiltiga rapport parametrar för skapande av start tid för rapporten måste vara minst 4 timmar från aktuell UTC-tid- | 400 | Start tids parametern som en del av nytto lasten för begäran får inte ha varit i det förflutna. Start tiden för rapporten bör vara minst 4 timmar från den aktuella UTC-tiden. |
| Det gick inte att hitta det begärda värdet ' sträng ' | 400 | Kontrol lera om du har uppdaterat parametrarna för begäran `callbackurl` eller `format` . |
| Det gick inte att hitta något objekt med angivet filter. | 404 | Kontrol lera den `reportID` parameter som används i API för att hämta rapport körningar. |
| Det finns inga körningar som har inträffat för de aktuella filter villkoren. Kontrol lera Rapportid eller executionId frågeparameter och försök att köra API: et igen efter rapportens schemalagda körnings tid | 404 | Kontrol lera att `reportId` är korrekt. Gör om API: et efter rapportens schemalagda körnings tid enligt vad som anges i nytto lasten för begäran. |
| Ett internt fel inträffade när rapporten skapades. Korrelations-ID <> | 500 | Kontrol lera att datum formatet för fälten "StartTime", "QueryStartTime" och "QueryEndTime" är korrekta. |
| Tjänsten är inte tillgänglig | 500 | Om du kontinuerligt får en tjänst som inte är tillgänglig (5xx-fel) kan du skapa ett [support ärende](support.md). |
||||

## <a name="no-records"></a>Inga poster

Jag får API-svar 200 när jag laddar ned rapporten från den säkra platsen. Varför får jag inga poster?

Kontrol lera om strängen i frågan har ett av de tillåtna värdena för kolumn rubriken. Den här frågan returnerar till exempel noll resultat:

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

I det här exemplet är de tillåtna värdena för SKUBillingType betalda eller kostnads fria. Se följande tabeller för alla möjliga värden för olika kolumner:

- [Tabellen Order Detaljer](orders-dashboard.md#orders-details-table)
- [Tabellen användnings information](usage-dashboard.md#usage-details-table)
- [Tabellen Kund information](customer-dashboard.md#customer-details-table)
- [Informations tabell för Marketplace](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>Nästa steg

- [API: er för åtkomst till kommersiella data för Marketplace-analys](analytics-available-apis.md)
