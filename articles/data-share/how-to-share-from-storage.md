---
title: Dela och ta emot data från Azure Blob Storage och Azure Data Lake Storage
description: Lär dig hur du delar och tar emot data från Azure Blob Storage och Azure Data Lake Storage
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 9dfc8be54fc55842440e376916b2eb9bb04a4610
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617093"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Dela och ta emot data från Azure Blob Storage och Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share stöder ögonblicks bilds-baserad delning från lagrings kontot. I den här artikeln förklaras hur du delar och tar emot data från följande källor: Azure Blob Storage Azure Data Lake Storage Gen1 Azure Data Lake Storage Gen2.

Azure data sharing stöder delning av filer, mappar och fil system från Azure Data Lake gen1 och Azure Data Lake Gen2. Det stöder också delning av blobbar, mappar och behållare från Azure Blob Storage. Endast Block-Blob stöds för närvarande. Data som delas från dessa källor kan tas emot i Azure Data Lake Gen2 eller Azure Blob Storage.

När fil system, behållare eller mappar delas i ögonblicks bilds-baserad delning, kan data konsumenten välja att göra en fullständig kopia av delnings data eller utnyttja en stegvis ögonblicks bilds kapacitet för att bara kopiera nya eller uppdaterade filer. En stegvis ögonblicks bild baseras på filernas senaste ändrings tid. Befintliga filer med samma namn kommer att skrivas över under ögonblicks bilden. Filen som togs bort från källan tas inte bort från målet. Tomma undermappar på källan kopieras inte över till målet. 

## <a name="share-data"></a>Dela data

### <a name="prerequisites-to-share-data"></a>Krav för att dela data

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Mottagarens e-postadress för Azure-inloggning (med deras e-postalias fungerar inte).
* Om käll Azure-datalagret finns i en annan Azure-prenumeration än den som du ska använda för att skapa en data resurs resurs registrerar du [Microsoft. DataShare Resource Provider](concepts-roles-permissions.md#resource-provider-registration) i prenumerationen där Azure Data Store finns. 

### <a name="prerequisites-for-source-storage-account"></a>Krav för käll lagrings konto

* Ett Azure Storage konto: om du inte redan har ett kan du skapa ett [Azure Storage konto](../storage/common/storage-account-create.md)
* Behörighet att skriva till lagrings kontot som finns i *Microsoft. Storage/storageAccounts/Write*. Den här behörigheten finns i deltagarrollen.
* Behörighet att lägga till roll tilldelning till lagrings kontot, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den här behörigheten finns i ägarrollen. 

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Skapa ett data resurs konto

Skapa en Azure Data Share-resurs i en Azure-resurs grupp.

1. Välj Meny knappen i det övre vänstra hörnet i portalen och välj sedan **skapa en resurs** (+).

1. Sök efter *data resurs*.

1. Välj data resurs och välj **skapa**.

1. Fyll i grundläggande information om din Azure Data Share-resurs med följande information. 

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som du vill använda för ditt data resurs konto.|
    | Resursgrupp | *test-resurs-grupp* | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
    | Plats | *USA, östra 2* | Välj en region för ditt data resurs konto.
    | Name | *datashareaccount* | Ange ett namn för ditt data resurs konto. |
    | | |

1. Välj **Granska + skapa** och sedan **skapa** för att etablera ditt data resurs konto. Det tar vanligt vis ungefär 2 minuter att tillhandahålla ett nytt data resurs konto. 

1. När distributionen är klar väljer du **Gå till resurs**.

### <a name="create-a-share"></a>Skapa en resurs

1. Gå till översikts sidan för data delning.

    ![Dela dina data](./media/share-receive-data.png "Dela dina data") 

1. Välj **börja dela dina data**.

1. Välj **Skapa**.   

1. Fyll i informationen för din resurs. Ange ett namn, resurs typ, en beskrivning av delnings innehåll och användnings villkor (valfritt). 

    ![EnterShareDetails](./media/enter-share-details.png "Ange resurs information") 

1. Välj **Fortsätt**.

1. Om du vill lägga till data uppsättningar i din resurs väljer du **Lägg till data uppsättningar**. 

    ![Lägg till data uppsättningar till din resurs](./media/datasets.png "Datauppsättningar")

1. Välj den data uppsättnings typ som du vill lägga till. Du ser en annan lista över data uppsättnings typer beroende på vilken resurs typ (ögonblicks bild eller på plats) som du har valt i föregående steg. 

    ![AddDatasets](./media/add-datasets.png "Lägg till data uppsättningar")    

1. Navigera till det objekt som du vill dela och välj Lägg till data uppsättningar. 

    ![SelectDatasets](./media/select-datasets.png "Välj data uppsättningar")    

1. På fliken mottagare anger du e-postadresserna till din data konsument genom att välja "+ Lägg till mottagare". 

    ![AddRecipients](./media/add-recipient.png "Lägga till mottagare") 

1. Välj **Fortsätt**.

1. Om du har valt typ av ögonblicks bilds resurs kan du konfigurera ögonblicks bilds schema för att tillhandahålla uppdateringar av dina data till din data konsument. 

    ![EnableSnapshots](./media/enable-snapshots.png "Aktivera ögonblicks bilder") 

1. Välj en start tid och upprepnings intervall. 

1. Välj **Fortsätt**.

1. På fliken Granska + skapa granskar du paket innehåll, inställningar, mottagare och synkroniseringsinställningar. Välj **Skapa**.

Din Azure Data-resurs har nu skapats och mottagaren av din data resurs är nu klar att acceptera din inbjudan. 

## <a name="receive-data"></a>Ta emot data

### <a name="prerequisites-to-receive-data"></a>Krav för att ta emot data
Innan du kan acceptera en inbjudan till en data resurs måste du etablera ett antal Azure-resurser som visas nedan. 

Se till att alla krav är uppfyllda innan du accepterar en inbjudan om data delning. 

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Inbjudan till en data resurs: en inbjudan från Microsoft Azure med ett ämne med rubriken "Azure Data Share-inbjudan från **<yourdataprovider@domain.com>** ".
* Registrera [resurs leverantören Microsoft. DataShare](concepts-roles-permissions.md#resource-provider-registration) i Azure-prenumerationen där du ska skapa en data resurs resurs och Azure-prenumerationen där dina Azure-datalager finns.

### <a name="prerequisites-for-target-storage-account"></a>Krav för mål lagrings konto

* Ett Azure Storage konto: om du inte redan har ett kan du skapa ett [Azure Storage-konto](../storage/common/storage-account-create.md). 
* Behörighet att skriva till lagrings kontot som finns i *Microsoft. Storage/storageAccounts/Write*. Den här behörigheten finns i deltagarrollen. 
* Behörighet att lägga till roll tilldelning till lagrings kontot, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den här behörigheten finns i ägarrollen.  

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

### <a name="open-invitation"></a>Öppna inbjudan

1. Du kan öppna inbjudan från e-post eller direkt från Azure Portal. 

   Om du vill öppna inbjudan från e-post, kontrollerar du Inkorgen för en inbjudan från din data leverantör. Inbjudan är från Microsoft Azure, med titeln **Azure Data Share-inbjudan från <yourdataprovider@domain.com>**. Klicka på **Visa inbjudan** för att se din inbjudan i Azure. 

   Du öppnar inbjudan från Azure Portal direkt genom att söka efter **inbjudningar för data delning** i Azure Portal. Då går du till listan med data resurs inbjudningar.

   ![Lista över inbjudningar](./media/invitations.png "Lista över inbjudningar") 

1. Välj den resurs som du vill visa. 

### <a name="accept-invitation"></a>Acceptera inbjudan
1. Se till att alla fält har granskats, inklusive **användnings villkoren**. Om du samtycker till användnings villkoren måste du markera kryss rutan för att ange att du godkänner. 

   ![Villkor för användning](./media/terms-of-use.png "Villkor för användning") 

1. Under *konto för mål data resurs* väljer du den prenumeration och resurs grupp som du ska distribuera data resursen till. 

   För fältet **data resurs konto** väljer du **Skapa nytt** om du inte har ett befintligt data resurs konto. Annars väljer du ett befintligt data resurs konto som du vill acceptera data resursen till. 

   I fältet **mottaget resurs namn** kan du lämna standardvärdet som anges av data ange eller ange ett nytt namn för den mottagna resursen. 

   När du har samtyckt till användnings villkoren och angett ett data delnings konto för att hantera din mottagna resurs väljer du **Godkänn och konfigurera**. En resurs prenumeration kommer att skapas. 

   ![Godkänn alternativ](./media/accept-options.png "Godkänn alternativ") 

   Detta gör att du är den mottagna resursen i ditt data resurs konto. 

   Om du inte vill acceptera inbjudan väljer du *avvisa*. 

### <a name="configure-received-share"></a>Konfigurera mottagen resurs
Följ stegen nedan för att konfigurera var du vill ta emot data.

1. Fliken Välj **data uppsättningar** . Markera kryss rutan bredvid den data uppsättning som du vill tilldela ett mål. Välj **+ Mappa till mål** för att välja ett mål data lager. 

   ![Mappa till mål](./media/dataset-map-target.png "Mappa till mål") 

1. Välj ett mål data lager som du vill att data ska hamna i. Alla datafiler i mål data lagret med samma sökväg och namn kommer att skrivas över. 

   ![Mål lagrings konto](./media/map-target.png "Mål lagring") 

1. För ögonblicks bilds-baserad delning, om dataprovidern har skapat ett ögonblicks bild schema för att tillhandahålla regelbunden uppdatering av data, kan du också aktivera schema för ögonblicks bild genom att välja fliken **ögonblicks bild schema** . Markera kryss rutan bredvid schemat för ögonblicks bilder och välj **+ Aktivera**.

   ![Aktivera schema för ögonblicks bild](./media/enable-snapshot-schedule.png "Aktivera schema för ögonblicks bild")

### <a name="trigger-a-snapshot"></a>Utlös en ögonblicks bild
De här stegen gäller endast för Snapshot-baserad delning.

1. Du kan utlösa en ögonblicks bild genom att välja fliken **information** följt av **Utlös ögonblicks bild**. Här kan du utlösa en fullständig eller stegvis ögonblicks bild av dina data. Om det är första gången du tar emot data från din dataprovider väljer du fullständig kopia. 

   ![Utlös ögonblicks bild](./media/trigger-snapshot.png "Utlös ögonblicks bild") 

1. När den senaste körnings statusen har *slutförts* går du till mål data lagret för att Visa mottagna data. Välj **data uppsättningar** och klicka på länken i mål Sök vägen. 

   ![Konsument data uppsättningar](./media/consumer-datasets.png "Mappning av konsument data uppsättning") 

### <a name="view-history"></a>Visa historik
Det här steget gäller endast för Snapshot-baserad delning. Välj fliken **Historik** för att visa historiken för dina ögonblicks bilder. Här hittar du historiken för alla ögonblicks bilder som har genererats under de senaste 30 dagarna. 

## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du delar och tar emot data från lagrings kontot med Azure Data Share-tjänsten. Om du vill veta mer om delning från andra data källor fortsätter du till [data lager som stöds](supported-data-stores.md).