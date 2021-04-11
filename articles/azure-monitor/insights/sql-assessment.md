---
title: Optimera din SQL Server-miljö med Azure Monitor | Microsoft Docs
description: Med Azure Monitor kan du använda SQL Health Check-lösningen för att bedöma hälso tillståndet och hälsan för dina miljöer med jämna mellanrum.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 0f2319ea6ba314c08a67651667837f05df5765a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723239"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Optimera din SQL-miljö med SQL Server Health Check-lösning i Azure Monitor

![Kontroll Symbol för SQL-hälsa](./media/sql-assessment/sql-assessment-symbol.png)

Du kan använda SQL-kontrollen för hälso kontroll för att bedöma Server miljöernas risker och hälsa med jämna mellanrum. Den här artikeln hjälper dig att installera lösningen så att du kan vidta nödvändiga åtgärder för eventuella problem.

Den här lösningen ger en prioriterad lista med rekommendationer som är speciella för din distribuerade Server infrastruktur. Rekommendationerna kategoriseras i sex fokus områden som hjälper dig att snabbt förstå risken och vidta lämpliga åtgärder.

Rekommendationerna baseras på den kunskap och erfarenhet som Microsoft-tekniker har fått från tusentals kund besök. Varje rekommendation ger vägledning om varför ett problem kan vara viktigt för dig och hur du implementerar de föreslagna ändringarna.

Du kan välja fokus områden som är viktigast för din organisation och följa förloppet för att köra en risk fri och felfri miljö.

När du har lagt till lösningen och en utvärdering har slutförts visas sammanfattnings information för fokus områden på instrument panelen för **SQL Health-kontroll** för infrastrukturen i din miljö. I följande avsnitt beskrivs hur du använder informationen på instrument panelen för **SQL Health-kontroll** , där du kan visa och sedan vidta rekommenderade åtgärder för din SQL Server-infrastruktur.

![bild av kontroll panelen för SQL-hälsa](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![bild av kontroll panelen för SQL Health-kontroll](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Förutsättningar

* SQL Health-kontrollen kräver att en version av .NET Framework 4.6.2 som stöds har installerats på alla datorer där Microsoft Monitoring Agent (MMA) har installerats.  MMA-agenten används av System Center 2016-Operations Manager och Operations Manager 2012 R2 och Azure Monitor.  
* Lösningen stöder SQL Server version 2012, 2014, 2016, 2017 och 2019.
* En Log Analytics arbets yta för att lägga till hälso kontroll lösningen för SQL från Azure Marketplace i Azure Portal. Du måste vara administratör eller deltagare i Azure-prenumerationen för att kunna installera lösningen.

  > [!NOTE]
  > När du har lagt till lösningen läggs AdvisorAssessment.exe-filen till i servrar med agenter. Konfigurations data läses och skickas sedan till Azure Monitor i molnet för bearbetning. Logik tillämpas på mottagna data och molntjänsten registrerar data.
  >
  >

För att utföra hälso kontrollen mot dina SQL Server-servrar, kräver de en agent och anslutning för att Azure Monitor med någon av följande metoder som stöds:

1. Installera [Microsoft Monitoring Agent (MMA)](../agents/agent-windows.md) om servern inte redan övervakas av System Center 2016-Operations Manager eller Operations Manager 2012 R2.
2. Om den övervakas med System Center 2016-Operations Manager eller Operations Manager 2012 R2 och hanterings gruppen inte är integrerad med Azure Monitor, kan servern ha flera hem med Log Analytics för att samla in data och vidarebefordra till tjänsten och fortfarande övervakas av Operations Manager.  
3. Annars, om din Operations Manager hanterings grupp är integrerad med tjänsten, måste du lägga till domän kontrol Lanterna för data insamling av tjänsten genom att följa stegen under [Lägg till agenter-hanterade datorer](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) när du har aktiverat lösningen i din arbets yta.  

Agenten på SQL Server som rapporterar till en Operations Manager hanterings grupp, samlar in data, vidarebefordrar till den tilldelade hanterings servern och sedan skickas direkt från en hanterings server till Azure Monitor.  Data skrivs inte till Operations Manager-databaser.  

Om SQL Server övervakas av Operations Manager måste du konfigurera ett Kör som-konto för Operations Manager. Mer information finns i [Operations Manager kör som-konton för Azure Monitor](#operations-manager-run-as-accounts-for-log-analytics) nedan.

## <a name="sql-health-check-data-collection-details"></a>Information om datainsamling i SQL-hälsokontroll
SQL Health-kontroll samlar in data från följande källor med hjälp av den agent som du har aktiverat:

* Windows Management Instrumentation (WMI)
* Register
* Prestandaräknare
* SQL Server dynamisk hantering av visnings resultat

Data samlas in på SQL Server och vidarebefordras till Log Analytics var sjunde dag.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Kör som-konton i Operations Manager för Log Analytics
Log Analytics använder Operations Manager agent och hanterings grupp för att samla in och skicka data till Log Analyticss tjänsten. Log Analytics bygger på hanterings paket för arbets belastningar för att tillhandahålla värde-Lägg till tjänster. För varje arbets belastning krävs arbets belastnings bara privilegier för att köra hanterings paket i en annan säkerhets kontext, till exempel ett domän användar konto. Du måste ange autentiseringsinformation genom att konfigurera ett Kör som-konto för Operations Manager.

Använd följande information för att ange Operations Manager kör som-konto för SQL Health-kontroll.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Ange Kör som-konto för SQL-hälsokontroll
 Om du redan använder SQL Server hanterings paketet bör du använda den som kör som-konfiguration.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Konfigurera SQL-kör som-kontot i drift konsolen
> [!NOTE]
> Som standard körs arbets flöden i hanterings paketet i säkerhets kontexten för det lokala system kontot. Om du använder Microsoft Monitoring Agent som är ansluten direkt till tjänsten i stället för att rapportera direkt till en Operations Manager hanterings grupp, hoppar du över steg 1-5 nedan och kör antingen T-SQL-eller PowerShell-exemplet och anger NT instans\system som användar namn.
>
>

1. I Operations Manager öppnar du drift konsolen och klickar sedan på **Administration**.
2. Under **Kör som-konfiguration** klickar du på **profiler** och öppnar **SQL-utvärdering kör som-profil**.
3. På sidan **Kör som-konton** klickar du på **Lägg till**.
4. Välj ett Kör som-konto i Windows som innehåller de autentiseringsuppgifter som krävs för SQL Server eller klicka på **nytt** för att skapa ett.

   > [!NOTE]
   > Kör som-kontots typ måste vara Windows. Kör som-kontot måste också vara en del av den lokala administratörs gruppen på alla Windows-servrar som är värdar för SQL Server instanser.
   >
   >
5. Klicka på **Spara**.
6. Ändra och kör sedan följande T-SQL-exempel på varje SQL Server instans för att bevilja de lägsta behörigheter som krävs för att kör som-kontot ska utföra hälso kontrollen. Men du behöver inte göra detta om ett Kör som-konto redan är en del av sysadmin-serverrollen på SQL Server instanser.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Konfigurera SQL-kör som-kontot med hjälp av Windows PowerShell
Öppna ett PowerShell-fönster och kör följande skript när du har uppdaterat det med din information:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Förstå hur rekommendationer prioriteras
Varje rekommendation ges ett vikt värde som identifierar rekommendationens relativa betydelse. Endast de tio viktigaste rekommendationerna visas.

### <a name="how-weights-are-calculated"></a>Hur vikter beräknas
Viktningar är aggregerade värden baserat på tre viktiga faktorer:

* *Sannolikheten* för att ett problem har identifierats kan orsaka problem. En högre sannolikhet motsvarar en större total poäng för rekommendationen.
* *Effekten* av problemet i din organisation om det uppstår problem. En högre påverkan motsvarar en större total poäng för rekommendationen.
* Den *insats* som krävs för att implementera rekommendationen. En högre ansträngning motsvarar en mindre övergripande Poäng för rekommendationen.

Viktningen för varje rekommendation uttrycks som en procent andel av det totala antalet poäng som är tillgängliga för varje fokus-sektion. Till exempel, om en rekommendation i fokus för säkerhet och efterlevnad har en poäng på 5%, kommer den här rekommendationen att öka din övergripande Poäng för säkerhet och efterlevnad med 5%.

### <a name="focus-areas"></a>Fokusområden
**Säkerhet och efterlevnad** – i det här fokus avsnittet visas rekommendationer för potentiella säkerhetshot och överträdelser, företags principer och tekniska, juridiska och regler för efterlevnad.

**Tillgänglighet och affärs kontinuitet** – det här fokus avsnittet visar rekommendationer för tjänst tillgänglighet, återhämtning av din infrastruktur och affärs skydd.

**Prestanda och skalbarhet** – i fokus avsnittet visas rekommendationer som hjälper organisationens IT-infrastruktur att växa, se till att din IT-miljö uppfyller aktuella prestanda krav och kan svara på föränderliga infrastruktur behov.

**Uppgradering, migrering och distribution** – det här fokus avsnittet visar rekommendationer för att hjälpa dig att uppgradera, migrera och distribuera SQL Server till din befintliga infrastruktur.

**Åtgärder och övervakning** – det här fokus avsnittet visar rekommendationer som hjälper dig att effektivisera IT-verksamheten, implementera förebyggande underhåll och maximera prestanda.

**Ändrings-och konfigurations hantering** – det här fokus avsnittet visar rekommendationer för att hjälpa till att skydda den dagliga verksamheten, se till att ändringarna inte påverkar infrastrukturen negativt, etablera ändrings kontroll procedurer och spåra och granska system konfigurationer.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du sikta på 100 % inom varje fokusområde?
Inte nödvändigt vis. Rekommendationerna baseras på den kunskap och de erfarenheter som Microsoft-tekniker har fått på tusentals kund besök. Dock är inte två server infrastrukturer identiska, och vissa rekommendationer kan vara mer eller mindre relevanta för dig. Vissa säkerhets rekommendationer kan till exempel vara mindre relevanta om dina virtuella datorer inte är exponerade för Internet. Vissa tillgänglighets rekommendationer kan vara mindre relevanta för tjänster som tillhandahåller låg prioritet för insamling och rapportering av ad hoc-data. Problem som är viktiga för en vuxen verksamhet kan vara mindre viktiga för en start. Du kanske vill identifiera vilka fokus områden som är dina prioriteringar och titta sedan på hur dina resultat förändras över tid.

Varje rekommendation innehåller vägledning om varför det är viktigt. Du bör använda den här vägledningen för att utvärdera om implementering av rekommendationen passar dig, baserat på dina IT-tjänsters beskaffenhet och organisationens affärs behov.

## <a name="use-health-check-focus-area-recommendations"></a>Använda rekommendationer om fokusområden i hälsokontroll
Innan du kan använda en utvärderings lösning i Azure Monitor måste du ha lösningen installerad.  När den har installerats kan du Visa en sammanfattning av rekommendationerna med hjälp av SQL-kontroll panelen på sidan **Översikt** för Azure Monitor i Azure Portal.

Visa de sammanfattade efterlevnadarna för din infrastruktur och gå sedan till rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Så här visar du rekommendationer för ett fokus fält och vidtar lämpliga åtgärder
1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Monitor**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Monitor**.
3. I avsnittet **insikter** på menyn väljer du **mer**.  
4. På sidan **Översikt** klickar du på **kontroll panelen SQL-hälso kontroll** .
5. På sidan **hälso kontroll** granskar du sammanfattnings informationen på ett av bladet för fokus området och klickar sedan på en för att Visa rekommendationer för det fokus området.
6. På någon av sidorna för fokus områden kan du se prioritets rekommendationer som gjorts för din miljö. Klicka på en rekommendation under **berörda objekt** om du vill visa information om varför rekommendationen görs.<br><br> ![bild av hälso kontroll rekommendationer för SQL](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Du kan vidta lämpliga åtgärder som föreslås i **föreslagna åtgärder**. När objektet har åtgärd ATS kommer senare utvärderingar registrera att de rekommenderade åtgärderna vidtogs och att poängen ökar. Korrigerade objekt visas som **överförda objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer
Om du har rekommendationer som du vill ignorera kan du skapa en textfil som Azure Monitor använda för att förhindra att rekommendationer visas i utvärderings resultatet.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Identifiera rekommendationer som du kommer att ignorera
1. Klicka på **loggar** på Azure Monitor-menyn.
2. Använd följande fråga för att lista rekommendationer som har misslyckats för datorer i din miljö.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Här är en skärm bild som visar logg frågan:<br><br> ![misslyckade rekommendationer](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Välj rekommendationer som du vill ignorera. Du använder värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Skapa och använda en IgnoreRecommendations.txt textfil
1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller Skriv varje RecommendationId för varje rekommendation som du vill Azure Monitor ignorera på en separat rad och spara och stäng sedan filen.
3. Placera filen i följande mapp på varje dator där du vill Azure Monitor ignorera rekommendationer.
   * På datorer med Microsoft Monitoring Agent (ansluten direkt eller via Operations Manager)- *systemen het*: \Program Files\Microsoft Monitoring Agent\Agent
   * På Operations Manager Management Server – *systemen het*: \Program\microsoft System Center 2012 R2\Operations Manager\Server
   * På Operations Manager 2016-hanterings Server- *systemen het*: \Program Files\Microsoft System Center 2016 \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Så här kontrollerar du att rekommendationer ignoreras
1. När nästa schemalagda utvärdering körs, som standard var 7: e dag, markeras de angivna rekommendationerna som ignorerade och visas inte på utvärderings instrument panelen.
2. Du kan använda följande logg Sök frågor för att lista alla ignorerade rekommendationer.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Om du senare bestämmer dig för att du vill se ignorerade rekommendationer, tar du bort alla IgnoreRecommendations.txt filer, eller så kan du ta bort RecommendationIDs från dem.

## <a name="sql-health-check-solution-faq"></a>Vanliga frågor och svar om SQL-hälsokontrollösning

*Vilka kontroller utförs av den SQL-utvärdering lösningen?*

* Följande fråga visar en beskrivning av alla kontroller som utförs för närvarande:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Resultaten kan sedan exporteras till Excel för ytterligare granskning.


*Hur ofta körs en hälso kontroll?*

* Kontrollen körs var sjunde dag.

*Finns det ett sätt att konfigurera hur ofta kontrollen körs?*

* Inte just nu.

*Kontrollerar du att en annan server identifieras när jag har lagt till hälso kontroll lösningen för SQL?*

* Ja, när den har identifierats kontrol leras den från och med, var sjunde dag.

*Om en server inaktive ras kommer den att tas bort från hälso kontrollen?*

* Om en server inte skickar data i 3 veckor tas den bort.

*Vad är namnet på processen som utför data insamlingen?*

* AdvisorAssessment.exe

*Hur lång tid tar det för data att samlas in?*

* Den faktiska data insamlingen på servern tar cirka 1 timme. Det kan ta längre tid på servrar som har ett stort antal SQL-instanser eller databaser.

*Vilken typ av data samlas in?*

* Följande typer av data samlas in:
  * WMI
  * Register
  * Prestandaräknare
  * SQL Dynamic Management views (DMV).

*Finns det något sätt att konfigurera när data samlas in?*

* Inte just nu.

*Varför måste jag konfigurera ett Kör som-konto?*

* För SQL Server körs ett litet antal SQL-frågor. För att de ska kunna köras måste ett Kör som-konto med Visa SERVER TILLSTÅNDs behörighet till SQL användas.  För att kunna fråga WMI krävs dessutom autentiseringsuppgifter för lokal administratör.

*Varför ska du bara visa de 10 viktigaste rekommendationerna?*

* I stället för att ge dig en omfattande lista över aktiviteter, rekommenderar vi att du fokuserar på att adressera prioritets rekommendationerna först. När du har adresserat dem blir ytterligare rekommendationer tillgängliga. Om du vill se den detaljerade listan kan du Visa alla rekommendationer med hjälp av Log Analytics loggs ökning.

*Finns det något sätt att ignorera en rekommendation?*

* Ja, se avsnittet om att [Ignorera rekommendationer](#ignore-recommendations) ovan.

## <a name="next-steps"></a>Nästa steg
* [Logga frågor](../logs/log-query-overview.md) för att lära dig hur du analyserar detaljerade SQL Health Check-data och rekommendationer.

