---
title: Skapa ett Service Fabric kluster i PowerShell
description: Azure PowerShell skript exempel – skapa ett Service Fabric-kluster som skyddas med ett X. 509-certifikat.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/19/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: bae42c9ec8643da1563fb6b566d8db4af6039225
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078963"
---
# <a name="create-a-service-fabric-cluster"></a>Skapa ett Service Fabric-kluster

Det här exempelskriptet skapar ett Service Fabric-kluster med fem noder som skyddas med ett X.509-certifikat.  Kommandot skapar ett självsignerat certifikat och laddar upp det till ett nytt nyckelvalv. Certifikatet kopieras även till en lokal katalog.  Ange parametern *-OS* för att välja den version av Windows eller Linux som körs på klusternoderna.  Anpassa parametrarna efter behov.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/) och kör sedan `Connect-AzAccount` för att skapa en anslutning med Azure. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptet har körts kan följande kommando användas för att ta bort resursgruppen, klustret och alla relaterade resurser.

```powershell
$groupname="mysfclustergroup"
Remove-AzResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) | Skapar ett nytt Service Fabric-kluster. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare Azure Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
