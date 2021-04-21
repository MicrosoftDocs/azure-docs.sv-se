---
title: Dela och ta emot data från Azure Blob Storage och Azure Data Lake Storage
description: Lär dig hur du delar och tar emot data från Azure Blob Storage och Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 59c1ca67c9e93b62890512cda647ffcdf7712f9a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819276"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Dela och ta emot data från Azure Blob Storage och Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share stöder ögonblicksbildbaserad delning från ett lagringskonto. Den här artikeln förklarar hur du delar och tar emot data från Azure Blob Storage, Azure Data Lake Storage Gen1 och Azure Data Lake Storage Gen2.

Azure Data Share har stöd för delning av filer, mappar och filsystem från Azure Data Lake Gen1 och Azure Data Lake Gen2. Den stöder också delning av blobar, mappar och containrar från Azure Blob Storage. Du kan dela block-, tilläggs- eller sidblobar och de tas emot som blockblobar. Data som delas från dessa källor kan tas emot av Azure Data Lake Gen2 eller Azure Blob Storage.

När filsystem, containrar eller mappar delas i ögonblicksbildbaserad delning kan datakonsumenter välja att göra en fullständig kopia av resursdata. De kan också använda funktionen för inkrementell ögonblicksbild för att endast kopiera nya eller uppdaterade filer. Funktionen för inkrementell ögonblicksbild baseras på filernas senaste ändringstid. 

Befintliga filer med samma namn skrivs över under en ögonblicksbild. En fil som tas bort från källan tas inte bort på målet. Tomma undermappar vid källan kopieras inte över till målet. 

## <a name="share-data"></a>Dela data

Använd informationen i följande avsnitt för att dela data med hjälp av Azure Data Share. 
### <a name="prerequisites-to-share-data"></a>Krav för att dela data

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Hitta mottagarens e-postadress för Azure-inloggning. Mottagarens e-postalias fungerar inte för dina syften.
* Om Azure-källdatalagret finns i en annan Azure-prenumeration än den där du skapar Data Share-resursen registrerar du resursprovidern [Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) i prenumerationen där Azure-datalagret finns. 

### <a name="prerequisites-for-the-source-storage-account"></a>Krav för källlagringskontot

* Ett Azure Storage-konto. Om du inte redan har ett konto skapar [du ett](../storage/common/storage-account-create.md).
* Behörighet att skriva till lagringskontot. Skrivbehörighet finns i *Microsoft.Storage/storageAccounts/write*. Det är en del av rollen Deltagare.
* Behörighet att lägga till rolltilldelning till lagringskontot. Den här behörigheten finns *i Microsoft.Authorization/role assignments/write*. Det är en del av rollen Ägare. 

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Skapa ett Data Share konto

Skapa en Azure Data Share resurs i en Azure-resursgrupp.

1. I det övre vänstra hörnet i portalen öppnar du menyn och väljer sedan **Skapa en resurs** (+).

1. Sök efter *Data Share*.

1. Välj **Data Share** och **Skapa**.

1. Ange grundläggande information om din Azure Data Share resurs: 

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Prenumeration | Din prenumeration | Välj en Azure-prenumeration för ditt dataresurskonto.|
    | Resursgrupp | *test-resource-group* | Använd en befintlig resursgrupp eller skapa en resursgrupp. |
    | Location | *USA, östra 2* | Välj en region för ditt dataresurskonto.
    | Name | *datashareaccount* | Namnge ditt dataresurskonto. |
    | | |

1. Välj **Granska + skapa skapa**  >  **för** att etablera ditt dataresurskonto. Etableringen av ett nytt dataresurskonto tar vanligtvis cirka 2 minuter. 

1. När distributionen är klar väljer du **Gå till resurs**.

### <a name="create-a-share"></a>Skapa en resurs

1. Gå till översiktssidan för **din** dataresurs.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Skärmbild som visar översikten över dataresursen.":::

1. Välj **Börja dela dina data.**

1. Välj **Skapa**.   

1. Ange information för din resurs. Ange namn, resurstyp, beskrivning av resursinnehåll och användningsvillkor (valfritt). 

    ![Skärmbild som visar information om dataresursen.](./media/enter-share-details.png "Ange information om dataresursen.") 

1. Välj **Fortsätt**.

1. Om du vill lägga till datauppsättningar i resursen väljer du **Lägg till datauppsättningar**. 

    ![Skärmbild som visar hur du lägger till datauppsättningar i din resurs.](./media/datasets.png "Datamängder.")

1. Välj en datauppsättningstyp som ska läggas till. Listan över datauppsättningstyper beror på om du valde ögonblicksbildsbaserad delning eller delning på plats i föregående steg. 

    ![Skärmbild som visar var du väljer en datauppsättningstyp.](./media/add-datasets.png "Lägg till datauppsättningar.")    

1. Gå till det objekt som du vill dela. Välj sedan **Lägg till datauppsättningar.** 

    ![Skärmbild som visar hur du väljer ett objekt att dela.](./media/select-datasets.png "Välj datauppsättningar.")    

1. På fliken **Mottagare** lägger du till datakonsumentens e-postadress genom att välja **Lägg till mottagare.** 

    ![Skärmbild som visar hur du lägger till mottagarens e-postadresser.](./media/add-recipient.png "Lägg till mottagare.") 

1. Välj **Fortsätt**.

1. Om du har valt en resurstyp för ögonblicksbilder kan du konfigurera schemat för ögonblicksbilder för att uppdatera dina data för datakonsumenten. 

    ![Skärmbild som visar inställningarna för schemat för ögonblicksbilder.](./media/enable-snapshots.png "Aktivera ögonblicksbilder.") 

1. Välj starttid och upprepningsintervall. 

1. Välj **Fortsätt**.

1. På fliken **Granska + skapa** granskar du paketinnehållet, inställningarna, mottagarna och synkroniseringsinställningarna. Välj sedan **Skapa**.

Nu har du skapat din Azure-dataresurs. Mottagaren av din dataresurs kan acceptera din inbjudan. 

## <a name="receive-data"></a>Ta emot data

I följande avsnitt beskrivs hur du tar emot delade data.
### <a name="prerequisites-to-receive-data"></a>Förutsättningar för att ta emot data
Innan du godkänner en dataresursinbjudan kontrollerar du att följande krav är uppfyllda: 

* En Azure-prenumeration. Om du inte har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En inbjudan från Azure. E-postämnet ska vara "Azure Data Share inbjudan från *\<yourdataprovider\@domain.com>* ".
* En registrerad [Microsoft.DataShare-resursprovider](concepts-roles-permissions.md#resource-provider-registration) i:
    * Azure-prenumerationen där du skapar en Data Share resurs.
    * Azure-prenumerationen där dina Azure-måldatalager finns.

### <a name="prerequisites-for-a-target-storage-account"></a>Krav för ett mållagringskonto

* Ett Azure Storage-konto. Om du inte redan har ett skapar [du ett konto](../storage/common/storage-account-create.md). 
* Behörighet att skriva till lagringskontot. Den här behörigheten *finns i Microsoft.Storage/storageAccounts/write*. Det är en del av rollen Deltagare. 
* Behörighet att lägga till rolltilldelning till lagringskontot. Den här tilldelningen finns *i Microsoft.Authorization/role assignments/write*. Det är en del av rollen Ägare.  

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Öppna en inbjudan

Du kan öppna en inbjudan via e-post eller direkt från Azure Portal.

1. Om du vill öppna en inbjudan från ett e-postmeddelande kontrollerar du om det finns en inbjudan från dataleverantören i din inkorg. Inbjudan från Microsoft Azure heter "Azure Data Share inbjudan från *\<yourdataprovider\@domain.com>* ". Välj **Visa inbjudan** för att se din inbjudan i Azure. 

   Om du vill öppna en inbjudan Azure Portal du efter *Data Share inbjudningar*. Du ser en lista över Data Share inbjudningar.

   ![Skärmbild som visar listan över inbjudningar i Azure Portal.](./media/invitations.png "Lista över inbjudningar.") 

1. Välj den resurs som du vill visa. 

### <a name="accept-an-invitation"></a>Acceptera en inbjudan
1. Granska alla fält, inklusive **Användningsvillkor**. Om du godkänner villkoren markerar du kryssrutan. 

   ![Skärmbild som visar Användningsvillkor området.](./media/terms-of-use.png "Användningsvillkor.") 

1. Under **Målkonto Data Share väljer** du den prenumeration och resursgrupp där du ska distribuera Data Share. Fyll sedan i följande fält:

   * I fältet **Dataresurskonto** väljer du **Skapa nytt** om du inte har ett Data Share konto. Annars väljer du ett befintligt Data Share som accepterar din dataresurs. 

   * I fältet **Mottaget resursnamn** lämnar du antingen standardinställningen som dataleverantören angav eller anger ett nytt namn för den mottagna resursen. 

1. Välj **Acceptera och konfigurera**. En resursprenumeration skapas. 

   ![Skärmbild som visar var du godkänner konfigurationsalternativen.](./media/accept-options.png "Acceptera alternativ") 

    Den mottagna resursen visas i ditt Data Share konto. 

    Om du inte vill acceptera inbjudan väljer du **Avvisa**. 

### <a name="configure-a-received-share"></a>Konfigurera en mottagen resurs
Följ stegen i det här avsnittet för att konfigurera en plats för att ta emot data.

1. På fliken **Datauppsättningar** markerar du kryssrutan bredvid den datauppsättning där du vill tilldela ett mål. Välj **Mappa till mål** för att välja ett måldatalager. 

   ![Skärmbild som visar hur du mappar till ett mål.](./media/dataset-map-target.png "Mappa till målet.") 

1. Välj ett måldatalager för data. Filer i måldatalagret som har samma sökväg och namn som filer i mottagna data skrivs över. 

   ![Skärmbild som visar var du väljer ett mållagringskonto.](./media/map-target.png "Mållagring.") 

1. För ögonblicksbildbaserad delning kan du aktivera schemat på fliken Schema för ögonblicksbilder om dataleverantören använder ett schema för **ögonblicksbilder** för att regelbundet uppdatera data. Markera rutan bredvid schemat för ögonblicksbilden. Välj sedan **Aktivera**. Observera att den första schemalagda ögonblicksbilden startar inom en minut från schematiden och efterföljande ögonblicksbilder startar inom några sekunder från den schemalagda tiden.

   ![Skärmbild som visar hur du aktiverar ett schema för ögonblicksbilder.](./media/enable-snapshot-schedule.png "Aktivera schema för ögonblicksbilder.")

### <a name="trigger-a-snapshot"></a>Utlösa en ögonblicksbild
Stegen i det här avsnittet gäller endast för ögonblicksbildbaserad delning.

1. Du kan utlösa en ögonblicksbild från **fliken** Information. På fliken väljer du Trigger **snapshot (Utlösa ögonblicksbild).** Du kan välja att utlösa en fullständig ögonblicksbild eller inkrementell ögonblicksbild av dina data. Om du tar emot data från dataleverantören för första gången väljer du **Fullständig kopia.** När en ögonblicksbild körs startar inte efterföljande ögonblicksbilder förrän den föregående har slutförts.

   ![Skärmbild som visar valet Avlösa ögonblicksbild.](./media/trigger-snapshot.png "Ögonblicksbild av utlösare.") 

1. När den senaste *körningsstatusen är lyckad* går du till måldatalagret för att visa mottagna data. Välj **Datauppsättningar** och välj sedan länken målsökväg. 

   ![Skärmbild som visar mappning av konsumentdatamängd.](./media/consumer-datasets.png "Mappning av konsumentdatamängd.") 

### <a name="view-history"></a>Visa historik
Du kan bara visa historiken för dina ögonblicksbilder i ögonblicksbildbaserad delning. Om du vill visa historiken öppnar **du fliken** Historik. Här ser du historiken för alla ögonblicksbilder som har genererats under de senaste 30 dagarna. 

## <a name="storage-snapshot-performance"></a>Prestanda för ögonblicksbilder av lagring
Prestanda för ögonblicksbilder av lagring påverkas av ett antal faktorer utöver antalet filer och storleken på delade data. Vi rekommenderar alltid att du utför dina egna prestandatester. Nedan visas några exempel på faktorer som påverkar prestanda.

* Samtidig åtkomst till käll- och måldatalager.  
* Plats för käll- och måldatalager. 
* För inkrementell ögonblicksbild kan antalet filer i den delade datauppsättningen påverka den tid det tar att hitta listan över filer med tiden för senaste ändring efter den senaste lyckade ögonblicksbilden. 


## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du delar och tar emot data från ett lagringskonto med hjälp av Azure Data Share tjänsten. Mer information om delning från andra datakällor finns i [Datalager som stöds.](supported-data-stores.md)
