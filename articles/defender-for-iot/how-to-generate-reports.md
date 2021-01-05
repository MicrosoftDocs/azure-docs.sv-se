---
title: Skapa rapporter
description: Få insikt i nätverks aktivitet, risker, attacker och trender genom att använda Defender för IoT repor ting tools.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d0c3f531ede0a590a6ba7bb21c6edbd768c08069
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97842563"
---
# <a name="generate-reports"></a>Skapa rapporter

Få insyn i nätverks aktivitet, risker, attacker och trender med hjälp av Azure Defender för IoT repor ting tools. Det finns verktyg för att generera rapporter:

- Baserat på aktivitet som identifieras av enskilda sensorer.
- Baserat på aktivitet som upptäckts av alla sensorer. 

Dessa rapporter genereras från den lokala hanterings konsolen.

## <a name="reports-for-sensor-risk-assessment"></a>Rapporter för sensor riskbedömning

Med riskbedömningen kan du skapa en säkerhets Poäng för varje nätverks enhet, samt en övergripande Poäng för nätverks säkerhet. Den totala poängen visar procent andelen 100 procent av säkerheten.

Den här poängen baseras på resultat från paket granskning, beteende modellerings motorer och en SCADA-speciell tillstånds dator design. Det finns en stor mängd annan information, till exempel:

- Konfigurations problem

- Enhets sårbarhet prioriterad av säkerhets nivå

- Nätverks säkerhets problem

- Problem med nätverks användning

- Angrepps vektorer

- Anslutningar till ICS-nätverk

- Internet anslutningar

- Indikatorer för industriella skadlig kod

- Protokollproblem

  - Säkra enheter: enheter med en säkerhets poäng över 90%.

- **Säkra enheter**: enheter med en säkerhets poäng över 90 procent.

- **Sårbara enheter**: enheter med säkerhets poängen under 70 procent.

- **Enheter som behöver förbättras**: enheter med en säkerhets poäng mellan 70 och 89 procent.

Så här skapar du en rapport:

1. Välj **riskbedömning** på sido menyn.
2. Välj en sensor i list rutan **Välj sensor** .
3. Välj **Generera rapport**.
4. Välj **Hämta** i avsnittet arkiverade rapporter.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="En vy över riskbedömningen.":::

Så här importerar du en företags logo typ:

Så här importerar du en företags logo typ:

- Välj **import-logotyp**.

## <a name="reports-for-sensor-attack-vector"></a>Rapporter för sensor angrepps vektor

Angrepps vektor rapporter ger en grafisk representation av en sårbarhets kedja av enheter som går att utnyttja. Dessa sårbarheter kan ge en angripare åtkomst till viktiga nätverks enheter. Angrepps vektor simulatorn beräknar angrepps vektorer i real tid och analyserar alla angrepps vektorer för ett särskilt mål.

Genom att arbeta med angrepps vektorn kan du utvärdera effekterna av minsknings aktiviteter i angrepps ordningen. Du kan till exempel bestämma om en system uppgradering ska störa angriparens sökväg genom att bryta angrepps kedjan eller om en annan angrepps Sök väg är kvar. Den här informationen hjälper dig att prioritera reparationer och mildrande aktiviteter.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Visa dina aviseringar i kontroll centret.":::

> [!NOTE]
> Administratörer och säkerhets analyser kan utföra de procedurer som beskrivs i det här avsnittet.

Så här skapar du en simulering av attack vektor:

1. Lägg till en simulering genom att välja :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="plus tecken":::på sido menyn.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="Simuleringen av attack vektorn.":::

2. Ange simulerings egenskaper:

   - **Namn**: namn på simulering.

   - **Högsta antal vektorer**: det högsta antalet vektorer i en enda simulering.

   - **Visa i enhets karta**: Visa angrepps vektorn som ett filter på enhets kartan.

   - **Alla käll enheter**: angrepps vektorn kommer att betrakta alla enheter som en attack källa.

   - **Attack källa**: angrepps vektorn kommer endast att betrakta de angivna enheterna som en attack källa.

   - **Alla mål enheter**: angrepps vektorn kommer att betrakta alla enheter som ett angrepps mål.

   - **Angrepps mål**: angrepps vektorn kommer endast att betrakta de angivna enheterna som ett angrepps mål.

   - **Exkludera enheter**: angivna enheter kommer att uteslutas från simuleringen av attack vektorn.

   - **Uteslut undernät**: angivna undernät kommer att uteslutas från simuleringen av attack vektorn.

3. Välj **Lägg till simulering**. Simuleringen läggs till i listan simuleringar.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Lägg till en ny simulering.":::

4. Välj :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: om du vill redigera simuleringen.

   Välj :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: om du vill ta bort simuleringen.

   Välj :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: om du vill markera simuleringen som en favorit.

5. En lista över angrepps vektorer visas och innehåller vektor Poäng (från 100), angrepps källans enhet och angrepps mål enhet. Välj en speciell attack för grafisk depresentation av angrepps vektorer.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Angrepps vektorer.":::

## <a name="sensor-data-mining-queries"></a>Sensor data – utvinnings frågor

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

Data utvinnings frågor som du skapar uppdateras dynamiskt varje gången du öppnar dem. Exempel:

- Om du skapar en rapport för versioner av inbyggd program vara på enheter den 1 juni och öppnar rapporten igen den 10 juni kommer den här rapporten att uppdateras med den information som är korrekt för 10 juni.

- Om du skapar en rapport för att identifiera nya enheter som identifierats under de senaste 30 dagarna den 1 juni och öppnar rapporten den 30 juni, visas resultaten för de senaste 30 dagarna.

### <a name="data-mining-use-cases"></a>Användnings fall för Data utvinning

Du kan använda frågor för att hantera en mängd olika säkerhets behov för olika säkerhets team:

- **SOC incident svar**: generera en rapport i real tid för att hjälpa dig att hantera omedelbara incident åtgärder. Du kan till exempel generera en rapport för en lista över enheter som kan behöva korrigeras.

- **Data utredning**: generera en rapport som baseras på historiska data för utrednings rapporter.

- **Nätverks integritet**: generera en rapport som hjälper till att förbättra den övergripande nätverks säkerheten. Generera till exempel en rapport som visar enheter med svaga autentiseringsuppgifter.

- **Synlighet**: generera en rapport som täcker alla frågedata för att visa alla bas linje parametrar i nätverket.

### <a name="data-mining-storage"></a>Data utvinnings lagring

Data utvinnings information sparas och lagras kontinuerligt, förutom när en enhet tas bort. Resultat för Data utvinning kan exporteras och lagras externt på en säker server. Dessutom utför sensorn automatisk daglig säkerhets kopiering för att säkerställa system kontinuitet och data bevarande.

### <a name="data-mining-and-reports"></a>Data utvinning och rapporter 

Vanliga rapporter, som nås från alternativet **rapporter** , är fördefinierade rapporter om Data utvinning. De är inte dynamiska frågor som är tillgängliga i Data utvinning, men en statisk representation av resultatet av data utvinnings frågan.

Frågeresultatet för Data utvinning är inte tillgängliga för RO-användare. Administratörer och säkerhetsanalytiker som vill att RO-användare ska ha åtkomst till den information som genereras av data utvinnings frågor bör spara informationen som rapport.

### <a name="predefined-queries"></a>Fördefinierade frågor

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

### <a name="create-a-data-mining-report"></a>Skapa en rapport över data utvinning

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

| Ikon bild | Beskrivning |
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

### <a name="reports-based-on-data-mining"></a>Rapporter baserade på Data utvinning

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

## <a name="reports-for-sensor-trends-and-statistics"></a>Rapporter för sensor trender och statistik

Du kan skapa widgets grafer och cirkel diagram för att få insyn i nätverks trender och statistik. Widgetar kan grupperas under användardefinierade instrument paneler.

> [!NOTE]
> Endast administratörer och säkerhetsanalytiker kan utföra procedurerna i det här avsnittet.

Om du vill se instrument paneler och widgetar väljer du **trender & statistik** på sido menyn.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Skärm bild av en undersökning.":::

Instrument panelen består av widgetar som grafiskt beskriver följande typer av information:

- Trafik efter port
- Kanal bandbredd
- Total bandbredd
- Aktiv TCP-anslutning
- Enheter:
  - Nya enheter
  - Upptagna enheter
  - Enheter efter leverantör
  - Enheter per operativ system
  - Frånkopplade enheter
- Anslutnings släpp per timme
- Aviseringar för incidenter efter typ
- Åtkomst till databas tabell
- Widgetar protokoll avsnitt
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

## <a name="reports-for-the-on-premises-management-console"></a>Rapporter för den lokala hanterings konsolen

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

## <a name="risk-assessment-reports-for-the-on-premises-management-console"></a>Riskbedömnings rapporter för den lokala hanterings konsolen

Generera en riskbedömnings rapport för varje sensor som är ansluten till den lokala hanterings konsolen. Den här rapporten ger insikter om vart och ett av de nätverk som dina sensorer övervakar.

Med riskbedömnings rapporter kan du generera en säkerhets Poäng för varje nätverks enhet samt en övergripande Poäng för nätverks säkerhet. Den totala poängen baseras på djup paket inspektion, beteende modellerings motorer och en SCADA-speciell tillstånds dator design. Det finns en stor mängd annan information. Exempel:

- Konfigurations problem

- Enhets sårbarhet prioriterad av säkerhets nivå

- Nätverks säkerhets problem

- Problem med nätverks användning

- Angrepps vektorer

- Anslutningar till ICS-nätverk

- Internet anslutningar

- Indikatorer för industriella skadlig kod

- Protokollproblem

I rapporten finns några rekommendationer som hjälper dig att förbättra din säkerhets poäng.

- Säkra enheter: enheter med en säkerhets poäng över 90%.

- **Sårbara enheter**: enheter med säkerhets poängen under 70 procent.

- **Enheter som behöver förbättras**: enheter med en säkerhets poäng mellan 70 och 89 procent.

Så här skapar du en rapport:

1. Välj **riskbedömning** på sido menyn.

2. Välj en sensor i list rutan **Välj sensor** .

3. Välj **Generera rapport**.

4. Välj **Hämta** i avsnittet **arkiverade rapporter** .

Så här importerar du en företags logo typ:

- Välj **import-logotyp**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Importera din logo typ via vyn riskbedömning.":::

## <a name="see-also"></a>Se även
[Arbeta med vyer för webbplats översikt](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
