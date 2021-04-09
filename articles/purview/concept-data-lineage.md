---
title: Data härkomst i Azure avdelningens kontroll (för hands version)
description: Beskriver koncepten för data härkomst.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 476355f41de5e0e6aaffdedea8947cab5221767a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200734"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Data härkomst i Azure avdelningens kontroll Data Catalog-klienten

Den här artikeln innehåller en översikt över data härkomst i Azure avdelningens kontroll Data Catalog. Den innehåller också information om hur data system kan integreras med katalogen för att avbilda härkomst av data. Avdelningens kontroll kan samla in härkomst för data i olika delar av organisationens datafastighet och på olika förberedelse nivåer, inklusive:

- Fullständigt RAW-data som mellanlagrats från olika plattformar
- Transformerade och för beredda data
- Data som används av visualiserings plattformar.

## <a name="use-cases"></a>Användningsfall

Data härkomst förstås i stort sett som livs cykeln som sträcker sig över data ursprunget och där det rör sig över tiden över data fastigheten. Den används för olika typer av bakåtkompatibla scenarier som fel sökning, spårning av rotor Saks data pipelines och fel sökning. Härkomst används också för data kvalitets analys, efterlevnad och "Vad händer om"-scenarier ofta kallas effekt analys. Härkomst representeras visuellt för att visa data som flyttas från källa till mål, inklusive hur data omvandlades. Med tanke på komplexiteten för de flesta företags data miljöer kan de här vyerna vara svåra att förstå utan att göra en konsolidering eller maskering av kring data punkter.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Härkomst-upplevelse i Azure avdelningens kontroll Data Catalog

Avdelningens kontroll Data Catalog kommer att ansluta till andra data bearbetnings-, lagrings-och analys system för att extrahera härkomst-information. Informationen kombineras för att representera en generisk, scenario-speciell härkomst-upplevelse i katalogen.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="End-end-härkomst visar data som kopierats från BLOB Store, vilket du kan använda för att Power BI instrument panelen":::

Din datafastighet kan omfatta system som utför data extrahering, transformering (ETL/ELT system), analyser och visualiserings system. Varje system samlar in omfattande statiska och operativa metadata som beskriver datans tillstånd och kvalitet inom systemets gränser. Målet med härkomst i en data katalog är att extrahera flytt-, omvandlings-och drifts-metadata från varje data system till lägsta möjliga kornig het.

Följande exempel är ett typiskt användnings fall av data som flyttas över flera system, där Data Catalog ansluter till var och en av systemen för härkomst.

- Data Factory kopierar data från lokal/obehandlad zon till en landnings zon i molnet. 
- Data bearbetnings system som Synapse, Databricks skulle bearbeta och transformera data från landnings zonen till den hanterade zonen med antecknings böcker.
- Ytterligare bearbetning av data till analytiska modeller för optimala prestanda och agg regering för frågor. 
- Data visualiserings system kommer att använda data uppsättningarna och bearbeta dem genom sin meta-modell för att skapa en BI-instrumentpanel, ML experiment och så vidare.

## <a name="lineage-granularity"></a>Härkomst granularitet

I det här avsnittet beskrivs detaljerna om vilken kornig het som härkomst-informationen samlas in av en data katalog. Den här granularitet kan variera beroende på vilka data system som är.

### <a name="entity-level-lineage-sources--process--targets"></a>Härkomst för enhets nivå: källa (er) > process > mål (ar) 

- Härkomst representeras som en graf, vanligt vis innehåller den käll-och mål enheter i data lagrings system som är anslutna till en process som anropas av ett beräknings system. 
- Data system ansluter till data katalogen för att generera och rapportera ett unikt objekt som refererar till det fysiska objektet i det underliggande data systemet, till exempel: SQL-lagrad procedur, Notebook-objekt och så vidare.
- Hög åter givning härkomst med ytterligare metadata som ägarskapet fångas för att Visa härkomst i ett läsligt format för käll & mål enheter. till exempel: härkomst på en Hive-tabell nivå i stället för partitioner eller filnivå.

### <a name="column-or-attribute-level-lineage"></a>Härkomst för kolumn-eller attributvärde

Identifiera attribut för en källentitet som används för att skapa eller härleda attribut i målentiteten. Namnet på källattributet kan behållas eller byta namn på ett mål. System som ADM kan göra en en kopia från en lokal miljö till molnet. Exempel: `Table1/ColumnA -> Table2/ColumnA`.

### <a name="process-execution-status"></a>Process körnings status

För att stödja rotor Saks analys och data kvalitets scenarier fångar vi körnings status för jobben i data bearbetnings system. Detta krav har inget att göra med att ersätta övervakningsfunktionerna i andra data bearbetnings system, varken målet är att ersätta dem. 

## <a name="summary"></a>Sammanfattning

Härkomst är en viktig funktion i avdelningens kontroll-Data Catalog som stöder scenarier för kvalitet, förtroende och granskning. Målet med en data katalog är att bygga ett robust ramverk där alla data system i din miljö kan ansluta och rapportera härkomst. När metadata är tillgängliga kan data katalogen samla de metadata som tillhandahålls av data systemen för att hantera data styrnings användnings fall.

## <a name="next-steps"></a>Nästa steg

* [Snabb start: skapa ett Azure avdelningens kontroll-konto i Azure Portal](create-catalog-portal.md)
* [Snabb start: skapa ett Azure avdelningens kontroll-konto med Azure PowerShell/Azure CLI](create-catalog-powershell.md)
* [Snabb start: Använd avdelningens kontroll Studio](use-purview-studio.md)
