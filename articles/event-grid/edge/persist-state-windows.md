---
title: Spara tillstånd i Windows-Azure Event Grid IoT Edge | Microsoft Docs
description: Sparat tillstånd i Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f38e23a3af1e2c81ee012a4f3c268cbff3fc1bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002825"
---
# <a name="persist-state-in-windows"></a>Sparat tillstånd i Windows

Ämnen och prenumerationer som skapas i Event Grid-modulen lagras som standard i behållar fil systemet. Utan persistence, om modulen har distribuerats om, kommer alla metadata som skapats att gå förlorade. Om du vill bevara data mellan distributioner och omstarter måste du spara data utanför behållar fil systemet. 

Som standard sparas endast metadata och händelser lagras fortfarande i minnet för bättre prestanda. Följ avsnittet beständiga händelser för att aktivera händelse beständighet.

Den här artikeln innehåller de steg som krävs för att distribuera Event Grid-modulen med persistence i Windows-distributioner.

> [!NOTE]
>Event Grid-modulen körs som en låg privilegie rad användar **ContainerUser** i Windows.

## <a name="persistence-via-volume-mount"></a>Persistence via volym montering

[Docker-volymer](https://docs.docker.com/storage/volumes/) används för att bevara data mellan distributioner. Om du vill montera en volym måste du skapa den med Docker-kommandon, ge behörigheter så att behållaren kan läsa, skriva till den och sedan distribuera modulen.

1. Skapa en volym genom att köra följande kommando:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Exempel:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Hämta värd katalogen som volymen mappar till genom att köra kommandot nedan

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Exempel:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Exempel på utdata:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Lägg till gruppen **användare** till värdet som påpekas av **monterings punkt** enligt följande:
    1. Starta Utforskaren.
    1. Navigera till mappen som är påpekad av **monterings punkt**.
    1. Högerklicka på och välj sedan **Egenskaper**.
    1. Välj **Säkerhet**.
    1. Under * grupp-eller användar namn väljer du **Redigera**.
    1. Välj **Lägg till**, ange `Users` , Välj **kontrol lera namn** och välj **OK**.
    1. Under *behörigheter för användare* väljer du **ändra** och sedan **OK**.
1. Använd **bindningar** för att montera volymen och distribuera om Event Grid modul från Azure Portal

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Ändra inte den andra delen av bind-värdet. Den pekar på en angiven plats i modulen. För Event Grid modul i Windows måste den vara **C: \\ app \\ metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>Beständighet via värd katalog montering

I stället för att montera en volym kan du skapa en katalog på värd systemet och montera den katalogen.

1. Skapa en katalog på värd fil systemet genom att köra följande kommando.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Exempel:

   ```sh
   mkdir C:\myhostdir
   ```
1. Använd **bindningar** för att montera katalogen och distribuera om Event Grid-modulen från Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Ändra inte den andra delen av bind-värdet. Den pekar på en angiven plats i modulen. För Event Grid modulen i Windows måste den vara **C: \\ app- \\ metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Kvarhåll händelser

Om du vill aktivera händelse persistence måste du först aktivera händelser som beständiges via volym montering eller värd katalog montering med hjälp av ovanstående avsnitt.

Viktiga saker att tänka på när du behåller händelser:

* Beständiga händelser aktive ras för en prenumeration per händelse och är valbar när en volym eller katalog har monterats.
* Händelse persistence konfigureras för en händelse prenumeration när den skapas och kan inte ändras när händelse prenumerationen har skapats. Om du vill växla händelse beständighet måste du ta bort och återskapa händelse prenumerationen.
* Beständiga händelser är nästan alltid långsammare än i minnes åtgärder, men hastigheten är mycket beroende av enhetens egenskaper. Kompromissen mellan hastighet och tillförlitlighet är till för alla meddelande system men blir bara en märkbart synlig i stor skala.

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