---
title: Skapa, ändra eller ta bort ett Azure Virtual Network-peering | Microsoft Docs
description: Skapa, ändra eller ta bort en virtuell nätverks-peering. Med peering av virtuella nätverk ansluter du virtuella nätverk i samma region och i flera regioner.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: altambaw
ms.openlocfilehash: dc8db3f1eccce2bb85f03d51fcfd1c4113823d49
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222678"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Skapa, ändra eller ta bort en virtuell nätverks-peering

Lär dig hur du skapar, ändrar eller tar bort en virtuell nätverks-peering. Med peering för virtuella nätverk kan du ansluta virtuella nätverk i samma region och mellan regioner (även kallade global VNet-peering) via Azures stamnät nätverk. När de virtuella nätverken har peer-kopplats hanteras de fortfarande som separata resurser. Om du inte har använt det virtuella nätverkets peering kan du lära dig mer om det i den [virtuella nätverkets peering-översikt](virtual-network-peering-overview.md) eller genom att slutföra en [själv studie kurs](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande åtgärder innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto kan du registrera dig för ett [kostnads fritt utvärderings konto](https://azure.microsoft.com/free).
- Om du använder portalen öppnar du https://portal.azure.com och loggar in med ett konto som har de [behörigheter som krävs](#permissions) för att arbeta med peer-kopplingar.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. I den här självstudien krävs Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` med ett konto som har de [behörigheter som krävs](#permissions) för att arbeta med peering, för att skapa en anslutning till Azure.
- Om du använder kommando rads kommandon i Azure för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. I den här självstudien krävs Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` med ett konto som har de [behörigheter som krävs](#permissions) för att arbeta med peering, för att skapa en anslutning till Azure.

Det konto som du loggar in på eller ansluta till Azure med måste tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [behörigheter](#permissions).

## <a name="create-a-peering"></a>Skapa en peer koppling

Innan du skapar en peering bör du bekanta dig med kraven och begränsningarna och [de behörigheter som krävs](#permissions).

1. I rutan Sök högst upp i Azure Portal anger du *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i Sök resultaten väljer du det. Välj inte **virtuella nätverk (klassisk)** om det visas i listan, eftersom du inte kan skapa en peering från ett virtuellt nätverk som distribueras via den klassiska distributions modellen.
2. Välj det virtuella nätverk i listan som du vill skapa en peering för.
3. Under **Inställningar** väljer du **peering**.
4. Välj **+ Lägg till**. 
5. <a name="add-peering"></a>Ange eller välj värden för följande inställningar:
    - **Namn:** Peer-kopplingens namn måste vara unikt inom det virtuella nätverket.
    - **Distributions modell för virtuellt nätverk:** Välj vilken distributions modell som det virtuella nätverk som du vill peer-koppla till har distribuerats via.
    - **Jag känner till mitt resurs-ID:** Om du har Läs behörighet till det virtuella nätverk som du vill peer-koppla med lämnar du kryss rutan avmarkerad. Markera den här kryss rutan om du inte har Läs behörighet till det virtuella nätverk eller den prenumeration som du vill använda peer-datorn med. Ange det fullständiga resurs-ID: t för det virtuella nätverk som du vill peer-koppla med i rutan **resurs-ID** som visades när du markerade rutan. Resurs-ID: t som du anger måste vara för ett virtuellt nätverk som finns i samma eller som stöds av en [annan](#requirements-and-constraints) Azure- [region](https://azure.microsoft.com/regions) som det här virtuella nätverket. Det fullständiga resurs-ID: t ser ut ungefär så här `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` . Du kan hämta resurs-ID för ett virtuellt nätverk genom att visa egenskaperna för ett virtuellt nätverk. Information om hur du visar egenskaperna för ett virtuellt nätverk finns i [Hantera virtuella nätverk](manage-virtual-network.md#view-virtual-networks-and-settings). Om prenumerationen är kopplad till en annan Azure Active Directory-klient än prenumerationen med det virtuella nätverk som du skapar peer-kopplingen från måste du först lägga till en användare från varje klient organisation som [gäst användare](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) i den motsatta klienten.
    - **Prenumeration:** Välj [prenumerationen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) för det virtuella nätverk som du vill peer-koppla med. En eller flera prenumerationer visas, beroende på hur många prenumerationer ditt konto har Läs behörighet för. Om du har markerat kryss rutan **resurs-ID** är den här inställningen inte tillgänglig.
    - **Virtuellt nätverk:** Välj det virtuella nätverk som du vill peer-koppla med. Du kan välja ett virtuellt nätverk som skapats via Azures distributions modell. Om du vill välja ett virtuellt nätverk i en annan region måste du välja ett virtuellt nätverk i en region som [stöds](#cross-region). Du måste ha Läs behörighet till det virtuella nätverket för att det ska synas i listan. Om ett virtuellt nätverk är listat, men nedtonat, kan det bero på att adress utrymmet för det virtuella nätverket överlappar adress utrymmet för det virtuella nätverket. Om virtuella nätverks adress utrymmen överlappar varandra, kan de inte peer-kopplas. Om du har markerat kryss rutan **resurs-ID** är den här inställningen inte tillgänglig.
    - **Tillåt åtkomst till virtuellt nätverk:** Välj **aktive rad** (standard) om du vill aktivera kommunikation mellan de två virtuella nätverken via standard `VirtualNetwork` flödet. Genom att aktivera kommunikation mellan virtuella nätverk kan resurser som är anslutna till ett virtuellt nätverk kommunicera med varandra med samma bandbredd och latens som om de var anslutna till samma virtuella nätverk. All kommunikation mellan resurser i de två virtuella nätverken är över Azures privata nätverk. Taggen **VirtualNetwork** för nätverks säkerhets grupper omfattar det virtuella nätverket och peer-kopplat virtuellt nätverk när den här inställningen är **aktive rad**. (Mer information om tjänst taggar för nätverks säkerhets grupper finns i [Översikt över nätverks säkerhets grupper](./network-security-groups-overview.md#service-tags).) Välj **inaktive rad** om du inte vill att trafik ska flöda till det peer-virtuella nätverket som standard. Du kan välja **inaktive rad** om du har peer-koppla ett virtuellt nätverk med ett annat virtuellt nätverk, men ibland vill inaktivera standard trafik flödet mellan de två virtuella nätverken. Du kanske tycker att aktivering/inaktive ring är bekvämare än att ta bort och skapa peer-kopplingar på nytt. När den här inställningen är inaktive rad flödar inte trafiken mellan peer-och peer-nätverk som standard. trafik kan dock fortfarande flöda om det uttryckligen tillåts via en regel för [nätverks säkerhets grupp](./network-security-groups-overview.md) som innehåller lämpliga IP-adresser eller program säkerhets grupper.
      > [!WARNING]
      > Om du inaktiverar inställningen **Tillåt åtkomst till virtuellt nätverk** ändras endast definitionen för **VirtualNetwork** -tjänst tag gen. Det förhindrar *inte* fullständigt trafikflöde över peer-anslutningen, vilket beskrivs i den här inställnings beskrivningen.    
    - **Tillåt vidarebefordrad trafik:** Markera den här kryss rutan om du vill tillåta trafik som *vidarebefordras* av en virtuell nätverks installation i ett virtuellt nätverk (som inte härstammar från det virtuella nätverket) för att flöda till det virtuella nätverket via en peering. Överväg till exempel tre virtuella nätverk som heter Spoke1, Spoke2 och Hub. Det finns en peering mellan varje eker-virtuellt nätverk och det virtuella hubb nätverket, men peer kopplingar finns inte mellan de virtuella eker-nätverken. En virtuell nätverks installation distribueras i det virtuella hubb nätverket och användardefinierade vägar tillämpas på varje eker-virtuellt nätverk som dirigerar trafik mellan undernät via den virtuella nätverks enheten. Om den här kryss rutan inte kontrol leras för peering mellan varje ekrar i det virtuella nätverket och det virtuella hubb nätverket, flödar inte trafiken mellan de virtuella ekrarna i eker eftersom navet inte vidarebefordrar trafiken mellan de virtuella nätverken. När den här funktionen aktive ras tillåts vidarebefordrad trafik via peering, men inga användardefinierade vägar eller virtuella nätverks enheter skapas. Användardefinierade vägar och virtuella nätverks enheter skapas separat. Lär dig mer om [användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). Du behöver inte kontrol lera den här inställningen om trafiken vidarebefordras mellan virtuella nätverk via en Azure-VPN Gateway.
    - **Tillåt Gateway-överföring:** Markera den här kryss rutan om du har en virtuell nätverksgateway kopplad till det här virtuella nätverket och vill tillåta trafik från det peer-kopplade virtuella nätverket att flöda genom gatewayen. Det här virtuella nätverket kan till exempel vara kopplat till ett lokalt nätverk via en virtuell nätverksgateway. Gatewayen kan vara en ExpressRoute eller VPN-gateway. Genom att markera den här kryss rutan kan trafik från det peer-kopplade virtuella nätverket flöda genom den gateway som är kopplad till det här virtuella nätverket till det lokala nätverket. Om du markerar den här kryss rutan kan inte ett peer-kopplat virtuellt nätverk ha en konfigurerad Gateway. Kryss rutan **Använd fjärranslutna gatewayer** måste markeras när du konfigurerar peering från det andra virtuella nätverket till det virtuella nätverket. Om du lämnar den här rutan omarkerad (standard), flödar trafiken från det peer-kopplade virtuella nätverket fortfarande till det här virtuella nätverket, men kan inte flöda genom en virtuell nätverksgateway som är kopplad till det virtuella nätverket. Om peering är mellan ett virtuellt nätverk (Resource Manager) och ett virtuellt nätverk (klassiskt) måste gatewayen finnas i det virtuella nätverket (Resource Manager).

       Förutom att vidarebefordra trafik till ett lokalt nätverk, kan en VPN-Gateway vidarebefordra nätverks trafik mellan virtuella nätverk som är peer-kopplade med det virtuella nätverk där gatewayen finns, utan att de virtuella nätverken behöver vara peer-kopplade till varandra. Att använda en VPN-gateway för att vidarebefordra trafik är användbart när du vill använda en VPN-gateway i en hubb (se exemplet för hubb och ekrar som beskrivs för **att tillåta vidarebefordrad trafik**) det virtuella nätverket för att dirigera trafik mellan ekrar virtuella nätverk som inte är peer-kopplade med varandra. Mer information om hur du tillåter användning av en gateway för överföring finns i [Konfigurera en VPN-gateway för överföring i en peering för virtuella nätverk](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Det här scenariot kräver implementering av användardefinierade vägar som anger den virtuella Nätverksgatewayen som nästa hopp typ. Lär dig mer om [användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). Du kan bara ange en VPN-gateway som nästa hopp typ i en användardefinierad väg. du kan inte ange en ExpressRoute-gateway som nästa hopp typ i en användardefinierad väg.

    - **Använd fjärrgatewayer:** Markera den här kryss rutan om du vill tillåta att trafik från det här virtuella nätverket flödar genom en virtuell nätverksgateway som är kopplad till det virtuella nätverk som du peer-koppla med. Till exempel har det virtuella nätverket som du peering med en VPN-Gateway ansluten till som möjliggör kommunikation till ett lokalt nätverk.  Genom att markera den här kryss rutan kan trafik från det här virtuella nätverket flöda via VPN-gatewayen som är kopplad till det peer-kopplade virtuella nätverket. Om du markerar den här kryss rutan måste det peer-kopplade virtuella nätverket ha en virtuell nätverksgateway ansluten och kryss rutan **Tillåt Gateway-överföring** måste vara markerad. Om du lämnar den här rutan omarkerad (standard) kan trafik från det peer-kopplade virtuella nätverket fortfarande flöda till det här virtuella nätverket, men det går inte att flöda genom en virtuell nätverksgateway som är kopplad till det virtuella nätverket.
    
      Den här inställningen kan vara aktive rad för endast en peering för det här virtuella nätverket.

      Du kan inte använda fjärrgatewayer om du redan har en gateway som kon figurer ATS i det virtuella nätverket. Mer information om hur du använder en gateway för överföring finns i [Konfigurera en VPN-gateway för överföring i en peering för virtuellt nätverk](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        
    > [!NOTE]
    > Om du använder en Virtual Network Gateway för att skicka lokal trafik transitivt till ett peer-använt VNet, måste IP-adressintervallet för peer-nätverk för den lokala VPN-enheten vara inställt på "intressant" trafik. Annars kommer dina lokala resurser inte att kunna kommunicera med resurser i det peer-kopplade VNet.

6. Välj **OK** för att lägga till peer koppling till det virtuella nätverk som du har valt.

Stegvisa instruktioner för hur du implementerar peering mellan virtuella nätverk i olika prenumerationer och distributions modeller finns i [Nästa steg](#next-steps).

### <a name="commands"></a>Kommandon

- **Azure CLI**: [AZ Network VNet peering Create](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Visa eller ändra inställningar för peering

Innan du ändrar en peering bör du bekanta dig med kraven och begränsningarna och [de behörigheter som krävs](#permissions).

1. Ange *virtuella nätverk* i sökrutan i rutan Sök högst upp i portalen. När **virtuella nätverk** visas i Sök resultaten väljer du det. Välj inte **virtuella nätverk (klassisk)** om det visas i listan, eftersom du inte kan skapa en peering från ett virtuellt nätverk som distribueras via den klassiska distributions modellen.
2. Välj det virtuella nätverk i listan som du vill ändra peering-inställningar för.
3. Under **Inställningar** väljer du **peering**.
4. Välj den peering som du vill visa eller ändra inställningarna för.
5. Ändra lämplig inställning. Läs om alternativen för varje inställning i [steg 5](#add-peering) i skapa en peering.
6. Välj **Spara**.

**Kommandon**

- **Azure CLI**: [AZ Network VNet peering-lista](/cli/azure/network/vnet/peering) för att Visa peering för ett virtuellt nätverk, [AZ Network VNet peering show](/cli/azure/network/vnet/peering) för att Visa inställningar för en speciell peering och [AZ Network VNet peering Update](/cli/azure/network/vnet/peering) för att ändra inställningar för peering. |
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) för att hämta Visa peering-inställningar och [set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) för att ändra inställningarna.

## <a name="delete-a-peering"></a>Ta bort en peer koppling

Innan du tar bort en peering bör du kontrol lera att ditt konto har de [behörigheter som krävs](#permissions).

När en peering tas bort flödar inte längre trafik från ett virtuellt nätverk till det peer-virtuella nätverket. När virtuella nätverk som distribueras via Resource Manager har peer-kopplats, har varje virtuellt nätverk en peer-koppling till det andra virtuella nätverket. Om du tar bort peering från ett virtuellt nätverk inaktive ras kommunikationen mellan de virtuella nätverken, men peering tas inte bort från det andra virtuella nätverket. Peering-statusen för peering som finns i det andra virtuella nätverket är **frånkopplad**. Du kan inte återskapa peering förrän du återskapar peering i det första virtuella nätverket och peering-statusen för båda virtuella nätverken ändras till *ansluten*.

Om du vill att virtuella nätverk ska kommunicera ibland, men inte alltid, i stället för att ta bort en peering, kan du ställa in inställningen **Tillåt att virtuella nätverks åtkomst** **inaktive ras** i stället. Läs mer i steg 6 i avsnittet Skapa en peering i den här artikeln. Du kan se till att inaktivera och aktivera nätverks åtkomsten enklare än att ta bort och återskapa peer-kopplingar.

1. Ange *virtuella nätverk* i sökrutan i rutan Sök högst upp i portalen. När **virtuella nätverk** visas i Sök resultaten väljer du det. Välj inte **virtuella nätverk (klassisk)** om det visas i listan, eftersom du inte kan skapa en peering från ett virtuellt nätverk som distribueras via den klassiska distributions modellen.
2. Välj det virtuella nätverk i listan som du vill ta bort en peering för.
3. Under **Inställningar** väljer du **peering**.
4. Välj **...** på höger sida av peering som du vill ta bort, Välj..., Välj **ta bort** och välj sedan **Ja** för att ta bort peer koppling från det första virtuella nätverket.
5. Slutför de föregående stegen för att ta bort peer koppling från det andra virtuella nätverket i peer-kopplingen.

**Kommandon**

- **Azure CLI**: [AZ Network VNet peering Delete](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Krav och begränsningar

- <a name="cross-region"></a>Du kan peer-koppla virtuella nätverk i samma region eller i olika regioner. Peering av virtuella nätverk i olika regioner kallas även *Global VNet-peering*. 
- När du skapar en global peering kan de peer-baserade virtuella nätverken finnas i valfri Azures offentliga moln region eller i moln regioner i Kina eller i myndigheter i myndigheter. Du kan inte peer-koppla över moln. Ett VNet i offentliga Azure-moln kan till exempel inte peer-kopplas till ett VNet i Azure Kina-molnet.
- Resurser i ett virtuellt nätverk kan inte kommunicera med IP-adressen i klientdelen för en intern Basic-lastbalanserare i ett globalt peerkopplat virtuellt nätverk. Stöd för grundläggande lastbalanserare finns bara inom samma region. Stöd för Standard Load Balancer finns för både VNet-peering och global VNet-peering. Tjänster som använder en grundläggande belastningsutjämnare som inte kommer att fungera över global VNet-peering dokumenteras [här.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Du kan använda fjärrgatewayer eller tillåta Gateway-överföring i globalt peer-kopplat virtuella nätverk och lokalt peer-kopplat virtuella nätverk.
- De virtuella nätverken kan finnas i samma eller olika prenumerationer. När du använder peer-virtuella nätverk i olika prenumerationer kan båda prenumerationerna associeras med samma eller olika Azure Active Directory-klienten. Om du inte redan har en AD-klient kan du [skapa en](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant).
- De virtuella nätverk som du peer-koppla måste ha icke-överlappande IP-adressutrymme.
- Du kan inte lägga till adress intervall i eller ta bort adress intervall från ett virtuellt nätverks adress utrymme när ett virtuellt nätverk är peer-kopplat med ett annat virtuellt nätverk. Om du vill lägga till eller ta bort adress intervall, tar du bort peering, lägger till eller tar bort adress intervallen och återskapar sedan peer-kopplingen. Information om hur du lägger till adress intervall i eller tar bort adress intervall från virtuella nätverk finns i [Hantera virtuella nätverk](manage-virtual-network.md).
- Du kan distribuera två virtuella nätverk som distribueras via Resource Manager eller ett virtuellt nätverk som distribueras via Resource Manager med ett virtuellt nätverk som distribueras via den klassiska distributions modellen. Du kan inte peer-koppla två virtuella nätverk som skapats via den klassiska distributions modellen. Om du inte är bekant med Azures distributions modeller kan du läsa artikeln [förstå Azures distributions modeller](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Du kan använda [VPN Gateway](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) för att ansluta två virtuella nätverk som har skapats via den klassiska distributionsmodellen.
- Vid peer-koppling av två virtuella nätverk som skapas via Resource Manager måste en peer-koppling konfigureras för varje virtuellt nätverk i peer-kopplingen. Du ser en av följande typer för peering-status: 
  - *Initierad:* När du skapar peer-kopplingen till det andra virtuella nätverket från det första virtuella nätverket *initieras* peering-statusen. 
  - *Ansluten:* När du skapar peer-kopplingen från det andra virtuella nätverket till det första virtuella nätverket är dess peering-status *ansluten*. Om du visar peering-statusen för det första virtuella nätverket ser du att dess status har ändrats från *initierad* till *ansluten*. Det gick inte att upprätta peering förrän peering-statusen för båda de virtuella nätverkets peering är *ansluten*.
- När du peer-koppla ett virtuellt nätverk som skapats via Resource Manager med ett virtuellt nätverk som skapats via den klassiska distributions modellen, konfigurerar du bara en peering för det virtuella nätverk som distribueras via Resource Manager. Du kan inte konfigurera peering för ett virtuellt nätverk (klassisk) eller mellan två virtuella nätverk som distribueras via den klassiska distributions modellen. När du skapar peer-kopplingen från det virtuella nätverket (Resource Manager) till det virtuella nätverket (klassisk) *uppdateras* peering-statusen och ändras sedan strax till *ansluten*.
- En peering upprättas mellan två virtuella nätverk. Motparter själva är inte transitiva. Om du skapar peering mellan:
  - VirtualNetwork1 & VirtualNetwork2-VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3-VirtualNetwork2 & VirtualNetwork3


  Det finns ingen peering mellan VirtualNetwork1 och VirtualNetwork3 via VirtualNetwork2. Om du vill skapa en virtuell nätverks-peering mellan VirtualNetwork1 och VirtualNetwork3 måste du skapa en peering mellan VirtualNetwork1 och VirtualNetwork3. Det finns ingen peering mellan VirtualNetwork1 och VirtualNetwork3 via VirtualNetwork2. Om du vill att VirtualNetwork1 och VirtualNetwork3 ska kommunicera direkt måste du skapa en explicit peering mellan VirtualNetwork1 och VirtualNetwork3 eller gå igenom en NVA i Hubbs nätverket.  
- Du kan inte matcha namn i peer-kopplat virtuella nätverk med hjälp av standard namn matchning i Azure. Om du vill matcha namn i andra virtuella nätverk måste du använda [Azure DNS för privata domäner](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller en anpassad DNS-server. Information om hur du konfigurerar en egen DNS-Server finns i [namn matchning med hjälp av en egen DNS-Server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Resurser i peer-kopplade virtuella nätverk i samma region kan kommunicera med varandra med samma bandbredd och svars tid som om de fanns i samma virtuella nätverk. Varje virtuell dator storlek har dock sin egen maximala nätverks bandbredd. Mer information om maximal nätverks bandbredd för olika storlekar på virtuella datorer finns i storlekar för virtuella [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -datorer.
- Ett virtuellt nätverk kan peer-kopplas till ett annat virtuellt nätverk och även anslutas till ett annat virtuellt nätverk med en virtuell Azure-nätverksgateway. När virtuella nätverk är anslutna via både peering och en gateway, flödar trafiken mellan de virtuella nätverken genom peering-konfigurationen, i stället för gatewayen.
- Punkt-till-plats-VPN-klienter måste laddas ned igen efter att det virtuella nätverkets peering har kon figurer ATS för att se till att de nya vägarna laddas ned till klienten.
- En nominell avgift tas ut för ingående och utgående trafik som använder virtuell nätverks-peering. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Behörigheter

De konton som du använder för att arbeta med peering av virtuella nätverk måste tilldelas följande roller:

- [Nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): för ett virtuellt nätverk som distribueras via Resource Manager.
- [Klassisk nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): för ett virtuellt nätverk som distribueras via den klassiska distributions modellen.

Om ditt konto inte har tilldelats någon av de tidigare rollerna, måste det tilldelas en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats nödvändiga åtgärder från följande tabell:

| Action                                                          | Name |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Krävs för att skapa en peering från ett virtuellt nätverk A till virtuellt nätverk B. virtuellt nätverk A måste vara ett virtuellt nätverk (Resource Manager)          |
| Microsoft. Network/virtualNetworks/peer/Action                   | Krävs för att skapa en peering från virtuellt nätverk B (Resource Manager) till virtuellt nätverk A                                                       |
| Microsoft. ClassicNetwork/virtualNetworks/peer/Action                   | Krävs för att skapa en peering från Virtual Network B (klassisk) till virtuellt nätverk A                                                                |
| Microsoft. Network/virtualNetworks/virtualNetworkPeerings/Read   | Läs en virtuell nätverks-peering   |
| Microsoft. Network/virtualNetworks/virtualNetworkPeerings/Delete | Ta bort en virtuell nätverks-peering |

## <a name="next-steps"></a>Nästa steg

- Peering för virtuellt nätverk skapas mellan virtuella nätverk som skapats via samma eller olika distributionsmodeller som finns i samma eller olika prenumerationer. Genomför en självstudiekurs för något av följande scenarier:

  |Azure-distributionsmodell             | Prenumeration  |
  |---------                          |---------|
  |Båda Resource Manager              |[Samma](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Olika](create-peering-different-subscriptions.md)|
  |En Resource Manager, en klassisk  |[Samma](create-peering-different-deployment-models.md)|
  |                                   |[Olika](create-peering-different-deployment-models-subscriptions.md)|

- Lär dig hur du skapar en [nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Skapa en virtuell nätverks-peering med [PowerShell](powershell-samples.md) -eller [Azure CLI](cli-samples.md) -exempel skript eller med Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tilldela [Azure policy definitioner](./policy-reference.md) för virtuella nätverk