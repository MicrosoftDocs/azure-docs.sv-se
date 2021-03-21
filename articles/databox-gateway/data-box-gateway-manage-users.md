---
title: Hantera användare för Azure Data Box Gateway | Microsoft Docs
description: Beskriver hur du använder Azure-portalen för att hantera användare i Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 9e941007ddc27f809de7d43cd33e44c5b521a6bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582479"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-gateway"></a>Använda Azure-portalen för att hantera användare i Azure Data Box Gateway

Den här artikeln beskriver hur du hanterar användare i din Azure Data Box Gateway. Du kan hantera Azure Data Box Gateway via Azure-portalen eller via det lokala webbgränssnittet. Använd Azure-portalen om du vill lägga till, ändra eller ta bort användare. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Lägga till en användare
> * Ändra användare
> * Ta bort en användare

## <a name="about-users"></a>Om användare

Användare kan ha skrivskyddad åtkomst eller fullständig behörighet. Som namnet antyder kan användare med skrivskyddad åtkomst endast visa data som delas. Användare med fullständig behörighet kan läsa data som delas, skriva till dessa resurser och ändra eller ta bort delade data.

 - **Användare med fullständig behörighet** – En lokal användare med fullständig åtkomst.
 - **Skrivskyddad användare** – En lokal användare med skrivskyddad åtkomst. Dessa användare är kopplade till resurser som tillåter skrivskyddade åtgärder.

Användarbehörigheterna definieras när användaren skapas i samband med att resurserna skapas. Ändring av behörigheter på resurs nivå stöds inte för närvarande.

## <a name="add-a-user"></a>Lägga till en användare

Lägg till en användare genom att följa de här instruktionerna på Azure-portalen.

1. I Azure Portal går du till Data Box Gateway resurs och navigerar sedan till **Översikt**. Klicka på **+ Lägg till användare** i kommandofältet.

    ![Klicka på lägg till användare](media/data-box-gateway-manage-users/add-user-1.png)

2. Ange användarnamnet och lösenordet för den användare som du vill lägga till. Bekräfta lösen ordet och klicka på **Lägg till**.

    ![Klicka på Lägg till användare 2](media/data-box-gateway-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Dessa användare är reserverade i systemet och bör inte användas: Administratör, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount och Gäst.  

3. Du meddelas när användargenereringen startar och när den är klar. När användaren har skapats klickar du på **Uppdatera** i kommandofältet för att visa den uppdaterade listan med användare.


## <a name="modify-user"></a>Ändra användare

Du kan ändra lösenordet som associeras med en användare när användaren har skapats. Välj och klicka i listan med användare. Ange och bekräfta det nya lösenordet. Spara ändringarna.
 
![Ändra användare](media/data-box-gateway-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Ta bort en användare

Utför följande steg på Azure-portalen om du vill ta bort en användare.

1. Markera och klicka på en användare i listan över användare och klicka sedan på **ta bort**.  

   ![Ta bort en användare](media/data-box-gateway-manage-users/delete-user-1.png)

2. Bekräfta borttagningen när du uppmanas att göra det. 

   ![Ta bort en användare 2](media/data-box-gateway-manage-users/delete-user-2.png)

Listan över användare uppdateras för att återspegla den borttagna användaren.

![Ta bort en användare 3](media/data-box-gateway-manage-users/delete-user-3.png)


## <a name="next-steps"></a>Nästa steg

- Lär dig att [hantera bandbredd](data-box-gateway-manage-bandwidth-schedules.md).
