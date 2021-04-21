---
title: Introduktion till vyn Gällande säkerhetsregler i Azure Network Watcher | Microsoft Docs
description: Den här sidan innehåller en översikt över Network Watcher – visning av gällande säkerhetsregler
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 54f1ef8f135c16b841df55094327c6bc5be521be
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778614"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Introduktion till vyn Gällande säkerhetsregler i Azure Network Watcher

Nätverkssäkerhetsgrupper är associerade på undernätsnivå eller på NIC-nivå. När det är associerat på undernätsnivå gäller det för alla VM-instanser i undernätet. Vyn Gällande säkerhetsregler returnerar alla konfigurerade NSG:er och regler som är associerade på NIC- och undernätsnivå för en virtuell dator som ger inblick i konfigurationen. Dessutom returneras gällande säkerhetsregler för vart och ett av nätverkskorten på en virtuell dator. Med hjälp av vyn Gällande säkerhetsregler kan du utvärdera en virtuell dator med hjälp av sårbarheter i nätverket, till exempel öppna portar. Du kan också kontrollera om nätverkssäkerhetsgruppen fungerar som förväntat baserat på en jämförelse mellan de [konfigurerade och godkända säkerhetsreglerna](network-watcher-nsg-auditing-powershell.md).

Ett mer utökat användningsfall handlar om säkerhetsefterlevnad och granskning. Du kan definiera en normativ uppsättning säkerhetsregler som en modell för säkerhetsstyrning i din organisation. En regelbunden efterlevnadsgranskning kan implementeras programmatiskt genom att jämföra de normativa reglerna med gällande regler för var och en av de virtuella datorerna i nätverket.

I portalen visas regler för varje nätverksgränssnitt och grupperas efter inkommande och utgående. Detta ger en enkel vy över de regler som tillämpas på en virtuell dator. En nedladdningsknapp finns för att enkelt ladda ned alla säkerhetsregler, oavsett fliken, till en CSV-fil.

![säkerhetsgruppvy][1]

Regler kan väljas och ett nytt blad öppnas för att visa nätverkssäkerhetsgruppen och käll- och målprefix. Från det här bladet kan du gå direkt till resursen Nätverkssäkerhetsgrupp.

![Specificera][2]

### <a name="next-steps"></a>Nästa steg

Du kan också använda funktionen *Effektiva säkerhetsgrupper* med hjälp av andra metoder som anges nedan:
* [REST-API](/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [PowerShell](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)
* [Azure CLI](/cli/azure/network/nic#az_network_nic_list_effective_nsg)

Lär dig hur du granskar inställningarna för nätverkssäkerhetsgruppen genom att [gå till Granska inställningar för nätverkssäkerhetsgrupp med PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png