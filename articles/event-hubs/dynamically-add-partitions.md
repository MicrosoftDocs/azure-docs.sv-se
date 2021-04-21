---
title: Lägga till partitioner dynamiskt till en händelsehubb i Azure Event Hubs
description: Den här artikeln visar hur du dynamiskt lägger till partitioner i en händelsehubb i Azure Event Hubs.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: aeeee1bcefe58b006dac0b6913aaa609cbeefb8c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775128"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Lägga till partitioner dynamiskt till en händelsehubb (Apache Kafka ämne) i Azure Event Hubs
Event Hubs tillhandahåller meddelandeströmning via ett partitionerat konsumentmönster där varje konsument endast läser en specifik delmängd, eller partition, av meddelandeströmmen. Det här mönstret gör det möjligt att skala horisontellt för händelsebearbetning och tillhandahåller andra strömfokuserade funktioner som inte är tillgängliga i köer och ämnen. En partition är en ordnad sekvens av händelser som hålls kvar i en händelsehubb. När nyare händelser anländer läggs de till i slutet av den här sekvensen. Mer information om partitioner i allmänhet finns i [Partitioner](event-hubs-scalability.md#partitions)

Du kan ange antalet partitioner när du skapar en händelsehubb. I vissa fall kan du behöva lägga till partitioner när händelsehubben har skapats. I den här artikeln beskrivs hur du dynamiskt lägger till partitioner i en befintlig händelsehubb. 

> [!IMPORTANT]
> Dynamiska tillägg av partitioner är bara tillgängliga på **dedikerade** Event Hubs kluster.

> [!NOTE]
> För Apache Kafka mappar en **händelsehubb** till ett **Kafka-ämne**. Fler mappningar mellan Azure Event Hubs och Apache Kafka finns i [Kafka och Event Hubs konceptuell mappning](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>Uppdatera antalet partitioner
Det här avsnittet visar hur du uppdaterar antalet partitioner för en händelsehubb på olika sätt (PowerShell, CLI och så vidare).

### <a name="powershell"></a>PowerShell
Använd [PowerShell-kommandot Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) för att uppdatera partitioner i en händelsehubb. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
Använd [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az_eventhubs_eventhub_update) CLI-kommandot för att uppdatera partitioner i en händelsehubb. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Resource Manager-mall
Uppdatera värdet för `partitionCount` egenskapen i Resource Manager mallen och distribuera mallen igen för att uppdatera resursen. 

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
Använd `AlterTopics` API:et (till exempel via **CLI-verktyget kafka-topics)** för att öka antalet partitioner. Mer information finns i [Modifying Kafka topics](http://kafka.apache.org/documentation/#basic_ops_modify_topic). 

## <a name="event-hubs-clients"></a>Event Hubs klienter
Nu ska vi titta på hur Event Hubs beter sig när antalet partitioner uppdateras på en händelsehubb. 

När du lägger till en partition till en befintlig jämn hubb tar händelsehubbens klient emot en från tjänsten som informerar klienterna om att entitetsmetadata (entiteten är din händelsehubb och metadata är `MessagingException` partitionsinformationen) har ändrats. Klienterna öppnar automatiskt AMQP-länkarna, som sedan hämtar den ändrade metadatainformationen. Klienterna fungerar sedan som de ska.

### <a name="senderproducer-clients"></a>Avsändare/producentklienter
Event Hubs tre avsändaralternativ:

- **Partitionsavsändare** – I det här scenariot skickar klienter händelser direkt till en partition. Även om partitioner kan identifieras och händelser kan skickas direkt till dem rekommenderar vi inte det här mönstret. Att lägga till partitioner påverkar inte det här scenariot. Vi rekommenderar att du startar om program så att de kan identifiera nyligen tillagda partitioner. 
- **Partitionsnyckelsavsändare** – i det här scenariot skickar klienter händelserna med en nyckel så att alla händelser som hör till den nyckeln hamnar i samma partition. I det här fallet hashar tjänsten nyckeln och vägarna till motsvarande partition. Uppdateringen av antalet partitioner kan orsaka fel i ordning på grund av ändring av hash-kod. Så om du bryr dig om ordning bör du se till att programmet använder alla händelser från befintliga partitioner innan du ökar antalet partitioner.
- **Avsändare av resursallokering (standard)** – I det här scenariot använder Event Hubs resursallokeringstjänst händelserna mellan partitioner och använder även en belastningsutjämningsalgoritm. Event Hubs är medveten om ändringar i antalet partitioner och skickar till nya partitioner inom några sekunder efter att antalet partitioner har ändrats.

### <a name="receiverconsumer-clients"></a>Mottagare/konsumentklienter
Event Hubs direktmottagare och ett bibliotek för enkel konsument som kallas [Event Processor Host (gammal SDK)](event-hubs-event-processor-host.md) eller [Händelseprocessor (ny SDK).](event-processor-balance-partition-load.md)

- **Direktmottagare** – De direkta mottagarna lyssnar på specifika partitioner. Deras körningsbeteende påverkas inte när partitioner skalas ut för en händelsehubb. Programmet som använder direkta mottagare måste ta hand om att hämta de nya partitionerna och tilldela mottagarna därefter.
- **Värd för händelseprocessor** – Den här klienten uppdaterar inte entitetens metadata automatiskt. Det skulle alltså inte öka antalet partitioner. När du återskapar en instans av händelseprocessorn hämtas en entitetsmetadata, som i sin tur skapar nya blobar för de nya partitionerna. Befintliga blobar påverkas inte. Vi rekommenderar att du startar om alla händelseprocessorinstanser för att säkerställa att alla instanser är medvetna om de nya partitionerna och att belastningsutjämningen hanteras korrekt bland konsumenterna.

    Om du använder den gamla versionen av .NET SDK ([WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)) tar händelseprocessorvärden bort en befintlig kontrollpunkt vid omstart om partitionsantalet i kontrollpunkten inte matchar det partitionsantal som hämtas från tjänsten. Det här beteendet kan påverka ditt program. 

## <a name="apache-kafka-clients"></a>Apache Kafka klienter
Det här avsnittet beskriver Apache Kafka klienter som använder Kafka-slutpunkten för Azure Event Hubs beter sig när antalet partitioner uppdateras för en händelsehubb. 

Kafka-klienter som använder Event Hubs med Apache Kafka-protokollet beter sig annorlunda än händelsehubbklienter som använder AMQP-protokollet. Kafka-klienter uppdaterar sina metadata en `metadata.max.age.ms` gång per millisekunder. Du anger det här värdet i klientkonfigurationerna. Biblioteken `librdkafka` använder också samma konfiguration. Metadatauppdateringar informerar klienterna om tjänständringar, inklusive antalet partitioner ökar. En lista över konfigurationer finns i [Apache Kafka konfigurationer för Event Hubs](apache-kafka-configurations.md).

### <a name="senderproducer-clients"></a>Avsändare/producentklienter
Producenter dikterar alltid att skicka begäranden innehåller partitionsmålet för varje uppsättning skapade poster. Därför görs all partitionering på klientsidan med producentens vy över den asyn av den asyn av asyn meddelandeköens metadata. När de nya partitionerna har lagts till i producentens metadatavy blir de tillgängliga för producentbegäranden.

### <a name="consumerreceiver-clients"></a>Konsument-/mottagarklienter
När en medlem i konsumentgruppen utför en metadatauppdatering och hämtar de nya partitionerna initierar medlemmen en ombalansering av gruppen. Konsumentmetadata uppdateras sedan för alla gruppmedlemmar och de nya partitionerna tilldelas av den tilldelade ombalanseringsledare.

## <a name="recommendations"></a>Rekommendationer

- Om du använder partitionsnyckel med dina producentprogram och är beroende av nyckel-hashning för att säkerställa ordningen i en partition, rekommenderas inte dynamiskt tillägg av partitioner. 

    > [!IMPORTANT]
    > Medan befintliga data bevarar ordningsföljden bryts partitionshashar för meddelanden som hashas efter att antalet partitioner har ändras på grund av tillägg av partitioner.
- Vi rekommenderar att du lägger till partition i ett befintligt ämne eller en händelsehubbinstans i följande fall:
    - När du använder standardmetoden för att skicka händelser
     - Kafka-standardpartitioneringsstrategier, exempel – Sticky Assignor-strategi


## <a name="next-steps"></a>Nästa steg
Mer information om partitioner finns i [Partitioner.](event-hubs-scalability.md#partitions)
