---
title: System avsnitt i Azure Event Grid
description: Beskriver system avsnitt i Azure Event Grid.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: b1fbecb1e372602f9c252d43d2a1f93524ef1846
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052973"
---
# <a name="system-topics-in-azure-event-grid"></a>System avsnitt i Azure Event Grid
Ett system avsnitt i Event Grid representerar en eller flera händelser som publicerats av Azure-tjänster som Azure Storage och Azure-Event Hubs. Ett system ämne kan till exempel representera **alla BLOB-händelser** eller bara **BLOB-skapade** och **borttagna BLOB** -händelser som har publicerats för ett **särskilt lagrings konto**. I det här exemplet, när en BLOB överförs till lagrings kontot, publicerar Azure Storages tjänsten en **BLOB-skapad** händelse i avsnittet system i Event Grid, som sedan vidarebefordrar händelsen till ämnes [prenumeranter](event-handlers.md) som tar emot och bearbetar händelsen. 

> [!NOTE] 
> Endast Azure-tjänster kan publicera händelser i system ämnen. Därför får du inte någon slut punkt eller åtkomst nycklar som du kan använda för att publicera händelser som du gör för anpassade ämnen eller domäner.

## <a name="azure-services-that-support-system-topics"></a>Azure-tjänster som stöder system ämnen
Här är den aktuella listan över Azure-tjänster som har stöd för att skapa system ämnen på dem.

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure App Service](event-schema-app-service.md)
- [Azure Blob Storage](event-schema-blob-storage.md)
- [Azure Communication Services](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Azure-resursgrupper](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure-prenumerationer](event-schema-subscriptions.md)
- [Azure Cache for Redis](event-schema-azure-cache.md)

## <a name="system-topics-as-azure-resources"></a>System ämnen som Azure-resurser
Tidigare var ett system ämne implicit och visade sig inte för enkelhetens skull. System ämnen visas nu som Azure-resurser och tillhandahåller följande funktioner:

- [Visa system ämnen i Azure Portal](create-view-manage-system-topics.md#view-all-system-topics)
- Exportera Resource Manager-mallar för system ämnen och händelse prenumerationer i Azure Portal
- [Konfigurera diagnostikloggar för system ämnen](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- Konfigurera aviseringar om publicerings-och leverans problem 

## <a name="lifecycle-of-system-topics"></a>Livs cykel för system ämnen
Du kan skapa ett system ämne på två sätt: 

- Skapa en [händelse prenumeration på en Azure-resurs som en tilläggs resurs](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)som automatiskt skapar ett system avsnitt med namnet i formatet: `<Azure resource name>-<GUID>` . System avsnittet som skapas på det här sättet tas automatiskt bort när den sista händelse prenumerationen för ämnet tas bort. 
- Skapa ett system ämne för en Azure-resurs och skapa sedan en händelse prenumeration för det system ämnet. När du använder den här metoden kan du ange ett namn för system avsnittet. Avsnittet system tas inte bort automatiskt när den sista händelse prenumerationen tas bort. Du måste ta bort den manuellt. 

    När du använder Azure Portal använder du alltid den här metoden. När du skapar en händelse prenumeration med hjälp av [sidan **händelser** i en Azure-resurs](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage)skapas avsnittet system först och sedan skapas prenumerationen för ämnet. Du kan uttryckligen skapa ett system avsnitt först med hjälp av [sidan **Event Grid system ämnen**](create-view-manage-system-topics.md#create-a-system-topic) och sedan skapa en prenumeration för ämnet. 

När du använder [CLI](create-view-manage-system-topics-cli.md), [rest](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)eller [Azure Resource Manager mall](create-view-manage-system-topics-arm.md)kan du välja någon av metoderna ovan. Vi rekommenderar att du först skapar ett system ämne och sedan skapar en prenumeration på ämnet, eftersom det här är det senaste sättet att skapa system ämnen.

Det går inte att skapa system avsnittet om du har konfigurerat Azure-principer på ett sådant sätt att tjänsten Event Grid inte kan skapa den. Du kan till exempel ha en princip som endast tillåter att vissa typer av resurser skapas (till exempel: Azure Storage, Azure Event Hubs osv.) i prenumerationen. 

## <a name="location-and-resource-group-for-a-system-topic"></a>Plats och resurs grupp för ett system ämne
För Azure Event-källor som finns i en speciell region/plats skapas system ämne på samma plats som Azure-händelseloggen. Om du till exempel skapar en händelse prenumeration för en Azure Blob Storage i östra USA skapas avsnittet system i USA, östra. För globala Azure-händelseloggar som Azure-prenumerationer, resurs grupper eller Azure Maps, skapar Event Grid system-avsnittet i den **globala** platsen. 

I allmänhet skapas system ämne i samma resurs grupp som Azures händelse källa. För händelse prenumerationer som skapats i Azure-prenumerationens omfattning skapas system avsnittet i resurs gruppen **standard-EventGrid** i regionen **USA, västra 2** . Om resurs gruppen inte finns skapar Azure Event Grid den innan du skapar system avsnittet. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Skapa, Visa och hantera system ämnen med hjälp av Azure Portal](create-view-manage-system-topics.md).
- [Skapa, Visa och hantera Event Grid system ämnen med hjälp av Azure CLI](create-view-manage-system-topics-cli.md)
- [Skapa Event Grid system ämnen med hjälp av Azure Resource Manager mallar](create-view-manage-system-topics-arm.md)
