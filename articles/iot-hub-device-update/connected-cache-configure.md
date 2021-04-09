---
title: Konfigurera Microsoft Connected cache för enhets uppdatering för Azure IoT Hub | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Översikt över Microsoft Connected cache för enhets uppdatering för Azure IoT Hub
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 2903407f88b57a7be948cdeb0610e6d65df975b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101663915"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Konfigurera Microsoft Connected cache för enhets uppdatering för Azure IoT Hub

Microsoft Connected cache distribueras till Azure IoT Edge gatewayer som en Azure IoT Edge modul. Precis som andra Azure IoT Edge moduler används MCC modul Deployment Environment-variabler och behållar skapande alternativ för att konfigurera Microsoft Connected cache-moduler.  I det här avsnittet definieras miljövariabler och behållare skapa alternativ som krävs för att en kund ska kunna distribuera Microsoft Connected cache-modulen för användning av enhets uppdatering för Azure IoT Hub.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Distributions information om Microsoft Connected cache Azure IoT Edge module

Att namnge Microsoft Connected cache module är av administratören. Det finns inga andra moduler eller tjänst interaktioner som förlitar sig på namnet på modulen för kommunikation. Dessutom är den överordnade underordnade relationen för Microsoft Connected cache-servrar inte beroende av det här modulnamnet, utan i stället FQDN eller IP-adressen för den Azure IoT Edge gateway som har kon figurer ATS som tidigare.

Microsoft Connected cache Azure IoT Edge module miljövariabler används för att skicka grundläggande identitets information och funktions inställningar för modulen till behållaren.

| Variabelnamn                 | Värdeformat                           | Obligatorisk/valfri | Funktioner                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | GUID för Azure-prenumerations-ID             | Obligatorisk          | Det här är kundens nyckel, som ger säker<br>autentisering av cache-noden till leverans optimering<br>Terminal. Krävs för att modulen ska fungera. |
| CACHE_NODE_ID                 | Cache Node ID-GUID                     | Obligatorisk          | Identifierar unikt Microsoft Connected cache<br>nod för att leverera optimerings tjänster. Krävs för<br> för att modulen ska fungera. |
| CUSTOMER_KEY                  | GUID för kund nyckel                     | Obligatorisk          | Det här är kundens nyckel, som ger säker<br>autentisering av cache-noden till tjänster för leverans optimering.<br>Krävs för att modulen ska fungera.|
| STORAGE_ *N* _SIZE_GB           | Där N är antalet GB krävs   | Obligatorisk          | Ange upp till nio enheter för att cachelagra innehåll och ange<br>det maximala utrymme i gigabyte som ska allokeras för innehåll på varje cache-enhet. Exempel:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>Enhetens nummer måste matcha de angivna värdena för cache-enhetens bindning<br>i behållaren skapa alternativ MicrosoftConnectedCache *N* värde|
| UPSTREAM_HOST                 | FQDN/IP                                | Valfritt          | Det här värdet kan ange en överordnad Microsoft-ansluten<br>Cache-nod som fungerar som en proxy om den anslutna cache-noden<br> är frånkopplad från Internet. Den här inställningen används för att stödja<br> det kapslade IoT-scenariot.<br>**Obs:** Microsoft Connected cache lyssnar på http-standardporten 80.|
| UPSTREAM_PROXY                | FQDN/IP: PORT                           | Valfritt          | Utgående Internet-proxy.<br>Detta kan också vara DMZ-proxyn om ett ISA 95-nätverk. |
| CACHEABLE_CUSTOM_ *N* _HOST     | VÄRD/IP<br>FQDN                        | Valfritt          | Krävs för att stödja anpassade paket arkiv.<br>Databaserna kan vara lokalt eller på Internet.<br>Det finns ingen gräns för hur många anpassade värdar som kan konfigureras.<br><br>Exempel:<br>Namn = CACHEABLE_CUSTOM_1_HOST värde = packages.foo.com<br> Namn = CACHEABLE_CUSTOM_2_HOST värde = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Valfritt          | Krävs för att stödja anpassade paket arkiv.<br>Det här värdet kan användas som ett alias och kommer att användas av cache-servern till referens<br>olika DNS-namn. Värd namnet för lagrings platsen kan till exempel vara packages.foo.com,<br>men för olika regioner kan ett ytterligare prefix läggas till i värd namnet<br>som westuscdn.packages.foo.com och eastuscdn.packages.foo.com.<br>Genom att ange det kanoniska aliaset ser du till att innehållet inte är duplicerat<br>för innehåll som kommer från samma värd, men olika CDN-källor.<br>Formatet för det kanoniska värdet är inte viktigt, men det måste vara unikt för värden.<br>Det kan vara enklast att ange värdet som matchar värd värdet.<br><br>Exempel som baseras på anpassade värd exempel:<br>Namn = CACHEABLE_CUSTOM_1_CANONICAL värde = foopackages<br> Namn = CACHEABLE_CUSTOM_2_CANONICAL värde = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | Sant eller falskt                          | Valfritt          | Möjliggör visning av sammanfattnings rapporten i det lokala nätverket eller Internet.<br>Användning av en API-nyckel (diskuteras senare) krävs för att Visa sammanfattnings rapporten om värdet är true. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| Sant eller falskt                          | Valfritt          | Aktiverar visning av sammanfattnings rapporten i det lokala nätverket eller Internet utan<br>användning av API-nyckel från vilken enhet som helst i nätverket. Använd om du inte vill låsa åtkomsten<br>Om du vill visa sammanfattnings data för cache-servern via webbläsaren. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Microsoft Connected cache Azure IoT Edge module container Create alternativ

Behållar skapande alternativ för MCC module-distribution ger kontroll över inställningarna för lagring och portar som används av MCC-modulen. Det här är en lista över obligatoriska variabler för behållar skapande som används för att distribuera MCC.

### <a name="container-to-host-os-drive-mappings"></a>Behållare som är värd för enhets mappningar för operativ system

Krävs för att mappa behållar lagrings platsen till lagrings platsen på disken. < upp till nio platser kan anges.

>[!Note]
>Enhetens nummer måste matcha de bindnings värden för cache-enheten som anges i miljövariabeln STORAGE_ *N* _SIZE_GB värde, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Behållare som ska vara värd för TCP-portmappning

Det här alternativet anger den externa datorns http-port som MCC lyssnar efter innehålls begär Anden. Standardvärdet för HostPort är port 80 och andra portar stöds inte för tillfället eftersom ADU-klienten gör förfrågningar på port 80 idag. TCP-port 8081 är den interna container porten som MCC lyssnar på och inte kan ändras.

```markdown
8081/tcp": [
   {
       "HostPort": "80"
   }
]
```

### <a name="container-service-tcp-port-mappings"></a>TCP-portmappning för container service

Microsoft Connected cache module har en .NET Core-tjänst som används av caching-motorn för olika funktioner.

>[!Note]
>För att stödja inkapslade Azure IoT-sidor får HostPort inte vara inställt på 5000 eftersom modulen redan lyssnar på värd porten 5000.

```markdown
5000/tcp": [
   {
       "HostPort": "5001"
   }
]
```

## <a name="microsoft-connected-cache-summary-report"></a>Sammanfattnings rapport för Microsoft Connected cache

Sammanfattnings rapporten är för närvarande det enda sättet för en kund att Visa datacache-data för de Microsoft-anslutna cache-instanser som distribueras till Azure IoT Edge gatewayer. Rapporten genereras med 15 sekunders intervall och innehåller genomsnitts statistik för perioden samt sammanställd statistik för modulens livs längd. Den nyckel statistik som kunder är intresserade av är:

* **hitBytes** – detta är summan av levererade byte som kom direkt från cachen.
* **missBytes** – detta är summan av byte som har levererats till att Microsoft Connected cache var tvungen att ladda ned från CDN för att se cachen.
* **eggressBytes** – detta är summan av HitBytes och missBytes och är det totala antalet byte som levereras till klienterna.
* **hitRatioBytes** – detta är förhållandet mellan HitBytes och egressBytes.  Om 100% av eggressBytes som levererats under en period var lika med hitBytes blir det till exempel 1.

Sammanfattnings rapporten finns på `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` (se miljö variabel information nedan om du vill ha mer information om den här rapportens synlighet).
