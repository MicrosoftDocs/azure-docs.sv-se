---
title: Skillnader från Azure Data Factory
description: Lär dig hur data integrerings funktionerna i Azure Synapse Analytics skiljer sig från Azure Data Factory
services: synapse-analytics
author: shirleywangmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: shwang
ms.reviewer: jrasnick
ms.openlocfilehash: db5d05e1a211ce14926ee4031054669fff5110d9
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930219"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Data integrering i Azure Synapse Analytics kontra Azure Data Factory

I Azure Synapse Analytics baseras data integrerings funktionerna som Synapse-pipeliner och data flöden på Azure Data Factory. Mer information finns i [Vad är Azure Data Factory](../../data-factory/introduction.md). Nästan alla funktioner är identiska eller liknande och dokumentationen delas mellan de två tjänsterna. I den här artikeln beskrivs och identifieras de aktuella skillnaderna mellan Azure Data Factory och Azure-Synapse.

Om du vill se om en Azure Data Factory funktion eller artikel gäller för Azure-Synapse kontrollerar du monikern överst i artikeln.

![Gäller för moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "Gäller för moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Funktioner i Azure Data Factory som inte har planer ATS för Azure-Synapse

Följande funktioner är tillgängliga i Azure Data Factory, men planeras inte för Azure-Synapse.

* **Liv-och Skift SSIS-paket:** I Azure Data Factory har du möjlighet att lyfta och byta SSIS-paket med hjälp av SSIS integration Runtime. Både SSIS integration Runtime och aktiviteten kör SSIS-paket är inte tillgängliga i Synapse-arbetsytor. 
* **Tid till Live:** Time to Live är en inställning i Azure integration runtime som gör det möjligt för Spark-klustret i att mappa data flöden att hålla sig *varm* under en viss tid efter att ett data flöde har slutförts. Den här funktionen är inte tillgänglig i Synapse-arbetsytor.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Azure Synapse-funktioner stöds inte i Azure Data Factory

Följande funktioner är tillgängliga i Azure Synapse, men planeras inte för Azure Data Factory.

* **Spark-jobb övervakning av mappnings data flöden:** I Synapse finns Spark-motorn i användarens prenumeration så att användarna kan se detaljerade Spark-loggar. I Azure Data Factory sker jobb körningen i ett Azure Data Factory hanterat Spark-kluster och denna information är inte tillgänglig. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory funktioner som beter sig annorlunda i Synapse

Följande funktioner fungerar antingen annorlunda eller finns inte i Azure-Synapse. 

* **Datatransformering data flöden:** Datatransformering Data Flow-aktiviteten är bara tillgänglig i Azure Data Factory för tillfället.
* **Galleriet för lösnings mal len:** I Azure Data Factory kan användarna hitta pipeline-mallar i lösnings galleriet. I Synapse-arbetsytor innehåller kunskaps centret en annan uppsättning mallar tillsammans med ytterligare data uppsättningar och SQL-skript. 
* **Integrering med Azure Monitor:** Synapse-arbetsytor integreras inte med Azure Monitor som Azure Data Factory gör.
* **Konfiguration av hybrid integrerings körning:** I en Synapse-arbetsyta kan en användare inte ha både en hanterad VNet IR och en Azure IR. Den här funktionen stöds i Azure Data Factory.
* **Integration runtime-delning:** Integration runtime med egen värd kan inte delas mellan Synapse-arbetsytor. Den här funktionen stöds i Azure Data Factory.
* **Integrerings körningar mellan regioner för data flöden:** Data flöden kan inte köras på integrerings körningar i olika regioner än en Synapse-arbetsyta. Den här funktionen stöds i Azure Data Factory.

## <a name="next-steps"></a>Nästa steg

Kom igång med data integrering på din Synapse-arbetsyta genom att lära dig att mata [in data i ett Azure Data Lake Storage Gen2-konto](data-integration-data-lake.md).
