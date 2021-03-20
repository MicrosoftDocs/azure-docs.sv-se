---
title: Sparat tillstånd i Linux-Azure Event Grid IoT Edge | Microsoft Docs
description: Bevara metadata i Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 92333c2528303a6fa53fa30f47def33c33235d39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171490"
---
# <a name="persist-state-in-linux"></a>Sparat tillstånd i Linux

Ämnen och prenumerationer som skapas i Event Grid-modulen lagras som standard i behållar fil systemet. Utan persistence, om modulen har distribuerats om, kommer alla metadata som skapats att gå förlorade. Om du vill bevara data mellan distributioner och omstarter måste du spara data utanför behållar fil systemet.

Som standard sparas endast metadata och händelser lagras fortfarande i minnet för bättre prestanda. Följ avsnittet beständiga händelser för att aktivera händelse beständighet.

Den här artikeln innehåller steg för att distribuera Event Grid-modulen med persistence i Linux-distributioner.

> [!NOTE]
>Event Grid-modulen körs som en låg privilegie rad användare med UID `2000` och namn `eventgriduser` .

## <a name="persistence-via-volume-mount"></a>Persistence via volym montering

 [Docker-volymer](https://docs.docker.com/storage/volumes/) används för att bevara data mellan distributioner. Du kan låta Docker skapa en namngiven volym automatiskt som en del av distributionen av Event Grid-modulen. Det här alternativet är det enklaste alternativet. Du kan ange det volym namn som ska skapas i avsnittet **bindningar** på följande sätt:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Ändra inte den andra delen av bind-värdet. Den pekar på en angiven plats i modulen. För modulen Event Grid i Linux måste den vara **/app/metadataDb**.

Följande konfiguration leder till exempel till skapandet av volymen **egmetadataDbVol** där metadata kommer att sparas.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

I stället för att montera en volym kan du skapa en katalog på värd systemet och montera den katalogen.

## <a name="persistence-via-host-directory-mount"></a>Beständighet via värd katalog montering

I stället för en Docker-volym har du också möjlighet att montera en Host-mapp.

1. Skapa först en användare med namnet **eventgriduser** och ID **2000** på värddatorn genom att köra följande kommando:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Skapa en katalog på värd fil systemet genom att köra följande kommando.

   ```sh
   md <your-directory-name-here>
   ```

    Om du till exempel kör följande kommando skapas en katalog med namnet **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Sedan gör du **eventgriduser** ägare av den här mappen genom att köra följande kommando.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Exempel:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Använd **bindningar** för att montera katalogen och distribuera om Event Grid-modulen från Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    Exempel:

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >Ändra inte den andra delen av bind-värdet. Den pekar på en angiven plats i modulen. För modulen Event Grid i Linux måste den vara **/app/metadataDb** och **/app/eventsDb**


## <a name="persist-events"></a>Kvarhåll händelser

Om du vill aktivera händelse persistence måste du först aktivera beständiga metadata antingen via volym montering eller värd katalog montering med hjälp av ovanstående avsnitt.

Viktiga saker att tänka på när du behåller händelser:

* Beständiga händelser aktive ras för en prenumeration per händelse och är valbar när en volym eller katalog har monterats.
* Händelse persistence konfigureras för en händelse prenumeration när den skapas och kan inte ändras när händelse prenumerationen har skapats. Om du vill växla händelse beständighet måste du ta bort och återskapa händelse prenumerationen.
* Beständiga händelser är nästan alltid långsammare än i minnes åtgärder, men hastigheten är mycket beroende av enhetens egenskaper. Kompromissen mellan hastighet och tillförlitlighet är till för alla meddelande system, men vanligt vis blir det bara en märkbart synligt i stor skala.

Om du vill aktivera händelse beständighet för en händelse prenumeration anger `persistencePolicy` du till `true` :

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
