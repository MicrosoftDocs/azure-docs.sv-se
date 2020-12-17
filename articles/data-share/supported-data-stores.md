---
title: Datalager som stöds i Azure Data Share
description: Lär dig mer om de data lager som stöds för användning av Azure Data Share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: d45d9a29f671e66c6caba689f49e1748780545e0
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616753"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Datalager som stöds i Azure Data Share

Azure Data Share ger öppen och flexibel data delning, inklusive möjligheten att dela från och till olika data lager. Data leverantörer kan dela data från en typ av data lager och deras data konsumenter kan välja vilket data lager som ska ta emot data i. 

I den här artikeln får du lära dig mer om den omfattande uppsättningen Azure-datalager som stöds i Azure Data Share. Du kan också hitta information om kombinationer av data lager som kan utnyttjas av data leverantörer och data konsumenter. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Vilka data lager stöds i Azure Data Share? 

Tabellen nedan innehåller information om de data källor som stöds för Azure Data Share. 

| Datalager | Ögonblicks bild baserad delning (fullständig ögonblicks bild) | Ögonblicks bild baserad delning (stegvis ögonblicks bild) | Delning på plats 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (tidigare Azure SQL DW) |✓ | | |
| Dedikerad SQL-pool för Azure Synapse Analytics (arbets yta) |✓ | | |
| Azure-datautforskaren | | |✓ |

## <a name="data-store-support-matrix"></a>Support mat ris för data lager

Azure Data Share ger flexibilitet för data konsumenter när de bestämmer sig för ett data lager för att ta emot data i. Till exempel kan data som delas från Azure SQL Database tas emot i Azure Data Lake Store Gen2, Azure SQL Database eller Azure Synapse Analytics. Kunder kan välja vilket format de vill ta emot data i när de konfigurerar en mottagen data resurs. 

Tabellen nedan innehåller information om olika kombinationer och alternativ som data konsumenter har när de godkänner och konfigurerar data resursen. Mer information om hur du konfigurerar mappningar för data uppsättningar finns i [så här konfigurerar du mappningar för data uppsättningar](how-to-configure-mapping.md).

| Datalager | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics (tidigare Azure SQL DW) | Dedikerad SQL-pool för Azure Synapse Analytics (arbets yta) | Azure-datautforskaren
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Azure Blob Storage | ✓ || ✓ |||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ |||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ |||
| Azure SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Azure Synapse Analytics (tidigare Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Dedikerad SQL-pool för Azure Synapse Analytics (arbets yta) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Azure-datautforskaren ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Dela från ett lagrings konto
Azure data sharing stöder delning av filer, mappar och fil system från Azure Data Lake gen1 och Azure Data Lake Gen2. Det stöder också delning av blobbar, mappar och behållare från Azure Blob Storage. Endast Block-Blob stöds för närvarande. När fil system, behållare eller mappar delas i ögonblicks bilds-baserad delning, kan data konsumenten välja att göra en fullständig kopia av delnings data eller utnyttja en stegvis ögonblicks bilds kapacitet för att bara kopiera nya eller uppdaterade filer. En stegvis ögonblicks bild baseras på filernas senaste ändrings tid. Befintliga filer med samma namn kommer att skrivas över under ögonblicks bilden. Filen som togs bort från källan tas inte bort från målet. 

Mer information finns i [dela och ta emot data från Azure Blob Storage och Azure Data Lake Storage](how-to-share-from-storage.md) .

## <a name="share-from-a-sql-based-source"></a>Dela från en SQL-baserad källa
Azure Data Share stöder delning av både tabeller och vyer från Azure SQL Database och Azure Synapse Analytics (tidigare Azure SQL DW) och delning av tabeller från en dedikerad SQL-pool för Azure Synapse Analytics (arbets yta). Det finns för närvarande inte stöd för att dela från Azure Synapse Analytics (arbets yta) SQL-poolen. Data konsumenter kan välja att acceptera data till Azure Data Lake Storage Gen2 eller Azure-Blob Storage som CSV-eller Parquet-fil, samt till Azure SQL Database och Azure Synapse Analytics som tabeller.

När du accepterar data i Azure Data Lake Store Gen2 eller Azure Blob Storage skrivs innehållet i mål filen över om det redan finns en fullständig ögonblicks bild.
När data tas emot i tabellen och om mål tabellen inte redan finns, skapar Azure Data Share SQL-tabellen med käll schemat. Om det redan finns en mål tabell med samma namn kommer den att tas bort och skrivas över med den senaste fullständiga ögonblicks bilden. Stegvisa ögonblicks bilder stöds inte för närvarande.

Mer information finns i [dela och ta emot data från Azure SQL Database och Azure Synapse Analytics](how-to-share-from-sql.md) .

## <a name="share-from-azure-data-explorer"></a>Dela från Azure Data Explorer
Azure Data Share stöder möjligheten att dela databaser på plats från Azure Datautforskaren-kluster. Data leverantören kan dela på databas-eller kluster nivå. Vid delning på databas nivå kommer data konsumenten bara att kunna komma åt de enskilda databaserna som delas av data leverantören. Vid delning på kluster nivå kan data konsumenten komma åt alla databaser från leverantörens kluster, inklusive alla framtida databaser som skapats av data leverantören.

För att få åtkomst till delade databaser måste data konsumenten ha ett eget Azure Datautforskaren-kluster. Data konsumentens Azure Datautforskaren-kluster måste hitta i samma Azure-datacenter som data leverantörens Azure Datautforskaren-kluster. När du har upprättat relationen skapas en symbolisk länk mellan providern och användarens Azure Datautforskaren-kluster vid Azure-dataresursen. Data som matas in med batch-läge i Azure Datautforskaren-klustret visas i mål klustret inom några sekunder till några minuter.

Mer information finns i [dela och ta emot data från Azure datautforskaren](/azure/data-explorer/data-share) . 

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .
