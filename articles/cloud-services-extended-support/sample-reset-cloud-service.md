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
ms.openlocfilehash: 5c43d61b1e7cd98674eab4c6d857cc1114a06013
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475327"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Återställa en Azure-molnbaserad tjänst (utökad support) 
Dessa exempel beskriver olika sätt att återställa en befintlig distribution av Azure Cloud Service (utökad support).

## <a name="reimage-role-instances-of-cloud-service"></a>Återställ avbildning av roll instanser för moln tjänsten
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Detta kommando återavbildningar 2 roll instanser ContosoFrontEnd_IN_0 och ContosoBackEnd_IN_1 av moln tjänsten med namnet ContosoCS som tillhör resurs gruppen med namnet conto sorg.

## <a name="reimage-all-roles-of-cloud-service"></a>Återställ avbildningen av alla roller för moln tjänsten
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Detta kommando återavbildar alla roll instanser av moln tjänsten med namnet ContosoCS som tillhör resurs gruppen med namnet conto sorg.

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Återställa avbildningen av en enskild roll instans av en moln tjänst
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
Det här kommandot avbildningar roll instansen med namnet ContosoFrontEnd_IN_0 av moln tjänsten med namnet ContosoCS som tillhör resurs gruppen med namnet conto sorg.

## <a name="rebuild-role-instances-of-cloud-service"></a>Återskapa roll instanser av moln tjänsten
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Detta kommando återskapar två roll instanser ContosoFrontEnd_IN_0 och ContosoBackEnd_IN_1 av moln tjänsten med namnet ContosoCS som tillhör resurs gruppen med namnet conto sorg.

## <a name="rebuild-all-roles-of-cloud-service"></a>Återskapa alla roller för moln tjänsten
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Det här kommandot återkonstruerar alla roll instanser av moln tjänsten med namnet ContosoCS som tillhör resurs gruppen med namnet conto sorg.

## <a name="restart-role-instances-of-cloud-service"></a>Starta om roll instanser av moln tjänsten
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Detta kommando startar om 2 roll instanser ContosoFrontEnd_IN_0 och ContosoBackEnd_IN_1 av moln tjänsten med namnet ContosoCS som tillhör resurs gruppen med namnet conto sorg.

## <a name="restart-all-roles-of-cloud-service"></a>Starta om alla roller för moln tjänsten
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Detta kommando startar om alla roll instanser av moln tjänsten med namnet ContosoCS som tillhör resurs gruppen med namnet conto sorg.

## <a name="next-steps"></a>Nästa steg

- Mer information om Azure Cloud Services (utökad support) finns i [Översikt över Azure Cloud Services (utökad support)](overview.md).
- Besök den [Cloud Services (utökad support) exempel lagrings plats](https://github.com/Azure-Samples/cloud-services-extended-support)
