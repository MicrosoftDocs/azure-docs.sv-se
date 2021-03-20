---
title: Konfiguration av regler för routning av Azure Application Gateway-begäran
description: I den här artikeln beskrivs hur du konfigurerar regler för routning av Azure Application Gateway-begäranden.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 047ce9b33836e2c23a37b1383942323d7c382485
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397543"
---
# <a name="application-gateway-request-routing-rules"></a>Regler för routning av Application Gateway-begäran

När du skapar en Programgateway med hjälp av Azure Portal skapar du en standard regel (*regel 1*). Den här regeln binder standard avlyssningen (*appGatewayHttpListener*) med standard server delen (*appGatewayBackendPool*) och standardvärdet för Server delens http-inställningar (*appGatewayBackendHttpSettings*). När du har skapat gatewayen kan du redigera inställningarna för standard regeln eller skapa nya regler.

## <a name="rule-type"></a>Regeltyp

När du skapar en regel väljer du mellan [ *Basic* och *Path-based*](./application-gateway-components.md#request-routing-rules).

- Välj Basic om du vill vidarebefordra alla begär Anden på den associerade lyssnaren (till exempel *blogg <i></i> . contoso.com/ \* )* till en enda backend-pool.
- Välj sökväg – baserat om du vill dirigera begär Anden från särskilda URL-sökvägar till särskilda backend-pooler. Sök vägs mönstret används bara på sökvägen till URL: en, inte dess frågeparametrar.

### <a name="order-of-processing-rules"></a>Bearbetnings ordning för regler

För v1-och v2-SKU bearbetas mönster matchning av inkommande begär anden i den ordning som Sök vägarna visas i sökvägen till URL-sökvägen för den Sök vägs baserade regeln. Om en begäran matchar mönstret i två eller flera sökvägar i Sök vägs kartan matchas den sökväg som anges först. Och begäran vidarebefordras till Server delen som är kopplad till den sökvägen.

## <a name="associated-listener"></a>Associerad lyssnare

Koppla en lyssnare till regeln så att regeln för *begär ande routning* som är associerad med lyssnaren utvärderas för att fastställa backend-poolen för att skicka begäran till.

## <a name="associated-back-end-pool"></a>Associerad backend-pool

Koppla till regeln till den backend-pool som innehåller backend-målen som hanterar begär Anden som lyssnaren tar emot.

 - Endast en backend-pool tillåts för en grundläggande regel. Alla begär Anden på den associerade lyssnaren vidarebefordras till den backend-poolen.

 - För en sökväg-baserad regel lägger du till flera Server dels pooler som motsvarar varje URL-sökväg. De begär Anden som matchar URL-sökvägen som anges vidarebefordras till motsvarande backend-pool. Lägg också till en standardpool för Server delen. Begär Anden som inte matchar någon URL-sökväg i regeln vidarebefordras till poolen.

## <a name="associated-back-end-http-setting"></a>Associerad HTTP-inställning på Server Sidan

Lägg till en HTTP-inställning på Server sidan för varje regel. Begär Anden dirigeras från programgatewayen till backend-målen genom att använda port nummer, protokoll och annan information som anges i den här inställningen.

För en grundläggande regel tillåts bara en HTTP-inställning på Server sidan. Alla begär Anden på den associerade lyssnaren vidarebefordras till motsvarande backend-mål genom att använda den här HTTP-inställningen.

För en sökväg-baserad regel lägger du till flera Server dels-HTTP-inställningar som motsvarar varje URL-sökväg. Begär Anden som matchar URL-sökvägen i den här inställningen vidarebefordras till motsvarande Server dels mål genom att använda de HTTP-inställningar som motsvarar varje URL-sökväg. Lägg också till en standard-HTTP-inställning. Begär Anden som inte matchar någon URL-sökväg i den här regeln vidarebefordras till standard-slutpunkt-poolen med hjälp av standard-HTTP-inställningen.

## <a name="redirection-setting"></a>Inställning för omdirigering

Om omdirigering har kon figurer ATS för en grundläggande regel omdirigeras alla begär Anden på den associerade lyssnaren till målet. Detta är *Global* omdirigering. Om omdirigering har kon figurer ATS för en Sök vägs baserad regel omdirigeras endast begär anden i ett särskilt plats område. Ett exempel är ett shopping vagns områden som betecknas av */Cart/ \**. Detta är en *Path-baserad* omdirigering.

Mer information om omdirigering finns i [Application Gateway omdirigerings översikt](redirect-overview.md).

### <a name="redirection-type"></a>Typ av omdirigering

Välj den typ av omdirigering som krävs: *permanent (301)*, *temporär (307)*, *found (302)* eller *Se övrigt (303)*.

### <a name="redirection-target"></a>Omdirigerings mål

Välj en annan lyssnare eller en extern plats som omdirigerings mål.

#### <a name="listener"></a>Lyssnare

Välj lyssnare som omdirigerings mål för att omdirigera trafik från en lyssnare till en annan på gatewayen. Den här inställningen krävs om du vill aktivera HTTP-till-HTTPS-omdirigering. Den omdirigerar trafik från den käll lyssnare som söker efter inkommande HTTP-begäranden till mål lyssnaren som söker efter inkommande HTTPS-begäranden. Du kan också välja att inkludera frågesträngen och sökvägen från den ursprungliga begäran i begäran som vidarebefordras till omdirigerings målet.

![Dialog rutan Application Gateway-komponenter](./media/configuration-overview/configure-redirection.png)

Mer information om omdirigering av HTTP-till-HTTPS finns i:
- [HTTP-till-HTTPS-omdirigering med hjälp av Azure Portal](redirect-http-to-https-portal.md)
- [HTTP-till-HTTPS-omdirigering med hjälp av PowerShell](redirect-http-to-https-powershell.md)
- [HTTP-till-HTTPS-omdirigering med hjälp av Azure CLI](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>Extern webbplats

Välj extern plats när du vill dirigera om trafiken för den lyssnare som är kopplad till den här regeln till en extern plats. Du kan välja att inkludera frågesträngen från den ursprungliga begäran i begäran som vidarebefordras till omdirigerings målet. Du kan inte vidarebefordra sökvägen till den externa platsen som fanns i den ursprungliga begäran.

Mer information om omdirigering finns i:
- [Omdirigera trafik till en extern plats med hjälp av PowerShell](redirect-external-site-powershell.md)
- [Omdirigera trafik till en extern plats med hjälp av CLI](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>Skriva om HTTP-huvuden och URL

Genom att använda regler för att skriva om, kan du lägga till, ta bort eller uppdatera HTTP (S)-begäran och svars rubriker samt URL-sökväg och parametrar för frågesträngar som begär ande-och svars paket flyttas mellan klienten och backend-pooler via Application Gateway.

Parametrarna headers och URL kan anges till statiska värden eller till andra huvuden och servervariabler. Detta hjälper till med viktiga användnings fall, till exempel att extrahera klientens IP-adresser, ta bort känslig information om Server delen, lägga till mer säkerhet och så vidare.
Mer information finns i:

 - [Skriv över översikt över HTTP-rubriker och URL](rewrite-http-headers-url.md)
 - [Konfigurera omskrivning av HTTP-huvud](rewrite-http-headers-portal.md)
 - [Konfigurera URL-omskrivning](rewrite-url-portal.md)

## <a name="next-steps"></a>Nästa steg

- [Läs mer om HTTP-inställningar](configuration-http-settings.md)