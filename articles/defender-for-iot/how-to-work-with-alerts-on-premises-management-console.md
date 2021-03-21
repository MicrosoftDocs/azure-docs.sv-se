---
title: Arbeta med aviseringar på den lokala hanteringskonsolen
description: Använd den lokala hanterings konsolen för att få en Enterprise-vy över de senaste hoten i nätverket och bättre förstå hur sensor användare hanterar dem.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 878b1b2d5ba13b68b5122e4b9cffc3c408e211e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100526331"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>Arbeta med aviseringar på den lokala hanteringskonsolen 

Du kan göra följande från fönstret **aviseringar** i hanterings konsolen:

- Arbeta med aviserings filter

- Arbeta med aviserings räknare

- Visa aviserings information

- Hantera aviserings händelser

- Skapa undantags regler för varningar

- Utlös varnings regler för undantag från externa system

- Påskynda incident arbets flödet med aviserings grupper

## <a name="view-alerts-in-the-on-premises-management-console"></a>Visa aviseringar i den lokala hanterings konsolen

Den lokala hanterings konsolen sammanställer aviseringar från alla anslutna sensorer. Detta ger en Enterprise-vy över de senaste hoten i nätverket och hjälper dig att bättre förstå hur sensor användare hanterar dem.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="Skärm bild av aviserings fönstret.":::

### <a name="work-with-alert-filters"></a>Arbeta med aviserings filter

**Aviserings** fönstret visar de aviseringar som genererats av sensorer som är anslutna till den lokala hanterings konsolen. Du kan visa alla aviseringar för anslutna sensorer eller Visa aviseringarna som skickas från en speciell:

- Webbplats

- Zon

- Enhet

- Mäta

Välj **Rensa filter** om du vill visa alla aviseringar.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="Rensa dina filter genom att välja knappen Rensa filter.":::

### <a name="work-with-alert-counters"></a>Arbeta med aviserings räknare

Aviserings räknare ger en analys av aviseringar efter allvarlighets grad och bekräftat tillstånd.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="Aviserings räknaren visar hur många aviseringar du har.":::

Följande allvarlighets grader visas i aviserings räknaren:

- **Kritisk**: anger en skadlig attack som ska hanteras omedelbart.

- **Större**: innebär ett säkerhetshot som är viktigt att åtgärda.

- **Mindre**: indikerar en avvikelse från bas linje beteendet som kan innehålla ett säkerhetshot.

- **Varning**! anger en avvikelse från bas linje beteendet utan säkerhetshot.

Allvarlighets nivåer är fördefinierade.

Du kan justera räknaren för att tillhandahålla siffror baserat på accepterade och ej accepterade aviseringar. Ej accepterade aviseringar utlöstes vid Defender för IoT-sensorer, men har ännu inte granskats av operatörerna på sensorn.

När alternativet **Visa godkända aviseringar** är markerat visas alla accepterade aviseringar i **aviserings** fönstret.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="Visa dina bekräftade aviseringar.":::

### <a name="view-alert-information"></a>Visa aviserings information

Aviseringen visar följande information:

- En sammanfattning av aviserings händelsen.

- Allvarlighets grad för avisering.

- En länk till aviseringen i sensorn som identifierade den.

- En aviserings-UUID. UUID: t består av det aviserings-ID som är kopplat till aviserings händelsen som upptäcktes på sensorn, avgränsat med ett bindestreck och följt av ett unikt system-ID-nummer.

**Aviserings-UUID för lokal hanterings konsol**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="En enhet är ansluten men inte auktoriserad.":::

**Aviserings-ID för sensor**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="Aviserings-ID för sensor.":::

Att arbeta med UUID: er säkerställer att varje avisering som visas i den lokala hanterings konsolen är sökbar och identifierbar med ett unikt nummer. Detta är obligatoriskt eftersom aviseringar som genereras från flera sensorer kan producera samma aviserings-ID.

> [!NOTE]
> Som standard visas UUID: er i följande partner system när regler för vidarebefordran definieras: ArcSight, syslog-servrar, QRadar, Sentinel och netvittne. Ingen konfiguration krävs.

Visa aviserings information:

- Välj en avisering i aviserings listan.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="Skärm bild av aviserings information.":::

Så här visar du aviseringen i sensorn:

- Välj **Öppna sensor** från aviseringen.

Visa enheterna i en zon karta:

- Om du vill visa enhets kartan med fokus på den aviseringade enheten och alla enheter som är anslutna till den, väljer du **Visa enheter**.

## <a name="manage-alert-events"></a>Hantera aviserings händelser

Det finns flera alternativ för att hantera aviserings händelser från den lokala hanterings konsolen.

- Lär dig eller bekräfta aviserings händelser. Välj **lär & bekräfta** om du vill veta alla aviserings händelser som kan godkännas och bekräfta alla aviserings händelser som för närvarande inte har bekräftats.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="Välj lär & bekräfta för att lära dig allt.":::

- Stäng av och inaktivera varnings händelser.

Mer information om hur du lär dig mer om att lära, bekräfta och stänga av varnings händelser finns i artikeln sensor [Hantera aviserings händelser](how-to-manage-the-alert-event.md) .

## <a name="export-alert-information"></a>Exportera aviserings information

Exportera aviserings information till en. csv-fil. Du kan exportera information om alla aviseringar som identifierats eller exportera information baserat på den filtrerade vyn. Följande information exporteras:

- Käll adress
- Mål adress
- Aviseringsrubrik
- Allvarlighetsgrad för avisering
- Aviserings meddelande
- Ytterligare information
- Bekräftad status
- PCAP tillgänglighet

Att exportera:

1. Välj aviseringar på menyn på sidan.
1. Välj Exportera.
1. Välj Exportera utökade aviseringar för att exportera aviserings information i separata rader för varje avisering som täcker flera enheter. När du väljer Exportera utökade aviseringar skapar CSV-filen en dubblett av aviseringen med de unika objekten på varje rad. Med det här alternativet blir det enklare att undersöka exporterade aviserings händelser.  

## <a name="create-alert-exclusion-rules"></a>Skapa undantags regler för varningar

Instruera Defender for IoT att ignorera aviserings utlösare baserat på:

- Tids zoner och tids perioder

- Enhets adress (IP, MAC, undernät)

- Aviserings namn

- En speciell sensor

Skapa undantags regler för varningar när du vill att Defender för IoT ska ignorera aktivitet som utlöser en avisering.

Om du till exempel vet att alla enheter som övervakas av en viss sensor kommer att gå igenom underhålls procedurer i två dagar, kan du definiera en undantags regel som instruerar Defender för IoT att ignorera aviseringar som upptäckts av den här sensorn under den fördefinierade perioden.

### <a name="alert-exclusion-logic"></a>Logik för varnings undantag

Logiken för varnings regler är `AND` baserad. Det innebär att en avisering endast utlöses när alla regel villkor är uppfyllda.

Om ett regel villkor inte har definierats kommer villkoret att innehålla alla alternativ. Om du till exempel inte tar med namnet på en sensor i regeln, kommer den att tillämpas på alla sensorer.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="Skärm bild av vyn Skapa undantags regel.":::

Regel sammanfattningar visas i fönstret **undantags regel** .

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="Skärm bild av sammanfattnings vyn för undantags regel.":::

Förutom att arbeta med undantags regler kan du förhindra aviseringar genom att stänga av dem.

### <a name="create-exclusion-rules"></a>Skapa undantags regler

Så här skapar du undantags regler:

1. I den vänstra rutan i den lokala hanterings konsolen väljer du **varnings undantag**. Definiera en ny regel för undantag genom att välja ikonen **Lägg till** :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: i det övre högra hörnet i fönstret som öppnas. Dialog rutan **skapa exkluderings regel** öppnas.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="Skapa en aviserings uteslutning genom att fylla i informationen här.":::

2. Ange ett regel namn i fältet **namn** . Namnet får inte innehålla citat tecken ( `"` ).

3. I avsnittet **efter tidszon/period** anger du en tids period inom en viss tidszon. Använd den här funktionen när en undantags regel skapas för en viss tids period i en tidszon, men bör implementeras samtidigt i andra tids zoner. Du kan till exempel behöva tillämpa en regel för undantag mellan 8:00 och 10:00 i tre olika tids zoner. I det här fallet skapar du tre separata undantags regler som använder samma tids period och relevant tidszon.

4. Välj **Lägg till**. Under undantags perioden skapas inga aviseringar på de anslutna sensorerna.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="Skärm bild av vyn efter tids period.":::

5. I avsnittet **per enhets adress** definierar du:

  - Enhetens IP-adress, MAC-adress eller under näts adress som du vill undanta.

  - Trafik riktning för exkluderade enheter, källa och mål.

6. Välj **Lägg till**.

7. I avsnittet **efter aviserings titel** börjar du skriva aviserings titeln. Välj aviserings rubriken eller rubrikerna som ska uteslutas i list rutan.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="Skärm bild av vyn via aviserings rubrik.":::

8. Välj **Lägg till**.

9. I avsnittet **efter sensor namn** börjar du skriva sensor namnet. I list rutan väljer du den sensor eller de sensorer som du vill undanta.

10. Välj **Lägg till**.

11. Välj **Spara**. Den nya regeln visas i listan med regler.

Du kan ignorera aviseringar genom att stänga av dem eller skapa varnings regler för undantag. I det här avsnittet beskrivs möjliga användnings fall för båda funktionerna.

- **Exkluderings regel**. Skriv en undantags regel när:

  - Du vet i förväg hur lång tid du vill undanta händelsen från databasen. Du vet till exempel att scenariot som identifieras vid en viss sensor kommer att utlösa irrelevanta aviseringar. Du kommer till exempel att utföra underhålls arbete på organisations PLCs på en enskild plats och vill förhindra aviseringar relaterade till PLCs för den här webbplatsen.

  - Du vill att Defender för IoT ska ignorera händelser under ett angivet tidsintervall (för system underhålls aktiviteter).

  - Du vill ignorera händelser i ett särskilt undernät.

  - Du vill kontrol lera aviserings händelser som genererats från flera sensorer med en regel.

  - Du vill inte spåra varnings undantaget som en händelse i händelse loggen.

- **Tyst**. Inaktivera en avisering när:

  - Objekt som behöver stängs av planeras inte. Du vet inte i förväg vilka händelser som kommer att vara irrelevanta.

  - Du vill ignorera aviseringen från fönstret **aviseringar** , men du vill fortfarande spåra den i händelse loggen.

  - Du vill ignorera händelser på en speciell kanal.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>Utlös varnings regler för undantag från externa system

Utlös varnings regler för undantag från externa system. Du kan till exempel hantera undantags regler från Enterprise tickning system eller system som hanterar nätverks underhålls processer.

Definiera sensorer, motorer, start tid och slut tid för att tillämpa regeln. Mer information finns i [Defender för API: er för IoT API-sensor och Management Console](references-work-with-defender-for-iot-apis.md).

Regler som du skapar med hjälp av API: et visas i fönstret **undantags regel** som ro.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="Skärm bild av vyn Redigera Exkluderings regel.":::

## <a name="see-also"></a>Se även

[Arbeta med aviseringar på sensorn](how-to-work-with-alerts-on-your-sensor.md)
