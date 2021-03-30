---
title: Azure Sakernas Internet (IoT)-tekniker och -lösningar
description: Beskriver en samling tekniker och tjänster som du kan använda för att bygga en Azure IoT-lösning.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 0cd98705d2d6947ff766f528e4e92f1e51bcc42d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96852820"
---
# <a name="what-azure-technologies-and-services-can-you-use-to-create-iot-solutions"></a>Vilka Azure-tekniker och tjänster kan du använda för att skapa IoT-lösningar?

Azure IoT-teknik och-tjänster ger dig alternativ för att skapa ett brett utbud av IoT-lösningar som möjliggör digital omvandling för din organisation. Du kan till exempel:

* Använd [Azure IoT Central](https://apps.azureiotcentral.com), en hanterad IoT-programplattform, för att bygga och distribuera en säker IoT-lösning i företags klass. IoT Central innehåller en samling branschspecifika programmallar, till exempel detalj handel och sjukvård, för att påskynda utvecklings processen för lösningen.
* Använd Azure IoT Platform-tjänster som [azure IoT Hub](../iot-hub/about-iot-hub.md) och SDK: er för [Azure IoT-enheter](../iot-hub/iot-hub-devguide-sdks.md) för att bygga en anpassad IoT-lösning från grunden.

![Azure IoT-tekniker,-tjänster och-lösningar](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

[IoT Central applikations plattform](https://apps.azureiotcentral.com) minskar belastningen och kostnaderna för att utveckla, hantera och underhålla IoT-lösningar i företags klass. Med IoT Centralens anpassningsbara webb gränssnitt i kan du övervaka enhets villkor, skapa regler och hantera miljon tals enheter och deras data under deras livs cykel. API-ytan i IoT Central ger dig programmatisk åtkomst till att konfigurera och interagera med din IoT-lösning.

Azure IoT Central är en helt hanterad program plattform som du kan använda för att skapa anpassade IoT-lösningar. IoT Central använder programmallar för att skapa lösningar. Det finns mallar för allmänna lösningar och för vissa branscher, till exempel energi, sjukvård, myndigheter och åter försäljning. Med IoT Central programmallar kan du distribuera ett IoT Central-program på några minuter som sedan kan anpassas med teman, instrument paneler och vyer.

Välj enheter från [Azure-certifierad för IoT-katalogen](https://catalog.azureiotsolutions.com) för att snabbt ansluta till din lösning. Använd IoT Central webb gränssnitt för att övervaka och hantera dina enheter för att hålla dem felfria och anslutna. Använd anslutningar och API: er för att integrera ditt IoT Central program med andra affärs program.

Som en helt hanterad program plattform är IoT Central en enkel, förutsägbar pris modell.

## <a name="azure-iot-solution-accelerators"></a>Azure IoT-lösningsacceleratorer

[Azure IoT Solution Accelerators](https://www.azureiotsolutions.com) är en samling anpassningsbara lösningar i företags klass. Du kan distribuera dessa lösningar som de är eller utveckla en anpassad IoT-lösning med hjälp av Java-eller .NET-källkoden med öppen källkod.

Azure IoT Solution-acceleratorer ger en hög kontroll över din IoT-lösning. Lösnings acceleratorer innehåller färdiga lösningar för vanliga IoT-scenarier som du kan distribuera till din Azure-prenumeration på några minuter. Scenarierna är:

* Ansluten fabrik
* Enhetssimulering

Kod basen med öppen källkod för alla lösnings acceleratorer finns på GitHub. Ladda ned koden för att anpassa en lösnings Accelerator så att den uppfyller dina särskilda IoT-krav.

Lösnings acceleratorerna använder Azure-tjänster som Azure IoT Hub och Azure Storage som du måste hantera i din Azure-prenumeration.

## <a name="custom-solutions"></a>Anpassade lösningar

Om du vill skapa en IoT-lösning från grunden eller utöka en lösning som skapats med hjälp av IoT Central eller en lösnings Accelerator, använder du en eller flera av följande Azure IoT-tekniker och-tjänster:

### <a name="devices"></a>Enheter

Utveckla dina IoT-enheter med hjälp av ett av [Azure IoT-Startpaketen](https://catalog.azureiotsolutions.com/kits) eller Välj en enhet som du vill använda från [Azure-certifierad för IoT-katalogen](https://catalog.azureiotsolutions.com). Implementera din inbäddade kod med hjälp av [enhets-SDK](../iot-hub/iot-hub-devguide-sdks.md): er med öppen källkod. Enhets-SDK: er har stöd för flera operativ system, till exempel Linux, Windows och real tids operativ system. Det finns SDK: er för flera programmeringsspråk, till exempel [C](https://github.com/Azure/azure-iot-sdk-c), [Node.js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.net](https://github.com/Azure/azure-iot-sdk-csharp)och [python](https://github.com/Azure/azure-iot-sdk-python).

Du kan förenkla hur du skapar den inbäddade koden för dina enheter genom att använda [IoT plug and Play](../iot-pnp/overview-iot-plug-and-play.md) -tjänsten. IoT Plug and Play gör att lösnings utvecklare kan integrera enheter med sina lösningar utan att skriva någon inbäddad kod. I IoT-Plug and Play är det ett modell schema för _enhets kapacitet_ som beskriver enhets funktioner. Använd enhetens kapacitets modell för att generera din inbäddade enhets kod och konfigurera en molnbaserad lösning, till exempel ett IoT Central program.

Med [Azure IoT Edge](../iot-edge/about-iot-edge.md) kan du avlasta delar av din IoT-arbetsbelastning från dina Azure Cloud Services till dina enheter. IoT Edge kan minska svars tiden i din lösning, minska mängden data som dina enheter utbyter med molnet och Aktivera offline-scenarier. Du kan hantera IoT Edge enheter från IoT Central och vissa lösnings acceleratorer.

[Azure Sphere](/azure-sphere/product-overview/what-is-azure-sphere) är en säker, högnivå applikations plattform med inbyggda kommunikations-och säkerhetsfunktioner för Internet-anslutna enheter. Den innehåller en säker mikrostyrenhet-enhet, ett anpassat Linux-baserat operativ system och en molnbaserad säkerhets tjänst som tillhandahåller kontinuerlig, förnybar säkerhet.

### <a name="cloud-connectivity"></a>Moln anslutning

[Azure IoT Hub](../iot-hub/about-iot-hub.md) -tjänsten möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljon tals IoT-enheter och en molnbaserad lösning. [Azure IoT Hub Device Provisioning service](../iot-dps/about-iot-dps.md) är en hjälp tjänst för IoT Hub. Tjänsten ger dig noll touch, just-in-Time-etablering av enheter till rätt IoT Hub utan mänsklig inblandning. Dessa funktioner gör att kunderna kan etablera miljon tals enheter på ett säkert och skalbart sätt.

IoT Hub är en kärn komponent i Solution Accelerators och du kan använda den för att uppfylla IoT-implementerings utmaningar som:

* Omfattande enhetsanslutning och hantering.
* Omfattande inmatning av telemetri.
* Kontroll av enheter.
* Tillämpning av enhetssäkerhet.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>Överbrygga avståndet mellan det fysiska och digitala världar

[Azure Digitals dubbla](../digital-twins/overview.md) är en IoT-tjänst som gör det möjligt att modellera en fysisk miljö. Den använder ett geografiskt informations diagram för att modellera relationerna mellan personer, utrymmen och enheter. Genom att samrelatera data över digitala och fysiska världar kan du skapa sammanhangsbaserade medvetna lösningar.

IoT Central använder digitala enheter för att synkronisera enheter och data i den verkliga världen med de digitala modeller som gör det möjligt för användare att övervaka och hantera anslutna enheter.

### <a name="data-and-analytics"></a>Data och analys

IoT-enheter genererar vanligt vis stora mängder tids serie data, till exempel temperatur läsningar från sensorer. [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) kan ansluta till en IoT-hubb, läsa telemetri-dataströmmen från dina enheter, lagra dessa data och göra det möjligt att fråga och visualisera den.

[Azure Maps](../azure-maps/index.yml) är en samling geospatiala tjänster som använder nya mappnings data för att tillhandahålla korrekt geografisk kontext till webb-och mobil program. Du kan använda en REST API, en webbaserad JavaScript-kontroll eller en Android SDK för att bygga dina program.

## <a name="next-steps"></a>Nästa steg

För en praktisk upplevelse kan du prova någon av snabb starterna:

- [Skapa ett Azure IoT Central-program](../iot-central/core/quick-deploy-iot-central.md)
- [Skicka telemetri från en enhet till en IoT-hubb](../iot-hub/quickstart-send-telemetry-cli.md)
