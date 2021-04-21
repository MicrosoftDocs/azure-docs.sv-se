---
title: Datalager som stöds i Azure Data Share
description: Lär dig mer om de datalager som stöds för användning i Azure Data Share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: def73d137f3cc2c79ae8417995ec6bdf6c519b7d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812632"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Datalager som stöds i Azure Data Share

Azure Data Share ger öppen och flexibel datadelning, inklusive möjligheten att dela från och till olika datalager. Dataleverantörer kan dela data från en typ av datalager, och datakonsumenter kan välja ett datalager för att ta emot data. 

I den här artikeln får du lära dig mer om den omfattande uppsättningen Azure-datalager som Azure Data Share stöder. Du får också lära dig mer om hur dataleverantörer och datakonsumenter kan kombinera olika datalager. 

## <a name="supported-data-stores"></a>Datalager som stöds 

I följande tabell beskrivs de datalager som Azure Data Share stöder. 

| Datalager | Delning baserat på fullständiga ögonblicksbilder | Delning baserat på inkrementella ögonblicksbilder | Delning på plats 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) |✓ | | |
| Azure Synapse Analytics (arbetsyta) dedikerad SQL-pool |✓ | | |
| Öppna Azure-datautforskaren | | |✓ |

## <a name="data-store-support-matrix"></a>Stödmatris för datalager

Azure Data Share datakonsumenter välja ett datalager för att acceptera data. Till exempel kan data som delas från Azure SQL Database tas emot till Azure Data Lake Storage Gen2, Azure SQL Database eller Azure Synapse Analytics. När kunder ställer in en mottagande dataresurs kan de välja vilket format som ska ta emot data. 

I följande tabell beskrivs de kombinationer och alternativ som datakonsumenter kan välja när de accepterar och konfigurerar en dataresurs. Mer information finns i Konfigurera [en datauppsättningsmappning.](how-to-configure-mapping.md)

| Datalager | Blob Storage | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL Database | Synapse Analytics (tidigare SQL Data Warehouse) | Synapse Analytics (arbetsyta) dedikerad SQL-pool | Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Storage | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (tidigare SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (arbetsyta) dedikerad SQL-pool | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Dela från ett lagringskonto
Azure Data Share stöder delning av filer, mappar och filsystem från Azure Data Lake Storage Gen1 och Azure Data Lake Storage Gen2. Den stöder också delning av blobar, mappar och containrar från Azure Blob Storage. Du kan dela block-, tilläggs- eller sidblobar och de tas emot som blockblobar.

När filsystem, containrar eller mappar delas i ögonblicksbildbaserad delning kan datakonsumenter välja att göra en fullständig kopia av delade data. Eller så kan de använda funktionen för inkrementell ögonblicksbild för att endast kopiera nya filer eller uppdaterade filer. 

En inkrementell ögonblicksbild baseras på den tid då filerna senast ändrades. Befintliga filer med samma namn som filer i mottagna data skrivs över i en ögonblicksbild. Filer som tas bort från källan tas inte bort på målet. 

Mer information finns i [Dela och ta emot data från Azure Blob Storage och Azure Data Lake Storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Dela från en SQL-baserad källa
Azure Data Share stöder delning av både tabeller och vyer från Azure SQL Database och Azure Synapse Analytics (tidigare Azure SQL Data Warehouse). Den stöder delning av tabeller från en Azure Synapse Analytics (arbetsyta) dedikerad SQL-pool. Delning från Azure Synapse Analytics (arbetsyta) serverlös SQL-pool stöds inte för närvarande. 

Datakonsumenter kan välja att acceptera data i Azure Data Lake Storage Gen2 eller Azure Blob Storage som en CSV-fil eller parquet-fil. De kan också acceptera data som tabeller i Azure SQL Database och Azure Synapse Analytics.

När konsumenter tar emot data Azure Data Lake Storage Gen2 eller Azure Blob Storage skriver fullständiga ögonblicksbilder över innehållet i målfilen om filen redan finns. När data tas emot i en tabell och måltabellen inte redan finns skapar Azure Data Share SQL-tabell med hjälp av källschemat. Om en måltabell redan finns och den har samma namn, ignoreras den och skrivs över med den senaste fullständiga ögonblicksbilden. Inkrementella ögonblicksbilder stöds inte för närvarande.

Mer information finns i Dela [och ta emot data från Azure SQL Database och Azure Synapse Analytics](how-to-share-from-sql.md).

## <a name="share-from-data-explorer"></a>Dela från Datautforskaren
Azure Data Share har stöd för möjligheten att dela databaser på plats Azure Data Explorer kluster. En dataleverantör kan dela på databas- eller klusternivå. 

När data delas på databasnivå kan datakonsumenter endast komma åt de databaser som dataleverantören delade. När en provider delar data på klusternivå kan datakonsumenter komma åt alla databaser från providerns kluster, inklusive eventuella framtida databaser som dataleverantören skapar.

För att få åtkomst till delade databaser behöver datakonsumenterna sina Azure Data Explorer kluster. Deras kluster måste finnas i samma Azure-datacenter som dataleverantörens Azure Data Explorer kluster. 

När en delningsrelation upprättas Azure Data Share en symbolisk länk mellan providerns kluster och konsumentens kluster. Data som matas in i källklustret med hjälp av batchläge visas i målklustret inom några minuter.

Mer information finns i Dela [och ta emot data från Azure Data Explorer](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till [självstudien Dela dina data.](share-your-data.md)
