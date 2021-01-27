---
title: Översikt över API:erna för Azure-förbrukning
description: Lär dig hur API:erna för Azure-förbrukning ger programmatisk åtkomst till kostnads- och användningsdata för dina Azure-resurser.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: reference
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 4b8b24bacaee87dc9868fab1d5d071201a7215b8
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897738"
---
# <a name="azure-consumption-api-overview"></a>Översikt över API:erna för Azure-förbrukning

Med Azure Consumption-API:erna får du programmatisk åtkomst till kostnads- och användningsdata för dina Azure-resurser. Dessa API:er stöder för närvarande endast Enterprise-registreringar och Web Direct-prenumerationer (med några få undantag). API:erna uppdateras kontinuerligt med stöd för andra typer av Azure-prenumerationer.

Azure Consumption-API:er ger åtkomst till:
- Enterprise- och Web Direct-kunder
    - Användningsinformation
    - Marketplace-debiteringar
    - Reservationsrekommendationer
    - Information om reservation
    - Reservationssammanfattningar
- Endast Enterprise-kunder
    - Prisdokument
    - Budgetar
    - Saldon

## <a name="usage-details-api"></a>API för användningsinformation

Använd API:et för användningsinformation för att få information om avgifter och användning för alla Azure-resurser från första part. Informationen visas i form av poster med användningsinformation och skickas för närvarande en gång per mätare per resurs och dag. Informationen kan användas för att summera kostnaderna för alla resurser eller för att analysera kostnader/användning för specifika resurser.

API:et ger tillgång till:

-    **Användningsinformation på mätarnivå** – Visa information om användningskostnaden, vilken mätare kostnaden skickas från och vilken Azure-resurs som avgiften avser. Alla poster med användningsinformation mappar till en daglig bucket.
-    **Rollbaserad åtkomst kontroll i Azure (Azure RBAC)** – konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](../../role-based-access-control/role-assignments-cli.md) -eller Azure PowerShell- [cmdletar](/powershell/azure/) för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda Azure Active Directory-standardtoken för autentisering. Lägg till anroparen till någon av rollerna Faktureringsläsare, Läsare, Ägare eller Deltagare för att få åtkomst till användningsdata för en specifik Azure-prenumeration.
-    **Filtrering** – Trimma API-resultatet till en mindre uppsättning poster med användningsdata genom att använda följande filter:
    - Användningsstart/användningsslut
    - Resursgrupp
    - Resursnamn
-    **Dataaggregering** – Använd OData för att lägga till uttryck som aggregerar användningsdata baserat på taggar eller filteregenskaper
-    **Användning för olika typer av erbjudanden** – Information om användningsdata är för närvarande tillgänglig för Enterprise- och Web Direct-kunder.

Mer information finns i den tekniska specifikationen för [API:et för användningsinformation](/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>API för Marketplace-debiteringar

Använd API:et för Marketplace-debiteringar för att hämta information om kostnader och användning för alla Marketplace-resurser (Azure-erbjudanden från tredje part). Den här informationen kan användas för att summera kostnaderna för alla Marketplace-resurser eller för att analysera kostnaderna/användningen för specifika resurser.

API:et ger tillgång till:

-    **Användningsinformation på mätarnivå** – Visa information om kostnaden för Marketplace-användning, vilken mätare kostnaden skickas från och vilken Azure-resurs som avgiften avser. Alla poster med användningsinformation mappar till en daglig bucket.
-    **Rollbaserad åtkomst kontroll i Azure (Azure RBAC)** – konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](../../role-based-access-control/role-assignments-cli.md) -eller Azure PowerShell- [cmdletar](/powershell/azure/) för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda Azure Active Directory-standardtoken för autentisering. Lägg till anroparen till någon av rollerna Faktureringsläsare, Läsare, Ägare eller Deltagare för att få åtkomst till användningsdata för en specifik Azure-prenumeration.
-    **Filtrering** – Trimma API-resultatet till en mindre uppsättning Marketplace-poster genom att använda följande filter:
    - Användningsstart/användningsslut
    - Resursgrupp
    - Resursnamn
-    **Användning för olika typer av erbjudanden** – Marketplace-information är för närvarande tillgänglig för Enterprise- och Web Direct-kunder.

Mer information finns i den tekniska specifikationen för [API:et för Marketplace-debiteringar](/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>API för saldon

Företagskunder kan använda API:et för saldon för att få en månatlig sammanfattning med information om saldon, nya inköp, kostnader för Azure Marketplace-tjänster, justeringar och kostnader för överförbrukning. Du kan hämta den här informationen för den aktuella faktureringsperioden eller för en tidigare period. Företag kan använda dessa data för att jämföra dem med manuellt beräknade kostnader. Det här API:et returnerar inte resursspecifik information och en sammanställd vy över kostnaderna.

API:et ger tillgång till:

-    **Rollbaserad åtkomst kontroll i Azure (Azure RBAC)** – konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](../../role-based-access-control/role-assignments-cli.md) -eller Azure PowerShell- [cmdletar](/powershell/azure/) för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda Azure Active Directory-standardtoken för autentisering. Lägg till anroparen till någon av rollerna Faktureringsläsare, Läsare, Ägare eller Deltagare för att få åtkomst till användningsdata för en specifik Azure-prenumeration.
-    **Endast företagskunder** Det här API:et är endast tillgängligt för EA-kunder.
    - Kunder måste ha behörigheter som företagsadministratör för att anropa detta API

Mer information finns i den tekniska specifikationen för [API:et för saldon](/rest/api/consumption/balances).

## <a name="budgets-api"></a>API för budgetar

Företagskunder kan använda det här API:et för att skapa kostnads- eller användningsbudgetar för resurser, resursgrupper eller faktureringsmätare. När dessa data har fastställts kan automatiska aviseringar skickas när användardefinierade budgettrösklar överskrids.

API:et ger tillgång till:

-    **Rollbaserad åtkomst kontroll i Azure (Azure RBAC)** – konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](../../role-based-access-control/role-assignments-cli.md) -eller Azure PowerShell- [cmdletar](/powershell/azure/) för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda Azure Active Directory-standardtoken för autentisering. Lägg till anroparen till någon av rollerna Faktureringsläsare, Läsare, Ägare eller Deltagare för att få åtkomst till användningsdata för en specifik Azure-prenumeration.
-    **Endast företagskunder** – Det här API:et är endast tillgängligt för EA-kunder.
-    **Konfigurerbara aviseringar** – Ange vilka användare som ska aviseras om budgeten överskrids.
-    **Användnings- eller kostnadsbaserade budgetar** – Skapa din budget baserat på förbrukning eller kostnad beroende på ditt scenario.
-    **Filtrering** – Filtrera din budget till en mindre delmängd resurser genom att använda följande konfigurerbara filter
    - Resursgrupp
    - Resursnamn
    - Mätare
-    **Konfigurerbara tidsperioder för budget** – Ange hur ofta budgeten ska återställas och hur länge budgeten är giltig.

Mer information finns i den tekniska specifikationen för [API:et för budgetar](/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>API för reservationsrekommendationer

Använd det här API:et för att få rekommendationer om köp av reserverade VM-instanser. Rekommendationerna är utformade för att kunderna ska kunna analysera förväntade kostnadsbesparingar och inköpsbelopp.

API:et ger tillgång till:

-    **Rollbaserad åtkomst kontroll i Azure (Azure RBAC)** – konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](../../role-based-access-control/role-assignments-cli.md) -eller Azure PowerShell- [cmdletar](/powershell/azure/) för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda Azure Active Directory-standardtoken för autentisering. Lägg till anroparen till någon av rollerna Faktureringsläsare, Läsare, Ägare eller Deltagare för att få åtkomst till användningsdata för en specifik Azure-prenumeration.
-    **Filtrering** – Skräddarsy rekommendationerna genom att använda följande filter:
    - Omfång
    - Period bakåt i tiden
-    **Reservationsinformation för olika typer av erbjudanden** – Reservationsinformation är för närvarande tillgänglig för Enterprise- och Web Direct-kunder.

Mer information finns i den tekniska specifikationen för [API:et för reservationsrekommendationer](/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>API för reservationsdata

Använd API:et för reservationsdata för att visa information om tidigare köpta VM-reservationer, till exempel hur stor förbrukning som har reserverats jämfört med hur mycket som faktiskt används. Du kan visa information på nivån för en enskild virtuell dator.

API:et ger tillgång till:

-    **Rollbaserad åtkomst kontroll i Azure (Azure RBAC)** – konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](../../role-based-access-control/role-assignments-cli.md) -eller Azure PowerShell- [cmdletar](/powershell/azure/) för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda Azure Active Directory-standardtoken för autentisering. Lägg till anroparen till någon av rollerna Faktureringsläsare, Läsare, Ägare eller Deltagare för att få åtkomst till användningsdata för en specifik Azure-prenumeration.
-    **Filtrering** – Trimma API-resultatet till en mindre uppsättning reservationer genom att använda följande filter:
    - Datumintervall
-    **Reservationsinformation för olika typer av erbjudanden** – Reservationsinformation är för närvarande tillgänglig för Enterprise- och Web Direct-kunder.

Mer information finns i den tekniska specifikationen för [API:et för reservationsdata](/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>API för reservationssammanställningar

Använd det här API:et för att visa en sammanställning av tidigare köpta VM-reservationer, till exempel hur stor förbrukning som har reserverats jämfört med hur mycket som faktiskt används.

API:et ger tillgång till:

-    **Rollbaserad åtkomst kontroll i Azure (Azure RBAC)** – konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](../../role-based-access-control/role-assignments-cli.md) -eller Azure PowerShell- [cmdletar](/powershell/azure/) för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda Azure Active Directory-standardtoken för autentisering. Lägg till anroparen till någon av rollerna Faktureringsläsare, Läsare, Ägare eller Deltagare för att få åtkomst till användningsdata för en specifik Azure-prenumeration.
-    **Filtrering** – Skräddarsy resultatet när du använder detaljnivån ”varje dag” genom att använda följande filter:
    - Datum för användning
-    **Reservationsinformation för olika typer av erbjudanden** – Reservationsinformation är för närvarande tillgänglig för Enterprise- och Web Direct-kunder.
-    **Dagliga eller månatliga aggregeringar** – Anropare kan välja att få sina reservationssammanställningar varje dag eller varje månad.

Mer information finns i den tekniska specifikationen för [API:et för reservationssammanställningar](/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>API för prisdokument
Enterprise-kunder kan använda det här API:et för att hämta sina priser för alla mätare. Företag kan använda det i kombination med användningsdata och information om Marketplace-användning för att göra kostnadsberäkningar med hjälp av användnings- och Marketplace-data.

API:et ger tillgång till:

-    **Rollbaserad åtkomst kontroll i Azure (Azure RBAC)** – konfigurera åtkomst principer på [Azure Portal](https://portal.azure.com), [Azure CLI](../../role-based-access-control/role-assignments-cli.md) -eller Azure PowerShell- [cmdletar](/powershell/azure/) för att ange vilka användare eller program som kan få åtkomst till prenumerationens användnings data. Anropare måste använda Azure Active Directory-standardtoken för autentisering. Lägg till anroparen till någon av rollerna Faktureringsläsare, Läsare, Ägare eller Deltagare för att få åtkomst till användningsdata för en specifik Azure-prenumeration.
-    **Endast företagskunder** – Det här API:et är endast tillgängligt för EA-kunder. Web Direct-kunder bör använda RateCard-API:et för att hämta priser.

Mer information finns i den tekniska specifikationen för [API:et för prisdokumentet](/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scenarier

Här följer några av de scenarier som är möjliga med förbrukning-API:erna:

-    **Fakturaavstämning** – Debiterade Microsoft mig rätt belopp?  Hur mycket är min faktura på och kan jag beräkna beloppet själv?
-    **Tvärdebiteringar** – Nu när jag vet hur mycket jag debiteras, vem i min organisation ska betala?
-    **Kostnadsoptimering** – Jag vet hur mycket jag har debiterats … Hur kan jag få ut mer av pengarna jag spenderar på Azure?
-    **Kostnadsspårning** – Jag vill se hur mycket jag betalar och använder Azure över tid. Hur ser trenderna ut? Hur kan jag förbättra situationen?
-    Kostar **Azure att spendera under månaden** – hur mycket är min nuvarande månads spenderade datum? Behöver jag göra några justeringar i mina utgifter och/eller användning av Azure? När under månaden använder jag Azure mest?
-    **Konfigurera aviseringar** – Jag vill ställa in resursbaserad förbrukning eller penningbaserade aviseringar baserat på en budget.

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder REST-API: er hämta priser för alla Azure-tjänster finns i [Översikt över Azures åter försäljnings priser](/rest/api/cost-management/retail-prices/azure-retail-prices).
