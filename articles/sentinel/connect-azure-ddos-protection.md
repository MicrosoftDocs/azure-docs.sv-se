---
title: Anslut Azure DDoS Protection data till Azure Sentinel
description: Lär dig hur du matar in Azure DDoS Protection data i Azure Sentinel, så att du kan visa den, analysera den och undersöka den.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 8089b1e74e88db81c1c15ad2cbf2072abcfff241
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621353"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Anslut data från Azure DDoS Protection

DDoS-attacker (distributed denial of Service) försöker avsluta ett programs resurser, vilket gör programmet otillgängligt för legitima användare. DDoS-attacker kan riktas mot valfri slutpunkt som kan nås offentligt via Internet. [Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md), kombinerat med bästa praxis för program design, ger ett robust försvar mot DDoS-attacker. Du kan ansluta Azure DDoS Protection loggar till Azure Sentinel, så att du kan visa loggdata i arbets böcker, använda den för att skapa anpassade aviseringar och införliva den för att förbättra dina undersökningar. 

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha en konfigurerad [Azure DDoS standard Protection-Plan](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan).

- Du måste ha ett konfigurerat [virtuellt nätverk med Azure DDoS standard aktiverat](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network).

## <a name="connect-to-azure-ddos-protection"></a>Anslut till Azure DDoS Protection
    
1. Välj **data kopplingar** på navigerings menyn i Azure Sentinel.

1. Välj **Azure DDoS Protection** i data kopplings galleriet och välj sedan **Öppna kopplings sida** i förhands gransknings fönstret.

1. Aktivera **diagnostikloggar** på alla offentliga IP-adresser vars loggar du vill ansluta:

    1. Välj länken **Öppna diagnostikinställningar >** och välj en **offentlig IP** -adressresurs i listan.

    1. Välj **+ Lägg till diagnostisk inställning**.

    1. På skärmen **diagnostikinställningar** :
       - Ange ett namn i fältet  **namn på diagnos inställning** .

       - Markera kryss rutan **Skicka till Log Analytics** . Två nya fält kommer att visas under det. Välj relevant **prenumeration** och **Log Analytics arbets yta** (där Azure Sentinel finns).

       - Markera kryss rutorna för de regel typer vars loggar du vill mata in. Vi rekommenderar **DDoSProtectionNotifications**, **DDoSMitigationFlowLogs** och **DDoSMitigationReports**.

    1. Klicka på **Spara** längst upp på skärmen. Upprepa den här processen för eventuella ytterligare brand väggar (offentliga IP-adresser) som du har aktiverat DDoS-skydd för.

1. Om du vill använda det relevanta schemat i Log Analytics för Azure DDoS Protection aviseringar söker du efter **AzureDiagnostics**.

> [!NOTE]
>
> Med den här specifika data kopplingen visas anslutnings status indikatorerna (en färg remsa i galleriet för data anslutningar och anslutnings ikoner bredvid data typens namn) som *ansluten* (grön) endast om data har matats in vid någon tidpunkt under de senaste två veckorna. När två veckor har passerat utan data inmatning visas kopplingen som frånkopplad. Den tid då mer data kommer till kommer den *anslutna* statusen att returneras.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure DDoS Protection loggar till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
