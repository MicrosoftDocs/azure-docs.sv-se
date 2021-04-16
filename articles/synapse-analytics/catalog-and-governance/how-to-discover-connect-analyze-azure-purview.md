---
title: Identifiera, ansluta och utforska data i Synapse med Azure Purview
description: Guide om hur du identifierar data, ansluter dem och utforskar dem i Synapse
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: f98507fa72f4503700bf39393063dd1ecc650e91
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567901"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Identifiera, ansluta och utforska data i Synapse med Azure Purview 

I det här dokumentet får du lära dig vilken typ av interaktioner du kan utföra när du registrerar ett Azure Purview-konto i Synapse. 

## <a name="prerequisites"></a>Förutsättningar 

- [Azure Purview-konto](../../purview/create-catalog-portal.md) 
- [Synapse-arbetsyta](../quickstart-create-workspace.md) 
- [Ansluta ett Azure Purview-konto till Synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Använda Azure Purview i Synapse 

Om du vill använda Azure Purview i Synapse måste du ha åtkomst till det Purview-kontot. Synapse passerar genom din Purview-behörighet. Om du till exempel har rollen som intendent kan du redigera metadata som skannas av Azure Purview. 

### <a name="data-discovery-search-datasets"></a>Dataidentifiering: sök efter datauppsättningar 

Om du vill identifiera data som registrerats och genomsökts av Azure Purview kan du använda sökfältet längst upp i mitten av Synapse-arbetsytan. Se till att du väljer Azure Purview för att söka efter alla dina organisationsdata. 

[![Sök efter Azure Purview-tillgångar](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Azure Purview Actions 

Här är en lista över De Azure Purview-funktioner som är tillgängliga i Synapse: 
- **Översikt** över metadata 
- Visa och **redigera scheman** för metadata med klassificeringar, ordlista, datatyper och beskrivningar 
- Visa **härledning** för att förstå beroenden och analysera påverkan. Mer information om finns [i härledning](../../purview/catalog-lineage-user-guide.md)
- Visa och redigera **kontakter för** att veta vem som är ägare eller expert på en datauppsättning 
- **Relaterat** till att förstå hierarkiska beroenden för en specifik datauppsättning. Den här upplevelsen är användbar för att bläddra i datahierarkin.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Åtgärder som du kan utföra över datauppsättningar med Synapse-resurser 

### <a name="connect-data-to-synapse"></a>Ansluta data till Synapse 

- Du kan skapa en **ny länkad tjänst** till Synapse. Den åtgärden krävs för att kopiera data till Synapse eller ha dem i din datahubb (för datakällor som stöds som ADLSg2) 
- För objekt som filer, mappar eller tabeller kan du direkt skapa en ny **integrationsdatauppsättning** och utnyttja en befintlig länkad tjänst om den redan har skapats 

Vi kan ännu inte dra slutsatsen om det finns en befintlig länkad tjänst eller integrationsdatauppsättning. 

###  <a name="develop-in-synapse"></a>Utveckla i Synapse 

Det finns tre åtgärder som du kan utföra: **Nytt SQL-skript,** **Ny notebook-dator** **och ny Dataflöde**. 

Med **Nytt SQL-skript** kan du, beroende på typ av stöd,: 
- Visa de 100 översta raderna för att förstå dataformen. 
- Skapa en extern tabell från Synapse SQL databas 
- Läsa in data i en Synapse SQL databas 
 
Med **Ny notebook-dator** kan du: 
- Läsa in data till en Spark DataFrame 
- Skapa en Spark-tabell (om du gör det i Parquet-format skapas även en serverlös SQL-pooltabell). 
 
Med **Nytt dataflöde** kan du skapa en integreringsdatauppsättning som kan användas som källa i en dataflödespipeline. Dataflödet är en utvecklarfunktion utan kod för att utföra datatransformering. Mer information om hur [du använder dataflöden i Synapse](../quickstart-data-flow.md).

##  <a name="nextsteps"></a>Nästa steg 

- [Registrera och skanna Azure Synapse i Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)
- [Söka efter data i Azure Purview Data Catalog](../../purview/how-to-search-catalog.md)
