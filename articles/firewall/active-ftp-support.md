---
title: Azure Firewall Active FTP-stöd
description: Som standard är Active FTP inaktiverat i Azure-brandväggen. Du kan aktivera den med hjälp av PowerShell-, CLI-och ARM-mallarna.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 2c89eb19025212490d29c97a061da52e779ecf42
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704405"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall Active FTP-stöd

Med Active FTP initierar FTP-servern data anslutningen till den angivna data porten för FTP-klienten. Brand väggar i nätverk på klient sidan blockerar normalt en begäran om extern anslutning till en intern klient port. Mer information finns i [Active FTP vs. passiv FTP, en slutgiltig förklaring](https://slacksite.com/other/ftp.html).

Som standard är Active FTP-stöd inaktiverat i Azure-brandväggen för att skydda mot FTP-attacker med FTP- `PORT` kommandot. Du kan dock aktivera aktiv FTP när du distribuerar med Azure PowerShell, Azure CLI eller en Azure ARM-mall.

> [!NOTE]
> För närvarande stöds endast Active FTP för brand väggar som distribueras i ett virtuellt nätverk. Virtual WAN-stöd kommer att läggas till senare.

## <a name="azure-powershell"></a>Azure PowerShell

Använd parametern för att distribuera med Azure PowerShell `AllowActiveFTP` . Mer information finns i [skapa en brand vägg med Tillåt Active FTP](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>Azure CLI

Använd parametern för att distribuera med hjälp av Azure CLI `--allow-active-ftp` . Mer information finns i [AZ Network Firewall Create](/cli/azure/ext/azure-firewall/network/firewall?view=azure-cli-latest#ext_azure_firewall_az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM)-mall

Om du vill distribuera med en ARM-mall använder du `AdditionalProperties` fältet:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Mer information finns i [Microsoft. Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar en Azure-brandvägg finns i [distribuera och konfigurera Azure-brandväggen med Azure PowerShell](deploy-ps.md).