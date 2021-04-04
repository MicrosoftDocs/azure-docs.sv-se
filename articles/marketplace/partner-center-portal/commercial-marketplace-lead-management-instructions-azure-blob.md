---
title: Hantering av leads med Azure Blob Storage – Microsoft Commercial Marketplace
description: Lär dig hur du använder Azure Blob för att konfigurera leads för Microsoft AppSource och Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/01/2020
ms.openlocfilehash: bd2f3d40b1aea1c0133f95e069ebfd527d30bd59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94491111"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Använd Azure Blob Storage för att hantera affärs marknads platser

>[!Caution]
>Stöd för kommersiella Marketplace för Azure Blob Storage är föråldrat och är inte längre ett alternativ för att bearbeta leads från ditt erbjudande. Om du för närvarande har ett erbjudande om kommersiellt marknads plats med hantering av lead som kon figurer ATS för Azure Blob får du inte längre kund leads. Uppdatera konfigurationen för hantering av lead till något av de andra alternativen för hantering av lead. Lär dig mer om de andra alternativen på [sidan för introduktions hanteringens landning](./commercial-marketplace-get-customer-leads.md). "

 Om ditt CRM-system (Customer Relations hip Management) inte uttryckligen stöds i Partner Center för att ta emot Microsoft AppSource och Azure Marketplace-leads, kan du använda Azure Blob Storage. Du kan sedan välja att exportera data och importera dem till ditt CRM-system. I instruktionerna i den här artikeln får du lära dig hur du skapar ett Azure Storage-konto och en BLOB under det kontot. Dessutom kan du skapa ett nytt flöde med hjälp av Automatisk inskrivning för att skicka ett e-postmeddelande när ditt erbjudande tar emot ett lead.

>[!NOTE]
>Den Energis par anslutning som används i dessa instruktioner kräver en betald prenumeration för att automatisera energi. Se till att du tar hänsyn till detta innan du följer anvisningarna i den här artikeln.

## <a name="configure-azure-blob-storage"></a>Konfigurera Azure Blob Storage

1. Om du inte har ett Azure-konto kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/pricing/free-trial/).

2. När ditt Azure-konto är aktivt loggar du in på [Azure Portal](https://portal.azure.com).

3. I Azure Portal skapar du ett lagrings konto med hjälp av följande procedur.  
    1. Välj **+ skapa en resurs** i den vänstra meny raden.  Det **nya** fönstret (bladet) kommer att visas till höger.
    2. Välj **lagring** i det **nya** fönstret.  En **aktuell** lista visas till höger.
    3. Välj **lagrings kontot** för att börja skapa konto.  Följ anvisningarna i artikeln [skapa ett lagrings konto](../../storage/common/storage-account-create.md?tabs=azure-portal).

    ![Steg för att skapa ett Azure Storage-konto](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Mer information om lagrings konton finns i den här [snabb](../../storage/blobs/storage-quickstart-blobs-portal.md)starten.  Mer information om lagrings priser finns i [Storage-priser](https://azure.microsoft.com/pricing/details/storage/).

4. Vänta tills ditt lagrings konto har tillhandahållits, en process som vanligt vis tar några minuter.  Kom sedan åt ditt lagrings konto från **Start** sidan för Azure Portal genom att välja **Visa alla resurser** eller genom att välja **alla resurser** i den vänstra navigerings meny raden i Azure Portal.

    ![Åtkomst till ditt Azure Storage-konto](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. I fönstret lagrings konto väljer du **åtkomst nycklar** och kopierar värdet för *anslutnings strängen* för nyckeln. Spara det här värdet eftersom det är det *lagrings kontots anslutnings sträng* värde som du måste ange i publicerings portalen för att ta emot leads för ditt Marketplace-erbjudande.

     Ett exempel på en anslutnings sträng är:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Skärm bild som visar sidan "åtkomst nycklar" med text rutan "anslutnings sträng" markerad.](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. På sidan lagrings konto väljer du **blobbar**.

   ![Skärm bild av sidan Azure Storage-konto med blobbar valda](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. På sidan blobbar väljer du knappen **+ container** .

8. Ange ett **namn** för den nya behållaren. Containernamnet får bara innehålla gemener, måste börja med en bokstav eller siffra och får bara innehålla bokstäver, siffror och bindestreck (-). Mer information om behållare och blob-namn finns i [namngivning och referens av behållare, blobbar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Spara det här värdet eftersom det är det *behållar namns* värde som du måste ange i publicerings portalen för att ta emot leads för ditt Marketplace-erbjudande.

9. Ange nivån för offentlig åtkomst till behållaren som **privat (ingen anonym åtkomst)**.

10. Klicka på **OK** för att skapa containern.

    ![Ny behållare](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Konfigurera ditt erbjudande för att skicka leads till Azure Blob Storage

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du stegen nedan:

1. Gå till sidan med **installations programmet** för erbjudandet.
2. Under avsnittet **kund leads** väljer du **Anslut**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="Kund ledare":::

3. I popup-fönstret anslutnings information väljer du Azure- **BLOB** för lead-målet.

    ![Anslutnings information](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Ange **behållar namnet** och **lagrings kontots anslutnings sträng** som du fick från att följa dessa anvisningar.

    * Exempel på container namn: `marketplaceleadcontainer`
    * Exempel på anslutnings sträng för lagrings konto: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![ anslutnings information](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Välj **Spara**.

    > [!NOTE]
    > Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet.