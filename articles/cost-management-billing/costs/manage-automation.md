---
title: Hantera Azure-kostnader med automatisering
description: Den här artikeln beskriver hur du kan hantera Azure-kostnader med automatisering.
author: bandersmsft
ms.author: banders
ms.date: 01/06/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 02215bace693ac5ac36f9fc29758215d45b23eb1
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051793"
---
# <a name="manage-costs-with-automation"></a>Hantera kostnader med automatisering

Med Cost Management-automatisering kan du skapa en anpassad uppsättning lösningar för att hämta och hantera kostnadsdata. Den här artikeln beskriver vanliga scenarier för Cost Management-automatisering och de alternativ som är tillgängliga utifrån din situation. Om du tänker använda API:er ges exempel på vanliga API-begäranden som hjälper dig att påskynda utvecklingsprocessen.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Automatisera hämtning av kostnadsdata för analys offline

Du kan behöva ladda ned dina Azure-kostnadsdata för att slå ihop dem med andra datauppsättningar. Eller så kan du behöva integrera kostnadsdata i dina egna system. Det finns olika alternativ tillgängliga beroende på hur mycket data det rör sig om. Du måste ha Cost Management-behörighet i lämpligt omfång för att kunna använda API:er och verktyg. Mer information finns i [Tilldela åtkomst till data](./assign-access-acm-data.md).

## <a name="suggestions-for-handling-large-datasets"></a>Förslag för hantering av stora datauppsättningar

Om din organisation använder Azure i stor omfattning med många resurser eller prenumerationer har du en stor mängd användningsinformation. Excel kanske inte kan läsa in så stora filer. I så fall rekommenderar vi följande alternativ:

**Power BI**

Power BI används för att mata in och hantera stora mängder data. Om du är Enterprise-avtalskund kan du använda Power BI-mallappen för att analysera kostnaderna för ditt faktureringskonto. Rapporten innehåller viktiga vyer som används av kunder. Mer information finns i [Analysera Azure-kostnader med Power BI-mallappen](./analyze-cost-data-azure-cost-management-power-bi-template-app.md).

**Power BI-dataanslutning**

Om du vill analysera dina data dagligen rekommenderar vi att du använder [Power BI-dataanslutningen](/power-bi/connect-data/desktop-connect-azure-cost-management) för att hämta data för detaljerad analys. Alla rapporter du skapar uppdateras av anslutningsprogrammet när det uppstår kostnader.

**Cost Management-exporter**

Du kanske inte behöver analysera data dagligen. I så fall kan du använda [exportfunktionen](./tutorial-export-acm-data.md) i Cost Management för att schemalägga dataexporter till ett Azure Storage-konto. Sedan kan du läsa in data i Power BI om det behövs, eller analysera dem i Excel om inte filen är för stor. Exportfunktionen finns i Azure-portalen, men du kan också konfigurera exporter med [export-API:et](/rest/api/cost-management/exports).

**API för användningsinformation**

Överväg att använda [API:et för användningsinformation](/rest/api/consumption/usageDetails) om du har en liten mängd kostnadsdata. Om du har en stor mängd kostnadsdata bör du begära den minsta möjliga mängden användningsdata för en period. Det gör du genom att antingen ange ett litet tidsintervall eller genom att använda ett filter i begäran. Om du till exempel behöver tre års kostnadsdata får du bättre prestanda med API:et om du gör flera anrop för olika tidsintervall i stället för ett enda anrop. Sedan kan du läsa in data i Excel för ytterligare analys.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatisera hämtning med API:et för användningsinformation

Med [API:et för användningsinformation](/rest/api/consumption/usageDetails) kan du enkelt hämta råa, icke-aggregerade kostnadsdata som motsvarar din Azure-faktura. API:et är användbart när din organisation behöver en lösning för att hämta data programmatiskt. Överväg att använda API:et om du vill analysera mindre mängder kostnadsdata. Du bör dock använda andra lösningar som nämnts tidigare om du har större datamängder. Användningsinformationen tillhandahålls per mätare per dag. Den används för beräkning av din månadsfaktura. Versionen för allmän tillgänglighet för API:erna är `2019-10-01`. Använd `2019-04-01-preview` för att komma åt förhandsversionen för reservation och Azure Marketplace-köp med API:erna.

### <a name="usage-details-api-suggestions"></a>Förslag för API:et för användningsinformation

**Schema för begäranden**

Vi rekommenderar att du inte gör _fler än ett begärande_ till API:et för användningsinformation per dag. Mer information om hur ofta kostnadsdata uppdateras och hur avrundning hanteras finns i [Förstå Cost Management-data](./understand-cost-mgt-data.md).

**Mål på den översta nivån utan filtrering**

Använd API:et för att hämta alla data som du behöver på den högsta tillgängliga nivån. Vänta tills alla data som behövs har matats in innan du utför någon filtrering, gruppering eller aggregerad analys. API:et är optimerat för att tillhandahålla stora mängder råa, icke-aggregerade kostnadsdata. Du kan läsa mer om vilka omfång som är tillgängliga i Cost Management i [Förstå och arbeta med omfång](./understand-work-scopes.md). När du har laddat ned de data som behövs för ett omfång använder du Excel för att analysera data ytterligare med filter och pivottabeller.

### <a name="notes-about-pricing"></a>Anmärkningar om prissättning

Observera följande information om du vill stämma av användning och avgifter mot ditt prisdokument eller din faktura.

Priser i prisdokumentet – Priserna som visas i prisdokumentet är de priser som du får från Azure. De justeras till en bestämd måttenhet. Tyvärr är måttenheten inte alltid samma som måttenheten som den faktiska resursanvändningen och de faktiska kostnaderna genereras i.

Priser i användningsinformation – Användningsfiler visar justerade data som kanske inte matchar exakt med prisdokumentet. Specifikt:

- Enhetspris – Priset justeras för att matcha måttenheten som avgifterna faktiskt genereras i av resurserna i Azure. Om en justering görs kommer priset inte att matcha priset som visas i prisdokumentet.
- Måttenhet – Representerar den enhet som kostnaderna faktiskt genereras i av resurserna i Azure.
- Faktiskt pris/resurspris – Priset motsvarar det faktiska pris som du betalar per enhet, efter att rabatter har beräknats. Det är det pris som ska användas med antalet i ”pris × antal”-beräkningar för att stämma av avgifter. Priset tar i beaktande följande scenarier och det justerade enhetspriset som också finns i filerna. Det innebär att det kan skilja sig från det skalade enhetspriset.
  - Nivåindelad prissättning – Exempel: 10 USD för de första 100 enheterna och 8 USD för efterföljande 100 enheter.
  - Inkluderad kvantitet – Exempel: De första 100 enheterna är kostnadsfria och därefter är priset 10 USD per enhet.
  - Reservationer
  - Avrundning som görs under beräkningen – Avrundningen tar hänsyn till den förbrukade kvantiteten, priset per nivåindelad/inkluderad kvantitet och det justerade enhetspriset.

## <a name="example-usage-details-api-requests"></a>Exempelbegäranden till API:et för användningsinformation

Följande exempel är vanliga typer av begäranden som används av Microsoft-kunder.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Hämta användningsinformation för ett omfång inom ett specifikt datumintervall

De data som returneras av begäran motsvarar det datum då användningen togs emot av faktureringssystemet. Informationen kan omfatta kostnader från flera fakturor. Vilket anrop som ska användas varierar beroende på prenumerationstyp.

För äldre kunder med ett Enterprise-avtal (EA) eller en prenumeration av typen Betala per användning, använder du följande anrop:

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01
```

För moderna kunder med ett Microsoft-kundavtal använder du följande anrop:

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?startDate=2020-08-01&endDate=&2020-08-05$top=1000&api-version=2019-10-01
```

### <a name="get-amortized-cost-details"></a>Hämta information om amorterad kostnad

Om du behöver de faktiska kostnaderna för att visa inköp när de påförs kan du ändra *måttet* till `ActualCost` i följande begäran. För att kunna använda amorterade och faktiska kostnader måste du använda `2019-04-01-preview`-versionen. Den aktuella API-versionen fungerar på samma sätt som `2019-10-01`-versionen, med undantag för det nya typ/mått-attributet och de ändrade egenskapsnamnen. Om du har ett Microsoft-kundavtal är dina filter `startDate` och `endDate` i följande exempel.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Hämta stora mängder data regelbundet med exportfunktionen

Du kan regelbundet exportera stora mängder data med exporter från Cost Management. Att exportera är det rekommenderade sättet att hämta icke sammansatta kostnadsdata. Det gäller särskilt när användningsfilerna är för stora för att kunna anropas och laddas ned på ett pålitligt sätt med hjälp av API:et för användningsinformation. Exporterade data placeras i det Azure Storage-konto som du väljer. Därifrån kan du läsa in data i dina egna system och analysera dem efter behov. Information om hur du konfigurerar exporter i Azure-portalen finns i [Exportera data](tutorial-export-acm-data.md).

Om du vill automatisera exporter i olika omfång är exempel-API:ets begäran i nästa avsnitt en bra utgångspunkt. Du kan använda export-API:et för att skapa automatiska exporter som en del av din allmänna miljökonfiguration. Med automatiska exporter ser du till att du har de data du behöver. Du kan använda dem i din organisations system när du utökar användningen av Azure.

### <a name="common-export-configurations"></a>Vanliga exportkonfigurationer

Innan du skapar din första export bör du ta hänsyn till ditt scenario och de konfigurationsalternativ som är nödvändiga för det. Ta följande exportalternativ som exempel:

- **Upprepning** – Bestäm hur ofta exportjobbet ska köras och när en fil ska placeras i ditt Azure Storage-konto. Välj mellan varje dag, varje vecka och varje månad. Försök att konfigurera upprepningen så att den matchar de dataimportjobb som används av organisationens interna system.
- **Upprepningsperiod** – Bestäm hur länge exporten ska vara giltig. Filerna exporteras endast under upprepningsperioden.
- **Tidsram** – Bestäm mängden data som ska genereras av exporten vid en specifik körning. Vanliga alternativ är MonthToDate och WeekToDate.
- **StartDate** – Konfigurera när du vill att exportschemat ska börja. En export skapas på StartDate och därefter baserat på din upprepning.
- **Typ** – Det finns tre exporttyper:
  - ActualCost – Visar den totala användningen och kostnaderna för den angivna perioden, allt eftersom de ackumuleras och visas på fakturan.
  - AmortizedCost – Visar den totala användningen och kostnaderna för den angivna perioden, med amortering tillämpad på de reservationsinköpskostnader som gäller.
  - Användning – Alla exporter som skapats före 20 juli 2020 är av typen Användning. Uppdatera alla schemalagda exporter som antingen ActualCost eller AmortizedCost.
- **Kolumner** – Definierar de datafält som du vill ska ingå i exportfilen. De motsvarar de fält som är tillgängliga i API:et för användningsinformation. Mer information finns i [API för användningsinformation](/rest/api/consumption/usagedetails/list).

### <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Skapa en daglig export för perioden hittills under månaden för en prenumeration

URL för begäran: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

### <a name="automate-alerts-and-actions-with-budgets"></a>Automatisera aviseringar och åtgärder med budgetar

Det finns två viktiga komponenter om du ska kunna få ut mesta möjliga av dina investeringar i molnet. En är automatiskt skapande av budget. Den andra är konfiguration av kostnadsbaserad orkestrering som svar på budgetaviseringar. Det finns olika sätt att automatisera skapandet av en Azure-budget. Olika aviseringssvar sker när de konfigurerade tröskelvärdena för aviseringar överskrids.

Följande avsnitt beskriver tillgängliga alternativ och ger exempel på API-begäranden för att komma igång med budgetautomatisering.

#### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>Så här utvärderas kostnader mot din budgettröskel

Dina kostnader utvärderas mot din budgettröskel en gång per dag. När du skapar en ny budget eller på din budgetåterställningsdag blir kostnaderna jämfört med tröskelvärdet noll/null eftersom utvärderingen kanske inte har inträffat.

När Azure upptäcker att kostnaderna har överskridit tröskelvärdet utlöses en avisering inom en timme från den tid då den identifierades.

#### <a name="view-your-current-cost"></a>Visa din aktuella kostnad

Om du vill visa dina aktuella kostnader måste du göra ett GET-anrop med hjälp av [fråge-API:et](/rest/api/cost-management/query).

Ett GET-anrop till budget-API:et returnerar inte de aktuella kostnader som visas i kostnadsanalysen. I stället returnerar anropet din senaste utvärderade kostnad.

### <a name="automate-budget-creation"></a>Automatisera skapandet av budget

Du kan automatisera skapandet av budgeten med hjälp av [budget-API:et](/rest/api/consumption/budgets). Du kan också skapa en budget med en [budgetmall](quick-create-budget-template.md). Mallar är ett enkelt sätt att standardisera Azure-distributioner samtidigt som du säkerställer att kostnadskontrollen är korrekt konfigurerad och framtvingas.

#### <a name="supported-locales-for-budget-alert-emails"></a>Nationella inställningar som stöds för budgetavisering via e-post

När du använder budgetar får du en avisering om kostnaderna överskrider ett angivet tröskelvärde. Du kan konfigurera upp till fem e-postmottagare per budget. Mottagarna får en e-postavisering inom 24 timmar efter att budgettröskeln har överskridits. Men en mottagare kan dock vilja få sina e-postmeddelanden på ett annat språk. Du kan använda följande språk/kulturkoder med API:erna för budgetar. Ange kulturkoden med parametern `locale`, som i följande exempel.

```json
{
  "eTag": "\"1d681a8fc67f77a\"",
  "properties": {
    "timePeriod": {
      "startDate": "2020-07-24T00:00:00Z",
      "endDate": "2022-07-23T00:00:00Z"
    },
    "timeGrain": "BillingMonth",
    "amount": 1,
    "currentSpend": {
      "amount": 0,
      "unit": "USD"
    },
    "category": "Cost",
    "notifications": {
      "actual_GreaterThan_10_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 20,
        "locale": "en-us",
        "contactEmails": [
          "user@contoso.com"
        ],
        "contactRoles": [],
        "contactGroups": [],
        "thresholdType": "Actual"
      }
    }
  }
}

```

Språk som stöds med en kulturkod:

| Kulturkod| Språk |
| --- | --- |
| sv-se | Engelska (USA) |
| ja-jp | Japanska (Japan) |
| zh-cn | kinesiska (förenklad, Kina) |
| de-de | Tyska (Tyskland) |
| es-es | Spanska (Spanien, internationell) |
| fr-fr | Franska (Frankrike) |
| it-it | Italienska (Italien) |
| ko-kr | Koreanska (Korea) |
| pt-br | Portugisiska (Brasilien) |
| ru-ru | Ryska (Ryssland) |
| zh-tw | kinesiska (traditionell, Taiwan) |
| cs-cz | Tjeckiska (Tjeckien) |
| pl-pl | Polska (Polen) |
| tr-tr | Turkiska (Turkiet) |
| da-dk | Danska (Danmark) |
| dn-gb | Engelska (Storbritannien) |
| hu-hu | Ungerska (Ungern) |
| nb-bo | Norska, bokmål (Norge) |
| nl-nl | Nederländska (Nederländerna) |
| pt-pt | Portugisiska (Portugal) |
| sv-se | Svenska (Sverige) |

#### <a name="common-budgets-api-configurations"></a>Vanliga budget API-konfigurationer

Det finns många sätt att konfigurera en budget i din Azure-miljö. Överväg ditt scenario först och identifiera sedan de konfigurationsalternativ som möjliggör det. Granska följande alternativ:

- **Tidsintervall** – Visar den återkommande period som din budget använder för att ackumulera och utvärdera kostnader. De vanligaste alternativen är månadsvis, kvartalsvis och årsvis.
- **Tidsperiod** – Visar hur länge din budget är giltig. Budgeten har aktiv övervakning och varnar dig bara när den fortfarande är giltig.
- **Aviseringar**
  - E-postadresser för kontakt – E-postadresserna får aviseringar när en budget påförs kostnader och överskrider definierade tröskelvärden.
  - Kontaktroller – Alla användare som har en matchande Azure-roll för det aktuella omfånget får e-postaviseringar med det här alternativet. Prenumerationsägare kan till exempel få en avisering för en budget som skapats för prenumerationsomfånget.
  - Kontaktgrupper – Budgeten anropar de konfigurerade åtgärdsgrupperna när ett aviseringströskelvärde har överskridits.
- **Kostnadsdimensionsfilter** – Samma filtrering som du kan utföra i kostnadsanalys för fråge-API:et kan också göras för din budget. Använd det här filtret för att minska det antal kostnader som du övervakar med budgeten.

När du har identifierat de alternativ för budgetskapande som motsvarar dina behov skapar du budgeten med hjälp av API:et. Exemplet nedan hjälper dig att komma igång med en gemensam budgetkonfiguration.

**Skapa en budget filtrerad på flera resurser och taggar**

URL för begäran: `PUT https://management.azure.com/subscriptions/{SubscriptionId} /providers/Microsoft.Consumption/budgets/{BudgetName}/?api-version=2019-10-01`

```json
{
  "eTag": "\"1d34d016a593709\"",
  "properties": {
    "category": "Cost",
    "amount": 100.65,
    "timeGrain": "Monthly",
    "timePeriod": {
      "startDate": "2017-10-01T00:00:00Z",
      "endDate": "2018-10-31T00:00:00Z"
    },
    "filter": {
      "and": [
        {
          "dimensions": {
            "name": "ResourceId",
            "operator": "In",
            "values": [
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}",
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}"
            ]
          }
        },
        {
          "tags": {
            "name": "category",
            "operator": "In",
            "values": [
              "Dev",
              "Prod"
            ]
          }
        },
        {
          "tags": {
            "name": "department",
            "operator": "In",
            "values": [
              "engineering",
              "sales"
            ]
          }
        }
      ]
    },
    "notifications": {
      "Actual_GreaterThan_80_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 80,
        "contactEmails": [
          "user1@contoso.com",
          "user2@contoso.com"
        ],
        "contactRoles": [
          "Contributor",
          "Reader"
        ],
        "contactGroups": [
          "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.insights/actionGroups/{actionGroupName}
        ],
        "thresholdType": "Actual"
      }
    }
  }
}
```

### <a name="configure-cost-based-orchestration-for-budget-alerts"></a>Konfigurera kostnadsbaserad orkestrering för budgetaviseringar

Du kan konfigurera budgetar för att starta automatiska åtgärder med hjälp av Azures åtgärdsgrupper. Mer information om hur du automatiserar åtgärder med hjälp av budgetar finns i [Automatisering med Azure-budgetar](../manage/cost-management-budget-scenario.md).

## <a name="data-latency-and-rate-limits"></a>Svarstid och hastighetsbegränsningar för data

Vi rekommenderar att du anropar API:erna högst en gång per dag. Cost Management-data uppdateras var fjärde timme i och med att nya användningsdata tas emot från Azure-resursleverantörer. Mer frekventa anrop ger inte mer data. Det skapar bara en ökad belastning. Om du vill veta mer om hur ofta data ändras och hur svarstiden hanteras kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Felkod 429 – Antalet anrop har överskridit hastighetsbegränsningen

För att alla Cost Management-prenumeranter ska få en smidig upplevelse har API:erna för Cost Management hastighetsbegränsningar. Om du når gränsen får du HTTP-statuskoden `429: Too many requests`. Aktuella dataflödesgränser för våra API:er:

- 30 anrop per minut – gränsen gäller per omfång, per användare eller program.
- 200 anrop per minut – gränsen gäller per klientorganisation, per användare eller program.

## <a name="next-steps"></a>Nästa steg

- [Analysera Azure-kostnader med Power BI-mallappen](./analyze-cost-data-azure-cost-management-power-bi-template-app.md).
- [Skapa och hantera exporterade data](./tutorial-export-acm-data.md) med exporter.
- Läs mer om [API:et för användningsinformation](/rest/api/consumption/usageDetails).