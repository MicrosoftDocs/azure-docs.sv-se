---
title: Lägga till eller ta bort nätverksgränssnitt från virtuella Azure-datorer
description: Lär dig hur du lägger till nätverksgränssnitt till eller tar bort nätverksgränssnitt från virtuella datorer.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 847f8dbd2d8f4064f12333348a4f03e5c5fcc611
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774277"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Lägga till nätverksgränssnitt till eller ta bort nätverksgränssnitt från virtuella datorer

Lär dig hur du lägger till ett befintligt nätverksgränssnitt när du skapar en virtuell Azure-dator (VM). Lär dig också att lägga till eller ta bort nätverksgränssnitt från en befintlig virtuell dator i stoppat (frisallokerat) tillstånd. Med ett nätverksgränssnitt kan en virtuell Azure-dator kommunicera med Internet, Azure och lokala resurser. En virtuell dator har ett eller flera nätverksgränssnitt. 

Om du behöver lägga till, ändra eller ta bort IP-adresser för ett nätverksgränssnitt kan du se [Hantera IP-adresser för nätverksgränssnitt.](virtual-network-network-interface-addresses.md) Information om hur du skapar, ändrar eller tar bort nätverksgränssnitt finns [i Hantera nätverksgränssnitt.](virtual-network-network-interface.md)

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du inte har ett konto kan du konfigurera ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Utför någon av dessa uppgifter innan du påbörjar resten av den här artikeln:

- **Portalanvändare:** Logga in på [Azure Portal](https://portal.azure.com) ditt Azure-konto.

- **PowerShell-användare:** Kör antingen [kommandona i Azure Cloud Shell](https://shell.azure.com/powershell)eller kör PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. På Azure Cloud Shell webbläsarflik hittar du  listrutan Välj miljö och väljer **sedan PowerShell** om det inte redan är markerat.

    Om du kör PowerShell lokalt använder du Azure PowerShell version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Kör `Connect-AzAccount` för att skapa en anslutning med Azure.

- **Azure CLI-användare (Command-Line Interface):** Kör antingen kommandona [i Azure Cloud Shell](https://shell.azure.com/bash)eller kör CLI från datorn. Använd Azure CLI version 2.0.26 eller senare om du kör Azure CLI lokalt. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Kör `az login` för att skapa en anslutning med Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Lägga till befintliga nätverksgränssnitt till en ny virtuell dator

När du skapar en virtuell dator via portalen skapar portalen ett nätverksgränssnitt med standardinställningar och kopplar nätverksgränssnittet till den virtuella datorn åt dig. Du kan inte använda portalen för att lägga till befintliga nätverksgränssnitt till en ny virtuell dator eller för att skapa en virtuell dator med flera nätverksgränssnitt. Du kan göra båda med hjälp av CLI eller PowerShell. Se till att bekanta dig med [begränsningarna](#constraints). Om du skapar en virtuell dator med flera nätverksgränssnitt måste du också konfigurera operativsystemet så att det använder dem korrekt när du har skapat den virtuella datorn. Lär dig hur du [konfigurerar Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) [eller Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverksgränssnitt.

### <a name="commands"></a>Kommandon

Innan du skapar den virtuella datorn [skapar du ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface).

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Lägga till ett nätverksgränssnitt till en befintlig virtuell dator

Så här lägger du till ett nätverksgränssnitt till den virtuella datorn:

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta en befintlig virtuell dator. Sök efter och välj **Virtuella datorer.**

2. Välj namnet på den virtuella datorn. Den virtuella datorn måste ha stöd för det antal nätverksgränssnitt som du vill lägga till. Om du vill ta reda på hur många nätverksgränssnitt varje VM-storlek stöder kan du se storlekarna i Azure för virtuella [Linux-datorer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [virtuella Windows-datorer.](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

3. I kommandofältet för den virtuella datorn **väljer du** Stoppa och **sedan OK** i bekräftelsedialogrutan. Vänta sedan tills **Status** för den virtuella datorn ändras till **Stoppad (frisallokerad).**

4. På menyraden för virtuella datorer väljer du  >  **Nätverksanslutningsnätverksgränssnitt.** I Anslut **befintligt nätverksgränssnitt** väljer du sedan det nätverksgränssnitt som du vill koppla och väljer **OK.**

    >[!NOTE]
    >Nätverksgränssnittet du väljer kan inte ha accelererat nätverk aktiverat, kan inte ha en IPv6-adress tilldelad och måste finnas i samma virtuella nätverk med nätverksgränssnittet som för närvarande är kopplat till den virtuella datorn.

    Om du inte har ett befintligt nätverksgränssnitt måste du först skapa ett. Det gör du genom att **välja Skapa nätverksgränssnitt**. Mer information om hur du skapar ett nätverksgränssnitt finns i [Skapa ett nätverksgränssnitt.](virtual-network-network-interface.md#create-a-network-interface) Mer information om ytterligare begränsningar när du lägger till nätverksgränssnitt till virtuella datorer finns i [Begränsningar.](#constraints)

5. Starta om den virtuella datorn genom att välja **Översikt** Starta på  >   VM-menyraden.

Nu kan du konfigurera vm-operativsystemet så att det använder flera nätverksgränssnitt på rätt sätt. Lär dig hur du [konfigurerar Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) [eller Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverksgränssnitt.

### <a name="commands"></a>Kommandon

|Verktyg|Kommando|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (reference); [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens); [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Visa flera nätverksgränssnitt för en virtuell dator

Du kan visa de nätverksgränssnitt som för närvarande är anslutna till en virtuell dator om du vill veta mer om varje nätverksgränssnitts konfiguration och de IP-adresser som är tilldelade till varje nätverksgränssnitt. 

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta en befintlig virtuell dator. Sök efter och välj **Virtuella datorer.**

    >[!NOTE]
    >Logga in med ett konto som har tilldelats rollen Ägare, Deltagare eller Nätverksdeltagare för din prenumeration. Mer information om hur du tilldelar roller till konton finns [i Inbyggda roller för rollbaserad åtkomstkontroll i Azure.](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

2. Välj namnet på den virtuella dator som du vill visa anslutna nätverksgränssnitt för.

3. I menyraden för virtuella datorer väljer du **Nätverk.**

Mer information om inställningar för nätverksgränssnitt och hur du ändrar dem finns [i Hantera nätverksgränssnitt.](virtual-network-network-interface.md) Mer information om hur du lägger till, ändrar eller tar bort IP-adresser som är tilldelade till ett nätverksgränssnitt finns i [Hantera IP-adresser för nätverksgränssnitt.](virtual-network-network-interface-addresses.md)

### <a name="commands"></a>Kommandon

|Verktyg|Kommando|
|---|---|
|CLI|[az vm nic list](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Ta bort ett nätverksgränssnitt från en virtuell dator

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta en befintlig virtuell dator. Sök efter och välj **Virtuella datorer.**

2. Välj namnet på den virtuella dator som du vill visa anslutna nätverksgränssnitt för.

3. Välj Stoppa i verktygsfältet för den **virtuella datorn.**

4. Vänta tills **statusen** för den virtuella datorn ändras **till Stoppad (frisallokerad).**

5. Från menyraden för virtuella datorer väljer du **Nätverk**  >  **Koppla från nätverksgränssnitt.**

6. I **dialogrutan Koppla från nätverksgränssnitt** väljer du det nätverksgränssnitt som du vill koppla från. Välj sedan **OK**.

    >[!NOTE]
    >Om bara ett nätverksgränssnitt visas kan du inte koppla från det, eftersom en virtuell dator alltid måste ha minst ett nätverksgränssnitt kopplat till det.

### <a name="commands"></a>Kommandon

|Verktyg|Kommando|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (referens); [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens); [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Villkor

- En virtuell dator måste ha minst ett anslutet nätverksgränssnitt.

- En virtuell dator kan bara ha så många nätverksgränssnitt som den virtuella datorns storlek stöder. Mer information om hur många nätverksgränssnitt varje VM-storlek stöder finns i storlekarna i Azure för virtuella [Linux-datorer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [virtuella Windows-datorer.](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Alla storlekar stöder minst två nätverksgränssnitt.

- Nätverksgränssnitten som du lägger till i en virtuell dator kan för närvarande inte kopplas till en annan virtuell dator. Mer information om hur du skapar nätverksgränssnitt finns i [Skapa ett nätverksgränssnitt.](virtual-network-network-interface.md#create-a-network-interface)

- Tidigare kunde du bara lägga till nätverksgränssnitt till virtuella datorer som hade stöd för flera nätverksgränssnitt och som skapades med minst två nätverksgränssnitt. Det gick inte att lägga till ett nätverksgränssnitt till en virtuell dator som har skapats med ett nätverksgränssnitt, även om VM-storleken hade stöd för fler än ett nätverksgränssnitt. Omvänt kan du bara ta bort nätverksgränssnitt från en virtuell dator med minst tre nätverksgränssnitt, eftersom virtuella datorer som skapats med minst två nätverksgränssnitt alltid var tvungna att ha minst två nätverksgränssnitt. Dessa begränsningar gäller inte längre. Nu kan du skapa en virtuell dator med val annat antal nätverksgränssnitt (upp till det antal som stöds av VM-storleken).

- Som standard är det första nätverksgränssnittet som är kopplat till en virtuell dator det *primära* nätverksgränssnittet. Alla andra nätverksgränssnitt på den virtuella datorn är *sekundära* nätverksgränssnitt.

- Du kan styra vilket nätverksgränssnitt du skickar utgående trafik till. En virtuell dator skickar dock som standard all utgående trafik till den IP-adress som är tilldelad till den primära IP-konfigurationen för det primära nätverksgränssnittet.

- Tidigare krävdes att alla virtuella datorer i samma tillgänglighetsuppsättning hade ett enda eller flera nätverksgränssnitt. Virtuella datorer med val annat antal nätverksgränssnitt kan nu finnas i samma tillgänglighetsuppsättning, upp till det antal som stöds av VM-storleken. Du kan bara lägga till en virtuell dator till en tillgänglighetsuppsättning när den skapas. Mer information om tillgänglighetsuppsättningar finns i [Hantera tillgängligheten för virtuella datorer i Azure.](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

- Du kan ansluta nätverksgränssnitt på samma virtuella dator till olika undernät i ett virtuellt nätverk. Alla nätverksgränssnitt måste dock vara anslutna till samma virtuella nätverk.

- Du kan lägga till valfri IP-adress för alla IP-konfigurationer för alla primära eller sekundära nätverksgränssnitt Azure Load Balancer en serverpool. Tidigare kunde endast den primära IP-adressen för det primära nätverksgränssnittet läggas till i en backend-pool. Mer information om IP-adresser och konfigurationer finns i [Lägga till, ändra eller ta bort IP-adresser.](virtual-network-network-interface-addresses.md)

- Om du tar bort en virtuell dator tas inte de nätverksgränssnitt som är kopplade till den bort. När du tar bort en virtuell dator frånkopplade nätverksgränssnitten från den virtuella datorn. Du kan lägga till dessa nätverksgränssnitt till olika virtuella datorer eller ta bort dem.

- För att uppnå optimala prestanda som dokumenteras krävs accelererat nätverk. I vissa fall måste du uttryckligen aktivera accelererat nätverk för virtuella [Windows-](create-vm-accelerated-networking-powershell.md) eller [Linux-datorer.](create-vm-accelerated-networking-cli.md)

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="next-steps"></a>Nästa steg

Om du vill skapa en virtuell dator med flera nätverksgränssnitt eller IP-adresser kan du se:

|Uppgift|Verktyg|
|---|---|
|Skapa en virtuell dator med flera nätverkskort|[CLI,](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Skapa en enskild virtuell NIC-dator med flera IPv4-adresser|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Skapa en enskild virtuell NIC-dator med en privat IPv6-adress (bakom en Azure Load Balancer)|[CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Azure Resource Manager [mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|