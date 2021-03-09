---
title: Lägg till eller ta bort nätverks gränssnitt från virtuella Azure-datorer
description: Lär dig hur du lägger till nätverks gränssnitt i eller tar bort nätverks gränssnitt från virtuella datorer.
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
ms.openlocfilehash: 43d3da49688a7daeb3ea4e0c1c3dba505dcd3b59
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508341"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Lägga till nätverksgränssnitt till eller ta bort nätverksgränssnitt från virtuella datorer

Lär dig hur du lägger till ett befintligt nätverks gränssnitt när du skapar en virtuell Azure-dator (VM). Lär dig också att lägga till eller ta bort nätverks gränssnitt från en befintlig virtuell dator i läget Stoppad (Frigjord). Ett nätverks gränssnitt gör det möjligt för en virtuell Azure-dator att kommunicera med Internet, Azure och lokala resurser. En virtuell dator har ett eller flera nätverks gränssnitt. 

Om du behöver lägga till, ändra eller ta bort IP-adresser för ett nätverks gränssnitt, se [Hantera IP-adresser för nätverks gränssnitt](virtual-network-network-interface-addresses.md). Information om hur du skapar, ändrar eller tar bort nätverks gränssnitt finns i [Hantera nätverks gränssnitt](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du inte har något konfigurerar du ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Slutför någon av dessa uppgifter innan du påbörjar resten av den här artikeln:

- **Portal användare**: Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

- **PowerShell-användare**: kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller kör PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. På fliken Azure Cloud Shell webbläsare letar du upp List rutan **Välj miljö** och väljer sedan **PowerShell** om den inte redan är markerad.

    Om du kör PowerShell lokalt använder du Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Kör `Connect-AzAccount` för att skapa en anslutning med Azure.

- **Kommando rads gränssnitt för Azure (CLI)**: kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller kör CLI från datorn. Använd Azure CLI version 2.0.26 eller senare om du kör Azure CLI lokalt. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Kör `az login` för att skapa en anslutning med Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Lägga till befintliga nätverks gränssnitt till en ny virtuell dator

När du skapar en virtuell dator via portalen skapar portalen ett nätverks gränssnitt med standardinställningar och kopplar nätverks gränssnittet till den virtuella datorn åt dig. Du kan inte använda portalen för att lägga till befintliga nätverks gränssnitt till en ny virtuell dator eller skapa en virtuell dator med flera nätverks gränssnitt. Du kan göra båda med CLI eller PowerShell. Se till att bekanta dig med [begränsningarna](#constraints). Om du skapar en virtuell dator med flera nätverks gränssnitt, måste du också konfigurera operativ systemet så att det används korrekt när du har skapat den virtuella datorn. Lär dig hur du konfigurerar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverks gränssnitt.

### <a name="commands"></a>Kommandon

[Skapa ett nätverks gränssnitt](virtual-network-network-interface.md#create-a-network-interface)innan du skapar den virtuella datorn.

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Lägga till ett nätverks gränssnitt till en befintlig virtuell dator

Så här lägger du till ett nätverks gränssnitt på den virtuella datorn:

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta en befintlig virtuell dator. Sök efter och välj **virtuella datorer**.

2. Välj namnet på den virtuella datorn. Den virtuella datorn måste ha stöd för det antal nätverks gränssnitt som du vill lägga till. För att ta reda på hur många nätverks gränssnitt varje VM-storlek stöder, se storlekarna i Azure för [virtuella Linux-datorer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [virtuella Windows-datorer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. I den virtuella datorns kommando fält väljer du **stoppa** och sedan **OK** i bekräftelse dialog rutan. Vänta tills **statusen** för den virtuella datorn ändras till **stoppad (Frigjord)**.

4. Från meny raden i virtuell **dator väljer du nätverk**  >  **Anslut nätverks gränssnitt**. I **bifoga befintligt nätverks gränssnitt** väljer du det nätverks gränssnitt som du vill koppla och väljer **OK**.

    >[!NOTE]
    >Det nätverks gränssnitt du väljer kan inte ha accelererat nätverk aktiverat, kan inte ha en tilldelad IPv6-adress och måste finnas i samma virtuella nätverk med det nätverks gränssnitt som är kopplat till den virtuella datorn.

    Om du inte har ett befintligt nätverks gränssnitt måste du först skapa ett. Det gör du genom att välja **skapa nätverks gränssnitt**. Mer information om hur du skapar ett nätverks gränssnitt finns i [skapa ett nätverks gränssnitt](virtual-network-network-interface.md#create-a-network-interface). Mer information om ytterligare begränsningar när du lägger till nätverks gränssnitt till virtuella datorer finns i [begränsningar](#constraints).

5. Från meny raden i virtuell dator väljer du **Översikt**  >  **Starta** för att starta om den virtuella datorn.

Nu kan du konfigurera den virtuella datorns operativ system så att det använder flera nätverks gränssnitt på rätt sätt. Lär dig hur du konfigurerar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverks gränssnitt.

### <a name="commands"></a>Kommandon

|Verktyg|Kommando|
|---|---|
|CLI|[AZ VM NIC Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (referens); [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens); [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Visa flera nätverksgränssnitt för en virtuell dator

Du kan visa de nätverks gränssnitt som är kopplade till en virtuell dator för att lära dig om varje nätverks gränssnitts konfiguration och de IP-adresser som tilldelats varje nätverks gränssnitt. 

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta en befintlig virtuell dator. Sök efter och välj **virtuella datorer**.

    >[!NOTE]
    >Logga in med ett konto som har tilldelats rollen ägare, deltagare eller nätverks deltagare för din prenumeration. Mer information om hur du tilldelar roller till konton finns i [inbyggda roller för rollbaserad åtkomst kontroll i Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).

2. Välj namnet på den virtuella dator som du vill visa anslutna nätverks gränssnitt för.

3. I meny raden VM väljer du **nätverk**.

Information om inställningar för nätverks gränssnitt och hur du ändrar dem finns i [Hantera nätverks gränssnitt](virtual-network-network-interface.md). Information om hur du lägger till, ändrar eller tar bort IP-adresser som tilldelats ett nätverks gränssnitt finns i [Hantera nätverks gränssnittets IP-adresser](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Kommandon

|Verktyg|Kommando|
|---|---|
|CLI|[AZ VM NIC-lista](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Ta bort ett nätverks gränssnitt från en virtuell dator

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta en befintlig virtuell dator. Sök efter och välj **virtuella datorer**.

2. Välj namnet på den virtuella dator som du vill visa anslutna nätverks gränssnitt för.

3. I verktygsfältet för virtuell dator väljer du **stoppa**.

4. Vänta tills **statusen** för den virtuella datorn ändras till **stoppad (Frigjord)**.

5. Från meny raden i virtuell dator väljer du **nätverk**  >  **Koppla bort nätverks gränssnitt**.

6. I dialog rutan **Koppla bort nätverks gränssnitt** väljer du det nätverks gränssnitt som du vill koppla från. Välj sedan **OK**.

    >[!NOTE]
    >Om det bara finns ett nätverks gränssnitt i listan kan du inte koppla bort det eftersom det alltid måste finnas minst ett nätverks gränssnitt kopplat till en virtuell dator.

### <a name="commands"></a>Kommandon

|Verktyg|Kommando|
|---|---|
|CLI|[AZ VM NIC Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (referens); [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens); [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Villkor

- En virtuell dator måste ha minst ett nätverks gränssnitt kopplat till sig.

- En virtuell dator kan bara ha så många nätverks gränssnitt som är anslutna till den virtuella datorns storlek. Mer information om hur många nätverks gränssnitt varje VM-storlek stöder finns i storlekarna i Azure för [virtuella Linux-datorer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [virtuella Windows-datorer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Alla storlekar har stöd för minst två nätverks gränssnitt.

- Nätverks gränssnitt som du lägger till i en virtuell dator kan för närvarande inte kopplas till en annan virtuell dator. Mer information om hur du skapar nätverks gränssnitt finns i [skapa ett nätverks gränssnitt](virtual-network-network-interface.md#create-a-network-interface).

- Tidigare kunde du bara lägga till nätverks gränssnitt för virtuella datorer som stödde flera nätverks gränssnitt och som har skapats med minst två nätverks gränssnitt. Du kunde inte lägga till ett nätverks gränssnitt till en virtuell dator som har skapats med ett nätverks gränssnitt, även om den virtuella dator storleken stöder fler än ett nätverks gränssnitt. Däremot kan du bara ta bort nätverks gränssnitt från en virtuell dator med minst tre nätverks gränssnitt, eftersom virtuella datorer som skapats med minst två nätverks gränssnitt alltid måste ha minst två nätverks gränssnitt. Dessa begränsningar gäller inte längre. Nu kan du skapa en virtuell dator med valfritt antal nätverks gränssnitt (upp till det antal som stöds av storleken på den virtuella datorn).

- Som standard är det första nätverks gränssnittet som är kopplat till en virtuell dator det *primära* nätverks gränssnittet. Alla andra nätverks gränssnitt på den virtuella datorn är *sekundära* nätverks gränssnitt.

- Du kan styra vilka nätverks gränssnitt du skickar utgående trafik till. En virtuell dator skickar som standard all utgående trafik till IP-adressen som har tilldelats den primära IP-konfigurationen för det primära nätverks gränssnittet.

- Tidigare behövde alla virtuella datorer i samma tillgänglighets uppsättning ha ett enskilt eller flera nätverks gränssnitt. Virtuella datorer med valfritt antal nätverks gränssnitt kan nu finnas i samma tillgänglighets uppsättning, upp till det antal som stöds av storleken på den virtuella datorn. Du kan bara lägga till en virtuell dator i en tillgänglighets uppsättning när den skapas. Mer information om tillgänglighets uppsättningar finns i [Hantera tillgängligheten för virtuella datorer i Azure](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

- Du kan ansluta nätverks gränssnitt i samma virtuella dator till olika undernät i ett virtuellt nätverk. Nätverks gränssnitten måste dock vara anslutna till samma virtuella nätverk.

- Du kan lägga till valfri IP-adress för alla IP-konfigurationer för ett primärt eller sekundärt nätverks gränssnitt till en Azure Load Balancer backend-pool. Tidigare kunde bara den primära IP-adressen för det primära nätverks gränssnittet läggas till i en backend-pool. Mer information om IP-adresser och konfigurationer finns i [lägga till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md).

- Om du tar bort en virtuell dator tas inte nätverks gränssnitten som är kopplade till den bort. När du tar bort en virtuell dator kopplas nätverks gränssnitten bort från den virtuella datorn. Du kan lägga till dessa nätverks gränssnitt till olika virtuella datorer eller ta bort dem.

- Att uppnå optimala prestanda som dokumenteras kräver accelererat nätverk. I vissa fall måste du uttryckligen aktivera accelererat nätverk för virtuella [Windows](create-vm-accelerated-networking-powershell.md) -eller [Linux](create-vm-accelerated-networking-cli.md) -datorer.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en virtuell dator med flera nätverks gränssnitt eller IP-adresser finns i:

|Uppgift|Verktyg|
|---|---|
|Skapa en virtuell dator med flera nätverkskort|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Skapa en virtuell dator med en virtuell NÄTVERKSKORTs uppsättning med flera IPv4-adresser|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Skapa en virtuell nätverkskort virtuell dator med en privat IPv6-adress (bakom en Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|