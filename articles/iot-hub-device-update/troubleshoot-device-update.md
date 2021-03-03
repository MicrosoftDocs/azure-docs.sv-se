---
title: Felsök vanliga enhets uppdateringar för Azure IoT Hub problem | Microsoft Docs
description: Det här dokumentet innehåller en lista över tips och knep för att hjälpa till att åtgärda många möjliga problem som du kan ha med enhets uppdatering för IoT Hub.
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: 6329e93bb5e628d68afbb2700ce0b9e3a2a711ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680007"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>Enhets uppdatering för IoT Hub fel söknings guide

Det här dokumentet innehåller några vanliga frågor och problem som enhets uppdaterings användare har rapporterat. När enhets uppdateringen fortskrider via den offentliga för hands versionen kommer den här fel söknings guiden att uppdateras regelbundet med nya frågor och lösningar. Om det uppstår ett problem som inte visas i den här fel söknings guiden, se avsnittet [kontakta Microsoft Support](#contact) för att dokumentera din situation.

## <a name="importing-updates"></a><a name="import"></a>Importerar uppdateringar

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>F: Jag har problem med att ansluta min enhets uppdaterings instans till min IoT Hub-instans.
_Kontrol lera att dina IoT Hub meddelande vägar är korrekt konfigurerade enligt [enhets uppdateringens](./device-update-resources.md) dokumentation._

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>F: Jag påträffar ett roll relaterat fel (fel meddelande i Azure Portal eller ett 403 API-fel).
_Du kanske inte har behörighet att konfigurera korrekt. Se till att du har konfigurerat åtkomst behörigheterna på rätt sätt enligt [enhets uppdateringen Access Control](./device-update-control-access.md) -dokumentationen._

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>F: Jag påträffar ett fel av typen 500 när innehållet importeras till enhets uppdaterings tjänsten.
_En felkod i intervallet 500 kan tyda på ett problem med enhets uppdaterings tjänsten. Vänta 5 minuter och försök sedan igen. Om samma fel kvarstår följer du anvisningarna i avsnittet [kontakta Microsoft Support](#contact) för att skicka en support förfrågan till Microsoft._

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>F: Jag påträffar en felkod när innehållet importeras och vill parsa det.
_Information om hur du tolkar felkoder finns i dokumentationen om [enhets uppdaterings fel koder](./device-update-error-codes.md) ._

## <a name="device-failures"></a><a name="device-failure"></a>Enhets problem

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>F: Hur kan jag se till att enheten är ansluten till enhets uppdatering för IoT Hub?
_Du kan kontrol lera att enheten är ansluten till enhets uppdateringen genom att kontrol lera om den visas under avsnittet "uppdelade enheter" i vyn efterlevnad för Azure Portal._

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>F: en eller flera av mina enheter kunde inte uppdateras.
_Det finns många möjliga rotor orsaker till ett enhets uppdaterings fel. Kontrol lera att enheten är: 1) ansluten till din IoT Hub instans, 2) ansluten till din enhets uppdaterings instans och 3) tjänsten för leverans optimering (utför) körs. Om alla tre är sanna för din enhet följer du anvisningarna i avsnittet [kontakta Microsoft Support](#contact) för att skicka en support förfrågan med Microsoft._

## <a name="deploying-an-update"></a><a name="deploy"></a> Distribuera en uppdatering

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>F: Jag har distribuerat en uppdatering till min (a) enhet (er), men kompabiliteten säger att den inte är den senaste uppdateringen. Vad ska jag göra?
_Det kan ta upp till 5 minuter att uppdatera enhetens kompatibilitetsstatus. Vänta och kontrol lera igen._
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>F: distributions statusen för min enhet visar inkompatibel, vad ska jag göra?
_Tillverkare och modell egenskaper för en riktad enhet kan ha ändrats efter att enheten anslutits till IoT Hub, vilket gör att enheten nu anses vara inkompatibel med uppdaterings innehållet i den aktuella distributionen._

_Kontrol lera [ADU Core-gränssnittet](./device-update-plug-and-play) för att se vilken tillverkare och modell enheten rapporterar till enhets uppdaterings tjänsten och se till att den matchar den tillverkare och modell som du angav i [import manifestet](./import-concepts.md) för det uppdaterings innehåll som distribueras. Du kan ändra dessa egenskaper för en specifik enhet med hjälp av [konfigurations filen för enhets uppdateringar](./device-update-cofiguration-file.md)._

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>F: Jag ser att min distribution är i "aktivt" Stadium men ingen av mina enheter är "pågående" med uppdateringen. Vad ska jag göra?
_Se till att start datumet för distributionen inte är inställt i framtiden. När du skapar en ny distribution, används distributions start datumet som standard till nästa dag som skydd om du inte uttryckligen ändrar det. Du kan antingen vänta tills start datumet för distributionen tas emot eller avbryta den pågående distributionen och skapa en ny distribution med önskat start datum._

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>F: Jag försöker gruppera mina enheter, men jag ser inte taggen i list rutan när du skapar en grupp.
_Kontrol lera att du har konfigurerat meddelande vägarna korrekt i IoT Hub enligt dokumentationen för [enhets uppdaterings resurser](./device-update-resources.md) . Du måste tagga enheten igen när du har konfigurerat vägen._

_En annan rotorsak kan vara att du tillämpade taggen innan du anslöt enheten till enhets uppdatering för IoT Hub. Kontrol lera att enheten redan är ansluten till enhets uppdateringen. Du kan kontrol lera att enheten är ansluten till enhets uppdatering för IoT Hub genom att kontrol lera om den visas under "uppdelade" enheter i vyn efterlevnad. Lägg tillfälligt till en tagg av ett annat värde och Lägg sedan till den avsedda taggen igen när enheten är ansluten._

_Om du använder enhets etablerings tjänsten (DPS) ska du kontrol lera att du taggar dina enheter när de har skapats och inte när enheten skapas. Om du redan har märkt enheten när du har skapat enheten måste du tillfälligt tagga enheten med ett annat värde när den har tillhandahållits och sedan lägga till den avsedda taggen igen._

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>F: min distribution har slutförts, men vissa enheter kunde inte uppdateras.
_Detta kan ha orsakats av ett fel på klient sidan på de felande enheterna. I den här fel söknings guiden finns mer information i avsnittet om enhets fel._

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>F: Jag påträffade ett fel i UX vid försök att starta en distribution.
_Detta kan ha orsakats av en tjänst/UX-bugg eller ett problem med API-behörighet. Följ anvisningarna i avsnittet [kontakta Microsoft Support](#contact) för att skicka en support förfrågan till Microsoft._

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>F: Jag startade en distribution men har inte nått slut läget.
_Detta kan ha orsakats av ett problem med tjänstens prestanda, ett fel i tjänsten eller ett klient fel. Försök att distribuera igen om 10 minuter. Om du stöter på samma problem kan du hämta enhets loggarna och se avsnittet enhets fel i den här fel söknings guiden. Om samma problem kvarstår följer du anvisningarna i avsnittet [kontakta Microsoft Support](#contact) för att skicka en support förfrågan till Microsoft._

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> Hämta uppdateringar till enheter

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>F: Hur gör jag för att återuppta en nedladdning när en enhet har återanslutits efter en tids fri anslutning?
_Nedladdningen kommer att återupptas automatiskt när anslutningen har återställts inom en 24-timmarsperiod. Efter 24 timmar måste hämtningen initieras om av användaren._
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> Använda Microsoft Connected cache (MCC)

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>F: Jag påträffar ett problem när jag försöker distribuera MCC-modulen på min IoT Edge-enhet.
_Läs IoT Edge- [dokumentationen]() för att distribuera Edge-moduler för att IoT Edge enheter. Du kan kontrol lera om modulen MCC körs på din IoT Edge enhet genom att gå till http://localhost:5100/Summary ._
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>F: en av mina IoT-enheter försöker hämta en uppdatering via MCC, men den Miss lyckas.
_Det finns flera problem som kan orsaka att en IoT-enhet inte kan ansluta till MCC. För att diagnostisera problemet kan du samla in nginx-loggar från den felande enheten (se avsnittet [kontakta Microsoft Support](#contact) för instruktioner om hur du samlar in klient loggar)._

_Enheten kanske inte kan hämta innehåll från Internet för att skicka den till MCC-modulen eftersom den URL som används inte är tillåten. För att avgöra om så är fallet måste du kontrol lera IoT Edge miljövariablerna i Azure Portal._
## <a name="contacting-microsoft-support"></a><a name="contact"></a> Kontaktar Microsoft Support

Om du stöter på problem som inte kan lösas med vanliga frågor och svar ovan, kan du skicka en support förfrågan med Microsoft Support via gränssnittet Azure Portal. Beroende på vilken kategori du anger att problemet tillhör, kan du bli ombedd att samla in och dela ytterligare data för att hjälpa Microsoft Support att undersöka problemet. 

Se nedan för instruktioner om hur du samlar in varje datatyp. Du kan använda [getDevices]() för att söka efter ytterligare information i nytto Last svaret för API: et.

Dessutom kan följande information vara användbar för att begränsa den bakomliggande orsaken till problemet:
* Vilken typ av enhet du försöker uppdatera (Azure percept, IoT Edge Gateway, övrigt)
* Vilken klient typ av enhets uppdatering du använder (avbildningsbaserad, Package-based, simulator)
* Vilket operativ system enheten körs på
* Information om enhetens arkitektur
* Om du har använt enhets uppdateringen för att uppdatera en enhet innan

Om du har någon av ovanstående uppgifter kan du ta med den i din beskrivning av problemet.

### <a name="collecting-client-logs"></a>Samla in klient loggar

* På Raspberry Pi-enheten finns det två uppsättningar med loggar som finns här:

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* Loggarna finns här för den paketerade klienten:

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* För simulatorn finns loggarna här:

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>Felkoder
Du kan bli ombedd att ange felkoder när du rapporterar ett problem som rör import av en uppdatering, ett enhets fel eller distribution av en uppdatering.

Du kan hämta felkoder genom att titta på [ADUCoreInterface](./device-update-plug-and-play.md) -gränssnittet. Mer information om hur du tolkar felkoder för självdiagnostik och fel sökning finns i dokumentationen om [enhets uppdaterings fel koder](./device-update-error-codes.md) .

### <a name="trace-id"></a>Spårnings-ID
Du kan bli ombedd att ange ett spårnings-ID när du rapporterar ett problem som rör import eller distribution av en uppdatering.

Spårnings-ID för en specifik användar åtgärd kan hittas i API-svaret eller i avsnittet import historik i Azure Portal användar gränssnitt. 

Spårnings-ID: n för distributions åtgärder är för närvarande endast tillgängliga via API-svaret.

### <a name="deployment-id"></a>Distributions-ID
Du kan bli ombedd att ange ett distributions-ID när du rapporterar ett problem som rör distribution av en uppdatering.

Distributions-ID: t skapas av användaren när API: et anropas för att starta en distribution.

För närvarande genereras distributions-ID: n för distributioner som initieras från Azure Portal användar gränssnittet automatiskt och visas inte på användarens sida.

### <a name="iot-hub-instance-name"></a>Namn på IoT Hub instans
Du kan uppmanas att ange namnet på din IoT Hub instans vid rapportering av ett problem som rör enhets fel eller distribution av en uppdatering.

IoT Hub namnet väljs av användaren när den först etablerades.

### <a name="device-update-account-name"></a>Konto namn för enhets uppdatering
Du kan uppmanas att ange namnet på ditt enhets uppdaterings konto när du rapporterar ett problem som rör import av en uppdatering, enhets fel eller distribution av en uppdatering.

Konto namnet för enhets uppdatering väljs av användaren när du först registrerar dig för tjänsten. Mer information finns i dokumentationen för [enhets uppdaterings resurser](./device-update-resources.md) .

### <a name="device-update-instance-name"></a>Instans namn för enhets uppdatering
Du kan uppmanas att ange namnet på enhets uppdaterings instansen när du rapporterar ett problem som rör import av en uppdatering, enhets fel eller distribution av en uppdatering.

Namnet på enhets uppdaterings instansen väljs av användaren när den först etablerades. Mer information finns i dokumentationen för [enhets uppdaterings resurser](./device-update-resources.md) .

### <a name="device-id"></a>Enhets-ID
Du kan bli ombedd att ange ett enhets-ID när du rapporterar ett problem som rör enhets fel eller distribuerar en uppdatering.

Enhets-ID: t definieras av kunden när enheten först konfigureras. Den kan också hämtas från enhetens enhet på plats två.

### <a name="update-id"></a>Uppdaterings-ID
Du kan bli ombedd att ange ett uppdaterings-ID när du rapporterar ett problem som rör distribution av en uppdatering.

Uppdaterings-ID: t definieras av kunden när en distribution initieras.

### <a name="nginx-logs"></a>Nginx-loggar
Du kan bli ombedd att tillhandahålla nginx-loggar när du rapporterar ett problem som rör Microsoft Connected cache.

### <a name="adu-conftxt"></a>ADU-conf.txt
Du kan bli ombedd att ange konfigurations filen för enhets uppdatering ("adu-conf.txt") när du rapporterar ett problem som rör distribution av en uppdatering.

Konfigurations filen är valfri och skapas av användaren enligt anvisningarna i [konfigurations dokumentationen för enhets uppdateringar](./device-update-configuration-file.md) .

### <a name="import-manifest"></a>Importera manifest
Du kan bli ombedd att ange import manifest filen när du rapporterar ett problem som rör import eller distribution av en uppdatering.

Import manifestet är en fil som skapats av kunden när du importerar uppdaterings innehåll till enhets uppdaterings tjänsten.

**[Nästa steg: Lär dig mer om fel koder för enhets uppdatering](.\device-update-error-codes.md)**