---
title: Skapa, Visa och hantera system ämnen i Azure Event Grid (portal)
description: Den här artikeln visar hur du visar det befintliga system avsnittet Skapa Azure Event Grid system ämnen med hjälp av Azure Portal.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2dbb0893668d72cfebf2d64e6515ff6ab6fed9bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86115119"
---
# <a name="create-view-and-manage-event-grid-system-topics-in-the-azure-portal"></a>Skapa, Visa och hantera Event Grid system ämnen i Azure Portal
Den här artikeln visar hur du skapar och hanterar system ämnen med hjälp av Azure Portal. En översikt över system ämnen finns i [system avsnitt](system-topics.md).

## <a name="create-a-system-topic"></a>Avsnittet Skapa ett system
Du kan skapa ett system ämne för en Azure-resurs (lagrings konto, Event Hubs namnrymd osv.) på två sätt:

- Med hjälp av sidan **händelser** i en resurs, till exempel lagrings konto eller Event Hubs namnrymd. När du använder sidan **händelser** i Azure Portal för att skapa en händelse prenumeration för en händelse som aktive ras av en Azure-källa (till exempel: Azure Storage konto), skapar portalen ett system avsnitt för Azure-resursen och skapar sedan en prenumeration för system-avsnittet. Du anger namnet på system avsnittet om du skapar en händelse prenumeration på Azure-resursen för första gången. Från och med kan du Visa namnet på system avsnittet i skrivskyddat läge. Se [snabb start: dirigera Blob Storage-händelser till webb slut punkt med Azure Portal](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage) för detaljerade steg.
- Använd sidan **Event Grid system ämnen** . Följande steg beskriver hur du skapar ett system avsnitt på sidan **Event Grid Systems ämnen** . 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I rutan Sök högst upp skriver du **Event Grid system ämnen** och trycker sedan på **RETUR**. 

    ![Sök efter system avsnitt](./media/create-view-manage-system-topics/search-system-topics.png)
3. På sidan **Event Grid system ämnen** väljer du **+ Lägg till** i verktygsfältet.

    ![Lägg till system ämne – verktygsfälts knapp](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. Utför följande steg på sidan **skapa Event Grid system system** :
    1. Välj **ämnes typen**. I följande exempel väljs alternativet **lagrings konton** . 
    2. Välj den **Azure-prenumeration** som har lagrings konto resursen. 
    3. Välj den **resurs grupp** som har lagrings kontot. 
    4. Välj **lagrings konto**. 
    5. Ange ett **namn** för det system ämne som ska skapas. 
    
        > [!NOTE]
        > Du kan använda det här namnet i system avsnittet för att söka efter statistik och diagnostikloggar.
    6. Välj **Granska + skapa**.

        ![Avsnittet skapa system](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. Granska inställningarna och välj **skapa**. 
        
        ![Avsnittet granska och skapa system](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. När distributionen har slutförts väljer du **gå till resurs** för att se **ämnes sidan Event Grid system** i avsnittet system som du har skapat. 

        ![Sidan system ämne](./media/create-view-manage-system-topics/system-topic-page.png)


## <a name="view-all-system-topics"></a>Visa alla system ämnen
Följ dessa steg om du vill visa alla befintliga Event Grid system ämnen. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I rutan Sök högst upp skriver du **Event Grid system ämnen** och trycker sedan på **RETUR**. 

    ![Sök efter system avsnitt](./media/create-view-manage-system-topics/search-system-topics.png)
3. På sidan **Event Grid system ämnen** visas alla system ämnen. 

    ![Lista över system ämnen](./media/create-view-manage-system-topics/list-system-topics.png)
4. Välj ett **system ämne** i listan om du vill se information om det. 

    ![Information om system ämne](./media/create-view-manage-system-topics/system-topic-details.png)

    Den här sidan visar information om system avsnittet, till exempel följande information: 
    - Källicensservern. Namnet på resursen som avsnittet system skapades på.
    - Källtyp. Typ av resurs. Till exempel: `Microsoft.Storage.StorageAccounts` , `Microsoft.EventHub.Namespaces` , `Microsoft.Resources.ResourceGroups` och så vidare.
    - Alla prenumerationer som skapats för system avsnittet.

    Den här sidan tillåter åtgärder som följande:
    - Skapa en händelse prenumeration Välj **+ händelse prenumeration** i verktygsfältet. 
    - Ta bort en händelse prenumeration. Välj **ta bort** i verktygsfältet. 
    - Lägg till taggar i avsnittet system. Välj **taggar** i den vänstra menyn och ange taggnamn och värden. 


## <a name="delete-a-system-topic"></a>Ta bort ett system ämne
1. Följ anvisningarna i avsnittet [Visa system ämnen](#view-all-system-topics) för att visa alla system ämnen och välj det system som du vill ta bort från listan. 
2. På **avsnittet Event Grid system** på sidan väljer du **ta bort** i verktygsfältet. 

    ![System ämne – knappen Ta bort](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. På sidan bekräftelse väljer du **OK** för att bekräfta borttagningen. Det tar bort system avsnittet och även alla händelse prenumerationer för system avsnittet.  

## <a name="create-an-event-subscription"></a>Skapa en händelseprenumeration
1. Följ anvisningarna i avsnittet [Visa system ämnen](#view-all-system-topics) för att visa alla system ämnen och välj det system som du vill ta bort från listan. 
2. På **avsnittet Event Grid system** på sidan väljer du **+ händelse prenumeration** i verktygsfältet. 

    ![System ämne – knappen Lägg till händelse prenumeration](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. Bekräfta att **ämnes typen**, **käll resursen** och **ämnes namnet** fylls i automatiskt. Ange ett namn, Välj en **slut punkts typ** och ange **slut punkten**. Välj sedan **skapa** för att skapa händelse prenumerationen. 

    ![System ämne – skapa händelse prenumeration](./media/create-view-manage-system-topics/create-event-subscription.png)

## <a name="next-steps"></a>Nästa steg
Mer information om system ämnen och ämnes typer som stöds av Azure Event Grid finns i avsnittet [system ämnen i Azure Event Grid](system-topics.md) . 
