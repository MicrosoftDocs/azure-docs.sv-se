---
title: StorSimple övervaknings indikatorer | Microsoft Docs
description: Beskriver de ljusbaserade dioderna (lysdioder) och akustiska larm som används för att övervaka status för StorSimple-enheten.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 97209dca7d30de037dbd21f5cc145b2941060e70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015408"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Använd StorSimple övervaknings indikatorer för att hantera din enhet


## <a name="overview"></a>Översikt
Din StorSimple-enhet innehåller ljus avsända dioder (lysdioder) och larm som du kan använda för att övervaka moduler och övergripande status för StorSimple-enheten. Övervaknings indikatorerna finns på maskin varu komponenterna i enhetens primära hölje och EBOD-höljet. Övervaknings indikatorerna kan vara antingen indikatorer eller akustiska larm.

Det finns tre LED lägen som används för att visa status för en modul: grön, blinkar grön till röd-gul eller röd-gul.  

* Gröna indikatorer representerar en felfri drift status.  
* Blinkande grön-till röd-gul lysdioder visar förekomsten av icke-kritiska villkor som kan kräva åtgärder från användaren.  
* Röda-gul-lampor indikerar att det finns ett allvarligt fel i modulen.  

I resten av den här artikeln beskrivs de olika övervaknings indikator lamporna, deras platser på StorSimple-enheten, enhetens status baserat på LYSDIODernas tillstånd och eventuella tillhör ande akustiska larm.

## <a name="front-panel-indicator-leds"></a>Indikator lampor på Front panelen
På Front panelen, som även kallas för *drift panelen* eller *ops panelen*, visas sammanställd status för alla moduler i systemet. Front panelen är identisk med den primära StorSimple-och EBOD-höljet och illustreras nedan.  

   ![Enhets Front Panel][1]

Front panelen innehåller följande indikatorer:  

1. Knappen ljud av
2. Indikator för ström indikator (grön/röd – gul)
3. Indikator för modul Fels indikator (på röd-gul/av)
4. Indikator för logisk fel indikator (på röd-gul/av
5. Visning av enhets-ID  

Den största skillnaden mellan lampor på Front panelen för enheten och de för EBOD-höljet är **system enhetens identifierings nummer** som visas på lampan. Standardenhets-ID som visas på enheten är **00**, medan standardenhets-ID: t som visas i EBOD-kammaren är **01**. På så sätt kan du snabbt skilja mellan enheten och EBOD-höljet när enheten är påslagen. Om enheten är inaktive rad kan du använda den information som finns i [Aktivera på en ny enhet](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) för att skilja enheten från EBOD-höljet.  

## <a name="front-panel-led-status"></a>Status för front panel
Använd följande tabell för att identifiera den status som anges av lysdioderna på Front panelen för enheten eller EBOD-höljet.  

| Systemets strömförsörjning | Modul-fel | Logiskt fel | Varningar | Status |
| --- | --- | --- | --- | --- |
| Röd-gul |OFF |OFF |Ej tillämpligt |Ström spar läge går förlorat, körs på säkerhets kopiering eller LIKSTRÖMs drift och Controller-modulerna har tagits bort. |
| Green |ON |ON |Ej tillämpligt |Test status för OPS-panelens start på (5 SEK) |
| Green |OFF |OFF |Ej tillämpligt |Slå på, alla funktioner är effektiva |
| Green |ON |Ej tillämpligt |Fel lampor för PCM, fläkt Fels lampor |Ett PCM-fel, fläkt fel, över eller under temperatur |
| Green |ON |Ej tillämpligt |I/O-modulens lampor |Ett fel i styrenhets module |
| Green |ON |Saknas |Saknas |Kabinett logiskt fel |
| Green |Utvecklingsverktyget |Ej tillämpligt |INDIKATOR för modulens status i Controller-modulen. Fel lampor för PCM, fläkt Fels lampor |Okänd modultyp har installerats, I2C Bus-fel, konfigurations fel i kontrollantens vitala produkt data (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Indikator lampor för Power kylning module (PCM)
Indikator lampor för Power kylning module (PCM) finns på bak sidan av den primära inne slutningen eller EBOD-kammaren på varje PCM-modul. I det här avsnittet beskrivs hur du använder följande indikatorer för att övervaka status för din StorSimple-enhet.  

* PCM-indikatorer för den primära inne slutningen
* PCM-lampor för EBOD-höljet

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM-indikatorer för den primära inne slutningen
StorSimple-enheten har en 764W PCM-modul med ett extra batteri. Följande bild visar indikator panelen för enheten.  

   ![PCM-indikatorer på den primära inne slutningen][2]

LED text förklaring:

1. AC-strömavbrott
2. Fläkt haveri
3. Batteri fel
4. PCM OK
5. DC-haveri
6. Batteriet är utmärkt  

Status för PCM visas på indikator panelen. Indikator lampan för enhets-PCM har sex lampor. Fyra av de här lysdioderna visar statusen för strömförsörjningen och fläkten. De återstående två lysdioderna visar status för modulen för säkerhets kopierings batteri i PCM. Du kan använda följande tabeller för att fastställa status för PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM-indikator lampor för strömförsörjning och fläkt
| Status | PCM OK (grönt) | Växel ström (gul) | Fläkten fungerar inte (gul) | DC-misslyckande (gul) |
| --- | --- | --- | --- | --- |
| Ingen växel ström (till kabinett) |OFF |OFF |OFF |OFF |
| Ingen växel ström (endast denna PCM) |OFF |ON |OFF |ON |
| AC-presentera PCM på OK |ON |OFF |OFF |OFF |
| PCM-misslyckande (fläkt misslyckande) |OFF |OFF |ON |Ej tillämpligt |
| PCM-fel (via amp, överspänning, över aktuella) |OFF |ON |ON |ON |
| PCM (från tolerans) |ON |OFF |OFF |ON |
| Vänte läge |Blinkande |OFF |OFF |OFF |
| Nedladdning av inbyggd PCM-programvara |OFF |Blinkande |Blinkande |Blinkande |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM-indikator lampor för säkerhets kopierings batteriet
| Status | Batteriet är utmärkt (grönt) | Batteri fel (gul) |
| --- | --- | --- |
| Batteriet är inte tillgängligt |OFF |OFF |
| Batteri som finns och debiteras |ON |OFF |
| Batteri laddning eller underhålls urladdning |Blinkande |OFF |
| Batteri "Soft"-fel (återställnings Bart) |OFF |Blinkande |
| Batteri "hård"-fel (det går inte att återskapa) |OFF |ON |
| Avmilitärt batteri |Blinkande |OFF |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM-lampor för EBOD-höljet
EBOD-kabinettet har en 580W PCM och inget extra batteri. EBOD-Kabinettets PCM-panel har indikator lampor för strömförsörjning och fläkt. Följande bild visar lysdioderna.

   ![PCM-lampor på EBOD-höljet][3] 

Du kan använda följande tabell för att fastställa status för PCM.  

| Status | PCM OK (grönt) | Växel ström (gul) | Fläkten fungerar inte (gul) | DC-misslyckande (gul) |
| --- | --- | --- | --- | --- |
| Ingen växel ström (till kabinett) |OFF |OFF |OFF |OFF |
| Ingen växel ström (endast denna PCM) |OFF |ON |OFF |ON |
| AC presentera PCM på – OK |ON |OFF |OFF |OFF |
| PCM-misslyckande (fläkt misslyckande) |OFF |OFF |ON |X |
| PCM-fel (via amp, överspänning, över aktuella |OFF |ON |ON |ON |
| PCM (från tolerans) |ON |OFF |OFF |ON |
| Vänte läges modell |Blinkande |OFF |OFF |OFF |
| Nedladdning av inbyggd PCM-programvara |OFF |Blinkande |Blinkande |Blinkande |

## <a name="controller-module-indicator-leds"></a>Indikator lampor för Controller-modulen
StorSimple-enheten innehåller lampor för den primära styrenheten och modulerna för EBOD Controller.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Övervaknings indikatorer för den primära styrenheten
Följande bild hjälper dig att identifiera lysdioderna på den primära kontroll enheten. (Alla komponenter listas för att hjälpa till med orientering.)  

   ![Övervaknings indikatorer – primär kontrollant][4]

Använd följande tabell för att avgöra om Controller-modulen fungerar som den ska.  

### <a name="controller-indicator-leds"></a>Indikator lampor för kontrollant
| SPOLNING | Beskrivning |
| --- | --- |
| Indikator för ID (blå) |Anger att modulen identifieras. Om den blå INDIKATORn blinkar på en styrenhet som körs, är kontrollanten den aktiva styrenheten och den andra är vänte läges styrenheten. Mer information finns i [identifiera den aktiva kontrollanten på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Fel indikator (gul) |Indikerar ett fel i kontrollanten. |
| Indikator för OK (grönt) |Stadig grön visar att kontrollanten är OK. Blinkande grönt indikerar ett konfigurations fel för styrenhetens VPD. |
| Indikatorer för SAS-aktivitet (grönt) |Stadig grön indikerar en anslutning utan aktuell aktivitet. Blinkande grönt indikerar att anslutningen har pågående aktivitet. |
| Ethernet-status lampor |Höger indikerar länk-/nätverks aktivitet: (stadig grön) länk aktiv, (blinkar grön) nätverks aktivitet. Vänster sida indikerar nätverks hastighet: (gul) 1000 MB/s, (grönt) 100 MB/s, och (av) 10 MB/s. Beroende på komponent modellen kan det här ljuset blinka även om nätverks gränssnittet inte är aktiverat. |
| PUBLICERA LED |Anger start förloppet när kontroll enheten aktive ras. Om StorSimple-enheten inte kan starta kan den här INDIKATORn hjälpa Microsoft Support att identifiera den punkt i Start processen där felet uppstod. |

> [!IMPORTANT]
> Om fel INDIKATORn är tänd, finns det ett problem med Controller-modulen som kan lösas genom att starta om kontrollanten. Kontakta Microsoft Support om du inte kan lösa problemet genom att starta om kontrollanten.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Övervaknings lampor för EBOD (EBOD-kabinettet)
Var och en av de 6 GB/s SAS-EBOD styrenheter har lysdioder som anger dess status som visas i följande bild.  

  ![Övervaknings lampor – EBOD hölje][5]

Använd följande tabell för att avgöra om modulen EBOD Controller fungerar normalt.  

### <a name="ebod-controller-module-indicator-leds"></a>Indikator lampor för EBOD Controller-modulen
| Status | I/O-modul OK (grön) | I/O-modulens fel (gul) | Värd ports aktivitet (grön) |
| --- | --- | --- | --- |
| Kontrollant-modul OK |ON |OFF |- |
| Fel i styrenhets modul |OFF |ON |- |
| Ingen anslutning till extern värd port |- |- |OFF |
| Port anslutning för extern värd – ingen aktivitet |- |- |ON |
| Anslutning till extern värd port-aktivitet |- |- |Blinkande |
| Metadata för kontrollant-modul |Blinkande |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Indikator lampor för disk enheten för den primära inne slutningen och EBOD-höljet
StorSimple-enheten har disk enheter som finns i både den primära och EBOD-höljet. Varje disk enhet innehåller övervaknings indikator lampor, enligt beskrivningen i det här avsnittet. 

För disk enheterna visas enhetens status med en grön indikator och en röd-gul lampa monterad på fram sidan av varje enhets transport modul. Följande bild visar lysdioderna.

  ![Indikatorer för disk enhet][6]

Använd följande tabell för att fastställa tillståndet för varje disk enhet, vilket i sin tur påverkar den övergripande indikator statusen för Front panelen.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Indikator lampor för disk enheten för EBOD-höljet
| Status | Indikator för aktivitets OK (grön) | Fel LED (röd-gul) | Indikator för tillhör ande Ops-panel |
| --- | --- | --- | --- |
| Ingen enhet installerad |OFF |OFF |Inget |
| Enhet installerad och drift |Blinkar på/av med aktivitet |X |Inget |
| SES (SCSI Enclosure Services) enhets identitets uppsättning |ON |Blinkar 1 sekund på/1 sekund av |Inget |
| SES enhets fel bit uppsättning |ON |ON |Logiskt fel (rött) |
| Ström styrnings krets-problem |OFF |ON |Module-fel (röd) |

## <a name="audible-alarms"></a>Akustiska larm
En StorSimple-enhet innehåller akustiska larm som är kopplade till både den primära inne slutningen och EBOD-höljet. Ett akustiskt larm finns på Front panelen (även kallat ops-panelen) för båda höljen. Akustiskt larm anger när ett fel tillstånd föreligger. Följande villkor aktiverar larmet:  

* Fläkt fel eller fel
* Spänning utanför intervallet
* Över eller under temperatur villkor
* Termisk buffertöverskridning
* System fel
* Logiskt fel
* El försörjnings fel
* Borttagning av en Power kylning module (PCM)  

I följande tabell beskrivs de olika larm tillstånden.  

### <a name="alarm-states"></a>Larm tillstånd
| Larm tillstånd | Action | Åtgärd med knappen Muted nedtryckt |
| --- | --- | --- |
| S0 |Normal läge: tyst |PIP två gånger |
| S1 |Fel läge: 1 sekund på/1 sekund av |Över gång till S2 eller S3 (se kommentarer) |
| S2 |Påminn-läge: återkommande ljud signal |Inget |
| S3 |Tyst läge: tyst |Inget |
| S4 |Kritiskt fel läge: kontinuerligt larm |Inte tillgängligt: Inaktivera inte aktiverat |

> [!NOTE]
> * I alarm State S1, om du inte trycker på ljud av igen inom 2 minuter, övergår läget automatiskt till S2 eller S3.  
> * Larm tillstånd S1 till S4 återgår till S0 när fel tillståndet har rensats.  
> * Tillstånd för kritiska fel i S4 kan anges från alla andra tillstånd.  


Du kan stänga av ljud larmet genom att trycka på ljud av-knappen på ops-panelen. Automatisk ljud avstängning sker efter två minuter om växeln MUTE inte körs manuellt. När larmet är avstängt fortsätter det att köras med korta tillfälliga signaler för att indikera att det fortfarande finns ett problem. Larmet kommer att vara tyst när alla problem är avmarkerade.

I följande tabell beskrivs de olika larm villkoren.

### <a name="alarm-conditions"></a>Larm villkor
| Status | Allvarlighetsgrad | Varningar | Indikator för OPS-panel |
| --- | --- | --- | --- |
| PCM-avisering – förlust av LIKSTRÖMs kraft från en enda PCM |Fel – ingen förlust av redundans |S1 |Modul-fel |
| PCM-avisering – förlust av LIKSTRÖMs kraft från en enda PCM |Fel – förlust av redundans |S1 |Modul-fel |
| PCM-fläkten fungerar inte |Fel – förlust av redundans |S1 |Modul-fel |
| MITTPLANSSAMMANKOPPLING-modulen identifierade PCM-fel |Problemet |S1 |Modul-fel |
| PCM borttaget |Konfigurationsfel |Inget |Modul-fel |
| Kabinett konfigurations fel |Fel – kritiskt |S1 |Modul-fel |
| Varning om låg varnings temperatur |Varning |S1 |Modul-fel |
| Varning om hög varnings temperatur |Varning |S1 |Modul-fel |
| Larm för över temperatur |Fel – kritiskt |S1 |Modul-fel |
| I2C Bus-haveri |Fel – förlust av redundans |S1 |Modul-fel |
| Kommunikations fel i OPS-panel (I2C) |Fel – kritiskt |S1 |Modul-fel |
| Fel i styrenhet |Fel – kritiskt |S1 |Modul-fel |
| Fel i MITTPLANSSAMMANKOPPLING-gränssnitt |Fel – kritiskt |S1 |Modul-fel |
| Fel i MITTPLANSSAMMANKOPPLING-gränssnitt – inga fungerande moduler återstår |Fel – kritiskt |S4 |Modul-fel |
| MITTPLANSSAMMANKOPPLING-modulen har tagits bort |Varning |Inget |Modul-fel |
| Enhetens energi kontroll fel |Varning – ingen förlust av enhets strömförsörjning |S1 |Modul-fel |
| Enhetens energi kontroll fel |Fel – kritiskt; förlust av enhets kraft |S1 |Modul-fel |
| Enheten har tagits bort |Varning |Inget |Modul-fel |
| Otillräcklig ström tillgänglig |Varning |inget |Modul-fel |

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple maskin varu komponenter och status](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


