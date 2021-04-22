---
title: Översikt över SDK-alternativ för Azure IoT-enheter
description: Lär dig vilket SDK för Azure IoT-enheter som ska användas baserat på din utvecklingsroll och dina uppgifter.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: c35a9045bf809c03630fbb7c57f9d31e7b143422
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876465"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Översikt över AZURE IoT-enhets-SDK:er

AZURE IoT-enhets-SDK:er är en uppsättning klientbibliotek för enheter, utvecklarguider, exempel och dokumentation. Enhets-SDK:erna hjälper dig att programmatiskt ansluta enheter till Azure IoT-tjänster.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Diagram som visar olika Azure IoT-SDK:er":::

Som diagrammet visar finns det flera enhets-SDK:er som passar dina behov för enheten och programmeringsspråket. Vägledning om hur du väljer lämplig enhets-SDK finns i [Vilket SDK ska jag använda.](#which-sdk-should-i-use) Det finns även Azure IoT-tjänst-SDK:er tillgängliga för att ansluta ditt molnbaserade program till Azure IoT-tjänster på backend-enheten. Den här artikeln fokuserar på enhets-SDK:er, men du kan lära dig mer om Azure-tjänst-SDK:er [här.](#service-sdks)

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Varför ska jag använda AZURE IoT-enhets-SDK:er?

Om du vill ansluta enheter till Azure IoT kan du skapa ett anpassat anslutningslager eller använda AZURE IoT-enhets-SDK:er. Det finns flera fördelar med att använda AZURE IoT-enhets-SDK:er:

| Utvecklingskostnad &nbsp; &nbsp; &nbsp;&nbsp; | Anpassat anslutningslager | AZURE IoT-enhets-SDK:er |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Support | Behöver support och dokumentera det du skapar | Ha åtkomst till Microsoft Support (GitHub, Microsoft Q&A, Microsoft Docs, kundsupport) |
| Nya funktioner | Behöver lägga till nya Azure-funktioner i anpassat mellanprogram | Kan omedelbart dra nytta av nya funktioner som Microsoft ständigt lägger till i IoT-SDK:er |
| Investering | Investera hundratals timmar inbäddad utveckling för att utforma, skapa, testa och underhålla en anpassad version | Kan dra nytta av kostnadsfria verktyg med öppen källkod. Den enda kostnaden som är kopplad till DEDK:erna är inlärningskurvan. |

## <a name="which-sdk-should-i-use"></a>Vilket SDK ska jag använda?

Azure IoT-enhets-SDK:er är tillgängliga i populära programmeringsspråk som C, C#, Java, Node.js och Python. Det finns två huvudsakliga överväganden när du väljer sdk: enhetsfunktioner och teamets kunskaper om programmeringsspråket.

### <a name="device-capabilities"></a>Enhetsfunktioner

När du väljer ett SDK måste du ta hänsyn till gränserna för de enheter som du använder. En begränsad enhet är en enhet som har en enda mikrostyrenhet (MCU) och begränsat minne. Om du använder en begränsad enhet rekommenderar vi att du använder [Embedded C SDK.](#embedded-c-sdk) Detta SDK är utformat för att tillhandahålla minsta möjliga uppsättning funktioner för att ansluta till Azure IoT. Du kan också välja de komponenter (MQTT-klient, TLS- och socketbibliotek) som är mest optimerade för din inbäddade enhet. Om den begränsade enheten även körs Azure RTOS kan du använda mellanprogram Azure RTOS för att ansluta till Azure IoT. Den Azure RTOS mellanprogram omsluter Embedded C SDK med extra funktioner för att förenkla anslutning Azure RTOS enhet till molnet.

En ej tränad enhet är en enhet som har en mer robust processor, som kan köra ett operativsystem för att stödja en språkkörning som .NET eller Python. Om du använder en ej tränad enhet är det viktigt att du är bekant med språket.

### <a name="your-teams-familiarity-with-the-programming-language"></a>Ditt teams kunskaper om programmeringsspråket

Azure IoT-enhets-SDK:er implementeras på flera språk så att du kan välja det språk som du föredrar. Enhets-SDK:erna integreras också med andra välbekanta, språkspecifika verktyg. Genom att kunna arbeta med ett bekant utvecklingsspråk och verktyg kan ditt team optimera utvecklingscykeln för forskning, prototyper, produktutveckling och löpande underhåll.

När det är möjligt väljer du ett SDK som känns bekant för utvecklingsteamet. Alla Azure IoT SDK:er har öppen källkod och har flera tillgängliga exempel som ditt team kan utvärdera och testa innan de utför en specifik SDK.

## <a name="how-can-i-get-started"></a>Hur kommer jag igång?

Det ställe att börja är att utforska GitHub-lagringsplatsen för Azure-enhets-SDK:er. Du kan också prova en [snabbstart som](quickstart-send-telemetry-python.md) visar hur du snabbt använder en SDK för att skicka telemetri till Azure IoT.

Alternativen för att komma igång beror på vilken typ av enhet du har:
- För begränsade enheter använder du [Embedded C SDK.](#embedded-c-sdk) 
- För enheter som körs på Azure RTOS kan du utveckla med Azure RTOS [mellanprogram](#azure-rtos-middleware). 
- För enheter som inte är tränade kan du [välja ett SDK](#unconstrained-device-sdks) på val annat språk. 

### <a name="constrained-device-sdks"></a>Begränsade enhets-SDK:er
Dessa SDK:er är specialiserade på att köras på enheter med begränsade beräknings- eller minnesresurser. Mer information om vanliga enhetstyper finns i [Översikt över Azure IoT-enhetstyper.](concepts-iot-device-types.md)

#### <a name="embedded-c-sdk"></a>Inbäddad C SDK
* [GitHub-lagringsplats](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Exempel](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Referensdokumentation](https://azure.github.io/azure-sdk-for-c/)
* [Så här skapar du Embedded C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Storleksdiagram för begränsade enheter](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTOS mellanprogram

* [GitHub-lagringsplats](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Komma igång guider](https://github.com/azure-rtos/getting-started) och [fler exempel](https://github.com/azure-rtos/samples)
* [Referensdokumentation](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>ENHETS-SDK:er som inte är tränade
Dessa SDK:er kan köras på alla enheter som har stöd för en språkkörning med högre ordning. Detta inkluderar enheter som datorer, Raspberry Pis och smartphones. De skiljer sig främst åt efter språk, så du kan välja det bibliotek som passar ditt team och ditt scenario bäst.

#### <a name="c-device-sdk"></a>C-enhets-SDK
* [GitHub-lagringsplats](https://github.com/Azure/azure-iot-sdk-c)
* [Exempel](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Paket](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Referensdokumentation](/azure/iot-hub/iot-c-sdk-ref/)
* [Referensdokumentation för Edge-moduler](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Kompilera C-enhets-SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Porta C SDK till andra plattformar](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Utvecklardokumentation](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) för information om kors kompilering och komma igång på olika plattformar
* [Azure IoT Hub information om C SDK-resursförbrukning](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>C# Device SDK

* [GitHub-lagringsplats](https://github.com/Azure/azure-iot-sdk-csharp)
* [Exempel](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Paket](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Referensdokumentation](/dotnet/api/microsoft.azure.devices)
* [Referensdokumentation för Edge-moduler](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Java Device SDK

* [GitHub-lagringsplats](https://github.com/Azure/azure-iot-sdk-java)
* [Exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Paket](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Referensdokumentation](/java/api/com.microsoft.azure.sdk.iot.device)
* [Referensdokumentation för Edge-moduler](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Node.js Device SDK

* [GitHub-lagringsplats](https://github.com/Azure/azure-iot-sdk-node)
* [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Paket](https://www.npmjs.com/package/azure-iot-device)
* [Referensdokumentation](/javascript/api/azure-iot-device/)
* [Referensdokumentation för Edge-moduler](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>SDK för Python-enhet

* [GitHub-lagringsplats](https://github.com/Azure/azure-iot-sdk-python)
* [Exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Paket](https://pypi.org/project/azure-iot-device/)
* [Referensdokumentation](/python/api/azure-iot-device)
* [Referensdokumentation för Edge-moduler](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>Tjänst-SDK:er
Azure IoT erbjuder även tjänst-SDK:er som gör att du kan skapa program på lösningssidan för att hantera enheter, få insikter, visualisera data och mycket mer. Dessa SDK:er är specifika för varje Azure IoT-tjänst och är tillgängliga i C#, Java, JavaScript och Python för att förenkla din utvecklingsupplevelse. 

#### <a name="iot-hub"></a>IoT Hub

Med IoT Hub-tjänst-SDK:er kan du skapa program som enkelt interagerar med IoT Hub för att hantera enheter och säkerhet. Du kan använda dessa SDK:er för att skicka meddelanden från moln till enhet, anropa direktmetoder på dina enheter, uppdatera enhetsegenskaper med mera.

[**Läs mer om IoT Hub**](https://azure.microsoft.com/services/iot-hub/)  |  [ **Prova att kontrollera en enhet**](../iot-hub/quickstart-control-device-python.md)

**Referensdokumentation för C# IoT Hub Service SDK:** [GitHub Repository](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [Package Samples](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [](/dotnet/api/microsoft.azure.devices)

**Java IoT Hub Service SDK:** [GitHub Repository Package](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)  |  [](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)  |  [Samples Reference](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [Documentation](/java/api/com.microsoft.azure.sdk.iot.service)

**JavaScript IoT Hub Service SDK:** [GitHub Repository Package](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)  |  [](https://www.npmjs.com/package/azure-iothub)  |  [Samples Reference](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)  |  [Documentation](/javascript/api/azure-iothub/)

**Referensdokumentation för Python IoT Hub Service SDK:** [GitHub Repository](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)  |  [](https://pypi.python.org/pypi/azure-iot-hub/)  |  [Package Samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Twins är en Plattform som en tjänst (PaaS) som gör det möjligt att skapa kunskapsdiagram baserade på digitala modeller av hela miljöer. Dessa miljöer kan vara byggnader, fabriker, farmar, energinätverk, stadsnät, stadion med mera – till och med hela städer. Dessa digitala modeller kan användas för att få insikter som ger bättre produkter, optimerad drift, minskade kostnader och banbrytande kundupplevelser. Azure IoT erbjuder tjänst-SDK:er för att göra det enkelt att skapa program som använder kraften hos Azure Digital Twins.

[**Läs mer om Azure Digital Twins**](https://azure.microsoft.com/services/digital-twins/)  |  [ **Koda ett ADT-program**](../digital-twins/tutorial-code.md)

**Referensdokumentation för C# ADT Service SDK:** [GitHub-lagringsplatspaketexempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)  |  [](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [](/dotnet/api/overview/azure/digitaltwins/client)

**Java ADT Service SDK:** [Referensdokumentation för GitHub-lagringsplatspaketexempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)  |  [](/java/api/overview/azure/digitaltwins/client)

**Node.js ADT Service SDK:** [Referensdokumentation för GitHub-lagringsplatspaketexempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)  |  [](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [](/javascript/api/@azure/digital-twins-core/)

**Python ADT Service SDK:** [Referensdokumentation för Paketexempel för](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://pypi.org/project/azure-digitaltwins-core/)  |  [](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)  |  [GitHub-lagringsplats](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>Device Provisioning Service

Tjänsten IoT Hub Device Provisioning Service (DPS) är en hjälptjänst för IoT Hub som möjliggör zero-touch och just-in-time-etablering till rätt IoT-hubb utan mänsklig inblandning. DPS möjliggör etablering av miljontals enheter på ett säkert och skalbart sätt. Med DPS-tjänstens SDK:er kan du skapa program som på ett säkert sätt kan hantera dina enheter genom att skapa registreringsgrupper och göra massåtgärder.

[**Läs mer om Device Provisioning-tjänsten**](../iot-dps/index.yml)  |  [ **Prova att skapa en gruppregistrering för X.509-enheter**](../iot-dps/quick-enroll-device-x509-csharp.md)

**C# Device Provisioning Service SDK:** [Referensdokumentation för GitHub-lagringsplatspaketexempel](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [](/dotnet/api/microsoft.azure.devices.provisioning.service)

**SDK för Java Device Provisioning Service:** [Referensdokumentation för GitHub-lagringsplatspaketexempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

**Node.js device provisioning service SDK:** [Referensdokumentation för GitHub-lagringsplatspaket](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [](https://www.npmjs.com/package/azure-iot-provisioning-service)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Ansluta en enhet till IoT Central (Python)](quickstart-send-telemetry-python.md)
* [Snabbstart: Ansluta en enhet till IoT Hub (Python)](quickstart-send-telemetry-cli-python.md)
* [Kom igång med inbäddad utveckling](quickstart-device-development.md)
* Läs mer om fördelarna [med att utveckla med Azure IoT-SDK:er](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)