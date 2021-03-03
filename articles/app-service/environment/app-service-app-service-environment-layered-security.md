---
title: Skiktad säkerhet v1
description: Lär dig hur du implementerar en lager säkerhets arkitektur i din App Services miljö. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 813160ee19c8447fdfcb705e7a9162a41a0c308c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739516"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementera en lager säkerhets arkitektur med App Service miljöer
Eftersom App Service miljöer tillhandahåller en isolerad körnings miljö som distribuerats till ett virtuellt nätverk, kan utvecklare skapa en lager säkerhets arkitektur som ger olika nivåer av nätverks åtkomst för varje fysisk program nivå.

En vanlig önskan är att dölja API-backend-ändar från allmän Internet åtkomst och bara tillåta att API: er anropas av överordnade webb program.  [Nätverks säkerhets grupper (NSG: er)][NetworkSecurityGroups] kan användas på undernät som innehåller App Service miljöer för att begränsa offentlig åtkomst till API-program.

Diagrammet nedan visar en exempel arkitektur med en WebAPI-baserad app som distribuerats på en App Service-miljön.  Tre separata Web App-instanser, distribuerade i tre separata App Service miljöer, gör backend-anrop till samma WebAPI-app.

![Konceptuell arkitektur][ConceptualArchitecture] 

De gröna plus tecknen indikerar att nätverks säkerhets gruppen i under nätet som innehåller "apiase" tillåter inkommande anrop från de överordnade webbapparna, samt anrop från sig själva.  Samma nätverks säkerhets grupp nekar dock uttryckligen åtkomst till allmän inkommande trafik från Internet. 

Resten av den här artikeln vägleder dig genom de steg som krävs för att konfigurera nätverks säkerhets gruppen på under nätet som innehåller "apiase".

## <a name="determining-the-network-behavior"></a>Avgöra nätverks beteendet
För att veta vilka nätverks säkerhets regler som behövs måste du bestämma vilka nätverks klienter som ska få åtkomst till den App Service-miljön som innehåller API-appen och vilka klienter som ska blockeras.

Eftersom [nätverks säkerhets grupper (NSG: er)][NetworkSecurityGroups] tillämpas på undernät och App Service miljöer distribueras i undernät, gäller reglerna i en NSG för **alla** appar som körs på en app service-miljön.  Med hjälp av exempel arkitekturen för den här artikeln när en nätverks säkerhets grupp tillämpas på under nätet som innehåller "apiase", skyddas alla appar som körs på "apiase"-App Service-miljön av samma uppsättning säkerhets regler. 

* **Bestäm utgående IP-adress för överordnade anropare:**  Vad är IP-adressen eller adresserna för de överordnade anroparna?  De här adresserna måste uttryckligen beviljas åtkomst i NSG.  Eftersom anropen mellan App Service miljöer betraktas som "Internet-anrop" måste den utgående IP-adress som tilldelats var och en av de tre överordnade App Service-miljöerna beviljas åtkomst i NSG för under nätet "apiase".   Mer information om hur du avgör utgående IP-adress för appar som körs i en App Service-miljön finns i översikts artikeln om [nätverks arkitektur][NetworkArchitecture] .
* **Måste Server dels-API-appen anropa sig själv?**  En överblickad och diskret punkt är ett scenario där backend-programmet måste anropa sig självt.  Om ett Server dels-API-program på en App Service-miljön behöver anropa sig, behandlas det också som ett "Internet"-anrop.  I exempel arkitekturen kräver detta att du tillåter åtkomst från den utgående IP-adressen för "apiase"-App Service-miljön också.

## <a name="setting-up-the-network-security-group"></a>Konfigurera nätverks säkerhets gruppen
När uppsättningen utgående IP-adresser är kända är nästa steg att skapa en nätverks säkerhets grupp.  Nätverks säkerhets grupper kan skapas både för Resource Manager-baserade virtuella nätverk, samt klassiska virtuella nätverk.  I exemplen nedan visas hur du skapar och konfigurerar en NSG i ett klassiskt virtuellt nätverk med PowerShell.

För exempel arkitekturen finns miljöerna i södra centrala USA, så en tom NSG skapas i den regionen:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" 
-Label "Only allow web frontend and loopback traffic"
```

Först en explicit Tillåt-regel läggs till för Azures hanterings infrastruktur som anges i artikeln om [inkommande trafik][InboundTraffic] för App Service miljöer.

```azurepowershell-interactive
#Open ports for access by Azure management infrastructure
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" 
-Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Sedan läggs två regler till för att tillåta HTTP-och HTTPS-anrop från den första överordnade App Service-miljön ("fe1ase").

```azurepowershell-interactive
#Grant access to requests from the first upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" 
-Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" 
-Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Skölj och upprepa för den andra och tredje överordnade App Service miljöer ("fe2ase" och "fe3ase").

```azurepowershell-interactive
#Grant access to requests from the second upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" 
-Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" 
-Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

#Grant access to requests from the third upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" 
-Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" 
-Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Till sist ger du åtkomst till den utgående IP-adressen för backend-API: et App Service-miljön så att den kan anropa sig själv.

```azurepowershell-interactive
#Allow apps on the apiase environment to call back into itself
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" 
-Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" 
-Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Det krävs inga andra nätverks säkerhets regler eftersom varje NSG har en uppsättning standard regler som blockerar inkommande åtkomst från Internet som standard.

Den fullständiga listan över regler i nätverks säkerhets gruppen visas nedan.  Observera hur den senaste regeln, som är markerad, blockerar inkommande åtkomst från alla anropare, förutom anropare som uttryckligen har beviljats åtkomst.

![NSG-konfiguration][NSGConfiguration] 

Det sista steget är att tillämpa NSG på det undernät som innehåller "apiase"-App Service-miljön.

```azurepowershell-interactive
#Apply the NSG to the backend API subnet
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet 
-VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'
```

När NSG tillämpas på under nätet, tillåts bara de tre överordnade App Service-miljöerna och de App Service-miljön som innehåller API-backend: n kan anropas i "apiase"-miljön.

## <a name="additional-links-and-information"></a>Ytterligare länkar och information
Information om [nätverks säkerhets grupper](../../virtual-network/network-security-groups-overview.md).

Förstå [utgående IP-adresser][NetworkArchitecture] och App Service miljöer.

[Nätverks portar][InboundTraffic] som används av App Service miljöer.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png