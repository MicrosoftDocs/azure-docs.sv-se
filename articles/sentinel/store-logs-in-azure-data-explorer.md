---
title: Integrera Azure Data Explorer för långsiktig loggbevarande | Microsoft Docs
description: Skicka Azure Sentinel loggar till Azure Data Explorer för långsiktig kvarhållning för att minska kostnaderna för datalagring.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/21/2021
ms.author: bagol
ms.openlocfilehash: c7d9ef58d4bb15afe59c6734ce887c2cc3c07c26
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836291"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>Integrera Azure Data Explorer för långsiktig loggbevarande

<!--Info not included:>

Script - can't xref out to a private github repo from docs
-->

Som standard lagras loggar som matas in i Azure Sentinel i Azure Monitor Log Analytics. Den här artikeln förklarar hur du minskar kvarhållningskostnaderna i Azure Sentinel genom att skicka dem till Azure Data Explorer (ADX) för långsiktig kvarhållning.

Lagring av loggar i ADX minskar kostnaderna samtidigt som du behåller möjligheten att köra frågor mot dina data, och det är särskilt användbart när dina data växer. Även om säkerhetsdata till exempel kan förlora värde över tid kan du behöva behålla loggar för regelkrav eller köra regelbundna undersökningar av äldre data.

## <a name="about-azure-data-explorer"></a>Om Azure Data Explorer

ADX är en plattform för stordataanalys som är mycket optimerad för logg- och dataanalys. Eftersom ADX använder Kusto Query Language (KQL) som frågespråk är det ett bra alternativ för Azure Sentinel datalagring. Med ADX för din datalagring kan du köra plattformsoberoende frågor och visualisera data i både ADX och Azure Sentinel.

Mer information finns i [ADX-dokumentationen och](/azure/data-explorer/) [bloggen](https://azure.microsoft.com/en-us/blog/tag/azure-data-explorer/).

### <a name="when-to-integrate-with-adx"></a>När du ska integrera med ADX

Azure Sentinel innehåller fullständiga SIEM- och SOAR-funktioner, snabb distribution och konfiguration, samt avancerade, inbyggda säkerhetsfunktioner för SOC-team. Värdet för att lagra säkerhetsdata i Azure Sentinel kan dock minska efter några månader, när SOC-användare inte behöver komma åt dem så ofta som de kommer åt nyare data.

Om du bara behöver komma åt vissa tabeller ibland, till exempel för periodiska undersökningar eller granskningar, kan du överväga att det inte längre är kostnadseffektivt att behålla dina data i Azure Sentinel data. Nu rekommenderar vi att du lagrar data i ADX, vilket kostar mindre, men ändå kan du utforska med samma KQL-frågor som du kör i Azure Sentinel.

Du kan komma åt data i ADX direkt från Azure Sentinel med hjälp av [Log Analytics ADX-proxyfunktionen](//azure/azure-monitor/logs/azure-monitor-data-explorer-proxy). Det gör du genom att använda frågor mellan kluster i din loggsökning eller arbetsböcker. 

> [!IMPORTANT]
> Viktiga SIEM-funktioner, inklusive analysregler, UEBA och undersökningsdiagrammet, stöder inte data som lagras i ADX.
>

> [!NOTE]
> Integrering med ADX kan också ge dig kontroll och kornighet i dina data. Mer information finns i [Designöverväganden.](#design-considerations)
> 
## <a name="send-data-directly-to-azure-sentinel-and-adx-in-parallel"></a>Skicka data direkt till Azure Sentinel och ADX parallellt

Du kanske vill behålla *alla* data med säkerhetsvärde i Azure Sentinel som ska användas i identifieringar, incidentundersökningar, hotjakt, UEBA och så vidare. Att lagra dessa data Azure Sentinel fördelar främst ANVÄNDARE av Security Operations Center (SOC), där det vanligtvis räcker med 3–12 månaders lagring.

Du kan också konfigurera alla dina data, oavsett dess *säkerhetsvärde,* så att de skickas till ADX samtidigt, där du kan lagra dem längre. När data skickas till både Azure Sentinel och ADX på samma gång resulterar det i viss duplicering, men kostnadsbesparingarna kan vara betydande eftersom du minskar kvarhållningskostnaderna i Azure Sentinel.

> [!TIP]
> Med det här alternativet kan du också korrelera data som sprids över datalager, till exempel för att utöka säkerhetsdata som lagras i Azure Sentinel med driftdata eller långsiktiga data som lagras i ADX. Mer information finns i [Frågefrågor mellan resurser Azure Data Explorer med hjälp av Azure Monitor](/azure/azure-monitor/logs/azure-monitor-data-explorer-proxy).
>

Följande bild visar hur du kan behålla alla dina data i ADX, samtidigt som du bara skickar dina säkerhetsdata till Azure Sentinel för daglig användning.

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="Lagra data i ADX och Azure Sentinel parallellt.":::

Mer information om hur du implementerar det här arkitekturalternativet finns [i Azure Data Explorer övervakning](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer).

## <a name="export-data-from-log-analytics-into-adx"></a>Exportera data från Log Analytics till ADX

I stället för att skicka data direkt till ADX kan du välja att exportera data från Log Analytics till ADX via en ADX-händelsehubb eller Azure Data Factory.

### <a name="data-export-architecture"></a>Arkitektur för dataexport

Följande bild visar ett exempelflöde med exporterade data via Azure Monitor datainmatningspipeline. Dina data dirigeras till Log Analytics som standard, men du kan också konfigurera dem för att exportera till ett Azure Storage-konto eller händelsehubb.

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="Exportera data från Azure Monitor – arkitektur.":::

När du konfigurerar dataexportreglerna väljer du de typer av loggar som du vill exportera. När den har konfigurerats exporteras nya data som kommer till Log Analytics-inmatningsslutpunkten och riktas mot arbetsytan för de valda tabellerna till ditt lagringskonto eller händelsehubb.

Tänk på följande när du konfigurerar data för export:

|Att tänka på  | Information |
|---------|---------|
|**Omfång för exporterade data**     |  När exporten har konfigurerats för en viss tabell exporteras alla data som skickas till tabellen, utan undantag. Det går inte att exportera en filtrerad delmängd av dina data eller begränsa exporten till specifika händelser.       |
|**Platskrav**     |   Både Azure Monitor/Azure Sentinel och målplatsen (ett Azure Storage-konto eller händelsehubb) måste finnas i samma geografiska region.      |
|**Tabeller som stöds**     | Alla tabeller stöds inte för export, till exempel anpassade loggtabeller, som inte stöds. <br><br>Mer information finns i [Dataexport för Log Analytics-arbetsyta i Azure Monitor](/azure/azure-monitor/logs/logs-data-export) och [listan över tabeller som stöds.](/azure/azure-monitor/logs/logs-data-export#supported-tables)         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>Metoder och procedurer för dataexport

Använd någon av följande procedurer för att exportera data från Azure Sentinel till ADX:

- **Via en ADX-händelsehubb**. Exportera data från Log Analytics till en händelsehubb, där du kan mata in dem i ADX. Den här metoden lagrar vissa data (de första X månaderna) i både Azure Sentinel och ADX.

- **Via Azure Storage och Azure Data Factory**. Exportera data från Log Analytics till Azure Blob Storage sedan Azure Data Factory för att köra ett periodiskt kopieringsjobb för att ytterligare exportera data till ADX. Med den här metoden kan du kopiera data från Azure Data Factory bara när den närmar sig sin kvarhållningsgräns i Azure Sentinel/Log Analytics, vilket undviker duplicering.

### <a name="adx-event-hub"></a>[ADX-händelsehubb](#tab/adx-event-hub)

Det här avsnittet beskriver hur du exporterar Azure Sentinel data från Log Analytics till en händelsehubb, där du kan mata in dem i ADX. På samma sätt som [data skickas direkt till Azure Sentinel och ADX](#send-data-directly-to-azure-sentinel-and-adx-in-parallel)parallellt , inkluderar den här metoden viss dataduplicering eftersom data strömmas till ADX när de tas emot i Log Analytics.

Följande bild visar ett exempelflöde med exporterade data till en händelsehubb, varifrån den matas in i ADX.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="Exportera data till ADX via en ADX-händelsehubb.":::

Arkitekturen som visas i föregående bild ger en fullständig Azure Sentinel SIEM-upplevelse, inklusive incidenthantering, visuella undersökningar, hotjakt, avancerade visualiseringar, UEBA med mera, för data som måste användas ofta, var *X* månad. Med den här arkitekturen kan du samtidigt köra frågor mot långsiktiga data genom att komma åt dem direkt i ADX eller via Azure Sentinel tack vare ADX-proxyfunktionen. Frågor till långsiktig datalagring i ADX kan portas utan ändringar från Azure Sentinel till ADX.

> [!TIP]
> Mer information om den här proceduren finns i [Självstudie: Mata in och fråga övervakningsdata i Azure Data Explorer](/azure/data-explorer/ingest-data-no-code).
>

**Så här exporterar du data till ADX via en händelsehubb:**

1. **Konfigurera Log Analytics-dataexporten till en händelsehubb.** Mer information finns i Dataexport [för Log Analytics-arbetsyta i Azure Monitor](/azure/azure-monitor/platform/logs-data-export).

1. **Skapa ett ADX-kluster och en databas**. Mer information finns i:

    - [Skapa ett Azure Data Explorer kluster och en databas](/azure/data-explorer/create-cluster-database-portal)
    - [Välj rätt beräknings-SKU för ditt Azure Data Explorer kluster](/azure/data-explorer/manage-cluster-choose-sku)

1. **Skapa måltabeller**. Rådata matas först in i en mellanliggande tabell, där rådata lagras, ändras och expanderas.

    En uppdateringsprincip, som liknar en funktion som tillämpas på alla nya data, används för att mata in expanderade data i den slutliga tabellen, som har samma schema som den ursprungliga tabellen i Azure Sentinel.

    Ange kvarhållningen för råtabellen till **0** dagar. Data lagras endast i den korrekt formaterade tabellen och tas bort i råtabellen så fort den transformeras.

    Mer information finns i [Mata in och köra frågor mot övervakningsdata i Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Skapa tabellmappning**. Mappa JSON-tabellerna för att definiera hur poster hamnar i råhändelsetabellen när de kommer från en händelsehubb. Mer information finns i [Skapa uppdateringsprincipen för mått- och loggdata.](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. **Skapa en uppdateringsprincip och koppla den till tabellen raw records**. I det här steget skapar du en funktion som kallas uppdateringsprincip och kopplar den till måltabellen så att data transformeras vid inmatningen.

    > [!NOTE]
    > Det här steget krävs bara när du vill ha datatabeller i ADX med samma schema och format som i Azure Sentinel.
    >

    Mer information finns i Ansluta [en händelsehubb till Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Skapa en dataanslutning mellan händelsehubben och rådatatabellen i ADX**. Konfigurera ADX med information om hur du exporterar data till händelsehubben.

    Följ instruktionerna i [Azure Data Explorer och](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) ange följande information:

    - **Rikta in dig på**. Ange den specifika tabellen med rådata.
    - **Formatera**. Ange `.json` som tabellformat.
    - **Mappning som ska tillämpas**. Ange mappningstabellen som skapades [i steg 4](#mapping) ovan.


1. **Ändra kvarhållning för måltabellen**. [Standardprincipen för Azure Data Explorer kan](/azure/data-explorer/kusto/management/retentionpolicy) vara mycket längre än du behöver.

    Använd följande kommando för att uppdatera kvarhållningsprincipen till ett år:

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```
### <a name="azure-storage--azure-data-factory"></a>[Azure Storage/Azure Data Factory](#tab/azure-storage-azure-data-factory)

I det här avsnittet beskrivs hur du exporterar Azure Sentinel data från Log Analytics till Azure Storage, där Azure Data Factory kan köra ett vanligt jobb för att exportera data till ADX.

Med Azure Storage och Azure Data Factory kan du kopiera data från Azure Storage endast när det är nära kvarhållningsgränsen i Azure Sentinel/Log Analytics. Det finns ingen dataduplicering  och ADX används endast för att komma åt data som är äldre än kvarhållningsgränsen i Azure Sentinel.

> [!TIP]
> Arkitekturen för att använda Azure Storage och Azure Data Factory för äldre data är mer komplex, men den här metoden kan ge övergripande större kostnadsbesparingar.
>
Följande bild visar ett exempelflöde med exporterade data till en Azure Storage, varifrån Azure Data Factory kör ett vanligt jobb för att ytterligare exportera den till ADX.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="Exportera data till ADX via Azure Storage och Azure Data Factory.":::

**Så här exporterar du data till ADX via Azure Storage och Azure Data Factory:**

1. **Konfigurera Log Analytics-dataexporten till en händelsehubb.** Mer information finns i Dataexport [för Log Analytics-arbetsyta i Azure Monitor](/azure/azure-monitor/logs/logs-data-export?tabs=portal#enable-data-export).

1. **Skapa ett ADX-kluster och en databas**. Mer information finns i:

    - [Skapa ett Azure Data Explorer kluster och en databas](/azure/data-explorer/create-cluster-database-portal)
    - [Välj rätt beräknings-SKU för ditt Azure Data Explorer kluster](/azure/data-explorer/manage-cluster-choose-sku)

1. **Skapa måltabeller**. Rådata matas först in i en mellanliggande tabell, där rådata lagras, ändras och expanderas.

    En uppdateringsprincip, som liknar en funktion som tillämpas på alla nya data, används för att mata in expanderade data i den slutliga tabellen, som har samma schema som den ursprungliga tabellen i Azure Sentinel.

    Ange kvarhållningen för råtabellen till **0** dagar. Data lagras endast i den korrekt formaterade tabellen och tas bort i råtabellen så fort den transformeras.

    Mer information finns i [Mata in och köra frågor mot övervakningsdata i Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Skapa tabellmappning**. Mappa JSON-tabellerna för att definiera hur poster hamnar i råhändelsetabellen när de kommer från en händelsehubb. Mer information finns i [Skapa uppdateringsprincipen för mått- och loggdata.](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. **Skapa en uppdateringsprincip och koppla den till tabellen raw records**. I det här steget skapar du en funktion som kallas uppdateringsprincip och kopplar den till måltabellen så att data transformeras vid inmatningen.

    > [!NOTE]
    > Det här steget krävs bara när du vill ha datatabeller i ADX med samma schema och format som i Azure Sentinel.
    >

    Mer information finns i Ansluta [en händelsehubb till Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Skapa en dataanslutning mellan händelsehubben och rådatatabellen i ADX**. Konfigurera ADX med information om hur du exporterar data till händelsehubben.

    Följ instruktionerna i [Azure Data Explorer och](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) ange följande information:

    - **Rikta in dig på**. Ange den specifika tabellen med rådata.
    - **Formatera**. Ange `.json` som tabellformat.
    - **Mappning som ska tillämpas**. Ange mappningstabellen som skapades [i steg 4](#mapping) ovan.

1. **Konfigurera Azure Data Factory pipeline:**

    - Skapa länkade tjänster för Azure Storage och Azure Data Explorer. Mer information finns i:

        - [Kopiera och transformera data i Azure Blob Storage med hjälp av Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
        - [Kopiera data till eller från Azure Data Explorer med hjälp av Azure Data Factory](/azure/data-factory/connector-azure-data-explorer).

    - Skapa en datauppsättning från Azure Storage. Mer information finns i [Datauppsättningar i Azure Data Factory](/azure/data-factory/concepts-datasets-linked-services).

    - Skapa en datapipeline med en kopieringsåtgärd baserat på **lastModifiedDate-egenskaperna.**

        Mer information finns i [Kopiera nya och ändrade filer av **LastModifiedDate** med Azure Data Factory](/azure/data-factory/solution-template-copy-new-files-lastmodifieddate).

---

## <a name="design-considerations"></a>Designöverväganden

Tänk på följande Azure Sentinel lagra dina data i ADX:

|Att tänka på  |Description  |
|---------|---------|
|**Klusterstorlek och SKU**     | Planera noggrant för antalet noder och VM-SKU:n i klustret. Dessa faktorer avgör mängden processorkraft och storleken på din heta cache (SSD och minne). Ju större cache, desto mer data kommer du att kunna köra frågor mot med högre prestanda. <br><br>Vi rekommenderar att du besöker [ADX-storlekskalkylatorn](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html), där du kan leka med olika konfigurationer och se den resulterande kostnaden. <br><br>ADX har också en funktion för automatisk skalning som fattar intelligenta beslut för att lägga till/ta bort noder efter behov baserat på klusterbelastningen. Mer information finns i Manage [cluster horizontal scaling (scale out) in Azure Data Explorer to accommodate changing demand](/azure/data-explorer/manage-cluster-horizontal-scaling).      |
|**Varm/kall cache**     | ADX ger kontroll över de datatabeller som finns i cacheminnet och returnerar resultat snabbare. Om du har stora mängder data i ADX-klustret kanske du vill dela upp tabeller per månad, så att du har större kornighet på de data som finns i din heta cache. <br><br>Mer information finns i [Cacheprincip (varm och kall cache)](/azure/data-explorer/kusto/management/cachepolicy)       |
|**Kvarhållning**     |   I ADX kan du konfigurera när data tas bort från en databas eller en enskild tabell, vilket också är en viktig del av att begränsa lagringskostnaderna. <br><br> Mer information finns i [Kvarhållningsprincip.](/azure/data-explorer/kusto/management/retentionpolicy)       |
|**Säkerhet**     |  Flera ADX-inställningar kan hjälpa dig att skydda dina data, till exempel identitetshantering, kryptering och så vidare. Specifikt för rollbaserad åtkomstkontroll (RBAC) kan ADX konfigureras för att begränsa åtkomsten till databaser, tabeller eller till och med rader i en tabell. Mer information finns i [Säkerhet i Azure Data Explorer säkerhet](/azure/data-explorer/security) på [radnivå.](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy)|
|**Datadelning**     |   Med ADX kan du göra datadelar tillgängliga för andra parter, till exempel partner eller leverantörer, och även köpa data från andra parter. Mer information finns i Använda [Azure Data Share för att dela data med Azure Data Explorer](/azure/data-explorer/data-share).      |
| **Andra kostnadskomponenter** | Överväg de andra kostnadskomponenterna för följande metoder: <br><br>**Exportera data via en ADX-händelsehubb:** <br>– Log Analytics-dataexportkostnader, debiteras per exporterad GB. <br>– Kostnader för händelsehubben som debiteras per dataflödesenhet.  <br><br>**Exportera data via Azure Storage och Azure Data Factory**: <br>– Log Analytics-dataexport, debiteras per exporterad GB. <br>– Azure Storage som debiteras av GB som lagras. <br>– Azure Data Factory debiteras per kopia av aktiviteter som körs.
|     |         |

## <a name="next-steps"></a>Nästa steg

Oavsett var du lagrar dina data kan du fortsätta att jaga och undersöka med hjälp av Azure Sentinel.

Mer information finns i:

- [Självstudie: Undersöka incidenter med Azure Sentinel](tutorial-investigate-cases.md)
- [Jaga efter hot med Azure Sentinel](hunting.md)
