---
title: Metod tips för att skriva till filer till data Lake med data flöden
description: Den här självstudien innehåller metod tips för att skriva till filer till data Lake med data flöden
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/01/2021
ms.openlocfilehash: 8010f3f95c9358714b659df5821a375bd8488ad8
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582320"
---
# <a name="best-practices-for-writing-to-files-to-data-lake-with-data-flows"></a>Metod tips för att skriva till filer till data Lake med data flöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får du lära dig metod tips som kan tillämpas när du skriver filer till ADLS Gen2 eller Azure Blob Storage med hjälp av data flöden. Du måste ha åtkomst till ett Azure Blob Storage-konto eller ett Azure Data Lake Store Gen2-konto för att läsa en Parquet-fil och sedan lagra resultatet i mappar.

## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**. Du använder ADLS-lagring som *käll* -och *mottagar* data lager. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md).

Stegen i den här självstudien förutsätter att du har 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

I det här steget skapar du en data fabrik och öppnar Data Factory UX för att skapa en pipeline i data fabriken.

1. Öppna **Microsoft Edge** eller **Google Chrome**. Data Factory-gränssnittet stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. På den vänstra menyn väljer du **skapa en resurs**  >  **integration**  >  **Data Factory**
1. På sidan **ny data fabrik** , under **namn**, anger du **ADFTutorialDataFactory**
1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
1. Gör något av följande för **Resursgrupp**:

    a. Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.
    
    b. Välj **Skapa ny** och ange namnet på en resurs grupp. Mer information om resurs grupper finns i [använda resurs grupper för att hantera dina Azure-resurser](../azure-resource-manager/management/overview.md).
    
1. Under **Version** väljer du **V2**.
1. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Data lager (till exempel Azure Storage och SQL Database) och beräkningarna (till exempel Azure HDInsight) som används av data fabriken kan finnas i andra regioner.
1. Välj **Skapa**.
1. När du har skapat meddelandet visas meddelandet i Notifications Center. Välj **gå till resurs** för att navigera till sidan data fabrik.
1. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Skapa en pipeline med en data flödes aktivitet

I det här steget ska du skapa en pipeline som innehåller en data flödes aktivitet.

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**.

   ![Skapa pipeline](./media/doc-common-process/get-started-page.png)

1. På fliken **Allmänt** för pipelinen anger du **DeltaLake** som **namn** på pipelinen.
1. I det fabriksinstallerade övre fältet drar du skjutreglaget för **data flödes fel sökning** på. Fel söknings läge möjliggör interaktiv testning av omvandlings logik mot ett aktivt Spark-kluster. Data flödes kluster tar 5-7 minuter att värma upp och användare rekommenderas att aktivera fel sökning först om de planerar att utföra data flödes utveckling. Mer information finns i [fel söknings läge](concepts-data-flow-debug-mode.md).

    ![Data flödes aktivitet](media/tutorial-data-flow/dataflow1.png)
1. I fönstret **aktiviteter** expanderar du **förflyttnings-och omvandlings** draget. Dra och släpp **data flödes** aktiviteten från fönstret till pipeline-arbetsytan.

    ![Skärm bild som visar pipeline-arbetsytan där du kan släppa data flödes aktiviteten.](media/tutorial-data-flow/activity1.png)
1. I popup-fönstret för **att lägga till data flöde** väljer du **Skapa nytt data flöde** och namnger sedan **DeltaLake** för data flödet. Klicka på Slutför när du är klar.

    ![Skärm bild som visar var du namnger ditt data flöde när du skapar ett nytt data flöde.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Bygg omvandlings logik i data flödets arbets yta

Du kommer att ta alla käll data (i den här självstudien använder vi en Parquet-filkälla) och använder en Sink-omvandling för att använda data i Parquet-format med de mest effektiva mekanismerna för data Lake ETL.

![Slutligt flöde](media/data-flow/parts-final.png "Slutligt flöde")

### <a name="tutorial-objectives"></a>Kurs mål

1. Välj någon av dina käll data uppsättningar i ett nytt data flöde
1. Använd data flöden för att effektivt partitionera din Sink-datauppsättning
1. Landa dina partitionerade data i ADLS Gen2 Lake-mappar

### <a name="start-from-a-blank-data-flow-canvas"></a>Börja med en tom arbets yta för data flöde

Först ska vi ställa in data flödes miljön för var och en av de mekanismer som beskrivs nedan för landnings data i ADLS Gen2

1. Klicka på käll omvandlingen.
1. Klicka på knappen nytt bredvid data uppsättning på den nedre panelen.
1. Välj en data uppsättning eller skapa en ny. I den här demonstrationen använder vi en Parquet-datauppsättning som kallas användar data.
1. Lägg till en härledd kolumn omvandling. Vi använder detta som ett sätt att ange önskade mappnamn dynamiskt.
1. Lägg till en Sink-omvandling.
   
### <a name="hierarchical-folder-output"></a>Utdata för hierarkisk mapp

Det är mycket vanligt att använda unika värden i dina data för att skapa mapphierarkier för att partitionera data i sjön. Detta är ett mycket optimalt sätt att organisera och bearbeta data i sjö och i Spark (beräknings motorn bakom data flöden). Det kommer dock att finnas en liten prestanda kostnad för att organisera dina utdata på det här sättet. Vi räknar med att se en liten minskning av den totala prestandan för pipelinen med den här mekanismen i mottagaren.

1. Gå tillbaka till data flödes designern och redigera data flödet skapa ovan. Klicka på Sink-omvandlingen.
1. Klicka på optimera > ange partitionering > nyckel
1. Välj den eller de kolumner som du vill använda för att ange den hierarkiska mappstrukturen.
1. Observera exemplet nedan använder år och månad som kolumner för namngivning av mappar. Resultaten blir mappar i formuläret ```releaseyear=1990/month=8``` .
1. När du använder datapartitionerna i en data flödes källa pekar du bara på den översta mappen ovan ```releaseyear``` och använder ett mönster för jokertecken för varje efterföljande mapp, t. ex.: ```**/**/*.parquet```
1. Om du vill ändra data värden eller även om du behöver generera syntetiska värden för mappnamn använder du den härledda kolumn omvandlingen för att skapa de värden som du vill använda i mappnamnen.

![Nyckel partitionering](media/data-flow/key-parts.png "Nyckel partitionering")
   
### <a name="name-folder-as-data-values"></a>Namnge mappen som data värden

En något bättre presterande tagnings teknik för sjö data som använder ADLS Gen2 som inte ger samma fördelar som nyckel/värde-partitionering, är ```Name folder as column data``` . Med nyckel partitionerings formatet för hierarkiska strukturer kan du bearbeta data sektorer enklare, den här tekniken är en utplattad mappstruktur som kan skriva data snabbare.

1. Gå tillbaka till data flödes designern och redigera data flödet skapa ovan. Klicka på Sink-omvandlingen.
1. Klicka på optimera > ange partitionering > Använd nuvarande partitionering.
1. Klicka på Inställningar > mappen namn som kolumn data.
1. Välj den kolumn som du vill använda för att skapa mappnamn.
1. Om du vill ändra data värden eller även om du behöver generera syntetiska värden för mappnamn använder du den härledda kolumn omvandlingen för att skapa de värden som du vill använda i mappnamnen.

![Mappalternativ](media/data-flow/folders.png "Mappar")

### <a name="name-file-as-data-values"></a>Namnge fil som data värden

De metoder som anges i de här självstudierna är användbara för att skapa mapp kategorier i data Lake. Standard namngivnings schemat som används av dessa tekniker är att använda jobb-ID: t Spark utförar. Ibland kanske du vill ange namnet på utdatafilen i en text mottagare för data flödet. Den här tekniken rekommenderas endast för användning med små filer. Processen att sammanfoga diskpartitioner till en enda utdatafil är en tids krävande process.

1. Gå tillbaka till data flödes designern och redigera data flödet skapa ovan. Klicka på Sink-omvandlingen.
1. Klicka på optimera > ange partitionering > en partition. Det här är det enda kravet på en partition som skapar en Flask hals i körnings processen när filerna slås samman. Det här alternativet rekommenderas endast för små filer.
1. Klicka på Inställningar > namn fil som kolumn data.
1. Välj den kolumn som du vill använda för att skapa fil namn.
1. Om du vill ändra datavärdena, eller även om du behöver generera syntetiska värden för fil namn, använder du den härledda kolumn omvandlingen för att skapa de värden som du vill använda i fil namnen.

## <a name="next-steps"></a>Nästa steg

Läs mer om [data flödes mottagare](data-flow-sink.md).
