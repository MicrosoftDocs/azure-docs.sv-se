---
title: Skapa, ändra eller ta bort ett Azure-nätverks gränssnitt
titlesuffix: Azure Virtual Network
description: Lär dig mer om ett nätverks gränssnitt och hur du skapar, ändrar inställningar för och tar bort ett.
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
ms.openlocfilehash: 6cf082aa33d2063982d85cf0c2fdd68d61072217
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216950"
---
# <a name="create-change-or-delete-a-network-interface"></a>Skapa, ändra eller ta bort ett nätverksgränssnitt

Lär dig hur du skapar, ändrar inställningar för och tar bort ett nätverks gränssnitt. Ett nätverks gränssnitt gör det möjligt för en virtuell Azure-dator att kommunicera med Internet, Azure och lokala resurser. När du skapar en virtuell dator med hjälp av Azure Portal skapar portalen ett nätverks gränssnitt med standardinställningar. Du kan i stället välja att skapa nätverks gränssnitt med anpassade inställningar och lägga till ett eller flera nätverks gränssnitt på en virtuell dator när du skapar det. Du kanske också vill ändra standardinställningarna för nätverks gränssnittet för ett befintligt nätverks gränssnitt. I den här artikeln förklaras hur du skapar ett nätverks gränssnitt med anpassade inställningar, ändrar befintliga inställningar, till exempel nätverks filter (nätverks säkerhets grupp) tilldelning, under näts tilldelning, DNS-serverinställningar och IP-vidarebefordran, och ta bort ett nätverks gränssnitt.

Om du behöver lägga till, ändra eller ta bort IP-adresser för ett nätverks gränssnitt, se [Hantera IP-adresser](virtual-network-network-interface-addresses.md). Om du behöver lägga till nätverks gränssnitt i eller ta bort nätverks gränssnitt från virtuella datorer kan du läsa mer i [lägga till eller ta bort nätverks gränssnitt](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande åtgärder innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto kan du registrera dig för ett [kostnads fritt utvärderings konto](https://azure.microsoft.com/free).
- Om du använder portalen öppnar du https://portal.azure.com och loggar in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. I den här självstudien krävs Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder kommando rads kommandon i Azure för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. I den här självstudien krävs Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

Det konto som du loggar in på eller ansluta till Azure med måste tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [behörigheter](#permissions).

## <a name="create-a-network-interface"></a>Skapa ett nätverks gränssnitt

När du skapar en virtuell dator med hjälp av Azure Portal skapar portalen ett nätverks gränssnitt med standardinställningar. Om du hellre vill ange alla inställningar för nätverks gränssnittet kan du skapa ett nätverks gränssnitt med anpassade inställningar och koppla nätverks gränssnittet till en virtuell dator när du skapar den virtuella datorn (med PowerShell eller Azure CLI). Du kan också skapa ett nätverks gränssnitt och lägga till det i en befintlig virtuell dator (med PowerShell eller Azure CLI). Information om hur du skapar en virtuell dator med ett befintligt nätverks gränssnitt eller lägger till eller tar bort nätverks gränssnitt från befintliga virtuella datorer finns i [lägga till eller ta bort nätverks gränssnitt](virtual-network-network-interface-vm.md). Innan du skapar ett nätverks gränssnitt måste du ha ett befintligt [virtuellt nätverk](manage-virtual-network.md) på samma plats och i den prenumeration som du skapar ett nätverks gränssnitt i.

1. Skriv *nätverks gränssnitt* i rutan som innehåller text *Sök resurserna* överst i Azure Portal. När **nätverks gränssnitt** visas i Sök resultaten väljer du det.
2. Välj **+ Lägg till** under **nätverks gränssnitt**.
3. Ange eller välj värden för följande inställningar och välj sedan **skapa**:

    |Inställning|Obligatoriskt?|Information|
    |---|---|---|
    |Name|Yes|Namnet måste vara unikt inom den resurs grupp du väljer. Med tiden har du troligen flera nätverks gränssnitt i din Azure-prenumeration. Förslag när du skapar en namngivnings konvention för att göra det enklare att hantera flera nätverks gränssnitt finns i [namngivnings konventioner](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). Namnet kan inte ändras efter att nätverks gränssnittet har skapats.|
    |Virtuellt nätverk|Yes|Välj det virtuella nätverket för nätverks gränssnittet. Du kan bara tilldela ett nätverks gränssnitt till ett virtuellt nätverk som finns i samma prenumeration och plats som nätverks gränssnittet. När ett nätverks gränssnitt har skapats kan du inte ändra det virtuella nätverk som det har tilldelats. Den virtuella dator som du lägger till nätverks gränssnittet till måste också finnas på samma plats och i samma prenumeration som nätverks gränssnittet.|
    |Undernät|Yes|Välj ett undernät i det virtuella nätverk som du har valt. Du kan ändra det undernät som nätverks gränssnittet tilldelas efter att det har skapats.|
    |Tilldelning av privat IP-adress|Yes| I den här inställningen väljer du tilldelnings metod för IPv4-adressen. Välj mellan följande tilldelnings metoder: **dynamiska:** när du väljer det här alternativet tilldelar Azure automatiskt nästa tillgängliga adress från det valda under nätets adress utrymme. **Statisk:** När du väljer det här alternativet måste du manuellt tilldela en tillgänglig IP-adress från det valda under nätets adress utrymme. Statiska och dynamiska adresser ändras inte förrän du ändrar dem eller nätverks gränssnittet har tagits bort. Du kan ändra tilldelnings metoden när nätverks gränssnittet har skapats. Azure DHCP-servern tilldelar den här adressen till nätverks gränssnittet i den virtuella datorns operativ system.|
    |Nätverkssäkerhetsgrupp|No| Lämna inställningen **ingen**, Välj en befintlig [nätverks säkerhets grupp](./network-security-groups-overview.md)eller [skapa en nätverks säkerhets grupp](tutorial-filter-network-traffic.md). Med nätverks säkerhets grupper kan du filtrera nätverks trafik i och ut ur ett nätverks gränssnitt. Du kan använda noll eller en nätverks säkerhets grupp för ett nätverks gränssnitt. Noll eller en nätverks säkerhets grupp kan också tillämpas på det undernät som nätverks gränssnittet är tilldelat till. När en nätverks säkerhets grupp tillämpas på ett nätverks gränssnitt och under nätet som nätverks gränssnittet tilldelas, uppstår ibland oväntade resultat. Information om hur du felsöker nätverks säkerhets grupper som tillämpas på nätverks gränssnitt och undernät finns i [Felsöka nätverks säkerhets grupper](diagnose-network-traffic-filter-problem.md).|
    |Prenumeration|Yes|Välj en av dina Azure- [prenumerationer](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Den virtuella datorn som du ansluter ett nätverks gränssnitt till och det virtuella nätverk som du ansluter till måste finnas i samma prenumeration.|
    |Privat IP-adress (IPv6)|No| Om du markerar den här kryss rutan tilldelas en IPv6-adress till nätverks gränssnittet, förutom IPv4-adressen som tilldelats till nätverks gränssnittet. I avsnittet IPv6 i den här artikeln finns viktig information om användningen av IPv6 med nätverks gränssnitt. Det går inte att välja en tilldelnings metod för IPv6-adressen. Om du väljer att tilldela en IPv6-adress tilldelas den den dynamiska metoden.
    |IPv6-namn (visas endast när kryss rutan **privat IP-adress (IPv6)** är markerad) |Ja, om kryss rutan **privat IP-adress (IPv6)** är markerad.| Det här namnet tilldelas en sekundär IP-konfiguration för nätverks gränssnittet. Mer information om IP-konfigurationer finns i [Visa inställningar för nätverks gränssnitt](#view-network-interface-settings).|
    |Resursgrupp|Yes|Välj en befintlig [resurs grupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) eller skapa en. Ett nätverks gränssnitt kan finnas i samma eller en annan resurs grupp än den virtuella dator som du kopplar det till eller det virtuella nätverk som du ansluter till.|
    |Plats|Ja|Den virtuella datorn som du ansluter ett nätverks gränssnitt till och det virtuella nätverk som du ansluter till måste finnas på samma [plats](https://azure.microsoft.com/regions), även kallat en region.|

Portalen ger inte alternativet att tilldela nätverks gränssnittet en offentlig IP-adress när du skapar den, men portalen skapar en offentlig IP-adress och tilldelar den till ett nätverks gränssnitt när du skapar en virtuell dator med hjälp av portalen. Information om hur du lägger till en offentlig IP-adress i nätverks gränssnittet när du har skapat den finns i [Hantera IP-adresser](virtual-network-network-interface-addresses.md). Om du vill skapa ett nätverks gränssnitt med en offentlig IP-adress måste du använda CLI eller PowerShell för att skapa nätverks gränssnittet.

Portalen ger inte möjlighet att tilldela nätverks gränssnittet till program säkerhets grupper när ett nätverks gränssnitt skapas, men Azure CLI och PowerShell gör. Du kan dock tilldela ett befintligt nätverks gränssnitt till en program säkerhets grupp med hjälp av portalen, så länge nätverks gränssnittet är kopplat till en virtuell dator. Information om hur du tilldelar ett nätverks gränssnitt till en program säkerhets grupp finns i [Lägg till i eller ta bort från program säkerhets grupper](#add-to-or-remove-from-application-security-groups).

>[!Note]
> Azure tilldelar bara en MAC-adress till nätverks gränssnittet när nätverks gränssnittet är kopplat till en virtuell dator och den virtuella datorn startas första gången. Du kan inte ange den MAC-adress som Azure tilldelar nätverks gränssnittet. MAC-adressen är tilldelad till nätverks gränssnittet tills nätverks gränssnittet har tagits bort eller så har den privata IP-adress som tilldelats den primära IP-konfigurationen för det primära nätverks gränssnittet ändrats. Mer information om IP-adresser och IP-konfigurationer finns i [Hantera IP-adresser](virtual-network-network-interface-addresses.md)

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Visa inställningar för nätverks gränssnitt

Du kan visa och ändra de flesta inställningar för ett nätverks gränssnitt när det har skapats. Portalen visar inte medlemskapet DNS-suffix eller program säkerhets grupp för nätverks gränssnittet. Du kan använda PowerShell-eller Azure CLI- [kommandon](#view-settings-commands) för att Visa medlemskapet DNS-suffix och program säkerhets grupp.

1. Skriv *nätverks gränssnitt* i rutan som innehåller text *Sök resurserna* överst i Azure Portal. När **nätverks gränssnitt** visas i Sök resultaten väljer du det.
2. Välj det nätverks gränssnitt som du vill visa eller ändra inställningarna för i listan.
3. Följande objekt visas för det nätverks gränssnitt som du har valt:
   - **Översikt:** Innehåller information om nätverks gränssnittet, till exempel IP-adresser som tilldelats det, det virtuella nätverk/undernät som nätverks gränssnittet är tilldelat och den virtuella dator som nätverks gränssnittet är kopplat till (om det är kopplat till ett). Följande bild visar översikts inställningarna för ett nätverks gränssnitt med namnet **mywebserver256**: ![ Översikt över nätverks gränssnitt](./media/virtual-network-network-interface/nic-overview.png)

     Du kan flytta ett nätverks gränssnitt till en annan resurs grupp eller prenumeration genom att välja (**ändra**) bredvid **resurs gruppen** eller **prenumerations namnet**. Om du flyttar nätverks gränssnittet till en ny prenumeration måste du flytta alla resurser som är relaterade till nätverks gränssnittet med det. Om nätverks gränssnittet är kopplat till en virtuell dator, till exempel, måste du också flytta den virtuella datorn och andra virtuella dator-relaterade resurser. Information om hur du flyttar ett nätverks gränssnitt finns i [Flytta resurs till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal). Artikeln innehåller förutsättningar och hur du flyttar resurser med hjälp av Azure Portal, PowerShell och Azure CLI.
   - **IP-konfigurationer:** Offentliga och privata IPv4-och IPv6-adresser som tilldelas till IP-konfigurationer visas här. Om en IPv6-adress har tilldelats en IP-konfiguration visas inte adressen. Mer information om IP-konfigurationer och hur du lägger till och tar bort IP-adresser finns i [Konfigurera IP-adresser för ett Azure-nätverks gränssnitt](virtual-network-network-interface-addresses.md). IP-vidarebefordring och tilldelning av undernät konfigureras också i det här avsnittet. Mer information om de här inställningarna finns i [Aktivera eller inaktivera IP-vidarebefordran](#enable-or-disable-ip-forwarding) och [Ändra tilldelning av undernät](#change-subnet-assignment).
   - **DNS-servrar:** Du kan ange vilken DNS-server som ett nätverks gränssnitt tilldelas av Azure DHCP-servrarna. Nätverks gränssnittet kan ärva inställningen från det virtuella nätverk som nätverks gränssnittet är tilldelat eller har en anpassad inställning som åsidosätter inställningen för det virtuella nätverk som den är tilldelad till. Information om hur du ändrar vad som visas finns i [ändra DNS-servrar](#change-dns-servers).
   - **Nätverks säkerhets grupp (NSG):** Visar vilka NSG som är kopplade till nätverks gränssnittet (om det finns några). En NSG innehåller regler för inkommande och utgående trafik för filtrering av nätverks trafik för nätverks gränssnittet. Om en NSG är kopplad till nätverks gränssnittet visas namnet på den associerade NSG. Om du vill ändra vad som visas, se [associera eller koppla bort en nätverks säkerhets grupp](#associate-or-dissociate-a-network-security-group).
   - **Egenskaper:** Visar nyckel inställningar för nätverks gränssnittet, inklusive MAC-adressen (tomt om nätverks gränssnittet inte är kopplat till en virtuell dator) och den prenumeration som det finns i.
   - **Effektiva säkerhets regler:**  Säkerhets regler anges om nätverks gränssnittet är kopplat till en virtuell dator som körs och en NSG är kopplad till nätverks gränssnittet, under nätet som den är tilldelad till eller båda. Mer information om vad som visas finns i [Visa effektiva säkerhets regler](#view-effective-security-rules). Mer information om NSG: er finns i [nätverks säkerhets grupper](./network-security-groups-overview.md).
   - **Effektiva vägar:** Vägar anges om nätverks gränssnittet är kopplat till en virtuell dator som körs. Vägarna är en kombination av Azures standard vägar, alla användardefinierade vägar och eventuella BGP-vägar som kan finnas för det undernät som nätverks gränssnittet är tilldelat. Mer information om vad som visas finns i [Visa effektiva vägar](#view-effective-routes). Mer information om Azures standard vägar och användardefinierade vägar finns i [Översikt över routning](virtual-networks-udr-overview.md).
Vanliga Azure Resource Manager inställningar: Mer information om vanliga Azure Resource Manager inställningar finns i [aktivitets logg](../azure-monitor/platform/platform-logs-overview.md), [åtkomst kontroll (IAM)](../role-based-access-control/overview.md), [taggar](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Lås](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)och automation- [skript](../azure-resource-manager/templates/export-template-portal.md).

<a name="view-settings-commands"></a>**Kommandon**

Om en IPv6-adress är tilldelad till ett nätverks gränssnitt returnerar PowerShell-utdata det faktum att adressen är tilldelad, men returnerar inte den tilldelade adressen. På samma sätt returnerar CLI det faktum att adressen är tilldelad, men returnerar *Null* i dess utdata för adressen.

|Verktyg|Kommando|
|---|---|
|CLI|[AZ Network NIC List](/cli/azure/network/nic) för att Visa nätverks gränssnitt i prenumerationen. [AZ Network NIC show](/cli/azure/network/nic) för att Visa inställningar för ett nätverks gränssnitt|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) för att Visa nätverks gränssnitt i prenumerations-eller visnings inställningarna för ett nätverks gränssnitt|

## <a name="change-dns-servers"></a>Ändra DNS-servrar

DNS-servern tilldelas av Azure DHCP-servern till nätverks gränssnittet i den virtuella datorns operativ system. Den DNS-server som är tilldelad är den DNS-Server inställningen för ett nätverks gränssnitt. Mer information om namn matchnings inställningar för ett nätverks gränssnitt finns i [namn matchning för virtuella datorer](virtual-networks-name-resolution-for-vms-and-role-instances.md). Nätverks gränssnittet kan ärva inställningarna från det virtuella nätverket eller använda egna unika inställningar som åsidosätter inställningen för det virtuella nätverket.

1. Skriv *nätverks gränssnitt* i rutan som innehåller text *Sök resurserna* överst i Azure Portal. När **nätverks gränssnitt** visas i Sök resultaten väljer du det.
2. Välj det nätverks gränssnitt som du vill ändra en DNS-server för från listan.
3. Välj **DNS-servrar** under **Inställningar**.
4. Välj antingen:
   - **Ärv från virtuellt nätverk**: Välj det här alternativet om du vill ärva DNS-serverinställningar som definierats för det virtuella nätverk som nätverks gränssnittet är tilldelat. På nivån för det virtuella nätverket definieras en anpassad DNS-server eller en DNS-server som har angetts av Azure. Den Azure-tillhandahållna DNS-servern kan matcha värdnamn för resurser som har tilldelats samma virtuella nätverk. FQDN måste användas för att matcha resurser som har tilldelats till olika virtuella nätverk.
   - **Anpassad**: du kan konfigurera en egen DNS-server för att matcha namn i flera virtuella nätverk. Ange IP-adressen för den server som du vill använda som en DNS-server. Den DNS-serveradress som du anger tilldelas bara det här nätverks gränssnittet och åsidosätter alla DNS-inställningar för det virtuella nätverk som nätverks gränssnittet är tilldelat till.
     >[!Note]
     >Om den virtuella datorn använder ett nätverkskort som ingår i en tillgänglighets uppsättning ärvs alla DNS-servrar som anges för var och en av de virtuella datorerna från alla nätverkskort som ingår i tillgänglighets uppsättningen.
5. Välj **Spara**.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ Network NIC Update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Aktivera eller inaktivera IP-vidarebefordring

IP-vidarebefordring gör det möjligt för den virtuella dator som ett nätverks gränssnitt är kopplat till:
- Ta emot nätverks trafik som inte är avsedd för en av IP-adresserna som tilldelats någon av de IP-konfigurationer som tilldelats till nätverks gränssnittet.
- Skicka nätverks trafik med en annan käll-IP-adress än den som tilldelats ett av nätverks gränssnittets IP-konfigurationer.

Inställningen måste vara aktive rad för alla nätverks gränssnitt som är kopplade till den virtuella datorn och som tar emot trafik som den virtuella datorn måste vidarebefordra. En virtuell dator kan vidarebefordra trafik om den har flera nätverks gränssnitt eller ett enda nätverks gränssnitt som är kopplat till det. Även om IP-vidarebefordring är en Azure-inställning måste den virtuella datorn också köra ett program som kan vidarebefordra trafiken, t. ex. brand vägg, WAN-optimering och belastnings Utjämnings program. När en virtuell dator kör nätverks program kallas den virtuella datorn ofta för en virtuell nätverks installation. Du kan visa en lista över redo att distribuera virtuella nätverks installationer i [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). IP-vidarebefordring används vanligt vis med användardefinierade vägar. Mer information om användardefinierade vägar finns i [användardefinierade vägar](virtual-networks-udr-overview.md).

1. Skriv *nätverks gränssnitt* i rutan som innehåller text *Sök resurserna* överst i Azure Portal. När **nätverks gränssnitt** visas i Sök resultaten väljer du det.
2. Välj det nätverks gränssnitt som du vill aktivera eller inaktivera IP-vidarebefordring för.
3. Välj **IP-konfigurationer** i avsnittet **Inställningar** .
4. Välj **aktive rad** eller **inaktive** rad (standardinställning) om du vill ändra inställningen.
5. Välj **Spara**.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ Network NIC Update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Ändra tilldelning av undernät

Du kan ändra under nätet, men inte det virtuella nätverket, som ett nätverks gränssnitt är tilldelat till.

1. Skriv *nätverks gränssnitt* i rutan som innehåller text *Sök resurserna* överst i Azure Portal. När **nätverks gränssnitt** visas i Sök resultaten väljer du det.
2. Välj det nätverks gränssnitt som du vill ändra under näts tilldelningen för.
3. Välj **IP-konfigurationer** under **Inställningar**. Om några privata IP-adresser för IP-konfigurationer i listan har **(statisk)** bredvid dem måste du ändra metod för tilldelning av IP-adress till dynamisk genom att följa stegen som följer. Alla privata IP-adresser måste tilldelas med den dynamiska tilldelnings metoden för att ändra under näts tilldelningen för nätverks gränssnittet. Om adresserna tilldelas med den dynamiska metoden fortsätter du till steg fem. Om du har tilldelats en IPv4-adress med metoden för statisk tilldelning utför du följande steg för att ändra tilldelnings metoden till dynamisk:
   - Välj den IP-konfiguration som du vill ändra tilldelnings metod för IPv4-adress för i listan med IP-konfigurationer.
   - Välj **dynamisk** för **tilldelnings** metoden för privata IP-adresser. Du kan inte tilldela en IPv6-adress med den statiska tilldelnings metoden.
   - Välj **Spara**.
4. Välj det undernät som du vill flytta nätverks gränssnittet till från den nedrullningsbara listan **undernät** .
5. Välj **Spara**. Nya dynamiska adresser tilldelas från under nätets adress intervall för det nya under nätet. När du har tilldelat nätverks gränssnittet till ett nytt undernät kan du tilldela en statisk IPv4-adress från det nya under nätets adress intervall om du väljer. Mer information om hur du lägger till, ändrar och tar bort IP-adresser för ett nätverks gränssnitt finns i [Hantera IP-adresser](virtual-network-network-interface-addresses.md).

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ Network NIC IP-config Update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Lägg till i eller ta bort från program säkerhets grupper

Du kan bara lägga till ett nätverks gränssnitt i eller ta bort ett nätverks gränssnitt från en program säkerhets grupp med hjälp av portalen om nätverks gränssnittet är kopplat till en virtuell dator. Du kan använda PowerShell eller Azure CLI för att lägga till ett nätverks gränssnitt i eller ta bort ett nätverks gränssnitt från en program säkerhets grupp, oavsett om nätverks gränssnittet är kopplat till en virtuell dator eller inte. Lär dig mer om [program säkerhets grupper](./network-security-groups-overview.md#application-security-groups) och hur du [skapar en program säkerhets grupp](manage-network-security-group.md).

1. I rutan *Sök efter resurser, tjänster och dokument* högst upp i portalen börjar du skriva namnet på en virtuell dator som har ett nätverks gränssnitt som du vill lägga till i eller ta bort från, en program säkerhets grupp. När namnet på den virtuella datorn visas i Sök resultatet väljer du det.
2. Under **INSTÄLLNINGAR** väljer du **Nätverk**.  Välj **program säkerhets grupper** och **Konfigurera** sedan program säkerhets grupper som väljer de program säkerhets grupper som du vill lägga till nätverks gränssnittet i eller avmarkera de program säkerhets grupper som du vill ta bort nätverks gränssnittet från och välj sedan **Spara**. Endast nätverks gränssnitt som finns i samma virtuella nätverk kan läggas till i samma program säkerhets grupp. Program säkerhets gruppen måste finnas på samma plats som nätverks gränssnittet.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ Network NIC Update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Koppla eller koppla bort en nätverks säkerhets grupp

1. I rutan Sök högst upp i portalen anger du *nätverks gränssnitt* i sökrutan. När **nätverks gränssnitt** visas i Sök resultaten väljer du det.
2. Välj det nätverks gränssnitt i listan som du vill associera en nätverks säkerhets grupp med eller koppla från en nätverks säkerhets grupp.
3. Välj **nätverks säkerhets grupp** under **Inställningar**.
4. Välj **Redigera**.
5. Välj **nätverks säkerhets grupp** och välj sedan den nätverks säkerhets grupp som du vill koppla till nätverks gränssnittet eller Välj **ingen** om du vill koppla bort en nätverks säkerhets grupp.
6. Välj **Spara**.

**Kommandon**

- Azure CLI: [AZ Network NIC Update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Ta bort ett nätverksgränssnitt

Du kan ta bort ett nätverks gränssnitt så länge det inte är kopplat till en virtuell dator. Om ett nätverks gränssnitt är kopplat till en virtuell dator måste du först placera den virtuella datorn i tillståndet Stoppad (Frigjord) och sedan koppla bort nätverks gränssnittet från den virtuella datorn. Om du vill koppla bort ett nätverks gränssnitt från en virtuell dator slutför du stegen i [Koppla bort ett nätverks gränssnitt från en virtuell dator](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Du kan dock inte ta bort ett nätverks gränssnitt från en virtuell dator om det är det enda nätverks gränssnitt som är kopplat till den virtuella datorn. En virtuell dator måste alltid ha minst ett nätverks gränssnitt kopplat till sig. Om du tar bort en virtuell dator kopplas alla nätverks gränssnitt som är anslutna till den bort, men nätverks gränssnitten tas inte bort.

1. Skriv *nätverks gränssnitt* i rutan som innehåller text *Sök resurserna* överst i Azure Portal. När **nätverks gränssnitt** visas i Sök resultaten väljer du det.
2. Välj det nätverks gränssnitt i listan som du vill ta bort.
3. Under **Översikt** väljer du **ta bort**.
4. Välj **Ja** för att bekräfta borttagning av nätverks gränssnittet.

När du tar bort ett nätverks gränssnitt frigörs alla MAC-eller IP-adresser som tilldelats till det.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ Network NIC Delete](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Lösa anslutnings problem

Om du inte kan kommunicera med eller från en virtuell dator, kan säkerhets regler för nätverks säkerhets grupper eller vägar som är effektiva för ett nätverks gränssnitt orsaka problemet. Du kan lösa problemet med följande alternativ:

### <a name="view-effective-security-rules"></a>Visa gällande säkerhets regler

De effektiva säkerhets reglerna för varje nätverks gränssnitt som är kopplat till en virtuell dator är en kombination av de regler som du har skapat i en nätverks säkerhets grupp och [Standard säkerhets regler](./network-security-groups-overview.md#default-security-rules). Att förstå de effektiva säkerhets reglerna för ett nätverks gränssnitt kan hjälpa dig att avgöra varför du inte kan kommunicera med eller från en virtuell dator. Du kan visa de gällande reglerna för alla nätverks gränssnitt som är kopplade till en virtuell dator som körs.

1. I rutan Sök högst upp i portalen anger du namnet på den virtuella dator som du vill visa gällande säkerhets regler för. Om du inte känner till namnet på en virtuell dator anger du *virtuella datorer* i sökrutan. När **virtuella datorer** visas i Sök resultaten väljer du den och väljer sedan en virtuell dator i listan.
2. Välj **nätverk** under **Inställningar**.
3. Välj ett nätverks gränssnitts namn.
4. Välj **effektiva säkerhets regler** under **support + fel sökning**.
5. Granska listan över effektiva säkerhets regler för att avgöra om rätt regler finns för din nödvändiga inkommande och utgående kommunikation. Läs mer om det du ser i listan i [Översikt över nätverks säkerhets gruppen](./network-security-groups-overview.md).

Funktionen för att verifiera IP-flöde i Azure Network Watcher kan också hjälpa dig att avgöra om säkerhets reglerna förhindrar kommunikation mellan en virtuell dator och en slut punkt. Läs mer i [kontrol lera IP-flöde](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Kommandon**

- Azure CLI: [AZ Network NIC List-effektiv-NSG](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Visa effektiva vägar

De effektiva vägarna för nätverks gränssnitt som är anslutna till en virtuell dator är en kombination av standard vägar, alla vägar som du har skapat och alla vägar som sprids från lokala nätverk via BGP via en virtuell Azure-nätverksgateway. Att förstå de effektiva vägarna för ett nätverks gränssnitt kan hjälpa dig att avgöra varför du inte kan kommunicera med eller från en virtuell dator. Du kan visa effektiva vägar för alla nätverks gränssnitt som är kopplade till en virtuell dator som körs.

1. I rutan Sök högst upp i portalen anger du namnet på den virtuella dator som du vill visa gällande säkerhets regler för. Om du inte känner till namnet på en virtuell dator anger du *virtuella datorer* i sökrutan. När **virtuella datorer** visas i Sök resultaten väljer du den och väljer sedan en virtuell dator i listan.
2. Välj **nätverk** under **Inställningar**.
3. Välj ett nätverks gränssnitts namn.
4. Välj **effektiva vägar** under **support + fel sökning**.
5. Granska listan över effektiva vägar för att avgöra om rätt vägar finns för din nödvändiga inkommande och utgående kommunikation. Läs mer om det du ser i listan i [Översikt över routning](virtual-networks-udr-overview.md).

Nästa hopp-funktion i Azure Network Watcher kan också hjälpa dig att avgöra om vägar förhindrar kommunikation mellan en virtuell dator och en slut punkt. Läs mer i [nästa hopp](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Kommandon**

- Azure CLI: [AZ Network NIC show-effektiv-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Behörigheter

För att utföra åtgärder på nätverks gränssnitt måste ditt konto tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som har tilldelats rätt behörigheter i följande tabell:

| Action                                                                     | Name                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft. Network/networkInterfaces/Read                                   | Hämta nätverks gränssnitt                                     |
| Microsoft. Network/networkInterfaces/Write                                  | Skapa eller uppdatera nätverks gränssnitt                        |
| Microsoft. Network/networkInterfaces/JOIN/åtgärd                            | Koppla ett nätverks gränssnitt till en virtuell dator           |
| Microsoft. Network/networkInterfaces/Delete                                 | Ta bort nätverks gränssnitt                                  |
| Microsoft. Network/networkInterfaces/joinViaPrivateIp/Action                | Anslut en resurs till ett nätverks gränssnitt via en servi...     |
| Microsoft. Network/networkInterfaces/effectiveRouteTable/Action             | Hämta nätverks gränssnitt effektiv väg tabell               |
| Microsoft. Network/networkInterfaces/effectiveNetworkSecurityGroups/Action  | Hämta nätverks gränssnitt gällande säkerhets grupper           |
| Microsoft. Network/networkInterfaces/belastningsutjämnare/Read                     | Hämta belastnings utjämning för nätverks gränssnitt                      |
| Microsoft. Network/networkInterfaces/serviceAssociations/Read               | Hämta tjänst koppling                                   |
| Microsoft. Network/networkInterfaces/serviceAssociations/Write              | Skapa eller uppdatera en tjänst koppling                    |
| Microsoft. Network/networkInterfaces/serviceAssociations/Delete             | Ta bort tjänst koppling                                |
| Microsoft. Network/networkInterfaces/serviceAssociations/validate/Action    | Verifiera tjänst koppling                              |
| Microsoft. Network/networkInterfaces/ipconfigurations/Read                  | Hämta IP-konfiguration för nätverks gränssnitt                    |

## <a name="next-steps"></a>Nästa steg

- Skapa en virtuell dator med flera nätverkskort med hjälp av [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Skapa en virtuell dator med en virtuell NIC med flera IPv4-adresser med hjälp av [Azure CLI](virtual-network-multiple-ip-addresses-cli.md) eller [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Skapa en virtuell nätverkskort virtuell dator med en privat IPv6-adress (bakom en Azure Load Balancer) med hjälp av [Azure CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-, [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-eller [Azure Resource Manager-mallen](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Skapa ett nätverks gränssnitt med [PowerShell](powershell-samples.md) -eller [Azure CLI](cli-samples.md) -exempel skript eller med Azure [Resource Manager-mall](template-samples.md)
- Skapa och tilldela [Azure policy definitioner](./policy-reference.md) för virtuella nätverk