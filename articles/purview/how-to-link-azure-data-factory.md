---
title: Anslut till Azure Data Factory
description: Den här artikeln beskriver hur du ansluter Azure Data Factory och Azure avdelningens kontroll för att spåra data härkomst.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/22/2020
ms.openlocfilehash: 0e993cb1e53645f7081a20fc6a2785b8cfef1cce
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954198"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Så här ansluter du Azure Data Factory och Azure-avdelningens kontroll

Det här dokumentet beskriver de steg som krävs för att ansluta ett Azure Data Factory-konto till ett Azure avdelningens kontroll-konto för att spåra data härkomst. Dokumentet får också information om täcknings omfånget och härkomst mönster som stöds.

## <a name="view-existing-data-factory-connections"></a>Visa befintliga Data Factory-anslutningar

Flera Azure-datafabriker kan ansluta till en enda Azure avdelningens kontroll-Data Catalog för att skicka härkomst-information. Med den aktuella gränsen kan du ansluta tio Data Factory-konton i taget från hanterings centret för avdelningens kontroll. Om du vill visa listan över Data Factory konton som är anslutna till din avdelningens kontroll Data Catalog gör du följande:

1. Välj **hanterings Center** i det vänstra navigerings fönstret.
2. Under **externa anslutningar** väljer du **Data Factory anslutning**.
3. Listan Data Factory anslutning visas.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Skärm bild som visar en lista med data fabriks anslutningar." lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. Lägg märke till de olika värdena för anslutnings **status**:

    - **Ansluten**: data fabriken är ansluten till data katalogen.
    - **Frånkopplad**: data fabriken har åtkomst till katalogen, men den är ansluten till en annan katalog. Det innebär att data härkomst inte rapporteras automatiskt till katalogen.
    - **CannotAccess**: den aktuella användaren har inte åtkomst till data fabriken, så anslutnings statusen är okänd.
 >[!Note]
 >För att kunna Visa Data Factory-anslutningar måste du tilldelas någon av avdelningens kontroll-rollerna:
 >- Deltagare
 >- Ägare
 >- Läsare
 >- Administratör för användaråtkomst

## <a name="create-new-data-factory-connection"></a>Skapa ny Data Factory anslutning

>[!Note]
>Om du vill lägga till eller ta bort Data Factory anslutningar måste du ha tilldelats någon av avdelningens kontroll-rollerna:
>- Ägare
>- Administratör för användaråtkomst
>
> Förutom måste användarna vara data fabrikens "ägare" eller "bidrag". 

Följ stegen nedan för att ansluta ett befintligt Data Factory-konto till din avdelningens kontroll-Data Catalog.

1. Välj **hanterings Center** i det vänstra navigerings fönstret.
2. Under **externa anslutningar** väljer du **Data Factory anslutning**.
3. På sidan **Data Factory anslutning** väljer du **ny**.

4. Välj ditt Data Factory konto i listan och välj **OK**. Du kan också filtrera efter prenumerations namn om du vill begränsa listan.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Skärm bild som visar hur du ansluter Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    Vissa Data Factory instanser kan inaktive ras om data fabriken redan är ansluten till det aktuella avdelningens kontroll-kontot eller om data fabriken inte har någon hanterad identitet.

    Ett varnings meddelande visas om någon av de valda data fabrikerna redan är ansluten till ett annat avdelningens kontroll-konto. Om du väljer OK kopplas Data Factory-anslutningen till det andra avdelningens kontroll-kontot bort. Inga ytterligare bekräftelser krävs.


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Skärm bild som visar varning för att koppla från Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>Vi har nu stöd för att lägga till fler än 10 data fabriker samtidigt. Om du vill lägga till fler än 10 data fabriker samtidigt kan du skicka en support biljett.

### <a name="how-does-the-authentication-work"></a>Hur fungerar autentiseringen?

När en avdelningens kontroll-användare registrerar en Data Factory som de har åtkomst till, sker följande i Server delen:

1. **Data Factory MSI** läggs till i avdelningens kontroll RBAC-rollen: **avdelningens kontroll data curator**.

    :::image type="content" source="./media/how-to-link-azure-data-factory/adf-msi.png" alt-text="Skärm bild som visar Azure Data Factory MSI." lightbox="./media/how-to-link-azure-data-factory/adf-msi.png":::
     
2. Data Factory pipelinen måste köras igen så att härkomst-metadata kan flyttas tillbaka till avdelningens kontroll.
3. Efter körning Data Factory metadata skickas till avdelningens kontroll.

### <a name="remove-data-factory-connections"></a>Ta bort data Factory-anslutningar
Gör så här om du vill ta bort en data fabriks anslutning:

1. På sidan **Data Factory anslutning** väljer du knappen **ta bort** bredvid en eller flera data fabriks anslutningar.
2. Välj **Bekräfta** i popup-fönstret för att ta bort de valda data fabriks anslutningarna.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="Skärm bild som visar hur du väljer data fabriker för att ta bort anslutningen." lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-ir-to-collect-lineage-from-on-prem-sql"></a>Konfigurera en egen värd-IR för att samla in härkomst från lokal SQL

Härkomst för aktiviteten Data Factory kopiering är tillgänglig för lokala SQL-databaser. Om du kör integration runtime med egen värd för data förflyttningen med Azure Data Factory och vill avbilda härkomst i Azure avdelningens kontroll, kontrollerar du att versionen är 4.8.7418.1 eller senare. Mer information om integration runtime med egen värd finns i [skapa och konfigurera en integration runtime med egen värd](../data-factory/create-self-hosted-integration-runtime.md).

## <a name="supported-azure-data-factory-activities"></a>Azure Data Factory aktiviteter som stöds

Azure avdelningens kontroll fångar upp runtime-härkomst från följande Azure Data Factory aktiviteter:

- Kopiera data
- Dataflöde
- Kör SSIS-paket

> [!IMPORTANT]
> Azure avdelningens kontroll släpper härkomst om källan eller målet använder ett data lagrings system som inte stöds.

Integrationen mellan Data Factory och avdelningens kontroll har endast stöd för en delmängd av de data system som Data Factory stöder, enligt beskrivningen i följande avsnitt.

### <a name="data-factory-copy-data-support"></a>Data Factory Kopiera data support

| Data lagrings system | Stöds som källa | Stöds som mottagare |
| ------------------- | ------------------- | ----------------- |
| ADLS Gen1 | Ja | Ja (endast icke-binär kopia) |
| ADLS Gen2 | Ja | Ja |
| Azure-blobb | Ja | Ja |
| Azure Cosmos DB (SQL API) | Ja | Ja |
| Azure Cosmos DB (Mongo-API) | Ja | Ja |
| Azure Cognitive Search | Ja | Ja |
| Azure-datautforskaren | Ja | Ja |
| Azure Database för Maria DB \* | Ja | Ja |
| Azure Database för MYSQL \* | Ja | Ja |
| Azure Database for PostgreSQL \* | Ja | Ja |
| Azure File Storage | Ja | Ja |
| Azure Table Storage | Ja | Ja |
| Azure SQL Database \* | Ja | Ja |
| Azure SQL MI \* | Ja | Ja |
| Azure Synapse Analytics (tidigare SQL DW) \* | Ja | Ja |
| SQL Server lokal (SHIR krävs) \* | Ja | Ja |
| Amazon S3 | Ja | Ja |
| Teradata | Ja | Ja |
| SAP S4 Hana | Ja | Ja |
| SAP ECC | Ja | Ja |
| Hive | Ja | Ja |

> [!Note]
> Härkomst-funktionen har vissa prestanda kostnader i Data Factory kopierings aktivitet. För de som installerar Data Factory-anslutningar i avdelningens kontroll kan du se att vissa kopierings jobb tar längre tid att slutföra. Det mesta av effekten är att ingen ska vara försumbar. Kontakta supporten med tids jämförelse om kopierings jobben tar betydligt längre tid att slutföra än vanligt.

### <a name="data-factory-data-flow-support"></a>Stöd för Data Factory data flöde

| Data lagrings system | Stöds |
| ------------------- | ------------------- | ----------------- |
| ADLS Gen1 | Ja |
| ADLS Gen2 | Ja |
| Azure-blobb | Ja |
| Azure SQL Database \* | Ja |
| Azure Synapse Analytics (tidigare SQL DW) \* | Ja |

### <a name="data-factory-execute-ssis-package-support"></a>Data Factory köra stöd för SSIS-paket

| Data lagrings system | Stöds |
| ------------------- | ------------------- | ----------------- |
| Azure-blobb | Ja |
| ADLS Gen1 | Ja |
| ADLS Gen2 | Ja |
| Azure SQL Database \* | Ja |
| Azure SQL MI \*| Ja |
| Azure Synapse Analytics (tidigare SQL DW) \* | Ja |
| SQL Server lokal \* | Ja |
| Azure File Storage | Ja |

*\* För SQL-scenarier (Azure och lokalt) stöder Azure avdelningens kontroll inte lagrade procedurer eller skript för härkomst eller genomsökning. Härkomst är begränsat till tabell-och visnings källor.*

> [!Note]
> Azure Data Lake Storage Gen2 är nu allmänt tillgänglig. Vi rekommenderar att du börjar använda den idag. Mer information finns på [produkt sidan](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="supported-lineage-patterns"></a>Härkomst mönster som stöds

Det finns flera mönster i härkomst som Azure avdelningens kontroll stöder. De genererade härkomst-data baseras på typen av källa och mottagare som används i Data Factory aktiviteter. Även om Data Factory stöder över 80-källa och handfat, stöder Azure avdelningens kontroll endast en delmängd, som visas i [Azure Data Factory aktiviteter som stöds](#supported-azure-data-factory-activities).

Information om hur du konfigurerar Data Factory att skicka härkomst-information finns i [Kom igång med härkomst](catalog-lineage-user-guide.md#get-started-with-lineage).

Några ytterligare sätt att hitta information i vyn härkomst innehåller följande:

- På fliken **härkomst** håller du mus pekaren över former för att förhandsgranska ytterligare information om till gången i knapp beskrivningen.
- Välj noden eller kanten för att se den till gångs typen den tillhör eller för att byta till gångar.
- Kolumner i en data uppsättning visas till vänster på fliken **härkomst** . Mer information om härkomst på kolumn nivå finns i [härkomst på kolumn nivå](catalog-lineage-user-guide.md#column-level-lineage).

### <a name="data-lineage-for-11-operations"></a>Data härkomst för 1:1-åtgärder

Det vanligaste mönstret för att samla in data härkomst är att flytta data från en enskild data uppsättning till en enda data uppsättning för utdata, med en process i mellan.

Ett exempel på det här mönstret är följande:

- 1 Källa/indata: *kund* (SQL-tabell)
- 1 mottagare/utdata: *Customer1.csv* (Azure Blob)
- 1 process: *CopyCustomerInfo1 \#Customer1.csv* (Data Factory kopierings aktivitet)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="Skärm bild som visar härkomst för en en-till-en Data Factory kopierings åtgärd." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>Data förflyttning med stöd för 1:1-härkomst och jokertecken

Ett annat vanligt scenario för att fånga härkomst är att använda ett jokertecken för att kopiera filer från en enda data uppsättnings data uppsättning till en enda utdata-datauppsättning. Med jokertecknet kan kopierings aktiviteten matcha flera filer för kopiering med en gemensam del av fil namnet. Azure avdelningens kontroll fångar upp härkomst på fil nivå för varje enskild fil som kopieras av motsvarande kopierings aktivitet.

Ett exempel på det här mönstret är följande:

* Källa/indatamängd: *CustomerCall \* . csv* (ADLS Gen2 sökväg)
* Handfat/utdata: *CustomerCall \* . csv* (Azure Blob-fil)
* 1 process: *CopyGen2ToBlob \#CustomerCall.csv* (Data Factory kopierings aktivitet)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="Skärm bild som visar härkomst för en till en kopierings åtgärd med stöd för jokertecken." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>Data förflyttning med n-härkomst

Du kan använda data flödes aktiviteter för att utföra data åtgärder som sammanfoga, delta och så vidare. Fler än en käll data uppsättning kan användas för att skapa en mål data uppsättning. I det här exemplet fångar Azure avdelningens kontroll upp fil nivå härkomst för enskilda indatafiler till en SQL-tabell som är en del av en data flödes aktivitet.

Ett exempel på det här mönstret är följande:

* 2 källor/indata: *Customer.csv*, *Sales. Parquet* (ADLS Gen2 sökväg)
* 1 mottagare/utdata: *företags data* (Azure SQL-tabell)
* 1 process: *DataFlowBlobsToSQL* (Data Factory data flödes aktivitet)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="Skärm bild som visar härkomst för en n till en D F-data flödes åtgärd." lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>Härkomst för resurs uppsättningar

En resurs uppsättning är ett logiskt objekt i katalogen som representerar många partitioner i den underliggande lagringen. Mer information finns i [förstå resurs uppsättningar](concept-resource-sets.md). När Azure avdelningens kontroll fångar härkomst från Azure Data Factory tillämpas reglerna för att normalisera de enskilda filerna och skapa ett enda logiskt objekt.

I följande exempel skapas en Azure Data Lake Gen2-resurs uppsättning från en Azure-Blob:

* 1 Källa/inmatare: *Medarbetares \_management.csv* (Azure Blob)
* 1 mottagare/utdata: *Medarbetares \_management.csv* (Azure Data Lake gen 2)
* 1 process: *CopyBlobToAdlsGen2 \_ RS* (Data Factory kopierings aktivitet)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="Skärm bild som visar härkomst för en resurs uppsättning." lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>Nästa steg

- [Användar guide för katalog härkomst](catalog-lineage-user-guide.md)
- [Länk till Azure Data Share för härkomst](how-to-link-azure-data-share.md)
