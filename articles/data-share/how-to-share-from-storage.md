---
title: Dela och ta emot data från Azure Blob Storage och Azure Data Lake Storage
description: Lär dig hur du delar och tar emot data från Azure Blob Storage och Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 242980ac1b89345ed9d8ff903e65129cff3cb917
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964107"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Dela och ta emot data från Azure Blob Storage och Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share stöder ögonblicks bilds-baserad delning från ett lagrings konto. Den här artikeln förklarar hur du delar och tar emot data från Azure Blob Storage, Azure Data Lake Storage Gen1 och Azure Data Lake Storage Gen2.

Azure Data Share stöder delning av filer, mappar och fil system från Azure Data Lake gen1 och Azure Data Lake Gen2. Det stöder också delning av blobbar, mappar och behållare från Azure Blob Storage. Endast block-blobbar stöds för närvarande. Data som delas från dessa källor kan tas emot av Azure Data Lake Gen2 eller Azure Blob Storage.

När fil system, behållare eller mappar delas i ögonblicks bilds-baserad delning, kan data konsumenter välja att göra en fullständig kopia av delnings data. Eller så kan de använda den stegvisa ögonblicks bilds funktionen för att endast kopiera nya eller uppdaterade filer. Den stegvisa ögonblicks bilds kapaciteten baseras på filernas senaste ändrings tid. 

Befintliga filer med samma namn skrivs över under en ögonblicks bild. En fil som tas bort från källan tas inte bort från målet. Tomma undermappar på källan kopieras inte till målet. 
## <a name="share-data"></a>Dela data

Använd informationen i följande avsnitt för att dela data med hjälp av Azure Data Share. 
### <a name="prerequisites-to-share-data"></a>Krav för att dela data

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Hitta mottagarens e-postadress för Azure-inloggning. Mottagarens e-postalias fungerar inte för dina behov.
* Om käll-Azure-datalagret finns i en annan Azure-prenumeration än den där du ska skapa data resurs resursen registrerar du [Microsoft. DataShare Resource Provider](concepts-roles-permissions.md#resource-provider-registration) i prenumerationen där Azure Data Store finns. 

### <a name="prerequisites-for-the-source-storage-account"></a>Krav för käll lagrings kontot

* Ett Azure Storage-konto. Om du inte redan har ett konto [skapar du ett](../storage/common/storage-account-create.md).
* Behörighet att skriva till lagrings kontot. Skriv behörigheten finns i *Microsoft. Storage/storageAccounts/Write*. Den är en del av deltagar rollen.
* Behörighet att lägga till roll tilldelning i lagrings kontot. Den här behörigheten finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den är en del av ägar rollen. 

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Skapa ett data resurs konto

Skapa en Azure Data Share-resurs i en Azure-resurs grupp.

1. Öppna menyn i det övre vänstra hörnet i portalen och välj sedan **skapa en resurs** (+).

1. Sök efter *data resurs*.

1. Välj **data resurs** och **skapa**.

1. Ange grundläggande information om din Azure Data Share-resurs: 

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Prenumeration | Din prenumeration | Välj en Azure-prenumeration för ditt data resurs konto.|
    | Resursgrupp | *test-resurs-grupp* | Använd en befintlig resurs grupp eller skapa en resurs grupp. |
    | Plats | *USA, östra 2* | Välj en region för ditt data resurs konto.
    | Namn | *datashareaccount* | Namnge ditt data resurs konto. |
    | | |

1. Välj **Granska + skapa**  >  **skapa** för att etablera ditt data resurs konto. Det tar vanligt vis ungefär 2 minuter att tillhandahålla ett nytt data resurs konto. 

1. När distributionen är klar väljer du **Gå till resurs**.

### <a name="create-a-share"></a>Skapa en resurs

1. Gå till **översikts** sidan för data delning.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Skärm bild som visar översikt över data resursen.":::

1. Välj **börja dela dina data**.

1. Välj **Skapa**.   

1. Ange information om din resurs. Ange ett namn, resurs typ, en beskrivning av delnings innehåll och användnings villkor (valfritt). 

    ![Skärm bild som visar information om data resurser.](./media/enter-share-details.png "Ange information om data resursen.") 

1. Välj **Fortsätt**.

1. Om du vill lägga till data uppsättningar i din resurs väljer du **Lägg till data uppsättningar**. 

    ![Skärm bild som visar hur du lägger till data uppsättningar i din resurs.](./media/datasets.png "Data uppsättningar.")

1. Välj en data mängds typ som ska läggas till. Listan över data uppsättnings typer beror på om du har valt ögonblicks bilds-baserad delning eller delning på plats i föregående steg. 

    ![Skärm bild som visar var du väljer en data uppsättnings typ.](./media/add-datasets.png "Lägg till data uppsättningar.")    

1. Gå till det objekt som du vill dela. Välj sedan **Lägg till data uppsättningar**. 

    ![Skärm bild som visar hur du väljer ett objekt att dela.](./media/select-datasets.png "Välj data uppsättningar.")    

1. På fliken **mottagare** lägger du till e-postadressen för din data konsument genom att välja **Lägg till mottagare**. 

    ![Skärm bild som visar hur du lägger till mottagares e-postadresser.](./media/add-recipient.png "Lägg till mottagare.") 

1. Välj **Fortsätt**.

1. Om du har valt en typ av ögonblicks bilds resurs kan du konfigurera ögonblicks bilds schemat för att uppdatera data för data konsumenten. 

    ![Skärm bild som visar inställningarna för ögonblicks bild scheman.](./media/enable-snapshots.png "Aktivera ögonblicks bilder.") 

1. Välj en start tid och upprepnings intervall. 

1. Välj **Fortsätt**.

1. På fliken **Granska + skapa** granskar du paket innehåll, inställningar, mottagare och synkroniseringsinställningar. Välj sedan **Skapa**.

Nu har du skapat din Azure-Dataresurs. Mottagaren av din data resurs kan acceptera din inbjudan. 

## <a name="receive-data"></a>Ta emot data

I följande avsnitt beskrivs hur du tar emot delade data.
### <a name="prerequisites-to-receive-data"></a>Krav för att ta emot data
Innan du accepterar en inbjudan om data delning måste du kontrol lera att du har följande krav: 

* En Azure-prenumeration. Om du inte har någon prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En inbjudan från Azure. E-postmeddelandets ämne bör vara "Azure Data Share-inbjudan från *\<yourdataprovider\@domain.com>* ".
* En registrerad [Microsoft. DataShare-resurs leverantör](concepts-roles-permissions.md#resource-provider-registration) i:
    * Azure-prenumerationen där du skapar en data resurs resurs.
    * Azure-prenumerationen där ditt mål för Azure-datalager finns.

### <a name="prerequisites-for-a-target-storage-account"></a>Krav för ett mål lagrings konto

* Ett Azure Storage-konto. [Skapa ett konto](../storage/common/storage-account-create.md)om du inte redan har en. 
* Behörighet att skriva till lagrings kontot. Den här behörigheten finns i *Microsoft. Storage/storageAccounts/Write*. Den är en del av deltagar rollen. 
* Behörighet att lägga till roll tilldelning i lagrings kontot. Den här tilldelningen finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den är en del av ägar rollen.  

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Öppna en inbjudan

Du kan öppna en inbjudan från e-post eller direkt från Azure Portal.

1. Om du vill öppna en inbjudan från e-post, kontrollerar du Inkorgen för en inbjudan från din data leverantör. Inbjudan från Microsoft Azure har titeln "Azure Data Share-inbjudan från *\<yourdataprovider\@domain.com>* ". Välj **Visa inbjudan** för att se din inbjudan i Azure. 

   Om du vill öppna en inbjudan från Azure Portal kan du söka efter *data dela inbjudningar*. Du ser en lista över inbjudningar för data resurser.

   ![Skärm bild som visar listan över inbjudningar i Azure Portal.](./media/invitations.png "Lista över inbjudningar.") 

1. Välj den resurs som du vill visa. 

### <a name="accept-an-invitation"></a>Acceptera en inbjudan
1. Granska alla fält, inklusive **användningsvillkor**. Om du godkänner villkoren markerar du kryss rutan. 

   ![Skärm bild som visar Användningsvillkor-ytan.](./media/terms-of-use.png "Användningsvillkor.") 

1. Under **konto för mål data resurs** väljer du den prenumeration och resurs grupp där du ska distribuera data resursen. Fyll sedan i följande fält:

   * I fältet **data resurs konto** väljer du **Skapa nytt** om du inte har ett data resurs konto. Annars väljer du ett befintligt data delnings konto som ska acceptera din data resurs. 

   * I fältet **mottaget resurs namn** lämnar du standardvärdet som data leverantören anger eller anger ett nytt namn för den mottagna resursen. 

1. Välj **Godkänn och konfigurera**. En resurs prenumeration skapas. 

   ![Skärm bild som visar var du accepterar konfigurations alternativen.](./media/accept-options.png "Godkänn alternativ") 

    Den mottagna resursen visas i ditt data resurs konto. 

    Om du inte vill acceptera inbjudan väljer du **avvisa**. 

### <a name="configure-a-received-share"></a>Konfigurera en mottagen resurs
Följ stegen i det här avsnittet om du vill konfigurera en plats för att ta emot data.

1. Markera kryss rutan bredvid den data uppsättning där du vill tilldela ett mål på fliken **data uppsättningar** . Välj **Mappa till mål** för att välja ett mål data lager. 

   ![Skärm bild som visar hur du mappar till ett mål.](./media/dataset-map-target.png "Mappa till mål.") 

1. Välj ett mål data lager för data. Filer i mål data lagret som har samma sökväg och namn som filer i de data som tas emot kommer att skrivas över. 

   ![Skärm bild som visar var du kan välja ett mål lagrings konto.](./media/map-target.png "Mål lagring.") 

1. För ögonblicks bilds-baserad delning, om dataprovidern använder ett ögonblicks bild schema för att regelbundet uppdatera data, kan du aktivera schemat från fliken **ögonblicks bild schema** . Markera rutan bredvid ögonblicks bilds schemat. Välj sedan **Aktivera**.

   ![Skärm bild som visar hur du aktiverar ett schema för ögonblicks bilder.](./media/enable-snapshot-schedule.png "Aktivera schema för ögonblicks bild.")

### <a name="trigger-a-snapshot"></a>Utlös en ögonblicks bild
Stegen i det här avsnittet gäller endast för Snapshot-baserad delning.

1. Du kan utlösa en ögonblicks bild från fliken **information** . På fliken väljer du **Utlös ögonblicks bild**. Du kan välja att utlösa en fullständig ögonblicks bild eller en stegvis ögonblicks bild av dina data. Om du tar emot data från data leverantören för första gången väljer du **fullständig kopia**. 

   ![Skärm bild som visar alternativet Utlös ögonblicks bild.](./media/trigger-snapshot.png "Utlös ögonblicks bild.") 

1. När den senaste körnings statusen har *slutförts* går du till mål data lagret för att Visa mottagna data. Välj **data uppsättningar** och välj sedan länken mål Sök väg. 

   ![Skärm bild som visar en mappning för en konsument data uppsättning.](./media/consumer-datasets.png "Mappning av konsument data uppsättning.") 

### <a name="view-history"></a>Visa historik
Du kan bara visa historiken för dina ögonblicks bilder i ögonblicks bilds-baserad delning. Öppna **Historik-fliken för** att visa historiken. Här visas historiken för alla ögonblicks bilder som har genererats under de senaste 30 dagarna. 

## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du delar och tar emot data från ett lagrings konto med hjälp av Azure Data Share-tjänsten. Information om hur du delar från andra data källor finns i [data lager som stöds](supported-data-stores.md).