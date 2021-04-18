---
title: Ta med din egen ML till Azure Sentinel | Microsoft Docs
description: Den här artikeln förklarar hur du skapar och använder dina egna maskininlärningsalgoritmer för dataanalys i Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 2164b8ac6e62b8826d5879da07384769c503bfb5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598609"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Ta med dina Machine Learning (ML) till Azure Sentinel

Machine Learning (ML) är en av de viktigaste Azure Sentinel och ett av de viktigaste attributen som skiljer den åt. Azure Sentinel erbjuder ML i flera upplevelser: inbyggd [](fusion.md) i Fusion-korrelationsmotorn och Jupyter Notebooks samt den nyligen tillgängliga BYO ML-plattformen (Build-Your-Own ML). 

ML-identifieringsmodeller kan anpassas till enskilda miljöer och till ändringar i användarbeteende, för att minska falska positiva identifieringar och identifiera hot som inte skulle hittas med en traditionell metod. Många säkerhetsorganisationer förstår värdet av ML för säkerhet, även om inte många av dem har en lyx av experter som har kunskaper inom både säkerhet och ML. Vi har utformat det ramverk som presenteras här så att säkerhetsorganisationer och experter kan växa med oss på deras ML-resa. Organisationer som inte har erfarenhet av ML, eller utan nödvändig expertis, kan få ut ett betydande skydd av Azure Sentinel inbyggda ML-funktioner.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="ramverk för maskininlärning":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Vad är BYO-ML Machine Learning plattformen Bring Your Own Machine Learning?

För organisationer som har ML-resurser och vill skapa anpassade ML-modeller för sina unika affärsbehov erbjuder vi **BYO-ML-plattformen**. Plattformen använder den här miljön [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/) Jupyter Notebooks för att skapa ML-miljön. Den innehåller följande komponenter:

- ett BYO-ML-paket som innehåller bibliotek som hjälper dig att komma åt data och skicka resultaten tillbaka till Log Analytics (LA), så att du kan integrera resultaten med identifiering, undersökning och jakt. 

- ML-algoritmmallar som du kan anpassa för att passa specifika säkerhetsproblem i din organisation. 

- exempelanteckningsböcker för att träna modellen och schemalägga modellbedömningen. 

Dessutom kan du använda dina egna ML-modeller och/eller din egen Spark-miljö för att integrera med Azure Sentinel.

Med BYO-ML-plattformen kan du komma igång snabbt med att skapa dina egna ML-modeller: 

- Notebook-datorn med exempeldata hjälper dig att få praktisk erfarenhet från end-to-end utan att behöva oroa dig för att hantera produktionsdata.

- Paketet som är integrerat med Spark-miljön minskar utmaningarna och friktionen med att hantera infrastrukturen.

- Biblioteken stöder dataförflyttning. Notebook-datorer för träning och bedömning visar upplevelsen från slutet till slut och fungerar som en mall som du kan anpassa till din miljö.

### <a name="use-cases"></a>Användningsfall
 
BYO-ML-plattformen och -paketet minskar avsevärt den tid och ansträngning som du behöver för att skapa dina egna ML-identifieringar och de frigör möjligheten att hantera specifika säkerhetsproblem i Azure Sentinel. Plattformen stöder följande användningsfall:

**Träna en ML-algoritm för att hämta en anpassad modell:** Du kan använda en befintlig ML-algoritm (som delas av Microsoft eller av användar communityn) och enkelt träna den på dina egna data för att få en anpassad ML-modell som passar dina data och din miljö bättre.

**Ändra en ML-algoritmmall för att hämta en anpassad modell:** Du kan ändra en ML-algoritmmall (delas av Microsoft eller av användar communityn) och träna den ändrade algoritmen på dina egna data för att härleda en anpassad modell som passar ditt specifika problem.

**Skapa en egen modell:** Skapa din egen modell från grunden med Azure Sentinel BYO-ML-plattformen och verktygen.

**Integrera din Databricks-/Spark-miljö:** Integrera din befintliga Databricks/Spark-miljö i Azure Sentinel och använd BYO-ML-bibliotek och -mallar för att skapa ML-modeller för deras unika situationer.

**Importera din egen ML-modell:** Du kan importera dina egna ML-modeller och använda BYO-ML-plattformen och -verktygen för att integrera dem med Azure Sentinel.

**Dela en ML-algoritm:** Dela en ML-algoritm som communityn kan använda och anpassa.

**Använd ML för att ge SecOps:** Använd din egen anpassade ML-modell och resultat för jakt, identifieringar, undersökning och svar.

Den här artikeln visar komponenterna i BYO-ML-plattformen och hur du använder plattformen och algoritmen för avvikande resursåtkomst för att leverera en anpassad ML-identifiering med Azure Sentinel.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark-miljö

[Apache Spark™](http://spark.apache.org/) tog ett stort steg framåt när det gäller att förenkla stordata genom att tillhandahålla ett enhetligt ramverk för att skapa datapipelines. Azure Databricks detta ytterligare genom att tillhandahålla en molnplattform utan hantering som bygger på Spark. Vi rekommenderar att du använder Databricks för din BYO-ML-plattform så att du kan fokusera på att hitta svar som direkt påverkar din verksamhet, i stället för att gå vidare med datapipelines och plattformsproblem.
Om du redan har Databricks eller någon annan Spark-miljö och föredrar att använda den befintliga konfigurationen fungerar BYO-ML-paketet bra på dem också. 

## <a name="byo-ml-package"></a>BYO-ML-paket

BYO ML-paketet innehåller metodtips och forskning av Microsoft i ml-fronten för säkerhet. I det här paketet tillhandahåller vi följande lista över verktyg, notebook-datorer och algoritmmallar för säkerhetsproblem.

| Filnamn | Description |
| --------- | ----------- |
| azure_sentinel_utilities.whl | Innehåller verktyg för att läsa blobar från Azure och skriva till Log Analytics. |
| AnomalousRASampleData | Notebook demonstrerar användningen av modellen för avvikande resursåtkomst i Azure Sentinel genererade exempeldata för träning och testning. |
| AnomalousRATraining.ipynb | Notebook för att träna algoritmen, skapa och spara modellerna. |
| AnomalousRAScoring.ipynb | Notebook för att schemalägga modellen att köras, visualisera resultatet och skriva poäng tillbaka till Azure Sentinel. |
|

Den första ML-algoritmmallen som vi erbjuder är [för identifiering av avvikande resursåtkomst.](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML) Den baseras på en samarbetsbaserad filtreringsalgoritm och tränas med åtkomstloggar för Windows-filresurs (säkerhetshändelser med händelse-ID 5140). Den viktigaste informationen du behöver för den här modellen i loggen är parkopplingen av användare och resurser som används. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Exempel på genomgång: Avvikande åtkomstidentifiering för filresurs 

Nu när du är bekant med de viktigaste komponenterna i BYO-ML-plattformen är det här ett exempel som visar hur du använder plattformen och komponenterna för att leverera en anpassad ML-identifiering.

### <a name="setup-the-databricksspark-environment"></a>Konfigurera Databricks/Spark-miljön

Du måste konfigurera din egen Databricks-miljö om du inte redan har en. Instruktioner finns i [dokumentet snabbstart för Databricks.](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal)

### <a name="auto-export-instruction"></a>Instruktion för automatisk export

Om du vill skapa anpassade ML-modeller baserat på dina egna data i Azure Sentinel måste du exportera dina data från Log Analytics till en Blob Storage- eller Event Hub-resurs, så att ML-modellen kan komma åt dem från Databricks. Lär dig hur [du matar in data i Azure Sentinel](connect-data-sources.md).

I det här exemplet måste du ha dina träningsdata för loggen för filresursåtkomst i Azure Blob Storage. Dataformatet dokumenteras i anteckningsboken och biblioteken.

Du kan automatiskt exportera data från Log Analytics med [hjälp av Azure-kommandoradsgränssnittet (CLI).](/cli/azure/monitor/log-analytics) 

Du måste tilldelas rollen **Deltagare** i Log Analytics-arbetsytan, ditt lagringskonto och din EventHub-resurs för att kunna köra kommandona. 

Här är en exempeluppsättning med kommandon för att konfigurera automatisk export:

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>Exportera anpassade data

För anpassade data som inte stöds av Log Analytics-automatisk export kan du använda logikappen eller andra lösningar för att flytta dina data. Du kan referera till [bloggen och skriptet Exportera Log Analytics-data](https://techcommunity.microsoft.com/t5/azure-monitor/log-analytics-data-export-preview/ba-p/1783530) till Blob Store.

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Korrelera med data utanför Azure Sentinel

Du kan också ta data från Azure Sentinel till bloblagringen eller händelsehubben och korrelera dem med Azure Sentinel data för att skapa dina ML-modeller. 
 
### <a name="copy-and-install-the-related-packages"></a>Kopiera och installera relaterade paket

Kopiera BYO-ML-paketet från github Azure Sentinel databasen som nämns ovan till Din Databricks-miljö. Öppna sedan notebook-anteckningsböckerna och följ anvisningarna i anteckningsboken för att installera de bibliotek som krävs i dina kluster.

### <a name="model-training-and-scoring"></a>Modellträning och bedömning

Följ instruktionerna i de två anteckningsböckerna för att ändra konfigurationerna enligt din egen miljö och dina egna resurser, följ stegen för att träna och skapa din modell och schemalägg sedan modellen för att poängdöma inkommande åtkomstloggar för filresurser.

### <a name="write-results-to-log-analytics"></a>Skriva resultat till Log Analytics

När du har schemalagt bedömning kan du använda modulen i anteckningsboken för bedömning för att skriva resultat till Log Analytics-arbetsytan som är associerad med din Azure Sentinel instans.

### <a name="check-results-in-azure-sentinel"></a>Kontrollera resultaten i Azure Sentinel

Om du vill se dina poängade resultat tillsammans med relaterad logginformation går du tillbaka till Azure Sentinel portalen. I **Loggar** > anpassade loggar visas resultatet i  AnomalousResourceAccessResult_CL (eller ditt eget anpassade tabellnamn). Du kan använda dessa resultat för att förbättra dina undersökningar och jaktupplevelser.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="Loggar för avvikande resursåtkomst":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Skapa en anpassad analysregel med ML-resultat

När du har bekräftat att ML-resultaten finns i den anpassade loggtabellen och du är nöjd med poängens återgivning kan du skapa en identifiering baserat på resultaten. Gå till **Analytics** från Azure Sentinel portalen och [skapa en ny identifieringsregel.](tutorial-detect-threats-custom.md) Nedan visas ett exempel som visar den fråga som används för att skapa identifieringen.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="skapa anpassad analysregel för B Y O M L-identifieringar":::

### <a name="view-and-respond-to-incidents"></a>Visa och reagera på incidenter
När du har ställt in analysregeln baserat på ML-resultaten och det finns resultat över det tröskelvärde som du  anger i frågan, genereras en incident som visas på sidan Incidenter på Azure Sentinel. 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur Azure Sentinel använder BYO-ML-plattformen för att skapa eller importera egna maskininlärningsalgoritmer för att analysera data och identifiera hot.

- Se inlägg om maskininlärning och många andra relevanta ämnen i [Azure Sentinel blogg](https://aka.ms/azuresentinelblog).
