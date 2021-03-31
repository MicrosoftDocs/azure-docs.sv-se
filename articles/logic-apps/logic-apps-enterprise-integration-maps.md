---
title: Transformera XML med XSLT Maps
description: Lägg till XSLT-mappningar för att transformera XML i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: 62c3d4533dd04dbb5a2ce0c73afa52b81d433913
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91570777"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformera XML med kartor i Azure Logic Apps med Enterprise-integrationspaket

Om du vill överföra XML-data mellan format för företagsintegreringsscenarier i Azure Logic Apps kan din logikapp använda kartor, eller mer specifikt, XSLT-kartor (Extensible Style sheet Language Transformations). En karta är ett XML-dokument som beskriver hur du konverterar data från ett XML-dokument till ett annat format. 

Anta till exempel att du regelbundet tar emot B2B-order eller fakturor från en kund som använder datum formatet YYYMMDD. Din organisation använder dock datum formatet MMDDYYY. Du kan definiera och använda en karta som omvandlar YYYMMDD datum format till MMDDYYY-formatet innan du lagrar order-eller faktura informationen i din kund aktivitets databas.

För begränsningar som rör integrations konton och artefakter som Maps, se [gränser och konfigurations information för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) där du lagrar dina kartor och andra artefakter för företags integrering och B2B-lösningar (Business-to-Business).

* Om kartan refererar till en extern sammansättning måste du ladda upp *både sammansättningen och kartan* till ditt integrations konto. Se till att du [*laddar upp din sammansättning först*](#add-assembly)och ladda sedan upp kartan som refererar till sammansättningen.

  Om din sammansättning är 2 MB eller mindre kan du lägga till din sammansättning i integrations kontot *direkt* från Azure Portal. Men om din sammansättning eller karta är större än 2 MB men inte större än [storleks gränsen för sammansättningar eller kartor](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), har du följande alternativ:

  * För sammansättningar behöver du en Azure Blob-behållare där du kan ladda upp din sammansättning och platsen för behållaren. På så sätt kan du ange platsen senare när du lägger till sammansättningen i integrations kontot. 
  För den här uppgiften behöver du följande objekt:

    | Objekt | Beskrivning |
    |------|-------------|
    | [Azure Storage-konto](../storage/common/storage-account-overview.md) | Skapa en Azure Blob-behållare för din sammansättning i det här kontot. Lär dig [hur du skapar ett lagrings konto](../storage/common/storage-account-create.md). |
    | Blobcontainer | I den här behållaren kan du ladda upp din sammansättning. Du behöver också den här behållarens plats när du lägger till sammansättningen i integrations kontot. Lär dig hur du [skapar en BLOB-behållare](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Med det här verktyget kan du enklare hantera lagrings konton och blob-behållare. Om du vill använda Storage Explorer [laddar du ned och installerar Azure Storage Explorer](https://www.storageexplorer.com/). Anslut sedan Storage Explorer till ditt lagrings konto genom att följa stegen i [komma igång med Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Mer information finns i [snabb start: skapa en BLOB i objekt lagring med Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Du kan också söka efter och välja ditt lagrings konto i Azure Portal. Från menyn lagrings konto väljer du **Storage Explorer**. |
    |||

  * För Maps kan du för närvarande lägga till större kartor genom att använda [Azure Logic Apps REST API-Maps](/rest/api/logic/maps/createorupdate).

Du behöver inte en logisk app när du skapar och lägger till Maps. Men om du vill använda en karta måste din Logic app länka till ett integrations konto där du lagrar kartan. Lär dig [hur du länkar Logic Apps till integrations konton](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Om du inte redan har en Logic app kan du läsa om [hur du skapar](../logic-apps/quickstart-create-first-logic-app-workflow.md)Logi Kap par.

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Lägg till refererade sammansättningar

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

1. Om du vill söka efter och öppna ditt integrations konto väljer du **alla tjänster** på huvud menyn i Azure. 
   I rutan Sök anger du "integrations konto". 
   Välj **integrations konton**.

   ![Hitta integrations konto](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Välj det integrations konto där du vill lägga till din sammansättning, till exempel:

   ![Välj integrations konto](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. På sidan **Översikt** för integrations kontot, under **komponenter**, väljer du panelen **sammansättningar** .

   ![Välj "sammansättningar"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. När sidan **sammansättningar** öppnas väljer du **Lägg till**.

   ![Skärm bild som visar knappen Lägg till på sidan sammansättningar.](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Baserat på sammansättnings filens storlek följer du stegen för att ladda upp en sammansättning som är antingen [upp till 2 MB](#smaller-assembly) eller [mer än 2 MB, men bara upp till 8 MB](#larger-assembly).
För begränsningar av sammansättnings mängder i integrations konton, se [gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Om du ändrar din sammansättning måste du också uppdatera kartan oavsett om kartan har ändrats eller inte.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Lägg till sammansättningar upp till 2 MB

1. Under **Lägg till sammansättning** anger du ett namn för sammansättningen. Se till att **små filer** är markerade. Välj mappikonen bredvid rutan **sammansättning** . Sök efter och välj den sammansättning som du överför, till exempel:

   ![Ladda upp mindre sammansättning](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   I egenskapen **sammansättnings namn** visas sammansättningens fil namn automatiskt när du har valt sammansättningen.

1. När du är klar väljer du **OK**.

   När sammansättnings filen har slutfört överföringen visas sammansättningen i listan **sammansättningar** .

   ![Lista över överförda sammansättningar](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   På sidan **Översikt** för integrations kontot, under **komponenter**, visar panelen **sammansättningar** nu antalet överförda sammansättningar, till exempel:

   ![Överförda sammansättningar](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Lägg till sammansättningar över 2 MB

Om du vill lägga till större sammansättningar kan du ladda upp din sammansättning till en Azure Blob-behållare i ditt Azure Storage-konto. Dina steg för att lägga till sammansättningar skiljer sig åt beroende på om BLOB-behållaren har offentlig Läs behörighet. Först kontrollerar du om BLOB-behållaren har offentlig Läs behörighet genom att följa dessa steg: [Ange offentlig åtkomst nivå för BLOB-behållare](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Kontrol lera åtkomst nivå för behållare

1. Öppna Azure Storage Explorer. I Explorer-fönstret expanderar du din Azure-prenumeration om den inte redan har expanderats.

1. Expandera **lagrings konton** > {*ditt-Storage-Account*} > **BLOB-behållare**. Välj din BLOB-behållare.

1. Från BLOB-behållarens snabb meny väljer du **Ange offentlig åtkomst nivå**.

   * Om din BLOB-behållare har minst offentlig åtkomst väljer du **Avbryt** och följer dessa steg senare på den här sidan: [Ladda upp till behållare med offentlig åtkomst](#public-access-assemblies)

     ![Offentlig åtkomst](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Om BLOB-behållaren inte har offentlig åtkomst väljer du **Avbryt** och följer de här stegen senare på den här sidan: [Ladda upp till behållare utan offentlig åtkomst](#no-public-access-assemblies)

     ![Ingen offentlig åtkomst](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Ladda upp till behållare med offentlig åtkomst

1. Ladda upp sammansättningen till ditt lagrings konto. 
   I det högra fönstret väljer du **överför**.

1. När du har slutfört överföringen väljer du den överförda sammansättningen. I verktygsfältet väljer du **Kopiera URL** så att du kopierar sammansättningens URL.

1. Gå tillbaka till Azure Portal där fönstret **Lägg till sammansättning** är öppet. 
   Ange ett namn för sammansättningen. 
   Välj **stor fil (större än 2 MB)**.

   Rutan **innehålls-URI** visas nu i stället för rutan **sammansättning** .

1. I rutan **innehålls-URI** klistrar du in din sammansättnings URL. 
   Slutför tillägget av sammansättningen.

När sammansättningen har slutfört överföringen visas schemat i listan **sammansättningar** .
På sidan **Översikt** för integrations kontot, under **komponenter**, visar panelen **sammansättningar** nu antalet överförda sammansättningar.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Ladda upp till behållare utan offentlig åtkomst

1. Ladda upp sammansättningen till ditt lagrings konto. 
   I det högra fönstret väljer du **överför**.

1. När du har slutfört överföringen genererar du en signatur för delad åtkomst (SAS) för din sammansättning. 
   Från din sammansättnings snabb meny väljer du **Hämta signatur för delad åtkomst**.

1. I fönstret **signatur för delad åtkomst** väljer du skapa **signatur-URI för delad åtkomst för container nivå**  >  . 
   När SAS-URL: en har genererats väljer du **Kopiera** bredvid rutan **URL** .

1. Gå tillbaka till Azure Portal där fönstret **Lägg till sammansättning** är öppet. 
   Ange ett namn för sammansättningen. 
   Välj **stor fil (större än 2 MB)**.

   Rutan **innehålls-URI** visas nu i stället för rutan **sammansättning** .

1. I rutan **innehålls-URI** klistrar du in SAS-URI: n som du skapade tidigare. Slutför tillägget av sammansättningen.

När sammansättningen har slutfört överföringen visas sammansättningen i listan **scheman** . På sidan **Översikt** för integrations kontot, under **komponenter**, visar panelen **sammansättningar** nu antalet överförda sammansättningar.

## <a name="create-maps"></a>Skapa kartor

Om du vill skapa ett XSLT-dokument som du kan använda som en karta kan du använda Visual Studio 2015 för att skapa ett BizTalk-integrations projekt med hjälp av [Enterprise-integrationspaket](logic-apps-enterprise-integration-overview.md). I det här projektet kan du bygga en integrations kart fil som gör det möjligt att visuellt mappa objekt mellan två XML-schemafiler. När du har skapat det här projektet får du ett XSLT-dokument.
För gränser för mappnings antalet i integrations konton, se [gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Lägg till kartor

När du har laddat upp sammansättningar som kartan refererar till kan du ladda upp kartan.

1. Om du inte redan har loggat in loggar du in på [Azure Portal](https://portal.azure.com) med dina autentiseringsuppgifter för Azure-kontot. 

1. Om ditt integrations konto inte redan är öppet väljer du **alla tjänster** på huvud menyn i Azure. 
   I rutan Sök anger du "integrations konto". 
   Välj **integrations konton**.

   ![Hitta integrations konto](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Välj det integrations konto där du vill lägga till kartan, till exempel:

   ![Välj integrations konto](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. På sidan **Översikt** för integrations kontot under **komponenter** väljer du panelen **kartor** .

   ![Välj "Maps"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. När sidan **kartor** öppnas väljer du **Lägg till**.

   ![Välj "Lägg till"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Lägg till kartor upp till 2 MB

1. Under **Lägg till karta** anger du ett namn för kartan. 

1. Under **kart typ** väljer du typ, till exempel: **flytande**, **XSLT**, **XSLT 2,0** eller **XSLT 3,0**.

1. Se till att **små filer** är markerade. Välj mappikonen bredvid rutan **karta** . Sök efter och välj den karta som du överför, till exempel:

   ![Överförings karta](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Om du lämnar **namn** egenskapen tom visas mappningens fil namn automatiskt i den egenskapen när du har valt mappnings filen. 
   Du kan dock använda valfritt unikt namn.

1. När du är klar väljer du **OK**. 
   När mappnings filen har slutfört överföringen visas kartan i listan **Maps** .

   ![Överförda Maps-lista](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   På sidan **Översikt** för integrations kontot, under **komponenter**, visar panelen **kartor** nu antalet uppladdade kartor, till exempel:

   ![Överförda kartor](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Lägg till kartor över 2 MB

För närvarande, för att lägga till större Maps, använder du [Azure Logic Apps REST API-Maps](/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Redigera kartor

Om du vill uppdatera en befintlig karta måste du ladda upp en ny mappnings fil som har de ändringar du önskar. Du kan dock först ladda ned den befintliga kartan för redigering.

1. Leta upp och öppna ditt integrations konto i [Azure Portal](https://portal.azure.com), om det inte redan är öppet.

1. På huvud menyn i Azure väljer du **alla tjänster**. I rutan Sök anger du "integrations konto". Välj **integrations konton**.

1. Välj det integrations konto där du vill uppdatera kartan.

1. På sidan **Översikt** för integrations kontot under **komponenter** väljer du panelen **kartor** .

1. När sidan **kartor** öppnas väljer du kartan. 
   Hämta och redigera kartan först genom att välja **Hämta** och spara kartan.

1. När du är redo att ladda upp den uppdaterade kartan väljer du den mapp som du vill uppdatera på sidan **kartor** och väljer **Uppdatera**.

1. Sök efter och välj den uppdaterade mappning som du vill ladda upp. 
   När mappnings filen har slutfört överföringen visas den uppdaterade kartan i listan **Maps** .

## <a name="delete-maps"></a>Ta bort kartor

1. Leta upp och öppna ditt integrations konto i [Azure Portal](https://portal.azure.com), om det inte redan är öppet.

1. På huvud menyn i Azure väljer du **alla tjänster**. 
   I rutan Sök anger du "integrations konto". 
   Välj **integrations konton**.

1. Välj det integrations konto där du vill ta bort kartan.

1. På sidan **Översikt** för integrations kontot under **komponenter** väljer du panelen **kartor** .

1. När sidan **kartor** öppnas väljer du kartan och sedan **ta bort**.

1. Bekräfta att du vill ta bort kartan genom att välja **Ja**.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Läs mer om scheman](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Läs mer om transformeringar](../logic-apps/logic-apps-enterprise-integration-transform.md)
