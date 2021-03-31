---
title: Parameterisera länkade tjänster i Azure Data Factory
description: Lär dig hur du Parameterisera länkade tjänster i Azure Data Factory och skickar dynamiska värden vid körning.
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/18/2021
author: dcstwh
ms.author: weetok
ms.openlocfilehash: df26b77f37100ae41b26c013c57cccbfa0d7e205
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595591"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parameterisera länkade tjänster i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nu kan du Parameterisera en länkad tjänst och skicka dynamiska värden vid körning. Om du till exempel vill ansluta till olika databaser på samma logiska SQL Server kan du nu Parameterisera databas namnet i den länkade tjänst definitionen. Detta gör att du inte behöver skapa en länkad tjänst för varje databas på den logiska SQL-servern. Du kan också Parameterisera andra egenskaper i den länkade tjänst definitionen, till exempel *användar namn.*

Du kan använda Data Factory gränssnittet i Azure Portal eller ett programmerings gränssnitt för att Parameterisera länkade tjänster.

> [!TIP]
> Vi rekommenderar att du inte Parameterisera lösen ord eller hemligheter. Lagra alla anslutnings strängar i Azure Key Vault i stället och Parameterisera det *hemliga namnet*.

> [!Note]
> Det finns öppen bugg för användning av "-" i parameter namn, vi rekommenderar att du använder namn utan "-" förrän felet har åtgärd ATS.

En introduktion till sju minuter och demonstration av den här funktionen finns på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>Länkade tjänst typer som stöds

Du kan Parameterisera alla typer av länkade tjänster.
När du redigerar en länkad tjänst i användar gränssnittet tillhandahåller Data Factory inbyggd Parameterisering-upplevelse för följande typer av länkade tjänster. I bladet skapa/redigera länkade tjänster kan du hitta alternativ för nya parametrar och lägga till dynamiskt innehåll.

- Amazon Redshift
- Amazon S3
- Azure Blob Storage
- Azure Cosmos DB (SQL API)
- Azure Data Lake Storage Gen2
- Azure Database for MySQL
- Azure Databricks
- Azure Key Vault
- Azure SQL Database
- Hanterad Azure SQL-instans
- Azure Synapse Analytics 
- Azure Table Storage
- Generisk HTTP
- REST (allmän)
- MySQL
- Oracle
- SQL Server

För andra länkade tjänst typer som inte finns med i listan ovan kan du Parameterisera den länkade tjänsten genom att redigera JSON-gränssnittet:

- I den länkade tjänsten skapa/redigera bladet > expandera "Avancerad" längst ned-> Markera kryss rutan Ange dynamiskt innehåll i JSON-format – > ange den länkade tjänstens JSON-nyttolast. 
- Eller, efter att du har skapat en länkad tjänst utan Parameterisering, i [Management Hub](author-visually.md#management-hub) -> länkade tjänster – > hitta den länkade tjänsten > Klicka på "kod" (knapp {} ) för att redigera JSON. 

Referera till avsnittet [JSON-exempel](#json) att lägga till ` parameters` för att definiera parametrar och referera till parametern med ` @{linkedService().paraName} ` .

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

![Lägg till dynamiskt innehåll i den länkade tjänst definitionen](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Skapa en ny parameter](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
