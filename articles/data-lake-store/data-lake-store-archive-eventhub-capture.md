---
title: Samla in data från Event Hubs till Azure Data Lake Storage Gen1
description: Lär dig hur du använder Azure Data Lake Storage Gen1 för att samla in data som tagits emot av Azure Event Hubs. Börja med att verifiera kraven.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8be242369ecae2c809a38428284c9ddcad440e3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91578248"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Använd Azure Data Lake Storage Gen1 för att avbilda data från Event Hubs

Lär dig hur du använder Azure Data Lake Storage Gen1 för att samla in data som tagits emot av Azure Event Hubs.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Ett Azure Data Lake Storage gen1 konto**. Instruktioner för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage gen1](data-lake-store-get-started-portal.md).

*  **Ett Event Hubs namn område**. Instruktioner finns i [skapa ett Event Hubs-namnområde](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Kontrol lera att Data Lake Storage Gen1-kontot och Event Hubs namn området finns i samma Azure-prenumeration.


## <a name="assign-permissions-to-event-hubs"></a>Tilldela Event Hubs behörigheter

I det här avsnittet skapar du en mapp i kontot där du vill samla in data från Event Hubs. Du tilldelar också behörigheter till Event Hubs så att de kan skriva data till ett Data Lake Storage Gen1-konto. 

1. Öppna det Data Lake Storage Gen1-konto där du vill samla in data från Event Hubs och klicka sedan på **datautforskaren**.

    ![Data Lake Storage Gen1 data Utforskaren](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1 data Utforskaren")

1.  Klicka på **ny mapp** och ange sedan ett namn för den mapp där du vill samla in data.

    ![Skapa en ny mapp i Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Skapa en ny mapp i Data Lake Storage Gen1")

1. Tilldela behörigheter i roten för Data Lake Storage Gen1. 

    a. Klicka på **datautforskaren**, Välj roten för det data Lake Storage gen1 kontot och klicka sedan på **åtkomst**.

    ![Skärm bild av data Utforskaren med roten för kontot och åtkomst alternativet som anropas.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Tilldela behörigheter för Data Lake Storage Gen1 roten")

    b. Under **åtkomst** klickar du på **Lägg till**, klicka på **Välj användare eller grupp** och Sök sedan efter `Microsoft.EventHubs` . 

    ![Skärm bild av åtkomst sidan med alternativet Lägg till, Välj alternativ för användare eller grupp och alternativet Microsoft-Eventhubs som kallas.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Tilldela behörigheter för Data Lake Storage Gen1 roten")
    
    Klicka på **Välj**.

    c. Under **tilldela behörigheter** klickar du på **Välj behörigheter**. Ange **behörigheter** som ska **köras**. Ange **Lägg** till i **den här mappen och alla underordnade**. Ange **Lägg till som** **åtkomst behörighets post och en standard behörighets post**.

    > [!IMPORTANT]
    > När du skapar en ny mapphierarki för att fånga data som tagits emot av Azure Event Hubs är det ett enkelt sätt att säkerställa åtkomst till målmappen.  Men det kan ta lång tid att lägga till behörigheter till alla underordnade till en mapp på översta nivån med många underordnade filer och mappar.  Om rotmappen innehåller ett stort antal filer och mappar kan det gå snabbare att lägga till **körnings** behörigheter för `Microsoft.EventHubs` enskilda mappar i sökvägen till den slutliga målmappen. 

    ![Skärm bild av avsnittet tilldela behörigheter med alternativet Välj behörigheter inringat. Avsnittet Välj behörigheter är intill det med alternativet Kör, Lägg till i alternativet och Lägg till som-alternativet som kallas.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Tilldela behörigheter för Data Lake Storage Gen1 roten")

    Klicka på **OK**.

1. Tilldela behörigheter för mappen under det Data Lake Storage Gen1 konto där du vill samla in data.

    a. Klicka på **datautforskaren**, Välj mappen i data Lake Storage gen1 konto och klicka sedan på **åtkomst**.

    ![Skärm bild av data Utforskaren med en mapp i kontot och åtkomst alternativet har anropats.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Tilldela behörigheter för mappen Data Lake Storage Gen1")

    b. Under **åtkomst** klickar du på **Lägg till**, klicka på **Välj användare eller grupp** och Sök sedan efter `Microsoft.EventHubs` . 

    ![Skärm bild av åtkomst sidan för data Utforskaren med alternativet Lägg till, Välj alternativ för användare eller grupp och alternativet Microsoft-Eventhubs.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Tilldela behörigheter för mappen Data Lake Storage Gen1")
    
    Klicka på **Välj**.

    c. Under **tilldela behörigheter** klickar du på **Välj behörigheter**. Ange **behörigheter** för att **läsa, skriva** och **köra**. Ange **Lägg** till i **den här mappen och alla underordnade**. Slutligen anger du **Lägg till som** till **en åtkomst behörighets post och en standard behörighets post**.

    ![Skärm bild av avsnittet tilldela behörigheter med alternativet Välj behörigheter inringat. Avsnittet Välj behörigheter är intill det med alternativen läsa, skriva och köra, alternativet Lägg till i och alternativet Lägg till som anropas.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Tilldela behörigheter för mappen Data Lake Storage Gen1")
    
    Klicka på **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Konfigurera Event Hubs att avbilda data till Data Lake Storage Gen1

I det här avsnittet skapar du en Event Hub i ett Event Hubs namn område. Du konfigurerar också Händelsehubben för att samla in data till ett Azure Data Lake Storage Gen1-konto. Det här avsnittet förutsätter att du redan har skapat ett Event Hubs-namnområde.

1. I fönstret **Översikt** i Event Hubs namn området klickar du på **+ händelsehubben**.

    ![Skärm bild av översikts fönstret med alternativet Händelsehubben inringat.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Skapa händelsehubb")

1. Ange följande värden för att konfigurera Event Hubs att avbilda data till Data Lake Storage Gen1.

    ![Skärm bild av dialog rutan skapa Händelsehubben med text rutan namn, avbildnings alternativ, alternativet Hämta Provider, alternativet Välj Data Lake Store och alternativet Data Lake sökväg som kallas.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Skapa händelsehubb")

    a. Ange ett namn för Händelsehubben.
    
    b. I den här självstudien anger du **antal partitioner** och **kvarhållning av meddelanden** till standardvärdena.
    
    c. Ställ **in** på **på**. Ange **tidsfönstret** (hur ofta hämtningen ska uppfångas) och **storleks fönstret** (data storleken för att avbilda). 
    
    d. För **avbildnings leverantör** väljer du **Azure Data Lake Store** och väljer sedan det data Lake Storage gen1 konto som du skapade tidigare. Ange namnet på den mapp som du skapade i Data Lake Storage Gen1-kontot för **data Lake sökväg**. Du behöver bara ange den relativa sökvägen till mappen.

    e. Lämna **exempel fil namns formatet** till standardvärdet. Med det här alternativet styrs mappstrukturen som skapas under mappen Capture.

    f. Klicka på **Skapa**.

## <a name="test-the-setup"></a>Testa installationen

Nu kan du testa lösningen genom att skicka data till Azure Event Hub. Följ anvisningarna i [skicka händelser till Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). När du börjar skicka data visas data som visas i Data Lake Storage Gen1 med den mappstruktur du angav. Till exempel visas en mappstruktur, som visas i följande skärm bild, i ditt Data Lake Storage Gen1-konto.

![Exempel på EventHub-data i Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Exempel på EventHub-data i Data Lake Storage Gen1")

> [!NOTE]
> Även om du inte har meddelanden i Event Hubs skriver Event Hubs tomma filer med bara rubrikerna i Data Lake Storage Gen1-kontot. Filerna skrivs med samma tidsintervall som du angav när du skapade Event Hubs.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analysera data i Data Lake Storage Gen1

När data är i Data Lake Storage Gen1 kan du köra analytiska jobb för att bearbeta och utföra data. Se [USQL Avro-exempel](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) på hur du gör detta med hjälp av Azure Data Lake Analytics.
  

## <a name="see-also"></a>Se även
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Kopiera data från Azure Storage blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
