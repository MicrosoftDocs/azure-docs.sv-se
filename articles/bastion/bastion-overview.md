---
title: Azure-skydds | Microsoft Docs
description: Lär dig om Azure skydds, som ger säker och sömlös RDP/SSH-anslutning till dina virtuella datorer utan att exponera RDP/SSH-portar externt.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 01/31/2020
ms.author: cherylmc
ms.openlocfilehash: 8b27a7c2fd03ed0a80f1775465f1f1bbb44f0202
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270350"
---
# <a name="what-is-azure-bastion"></a>Vad är Azure Bastion?

Azure skydds-tjänsten är en ny helt plattforms oberoende PaaS-tjänst som du etablerar i det virtuella nätverket. Den ger säker och sömlös RDP/SSH-anslutning till dina virtuella datorer direkt i Azure Portal via TLS. När du ansluter via Azure Bastion behöver dina virtuella datorer inte någon offentlig IP-adress.

Skydds tillhandahåller säker RDP-och SSH-anslutning till alla virtuella datorer i det virtuella nätverk där den är etablerad. Med hjälp av Azure skydds kan du skydda dina virtuella datorer från att exponera RDP/SSH-portar till utsidan, samtidigt som du ger säker åtkomst med RDP/SSH. Med Azure skydds ansluter du till den virtuella datorn direkt från Azure Portal. Du behöver inte ytterligare en klient, en agent eller en program varu enhet.

## <a name="architecture"></a>Arkitektur

Azure skydds-distributionen är per virtuellt nätverk, inte per prenumeration/konto eller virtuell dator. När du har etablerat en Azure skydds-tjänst i det virtuella nätverket är RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk.

RDP och SSH är några av de grundläggande metoder som du kan använda för att ansluta till dina arbets belastningar som körs i Azure. Att exponera RDP/SSH-portar över Internet är inte önskas och visas som en betydande hot yta. Detta beror ofta på protokoll sårbarheter. För att kunna innehålla den här hot ytan kan du distribuera skydds-värdar (kallas även hopp servrar) på den offentliga sidan av perimeternätverket. Skydds-värd servrar är utformade och konfigurerade för att motstå attacker. Skydds-servrar tillhandahåller också RDP-och SSH-anslutningar till arbets belastningarna bakom skydds, och även i nätverket.

![Arkitektur](./media/bastion-overview/architecture.png)

Den här bilden visar arkitekturen för en Azure skydds-distribution. I det här diagrammet:

* Skydds-värden har distribuerats i det virtuella nätverket.
* Användaren ansluter till Azure Portal med valfri HTML5-webbläsare.
* Användaren väljer den virtuella dator som du vill ansluta till.
* Med ett enda klick öppnas RDP/SSH-sessionen i webbläsaren.
* Ingen offentlig IP-adress krävs på den virtuella Azure-datorn.

## <a name="key-features"></a>Huvudfunktioner

Följande funktioner är tillgängliga:

* **RDP och SSH direkt i Azure Portal:** Du kan direkt komma åt RDP-och SSH-sessionen direkt i Azure Portal med ett enda klick sömlöst.
* **Fjärrsession över TLS-och brand Väggs Traversal för RDP/SSH:** Azure-skydds använder en HTML5-baserad webb klient som automatiskt strömmas till din lokala enhet, så att du får din RDP/SSH-session över TLS på port 443, så att du kan förflytta företags brand väggar på ett säkert sätt.
* **Ingen offentlig IP krävs på den virtuella Azure-datorn:** Azure skydds öppnar RDP/SSH-anslutningen till din virtuella Azure-dator med hjälp av privat IP på den virtuella datorn. Du behöver ingen offentlig IP-adress på den virtuella datorn.
* **Inget krångel för att hantera NSG: er:** Azure skydds är en helt hanterad plattform PaaS-tjänst från Azure som är skärpt internt för att tillhandahålla säker RDP/SSH-anslutning. Du behöver inte använda någon NSG: er i Azure skydds-undernätet. Eftersom Azure skydds ansluter till dina virtuella datorer via privat IP-adress kan du konfigurera din NSG: er för att tillåta RDP/SSH från Azure skydds. Detta eliminerar besväret med att hantera NSG: er varje gången du behöver ansluta till dina virtuella datorer på ett säkert sätt.
* **Skydd mot Port genomsökning:** Eftersom du inte behöver exponera dina virtuella datorer på offentliga Internet skyddas dina virtuella datorer mot Port genomsökning av falska och skadliga användare som finns utanför det virtuella nätverket.
* **Skydda dig mot noll dagar. Härdning endast på ett enda ställe:** Azure skydds är en fullständigt plattforms oberoende PaaS-tjänst. Eftersom det finns i det virtuella nätverkets perimeter behöver du inte bekymra dig om att skärpa var och en av de virtuella datorerna i det virtuella nätverket. Azure-plattformen skyddar mot en noll-dagars sårbarhet genom att hålla Azure-skydds skärpt och alltid uppdaterat.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure skydds Host-resurs](bastion-create-host-portal.md).
* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
