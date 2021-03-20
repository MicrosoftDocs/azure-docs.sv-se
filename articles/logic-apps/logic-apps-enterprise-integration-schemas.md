---
title: Verifiera XML med scheman
description: Lägg till scheman för att verifiera XML-dokument i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75979366"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validera XML med scheman i Azure Logic Apps med Enterprise-integrationspaket

För att kontrol lera att dokumenten använder giltig XML och har förväntat data i det fördefinierade formatet för företags integrerings scenarier i Azure Logic Apps kan din Logic-app använda scheman. Ett schema kan också verifiera meddelanden som Logic Apps utbyter i scenarier för Business-to-Business (B2B).

För begränsningar som rör integrations konton och artefakter som scheman, se [gränser och konfigurations information för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* Ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) där du lagrar scheman och andra artefakter för företags integrering och B2B-lösningar (Business-to-Business). 

  Om schemat är [2 MB eller mindre](#smaller-schema)kan du lägga till schemat i integrations kontot direkt från Azure Portal. Men om schemat är större än 2 MB men inte större än [schemats storleks gräns](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), kan du överföra schemat till ett Azure Storage-konto. 
  Om du vill lägga till schemat i integrations kontot kan du sedan länka till ditt lagrings konto från integrations kontot. 
  För den här uppgiften är de objekt som du behöver: 

  * [Azure Storage-konto](../storage/common/storage-account-overview.md) där du skapar en BLOB-behållare för ditt schema. Lär dig hur du [skapar ett lagrings konto](../storage/common/storage-account-create.md). 

  * BLOB-behållare för lagring av ditt schema. Lär dig hur du [skapar en BLOB-behållare](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Du behöver behållarens innehålls-URI senare när du lägger till schemat i integrations kontot.

  * [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md), som du kan använda för att hantera lagrings konton och blob-behållare. 
  Om du vill använda Storage Explorer väljer du något av alternativen här:
  
    * I Azure Portal söker du efter och väljer ditt lagrings konto. 
    Från menyn lagrings konto väljer du **Storage Explorer**.

    * [Hämta och installera Azure Storage Explorer](https://www.storageexplorer.com/)för Skriv bords versionen. 
    Anslut sedan Storage Explorer till ditt lagrings konto genom att följa stegen i [komma igång med Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Mer information finns i [snabb start: skapa en BLOB i objekt lagring med Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Du behöver inte en logisk app när du skapar och lägger till scheman. Men om du vill använda ett schema måste din Logic app länka till ett integrations konto där du lagrar schemat. Lär dig [hur du länkar Logic Apps till integrations konton](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Om du inte redan har en Logic app kan du läsa om [hur du skapar](../logic-apps/quickstart-create-first-logic-app-workflow.md)Logi Kap par.

## <a name="add-schemas"></a>Lägga till scheman

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a> med autentiseringsuppgifterna för ditt Azure-konto.

1. Om du vill söka efter och öppna ditt integrations konto väljer du **alla tjänster** på huvud menyn i Azure. I rutan Sök anger du "integrations konto". Välj **integrations konton**.

   ![Hitta integrations konto](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Välj det integrations konto där du vill lägga till ditt schema, till exempel:

   ![Välj integrations konto](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. På sidan **Översikt** för integrations kontot, under **komponenter**, väljer du panelen **scheman** .

   ![Välj "scheman"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. När sidan **scheman** öppnas väljer du **Lägg till**.

   ![Välj "Lägg till"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Baserat på schema filens storlek (XSD) följer du stegen för att ladda upp ett schema som är antingen [upp till 2 MB](#smaller-schema) eller [mer än 2 MB, upp till 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Lägg till scheman upp till 2 MB

1. Under **Lägg till schema** anger du ett namn för schemat. 
   Se till att **små filer** är markerade. Välj mappikonen bredvid rutan **schema** . Sök efter och välj det schema som du överför, till exempel:

   ![Ladda upp mindre schema](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. När du är klar väljer du **OK**.

   När schemat har slutfört överföringen visas schemat i listan **scheman** .

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Lägg till scheman över 2 MB

Om du vill lägga till större scheman kan du ladda upp schemat till en Azure Blob-behållare i ditt Azure Storage-konto. Stegen för att lägga till scheman skiljer sig åt beroende på om BLOB-behållaren har offentlig Läs behörighet. Först kontrollerar du om BLOB-behållaren har offentlig Läs behörighet genom att följa dessa steg: [Ange offentlig åtkomst nivå för BLOB-behållare](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Kontrol lera åtkomst nivå för behållare

1. Öppna Azure Storage Explorer. I Explorer-fönstret expanderar du din Azure-prenumeration om den inte redan har expanderats.

1. Expandera **lagrings konton** > {*ditt-Storage-Account*} > **BLOB-behållare**. Välj din BLOB-behållare.

1. Från BLOB-behållarens snabb meny väljer du **Ange offentlig åtkomst nivå**.

   * Om din BLOB-behållare har minst offentlig åtkomst väljer du **Avbryt** och följer dessa steg senare på den här sidan: [Ladda upp till behållare med offentlig åtkomst](#public-access)

     ![Offentlig åtkomst](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Om BLOB-behållaren inte har offentlig åtkomst väljer du **Avbryt** och följer de här stegen senare på den här sidan: [Ladda upp till behållare utan offentlig åtkomst](#public-access)

     ![Ingen offentlig åtkomst](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Ladda upp till behållare med offentlig åtkomst

1. Överför schemat till ditt lagrings konto. 
   I det högra fönstret väljer du **överför**.

1. När du har slutfört överföringen väljer du det överförda schemat. I verktygsfältet väljer du **Kopiera URL** så att du kan kopiera schemats URL.

1. Gå tillbaka till Azure Portal där fönstret **Lägg till schema** är öppet. 
   Ange ett namn för sammansättningen. 
   Välj **stor fil (större än 2 MB)**. 

   Rutan **innehålls-URI** visas nu i stället för rutan **schema** .

1. I rutan **innehålls-URI** klistrar du in ditt schemas URL. 
   Slutför tillägget av schemat.

När schemat har slutfört överföringen visas schemat i listan **scheman** . På sidan **Översikt** för integrations kontot, under **komponenter**, visar panelen **scheman** nu antalet överförda scheman.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Ladda upp till behållare utan offentlig åtkomst

1. Överför schemat till ditt lagrings konto. 
   I det högra fönstret väljer du **överför**.

1. När du har slutfört överföringen genererar du en signatur för delad åtkomst (SAS) för schemat. 
   Från schemats snabb meny väljer du **Hämta signatur för delad åtkomst**.

1. I fönstret **signatur för delad åtkomst** väljer du skapa **signatur-URI för delad åtkomst för container nivå**  >  . 
   När SAS-URL: en har genererats väljer du **Kopiera** bredvid rutan **URL** .

1. Gå tillbaka till Azure Portal där fönstret **Lägg till schema** är öppet. Välj **stor fil**.

   Rutan **innehålls-URI** visas nu i stället för rutan **schema** .

1. I rutan **innehålls-URI** klistrar du in SAS-URI: n som du skapade tidigare. Slutför tillägget av schemat.

När schemat har slutfört överföringen visas schemat i listan **scheman** . På sidan **Översikt** för integrations kontot, under **komponenter**, visar panelen **scheman** nu antalet överförda scheman.

## <a name="edit-schemas"></a>Redigera scheman

Om du vill uppdatera ett befintligt schema måste du ladda upp en ny schema fil som har de ändringar du önskar. Du kan dock först ladda ned det befintliga schemat för redigering.

1. Leta upp och öppna ditt integrations konto i <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, om det inte redan är öppet.

1. På huvud menyn i Azure väljer du **alla tjänster**. 
   I rutan Sök anger du "integrations konto". 
   Välj **integrations konton**.

1. Välj det integrations konto där du vill uppdatera schemat.

1. På sidan **Översikt** för integrations kontot, under **komponenter**, väljer du panelen **scheman** .

1. När sidan **scheman** öppnas väljer du ditt schema. 
   Hämta och redigera schemat först genom att välja **Hämta** och spara schemat.

1. När du är redo att ladda upp det uppdaterade schemat väljer du det schema som du vill uppdatera på sidan **scheman** och väljer **Uppdatera**.

1. Sök efter och välj det uppdaterade schema som du vill ladda upp. 
   När schema filen har slutfört överföringen visas det uppdaterade schemat i listan **scheman** .

## <a name="delete-schemas"></a>Ta bort scheman

1. Leta upp och öppna ditt integrations konto i <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, om det inte redan är öppet.

1. På huvud menyn i Azure väljer du **alla tjänster**. 
   I rutan Sök anger du "integrations konto". 
   Välj **integrations konton**.

1. Välj det integrations konto där du vill ta bort schemat.

1. På sidan **Översikt** för integrations kontot, under **komponenter**, väljer du panelen **scheman** .

1. När sidan **scheman** har öppnats väljer du ditt schema och väljer **ta bort**.

1. Bekräfta att du vill ta bort schemat genom att välja **Ja**.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Enterprise-integrationspaket](logic-apps-enterprise-integration-overview.md)
* [Läs mer om Maps](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Läs mer om transformeringar](../logic-apps/logic-apps-enterprise-integration-transform.md)
