---
title: Azure Application insikter telemetri data modell – telemetri-kontext | Microsoft Docs
description: Application Insights telemetri kontext data modell
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 76f3be510494a1f005b0080ee8f2390a3fbc3622
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91767850"
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetri-kontext: Application Insights data modell

Varje telemetri-objekt kan ha ett starkt angivet Sammanhangs fält. Varje fält aktiverar ett bestämt övervaknings scenario. Använd samlingen anpassade egenskaper för att lagra anpassad eller programspecifik kontext information.


## <a name="application-version"></a>Program version

Information i program kontexts fälten är alltid om det program som skickar Telemetrin. Program versionen används för att analysera trend ändringar i programmets beteende och dess korrelation till distributionerna.

Max längd: 1024


## <a name="client-ip-address"></a>Klientens IP-adress

IP-adressen för klient enheten. Har stöd för IPv4 och IPv6. När telemetri skickas från en tjänst är plats kontexten för den användare som initierade åtgärden i tjänsten. Application Insights extrahera information om geo-platsen från klientens IP-adress och sedan trunkera den. Klientens IP-adress kan inte användas som identifierbar information för slutanvändare. 

Max längd: 46


## <a name="device-type"></a>Enhetstyp

Ursprungligen användes det här fältet för att ange vilken typ av enhet som slutanvändaren använder. Används idag främst för att skilja på JavaScript-telemetri med enhets typen Browser från telemetri på Server sidan med enhets typen PC.

Max längd: 64


## <a name="operation-id"></a>Åtgärds-ID

En unik identifierare för rot åtgärden. Den här identifieraren gör det möjligt att gruppera telemetri över flera komponenter. Se [korrelation för telemetri](./correlation.md) för mer information. Åtgärds-ID: t skapas antingen i en begäran eller i en sid visning. All annan telemetri anger det här fältet till värdet för den som innehåller begäran eller sid visning. 

Max längd: 128


## <a name="parent-operation-id"></a>ID för överordnad åtgärd

Den unika identifieraren för telemetri-objektets direkta överordnade objekt. Se [korrelation för telemetri](./correlation.md) för mer information.

Max längd: 128


## <a name="operation-name"></a>Åtgärdsnamn

Åtgärdens namn (grupp). Åtgärds namnet skapas antingen av en begäran eller en sidvy. Alla andra telemetridata ställer in det här fältet till värdet för den som innehåller begäran eller sid visningen. Åtgärds namnet används för att hitta alla telemetri-objekt för en grupp med åtgärder (till exempel "GET Home/index"). Den här kontext egenskapen används för att besvara frågor som "vilka är de vanligaste undantagen som utlösts på den här sidan".

Max längd: 1024


## <a name="synthetic-source-of-the-operation"></a>Åtgärdens syntetiska källa

Namn på syntetisk källa. Vissa telemetri från programmet kan representera syntetisk trafik. Det kan vara webbcrawler som indexera webbplatsen, test av plats tillgänglighet eller spår från diagnostiska bibliotek som Application Insights SDK.

Max längd: 1024


## <a name="session-id"></a>Sessions-ID

Sessions-ID – instansen för användarens interaktion med appen. Informationen i fälten för sessions kontexten är alltid om slutanvändaren. När telemetri skickas från en tjänst är det den användare som initierade åtgärden i-sessionen.

Max längd: 64


## <a name="anonymous-user-id"></a>Anonymt användar-ID

Anonymt användar-ID. Representerar slutanvändaren för programmet. När telemetri skickas från en tjänst är användar kontexten för den användare som initierade åtgärden i tjänsten.

[Sampling](./sampling.md) är en av metoderna för att minimera mängden insamlad telemetri. Samplings algoritmen försöker antingen ta ett prov i eller ut hela den korrelerade Telemetrin. Anonymt användar-ID används för generering av samplings poäng. Anonymt användar-ID bör vara ett slumpmässigt tillräckligt värde. 

Att använda anonymt användar-ID för att lagra användar namn är ett missbruk av fältet. Använd autentiserat användar-ID.

Max längd: 128


## <a name="authenticated-user-id"></a>Autentiserat användar-ID

Autentiserat användar-ID. Motsatsen till anonymt användar-ID, det här fältet representerar användaren med ett eget namn. Detta samlas endast in som standard med ASP.NET Framework SDK: n [`AuthenticatedUserIdTelemetryInitializer`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/WEB/Src/Web/Web/AuthenticatedUserIdTelemetryInitializer.cs) .  

Max längd: 1024


## <a name="account-id"></a>Konto-ID

I program med flera klient organisationer är detta det konto-ID eller namn som användaren agerar med. Exempel kan vara prenumerations-ID för Azure Portal eller blogg namn för en blogg plattform.

Max längd: 1024


## <a name="cloud-role"></a>Moln roll

Namnet på rollen som programmet ingår i. Mappar direkt till roll namnet i Azure. Kan också användas för att särskilja Micro-tjänster, som ingår i ett enda program.

Max längd: 256


## <a name="cloud-role-instance"></a>Moln roll instans

Namnet på den instans där programmet körs. Dator namn för lokalt instans namn för Azure.

Max längd: 256


## <a name="internal-sdk-version"></a>Internt: SDK-version

SDK-version. Mer information finns i [den här artikeln](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md) .

Max längd: 64


## <a name="internal-node-name"></a>Internt: nodnamn

Det här fältet representerar det nodnamn som används för fakturerings syfte. Använd den för att åsidosätta standard identifiering av noder.

Max längd: 256


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [utökar och filtrerar telemetri](./api-filtering-sampling.md).
- Se [data modell](data-model.md) för Application Insights typer och data modell.
- Kolla i [konfiguration](./configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)av standard kontext egenskaper samling.

