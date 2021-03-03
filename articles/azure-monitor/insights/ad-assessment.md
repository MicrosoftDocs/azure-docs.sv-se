---
title: Optimera din Active Directory-miljö med Azure Monitor | Microsoft Docs
description: Du kan använda en lösning för Active Directory hälso kontroll för att utvärdera miljö risker och hälso tillstånd med jämna mellanrum.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: bff61ec9dfcb985ea0111ca58bfd58273e1fe432
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723256"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Optimera din Active Directory-miljö med Active Directory-hälsokontrollösningen i Azure Monitor

![Kontroll Symbol för AD-hälsa](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Du kan använda lösningen Active Directory hälso kontroll för att bedöma Server miljöernas risker och hälsa med jämna mellanrum. Den här artikeln hjälper dig att installera och använda lösningen så att du kan vidta nödvändiga åtgärder för eventuella problem.

Den här lösningen ger en prioriterad lista med rekommendationer som är speciella för din distribuerade Server infrastruktur. Rekommendationerna kategoriseras över fyra fokus områden, vilket hjälper dig att snabbt förstå risken och vidta åtgärder.

Rekommendationerna baseras på den kunskap och erfarenhet som Microsoft-tekniker har fått från tusentals kund besök. Varje rekommendation ger vägledning om varför ett problem kan vara viktigt för dig och hur du implementerar de föreslagna ändringarna.

Du kan välja fokus områden som är viktigast för din organisation och följa förloppet för att köra en risk fri och felfri miljö.

När du har lagt till lösningen och en kontroll har slutförts visas sammanfattnings information för fokus områden på instrument panelen för **AD Health-kontroll** för infrastrukturen i din miljö. I följande avsnitt beskrivs hur du använder informationen på instrument panelen för **AD Health-kontroll** , där du kan visa och sedan vidta rekommenderade åtgärder för din Active Directory Server infrastruktur.  

![bild av kontroll panelen för AD-hälsokontroll](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![bild av kontroll panelen för AD Health-kontroll](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Förutsättningar

* Active Directory Health Check-lösningen kräver en version av .NET Framework 4.6.2 som stöds eller som är installerad på varje dator som har Log Analytics agent för Windows (kallas även Microsoft Monitoring Agent (MMA)) installerad.  Agenten används av System Center 2016-Operations Manager, Operations Manager 2012 R2 och Azure Monitor.
* Lösningen har stöd för domänkontrollanter som kör Windows Server 2008 och 2008 R2, Windows Server 2012 och 2012 R2, Windows Server 2016 och Windows Server 2019.
* En Log Analytics arbets yta där du kan lägga till Active Directory hälso kontroll från Azure Marketplace i Azure Portal. Ingen ytterligare konfiguration krävs.

  > [!NOTE]
  > När du har lagt till lösningen läggs AdvisorAssessment.exe-filen till i servrar med agenter. Konfigurations data läses och skickas sedan till Azure Monitor i molnet för bearbetning. Logik tillämpas på mottagna data och molntjänsten registrerar data.
  >
  >

För att utföra hälso kontrollen mot domän kontrol Lanterna som är medlemmar i domänen som ska utvärderas måste varje domänkontrollant i domänen ha en agent och anslutning för att kunna Azure Monitor med någon av följande metoder som stöds:

1. Installera [Log Analytics agent för Windows](../agents/agent-windows.md) om domänkontrollanten inte redan övervakas av System Center 2016-Operations Manager eller Operations Manager 2012 R2.
2. Om den övervakas med System Center 2016-Operations Manager eller Operations Manager 2012 R2 och hanterings gruppen inte är integrerad med Azure Monitor, kan domänkontrollanten köras i flera hem med Azure Monitor för att samla in data och vidarebefordra till tjänsten och fortfarande övervakas av Operations Manager.  
3. Annars, om din Operations Manager hanterings grupp är integrerad med tjänsten, måste du lägga till domän kontrol Lanterna för data insamling av tjänsten genom att följa stegen under [Lägg till agenter-hanterade datorer](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) när du har aktiverat lösningen i din arbets yta.  

Agenten på domänkontrollanten som rapporterar till en Operations Manager hanterings grupp samlar in data, vidarebefordrar till den tilldelade hanterings servern och sedan skickas direkt från en hanterings server till Azure Monitor.  Data skrivs inte till Operations Manager-databaser.  

## <a name="active-directory-health-check-data-collection-details"></a>Information om datainsamling i Active Directory-hälsokontroll

Active Directory hälso kontroll samlar in data från följande källor med hjälp av den agent som du har aktiverat:

- Register
- LDAP
- .NET Framework
- Händelseloggen
- ADSI (Active Directory Service Interfaces)
- Windows PowerShell
- Fildata
- Windows Management Instrumentation (WMI)
- API för DCDIAG-verktyg
- API för tjänsten File Replication (NTFRS)
- Anpassad C#-kod

Data samlas in på domänkontrollanten och vidarebefordras till Azure Monitor var sjunde dag.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Förstå hur rekommendationer prioriteras

Varje rekommendation ges ett vikt värde som identifierar rekommendationens relativa betydelse. Endast de 10 viktigaste rekommendationerna visas.

### <a name="how-weights-are-calculated"></a>Hur vikter beräknas

Viktningar är aggregerade värden baserat på tre viktiga faktorer:

* *Sannolikheten* att ett problem har identifierats orsakar problem. En högre sannolikhet motsvarar en större total poäng för rekommendationen.
* *Effekten* av problemet i din organisation om det uppstår problem. En högre påverkan motsvarar en större total poäng för rekommendationen.
* Den *insats* som krävs för att implementera rekommendationen. En högre ansträngning motsvarar en mindre övergripande Poäng för rekommendationen.

Viktningen för varje rekommendation uttrycks som en procent andel av det totala antalet poäng som är tillgängliga för varje fokus-sektion. Till exempel, om en rekommendation i fokus för säkerhet och efterlevnad har en poäng på 5%, ökar implementeringen av den här rekommendationen din övergripande Poäng för säkerhet och efterlevnad med 5%.

### <a name="focus-areas"></a>Fokusområden

**Säkerhet och efterlevnad** – i det här fokus avsnittet visas rekommendationer för potentiella säkerhetshot och överträdelser, företags principer och tekniska, juridiska och regler för efterlevnad.

**Tillgänglighet och affärs kontinuitet** – det här fokus avsnittet visar rekommendationer för tjänst tillgänglighet, återhämtning av din infrastruktur och affärs skydd.

**Prestanda och skalbarhet** – i fokus avsnittet visas rekommendationer som hjälper organisationens IT-infrastruktur att växa, se till att din IT-miljö uppfyller aktuella prestanda krav och kan svara på föränderliga infrastruktur behov.

**Uppgradering, migrering och distribution** – det här fokus avsnittet visar rekommendationer för att hjälpa dig att uppgradera, migrera och distribuera Active Directory till din befintliga infrastruktur.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du sikta på 100 % inom varje fokusområde?

Inte nödvändigt vis. Rekommendationerna baseras på den kunskap och de erfarenheter som Microsoft-tekniker har fått på tusentals kund besök. Dock är inte två server infrastrukturer identiska, och vissa rekommendationer kan vara mer eller mindre relevanta för dig. Vissa säkerhets rekommendationer kan till exempel vara mindre relevanta om dina virtuella datorer inte är exponerade för Internet. Vissa tillgänglighets rekommendationer kan vara mindre relevanta för tjänster som tillhandahåller låg prioritet för insamling och rapportering av ad hoc-data. Problem som är viktiga för en vuxen verksamhet kan vara mindre viktiga för en start. Du kanske vill identifiera vilka fokus områden som är dina prioriteringar och titta sedan på hur dina resultat förändras över tid.

Varje rekommendation innehåller vägledning om varför det är viktigt. Du bör använda den här vägledningen för att utvärdera om implementering av rekommendationen passar dig, baserat på dina IT-tjänsters beskaffenhet och organisationens affärs behov.

## <a name="use-health-check-focus-area-recommendations"></a>Använd rekommendationer för fokus området för hälso kontroll

När den har installerats kan du Visa en sammanfattning av rekommendationerna med hjälp av hälso kontroll panelen på lösnings sidan i Azure Portal.

Visa de sammanfattade efterlevnadarna för din infrastruktur och gå sedan till rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Så här visar du rekommendationer för ett fokus fält och vidtar lämpliga åtgärder

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. På sidan **Översikt** klickar du på panelen **Active Directory hälso kontroll** .

2. På sidan **hälso kontroll** granskar du sammanfattnings informationen på ett av bladet för fokus området och klickar sedan på en för att Visa rekommendationer för det fokus området.

3. På någon av sidorna för fokus områden kan du se prioritets rekommendationer som gjorts för din miljö. Klicka på en rekommendation under **berörda objekt** om du vill visa information om varför rekommendationen görs.

    ![bild av hälso kontroll rekommendationer](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. Du kan vidta lämpliga åtgärder som föreslås i **föreslagna åtgärder**. När objektet har åtgärd ATS kommer senare att bedöma poster som rekommenderade åtgärder vidtogs och poängen ökar. Korrigerade objekt visas som **överförda objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer

Om du har rekommendationer som du vill ignorera kan du skapa en textfil som Azure Monitor använda för att förhindra att rekommendationer visas i utvärderings resultatet.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Identifiera rekommendationer som du kommer att ignorera

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Använd följande fråga för att lista rekommendationer som har misslyckats för datorer i din miljö.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Här är en skärm bild som visar logg frågan: <

![misslyckade rekommendationer](media/ad-assessment/ad-failed-recommendations.png)

Välj rekommendationer som du vill ignorera. Du använder värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Skapa och använda en IgnoreRecommendations.txt textfil

1. Skapa en fil med namnet IgnoreRecommendations.txt.

2. Klistra in eller Skriv varje RecommendationId för varje rekommendation som du vill Azure Monitor ignorera på en separat rad och spara och stäng sedan filen.

3. Placera filen i följande mapp på varje dator där du vill Azure Monitor ignorera rekommendationer.

   * På datorer med Microsoft Monitoring Agent (ansluten direkt eller via Operations Manager)- *systemen het*: \Program Files\Microsoft Monitoring Agent\Agent
   * På Operations Manager 2012 R2-hanterings Server – *systemen het*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * På Operations Manager 2016-hanterings Server- *systemen het*: \Program Files\Microsoft System Center 2016 \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Så här kontrollerar du att rekommendationer ignoreras

Efter nästa schemalagda hälso kontroll körs som standard var sjunde dag, och de angivna rekommendationerna markeras som *ignorerade* och visas inte på instrument panelen.

1. Du kan använda följande logg frågor för att lista alla ignorerade rekommendationer.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Om du senare bestämmer dig för att du vill se ignorerade rekommendationer, tar du bort alla IgnoreRecommendations.txt filer, eller så kan du ta bort RecommendationIDs från dem.

## <a name="ad-health-check-solutions-faq"></a>Vanliga frågor och svar om AD-hälsokontrollösningar

*Vilka kontroller utförs av den AD-utvärdering lösningen?*

* Följande fråga visar en beskrivning av alla kontroller som utförs för närvarande:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Resultaten kan sedan exporteras till Excel för ytterligare granskning.

*Hur ofta körs en hälso kontroll?*

* Kontrollen körs var sjunde dag.

*Finns det något sätt att konfigurera hur ofta hälso kontrollen körs?*

* Inte just nu.

*Om en annan server för identifieras efter att jag har lagt till en hälso kontroll lösning, kontrol leras den*

* Ja, när den har identifierats kontrol leras den från och med, var sjunde dag.

*Om en server inaktive ras kommer den att tas bort från hälso kontrollen?*

* Om en server inte skickar data i 3 veckor tas den bort.

*Vad är namnet på processen som utför data insamlingen?*

* AdvisorAssessment.exe

*Hur lång tid tar det för data att samlas in?*

* Den faktiska data insamlingen på servern tar cirka 1 timme. Det kan ta längre tid på servrar som har ett stort antal Active Directory-servrar.

*Finns det något sätt att konfigurera när data samlas in?*

* Inte just nu.

*Varför ska du bara visa de 10 viktigaste rekommendationerna?*

* I stället för att ge dig en omfattande lista över aktiviteter, rekommenderar vi att du fokuserar på att adressera prioritets rekommendationerna först. När du har adresserat dem blir ytterligare rekommendationer tillgängliga. Om du vill se den detaljerade listan kan du Visa alla rekommendationer med hjälp av en logg fråga.

*Finns det något sätt att ignorera en rekommendation?*

* Ja, se avsnittet om att [Ignorera rekommendationer](#ignore-recommendations) ovan.

## <a name="next-steps"></a>Nästa steg

Använd [Azure Monitor logg frågor](../logs/log-query-overview.md) för att lära dig hur du analyserar detaljerade hälso kontroll data och rekommendationer för AD.

