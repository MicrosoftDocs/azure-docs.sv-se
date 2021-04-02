---
title: Vad är en skyddad virtuell hubb?
description: Läs mer om skyddade virtuella hubbar
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91948076"
---
# <a name="what-is-a-secured-virtual-hub"></a>Vad är en skyddad virtuell hubb?

En virtuell hubb är ett Microsoft-hanterat virtuellt nätverk som möjliggör anslutning från andra resurser. När en virtuell hubb skapas från ett virtuellt WAN-nätverk i Azure Portal skapas en virtuell hubb och gateway (valfritt) som dess komponenter.

En *säker* virtuell hubb är en [virtuell WAN-hubb i Azure](../virtual-wan/virtual-wan-about.md#resources) med associerade säkerhets-och routningsprinciperna som kon figurer ATS av Azure Firewall Manager. Använd skyddade virtuella hubbar för att enkelt skapa nav-och ekrar och transitiva arkitekturer med interna säkerhets tjänster för trafik styrning och skydd. 

Du kan använda en säker virtuell hubb för att filtrera trafik mellan virtuella nätverk (V2V), virtuella nätverk och avdelnings kontor (B2V) och trafik till Internet (B2I/V2I). En säker virtuell hubb ger automatisk routning. Du behöver inte konfigurera egna UDR (användardefinierade vägar) för att dirigera trafik genom brand väggen.

Du kan välja vilka säkerhets leverantörer som krävs för att skydda och styra nätverks trafiken, inklusive Azure Firewall, SECaaS-providers (Security as a Service) eller båda. För närvarande stöder inte en skyddad hubb gren till förgrening (B2B)-filtrering och filtrering över flera hubbar. Mer information finns i [Vad är Azure Firewall Manager?](overview.md#known-issues). 

## <a name="create-a-secured-virtual-hub"></a>Skapa en säker virtuell hubb

Med hjälp av brand Väggs hanteraren i Azure Portal kan du antingen skapa en ny säker virtuell hubb eller konvertera ett befintligt virtuellt nav som du tidigare har skapat med Azure Virtual WAN.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en säker virtuell hubb och använder den för att skydda och styra ett nav och eker-nätverk finns i [Självstudier: skydda ditt moln nätverk med Azure Firewall Manager med hjälp av Azure Portal](secure-cloud-network.md).