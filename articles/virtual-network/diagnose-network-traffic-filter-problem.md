---
title: Diagnostisera problem med filtrering av nätverkstrafik på virtuella datorer | Microsoft Docs
description: Lär dig hur du diagnostiserar problem med filtreringen av nätverkstrafik på virtuella datorer genom att visa de gällande säkerhetsreglerna för en virtuell dator.
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
ms.openlocfilehash: d6835d06015923a70301c95370c76efbd0c2163e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776743"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnostisera problem med filtrering av nätverkstrafik på virtuella datorer

I den här artikeln får du lära dig hur du diagnostiserar problem med filtrering av nätverkstrafik genom att visa de säkerhetsregler för nätverkssäkerhetsgrupp (NSG) som är effektiva för en virtuell dator (VM).

Med NSG:er kan du styra vilka typer av trafik som flödar in och ut från en virtuell dator. Du kan associera en NSG till ett undernät i ett virtuellt Azure-nätverk, ett nätverksgränssnitt som är kopplat till en virtuell dator eller båda. De gällande säkerhetsregler som tillämpas på ett nätverksgränssnitt är en sammanställning av de regler som finns i den NSG som är associerad med ett nätverksgränssnitt och det undernät som nätverksgränssnittet finns i. Regler i olika NSG:er kan ibland vara i konflikt med varandra och påverka en virtuell dators nätverksanslutning. Du kan visa alla gällande säkerhetsregler från NSG:er som tillämpas på den virtuella datorns nätverksgränssnitt. Om du inte är bekant med begreppen virtuellt nätverk, nätverksgränssnitt eller NSG kan du gå till Översikt över virtuellt [nätverk,](virtual-networks-overview.md)Nätverksgränssnitt [och](virtual-network-network-interface.md) [Nätverkssäkerhetsgrupper – översikt.](./network-security-groups-overview.md)

## <a name="scenario"></a>Scenario

Du försöker ansluta till en virtuell dator via port 80 från Internet, men anslutningen misslyckas. Om du vill ta reda på varför du inte kan komma åt port 80 från Internet kan du visa de gällande säkerhetsreglerna för ett nätverksgränssnitt med [hjälp](#diagnose-using-azure-portal)av [Azure-portalen, PowerShell](#diagnose-using-powershell)eller [Azure CLI.](#diagnose-using-azure-cli)

Stegen nedan förutsätter att du har en befintlig virtuell dator som du kan visa de gällande säkerhetsreglerna för. Om du inte har en befintlig virtuell dator distribuerar du först en virtuell [Linux-](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows-dator](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att slutföra uppgifterna i den här artikeln med. Exemplen i den här artikeln gäller för en virtuell dator med namnet *myVM* med ett nätverksgränssnitt med namnet *myVMVMNic.* Den virtuella datorn och nätverksgränssnittet finns i en resursgrupp med namnet *myResourceGroup* och finns i regionen *USA,* östra. Ändra värdena i stegen efter behov för den virtuella dator som du diagnostiserar problemet för.

## <a name="diagnose-using-azure-portal"></a>Diagnostisera med Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Azure-konto som har [de behörigheter som krävs.](virtual-network-network-interface.md#permissions)
2. Längst upp i Azure Portal anger du namnet på den virtuella datorn i sökrutan. När namnet på den virtuella datorn visas i sökresultatet väljer du det.
3. Under **INSTÄLLNINGAR** väljer du **Nätverk** enligt följande bild:

   ![Skärmbild som visar Azure Portal med nätverksinställningar för V M V M-nätverkskortet.](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Reglerna som visas i föregående bild gäller för ett nätverksgränssnitt med namnet **myVMVMNic.** Du ser att det finns **REGLER FÖR INKOMMANDE PORTAR** för nätverksgränssnittet från två olika nätverkssäkerhetsgrupper:
   
   - **mySubnetNSG:** Associerat med det undernät som nätverksgränssnittet finns i.
   - **myVMNSG:** Associerad med nätverksgränssnittet på den virtuella datorn med namnet **myVMVMNic**.

   Regeln med namnet **DenyAllInBound** förhindrar inkommande kommunikation till den virtuella datorn via port 80 från Internet, enligt beskrivningen i [scenariot](#scenario). Regeln listar *0.0.0.0/0* **för SOURCE**, som innehåller Internet. Ingen annan regel med högre prioritet (lägre nummer) tillåter inkommande trafik på port 80. Information om hur du tillåter inkommande port 80 till den virtuella datorn från Internet finns [i Lösa ett problem.](#resolve-a-problem) Mer information om säkerhetsregler och hur Azure tillämpar dem finns i [Nätverkssäkerhetsgrupper.](./network-security-groups-overview.md)

   Längst ned på bilden visas även REGLER **FÖR UTGÅENDE PORTAR.** Under det finns regler för utgående portar för nätverksgränssnittet. Även om bilden bara visar fyra regler för inkommande trafik för varje NSG kan dina NSG:er ha många fler än fyra regler. På bilden ser du **VirtualNetwork** under **SOURCE och** **DESTINATION och** **AzureLoadBalancer** under **SOURCE**. **VirtualNetwork** och **AzureLoadBalancer är** [tjänsttaggar](./network-security-groups-overview.md#service-tags). Tjänsttaggar representerar en grupp MED IP-adressprefix för att minimera komplexiteten vid skapande av säkerhetsregel.

4. Kontrollera att den virtuella datorn är i körningstillstånd och välj sedan Gällande säkerhetsregler **,** som du ser i föregående bild, för att se de gällande säkerhetsreglerna, som visas i följande bild:

   ![Skärmbild som visar fönstret Gällande säkerhetsregler med Ladda ned valt och regeln AllowAzureLoadBalancerInbound Inbound (Inkommande inkommande) har valts.](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Reglerna i listan är desamma som du såg i steg 3, även om det finns olika flikar för den NSG som är associerad med nätverksgränssnittet och undernätet. Som du ser på bilden visas endast de första 50 reglerna. Om du vill ladda ned en CSV-fil som innehåller alla regler väljer du **Ladda ned**.

   Om du vill se vilka prefix varje tjänsttagg representerar väljer du en regel, till exempel regeln **AllowAzureLoadBalancerInbound.** Följande bild visar prefixen för **tjänsttaggen AzureLoadBalancer:**

   ![Skärmbild som visar adressprefix för AllowAzureLoadBalancerInbound angivet.](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Även om **tjänsttaggen AzureLoadBalancer** bara representerar ett prefix representerar andra tjänsttaggar flera prefix.

5. Föregående steg visade säkerhetsreglerna för ett nätverksgränssnitt med namnet **myVMVMNic,** men du har också sett ett nätverksgränssnitt med namnet **myVMVMNic2** i några av de föregående bilderna. Den virtuella datorn i det här exemplet har två anslutna nätverksgränssnitt. De gällande säkerhetsreglerna kan vara olika för varje nätverksgränssnitt.

   Om du vill se reglerna för **nätverksgränssnittet myVMVMNic2** väljer du det. Som du ser i bilden nedan har nätverksgränssnittet samma regler som är associerade med undernätet som **nätverksgränssnittet myVMVMNic,** eftersom båda nätverksgränssnitten finns i samma undernät. När du associerar en NSG till ett undernät tillämpas dess regler på alla nätverksgränssnitt i undernätet.

   ![Skärmbild som visar Azure Portal med nätverksinställningar för V M V M Nic 2.](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   Till skillnad **från nätverksgränssnittet myVMVMNic** har **nätverksgränssnittet myVMVMNic2** ingen associerad nätverkssäkerhetsgrupp. Varje nätverksgränssnitt och undernät kan ha noll eller en NSG kopplad till sig. NSG:n som är associerad med varje nätverksgränssnitt eller undernät kan vara samma eller olika. Du kan associera samma nätverkssäkerhetsgrupp med så många nätverksgränssnitt och undernät som du väljer.

Även om effektiva säkerhetsregler visades via den virtuella datorn kan du också visa effektiva säkerhetsregler via en enskild person:
- **Nätverksgränssnitt:** Lär dig hur du [visar ett nätverksgränssnitt](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG:** Lär dig hur du [visar en NSG.](manage-network-security-group.md#view-details-of-a-network-security-group)

## <a name="diagnose-using-powershell"></a>Diagnostisera med Hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Det Azure Cloud Shell är ett kostnadsfritt interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra för att logga `Connect-AzAccount` in på Azure med ett konto som har nödvändiga [behörigheter](virtual-network-network-interface.md#permissions).

Hämta de effektiva säkerhetsreglerna för ett nätverksgränssnitt med [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup). I följande exempel hämtar de gällande säkerhetsreglerna för ett nätverksgränssnitt med namnet *myVMVMNic*, som finns i en resursgrupp med namnet *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Utdata returneras i json-format. Information om utdata finns i [tolka kommandoutdata](#interpret-command-output).
Utdata returneras endast om en NSG är associerad med nätverksgränssnittet, det undernät som nätverksgränssnittet finns i eller båda. Den virtuella datorn måste vara i körningstillstånd. En virtuell dator kan ha flera nätverksgränssnitt med olika NSG:er tillämpade. När du felsöker kör du kommandot för varje nätverksgränssnitt.

Om du fortfarande har problem med anslutningen kan du se ytterligare [diagnos och](#additional-diagnosis) [överväganden.](#considerations)

Om du inte känner till namnet på ett nätverksgränssnitt, men vet namnet på den virtuella dator som nätverksgränssnittet är kopplat till, returnerar följande kommandon DED:erna för alla nätverksgränssnitt som är anslutna till en virtuell dator:

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

I föregående utdata är nätverksgränssnittsnamnet *myVMVMNic.*

## <a name="diagnose-using-azure-cli"></a>Diagnostisera med Hjälp av Azure CLI

Om du använder Azure CLI-kommandon (Kommandoradsgränssnitt) för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra och logga in på `az login` Azure med ett konto som har de behörigheter som [krävs.](virtual-network-network-interface.md#permissions)

Hämta de gällande säkerhetsreglerna för ett nätverksgränssnitt med [az network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg). I följande exempel hämtar de gällande säkerhetsreglerna för ett nätverksgränssnitt med namnet *myVMVMNic* som finns i en resursgrupp med namnet *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Utdata returneras i json-format. Information om utdata finns i [tolka kommandoutdata](#interpret-command-output).
Utdata returneras endast om en NSG är associerad med nätverksgränssnittet, det undernät som nätverksgränssnittet finns i eller båda. Den virtuella datorn måste vara i körningstillstånd. En virtuell dator kan ha flera nätverksgränssnitt med olika NSG:er tillämpade. När du felsöker kör du kommandot för varje nätverksgränssnitt.

Om du fortfarande har problem med anslutningen kan du se ytterligare [diagnos och](#additional-diagnosis) [överväganden.](#considerations)

Om du inte känner till namnet på ett nätverksgränssnitt, men vet namnet på den virtuella dator som nätverksgränssnittet är kopplat till, returnerar följande kommandon DED:erna för alla nätverksgränssnitt som är anslutna till en virtuell dator:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

I de returnerade utdata visas information som liknar följande exempel:

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

I föregående utdata är nätverksgränssnittsnamnet *myVMVMNic-gränssnittet*.

## <a name="interpret-command-output"></a>Tolka kommandoutdata

Oavsett om du använde [PowerShell](#diagnose-using-powershell)eller [Azure CLI](#diagnose-using-azure-cli) för att diagnostisera problemet får du utdata som innehåller följande information:

- **NetworkSecurityGroup:** ID:t för nätverkssäkerhetsgruppen.
- **Association**: Om nätverkssäkerhetsgruppen är associerad med ett *NetworkInterface* eller *ett undernät.* Om en NSG är associerad med båda returneras utdata med **NetworkSecurityGroup,** **Association** och **EffectiveSecurityRules** för varje NSG. Om NSG är associerad eller frånassocierad omedelbart innan du kör kommandot för att visa gällande säkerhetsregler, kan du behöva vänta några sekunder tills ändringen återspeglas i kommandots utdata.
- **EffectiveSecurityRules:** En förklaring av varje egenskap beskrivs i [Skapa en säkerhetsregel.](manage-network-security-group.md#create-a-security-rule) Regelnamn som föregås av *defaultSecurityRules/* är standardsäkerhetsregler som finns i varje NSG. Regelnamn som föregås av *securityRules/* är regler som du har skapat. Regler som anger en [tjänsttagg,](./network-security-groups-overview.md#service-tags)till exempel **Internet,** **VirtualNetwork** och **AzureLoadBalancer** för egenskaperna **destinationAddressPrefix** eller **sourceAddressPrefix,** har även värden för egenskapen **expandedDestinationAddressPrefix.** Egenskapen **expandedDestinationAddressPrefix** visar en lista över alla adressprefix som representeras av tjänsttaggen.

Om dubblettregler visas i utdata beror det på att en NSG är associerad med både nätverksgränssnittet och undernätet. Båda NSG:erna har samma standardregler och kan ha ytterligare dubblettregler om du har skapat egna regler som är desamma i båda NSG:erna.

Regeln med namnet **defaultSecurityRules/DenyAllInBound** är det som förhindrar inkommande kommunikation till den virtuella datorn via port 80 från Internet, enligt [beskrivningen](#scenario)i scenariot . Ingen annan regel med högre prioritet (lägre nummer) tillåter inkommande trafik via port 80 från Internet.

## <a name="resolve-a-problem"></a>Lösa ett problem

Oavsett om du använder [Azure-portalen,](#diagnose-using-azure-portal) [PowerShell](#diagnose-using-powershell)eller [Azure CLI](#diagnose-using-azure-cli) för att diagnostisera problemet som presenteras i [scenariot](#scenario) i den här artikeln är lösningen att skapa en nätverkssäkerhetsregel med följande egenskaper:

| Egenskap                | Värde                                                                              |
|---------                |---------                                                                           |
| Källa                  | Valfri                                                                                |
| Källportintervall      | Valfri                                                                                |
| Mål             | IP-adressen för den virtuella datorn, ett intervall med IP-adresser eller alla adresser i undernätet. |
| Målportintervall | 80                                                                                 |
| Protokoll                | TCP                                                                                |
| Åtgärd                  | Tillåt                                                                              |
| Prioritet                | 100                                                                                |
| Namn                    | Tillåt HTTP-Alla                                                                     |

När du har skapat regeln tillåts port 80 inkommande från Internet eftersom regelns prioritet är högre än standardsäkerhetsregeln *DenyAllInBound* som nekar trafiken. Lär dig hur [du skapar en säkerhetsregel](manage-network-security-group.md#create-a-security-rule). Om olika NSG:er är associerade med både nätverksgränssnittet och undernätet måste du skapa samma regel i båda NSG:erna.

När Azure bearbetar inkommande trafik bearbetar den regler i den NSG som är associerad med undernätet (om det finns en associerad NSG) och bearbetar sedan reglerna i den NSG som är associerad med nätverksgränssnittet. Om det finns en NSG som är associerad med nätverksgränssnittet och undernätet måste porten vara öppen i båda NSG:erna för att trafiken ska nå den virtuella datorn. För att underlätta problem med administration och kommunikation rekommenderar vi att du associerar en NSG med ett undernät i stället för enskilda nätverksgränssnitt. Om virtuella datorer i ett undernät behöver olika säkerhetsregler kan du göra nätverksgränssnitten till medlemmar i en programsäkerhetsgrupp (ASG) och ange en ASG som källa och mål för en säkerhetsregel. Läs mer om [programsäkerhetsgrupper.](./network-security-groups-overview.md#application-security-groups)

Om du fortfarande har kommunikationsproblem kan du gå till [Överväganden](#considerations) och Ytterligare diagnos.

## <a name="considerations"></a>Överväganden

Tänk på följande när du felsöker anslutningsproblem:

* Standardsäkerhetsregler blockerar inkommande åtkomst från Internet och tillåter endast inkommande trafik från det virtuella nätverket. Om du vill tillåta inkommande trafik från Internet lägger du till säkerhetsregler med högre prioritet än standardreglerna. Läs mer om [standardsäkerhetsregler eller](./network-security-groups-overview.md#default-security-rules)hur du lägger [till en säkerhetsregel](manage-network-security-group.md#create-a-security-rule).
* Om du har peer-peer-ade virtuella nätverk utökas **VIRTUAL_NETWORK** automatiskt med prefix för peer-ade virtuella nätverk. Om du vill felsöka problem som rör peering för virtuella nätverk kan du visa prefixen i listan **ExpandedAddressPrefix.** Läs mer om [peering för virtuella nätverk](virtual-network-peering-overview.md) och [tjänsttaggar.](./network-security-groups-overview.md#service-tags)
* Gällande säkerhetsregler visas bara för ett nätverksgränssnitt om det finns en NSG som är associerad med den virtuella datorns nätverksgränssnitt och, eller, undernät, och om den virtuella datorn är i körningstillstånd.
* Om det inte finns några NSG:er associerade med nätverksgränssnittet eller undernätet och du har en offentlig [IP-adress](virtual-network-public-ip-address.md) tilldelad till en virtuell dator, är alla portar öppna för inkommande åtkomst från och utgående åtkomst till var som helst. Om den virtuella datorn har en offentlig IP-adress rekommenderar vi att du tillämpar en NSG på undernätet i nätverksgränssnittet.

## <a name="additional-diagnosis"></a>Ytterligare diagnos

* Om du vill köra ett snabbtest för att avgöra om trafik tillåts till eller från en virtuell dator använder du funktionen Kontrollera [IP-flöde](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) i Azure Network Watcher. Kontrollera IP-flöde anger om trafik tillåts eller nekas. Om den nekas anger Kontrollera IP-flöde vilken säkerhetsregel som nekar trafiken.
* Om det inte finns några säkerhetsregler som gör att en virtuell dators nätverksanslutning misslyckas kan problemet bero på:
  * Brandväggsprogramvara som körs i den virtuella datorns operativsystem
  * Vägar som konfigurerats för virtuella installationer eller lokal trafik. Internettrafik kan omdirigeras till ditt lokala nätverk via [tvingad tunneltrafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du tvingar internettrafik via tunneltrafik till en virtuell installation eller lokalt kanske du inte kan ansluta till den virtuella datorn från Internet. Information om hur du diagnostiserar problem med routning som kan hindra trafikflödet från den virtuella datorn finns i Diagnostisera problem med [nätverkstrafikroutning för virtuella datorer.](diagnose-network-routing-problem.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om alla uppgifter, egenskaper och inställningar för en [nätverkssäkerhetsgrupp](manage-network-security-group.md#work-with-network-security-groups) och [säkerhetsregler.](manage-network-security-group.md#work-with-security-rules)
- Lär dig [mer om standardsäkerhetsregler,](./network-security-groups-overview.md#default-security-rules) [tjänsttaggar](./network-security-groups-overview.md#service-tags)och hur Azure bearbetar [säkerhetsregler för inkommande och utgående trafik för](./network-security-groups-overview.md#network-security-groups) en virtuell dator.