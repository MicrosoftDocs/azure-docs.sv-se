---
title: Ta med din egen ML till Azure Sentinel | Microsoft Docs
description: Den här artikeln beskriver hur du skapar och använder dina egna Machine Learning-algoritmer för data analys i Azure Sentinel.
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
ms.openlocfilehash: f8d795dcf64230140e1dd07e3f30ca3aa6825ab4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99807011"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Ta med din egen Machine Learning (ML) till Azure Sentinel

Machine Learning (ML) är ett av de största underfästningarna av Azure Sentinel och ett av de viktigaste attributen som informerar varandra. Azure Sentinel erbjuder ML i flera upplevelser: inbyggd med [fusions](fusion.md) korrelations motorn och Jupyter Notebooks och den nyligen tillgängliga versionen av build-your ml (BYOD ml). 

ML-identifierings modeller kan anpassas till enskilda miljöer och ändringar i användar beteende, för att minska antalet falska positiva identifieringar och identifiera hot som inte kan hittas med en traditionell metod. Många säkerhets organisationer förstår värdet av ML för säkerhet, men inte många av dem har lyxen av experter som har expert kunskaper i både säkerhet och ML. Vi har utformat ramverket som presenteras här för säkerhets organisationer och yrkes organisationer som kan växa med oss i sin ML-resa. Organisationer som är nya för ML eller utan nödvändig expertis kan få ett betydande skydds värde av Azure Sentinels inbyggda ML-funktioner.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="ramverk för Machine Learning":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Vad är en egen Machine Learning-plattform (BYOD-ML)?

För organisationer som har ML-resurser och vill bygga anpassade ML-modeller för sina unika affärs behov erbjuder vi **BYOD-ml-plattformen**. Plattformen använder [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/) miljö och Jupyter-anteckningsböcker för att skapa en ml-miljö. Den innehåller följande komponenter:

- ett BYOD-ML-paket, som innehåller bibliotek som hjälper dig att komma åt data och skicka tillbaka resultaten till Log Analytics (LA), så att du kan integrera resultaten med din identifiering, undersökning och jakt. 

- ML-algoritmer som du kan anpassa efter vissa säkerhets problem i din organisation. 

- exempel på bärbara datorer för att träna modellen och schemalägga modell poängen. 

Förutom allt detta kan du ta med dina egna ML-modeller och/eller din egen Spark-miljö för att integrera med Azure Sentinel.

Med BYOD-ML-plattformen kan du få en snabb start för att skapa egna ML-modeller: 

- Den bärbara datorn med exempel data hjälper dig att få praktisk erfarenhet från slut punkt till slut punkt utan att oroa dig för att hantera produktions data.

- Paketet som är integrerat med Spark-miljön minskar utmaningarna och friktionarna vid hantering av infrastrukturen.

- Biblioteken har stöd för data förflyttning. Antecknings böcker för utbildning och bedömning demonstrerar upplevelsen från slut punkt till slut punkt och fungerar som en mall som du kan anpassa efter din miljö.

### <a name="use-cases"></a>Användningsfall
 
BYOD-ML-plattformen och paketet minskar avsevärt den tid och ansträngning du behöver för att bygga dina egna ML-identifieringar och de utökar möjligheterna att åtgärda specifika säkerhets problem i Azure Sentinel. Plattformen stöder följande användnings fall:

**Träna en ml-algoritm för att få en anpassad modell:** Du kan ta en befintlig ML-algoritm (delas av Microsoft eller användarens community) och enkelt träna den på dina egna data och få en anpassad ML-modell som passar dina data och din miljö bättre.

**Ändra mallen för en ml-algoritm för att hämta en anpassad modell:** Du kan ändra mallen för en ML-algoritm (delas av Microsoft eller av användarens community) och träna den ändrade algoritmen på dina egna data, så att en anpassad modell kan anpassas efter ditt specifika problem.

**Skapa din egen modell:** Skapa en egen modell från grunden med hjälp av Azure Sentinels BYOD-plattform och-verktyg.

**Integrera din Databricks/Spark-miljö:** Integrera din befintliga Databricks/Spark-miljö i Azure Sentinel och Använd BYOD-ML-bibliotek och mallar för att bygga ML-modeller för sina unika situationer.

**Importera en egen ml-modell:** Du kan importera egna ML-modeller och använda BYOD-ML-plattformen och-verktygen för att integrera dem med Azure Sentinel.

**Dela en ml-algoritm:** Dela en ML-algoritm för communityn för att införa och anpassa.

**Använd ml för att ge SecOps:** Använd din egen anpassade ml-modell och resultat för jakt, identifiering, undersökning och svar.

Den här artikeln visar komponenterna i BYOD-ML-plattformen och hur du utnyttjar plattformen och algoritmen för avvikande resurs åtkomst för att leverera en anpassad ML-identifiering med Azure Sentinel.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark-miljö

[Apache Spark™](http://spark.apache.org/) ett stort steg framåt för att förenkla Big data genom att tillhandahålla ett enhetligt ramverk för att bygga datapipeliner. Azure Databricks tar detta ytterligare genom att tillhandahålla en moln plattform med Zero Management som skapats runt Spark. Vi rekommenderar att du använder Databricks för din BYOD-ML-plattform så att du kan fokusera på att hitta svar som gör en omedelbar inverkan på din verksamhet, i stället för att hantera data pipelines och plattforms problem.
Om du redan har Databricks eller någon annan Spark-miljö, och föredrar att använda den befintliga installationen, fungerar BYOD-ML-paketet också som det ska. 

## <a name="byo-ml-package"></a>BYOD – ML-paket

BYOD ML-paketet innehåller bästa praxis och forskning från Microsoft i den första delen av ML för säkerhet. I det här paketet innehåller vi följande lista över verktyg, mallar för bärbara datorer och algoritmer för säkerhets problem.

| Filnamn | Beskrivning |
| --------- | ----------- |
| azure_sentinel_utilities. WHL | Innehåller verktyg för att läsa blobbar från Azure och skriva till Log Analytics. |
| AnomalousRASampleData | Notebook visar användningen av avvikande resurs åtkomst modell i Azure Sentinel med genererad utbildning och testning av exempel data. |
| AnomalousRATraining. ipynb | Bärbar dator för att träna algoritmen, skapa och spara modeller. |
| AnomalousRAScoring. ipynb | Bärbar dator om du vill schemalägga modellen för körning, visualisera resultatet och skriv poängen tillbaka till Azure Sentinel. |
|

Mallen för den första ML-algoritmen som vi erbjuder är för [avvikande resurs åtkomst identifiering](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML). Den baseras på en algoritm för samordnad filtrering och tränas med Windows fil resurs åtkomst loggar (säkerhets händelser med händelse-ID 5140). Den viktigaste informationen du behöver för den här modellen i loggen är länkning av användare och resurser som används. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Exempel genom gång: identifiering av avvikande fil resurs åtkomst 

Nu när du har bekantat dig med de viktigaste komponenterna i BYOD-ML-plattformen finns här ett exempel på hur du kan använda plattformen och komponenterna för att leverera en anpassad ML-identifiering.

### <a name="setup-the-databricksspark-environment"></a>Konfigurera Databricks/Spark-miljön

Du måste konfigurera din egen Databricks-miljö om du inte redan har en. Instruktioner hittar du i [Databricks snabb starts](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) dokument.

### <a name="auto-export-instruction"></a>Automatiskt exportera-instruktion

Om du vill bygga anpassade ML-modeller baserat på dina egna data i Azure Sentinel måste du exportera dina data från Log Analytics till en Blob Storage-eller Event Hub-resurs, så att ML-modellen kan komma åt den från Databricks. Lär dig hur du matar [in data i Azure Sentinel](connect-data-sources.md).

I det här exemplet måste du ha dina utbildnings data för fil resursens åtkomst logg i Azure Blob Storage. Formatet på data dokumenteras i antecknings boken och biblioteken.

Du kan automatiskt exportera dina data från Log Analytics med hjälp av [kommando rads gränssnittet för Azure (CLI)](/cli/azure/monitor/log-analytics). 

Du måste tilldelas **deltagar** rollen i din Log Analytics-arbetsyta, ditt lagrings konto och din EventHub-resurs för att kunna köra kommandona. 

Här är en exempel uppsättning kommandon för att konfigurera automatisk export:

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

För anpassade data som inte stöds av Log Analytics Auto-export kan du använda Logic app eller andra lösningar för att flytta dina data. Du kan referera till [exporten av Log Analytics data till BLOB Store](https://www.borninthecloud.com/exporting-log-analytics-data-to-blob-store/?preview=true) -bloggen och skriptet.

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Korrelera med data utanför Azure Sentinel

Du kan också hämta data utanför Azure Sentinel till Blob Storage eller Event Hub och korrelera dem med Azure Sentinel-data för att bygga dina ML-modeller. 
 
### <a name="copy-and-install-the-related-packages"></a>Kopiera och installera de relaterade paketen

Kopiera BYOD-ML-paketet från Azure Sentinel GitHub-databasen som nämns ovan till din Databricks-miljö. Öppna sedan antecknings böckerna och följ anvisningarna i antecknings boken för att installera de bibliotek som krävs i klustren.

### <a name="model-training-and-scoring"></a>Modell utbildning och poängsättning

Följ anvisningarna i de två antecknings böckerna för att ändra konfigurationerna enligt din egen miljö och dina resurser, Följ stegen för att träna och bygga din modell och Schemalägg sedan modellen så att den visar inkommande loggar för fil resurs åtkomst.

### <a name="write-results-to-log-analytics"></a>Skriv resultat till Log Analytics

När du har fått den schemalagda poängen kan du använda modulen i antecknings antecknings boken för att skriva resultatet till arbets ytan Log Analytics som är kopplad till din Azure Sentinel-instans.

### <a name="check-results-in-azure-sentinel"></a>Kontrol lera resultat i Azure Sentinel

Gå tillbaka till Azure Sentinel-portalen för att se resultatet av dina resultat tillsammans med relaterad logg information. I **loggar** > anpassade loggar visas resultaten i **AnomalousResourceAccessResult_CLs** tabellen (eller ditt eget anpassade tabell namn). Du kan använda dessa resultat för att förbättra dina undersökningar och jakt upplevelser.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="avvikande resurs åtkomst loggar":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Bygg en anpassad analys regel med ML-resultat

När du har bekräftat att ML-resultaten finns i tabellen anpassade loggar och du är nöjd med resultatet av poängen kan du skapa en identifiering baserat på resultaten. Gå till **analyser** från Azure Sentinel-portalen och [skapa en ny identifierings regel](tutorial-detect-threats-custom.md). Nedan visas ett exempel som visar frågan som används för att skapa identifieringen.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="Skapa anpassad analys regel för B Y O M L-identifieringar":::

### <a name="view-and-respond-to-incidents"></a>Visa och svara på incidenter
När du har konfigurerat analys regeln baserat på ML-resultatet, om det finns resultat ovanför tröskelvärdet som du ställer in i frågan, genereras en incident som visas på sidan **incidenter** på Azure Sentinel. 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder Azure Sentinels BYOD-ML-plattform för att skapa eller importera dina egna Machine Learning-algoritmer för att analysera data och identifiera hot.

- Se inlägg om Machine Learning och många andra relevanta ämnen i [Azure Sentinel-bloggen](https://aka.ms/azuresentinelblog).