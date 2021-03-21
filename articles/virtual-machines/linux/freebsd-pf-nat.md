---
title: Använd FreeBSD paket filter för att skapa en brand vägg i Azure
description: Lär dig hur du distribuerar en NAT-brandvägg med FreeBSD-PF i Azure.
author: KylieLiang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 673ddf30771f63f559a12dbea47a1f022ea5599a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565928"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Så här använder du FreeBSD Packet filter för att skapa en säker brand vägg i Azure
Den här artikeln beskriver hur du distribuerar en NAT-brandvägg med hjälp av FreeBSD-filter genom Azure Resource Manager mall för vanliga webb server scenarier.

## <a name="what-is-pf"></a>Vad är PF?
PF (paket filter, även skrivet PF) är ett BSD-paket med tillstånds känsligt paket, en central del av program varan för brand väggar. PF har sedan utvecklats snabbt och har nu flera fördelar jämfört med andra tillgängliga brand väggar. NAT (Network Address Translation) är i PF sedan dag ett, sedan Packet Scheduler och hantering av aktiva köer har integrerats i PF, genom att integrera ALTQ och göra det konfigurerbart via PF-konfigurationen. Funktioner som pfsync och karp för redundans och redundans, authpf för certifikatautentisering och FTP-Proxy för att under lätta brand väggar av det svåra FTP-protokollet, har även utökad PF. I korthet är PF en kraftfull och funktions rik brand vägg. 

## <a name="get-started"></a>Kom igång
Om du är intresse rad av att skapa en säker brand vägg i molnet för dina webb servrar, kan du börja med att sätta igång. Du kan också använda skripten som används i den här Azure Resource Manager-mallen för att ställa in nätverk sto pol Ogin.
Azure Resource Manager-mallen konfigurerar en virtuell FreeBSD-dator som utför NAT-/Redirection med hjälp av PF och två FreeBSD virtuella datorer med Nginx-webbservern installerad och konfigurerad. Förutom att utföra NAT för de två webb servrar som utgående trafik, fångar NAT/omdirigering av den virtuella datorn HTTP-förfrågningar och dirigerar dem till de två webb servrarna i resursallokering. VNet använder det privata icke-flyttbara IP-adressutrymmet 10.0.0.2/24 och du kan ändra parametrarna för mallen. Azure Resource Manager-mallen definierar också en routningstabell för hela VNet, som är en samling enskilda vägar som används för att åsidosätta Azures standard vägar baserat på mål-IP-adressen. 

![Diagrammet visar en offentlig I P-adress på en NAT-instans som omdirigeras av Round-Robin-metoden till två virtuella server dels datorer som är värdar för nginx-webbservrar.](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Distribuera via Azure CLI
Du behöver det senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggat på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index). Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas ett resurs grupps namn `myResourceGroup` på `West US` platsen.

```azurecli
az group create --name myResourceGroup --location westus
```

Sedan distribuerar du mallen PF-FreeBSD-setup med [AZ Deployment Group Create](/cli/azure/deployment/group). Hämta azuredeploy.parameters.jspå samma sökväg och definiera dina egna resurs värden, till exempel, `adminPassword` `networkPrefix` och `domainNamePrefix` . 

```azurecli
az deployment group create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Efter cirka fem minuter kommer du att få information om `"provisioningState": "Succeeded"` . Sedan kan du använda SSH till den virtuella datorns virtuella dator (NAT) eller åtkomst nginx webb server i en webbläsare med hjälp av den offentliga IP-adressen eller FQDN för den virtuella datorns VM (NAT). I följande exempel visas FQDN och offentlig IP-adress som har tilldelats till den virtuella datorns VM (NAT) i `myResourceGroup` resurs gruppen. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Nästa steg
Vill du konfigurera din egen NAT i Azure? Öppen källkod, kostnads fritt men kraftfullt? Sedan är PF ett bra val. Genom att använda mallen PF-FreeBSD-setup behöver du bara fem minuter för att konfigurera en NAT-brandvägg med resursallokering med resursallokering med FreeBSD: s PF i Azure för gemensamt webb server scenario. 

Om du vill lära dig att erbjuda FreeBSD i Azure kan du läsa [Introduktion till FreeBSD på Azure](freebsd-intro-on-azure.md).

Om du vill veta mer om PF, se [FreeBSD handböcker](https://www.freebsd.org/doc/handbook/firewalls-pf.html) eller [PF-User ' s guide](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
