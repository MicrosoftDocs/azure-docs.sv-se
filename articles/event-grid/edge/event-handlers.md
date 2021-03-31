---
title: Händelse hanterare och destinationer – Azure Event Grid IoT Edge | Microsoft Docs
description: Händelse hanterare och mål i Event Grid på kant
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 18e2b944f60ebdf8a1b0c60ba446a13df13134c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171592"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Händelse hanterare och mål i Event Grid på kant

En händelse hanterare är den plats där händelsen för ytterligare åtgärder eller bearbetning av händelsen bearbetas. Med modulen Event Grid i Edge kan händelse hanteraren finnas på samma Edge-enhet, en annan enhet eller i molnet. Du kan använda en webhook för att hantera händelser eller skicka händelser till en av de inbyggda hanterarna som Azure Event Grid.

Den här artikeln innehåller information om hur du konfigurerar var och en.

## <a name="webhook"></a>WebHook

Om du vill publicera till en webhook-slutpunkt anger `endpointType` du till `WebHook` och anger:

* endpointUrl: URL för webhook-slutpunkten

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Om du vill publicera till en Azure Event Grid moln slut punkt ställer `endpointType` du in på `eventGrid` och anger:

* endpointUrl: URL för Event Grid avsnittet i molnet
* sasKey: Event Grid ämnets SAS-nyckel
* topicName: namn för att stämpla alla utgående händelser till Event Grid. Ämnes namnet är användbart när du publicerar i ett Event Grid domän ämne.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                 "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
    }
   ```

## <a name="iot-edge-hub"></a>IoT Edge hubb

Om du vill publicera till en Edge Hub-modul ställer `endpointType` du in på `edgeHub` och anger:

* outputName: utdata som Event Grid-modulen dirigerar händelser som matchar den här prenumerationen på edgeHub. Till exempel kommer händelser som matchar prenumerationen nedan att skrivas till/messages/modules/eventgridmodule/outputs/sampleSub4.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "edgeHub",
              "properties": {
                "outputName": "sampleSub4"
              }
            }
          }
        }
    ```

## <a name="event-hubs"></a>Event Hubs

Om du vill publicera till en Event Hub ställer `endpointType` du in på `eventHub` och anger:

* connectionString: anslutnings sträng för den specifika Händelsehubben som du har genererat via en princip för delad åtkomst.

    >[!NOTE]
    > Anslutnings strängen måste vara en speciell entitet. Det går inte att använda en anslutnings sträng för namn områden. Du kan skapa en entitets-Specific-anslutningssträng genom att navigera till den speciella händelsehubben som du vill publicera till i Azure-portalen och klicka på **principer för delad åtkomst** för att generera en ny entitets connecection sträng.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Service Bus-köer

Om du vill publicera till en Service Bus kö anger `endpointType` du till `serviceBusQueue` och anger:

* connectionString: anslutnings sträng för den specifika Service Bus kön som du har genererat via en princip för delad åtkomst.

    >[!NOTE]
    > Anslutnings strängen måste vara en speciell entitet. Det går inte att använda en anslutnings sträng för namn områden. Generera en entitets-Specific-anslutningssträng genom att gå till den angivna Service Buss kön som du vill publicera till i Azure-portalen och klicka på **principer för delad åtkomst** för att generera en ny entitets Specific connecection-sträng.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Avsnitt om Service Bus

Om du vill publicera till ett Service Bus ämne anger `endpointType` du till `serviceBusTopic` och anger:

* connectionString: anslutnings sträng för det specifika Service Bus ämne som du har genererat via en princip för delad åtkomst.

    >[!NOTE]
    > Anslutnings strängen måste vara en speciell entitet. Det går inte att använda en anslutnings sträng för namn områden. Generera en entitets-speciell anslutnings sträng genom att gå till den aktuella Service Bus avsnittet som du vill publicera till i Azure-portalen och klicka på **principer för delad åtkomst** för att generera en ny entitets Specific connecection-sträng.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Lagringsköer

För att publicera till en lagrings kö, ange  `endpointType` till `storageQueue` och ange:

* queueName: namnet på lagrings kön som du publicerar till.
* connectionString: anslutnings sträng för lagrings kontot som lagrings kön finns i.

    >[!NOTE]
    > Unline Event Hubs, Service Bus köer och Service Bus ämnen, den anslutnings sträng som används för lagrings köer är inte entitets-specifika. I stället måste den, men anslutnings strängen för lagrings kontot.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```