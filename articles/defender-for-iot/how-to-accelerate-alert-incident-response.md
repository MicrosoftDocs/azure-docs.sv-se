---
title: Påskynda aviserings arbets flöden
description: Förbättra aviseringar och incident arbets flöden.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 64e81e246ec62c8995d0e31629b4f21a2c1096b0
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522554"
---
# <a name="accelerate-alert-workflows"></a>Påskynda aviserings arbets flöden

Den här artikeln beskriver hur du påskyndar aviserings arbets flöden med hjälp av aviserings kommentarer, aviserings grupper och anpassade aviserings regler i Azure Defender för IoT.  Dessa verktyg hjälper dig att:

- Analysera och hantera den stora volymen av aviserings händelser som identifierats i nätverket.

- Hitta och hantera en speciell nätverks aktivitet.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>Påskynda incident arbets flöden med hjälp av aviserings kommentarer

Arbeta med aviserings kommentarer för att förbättra kommunikationen mellan individer och team under undersökningen av en varnings händelse.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="Skärm bild som visar skadlig aktivitet.":::

Använd aviserings kommentarer för att förbättra:

- **Arbets flödes steg**: Ange steg för aviserings minskning.

- **Uppföljning av arbets flöde**: meddela att steg vidtogs.

- **Vägledning för arbets flöde**: ge rekommendationer, insikter eller varningar om händelsen.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="Skärm bild som visar aviserings kommentarer.":::

Listan över tillgängliga alternativ visas i varje avisering. Användare kan välja ett eller flera meddelanden.

Lägga till aviserings kommentarer:

1. På menyn på sidan väljer du **Systeminställningar**.

2. I fönstret **systeminställning** väljer du **aviserings kommentarer**.

3. Ange kommentars texten i rutan **Lägg till kommentarer** . Använd upp till 50 tecken. Kommatecken är inte tillåtna.

4. Välj **Lägg till**.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>Påskynda incident arbets flöden med hjälp av aviserings grupper

Med aviserings grupper kan SOC-team Visa och filtrera aviseringar i sina SIEM-lösningar och sedan hantera dessa aviseringar baserat på företagets säkerhets principer och affärs prioriteringar. Till exempel organiseras aviseringar om nya identifieringar i en identifierings grupp. Den här gruppen innehåller aviseringar som behandlar identifiering av nya enheter, nya virtuella lokala nätverk, nya användar konton, nya MAC-adresser med mera.

Aviserings grupper används när du skapar regler för vidarebefordran för följande partner lösningar:

  - Syslog-servrar

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="Skärm bild som visar hur du skapar en vidarebefordrings regel.":::

Den relevanta aviserings gruppen visas i lösningar för partner utdata. 

### <a name="requirements"></a>Krav

Aviserings gruppen visas i partner lösningar som stöds med följande prefix:

- **katt** för QRadar, ArcSight, syslog CEF, syslog LEEF

- **Aviserings grupp** för syslog-textmeddelanden

- **alert_group** för syslog-objekt

Dessa fält ska konfigureras i partner lösningen för att Visa aviserings gruppens namn. Om det inte finns någon avisering som är associerad med en aviserings grupp, kommer fältet i partner lösningen att visa **na**.

### <a name="default-alert-groups"></a>Standard aviserings grupper

Följande aviserings grupper definieras automatiskt:
|  |  |  |
|--|--|--|
| Onormalt kommunikations beteende | Anpassade aviseringar | Fjärråtkomst |
| Onormalt HTTP-kommunikations beteende | Identifiering | Starta om och stoppa kommandon |
| Autentisering | Ändring av inbyggd program vara | Genomsöka |
| Obehörigt kommunikations beteende | Ogiltiga kommandon | Sensor trafik |
| Bandbredds avvikelser | Internetåtkomst | Misstanke om skadlig kod |
| Buffertspill | Åtgärds problem | Misstanke om skadlig aktivitet |
| Kommando felen | Driftsproblem |  |
| Konfigurations ändringar | Programmeringsspråk |  |

Aviserings grupper är fördefinierade. Kontakta [Microsoft Support](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099)om du vill ha mer information om aviseringar som är kopplade till aviserings grupper och om att skapa anpassade aviserings grupper.

## <a name="customize-alert-rules"></a>Anpassa aviserings regler

Använd anpassade aviserings regler för att mer specifikt hitta aktiviteter av intresse för dig. 

Du kan lägga till anpassade aviserings regler baserat på:

- En kategori, till exempel ett protokoll, en port eller en fil.
- Käll-och mål adresser
- Ett villkor baserat på vald kategori, till exempel en funktion som är associerad med ett protokoll, ett fil namn, en port eller ett transport nummer.
- Ett villkor baserat på datum-och tids referens, till exempel om en identifiering har gjorts på en viss dag eller en viss del av dagen.

Om sensorn identifierar den aktivitet som beskrivs i regeln, skickas aviseringen.
information som enskilda sensorer identifierar. Definiera till exempel en regel som instruerar en sensor att utlösa en avisering baserat på käll-IP, mål-IP eller kommando (inom ett protokoll). När sensorn identifierar den trafik som definierats i regeln genereras en avisering eller händelse.

Du kan också använda varnings regel åtgärder för att instruera Defender för IoT att:

- Tillåt användare att komma åt PCAP-filen från aviseringen.
- Tilldela en allvarlighets grad för aviseringar.
- Generera en händelse i stället för avisering. Den identifierade informationen visas i händelse tids linjen.

:::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="Skärm bild som visar en användardefinierad regel.":::

Varnings meddelandet anger att en användardefinierad regel utlöste aviseringen.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Skärm bild som visar anpassade aviseringar.":::

Så här skapar du en anpassad aviserings regel:

1. Välj **anpassade aviseringar** på sido menyn i en sensor.
1. Välj plus tecknet ( **+** ) för att skapa en regel.
1. Definiera ett regel namn.
1. Välj en kategori eller ett protokoll i fönstret **Kategorier** .
1. Definiera en bestämd käll-och mål-IP-adress eller MAC-adress, eller Välj valfri adress.
1. Definiera ett eller flera regel villkor. Du kan skapa två typer av villkor:
    - Villkor baserat på unika värden som är associerade med den valda kategorin. Välj Lägg till och definiera värdena.
    - Villkor baserat på när aktiviteten identifierades. I avsnittet identifieringar väljer du en tids period och dag då identifieringen ska ske för att skicka aviseringen. Du kan välja att skicka aviseringen om aktiviteten upptäcks när som helst, under eller efter arbets tid. Använd alternativet definiera arbets tid för att instruera Defender för IoT-arbetstimmar för din organisation.
1. Definiera regel åtgärder: 
    - Ange om regeln utlöser ett **larm** eller en **händelse**.
    - Tilldela en allvarlighets grad till aviseringen.
    - Ange om aviseringen ska innehålla en PCAP-fil.
1. Välj **Spara**.

Regeln läggs till i listan **anpassade aviserings regler** där du kan granska grundläggande regel parametrar, den senaste gången regeln utlöstes och mer. Du kan också aktivera och inaktivera regeln från listan.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Skärm bild av en anpassad regel som har lagts till av användaren.":::

## <a name="next-steps"></a>Nästa steg

[Visa informationen som tillhandahålls i aviseringar](how-to-view-information-provided-in-alerts.md)

[Hantera aviseringshändelsen](how-to-manage-the-alert-event.md)
