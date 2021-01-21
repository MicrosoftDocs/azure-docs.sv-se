---
title: Lägg till partitioner dynamiskt i en Event Hub i Azure Event Hubs
description: Den här artikeln visar hur du lägger till partitioner i en Event Hub dynamiskt i Azure Event Hubs.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: e6efdc7bab309f825032555c97f1e1128f5addd6
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625273"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Lägga till partitioner i en Event Hub dynamiskt (Apache Kafka ämne) i Azure Event Hubs
Event Hubs tillhandahåller meddelandeströmning via ett partitionerat konsumentmönster där varje konsument endast läser en specifik delmängd, eller partition, av meddelandeströmmen. Det här mönstret gör det möjligt att skala horisontellt för händelsebearbetning och tillhandahåller andra strömfokuserade funktioner som inte är tillgängliga i köer och ämnen. En partition är en ordnad sekvens av händelser som hålls kvar i en händelsehubb. När nya händelser anländer läggs de till i slutet av den här sekvensen. Mer information om partitioner i allmänhet finns i [partitioner](event-hubs-scalability.md#partitions)

Du kan ange antalet partitioner vid tidpunkten för att skapa en Event Hub. I vissa fall kan du behöva lägga till partitioner när händelsehubben har skapats. Den här artikeln beskriver hur du lägger till partitioner i en befintlig händelsehubben dynamiskt. 

> [!IMPORTANT]
> Dynamiska tillägg till partitioner är bara tillgängligt på **dedikerade** Event Hubs kluster.

> [!NOTE]
> För Apache Kafka klienter mappas en **Event Hub** till ett **Kafka-ämne**. Fler mappningar mellan Azure Event Hubs och Apache Kafka finns i [Kafka och Event Hubs konceptuell mappning](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>Uppdatera antalet partitioner
Det här avsnittet visar hur du uppdaterar antalet partitioner i en Event Hub på olika sätt (PowerShell, CLI och så vidare).

### <a name="powershell"></a>PowerShell
Använd PowerShell [-kommandot Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) för att uppdatera partitioner i en Event Hub. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
Använd [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az-eventhubs-eventhub-update) CLI-kommandot för att uppdatera partitioner i en Event Hub. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Resource Manager-mall
Uppdatera värdet för `partitionCount` egenskapen i Resource Manager-mallen och distribuera om mallen för att uppdatera resursen. 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
Använd `AlterTopics` API: t (till exempel via **Kafka-topics** CLI-verktyget) för att öka antalet partitioner. Mer information finns i [ändra Kafka-ämnen](http://kafka.apache.org/documentation/#basic_ops_modify_topic). 

## <a name="event-hubs-clients"></a>Event Hubs klienter
Nu ska vi titta på hur Event Hubs klienter beter sig när antalet partitioner uppdateras i en händelsehubben. 

När du lägger till en partition i ett befintligt jämnt NAV tar Event Hub-klienten emot en `MessagingException` från tjänsten som informerar klienterna om att entitetens metadata (entiteten är din Event Hub och metadata är partitionens information) har ändrats. Klienterna kommer automatiskt att öppna AMQP-länkarna igen, vilket sedan hämtar den ändrade metadata-informationen. Klienterna fungerar normalt.

### <a name="senderproducer-clients"></a>Avsändare/producerande klienter
Event Hubs innehåller tre avsändares alternativ:

- **Partitions sändare** – i det här scenariot skickar klienter händelser direkt till en partition. Även om partitioner är identifierbara och händelser kan skickas direkt till dem, rekommenderar vi inte det här mönstret. Att lägga till partitioner påverkar inte det här scenariot. Vi rekommenderar att du startar om program så att de kan identifiera nya partitioner som har lagts till. 
- **Partitionsnyckel-Sender** – i det här scenariot skickar klienter händelser med en nyckel så att alla händelser som tillhör den nyckeln slutar på samma partition. I det här fallet hash-kodar tjänsten nyckeln och vägarna till motsvarande partition. Uppdatering av partitioner kan orsaka problem som inte är i följd på grund av hash-ändringar. Om du bryr dig om att beställa bör du se till att ditt program förbrukar alla händelser från befintliga partitioner innan du ökar antalet partitioner.
- **Round-Robin-avsändare (standard)** – i det här scenariot avEvent Hubs tjänsten Robins händelserna mellan partitioner och använder också en algoritm för belastnings utjämning. Event Hubs tjänsten är medveten om antalet ändringar i partitionen och skickas till nya partitioner inom några sekunder efter att antalet partitioner ändrats.

### <a name="receiverconsumer-clients"></a>Mottagare/konsument klienter
Event Hubs tillhandahåller direkta mottagare och ett enkelt konsument bibliotek som kallas [händelse bearbetnings värd (gammal SDK)](event-hubs-event-processor-host.md)  eller [händelse processor (ny SDK)](event-processor-balance-partition-load.md).

- **Direkta mottagare** – direkta mottagare lyssnar på vissa partitioner. Deras körnings beteende påverkas inte när partitioner skalas ut för en Event Hub. Det program som använder direkt mottagare måste ta hand om att hämta de nya partitionerna och tilldela mottagarna på motsvarande sätt.
- **Värd för händelse bearbetning** – den här klienten uppdaterar inte entitetens metadata automatiskt. Därför skulle den inte hämta på att öka antalet partitioner. Om du återskapar en instans av en händelse processor kommer metadata för entiteten att hämtas, vilket i sin tur skapar nya blobbar för de partitioner som har lagts till. Redan befintliga blobbar påverkas inte. Att starta om alla instanser av händelse processorer rekommenderas för att säkerställa att alla instanser är medvetna om de partitioner som har lagts till och belastnings utjämning hanteras korrekt mellan konsumenter.

    Om du använder den gamla versionen av .NET SDK ([windowsazure. Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)) tar händelse processor värden bort en befintlig kontroll punkt vid omstart om antalet partitioner i kontroll punkten inte matchar antalet partitioner som hämtats från tjänsten. Det här beteendet kan påverka ditt program. 

## <a name="apache-kafka-clients"></a>Apache Kafka klienter
I det här avsnittet beskrivs hur Apache Kafka klienter som använder Kafka-slutpunkten för Azure Event Hubs beter sig när antalet partitioner uppdateras för en Event Hub. 

Kafka-klienter som använder Event Hubs med Apache Kafka-protokollet fungerar annorlunda än Event Hub-klienter som använder AMQP-protokollet. Kafka-klienterna uppdaterar sina metadata en gång i `metadata.max.age.ms` millisekunder. Du anger det här värdet i klient konfigurationerna. `librdkafka`Biblioteken använder också samma konfiguration. Uppdateringar av metadata meddelar klienterna om tjänst ändringar, inklusive antalet partitioner ökar. En lista över konfigurationer finns i [Apache Kafka konfigurationer för Event Hubs](apache-kafka-configurations.md).

### <a name="senderproducer-clients"></a>Avsändare/producerande klienter
Producenter dikterar alltid att sändnings begär Anden innehåller partitionens mål för varje uppsättning producerade poster. Därför görs all tillverkning partitionering på klient sidan med producentens vy över Broker-metadata. När de nya partitionerna har lagts till i producentens metadatacache är de tillgängliga för producent förfrågningar.

### <a name="consumerreceiver-clients"></a>Konsument/mottagare-klienter
När en konsument grupp medlem utför en uppdatering av metadata och hämtar de nyss skapade partitionerna initierar den medlemmen en grupp balansering. Konsument-metadata kommer att uppdateras för alla grupp medlemmar och de nya partitionerna tilldelas av den tilldelade ombalanserings ledaren.

## <a name="recommendations"></a>Rekommendationer

- Om du använder partitionsnyckel med dina producerande program och är beroende av nyckel-hash för att säkerställa ordning i en partition rekommenderas du att lägga till partitioner dynamiskt. 

    > [!IMPORTANT]
    > Medan befintliga data bevarar ordning, avbryts partitionens hashing för meddelanden som hashas efter att antalet partitioner ändrats på grund av att partitioner har lagts till.
- Att lägga till partitioner till ett befintligt ämne eller Event Hub-instans rekommenderas i följande fall:
    - När du använder standard metoden för att skicka händelser
     - Kafka standard partitionerings strategier, exempel – fästis för fästisar


## <a name="next-steps"></a>Nästa steg
Mer information om partitioner finns i [partitioner](event-hubs-scalability.md#partitions).

