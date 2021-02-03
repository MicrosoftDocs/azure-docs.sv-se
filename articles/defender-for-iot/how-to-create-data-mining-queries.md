---
title: Skapa data utvinnings rapporter
description: generera omfattande och detaljerad information om dina nätverks enheter på olika lager, till exempel protokoll, versioner av inbyggd program vara eller programmerings kommandon.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/20/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: edeb383317d4bdc0e7beef1f8390ddabd350f002
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507578"
---
# <a name="sensor-data-mining-queries"></a>Frågor om sensor Data utvinning

## <a name="about-sensor-data-mining-queries"></a>Om sensor data utvinnings frågor

Med data utvinnings verktyg kan du generera omfattande och detaljerad information om dina nätverks enheter på olika lager. Du kan till exempel skapa en fråga baserat på:

- Tids perioder

- Anslutningar till Internet

- Portar

- Protokoll

- Versioner av inbyggd program vara

- Programmerings kommandon

- Inaktivitet av enheten

Du kan finjustera rapporten baserat på filter. Du kan till exempel fråga ett särskilt undernät där den inbyggda program varan har uppdaterats.

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="Lista över aktiva enheter.":::

Det finns olika verktyg för att hantera frågor. Du kan till exempel exportera och spara.

> [!NOTE]
> Administratörer och säkerhets analytiker har åtkomst till data utvinnings alternativ.

### <a name="dynamic-updates"></a>Dynamiska uppdateringar

Frågor för Data utvinning som du skapar uppdateras dynamiskt varje gången du öppnar dem. Exempel:

- Om du skapar en rapport för versioner av inbyggd program vara på enheter den 1 juni och öppnar rapporten igen den 10 juni kommer den här rapporten att uppdateras med den information som är korrekt för 10 juni.

- Om du skapar en rapport för att identifiera nya enheter som identifierats under de senaste 30 dagarna den 1 juni och öppnar rapporten den 30 juni, visas resultaten för de senaste 30 dagarna.

### <a name="data-mining-use-cases"></a>Användnings fall för Data utvinning

Du kan använda frågor för att hantera en mängd olika säkerhets behov för olika säkerhets team:

- **SOC incident svar**: generera en rapport i real tid för att hjälpa dig att hantera omedelbara incident åtgärder. Data utvinning kan till exempel generera en rapport för en lista över enheter som kan behöva korrigeras.

- **Data utredning**: generera en rapport som baseras på historiska data för utrednings rapporter.

- **Nätverks integritet**: generera en rapport som hjälper till att förbättra den övergripande nätverks säkerheten. Du kan till exempel generera en rapport som visar en lista över enheter med svaga autentiseringsuppgifter.

- **Synlighet**: generera en rapport som täcker alla frågedata för att visa alla bas linje parametrar i nätverket.

## <a name="data-mining-storage"></a>Data utvinnings lagring

Data utvinnings information sparas och lagras kontinuerligt, förutom när en enhet tas bort. Resultat för Data utvinning kan exporteras och lagras externt på en säker server. Dessutom utför sensorn automatisk daglig säkerhets kopiering för att säkerställa system kontinuitet och data bevarande.


## <a name="predefined-data-mining-queries"></a>Fördefinierade frågor för Data utvinning

Följande fördefinierade frågor är tillgängliga. Dessa frågor genereras i real tid.

- **CVEs**: en lista över enheter som har identifierats med kända sårbarheter under de senaste 24 timmarna.

- **Exkluderade CVEs**: en lista över alla CVEs som har uteslutits manuellt. Om du vill uppnå mer exakta resultat i VA-rapporter och angrepps vektorer kan du anpassa CVE-listan manuellt genom att inkludera och utesluta CVEs.

- **Internet aktivitet**: enheter som är anslutna till Internet.

- Ej **aktiva enheter**: enheter som inte har kommunicerats under de senaste sju dagarna.

- **Aktiva enheter**: aktiva nätverks enheter under de senaste 24 timmarna.

- **Fjärråtkomst**: enheter som kommunicerar via protokoll för fjärr anslutning.

- **Programmerings kommandon**: enheter som skickar industriell programmering.

Dessa rapporter är automatiskt tillgängliga från sidan **rapporter** , där ro-användare och andra användare kan visa dem. RO-användare kan inte komma åt data utvinnings rapporter.

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Skärmen Data utvinning.":::

## <a name="create-a-data-mining-query"></a>Skapa en data utvinnings fråga

Så här skapar du en data utvinnings rapport:

1. Välj **Data utvinning** på sido menyn. Fördefinierade föreslagna rapporter visas automatiskt.

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Välj data utvinning från sido rutan.":::

2. Välj :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::.

3. Välj **ny rapport** och definiera rapporten.

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="Skapa en ny rapport genom att fylla i den här skärmen.":::

   Följande parametrar är tillgängliga:

   - Ange ett rapport namn och en beskrivning.

   - För kategorier väljer du antingen:

      - **Kategorier (alla)** om du vill visa alla rapport resultat för alla enheter i nätverket.

      - **Allmän** för att välja standard kategorier.

   - Välj specifika rapport parametrar som intresserar dig.

   - Välj en sorterings ordning (sortera **efter**). Sortera resultat baserat på aktivitet eller kategori.

   - Välj **Spara till rapport sidor** för att spara rapport resultatet som en rapport som kan nås från **rapport** sidan. Detta gör det möjligt för RO-användare att köra rapporten som du har skapat.

   - Välj **filter (Lägg till)** om du vill lägga till fler filter. Jokertecken stöds.

   - Ange en enhets grupp (definieras i enhets kartan).

   - Ange en IP-adress.

   - Ange en port.

   - Ange en MAC-adress.

4. Välj **Spara**. Rapport resultatet öppnas på **data utvinnings** sidan.

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="Rapportera resultat som visas på data utvinnings sidan.":::

### <a name="manage-data-mining-reports"></a>Hantera data utvinnings rapporter

I följande tabell beskrivs hanterings alternativ för Data utvinning:

| Ikon bild | Description |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | Redigera rapport parametrarna. |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | Exportera som PDF. |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |Exportera som CSV. |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | Visa ytterligare information, till exempel datumet då det senast ändrades. Använd den här funktionen för att skapa en ögonblicks bild av frågeresultatet. Du kan behöva göra detta för ytterligare undersökningar med team ledare eller SOC-analytiker, till exempel. |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | Visa på sidan **rapporter** eller dölj på sidan **rapporter** . :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="Dölj eller Visa dina rapporter."::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | Ta bort rapporten. |

#### <a name="create-customized-directories"></a>Skapa anpassade kataloger 

Du kan organisera den omfattande informationen för data utvinnings frågor genom att skapa kataloger för kategorier. Du kan till exempel skapa kataloger för protokoll eller platser.

Så här skapar du en ny katalog:

1. Välj :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: om du vill lägga till en ny katalog.

2. Välj **ny katalog** för att visa det nya katalog formuläret.

3. Namnge den nya katalogen.

4. Dra de nödvändiga rapporterna till den nya katalogen. Du kan när som helst dra rapporten tillbaka till huvudvyn.

5. Högerklicka på den nya katalogen för att öppna, redigera eller ta bort den.

#### <a name="create-snapshots-of-report-results"></a>Skapa ögonblicks bilder av rapport resultat

Du kan behöva spara vissa frågeresultat för ytterligare undersökning. Du kan till exempel behöva dela resultat med olika säkerhets team.

Ögonblicks bilder sparas i rapport resultaten och genererar inte dynamiska frågor.

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="Ögonblicks bilder.":::

Så här skapar du en ögonblicks bild:

1. Öppna den nödvändiga rapporten.

2. Välj informations ikonen :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: .

3. Välj **bli ny**.

4. Ange ett namn för ögonblicks bilden och välj **Spara**.

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="Ta en ögonblicks bild.":::

#### <a name="customize-the-cve-list"></a>Anpassa CVE-listan

Du kan anpassa CVE-listan manuellt på följande sätt:

  - Inkludera/exkludera CVEs

  - Ändra CVE-poängen

Utföra manuella ändringar i CVE-rapporten:

1.  På sido menyn väljer du **Data utvinning**.

2. Välj :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: i det övre vänstra hörnet i **data utvinnings** fönstret. Välj sedan **ny rapport**.

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="Skapa en ny rapport.":::

3. Välj något av följande alternativ i den vänstra rutan:

   - **Kända sårbarheter**: väljer båda alternativen och visar resultatet i rapportens två tabeller, en med CVEs och den andra med undantagen CVEs.

   - **CVEs**: Välj det här alternativet om du vill visa en lista över alla CVEs.

   - **Exkluderade CVEs**: Välj det här alternativet om du vill visa en lista över alla undantagna CVEs.

4. Fyll i **namn** och **beskrivnings** information och välj **Spara**. Den nya rapporten visas i fönstret **Data utvinning** .

5. Om du vill utesluta CVEs öppnar du rapporten Data utvinning för CVEs. Listan över alla CVEs visas.

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="C V E-rapport.":::

6. Om du vill aktivera att välja objekt i listan väljer :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: du och väljer den CVEs som du vill anpassa. **Åtgärds** fältet visas längst ned.

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="Skärm bild av åtgärds fältet Data utvinning.":::

7. Välj den CVEs som du vill undanta och välj sedan **ta bort poster**. CVEs som du har valt visas inte i listan över CVEs och visas i listan över undantagna CVEs när du genererar ett.

8. Om du vill ta med den exkluderade CVEs i listan över CVEs genererar du rapporten för undantagna CVEs och tar bort den från listan över de objekt som du vill ta med i listan över CVEs.

9. Välj den CVEs där du vill ändra poängen och välj sedan **Uppdatera CVE Poäng**.

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="Uppdatera CVE-poängen.":::

10. Ange det nya resultatet och välj **OK**. De uppdaterade poängen visas i den CVEs som du har valt.



## <a name="sensor-reports-based-on-data-mining"></a>Sensor rapporter baserade på Data utvinning

Vanliga rapporter, som nås från alternativet **rapporter** , är fördefinierade rapporter för Data utvinning. De är inte dynamiska frågor som är tillgängliga i Data utvinning, men en statisk representation av frågeresultatet för Data utvinning.

Frågeresultatet för Data utvinning är inte tillgängligt för skrivskyddade användare. Administratörer och säkerhets analyser som vill att skrivskyddade användare ska ha åtkomst till den information som genereras av data utvinnings frågor bör spara informationen som rapport.

Rapporter reflekterar information som genereras av frågeresultatet för Data utvinning. Detta omfattar standard rapporter för Data utvinning som är tillgängliga i vyn rapporter. Administratörer och säkerhetsanalytiker kan också skapa anpassade data utvinnings frågor och spara dem som rapporter. Dessa rapporter är också tillgängliga för RO-användare.

Så här skapar du en rapport:

1. Välj **rapporter** på sido menyn.

2. Välj den rapport som krävs för att visa. Valet kan vara **anpassade** eller **automatiskt genererade** rapporter, till exempel **programmerings kommandon** och **fjärråtkomst**.

3. Du kan exportera rapporten genom att välja en av ikonerna längst upp till höger på skärmen:

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: Exportera till en PDF-fil.

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: Exportera till en CSV-fil.

> [!NOTE] 
> RO-användaren kan bara se rapporter som skapats för dem.

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="Välj den rapport som ska genereras.":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="Rapporten om fjärråtkomst skapades.":::

## <a name="on-premises-management-console-reports-based-on-data-mining-reports"></a>Lokala hanterings konsol rapporter baserade på rapporter om Data utvinning

Med den lokala hanterings konsolen kan du generera rapporter för varje sensor som är ansluten till den. Rapporter baseras på sensor data-utvinnings frågor som utförs.

Du kan generera följande rapporter:

- **Aktiva enheter (senaste 24 timmarna)**: visar en lista över enheter som visar nätverks aktivitet inom en period på 24 timmar.

- **Icke-aktiva enheter (senaste 7 dagarna)**: visar en lista över enheter som inte visar någon nätverks aktivitet under de senaste sju dagarna.

- **Programmerings kommandon**: visar en lista över enheter som har skickat programmerings kommandon under de senaste 24 timmarna.

- **Fjärråtkomst**: visar en lista över enheter som fjärranslutna källor har åtkomst till under de senaste 24 timmarna.

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="Skärm bild av rapportvyn.":::

När du väljer sensorn från den lokala hanterings konsolen visas alla anpassade rapporter som kon figurer ATS på sensorn i listan över rapporter. För varje sensor kan du generera en standard rapport eller en anpassad rapport som kon figurer ATS på sensorn.

Så här skapar du en rapport:

1. I den vänstra rutan väljer du **rapporter**. Fönstret **rapporter** visas.

2. I list rutan **sensorer** väljer du den sensor som du vill generera rapporten för.

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="Skärm bild av sensorer-vy.":::

3. I den högra List rutan väljer du den rapport som du vill generera.

4. Om du vill skapa en PDF-fil med rapport resultaten väljer du :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false"::: .
