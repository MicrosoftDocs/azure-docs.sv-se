---
title: Nätverks alternativ för Azure Image Builder-tjänster
description: Förstå nätverks alternativen när du distribuerar Azure VM Image Builder-tjänsten
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: cd5027ca6e0ce3dc02da14b7dd6afd6e00e3f92d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669434"
---
# <a name="azure-image-builder-service-networking-options"></a>Nätverks alternativ för Azure Image Builder-tjänster

Du måste välja att distribuera Azure Image Builder med eller utan ett befintligt VNET.

## <a name="deploy-without-specifying-an-existing-vnet"></a>Distribuera utan att ange ett befintligt VNET

Om du inte anger ett befintligt virtuellt nätverk skapar Azure Image Builder ett VNET och undernät i den mellanlagrings resurs gruppen. En offentlig IP-resurs används med en nätverks säkerhets grupp för att begränsa inkommande trafik till Azure Image Builder-tjänsten. Den offentliga IP-adressen underlättar kanal för Azure Image Builder-kommandon under avbildnings versionen. När bygget är klart raderas den virtuella datorn, den offentliga IP-adressen, diskarna och det virtuella nätverket. Om du vill använda det här alternativet ska du inte ange några VNET-egenskaper.

## <a name="deploy-using-an-existing-vnet"></a>Distribuera med ett befintligt VNET

Om du anger ett VNET och undernät distribuerar Azure Image Builder den virtuella datorns build-dator till ditt valda VNET. Du kan komma åt resurser som är tillgängliga i ditt VNET. Du kan också skapa ett Silot VNET som inte är anslutet till något annat VNET. Om du anger ett VNET används inte en offentlig IP-adress i Azure Image Builder. Kommunikation från Azure Image Builder-tjänsten till build VM använder Azures teknik för privata länkar.

Mer information finns i något av följande exempel:

* [Använd Azure Image Builder för virtuella Windows-datorer som tillåter åtkomst till ett befintligt Azure VNET](../windows/image-builder-vnet.md)
* [Använd Azure Image Builder för virtuella Linux-datorer som ger åtkomst till ett befintligt Azure VNET](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>Vad är Azure Privat Link?

Azures privata länk ger privat anslutning från ett virtuellt nätverk till Azure-PaaS (Platform as a Service), kundägda eller Microsoft partner tjänster. Det fören klar nätverks arkitekturen och skyddar anslutningen mellan slut punkter i Azure genom att eliminera data exponeringen för det offentliga Internet. Mer information finns i dokumentationen till [privat länk](../../private-link/index.yml).

### <a name="required-permissions-for-an-existing-vnet"></a>Nödvändiga behörigheter för ett befintligt VNET

Azure Image Builder kräver vissa behörigheter för att använda ett befintligt VNET. Mer information finns i [Konfigurera Azure Image Builder-tjänst behörigheter med Azure CLI](image-builder-permissions-cli.md) eller [Konfigurera Azure avbildning Builder-tjänst behörigheter med PowerShell](image-builder-permissions-powershell.md).

### <a name="what-is-deployed-during-an-image-build"></a>Vad distribueras under en avbildnings version?

Att använda ett befintligt VNET innebär att Azure Image Builder distribuerar en ytterligare virtuell dator (proxy VM) och en Azure Load Balancer (ALB) som är ansluten till den privata länken. Trafik från AIB-tjänsten flyttas över den privata länken till ALB. ALB kommunicerar med den virtuella proxy-datorn med port 60001 för Linux OS eller 60000 för Windows OS. Proxyn vidarebefordrar kommandon till build VM med port 22 för Linux OS eller 5986 för Windows OS.

> [!NOTE]
> Det virtuella nätverket måste finnas i samma region som Azure Image Builder service-regionen.
> 

### <a name="why-deploy-a-proxy-vm"></a>Varför ska jag distribuera en virtuell proxy-dator?

När en virtuell dator utan offentlig IP-adress ligger bakom en intern Load Balancer har den inte Internet åtkomst. Den Azure Load Balancer som används för VNET är intern. Den virtuella proxy-datorn tillåter Internet åtkomst för den virtuella dator som skapas under build-versioner. De nätverks säkerhets grupper som är associerade kan användas för att begränsa skapandet av VM-åtkomst.

Den distribuerade virtuella proxyns storlek är standard A1_v2 förutom den virtuella dator som skapas. Den virtuella proxy-datorn används för att skicka kommandon mellan Azure Image Builder-tjänsten och build VM. Egenskaperna för den virtuella datorns proxy kan inte ändras, inklusive storlek eller OS. Du kan inte ändra egenskaperna för virtuella proxy-datorer för både Windows-och Linux-avbildningar.

### <a name="image-template-parameters-to-support-vnet"></a>Parametrar för avbildnings mal len som stöder VNET
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| Inställning | Beskrivning |
|---------|---------|
| name | Valfritt Namn på ett redan befintligt virtuellt nätverk. |
| subnetName | Namnet på under nätet i det angivna virtuella nätverket. Måste anges om och endast om *namn* har angetts. |
| resourceGroupName | Namnet på den resurs grupp som innehåller det angivna virtuella nätverket. Måste anges om och endast om *namn* har angetts. |

Private Link Service kräver en IP-adress från angivet VNET och undernät. För närvarande stöder inte Azure nätverks principer på de här IP-adresserna. Nätverks principer måste därför inaktive ras på under nätet. Mer information finns i dokumentationen till [privat länk](../../private-link/index.yml).

### <a name="checklist-for-using-your-vnet"></a>Check lista för att använda ditt VNET

1. Tillåt Azure Load Balancer (ALB) för att kommunicera med den virtuella proxy-datorn i en NSG
    * [AZ CLI-exempel](image-builder-vnet.md#add-network-security-group-rule)
    * [PowerShell-exempel](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. Inaktivera princip för privata tjänster på undernät
    * [AZ CLI-exempel](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [PowerShell-exempel](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Tillåt Azure Image Builder att skapa en ALB och lägga till virtuella datorer i VNET
    * [AZ CLI-exempel](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [PowerShell-exempel](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. Tillåt Azure Image Builder att läsa/skriva käll avbildningar och skapa bilder
    * [AZ CLI-exempel](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [PowerShell-exempel](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. Se till att du använder VNET i samma region som Azure Image Builder service-regionen.


## <a name="next-steps"></a>Nästa steg

Mer information finns i [Översikt över Azure Image Builder](../image-builder-overview.md).