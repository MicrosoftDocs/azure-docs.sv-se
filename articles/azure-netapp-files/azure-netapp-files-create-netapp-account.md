---
title: Skapa ett NetApp-konto för åtkomst till Azure NetApp Files | Microsoft Docs
description: Beskriver hur du kommer åt Azure NetApp Files och skapar ett NetApp-konto så att du kan konfigurera en kapacitetspool och skapa en volym.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: e5e0c43aa8c4fbf0db89065e0fab93ee174a4ede
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006601"
---
# <a name="create-a-netapp-account"></a>Skapa ett NetApp-konto
Genom att skapa ett NetApp-konto kan du konfigurera en kapacitetspool och sedan skapa en volym. Du använder bladet Azure NetApp-filer för att skapa ett nytt NetApp-konto.

## <a name="before-you-begin"></a>Innan du börjar
Du måste ha fått ett e-postmeddelande från Azure NetApp Filess teamet som bekräftar att du har beviljats åtkomst till tjänsten. Se [skicka en Waitlist-begäran om åtkomst till tjänsten](azure-netapp-files-register.md#waitlist).

Du måste också ha registrerat din prenumeration för att använda NetApp-resurs leverantören. Se [Registrera NetApp Resource Provider](azure-netapp-files-register.md#resource-provider).

## <a name="steps"></a>Steg 

1. Logga in på Azure-portalen. 
2. Gå till bladet Azure NetApp-filer genom att använda någon av följande metoder:  
   * Sök efter **Azure NetApp Files** i sökrutan på Azure Portal.  
   * Klicka på **Alla tjänster** i navigeringen och filtrera sedan på Azure NetApp Files.  

   Du kan lägga till bladet Azure NetApp-filer som en favorit genom att klicka på stjärnikonen bredvid det. 

3. Klicka på **+ Lägg till** för att skapa ett nytt NetApp-konto.  
   Fönstret Nytt NetApp-konto visas.  

4. Ange följande information för NetApp-kontot: 
   * **Kontonamn**  
     Ange ett unikt namn för prenumerationen.
   * **Prenumeration**  
     Välj en prenumeration från dina befintliga prenumerationer.
   * **Resurs grupp**   
     Använd en befintlig resursgrupp eller skapa en ny.
   * **Plats**  
     Välj en region för kontot och dess underordnade resurser.  

     ![Nytt NetApp-konto](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Klicka på **Skapa**.     
   NetApp-kontot som du skapat visas nu på bladet Azure NetApp-filer. 

> [!NOTE] 
> Om du inte har beviljats åtkomst till Azure NetApp Files tjänsten visas följande fel meddelande när du försöker skapa det första NetApp-kontot:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Nästa steg  

[Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)

