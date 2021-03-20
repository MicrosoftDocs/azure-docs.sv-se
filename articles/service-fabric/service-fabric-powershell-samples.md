---
title: Azure PowerShell-exempel – Service Fabric
description: Lär dig mer om att skapa och hantera Azure Service Fabric-kluster,-appar och-tjänster med hjälp av PowerShell.
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: 4b85fd604eb27f0963af882b41e823d800005dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86187106"
---
# <a name="azure-service-fabric-powershell-samples"></a>Azure Service Fabric PowerShell-exempel

Följande tabell innehåller länkar till PowerShell-exempelskript för att hantera Service Fabric-kluster, appar och tjänster.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| Skript | Beskrivning |
|-|-|
| **Skapa kluster** ||
| [Skapa ett kluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Skapar ett Azure Service Fabric-kluster. |
| **Hantera kluster, noder och infrastruktur** ||
| [Lägga till ett appcertifikat](./scripts/service-fabric-powershell-add-application-certificate.md)| Skapar ett X509-certifikat för att Key Vault och distribuera det till en skalnings uppsättning för virtuella datorer i klustret. |
| [Uppdatera RDP-portintervallen på virtuella datorer i klustret](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Ändrar RDP-portintervallet på klusternodens virtuella datorer i ett distribuerat kluster.|
| [Uppdatera admin-användare och lösenord för virtuella datorer i klusternoden](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Uppdaterar admin-användare och lösenord för virtuella datorer i klusternoden. |
| [Öppna en port i lastbalanseraren](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Öppna en apport i Azure Load Balancer som tillåter inkommande trafik på en specifik port. |
| [Skapa en regel för inkommande nätverkssäkerhetsgrupper](./scripts/service-fabric-powershell-add-nsg-rule.md) | Skapa ett regel för inkommande nätverkssäkerhetsgrupper för att tillåta inkommande trafik till klustret på en specifik port. |
| **Hantera program** ||
| [Distribuera ett program](./scripts/service-fabric-powershell-deploy-application.md)| Distribuera ett program till ett kluster.|
| [Uppgradera ett program](./scripts/service-fabric-powershell-upgrade-application.md)| Uppgraderar ett program.|
| [Ta bort ett program](./scripts/service-fabric-powershell-remove-application.md)| Tar bort ett program från ett kluster.|
