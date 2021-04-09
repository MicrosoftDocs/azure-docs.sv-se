---
title: Diagnostisera ett problem med nätverks trafik filter för virtuella datorer | Microsoft Docs
description: Lär dig hur du diagnostiserar ett nätverks trafik filter problem med en virtuell dator genom att se de effektiva säkerhets reglerna för en virtuell dator.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: 73562d8d32f265fa43ca80d2f8d4f84b1b631ec6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223677"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnostisera ett problem med nätverks trafik filter för virtuella datorer

I den här artikeln får du lära dig hur du diagnostiserar ett problem med nätverks trafik filter genom att Visa säkerhets reglerna för nätverks säkerhets gruppen (NSG) som är effektiva för en virtuell dator (VM).

Med NSG: er kan du styra vilka typer av trafik som flödar in och ut ur en virtuell dator. Du kan associera en NSG till ett undernät i ett virtuellt Azure-nätverk, ett nätverks gränssnitt som är kopplat till en virtuell dator eller både och. De effektiva säkerhets regler som tillämpas på ett nätverks gränssnitt är en agg regering av de regler som finns i NSG som är kopplade till ett nätverks gränssnitt och under nätet som nätverks gränssnittet finns i. Regler i olika NSG: er kan ibland vara i konflikt med varandra och påverka en virtuell dators nätverks anslutning. Du kan visa alla gällande säkerhets regler från NSG: er som tillämpas på den virtuella datorns nätverks gränssnitt. Om du inte är bekant med Virtual Network, Network Interface eller NSG Concepts, se Översikt över [virtuella nätverk](virtual-networks-overview.md), [nätverks gränssnitt](virtual-network-network-interface.md)och [nätverks säkerhets grupper](./network-security-groups-overview.md).

## <a name="scenario"></a>Scenario

Du försöker ansluta till en virtuell dator via port 80 från Internet, men anslutningen Miss lyckas. För att avgöra varför du inte kan komma åt port 80 från Internet kan du Visa de effektiva säkerhets reglerna för ett nätverks gränssnitt med hjälp av Azure [Portal](#diagnose-using-azure-portal), [POWERSHELL](#diagnose-using-powershell)eller [Azure CLI](#diagnose-using-azure-cli).

Stegen nedan förutsätter att du har en befintlig virtuell dator för att se de effektiva säkerhets reglerna för. Om du inte har en befintlig virtuell dator distribuerar du först en [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dator för att slutföra uppgifterna i den här artikeln med. Exemplen i den här artikeln gäller för en virtuell dator med namnet *myVM* med ett nätverks gränssnitt med namnet *myVMVMNic*. Den virtuella datorn och nätverks gränssnittet finns i en resurs grupp med namnet *myResourceGroup* och finns i regionen *USA, östra* . Ändra värdena i stegen efter behov för den virtuella dator som du diagnostiserar problemet för.

## <a name="diagnose-using-azure-portal"></a>Diagnostisera med Azure Portal

1. Logga in på Azure [Portal](https://portal.azure.com) med ett Azure-konto som har de [behörigheter som krävs](virtual-network-network-interface.md#permissions).
2. Ange namnet på den virtuella datorn i sökrutan överst i Azure Portal. När namnet på den virtuella datorn visas i Sök resultatet väljer du det.
3. Under **Inställningar** väljer du **nätverk**, som du ser i följande bild:

   ![Skärm bild som visar Azure Portal med nätverks inställningar för mitt V M V M-nätverkskort.](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Reglerna som visas i föregående bild är för ett nätverks gränssnitt med namnet **myVMVMNic**. Du ser att det finns **regler för inkommande portar** för nätverks gränssnittet från två olika nätverks säkerhets grupper:
   
   - **mySubnetNSG**: associerat med det undernät som nätverks gränssnittet finns i.
   - **myVMNSG**: kopplade till nätverks gränssnittet på den virtuella datorn med namnet **myVMVMNic**.

   Regeln med namnet **DenyAllInBound** är vad som hindrar inkommande kommunikation till den virtuella datorn via port 80 från Internet, enligt beskrivningen i [scenariot](#scenario). Regeln listar *0.0.0.0/0* för **källa**, inklusive Internet. Ingen annan regel med högre prioritet (lägre nummer) tillåter port 80 inkommande. Information om hur du tillåter port 80 inkommande till den virtuella datorn från Internet finns i [lösa ett problem](#resolve-a-problem). Mer information om säkerhets regler och hur Azure använder dem finns i [nätverks säkerhets grupper](./network-security-groups-overview.md).

   Längst ned på bilden visas även **utgående port regler**. Under som är regler för utgående portar för nätverks gränssnittet. Även om bilden visar fyra regler för inkommande trafik för varje NSG, kan NSG: er ha många fler än fyra regler. I bilden ser du **VirtualNetwork** under **källa** och **mål** och **AzureLoadBalancer** under **källa**. **VirtualNetwork** och **AzureLoadBalancer** är [service märken](./network-security-groups-overview.md#service-tags). Service märken representerar en grupp med IP-adressprefix för att minimera komplexiteten för att skapa säkerhets regler.

4. Se till att den virtuella datorn är i körnings läge och välj sedan **effektiva säkerhets regler**, som du ser i föregående bild, för att se de effektiva säkerhets reglerna som visas på följande bild:

   ![Skärm bild som visar fönstret gällande säkerhets regler med Hämta valda och AllowAzureLoadBalancerInbound inkommande regel vald.](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Reglerna i listan är samma som du såg i steg 3, men det finns olika flikar för de NSG som är kopplade till nätverks gränssnittet och under nätet. Som du kan se i bilden visas endast de första 50 reglerna. Om du vill hämta en. csv-fil som innehåller alla regler väljer du **Hämta**.

   Om du vill se vilka prefix varje service tag representerar väljer du en regel, till exempel regeln med namnet **AllowAzureLoadBalancerInbound**. Följande bild visar prefixen för **AzureLoadBalancer** -tjänst tag gen:

   ![Skärm bild som visar adressprefix för AllowAzureLoadBalancerInbound som har angetts.](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Även om **AzureLoadBalancer** service tag bara representerar ett prefix, representerar andra tjänst Taggar flera prefix.

5. Föregående steg visade säkerhets reglerna för ett nätverks gränssnitt med namnet **myVMVMNic**, men du har också sett ett nätverks gränssnitt med namnet **myVMVMNic2** i några av de föregående bilderna. Den virtuella datorn i det här exemplet har två nätverks gränssnitt anslutna. De effektiva säkerhets reglerna kan vara olika för varje nätverks gränssnitt.

   Om du vill se reglerna för nätverks gränssnittet **myVMVMNic2** väljer du det. Som du ser i bilden nedan, har nätverks gränssnittet samma regler som är kopplade till sitt undernät som nätverks gränssnittet **myVMVMNic** , eftersom båda nätverks gränssnitten finns i samma undernät. När du associerar en NSG till ett undernät tillämpas reglerna på alla nätverks gränssnitt i under nätet.

   ![Skärm bild som visar Azure Portal med nätverks inställningar för mitt V M V M NIC 2.](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   Till skillnad från nätverks gränssnittet för **myVMVMNic** har nätverks gränssnittet **myVMVMNic2** ingen nätverks säkerhets grupp kopplad till sig. Varje nätverks gränssnitt och undernät kan ha noll, eller ett, NSG kopplat till det. NSG som är kopplade till varje nätverks gränssnitt eller undernät kan vara samma eller olika. Du kan associera samma nätverks säkerhets grupp med så många nätverks gränssnitt och undernät du väljer.

Trots att de effektiva säkerhets reglerna har visats via den virtuella datorn kan du också Visa effektiva säkerhets regler via en individ:
- **Nätverks gränssnitt**: Lär dig hur du [visar ett nätverks gränssnitt](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG**: Lär dig hur du [visar en NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnostisera med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell-modulen, version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att logga in på Azure med ett konto som har de [behörigheter som krävs](virtual-network-network-interface.md#permissions)].

Hämta de effektiva säkerhets reglerna för ett nätverks gränssnitt med [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup). I följande exempel hämtas effektiva säkerhets regler för ett nätverks gränssnitt med namnet *myVMVMNic*, som finns i en resurs grupp med namnet *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Utdata returneras i JSON-format. Information om utdata finns i [tolka kommandoutdata utdata](#interpret-command-output).
Utdata returneras endast om en NSG är kopplad till nätverks gränssnittet, under nätet som nätverks gränssnittet finns i eller både och. Den virtuella datorn måste vara i körnings läge. En virtuell dator kan ha flera nätverks gränssnitt med olika NSG: er tillämpade. Kör kommandot för varje nätverks gränssnitt vid fel sökning.

Om du fortfarande har problem med anslutningen läser du [ytterligare diagnostik](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverks gränssnitt, men känner till namnet på den virtuella dator som nätverks gränssnittet är kopplat till, returnerar följande kommandon ID: n för alla nätverks gränssnitt som är anslutna till en virtuell dator:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Du får utdata som liknar följande exempel:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

I föregående utdata är nätverks gränssnittets namn *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnostisera med Azure CLI

Om du använder kommando rads kommandon i Azure för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` och logga in på Azure med ett konto som har de [behörigheter som krävs](virtual-network-network-interface.md#permissions).

Hämta de effektiva säkerhets reglerna för ett nätverks gränssnitt med [AZ Network NIC List-effektiv-NSG](/cli/azure/network/nic#az-network-nic-list-effective-nsg). I följande exempel hämtas effektiva säkerhets regler för ett nätverks gränssnitt med namnet *myVMVMNic* som finns i en resurs grupp med namnet *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Utdata returneras i JSON-format. Information om utdata finns i [tolka kommandoutdata utdata](#interpret-command-output).
Utdata returneras endast om en NSG är kopplad till nätverks gränssnittet, under nätet som nätverks gränssnittet finns i eller både och. Den virtuella datorn måste vara i körnings läge. En virtuell dator kan ha flera nätverks gränssnitt med olika NSG: er tillämpade. Kör kommandot för varje nätverks gränssnitt vid fel sökning.

Om du fortfarande har problem med anslutningen läser du [ytterligare diagnostik](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverks gränssnitt, men känner till namnet på den virtuella dator som nätverks gränssnittet är kopplat till, returnerar följande kommandon ID: n för alla nätverks gränssnitt som är anslutna till en virtuell dator:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

I returnerade utdata ser du information som liknar följande exempel:

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

I föregående utdata är nätverks gränssnittets namn MyVMVMNic- *gränssnittet*.

## <a name="interpret-command-output"></a>Tolka kommandoutdata

Oavsett om du har använt [PowerShell](#diagnose-using-powershell)eller [Azure CLI](#diagnose-using-azure-cli) för att diagnosticera problemet får du utdata som innehåller följande information:

- **NetworkSecurityGroup**: ID för nätverks säkerhets gruppen.
- **Association**: om nätverks säkerhets gruppen är kopplad till en *NetworkInterface* eller ett *undernät*. Om en NSG är kopplad till båda returneras utdata med **NetworkSecurityGroup**, **Association** och **EffectiveSecurityRules** för varje NSG. Om NSG är associerat eller disassocierat omedelbart innan du kör kommandot för att se de effektiva säkerhets reglerna kan du behöva vänta några sekunder innan ändringen visas i kommandoutdata.
- **EffectiveSecurityRules**: en förklaring av varje egenskap beskrivs i [skapa en säkerhets regel](manage-network-security-group.md#create-a-security-rule). Regel namn som föregås av *defaultSecurityRules/* är standard säkerhets regler som finns i alla NSG. Regel namn som föregås av *securityRules/* är regler som du har skapat. Regler som anger en [service tagg](./network-security-groups-overview.md#service-tags), till exempel **Internet**, **VirtualNetwork** och **AzureLoadBalancer** för egenskaperna **DestinationAddressPrefix** eller **sourceAddressPrefix** , har också värden för egenskapen **expandedDestinationAddressPrefix** . Egenskapen **expandedDestinationAddressPrefix** visar alla adressprefix som representeras av tjänst tag gen.

Om du ser dubbla regler som visas i utdata, beror det på att en NSG är kopplad till både nätverks gränssnittet och under nätet. Båda NSG: er har samma standard regler och kan ha ytterligare dubbletter om du har skapat dina egna regler som är samma i båda NSG: er.

Regeln med namnet **defaultSecurityRules/DenyAllInBound** är vad som hindrar inkommande kommunikation till den virtuella datorn via port 80 från Internet, enligt beskrivningen i [scenariot](#scenario). Ingen annan regel med högre prioritet (lägre nummer) tillåter port 80 inkommande från Internet.

## <a name="resolve-a-problem"></a>Lösa ett problem

Oavsett om du använder Azure- [portalen](#diagnose-using-azure-portal), [POWERSHELL](#diagnose-using-powershell)eller [Azure CLI](#diagnose-using-azure-cli) för att diagnosticera problemet som presenteras i den här [](#scenario) artikeln, är lösningen att skapa en nätverks säkerhets regel med följande egenskaper:

| Egenskap                | Värde                                                                              |
|---------                |---------                                                                           |
| Källa                  | Valfri                                                                                |
| Källportintervall      | Valfri                                                                                |
| Mål             | IP-adressen för den virtuella datorn, ett intervall med IP-adresser eller alla adresser i under nätet. |
| Målportintervall | 80                                                                                 |
| Protokoll                | TCP                                                                                |
| Åtgärd                  | Tillåt                                                                              |
| Prioritet                | 100                                                                                |
| Namn                    | Tillåt-HTTP-alla                                                                     |

När du har skapat regeln tillåts port 80 inkommande från Internet, eftersom regelns prioritet är högre än standard säkerhets regeln med namnet *DenyAllInBound*, som nekar trafiken. Lär dig hur du [skapar en säkerhets regel](manage-network-security-group.md#create-a-security-rule). Om olika NSG: er är kopplade till både nätverks gränssnittet och under nätet måste du skapa samma regel i båda NSG: er.

När Azure bearbetar inkommande trafik, bearbetar den regler i NSG som är kopplade till under nätet (om det finns en associerad NSG) och bearbetar sedan reglerna i de NSG som är kopplade till nätverks gränssnittet. Om det finns en NSG kopplad till nätverks gränssnittet och under nätet måste porten vara öppen i båda NSG: er för att trafiken ska kunna uppnå den virtuella datorn. För att under lätta administration och kommunikations problem rekommenderar vi att du associerar en NSG till ett undernät i stället för enskilda nätverks gränssnitt. Om virtuella datorer inom ett undernät behöver olika säkerhets regler, kan du göra nätverks gränssnitten till medlemmar i en program säkerhets grupp (grupperna) och ange en grupperna som källa och mål för en säkerhets regel. Läs mer om [program säkerhets grupper](./network-security-groups-overview.md#application-security-groups).

Om du fortfarande har kommunikations problem, se [överväganden](#considerations) och ytterligare diagnos.

## <a name="considerations"></a>Överväganden

Tänk på följande när du felsöker anslutnings problem:

* Standard säkerhets regler blockerar inkommande åtkomst från Internet och tillåter endast inkommande trafik från det virtuella nätverket. Om du vill tillåta inkommande trafik från Internet lägger du till säkerhets regler med högre prioritet än standard regler. Läs mer om [Standard säkerhets regler](./network-security-groups-overview.md#default-security-rules)eller hur du [lägger till en säkerhets regel](manage-network-security-group.md#create-a-security-rule).
* Om du har peer-kopplat virtuella nätverk expanderar **VIRTUAL_NETWORK** service tag som standard automatiskt till att inkludera prefix för peer-kopplat virtuella nätverk. Om du vill felsöka problem som rör peering av virtuella nätverk kan du Visa prefixen i **ExpandedAddressPrefix** -listan. Läs mer om [peering](virtual-network-peering-overview.md) [-och service-Taggar](./network-security-groups-overview.md#service-tags)för virtuella nätverk.
* Effektiva säkerhets regler visas bara för ett nätverks gränssnitt om det finns en NSG kopplad till den virtuella datorns nätverks gränssnitt och, eller, undernät, och om den virtuella datorn är i körnings tillstånd.
* Om det inte finns några NSG: er kopplade till nätverks gränssnittet eller under nätet och du har en [offentlig IP-adress](virtual-network-public-ip-address.md) tilldelad till en virtuell dator, är alla portar öppna för inkommande åtkomst från och utgående åtkomst till var som helst. Om den virtuella datorn har en offentlig IP-adress rekommenderar vi att du använder en NSG till under nätet nätverks gränssnittet.

## <a name="additional-diagnosis"></a>Ytterligare diagnos

* Om du vill köra ett snabb test för att avgöra om trafik tillåts till eller från en virtuell dator använder du [IP-flödet verifiera Azure-](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) Network Watcher. Med kontrol lera IP-flöde får du information om trafik tillåts eller nekas. Om nekas verifierar IP-flödet vilken säkerhets regel som nekar trafiken.
* Om det inte finns några säkerhets regler som orsakar en virtuell dators nätverks anslutning kan problemet bero på att:
  * Brand Väggs program som körs i den virtuella datorns operativ system
  * Vägar som kon figurer ATS för virtuella enheter eller lokal trafik. Internet trafiken kan omdirigeras till ditt lokala nätverk via [Tvingad tunnel trafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du tvingar Internet-trafik till en virtuell installation eller lokalt kan du inte ansluta till den virtuella datorn från Internet. Information om hur du diagnostiserar flödes problem som kan hindra trafikflödet från den virtuella datorn finns i [diagnostisera ett problem med Routning av nätverks trafik i virtuell dator](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om alla aktiviteter, egenskaper och inställningar för en [nätverks säkerhets grupp](manage-network-security-group.md#work-with-network-security-groups) och [säkerhets regler](manage-network-security-group.md#work-with-security-rules).
- Lär dig mer om [Standard säkerhets regler](./network-security-groups-overview.md#default-security-rules), [service märken](./network-security-groups-overview.md#service-tags)och [hur Azure bearbetar säkerhets regler för inkommande och utgående trafik](./network-security-groups-overview.md#network-security-groups) för en virtuell dator.