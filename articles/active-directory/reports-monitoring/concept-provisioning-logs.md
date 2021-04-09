---
title: Översikt över etablering av loggar i Azure Portal (för hands version) | Microsoft Docs
description: Få en introduktion till etablerings logg rapporter i Azure Active Directory via Azure Portal.
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
ms.openlocfilehash: 7d8c4876faf9ebc2619309aa0095a8ffe1e9e93d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500554"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Översikt över etablering av loggar i Azure Portal (för hands version)

Rapporterings arkitekturen i Azure Active Directory (Azure AD) består av följande komponenter:

- Aktivitet: 
    - **Inloggningar**: information om användningen av hanterade program och användar inloggnings aktiviteter.
    - [Gransknings loggar](concept-audit-logs.md): system aktivitets information om användar-och grupp hantering, hanterade program och katalog aktiviteter.
    - **Etablerings loggar**: system aktivitet om användare, grupper och roller som tillhandahålls av Azure AD Provisioning-tjänsten. 

- Bullet 
    - **Riskfyllda inloggningar**: en [riskfylld inloggning](../identity-protection/overview-identity-protection.md) är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är en legitim ägare till ett användar konto.
    - **Användare som har flaggats för risk**: en [riskfylld användare](../identity-protection/overview-identity-protection.md) är en indikator för ett användar konto som kan ha komprometterats.

I det här avsnittet får du en översikt över etablerings loggarna. Loggarna ger svar på frågor som: 

* Vilka grupper har skapats i ServiceNow?
* Vilka användare har tagits bort från Adobe?
* Vilka användare från Workday har skapats i Active Directory? 

## <a name="prerequisites"></a>Förutsättningar

Dessa användare kan komma åt data i etablerings loggar:

* Program ägare (loggar för sina egna program)
* Användare i rollerna säkerhets administratör, säkerhets läsare, rapport läsare, säkerhets operatör, program administratör och moln program administratör
* Användare i en anpassad roll med [provisioningLogs-behörighet](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Globala administratörer


För att du ska kunna visa etablerings aktivitets rapporten måste klienten ha en Azure AD Premium licens som är kopplad till den. Information om hur du uppgraderar din Azure AD-utgåva finns i [komma igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md). 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Sätt att interagera med etablerings loggar 
Kunder kan interagera med etablerings loggar på fyra sätt:

- Åtkomst till loggarna från Azure Portal, enligt beskrivningen i nästa avsnitt.
- Strömmar etablerings loggarna till [Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md). Med den här metoden kan du utöka data kvarhållning och skapa anpassade instrument paneler, aviseringar och frågor.
- Fråga Microsoft Graph- [API:](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) t för etablerings loggarna.
- Hämtar etablerings loggarna som en CSV-eller JSON-fil.

## <a name="access-the-logs-from-the-azure-portal"></a>Komma åt loggarna från Azure Portal
Du kan komma åt etablerings loggarna genom att välja **etablerings loggar** i avsnittet **övervakning** i fönstret **Azure Active Directory** i [Azure Portal](https://portal.azure.com). Det kan ta upp till två timmar innan etablerings poster visas i portalen.

![Skärm bild som visar val för att få åtkomst till etablerings loggar.](./media/concept-provisioning-logs/access-provisioning-logs.png "Etableringsloggar")


En etablerings logg har en Standardlistvy som visar:

- Identiteten
- Åtgärden
- Käll systemet
- Mål systemet
- Status
- Datum


![Skärm bild som visar standard kolumner i en etablerings logg.](./media/concept-provisioning-logs/default-columns.png "Standard kolumner")

Du kan anpassa List visningen genom att välja **kolumner** i verktygsfältet.

![Skärm bild som visar knappen för att anpassa kolumner.](./media/concept-provisioning-logs/column-chooser.png "Kolumn väljare")

I det här avsnittet kan du Visa ytterligare fält eller ta bort fält som redan visas.

![Skärm bild som visar tillgängliga kolumner med vissa markerade.](./media/concept-provisioning-logs/available-columns.png "Tillgängliga kolumner")

Välj ett objekt i listvyn om du vill ha mer detaljerad information.

![Skärm bild som visar detaljerad information.](./media/concept-provisioning-logs/steps.png "Filtrera")


## <a name="filter-provisioning-activities"></a>Filtrera etablerings aktiviteter

Du kan filtrera dina etablerings data. Vissa filter värden fylls i dynamiskt baserat på din klient. Om du till exempel inte har några "skapa"-händelser i din klient, så finns det inget alternativ för att **skapa** filter.

I standardvyn kan du välja följande filter:

- **Identitet**
- **Datum**
- **Status**
- **Åtgärd**


![Skärm bild som visar filter värden.](./media/concept-provisioning-logs/default-filter.png "Filtrera")

Med filtret **identitet** kan du ange namnet eller identiteten som du bryr dig om. Den här identiteten kan vara en användare, grupp, roll eller något annat objekt. 

Du kan söka efter objektets namn eller ID. ID varierar beroende på scenario. När du till exempel konfigurerar ett objekt från Azure AD till Salesforce, är käll-ID: t objekt-ID för användaren i Azure AD. Mål-ID: t är användarens ID i Salesforce. När du håller på att etablering från arbets dagar till Active Directory, är käll-ID: t arbets dagen anställdas anställnings-ID. 

> [!NOTE]
> Namnet på användaren kanske inte alltid finns i **identitets** kolumnen. Det kommer alltid att finnas ett ID. 


Med filtret **Datum** kan du definiera en tidsram för de data som returneras. Möjliga värden:

- 1 månad
- 7 dagar
- 30 dagar
- 24 timmar
- Anpassat tidsintervall

När du väljer en anpassad tidsram kan du konfigurera ett start datum och ett slutdatum.

Med **status** filtret kan du välja:

- **Alla**
- **Klart**
- **Haverera**
- **Överhoppad**

Med **Åtgärds** filtret kan du filtrera följande åtgärder:

- **Skapa** 
- **Uppdatera**
- **Ta bort**
- **Inaktivera**
- **Övrigt**

Förutom filtren i standardvyn kan du ange följande filter.

![Skärm bild som visar fält som du kan lägga till som filter.](./media/concept-provisioning-logs/add-filter.png "Välj ett fält")

- **Jobb-ID**: ett unikt jobb-ID är associerat med varje program som du har aktiverat etableringen för.   

- **Cykel-ID**: cykel-ID: t identifierar en unik etablerings cykel. Du kan dela detta ID med produkt support för att leta upp den cykel där händelsen inträffade.

- **Ändrings-ID**: ändrings-ID: t är en unik identifierare för etablerings händelsen. Du kan dela detta ID med produkt support för att leta upp etablerings händelsen.   

- **Käll system**: du kan ange var identiteten ska hämtas från. När du till exempel konfigurerar ett objekt från Azure AD till ServiceNow är käll systemet Azure AD. 

- **Mål system**: du kan ange var identiteten ska hämtas till. När du till exempel konfigurerar ett objekt från Azure AD till ServiceNow är mål systemet ServiceNow. 

- **Program**: du kan bara visa poster med program med ett visnings namn som innehåller en specifik sträng.

## <a name="provisioning-details"></a>Information om etablering 

När du väljer ett objekt i etablerings listans vy får du mer information om det här objektet. Informationen är grupperad i följande flikar.

![Skärm bild som visar fyra flikar som innehåller information om etablering.](./media/concept-provisioning-logs/provisioning-tabs.png "Tabbar")

- **Steg**: beskriver stegen för att etablera ett objekt. Etablering av ett objekt kan bestå av fyra steg:
  
  1. Importera objektet.
  1. Kontrol lera om objektet är i omfånget.
  1. Matcha objektet mellan källa och mål.
  1. Etablera objektet (skapa, uppdatera, ta bort eller inaktivera).

  ![Skärm bild som visar etablerings stegen på fliken steg.](./media/concept-provisioning-logs/steps.png "Filtrera")

- Fel **sökning av & rekommendationer**: innehåller felkoden och orsaken. Fel informationen är bara tillgänglig om ett fel inträffar.

- **Ändrade egenskaper**: visar det gamla värdet och det nya värdet. Om det inte finns något gammalt värde är den kolumnen tom.

- **Sammanfattning**: innehåller en översikt över vad som hände och identifierare för objektet i käll-och mål systemen.

## <a name="download-logs-as-csv-or-json"></a>Hämta loggar som CSV eller JSON

Du kan hämta etablerings loggarna för senare användning genom att gå till loggarna i Azure Portal och välja **Hämta**. Filen kommer att filtreras baserat på de filter villkor du har valt. Gör filtren så exakta som möjligt för att minska storleken och tiden för nedladdningen. 

CSV-nedladdningen innehåller tre filer:

* **ProvisioningLogs**: hämtar alla loggar, förutom etablerings stegen och ändrade egenskaper.
* **ProvisioningLogs_ProvisioningSteps**: innehåller etablerings stegen och ändrings-ID: t. Du kan använda ändrings-ID: t för att koppla händelsen till de andra två filerna.
* **ProvisioningLogs_ModifiedProperties**: innehåller de attribut som har ändrats och ändrings-ID. Du kan använda ändrings-ID: t för att koppla händelsen till de andra två filerna.

#### <a name="open-the-json-file"></a>Öppna JSON-filen
Öppna JSON-filen genom att använda en text redigerare, till exempel [Microsoft Visual Studio Code](https://aka.ms/vscode). Visual Studio Code gör filen lättare att läsa genom att tillhandahålla syntax för att markera. Du kan också öppna JSON-filen med hjälp av webbläsare i ett format som inte kan redige ras, till exempel [Microsoft Edge](https://aka.ms/msedge). 

#### <a name="prettify-the-json-file"></a>Prettify JSON-filen
JSON-filen laddas ned i minified-format för att minska storleken på nedladdningen. Det här formatet kan göra nytto lasten svår att läsa. Ta en titt på två alternativ för att prettify filen:

- Använd [Visual Studio Code för att formatera JSON](https://code.visualstudio.com/docs/languages/json#_formatting).

- Använd PowerShell för att formatera JSON. Det här skriptet matar ut JSON i ett format som innehåller tabbar och blank steg: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Parsa JSON-filen

Här följer några exempel kommandon för att arbeta med JSON-filen med hjälp av PowerShell. Du kan använda valfritt programmeringsspråk som du är van vid.  

Börja med [att läsa JSON-filen](/powershell/module/microsoft.powershell.utility/convertfrom-json) genom att köra det här kommandot:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

Nu kan du analysera data enligt ditt scenario. Här följer några exempel: 

- Utdata för alla jobb-ID: n i JSON-filen:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Spara alla ändrings-ID: n för händelser där åtgärden skapades:

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Det här bör du veta

Här följer några tips och överväganden för etablering av rapporter:

- Azure Portal lagrar rapporterade etablerings data i 30 dagar om du har en Premium-version och 7 dagar om du har en kostnads fri version. Du kan publicera etablerings loggarna till [Log Analytics](../app-provisioning/application-provisioning-log-analytics.md) för kvarhållning bortom 30 dagar. 

- Du kan använda attributet ändra ID som unik identifierare. Detta är användbart när du interagerar med produkt support, till exempel.

- Du kan se överhoppade händelser för användare som inte omfattas av omfånget. Detta förväntas, särskilt när Sync-omfånget är inställt på alla användare och grupper. Tjänsten utvärderar alla objekt i klienten, även de som ligger utanför omfånget. 

- Etablerings loggarna är för närvarande inte tillgängliga i det offentliga molnet. Om du inte kan komma åt etablerings loggarna använder du gransknings loggarna som en tillfällig lösning. 

- Etablerings loggarna visar inte roll import (gäller för AWS, Salesforce och Zendesk). Du hittar loggarna för roll importer i gransknings loggarna. 

## <a name="error-codes"></a>Felkoder

Använd följande tabell för att bättre förstå hur du löser fel som du hittar i etablerings loggarna. För felkoder som saknas ger du feedback genom att använda länken längst ned på den här sidan. 

|Felkod|Beskrivning|
|---|---|
|Konflikt, EntryConflict|Korrigera attributvärdena i konflikt i antingen Azure AD eller programmet. Du kan också granska konfigurationen av matchande attribut om det motstridiga användar kontot skulle matchas och tas över. Läs [dokumentationen](../app-provisioning/customize-application-attributes.md) för mer information om hur du konfigurerar matchande attribut.|
|TooManyRequests|Mål appen avvisade det här försöket att uppdatera användaren eftersom den är överbelastad och tar emot för många begär Anden. Det finns inget att göra. Detta försök kommer automatiskt att dras tillbaka. Microsoft har också fått ett meddelande om det här problemet.|
|InternalServerError |Mål appen returnerade ett oväntat fel. Ett tjänst problem med mål programmet kan förhindra att detta fungerar. Detta försök kommer automatiskt att dras tillbaka om 40 minuter.|
|InsufficientRights, MethodNotAllowed, NotPermitted, obehörig| Azure AD har autentiserats med mål programmet men har inte behörighet att utföra uppdateringen. Granska eventuella instruktioner som mål programmet har tillhandahållit, tillsammans med respektive program [självstudie](../saas-apps/tutorial-list.md).|
|UnprocessableEntity|Mål programmet returnerade ett oväntat svar. Konfigurationen av mål programmet kanske inte är korrekt eller också kan ett tjänst problem med mål programmet förhindra detta från att fungera.|
|WebExceptionProtocolError |Ett HTTP-protokollfel inträffade vid anslutning till mål programmet. Det finns inget att göra. Detta försök kommer automatiskt att dras tillbaka om 40 minuter.|
|InvalidAnchor|En användare som tidigare har skapats eller matchade av etablerings tjänsten finns inte längre. Se till att användaren finns. Om du vill tvinga fram en ny matchning av alla användare använder du Microsoft Graph API för att [starta om jobbet](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). <br><br>Om du startar om etableringen utlöses en första cykel, vilket kan ta lång tid att slutföra. Om du startar om etableringen tas även den cache som etablerings tjänsten använder att använda. Det innebär att alla användare och grupper i klienten måste utvärderas igen och vissa etablerings händelser kan släppas.|
|NotImplemented | Mål appen returnerade ett oväntat svar. Appens konfiguration kanske inte är korrekt eller också kan ett tjänst problem med mål appen förhindra att detta fungerar. Granska eventuella instruktioner som mål programmet har tillhandahållit, tillsammans med respektive program [självstudie](../saas-apps/tutorial-list.md). |
|MandatoryFieldsMissing, MissingValues |Det gick inte att skapa användaren eftersom de värden som krävs saknas. Korrigera attributvärdena som saknas i käll posten eller granska konfigurationen av matchande attribut för att se till att de obligatoriska fälten inte utelämnas. [Läs mer](../app-provisioning/customize-application-attributes.md) om hur du konfigurerar matchande attribut.|
|SchemaAttributeNotFound |Det gick inte att utföra åtgärden eftersom ett attribut angavs som inte finns i mål programmet. Se [dokumentationen](../app-provisioning/customize-application-attributes.md) om attribut anpassning och kontrol lera att konfigurationen är korrekt.|
|InternalError |Ett internt tjänst fel uppstod i Azure AD Provisioning-tjänsten. Det finns inget att göra. Det här försöket görs automatiskt om 40 minuter.|
|InvalidDomain |Det gick inte att utföra åtgärden eftersom ett attributvärde innehåller ett ogiltigt domän namn. Uppdatera domän namnet på användaren eller Lägg till det i listan över tillåtna i mål programmet. |
|Tidsgräns |Det gick inte att slutföra åtgärden eftersom mål programmet tog för lång tid att svara. Det finns inget att göra. Det här försöket görs automatiskt om 40 minuter.|
|LicenseLimitExceeded|Det gick inte att skapa användaren i mål programmet eftersom det inte finns några tillgängliga licenser för den här användaren. Skaffa fler licenser för mål programmet. Du kan också granska användar tilldelningarna och konfigurationen av attributmappning för att säkerställa att rätt användare har tilldelats rätt attribut.|
|DuplicateTargetEntries  |Det gick inte att slutföra åtgärden eftersom mer än en användare i mål programmet hittades med de konfigurerade matchande attributen. Ta bort den duplicerade användaren från mål programmet eller [Konfigurera om dina mappningar för attribut](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Det gick inte att slutföra åtgärden eftersom mer än en användare hittades med de konfigurerade matchande attributen. Ta bort den duplicerade användaren eller [Konfigurera om dina mappningar för attribut](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | När varje användare utvärderas försöker systemet importera användaren från käll systemet. Det här felet uppstår vanligt vis när användaren som importeras saknar matchande egenskap som definierats i dina Attribute-mappningar. Utan ett värde som finns på användarobjektet för matchande attribut kan systemet inte utvärdera omfattnings-, matchnings-eller export ändringar. Observera att förekomsten av det här felet inte indikerar att användaren är i omfånget, eftersom du ännu inte har utvärderat omfattning för användaren.|
|EntrySynchronizationSkipped | Etablerings tjänsten har skickat frågan till käll systemet och identifierade användaren. Ingen ytterligare åtgärd vidtogs för användaren och de hoppades över. Användaren kan ha varit utanför omfånget eller också har användaren redan funnits i mål systemet utan att ytterligare ändringar krävs.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| En GET-begäran för att hämta en användare eller grupp tog emot flera användare eller grupper i svaret. Systemet förväntar sig att endast ta emot en användare eller grupp i svaret. Om du [till exempel](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)gör en get-begäran för att hämta en grupp och anger ett filter för att undanta medlemmar, och systemet för slut punkten för scim (Cross-Domain Identity Management) returnerar medlemmarna, får du det här felet.|

## <a name="next-steps"></a>Nästa steg

* [Kontrol lera status för användar etablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problem med att konfigurera användar etablering i ett Azure AD Gallery-program](../app-provisioning/application-provisioning-config-problem.md)
* [Graph API för etablerings loggar](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)