---
title: Beräkna BLOB-antal och storlek med Azure Storage inventering
description: Lär dig hur du beräknar antalet och den totala storleken på blobbar per behållare.
services: storage
author: twooley
ms.author: twooley
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: e752d40ce2f237c2ab08bac2e71133cd06ec40e4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277189"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Beräkna antal blobar och Total storlek per behållare med hjälp av Azure Storage inventering

I den här artikeln används Azure Blob Storage Inventory-funktionen och Azure-Synapse för att beräkna BLOB-antalet och den totala storleken på blobbar per behållare. Dessa värden är användbara när du optimerar BLOB-användning per behållare.

BLOB-metadata ingår inte i den här metoden. Azure Blob Storage Inventory-funktionen använder [list-blobbar](/rest/api/storageservices/list-blobs) REST API med standard parametrar. I så fall har exemplet inte stöd för ögonblicks bilder, $-behållare och så vidare.

> [!IMPORTANT]
> BLOB-inventering är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner av juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släpps till allmän tillgänglighet.

## <a name="enable-inventory-reports"></a>Aktivera inventerings rapporter

Det första steget i den här metoden är att [Aktivera inventerings rapporter](blob-inventory.md#enable-inventory-reports) på ditt lagrings konto. Du kan behöva vänta upp till 24 timmar efter att du aktiverat inventerings rapporterna för att den första rapporten ska genereras.

När du har en inventerings rapport som ska analyseras kan du ge dig till gång till BLOB-Läs behörighet till behållaren där rapportens CSV-fil finns.

1. Navigera till behållaren med lager CSV-rapport filen.
1. Välj **Access Control (IAM)** och **Lägg sedan till roll tilldelningar**

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="Välj Lägg till roll tilldelningar":::

1. Välj **Storage BLOB data Reader** i list rutan **roll** .

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="Lägg till rollen Storage BLOB data Reader i list rutan":::

1. Ange e-postadressen för det konto som du använder för att köra rapporten i fältet **Välj** .

## <a name="create-an-azure-synapse-workspace"></a>Skapa en Azure Synapse-arbetsyta

Skapa sedan en [Azure Synapse-arbetsyta](../../synapse-analytics/get-started-create-workspace.md) där du kan köra en SQL-fråga för att rapportera inventerings resultatet.

## <a name="create-the-sql-query"></a>Skapa SQL-frågan

Utför följande steg när du har skapat din Azure dataSynapses-arbetsyta.

1. Navigera till [https://web.azuresynapse.net](https://web.azuresynapse.net) .
1. Välj fliken **utveckla** på den vänstra kanten.
1. Välj det stora plus tecknet (+) för att lägga till ett objekt.
1. Välj **SQL-skript**.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="Välj SQL-skript för att skapa en ny fråga":::

## <a name="run-the-sql-query"></a>Kör SQL-frågan

1. Lägg till följande SQL-fråga i din Azure Synapse-arbetsyta för att [läsa lager-CSV-filen](../../synapse-analytics/sql/query-single-csv-file.md#read-a-csv-file).

    För `bulk` parametern använder du webb adressen för den CSV-fil för inventerings rapport som du vill analysera.

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. Namnge SQL-frågan i rutan egenskaper till höger.

1. Publicera din SQL-fråga genom att trycka på CTRL + S eller genom att välja knappen **publicera alla** .

1. Klicka på knappen **Kör** för att köra SQL-frågan. Antalet blobar och Total storlek per behållare rapporteras i **resultat** fönstret.

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="Utdata från att köra skriptet för att beräkna BLOB-antal och Total storlek.":::

## <a name="next-steps"></a>Nästa steg

- [Använd Azure Storage BLOB Inventory för att hantera BLOB-data](blob-inventory.md)
- [Beräkna den totala faktureringsstorleken på en blob-container](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)