---
title: Masskopiera från filer till databas
description: Lär dig hur du använder en lösnings mall för att kopiera data i bulk från Azure Data Lake Storage Gen2 till Azure Synapse Analytics/Azure SQL Database.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 5f68c7b33a26bc8c01f3f413943351ff8d61b380
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376129"
---
# <a name="bulk-copy-from-files-to-database"></a>Masskopiera från filer till databas

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver en lösnings mall som du kan använda för att kopiera data i bulk från Azure Data Lake Storage Gen2 till Azure Synapse Analytics/Azure SQL Database.

## <a name="about-this-solution-template"></a>Om den här lösnings mal len

Den här mallen hämtar filer från Azure Data Lake Storage Gen2 källa. Sedan upprepas den över varje fil i källan och filen kopieras till mål data lagret. 

Den här mallen stöder för närvarande endast kopiering av data i **DelimitedText** -format. Filer i andra data format kan också hämtas från käll data lagret, men de kan inte kopieras till mål data lagret.  

Mallen innehåller tre aktiviteter:
- **Hämta metadata** -aktivitet hämtar filer från Azure Data Lake Storage Gen2 och skickar dem *till efterföljande aktiviteter* .
- Med en **förgrunds** aktivitet hämtas filer från aktiviteten *Hämta metadata* och upprepas varje fil till *kopierings* aktiviteten.
- **Kopierings** aktiviteten finns i en *förgrunds* aktivitet för att kopiera varje fil från käll data lagret till mål data lagret.

Mallen definierar följande två parametrar:
- *SourceContainer* är sökvägen till rot behållaren där data kopieras från i Azure Data Lake Storage Gen2. 
- *SourceDirectory* är katalog Sök vägen under rot behållaren där data kopieras från i din Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösnings mal len

1. Gå till mallen **Mass kopiering från filer till databas** . Skapa en **ny** anslutning till käll Gen2 Store. Tänk på att "GetMetadataDataset" och "SourceDataset" är referenser till samma anslutning av käll fil arkivet.

    ![Skapa en ny anslutning till käll data lagret](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Skapa en **ny** anslutning till det data lager för mottagare som du kopierar data till.

    ![Skapa en ny anslutning till data lagret för mottagare](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Du ser en pipeline som skapats på det sätt som visas i följande exempel:

    ![Granska pipelinen](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Om du väljer **Azure Synapse Analytics** som data mål i **steg 2** ovan, måste du ange en anslutning till Azure Blob Storage för mellanlagring, vilket krävs av Azure Synapse Analytics PolyBase. När följande skärm bild visas kommer mallen automatiskt att generera en *lagrings Sök väg* för blob-lagringen. Kontrol lera om behållaren har skapats efter att pipelinen har körts.
        
    ![PolyBase-inställning](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Välj **Felsök**, ange **parametrarna** och välj sedan **Slutför**.

    ![Klicka på * * Felsök * *](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. När pipeline-körningen har slutförts visas resultat som liknar följande exempel:

    ![Granska resultatet](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)