---
title: Grov omlokalisering
description: Lär dig mer om att använda grov omlokalisering för att hitta ankare nära dig.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: fc04242e61c748d7ae52e61e40206ba338a1b6aa
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071149"
---
# <a name="coarse-relocalization"></a>Grov omlokalisering

Grov omlokalisering är en funktion som möjliggör storskalig lokalisering genom att tillhandahålla ett ungefärligt men snabbt svar på frågan: *var är min enhet nu/vad innehållet bör jag iaktta?* Svaret är inte exakt, utan är i formatet: du är *nära dessa ankare. försök att hitta någon av dem*.

Grov omlokalisering fungerar genom att tagga ankare med olika sensor läsningar på enhet som används senare för snabb frågor. Sensor data är vanligt vis enhetens GPS-position (Global Positioning System). Om GPS inte är tillgängligt eller otillförlitligt (t. ex. inomhus) består sensor data av WiFi-åtkomst punkter och Bluetooth-beacons inom räckhåll. De insamlade sensor data bidrar till att underhålla ett rums index som används av Azures ankare för att snabbt avgöra vilka ankare som är i närheten av enheten.

## <a name="when-to-use-coarse-relocalization"></a>När du ska använda grov omlokalisering

Om du planerar att hantera fler än 35 avstånds ankare i ett utrymme som är större än en tennis domstol kommer du troligen att ha nytta av grov lokalisering av rums indexering.

Snabb sökning av ankare som aktive ras genom grov omlokalisering är utformad för att förenkla utvecklingen av program som stöds av världs omfattande samlingar av (t. ex. miljon tals geo-distribuerade) ankare. Komplexiteten vid rums indexering är allt dolt, så att du kan fokusera på din program logik. All fäst punkts-och lyfter görs åt dig bakom kulisserna av molnets avstånds ankare.

## <a name="using-coarse-relocalization"></a>Använda grov omlokalisering

Det vanligaste arbets flödet för att skapa och fråga Azure-spatiala ankare med grov omlokalisering är:
1.  Skapa och konfigurera en sensor finger avtrycks leverantör för att samla in sensor data som du väljer.
2.  Starta en Azure-spatial Anchor-session och skapa ankare. Eftersom sensor finger avtryck är aktive rad indexeras ankaret av grov omlokalisering.
3.  Fråga kring ankare med grov omlokalisering med hjälp av de dedikerade Sök kriterierna i Azure-sessionen med spatiala ankare.

Du kan referera till motsvarande följande självstudie för att ställa in grov lokalisering i programmet:
* [Grov omlokalisering i Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Grov omlokalisering i mål-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Grov omlokalisering i Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Grov omlokalisering i Java](../how-tos/set-up-coarse-reloc-java.md)
* [Grov omlokalisering i C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Grov omlokalisering i C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sensorer och plattformar

### <a name="platform-availability"></a>Plattforms tillgänglighet

De typer av sensor data som du kan skicka till Anchor-tjänsten är:

* GPS-position: latitud, longitud, höjd.
* Signal styrka för WiFi-åtkomst punkter i intervallet.
* Signal styrkan för Bluetooth-beacons i intervallet.

Tabellen nedan sammanfattar tillgängligheten för sensor data på plattformar som stöds, tillsammans med alla plattformsspecifika varningar:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | INGEN<sup>1</sup>  | Ja<sup>2</sup> | Ja<sup>3</sup> |
| **WiFi**        | Ja<sup>4</sup> | Ja<sup>5</sup> | NO  |
| **Bell-beacons** | Ja<sup>6</sup> | Ja<sup>6</sup> | Ja<sup>6</sup>|


<sup>1</sup> en extern GPS-enhet kan kopplas till HoloLens. Kontakta [vår support](../spatial-anchor-support.md) om du skulle vilja använda HoloLens med en GPS-spårare.<br/>
<sup>2</sup> stöds via [LocationManager][3] -API: er (både GPS och nätverk)<br/>
<sup>3</sup> stöds via [CLLocationManager][4] -API: er<br/>
<sup>4</sup> stöds till ungefär en genomsökning var tredje sekund <br/>
<sup>5</sup> från och med API-nivå 28 begränsas WiFi-genomsökningar till 4 anrop var 2: e minut. Från Android 10 kan begränsningen inaktive ras från menyn för inställningar för utvecklare. Mer information finns i Android- [dokumentationen][5].<br/>
<sup>6</sup> begränsat till [Eddystone][1] och [iBeacon][2]

### <a name="which-sensor-to-enable"></a>Vilken sensor som ska aktive ras

Valet av sensor är specifik för det program som du utvecklar och plattformen.
Följande diagram ger en start punkt där kombinationen av sensorer kan aktive ras beroende på lokaliserings scenariot:

![Diagram över aktiverade sensorer-val](media/coarse-relocalization-enabling-sensors.png)

I följande avsnitt får du fler insikter om fördelarna och begränsningarna för varje sensor typ.

### <a name="gps"></a>GPS

GPS är alternativet för att gå till i scenarier med utomhus.
När du använder GPS i ditt program bör du tänka på att de läsningar som tillhandahålls av maskin varan är vanligt vis:

* asynkron och låg frekvens (mindre än 1 Hz).
* otillförlitligt/brus (i genomsnittlig standard avvikelse på 7 m).

I allmänhet kommer både enhetens OS-och Azure-spatialdata att göra vissa filtreringar och extrapoleringar på den obehandlade GPS-signalen i ett försök att undvika problemen. Den här extra bearbetningen kräver tid för konvergens, så för bästa resultat bör du försöka:

* skapa en sensor finger avtrycks leverantör så tidigt som möjligt i ditt program
* Behåll sensorn för finger avtryck mellan flera sessioner
* Dela sensorn finger avtrycks leverantör mellan flera sessioner

GPS-enheter i konsument klass är vanligt vis inexakta. En studie av [Zandenbergen och Barbeau (2011)][6] rapporterar median precisionen för mobil telefoner med stöd för GPS (A-GPS) till omkring 7 meter – ett stort värde ignoreras! För att kontona ska kunna beaktas, behandlar tjänsten ankare som sannolikhets distributioner i GPS-utrymme. En fäst punkt är nu den region där det är troligt (det vill säga med mer än 95% konfidensgrad) som innehåller den faktiska, okända GPS-positionen.

Samma orsak tillämpas när du frågar med GPS. Enheten representeras som en annan region för rums exakthet runt den logiska, okända GPS-positionen. Att identifiera närliggande ankare översätts till att helt enkelt hitta ankare med konfidens områden som är *tillräckligt nära* för enhetens konfidens område, enligt bilden nedan:

![Val av fäst kandidater med GPS](media/coarse-reloc-gps-separation-distance.png)

### <a name="wifi"></a>WiFi

På HoloLens och Android kan WiFi signal styrka vara ett lämpligt alternativ för att aktivera avlokalisering av inaktivitet.
Dess fördel är möjligheten att omedelbart komma åt WiFi-åtkomst punkter (vanliga i, t. ex. Office-Spaces eller shopping malls) utan extra konfiguration.

> [!NOTE]
> iOS tillhandahåller inga API: er för att läsa WiFi signal styrka, och det går inte att använda för att använda WiFi-aktiverad avlokalisering.

När du använder WiFi i ditt program bör du tänka på att de läsningar som tillhandahålls av maskin varan är vanligt vis:

* asynkron och låg frekvens (mindre än 0,1 Hz).
* eventuellt begränsad till operativ system nivå.
* otillförlitligt/brus (i genomsnitt 3 – dBm standard avvikelse).

Med hjälp av Azures spatialdata görs ett försök att bygga en filtrerad WiFi signal styrke karta under en session i ett försök att minimera problemen. För bästa resultat bör du försöka:

* skapa en bra session innan du placerar den första fäst punkten.
* Se till att sessionen är aktiv så länge som möjligt (det vill säga skapa alla ankare och fråga i en session).

### <a name="bluetooth-beacons"></a>Bluetooth-beacons
<a name="beaconsDetails"></a>

Att distribuera Bluetooth-beacons är en bra lösning för storskaliga Omlokaliserings scenarier i stor skala, där GPS saknas eller är felaktigt. Det är också den enda inomhus-metod som stöds på alla tre plattformarna.

Beacons är vanligt vis mångsidiga enheter, där allt, inklusive UUID-och MAC-adresser, kan konfigureras. I Azures spatiala ankare förväntas att signaler identifieras unikt av deras UUID. Om du inte vill att den här unikheten skulle bli så stor orsakar det förmodligen felaktiga resultat. För bästa resultat bör du:

* tilldela unika UUID: er till dina beacons.
* distribuera dem på ett sätt som täcker ditt utrymme på ett enhetligt sätt och så att det går att komma åt minst tre beacons från någon punkt i utrymmet.
* skicka listan med unika Beacon-UUID till sensor finger avtrycks leverantören

Radio signaler som Bluetooth påverkas av hinder och kan störa andra radio signaler. Därför kan det vara svårt att gissa om ditt utrymme är enhetligt täckt. För att garantera en bättre kund upplevelse rekommenderar vi att du testar täckningen för dina beacons manuellt. Detta kan göras genom att flytta runt ditt space med kandidat enheter och ett program som visar Bluetooth inom räckhåll. När du testar täckningen bör du kontrol lera att du kan kontakta minst tre beacons från valfri strategisk position för ditt space. Om du ställer in för många beacons kan du öka störningarna och inte nödvändigt vis förbättra grov lokaliserings precisionen.

Bluetooth-beacons har normalt en täckning på 80 meter om det inte finns några hinder i utrymmet.
Det innebär att ett utrymme som inte har stora hinder kan användas för att distribuera beacons i ett rutnäts mönster var 40 meter.

En signal om att batteriet börjar ta slut påverkar resultatet negativt, så se till att du övervakar distributionen regelbundet för låga eller döda batterier.

Azures avstånds ankare spårar bara Bluetooth-beacons som finns i listan med kända Beacon-UUID. Skadliga beacons som är programmerade med tillåtna UUID: er i listan kan påverka kvaliteten på tjänsten negativt. Av den anledningen får du bäst resultat i granskade utrymmen där du kan styra deras distribution.

### <a name="sensors-accuracy"></a>Sensor precision

Noggrannheten i GPS-signalen, både vid skapande av ankare och under frågor, har ett stort inflytande över uppsättningen returnerade ankare. Frågor som baseras på WiFi/beacons ser däremot till att alla ankare som har minst en åtkomst punkt/beacon är gemensamma med frågan. I det här fallet bestäms resultatet av en fråga som baseras på WiFi/beacons huvudsakligen av den fysiska räckvidden för åtkomst punkter/signaler och miljö hinder.
Tabellen nedan uppskattar det förväntade Sök utrymmet för varje sensor typ:

| Mäta      | Sök efter utrymmes radie (ca.) | Information |
|-------------|:-------:|---------|
| GPS         | 20 m – 30 m | Bestäms av en GPS-osäkerhet bland andra faktorer. De rapporterade siffrorna beräknas för median precisionen för median för mobil telefoner med en-GPS, som är 7 meter. |
| WiFi        | 50 m-100 m | Bestäms av intervallet för de trådlösa åtkomst punkterna. Beror på frekvensen, sändnings styrkan, fysiska hinder, störning och så vidare. |
| Bell-beacons |  70 m | Bestäms av Beacon-intervallet. Beror på frekvensen, överförings styrkan, fysiska hinder, störning och så vidare. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
