---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: cf67ea58c4c13756fdc6e437883f12124ca47eb9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105880"
---
1. I [Azure Portal](https://portal.azure.com/)väljer du din Azure Stack Edge-resurs och går sedan till **översikten**. Enheten bör vara online. Gå till **Cloud Storage gateway > lagrings konton**.

2. Välj **+ Lägg till lagrings konto** i enhetens kommando fält. 

   ![Lägg till ett lagrings konto](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. I fönstret **Lägg till gräns lagrings konto** anger du följande inställningar:

    1. Ange ett unikt namn för gräns lagrings kontot på enheten. Lagrings konto namn får bara innehålla siffror och bokstäver i gemener. Specialtecken är inte tillåtna. Lagrings kontots namn måste vara unikt inom enheten (inte mellan enheterna).

    2. Ange en valfri beskrivning av informationen om data som lagrings kontot innehar.  
    
    3. Som standard mappas gräns lagrings kontot till ett Azure Storage-konto i molnet och data från lagrings kontot skickas automatiskt till molnet. Ange det Azure Storage-konto som ditt gräns lagrings konto är mappat till.

    4. Skapa en ny behållare eller Välj från en befintlig behållare i Azure Storage-kontot. Alla data från enheten som skrivs till lagrings kontot för Edge överförs automatiskt till den valda lagrings behållaren i det mappade Azure Storage kontot.

    5. När du har angett alla alternativ för lagrings kontot väljer du **Lägg till** för att skapa gränsen för lagrings kontot. Du får ett meddelande när Edge Storage-kontot har skapats. Det nya gräns lagrings kontot visas sedan i listan över lagrings konton i Azure Portal.

    <!--[Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->
    
4. Om du väljer det här nya lagrings kontot och går till **åtkomst nycklar** kan du hitta BLOB-tjänstens slut punkt och motsvarande lagrings konto namn. Kopiera den här informationen som de här värdena tillsammans med åtkomst nycklarna hjälper dig att ansluta till Edge Storage-kontot.

    ![Lägg till ett lagrings konto 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    Du får åtkomst nycklar genom [att ansluta till enhetens lokala API: er med hjälp av Azure Resource Manager](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md).