---
title: Generera trender och statistik rapporter
description: Få insikt i nätverks aktivitet, statistik och trender genom att använda Defender för IoT-trender och statistik-widgetar.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 2/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 7cb3848f3711b6bf10c316ba4f5321286e2260fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706643"
---
# <a name="sensor-trends-and-statistics-reports"></a>Sensor trender och statistik rapporter

Du kan skapa widgets grafer och cirkel diagram för att få insyn i nätverks trender och statistik. Widgetar kan grupperas under användardefinierade instrument paneler.

> [!NOTE]
> Administratörer och säkerhets analyser kan skapa trender och statistik rapporter.

Instrument panelen består av widgetar som grafiskt beskriver följande typer av information:

- Trafik efter port
- Högsta trafik efter port
- Kanal bandbredd
- Total bandbredd
- Aktiv TCP-anslutning
- Högsta bandbredd per VLAN
- Enheter:
  - Nya enheter
  - Upptagna enheter
  - Enheter efter leverantör
  - Enheter per operativ system
  - Antal enheter per VLAN
  - Frånkopplade enheter
- Anslutningen faller efter timmar
- Aviseringar för incidenter efter typ
- Åtkomst till databas tabell
- Widgetar protokoll avsnitt
- DELTAV
  - Distribution av DeltaV Roc-åtgärder
  - DeltaV ROC-händelser efter namn
  - DeltaV händelser efter tid
- AMS
  - AMS trafik efter server port
  - AMS trafik via kommando
- Ethernet-och IP-adress:
  - Ethernet-och IP-CIP av tjänster
  - Ethernet-och IP-datatrafik per CIP-klass
  - Ethernet-och IP-adress trafik via kommando
- OPC
  - OPC topp hanterings åtgärder
  - OPC främsta I/O-åtgärder
- Siemens S7:
  - S7 trafik efter kontroll funktion
  - S7 trafik per underfunktion
- LOKALT
  - Antal enheter per VLAN
  - Högsta bandbredd per VLAN
- 60870-5-104
  - IEC-60870-trafik efter ASDU
- BACNET
  - BACnet-tjänster
- DNP3
  - DNP3 trafik efter funktion
- SRTP:
  - SRTP trafik per service kod
  - SRTP fel per dag
- SuiteLink:
  - SuiteLink-Taggar med översta frågan
  - SuiteLink för numerisk tagg
- IEC-60870-trafik efter ASDU
- DNP3 trafik efter funktion
- MMS-trafik per tjänst
- Modbus trafik efter funktion
- OPC-UA-trafik per tjänst

> [!NOTE]
>  Tiden i widgeten anges enligt sensor tiden.

## <a name="create-reports"></a>Skapa rapporter

Så här visar du instrument paneler och widgetar:

Välj **trender & statistik** på sido menyn.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Skärm bild av en undersökning.":::

Som standard visas resultat för identifieringar under de senaste sju dagarna. Du kan använda filter verktyg för att ändra det här intervallet. Till exempel en texts ökning i fritext.

## <a name="create-a-dashboard"></a>Skapa en instrument panel

Skapa en ny instrument panel genom att välja den nedrullningsbara menyn för **instrument panelen** . Du kan skapa och lägga till så många widgetar som en instrument panel.

Du kan skapa anpassade instrument paneler med hjälp av följande alternativ:

- Lägga till en widget i instrument panelen

- Ta bort en widget från instrument panelen

- Ändra filtret för widgeten

- Ändra storlek på en widget

- Ändra platsen för en widget

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="Ändra platsen för en widget.":::

Så här skapar du en anpassad instrument panel:

1. Välj **trender och statistik** i den vänstra panelen.

1. Välj List menyn **Välj instrument panel** och välj knappen **skapa instrument panel** .

1. Ange ett beskrivande namn för den nya instrument panelen och välj **skapa**.

1. Välj **Lägg till widget** högst upp till vänster på sidan.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Välj widgeten från widgeten arkiv.":::

1. **Säkerhets**-och **operativa** widgetar är tillgängliga överst till höger i fönstret. Välj mellan olika kategorier och protokoll. En kort beskrivning med en miniatyr bild visas med varje widget. Använd rullnings listen för att visa alla tillgängliga widgetar.

1. Välj en widget med knappen **Klicka för att lägga till** . Widgeten visas direkt på instrument panelen.

Ta bort en instrument panel:

1. Välj instrument panelens namn på den nedrullningsbara menyn.

1. Välj ikonen **ta bort** och välj sedan **OK**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="Välj ikonen Ta bort för att ta bort instrument panelen.":::

Så här redigerar du ett namn på instrument panelen:

1. Välj instrument panelens namn på den nedrullningsbara menyn.

1. Välj **redigerings** ikonen.
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="Välj redigerings ikonen för att redigera namnet på din instrument panel.":::

1. Ange ett nytt namn på instrument panelen och välj **Spara**.
 
Ange standard instrument panelen:

1. Välj instrument panelens namn på den nedrullningsbara menyn.

1. Välj **stjärn** ikonen för att välja den instrument panel som ska anges som standard instrument panel.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="Välj stjärn ikonen för att välja standard instrument panel."::: 

Ändra filtrerings data i en widget:

1. Välj **filter** ikonen.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="Välj filter ikonen för att ange parametrar för widgeten.":::

1. Redigera de nödvändiga parametrarna.

1. Välj **OK**.

Ta bort en widget:

- Välj :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: ikonen.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="Välj X för att ta bort widgeten.":::

## <a name="creating-widgets"></a>Skapa widgetar 

I widgets arkivet kan du välja widgetar efter kategori och protokoll. Du kan visa den **säkerhet** eller de **operativa** widgetar som är tillgängliga genom att välja dem.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Välj widgeten från widgeten arkiv.":::

Varje widget innehåller detaljerad information om system trafik, nätverks statistik, protokoll statistik, enhets-och aviserings information. Ett meddelande visas när det inte finns några data för widgeten.

Du kan ta bort ett avsnitt från cirkeln, i ett cirkel diagram, för att se den relativa betydelsen av de återstående segmenten tydligare. Välj segmentets namn i förklaringen längst ned på skärmen för att göra detta.

I följande avsnitt finns exempel på användnings fall för några av widgetarna.

### <a name="busy-devices-widget"></a>Widgeten upptagna enheter

Den här widgeten visar de fem översta vanligaste enheterna. I **redigerings** läge kan du filtrera efter kända protokoll.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="En vy av widgeten för upptagen enhet.":::

### <a name="total-bandwidth-widget"></a>Widgeten total bandbredd

Den här widgeten spårar bandbredden i Mbit/s (megabit per sekund). Bandbredden anges på y-axeln, med datumet som visas på x-axeln. I **redigerings** läget kan du filtrera resultat efter klient, Server, server port eller undernät. En knapp beskrivning visas när du hovrar markören över grafen.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="En vy av widgeten total bandbredd.":::

### <a name="channels-bandwidth-widget"></a>Widgeten kanaler bandbredd

Den här widgeten visar de fem främsta trafik kanalerna. Du kan filtrera efter adress och ange antalet visade resultat. Välj nedpilen för att visa fler kanaler.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="En vy av widgeten kanaler bandbredd.":::

### <a name="traffic-by-port-widget"></a>Widgeten trafik efter port

Den här widgeten visar trafiken efter port, som anges av ett cirkel diagram med varje port som anges av en annan färg. Mängden trafik i varje port är proportionerlig till storleken på dess del av cirkeln.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="En vy över widgeten trafik efter port.":::

### <a name="new-devices-widget"></a>Widgeten nya enheter

Den här widgeten visar det nya enhets stapeldiagrammet, som anger hur många nya enheter som upptäcktes vid ett visst datum.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="En vy av widgeten nya enheter.":::

### <a name="protocol-dissection-widgets"></a>Widgetar protokoll avsnitt

Den här widgeten visar ett cirkel diagram som ger dig en överblick över trafiken per protokoll, som har avvisats av funktions koder och tjänster. Storleken på varje sektor i cirkeln är proportionell mot mängden trafik i förhållande till de andra sektorerna.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="En vy av widgeten protokoll Dissection.":::

### <a name="active-tcp-connections-widget"></a>Widgeten Active TCP-anslutningar

Den här widgeten visar ett diagram som visar antalet aktiva TCP-anslutningar i systemet.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="En vy av widgeten Active TCP-anslutningar.":::

### <a name="incident-by-type-widget"></a>Widgeten incident per typ

Den här widgeten visar ett cirkel diagram som visar antalet incidenter efter typ. Detta är antalet aviseringar som genereras av varje motor under en fördefinierad tids period.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="En vy av widgeten incident efter typ.":::

## <a name="devices-by-vendor-widget"></a>Widgeten enheter efter leverantör

Den här widgeten visar ett cirkel diagram som visar antalet enheter per leverantör. Antalet enheter för en speciell leverantör är proportionellt till storleken på enhetens leverantörs del av disken i förhållande till andra enhets leverantörer.

## <a name="number-of-devices-per-vlan-widget"></a>Widgeten antal enheter per VLAN

Den här widgeten visar ett cirkel diagram som visar antalet identifierade enheter per VLAN. Storleken på varje sektor i cirkeln är proportionell mot antalet identifierade enheter i förhållande till de andra sektorerna.

Varje VLAN visas med den VLAN-tagg som tilldelats av sensorn eller namnet som du har lagt till manuellt.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="En vy av widgeten antal enheter.":::

### <a name="top-bandwidth-by-vlan-widget"></a>Widgeten Top bandbredd per VLAN

Den här widgeten visar bandbredds användningen av VLAN. Som standard visar widgeten fem VLAN med högsta bandbredds användning.

Du kan filtrera data efter den period som visas i widgeten. Välj nedpilen för att visa fler resultat.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="En vy av widgeten Top bandbredd efter VLAN.":::

## <a name="system-report"></a>System rapport

Så här hämtar du system rapporten: 

1. Välj **trender & statistik** på sido menyn.

1. Välj **System rapport** i det övre högra hörnet. Rapporten hämtas automatiskt.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="Klicka på knappen system rapport om du vill ladda ned en kopia av system rapporten.":::

System rapporten är en PDF-fil som innehåller alla data i systemet:

  - Enheter

  - Aviseringar

  - Information om nätverks princip

## <a name="devices-in-a-system-report"></a>Enheter i en system rapport 

I system rapporten visas en lista över alla enheter och deras information. Till exempel typ, namn och protokoll som används. System rapporten visar också en lista över enheter per leverantör.

## <a name="alerts-in-system-report"></a>Aviseringar i System rapport 

I system rapporten visas en lista över alla aviseringar med information, till exempel datum och allvarlighets grad.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="En vy över aviseringarna i system rapporterna.":::

## <a name="network-information-in-system-report"></a>Nätverksinformation i System rapport 

System rapporten innehåller i detalj, din nätverks bas linje. Till exempel DNP3 funktions kod och öppna portar per anslutning.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="En vy av funktionen DNP3 i system rapporten.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="En vy av rapporten öppna port per anslutning.":::

## <a name="see-also"></a>Se även

[Rapportering](how-to-create-risk-assessment-reports.md) 
 om riskbedömning Frågor om sensor [Data utvinning](how-to-create-data-mining-queries.md) 
 [Rapportering om angrepps vektor](how-to-create-attack-vector-reports.md)
