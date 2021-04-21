---
title: Konfigurera Microsoft Ansluten cache för enhetsuppdatering för Azure IoT Hub | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Översikt över Microsoft Ansluten cache för enhetsuppdatering för Azure IoT Hub
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 6e7b8d567034cc9557a2d9fcec4afbffa878cf75
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811839"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Konfigurera Microsoft Ansluten cache för enhetsuppdatering för Azure IoT Hub

Microsoft Ansluten cache distribueras till gateways Azure IoT Edge som en Azure IoT Edge modul. Precis som Azure IoT Edge moduler används variabler i MCC-modulens distributionsmiljö och alternativ för att skapa containrar för att konfigurera Microsoft Ansluten cache-moduler.  Det här avsnittet definierar de miljövariabler och alternativ för att skapa containrar som krävs för att en kund ska kunna distribuera Microsoft Ansluten cache-modulen för användning av enhetsuppdatering för Azure IoT Hub.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Distributionsinformation Ansluten cache Azure IoT Edge Microsoft Ansluten cache Azure IoT Edge-modulen

Det är administratörens eget Ansluten cache att namnge Microsoft Ansluten cache modulen. Det finns inga andra modul- eller tjänstinteraktioner som förlitar sig på namnet på modulen för kommunikation. Dessutom är den överordnade underordnade relationen för Microsoft Ansluten cache-servrarna inte beroende av det här modulnamnet, utan I stället FQDN- eller IP-adressen för Azure IoT Edge-gatewayen som har konfigurerats enligt tidigare diskussion.

Microsoft Ansluten cache Azure IoT Edge-modulens miljövariabler används för att skicka grundläggande modulidentitetsinformation och funktionella modulinställningar till containern.

| Variabelnamn                 | Värdeformat                           | Obligatorisk/valfri | Funktioner                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| Kund                   | GUID för Azure-prenumerations-ID             | Obligatorisk          | Det här är kundens nyckel, som ger säker<br>autentisering av cachenoden till Leveransoptimering<br>Tjänster.<br>Krävs för att modulen ska fungera. |
| CACHE_NODE_ID                 | CACHEnod-ID GUID                     | Obligatorisk          | Identifierar Unikt Microsoft-Ansluten cache<br>till Leveransoptimering Services.<br>Krävs i ordning<br> för modulen ska fungera. |
| CUSTOMER_KEY                  | Kundnyckel-GUID                     | Obligatorisk          | Det här är kundens nyckel, som ger en säker<br>autentisering av cachenoden till Leveransoptimering Services.<br>Krävs för att modulen ska fungera.|
| STORAGE_ *N* _SIZE_GB           | Där N är cacheenheten   | Obligatorisk          | Ange upp till 9 enheter för att cachelagra innehåll och ange maximalt utrymme i<br>Gigabyte som ska allokeras för innehåll på varje cacheenhet. Exempel:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>Antalet enheten måste matcha de angivna cacheenhetsbindningsvärdena<br>i containern skapa alternativ MicrosoftConnectedCache *N* värde<br>Den minsta storleken på cacheminnet är 10 GB.|
| UPSTREAM_HOST                 | FQDN/IP                                | Valfritt          | Det här värdet kan ange en överordnad Microsoft Connected<br>Cachenod som fungerar som proxy om den Ansluten cache noden<br> är frånkopplad från Internet. Den här inställningen används för att stödja<br> Kapslat IoT-scenario.<br>**Obs!** Microsoft Ansluten cache lyssnar på http-standardport 80.|
| UPSTREAM_PROXY                | FQDN/IP:PORT                           | Valfritt          | Den utgående Internetproxy.<br>Detta kan också vara DMZ-proxyn om ett ISA 95-nätverk. |
| CACHEABLE_CUSTOM_ *N* _HOST     | VÄRD/IP<br>FQDN                        | Valfritt          | Krävs för att stödja anpassade paketdatabaser.<br>Lagringsplatsen kan finnas lokalt eller på Internet.<br>Det finns ingen gräns för hur många anpassade värdar som kan konfigureras.<br><br>Exempel:<br>Namn = CACHEABLE_CUSTOM_1_HOST Värde = packages.foo.com<br> Namn = CACHEABLE_CUSTOM_2_HOST värde = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Valfritt          | Krävs för att stödja anpassade paketdatabaser.<br>Det här värdet kan användas som ett alias och används av cacheservern för referens<br>olika DNS-namn. Värdnamnet för lagringsplatsens innehåll kan till exempel packages.foo.com,<br>men för olika regioner kan det finnas ytterligare ett prefix som läggs till i värdnamnet<br>som westuscdn.packages.foo.com och eastuscdn.packages.foo.com.<br>Genom att ange det kanoniska aliaset ser du till att innehållet inte dupliceras<br>för innehåll som kommer från samma värd, men olika CDN-källor.<br>Formatet för det kanoniska värdet är inte viktigt, men det måste vara unikt för värden.<br>Det kan vara enklast att ange värdet så att det matchar värdvärdet.<br><br>Exempel som baseras på exempel för anpassad värd ovan:<br>Name = CACHEABLE_CUSTOM_1_CANONICAL Value = foopackages<br> Namn = CACHEABLE_CUSTOM_2_CANONICAL värde = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | Sant eller falskt                          | Valfritt          | Aktiverar visning av sammanfattningsrapporten på det lokala nätverket eller Internet.<br>Användning av en API-nyckel (beskrivs senare) krävs för att visa sammanfattningsrapporten om den är inställd på sant. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| Sant eller falskt                          | Valfritt          | Aktiverar visning av sammanfattningsrapport i det lokala nätverket eller Internet utan<br>användning av API-nyckel från valfri enhet i nätverket. Använd om du inte vill låsa åtkomsten<br>för att visa sammanfattningsdata för cacheservern via webbläsaren. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Alternativ för att Ansluten cache Azure IoT Edge container för Microsoft Ansluten cache Azure IoT Edge modul

Alternativ för att skapa containrar för MCC-moduldistribution ger kontroll över inställningarna för lagring och portar som används av MCC-modulen. Det här är listan över nödvändiga variabler som skapats av containern som används för att distribuera MCC.

### <a name="container-to-host-os-drive-mappings"></a>Container som värd för mappningar av operativsystemenhet

Krävs för att mappa containerlagringsplatsen till lagringsplatsen på disk.< upp till nio platser kan anges.

>[!Note]
>Antalet enheten måste matcha cacheenhetsbindningsvärdena som anges i miljövariabeln STORAGE_ *N* _SIZE_GB värdet, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Container som värd för TCP-portmappningar

Det här alternativet anger den externa datorns HTTP-port som MCC lyssnar på för innehållsbegäranden. Standardvärdet för HostPort är port 80 och andra portar stöds inte just nu eftersom ADU-klienten skickar begäranden på port 80 i dag. TCP-port 8081 är den interna containerport som MCC lyssnar på och inte kan ändras.

### <a name="container-service-tcp-port-mappings"></a>TCP-portmappningar för containertjänst

Microsoft Ansluten cache-modulen har en .NET Core-tjänst som används av cachelagringsmotorn för olika funktioner.

>[!Note]
>För att stödja Kapslad Azure IoT-gräns måste HostPort inte anges till 5000 eftersom registerproxymodulen redan lyssnar på värdporten 5000.


Exempel på alternativ för att skapa containrar

```json
{
    "HostConfig": {
        "Binds": [
            "/microsoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="microsoft-connected-cache-summary-report"></a>Microsoft Ansluten cache sammanfattningsrapport

Sammanfattningsrapporten är för närvarande det enda sättet för en kund att visa cachelagringsdata för Microsoft Ansluten cache-instanser som distribuerats till Azure IoT Edge gatewayer. Rapporten genereras med 15 sekunders intervall och innehåller genomsnittlig statistik för perioden samt aggregerad statistik för modulens livslängd. De viktigaste statistikerna som kunder är intresserade av är:

* **hitBytes** – Det här är summan av de byte som levereras och som kom direkt från cachen.
* **missBytes** – Det här är summan av de byte som levereras som Microsoft Ansluten cache behövde ladda ned från CDN för att se cachen.
* **bytesbyte –** Det här är summan av hitBytes och missBytes och är det totala antalet byte som levereras till klienter.
* **hitRatioBytes** – Det här är förhållandet mellan hitBytes och egressBytes.  Om 100 % av antalet bytebyte som levererades under en period var lika med hitBytes skulle detta till exempel vara 1.


Sammanfattningsrapporten finns på Ersätt `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` med \<Azure IoT Edge Gateway IP\> IP-adressen eller värdnamnet för din IoT Edge gateway. (se information om miljövariabler för information om den här rapportens synlighet).