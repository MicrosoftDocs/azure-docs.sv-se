---
title: Utvärdera System Center Operations Manager med Azure Monitor
description: Du kan använda System Center Operations Manager-hälsokontroll-lösningen för att bedöma hälso tillståndet och hälsan för dina miljöer med jämna mellanrum.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 35ae1e09fd0a06014a747cef99631a7bfe2dee1c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731416"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimera din miljö med System Center Operations Manager-hälsokontrollösningen (förhandsversion)

![System Center Operations Manager-hälsokontroll symbol](./media/scom-assessment/scom-assessment-symbol.png)

Du kan använda System Center Operations Manager-hälsokontroll-lösningen för att utvärdera riskerna och hälsan för din System Center Operations Manager hanterings grupp enligt ett regelbundet intervall. Den här artikeln hjälper dig att installera, konfigurera och använda lösningen så att du kan vidta nödvändiga åtgärder för eventuella problem.

Den här lösningen ger en prioriterad lista med rekommendationer som är speciella för din distribuerade Server infrastruktur. Rekommendationerna kategoriseras över fyra fokus områden, vilket hjälper dig att snabbt förstå risken och vidta lämpliga åtgärder.

Rekommendationerna baseras på den kunskap och erfarenhet som Microsoft-tekniker har fått från tusentals kund besök. Varje rekommendation ger vägledning om varför ett problem kan vara viktigt för dig och hur du implementerar de föreslagna ändringarna.

Du kan välja fokus områden som är viktigast för din organisation och följa förloppet för att köra en risk fri och felfri miljö.

När du har lagt till lösningen och en utvärdering utförs, visas sammanfattnings information för fokus områden på den **System Center Operations Manager-hälsokontroll** instrument panelen för din infrastruktur. I följande avsnitt beskrivs hur du använder informationen på instrument panelen för **System Center Operations Manager-hälsokontroll** , där du kan visa och sedan vidta rekommenderade åtgärder för din Operations Manager-miljö.

![Panel för System Center Operations Manager lösning](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![System Center Operations Manager-hälsokontroll instrument panel](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen

Lösningen fungerar med Microsoft System Center 2012 Operations Manager Service Pack 1, Microsoft System Center 2012 R2 Operations Manager, Microsoft System Center 2016 Operations Manager, Microsoft System Center 2016 Operations Manager och Microsoft System Center Operations Manager 1807. En version av .NET Framework-4.6.2 som stöds måste installeras på varje hanterings Server.

Använd följande information för att installera och konfigurera lösningen.

- Innan du kan använda hälso kontroll lösningen i Log Analytics måste du ha lösningen installerad. Installera lösningen från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

- När du har lagt till lösningen till arbets ytan visar panelen **System Center Operations Manager-hälsokontroll** på instrument panelen ytterligare ett obligatoriskt konfigurations meddelande. Klicka på panelen och följ de konfigurations steg som anges på sidan

  ![Panelen System Center Operations Manager instrument panel](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Konfiguration av System Center Operations Manager kan göras med hjälp av ett skript genom att följa anvisningarna på sidan konfiguration i lösningen i Log Analytics.

 Konfigurera utvärderingen genom Operations Manager drift konsolen genom att följa stegen nedan i följande ordning:
1. [Ange kör som-konto för System Center Operations Manager-hälsokontroll](#operations-manager-run-as-accounts-for-log-analytics)  
2. Konfigurera System Center Operations Manager-hälsokontrolls regeln

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Information om datainsamling i System Center Operations Manager-hälsokontroll

System Center Operations Manager-hälsokontroll lösning samlar in data från följande källor:

* Register
* Windows Management Instrumentation (WMI)
* Händelseloggen
* Fildata
* Direkt från Operations Manager med PowerShell-och SQL-frågor från en hanterings server som du har angett.  

Data samlas in på hanterings servern och vidarebefordras till Log Analytics var sjunde dag.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Kör som-konton i Operations Manager för Log Analytics

Log Analytics bygger på hanterings paket för arbets belastningar för att tillhandahålla värde-Lägg till tjänster. För varje arbets belastning krävs arbets belastnings bara privilegier för att köra hanterings paket i en annan säkerhets kontext, till exempel ett domän användar konto. Konfigurera ett Kör som-konto Operations Manager med privilegierade autentiseringsuppgifter. Mer information finns i [så här skapar du ett Kör som-konto](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) i Operations Manager-dokumentationen.

Använd följande information för att ange Operations Manager kör som-konto för System Center Operations Manager-hälsokontroll.

### <a name="set-the-run-as-account"></a>Ange Kör som-konto

Kör som-kontot måste uppfylla följande krav innan du fortsätter:

* Ett domän användar konto som är medlem i den lokala gruppen Administratörer på alla servrar som har stöd för Operations Manager roll hanterings server SQL Server vara värd för drift, informations lager och ACS-databas, rapportering, webb konsol och gateway-server.
* Operations Managers administratörs roll för hanterings gruppen utvärderas
* Om kontot inte har SQL sysadmin-behörighet kör du [skriptet](#sql-script-to-grant-granular-permissions-to-the-run-as-account) för att ge detaljerade behörigheter till kontot på varje SQL Server instans som är värd för en eller alla Operations Manager-databaser.

1. Välj navigerings knappen **Administration** i Operations Manager-konsolen.
2. Under **Kör som-konfiguration** klickar du på **konton**.
3. I guiden **skapa kör som-konto** klickar du på **Nästa** på sidan **Introduktion** .
4. På sidan **allmänna egenskaper** väljer du **Windows** i listan **typ av kör som-konto:** .
5. Skriv ett visnings namn i text rutan **visnings namn** och skriv en beskrivning i rutan **Beskrivning** och klicka sedan på **Nästa**.
6. På sidan **distributions säkerhet** väljer du **säkrare**.
7. Klicka på **Skapa**.  

Nu när kör som-kontot har skapats måste mål hanterings servrarna i hanterings gruppen och associeras med en fördefinierad kör som-profil så att arbets flöden körs med hjälp av autentiseringsuppgifterna.  

1. Under **Kör som-konfiguration**, **konton** i resultat fönstret, dubbelklickar du på det konto som du skapade tidigare.
2. På fliken **distribution** klickar du på **Lägg till** för rutan **valda datorer** och lägger till den hanterings server som kontot ska distribueras till.  Klicka på **OK** två gånger för att spara ändringarna.
3. Under **Kör som-konfiguration** klickar du på **profiler**.
4. Sök efter *profilen för SCOM-utvärdering*.
5. Profil namnet ska vara: *Microsoft System Center Operations Manager-hälsokontroll kör som-profil*.
6. Högerklicka på och uppdatera dess egenskaper och Lägg till det senast skapade kör som-kontot som du skapade tidigare.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>SQL-skript för att bevilja detaljerade behörigheter till Kör som-kontot

Kör följande SQL-skript för att ge nödvändiga behörigheter till kör som-kontot på SQL Server-instansen som används av Operations Manager som är värd för drift-, informations lager-och ACS-databasen.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Konfigurera hälsokontrollregeln

I hanterings paketet för System Center Operations Manager-hälsokontrolls lösningen ingår en regel med namnet *Microsoft System Center Operations Manager kör hälso kontrolls regel*. Den här regeln ansvarar för att köra hälso kontrollen. Använd anvisningarna nedan om du vill aktivera regeln och konfigurera frekvensen.

Som standard är Microsoft System Center Operations Manager kör hälso kontroll regeln inaktive rad. Om du vill köra hälso kontrollen måste du aktivera regeln på en-hanterings Server. Följ dessa steg.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Aktivera regeln för en specifik hanteringsserver

1. I arbets ytan **redigering** i Operations Manager drift konsolen söker du efter regeln *Microsoft System Center Operations Manager kör hälso kontroll regel* i fönstret **regler** .
2. I Sök resultaten väljer du den som innehåller text *typen: Management Server*.
3. Högerklicka på regeln och klicka sedan på **åsidosättningar**  >  **för ett särskilt objekt i klassen: Management Server**.
4.  I listan tillgängliga hanterings servrar väljer du den hanterings server där regeln ska köras.  Det bör vara samma hanterings server som du konfigurerade tidigare för att koppla kör som-kontot till.
5.  Se till att du ändrar värdet för åsidosättning till **Sant** för det **aktiverade** parametervärdet.<br><br> ![Åsidosätt parameter](./media/scom-assessment/rule.png)

    När du fortfarande är i det här fönstret konfigurerar du körnings frekvensen med hjälp av nästa procedur.

#### <a name="configure-the-run-frequency"></a>Konfigurera körningsfrekvensen

Utvärderingen konfigureras att köras var 10 080: e minut (eller sju dagar) som standard. Du kan åsidosätta värdet till ett lägsta värde på 1440 minuter (eller en dag). Värdet representerar den minsta tids lucka som krävs mellan efterföljande utvärderings körningar. Om du vill åsidosätta intervallet följer du stegen nedan.

1. I arbets ytan **redigering** i Operations Manager-konsolen söker du efter regeln *Microsoft System Center Operations Manager kör hälso kontroll regel* i avsnittet **regler** .
2. I Sök resultaten väljer du den som innehåller text *typen: Management Server*.
3. Högerklicka på regeln och klicka sedan på **Åsidosätt regeln**  >  **för alla objekt i klassen: Management Server**.
4. Ändra värdet för parametern **Interval** till det önskade intervallet. I exemplet nedan anges värdet till 1440 minuter (en dag).<br><br> ![parametern Interval](./media/scom-assessment/interval.png)<br>  

    Om värdet är inställt på mindre än 1440 minuter körs regeln med ett dags intervall. I det här exemplet ignorerar regeln värdet för intervall och körs med en frekvens på en dag.


## <a name="understanding-how-recommendations-are-prioritized"></a>Förstå hur rekommendationer prioriteras

Varje rekommendation ges ett vikt värde som identifierar rekommendationens relativa betydelse. Endast de 10 viktigaste rekommendationerna visas.

### <a name="how-weights-are-calculated"></a>Hur vikter beräknas

Viktningar är aggregerade värden baserat på tre viktiga faktorer:

- *Sannolikheten* för att ett problem har identifierats kan orsaka problem. En högre sannolikhet motsvarar en större total poäng för rekommendationen.
- *Effekten* av problemet i din organisation om det uppstår problem. En högre påverkan motsvarar en större total poäng för rekommendationen.
- Den *insats* som krävs för att implementera rekommendationen. En högre ansträngning motsvarar en mindre övergripande Poäng för rekommendationen.

Viktningen för varje rekommendation uttrycks som en procent andel av det totala antalet poäng som är tillgängliga för varje fokus-sektion. Om en rekommendation i fokus område för tillgänglighet och affärs kontinuitet till exempel har en poäng på 5%, ökar implementeringen av den här rekommendationen din övergripande Poäng för tillgänglighet och affärs kontinuitet med 5%.

### <a name="focus-areas"></a>Fokusområden

**Tillgänglighet och affärs kontinuitet** – det här fokus avsnittet visar rekommendationer för tjänst tillgänglighet, återhämtning av din infrastruktur och affärs skydd.

**Prestanda och skalbarhet** – i fokus avsnittet visas rekommendationer som hjälper organisationens IT-infrastruktur att växa, se till att din IT-miljö uppfyller aktuella prestanda krav och kan svara på föränderliga infrastruktur behov.

**Uppgradering, migrering och distribution** – det här fokus avsnittet visar rekommendationer för att hjälpa dig att uppgradera, migrera och distribuera SQL Server till din befintliga infrastruktur.

**Åtgärder och övervakning** – det här fokus avsnittet visar rekommendationer som hjälper dig att effektivisera IT-verksamheten, implementera förebyggande underhåll och maximera prestanda.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du sikta på 100 % inom varje fokusområde?

Inte nödvändigt vis. Rekommendationerna baseras på den kunskap och de erfarenheter som Microsoft-tekniker har fått på tusentals kund besök. Dock är inte två server infrastrukturer identiska, och vissa rekommendationer kan vara mer eller mindre relevanta för dig. Vissa säkerhets rekommendationer kan till exempel vara mindre relevanta om dina virtuella datorer inte är exponerade för Internet. Vissa tillgänglighets rekommendationer kan vara mindre relevanta för tjänster som tillhandahåller låg prioritet för insamling och rapportering av ad hoc-data. Problem som är viktiga för en vuxen verksamhet kan vara mindre viktiga för en start. Du kanske vill identifiera vilka fokus områden som är dina prioriteringar och titta sedan på hur dina resultat förändras över tid.

Varje rekommendation innehåller vägledning om varför det är viktigt. Använd den här vägledningen för att utvärdera om implementering av rekommendationen passar dig, baserat på dina IT-tjänsters beskaffenhet och organisationens affärs behov.

## <a name="use-health-check-focus-area-recommendations"></a>Använd rekommendationer för fokus området för hälso kontroll

Innan du kan använda en hälso kontroll lösning i Log Analytics måste du ha lösningen installerad. Läs mer om hur du installerar lösningar i [installera en hanterings lösning](./solutions.md). När den har installerats kan du Visa en sammanfattning av rekommendationerna med hjälp av panelen System Center Operations Manager-hälsokontroll på sidan **Översikt** för din arbets yta i Azure Portal.

Visa de sammanfattade efterlevnadarna för din infrastruktur och gå sedan till rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Så här visar du rekommendationer för ett fokus fält och vidtar lämpliga åtgärder
1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com) .
2. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
3. I fönstret Log Analytics prenumerationer väljer du en arbets yta och klickar sedan på meny alternativet **Sammanfattning av arbets yta** .  
4. På sidan **Översikt** klickar du på panelen **System Center Operations Manager-hälsokontroll** .
5. På sidan **System Center Operations Manager-hälsokontroll** granskar du sammanfattnings informationen på ett av bladet fokus områden och klickar sedan på en för att Visa rekommendationer för det fokus fältet.
6. På någon av sidorna för fokus områden kan du se prioritets rekommendationer som gjorts för din miljö. Klicka på en rekommendation under **berörda objekt** om du vill visa information om varför rekommendationen görs.<br><br> ![fokus områden](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Du kan vidta lämpliga åtgärder som föreslås i **föreslagna åtgärder**. När objektet har åtgärd ATS kommer senare utvärderingar registrera att de rekommenderade åtgärderna vidtogs och att poängen ökar. Korrigerade objekt visas som **överförda objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer

Om du har rekommendationer som du vill ignorera kan du skapa en textfil som Log Analytics använder för att förhindra att rekommendationer visas i utvärderings resultatet.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Identifiera rekommendationer som du vill ignorera
1. I Azure Portal på sidan Log Analytics arbets yta för den valda arbets ytan klickar du på meny alternativet **logg sökning** .
2. Använd följande fråga för att lista rekommendationer som har misslyckats för datorer i din miljö.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Om din arbets yta har uppgraderats till det [nya Log Analytics frågespråket](../logs/log-query-overview.md), ändras ovanstående fråga till följande.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Här är en skärm bild som visar logg Sök frågan:<br><br> ![loggsökning](./media/scom-assessment/scom-log-search.png)<br>

3. Välj rekommendationer som du vill ignorera. Du använder värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Skapa och använda en IgnoreRecommendations.txt textfil

1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller Skriv varje RecommendationId för varje rekommendation som du vill Log Analytics ignorera på en separat rad och spara och stäng sedan filen.
3. Placera filen i följande mapp på varje dator där du vill Log Analytics ignorera rekommendationer.
4. På Operations Manager Management Server – *systemen het*: \Program\microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Så här kontrollerar du att rekommendationer ignoreras

1. När nästa schemalagda utvärdering körs, som standard var sjunde dag, markeras de angivna rekommendationerna som ignorerade och visas inte på instrument panelen för hälso kontroll.
2. Du kan använda följande logg Sök frågor för att lista alla ignorerade rekommendationer.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Om din arbets yta har uppgraderats till det [nya Log Analytics frågespråket](../logs/log-query-overview.md), ändras ovanstående fråga till följande.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Om du senare bestämmer dig för att du vill se ignorerade rekommendationer, tar du bort alla IgnoreRecommendations.txt filer, eller så kan du ta bort RecommendationIDs från dem.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Vanliga frågor och svar om System Center Operations Manager-hälsokontrollösning

*Jag har lagt till hälso kontroll lösningen i min Log Analytics-arbetsyta. Men jag ser inte rekommendationerna. Varför inte?* När du har lagt till lösningen använder du följande steg för att Visa rekommendationerna på Log Analytics instrument panelen.  

- [Ange kör som-konto för System Center Operations Manager-hälsokontroll](#operations-manager-run-as-accounts-for-log-analytics)  
- [Konfigurera System Center Operations Manager-hälsokontrolls regeln](#configure-the-health-check-rule)


*Finns det ett sätt att konfigurera hur ofta kontrollen körs?* Ja. Se [Konfigurera körnings frekvensen](#configure-the-run-frequency).

*Kontrollerar du att en annan server identifieras när jag har lagt till System Center Operations Manager-hälsokontroll lösningen?* Ja, efter att identifieringen kontrol leras från och med, som standard var sjunde dag.

*Vad är namnet på processen som utför data insamlingen?* AdvisorAssessment.exe

*Var körs AdvisorAssessment.exe processen?* AdvisorAssessment.exe körs under HealthService-processen för hanterings servern där hälso kontroll regeln är aktive rad. Med den processen uppnås identifieringen av hela din miljö via fjärrinsamling av data.

*Hur lång tid tar det för data insamlingen?* Det tar ungefär en timme att samla in data på servern. Det kan ta längre tid i miljöer som har många Operations Manager instanser eller databaser.

*Vad händer om jag anger intervallet för utvärderingen till mindre än 1440 minuter?* Utvärderingen är förkonfigurerad att köras maximalt en gång per dag. Om du åsidosätter värdet för intervall till ett värde som är mindre än 1440 minuter, använder utvärderingen 1440 minuter som intervall värde.

*Hur vet jag om det finns nödvändiga fel?* Om hälso kontrollen kördes och du inte ser resultatet är det troligt att vissa av kraven för kontrollen misslyckades. Du kan köra frågor: `Operation Solution=SCOMAssessment` och `SCOMAssessmentRecommendation FocusArea=Prerequisites` i loggs ökningen för att se de fel som krävs.

*Det finns ett `Failed to connect to the SQL Instance (….).` meddelande om nödvändiga fel. Vad är problemet?* AdvisorAssessment.exe, den process som samlar in data, körs under HealthService-processen på hanterings servern. Som en del av hälso kontrollen försöker processen ansluta till den SQL Server där Operations Manager databasen finns. Det här felet kan inträffa när brand Väggs regler blockerar anslutningen till SQL Server-instansen.

*Vilken typ av data samlas in?* Följande typer av data samlas in:-WMI-data-register data-EventLog data-Operations Manager data via Windows PowerShell, SQL-frågor och insamling av fil information.

*Varför måste jag konfigurera ett Kör som-konto?* Med Operations Manager körs olika SQL-frågor. Du måste använda ett Kör som-konto med nödvändiga behörigheter för att de ska kunna köras. Dessutom krävs autentiseringsuppgifter för lokal administratör för att fråga WMI.

*Varför ska du bara visa de 10 viktigaste rekommendationerna?* I stället för att ge dig en fullständig, överbelastad lista över aktiviteter, rekommenderar vi att du fokuserar på att adressera prioritets rekommendationerna först. När du har adresserat dem blir ytterligare rekommendationer tillgängliga. Om du vill se den detaljerade listan kan du Visa alla rekommendationer med loggs ökning.

*Finns det något sätt att ignorera en rekommendation?* Ja, se [rekommendationerna](#ignore-recommendations)för att ignorera.


## <a name="next-steps"></a>Nästa steg

- [Sök i loggar](../logs/log-query-overview.md) för att lära dig hur du analyserar detaljerade System Center Operations Manager-hälsokontroll data och rekommendationer.

