---
title: Anslut Azure Firewall-data till Azure Sentinel
description: Lär dig hur du ansluter Azure Firewall-data till Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: b21ce75bfb33b5a8869c63b7d3f71fb9f0c93768
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98621303"
---
# <a name="connect-data-from-azure-firewall"></a>Anslut data från Azure-brandväggen

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillstånds känslig brand vägg som en tjänst med inbyggd hög tillgänglighet och obegränsad moln skalbarhet. 

Du kan ansluta Azure Firewall-loggar till Azure Sentinel, så att du kan visa loggdata i arbets böcker, använda den för att skapa anpassade aviseringar och införliva den för att förbättra din undersökning.

Läs mer om [övervakning av Azure Firewall-loggar](../firewall/firewall-diagnostics.md).

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

## <a name="connect-to-azure-firewall"></a>Ansluta till Azure-brandväggen
    
1. Välj **data kopplingar** på navigerings menyn i Azure Sentinel.

1. Välj **Azure-brandvägg** från data kopplings galleriet och välj sedan **Öppna kopplings sida**  i förhands gransknings fönstret.

1. Aktivera **diagnostikloggar** på alla brand väggar vars loggar du vill ansluta:

    1. Välj länken **öppna Azure Firewall-resursen >** .

    1. Från navigerings menyn **brand väggar** väljer du **diagnostikinställningar**.

    1. Välj **+ Lägg till diagnostisk inställning** längst ned i listan.

    1. På skärmen **diagnostikinställningar** anger du ett namn i fältet namn på  **diagnos inställningar** .
    
    1. Markera kryss rutan **Skicka till Log Analytics** . Två nya fält kommer att visas under det. Välj relevant **prenumeration** och **Log Analytics arbets yta** (där Azure Sentinel finns).

    1. Markera kryss rutorna för de regel typer vars loggar du vill mata in. Vi rekommenderar **AzureFirewallApplicationRule** och **AzureFirewallNetworkRule**.

    1. Välj **Spara** längst upp på skärmen.

1. Om du vill använda det relevanta schemat i Log Analytics för Azure Firewall-aviseringar söker du efter **AzureDiagnostics**.

> [!NOTE]
>
> Med den här specifika data kopplingen visas anslutnings status indikatorerna (en färg remsa i galleriet för data anslutningar och anslutnings ikoner bredvid data typens namn) som *ansluten* (grön) endast om data har matats in vid någon tidpunkt under de senaste två veckorna. När två veckor har passerat utan data inmatning visas kopplingen som frånkopplad. Den tid då mer data kommer till kommer den *anslutna* statusen att returneras.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Firewall-loggar till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).