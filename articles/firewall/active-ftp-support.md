---
title: Azure Firewall Active FTP-stöd
description: Som standard är Aktiv FTP inaktiverat på Azure Firewall. Du kan aktivera det med hjälp av PowerShell-, CLI- och ARM-mall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: victorh
ms.openlocfilehash: 18b3680e47fe808413998144259e033a4cbcaa27
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864477"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall Active FTP-stöd

Med Aktiv FTP initierar FTP-servern dataanslutningen till den avsedda FTP-klientdataporten. Brandväggar i klientnätverket blockerar vanligtvis en extern anslutningsbegäran till en intern klientport. Mer information finns i [Active FTP vs. Passive FTP, a Definitive Explanation](https://slacksite.com/other/ftp.html).

Som standard är active FTP-stöd inaktiverat på Azure Firewall för att skydda mot FTP-avvisningsattacker med hjälp av `PORT` FTP-kommandot. Du kan dock aktivera aktiv FTP när du distribuerar med Azure PowerShell, Azure CLI eller en Azure ARM-mall.

Följande TCP-portar måste vara öppna för att stödja aktiv-läge FTP:

- FTP-serverns port 21 var som helst (klienten initierar anslutning)
- FTP-serverns port 21 till portarna > 1023 (servern svarar på klientens kontrollport)
- FTP-serverns port 20 till portarna > 1023 på klienter (servern initierar dataanslutning till klientens dataport)
- FTP-serverns port 20 från portarna > 1023 på klienter (klienten skickar AK:er till serverns dataport)

## <a name="azure-powershell"></a>Azure PowerShell

Om du vill Azure PowerShell med hjälp av Azure PowerShell använder du `AllowActiveFTP` parametern . Mer information finns i Skapa [en brandvägg med Tillåt aktiv FTP.](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-)

## <a name="azure-cli"></a>Azure CLI

Om du vill distribuera med Hjälp av Azure CLI använder du `--allow-active-ftp` parametern . Mer information finns i [az network firewall create](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM)-mall

Om du vill distribuera med hjälp av en ARM-mall använder du `AdditionalProperties` fältet :

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Mer information finns i [Microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar en Azure Firewall finns i [Distribuera och konfigurera Azure Firewall med Azure PowerShell](deploy-ps.md).