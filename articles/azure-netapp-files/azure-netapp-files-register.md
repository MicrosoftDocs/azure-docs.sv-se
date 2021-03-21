---
title: Registrera för Azure NetApp Files| Microsoft Docs
description: Lär dig hur du registrerar dig för Azure NetApp Files genom att skicka en Waitlist-begäran och registrera Azure Resource Provider för Azure NetApp Files.
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
ms.date: 06/09/2020
ms.author: b-juche
ms.openlocfilehash: a61d6ba1f908a7dae56db066bfae329fc26f1c5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696133"
---
# <a name="register-for-azure-netapp-files"></a>Registrera för Azure NetApp Files

> [!IMPORTANT] 
> Innan du registrerar Azure NetApp Files Resource Provider måste du ha fått ett e-postmeddelande från Azure NetApp Filess teamet som bekräftar att du har beviljats åtkomst till tjänsten. 

I den här artikeln får du lära dig hur du registrerar dig för Azure NetApp Files så att du kan börja använda tjänsten.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Skicka en Waitlist-begäran om åtkomst till tjänsten

1. Gå till följande sida och skicka en Waitlist-begäran för att få åtkomst till tjänsten Azure NetApp Files:  
    [**Azure NetApp Files sändnings sida för Waitlist**](https://aka.ms/azurenetappfiles) 

    Waitlist-registrering garanterar inte omedelbar åtkomst till tjänsten. 

2. Vänta en officiell bekräftelse via e-post från Azure NetApp Files-teamet innan du fortsätter med andra uppgifter. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>Registrera resursprovidern för NetApp

För att kunna använda tjänsten behöver du registrera Azure-resursprovidern för Azure NetApp Files.

> [!NOTE] 
> Du kommer att kunna registrera NetApp-resurs leverantören trots att den inte har beviljats åtkomst till tjänsten. Men utan åtkomst behörighet avvisas eventuellt Azure Portal-eller API-begäran för att skapa ett NetApp-konto eller någon annan Azure NetApp Files resurs med följande fel:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. På Azure-portalen klickar du på Azure Cloud Shell-ikonen i det övre högra hörnet:

      ![Azure Cloud Shell-ikonen](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Om du har flera prenumerationer på ditt Azure-konto väljer du det som har godkänts för Azure NetApp Files:
    
    ```azurepowershell
    az account set --subscription <subscriptionId>
    ```

3. I Azure Cloud Shell-konsolen anger du följande kommando för att kontrol lera att din prenumeration har godkänts:
    
    ```azurepowershell
    az feature list | grep NetApp
    ```

   Utdata från kommandot visas på följande sätt:
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` är ditt prenumerations-ID.

    Om du inte ser funktions namnet `Microsoft.NetApp/ANFGA` har du inte åtkomst till tjänsten. Stoppa i det här steget. Följ anvisningarna i [skicka en Waitlist-begäran om åtkomst till tjänsten](#waitlist) för att begära åtkomst till tjänsten innan du fortsätter. 

4. I Azure Cloud Shell-konsolen anger du följande kommando för att registrera Azure-resursprovidern: 
    
    ```azurepowershell
    az provider register --namespace Microsoft.NetApp --wait
    ```

   Parametern `--wait` instruerar konsolen att vänta tills registreringen är klar. Registreringsprocessen kan ta lite tid att slutföra.

5. I Azure Cloud Shell-konsolen anger du följande kommando för att kontrollera att Azure-resursprovidern har registrerats: 
    
    ```azurepowershell
    az provider show --namespace Microsoft.NetApp
    ```

   Utdata från kommandot visas på följande sätt:
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` är ditt prenumerations-ID.  `state`-parametervärdet anger `Registered`.

6. På Azure-portalen klickar du på bladet **Prenumerationer**.
7. På bladet Prenumerationer klickar du på ditt prenumerations-ID. 
8. I inställningarna för prenumerationen klickar du på **Resursprovidrar** för att kontrollera att Microsoft.NetApp-providern anger statusen Registrerad: 

      ![Registrerad Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Nästa steg

[Skapa ett NetApp-konto](azure-netapp-files-create-netapp-account.md)
