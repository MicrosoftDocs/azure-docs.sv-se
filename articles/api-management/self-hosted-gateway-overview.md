---
title: Översikt över egen värd-Gateway | Microsoft Docs
description: 'Lär dig hur funktionen för lokal gateway i Azure API Management hjälper organisationer att hantera API: er i hybrid miljöer och moln miljöer.'
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/25/2021
ms.author: apimpm
ms.openlocfilehash: 48abce693ca22163c0a1742ba71faf36fc6156a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99989087"
---
# <a name="self-hosted-gateway-overview"></a>Översikt över gateway med egen värd

I den här artikeln förklaras hur en lokal gateway-funktion i Azure API Management aktiverar hybrid-och multi-Cloud API Management, presenterar den övergripande arkitekturen och markerar dess funktioner.

## <a name="hybrid-and-multi-cloud-api-management"></a>API Management för Hybrid och flera moln

Den lokala gateway-funktionen utökar API Management stöd för miljöer med hybrid miljöer och flera moln och gör det möjligt för organisationer att effektivt och säkert hantera API: er som finns lokalt och över moln från en enda API Management tjänst i Azure.

Med den egen värdbaserade gatewayen har kunderna möjlighet att distribuera en behållar version av API Management Gateway-komponenten till samma miljöer där de är värdar för API: erna. Alla egna gatewayer hanteras från den API Management tjänsten de är federerade med, och ger kunderna insyn och enhetlig hanterings upplevelse i alla interna och externa API: er. Att placera gatewayerna nära API: erna gör det möjligt för kunder att optimera API-trafikflöden och kraven på säkerhets-och efterlevnad.

Varje API Management tjänst består av följande viktiga komponenter:

-   Hanterings plan, som visas som ett API, som används för att konfigurera tjänsten via Azure Portal, PowerShell och andra mekanismer som stöds.
-   Gateway (eller data plan) är ansvarig för proxy-API-begäranden, tillämpa principer och samla in telemetri
-   Developer-portalen som används av utvecklare för att identifiera, lära sig och publicera för att använda API: erna

Som standard distribueras alla dessa komponenter i Azure, vilket ger all API-trafik (visas som heldragna svarta pilar på bilden nedan) för att flöda genom Azure, oavsett var Server delar som implementerar API: erna finns. Den drift enkelhet som används för den här modellen kommer till kostnaden för ökad latens, problem med efterlevnad och i vissa fall ytterligare avgifter för data överföring.

![API-trafikflöde utan egen värd-Gateway](media/self-hosted-gateway-overview/without-gateways.png)

Att distribuera lokala gateways i samma miljöer där Server dels API-implementeringar finns tillåter att API-trafik flödar direkt till Server dels-API: erna, vilket förbättrar svars tiden, optimerar kostnaderna för data överföring och möjliggör efterlevnad samtidigt som du behåller fördelarna med att ha en enda hanterings plats, lämplighet och identifiering av alla API: er inom organisationen oavsett var deras implementeringar finns.

![API-trafikflöde med egen värd-Gateway](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Paketering och funktioner

Den egen värdbaserade gatewayen är en behållare som är funktions ekvivalent med den hanterade gateway som distribuerats till Azure som en del av varje API Management tjänst. Den egna värdbaserade gatewayen är tillgänglig som en Linux-baserad Docker- [behållare](https://aka.ms/apim/sputnik/dhub) från Microsoft container Registry. Den kan distribueras till Docker, Kubernetes eller någon annan lösning för behållar dirigering som körs på ett Server kluster lokalt, i molnet eller i utvärderings-och utvecklings syfte på en personlig dator.

Följande funktioner som finns i de hanterade gatewayerna är **inte tillgängliga** i de egna värdbaserade gatewayerna:

- Azure Monitor-loggar
- Överordnad (Server dels sida) TLS-version och cipher-hantering
- Validering av Server-och klient certifikat med hjälp av [certifikat utfärdarens rot certifikat](api-management-howto-ca-certificates.md) som laddats upp till API Management tjänsten. Mer information finns i [certifikat validering i lokal gateway](api-management-howto-mutual-certificates-for-clients.md#certificate-validation-in-self-hosted-gateway).
- Integrering med [Service Fabric](../service-fabric/service-fabric-api-management-overview.md)
- Återupptagande av TLS-session
- Återförhandlat klient certifikat. Det innebär att för [autentisering av klient certifikat](api-management-howto-mutual-certificates-for-clients.md) till arbets-API-konsumenter måste presentera sina certifikat som en del av den första TLS-handskakningen. För att se till att aktivera inställningen förhandla klient certifikat när du konfigurerar ett anpassat värdnamn för en egen värd.
- Inbyggt cacheminne. Se det här [dokumentet](api-management-howto-cache-external.md) om du vill veta mer om hur du använder extern cache i egna värdbaserade gatewayer.

## <a name="connectivity-to-azure"></a>Anslutningar till Azure SQL

Gatewayer för egen värd kräver utgående TCP/IP-anslutning till Azure på port 443. Varje lokal gateway måste associeras med en enda API Management tjänst och konfigureras via dess hanterings plan. Den egna värdbaserade gatewayen använder anslutning till Azure för:

-   Rapportera status genom att skicka pulsslags meddelanden varje minut
-   Söker regelbundet efter (var tionde sekund) och tillämpar konfigurations uppdateringar när de är tillgängliga
-   Skicka begär ande loggar och mått till Azure Monitor, om det är konfigurerat att göra det
-   Skicka händelser till Application Insights, om det är inställt på detta

När anslutningen till Azure förloras kan inte gatewayen för egen värd ta emot konfigurations uppdateringar, rapportera statusen eller överföra telemetri.

Den egen värdbaserade gatewayen är utformad för att "misslyckad statisk" och kan överleva tillfälligt förlorad anslutning till Azure. Den kan distribueras med eller utan en lokal konfigurations säkerhets kopia. I det förra fallet sparar lokala gatewayer regelbundet en säkerhets kopia av den senaste nedladdade konfigurationen på en permanent volym som är kopplad till dess behållare eller pod.

När säkerhets kopiering av konfigurationen är avstängd och anslutningen till Azure avbryts:

-   Att köra gateways med egen värd fortsätter att fungera med en minnes kopia av konfigurationen
-   Stoppade egna gatewayer kommer inte att kunna starta

När säkerhets kopiering av konfigurationen är aktive rad och anslutningen till Azure avbryts:

-   Att köra gateways med egen värd fortsätter att fungera med en minnes kopia av konfigurationen
-   Stoppade egna gatewayer kommer att kunna börja använda en säkerhets kopia av konfigurationen

När anslutningen återställs återansluter varje lokal gateway som påverkas av avbrottet automatiskt till den associerade API Management tjänsten och hämtar alla konfigurations uppdateringar som uppstod när gatewayen var offline.

## <a name="next-steps"></a>Nästa steg

-   [Läs ett whitepaper om du vill ha mer bakgrund i det här avsnittet](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Distribuera egen värd-Gateway till Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [Distribuera egen värd-Gateway till Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
