---
title: Uppgradera ett Service Fabric program i PowerShell
description: Azure PowerShell skript exempel – uppgradera och övervaka ett Azure Service Fabric-program med hjälp av PowerShell.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 7b55dc6a400f936ac23b233e4c84a6b1aebf45cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784704"
---
# <a name="upgrade-a-service-fabric-application"></a>Uppgradera ett Service Fabric-program

Det här exempel skriptet uppgraderar en Service Fabric program instans som körs till version 1.3.0. Skriptet kopierar det nya programpaketet till klustrets avbildnings Arkiv, registrerar program typen och tar bort det onödiga programpaketet.  Skriptet startar en övervakad uppgradering och kontrollerar regelbundet uppgraderings statusen tills uppgraderingen har slutförts eller återställts. Anpassa parametrarna efter behov. 

Installera, om så behövs, Service Fabric PowerShell-modulen installeras med [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) | Hämtar alla program i Service Fabric-klustret eller ett särskilt program.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) | Hämtar status för en Service Fabric program uppgradering. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) | Hämtar de Service Fabric program typer som är registrerade i Service Fabric klustret. |
| [Avregistrera-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | Avregistrerar en Service Fabric program typ.  |
| [Kopiera – ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) | Kopierar ett Service Fabric-programpaket till avbildnings arkivet.  |
| [Registrera – ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) | Registrerar en Service Fabric program typ. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) | Uppgraderar ett Service Fabric program till den angivna program typ versionen. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage) | Tar bort ett Service Fabric-programpaket från avbildnings arkivet.|


## <a name="next-steps"></a>Nästa steg

Mer information om Service Fabric PowerShell-modulen finns i [Azure PowerShell dokumentation](/powershell/azure/service-fabric/overview).

Ytterligare Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
