---
title: Azure Service Fabric standalone-paket för Windows Server
description: Beskrivning och innehåll för det fristående Azure Service Fabric-paketet för Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: fb9a52510788a275bdeea67cd5c1fdd2e894738d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86261015"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Innehåll i Service Fabric fristående paket för Windows Server
[I det fristående](https://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric fristående paketet hittar du följande filer:

| **Filnamn** | **Kort beskrivning** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Ett PowerShell-skript som skapar klustret med hjälp av inställningarna i ClusterConfig.jspå. |
| RemoveServiceFabricCluster.ps1 |Ett PowerShell-skript som tar bort ett kluster med hjälp av inställningarna i ClusterConfig.jspå. |
| AddNode.ps1 |Ett PowerShell-skript för att lägga till en nod i ett befintligt distribuerat kluster på den aktuella datorn. |
| RemoveNode.ps1 |Ett PowerShell-skript för att ta bort en nod från ett befintligt distribuerat kluster från den aktuella datorn. |
| CleanFabric.ps1 |Ett PowerShell-skript för att rensa en fristående Service Fabric installation av den aktuella datorn. Tidigare MSI-installationer bör tas bort med hjälp av sina egna associerade avinstallationer. |
| TestConfiguration.ps1 |Ett PowerShell-skript för att analysera infrastrukturen som anges i Cluster.jspå. |
| DownloadServiceFabricRuntimePackage.ps1 |Ett PowerShell-skript som används för att hämta det senaste Runtime-paketet out-of-band, för scenarier där distributions datorn inte är ansluten till Internet. |
| DeploymentComponentsAutoextractor.exe |Självextraherande arkivering som innehåller distributions komponenter som används av de fristående paket skripten. |
| EULA_ENU.txt |Licens villkoren för användning av Microsoft Azure Service Fabric fristående Windows Server-paket. Du kan [Ladda ned en kopia av licens avtalet](https://go.microsoft.com/fwlink/?LinkID=733084) nu. |
| Readme.txt |En länk till viktig information och grundläggande installations anvisningar. Det är en del av instruktionerna i det här dokumentet. |
| ThirdPartyNotice. RTF |Meddelande om program vara från tredje part som ingår i paketet. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe som körs på begäran för att samla in och överföra spårnings loggar till Microsoft för support ändamål. |
| Tools\ServiceFabricUpdateService.zip |Ett verktyg som används för att aktivera automatisk kod uppgradering för kluster som inte har till gång till Internet. [Här](service-fabric-cluster-upgrade-windows-server.md) hittar du mer information|

**Mallar** 

| **Filnamn** | **Kort beskrivning** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.jspå |En exempel fil för kluster konfiguration som innehåller inställningarna för en oskyddad, tre-nods (eller virtuell dator) utvecklings kluster, inklusive information för varje nod i klustret. |
| ClusterConfig.Unsecure.MultiMachine.jspå |En exempel fil för kluster konfiguration som innehåller inställningarna för ett oskyddat kluster med flera datorer (eller virtuella datorer), inklusive information för varje dator i klustret. |
| ClusterConfig.Windows.DevCluster.jspå |En exempel fil för kluster konfiguration som innehåller alla inställningar för ett säkert utvecklings kluster med tre noder, en dator (eller virtuell dator), inklusive information om varje nod i klustret. Klustret skyddas med hjälp av [Windows-identiteter](/previous-versions/msp-n-p/ff649396(v=pandp.10)). |
| ClusterConfig.Windows.MultiMachine.jspå |En exempel fil för kluster konfiguration som innehåller alla inställningar för ett säkert kluster med flera datorer (eller virtuella datorer) som använder Windows-säkerhet, inklusive information för varje dator som finns i det säkra klustret. Klustret skyddas med hjälp av [Windows-identiteter](/previous-versions/msp-n-p/ff649396(v=pandp.10)). |
| ClusterConfig.x509.DevCluster.jspå |En exempel fil för kluster konfiguration som innehåller alla inställningar för ett säkert utvecklings kluster med tre noder, en dator (eller virtuell dator), inklusive information om varje nod i klustret. Klustret skyddas med x509-certifikat. |
| ClusterConfig.x509.MultiMachine.jspå |En exempel fil för kluster konfiguration som innehåller alla inställningar för ett säkert kluster, flera datorer (eller virtuell dator), inklusive information för varje nod i det säkra klustret. Klustret skyddas med x509-certifikat. |
| ClusterConfig.gMSA.Windows.MultiMachine.jspå |En exempel fil för kluster konfiguration som innehåller alla inställningar för ett säkert kluster, flera datorer (eller virtuell dator), inklusive information för varje nod i det säkra klustret. Klustret skyddas med [grupphanterade tjänst konton](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)). |

## <a name="cluster-configuration-samples"></a>Kluster konfigurations exempel
De senaste versionerna av mallarna för kluster konfiguration finns på sidan GitHub: [exempel på fristående kluster konfiguration](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Oberoende körnings paket
Det senaste körnings paketet hämtas automatiskt vid kluster distribution från [nedladdnings Service Fabric runtime-Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Relaterat
* [Skapa ett fristående Azure Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md)
* [Service Fabric kluster säkerhets scenarier](service-fabric-windows-cluster-windows-security.md)
