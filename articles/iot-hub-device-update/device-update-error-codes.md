---
title: Klient fel koder för enhets uppdatering för Azure IoT Hub | Microsoft Docs
description: Det här dokumentet innehåller en tabell med klient fel koder för olika enhets uppdaterings komponenter.
author: chrisjlin
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: dbdddc7cee0c3664a83501ba619a38e1cc44e1f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200343"
---
# <a name="device-update-for-iot-hub-error-codes"></a>Enhets uppdatering för IoT Hub felkoder

Det här dokumentet innehåller en tabell med felkoder för olika enhets uppdaterings komponenter. Detta är avsett att användas som referens för användare som vill försöka parsa sina egna felkoder för att diagnostisera och felsöka problem.

Det finns två primära komponenter på klient sidan som kan utlösa felkoder: enhets uppdaterings agenten och leverans optimerings agenten.

## <a name="device-update-agent"></a>Enhets uppdaterings agent

### <a name="resultcode-and-extendedresultcode"></a>ResultCode och ExtendedResultCode

Enhets uppdateringen för IoT Hub Core PnP interface `ResultCode` -rapporter och `ExtendedResultCode` , som kan användas för att diagnostisera fel. [Läs mer](device-update-plug-and-play.md) om enhets uppdateringens kärn PnP-gränssnitt.

#### <a name="resultcode"></a>ResultCode

`ResultCode` är en allmän status kod och följer http-status kod konvention.
[Läs mer](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) om HTTP-statuskod.

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` är ett heltal med kodad fel information.

Du kommer troligen att se det `ExtendedResultCode` som ett signerat heltal i PnP-gränssnittet. Om du vill avkoda `ExtendedResultCode` konverterar du det signerade heltalet till ett osignerat hex. Endast de första 4 byten i `ExtendedResultCode` används och är av formuläret `F` `FFFFFFF` där den första Nibble är **funktions koden** och resten av bitarna är **felkoden**.

**Anläggnings koder**

| Funktions kod     | Beskrivning  |
|-------------------|--------------|
| D                 | Fel som skapats från SDK-SDK|
| E                 | Felkoden är en errno |


Exempel:

`ExtendedResultCode` är `-536870781`

Den osignerade hex-representationen av `-536870781` är `FFFFFFFF E0000083` .

| Ignorera    | Funktions kod  | Felkod   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

`0x83` i hex är `131` i decimal, vilket är errno-värdet för `ENOLCK` .

## <a name="delivery-optimization-agent"></a>Leverans optimerings agent
I följande tabell visas felkoder som rör leverans optimerings komponenten i enhets uppdaterings klienten. Komponenten gör är ansvarig för att ladda ned uppdaterings innehåll på IoT-enheten.

Du kan hämta felkoden genom att undersöka undantag som har utlösts som svar på ett API-anrop. Alla fel koder kan identifieras av 0x80D0-prefixet.

| Felkod  | Sträng fel                       | Typ                 | Beskrivning |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | saknas                  | Leverans optimering kunde inte tillhandahålla tjänsten |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | Hämta jobb         | Hämtning av en fil försetts inte inom den definierade perioden |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | Hämta jobb         | SetProperty () eller GetProperty () anropades med ett okänt egenskaps-ID |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | Hämta jobb         | Det går inte att anropa SetProperty () för en skrivskyddad egenskap |
| 0x80D02013L | DO_E_INVALID_STATE                 | Hämta jobb         | Den begärda åtgärden tillåts inte i det aktuella jobb läget. Jobbet kan ha avbrutits eller överföringen har slutförts. Den är i ett skrivskyddat tillstånd nu. |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | Hämta jobb         | Det gick inte att starta en nedladdning eftersom ingen hämtnings mottagare (antingen lokal fil eller Stream-gränssnitt) angavs |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | IDODownload-gränssnitt| Hämtningen startades utan att tillhandahålla en URI |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | Tillfälliga villkor | Nedladdningen pausades på grund av förlust av nätverks anslutning |

## <a name="device-update-content-service"></a>Innehålls tjänst för enhets uppdatering
I följande tabell visas felkoder som rör innehålls tjänst komponenten i enhets uppdaterings tjänsten. Innehålls tjänst komponenten ansvarar för hantering av import av uppdaterings innehåll.

| Felkod                    | Sträng fel                                                               | Nästa steg                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | Det finns redan en uppdatering med samma identitet.                              | Kontrol lera att du importerar en uppdatering som inte redan har importer ATS till den här instansen av enhets uppdateringen för IoT Hub. |
| "DuplicateContentImport"      | Identiskt innehåll importerades samtidigt flera gånger.                  | Samma som för UpdateAlreadyExists. |
| "CannotProcessImportManifest" | Det gick inte att bearbeta import manifestet.                                          | Se [Importera koncept](./import-concepts.md) och [Importera uppdaterings](./import-update.md) dokumentation för korrekt import manifest formatering. |
| "CannotDownload"              | Det går inte att hämta import manifestet.                                           | Kontrol lera att URL: en för import manifest filen fortfarande är giltig. |
| "CannotParse"                 | Det går inte att parsa import manifestet.                                              | Kontrol lera att import manifestet stämmer mot det schema som definierats i [import uppdaterings](./import-update.md) dokumentationen. |
| "UnsupportedVersion"          | Det finns inte stöd för att importera manifest schema version.                           | Kontrol lera att import manifestet använder det senaste schema som definierats i dokumentationen för [import uppdatering](./import-update.md) . |
| "UpdateLimitExceeded"         | Det gick inte att importera uppdateringen på grund av Överskriden gräns.                              | Du har nått gränsen för antalet olika leverantörer, namn eller versioner som tillåts i din instans av enhets uppdatering för IoT Hub. Ta bort några uppdateringar från instansen och försök igen. |
| "UpdateProvider"              | Det går inte att importera en ny uppdaterings leverantör.                                       | Du har nått gränsen för antalet olika __providrar__ som tillåts i din instans av enhets uppdatering för IoT Hub. Ta bort några uppdateringar från instansen och försök igen. |
| "UpdateName"                  | Det går inte att importera ett nytt uppdaterings namn för den angivna providern.                | Du har nått gränsen för antalet olika __namn__ som tillåts under en provider i din instans av enhets uppdatering för IoT Hub. Ta bort några uppdateringar från instansen och försök igen. |
| "UpdateVersion"               | Det går inte att importera en ny uppdaterings version för den angivna providern och namnet.    | Du har nått gränsen för antalet olika __versioner__ som tillåts under en provider och ett namn i din instans av enhets uppdatering för IoT Hub. Ta bort några uppdateringar med det namnet från instansen och försök igen. |
| "UpdateProviderCompatibility" | Det går inte att importera ytterligare en uppdaterings leverantör med den angivna kompatibiliteten. | När du definierar egenskaper för kompatibilitet av enhets tillverkare och enhets modell i ett import manifest bör du tänka på att enhets uppdatering för IoT Hub stöder en enskild Provider och namn kombination för en viss tillverkare/modell. Det innebär att om du försöker använda samma egenskaper för tillverkare/modell-kompatibilitet med fler än en kombination av Provider/namn visas dessa fel. Du kan lösa detta genom att kontrol lera att alla uppdateringar för en viss enhet (som definieras av tillverkare/modell) använder samma provider och namn. Även om det inte behövs kan du överväga att göra leverantören densamma som tillverkaren och namnet på samma sätt som modellen, bara för enkelhetens skull. |
| "UpdateNameCompatibility"     | Det går inte att importera ytterligare uppdaterings namn med den angivna kompatibiliteten.     | Samma som för UpdateProviderCompatibility. ContentLimitNamespaceCompatibility. |
| "UpdateVersionCompatibility"  | Det går inte att importera ytterligare uppdaterings version med den angivna kompatibiliteten.  | Samma som för UpdateProviderCompatibility. ContentLimitNamespaceCompatibility. |
| "CannotProcessUpdateFile"     | Det gick inte att bearbeta käll filen.                                              |                                    |
| "ContentFileCannotDownload"   | Det går inte att hämta käll filen.                                               | Kontrol lera att URL: en för uppdaterings filen (s) fortfarande är giltig. |

**[Nästa steg: Felsök problem med enhets uppdatering](.\troubleshoot-device-update.md)**
