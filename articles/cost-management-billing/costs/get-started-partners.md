---
title: Kom igång med Azure Cost Management för partner
description: I den här artikeln förklaras hur partner använder Azure Cost Management-funktioner och hur de ger Cost Management-åtkomst för sina kunder.
author: bandersmsft
ms.author: banders
ms.date: 01/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: 8a04cb2c590bc4f0104a93ac7c7c2cf7b71a4662
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602174"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Kom igång med Azure Cost Management för partner

Azure Cost Management är internt tillgängligt för direkta partner som har upprättat ett Microsoft-kundavtal med sina kunder och har [köpt en Azure-plan](/partner-center/purchase-azure-plan). I den här artikeln förklaras hur partner använder [Azure Cost Management](../index.yml)-funktioner för att visa kostnader för prenumerationer i Azure-planen. Den beskriver också hur partner ger kunder åtkomst till Cost Management, där de kan visa kostnader enligt återförsäljningspris.

För direkta partner och indirekta leverantörer kan globala administratörer och administratörsagenter komma åt Cost Management i partnerns klientorganisation och hantera kostnader enligt fakturerade priser.

Återförsäljare och kunder kan komma åt Cost Management i kundens klientorganisation och visa kostnader för prenumerationerna. Kostnaderna beräknas och visas enligt återförsäljarpris. De måste dock ha Azure RBAC-åtkomst till prenumerationen i kundens klientorganisation för att kunna visa kostnader. Providern måste ha aktiverat principen för kostnadssynlighet för kundens klientorganisation.

Kunder kan använda Cost Management-funktioner när de har aktiverats av sin CSP-partner.

CSP-partner använder Cost Management för att:

- Förstå fakturerade kostnader och koppla kostnaderna till kunden, prenumerationer, resursgrupperna och tjänster.
- Få en intuitiv vy över Azure-kostnader i [kostnadsanalysen](quick-acm-cost-analysis.md) med funktioner för att analysera kostnader efter kund, prenumeration, resursgrupp, resurs, mätare, tjänst och många andra dimensioner.
- Visa resurskostnader där partners intjänade krediter (PEC) har tillämpats i kostnadsanalysen.
- Konfigurera meddelanden och automatisering med hjälp av programmässiga [budgetar](tutorial-acm-create-budgets.md) och aviseringar när kostnaderna överstiger budgetarna.
- Aktivera den Azure Resource Manager-princip som ger kunder åtkomst till Cost Management-data. Kunderna kan sedan visa kostnadsdata för förbrukning för sina prenumerationer med hjälp av [betala per användning-taxa](https://azure.microsoft.com/pricing/calculator/).
- Exportera sina kostnader och användningsdata till en lagringsblob med en prenumeration med betalning per användning.

Här är ett exempel som visar kostnader för alla kunder.

[![Exempel som visar kostnader för alla kunder](./media/get-started-partners/customer-costs1.png)](./media/get-started-partners/customer-costs1.png#lightbox)

Här är ett exempel som visar kostnader för en enda kund.

[![Exempel som visar kostnader för en enda kund](./media/get-started-partners/customer-costs2.png)](./media/get-started-partners/customer-costs2.png#lightbox)

Alla funktioner som är tillgängliga i Azure Cost Management är också tillgängliga i REST-API:er. Använd API:erna för att automatisera kostnadshanteringsuppgifter.

## <a name="prerequisites"></a>Krav

Som partner är Azure Cost Management internt tillgängligt endast för prenumerationer som finns i Azure-planen.

Om du vill aktivera Azure Cost Management i Azure Portal måste du ha bekräftat kundgodkännande av Microsofts kundavtal (för kundens räkning) och förflyttat kunden till Azure-planen. Endast kostnaderna för prenumerationer som övergår till Azure-planen är tillgängliga i Azure Cost Management.

Azure Cost Management kräver läsbehörighet till ditt faktureringskonto eller prenumerationen.

Mer information om hur du aktiverar och tilldelar åtkomst till Azure Cost Management för ett faktureringskonto finns i [Tilldela användarroller och behörigheter](/partner-center/permissions-overview). Rollerna **Global administratör** och **Administratörsrepresentant** kan hantera kostnader för ett faktureringskonto.

För att få åtkomst till Azure Cost Management i prenumerationsomfånget kan alla användare med Azure RBAC-åtkomst till en prenumeration visa kostnader som återförsäljarpris (betala per användning). Principen för [kostnadssynlighet för kundens klientorganisation](#enable-the-policy-to-view-azure-usage-charges) måste dock vara aktiverad. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md).

## <a name="how-cost-management-uses-scopes"></a>Hur Cost Management använder omfång

Omfång är den plats där du hanterar faktureringsdata, har specifika roller för betalningar, ser fakturor samt utför allmän kontohantering. Rollerna för fakturering och konton hanteras separat från omfång som används för resurshantering, vilka använder Azure RBAC. För att tydligt särskilja avsikten med de separata omfången, inklusive skillnaderna i åtkomstkontroll, kallas dessa för faktureringsomfång respektive Azure RBAC-omfång.

Information om faktureringsomfång och Azure RBAC-omfång samt hur kostnadshantering fungerar med omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Hantera kostnader med faktureringsomfång för partners klientorganisation

När du har registrerat dina kunder med ett Microsoft-kundavtal är följande _faktureringsomfång_ tillgängliga i din klientorganisation. Använd omfång för att hantera kostnader i Cost Management.

### <a name="billing-account-scope"></a>Faktureringskontoomfång

Använd faktureringskontoomfånget för att visa kostnader före moms i alla dina kund- och faktureringsprofiler. Fakturakostnader visas bara för kundens förbrukningsbaserade produkter i Microsoft-kundavtalet. Fakturakostnader visas dock för inköpsbaserade produkter för kunder i både Microsoft-kundavtalet och CSP-erbjudandet. För närvarande är standardvalutan för att visa kostnader i omfånget USD. Budgetar som angetts för omfånget är också i USD.

Oavsett olika valutor för fakturering använder partner faktureringskontoomfånget för att ange budgetar och hantera kostnader i USD för alla kunder, prenumerationer, resurser och resursgrupper.

Partner filtrerar också kostnader i en bestämd faktureringsvaluta för kunderna i vyn för kostnadsanalys. Välj listan **Faktisk kostnad** för att se kostnaderna i de faktureringsvalutor som stöds.

![Exempel som visar val av Faktisk kostnad för valutor](./media/get-started-partners/actual-cost-selector.png)

Använd vyn [Amorterad kostnad](quick-acm-cost-analysis.md#customize-cost-views) i faktureringsomfången för att visa amorterade kostnader för reserverade instanser för en reservationsperiod.

### <a name="billing-profile-scope"></a>Faktureringsprofilsomfång

Använd faktureringsprofilsomfången för att visa kostnader före skatt i faktureringsvalutan för alla kunder för alla produkter och prenumerationer som ingår i en faktura. Du kan filtrera kostnader i en faktureringsprofil för en specifik faktura med hjälp av filtret **InvoiceID**. Filtret visar förbruknings- och produktinköpskostnader för en specifik faktura. Du kan också filtrera kostnaderna för en specifik kund på fakturan för att visa kostnader före skatt.

När du har registrerat kunderna med ett Microsoft-kundavtal får du en faktura som innehåller alla avgifter för alla produkter (förbrukning, inköp och berättiganden) för dessa kunder i Microsoft-kundavtalet. Vid fakturering i samma valuta inkluderar dessa fakturor även avgifter för berättigande och inköpta produkter som SaaS, Azure Marketplace och reservationer för kunder som fortfarande ingår i CSP-erbjudandet, inte i Azure-planen.

För att hjälpa till att stämma av avgifterna mot kundfakturan kan du med faktureringsprofilsomfånget se alla kostnader som påförs en faktura för kunderna. Precis som fakturan visar omfånget kostnader för varje kund i det nya Microsoft-kundavtalet. Omfånget visar också alla kostnader för kundberättigande till produkter som fortfarande finns i det aktuella CSP-erbjudandet.

Faktureringsprofilen och faktureringskontoomfånget är de enda tillämpliga omfången som visar avgifter för berättiganden och inköpsbaserade produkter som Azure Marketplace och reservationsköp.

Faktureringsprofiler definierar de prenumerationer som ingår i en faktura. Faktureringsprofiler är den funktionella motsvarigheten till en företagsavtalsregistrering. En faktureringsprofil är det omfång där fakturor genereras.

För närvarande är faktureringsvalutan standardvaluta när du visar kostnader i faktureringsprofilsomfånget. Budgetarna som anges i faktureringsprofilsomfånget visas i faktureringsvalutan.

Partner kan använda omfånget för att stämma av fakturor. Och de använder omfånget för att ange budgetar i faktureringsvalutan för följande objekt:

- Specifik filtrerad faktura
- Kund
- Prenumeration
- Resursgrupp
- Resurs
- Azure-tjänst
- Mätare
- ResellerMPNID

### <a name="customer-scope"></a>Kundomfång

Partner använder omfånget för att hantera kostnader som är kopplade till kunder som har registrerats med ett Microsoft-kundavtal. Med hjälp av omfånget kan partner visa kostnader före skatt för en specifik kund i en faktureringsvaluta. Du kan också filtrera kostnader före skatt för en specifik prenumeration, resursgrupp eller resurs.

I kundomfånget ingår inte kunder som omfattas av det aktuella CSP-erbjudandet. Omfånget omfattar bara kunder som har ett Microsoft-kundavtal. Berättigandekostnader, inte Azure-användning, för kunder med det aktuella CSP-erbjudandet är tillgängliga för faktureringskonto- och faktureringsprofilsomfången när du tillämpar kundfiltret. Budgetarna som anges i omfånget visas i faktureringsvalutan.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Partneråtkomst till faktureringsomfång i Cost Management

Det är bara användare med rollen **Global administratör** och **Administratörsrepresentant** som kan hantera och se kostnader för faktureringskonton, faktureringsprofiler och kunder direkt i partnerns Azure-klient. Mer information om Partner Center-roller finns i [Tilldela användarroller och behörigheter](/partner-center/permissions-overview).

## <a name="enable-cost-management-for-customer-tenant-subscriptions"></a>Aktivera kostnadshantering för prenumerationer i kundens klientorganisation

Partner kan ge åtkomst till Cost Management när kunderna har registrerats med ett Microsoft-kundavtal. Partner kan sedan aktivera en princip som gör att kunderna kan se sina kostnader för Azure-förbrukade tjänster baserat på återförsäljningspriset för ”betala per användning”. Kostnaderna visas i kundens faktureringsvaluta för den förbrukade användningen i prenumerations- och resursgruppsomfång för Azure RBAC.

När principen för kostnadssynlighet har aktiverats av partner, kan alla användare med Azure Resource Manager-åtkomst till prenumerationen hantera och analysera kostnader enligt priserna för betala per användning. Alltså kan återförsäljare och kunder som har rätt Azure RBAC-åtkomst till Azure-prenumerationer se kostnaden.

Oavsett principen kan leverantörens globala administratörer och administratörsagenter visa prenumerationskostnader om de har åtkomst till prenumerationen och resursgruppen.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Aktivera principen för att visa användningsavgifter för Azure

Du måste vara medlem i gruppen **adminagent** för att kunna visa och uppdatera policyn. Använd följande information när du ska aktivera policyn så att kunder kan visa användningsavgifter för Azure.

Logga in på *partnerklienten* i Azure Portal och välj **Cost Management + Fakturering**. Välj önskat faktureringsomfång i området Faktureringsomfång och välj sedan **Kunder**. Listan över kunder är kopplad till faktureringskontot. *Om du loggar in av misstag på kundklienten visas inte **listan** Kunder.*

I listan över kunder väljer du den kund som du vill tillåta visning av kostnader.

[![Välj kunder i Cost Management](./media/get-started-partners/customer-list.png)](./media/get-started-partners/customer-list.png#lightbox)

Under **Inställningar** väljer du **Principer**.

Den aktuella principen för kostnadssynlighet visas för avgifter för **Azure-användning** som är kopplade till prenumerationer för den valda kunden.
![Princip som tillåter kunder att visa avgifter enligt betala per användning](./media/get-started-partners/cost-management-billing-policies.png)

När principen har angetts som **Nej** är Azure Cost Management inte tillgängligt för prenumerationsanvändare som är kopplade till kunden. Om den inte aktiveras av en partner är principen för kostnadssynlighet inaktiverad som standard för alla prenumerationsanvändare.

När kostnadsprincipen är inställd på **Ja** kan prenumerationsanvändare som är kopplade till kundklientorganisationen se användningsavgifter enligt priset för betala per användning.

När principen för kostnadssynlighet är aktiverad visar alla tjänster som har prenumerationsanvändning kostnader enligt priserna för betala per användning. Reservationsanvändningen visas med noll avgifter för faktiska och periodiserade kostnader. Köp och berättiganden är inte kopplade till en specifik prenumeration. Därför visas inte inköpen i prenumerationsomfånget. En global administratör/administratörsagent eller en indirekt leverantör kan också använda [API:et för kunduppdatering](/rest/api/billing/2019-10-01-preview/policies/updatecustomer) för att ange varje kunds kostnadssynlighetsprincip i stor skala.

### <a name="view-subscription-costs-in-the-customer-tenant"></a>Visa prenumerationskostnader i kundens klientorganisation

Visa kostnaderna för en prenumeration genom att öppna **Kostnadshantering + fakturering** i kundens Azure-klientorganisation. Välj **Kostnadsanalys** för prenumerationen för att börja granska kostnaderna. Du kan visa förbrukningskostnader för varje prenumeration separat i kundens klientorganisation.

[![Visa kostnadsanalys som en kund ](./media/get-started-partners/subscription-costs.png)](./media/get-started-partners/subscription-costs.png#lightbox)

Kostnadsanalys, budgetar och aviseringar är tillgängliga för prenumerationens och resursgruppens Azure RBAC-omfång för priser baserade på betala per användning.

Vyer för amorterad och faktisk kostnad för reserverade instanser i Azure RBAC-omfången visar noll avgifter. Inköpskostnader för rättigheter såsom reserverade instanser och Marketplace-avgifter visas bara i faktureringsomfång i partnerns klientorganisation där köpen gjordes.

De återförsäljningspriser som används för att beräkna kostnaderna som visas i vyn är samma priser som de som visas i priskalkylatorn för Azure för alla kunder. Kostnaderna som visas inkluderar inte rabatter eller krediter som partnern kan ha, t.ex. partnerns intjänade krediter, nivårabatter och globala tjänstrabatter.

 

## <a name="analyze-costs-in-cost-analysis"></a>Analysera kostnader i kostnadsanalys

Partner med åtkomst till faktureringsomfång i partnerns klientorganisation kan visa och analysera fakturerade kostnader i kostnadsanalys för alla kunder för en specifik kund eller för en faktura. I vyn [Kostnadsanalys](quick-acm-cost-analysis.md) kan du också [spara vyer](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) och exportera data till [CSV- och PNG-filer](quick-acm-cost-analysis.md#download-usage-data).

Azure RBAC-användare med åtkomst till prenumerationen i kundens klientorganisation kan också analysera återförsäljarkostnader för prenumerationer i kundens klientorganisation, spara vyer och exportera data till CSV- och PNG-filer.

Du kan använda filter och gruppera efter funktioner i kostnadsanalysen för att analysera kostnader efter flera fält. Partnerspecifika fält visas i nästa avsnitt.

## <a name="data-fields"></a>Datafält

Följande datafält finns i filer för detaljerad användningsinformation och API:er för Cost Management. Om tillgängligt visas motsvarande Partner Center-information. För följande fetstilta fält kan partner använda filter och gruppera efter funktioner i kostnadsanalysen för att analysera kostnader efter flera fält. Fetstilta fält gäller endast för Microsoft-kundavtal som stöds av partner.

| **Fältnamn** | **Beskrivning** | **Partnercenter-händelser** |
| --- | --- | --- |
| invoiceId | Faktura-ID som visas på fakturan för den specifika transaktionen. | Fakturanummer där transaktionen visas. |
| previousInvoiceID | Referens till en ursprunglig faktura om det är en återbetalning (negativ kostnad). Fylls bara i när det finns en återbetalning. | Ej tillämpligt |
| billingAccountName | Namnet på det faktureringskonto som representerar partnern. Det påförs alla kostnader för de kunder som har registrerats med ett Microsoft-kundavtal och de CSP-kunder som har gjort berättigandeköp som SaaS, Azure Marketplace och reservationer. | Ej tillämpligt |
| billingAccountID | Identifierare för det faktureringskonto som representerar partnern. | Rot-ID för MCAPI Partner Commerce. Används i en begäran, men ingår inte i något svar.|
| billingProfileID | Identifierare för den faktureringsprofil som grupperar kostnader för fakturor i en enda faktureringsvaluta för kunder som har registrerats med ett Microsoft-kundavtal och de CSP-kunder som har gjort berättigandeköp som SaaS, Azure Marketplace och reservationer. | Grupp-ID för MCAPI Partner Billing. Används i en begäran, men ingår inte i något svar. |
| billingProfileName | Namn på den faktureringsprofil som grupperar kostnader för fakturor i en enda faktureringsvaluta för kunder som har registrerats med ett Microsoft-kundavtal och de CSP-kunder som har gjort berättigandeköp som SaaS, Azure Marketplace och reservationer. | Ej tillämpligt |
| invoiceSectionName | Namnet på projektet som debiteras på fakturan. Ej tillämpligt för Microsoft-kundavtal som registrerats av partner. | Ej tillämpligt |
| invoiceSectionID | Identifierare för projektet som debiteras på fakturan. Ej tillämpligt för Microsoft-kundavtal som registrerats av partner. | Ej tillämpligt |
| **CustomerTenantID** | Identifierare för Azure Active Directory-klientorganisationen för kundens prenumeration. | Kundens organisations-ID – kundens TenantID Azure Active Directory. |
| **CustomerName** | Namnet på Azure Active Directory-klientorganisationen för kundens prenumeration. | Kundens organisationsnamn, som visas i Partner Center. Viktigt för att stämma av fakturan med systeminformationen. |
| **CustomerTenantDomainName** | Domännamn för Azure Active Directory-klientorganisationen för kundens prenumeration. | Domän för kundens klientorganisation i Azure Active Directory. |
| **PartnerTenantID** | Identifierare för partnerns Azure Active Directory-klientorganisation. | ID för partnerns Azure Active Directory-klientorganisation som kallas partner-ID, i GUID-format. |
| **PartnerName** | Namnet på partnerns Azure Active Directory-klientorganisation. | Partnerns namn. |
| **ResellerMPNID** | MPNID för återförsäljaren som är associerad med prenumerationen. | MPN-ID för återförsäljaren som är registrerad för prenumerationen. Inte tillgängligt för den aktuella aktiviteten. |
| costCenter | Kostnadsställe som är associerat med prenumerationen. | Ej tillämpligt |
| billingPeriodStartDate | Faktureringsperiodens startdatum, som visas på fakturan. | Ej tillämpligt |
| billingPeriodEndDate | Faktureringsperiodens slutdatum, som visas på fakturan. | Ej tillämpligt |
| servicePeriodStartDate | Startdatum för klassificeringsperioden när tjänsteanvändningen bedömdes för avgifter. Priserna för Azure-tjänster bestäms för klassificeringsperioden. | ChargeStartDate i Partner Center.  Startdatum för faktureringscykeln, förutom när du presenterar datum för tidigare odebiterade fördröjda användningsdata från en tidigare faktureringsperiod. Tiden är alltid början på dagen, 0:00. |
| servicePeriodEndDate | Slutdatum för perioden när tjänsteanvändningen bedömdes för avgifter. Priserna för Azure-tjänster bestäms baserat på klassificeringsperioden. | Ej tillämpligt |
| date | För Azure-förbrukningsdata, som visar användningsdatum enligt klassificering. För reserverad instans visas inköpsdatum. För återkommande avgifter och engångsavgifter, till exempel Marketplace och support, visas inköpsdatumet. | Ej tillämpligt |
| productID | Identifierare för den produkt som har upplupna kostnader per förbrukning eller inköp. Det är den sammanfogade nyckeln productID och SKuID, som visas i Partner Center. | ID för produkten. |
| produkt | Namnet på den produkt som har upplupna kostnader per förbrukning eller inköp, som visas på fakturan. | Produktnamnet i katalogen. |
| serviceFamily | Visar tjänstfamiljen för den köpta eller debiterade produkten. Till exempel, Storage eller Compute. | Ej tillämpligt |
| productOrderID | Identifieraren för tillgångs- eller Azure-plansnamnet som prenumerationen tillhör. Till exempel Azure-planen. | CommerceSubscriptionID |
| productOrderName | Namnet på Azure-planen som prenumerationen tillhör. Till exempel Azure-planen. | Ej tillämpligt|
| consumedService | Förbrukad tjänst (äldre taxonomi) som använts i tidigare EA-användningsinformation. | Tjänsten som visas i Partner Center. Till exempel Microsoft.Storage, Microsoft.Compute och microsoft.operationalinsights. |
| meterID | Mätningsidentifierare för uppmätt förbrukning. | ID för förbrukningsmätaren. |
| meterName | Anger namnet på mätaren för uppmätt förbrukning. | Namnet på förbrukningsmätaren. |
| meterCategory | Anger den huvudtjänst som användningen gäller. | Huvudtjänst som användningen gäller. |
| meterSubCategory | Anger typen eller underkategorin för Azure-tjänsten, vilket kan påverka priset. | Typen av Azure-tjänst, vilket kan påverka priset.|
| meterRegion | Anger datacenterplats för vissa tjänster som prissätts beroende på var datacentret ligger. | Den regionala platsen för ett datacenter för tjänster, där det är tillämpligt och ifyllt. |
| subscription ID | Unik Microsoft-genererad identifierare för Azure-prenumerationen. | EntitlementID |
| subscriptionName | Namnet på Azure-prenumerationen. | Ej tillämpligt |
| Period | Visar giltighetstiden för erbjudandet. Reserverade instanser visar till exempel 12 månader av en årlig period för den reserverade instansen. Vid engångsköp eller återkommande köp visar termen en månad för SaaS, Azure Marketplace och support. Gäller inte för Azure-förbrukning. | Ej tillämpligt |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Typ av utgivare som identifierar utgivaren som första part, tredjepartsåterförsäljare eller tredjepartsagent. | Ej tillämpligt |
| partNumber | Artikelnummer för den oanvända reserverade instansen och Azure Marketplace-tjänster. | Ej tillämpligt |
| publisherName | Namnet på utgivaren av tjänsten, inklusive Microsoft eller tredjepartsutgivare. | Namnet på produktens utgivare.|
| reservationId | Identifierare för köpet av den reserverade instansen. | Ej tillämpligt |
| reservationName | Namnet på den reserverade instansen. | Ej tillämpligt |
| reservationOrderId | OrderID för den reserverade instansen. | Ej tillämpligt |
| frequency | Betalningsfrekvens för en reserverad instans. | Ej tillämpligt |
| resourceGroup | Namnet på den Azure-resursgrupp som används för hantering livscykelresurs. | Resursgruppens namn. |
| instanceID (eller) ResourceID | Identifierare för resursinstansen. | Visas som ResourceURI som innehåller fullständiga resursegenskaper. |
| resourceLocation | Resursplatsens namn. | Platsen för resursen. |
| Location | Normaliserad plats för resursen. | Ej tillämpligt |
| effectivePrice | Det effektiva enhetspriset för tjänsten, i prisvalutan. Unikt för en produkt, en tjänstfamilj, en mätare och ett erbjudande. Används med prissättning i prisdokumentet för faktureringskontot. När det finns en nivåindelad prissättning eller en inkluderad kvantitet visas det blandade priset för förbrukning. | Enhetspriset efter det att justeringarna har gjorts. |
| Kvantitet | Uppmätt kvantitet som köpts eller förbrukats. Mängden av mätaren som använts under faktureringsperioden. | Antal enheter. Se till att det matchar informationen i faktureringssystemet under avstämningen. |
| unitOfMeasure | Identifierar i vilken enhet tjänsten debiteras. Till exempel GB och timmar. | Identifierar i vilken enhet tjänsten debiteras. Till exempel GB, timmar, 10 000-tal. |
| pricingCurrency | Den valuta som definierar enhetspriset. | Valutan i prislistan.|
| billingCurrency | Den valuta som definierar den fakturerade kostnaden. | Den valuta som definieras som faktureringsvaluta på fakturan. |
| chargeType | Definierar den typ av avgift som kostnaden representerar i Azure Cost Management, som inköp och återbetalning. | Typ av avgift eller justering. Inte tillgängligt för den aktuella aktiviteten. |
| costinBillingCurrency | ExtendedCost eller blandad kostnad före skatt i den fakturerade valutan. | Ej tillämpligt |
| costinPricingCurrency | ExtendedCost eller blandad kostnad före skatt i prissättningsvalutan som motsvarar priserna. | Ej tillämpligt |
| **costinUSD** | Beräknad ExtendedCost eller blandad kostnad före skatt i USD. | Ej tillämpligt |
| **paygCostInBillingCurrency** | Visar kostnader om priserna visas i återförsäljarpriser. Visar priser enligt principen betala per användning i faktureringsvalutan. Endast tillgängligt för Azure RBAC-omfång. | Ej tillämpligt |
| **paygCostInUSD** | Visar kostnader om priserna visas i återförsäljarpriser. Visar priser för betala per användning i USD. Endast tillgängligt för Azure RBAC-omfång. | Ej tillämpligt |
| exchangeRate | Växelkurs som används för att konvertera från prissättningsvalutan till faktureringsvalutan. | Kallas PCToBCExchangeRate i Partner Center. Växelkursen för prissättningsvaluta till faktureringsvaluta.|
| exchangeRateDate | Datum för den växelkurs som används för att konvertera från prissättningsvalutan till faktureringsvalutan. | Kallas PCToBCExchangeRateDat i Partner Center. Datum för växelkursen för prissättningsvaluta till faktureringsvaluta.|
| isAzureCreditEligible | Anger om kostnaden är berättigad till betalning av Azure-krediter. | Ej tillämpligt |
| serviceInfo1 | Ett äldre fält som registrerar valfria tjänstspecifika metadata. | Interna Azure-tjänstemetadata. |
| serviceInfo2 | Ett äldre fält som registrerar valfria tjänstspecifika metadata. | Tjänstinformation. Det kan till exempel vara en avbildningstyp för en virtuell dator och ISP-namn för ExpressRoute.|
| additionalInfo | Tjänstspecifika metadata. Det kan till exempel vara en avbildningstyp för en virtuell dator. | Eventuell ytterligare information som inte omfattas i andra kolumner. Tjänstspecifika metadata. Det kan till exempel vara en avbildningstyp för en virtuell dator.|
| tags | Tagg som du tilldelar mätaren. Använd taggar för att gruppera faktureringsposter. Du kan till exempel använda taggar för att fördela kostnader per avdelning som använder mätaren. | Taggar som lagts till av kunden.|
| **partnerEarnedCreditRate** | Rabatt som tillämpas om det finns en partnerintjänad kredit (PEC) baserat på länkåtkomst för partneradministratör. | Belopp för partners intjänade kredit (PEC). Till exempel 0 % eller 15 %. |
| **partnerEarnedCreditApplied** | Anger om partners intjänade kredit har tillämpats. | Ej tillämpligt |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Visa resurskostnader för partners intjänade kredit (PEC)

I Azure Cost Management kan partner använda kostnadsanalys för att visa kostnader som fått del av PEC-förmånerna.

Logga in på partnerklienten i Azure Portal och välj **Cost Management + Fakturering**. Under **Cost Management** väljer du **Kostnadsanalys**.

I vyn Kostnadsanalys visas kostnader för partnerns faktureringskonto. Välj **omfång** efter behov för partnern, en specifik kund eller en faktureringsprofil för att stämma av fakturor.

I ett ringdiagram väljer du listrutan och väljer **PartnerEarnedCreditApplied** för att gå in på PEC-kostnader.

![Exempel som visar hur du visar partners intjänade kredit](./media/get-started-partners/cost-analysis-pec1.png)

När egenskapen **PartnerEarnedCreditApplied** har värdet _Sant_, har den tillhörande kostnaden fördelarna med partners intjänade kredit med administratörsåtkomst.

När egenskapen **PartnerEarnedCreditApplied** är _Falskt_, har den tillhörande kostnaden inte uppnått nödvändigt berättigande för krediten. Eller så är den köpta tjänsten inte berättigad till partnerintjänad kredit.

Det tar normalt 8–24 timmar innan tjänstanvändningsdata visas i Cost Management. Mer information finns i [Uppdateringar av kostnader och användningsdata och kvarhållning](understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). PEC-krediter visas inom 48 timmar från tiden för åtkomst i Azure Cost Management.


Du kan också gruppera och filtrera efter egenskapen **PartnerEarnedCreditApplied** med hjälp av alternativet **Gruppera efter**. Använd alternativen för att undersöka kostnader som har respektive inte har PEC.

![Gruppera eller filtrera efter partnerintjänad kredit](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Exportera kostnadsdata till Azure Storage

Partner med åtkomst till faktureringsomfång i en partnerklientorganisation kan exportera sina kostnader och användningsdata till en Azure Storage-blob. Bloben måste finnas i en prenumeration i partnerns klientorganisation som inte är en [delad tjänstprenumeration](/partner-center/shared-services) eller en kunds prenumeration. Om du vill aktivera kostnadsdataexport rekommenderar vi att du ställer in en oberoende prenumeration baserad på betala per användning i partnerklientorganisationen som värd för exporterade kostnadsdata. Exportlagringskontot skapas i Azure Storage-blob som finns i prenumerationen baserad på betala per användning. Utifrån det omfång där partnern skapar exporten, exporteras tillhörande data automatiskt till lagringskontot med återkommande frekvens.

Användare med Azure RBAC-åtkomst till prenumerationen kan också exportera kostnadsdata till en Azure Storage-blob som finns i valfri prenumeration i kundens klientorganisation.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Skapa en export i en partnerklientorganisation eller kundklientorganisation

Logga in på partnerklienten eller kundklienten i Azure Portal och välj **Cost Management + Fakturering**. Välj ett lämpligt omfång, till exempel ett faktureringskonto med Microsoft-partneravtal, och välj sedan **Kostnadsanalys**. När sidan läses in väljer du **Exportera**. Välj **Visa alla exporter** under Schemalägg export.

![Välj Exportera och Visa alla exporter](./media/get-started-partners/export01.png)

Välj sedan **Lägg till** och skriv namnet och välj en exporttyp. Välj fliken **Lagring** och ange nödvändig information.

![Lägg till ny export och välj fliken Lagring](./media/get-started-partners/export02.png)

När du skapar en export i partnerklientorganisationen väljer du prenumerationen baserad på betala per användning i partnerklientorganisationen. Skapa ett Azure Storage-konto med hjälp av den prenumerationen.

För Azure RBAC-användare i kundklientorganisationen väljer du en prenumeration i kundklientorganisationen. Skapa ett Azure Storage-konto med hjälp av prenumerationen.

Granska innehållet och välj sedan **Skapa** för att schemalägga en export.

För att verifiera data i exportlistan väljer du lagringskontots namn. På lagringskontosidan väljer du **Containers** och väljer sedan containern. Navigera till motsvarande mapp och välj CSV-filen. Välj **Ladda ned** för att ladda ned CSV-filen och öppna den. Exporterade data liknar kostnadsdata och påminner om användningsinformation från Azure Portal.

![Exempel på exporterade data](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>Cost Management REST API:er

Partner och kunder kan använda Cost Management API:er som beskrivs i följande avsnitt för vanliga uppgifter.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management-API:er – direkta och indirekta providers

Partner med åtkomst till faktureringsomfång i en partnerklientorganisation kan använda följande API:er för att visa fakturerade kostnader.

API:er i prenumerationsomfånget kan anropas av en partner oavsett kostnadsprincip om de har åtkomst till prenumerationen. Andra användare med åtkomst till prenumerationen, t.ex. kunden eller återförsäljaren, kan anropa API:erna endast efter att partnern har aktiverat kostnadsprincipen för kundklientorganisationen.


#### <a name="to-get-a-list-of-billing-accounts"></a>Hämta en lista över faktureringskonton

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Hämta en lista över kunder

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Hämta en lista över prenumerationer

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Hämta en lista över fakturor för en tidsperiod

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

API-anropet returnerar en matris med fakturor som innehåller element som liknar följande JSON-kod.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Använd föregående returnerat ID-fältvärde och ersätt det i följande exempel som omfång för att fråga efter användningsinformation.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

Exemplet returnerar de användningsposter som är associerade med den specifika fakturan.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Hämta principen för hur kunder kan visa kostnader

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Ange principen för hur kunder kan visa kostnader

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Hämta Azure-tjänstanvändning för ett faktureringskonto

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Hämta en kunds Azure-tjänstanvändning

Följande get-anrop är en asynkron åtgärd.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Anropa den `Location` URI som returnerades i svaret för att kontrollera åtgärdens status. När statusen är *Slutförd* innehåller egenskapen `downloadUrl` en länk som du kan använda för att ladda ned den genererade rapporten.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Hämta eller ladda ned prisdokumentet för förbrukade Azure-tjänster

Använd först följande post.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Anropa sedan egenskapsvärdet för den asynkrona åtgärden. Ett exempel:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
Föregående get-anrop returnerar nedladdningslänken som innehåller prisdokumentet.


#### <a name="to-get-aggregated-costs"></a>Hämta aggregerade kostnader

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Skapa en budget för en partner

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Skapa en budget för en kund

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Ta bort en budget

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Nästa steg
- [Börja analysera kostnader](quick-acm-cost-analysis.md) i Cost Management
- [Skapa och hantera budgetar](tutorial-acm-create-budgets.md) i Cost Management
