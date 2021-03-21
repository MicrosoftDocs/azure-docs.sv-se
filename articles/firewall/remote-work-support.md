---
title: Support för Azure Firewall Remote Work
description: Den här artikeln visar hur Azure-brandväggen kan ge support för ditt krav på fjärranslutna arbets kraft.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 3c0e2033ee559af38a6816bdfa611eea86b14dea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94658323"
---
# <a name="azure-firewall-remote-work-support"></a>Support för Azure Firewall Remote Work

Azure-brandväggen är en hanterad, molnbaserad nätverks säkerhets tjänst som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillståndskänslig tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet.

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>Support för VDI-distribution (Virtual Desktop Infrastructure)

Arbete från start principer kräver många IT-organisationer för att åtgärda viktiga förändringar i kapacitet, nätverk, säkerhet och styrning. De anställda skyddas inte av de skiktade säkerhets principer som är kopplade till lokala tjänster samtidigt som de arbetar hemifrån. VDI-distributioner (Virtual Desktop Infrastructure) i Azure kan hjälpa organisationer att snabbt svara på den här ändrade miljön. Men du behöver ett sätt att skydda inkommande/utgående Internet åtkomst till och från dessa VDI-distributioner. Du kan använda Azure Firewall [DNAt-regler](rule-processing.md) tillsammans med dess [Hot information](threat-intel.md) -baserade filtrerings funktioner för att skydda dina VDI-distributioner.

## <a name="azure-windows-virtual-desktop-support"></a>Stöd för virtuella Azure Windows-datorer

Windows Virtual Desktop är en omfattande Desktop-och app Virtualization-tjänst som körs i Azure. Det är den enda virtuella Skriv bords infrastrukturen (VDI) som ger förenklad hantering, multi-session Windows 10, optimeringar för Microsoft 365 appar för företag och stöd för Fjärrskrivbordstjänster-miljöer (RDS). Du kan distribuera och skala dina Windows-datorer och appar på Azure på bara några minuter och få inbyggda funktioner för säkerhet och efterlevnad. Windows Virtual Desktop kräver inte att du öppnar någon inkommande åtkomst till det virtuella nätverket. Du måste dock tillåta en uppsättning utgående nätverks anslutningar för virtuella Windows-datorer med virtuella datorer som körs i det virtuella nätverket. Mer information finns i [använda Azure Firewall för att skydda fönster distributioner av virtuella skriv bord](protect-windows-virtual-desktop.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om [virtuella Windows-datorer](../virtual-desktop/index.yml).