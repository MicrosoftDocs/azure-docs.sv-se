---
title: Vad är Azure Digital Twins?
titleSuffix: Azure Digital Twins
description: Översikt över vad som kan göras med Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: d03a4865c8db52f74f4130c458fec3028f5b95a4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481675"
---
# <a name="what-is-azure-digital-twins"></a>Vad är Azure Digital Twins?

**Azure Digital Twins** är en Plattform som en tjänst (PaaS) som gör det möjligt att skapa kunskapsdiagram baserade på digitala modeller av hela miljöer. Dessa miljöer kan vara byggnader, fabriker, farmar, energinätverk, stadsnät, stadion med mera – till och med hela städer. Dessa digitala modeller kan användas för att få insikter som ger bättre produkter, optimerad drift, minskade kostnader och banbrytande kundupplevelser.

Utnyttja dina domänkunskaper utöver de Azure Digital Twins att skapa anpassade, anslutna lösningar som:
* Modellera alla miljöer och gör digitala tvillingar till liv på ett skalbart och säkert sätt
* Ansluta tillgångar som IoT-enheter och befintliga affärssystem
* Använda ett robust händelsesystem för att skapa dynamisk affärslogik och databehandling
* Integrera med Azures data-, analys- och AI-tjänster som hjälper dig att spåra det förflutna och sedan förutsäga framtiden

## <a name="azure-digital-twins-capabilities"></a>Azure Digital Twins funktioner

Här är en sammanfattning av de funktioner som tillhandahålls av Azure Digital Twins.

### <a name="open-modeling-language"></a>Öppna modelleringsspråket

I Azure Digital Twins definierar du de digitala entiteter som representerar personer, platser och saker i din fysiska miljö med hjälp av anpassade tvillingtyper som kallas [**modeller.**](concepts-models.md) 

Du kan tänka på dessa modelldefinitioner som en specialiserad vokabulär för att beskriva din verksamhet. För en lösning för bygghantering kan du till exempel definiera modeller som "byggnad", "golv" och "hiss". Du kan sedan skapa **digitala tvillingar baserat** på dessa modeller för att representera din specifika miljö.

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

Modeller definieras i ett JSON-liknande språk som kallas [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)och de beskriver tvillingar vad gäller deras tillståndsegenskaper, telemetrihändelser, kommandon, komponenter och relationer.
* Modeller definierar **semantiska** relationer mellan dina entiteter så att du kan ansluta dina tvillingar till ett kunskapsdiagram som återspeglar deras interaktioner. Du kan tänka på modellerna som substantiv i en beskrivning av din värld och relationerna som verb.
* Du kan också specialisera tvillingar med hjälp av modellarv. En modell kan ärva från en annan.

DTDL används för datamodeller i andra Azure IoT-tjänster, inklusive [IoT Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) [och Time Series Insights (TSI).](../time-series-insights/overview-what-is-tsi.md) På så sätt kan du hålla Azure Digital Twins-lösningen ansluten och kompatibel med andra delar av Azure-ekosystemet.

### <a name="live-execution-environment"></a>Livekörningsmiljö

Digitala modeller Azure Digital Twins är live, uppdaterade representationer av den verkliga världen. Med hjälp av relationerna i dina anpassade DTDL-modeller ansluter du tvillingar till en **live-graf som** representerar din miljö.

Du kan visa en visualisering av ditt Azure Digital Twins med hjälp av ett exempelprogram, Azure Digital Twins [**utforskaren**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/).

Här är en vy över hur exempelvisualiseringen ser ut:

:::image type="content" source="media/includes/azure-digital-twins-explorer.png" alt-text="Skärmbild av exempelprogrammet Azure Digital Twins Explorer som visar ett diagram över noder som representerar digitala tvillingar" lightbox="media/includes/azure-digital-twins-explorer.png":::

Azure Digital Twins ett omfattande **händelsesystem som** håller diagrammet aktuellt med databehandling och affärslogik. Du kan ansluta externa beräkningsresurser, till [exempel Azure Functions](../azure-functions/functions-overview.md), för att driva den här databearbetningen på flexibla, anpassade sätt.

Du kan också extrahera insikter från livekörningsmiljön med hjälp Azure Digital Twins kraftfulla **fråge-API: et**. Med API:et kan du fråga med omfattande sökvillkor, inklusive egenskapsvärden, relationer, relationsegenskaper, modellinformation med mera. Du kan också kombinera frågor, samla in en mängd olika insikter om din miljö och besvara anpassade frågor som är viktiga för dig.

### <a name="input-from-iot-and-business-systems"></a>Indata från IoT och affärssystem

Om du vill hålla livekörningsmiljön Azure Digital Twins uppdaterad med den verkliga världen kan du använda [IoT Hub](../iot-hub/about-iot-hub.md) för att ansluta din lösning till IoT- och IoT Edge enheter. Dessa hubb-hanterade enheter representeras som en del av tvillingdiagrammet och tillhandahåller de data som driver din modell.

Du kan skapa en ny IoT Hub för detta ändamål med Azure Digital Twins, eller ansluta en befintlig IoT Hub tillsammans med de enheter som den redan hanterar.

Du kan också köra Azure Digital Twins från andra datakällor med hjälp av REST-API:er eller anslutningsappar till andra tjänster som [Logic Apps](../logic-apps/logic-apps-overview.md).

### <a name="output-to-tsi-storage-and-analytics"></a>Utdata till TSI, lagring och analys

Data i din Azure Digital Twins kan dirigeras till underordnade Azure-tjänster för ytterligare analys eller lagring. Detta tillhandahålls via **händelsevägar,** som använder [Händelsehubb,](../event-hubs/event-hubs-about.md) [Event Grid](../event-grid/overview.md)eller [Service Bus för](../service-bus-messaging/service-bus-messaging-overview.md) att driva dina önskade dataflöden.

Några saker du kan göra med händelsevägar är:
* Lagra Azure Digital Twins-data i [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md)
* Analysera Azure Digital Twins-data med [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) eller andra Microsoft-verktyg för dataanalys
* Integrera större arbetsflöden med Logic Apps
* Ansluta Azure Digital Twins till Time Series Insights att spåra tidsseriehistorik för varje tvilling
* Anpassa en Time Series-modell i Time Series Insights med en källa i Azure Digital Twins

Det här är ett annat sätt Azure Digital Twins kan ansluta till en större lösning och stödja dina anpassade behov för fortsatt arbete med dessa insikter.

## <a name="azure-digital-twins-in-a-solution-context"></a>Azure Digital Twins i en lösningskontext

Azure Digital Twins används ofta i kombination med andra Azure-tjänster som en del av en större IoT-lösning. 

En komplett lösning med Azure Digital Twins kan innehålla följande delar:
* Den Azure Digital Twins tjänstinstansen. Här lagras dina tvillingmodeller och din tvillinggraf med dess tillstånd och dirigerar händelsebearbetning.
* En eller flera klientappar som driver Azure Digital Twins instansen genom att konfigurera modeller, skapa topologi och extrahera insikter från tvillingdiagrammet.
* En eller flera externa beräkningsresurser för att bearbeta händelser Azure Digital Twins eller anslutna datakällor, till exempel enheter. Ett vanligt sätt att tillhandahålla beräkningsresurser är via [Azure Functions](../azure-functions/functions-overview.md).
* En IoT-hubb som tillhandahåller funktioner för enhetshantering och IoT-dataströmmar.
* Underordnade tjänster för att hantera uppgifter som arbetsflödesintegrering (till [exempel Logic Apps,](../logic-apps/logic-apps-overview.md)kall lagring, tidsserieintegrering eller analys).

Följande diagram visar var Azure Digital Twins ligger i kontexten för en större Azure IoT-lösning.

:::image type="content" source="media/overview/solution-context.png" alt-text="Diagram som visar indatakällor, utdatatjänster och tvåvägskommunikation med både klientappar och externa beräkningsresurser." border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>Tjänstbegränsningar

Du kan läsa om **tjänstbegränsningarna för** Azure Digital Twins här: [Azure Digital Twins tjänstbegränsningar.](reference-service-limits.md) Detta kan vara användbart när du arbetar med tjänsten för att förstå tjänstens funktions- och hastighetsbegränsningar, samt vilka gränser som kan justeras vid behov.

## <a name="terminology"></a>Terminologi

Du kan visa en lista över vanliga **IoT-termer** och deras användning i Azure IoT-tjänsterna, inklusive Azure Digital Twins här: [Azure IoT-ordlistan](../iot-fundamentals/iot-glossary.md?toc=/azure/digital-twins/toc.json&bc=/azure/digital-twins/breadcrumb/toc.json). Detta kan vara en användbar referens när du kommer igång med att Azure Digital Twins och skapa en IoT-lösning.

## <a name="next-steps"></a>Nästa steg

* Fördjupa dig i att Azure Digital Twins i snabbstarten: [*Snabbstart: Utforska ett exempelscenario*](quickstart-azure-digital-twins-explorer.md).

* Eller börja läsa om Azure Digital Twins begrepp med [*Koncept: Anpassade modeller*](concepts-models.md).