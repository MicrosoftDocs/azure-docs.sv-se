---
title: Konfigurera modulen API-proxy – Azure IoT Edge | Microsoft Docs
description: Lär dig hur du anpassar API-proxy-modulen för IoT Edge Gateway-hierarkier.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: f55c3a1f699f8a087eb97eaba347a3f21c124cc9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307324"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>Konfigurera API-proxy-modulen för gateway-hierarkins scenario (för hands version)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

API-proxy-modulen gör det möjligt för IoT Edge enheter att skicka HTTP-förfrågningar via gatewayer i stället för att göra direkta anslutningar till moln tjänster. Den här artikeln vägleder dig genom konfigurations alternativen så att du kan anpassa modulen så att den stöder Gateway-hierarkins krav.

>[!NOTE]
>Den här funktionen kräver IoT Edge version 1,2, som finns i offentlig för hands version, som kör Linux-behållare.

I vissa nätverks arkitekturer har IoT Edge enheter bakom gatewayer inte direkt åtkomst till molnet. Alla moduler som försöker ansluta till moln tjänster kommer att Miss lyckas. Modulen API-proxy stöder underordnade IoT Edge enheter i den här konfigurationen genom att dirigera om modul anslutningar för att gå igenom lagren i en gateway-hierarki i stället. Det ger ett säkert sätt för klienter att kommunicera med flera tjänster via HTTPS utan tunnel trafik, utan genom att avsluta anslutningarna på varje lager. API-proxy-modulen fungerar som en proxy-modul mellan IoT Edge enheter i en gateway-hierarki tills den når IoT Edge-enheten på det översta lagret. I det här läget hanterar tjänster som körs på det översta lagret IoT Edge enhet dessa begär Anden och API-proxy-modulen proxyservrar all HTTP-trafik från lokala tjänster till molnet via en enda port.

API-proxy-modulen kan aktivera många scenarier för gateway-hierarkier, inklusive tillåta att lägre lager enheter hämtar behållar avbildningar eller push-blobbar till lagring. Konfigurationen av proxy-reglerna definierar hur data dirigeras. I den här artikeln beskrivs flera vanliga konfigurations alternativ.

## <a name="deploy-the-proxy-module"></a>Distribuera Proxy-modulen

Modulen API-proxy är tillgänglig från Microsoft Container Registry (MCR): `mcr.microsoft.com/azureiotedge-api-proxy:1.0` .

Du kan också distribuera modulen API-proxy direkt från Azure Marketplace: [IoT Edge API-proxy](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview).

## <a name="understand-the-proxy-module"></a>Förstå modulen proxy

API-proxy-modulen utnyttjar en nginx-omvänd proxy för att dirigera data via nätverks lager. En proxy är inbäddad i modulen, vilket innebär att modul avbildningen måste ha stöd för proxykonfigurationen. Om proxyservern till exempel lyssnar på en viss port måste modulen ha den porten öppen.

Proxyn startar med en standard konfigurations fil som är inbäddad i modulen. Du kan skicka en ny konfiguration till modulen från molnet med hjälp av sin [modul](../iot-hub/iot-hub-devguide-module-twins.md). Dessutom kan du använda miljövariabler för att aktivera eller inaktivera konfigurations inställningar vid distributions tillfället.

Den här artikeln fokuserar först på standard konfigurations filen och hur du använder miljövariabler för att aktivera dess inställningar. Sedan diskuterar vi anpassningen av konfigurations filen i slutet.

### <a name="default-configuration"></a>Standardkonfiguration

API-proxy-modulen levereras med en standard konfiguration som stöder vanliga scenarier och möjliggör anpassning. Du kan styra standard konfigurationen via miljövariabler i modulen.

För närvarande är standard miljö variablerna:

| Miljövariabel | Description |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | Visa en lista med alla variabler som du vill uppdatera i en kommaavgränsad lista. Det här steget förhindrar oavsiktlig ändring av felaktiga konfigurations inställningar.
| `NGINX_DEFAULT_PORT` | Ändrar porten som nginx proxy lyssnar på. Om du uppdaterar den här miljövariabeln ser du till att porten du väljer också exponeras i modulen Dockerfile och deklareras som en port bindning i distributions manifestet.<br><br>Standardvärdet är 443.<br><br>När den distribueras från Azure Marketplace uppdateras standard porten till 8000 för att förhindra konflikter med edgeHub-modulen. Mer information finns i [minimera öppna portar](#minimize-open-ports). |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Adress till routning av Docker-begäranden. Ändra den här variabeln på enheten på den översta nivån så att den pekar på Registry-modulen.<br><br>Standardvärdet är ett överordnat värdnamn. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | Adress för att dirigera BLOB Registry-begäranden. Ändra den här variabeln på enheten på den översta nivån så att den pekar på Blob Storage-modulen.<br><br>Standardvärdet är ett överordnat värdnamn. |

## <a name="minimize-open-ports"></a>Minimera öppna portar

För att minimera antalet öppna portar bör API-proxy-modulen vidarebefordra all HTTPS-trafik (port 443), inklusive trafik som riktar sig till edgeHub-modulen. Modulen API-proxy är konfigurerad som standard för att omdirigera all edgeHub-trafik på port 443.

Använd följande steg för att konfigurera distributionen för att minimera öppna portar:

1. Uppdatera inställningarna för edgeHub-modulen så att de inte binds på port 443, annars kommer port bindnings konflikter. Som standard binder edgeHub-modulen på portarna 443, 5671 och 8883. Ta bort port 443-bindningen och lämna de andra två på plats:

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Konfigurera API-proxy-modulen för bindning på port 443.

   1. Ange värdet för **NGINX_DEFAULT_PORT** -miljövariabeln till `443` .
   1. Uppdatera behållaren skapa alternativ för bindning på port 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

Om du inte behöver minimera öppna portar kan du låta edgeHub-modulen använda port 443 och konfigurera API-proxy-modulen att lyssna på en annan port. API-proxy-modulen kan till exempel lyssna på port 8000 genom att ange värdet för **NGINX_DEFAULT_PORT** -miljövariabeln till `8000` och skapa en port bindning för port 8000.

## <a name="enable-container-image-download"></a>Aktivera hämtning av behållare avbildning

Ett vanligt användnings fall för API-modulen är att aktivera IoT Edge enheter i lägre lager för att hämta behållar avbildningar. I det här scenariot används [Docker-register-modulen](https://hub.docker.com/_/registry) för att hämta behållar avbildningar från molnet och cachelagra dem på det översta lagret. API-proxyn reläerar alla HTTPS-begäranden för att ladda ned en behållar avbildning från de nedre skikten som ska hanteras av register-modulen i det översta lagret.

Det här scenariot kräver att underordnade IoT Edge enheter pekar på domän namnet `$upstream` följt av API-proxyns port nummer i stället för behållar registret i en avbildning. Exempel: `$upstream:8000/azureiotedge-api-proxy:1.0`.

Det här användnings fallet visas i självstudien [skapa en hierarki med IoT Edge enheter med hjälp av gatewayer](tutorial-nested-iot-edge.md).

Konfigurera följande moduler på det **översta lagret**:

* En Docker-register-modul
  * Konfigurera modulen med ett minnes minnes minnes namn som *register* och exponera en port i modulen för att ta emot begär Anden.
  * Konfigurera modulen att mappa till behållar registret.
* Modul för API-proxy
  * Konfigurera följande miljövariabler:

    | Name | Värde |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | Registry-modulens namn och öppna port. Till exempel `registry:5000`. |
    | `NGINX_DEFAULT_PORT` | Porten som nginx proxy lyssnar på efter begär Anden från underordnade enheter. Till exempel `8000`. |

  * Konfigurera följande createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Konfigurera följande modul på ett **lägre lager** i det här scenariot:

* Modul för API-proxy
  * Konfigurera följande miljövariabler:

    | Name | Värde |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Porten som nginx proxy lyssnar på efter begär Anden från underordnade enheter. Till exempel `8000`. |

  * Konfigurera följande createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>Aktivera BLOB-uppladdning

Ett annat användnings fall för API-modulen är att aktivera IoT Edge enheter i lägre lager för att ladda upp blobbar. Det här användnings fallet aktiverar fel söknings funktioner på lägre lager enheter som att ladda upp modul loggar eller ladda upp support paketet.

I det här scenariot används [Azure-Blob Storage i IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) -modulen på det översta lagret för att hantera generering och uppladdning av BLOB.

Konfigurera följande moduler på det **översta lagret**:

* En Azure-Blob Storage i IoT Edge-modulen.
* Modul för API-proxy
  * Konfigurera följande miljövariabler:

    | Name | Värde |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Blob Storage-modulens namn och öppen port. Till exempel `azureblobstorageoniotedge:1102`. |
    | `NGINX_DEFAULT_PORT` | Porten som nginx proxy lyssnar på efter begär Anden från underordnade enheter. Till exempel `8000`. |

  * Konfigurera följande createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Konfigurera följande modul på ett **lägre lager** i det här scenariot:

* Modul för API-proxy
  * Konfigurera följande miljövariabler:

    | Name | Värde |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Porten som nginx proxy lyssnar på efter begär Anden från underordnade enheter. Till exempel `8000`. |

  * Konfigurera följande createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Använd följande steg för att ladda upp support paketet eller logg filen till Blob Storage-modulen som finns på det översta lagret:

1. Skapa en BLOB-behållare med antingen Azure Storage Explorer eller REST-API: er. Mer information finns i [lagra data på gränsen med Azure Blob Storage på IoT Edge](how-to-store-data-blob.md).
1. Begär en logg-eller support paket uppladdning enligt stegen i [Hämta loggar från IoT Edge distributioner](how-to-retrieve-iot-edge-logs.md), men Använd domän namnet `$upstream` och den öppna proxy-porten i stället för adressen till Blob Storage-modulen. Exempel:

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>Redigera proxykonfigurationen

En standard konfigurations fil är inbäddad i API-proxy-modulen, men du kan skicka en ny konfiguration till modulen via molnet med hjälp av modulen dubbla.

När du skriver din egen konfiguration kan du fortfarande använda miljön för att justera inställningarna per distribution. Använd följande syntax:

* Används `${MY_ENVIRONMENT_VARIABLE}` för att hämta värdet för en miljö variabel.
* Använd villkorliga uttryck för att aktivera eller inaktivera inställningar baserat på värdet för en miljö variabel:

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

När API-proxyn parsar en proxykonfiguration, ersätter den först alla miljövariabler som anges i `PROXY_CONFIG_ENV_VAR_LIST` med sina angivna värden med hjälp av ersättning. Sedan ersätts allt mellan ett `#if_tag` och- `#endif_tag` par. Modulen tillhandahåller sedan den parsade konfigurationen till den omvända proxyn för nginx.

Använd följande steg för att uppdatera proxykonfigurationen dynamiskt:

1. Skriv konfigurations filen. Du kan använda standard mal len som referens: [nginx_default_config. conf](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. Kopiera texten i konfigurations filen och konvertera den till base64.
1. Klistra in den kodade konfigurations filen som värde för `proxy_config` önskad egenskap i modulen.

   ![Klistra in kodad config-fil som värde för egenskapen proxy_config](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>Nästa steg

Använd modulen API-proxy för att [ansluta en underordnad IoT Edge enhet till en Azure IoT Edge Gateway](how-to-connect-downstream-iot-edge-device.md).