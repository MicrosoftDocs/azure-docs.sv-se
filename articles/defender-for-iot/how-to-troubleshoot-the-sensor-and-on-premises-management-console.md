---
title: Felsöka sensorn och den lokala hanteringskonsolen
description: Felsök sensorn och den lokala hanterings konsolen för att eliminera eventuella problem som du kan ha.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/3/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 624f60441f56646c3633a2afa79d326c92ba1a37
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523727"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>Felsöka sensorn och den lokala hanteringskonsolen

Den här artikeln beskriver grundläggande fel söknings verktyg för sensorn och den lokala hanterings konsolen. Utöver de objekt som beskrivs här kan du kontrol lera systemets hälso tillstånd på följande sätt:

**Aviseringar**: en avisering skapas när sensor gränssnittet som övervakar trafiken är nere. 

**SNMP**: sensor hälsa övervakas via SNMP. Azure Defender for IoT svarar på SNMP-frågor som skickas från en auktoriserad övervaknings Server. 

**System meddelanden**: när en hanterings konsol styr sensorn kan du vidarebefordra aviseringar om misslyckade sensor säkerhets kopieringar och frånkopplade sensorer.

## <a name="sensor-troubleshooting-tools"></a>Sensor fel söknings verktyg

### <a name="investigate-password-failure-at-initial-sign-in"></a>Undersök lösen ords haveri vid inledande inloggning

När du loggar in på en förkonfigurerad pil sensor för första gången måste du återställa lösen ordet.

Så här återställer du ditt lösen ord:

1. På inloggnings skärmen för Defender för IoT väljer du  **återställning av lösen ord**. Skärmen för **lösen ords återställning** öppnas.

1. Välj antingen **CyberX** eller **support** och kopiera den unika identifieraren.

1. Navigera till Azure Portal och välj **platser och sensorer**.  

1. Välj fliken **Återställ lösen ord för den lokala hanterings konsolen** .

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Välj knappen Återställ lokal hantering för att ladda ned återställnings filen.":::

1. Ange den unika identifierare som du fick på skärmen för **lösen ords återställning** och välj **Återställ**. `password_recovery.zip`Filen har hämtats.

    > [!NOTE]
    > Ändra inte lösen ords återställnings filen. Det är en signerad fil och fungerar inte om du manipulerar den.

1. På skärmen för **lösen ords återställning** väljer du **överför**. Fönstret **överför lösen ords återställnings fil** öppnas.

1. Välj **Bläddra** för att hitta `password_recovery.zip` filen eller dra `password_recovery.zip` till fönstret.

1. Välj **Nästa** och ditt användar-och systemgenererade lösen ord för hanterings konsolen visas sedan.

    > [!NOTE]
    > När du loggar in på en sensor eller lokal hanterings konsol för första gången kommer den att länkas till den prenumeration du anslöt till. Om du behöver återställa lösen ordet för CyberX-eller support användaren måste du välja den prenumerationen. Mer information om hur du återställer en CyberX eller stöder användar lösen ord finns i [återställa en användares lösen ord för sensorn eller den lokala hanterings konsolen](how-to-create-and-manage-users.md#resetting-a-users-password-for-the-sensor-or-on-premises-management-console)

### <a name="investigate-a-lack-of-traffic"></a>Undersök bristande trafik

En indikator visas överst i-konsolen när sensorn känner av att det inte finns någon trafik på någon av de konfigurerade portarna. Den här indikatorn är synlig för alla användare.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="Skärm bild av aviseringen om att ingen trafik har identifierats.":::
 
När det här meddelandet visas kan du undersöka om det inte finns någon trafik. Se till att spänn kabeln är ansluten och att det inte fanns någon förändring i span-arkitekturen.  

Kontakta [Microsoft Support](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)för information om support och fel sökning.

### <a name="check-system-performance"></a>Kontrol lera system prestanda 

När en ny sensor distribueras eller om sensorn till exempel arbetar långsamt eller inte visar några varningar, kan du kontrol lera systemets prestanda.

Kontrol lera system prestandan:

1. I instrument panelen, se till att `PPS > 0` .

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="Skärm bild av ett exempel på en instrument panel."::: 

1. Välj **enheter** på menyn sida.

1. I fönstret **enheter** kontrollerar du att enheterna identifieras.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="Se till att enheterna identifieras.":::

1. På sido menyn väljer du **Data utvinning**.

1. I fönstret **Data utvinning** väljer du **alla** och genererar en rapport.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Generera en ny rapport med hjälp av data utvinning.":::

1. Se till att rapporten innehåller data.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Se till att rapporten innehåller data.":::

1. På sido menyn väljer du **trender & statistik**.

1. I fönstret **trender & statistik** väljer du **Lägg till widget**.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="Lägg till en widget genom att markera den.":::

1. Lägg till en widget och se till att den visar data.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="Kontrol lera att widgeten visar data.":::

1. På menyn på sidan väljer du **aviseringar**. Fönstret **aviseringar** visas.

1. Kontrol lera att aviseringarna har skapats.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="Se till att aviseringar har skapats.":::


### <a name="investigate-a-lack-of-expected-alerts"></a>Undersök brist på förväntade aviseringar

Om **aviserings** fönstret inte visar en avisering som du förväntar dig kontrollerar du följande:

- Kontrol lera om samma avisering redan visas i **aviserings** fönstret som en reaktion på en annan säkerhets instans. Om ja, och den här aviseringen ännu inte har hanterats, visas ingen ny avisering i sensor konsolen.

- Se till att du inte utelämnade den här aviseringen med hjälp av **varnings** reglerna för undantag i hanterings konsolen. 

### <a name="investigate-widgets-that-show-no-data"></a>Undersök widgetar som inte visar några data

Gör följande när widgetarna i fönstret **trender & statistik** inte visar några data:

- [Kontrol lera system prestanda](#check-system-performance).

- Kontrol lera att inställningarna för tid och region har kon figurer ATS korrekt och inte är inställt på en framtida tidpunkt. 

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>Undersök en enhets karta som endast visar sändnings enheter

När enheter som visas på kartan inte visas anslutna till varandra kan något vara fel med konfigurationen för SPAN-porten. Det innebär att du kanske bara ser broadcast-enheter och ingen unicast-trafik.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="Visa dina sändnings enheter.":::

I sådana fall måste du verifiera att du bara kan se sändnings trafiken. Be nätverks teknikern att åtgärda konfigurationen för spänn porten så att du kan se unicast-trafiken.

Verifiera att du bara ser sändnings trafiken:

- På skärmen **Data utvinning** skapar du en rapport med alternativet **alla** . Se om endast broadcast-och multicast-trafik (och ingen unicast-trafik) visas i rapporten.

Eller

- Registrera en PCAP direkt från växeln eller Anslut en bärbar dator med hjälp av wireshark.

### <a name="connect-the-sensor-to-ntp"></a>Anslut sensorn till NTP

Du kan konfigurera en fristående sensor och en hanterings konsol, med sensorer som är kopplade till den, för att ansluta till NTP.

Så här ansluter du en fristående sensor till NTP:

- [Kontakta support teamet om du behöver hjälp](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

Så här ansluter du en sensor som styrs av hanterings konsolen till NTP:

- Anslutningen till NTP konfigureras i hanterings konsolen. Alla sensorer som hanterings konsolen kontrollerar får NTP-anslutningen automatiskt.

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>Undersök när enheter inte visas på kartan eller om du har flera Internet-relaterade aviseringar

Ibland konfigureras ICS-enheter med externa IP-adresser. Dessa ICS-enheter visas inte på kartan. I stället för enheterna visas ett Internet moln på kartan. IP-adresserna för de här enheterna ingår i moln avbildningen.

En annan indikation på samma problem är när flera Internet-relaterade aviseringar visas.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="Flera Internet-relaterade aviseringar.":::

Så här löser du konfigurationen:

1. Högerklicka på moln ikonen på enhets kartan och välj **Exportera IP-adresser**. Kopiera de offentliga intervallet som är privata och Lägg till dem i under näts listan. Mer information finns i [Konfigurera undernät](how-to-control-what-traffic-is-monitored.md#configure-subnets).

1. Generera en ny data utvinnings rapport för Internet anslutningar.

1. I rapporten Data utvinning väljer :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: du att ange administratörs läget och tar bort IP-adresserna för dina ICS-enheter.

### <a name="tweak-the-sensors-quality-of-service"></a>Justera sensorns Quality of service

Om du vill spara dina nätverks resurser kan du begränsa bandbredden för gränssnittet som sensorn använder för dagliga procedurer.

Om du vill begränsa bandbredden för gränssnittet använder du `cyberx-xsense-limit-interface` CLI-verktyget som måste köras med sudo-behörigheter. Verktyget hämtar följande argument:

  - `* -i`: gränssnitt (exempel: ETH0).

  - `* -l`: gräns (exempel: 30 kbit/1 Mbit). Du kan använda följande bandbredds enheter: kbps, Mbit/s, kbit, Mbit eller bps.

  - `* -c`: Clear (för att ta bort gränssnittets bandbredds begränsning).

Så här anpassar du tjänstens kvalitet:

1. Logga in på sensorn CLI som en Defender för IoT-användare och ange `sudo cyberx-xsense-limit-interface-I eth0 -l value` .

   Exempelvis: `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > För en fysisk installation använder du EM1-gränssnittet.

1. Om du vill ta bort gränssnitts begränsningen anger du `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c` .

## <a name="on-premises-management-console-troubleshooting-tools"></a>Fel söknings verktyg för lokal hanterings konsol

### <a name="investigate-a-lack-of-expected-alerts"></a>Undersök brist på förväntade aviseringar

Om en förväntad avisering inte visas i fönstret **aviseringar** kontrollerar du följande:

- Kontrol lera om samma avisering redan visas i **aviserings** fönstret som en reaktion på en annan säkerhets instans. Om ja, och den här aviseringen inte har hanterats ännu, visas inte en ny avisering.

- Kontrol lera att du inte utelämnade den här aviseringen med hjälp av **varnings** reglerna för undantag i den lokala hanterings konsolen.  

### <a name="tweak-the-quality-of-service"></a>Justera tjänst kvaliteten

Om du vill spara nätverks resurserna kan du begränsa antalet aviseringar som skickas till externa system (t. ex. e-post eller SIEM) i en synkroniseringsåtgärd mellan en installation och den lokala hanterings konsolen.

Standard är 50. Det innebär att i en klientsession mellan en installation och den lokala hanterings konsolen, kommer det inte finnas fler än 50 aviseringar till externa system. 

Använd `notifications.max_number_to_report` egenskapen som är tillgänglig i om du vill begränsa antalet aviseringar `/var/cyberx/properties/management.properties` . Ingen omstart krävs efter att du har ändrat den här egenskapen.

Så här anpassar du tjänstens kvalitet:

1. Logga in som en Defender för IoT-användare. 

1. Verifiera standardvärdena:

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   Följande standardvärden visas:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Redigera standardinställningarna:

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. Redigera inställningarna för följande rader:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Spara ändringarna. Ingen omstart krävs.

## <a name="export-information-for-troubleshooting"></a>Exportera information för fel sökning

Förutom verktyg för övervakning och analys av ditt nätverk kan du skicka information till support teamet för ytterligare undersökning. När du exporterar loggar genererar sensorn automatiskt ett eng ång slö sen ord (eng ång slö sen ord) som är unikt för de exporterade loggarna i en separat textfil. 

Så här exporterar du loggar:

1. I den vänstra rutan väljer du **Systeminställningar**.

1. Välj **Exportera loggar**.

    :::image type="content" source="media/how-to-export-information-for-troubleshooting/export-a-log.png" alt-text="Exportera en logg till system stöd.":::

1. I rutan **fil namn** anger du det fil namn som du vill använda för logg exporten. Standardvärdet är det aktuella datumet.

1. För att definiera vilka data du vill exportera väljer du data kategorier:  

    | Exportera kategori | Beskrivning |
    |--|--|
    | **Operativ system loggar** | Välj det här alternativet för att hämta information om operativ systemets tillstånd. |
    | **Installations-/uppgraderings loggar** | Välj det här alternativet för att undersöka installations-och uppgraderings parametrarna. |
    | **Systemets Sanity-utdata** | Välj det här alternativet om du vill kontrol lera system prestanda. |
    | **Avsnitts loggar** | Välj det här alternativet om du vill tillåta avancerad inspektion av protokoll avsnittet. |
    | **Dumpar OS-kernel** | Välj det här alternativet om du vill exportera Kernel-minnesdumpen. En kernel-minnesdump innehåller allt minne som används vid tidpunkten för det problem som uppstod i denna kernel. Dumpnings filens storlek är mindre än den fullständiga minnes dumpningen. Normalt är dumpfilen filen en tredjedel av storleken på det fysiska minnet på systemet. |
    | **Vidarebefordra loggar** | Välj det här alternativet för att undersöka vidarebefordrings reglerna. |
    | **SNMP-loggar** | Välj det här alternativet för att ta emot hälso kontroll information för SNMP. |
    | **Kärn program loggar** | Välj det här alternativet om du vill exportera data om kärn programmets konfiguration och åtgärd. |
    | **Kommunikation med CM-loggar** | Välj det här alternativet om det finns kontinuerliga problem eller avbrott i anslutningen till hanterings konsolen. |
    | **Webb program loggar** | Välj det här alternativet om du vill få information om alla begär Anden som skickats från programmets webb gränssnitt. |
    | **System säkerhets kopiering** | Välj det här alternativet om du vill exportera en säkerhets kopia av alla system data för att undersöka systemets exakta tillstånd. |
    | **Avsnitts statistik** | Välj det här alternativet om du vill tillåta avancerad inspektion av protokoll statistik. |
    | **Databas loggar** | Välj det här alternativet om du vill exportera loggar från system databasen. Genom att undersöka system loggar kan du identifiera system problem. |
    | **Konfiguration** | Välj det här alternativet om du vill exportera information om alla parametrar som kan konfigureras för att kontrol lera att allt har kon figurer ATS korrekt. |

1. Om du vill välja alla alternativ väljer du **Välj alla** nästa för att **välja kategorier**.

1. Välj **Exportera loggar**.

De exporterade loggarna läggs till i listan **arkiverade loggar** . Skicka eng ång slö sen ord till support teamet i ett separat meddelande och medium från de exporterade loggarna. Support teamet kommer bara att kunna extrahera exporterade loggar genom att använda det unika eng ång slö sen ord som används för att kryptera loggarna.

Listan över arkiverade loggar kan innehålla upp till fem objekt. Om antalet objekt i listan hamnar bortom det antalet raderas det tidigaste objektet.

## <a name="next-steps"></a>Nästa steg

- [Visa aviseringar](how-to-view-alerts.md)

- [Konfigurera SNMP MIB-övervakning](how-to-set-up-snmp-mib-monitoring.md)

- [Förstå händelser för sensor från koppling](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
