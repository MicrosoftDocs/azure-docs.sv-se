---
title: Förstå Azure Cost Management-data
description: Den här artikeln hjälper dig att bättre förstå data som ingår i Azure Cost Management samt hur ofta de bearbetas, samlas in, visas och stängs.
author: bandersmsft
ms.author: banders
ms.date: 01/06/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: contperf-fy21q2
ms.openlocfilehash: e6096c259ec1870a711a515bf02d5d00b4f75345
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964158"
---
# <a name="understand-cost-management-data"></a>Förstå Cost Management-data

Den här artikeln hjälper dig att bättre förstå kostnads- och användningsdata i Azure som ingår i Azure Cost Management. Den förklarar hur ofta data bearbetas, samlas in, visas och stängs. Du faktureras för Azure-användning månatligen. Även om faktureringscykler är månadsperioder varierar cykelns startdatum och slutdatum efter prenumerationstyp. Hur ofta Cost Management tar emot användningsdata varierar beroende på olika faktorer. Dessa faktorer omfattar hur lång tid det tar att bearbeta data och hur ofta Azure-tjänster genererar användning till faktureringssystemet.

Cost Management omfattar all användning och alla inköp, inklusive reservationer och erbjudanden från tredje part för Enterprise-avtalskonton (EA). Microsoft-kundavtalskonton och enskilda prenumerationer med Betala per användning-priser omfattar endast användning från Azure- och Marketplace-tjänster. Support och andra kostnader ingår inte. Kostnader beräknas tills en faktura genereras och tar inte med krediter i beräkningen.

Om du har en ny prenumeration kan du inte använda Cost Management-funktioner direkt. Det kan ta upp till 48 timmar innan du kan använda alla Cost Management-funktioner.

## <a name="supported-microsoft-azure-offers"></a>Microsoft Azure-erbjudanden som stöds

Följande information visar de [Microsoft Azure-erbjudanden](https://azure.microsoft.com/support/legal/offer-details/) som för närvarande stöds i Azure Cost Management. Ett Azure-erbjudande är den typ av Azure-prenumeration som du har. Data är tillgängliga i Cost Management med början från datumet för **Data tillgängliga från**. Om en prenumeration ändrar erbjudanden är kostnader före datumet för erbjudandets ändring inte tillgängliga.

| **Kategori**  | **Erbjudandets namn** | **Kvot-ID** | **Erbjudandets nummer** | **Data tillgängliga från** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Maj 2014<sup>1</sup> |
| **Enterprise-avtal (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Maj 2014<sup>1</sup> |
| **Enterprise-avtal (EA)** | Microsoft Azure Enterprise | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Maj 2014<sup>1</sup> |
| **Microsoft-kundavtal** | Microsoft Azure Plan | EnterpriseAgreement_2014-09-01 | Ej tillämpligt | Mars 2019<sup>3</sup> |
| **Microsoft-kundavtal** | Microsoft Azure Plan for Dev/Test | MSDNDevTest_2014-09-01 | Ej tillämpligt | Mars 2019<sup>3</sup> |
| **Microsoft-kundavtal som stöds av partner** | Microsoft Azure Plan | CSP_2015-05-01, CSP_MG_2017-12-01 och CSPDEVTEST_2018-05-01<br><br>Kvot-ID återanvänds för Microsoft-kundavtalsprenumerationer och äldre CSP-prenumerationer. För närvarande stöds endast Microsoft-kundavtalsprenumerationer. | Ej tillämpligt | Oktober 2019 |
| **Microsoft Developer Network (MSDN)** | MSDN-plattformar<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | Betala per användning                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | Dev/Test – betala per användning         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | Microsoft Partner Network      | MPN_2014-09-01 | MS-AZR-0025P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | Kostnadsfri utvärderingsversion<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | Azure i Open<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | Azure-pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise – MPN<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Professional<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Test Professional<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise: BizSpark<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 oktober 2018<sup>2</sup> |

_<sup>**1**</sup> Data före maj 2014 finns i [Azure Enterprise-portalen](https://ea.azure.com)._

_<sup>**2**</sup> Data före 2 oktober 2018 finns i [Azure-kontocenter](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Microsoft-kundavtal som påbörjades i mars 2019 och inte har historiska data före det._

_<sup>**4**</sup> Historiska data för kreditbaserade och förbetalda prenumerationer överensstämmer kanske inte med din faktura. Se [Historiska data överensstämmer kanske inte med fakturan](#historical-data-might-not-match-invoice) nedan._

Följande erbjudanden stöds inte ännu:

| Kategori  | **Erbjudandets namn** | **Kvot-ID** | **Erbjudandets nummer** |
| --- | --- | --- | --- |
| **Azure Tyskland** | Azure Tyskland – Betala per användning | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government – Betala per användning | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Leverantör av molnlösningar (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Leverantör av molnlösningar (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Leverantör av molnlösningar (CSP)** | Azure Tyskland i CSP för Microsoft Cloud Tyskland   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Betala per användning**                 | Microsoft Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Betala per användning** | Azure for Students<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Betala per användning**                 | Microsoft Azure-sponsring | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Supportavtal** | Standardsupport                    | Default_2014-09-01 | MS-AZR-0041P |
| **Supportavtal** | Professional Direct-support         | Default_2014-09-01 | MS-AZR-0042P |
| **Supportavtal** | Support för utvecklare                   | Default_2014-09-01 | MS-AZR-0043P |
| **Supportavtal** | Supportplan för Tyskland                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Supportavtal** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Supportavtal** | Azure Government Pro-Direct Support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Supportavtal** | Azure Government Developer Support  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

### <a name="free-trial-to-pay-as-you-go-upgrade"></a>Uppgradering från kostnadsfri utvärderingsversion till Betala per användning

Information om tillgängligheten av tjänster på kostnadsfri nivå efter uppgradering till Betala per användning från en kostnadsfri utvärderingsversion finns i [avsnittet med vanliga frågor och svar](https://azure.microsoft.com/free/free-account-faq/).

### <a name="determine-your-offer-type"></a>Fastställ din erbjudandetyp

Om du inte ser data för en prenumeration och vill ta reda på om din prenumeration omfattas av de erbjudanden som stöds kan du kontrollera om din prenumeration stöds. För att kontrollera att en Azure-prenumeration stöds loggar du in på Azure-portalen. Välj sedan **Alla tjänster** i det vänstra menyfönstret. I listan över tjänster väljer du **Prenumerationer**. I listmenyn för prenumerationer väljer du den prenumeration som du vill kontrollera. Din prenumeration visas på fliken Översikt, och du kan se **Erbjudande** och **Erbjudande-ID**. I följande bild visas ett exempel.

![Exempel på fliken Prenumerationsöversikt som visar Erbjudande och Erbjudande-ID](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Kostnader som ingår i Cost Management

Följande tabeller visar data som ingår eller inte ingår i Cost Management. Alla kostnader är endast beräknade sådana tills en faktura genereras. Kostnader som visas omfattar inte kostnadsfria och förskottsbetalade krediter.

| **Ingår** | **Ingår inte** |
| --- | --- |
| Användning av Azure-tjänster<sup>5</sup>        | Supportavgifter – mer information finns i [Förklaring av fakturavillkor](../understand/understand-invoice.md). |
| Användning av Marketplace-erbjudanden<sup>6</sup> | Skatter – mer information finns i [Förklaring av fakturavillkor](../understand/understand-invoice.md). |
| Marketplace-inköp<sup>6</sup>      | Krediter – mer information finns i [Förklaring av fakturavillkor](../understand/understand-invoice.md). |
| Reservationsköp<sup>7</sup>      |  |
| Upplupna kostnader för reservationsköp<sup>7</sup>      |  |

_<sup>**5**</sup> Användning av Azure-tjänster baseras på reservationer och förhandlade priser._

_<sup>**6**</sup> Marketplace-köp är för närvarande inte tillgängliga för MSDN- och Visual Studio-erbjudanden._

_<sup>**7**</sup> Reservationsköp är endast tillgängliga för Enterprise-avtalskonton (EA) och Microsoft-kundavtalskonton för tillfället._

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>Hur taggar används i kostnads- och användningsdata

Azure Cost Management tar emot taggar som en del av varje användningspost som skickas av de enskilda tjänsterna. Följande begränsningar gäller för dessa taggar:

- Taggar måste användas direkt för resurser och ärvs inte implicit från den överordnade resursgruppen.
- Resurstaggar stöds bara för resurser som har distribuerats till resursgrupper.
- Vissa distribuerade resurser kanske inte stöder taggar eller så kanske de inte innehåller taggar i användningsdata.
- Resurstaggar ingår bara i användningsdata under tiden taggen används – taggar används inte för historiska data.
- Resurstaggar är endast tillgängliga i Cost Management efter att data har uppdaterats.
- Resurstaggar är bara tillgängliga i Cost Management när resursen är aktiv/körs och skapar användningsposter. Exempelvis när en virtuell dator frigörs.
- För hantering av taggar krävs deltagaråtkomst till varje resurs.
- För hantering av taggprinciper krävs antingen ägar- eller principdeltagaråtkomst till en hanteringsgrupp, prenumeration eller resursgrupp.
    
Gå igenom följande frågor om du inte kan se en specifik tagg i Cost Management:

- Användes taggen direkt för resursen?
- Användes taggen för mer än 24 timmar sedan?
- Stöder resurstypen taggar? Följande resurstyper stöder inte taggar i användningsdata per den 1 december 2019. En fullständig lista över vad som stöds finns i [Stöd för taggar för Azure-resurser](../../azure-resource-manager/management/tag-support.md).
    - Azure Active Directory B2C-kataloger
    - Azure Bastion
    - Azure Firewall-brandväggar
    - Azure NetApp Files
    - Data Factory
    - Databricks
    - Lastbalanserare
    - Network Watcher
    - Notification Hubs
    - Service Bus
    - Time Series Insights
    
Här följer några tips för att arbeta med taggar:

- Planera framåt och definiera en taggningsstrategi som gör att du kan bryta ned kostnader efter organisation, program, miljö osv.
- Använd Azure Policy för att kopiera resursgrupptaggar till enskilda resurser och tillämpa din taggningsstrategi.
- Använd Tags-API:et med Query eller UsageDetails om du vill hämta alla kostnader baserat på de aktuella taggarna.

## <a name="cost-and-usage-data-updates-and-retention"></a>Uppdateringar av kostnader och användningsdata och kvarhållning

Kostnads- och användningsdata finns vanligtvis i Kostnadshantering + fakturering i Azure-portalen och stödjande API:er inom 8–24 timmar. Tänk på följande när du granskar kostnader:

- Varje Azure-tjänst (till exempel Storage, Compute och SQL) utvärderar användning vid olika intervall – du kan se data för vissa tjänster tidigare än andra.
- Uppskattade avgifter för den aktuella faktureringsperioden uppdateras sex gånger per dag.
- Uppskattade avgifter för den aktuella faktureringsperioden kan ändras när du förbrukar mer användning.
- Varje uppdatering är kumulativ och innehåller alla radobjekt och all information från föregående uppdatering.
- Azure slutför, eller _stänger_, den aktuella faktureringsperioden upp till 72 timmar (tre kalenderdagar) efter faktureringsperiodens slut.
- Under perioden för den öppna månaden (inte fakturerad) bör kostnadshanteringsdata endast betraktas som en uppskattning. I vissa fall kan det uppstå en fördröjning mellan själva användningen och dess registrering i systemet.

I följande exempel visas hur faktureringsperioder kan sluta:

* Enterprise-avtalsprenumerationer (EA) – om faktureringsmånaden upphör den 31 mars uppdateras de uppskattade avgifterna upp till 72 timmar senare. I det här exemplet blir det senast midnatt (UTC) 4 april.
* Betala per användning-prenumerationer – om faktureringsmånaden upphör den 15 maj kan de uppskattade avgifterna uppdateras upp till 72 timmar senare. I det här exemplet blir det senast midnatt (UTC) 19 maj.

När kostnader och användningsdata blir tillgängliga i Cost Management + Billing bevaras de i minst sju år.

### <a name="rerated-data"></a>Omklassificerade data

Oavsett om du använder Cost Management-API:er, Power BI eller Azure-portalen för att hämta data bör du förvänta dig att den aktuella faktureringsperiodens avgifter omklassificeras och därför ändras fram till att fakturan stängs.

## <a name="cost-rounding"></a>Kostnadsavrundning

Kostnaderna som visas i Cost Management avrundas. Kostnaderna som returneras av fråge-API:et avrundas inte. Ett exempel:

- Kostnadsanalys på Azure-portalen – avgifterna avrundas med standardavrundningsregler: värden från 0,5 och högre avrundas uppåt, annars avrundas kostnaderna nedåt. Avrundning görs endast när värden visas. Ingen avrundning görs under databearbetning och aggregering. Till exempel aggregeras kostnader i en kostnadsanalys så här:
  -    Kostnad 1: 0,004 USD
  - Kostnad 2: 0,004 USD
  -    Den aggregerade kostnaden blir: 0,004 + 0,004 = 0,008. Kostnaden som visas är 0,01.
- Fråge-API – kostnader visas med åtta decimaler och ingen avrundning görs.

## <a name="historical-data-might-not-match-invoice"></a>Historiska data överensstämmer kanske inte med fakturan

Historiska data för kreditbaserade och förbetalda erbjudanden överensstämmer kanske inte med din faktura. Vissa erbjudanden för Azure Betala per användning, MSDN och Visual Studio kan ha Azure-krediter och förskottsbetalningar som tillämpas på fakturan. Men historiska data som visas i Cost Management baseras endast på dina beräknade förbrukningsavgifter. Historiska data i Cost Management inkluderar inte betalningar och krediter. Därmed överensstämmer kanske inte historiska data som visas för följande erbjudanden exakt med din faktura.

- Azure for Students (MS-AZR-0170P)
- Azure i Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Kostnadsfri utvärderingsversion (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har slutfört den första Cost Management-snabbstarten kan du gå till [Börja analysera kostnader](./quick-acm-cost-analysis.md).