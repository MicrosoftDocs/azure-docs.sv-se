---
title: Upptäck, Anslut och utforska data i Synapse med Azure avdelningens kontroll
description: Guide om hur du identifierar data, ansluter dem och utforskar dem i Synapse
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: 79090bfbf08cde3f18b1ca734b8af22d16e7e0ea
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628670"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Upptäck, Anslut och utforska data i Synapse med Azure avdelningens kontroll 

I det här dokumentet får du lära dig vilken typ av interaktioner som du kan utföra när du registrerar ett Azure avdelningens kontroll-konto i Synapse. 

## <a name="prerequisites"></a>Förutsättningar 

- [Azure avdelningens kontroll-konto](../../purview/create-catalog-portal.md) 
- [Synapse-arbetsyta](../quickstart-create-workspace.md) 
- [Anslut ett Azure avdelningens kontroll-konto till Synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Använda Azure-avdelningens kontroll i Synapse 

Med Azure-avdelningens kontroll i Synapse måste du ha åtkomst till det avdelningens kontroll-kontot. Synapse passerar din avdelningens kontroll-behörighet. Om du till exempel har en curator-behörighet kan du redigera metadata som genomsöks av Azure avdelningens kontroll. 

### <a name="data-discovery-search-datasets"></a>Data identifiering: Sök data uppsättningar 

Om du vill identifiera data som registrerats och genomsökts av Azure avdelningens kontroll kan du använda Sök fältet längst upp i mitten av Synapse-arbetsytan. Se till att du väljer Azure-avdelningens kontroll för att söka efter alla organisations data. 

[![Sök efter Azure avdelningens kontroll-tillgångar](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Azure avdelningens kontroll-åtgärder 

Här är en lista över de Azure avdelningens kontroll-funktioner som är tillgängliga i Synapse: 
- **Översikt** över metadata 
- Visa och redigera **schemat** för metadata med klassificeringar, ord listans villkor, data typer och beskrivningar 
- Visa **härkomst** för att förstå beroenden och gör konsekvens analys. Mer information om finns i [härkomst](../../purview/catalog-lineage-user-guide.md)
- Visa och redigera **kontakter** för att veta vem som är en ägare eller expert på en data uppsättning 
- **Relaterade** till förståelse av hierarkiska beroenden för en speciell data uppsättning. Den här upplevelsen är användbar för att bläddra igenom Datahierarkin.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Åtgärder som du kan utföra över data uppsättningar med Synapse-resurser 

### <a name="connect-data-to-synapse"></a>Anslut data till Synapse 

- Du kan skapa en **ny länkad tjänst** till Synapse. Åtgärden kommer att krävas för att kopiera data till Synapse eller ha dem i din data hubb (för data källor som stöds som ADLSg2) 
- För objekt som filer, mappar eller tabeller kan du direkt skapa en **ny integrerings data uppsättning** och utnyttja en befintlig länkad tjänst om den redan har skapats 

Vi kan ännu inte härleda om det finns en befintlig länkad tjänst eller integrations data uppsättning. 

###  <a name="develop-in-synapse"></a>Utveckla i Synapse 

Det finns tre åtgärder som du kan utföra: **nytt SQL-skript**, **ny antecknings bok** och **nytt data flöde**. 

Med det **nya SQL-skriptet**, beroende på typen av support, kan du: 
- Visa de översta 100 raderna för att förstå formen på data. 
- Skapa en extern tabell från Synapse SQL Database 
- Läs in data i en Synapse SQL-databas 
 
Med **ny antecknings bok** kan du: 
- Läs in data i en spark-DataFrame 
- Skapa en spark-tabell (om du gör det över Parquet-formatet skapas även en server lös SQL-adresspool). 
 
Med **nytt data flöde** kan du skapa en integrations data uppsättning som kan användas som en källa i en pipeline för data flöde. Data flödet är en programmerings funktion utan kod för att utföra datatransformering. Mer information om hur du [använder data flöden i Synapse](../quickstart-data-flow.md).

##  <a name="nextsteps"></a>Nästa steg 

- [Registrera och skanna Azure Synapse-tillgångar i Azure avdelningens kontroll](../../purview/register-scan-azure-synapse-analytics.md)
- [Söka efter data i Azure avdelningens kontroll Data Catalog](../../purview/how-to-search-catalog.md)
