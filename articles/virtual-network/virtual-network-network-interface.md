---
title: Skapa, ändra eller ta bort ett Azure-nätverksgränssnitt
titlesuffix: Azure Virtual Network
description: Lär dig vad ett nätverksgränssnitt är och hur du skapar, ändrar inställningar för och tar bort ett.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/22/2020
ms.author: kumud
ms.openlocfilehash: 8003bf14bcade08f36a7877fdb3a53998aff9e63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773078"
---
# <a name="create-change-or-delete-a-network-interface"></a>Skapa, ändra eller ta bort ett nätverksgränssnitt

Lär dig hur du skapar, ändrar inställningar för och tar bort ett nätverksgränssnitt. Med ett nätverksgränssnitt kan en virtuell Azure-dator kommunicera med Internet, Azure och lokala resurser. När du skapar en virtuell dator med Azure Portal skapar portalen ett nätverksgränssnitt med standardinställningar åt dig. Du kan i stället välja att skapa nätverksgränssnitt med anpassade inställningar och lägga till ett eller flera nätverksgränssnitt till en virtuell dator när du skapar det. Du kanske också vill ändra standardinställningarna för nätverksgränssnittet för ett befintligt nätverksgränssnitt. Den här artikeln beskriver hur du skapar ett nätverksgränssnitt med anpassade inställningar, ändrar befintliga inställningar, till exempel tilldelning av nätverksfilter (nätverkssäkerhetsgrupp), tilldelning av undernät, DNS-serverinställningar och IP-vidarebefordran samt borttagning av ett nätverksgränssnitt.

Om du behöver lägga till, ändra eller ta bort IP-adresser för ett nätverksgränssnitt kan du gå till [Hantera IP-adresser.](virtual-network-network-interface-addresses.md) Om du behöver lägga till nätverksgränssnitt till eller ta bort nätverksgränssnitt från virtuella datorer kan du gå till [Lägg till eller ta bort nätverksgränssnitt.](virtual-network-network-interface-vm.md)

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande uppgifter innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto kan du registrera dig för ett kostnadsfritt [utvärderingskonto.](https://azure.microsoft.com/free)
- Om du använder portalen öppnar du https://portal.azure.com och loggar in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra [kommandona i Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder Azure CLI-kommandon (Kommandoradsgränssnitt) för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här självstudien kräver Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra för `az login` att skapa en anslutning till Azure.

Det konto som du loggar in på, eller [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ansluter till Azure [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) med, måste tilldelas till nätverksdeltagarerollen eller till en anpassad roll som har tilldelats lämpliga åtgärder som anges i [Behörigheter](#permissions).

## <a name="create-a-network-interface"></a>Skapa ett nätverksgränssnitt

När du skapar en virtuell dator med Azure Portal skapar portalen ett nätverksgränssnitt med standardinställningar åt dig. Om du hellre vill ange alla inställningar för nätverksgränssnittet kan du skapa ett nätverksgränssnitt med anpassade inställningar och koppla nätverksgränssnittet till en virtuell dator när du skapar den virtuella datorn (med Hjälp av PowerShell eller Azure CLI). Du kan också skapa ett nätverksgränssnitt och lägga till det i en befintlig virtuell dator (med PowerShell eller Azure CLI). Information om hur du skapar en virtuell dator med ett befintligt nätverksgränssnitt eller hur du lägger till eller tar bort nätverksgränssnitt från befintliga virtuella datorer finns i Lägga till eller ta bort [nätverksgränssnitt.](virtual-network-network-interface-vm.md) Innan du skapar ett nätverksgränssnitt måste du ha ett befintligt [virtuellt nätverk](manage-virtual-network.md) på samma plats och prenumeration som du skapar ett nätverksgränssnitt i.

1. I rutan som innehåller texten *Sök efter resurser* överst i Azure Portal skriver du *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultatet väljer du det.
2. Välj **+ Lägg** till under **Nätverksgränssnitt**.
3. Ange eller välj värden för följande inställningar och välj sedan **Skapa:**

    |Inställning|Obligatoriskt?|Information|
    |---|---|---|
    |Name|Yes|Namnet måste vara unikt inom den resursgrupp som du väljer. Med tiden har du förmodligen flera nätverksgränssnitt i din Azure-prenumeration. Förslag på hur du skapar en namngivningskonvention som gör det enklare att hantera flera nätverksgränssnitt finns i [Namngivningskonventioner.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) Namnet kan inte ändras när nätverksgränssnittet har skapats.|
    |Virtuellt nätverk|Yes|Välj det virtuella nätverket för nätverksgränssnittet. Du kan bara tilldela ett nätverksgränssnitt till ett virtuellt nätverk som finns i samma prenumeration och plats som nätverksgränssnittet. När ett nätverksgränssnitt har skapats kan du inte ändra det virtuella nätverk som det har tilldelats. Den virtuella dator som du lägger till nätverksgränssnittet i måste också finnas på samma plats och prenumeration som nätverksgränssnittet.|
    |Undernät|Yes|Välj ett undernät i det virtuella nätverk som du har valt. Du kan ändra undernätet som nätverksgränssnittet tilldelas till när det har skapats.|
    |Tilldelning av privat IP-adress|Yes| I den här inställningen väljer du tilldelningsmetod för IPv4-adressen. Välj bland följande tilldelningsmetoder: **Dynamisk:** När du väljer det här alternativet tilldelar Azure automatiskt nästa tillgängliga adress från adressutrymmet för det undernät som du har valt. **Statisk:** När du väljer det här alternativet måste du manuellt tilldela en tillgänglig IP-adress inom adressutrymmet för det undernät som du har valt. Statiska och dynamiska adresser ändras inte förrän du ändrar dem eller nätverksgränssnittet tas bort. Du kan ändra tilldelningsmetoden när nätverksgränssnittet har skapats. Azure DHCP-servern tilldelar den här adressen till nätverksgränssnittet i den virtuella datorns operativsystem.|
    |Nätverkssäkerhetsgrupp|No| Lämna inställt **på Ingen,** välj en [befintlig nätverkssäkerhetsgrupp](./network-security-groups-overview.md)eller [skapa en nätverkssäkerhetsgrupp](tutorial-filter-network-traffic.md). Med nätverkssäkerhetsgrupper kan du filtrera nätverkstrafik in och ut från ett nätverksgränssnitt. Du kan använda noll eller en nätverkssäkerhetsgrupp för ett nätverksgränssnitt. Ingen eller en nätverkssäkerhetsgrupp kan också tillämpas på det undernät som nätverksgränssnittet har tilldelats. När en nätverkssäkerhetsgrupp tillämpas på ett nätverksgränssnitt och det undernät som nätverksgränssnittet tilldelas, uppstår ibland oväntade resultat. Information om hur du felsöker nätverkssäkerhetsgrupper som tillämpas på nätverksgränssnitt och undernät finns [i Felsöka nätverkssäkerhetsgrupper.](diagnose-network-traffic-filter-problem.md)|
    |Prenumeration|Yes|Välj en av dina [Azure-prenumerationer.](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) Den virtuella dator som du kopplar ett nätverksgränssnitt till och det virtuella nätverk som du ansluter det till måste finnas i samma prenumeration.|
    |Privat IP-adress (IPv6)|No| Om du markerar den här kryssrutan tilldelas en IPv6-adress till nätverksgränssnittet, utöver den IPv4-adress som tilldelats nätverksgränssnittet. Viktig information om användning av IPv6 med nätverksgränssnitt finns i avsnittet IPv6 i den här artikeln. Du kan inte välja en tilldelningsmetod för IPv6-adressen. Om du väljer att tilldela en IPv6-adress tilldelas den med den dynamiska metoden.
    |IPv6-namn (visas endast när **kryssrutan Privat IP-adress (IPv6)** är markerad) |Ja, om kryssrutan **Privat IP-adress (IPv6)** är markerad.| Det här namnet tilldelas till en sekundär IP-konfiguration för nätverksgränssnittet. Mer information om IP-konfigurationer finns i [Visa inställningar för nätverksgränssnitt.](#view-network-interface-settings)|
    |Resursgrupp|Yes|Välj en befintlig [resursgrupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) eller skapa en. Ett nätverksgränssnitt kan finnas i samma resursgrupp, eller en annan resursgrupp, än den virtuella dator som du kopplar den till, eller det virtuella nätverk som du ansluter den till.|
    |Plats|Ja|Den virtuella dator som du kopplar ett nätverksgränssnitt till och det virtuella nätverk som du ansluter det till måste finnas på samma plats [,](https://azure.microsoft.com/regions)vilket även kallas en region.|

Portalen ger inte möjlighet att tilldela en offentlig IP-adress till nätverksgränssnittet när du skapar det, men portalen skapar en offentlig IP-adress och tilldelar den till ett nätverksgränssnitt när du skapar en virtuell dator med hjälp av portalen. Information om hur du lägger till en offentlig IP-adress i nätverksgränssnittet när du har skapat den finns [i Hantera IP-adresser.](virtual-network-network-interface-addresses.md) Om du vill skapa ett nätverksgränssnitt med en offentlig IP-adress måste du använda CLI eller PowerShell för att skapa nätverksgränssnittet.

Portalen ger inte möjlighet att tilldela nätverksgränssnittet till programsäkerhetsgrupper när du skapar ett nätverksgränssnitt, men det gör Azure CLI och PowerShell. Du kan dock tilldela ett befintligt nätverksgränssnitt till en programsäkerhetsgrupp med hjälp av portalen, så länge nätverksgränssnittet är kopplat till en virtuell dator. Information om hur du tilldelar ett nätverksgränssnitt till en programsäkerhetsgrupp finns i [Lägga till i eller ta bort från programsäkerhetsgrupper.](#add-to-or-remove-from-application-security-groups)

>[!Note]
> Azure tilldelar en MAC-adress till nätverksgränssnittet först när nätverksgränssnittet har kopplats till en virtuell dator och den virtuella datorn startas första gången. Du kan inte ange mac-adressen som Azure tilldelar till nätverksgränssnittet. MAC-adressen förblir tilldelad till nätverksgränssnittet tills nätverksgränssnittet tas bort eller den privata IP-adressen som tilldelats den primära IP-konfigurationen för det primära nätverksgränssnittet ändras. Mer information om IP-adresser och IP-konfigurationer finns i [Hantera IP-adresser](virtual-network-network-interface-addresses.md)

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Visa inställningar för nätverksgränssnitt

Du kan visa och ändra de flesta inställningar för ett nätverksgränssnitt när det har skapats. Portalen visar inte DNS-suffixet eller programsäkerhetsgruppens medlemskap för nätverksgränssnittet. Du kan använda PowerShell- eller Azure [CLI-kommandon för](#view-settings-commands) att visa DNS-suffixet och programsäkerhetsgruppens medlemskap.

1. I rutan som innehåller texten *Sök efter resurser* längst upp i Azure Portal skriver du *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten väljer du det.
2. Välj det nätverksgränssnitt som du vill visa eller ändra inställningarna för i listan.
3. Följande objekt visas för det nätverksgränssnitt som du har valt:
   - **Översikt:** Innehåller information om nätverksgränssnittet, till exempel tilldelade IP-adresser, det virtuella nätverk/undernät som nätverksgränssnittet är kopplat till och den virtuella dator som nätverksgränssnittet är kopplat till (om det är kopplat till ett). Följande bild visar översiktsinställningarna för ett nätverksgränssnitt med namnet **mywebserver256:** ![ Översikt över nätverksgränssnittet](./media/virtual-network-network-interface/nic-overview.png)

     Du kan flytta ett nätverksgränssnitt till en annan resursgrupp eller prenumeration genom att välja (**ändra**) bredvid **resursgruppen eller** **prenumerationsnamnet**. Om du flyttar nätverksgränssnittet till en ny prenumeration måste du flytta alla resurser som är relaterade till nätverksgränssnittet till den. Om nätverksgränssnittet till exempel är kopplat till en virtuell dator måste du också flytta den virtuella datorn och andra resurser relaterade till virtuella datorer. Information om hur du flyttar ett nätverksgränssnitt [finns i Flytta resurs till en ny resursgrupp eller prenumeration.](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal) Artikeln innehåller krav och hur du flyttar resurser med hjälp av Azure Portal, PowerShell och Azure CLI.
   - **IP-konfigurationer:** Offentliga och privata IPv4- och IPv6-adresser som tilldelats IP-konfigurationer visas här. Om en IPv6-adress tilldelas till en IP-konfiguration visas inte adressen. Mer information om IP-konfigurationer och hur du lägger till och tar bort IP-adresser finns i [Konfigurera IP-adresser för ett Azure-nätverksgränssnitt.](virtual-network-network-interface-addresses.md) IP-vidarebefordran och tilldelning av undernät konfigureras också i det här avsnittet. Mer information om de här inställningarna finns i [Aktivera eller inaktivera IP-vidarebefordran](#enable-or-disable-ip-forwarding) och [Ändra tilldelning av undernät.](#change-subnet-assignment)
   - **DNS-servrar:** Du kan ange vilken DNS-server som ett nätverksgränssnitt tilldelas av Azure DHCP-servrarna. Nätverksgränssnittet kan ärva inställningen från det virtuella nätverk som nätverksgränssnittet har tilldelats, eller ha en anpassad inställning som åsidosätter inställningen för det virtuella nätverk som det är tilldelat till. Information om hur du ändrar vad som visas finns i [Ändra DNS-servrar.](#change-dns-servers)
   - **Nätverkssäkerhetsgrupp (NSG):** Visar vilken NSG som är associerad med nätverksgränssnittet (om det finns någon). En NSG innehåller regler för inkommande och utgående trafik för att filtrera nätverkstrafik för nätverksgränssnittet. Om en NSG är associerad med nätverksgränssnittet visas namnet på den associerade NSG:n. Information om hur du ändrar vad som visas finns [i Associera eller koppla bort en nätverkssäkerhetsgrupp.](#associate-or-dissociate-a-network-security-group)
   - **Egenskaper:** Visar viktiga inställningar för nätverksgränssnittet, inklusive dess MAC-adress (tomt om nätverksgränssnittet inte är kopplat till en virtuell dator) och den prenumeration som det finns i.
   - **Gällande säkerhetsregler:**  Säkerhetsregler visas om nätverksgränssnittet är kopplat till en virtuell dator som körs och en NSG är associerad med nätverksgränssnittet, det undernät som det är tilldelat till, eller både och. Mer information om vad som visas finns i [Visa gällande säkerhetsregler.](#view-effective-security-rules) Mer information om nätverkssäkerhetsgrupper finns i [Nätverkssäkerhetsgrupper.](./network-security-groups-overview.md)
   - **Gällande vägar:** Vägar visas om nätverksgränssnittet är kopplat till en virtuell dator som körs. Vägarna är en kombination av Azures standardvägar, användardefinierade vägar och eventuella BGP-vägar som kan finnas för det undernät som nätverksgränssnittet är kopplat till. Mer information om vad som visas finns i [Visa effektiva vägar.](#view-effective-routes) Mer information om Standardvägar i Azure och användardefinierade vägar finns i [Routningsöversikt.](virtual-networks-udr-overview.md)
Vanliga Azure Resource Manager: Mer information om vanliga Azure Resource Manager finns i [Aktivitetslogg,](../azure-monitor/essentials/platform-logs-overview.md) [Åtkomstkontroll (IAM),](../role-based-access-control/overview.md) [Taggar,](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [Lås](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)och [Automation-skript.](../azure-resource-manager/templates/export-template-portal.md)

<a name="view-settings-commands"></a>**Kommandon**

Om en IPv6-adress tilldelas till ett nätverksgränssnitt returnerar PowerShell-utdata det faktum att adressen har tilldelats, men den returnerar inte den tilldelade adressen. På samma sätt returnerar CLI det faktum att adressen är tilldelad, men *returnerar null* i utdata för adressen.

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic) to view network interfaces in the subscription; [az network nic show](/cli/azure/network/nic) to view settings for a network interface|
|PowerShell|[Get-AzNetworkInterface för](/powershell/module/az.network/get-aznetworkinterface) att visa nätverksgränssnitt i prenumerationen eller visa inställningar för ett nätverksgränssnitt|

## <a name="change-dns-servers"></a>Ändra DNS-servrar

DNS-servern tilldelas av Azure DHCP-servern till nätverksgränssnittet i operativsystemet för den virtuella datorn. Den tilldelade DNS-servern är den DNS-serverinställning som används för ett nätverksgränssnitt. Mer information om namnmatchningsinställningar för ett nätverksgränssnitt finns i [Namnmatchning för virtuella datorer.](virtual-networks-name-resolution-for-vms-and-role-instances.md) Nätverksgränssnittet kan ärva inställningarna från det virtuella nätverket eller använda egna unika inställningar som åsidosätter inställningen för det virtuella nätverket.

1. I rutan som innehåller texten *Sök efter resurser* överst i Azure Portal skriver du *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultatet väljer du det.
2. Välj det nätverksgränssnitt som du vill ändra en DNS-server för i listan.
3. Välj **DNS-servrar** under **INSTÄLLNINGAR.**
4. Välj antingen:
   - **Ärv från virtuellt** nätverk: Välj det här alternativet om du vill ärva DNS-serverinställningen som definierats för det virtuella nätverk som nätverksgränssnittet är kopplat till. På nivån för virtuellt nätverk definieras antingen en anpassad DNS-server eller den Azure-tillhandahållna DNS-servern. Den Azure-tillhandahållna DNS-servern kan matcha värdnamn för resurser som tilldelats till samma virtuella nätverk. FQDN måste användas för att matcha resurser som tilldelats till olika virtuella nätverk.
   - **Anpassad:** Du kan konfigurera en egen DNS-server för att matcha namn i flera virtuella nätverk. Ange IP-adressen för den server som du vill använda som DNS-server. DEN DNS-serveradress som du anger tilldelas endast till det här nätverksgränssnittet och åsidosätter alla DNS-inställningar för det virtuella nätverk som nätverksgränssnittet är kopplat till.
     >[!Note]
     >Om den virtuella datorn använder ett nätverkskort som ingår i en tillgänglighetsuppsättning ärvs alla DNS-servrar som har angetts för var och en av de virtuella datorerna från alla nätverkskort som ingår i tillgänglighetsuppsättningen.
5. Välj **Spara**.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Aktivera eller inaktivera IP-vidarebefordran

IP-vidarebefordran gör det möjligt för den virtuella datorn att ha ett nätverksgränssnitt kopplat till:
- Ta emot nätverkstrafik som inte är avsedd för någon av de IP-adresser som tilldelats någon av de IP-konfigurationer som tilldelats nätverksgränssnittet.
- Skicka nätverkstrafik med en annan IP-källadress än den som tilldelats till en av ett nätverksgränssnitts IP-konfigurationer.

Inställningen måste vara aktiverad för varje nätverksgränssnitt som är kopplat till den virtuella datorn som tar emot trafik som den virtuella datorn behöver vidarebefordra. En virtuell dator kan vidarebefordra trafik oavsett om den har flera nätverksgränssnitt eller ett enda nätverksgränssnitt kopplat till den. Ip-vidarebefordran är en Azure-inställning, men den virtuella datorn måste också köra ett program som kan vidarebefordra trafiken, till exempel brandvägg, WAN-optimering och belastningsutjämningsprogram. När en virtuell dator kör nätverksprogram kallas den virtuella datorn ofta för en virtuell nätverksinstallation. Du kan visa en lista över virtuella nätverksutrustning som är redo att distribueras i [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). IP-vidarebefordran används vanligtvis med användardefinierade vägar. Mer information om användardefinierade vägar finns i [Användardefinierade vägar.](virtual-networks-udr-overview.md)

1. I rutan som innehåller texten *Sök efter resurser* längst upp i Azure Portal skriver du *nätverksgränssnitt .* När **nätverksgränssnitt** visas i sökresultaten väljer du det.
2. Välj det nätverksgränssnitt som du vill aktivera eller inaktivera IP-vidarebefordran för.
3. Välj **IP-konfigurationer** i **avsnittet** INSTÄLLNINGAR.
4. Välj **Aktiverad** eller **Inaktiverad** (standardinställning) för att ändra inställningen.
5. Välj **Spara**.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Ändra tilldelning av undernät

Du kan ändra undernätet, men inte det virtuella nätverket, som ett nätverksgränssnitt har tilldelats.

1. I rutan som innehåller texten *Sök efter resurser* längst upp i Azure Portal skriver du *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten väljer du det.
2. Välj det nätverksgränssnitt som du vill ändra undernätstilldelningen för.
3. Välj **IP-konfigurationer** under **INSTÄLLNINGAR.** Om några privata IP-adresser för ip-konfigurationer i listan har **(statiska)** bredvid sig, måste du ändra tilldelningsmetoden för IP-adresser till dynamisk genom att följa stegen nedan. Alla privata IP-adresser måste tilldelas med den dynamiska tilldelningsmetoden för att ändra undernätstilldelningen för nätverksgränssnittet. Om adresserna tilldelas med den dynamiska metoden fortsätter du till steg fem. Om några IPv4-adresser tilldelas med den statiska tilldelningsmetoden utför du följande steg för att ändra tilldelningsmetoden till dynamisk:
   - Välj den IP-konfiguration som du vill ändra tilldelningsmetoden för IPv4-adresser för i listan över IP-konfigurationer.
   - Välj **Dynamisk** som tilldelningsmetod för privat **IP-adress.** Du kan inte tilldela en IPv6-adress med den statiska tilldelningsmetoden.
   - Välj **Spara**.
4. Välj det undernät som du vill flytta nätverksgränssnittet till **från listrutan** Undernät.
5. Välj **Spara**. Nya dynamiska adresser tilldelas från undernätets adressintervall för det nya undernätet. När du har tilldelar nätverksgränssnittet till ett nytt undernät kan du tilldela en statisk IPv4-adress från det nya adressintervallet för undernätet om du vill. Mer information om hur du lägger till, ändrar och tar bort IP-adresser för ett nätverksgränssnitt finns [i Hantera IP-adresser.](virtual-network-network-interface-addresses.md)

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Lägga till eller ta bort från programsäkerhetsgrupper

Du kan bara lägga till ett nätverksgränssnitt till eller ta bort ett nätverksgränssnitt från en programsäkerhetsgrupp med hjälp av portalen om nätverksgränssnittet är kopplat till en virtuell dator. Du kan använda PowerShell eller Azure CLI för att lägga till ett nätverksgränssnitt till eller ta bort ett nätverksgränssnitt från en programsäkerhetsgrupp, oavsett om nätverksgränssnittet är kopplat till en virtuell dator eller inte. Läs mer om [programsäkerhetsgrupper](./network-security-groups-overview.md#application-security-groups) och hur du [skapar en programsäkerhetsgrupp.](manage-network-security-group.md)

1. I rutan *Sök efter resurser,* tjänster och dokument högst upp i portalen börjar du skriva namnet på en virtuell dator som har ett nätverksgränssnitt som du vill lägga till eller ta bort från en programsäkerhetsgrupp. När namnet på den virtuella datorn visas i sökresultatet väljer du det.
2. Under **INSTÄLLNINGAR** väljer du **Nätverk**.  Välj **Programsäkerhetsgrupper** och sedan Konfigurera programsäkerhetsgrupper de programsäkerhetsgrupper som du vill lägga till nätverksgränssnittet i, eller avmarkera de programsäkerhetsgrupper som du vill ta bort nätverksgränssnittet från och välj **sedan Spara**.  Endast nätverksgränssnitt som finns i samma virtuella nätverk kan läggas till i samma programsäkerhetsgrupp. Programsäkerhetsgruppen måste finnas på samma plats som nätverksgränssnittet.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Associera eller koppla bort en nätverkssäkerhetsgrupp

1. I sökrutan överst i portalen anger du *nätverksgränssnitt* i sökrutan. När **nätverksgränssnitt** visas i sökresultaten väljer du det.
2. Välj nätverksgränssnittet i listan som du vill associera en nätverkssäkerhetsgrupp med eller koppla bort en nätverkssäkerhetsgrupp från.
3. Välj **Nätverkssäkerhetsgrupp** under **INSTÄLLNINGAR.**
4. Välj **Redigera**.
5. Välj **Nätverkssäkerhetsgrupp** och välj sedan den nätverkssäkerhetsgrupp som du vill koppla till nätverksgränssnittet, eller välj **Ingen** för att koppla bort en nätverkssäkerhetsgrupp.
6. Välj **Spara**.

**Kommandon**

- Azure CLI: [az network nic update](/cli/azure/network/nic#az_network_nic_update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Ta bort ett nätverksgränssnitt

Du kan ta bort ett nätverksgränssnitt så länge det inte är kopplat till en virtuell dator. Om ett nätverksgränssnitt är kopplat till en virtuell dator måste du först placera den virtuella datorn i tillståndet stoppad (frikopplad) och sedan koppla från nätverksgränssnittet från den virtuella datorn. Om du vill koppla från ett nätverksgränssnitt från en virtuell dator slutför du stegen i [Koppla från ett nätverksgränssnitt från en virtuell dator](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Du kan dock inte koppla från ett nätverksgränssnitt från en virtuell dator om det är det enda nätverksgränssnittet som är kopplat till den virtuella datorn. En virtuell dator måste alltid ha minst ett anslutet nätverksgränssnitt. Om du tar bort en virtuell dator så kopplas alla nätverksgränssnitt bort, men nätverksgränssnitten tas inte bort.

1. I rutan som innehåller texten *Sök efter resurser* överst i Azure Portal skriver du *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultatet väljer du det.
2. Välj nätverksgränssnittet i listan som du vill ta bort.
3. Under **Översikt väljer** du Ta **bort.**
4. Välj **Ja** för att bekräfta borttagningen av nätverksgränssnittet.

När du tar bort ett nätverksgränssnitt släpps alla MAC- eller IP-adresser som tilldelats det.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Lösa anslutningsproblem

Om du inte kan kommunicera med eller från en virtuell dator kan det vara säkerhetsregler eller vägar för nätverkssäkerhetsgruppen som är effektiva för ett nätverksgränssnitt som orsakar problemet. Du har följande alternativ för att lösa problemet:

### <a name="view-effective-security-rules"></a>Visa effektiva säkerhetsregler

De gällande säkerhetsreglerna för varje nätverksgränssnitt som är kopplat till en virtuell dator är en kombination av de regler som du har skapat i en nätverkssäkerhetsgrupp och [standardsäkerhetsreglerna](./network-security-groups-overview.md#default-security-rules). Att förstå de effektiva säkerhetsreglerna för ett nätverksgränssnitt kan hjälpa dig att avgöra varför du inte kan kommunicera med eller från en virtuell dator. Du kan visa gällande regler för alla nätverksgränssnitt som är kopplade till en virtuell dator som körs.

1. I sökrutan högst upp i portalen anger du namnet på en virtuell dator som du vill visa gällande säkerhetsregler för. Om du inte känner till namnet på en virtuell dator anger du *virtuella datorer* i sökrutan. När **Virtuella datorer** visas i sökresultatet markerar du det och väljer sedan en virtuell dator i listan.
2. Välj **Nätverk** under **INSTÄLLNINGAR.**
3. Välj namnet på ett nätverksgränssnitt.
4. Välj **Gällande säkerhetsregler** under **SUPPORT + FELSÖKNING.**
5. Granska listan över gällande säkerhetsregler för att avgöra om rätt regler finns för den inkommande och utgående kommunikationen som krävs. Läs mer om vad du ser i listan i [Översikt över nätverkssäkerhetsgrupp.](./network-security-groups-overview.md)

Funktionen Kontrollera IP-flöde i Azure Network Watcher kan också hjälpa dig att avgöra om säkerhetsregler förhindrar kommunikation mellan en virtuell dator och en slutpunkt. Mer information finns i Kontrollera [IP-flöde.](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

**Kommandon**

- Azure CLI: [az network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Visa effektiva vägar

De effektiva vägarna för nätverksgränssnitt som är kopplade till en virtuell dator är en kombination av standardvägar, alla vägar som du har skapat och alla vägar som sprids från lokala nätverk via BGP via en virtuell Nätverksgateway i Azure. Att förstå de effektiva vägarna för ett nätverksgränssnitt kan hjälpa dig att avgöra varför du inte kan kommunicera till eller från en virtuell dator. Du kan visa de gällande vägarna för alla nätverksgränssnitt som är anslutna till en virtuell dator som körs.

1. I sökrutan högst upp i portalen anger du namnet på en virtuell dator som du vill visa gällande säkerhetsregler för. Om du inte känner till namnet på en virtuell dator anger du *virtuella datorer* i sökrutan. När **Virtuella datorer** visas i sökresultatet markerar du det och väljer sedan en virtuell dator i listan.
2. Välj **Nätverk** under **INSTÄLLNINGAR.**
3. Välj namnet på ett nätverksgränssnitt.
4. Välj **Gällande vägar** under SUPPORT + **FELSÖKNING.**
5. Granska listan över effektiva vägar för att avgöra om rätt vägar finns för din nödvändiga inkommande och utgående kommunikation. Läs mer om vad du ser i listan i Routing [overview (Routningsöversikt).](virtual-networks-udr-overview.md)

Nästa hopp-funktionen i Azure Network Watcher kan också hjälpa dig att avgöra om vägar förhindrar kommunikation mellan en virtuell dator och en slutpunkt. Mer information finns i [Nästa hopp](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Kommandon**

- Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i nätverksgränssnitt [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) måste ditt konto [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tilldelas till nätverksdeltagarerollen eller till en anpassad roll som har tilldelats lämpliga behörigheter som anges i följande tabell:

| Action                                                                     | Name                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Hämta nätverksgränssnitt                                     |
| Microsoft.Network/networkInterfaces/write                                  | Skapa eller uppdatera nätverksgränssnitt                        |
| Microsoft.Network/networkInterfaces/join/action                            | Koppla ett nätverksgränssnitt till en virtuell dator           |
| Microsoft.Network/networkInterfaces/delete                                 | Ta bort nätverksgränssnitt                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Anslut en resurs till ett nätverksgränssnitt via en ...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Hämta den effektiva vägtabellen för nätverksgränssnittet               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Hämta effektiva säkerhetsgrupper för nätverksgränssnitt           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Hämta lastbalanserare för nätverksgränssnitt                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Hämta tjänstassociaty                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Skapa eller uppdatera en tjänstassociaty                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Ta bort tjänstassociat                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Verifiera tjänstassociaty                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Hämta IP-konfiguration för nätverksgränssnitt                    |

## <a name="next-steps"></a>Nästa steg

- Skapa en virtuell dator med flera nätverkskort med hjälp av [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Skapa en enskild virtuell NIC-dator med flera IPv4-adresser med [hjälp av Azure CLI](virtual-network-multiple-ip-addresses-cli.md) eller [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Skapa en virtuell dator med ett nätverkskort med en privat IPv6-adress (bakom en Azure Load Balancer) med hjälp av [Azure CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [eller Azure Resource Manager mallen](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Skapa ett nätverksgränssnitt med [Hjälp av PowerShell-](powershell-samples.md) eller Azure CLI-exempelskript, eller använd Azure [](cli-samples.md) [Resource Manager mall](template-samples.md)
- Skapa och tilldela [Azure Policy definitioner](./policy-reference.md) för virtuella nätverk