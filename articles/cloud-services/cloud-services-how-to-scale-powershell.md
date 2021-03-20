---
title: Skala en Azure Cloud Service (klassisk) i Windows PowerShell | Microsoft Docs
description: form Lär dig hur du använder PowerShell för att skala en webb roll eller arbets roll i Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: a090da1933b0fcd6edb5b2415c773f9efcb27387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743312"
---
# <a name="how-to-scale-an-azure-cloud-service-classic-in-powershell"></a>Så här skalar du en Azure Cloud Service (klassisk) i PowerShell

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Du kan använda Windows PowerShell för att skala en webb roll eller arbets roll i eller ut genom att lägga till eller ta bort instanser.  

## <a name="log-in-to-azure"></a>Logga in på Azure

Innan du kan utföra några åtgärder för din prenumeration via PowerShell måste du logga in:

```powershell
Add-AzureAccount
```

Om du har flera prenumerationer som är kopplade till ditt konto kan du behöva ändra den aktuella prenumerationen beroende på var moln tjänsten finns. Om du vill kontrol lera den aktuella prenumerationen kör du:

```powershell
Get-AzureSubscription -Current
```

Om du behöver ändra den aktuella prenumerationen kör du:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Kontrol lera det aktuella instans antalet för rollen

Kör följande för att kontrol lera den aktuella status för din roll:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Du bör få tillbaka information om rollen, inklusive dess aktuella OS-version och antalet instanser. I det här fallet har rollen en enda instans.

![Information om rollen](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Skala ut rollen genom att lägga till fler instanser

Du kan skala ut rollen genom att skicka det önskade antalet instanser som **Count** -parametern till **set-AzureRole** -cmdleten:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Cmdleten blockerar tillfälligt medan de nya instanserna har tillhandahållits och startats. Om du i det här fallet öppnar ett nytt PowerShell-fönster och anropar **Get-AzureRole** som visas tidigare visas antalet nya mål instanser. Och om du inspekterar roll status i portalen bör du se att den nya instansen startar:

![VM-instans som startar i portalen](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

När de nya instanserna har startat kommer cmdleten att returnera:

![Roll instans ökning slutförd](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Skala i rollen genom att ta bort instanser

Du kan skala i en roll genom att ta bort instanser på samma sätt. Ange **Count** -parametern på **set-AzureRole** till det antal instanser som du vill ha när skalningen har slutförts.

## <a name="next-steps"></a>Nästa steg

Det går inte att konfigurera automatisk skalning för moln tjänster från PowerShell. Information om hur du gör det finns i [så här skalar du automatiskt en moln tjänst](cloud-services-how-to-scale-portal.md).
