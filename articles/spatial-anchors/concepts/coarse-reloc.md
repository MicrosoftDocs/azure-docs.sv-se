---
title: Grov omlokalisering
description: Lär dig hur och när du ska använda grov omlokalisering. Grov omlokalisering hjälper dig att hitta fästpunkter som är nära dig.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: d2737f58fa95d1aa45d9952e8b501c1b9be4d1b0
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600360"
---
# <a name="coarse-relocalization"></a>Grov omlokalisering

Grov omlokalisering är en funktion som möjliggör storskalig lokalisering genom att ge ett ungefärligt men snabbt svar på dessa frågor: 
- *Var är min enhet nu?* 
- *Vilket innehåll ska jag observera?* 
 
Svaret är inte exakt. Den är i det här formuläret: *Du är nära dessa fästpunkter. Försök att hitta en av dem*.

Grov omlokalisering fungerar genom att tagga fästpunkter med olika sensoravläsningar på enheten som senare används för snabba frågor. I utomhusscenarier är sensordata vanligtvis enhetens GPS-position (Global Positioning System). När GPS är otillgängligt eller otillförlitligt, till exempel när du är inomhus, består sensordata av de Wi-Fi åtkomstpunkter och Bluetooth-sändare som är inom räckhåll. Insamlade sensordata bidrar till att upprätthålla ett spatialt index som används av Azure Spatial Anchors för att snabbt avgöra vilka fästpunkter som är nära din enhet.

## <a name="when-to-use-coarse-relocalization"></a>När du ska använda grov omlokalisering

Om du planerar att hantera fler än 35 spatiala fästpunkter i ett utrymme som är större än en områdesbana kan du förmodligen dra nytta av grov omlokalisering av rumslig indexering.

Den snabba sökning av fästpunkter som aktiveras av grov omlokalisering är utformad för att förenkla utvecklingen av program som backas upp av världsskaliga samlingar av till exempel miljontals geo-distribuerade fästpunkter. Komplexiteten i spatial indexering är dold, så du kan fokusera på din programlogik. Allt svårt arbete utförs i bakgrunden av Azure Spatial Anchors.

## <a name="using-coarse-relocalization"></a>Använda grov omlokalisering

Här är det vanliga arbetsflödet för att skapa och köra frågor mot Azure-Spatial Anchors grov omlokalisering:
1.  Skapa och konfigurera en sensor fingeravtrycksprovider för att samla in de sensordata som du vill ha.
2.  Starta en Azure Spatial Anchors-session och skapa fästpunkter. Eftersom fingeravtryckssensorer är aktiverade indexeras fästpunkter rumsligt genom grov omlokalisering.
3.  Fråga kring kringliggande fästpunkter genom att använda grov omlokalisering via de dedikerade sökvillkoren i Spatial Anchors sessionen.

Du kan gå till någon av dessa självstudier för att konfigurera grov omlokalisering i ditt program:
* [Grov omlokalisering i Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Grov omlokalisering i Objective-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Grov omlokalisering i Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Grov omlokalisering i Java](../how-tos/set-up-coarse-reloc-java.md)
* [Grov omlokalisering i C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Grov omlokalisering i C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sensorer och plattformar

### <a name="platform-availability"></a>Plattformstillgänglighet

Du kan skicka dessa typer av sensordata till fästpunktstjänsten:

* GPS-position: latitud, longitud, höjd
* Signalstyrka för Wi-Fi åtkomstpunkter i intervallet
* Signalstyrka för Bluetooth-sändare inom räckhåll

I den här tabellen sammanfattas tillgängligheten för sensordata på plattformar som stöds och innehåller information som du bör känna till:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **Gps**         | Nej<sup>1</sup>  | Ja<sup>2</sup> | Ja<sup>3</sup> |
| **Wi-Fi**        | Ja<sup>4</sup> | Ja<sup>5</sup> | No  |
| **BLE-sändare** | Ja<sup>6</sup> | Ja<sup>6</sup> | Ja<sup>6</sup>|


<sup>1</sup> En extern GPS-enhet kan associeras med HoloLens. Kontakta [vår support](../spatial-anchor-support.md) om du vill använda HoloLens med en GPS-spårare.<br/>
<sup>2</sup> Stöds via [LocationManager-API:er][3] (både GPS och NETWORK).<br/>
<sup>3 Stöds</sup> via [API:er för CLLocationManager.][4]<br/>
<sup>4</sup> Stöds med en hastighet på cirka en genomsökning var tredje sekund. <br/>
<sup>5</sup> Från och med API-nivå 28 begränsas Wi-Fi till fyra anrop varannan minut. Från och med Android 10 kan du inaktivera den här begränsningen från menyn **Inställningar för** utvecklare. Mer information finns i [Android-dokumentationen.][5]<br/>
<sup>6</sup> Begränsat till [Eddystone][1] och [iBeacon][2].

### <a name="which-sensor-to-enable"></a>Vilken sensor som ska aktiveras

Valet av sensor beror på vilket program du utvecklar och plattformen.
Det här diagrammet ger en startpunkt för att avgöra vilken kombination av sensorer som du kan aktivera, beroende på lokaliseringsscenariot:

![Diagram som visar aktiverade sensorer för olika scenarier.](media/coarse-relocalization-enabling-sensors.png)

Följande avsnitt innehåller mer information om fördelarna och begränsningarna för varje sensortyp.

### <a name="gps"></a>Gps

GPS är alternativet för utomhusscenarier.
När du använder GPS i ditt program bör du tänka på att de avläsningar som tillhandahålls av maskinvaran vanligtvis är:

* Asynkron och låg frekvens (mindre än 1 Hz).
* Opålitlig/brus (i genomsnitt 7 m standardavvikelse).

I allmänhet kommer både enhetens operativsystem och Spatial Anchors att utföra viss filtrering och extrapolering av DEN råa GPS-signalen i ett försök att åtgärda dessa problem. Den här extra bearbetningen kräver tid för konvergens, så för bästa resultat bör du försöka att:

* Skapa en sensor med fingeravtrycksprovider så tidigt som möjligt i ditt program.
* Håll sensoravtrycksprovidern vid liv mellan flera sessioner.
* Dela fingeravtrycksprovidern för sensorn mellan flera sessioner.

GPS-enheter i konsumentklass är vanligtvis inexakta. En studie av [Zandéen och Barbantal (2011)][6] rapporterar att medianprecisionen för mobiltelefoner som har assisterat GPS (A-GPS) är cirka 7 meter. Det är ett ganska stort värde att ignorera! För att ta hänsyn till dessa måttfel behandlar tjänsten fästpunkter som sannolikhetsfördelningar i GPS-utrymme. Så en fästpunkt är den region av utrymme som troligen (med mer än 95 % konfidens) innehåller sin sanna, okända GPS-position.

Samma resonemang gäller när du frågar med hjälp av GPS. Enheten representeras som en annan rumslig konfidensregion runt dess sanna, okända GPS-position. Identifiering av närliggande fästpunkter innebär att hitta fästpunkter med konfidensregioner som är tillräckligt nära enhetens konfidensregion, vilket illustreras här: 

![Diagram som visar hur du hittar fästpunktskandidater med hjälp av GPS.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

På HoloLens och Android kan Wi-Fi signalstyrka vara ett bra sätt att aktivera grov omlokalisering av inomhus.
Fördelen är den potentiella omedelbara tillgängligheten Wi-Fi åtkomstpunkter (t.ex. kontors- och shoppingcenter) utan att det behövs någon extra konfiguration.

> [!NOTE]
> iOS tillhandahåller inte något API för läsning Wi-Fi signalstyrka, så det kan inte användas för grov omlokalisering som aktiveras via Wi-Fi.

När du använder Wi-Fi i ditt program bör du tänka på att de avläsningar som tillhandahålls av maskinvaran vanligtvis är:

* Asynkron och låg frekvens (mindre än 0,1 Hz).
* Potentiellt begränsat på operativsystemnivå.
* Otillförlitlig/brus (i genomsnitt 3 dBm standardavvikelse).

Spatial Anchors försöker skapa en filtrerad karta Wi-Fi signalstyrka under en session i ett försök att åtgärda dessa problem. För bästa resultat kan du försöka att:

* Skapa sessionen väl innan du placerar den första fästpunkten.
* Håll sessionen vid liv så länge som möjligt. (Det vill säga skapa alla fästpunkter och fråga i en session.)

### <a name="bluetooth-beacons"></a>Bluetooth-sändare
<a name="beaconsDetails"></a>

Noggrann distribution av Bluetooth-sändare är en bra lösning för storskaliga scenarier med grov omlokalisering av inomhus, där GPS saknas eller är felaktig. Det är också den enda inomhusmetoden som stöds på alla tre plattformarna.

Sändare är vanligtvis mångsidiga enheter där allt kan konfigureras, inklusive UID:er och MAC-adresser. Azure Spatial Anchors förväntar sig att sändare identifieras unikt av sina UID:er. Om du inte ser till att detta är unikt får du förmodligen felaktiga resultat. För bästa resultat:

* Tilldela unika UID:er till dina sändare.
* Distribuera sändare på ett sätt som täcker ditt utrymme enhetligt och så att minst tre sändare kan nås från valfri punkt i rymden.
* Skicka listan över unika beacon-UID:er till sensorens fingeravtrycksprovider.

Radiosignaler som Bluetooth påverkas av hinder och kan störa andra radiosignaler. Därför kan det vara svårt att gissa om ditt utrymme är enhetligt täckt. För att garantera en bättre kundupplevelse rekommenderar vi att du testar täckningen för dina sändare manuellt. Du kan utföra ett test genom att gå runt i utrymmet med kandidatenheter och ett program som visar Bluetooth inom räckhåll. När du testar täckningen bör du se till att du kan nå minst tre sändare från valfri strategisk position i ditt utrymme. Att ha för många sändare kan resultera i mer interferens mellan dem och förbättrar inte nödvändigtvis noggrannheten för grov omlokalisering.

Bluetooth-sändare täcker vanligtvis 80 meter om det inte finns några hinder i utrymmet.
Så för ett utrymme som inte har några stora hinder kan du distribuera beacons i ett rutnätsmönster var 40:e meter.

En sändare som börjar få slut på batteri påverkar resultatet, så se till att regelbundet övervaka distributionen för låga eller oladdade batterier.

Azure Spatial Anchors spårar endast Bluetooth-sändare som finns i listan med kända närhets-UIID:er. Men skadliga sändare som programmerats att ha tillåtna UID:er kan påverka tjänstens kvalitet negativt. Därför får du de bästa resultaten i curated spaces där du kan styra distributionen av sändare.

### <a name="sensor-accuracy"></a>Sensorprecision

Precisionen för GPS-signalen, både när fästpunkten skapas och under frågor, har en betydande inverkan på uppsättningen returnerade fästpunkter. Frågor som baseras på Wi-Fi/beacons överväger däremot alla fästpunkter som har minst en åtkomstpunkt/-beacon som är gemensam för frågan. I det avseendet bestäms resultatet av en fråga som baseras på Wi-Fi/beacons huvudsakligen av det fysiska intervallet för åtkomstpunkter/sändare och miljöfaktorer.
I den här tabellen beräknas det förväntade sökutrymmet för varje sensortyp:

| Sensor      | Sökområdesradie (ungefärlig) | Information |
|-------------|:-------:|---------|
| **Gps**         | 20 m till 30 m | Bestäms av GPS-osäkerheten, bland andra faktorer. De rapporterade siffrorna beräknas för median-GPS-precisionen för mobiltelefoner med A-GPS: 7 meter. |
| **Wi-Fi**        | 50 m till 100 m | Bestäms av intervallet för de trådlösa åtkomstpunkterna. Beror på frekvens, sändarstyrka, fysiska hinder, interferens och så vidare. |
| **BLE-sändare** |  70 m | Bestäms av intervallet för sändaren. Beror på frekvens, överföringsstyrka, fysiska hinder, interferens och så vidare. |

<!-- Reference links in article -->
[1]: https://developer.estimote.com/eddystone/
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
