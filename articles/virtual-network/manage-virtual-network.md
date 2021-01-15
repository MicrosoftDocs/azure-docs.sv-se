---
title: Skapa, ändra eller ta bort ett virtuellt Azure-nätverk
titlesuffix: Azure Virtual Network
description: Skapa och ta bort ett virtuellt nätverk och ändra inställningar, t. ex. DNS-servrar och IP-adressutrymme, för ett befintligt virtuellt nätverk.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 61ca4dc8cd7048df69c827e7ca657b9882900819
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218917"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Skapa, ändra eller ta bort ett virtuellt nätverk

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Lär dig hur du skapar och tar bort ett virtuellt nätverk och ändrar inställningar, t. ex. DNS-servrar och IP-adressutrymme, för ett befintligt virtuellt nätverk. Om du inte har använt virtuella nätverk tidigare kan du lära dig mer om dem i det [virtuella nätverkets översikt](virtual-networks-overview.md) eller genom att slutföra en [själv studie kurs](quick-create-portal.md). Ett virtuellt nätverk innehåller undernät. Information om hur du skapar, ändrar och tar bort undernät finns i [Hantera undernät](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Innan du börjar

Utför följande åtgärder innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto kan du registrera dig för ett [kostnads fritt utvärderings konto](https://azure.microsoft.com/free).
- Om du använder portalen öppnar du https://portal.azure.com och loggar in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. I den här självstudien krävs Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder kommando rads kommandon i Azure för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. I den här självstudien krävs Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.
- Det konto som du loggar in på eller ansluta till Azure med måste tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [behörigheter](#permissions).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Välj **+ skapa en resurs**  >  **nätverk**  >  **virtuellt nätverk**.
2. Ange eller välj värden för följande inställningar och välj sedan **skapa**:
   - **Namn**: namnet måste vara unikt i den [resurs grupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) som du väljer för att skapa det virtuella nätverket i. Du kan inte ändra namnet när det virtuella nätverket har skapats. Du kan skapa flera virtuella nätverk över tid. Information om namngivning av förslag finns i [namngivnings konventioner](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources). Genom att följa en namngivnings konvention kan du göra det enklare att hantera flera virtuella nätverk.
   - **Adress utrymme**: adress utrymmet för ett virtuellt nätverk består av ett eller flera icke överlappande adress intervall som anges i CIDR-notering. Adress intervallet som du definierar kan vara offentliga eller privata (RFC 1918). Oavsett om du definierar adress intervallet som offentligt eller privat, kan adress intervallet bara nås från det virtuella nätverket, från sammankopplade virtuella nätverk och från alla lokala nätverk som du har anslutit till det virtuella nätverket. Du kan inte lägga till följande adress intervall:
     - 224.0.0.0/4 (multicast)
     - 255.255.255.255/32 (sändning)
     - 127.0.0.0/8 (loopback)
     - 169.254.0.0/16 (länk-lokal)
     - 168.63.129.16/32 (intern DNS, DHCP och Azure Load Balancer [hälso avsökning](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

     Även om du bara kan definiera ett adress intervall när du skapar det virtuella nätverket i portalen, kan du lägga till fler adress intervall i adress utrymmet när det virtuella nätverket har skapats. Information om hur du lägger till ett adress intervall i ett befintligt virtuellt nätverk finns i [lägga till eller ta bort ett adress intervall](#add-or-remove-an-address-range).

     > [!WARNING]
     > Om ett virtuellt nätverk har adress intervall som överlappar ett annat virtuellt nätverk eller lokalt nätverk, kan inte de två nätverken anslutas. Innan du definierar ett adress intervall bör du fundera över om du kanske vill ansluta det virtuella nätverket till andra virtuella nätverk eller lokala nätverk i framtiden. Microsoft rekommenderar att du konfigurerar adress intervall för virtuella nätverk med privat adress utrymme eller offentligt adress utrymme som ägs av din organisation.
     >

     - **Under näts namn**: under nätets namn måste vara unikt inom det virtuella nätverket. Du kan inte ändra under nätets namn när under nätet har skapats. Portalen kräver att du definierar ett undernät när du skapar ett virtuellt nätverk, även om ett virtuellt nätverk inte krävs för att ha några undernät. I portalen kan du bara definiera ett undernät när du skapar ett virtuellt nätverk. Du kan lägga till fler undernät till det virtuella nätverket senare, efter att det virtuella nätverket har skapats. Information om hur du lägger till ett undernät i ett virtuellt nätverk finns i [Hantera undernät](virtual-network-manage-subnet.md). Du kan skapa ett virtuellt nätverk som har flera undernät med hjälp av Azure CLI eller PowerShell.

       >[!TIP]
       >Administratörer skapar ibland olika undernät för att filtrera eller styra trafik flödet mellan under näten. Innan du definierar undernät bör du fundera över hur du kan filtrera och dirigera trafik mellan dina undernät. Mer information om hur du filtrerar trafik mellan undernät finns i [nätverks säkerhets grupper](./network-security-groups-overview.md). Azure dirigerar automatiskt trafik mellan undernät, men du kan åsidosätta Azures standard vägar. Om du vill veta mer om Azures standard trafik routning för [routning, se Routning: översikt](virtual-networks-udr-overview.md).
       >

     - **Adress intervall för under nätet**: intervallet måste ligga inom det adress utrymme du angav för det virtuella nätverket. Det minsta intervallet du kan ange är/29, vilket ger åtta IP-adresser för under nätet. Azure reserverar den första och sista adressen i varje undernät för protokoll avvikelse. Tre ytterligare adresser är reserverade för användning av Azure-tjänster. Därför har ett virtuellt nätverk med ett under näts adress intervall på/29 bara tre användbara IP-adresser. Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway måste du skapa ett Gateway-undernät. Läs mer om [vissa adress intervall för gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Du kan ändra adress intervallet när under nätet har skapats, under vissa förhållanden. Information om hur du ändrar ett adress intervall för ett undernät finns i [Hantera undernät](virtual-network-manage-subnet.md).
     - **Prenumeration**: Välj en [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Du kan inte använda samma virtuella nätverk i fler än en Azure-prenumeration. Du kan dock ansluta ett virtuellt nätverk i en prenumeration till virtuella nätverk i andra prenumerationer med [peering av virtuella nätverk](virtual-network-peering-overview.md). Alla Azure-resurser som du ansluter till det virtuella nätverket måste finnas i samma prenumeration som det virtuella nätverket.
     - **Resurs grupp**: Välj en befintlig [resurs grupp](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) eller skapa en ny. En Azure-resurs som du ansluter till det virtuella nätverket kan finnas i samma resurs grupp som det virtuella nätverket eller i en annan resurs grupp.
     - **Plats**: Välj en Azure- [plats](https://azure.microsoft.com/regions/), även kallat en region. Ett virtuellt nätverk kan bara finnas på en Azure-plats. Du kan dock ansluta ett virtuellt nätverk på en plats till ett virtuellt nätverk på en annan plats med hjälp av en VPN-gateway. Alla Azure-resurser som du ansluter till det virtuella nätverket måste finnas på samma plats som det virtuella nätverket.

**Kommandon**

- Azure CLI: [AZ Network VNet Create](/cli/azure/network/vnet)
- PowerShell: [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Visa virtuella nätverk och inställningar

1. Ange *virtuella nätverk* i sökrutan i rutan Sök högst upp i portalen. När **virtuella nätverk** visas i Sök resultaten väljer du det.
2. I listan med virtuella nätverk väljer du det virtuella nätverk som du vill visa inställningarna för.
3. Följande inställningar visas för det virtuella nätverk som du har valt:
   - **Översikt**: innehåller information om det virtuella nätverket, inklusive adress utrymme och DNS-servrar. Följande skärm bild visar översikts inställningarna för ett virtuellt nätverk med namnet **MyVNet**:

     ![Översikt över nätverks gränssnitt](./media/manage-virtual-network/vnet-overview.png)

     Du kan flytta ett virtuellt nätverk till en annan prenumeration eller resurs grupp genom att välja **ändra** bredvid **resurs grupp** eller **prenumerations namn**. Information om hur du flyttar ett virtuellt nätverk finns i [Flytta resurser till en annan resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Artikeln innehåller förutsättningar och hur du flyttar resurser med hjälp av Azure Portal, PowerShell och Azure CLI. Alla resurser som är anslutna till det virtuella nätverket måste flyttas med det virtuella nätverket.
   - **Adress utrymme**: adress utrymmen som är kopplade till det virtuella nätverket visas i listan. Om du vill lära dig hur du lägger till och tar bort ett adress intervall i adress utrymmet slutför du stegen i [Lägg till eller ta bort ett adress intervall](#add-or-remove-an-address-range).
   - **Anslutna enheter**: alla resurser som är anslutna till det virtuella nätverket visas i listan. I föregående skärm bild är tre nätverks gränssnitt och en belastningsutjämnare anslutna till det virtuella nätverket. Alla nya resurser som du skapar och ansluter till det virtuella nätverket visas i listan. Om du tar bort en resurs som är ansluten till det virtuella nätverket visas den inte längre i listan.
   - **Undernät**: en lista över undernät som finns i det virtuella nätverket visas. Information om hur du lägger till och tar bort ett undernät finns i [Hantera undernät](virtual-network-manage-subnet.md).
   - **DNS-servrar**: du kan ange om den interna Azure-DNS-servern eller en anpassad DNS-Server tillhandahåller namn matchning för enheter som är anslutna till det virtuella nätverket. När du skapar ett virtuellt nätverk med hjälp av Azure Portal används Azures DNS-servrar för namn matchning i ett virtuellt nätverk som standard. Om du vill ändra DNS-servrarna slutför du stegen i [ändra DNS-servrar](#change-dns-servers) i den här artikeln.
   - **Peering**: om det finns befintliga peer-kopplingar i prenumerationen visas de här. Du kan visa inställningar för befintlig peering eller skapa, ändra eller ta bort peer-kopplingar. Mer information om peering finns i [peering för virtuella nätverk](virtual-network-peering-overview.md).
   - **Egenskaper**: visar inställningar för det virtuella nätverket, inklusive det virtuella nätverkets resurs-ID och Azure-prenumerationen.
   - **Diagram**: diagrammet innehåller en visuell representation av alla enheter som är anslutna till det virtuella nätverket. Diagrammet innehåller viss viktig information om enheterna. Om du vill hantera en enhet i den här vyn väljer du enheten i diagrammet.
   - **Vanliga Azure-inställningar**: Mer information om vanliga Azure-inställningar finns i följande information:
     - [Aktivitetslogg](../azure-monitor/platform/platform-logs-overview.md)
     - [Åtkomstkontroll (IAM)](../role-based-access-control/overview.md)
     - [Taggar](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Lås](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Automationsskript](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Kommandon**

- Azure CLI: [AZ Network VNet show](/cli/azure/network/vnet)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Lägga till eller ta bort ett adress intervall

Du kan lägga till och ta bort adress intervall för ett virtuellt nätverk. Ett adress intervall måste anges i CIDR-format och får inte överlappa andra adress intervall inom samma virtuella nätverk. De adress intervall som du definierar kan vara offentliga eller privata (RFC 1918). Oavsett om du definierar adress intervallet som offentligt eller privat, kan adress intervallet bara nås från det virtuella nätverket, från sammankopplade virtuella nätverk och från alla lokala nätverk som du har anslutit till det virtuella nätverket. 

Du kan minska adress intervallet för ett virtuellt nätverk så länge det fortfarande innehåller intervallen för associerade undernät. Dessutom kan du utöka adress intervallet, till exempel ändra a/16 till/8. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Du kan inte lägga till följande adress intervall:

- 224.0.0.0/4 (multicast)
- 255.255.255.255/32 (sändning)
- 127.0.0.0/8 (loopback)
- 169.254.0.0/16 (länk-lokal)
- 168.63.129.16/32 (intern DNS, DHCP och Azure Load Balancer [hälso avsökning](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

Så här lägger du till eller tar bort ett adress intervall:

1. Ange *virtuella nätverk* i sökrutan i rutan Sök högst upp i portalen. När **virtuella nätverk** visas i Sök resultaten väljer du det.
2. I listan över virtuella nätverk väljer du det virtuella nätverk som du vill lägga till eller ta bort ett adress intervall för.
3. Välj **adress utrymme** under **Inställningar**.
4. Slutför något av följande alternativ:
    - **Lägg till ett adress intervall**: Ange det nya adress intervallet. Adress intervallet får inte överlappa ett befintligt adress intervall som har definierats för det virtuella nätverket.
    - **Ta bort ett adress intervall**: till höger om det adress intervall som du vill ta bort väljer du **...** och väljer sedan **ta bort**. Om det finns ett undernät i adress intervallet kan du inte ta bort adress intervallet. Om du vill ta bort ett adress intervall måste du först ta bort alla undernät (och eventuella resurser i under näten) som finns i adress intervallet.
5. Välj **Spara**.

**Kommandon**

- Azure CLI: [AZ Network VNet Update](/cli/azure/network/vnet)
- PowerShell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Ändra DNS-servrar

Alla virtuella datorer som är anslutna till det virtuella nätverks registret med de DNS-servrar som du anger för det virtuella nätverket. De använder också den angivna DNS-servern för namn matchning. Varje nätverks gränssnitt (NIC) i en virtuell dator kan ha egna DNS-serverinställningar. Om ett nätverkskort har sina egna DNS-serverinställningar åsidosätter de DNS-serverinställningarna för det virtuella nätverket. Mer information om NÄTVERKSKORTs DNS-inställningar finns i [nätverks gränssnitts aktiviteter och inställningar](virtual-network-network-interface.md#change-dns-servers). Mer information om namn matchning för virtuella datorer och roll instanser i Azure Cloud Services finns i [namn matchning för virtuella datorer och roll instanser](virtual-networks-name-resolution-for-vms-and-role-instances.md). Lägga till, ändra eller ta bort en DNS-Server:

1. Ange *virtuella nätverk* i sökrutan i rutan Sök högst upp i portalen. När **virtuella nätverk** visas i Sök resultaten väljer du det.
2. I listan med virtuella nätverk väljer du det virtuella nätverk som du vill ändra DNS-servrar för.
3. Välj **DNS-servrar** under **Inställningar**.
4. Välj något av följande alternativ:
   - **Standard (Azure-tillhandahöll)**: alla resurs namn och privata IP-adresser registreras automatiskt till Azure DNS servrar. Du kan matcha namn mellan alla resurser som är anslutna till samma virtuella nätverk. Du kan inte använda det här alternativet för att matcha namn i virtuella nätverk. Om du vill matcha namn i virtuella nätverk måste du använda en anpassad DNS-server.
   - **Anpassad**: du kan lägga till en eller flera servrar, upp till Azure-gränsen för ett virtuellt nätverk. Mer information om begränsningar för DNS-servern finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Du kan välja mellan följande alternativ:
   - **Lägg till en adress**: lägger till servern i listan med DNS-servrar för virtuella nätverk. Det här alternativet registrerar även DNS-servern med Azure. Om du redan har registrerat en DNS-server med Azure kan du välja den DNS-servern i listan.
   - **Ta bort en adress**: bredvid den server som du vill ta bort väljer du **...** och sedan **ta bort**. Om du tar bort servern tas bara servern bort från listan med virtuella nätverk. DNS-servern är fortfarande registrerad i Azure för de andra virtuella nätverk som ska användas.
   - **Sortera om DNS-serveradresser**: det är viktigt att kontrol lera att du listar dina DNS-servrar i rätt ordning för din miljö. Listor över DNS-servrar används i den ordning som de anges. De fungerar inte som en installation för resursallokering. Om den första DNS-servern i listan kan nås använder klienten den DNS-servern, oavsett om DNS-servern fungerar som den ska. Ta bort alla DNS-servrar som visas i listan och Lägg sedan till dem igen i den ordning du vill.
   - **Ändra en adress**: Markera DNS-servern i listan och ange den nya adressen.
5. Välj **Spara**.
6. Starta om de virtuella datorerna som är anslutna till det virtuella nätverket så tilldelas de nya DNS-serverinställningarna. De virtuella datorerna fortsätter att använda sina aktuella DNS-inställningar tills de startas om.

**Kommandon**

- Azure CLI: [AZ Network VNet Update](/cli/azure/network/vnet)
- PowerShell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Ta bort ett virtuellt nätverk

Du kan bara ta bort ett virtuellt nätverk om det inte finns några resurser anslutna till den. Om det finns resurser som är anslutna till ett undernät i det virtuella nätverket måste du först ta bort de resurser som är anslutna till alla undernät i det virtuella nätverket. De steg du tar för att ta bort en resurs varierar beroende på resursen. Läs dokumentationen för varje resurs typ som du vill ta bort för att lära dig hur du tar bort resurser som är anslutna till undernät. Så här tar du bort ett virtuellt nätverk:

1. Ange *virtuella nätverk* i sökrutan i rutan Sök högst upp i portalen. När **virtuella nätverk** visas i Sök resultaten väljer du det.
2. I listan med virtuella nätverk väljer du det virtuella nätverk som du vill ta bort.
3. Bekräfta att det inte finns några enheter anslutna till det virtuella nätverket genom att välja **anslutna enheter** under **Inställningar**. Om det finns anslutna enheter måste du ta bort dem innan du kan ta bort det virtuella nätverket. Om det inte finns några anslutna enheter väljer du **Översikt**.
4. Välj **Ta bort**.
5. Välj **Ja** för att bekräfta borttagningen av det virtuella nätverket.

**Kommandon**

- Azure CLI: [Azure Network VNet Delete](/cli/azure/network/vnet)
- PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i virtuella nätverk måste ditt konto tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som har tilldelats lämpliga åtgärder i följande tabell:

| Action                                  |   Name                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft. Network/virtualNetworks/Read   |   Läs ett virtuellt nätverk              |
|Microsoft. Network/virtualNetworks/Write  |   Skapa eller uppdatera ett virtuellt nätverk  |
|Microsoft. Network/virtualNetworks/Delete |   Ta bort ett virtuellt nätverk            |

## <a name="next-steps"></a>Nästa steg

- Skapa ett virtuellt nätverk med [PowerShell](powershell-samples.md) -eller [Azure CLI](cli-samples.md) -exempel skript eller med Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tilldela [Azure policy definitioner](./policy-reference.md) för virtuella nätverk