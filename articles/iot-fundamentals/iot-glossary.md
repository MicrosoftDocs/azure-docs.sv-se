---
title: Azure IoT-ordlista med villkor | Microsoft Docs
description: Guide för utvecklare – en ord lista som förklarar några av de vanliga termer som används i Azure IoT-artiklarna.
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: d7ae1e72dee28509c1338a1b56cf42a5293af9bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670264"
---
# <a name="glossary-of-iot-terms"></a>Ord lista med IoT-termer

Den här artikeln innehåller några av de vanliga termer som används i IoT-artiklarna.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Avancerat protokoll för meddelandekö

[Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) är ett av de meddelande protokoll som [IoT Hub](#iot-hub) stöder för att kommunicera med enheter. Mer information om de meddelande protokoll som IoT Hub stöder finns i [skicka och ta emot meddelanden med IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="allocation-policy"></a>Resursallokeringsprincip

I [enhets etablerings tjänsten](#device-provisioning-service)avgör allokeringsregeln hur tjänsten tilldelar enheter till [länkade IoT-hubbar](#linked-iot-hub).

### <a name="attestation-mechanism"></a>Mekanism för attestering

I [Device Provisioning-tjänsten](#device-provisioning-service)är mekanismen för attestering den metod som används för att bekräfta en enhets identitet. Mekanismen för attestering har kon figurer ATS vid [registrering](#enrollment).

Mekanismer för attestering inkluderar 509-certifikat, betrodda plattformar och symmetriska nycklar.

### <a name="automatic-deployment"></a>Automatisk distribution

I IoT Edge konfigurerar en automatisk distribution en mål uppsättning med IoT Edge enheter för att köra en uppsättning IoT Edge-moduler. Varje distribution säkerställer kontinuerligt att alla enheter som matchar dess mål villkor kör den angivna uppsättningen moduler, även när nya enheter skapas eller ändras för att matcha mål villkoret. Varje IoT Edge enhet tar bara emot den högsta prioritets distributionen vars mål villkor är uppfyllda. Läs mer om [IoT Edge automatisk distribution](../iot-edge/module-deployment-monitoring.md).

### <a name="automatic-device-configuration"></a>Automatisk enhets konfiguration

Server delen av lösningen kan använda [automatiska enhets konfigurationer](../iot-hub/iot-hub-automatic-device-management.md) för att tilldela önskade egenskaper till en uppsättning [enheter, dubbla](#device-twin) och rapportera status med hjälp av system mått och anpassade mått.

### <a name="automatic-device-management"></a>Automatisk enhets hantering

Automatisk enhets hantering i Azure IoT Hub automatiserar många av de repetitiva och komplexa uppgifterna i hanteringen av stora enhets flottor över hela livs cykeln. Med automatisk enhets hantering kan du rikta en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och låta IoT Hub uppdatera enheter när de kommer till omfånget.  Består av [Automatisk enhets konfiguration](../iot-hub/iot-hub-automatic-device-management.md) och [IoT Edge automatiska distributioner](../iot-edge/how-to-deploy-at-scale.md).

### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digitals är ett PaaS-erbjudande (Platform as a Service) för att skapa digitala representationer av verkliga saker, platser, affärs processer och människor. Skapa kunskaps diagram som representerar hela miljöer och Använd dem för att få insikter om bättre produkter, optimera drift och kostnader och skapa revolutionerande kund upplevelser. Mer information finns i [Azure Digital-dubbla](../digital-twins/index.yml).

### <a name="azure-digital-twins-instance"></a>Azure Digitals dubbla instanser

En enda instans av Azure Digitals dubbla tjänster i en kunds prenumeration. Även om [Azures digitala dubbla](#azure-digital-twins) är en helhet, är din Azure Digital- **instansen** av Azure Digitals dubblare-resurs.

### <a name="azure-iot-device-sdks"></a>SDK: er för Azure IoT-enheter

Det finns tillgängliga _enhets-SDK_ : er för flera språk som gör att du kan skapa [enhets program](#device-app) som interagerar med en IoT-hubb. IoT Hub självstudier visar hur du använder dessa enhets-SDK: er. Du hittar käll koden och ytterligare information om enhets-SDK: erna i den här GitHub- [lagringsplatsen](https://github.com/Azure/azure-iot-sdks).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

[Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) används för att Visa telemetri som enheten skickar, arbeta med enhets egenskaper och anropa kommandon. Du kan också använda Utforskaren för att interagera med och testa dina enheter och för att hantera [IoT plug and Play-enheter](#iot-plug-and-play-device).

### <a name="azure-iot-service-sdks"></a>SDK: er för Azure IoT-tjänster

Det finns _tjänst-SDK_ : er för flera språk som gör att du kan skapa [backend-appar](#back-end-app) som interagerar med en IoT-hubb. IoT Hub självstudier visar hur du använder dessa tjänst-SDK: er. Du hittar käll koden och ytterligare information om tjänst-SDK: er i den här GitHub- [lagringsplatsen](https://github.com/Azure/azure-iot-sdks).

### <a name="azure-iot-tools"></a>Azure IoT-verktyg

[Azure IoT-verktygen](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) är ett plattforms oberoende Visual Studio Code-tillägg med öppen källkod som hjälper dig att hantera Azure-IoT Hub och enheter i vs Code. Med Azure IoT-verktyg kan IoT-utvecklare utveckla IoT-projekt i VS Code enkelt.

## <a name="b"></a>B

### <a name="back-end-app"></a>Backend-app

I samband med [IoT Hub](#iot-hub)är en backend-app en app som ansluter till en av de service riktade slut punkter som finns på en IoT-hubb. Till exempel kan en backend-app hämta meddelanden [från enheten till molnet](#device-to-cloud) eller hantera [identitets registret](#identity-registry). Normalt körs en backend-app i molnet, men i många av de självstudierna är de konsol program som körs på den lokala utvecklings datorn.

### <a name="built-in-endpoints"></a>Inbyggda slut punkter

En typ av [slut punkt](#endpoint) som är inbyggd i IoT Hub. Varje IoT-hubb innehåller en inbyggd [slut punkt](../iot-hub/iot-hub-devguide-endpoints.md) som är kompatibel med Event Hub. Du kan använda valfri mekanism som fungerar med Event Hubs för att läsa meddelanden från enheten till molnet från den här slut punkten.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Cloud Gateway

En molnbaserad Gateway möjliggör anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub). En moln-Gateway finns i molnet i motsats till en [fält-Gateway](#field-gateway) som körs lokalt på dina enheter. Ett vanligt användnings fall för en moln-Gateway är att implementera protokoll översättning för dina enheter.

### <a name="cloud-to-device"></a>Moln till enhet

Avser meddelanden som skickas från en IoT-hubb till en ansluten enhet. Dessa meddelanden är ofta kommandon som instruerar enheten att vidta en åtgärd. Mer information finns i [skicka och ta emot meddelanden med IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="commands"></a>Kommandon

I IoT Plug and Play representerar kommandon som definierats i ett [gränssnitt](#interface) metoder som kan köras på den [digitala, dubbla](#digital-twin). Till exempel ett kommando för att starta om en enhet.

### <a name="component"></a>Komponent

I IoT Plug and Play och Azure Digitals dubbla delar kan du med komponenter bygga ett modell [gränssnitt](#interface) som en sammansättning av andra gränssnitt. En [enhets modell](#device-model) kan kombinera flera gränssnitt som komponenter. En modell kan till exempel innehålla en växel komponent och en termostat-komponent. Flera komponenter i en modell kan också använda samma gränssnitts typ. En modell kan till exempel innehålla två termostat-komponenter.

### <a name="configuration"></a>Konfiguration

I samband med [Automatisk enhets konfiguration](../iot-hub/iot-hub-automatic-device-management.md)definierar en konfiguration i IoT Hub den önskade konfigurationen för en uppsättning enheter är tillsammans med en uppsättning mått för att rapportera status och förlopp.

### <a name="connection-string"></a>Anslutningssträng

Du använder anslutnings strängar i din app-kod för att kapsla in den information som krävs för att ansluta till en slut punkt. En anslutnings sträng innehåller vanligt vis adressen till slut punkten och säkerhets informationen, men anslutnings sträng formaten varierar mellan olika tjänster. Det finns två typer av anslutnings strängar kopplade till den IoT Hub tjänsten:

- *Anslutnings strängar* för enheter gör det möjligt för enheter att ansluta till enhets riktade slut punkter på en IoT-hubb.

- *IoT Hub anslutnings strängar* aktivera backend-appar för att ansluta till tjänstens slut punkter på en IoT-hubb.

### <a name="custom-endpoints"></a>Anpassade slut punkter

Du kan skapa anpassade [slut punkter](../iot-hub/iot-hub-devguide-endpoints.md) i en IoT-hubb för att leverera meddelanden som skickas av en [regel för routning](#routing-rules). Anpassade slut punkter ansluter direkt till en Event Hub, en Service Bus kö eller ett Service Bus ämne.

### <a name="custom-gateway"></a>Anpassad Gateway

En Gateway möjliggör anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub). Du kan använda Azure IoT Edge för att bygga anpassade gatewayer som implementerar anpassad logik för att hantera meddelanden, anpassade protokoll konverteringar och annan bearbetning i kanten.

## <a name="d"></a>D

### <a name="data-point-message"></a>Meddelande om data punkt

Ett data punkts meddelande är ett [enhets-till-moln-](#device-to-cloud) meddelande som innehåller [telemetridata](#telemetry) som vridnings hastighet eller temperatur.

### <a name="default-component"></a>Standard komponent

I IoT Plug and Play har alla [enhets modeller](#device-model) en standard komponent. En enkel enhets modell har bara en standard komponent – sådan en modell kallas även ingen komponent enhet. En mer komplex modell har flera komponenter som är kapslade under standard komponenten.

### <a name="deployment-manifest"></a>Distributionsmanifest

I [IoT Edge](#iot-edge)är distributions manifestet ett JSON-dokument som innehåller den information som ska kopieras i en eller flera IoT Edge enheters modul dubbla (s) för att distribuera en uppsättning moduler, vägar och önskade egenskaper för associerade moduler.

### <a name="desired-configuration"></a>Önskad konfiguration

I kontexten för en [enhet](../iot-hub/iot-hub-devguide-device-twins.md), refererar önskad konfiguration till en fullständig uppsättning egenskaper och metadata i enheten, som ska synkroniseras med enheten.

### <a name="desired-properties"></a>Önskade egenskaper

I kontexten för en [enhet](../iot-hub/iot-hub-devguide-device-twins.md), är önskade egenskaper ett underavsnitt av den enhet som används med [rapporterade egenskaper](#reported-properties) för att synkronisera enhetens konfiguration eller villkor. Önskade egenskaper kan bara ställas in av en [backend-app](#back-end-app) och observeras av [enhets appen](#device-app).

### <a name="device"></a>Enhet

I IoT-kontexten är en enhet vanligt vis en småskalig, fristående data behandlings enhet som kan samla in data eller styra andra enheter. En enhet kan till exempel vara en miljö övervaknings enhet eller en kontrollant för vatten-och ventilations system i en växthus. [Enhets katalogen](https://catalog.azureiotsolutions.com/) innehåller en lista över maskin varu enheter som är certifierade för att fungera med [IoT Hub](#iot-hub).

### <a name="device-app"></a>Enhetsapp

En enhets App körs på [enheten](#device) och hanterar kommunikationen med [IoT Hub](#iot-hub). Normalt använder du en av [Azure IoT-enhetens SDK](#azure-iot-device-sdks) : er när du implementerar en enhets app. I många av IoT-självstudierna använder du en [simulerad enhet](#simulated-device) för bekvämlighet.

### <a name="device-builder"></a>Enhets verktyg

En Device Builder använder en [enhets modell](#device-model) och [gränssnitt](#interface) när du implementerar kod som ska köras på en [IoT plug and Play-enhet](#iot-plug-and-play-device). Enhets byggare använder vanligt vis en av [Azure IoT-enhetens SDK](#azure-iot-device-sdks) : er för att implementera enhets klienten.

### <a name="device-certification"></a>Enhetscertifiering

IoT Plug and Play enhets certifierings programmet verifierar att en enhet uppfyller IoT-Plug and Play certifierings krav. Du kan lägga till en certifierad enhet i [katalogen för offentlig certifierad för Azure IoT-enheter](https://aka.ms/devicecatalog).

### <a name="device-condition"></a>Enhets villkor

Avser enhets tillståndsinformation, till exempel anslutnings metoden som används för närvarande, som rapporteras av en [app i enheten](#device-app). [Enhets appar](#device-app) kan också rapportera sina funktioner. Du kan fråga efter villkor och kapacitets information med hjälp av enhets dubbla.

### <a name="device-data"></a>Enhetsdata

Enhets data refererar till de enhets data som lagras i IoT Hub [identitets registret](#identity-registry). Det går att importera och exportera dessa data.

### <a name="device-identity"></a>Enhetsidentitet

Enhets identiteten (eller enhets-ID) är den unika identifierare som tilldelats varje enhet som är registrerad i IoT Hub [identitets registret](#identity-registry).

### <a name="device-management"></a>Enhetshantering

Enhets hantering omfattar hela livs cykeln som är kopplad till hanteringen av enheterna i din IoT-lösning, inklusive planering, etablering, konfiguration, övervakning och pensionering.

### <a name="device-management-patterns"></a>Enhetshanteringsmönster

[IoT Hub](#iot-hub) möjliggör vanliga enhets hanterings mönster, inklusive omstart, återställning av fabriks uppdateringar och uppdatering av inbyggd program vara på dina enheter.

### <a name="device-model"></a>Enhetsmodell

En enhets modell är en typ av [modell](#model) som använder det [digitala dubbla definitions språket](#digital-twins-definition-language-dtdl) för att beskriva funktionerna i en IoT plug and Play-enhet. En enkel enhets modell använder ett enda gränssnitt för att beskriva enhetens funktioner. En mer komplex enhets modell innehåller flera komponenter, som var och en beskriver en uppsättning funktioner. Läs mer i [IoT plug and Play-komponenter i modeller](../iot-pnp/concepts-components.md).

### <a name="device-modeling"></a>Enhets modellering

Ett [Device Builder](#device-builder) -eller [modul Builder](#module-builder)använder det [digitala dubbla definitions språket](#digital-twins-definition-language-dtdl) för att modellera funktionerna i en [IoT plug and Play-enhet](#iot-plug-and-play-device). Ett [Solution Builder](#solution-builder) kan konfigurera en IoT-lösning från modellen.

### <a name="device-provisioning"></a>Enhets etablering

Enhets etablering är en process för att lägga till den ursprungliga [enhets informationen](#device-data) i butikerna i din lösning. Om du vill aktivera en ny enhet för att ansluta till navet måste du lägga till ett enhets-ID och nycklar i IoT Hub [identitets registret](#identity-registry). Som en del av etablerings processen kan du behöva initiera enhetsspecifika data i andra lösnings lager.

### <a name="device-provisioning-service"></a>Device Provisioning Service

IoT Hub Device Provisioning Service (DPS) är en hjälp tjänst för [IoT Hub](#iot-hub) som du använder för att konfigurera enhets etablering med noll touch till en angiven IoT Hub. Med DPS kan du etablera miljon tals enheter på ett säkert och skalbart sätt.

### <a name="device-rest-api"></a>Enhets REST API

Du kan använda [enhets REST API](/rest/api/iothub/device) från en enhet för att skicka meddelanden från enheten till molnet till en IoT-hubb och ta emot meddelanden från [molnet till enheten](#cloud-to-device) från en IoT-hubb. Normalt bör du använda en av de högre [enhets-SDK: erna](#azure-iot-device-sdks) som visas i självstudierna för IoT Hub.

### <a name="device-twin"></a>Enhetstvilling

En enhet med dubbla är JSON-dokument som lagrar information om enhets tillstånd, till exempel metadata, konfigurationer och villkor. IoT Hub sparar en enhet för varje enhet som du etablerar i IoT Hub. Med enhets dubbla kan du synkronisera enhets villkor och konfigurationer mellan enheten och Server delen för lösningen. Du kan köra en fråga på enheten för att hitta vissa enheter och för att köra långvariga åtgärder.

### <a name="device-to-cloud"></a>Enhet till moln

Avser meddelanden som skickas från en ansluten enhet till [IoT Hub](#iot-hub). Dessa meddelanden kan vara [data punkts-](#data-point-message) eller [interaktiva](#interactive-message) meddelanden. Mer information finns i [skicka och ta emot meddelanden med IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="digital-twin"></a>Digital, dubbel

En digital, dubbla är en samling digitala data som representerar ett fysiskt objekt. Ändringar i det fysiska objektet visas i den digitala dubbla. I vissa fall kan du använda digitala dubbla för att ändra det fysiska objektet. [Azure Digitals dubbla tjänster](../digital-twins/index.yml) använder [modeller](#model) som uttrycks i [DTDL (Digitals definitions språk)](#digital-twins-definition-language-dtdl) för att representera digitala enheter av fysiska enheter eller abstrakta affärs koncept på högre nivå, vilket möjliggör en mängd olika molnbaserade digitala dubbla lösningar. En [IoT plug and Play](../iot-pnp/index.yml) -enhet har en digital, som beskrivs av en DTDL [enhets modell](#device-model).

### <a name="digital-twin-change-events"></a>Ändringshändelser för digitala tvillingar

När en [iot plug and Play-enhet](#iot-plug-and-play-device) är ansluten till en IoT-hubb kan hubben använda sin routningsfunktioner för att skicka meddelanden om digitala dubbla ändringar. När ett [egenskaps](#properties) värde ändras på en enhet kan IoT Hub till exempel skicka ett meddelande till en slut punkt, till exempel en Event Hub.

### <a name="digital-twin-route"></a>Digital, dubbel väg

En väg som kon figurer ATS i en IoT-hubb för att leverera [digitala dubbla ändrings händelser](#digital-twin-change-events) till en slut punkt, till exempel en Event Hub.

### <a name="digital-twins-definition-language-dtdl"></a>Digital Twins Definition Language (DTDL)

Ett JSON-LD-språk för att beskriva [modeller](#model) och [gränssnitt](#interface) för [IoT plug and Play-enheter](#iot-plug-and-play-device) och [Azure Digitals dubbla](../digital-twins/index.yml) entiteter. Använd det [digitala flätade definitions språket version 2](https://github.com/Azure/opendigitaltwins-dtdl) för att beskriva [digitala dubbla](#digital-twin) funktioner och aktivera IoT-plattform och IoT-lösningar för att använda entiteternas semantik. Digital enformat definitions språk är ofta förkortat som DTDL.

### <a name="direct-method"></a>Direkt metod

En [direkt metod](../iot-hub/iot-hub-devguide-direct-methods.md) är ett sätt för dig att utlösa en metod som ska köras på en enhet genom att anropa ett API i IoT Hub.

### <a name="downstream-services"></a>Underordnade tjänster

En relativ term som beskriver tjänster som tar emot data från den aktuella kontexten. Om du till exempel tänker på Azures digitala dubbla, skulle [Time Series Insights](../time-series-insights/index.yml) anses vara en underordnad tjänst om du konfigurerar dina data till att flöda från Azure Digital-strömmar till Time Series Insights.

## <a name="e"></a>E

### <a name="endpoint"></a>Slutpunkt

En namngiven representation av en data Dirigerings tjänst som kan ta emot data från andra tjänster.

En IoT-hubb visar flera [slut punkter](../iot-hub/iot-hub-devguide-endpoints.md) som gör det möjligt för dina appar att ansluta till IoT Hub. Det finns enhets slut punkter som gör det möjligt för enheter att utföra åtgärder som att skicka meddelanden [från enheten till molnet](#device-to-cloud) och ta emot meddelanden från [molnet till enheten](#cloud-to-device) . Det finns slut punkter för hantering av tjänster som gör att [backend-appar](#back-end-app) kan utföra åtgärder som hantering av [enhetens identitet](#device-identity) och enhetens dubbla hantering. Det finns tjänstbaserade [inbyggda slut punkter](#built-in-endpoints) för att läsa meddelanden från enheten till molnet. Du kan skapa [anpassade slut punkter](#custom-endpoints) för att ta emot meddelanden från enheten till molnet som skickas av en [regel för routning](#routing-rules).

### <a name="enrollment"></a>Registrering

I [enhets etablerings tjänsten](#device-provisioning-service)är en registrering posten för enskilda enheter eller grupper av enheter som kan registreras med en [länkad IoT-hubb](#linked-iot-hub) genom autoetablering.

### <a name="enrollment-group"></a>Registrerings grupp

I [enhets etablerings tjänsten](#device-provisioning-service)identifierar en registrerings grupp en grupp av enheter som delar en mekanism för X. 509 eller symmetrisk nyckel [attestering](#attestation-mechanism).

### <a name="event-handlers"></a>Händelsehanterare

Detta kan referera till alla processer som utlöses av en händelses ankomst och utför vissa bearbetnings åtgärder. Ett sätt att skapa händelse hanterare är genom att lägga till händelse bearbetnings kod i en Azure-funktion och skicka data genom att använda [slut punkter](#endpoint) och [händelse dirigering](#event-routing).

### <a name="event-hub-compatible-endpoint"></a>Event Hub – kompatibel slut punkt

Om du vill läsa meddelanden från [enheten till molnet](#device-to-cloud) som skickas till din IoT-hubb kan du ansluta till en slut punkt på hubben och använda valfri Event Hub-kompatibel metod för att läsa dessa meddelanden. Event Hub-kompatibla metoder omfattar användning av [Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) : er och [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

### <a name="event-routing"></a>Händelsedirigering

Processen för att skicka händelser och deras data från en enhet eller tjänst till [slut punkten](#endpoint) för en annan. 

I IoT Hub kan du definiera [regler för routning](#routing-rules) som beskriver hur meddelanden ska skickas. I Azure Digitals dubbla, är händelse vägar entiteter som skapas för det här ändamålet. Azure Digitals sammanställda händelse vägar kan innehålla filter för att begränsa vilka typer av händelser som skickas till varje slut punkt.

## <a name="f"></a>F

### <a name="field-gateway"></a>Fält-Gateway

Med en fält-Gateway kan du ansluta till enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub) och distribueras vanligt vis lokalt med dina enheter. Mer information finns i [Vad är Azure IoT Hub?](../iot-hub/about-iot-hub.md).

## <a name="g"></a>G

### <a name="gateway"></a>Gateway

En Gateway möjliggör anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub). Se även [Field Gateway](#field-gateway), [Cloud Gateway](#cloud-gateway)och [anpassad Gateway](#custom-gateway).

### <a name="gateway-device"></a>Gateway-enhet

En enhet är ett exempel på en [fält-Gateway](#field-gateway). En gateway-enhet kan vara en standard IoT- [enhet](#device) eller en [IoT Edge enhet](#iot-edge-device).

En gateway-enhet möjliggör anslutning för underordnade enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub).

## <a name="h"></a>H

### <a name="hardware-security-module"></a>Modul för maskin varu säkerhet

En modul för maskin varu säkerhet (HSM) används för säker, maskinvarubaserad lagring av enhets hemligheter. Det är den säkraste formen av Secret Storage för en enhet. Både X. 509-certifikat och symmetriska nycklar kan lagras i en HSM. I [enhets etablerings tjänsten](#device-provisioning-service)kan en [mekanism för attestering](#attestation-mechanism) använda en HSM.

## <a name="i"></a>I

### <a name="id-scope"></a>ID-omfång

ID-omfånget är unikt som tilldelas en [tjänst för enhets etablerings tjänsten (DPS)](#device-provisioning-service) när den skapas.

IoT Central program använder DPS-instanser och gör ID-omfånget tillgängligt via IoT Central gränssnittet.

### <a name="identity-registry"></a>Identitets register

[Identitets registret](../iot-hub/iot-hub-devguide-identity-registry.md) är den inbyggda komponenten i en IoT-hubb som lagrar information om de enskilda enheter som tillåts att ansluta till en IoT-hubb.

### <a name="individual-enrollment"></a>Individuell registrering

I [enhets etablerings tjänsten](#device-provisioning-service)identifierar en enskild enhet som använder ett löv certifikat för X. 509 eller symmetrisk nyckel som en [mekanism för attestering](#attestation-mechanism).

### <a name="interactive-message"></a>Interaktivt meddelande

Ett interaktivt meddelande är ett [moln-till-enhet-](#cloud-to-device) meddelande som utlöser en omedelbar åtgärd i Server delen av lösningen. En enhet kan till exempel skicka ett larm om ett haveri som ska loggas in automatiskt på ett CRM-system.

### <a name="interface"></a>Gränssnitt

I IoT Plug and Play beskriver ett gränssnitt relaterade funktioner som implementeras av en [IoT plug and Play-enhet](#iot-plug-and-play-device) eller [digital](#digital-twin). Du kan återanvända gränssnitt mellan olika [enhets modeller](#device-model). När ett gränssnitt används i en enhets modell definierar den en [komponent](#component) i enheten. En enkel enhet innehåller bara ett standard gränssnitt.

I Azure Digitals, kan *gränssnittet* användas för att referera till det översta kod objektet i en [DTDL](#digital-twins-definition-language-dtdl) -modell definition.

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge möjliggör moln driven distribution av Azure-tjänster och lösnings kod till lokala enheter. [IoT Edge enheter](#iot-edge-device) kan samla in data från andra enheter för att utföra data behandling och analys innan data skickas till molnet. Läs mer i [Azure IoT Edge](../iot-edge/index.yml).

### <a name="iot-edge-agent"></a>IoT Edge agent

Den del av IoT Edge runtime som ansvarar för att distribuera och övervaka moduler.

### <a name="iot-edge-device"></a>IoT Edge-enhet

En IoT Edge enhet använder behållare IoT Edge [moduler](#modules) för att köra Azure-tjänster, tjänster från tredje part eller din egen kod. [IoT Edge runtime](#iot-edge-runtime) hanterar modulerna på den IoT Edge enheten. Du kan fjärrövervaka och hantera en IoT Edge-enhet från molnet.

### <a name="iot-edge-hub"></a>IoT Edge hubb

Den del av IoT Edge runtime som är ansvarig för att modulerna ska kunna kommunicera, strömmas (mot IoT Hub) och underordnad (från IoT Hub) kommunikation.

### <a name="iot-edge-runtime"></a>IoT Edge-körning

IoT Edge runtime innehåller allt som Microsoft distribuerar för att installeras på en IoT Edge enhet. Den innehåller Edge-agent, Edge Hub och IoT Edge Security daemon.

### <a name="iot-extension-for-azure-cli"></a>IoT-tillägg för Azure CLI

[IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) är ett kommando rads verktyg för flera plattformar. Med verktyget kan du hantera dina enheter i [identitets registret](#identity-registry), skicka och ta emot meddelanden och filer från dina enheter och övervaka dina IoT Hub-åtgärder.

### <a name="iot-hub"></a>IoT Hub

IoT Hub är en helt hanterad Azure-tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljon tals enheter och en lösnings Server del. Mer information finns i [Vad är Azure IoT Hub?](../iot-hub/about-iot-hub.md). Med din Azure-prenumeration kan du skapa IoT-hubbar för att hantera dina IoT Messaging-arbetsbelastningar.

### <a name="iot-hub-metrics"></a>IoT Hub mått

IoT Hub mått ger dig information om status för IoT-hubbarna i din Azure-prenumeration. Med IoT Hub mått kan du utvärdera den övergripande hälsan för tjänsten och de enheter som är anslutna till den. IoT Hub mått kan hjälpa dig att se vad som händer med din IoT-hubb och undersöka rotor Saks problem utan att behöva kontakta Azure-supporten. Mer information finns i [övervaka IoT Hub](../iot-hub/monitor-iot-hub.md).

### <a name="iot-hub-query-language"></a>IoT Hub frågespråk

Det [IoT Hub frågespråket](../iot-hub/iot-hub-devguide-query-language.md) är ett SQL-liknande språk som gör det möjligt att fråga ditt [jobb](#job), digitala enheter och enheten.

### <a name="iot-hub-resource-rest-api"></a>IoT Hub resurs REST API

Du kan använda [IoT Hub resurs REST API](/rest/api/iothub/iothubresource) för att hantera IoT-hubbar i din Azure-prenumeration som utför åtgärder som att skapa, uppdatera och ta bort hubbar.

### <a name="iot-plug-and-play-bridge"></a>IoT Plug and Play-brygga

IoT Plug and Play Bridge är ett program med öppen källkod som gör att befintliga sensorer och kring utrustning som är anslutna till Windows-eller Linux-gatewayer kan anslutas som [IoT plug and Play-enheter](#iot-plug-and-play-device).

### <a name="iot-plug-and-play-conventions"></a>Konventioner för IoT Plug and Play

IoT Plug and Play- [enheter](#iot-plug-and-play-device) förväntas följa en uppsättning konventioner när de utbyter data med en lösning.

### <a name="iot-plug-and-play-device"></a>IoT Plug and Play-enhet

En IoT Plug and Play-enhet är vanligt vis en småskalig, fristående data behandlings enhet som samlar in data eller styr andra enheter och som kör program vara eller inbyggd program vara som implementerar en [enhets modell](#device-model).  En IoT Plug and Play-enhet kan till exempel vara en miljö övervaknings enhet eller en kontrollant för ett bevattnings system med Smart-jordbruk. En IoT Plug and Play-enhet kan implementeras direkt eller som en IoT Edge modul. Du kan skriva en molnbaserad IoT-lösning i molnet för att kommando, kontrol lera och ta emot data från IoT Plug and Play-enheter.

### <a name="iot-solution-accelerators"></a>IoT-lösningsacceleratorer

Azure IoT Solution Accelerators-paket tillsammans med flera Azure-tjänster i lösningar. Med dessa lösningar kan du snabbt komma igång med implementeringar från slut punkt till slut punkt av vanliga IoT-scenarier. Mer information finns i [Vad är acceleratorer för Azure IoT-lösningar?](../iot-accelerators/about-iot-accelerators.md)

## <a name="j"></a>J

### <a name="job"></a>Jobb

Server delen av lösningen kan använda [jobb](../iot-hub/iot-hub-devguide-jobs.md) för att schemalägga och spåra aktiviteter på en uppsättning enheter som är registrerade i IoT Hub. Aktiviteterna omfattar att uppdatera enhetens dubbla [önskade egenskaper](#desired-properties), uppdatera enhetens dubbla [taggar](#tags)och anropa [direkta metoder](#direct-method). [IoT Hub](#iot-hub) använder också för att [Importera till och exportera](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) från [identitets registret](#identity-registry).

## <a name="l"></a>L

### <a name="leaf-device"></a>Löv enhet

I [IoT Edge](#iot-edge)är en löv enhet en enhet utan någon underordnad enhet.

### <a name="lifecycle-event"></a>Livs cykel händelse

I Azure Digitals, utlöses den här typen av händelse när ett data objekt, till exempel en digital, en relation eller en händelse hanterare, skapas eller tas bort från din Azure Digital-instansen.

### <a name="linked-iot-hub"></a>Länkad IoT Hub

[Enhets etablerings tjänsten (DPS)](#device-provisioning-service)kan etablera enheter till IoT-hubbar som har länkats till den. Genom att länka en IoT-hubb till en DPS-instans kan tjänsten registrera ett enhets-ID och ange den inledande konfigurationen på enheten.

## <a name="m"></a>M

### <a name="model"></a>Modell

En modell definierar en typ av entitet i din fysiska miljö, inklusive dess egenskaper, telemetrivärden, komponenter och ibland annan information. Modeller används för att skapa [digitala dubbla](#digital-twin) grupper som representerar fysiska objekt av den här typen. Modeller skrivs i det [digitala, dubbla definitions språket](#digital-twins-definition-language-dtdl).

I [Azure Digitals dubbla tjänster](../digital-twins/index.yml)kan modeller definiera enheter eller abstrakta affärs koncept på högre nivå. I [IoT plug and Play](../iot-pnp/index.yml)används [enhets modeller](#device-model) för att beskriva enheter specifikt.

### <a name="model-id"></a>Modell-ID

När en IoT Plug and Play-enhet ansluter till en IoT Hub skickas **modell-ID: t** för den [DTDL](#digital-twins-definition-language-dtdl) -modell som den implementerar. Detta ID gör att lösningen kan hitta enhets modellen.

### <a name="model-repository"></a>Modelldatabas

En modell databas lagrar [enhets modeller](#device-model) och [gränssnitt](#interface).

### <a name="model-repository-rest-api"></a>Modell databas REST API

Ett API för att hantera och interagera med modell databasen. Du kan till exempel använda API: et för att lägga till och söka efter [enhets modeller](#device-model).

### <a name="module-builder"></a>Modul Builder

En modul Builder använder en [enhets modell](#device-model) och [gränssnitt](#interface) när de implementerar kod som ska köras på en [IoT plug and Play-enhet](#iot-plug-and-play-device). Modul Builder implementerar koden som en modul eller en IoT Edge-modul som ska distribueras till IoT Edge runtime på en enhet.

### <a name="module-identity"></a>Modulens identitet

Modulens identitet är den unika identifierare som tilldelats varje modul som tillhör en enhet. Modulens identitet registreras också i [identitets registret](#identity-registry).

Modulen identifierar information de säkerhets referenser som modulen använder för att autentisera med [IoT Hub](#iot-hub) eller, om det finns en IoT Edge-modul till [IoT Edge Hub](#iot-edge-hub).

### <a name="module-image"></a>Modul avbildning

Docker-avbildningen som [IoT Edge körning](#iot-edge-runtime) använder för att instansiera modul instanser.

### <a name="module-twin"></a>Modul, delad

På samma sätt som enhets dubbla är en modul i ett JSON-dokument som lagrar information om modulens tillstånd, till exempel metadata, konfigurationer och villkor. IoT Hub behåller en modul som är sammanflätad för varje modul identitet som du etablerar under en enhets identitet i din IoT-hubb. I modulen är det möjligt att synkronisera modulens villkor och konfigurationer mellan modulen och Server delen för lösningen. Du kan köra en fråga om modulerna för att hitta vissa moduler och fråga efter statusen för långvariga åtgärder.

### <a name="modules"></a>Moduler

På enhets sidan kan du med IoT Hub enhets-SDK: er skapa [moduler](../iot-hub/iot-hub-devguide-module-twins.md) där var och en öppnar en oberoende anslutning till IoT Hub. Med den här funktionen kan du använda separata namn rymder för olika komponenter på enheten.

Modul identitet och modul dubbla ger samma funktioner som [enhets identitet](#device-identity) och [enhet](#device-twin) , men med en bättre precision. Med den här bättre precisionen kan enheter, till exempel operativ systembaserade enheter eller inbyggda enheter hantera flera komponenter, för att isolera konfiguration och villkor för var och en av dessa komponenter.

I [IoT Edge](#iot-edge)är en modul en Docker-behållare som du kan distribuera till IoT Edge enheter. Den utför en speciell uppgift, t. ex. inmatning av ett meddelande från en enhet, omvandling av ett meddelande eller att skicka ett meddelande till en IoT-hubb. Den kommunicerar med andra moduler och skickar data till [IoT Edge runtime](#iot-edge-runtime).

### <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) är ett av de meddelande protokoll som [IoT Hub](#iot-hub) stöder för att kommunicera med enheter. Mer information om de meddelande protokoll som IoT Hub stöder finns i [skicka och ta emot meddelanden med IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

## <a name="o"></a>O

### <a name="operations-monitoring"></a>Övervakning av åtgärder

Med IoT Hub [Åtgärds övervakning](../iot-hub/iot-hub-operations-monitoring.md) kan du övervaka statusen för åtgärder i IoT Hub i real tid. [IoT Hub](#iot-hub) spårar händelser i flera kategorier av åtgärder. Du kan välja att skicka händelser från en eller flera kategorier till en IoT Hub-slutpunkt för bearbetning. Du kan övervaka data för fel eller ställa in mer komplex bearbetning baserat på data mönster.

## <a name="p"></a>P

### <a name="physical-device"></a>Fysisk enhet

En fysisk enhet är en riktig enhet, till exempel en Raspberry Pi som ansluter till en IoT-hubb. För enkelhetens skull använder många av de IoT Hub självstudierna [simulerade enheter](#simulated-device) för att kunna köra exempel på din lokala dator.

### <a name="primary-and-secondary-keys"></a>Primära och sekundära nycklar

När du ansluter till en enhets riktad eller riktad slut punkt på en IoT-hubb innehåller [anslutnings strängen](#connection-string) nyckeln för att ge dig åtkomst. När du lägger till en enhet i [identitets registret](#identity-registry) eller lägger till en [princip för delad åtkomst](#shared-access-policy) i din hubb, genererar tjänsten en primär och sekundär nyckel. Med två nycklar kan du återställa från en nyckel till en annan när du uppdaterar en nyckel utan att förlora åtkomsten till IoT Hub.

### <a name="properties"></a>Egenskaper

Egenskaper är data fält som definierats i ett [gränssnitt](#interface) som representerar ett beständigt tillstånd för en [digital, dubbel](#digital-twin). Du kan deklarera egenskaper som skrivskyddade eller skrivbara. Skrivskyddade egenskaper, till exempel serie nummer, anges med kod som körs på [IoT plug and Play själva enheten](#iot-plug-and-play-device) . Skrivbara egenskaper, till exempel ett larm tröskelvärde, anges vanligt vis från den molnbaserade IoT-lösningen.

### <a name="property-change-event"></a>Egenskaps ändrings händelse

En händelse som resulterar i en egenskaps ändring i en [digital, dubbel](#digital-twin).

### <a name="protocol-gateway"></a>Protokollgateway

En protokoll-Gateway distribueras vanligt vis i molnet och tillhandahåller protokoll översättnings tjänster för enheter som ansluter till [IoT Hub](#iot-hub). Mer information finns i [Vad är Azure IoT Hub?](../iot-hub/about-iot-hub.md).

## <a name="r"></a>R

### <a name="registration"></a>Registrering

En registrering är en enhets post i IoT Hub [identitets registret](#identity-registry). Du kan registrera enheten direkt eller använda [enhets etablerings tjänsten](#device-provisioning-service) för att automatisera enhets registreringen.

### <a name="registration-id"></a>Registrerings-ID

Registrerings-ID: t används för att unikt identifiera en enhets [registrering](#registration) med [enhets etablerings tjänsten](#device-provisioning-service). Registrerings-ID: t kan vara samma värde som [enhets identiteten](#device-identity).

### <a name="relationship"></a>Relation

I [Azure Digitals dubbla](../digital-twins/index.yml) tjänster används relationer för att ansluta [digitala](#digital-twin) delar till kunskaps diagram som digitalt representerar hela den fysiska miljön. De typer av relationer som dina delar kan ha definierats som en del av modell definitionerna för dubbla [modeller](#model) – [DTDL](#digital-twins-definition-language-dtdl) -modellen för en viss typ av dubbla innehåller information om vilka relationer den kan ha till andra.

### <a name="reported-configuration"></a>Rapporterad konfiguration

I samband med en [enhet](../iot-hub/iot-hub-devguide-device-twins.md), refererar konfigurationen till en fullständig uppsättning egenskaper och metadata i den enhet som ska rapporteras till lösningens Server del.

### <a name="reported-properties"></a>Rapporterade egenskaper

I kontexten för en [enhet](../iot-hub/iot-hub-devguide-device-twins.md), är rapporterade egenskaper ett underavsnitt av enheten, som används med [önskade egenskaper](#desired-properties) för att synkronisera enhetens konfiguration eller villkor. Rapporterade egenskaper kan bara ställas in av [enhets appen](#device-app) och kan läsas och frågas av en [backend-app](#back-end-app).

### <a name="retry-policy"></a>Återförsöksprincip

Du använder en princip för återförsök för att hantera [tillfälliga fel](/azure/architecture/best-practices/transient-faults) när du ansluter till en moln tjänst.

### <a name="routing-rules"></a>Dirigeringsregler

Du konfigurerar [regler för routning](../iot-hub/iot-hub-devguide-messages-read-custom.md) i IoT-hubben så att de dirigerar enhets-till-moln-meddelanden till en [inbyggd slut punkt](#built-in-endpoints) eller [anpassade slut punkter](#custom-endpoints) för bearbetning av Server delen av lösningen.

## <a name="s"></a>S

### <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN är ett protokoll som används av AMQP-protokollet för att överföra säkerhetstoken.

### <a name="service-operations-endpoint"></a>Slut punkt för tjänst åtgärder

En [slut punkt](#endpoint) för att hantera tjänst inställningar som används av en tjänst administratör. I [enhets etablerings tjänsten](#device-provisioning-service) använder du exempelvis tjänst slut punkten för att hantera registreringar.

### <a name="service-rest-api"></a>Tjänsten REST API

Du kan använda [tjänst REST API](/rest/api/iothub/service/configuration) från lösningens Server del för att hantera dina enheter. Med API: et kan du hämta och uppdatera [enhetens dubbla](#device-twin) egenskaper, anropa [direkta metoder](#direct-method)och schemalägga [jobb](#job). Normalt bör du använda en av de högre [service SDK: erna](#azure-iot-service-sdks) som visas i självstudierna för IoT Hub.

### <a name="shared-access-policy"></a>Princip för delad åtkomst

En princip för delad åtkomst definierar de behörigheter som beviljats för alla som har en giltig [primär eller sekundär nyckel](#primary-and-secondary-keys) som är associerad med principen. Du kan hantera principer och nycklar för delad åtkomst för hubben i portalen.

### <a name="shared-access-signature"></a>Signatur för delad åtkomst

Signaturer för delad åtkomst (SAS) är en autentiseringsmekanism baserad på SHA-256-säkra hash-värden eller URI: er. SAS-autentisering har två komponenter: en _delad åtkomst princip_ och en _signatur för delad åtkomst_ (kallas ofta för en token). En enhet använder SAS för att autentisera med en IoT-hubb. [Backend-appar](#back-end-app) använder också SAS för att autentisera med tjänstens slut punkter på en IoT-hubb. Normalt inkluderar du SAS-token i [anslutnings strängen](#connection-string) som en app använder för att upprätta en anslutning till en IoT-hubb.

### <a name="simulated-device"></a>Simulerad enhet

För enkelhetens skull använder många av de IoT Hub självstudierna simulerade enheter för att kunna köra exempel på din lokala dator. En [fysisk enhet](#physical-device) är däremot en riktig enhet, till exempel en Raspberry Pi som ansluter till en IoT-hubb.

### <a name="solution"></a>Lösning

En _lösning_ kan referera till en Visual Studio-lösning som innehåller ett eller flera projekt. En _lösning_ kan också referera till en IoT-lösning som innehåller element som enheter, [enhets appar](#device-app), en IoT-hubb, andra Azure-tjänster och [backend-appar](#back-end-app).

### <a name="solution-builder"></a>Solution Builder

En Solution Builder skapar lösningens Server del. En Solution Builder fungerar vanligt vis med Azure-resurser som IoT Hub och [modell databaser](#model-repository).

### <a name="system-properties"></a>Systemegenskaper

I en [enhets](../iot-hub/iot-hub-devguide-device-twins.md)kontext är system egenskaperna skrivskyddade och innehåller information om enhets användningen, till exempel senaste aktivitets tid och anslutnings tillstånd.

## <a name="t"></a>T

### <a name="tags"></a>Taggar

I en [enhets](../iot-hub/iot-hub-devguide-device-twins.md)kontext är Taggar enhetens metadata som lagras och hämtas av lösningens Server del i form av ett JSON-dokument. Taggarna är inte synliga för appar på en enhet.

### <a name="target-condition"></a>Mål villkor

I en IoT Edge-distribution väljer mål villkoret mål enheter för distributionen, till exempel **tagg. Environment = Prod**. Mål villkoret utvärderas kontinuerligt för att omfatta alla nya enheter som uppfyller kraven eller tar bort enheter som inte längre fungerar.

### <a name="telemetry"></a>Telemetri

Enheter samlar in telemetridata, till exempel vridnings hastighet eller temperatur, och använder data punkt meddelanden för att skicka telemetri till en IoT-hubb.

I IoT Plug and Play och Azure Digitals dubbla, representerar telemetridata i ett [gränssnitt](#interface) mått. Dessa mått är vanligt vis värden, till exempel sensor avläsningar som skickas av enheter, som [IoT plug and Play-enheter](#iot-plug-and-play-device), som en data ström.

Till skillnad från [Egenskaper](#properties)lagras inte telemetri på ett [digitalt](#digital-twin), Det är en data ström med tidsbegränsade data händelser som måste hanteras när de inträffar.

### <a name="telemetry-event"></a>Telemetri-händelse

En händelse som anger att telemetri-data har kommit.

### <a name="token-service"></a>Token service

Du kan använda en token-tjänst för att implementera en autentiseringsmekanism för dina enheter. Den använder en IoT Hub [princip för delad åtkomst](#shared-access-policy) med **DeviceConnect** -behörigheter för att skapa *enhets omfång* . Dessa token gör det möjligt för en enhet att ansluta till IoT Hub. En enhet använder en anpassad autentiseringsmekanism för att autentisera med token-tjänsten. Om enheten autentiseras, utfärdar token-tjänsten en SAS-token för enheten som ska användas för att få åtkomst till din IoT-hubb.

### <a name="twin-graph-or-digital-twin-graph"></a>Två diagram (eller elektroniskt flätat diagram)

I [Azure Digitals dubbla](../digital-twins/index.yml) tjänster kan du ansluta [digitala](#digital-twin) fogar med [relationer](#relationship) för att skapa kunskaps diagram som digitalt representerar hela den fysiska miljön. En enda [digital Azure Digital-instans](#azure-digital-twins-instance) kan vara värd för många frånkopplade diagram eller ett enda sammankopplat diagram.

### <a name="twin-queries"></a>Dubbla frågor

[Enhets-och modulernas dubbla frågor](../iot-hub/iot-hub-devguide-query-language.md) använder SQL-like IoT Hub frågespråk för att hämta information från enheten med dubbla eller flera moduler. Du kan använda samma språk för IoT Hub frågor för att hämta information om ett [jobb](#job) som körs i IoT Hub.

### <a name="twin-synchronization"></a>Dubbel synkronisering

Den dubbla synkroniseringen använder de [önskade egenskaperna](#desired-properties) i enheten, med dubbla eller moduler, så att du kan konfigurera enheter eller moduler och hämta [rapporterade egenskaper](#reported-properties) från dem till Store i den dubbla.

## <a name="u"></a>U

### <a name="upstream-services"></a>Överordnade tjänster

En relativ term som beskriver tjänster som matar in data i den aktuella kontexten. Om du till exempel tänker på Azure Digitals dubbla är IoT Hub betraktas som en uppströms tjänst eftersom data flödar från IoT Hub till digitala Digital-objekt i Azure.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>X. 509 klient certifikat

En enhet kan använda ett X. 509-certifikat för att autentisera med [IoT Hub](#iot-hub). Att använda ett X. 509-certifikat är ett alternativ till att använda en [SAS-token](#shared-access-signature).
