---
title: Övervaka Azure FXT Edge Filer
description: Övervaka maskin varu status för Azure FXT Edge-filer hybrid Storage cache
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 86e1d74d5e4ab9f6e799c73bcf0d807d0d874f21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219725"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Övervaka maskin varu status för Azure FXT Edge-filer

Azure FXT Edge-filer hybrid Storage cache-systemet har flera status lampor som är inbyggda i chassit för att hjälpa administratörer att förstå hur maskin varan fungerar.

## <a name="system-health-status"></a>System hälso status

Om du vill övervaka cache-åtgärder på en högre nivå, använder du kontroll panelens **instrument** panels sida, enligt beskrivningen i [kontroll panelens instrument panels guide](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Maskin varu status lampor

I det här avsnittet beskrivs de olika status lampor som är inbyggda i Azure FXT Edge-maskin vara.

### <a name="hard-drive-status-leds"></a>Status lampor för hård disk

![bild av hård disk, vågrätt, med bild text etiketter 2 (övre vänstra hörnet), 1 (nedre vänstra hörnet) och 3 (höger sida)](media/fxt-monitor/fxt-drive-callouts.png)

Varje enhets bärare har två status lampor: en aktivitets indikator (1) och en status indikator (2).

* Aktivitetens lysdiod (1) tänds när enheten används.
* Status LAMPAn (2) anger enhetens villkor genom att använda koderna i tabellen nedan.

| Status för enhets status              | Innebörd  |
|-------------------------------------|----------------------------------------------------------|
| Blinkar grönt två gånger per sekund      | Identifiera enhet *eller* <br> Enheten förbereds för borttagning  |
| Av (unlit)                         | Systemet har inte slutat att starta *eller* <br>Enheten är redo att tas bort |
| Blinkar grönt, gult och av       | Enhets haveri förutsägs   |
| Blinkar fyra gånger per sekund | Enheten misslyckades   |
| Solid grönt                         | Enheten är online |

Enhetens högra sida (3) är märkt med enhetens kapacitet och annan information.

Enhets nummer skrivs ut på utrymmet mellan enheter. I Azure FXT Edge-filer är enhet 0 den övre vänstra enheten och enhet 1 är direkt under den. Numreringen fortsätter i det mönstret.

![Foto av ett hård disk fack i FXT-chassit, som visar enhets nummer och kapacitets etiketter](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Vänster kontroll panel

Den vänstra kontroll panelen har olika status indikator indikatorer (1) och en stor ljus indikator för system hälsa (2).

![vänster status panel, med 1 etikettering av status indikatorer till vänster och 2 som anger att indikatorn för stora system hälso indikatorer visas till höger](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Kontroll panels status indikatorer

Status indikatorerna till vänster visar en solid gul lampa om det uppstår ett fel i systemet. I tabellen nedan beskrivs möjliga orsaker och lösningar på felen.

Om felet kvarstår när du har försökt med dessa lösningar kan du [kontakta supporten](fxt-support-ticket.md) för hjälp.

| Ikon | Beskrivning | Feltillstånd | Möjliga lösningar |
|----------------|---------------|--------------------|----------------------|
| ![enhets ikon](media/fxt-monitor/fxt-hd-icon.jpg) | Enhets status | Enhets fel | Kontrol lera systemets händelse logg för att avgöra om enheten har ett fel eller <br>Kör lämpligt test för online-diagnostik; Starta om systemet och kör Embedded Diagnostics (ePSA) eller <br>Om enheterna har kon figurer ATS i en RAID-matris startar du om systemet och anger verktyget för konfiguration av värd korts program |
|![temperatur ikon](media/fxt-monitor/fxt-temp-icon.jpg) | Temperatur status | Termiskt fel – en fläkt har fel eller så ligger den omgivande temperaturen utanför intervallet | Sök efter följande adresser bara villkor: <br>En fläkt fläkt saknas eller har misslyckats <br>Systemets Cover, Air-Shroud, minnesmodulen tom eller backa filler-hakparentesen tas bort <br>Omgivnings temperaturen är för hög <br>Externt luft flöde är blockerat |
|![El-ikon](media/fxt-monitor/fxt-electric-icon.jpg) | Elektrisk status | Elektriskt fel – till exempel spänning utanför intervallet, misslyckade PSU eller en misslyckad spännings regulator |  Kontrol lera systemets händelse logg eller system meddelanden för det aktuella problemet. Om det uppstår ett PSU problem kontrollerar du status LAMPAn för PSU och flyttar PSU igen om det behövs. |
|![minnes ikon](media/fxt-monitor/fxt-memory-icon.jpg) | Minnes status | Minnes fel | Kontrol lera systemets händelse logg eller system meddelanden för att se platsen för det misslyckade minnet. Placera om minnesmodulen. |
|![PCIe-ikon](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe-status | PCIe-kort fel | Starta om systemet. uppdatera driv rutiner för PCIe-kort; installera om kortet |

### <a name="system-health-status-indicator"></a>Status indikator för system hälsa

Den stora lit-knappen till höger om den vänstra kontroll panelen anger allmän system status och används också som enhets lokaliserings ljus i system-ID-läge.

Tryck på knappen system hälsa och ID för att växla mellan system-ID-läge och system hälso läge.

|Tillstånd för system hälso status | Villkor |
|-------------------------------------------|-----------------------------------------------|
| Heldragen blå | Normal drift: systemet är aktiverat, fungerar normalt och system-ID-läget är inte aktivt. <br/>Tryck på knappen system hälsa och ID om du vill växla till system-ID-läge. |
| Blinkande blå | System-ID-läget är aktivt. Tryck på knappen system hälsa och system-ID om du vill växla till system hälso läge. |
| Solid gul | Systemet är i fel säkert läge. [Kontakta Microsofts kund service och support](fxt-support-ticket.md)om problemet kvarstår. |
| Blinkande gul | Systemfel. Se efter i system händelse loggen om det finns några fel meddelanden. Information om händelse-och fel meddelanden som genereras av den inbyggda program varan och agenter som övervakar system komponenter finns i Sök sidan för felkoder på qrl.dell.com. |
