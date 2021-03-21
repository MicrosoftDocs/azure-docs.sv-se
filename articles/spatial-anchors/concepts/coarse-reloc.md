---
title: Grov omlokalisering
description: Lär dig hur och när du ska använda grov omlokalisering. Grov omlokalisering hjälper dig att hitta ankare som ligger nära dig.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f0b04183c4df469d4f723486103790c4f97671b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656183"
---
# <a name="coarse-relocalization"></a>Grov omlokalisering

Grov omlokalisering är en funktion som möjliggör storskalig lokalisering genom att tillhandahålla ett ungefärligt men snabbt svar på dessa frågor: 
- *Var finns min enhet nu?* 
- *Vilket innehåll ska jag ha iakttagit?* 
 
Svaret är inte exakt. Det är i det här formuläret: *du är nära dessa ankare. Försök att hitta en av dem*.

Grov omlokalisering fungerar genom att tagga ankare med olika sensor läsningar på enhet som används senare för snabb frågor. Sensor data är vanligt vis enhetens GPS-position (Global Positioning System). Om GPS inte är tillgängligt eller otillförlitligt, t. ex. När du är i dörren, består sensor data av Wi-Fi åtkomst punkter och Bluetooth-beacons inom räckhåll. De insamlade sensor data bidrar till att underhålla ett rums index som används av Azures ankare för att snabbt avgöra vilka ankare som ligger nära enheten.

## <a name="when-to-use-coarse-relocalization"></a>När du ska använda grov omlokalisering

Om du planerar att hantera fler än 35 avstånds ankare i ett utrymme som är större än en tennis domstol, kommer du förmodligen att ha nytta av grov lokalisering av rums indexering.

Den snabba ökningen av ankare som aktive ras genom grov omlokalisering är utformad för att förenkla utvecklingen av program som backas upp av världs omfattande samlingar av, t. ex. miljon tals geo-distribuerade ankare. Komplexiteten för rums indexering är alla dolda, så att du kan fokusera på din program logik. Allt det svåra arbetet görs i bakgrunden av de spatiala Ankarena i Azure.

## <a name="using-coarse-relocalization"></a>Använda grov omlokalisering

Här är det vanligaste arbets flödet för att skapa och fråga Azure-spatialdata med grov omlokalisering:
1.  Skapa och konfigurera en sensor finger avtrycks leverantör för att samla in de sensor data som du vill ha.
2.  Starta en session med Azure-spatiala ankare och skapa fäst punkter. Eftersom sensor finger avtryck är aktive rad indexeras ankaret av grov omlokalisering.
3.  Fråga kring ankare med hjälp av grov omlokalisering via de dedikerade Sök kriterierna i sessionen för spatiala ankare.

Du kan referera till någon av de här självstudierna för att ställa in grov lokalisering i ditt program:
* [Grov omlokalisering i Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Grov omlokalisering i mål-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Grov omlokalisering i Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Grov omlokalisering i Java](../how-tos/set-up-coarse-reloc-java.md)
* [Grov omlokalisering i C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Grov omlokalisering i C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sensorer och plattformar

### <a name="platform-availability"></a>Plattforms tillgänglighet

Du kan skicka dessa typer av sensor data till Anchor-tjänsten:

* GPS-position: latitud, longitud, höjd
* Signal styrka för Wi-Fi åtkomst punkter i intervallet
* Signal styrka för Bluetooth-beacons inom räckhåll

Den här tabellen sammanfattar tillgängligheten för sensor data på plattformar som stöds och innehåller information som du bör vara medveten om:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Ingen<sup>1</sup>  | Ja<sup>2</sup> | Ja<sup>3</sup> |
| **Wi-Fi**        | Ja<sup>4</sup> | Ja<sup>5</sup> | Inga  |
| **Bell-beacons** | Ja<sup>6</sup> | Ja<sup>6</sup> | Ja<sup>6</sup>|


<sup>1</sup> en extern GPS-enhet kan kopplas till HoloLens. Kontakta [vår support](../spatial-anchor-support.md) om du skulle vilja använda HoloLens med en GPS-spårare.<br/>
<sup>2</sup> stöds via [LocationManager][3] -API: er (både GPS och nätverk).<br/>
<sup>3</sup> stöds via [CLLocationManager][4] -API: er.<br/>
<sup>4</sup> stöds till ungefär en genomsökning var 3: e sekund. <br/>
<sup>5</sup> från och med API-nivå 28 begränsas Wi-Fi genomsökningar till fyra anrop var 2: e minut. Från och med Android 10 kan du inaktivera begränsningen från menyn **Inställningar för utvecklare** . Mer information finns i Android- [dokumentationen][5].<br/>
<sup>6</sup> begränsat till [Eddystone][1] och [iBeacon][2].

### <a name="which-sensor-to-enable"></a>Vilken sensor som ska aktive ras

Valet av sensor beror på vilket program du utvecklar och plattformen.
Det här diagrammet ger en start punkt för att avgöra vilken kombination av sensorer som du kan aktivera, beroende på lokaliserings scenariot:

![Diagram som visar aktiverade sensorer för olika scenarier.](media/coarse-relocalization-enabling-sensors.png)

I följande avsnitt får du mer insikt om fördelarna och begränsningarna för varje sensor typ.

### <a name="gps"></a>GPS

GPS är alternativet för att gå till i scenarier med utomhus.
När du använder GPS i ditt program bör du tänka på att de läsningar som tillhandahålls av maskin varan är vanligt vis:

* Asynkron och låg frekvens (mindre än 1 Hz).
* Otillförlitligt/brus (i genomsnitt 7-m standard avvikelse).

I allmänhet kommer både enhetens operativ system och spatiala ankare att göra vissa filtreringar och extrapoleringar av den obehandlade GPS-signalen i ett försök att minimera problemen. Den här extra bearbetningen kräver tid för konvergens, så för bästa resultat bör du försöka:

* Skapa en sensor finger avtrycks leverantör så tidigt som möjligt i ditt program.
* Behåll sensorn för finger avtryck mellan flera sessioner.
* Dela sensorn finger avtrycks leverantör mellan flera sessioner.

GPS-enheter i konsument klass är vanligt vis inexakta. En studie av [Zandenbergen och Barbeau (2011)][6] rapporterar att median precisionen för mobil telefoner som har stöd för GPS (A-GPS) är cirka 7 meter. Det är ganska ett stort värde att ignorera! För att kontona ska kunna beaktas, behandlar tjänsten ankare som sannolikhets distributioner i GPS-utrymme. En fäst punkt är den region där det är troligt (med mer än 95% konfidensgrad) som innehåller den verkliga, okända GPS-positionen.

Samma orsak gäller när du frågar med hjälp av GPS. Enheten representeras som en annan region för rums exakthet runt den logiska, okända GPS-positionen. Identifiera närliggande ankare översätter sig för att hitta ankarena med konfidens områden *nära tillräckligt nära* enhetens konfidens område, enligt följande:

![Diagram som illustrerar hur du söker efter fäst kandidater med hjälp av GPS.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

På HoloLens och Android kan Wi-Fi signal styrkan vara ett bra sätt att aktivera grov lokalisering i inomhus.
Fördelen är att du får omedelbar till gång till Wi-Fi åtkomst punkter (vanligt i Office-utrymmen och shopping-malls, till exempel) utan att det krävs någon extra installation.

> [!NOTE]
> iOS tillhandahåller inte ett API för att läsa Wi-Fi signal styrka, så det kan inte användas för grov lokalisering som är aktiverat via Wi-Fi.

När du använder Wi-Fi i ditt program bör du tänka på att de läsningar som tillhandahålls av maskin varan är vanligt vis:

* Asynkron och låg frekvens (mindre än 0,1 Hz).
* Eventuellt begränsad till operativ system nivå.
* Otillförlitligt/brus (i genomsnitt 3 – dBm standard avvikelse).

Avstånds ankare kommer att försöka bygga en filtrerad karta över Wi-Fi signal styrkan under en session i ett försök att minimera problemen. För bästa resultat kan du försöka:

* Skapa en bra session innan du placerar den första fäst punkten.
* Se till att sessionen är aktiv så länge som möjligt. (Det vill säga skapa alla ankare och fråga i en session.)

### <a name="bluetooth-beacons"></a>Bluetooth-beacons
<a name="beaconsDetails"></a>

En noggrann distribution av Bluetooth-beacons är en bra lösning för storskaliga inlokaliserings scenarier i stor skala, där GPS saknas eller är felaktigt. Det är också den enda inomhus-metod som stöds på alla tre plattformarna.

Beacons är vanligt vis flexibla enheter där allt kan konfigureras, inklusive UUID-och MAC-adresser. I Azures spatiala ankare förväntas att signaler identifieras unikt av deras UUID. Om du inte ser den här unikaheten får du förmodligen felaktiga resultat. För bästa resultat:

* Tilldela unika UUID: er till dina beacons.
* Distribuera beacons på ett sätt som täcker ditt utrymme på ett enhetligt sätt och så att minst tre beacons kan uppnås från någon punkt i utrymmet.
* Skicka listan med unika Beacon-UUID: er till sensor finger avtrycks leverantören.

Radio signaler som Bluetooth påverkas av hinder och kan störa andra radio signaler. Det kan vara svårt att gissa om ditt utrymme är enhetligt täckt. För att garantera en bättre kund upplevelse rekommenderar vi att du testar täckningen för dina beacons manuellt. Du kan utföra ett test genom att flytta runt ditt space med kandidat enheter och ett program som visar Bluetooth inom räckhåll. När du testar täckningen bör du kontrol lera att du kan komma åt minst tre beacons från valfri strategisk position på ditt space. Om du har för många signaler kan det leda till mer störningar mellan dem och inte nödvändigt vis förbättra noggrannheten vid grov omlokalisering.

Bluetooth-beacons är vanligt vis 80 meter om det inte finns några hinder i utrymmet.
För ett utrymme som inte har stora hinder kan du distribuera beacons i ett rutnäts mönster var 40 meter.

En signal som börjar få slut på batteriet påverkar resultatet, så se till att övervaka distributionen regelbundet för låg eller ej debiterade batterier.

Med moln ankare i Azure spåras endast Bluetooth-beacons som finns i listan med kända signaler i närhets UUID. Men skadliga Beacon-program som har allowlisted-UUID: er kan påverka kvaliteten på tjänsten negativt. Du får det bästa resultatet i granskade utrymmen där du kan styra Beacon-distributionen.

### <a name="sensor-accuracy"></a>Sensor exakthet

Den korrekta inläsningen av GPS-signalen, både vid skapande av ankare och under frågor, har en betydande inverkan på uppsättningen av returnerade ankare. Frågor som baseras på Wi-Fi/beacons ser däremot till att alla ankare som har minst en åtkomst punkt/beacon är gemensamma med frågan. I det här fallet bestäms resultatet av en fråga som baseras på Wi-Fi/beacons av det fysiska intervallet för åtkomst punkter/signaler och miljö hinder.
Den här tabellen beräknar det förväntade Sök utrymmet för varje sensor typ:

| Mäta      | Search-Space-radie (ungefärlig) | Information |
|-------------|:-------:|---------|
| **GPS**         | 20 m till 30 m | Bestäms av en GPS-osäkerhet bland andra faktorer. De rapporterade siffrorna beräknas för median precisionen för median för mobil telefoner med A-GPS: 7 meter. |
| **Wi-Fi**        | 50 m till 100 m | Bestäms av intervallet för de trådlösa åtkomst punkterna. Beror på frekvensen, sändnings styrkan, fysiska hinder, störning och så vidare. |
| **Bell-beacons** |  70 m | Bestäms av Beacon-intervallet. Beror på frekvensen, överförings styrkan, fysiska hinder, störning och så vidare. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
