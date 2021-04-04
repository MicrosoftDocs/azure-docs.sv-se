---
title: Datalager som stöds i Azure Data Share
description: Lär dig mer om data lager som stöds för användning i Azure Data Share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 852c44f5edc5c0b0f5f655f63ab040927bd9bc7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963687"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Datalager som stöds i Azure Data Share

Azure Data Share ger öppen och flexibel data delning, inklusive möjligheten att dela från och till olika data lager. Data leverantörer kan dela data från en typ av data lager och data konsumenter kan välja ett data lager för att ta emot data. 

I den här artikeln får du lära dig mer om den omfattande uppsättningen Azure-datalager som stöds av Azure Data Share. Du får också lära dig hur data leverantörer och data konsumenter kan kombinera olika data lager. 

## <a name="supported-data-stores"></a>Datalager som stöds 

I följande tabell förklaras de data lager som stöds av Azure Data Share. 

| Datalager | Delning baserat på fullständiga ögonblicks bilder | Delning baserat på stegvisa ögonblicks bilder | Delning på plats 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) |✓ | | |
| Dedikerad SQL-pool för Azure Synapse Analytics (arbets yta) |✓ | | |
| Öppna Azure-datautforskaren | | |✓ |

## <a name="data-store-support-matrix"></a>Support mat ris för data lager

Med Azure Data Share kan data konsumenter välja ett data lager för att acceptera data. Till exempel kan data som delas från Azure SQL Database tas emot i Azure Data Lake Storage Gen2, Azure SQL Database eller Azure Synapse Analytics. När kunder konfigurerar en mottagar data resurs kan de välja format för att ta emot data. 

I följande tabell förklaras de kombinationer och alternativ som data konsumenter kan välja när de accepterar och konfigurerar en data resurs. Mer information finns i [Konfigurera en mappning av data uppsättningar](how-to-configure-mapping.md).

| Datalager | Blob Storage | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL Database | Synapse-analys (tidigare SQL Data Warehouse) | Dedikerad SQL-pool för Synapse Analytics (arbets yta) | Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Storage | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse-analys (tidigare SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Dedikerad SQL-pool för Synapse Analytics (arbets yta) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Dela från ett lagrings konto
Azure Data Share stöder delning av filer, mappar och fil system från Azure Data Lake Storage Gen1 och Azure Data Lake Storage Gen2. Det stöder också delning av blobbar, mappar och behållare från Azure Blob Storage. Endast block-blobbar stöds för närvarande. 

När fil system, behållare eller mappar delas i ögonblicks bilds-baserad delning, kan data konsumenter välja att göra en fullständig kopia av delade data. Eller så kan de använda den stegvisa ögonblicks bilds funktionen för att kopiera endast nya filer eller uppdaterade filer. 

En stegvis ögonblicks bild baseras på filens senaste ändrings tid. Befintliga filer som har samma namn som filer i de mottagna data skrivs över i en ögonblicks bild. Filer som tas bort från källan tas inte bort från målet. 

Mer information finns i [dela och ta emot data från Azure Blob Storage och Azure Data Lake Storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Dela från en SQL-baserad källa
Azure Data Share stöder delning av både tabeller och vyer från Azure SQL Database och Azure Synapse Analytics (tidigare Azure SQL Data Warehouse). Det stöder delning av tabeller från en dedikerad SQL-pool för Azure Synapse Analytics (arbets yta). Det finns för närvarande inte stöd för att dela från Azure Synapse Analytics (arbets yta) SQL-poolen. 

Data konsumenter kan välja att acceptera data till Azure Data Lake Storage Gen2 eller Azure Blob Storage som en CSV-fil eller Parquet-fil. De kan också acceptera data som tabeller i Azure SQL Database och Azure Synapse Analytics.

När konsumenterna accepterar data till Azure Data Lake Storage Gen2 eller Azure Blob Storage skrivs innehållet i målfilen över av alla ögonblicks bilder om filen redan finns. När data tas emot i en tabell och mål tabellen inte redan finns skapar Azure Data Share en SQL-tabell med hjälp av käll schemat. Om det redan finns en mål tabell och den har samma namn, tas den bort och skrivs över med den senaste fullständiga ögonblicks bilden. Stegvisa ögonblicks bilder stöds inte för närvarande.

Mer information finns i [dela och ta emot data från Azure SQL Database och Azure Synapse Analytics](how-to-share-from-sql.md).

## <a name="share-from-data-explorer"></a>Dela från Datautforskaren
Azure Data Share stöder möjligheten att dela databaser på plats från Azure Datautforskaren-kluster. En DataProvider kan dela på samma nivå som databasen eller klustret. 

När data delas på databas nivå kan data konsumenter bara komma åt de databaser som dataprovidern har delat. När en provider delar data på kluster nivå kan data konsumenter komma åt alla databaser från leverantörens kluster, inklusive alla framtida databaser som data leverantören skapar.

För att få åtkomst till delade databaser behöver data konsumenter sina egna Azure Datautforskaren-kluster. Deras kluster måste finnas i samma Azure-datacenter som data leverantörens Azure Datautforskaren-kluster. 

När en delnings relation upprättas skapar Azure-dataresursen en symbolisk länk mellan providerns kluster och konsument klustret. Data som matas in i käll klustret med hjälp av batch-läget visas i mål klustret inom några minuter.

Mer information finns i [dela och ta emot data från Azure datautforskaren](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .
