---
title: Azure PowerShell exempel – återställa Azure Cloud Services (utökad support)
description: Exempel skript för att återställa en Azure Cloud service-distribution (utökad support)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a2c10e7915b0d293d3193c710885ded26a791f2c
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745137"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Återställa en Azure-molnbaserad tjänst (utökad support) 
Dessa exempel beskriver olika sätt att återställa en befintlig distribution av Azure Cloud Service (utökad support).

## <a name="reimage-role-instances-of-cloud-service"></a>Återställ avbildning av roll instanser för moln tjänsten
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances -Reimage
```
Detta kommando återavbildningar 2 roll instanser **ContosoFrontEnd \_ i \_ 0** och **ContosoBackEnd \_ i \_ 1** av moln tjänsten med namnet ContosoCS som tillhör resurs gruppen med namnet conto sorg.

## <a name="reimage-all-roles-of-cloud-service"></a>Återställ avbildningen av alla roller för moln tjänsten
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Reimage
```

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Återställa avbildningen av en enskild roll instans av en moln tjänst
```powershell
Reset-AzCloudServiceRoleInstance -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0" -Reimage
```

## <a name="restart-a-single-role-instance-of-a-cloud-service"></a>Starta om en enda roll instans av en moln tjänst
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Restart
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cloud Services (utökad support) finns i [Översikt över Azure Cloud Services (utökad support)](overview.md).