---
title: Översikt över Azure Industrial IoT
description: Den här artikeln innehåller en översikt över bransch-IoT. Här förklaras anslutningen till butiksgolvet och säkerhetskomponenterna i IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: ce582f810f483f2e5d3fdda2c3379ecad3842d51
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813280"
---
# <a name="what-is-industrial-iot-iiot"></a>Vad är bransch-IoT (IIoT)?

![Bransch-IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

Microsoft Azure Industrial Sakernas Internet (IIoT) är en uppsättning Azure-moduler och -tjänster som integrerar molnets kraft i industriella golv och fabriksgolv. Med branschstandardbaserade öppna gränssnitt som [OPC UA (Open Platform Communications Unified Architecture),](https://opcfoundation.org/about/opc-technologies/opc-ua/)ger Azure IIoT dig möjlighet att integrera data från tillgångar och sensorer – inklusive de som redan arbetar på fabriksgolvet – i Azure-molnet. Genom att ha dina data i molnet kan de användas snabbare och flexibelt som feedback för att utveckla transformativa affärs- och industriella processer.

## <a name="discover-register-and-manage-your-industrial-assets-with-azure"></a>Identifiera, registrera och hantera dina industriella tillgångar med Azure

Med Azure Industrial IoT kan anläggningsoperatörer identifiera OPC UA-aktiverade servrar i ett fabriksnätverk och registrera dem i Azure IoT Hub. Driftpersonal kan prenumerera på och reagera på händelser på fabriksgolvet var som helst i världen, få aviseringar och larm och reagera på dem i realtid.

IIoT tillhandahåller en uppsättning mikrotjänster som implementerar OPC UA-funktioner. REST-API:erna för Mikrotjänster speglar OPC UA-tjänsternas kantsida. De skyddas med OAUTH-autentisering och -auktorisering som backas upp Azure Active Directory (AAD). På så sätt kan dina molnprogram bläddra bland serveradressutrymmen eller läs-/skrivvariabler och köra metoder med hjälp av HTTPS och enkla OPC UA JSON-nyttolaster. Gränstjänsterna implementeras som Azure IoT Edge moduler och körs lokalt. Moln mikrotjänsterna implementeras som ASP.NET mikrotjänster med ett REST-gränssnitt och körs på hanterade Azure Kubernetes Services eller fristående på Azure App Service. För både gräns- och molntjänster tillhandahåller IIoT förbyggda Docker-containrar i Microsoft Container Registry (MCR), vilket tar bort det här steget för kunden. Gräns- och molntjänsterna utnyttjar varandra och måste användas tillsammans. IIoT innehåller också lättanvända distributionsskript som gör det möjligt att distribuera hela plattformen med ett enda kommando.

Dessutom kan REST-API:erna användas med alla programmeringsspråk via den exponerade Open API-specifikationen (Swagger). Det innebär att när du integrerar OPC UA i molnhanteringslösningar kan utvecklare välja teknik som matchar deras färdigheter, intressen och arkitekturval. Till exempel kan en komplett stack-webbutvecklare som utvecklar ett program för ett larm och en instrumentpanel för händelser skriva logik för att svara på händelser i JavaScript eller TypeScript utan att komma upp i OPC UA SDK, C, C++, Java eller C#.

## <a name="manage-certificates-and-trust-groups"></a>Hantera certifikat och förtroendegrupper

Azure Industrial IoT hanterar OPC UA-programcertifikat och förtroendelistor för fabriksgolvmaskiner och kontrollsystem för att skydda OPC UA-klient-till-server-kommunikationen. Den begränsar vilken klient som får tala med vilken server. Lagring av privata nycklar och signering av certifikat backas upp Azure Key Vault, som stöder maskinvarubaserad säkerhet (HSM).

## <a name="industrial-iot-components"></a>Industriella IoT-komponenter

Azure IIoT-lösningar bygger på specifika komponenter. Dessa inkluderar följande.

- **Minst en Azure IoT Hub.**
- **IoT Edge enheter.**
- **Industrial Edge-moduler.**

### <a name="iot-hub"></a>IoT Hub
[Azure IoT Hub]( fungerar som en central meddelandehubb för säker dubbelriktad kommunikation mellan alla IoT-program och de enheter https://azure.microsoft.com/services/iot-hub/ som den hanterar. Det är en öppen och flexibel molnplattform som en tjänst (PaaS) som stöder SDK:er med öppen källkod och flera protokoll. 

Genom att samla in bransch- och affärsdata på IoT Hub kan du lagra dina data på ett säkert sätt, utföra verksamhets- och effektivitetsanalyser på dem och generera rapporter från dem. Du kan också Microsoft Azure tjänster och verktyg, till exempel [Power BI](https://powerbi.microsoft.com), på dina konsoliderade data.

### <a name="iot-edge-devices"></a>IoT Edge enheter
Gränstjänsterna [implementeras](https://azure.microsoft.com/services/iot-edge/) som Azure IoT Edge moduler och körs lokalt. Moln mikrotjänster implementeras som ASP.NET mikrotjänster med ett REST-gränssnitt och körs på hanterade Azure Kubernetes Services eller fristående på Azure App Service. För både gräns- och molntjänster har vi tillhandahållit förbyggda Docker-containrar i Microsoft Container Registry (MCR), vilket tar bort det här steget för kunden. Gräns- och molntjänsterna utnyttjar varandra och måste användas tillsammans. Vi har också tillhandahållit lättanvända distributionsskript som gör det möjligt att distribuera hela plattformen med ett enda kommando.

En IoT Edge enhet består av Edge Runtime- och Edge-moduler.
- **Edge-moduler** är Docker-containrar, som är den minsta beräkningsenheten, som OPC Publisher och OPC Twin. 
- **Edge-enheten** används för att distribuera sådana moduler, som fungerar som medlare mellan OPC UA-servern och IoT Hub i molnet.

### <a name="industrial-edge-modules"></a>Industrial Edge-moduler
- **OPC Publisher:** OPC Publisher körs i IoT Edge. Den ansluter till OPC UA-servrar och publicerar JSON-kodade telemetridata från dessa servrar i OPC UA-format "Pub/Sub" till Azure IoT Hub. Alla transportprotokoll som stöds av Azure IoT Hub klient-SDK kan användas, t.ex. HTTPS, AMQP och MQTT.
- **OPC-tvilling:** OPC-tvillingen består av mikrotjänster som använder Azure IoT Edge och IoT Hub för att ansluta molnet och fabriksnätverket. OPC Twin tillhandahåller identifiering, registrering och fjärrstyrning av industriella enheter via REST-API:er. OPC Twin kräver inte NÅGON OPC Unified Architecture (OPC UA) SDK. Det är programmeringsspråksoberoende och kan ingå i ett serverlöst arbetsflöde.
- **Identifiering:** Identifieringsmodulen, som representeras av identifieringsidentiteten, tillhandahåller identifieringstjänster vid gränsen, inklusive OPC UA-serveridentifiering. Om identifiering har konfigurerats och aktiverats skickar modulen resultatet av en skanningsavsökning via IoT Edge och IoT Hub telemetrisökvägen till onboarding-tjänsten. Tjänsten bearbetar resultaten och uppdaterar alla relaterade identiteter i registret.

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig vad bransch-IoT är kan du läsa om den industriella IoT-plattformen och OPC Publisher:

> [!div class="nextstepaction"]
> [Vad är OPC Publisher?](overview-what-is-opc-publisher.md)