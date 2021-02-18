---
title: Förstå Azure IoT Hub meddelanderoutning | Microsoft Docs
description: Guide för utvecklare – hur du använder meddelanderoutning för att skicka meddelanden från enheten till molnet. Innehåller information om hur du skickar både telemetri och data som inte är telemetri.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- devx-track-csharp
ms.openlocfilehash: 19ae5dc24e0a08548f4914114c9c0a6be65f4f0b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096085"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Använd IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Med meddelanderoutning kan du skicka meddelanden från dina enheter till moln tjänster på ett automatiserat, skalbart och tillförlitligt sätt. Meddelanderoutning kan användas för: 

* **Skicka meddelanden om telemetri och händelser** , t. ex. enhets livs cykel händelser, enhets dubbla ändrings händelser och digitala dubbla ändrings händelser till inbyggda slut punkter och anpassade slut punkter. Lär dig mer om [routning slut punkter](#routing-endpoints). Om du vill veta mer om de händelser som skickas från IoT Plug and Play-enheter kan du läsa [förstå IoT plug and Play digitala dubbla](../iot-pnp/concepts-digital-twin.md).

* **Filtrering av data innan de dirigeras till olika slut punkter** genom att använda omfattande frågor. Med meddelanderoutning kan du fråga efter meddelande egenskaper och meddelande text samt enhetens dubbla Taggar och enhetens dubbla egenskaper. Läs mer om hur du använder [frågor i](iot-hub-devguide-routing-query-syntax.md)meddelanderoutning.

IoT Hub behöver skriv åtkomst till dessa tjänst slut punkter för att meddelanderoutning ska fungera. Om du konfigurerar dina slut punkter via Azure Portal läggs de nödvändiga behörigheterna till. Se till att du konfigurerar dina tjänster så att de stöder det förväntade data flödet. Om du till exempel använder Event Hubs som en anpassad slut punkt måste du konfigurera **data flödes enheter** för den händelsehubben så att den kan hantera ingångs händelser som du planerar att skicka via IoT Hub meddelanderoutning. När du använder en Service Bus-kö som en slut punkt måste du konfigurera den **maximala storleken** så att kön kan hålla alla data inträngda tills den har utgångs punkt av konsumenter. När du först konfigurerar din IoT-lösning kan du behöva övervaka ytterligare slut punkter och göra nödvändiga justeringar för den faktiska belastningen.

IoT Hub definierar ett [gemensamt format](iot-hub-devguide-messages-construct.md) för all kommunikation från enhet till moln för samverkan mellan protokoll. Om ett meddelande matchar flera vägar som pekar på samma slut punkt, IoT Hub levererar meddelandet till slut punkten bara en gång. Därför behöver du inte konfigurera deduplicering i Service Bus kö eller ämne. I partitionerade köer garanterar partitionens tillhörighet meddelande ordning. Använd den här självstudien för att lära dig hur du [konfigurerar meddelanderoutning](tutorial-routing.md).

## <a name="routing-endpoints"></a>Slutpunkter för routning

En IoT-hubb har en inbyggd standard slut punkt (**meddelanden/händelser**) som är kompatibel med Event Hubs. Du kan skapa [anpassade slut punkter](iot-hub-devguide-endpoints.md#custom-endpoints) för att dirigera meddelanden till genom att länka andra tjänster i din prenumeration till IoT Hub. 

Varje meddelande dirigeras till alla slut punkter vars vägvals frågor det matchar. Med andra ord kan ett meddelande dirigeras till flera slut punkter.

Om din anpassade slut punkt har brand Väggs konfiguration, bör du överväga att använda Microsofts betrodda första part undantag för att ge din IoT Hub åtkomst till den aktuella slut punkten – [Azure Storage](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) [Azure Event Hubs](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) och [Azure Service Bus](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing). Detta är tillgängligt i SELECT-regioner för IoT Hub med [hanterad tjänst identitet](./virtual-network-support.md).

IoT Hub stöder för närvarande följande slut punkter:

 - Inbyggd slut punkt
 - Azure Storage
 - Service Bus köer och Service Bus ämnen
 - Event Hubs

## <a name="built-in-endpoint-as-a-routing-endpoint"></a>Inbyggd slut punkt som en cirkulations slut punkt

Du kan använda standard [Event Hubs integration och SDK](iot-hub-devguide-messages-read-builtin.md) : er för att ta emot meddelanden från enheten till molnet från den inbyggda slut punkten (**meddelanden/händelser**). När en väg har skapats slutar data flöda till den inbyggda slut punkten om inte en väg skapas till den slut punkten.

## <a name="azure-storage-as-a-routing-endpoint"></a>Azure Storage som en cirkulations slut punkt

Det finns två lagrings tjänster IoT Hub kan dirigera meddelanden till-- [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) -och [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) -konton (ADLS Gen2). Azure Data Lake Storage-konton är [hierarkiska namn rymds](../storage/blobs/data-lake-storage-namespace.md)-aktiverade lagrings konton som byggts ovanpå Blob Storage. Båda använder blobbar för lagringen.

IoT Hub stöder skrivning av data till Azure Storage i [Apache Avro](https://avro.apache.org/) -format samt i JSON-format. Standardvärdet är AVRO. När du använder JSON-kodning måste du ange contentType till **Application/JSON** och ContentEncoding till **UTF-8** i meddelande [systemets egenskaper](iot-hub-devguide-routing-query-syntax.md#system-properties). Båda dessa värden är Skift läges känsliga. Om innehålls kodningen inte har angetts skrivs meddelandena i bas 64-kodat format IoT Hub.

Kodnings formatet kan bara anges när Blob Storage-slutpunkten har kon figurer ATS. den kan inte redige ras för en befintlig slut punkt. Om du vill byta kodnings format för en befintlig slut punkt måste du ta bort och återskapa den anpassade slut punkten med det format som du vill använda. En praktisk strategi kan vara att skapa en ny anpassad slut punkt med önskat kodnings format och lägga till en parallell väg till den slut punkten. På så sätt kan du verifiera dina data innan du tar bort den befintliga slut punkten.

Du kan välja kodnings formatet med IoT Hub skapa eller uppdatera REST API, särskilt [RoutingStorageContainerProperties](/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), Azure Portal, [Azure CLI](/cli/azure/iot/hub/routing-endpoint)eller [Azure PowerShell](/powershell/module/az.iothub/add-aziothubroutingendpoint). Följande bild visar hur du väljer kodnings formatet i Azure Portal.

![Slut punkts kodning för Blob Storage](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub batchar meddelanden och skriver data till lagring när batchen når en viss storlek eller en viss tid har förflutit. IoT Hub standardvärdet för följande fil namns konvention:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Du kan använda valfri fil namns konvention, men du måste använda alla listade tokens. IoT Hub skrivs till en tom BLOB om det inte finns några data att skriva.

Vi rekommenderar att du visar blobbar eller filer och sedan söker efter dem, för att se till att alla blobbar eller filer läses utan att göra några antaganden om partitionen. Partitions intervallet kan eventuellt ändras under en [Microsoft-initierad redundansväxling](iot-hub-ha-dr.md#microsoft-initiated-failover) eller IoT Hub [manuell redundans](iot-hub-ha-dr.md#manual-failover). Du kan använda [list-BLOB-API: et](/rest/api/storageservices/list-blobs) för att räkna upp listan över blobbar eller [lista ADLS Gen2 API](/rest/api/storageservices/datalakestoragegen2/path/list) för listan med filer. Se följande exempel som vägledning.

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

Om du vill skapa ett Azure Data Lake Gen2 lagrings konto skapar du ett nytt v2-lagrings konto och väljer *aktiverat* i fältet *hierarkisk namnrymd* på fliken **Avancerat** , som du ser i följande bild:

![Välj Azure date Lake Gen2-lagring](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)

## <a name="service-bus-queues-and-service-bus-topics-as-a-routing-endpoint"></a>Service Bus köer och Service Bus ämnen som en cirkulations slut punkt

Service Bus köer och ämnen som används som IoT Hub slut punkter får inte ha **sessioner** eller **dubblettidentifiering** aktiverade. Om något av dessa alternativ är aktiverat visas slut punkten som **ej nåbar** i Azure Portal.

## <a name="event-hubs-as-a-routing-endpoint"></a>Event Hubs som en cirkulations slut punkt

Förutom den inbyggda-Event Hubs-kompatibla slut punkten kan du också dirigera data till anpassade slut punkter av typen Event Hubs. 

## <a name="reading-data-that-has-been-routed"></a>Läser data som har dirigerats

Du kan konfigurera en väg genom att följa den här [självstudien](tutorial-routing.md).

Använd följande självstudier om du vill lära dig mer om att läsa meddelanden från en slut punkt.

* Läser från [inbyggd slut punkt](quickstart-send-telemetry-node.md)

* Läser från [Blob Storage](../storage/blobs/storage-blob-event-quickstart.md)

* Läser från [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Läser från [Service Bus köer](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Läsa från [Service Bus ämnen](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)


## <a name="fallback-route"></a>Reserv väg

Reserv vägen skickar alla meddelanden som inte uppfyller frågevillkor på någon av de befintliga vägarna till de inbyggda Event Hubs (**meddelanden/händelser**) som är kompatibla med [Event Hubs](../event-hubs/index.yml). Om meddelanderoutning är aktiverat kan du aktivera reserv väg funktionen. När en väg har skapats slutar data flöda till den inbyggda slut punkten, om inte en väg skapas till den slut punkten. Om det inte finns några vägar till den inbyggda slut punkten och en återställnings väg är aktive rad skickas endast meddelanden som inte matchar några frågevillkor i vägar till den inbyggda slut punkten. Om alla befintliga vägar tas bort måste återställnings vägen vara aktive rad för att ta emot alla data vid den inbyggda slut punkten.

Du kan aktivera/inaktivera återställnings vägen på bladet Azure Portal->meddelande cirkulation. Du kan också använda Azure Resource Manager för [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) om du vill använda en anpassad slut punkt för återställnings väg.

## <a name="non-telemetry-events"></a>Händelser som inte är telemetri

Förutom telemetri möjliggör meddelanderoutning även sändning av enhets dubbla ändrings händelser, livs cykel händelser för enheter och digitala dubbla ändrings händelser. Om en väg till exempel skapas med data källa inställt på **enhet dubbla ändrings händelser** skickar IoT Hub meddelanden till slut punkten som innehåller ändringen i enheten. På liknande sätt skickar IoT Hub ett meddelande som anger om enheten har tagits bort eller skapats, om en väg skapas med data källa inställt på **enhetens livs cykel händelser**. Som en del av [Azure IoT plug and Play](../iot-pnp/overview-iot-plug-and-play.md)kan en utvecklare skapa vägar med data källa inställt på **digitala dubbla ändrings händelser** och IoT Hub skickar meddelanden när en digital enhets [egenskap](../iot-pnp/iot-plug-and-play-glossary.md) har angetts eller ändrats, en [digital](../iot-pnp/iot-plug-and-play-glossary.md) enhet byts ut eller när en ändrings händelse inträffar för den underliggande enheten.

[IoT Hub integreras också med Azure Event Grid](iot-hub-event-grid.md) för att publicera enhets händelser som stöder real tids integrering och automatisering av arbets flöden baserat på dessa händelser. Se viktiga [skillnader mellan meddelanderoutning och event Grid](iot-hub-event-grid-routing-comparison.md) för att se vilka som fungerar bäst för ditt scenario.

## <a name="testing-routes"></a>Testa vägar

När du skapar en ny väg eller redigerar en befintlig väg bör du testa väg frågan med ett exempel meddelande. Du kan testa enskilda vägar eller testa alla vägar samtidigt och inga meddelanden dirigeras till slut punkterna under testet. Azure Portal, Azure Resource Manager, Azure PowerShell och Azure CLI kan användas för testning. Med hjälp av kan du se om exempel meddelandet matchade frågan, meddelandet inte matchade frågan eller eftersom det inte gick att köra testet eftersom exempel meddelandet eller frågesyntaxen är felaktiga. Läs mer i [testa väg](/rest/api/iothub/iothubresource/testroute) och [testa alla vägar](/rest/api/iothub/iothubresource/testallroutes).

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Beställ garantier med minst en gång

IoT Hub meddelanderoutning garanterar beställd och minst en gång efter leverans av meddelanden till slut punkterna. Det innebär att det kan finnas dubbla meddelanden och en serie meddelanden kan återsändas med den ursprungliga meddelande ordningen. Om den ursprungliga meddelande ordningen exempelvis är [1, 2, 3, 4], kan du få en meddelande sekvens som [1, 2, 1, 2, 3, 1, 2, 3, 4]. Beställnings garantin är att om du någonsin får meddelandet [1], följs det alltid av [2, 3, 4].

För att hantera meddelande kopior rekommenderar vi att du stämplar en unik identifierare i program egenskaperna för meddelandet vid ursprungs punkten, som vanligt vis är en enhet eller en modul. Tjänsten som använder meddelandena kan hantera dubbletter av meddelanden med hjälp av den här identifieraren.

## <a name="latency"></a>Svarstid

När du dirigerar meddelanden från enhet till molnet med hjälp av inbyggda slut punkter, finns det en liten ökning av svars tiden från slut punkt till slut punkt efter att den första vägen har skapats.

I de flesta fall är den genomsnittliga ökningen i svars tiden mindre än 500 ms. Du kan övervaka svars tiden med **Routning: meddelande fördröjning för meddelanden/händelser** eller **D2C. endpoints. latens. builtity. events** IoT Hub mått. Om du skapar eller tar bort en väg när den första inte påverkar svars tiden från slut punkt till slut punkt.

## <a name="monitoring-and-troubleshooting"></a>Övervaka och felsöka

IoT Hub tillhandahåller flera mått som rör Routning och slut punkter för att ge dig en översikt över hälsan för ditt nav och meddelanden som skickas. För en lista över alla IoT Hub mått som uppdelats efter funktionell kategori, se [mått i övervaknings data referensen](monitor-iot-hub-reference.md#metrics). Du kan spåra fel som uppstår under utvärderingen av en cirkulations fråga och slut punkts hälsa som uppfattas av IoT Hub med [kategorin **vägar** i IoT Hub resurs loggar](monitor-iot-hub-reference.md#routes). Mer information om hur du använder mått och resurs loggar med IoT Hub finns i [övervaka IoT Hub](monitor-iot-hub.md).

Du kan använda REST API [Hämta slut punkts hälsa](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) för att få [hälso status](iot-hub-devguide-endpoints.md#custom-endpoints) för slut punkterna.

Använd [fel söknings guiden för routning](troubleshoot-message-routing.md) för mer information och stöd för fel sökning av routning.

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar meddelande vägar finns i [Process IoT Hub enhet-till-moln-meddelanden med hjälp av vägar](tutorial-routing.md).

* [Skicka meddelanden från enheten till molnet](quickstart-send-telemetry-node.md)

* Information om de SDK: er som du kan använda för att skicka meddelanden från enheten till molnet finns i [Azure IoT SDK](iot-hub-devguide-sdks.md): er.