---
title: Etablering av loggar i Azure Active Directory portal (förhands granskning) | Microsoft Docs
description: Introduktion till etablering av loggar rapporter i Azure Active Directory Portal
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
ms.date: 12/28/2020
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56818862b6bc4eb38b819185aceb121e6e78488e
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803535"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Etablering av rapporter i Azure Active Directory portal (för hands version)

Rapporterings arkitekturen i Azure Active Directory (Azure AD) består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – information om användningen av hanterade program och användar inloggnings aktiviteter.
    - **Gransknings loggar**  -  [Gransknings loggar](concept-audit-logs.md) innehåller information om system aktivitet för användare och grupp hantering, hanterade program och katalog aktiviteter.
    - **Etablerings loggar** – ger system aktivitet om användare, grupper och roller som tillhandahålls av Azure AD Provisioning-tjänsten. 

- **Säkerhet** 
    - **Riskfyllda inloggningar** – en [riskfylld inloggning](../identity-protection/overview-identity-protection.md) är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är en legitim ägare till ett användar konto.
    - **Användare som har flaggats för risk** – en [riskfylld användare](../identity-protection/overview-identity-protection.md) är en indikator för ett användar konto som kan ha komprometterats.

I det här avsnittet får du en översikt över etablerings rapporten.

## <a name="prerequisites"></a>Förutsättningar

### <a name="who-can-access-the-data"></a>Vem som kan komma åt data?
* Program ägare kan visa loggar för program som de äger
* Användare i rollerna säkerhets administratör, säkerhets läsare, rapport läsare, program administratör och moln program administratör
* Användare i en anpassad roll med [provisioningLogs-behörighet](https://docs.microsoft.com/azure/active-directory/roles/custom-enterprise-app-permissions#full-list-of-permissions)
* Globala administratörer


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Vilken Azure AD-licens behöver du för att få åtkomst till etablerings aktiviteter?

Din klient organisation måste ha en Azure AD Premium licens kopplad till sig för att se rapporten all etablerings aktivitet. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. 

## <a name="provisioning-logs"></a>Etableringsloggar

Etablerings loggarna ger svar på följande frågor:

* Vilka grupper har skapats i ServiceNow?
* Vilka användare har tagits bort från Adobe?
* Vad användarna inte kunde skapa i DropBox?

Du kan komma åt etablerings loggarna genom att välja **etablerings loggar** i avsnittet **övervakning** på bladet **Azure Active Directory** i [Azure Portal](https://portal.azure.com). Det kan ta upp till två timmar för vissa etablerings poster att visas i portalen.

![Etableringsloggar](./media/concept-provisioning-logs/access-provisioning-logs.png "Etableringsloggar")


En etablerings logg har en Standardlistvy som visar:

- Identiteten
- Åtgärden
- Käll systemet
- Mål systemet
- Status
- Datum


![Standard kolumner](./media/concept-provisioning-logs/default-columns.png "Standard kolumner")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Kolumn väljare](./media/concept-provisioning-logs/column-chooser.png "Kolumn väljare")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Tillgängliga kolumner](./media/concept-provisioning-logs/available-columns.png "Tillgängliga kolumner")

Välj ett objekt i listvyn om du vill ha mer detaljerad information.

![Detaljerad information](./media/concept-provisioning-logs/steps.png "Filter")


## <a name="filter-provisioning-activities"></a>Filtrera etablerings aktiviteter

Du kan filtrera dina etablerings data. Vissa filter värden fylls i dynamiskt baserat på din klient. Om du till exempel inte har några skapa-händelser i din klient organisation, så finns det inget filter alternativ för att skapa.
I standardvyn kan du välja följande filter:

- Identitet
- Date
- Status
- Åtgärd


![Lägg till filter](./media/concept-provisioning-logs/default-filter.png "Filter")

Med filtret **identitet** kan du ange namnet eller identiteten som du bryr dig om. Den här identiteten kan vara en användare, grupp, roll eller något annat objekt. Du kan söka efter objektets namn eller ID. ID varierar beroende på scenario. När ett objekt till exempel konfigureras från Azure AD till SalesForce, är käll-ID: t objekt-ID för användaren i Azure AD medan TargetID är användarens ID i Salesforce. Vid etablering från arbets dagar till Active Directory, är käll-ID: t arbets dagen anställdas anställnings-ID. Observera att namnet på användaren kanske inte alltid finns i identitets kolumnen. Det kommer alltid att finnas ett ID. 


Med filtret **Datum** kan du definiera en tidsram för de data som returneras.  
Möjliga värden:

- 1 månad
- 7 dagar
- 30 dagar
- 24 timmar
- Anpassat tidsintervall

När du väljer en anpassad tidsram kan du konfigurera ett start datum och ett slutdatum.


Med **status** filtret kan du välja:

- Alla
- Success
- Fel
- Överhoppad



Med **Åtgärds** filtret kan du filtrera:

- Skapa 
- Uppdatera
- Ta bort
- Inaktivera
- Annat

Dessutom kan du också ange följande filter till filtren i standardvyn:

- Job-ID
- Cykel-ID
- Ändra ID
- Käll-ID
- Mål-ID
- Program


![Välj ett fält](./media/concept-provisioning-logs/add-filter.png "Välj ett fält")


- **Jobb-ID** – ett unikt jobb-ID är associerat med varje program som du har aktiverat etableringen för.   

- **Cykel-ID** – identifierar etablerings cykeln unikt. Du kan dela detta ID för att få stöd för att leta upp den cykel där händelsen inträffade.

- **Ändra ID** – unik identifierare för etablerings händelsen. Du kan dela detta ID för att ge stöd för att leta upp etablerings händelsen.   


- **Käll system** – här kan du ange var identiteten ska hämtas från. När ett objekt till exempel konfigureras från Azure AD till ServiceNow är käll systemet Azure AD. 

- **Mål system** – här kan du ange var identiteten ska hämtas till. När ett objekt till exempel konfigureras från Azure AD till ServiceNow, är mål systemet ServiceNow. 

- **Program** – gör att du endast kan visa poster för program med ett visnings namn som innehåller en specifik sträng.

 

## <a name="provisioning-details"></a>Information om etablering 

När du väljer ett objekt i etablerings listans vy får du mer information om det här objektet.
Detaljerna grupperas baserat på följande kategorier:

- Steg

- Felsöka och rekommendationer

- Ändrade egenskaper

- Sammanfattning


![Information om etablering](./media/concept-provisioning-logs/provisioning-tabs.png "Tabbar")



### <a name="steps"></a>Steg

På fliken **steg** beskrivs de steg som vidtas för att etablera ett objekt. Etablering av ett objekt kan bestå av fyra steg: 

- Importera objekt
- Avgöra om objektet är i omfånget
- Matcha objekt mellan källa och mål
- Etablera objekt (vidta åtgärder – det kan vara en skapa, uppdatera, ta bort eller inaktivera)



![Skärm bild som visar fliken steg som visar etablerings stegen.](./media/concept-provisioning-logs/steps.png "Filter")


### <a name="troubleshoot-and-recommendations"></a>Felsöka och rekommendationer


På fliken **fel sökning och rekommendationer** visas felkoden och orsaken. Fel informationen är endast tillgänglig om det skulle uppstå ett fel. 


### <a name="modified-properties"></a>Ändrade egenskaper

De **ändrade egenskaperna** visar det gamla värdet och det nya värdet. I fall där det inte finns något gammalt värde är kolumnen gammalt värde tom. 


### <a name="summary"></a>Sammanfattning

Fliken **Sammanfattning** ger en översikt över vad som hände och identifierare för objektet i käll-och mål systemet. 

## <a name="what-you-should-know"></a>Det här bör du veta

- Azure Portal lagrar rapporterade etablerings data i 30 dagar om du har en Premium-version och 7 dagar om du har en kostnads fri version. Etablerings loggarna kan publiceras i [Log Analytics](../app-provisioning/application-provisioning-log-analytics.md) för kvarhållning bortom 30 dagar. 

- Du kan använda attributet ändra ID som unik identifierare. Detta är till exempel användbart när du interagerar med produkt supporten.

- Det finns för närvarande inget alternativ för att ladda ned etablerings data som en CSV-fil, men du kan exportera data med hjälp av [Microsoft Graph](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta).

- Du kan se hoppade händelser för användare som inte omfattas av omfånget. Detta förväntas, särskilt när Sync-omfånget är inställt på alla användare och grupper. Tjänsten kommer att utvärdera alla objekt i klienten, även de som ligger utanför omfånget. 

- Etablerings loggarna är för närvarande inte tillgängliga i det offentliga molnet. Om du inte kan komma åt etablerings loggarna ska du använda gransknings loggarna som en tillfällig lösning. 

- Etablerings loggarna visar inte roll import (gäller för AWS, SalesForce och ZenDesk). Loggarna för roll import finns i gransknings loggarna. 

## <a name="error-codes"></a>Felkoder

Använd tabellen nedan för att bättre förstå hur du löser fel som du kan hitta i etablerings loggarna. För felkoder som saknas ger du feedback med hjälp av länken längst ned på sidan. 

|Felkod|Beskrivning|
|---|---|
|Konflikt, EntryConflict|Korrigera attributvärdena i konflikt antingen i Azure AD eller i programmet eller granska konfigurationen av matchande attribut om det användar konto som står i konflikt skulle matchas och tas över. Läs följande [dokumentation](../app-provisioning/customize-application-attributes.md) om du vill ha mer information om hur du konfigurerar matchande attribut.|
|TooManyRequests|Mål appen avvisade det här försöket att uppdatera användaren eftersom den är överbelastad och tar emot för många begär Anden. Det finns inget att göra. Detta försök kommer automatiskt att dras tillbaka. Microsoft har också fått ett meddelande om det här problemet.|
|InternalServerError |Mål appen returnerade ett oväntat fel. Det kan finnas ett tjänst problem med mål programmet som hindrar detta från att fungera. Detta försök kommer automatiskt att dras tillbaka om 40 minuter.|
|InsufficientRights, MethodNotAllowed, NotPermitted, obehörig| Azure AD kunde autentiseras med mål programmet, men har inte behörighet att utföra uppdateringen. Granska eventuella instruktioner från mål programmet samt [själv studie kursen](../saas-apps/tutorial-list.md)för programmet.|
|UnprocessableEntity|Mål programmet returnerade ett oväntat svar. Konfigurationen av mål programmet kanske inte är korrekt, eller så kan det finnas ett tjänst problem med mål programmet som hindrar detta från att fungera.|
|WebExceptionProtocolError |Ett HTTP-protokollfel inträffade vid anslutning till mål programmet. Det finns inget att göra. Detta försök kommer automatiskt att dras tillbaka om 40 minuter.|
|InvalidAnchor|En användare som tidigare har skapats eller matchade av etablerings tjänsten finns inte längre. Kontrol lera att användaren finns. Om du vill tvinga fram en ny matchning av alla användare använder du MS Graph API för att [starta om jobbet](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Observera att om du startar om etableringen utlöses en första cykel, vilket kan ta tid att slutföra. Det tar också bort det cacheminne som etablerings tjänsten använder för att hantera, vilket innebär att alla användare och grupper i klienten måste utvärderas igen och att vissa etablerings händelser kan släppas.|
|NotImplemented | Mål appen returnerade ett oväntat svar. Appens konfiguration kanske inte är korrekt, eller så kan det finnas ett tjänst problem med den målfil som hindrar detta från att fungera. Granska eventuella instruktioner från mål programmet samt [själv studie kursen](../saas-apps/tutorial-list.md)för programmet. |
|MandatoryFieldsMissing, MissingValues |Det gick inte att skapa användaren eftersom de värden som krävs saknas. Korrigera attributvärdena som saknas i käll posten eller granska konfigurationen av matchande attribut för att se till att de obligatoriska fälten inte utelämnas. [Läs mer](../app-provisioning/customize-application-attributes.md) om hur du konfigurerar matchande attribut.|
|SchemaAttributeNotFound |Det gick inte att utföra åtgärden eftersom ett attribut som inte finns i mål programmet har angetts. Se [dokumentationen](../app-provisioning/customize-application-attributes.md) om attribut anpassning och se till att konfigurationen är korrekt.|
|InternalError |Ett internt tjänst fel uppstod i Azure AD Provisioning-tjänsten. Det finns inget att göra. Det här försöket görs automatiskt om 40 minuter.|
|InvalidDomain |Det gick inte att utföra åtgärden på grund av ett attributvärde som innehåller ett ogiltigt domän namn. Uppdatera domän namnet på användaren eller Lägg till det i listan över tillåtna i mål programmet. |
|Tidsgräns |Det gick inte att utföra åtgärden eftersom mål programmet tog för lång tid att svara. Det finns inget att göra. Det här försöket görs automatiskt om 40 minuter.|
|LicenseLimitExceeded|Det gick inte att skapa användaren i mål programmet eftersom det inte finns några tillgängliga licenser för den här användaren. Du kan antingen köpa ytterligare licenser för mål programmet eller granska användar tilldelningarna och mappnings konfigurationen för attribut för att säkerställa att rätt användare tilldelas rätt attribut.|
|DuplicateTargetEntries  |Det gick inte att slutföra åtgärden eftersom mer än en användare i mål programmet hittades med de konfigurerade matchande attributen. Ta antingen bort den duplicerade användaren från mål programmet eller konfigurera om dina mappningar för attribut enligt beskrivningen [här](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Det gick inte att slutföra åtgärden eftersom mer än en användare hittades med de konfigurerade matchande attributen. Ta antingen bort den duplicerade användaren eller konfigurera om dina mappningar för attribut enligt beskrivningen [här](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | När varje användare utvärderas försöker vi importera användaren från käll systemet. Det här felet uppstår vanligt vis när användaren som importeras saknar matchande egenskap som definierats i dina Attribute-mappningar. Utan ett värde som finns på användarobjektet för det matchande attributet kan vi inte utvärdera omfattnings-, matchnings-eller export ändringar. Obs! närvaro av det här felet anger inte att användaren är i omfånget eftersom vi ännu inte har utvärderat omfattning för användaren.|
|EntrySynchronizationSkipped | Etablerings tjänsten har skickat frågan till käll systemet och identifierade användaren. Ingen ytterligare åtgärd vidtogs för användaren och de hoppades över. Det kan bero på att användaren är utanför omfånget eller att användaren redan finns i mål systemet utan ytterligare ändringar som krävs.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| När du utför en GET-begäran för att hämta en användare eller grupp, fick vi flera användare eller grupper i svaret. Vi förväntade dig att endast ta emot en användare eller grupp i svaret. Om [exempelvis](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)vi gör en get-begäran för att hämta en grupp och tillhandahåller ett filter för att undanta medlemmar och din scim-slutpunkt returnerar medlemmarna, kommer vi att utlösa det här felet.|

## <a name="next-steps"></a>Nästa steg

* [Kontrol lera status för användar etablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problem med att konfigurera användar etablering i ett Azure AD Gallery-program](../app-provisioning/application-provisioning-config-problem.md)
* [Konfiguration av loggar Graph API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)
