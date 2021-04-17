---
title: Översikt över etableringsloggar i Azure Portal (förhandsversion) | Microsoft Docs
description: Få en introduktion till etablering av loggrapporter i Azure Active Directory via Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 468e885bab6aab4becb5aaaec7b4d52ce5ef5e07
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535985"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Översikt över etableringsloggar i Azure Portal (förhandsversion)

Rapporteringsarkitekturen i Azure Active Directory (Azure AD) består av följande komponenter:

- Aktivitet: 
    - **Inloggningar:** Information om användningen av hanterade program och användaraktiviteter för inloggning.
    - [Granskningsloggar:](concept-audit-logs.md)Systemaktivitetsinformation om användar- och grupphantering, hanterade program och katalogaktiviteter.
    - **Etableringsloggar:** Systemaktivitet om användare, grupper och roller som etableras av Azure AD-etableringstjänsten. 

- Säkerhet: 
    - **Riskfyllda inloggningar:** En [riskabel](../identity-protection/overview-identity-protection.md) inloggning är en indikator för ett inloggningsförsök som kan ha utförts av någon som inte är legitim ägare till ett användarkonto.
    - **Användare som har flaggats** för risk: [En riskabel](../identity-protection/overview-identity-protection.md) användare är en indikator för ett användarkonto som kan ha komprometterats.

Det här avsnittet ger dig en översikt över etableringsloggarna. Loggarna ger svar på frågor som: 

* Vilka grupper har skapats i ServiceNow?
* Vilka användare har tagits bort från Adobe?
* Vilka användare från Workday har skapats i Active Directory? 

## <a name="prerequisites"></a>Förutsättningar

Dessa användare kan komma åt data i etableringsloggar:

* Programägare (loggar för sina egna program)
* Användare med rollerna Säkerhetsadministratör, Säkerhetsläsare, Rapportläsare, Säkerhetsoperatör, Programadministratör och Molnprogramadministratör
* Användare i en anpassad roll med [behörigheten provisioningLogs](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Globala administratörer


För att du ska kunna visa etableringsaktivitetsrapporten måste klientorganisationen ha en associerad Azure AD Premium licens. Information om hur du uppgraderar din Azure [AD-version finns i Komma igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md). 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Sätt att interagera med etableringsloggarna 
Kunder kan interagera med etableringsloggarna på fyra sätt:

- Åtkomst till loggarna från Azure Portal enligt beskrivningen i nästa avsnitt.
- Strömma etableringsloggarna till [Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md). Med den här metoden kan du använda utökad datalagring och skapa anpassade instrumentpaneler, aviseringar och frågor.
- Köra frågor mot [Microsoft Graph-API:et](/graph/api/resources/provisioningobjectsummary) för etableringsloggarna.
- Ladda ned etableringsloggarna som en CSV- eller JSON-fil.

## <a name="access-the-logs-from-the-azure-portal"></a>Få åtkomst till loggarna från Azure Portal
Du kan komma åt etableringsloggarna genom  **att** välja Etableringsloggar **i avsnittet Övervakning Azure Active Directory** fönstret i [Azure Portal](https://portal.azure.com). Det kan ta upp till två timmar innan vissa etableringsposter visas i portalen.

![Skärmbild som visar val för åtkomst till etableringsloggar.](./media/concept-provisioning-logs/access-provisioning-logs.png "Etableringsloggar")


En etableringslogg har en standardlistvy som visar:

- Identiteten
- Åtgärden
- Källsystemet
- Målsystemet
- Status
- Datumet


![Skärmbild som visar standardkolumner i en etableringslogg.](./media/concept-provisioning-logs/default-columns.png "Standardkolumner")

Du kan anpassa listvyn genom att **välja** Kolumner i verktygsfältet.

![Skärmbild som visar knappen för att anpassa kolumner.](./media/concept-provisioning-logs/column-chooser.png "Kolumn-väljaren")

Med det här området kan du visa ytterligare fält eller ta bort fält som redan visas.

![Skärmbild som visar tillgängliga kolumner med vissa valda.](./media/concept-provisioning-logs/available-columns.png "Tillgängliga kolumner")

Välj ett objekt i listvyn för att få mer detaljerad information.

![Skärmbild som visar detaljerad information.](./media/concept-provisioning-logs/steps.png "Filtrera")


## <a name="filter-provisioning-activities"></a>Filtrera etableringsaktiviteter

Du kan filtrera dina etableringsdata. Vissa filtervärden fylls i dynamiskt baserat på din klientorganisation. Om du till exempel inte har några "skapa"-händelser i din klientorganisation finns det inget alternativ **för att skapa** filter.

I standardvyn kan du välja följande filter:

- **Identitet**
- **Datum**
- **Status**
- **Åtgärd**


![Skärmbild som visar filtervärden.](./media/concept-provisioning-logs/default-filter.png "Filtrera")

Med **filtret** Identitet kan du ange det namn eller den identitet som du bryr dig om. Den här identiteten kan vara en användare, en grupp, en roll eller ett annat objekt. 

Du kan söka efter namn eller ID för objektet. ID:t varierar beroende på scenario. När du till exempel etablerar ett objekt från Azure AD till Salesforce är käll-ID:t objekt-ID:t för användaren i Azure AD. Mål-ID:t är ID:t för användaren i Salesforce. När du etablerar från Workday till Active Directory är käll-ID:t arbetsmedarbetarens ID för Workday. 

> [!NOTE]
> Namnet på användaren kanske inte alltid finns i **kolumnen** Identitet. Det kommer alltid att finnas ett ID. 


Med filtret **Datum** kan du definiera en tidsram för de data som returneras. Möjliga värden:

- 1 månad
- 7 dagar
- 30 dagar
- 24 timmar
- Anpassat tidsintervall

När du väljer en anpassad tidsram kan du konfigurera ett startdatum och ett slutdatum.

Med **filtret** Status kan du välja:

- **Alla**
- **Klart**
- **Fel**
- **Överhoppad**

Med **filtret** Åtgärd kan du filtrera följande åtgärder:

- **Skapa** 
- **Uppdatera**
- **Ta bort**
- **Inaktivera**
- **Övrigt**

Förutom filtren i standardvyn kan du ange följande filter.

![Skärmbild som visar fält som du kan lägga till som filter.](./media/concept-provisioning-logs/add-filter.png "Välj ett fält")

- **Jobb-ID:** Ett unikt jobb-ID är associerat med varje program som du har aktiverat etablering för.   

- **Cykel-ID:** Cykel-ID:t identifierar etableringscykeln unikt. Du kan dela det här ID:t med produktsupporten för att leta upp den cykel där den här händelsen inträffade.

- **Ändrings-ID:** Ändrings-ID:t är en unik identifierare för etableringshändelsen. Du kan dela detta ID med produktsupporten för att leta upp etableringshändelsen.   

- **Källsystem:** Du kan ange var identiteten etableras från. När du till exempel etablerar ett objekt från Azure AD till ServiceNow är källsystemet Azure AD. 

- **Målsystem:** Du kan ange var identiteten ska etableras. När du till exempel etablerar ett objekt från Azure AD till ServiceNow är målsystemet ServiceNow. 

- **Program:** Du kan bara visa poster för program med ett visningsnamn som innehåller en specifik sträng.

## <a name="provisioning-details"></a>Etableringsinformation 

När du väljer ett objekt i etableringslistvyn får du mer information om det här objektet. Informationen är grupperad i följande flikar.

![Skärmbild som visar fyra flikar som innehåller etableringsinformation.](./media/concept-provisioning-logs/provisioning-tabs.png "Tabbar")

- **Steg:** Beskriver de steg som vidtas för att etablera ett -objekt. Etablering av ett objekt kan bestå av fyra steg:
  
  1. Importera -objektet.
  1. Kontrollera om objektet finns i omfånget.
  1. Matcha objektet mellan källa och mål.
  1. Etablera objektet (skapa, uppdatera, ta bort eller inaktivera).

  ![Skärmbild som visar etableringsstegen på fliken Steg.](./media/concept-provisioning-logs/steps.png "Filtrera")

- **Felsökning &:** Innehåller felkoden och orsaken. Felinformationen är bara tillgänglig om ett fel inträffar.

- **Ändrade egenskaper:** Visar det gamla värdet och det nya värdet. Om det inte finns något gammalt värde är den kolumnen tom.

- **Sammanfattning:** Ger en översikt över vad som hände och identifierare för objektet i käll- och målsystemen.

## <a name="download-logs-as-csv-or-json"></a>Ladda ned loggar som CSV eller JSON

Du kan ladda ned etableringsloggarna för senare användning genom att gå till loggarna i Azure Portal och välja **Ladda ned**. Filen filtreras baserat på de filterkriterier som du har valt. Gör filtren så specifika som möjligt för att minska nedladdningens storlek och tid. 

CSV-nedladdningen innehåller tre filer:

* **ProvisioningLogs**: Hämtar alla loggar, förutom etableringsstegen och ändrade egenskaper.
* **ProvisioningLogs_ProvisioningSteps:** Innehåller etableringsstegen och ändrings-ID:t. Du kan använda ändrings-ID:t för att koppla händelsen till de andra två filerna.
* **ProvisioningLogs_ModifiedProperties:** Innehåller attributen som har ändrats och ändrings-ID:t. Du kan använda ändrings-ID:t för att koppla händelsen till de andra två filerna.

#### <a name="open-the-json-file"></a>Öppna JSON-filen
Om du vill öppna JSON-filen använder du en textredigerare, till [exempel Microsoft Visual Studio Code](https://aka.ms/vscode). Visual Studio Code gör det lättare att läsa filen genom att ange syntaxmarkering. Du kan också öppna JSON-filen med hjälp av webbläsare i ett oredigerbart format, till [exempel Microsoft Edge](https://aka.ms/msedge). 

#### <a name="prettify-the-json-file"></a>Förinstallera JSON-filen
JSON-filen laddas ned i minimerat format för att minska storleken på nedladdningen. Det här formatet kan göra nyttolasten svår att läsa. Titta på två alternativ för att prettifiera filen:

- Använd [Visual Studio Code för att formatera JSON.](https://code.visualstudio.com/docs/languages/json#_formatting)

- Använd PowerShell för att formatera JSON. Det här skriptet matar ut JSON i ett format som innehåller tabbar och blanksteg: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Parsa JSON-filen

Här är några exempelkommandon för att arbeta med JSON-filen med hjälp av PowerShell. Du kan använda alla programmeringsspråk som du känner dig bekväm med.  

Läs först [JSON-filen genom att](/powershell/module/microsoft.powershell.utility/convertfrom-json) köra det här kommandot:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

Nu kan du parsa data enligt ditt scenario. Här följer några exempel: 

- Mata ut alla jobb-ID:er i JSON-filen:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Mata ut alla ändrings-ID:er för händelser där åtgärden var "create":

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Det här bör du veta

Här är några tips och överväganden för att etablera rapporter:

- Den Azure Portal lagrar rapporterade etableringsdata i 30 dagar om du har en Premium-version och 7 dagar om du har en kostnadsfri version. Du kan publicera etableringsloggarna till [Log Analytics för](../app-provisioning/application-provisioning-log-analytics.md) kvarhållning längre än 30 dagar. 

- Du kan använda attributet ändra ID som unik identifierare. Detta är till exempel användbart när du interagerar med produktsupporten.

- Du kan se överhoppade händelser för användare som inte ingår i omfånget. Detta är förväntat, särskilt när synkroniseringsomfånget är inställt på alla användare och grupper. Tjänsten utvärderar alla objekt i klientorganisationen, även de som ligger utanför omfånget. 

- Etableringsloggarna är för närvarande inte tillgängliga i myndighetsmolnet. Om du inte kan komma åt etableringsloggarna använder du granskningsloggarna som en tillfällig lösning. 

- Etableringsloggarna visar inte rollimporter (gäller för AWS, Salesforce och Zendesk). Du hittar loggarna för rollimporter i granskningsloggarna. 

## <a name="error-codes"></a>Felkoder

Använd följande tabell för att bättre förstå hur du löser fel som du hittar i etableringsloggarna. Om du har felkoder som saknas kan du ge feedback via länken längst ned på den här sidan. 

|Felkod|Description|
|---|---|
|Konflikt, EntryConflict|Korrigera de motstridiga attributvärdena i antingen Azure AD eller programmet. Du kan också granska din matchande attributkonfiguration om det motstridiga användarkontot skulle matchas och tas över. Mer information [om hur](../app-provisioning/customize-application-attributes.md) du konfigurerar matchande attribut finns i dokumentationen.|
|TooManyRequests|Målappen avvisade det här försöket att uppdatera användaren eftersom den är överbelastad och tar emot för många begäranden. Det finns inget att göra. Det här försöket dras automatiskt tillbaka. Microsoft har också fått ett meddelande om det här problemet.|
|InternalServerError |Målappen returnerade ett oväntat fel. Ett tjänstproblem med målprogrammet kan hindra detta från att fungera. Det här försöket dras automatiskt tillbaka om 40 minuter.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Unauthorized| Azure AD autentiserades med målprogrammet men har inte behörighet att utföra uppdateringen. Granska alla instruktioner som målprogrammet har angett, tillsammans med respektive programs [självstudie.](../saas-apps/tutorial-list.md)|
|UnprocessableEntity|Målprogrammet returnerade ett oväntat svar. Konfigurationen av målprogrammet kanske inte är korrekt, eller så kanske ett tjänstproblem med målprogrammet förhindrar att det fungerar.|
|WebExceptionProtocolError |Ett HTTP-protokollfel inträffade vid anslutning till målprogrammet. Det finns inget att göra. Det här försöket dras automatiskt tillbaka om 40 minuter.|
|InvalidAnchor|En användare som tidigare har skapats eller matchats av etableringstjänsten finns inte längre. Kontrollera att användaren finns. Om du vill tvinga en ny matchning av alla användare använder du Microsoft Graph API för [att starta om jobbet](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). <br><br>Om etableringen startas om utlöses en inledande cykel, vilket kan ta tid att slutföra. När etableringen startas om tas även den cache som etableringstjänsten använder för att fungera. Det innebär att alla användare och grupper i klientorganisationen måste utvärderas igen, och vissa etableringshändelser kan tas bort.|
|Inte implementerad | Målappen returnerade ett oväntat svar. Konfigurationen av appen kanske inte är korrekt, eller så kan ett tjänstproblem med målappen hindra detta från att fungera. Granska alla instruktioner som målprogrammet har angett tillsammans med respektive programs [självstudie.](../saas-apps/tutorial-list.md) |
|MandatoryFieldsMissing, MissingValues |Det gick inte att skapa användaren eftersom nödvändiga värden saknas. Korrigera de saknade attributvärdena i källposten eller granska din matchande attributkonfiguration för att säkerställa att de obligatoriska fälten inte utelämnas. [Läs mer om](../app-provisioning/customize-application-attributes.md) hur du konfigurerar matchande attribut.|
|SchemaAttributeNotFound |Det gick inte att utföra åtgärden eftersom ett attribut har angetts som inte finns i målprogrammet. Se dokumentationen [om](../app-provisioning/customize-application-attributes.md) attributanpassning och se till att konfigurationen är korrekt.|
|InternalError |Ett internt tjänstfel inträffade i Azure AD-etableringstjänsten. Det finns inget att göra. Det här försöket görs automatiskt om 40 minuter.|
|InvalidDomain |Det gick inte att utföra åtgärden eftersom ett attributvärde innehåller ett ogiltigt domännamn. Uppdatera domännamnet på användaren eller lägg till det i listan över tillåtna i målprogrammet. |
|Tidsgräns |Det gick inte att slutföra åtgärden eftersom målprogrammet tog för lång tid att svara. Det finns inget att göra. Det här försöket görs automatiskt om 40 minuter.|
|LicenseLimitExceeded|Det gick inte att skapa användaren i målprogrammet eftersom det inte finns några tillgängliga licenser för den här användaren. Skaffa fler licenser för målprogrammet. Du kan också granska konfigurationen av användartilldelningar och attributmappning för att se till att rätt användare tilldelas rätt attribut.|
|DuplicateTarget-poster  |Det gick inte att slutföra åtgärden eftersom fler än en användare i målprogrammet hittades med de konfigurerade matchande attributen. Ta bort den duplicerade användaren från målprogrammet eller [konfigurera om attributmappningarna](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Det gick inte att slutföra åtgärden eftersom fler än en användare hittades med de konfigurerade matchande attributen. Ta bort den duplicerade användaren [eller konfigurera om attributmappningarna](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | När varje användare utvärderas försöker systemet importera användaren från källsystemet. Det här felet uppstår vanligtvis när den användare som importeras saknar den matchande egenskapen som definierats i dina attributmappningar. Utan ett värde som finns i användarobjektet för det matchande attributet kan systemet inte utvärdera omfångs-, matchnings- eller exportändringar. Observera att förekomsten av det här felet inte indikerar att användaren finns i omfånget, eftersom du ännu inte har utvärderat omfånget för användaren.|
|EntrySynchronizationSkipped | Etableringstjänsten har frågat källsystemet och identifierat användaren. Ingen ytterligare åtgärd vidtogs för användaren och de hoppas över. Användaren kan ha varit utanför omfånget eller så kanske användaren redan har funnits i målsystemet utan att ytterligare ändringar krävs.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| En GET-begäran för att hämta en användare eller grupp tog emot flera användare eller grupper i svaret. Systemet förväntar sig att endast ta emot en användare eller grupp i svaret. [Om du](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)till exempel gör en GET-begäran för att hämta en grupp och ange ett filter för att undanta medlemmar, och din SYSTEM for Cross-Domain Identity Management-slutpunkt (SCIM) returnerar medlemmarna, får du det här felet.|

## <a name="next-steps"></a>Nästa steg

* [Kontrollera status för användareablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problem med att konfigurera användareablering till ett Azure AD-galleriprogram](../app-provisioning/application-provisioning-config-problem.md)
* [Graph API för etableringsloggar](/graph/api/resources/provisioningobjectsummary)