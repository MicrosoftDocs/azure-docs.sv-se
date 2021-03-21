---
title: Översikt över Azure IoT-enhetens SDK-alternativ
description: Lär dig vilken Azure IoT-enhets-SDK som ska användas baserat på din utvecklings roll och dina uppgifter.
author: elhorton
ms.author: elhorton
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: c9624e9a23d005185429c82199324ac570cbd63e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102607738"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Översikt över SDK: er för Azure IoT-enheter

SDK: er för Azure IoT-enheter är en uppsättning enhets klient bibliotek, utvecklings guider, exempel och dokumentation. Med enhets-SDK: er kan du program mässigt ansluta enheter till Azure IoT-tjänster.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Diagram över olika Azure IoT-SDK: er":::

När diagrammet visas finns det flera enhets-SDK: er tillgängliga för att passa dina behov av enheter och programmeringsspråk. Vägledning om hur du väljer lämplig enhets-SDK är tillgänglig i [vilken SDK ska jag använda](#which-sdk-should-i-use). Det finns också Azure IoT service SDK: er tillgängliga för att ansluta till ditt molnbaserade program med Azure IoT-tjänster på Server delen. Den här artikeln fokuserar på enhets-SDK: er, men du kan lära dig mer om SDK: er för Azure-tjänsten [här](#service-sdks).

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Varför ska jag använda SDK: er för Azure IoT-enheter?

För att ansluta enheter till Azure IoT kan du bygga ett anpassat anslutnings lager eller använda Azure IoT-enhets-SDK: er. Det finns flera fördelar med att använda Azure IoT-enhets-SDK: er:

| Utvecklings kostnad &nbsp; &nbsp; &nbsp;&nbsp; | Anpassat anslutnings skikt | SDK: er för Azure IoT-enheter |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Support | Behöver stödja och dokumentera vad du skapar | Har åtkomst till Microsoft Support (GitHub, Microsoft Q&A, Microsoft Docs, kund support team) |
| Nya funktioner | Du måste lägga till nya Azure-funktioner till anpassade mellanprogram | Kan direkt dra nytta av nya funktioner som Microsoft ständigt lägger till i IoT SDK: er |
| Investering | Investera hundratals timmar med inbäddad utveckling för att utforma, skapa, testa och underhålla en anpassad version | Kan dra nytta av kostnads fria verktyg med öppen källkod. Den enda kostnad som är associerad med SDK: er är inlärnings kurvan. |

## <a name="which-sdk-should-i-use"></a>Vilken SDK ska jag använda?

SDK: er för Azure IoT-enheter är tillgängliga i populära programmeringsspråk, inklusive C, C#, Java, Node.js och python. Det finns två huvudsakliga överväganden när du väljer en SDK: enhets funktioner och ditt teams bekanta med programmeringsspråket.

### <a name="device-capabilities"></a>Enhets funktioner

När du väljer ett SDK måste du fundera över gränserna för de enheter som du använder. En begränsad enhet är en som har en enda MikroKONTROLLANT (MCU) och begränsat minne. Om du använder en begränsad enhet rekommenderar vi att du använder [Embedded C SDK](#embedded-c-sdk). Den här SDK: n är utformad för att tillhandahålla minimal uppsättning funktioner för att ansluta till Azure IoT. Du kan också välja komponenter (MQTT-klient, TLS och socket-bibliotek) som är mest optimerade för den inbäddade enheten. Om din begränsade enhet också kör Azure-återställnings tider kan du använda Azure återställnings tider mellanprogram för att ansluta till Azure IoT. Azure återställnings tider-omsluter den inbäddade C SDK: n med extra funktioner för att förenkla anslutning av din Azure återställnings tider-enhet till molnet.

En obegränsad enhet är en stabilare processor, som kan köra ett operativ system för att stödja en språk körning som .NET eller python. Om du använder en obegränsad enhet är den främsta förtänkaren bekant med språket.

### <a name="your-teams-familiarity-with-the-programming-language"></a>Ditt teams bekanta med programmeringsspråket

SDK: er för Azure IoT-enheter implementeras på flera språk så att du kan välja det språk som helst. Enhets-SDK: er integreras också med andra välbekanta, språkspecifika verktyg. Du kan arbeta med ett välbekant utvecklings språk och-verktyg, vilket gör det möjligt för ditt team att optimera utvecklings cykeln för forskning, prototyper, produkt utveckling och pågående underhåll.

När det är möjligt väljer du ett SDK som känner till ditt utvecklings team. Alla Azure IoT SDK: er är öppen källkod och har flera exempel som är tillgängliga för ditt team att utvärdera och testa innan du genomför en speciell SDK.

## <a name="how-can-i-get-started"></a>Hur kommer jag igång?

Den plats där du vill starta är att utforska GitHub-databaserna för SDK: er för Azure-enheter. Du kan också testa en [snabb start](quickstart-send-telemetry-python.md) som visar hur du snabbt kan använda en SDK för att skicka telemetri till Azure IoT.

Dina alternativ för att komma igång beror på vilken typ av enhet du har:
- Använd [Embedded C SDK](#embedded-c-sdk)för begränsade enheter. 
- För enheter som körs på Azure återställnings tider kan du utveckla med [Azure återställnings tider-mellanprogram](#azure-rtos-middleware). 
- För enheter som är obegränsade kan du [välja en SDK](#unconstrained-device-sdks) på valfritt språk. 

### <a name="constrained-device-sdks"></a>Begränsade enhets-SDK: er
Dessa SDK: er är specialiserade för att köras på enheter med begränsade beräknings-eller minnes resurser. Mer information om vanliga enhets typer finns i [Översikt över enhets typer för Azure IoT](concepts-iot-device-types.md).

#### <a name="embedded-c-sdk"></a>Embedded C SDK
* [GitHub-lagringsplats](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Exempel](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Referensdokumentation](https://azure.github.io/azure-sdk-for-c/)
* [Så här skapar du den inbäddade C SDK: n](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Storleks diagram för begränsade enheter](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure återställnings tider mellanprogram

* [GitHub-lagringsplats](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Komma igång guider](https://github.com/azure-rtos/getting-started) och [fler exempel](https://github.com/azure-rtos/samples)
* [Referensdokumentation](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>Obegränsade enhets-SDK: er
Dessa SDK: er kan köras på alla enheter som har stöd för en språk körning på högre nivå. Detta inkluderar enheter som PC, Raspberry Pis och smartphones. De skiljer sig främst från språk så att du kan välja vilket bibliotek som passar ditt team och scenario bäst.

#### <a name="c-device-sdk"></a>C-enhets-SDK
* [GitHub-lagringsplats](https://github.com/Azure/azure-iot-sdk-c)
* [Exempel](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Paket](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Referensdokumentation](/azure/iot-hub/iot-c-sdk-ref/)
* [Referens dokumentation för Edge-modul](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Kompilera C-enhetens SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Porting C SDK till andra plattformar](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Dokumentation för utvecklare](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) för information om att korsa och komma igång på olika plattformar
* [Information om resurs förbrukning för Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>C#-enhets-SDK

* [GitHub-lagringsplats](https://github.com/Azure/azure-iot-sdk-csharp)
* [Exempel](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Paket](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Referensdokumentation](/dotnet/api/microsoft.azure.devices)
* [Referens dokumentation för Edge-modul](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Java-enhets-SDK

* [GitHub-lagringsplats](https://github.com/Azure/azure-iot-sdk-java)
* [Exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Paket](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Referensdokumentation](/java/api/com.microsoft.azure.sdk.iot.device)
* [Referens dokumentation för Edge-modul](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Node.js-enhets-SDK

* [GitHub-lagringsplats](https://github.com/Azure/azure-iot-sdk-node)
* [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Paket](https://www.npmjs.com/package/azure-iot-device)
* [Referensdokumentation](/javascript/api/azure-iot-device/)
* [Referens dokumentation för Edge-modul](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Python-enhets-SDK

* [GitHub-lagringsplats](https://github.com/Azure/azure-iot-sdk-python)
* [Exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Paket](https://pypi.org/project/azure-iot-device/)
* [Referensdokumentation](/python/api/azure-iot-device)
* [Referens dokumentation för Edge-modul](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>Tjänst-SDK:er
Azure IoT erbjuder också tjänst-SDK: er som gör det möjligt att skapa program på lösnings sidan för att hantera enheter, få insikter, visualisera data och mycket mer. Dessa SDK: er är speciella för varje Azure IoT-tjänst och är tillgängliga i C#, Java, Java Script och python för att förenkla din utvecklings upplevelse. 

#### <a name="iot-hub"></a>IoT Hub

Med SDK: erna för IoT Hub tjänst kan du bygga program som enkelt kan interagera med din IoT Hub för att hantera enheter och säkerhet. Du kan använda dessa SDK: er för att skicka meddelanden från molnet till enheten, anropa direkta metoder på dina enheter, uppdatera enhets egenskaper och mycket annat.

[**Läs mer om IoT Hub**](https://azure.microsoft.com/services/iot-hub/)  |  [ **Försök att kontrol lera en enhet**](../iot-hub/quickstart-control-device-python.md)

**C# IoT Hub service SDK**: [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [referens dokumentation](/dotnet/api/microsoft.azure.devices) för GitHub lagrings plats paket

**Java IoT Hub service SDK**: [](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)  |  [](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [referens dokumentation](/java/api/com.microsoft.azure.sdk.iot.service) för GitHub lagrings plats paket

**Java Script IoT Hub service SDK**: dokumentation om [GitHub lagrings](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)  |  [paket](https://www.npmjs.com/package/azure-iothub)  |  [exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)  |  [referens](/javascript/api/azure-iothub/)

**Python IoT Hub service SDK**: dokumentation om [GitHub lagrings](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)  |  [paket](https://pypi.python.org/pypi/azure-iot-hub/)  |  [exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [referens](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digitals är ett PaaS-erbjudande (Platform as a Service) som gör det möjligt att skapa kunskaps grafer baserade på digitala modeller av hela miljöer. De här miljöerna kan vara byggnader, fabriker, anläggningar, energi nät, järnvägar, stadium med mera, även hela städer. Dessa digitala modeller kan användas för att få insikter om bättre produkter, optimerade åtgärder, lägre kostnader och revolutionerande kund upplevelser. Azure IoT erbjuder tjänst-SDK: er som gör det enkelt att skapa program som använder kraften i Azures digitala dubbla.

[**Lär dig mer om digitala Azure-dubbla**](https://azure.microsoft.com/services/digital-twins/)  |  [ **Koda ett ADT-program**](../digital-twins/tutorial-code.md)

**C# ADT service SDK**: [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)-  |  [paket](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [referens dokumentation](/dotnet/api/overview/azure/digitaltwins/client)

**Java ADT service SDK**: [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)-  |  [paket](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)  |  [referens dokumentation](/java/api/overview/azure/digitaltwins/client)

**Node.js ADT service SDK**: [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)  |  [](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [referens dokumentation](/javascript/api/@azure/digital-twins-core/) för GitHubs lagrings plats paket

**Python ADT service SDK**: [](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://pypi.org/project/azure-digitaltwins-core/)  |  [exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)  |  [referens dokumentation](/python/api/azure-digitaltwins-core/azure.digitaltwins.core) för GitHub-lagringsplats

#### <a name="device-provisioning-service"></a>Device Provisioning Service

IoT Hub Device Provisioning Service (DPS) är en hjälp tjänst för IoT Hub som möjliggör Zero-Touch och just-in-Time-etablering till rätt IoT Hub utan mänsklig inblandning. DPS möjliggör etablering av miljon tals enheter på ett säkert och skalbart sätt. Med tjänst-SDK: er för DPS kan du skapa program som kan hantera dina enheter på ett säkert sätt genom att skapa registrerings grupper och utföra Mass åtgärder.

[**Läs mer om enhets etablerings tjänsten**](../iot-dps/index.yml)  |  [ **Försök att skapa en grupp registrering för X. 509-enheter**](../iot-dps/quick-enroll-device-x509-csharp.md)

**C# Device Provisioning service SDK**: dokumentation om [GitHub lagrings](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [paket](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)  |  [exempel](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [referens](/dotnet/api/microsoft.azure.devices.provisioning.service)

**Java Device Provisioning service SDK**: dokumentation om [GitHub lagrings](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [paket](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)  |  [exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [referens](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

**Node.js Device Provisioning service SDK**: dokumentation om [GitHub lagrings](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [paket](https://www.npmjs.com/package/azure-iot-provisioning-service)  |  [exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [referens](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>Nästa steg

* [Snabb start: ansluta en enhet till IoT Central (python)](quickstart-send-telemetry-python.md)
* [Snabb start: ansluta en enhet till IoT Hub (python)](quickstart-send-telemetry-cli-python.md)
* [Kom igång med inbäddad utveckling](quickstart-device-development.md)
* Lär dig mer om [fördelarna med att utveckla med Azure IoT-SDK](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) : er