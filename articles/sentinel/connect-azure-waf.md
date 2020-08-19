---
title: Anslut WAF-data (Azure Web Application Firewall) till Azure Sentinel
description: Lär dig hur du ansluter Azure WAF-data till Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: 8f21b415ef36442d6ac1aac518cd1327f70b8927
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88264076"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Ansluta data från Azure Web Application-brandväggen (WAF)

Webb program är alltmer riktade mot skadliga attacker som utnyttjar ofta kända sårbarheter. Azure brand vägg för webbaserade program (WAF) tillhandahåller centraliserat skydd av dina webb program från vanliga sårbarheter och hot, till exempel kod inmatning och skript körning över olika platser. Azure-WAF kan distribueras i [Azure Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview) -tjänsten, [Azures frontend](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview) -tjänst och via en [Azure Content Delivery Network-WAF (CDN)](https://docs.microsoft.com/azure/web-application-firewall/cdn/cdn-overview) (den aktuella för närvarande i offentlig för hands version).
Du kan ansluta Azure WAF-loggar till Azure Sentinel, så att du kan visa loggdata i arbets böcker, använda den för att skapa anpassade aviseringar och införliva den för att förbättra din undersökning.

## <a name="prerequisites"></a>Krav

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

## <a name="connect-to-azure-waf"></a>Anslut till Azure WAF

### <a name="instructions-tab"></a>Fliken instruktioner

1. Välj **data kopplingar**på navigerings menyn i Azure Sentinel.

1. Välj **Azure Web Application Firewall (WAF)** från data kopplings galleriet och välj sedan **Öppna kopplings sida** i förhands gransknings fönstret.

1. Välj länken för den typ av WAF-resurs vars loggar du vill ansluta – **öppna Application Gateway resurs >**, **Öppna Front dörr resurs >** eller **Öppna Content Delivery Network (CDN) WAF princip >** – och en gång i resurs listans skärm väljer du en WAF resurs i listan.

    1. Från WAF-resursens navigerings meny väljer du **diagnostikinställningar**.

    1. Välj **+ Lägg till diagnostisk inställning** längst ned i listan.

    1. På skärmen **diagnostikinställningar** anger du ett namn i fältet namn på **diagnos inställningar** .

    1. Klicka på kryss rutan **Skicka till Log Analytics** . Två nya fält kommer att visas under det. Välj relevant **prenumeration** och **Log Analytics arbets yta** (där Azure Sentinel finns).

    1. Klicka på kryss rutorna för de regel typer vars loggar du vill mata in. Våra rekommendationer för varje resurs typ:

        | Resurs | Loggar som ska väljas för inmatning |
        |----------|------------------------------|
        | Application Gateway | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | Princip för CDN-WAF      | WebApplicationFirewallLogs |
        |

    1. Välj **Spara**.

### <a name="next-steps-tab"></a>Fliken nästa steg

- Se de rekommenderade arbets böckerna, fråge exemplen och analys regel mallarna som är paketerade med **Azure Web Application Firewall** data Connector för att få insikter om dina data i dina Azure WAF-loggfiler.

- Om du vill fråga Azure WAF-data i **loggar**skriver du **AzureDiagnostics** i frågefönstret.

> [!NOTE]
>
> Med den här specifika data kopplingen visas anslutnings status indikatorerna (en färg remsa i galleriet för data anslutningar och anslutnings ikoner bredvid data typens namn) som *ansluten* (grön) endast om data har matats in vid någon tidpunkt under de senaste två veckorna. När två veckor har passerat utan data inmatning visas kopplingen som frånkopplad. Den tid då mer data kommer till kommer den *anslutna* statusen att returneras.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure WAF-loggar till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
