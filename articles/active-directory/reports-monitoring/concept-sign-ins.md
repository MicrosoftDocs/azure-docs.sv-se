---
title: Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen | Microsoft Docs
description: Introduktion till rapporter om inloggningsaktiviteter i Azure Active Directory-portalen
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a4cd69224daa05a3e5931fbde1c85bb8dc9e46a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719015"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen

Som IT-administratör vill du veta hur it-miljön fungerar. Med informationen om systemets hälsotillstånd kan du bedöma om och hur du behöver svara på potentiella problem. 

Som stöd för det här målet ger Azure Active Directory-portalen dig åtkomst till tre aktivitetsloggar:

- **Inloggningar –** Information om inloggningar och hur dina resurser används av användarna.
- **[Granskning](concept-audit-logs.md)** – Information om ändringar som tillämpas på din klientorganisation, till exempel användare och grupphantering eller uppdateringar som tillämpas på klientorganisationens resurser.
- **[Etablering –](concept-provisioning-logs.md)** Aktiviteter som utförs av etableringstjänsten, till exempel skapande av en grupp i ServiceNow eller en användare som importerats från Workday.

Den här artikeln ger dig en översikt över inloggningsrapporten.

## <a name="prerequisites"></a>Förutsättningar

### <a name="who-can-access-the-data"></a>Vem som kan komma åt data?

* Användare med rollerna Säkerhetsadministratör, Säkerhetsläsare, Global läsare och Rapportläsare
* Globala administratörer
* Alla användare (icke-administratörer) kan komma åt sina egna inloggningar 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?

Inloggningsrapporten är tillgänglig i alla [utgåvor av Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) och kan även nås via Microsoft Graph API.

## <a name="sign-ins-report"></a>Rapport över inloggningar

Rapporten över användar inloggningar ger svar på följande frågor:

* Vilket inloggningsmönster har en användare?
* Hur många användare har loggat in under en vecka?
* Vad är status för dessa inloggningar?

På menyn [Azure Portal](https://portal.azure.com) väljer du **Azure Active Directory**, eller söker efter och väljer **Azure Active Directory** från valfri sida.

![Välj Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

Under **Övervakning** väljer **du Inloggningar** för att öppna [inloggningsrapporten.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)

![Skärmbild som visar inloggningar som valts på menyn Övervakning.](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Inloggningsaktivitet")

Det kan ta upp till två timmar innan vissa inloggningsposter visas i portalen.

> [!IMPORTANT]
> Inloggningsrapporten visar bara interaktiva  inloggningar, det vill säga inloggningar där en användare loggar in manuellt med sitt användarnamn och lösenord. Icke-interaktiva inloggningar, till exempel tjänst-till-tjänst-autentisering, visas inte i inloggningsrapporten. 

En inloggningslogg har en standardlistvy som visar:

- inloggningsdatum
- den aktuella användaren
- Programmet som användaren har loggat in på
- inloggningsstatus
- status för riskidentifieringen
- status för MFA-kravet (multifaktorautentisering).

![Skärmbild som visar inloggningar för Office 365 SharePoint Online.](./media/concept-sign-ins/sign-in-activity.png "Inloggningsaktivitet")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Skärmbild som visar alternativet Kolumner på sidan Inloggningar.](./media/concept-sign-ins/19.png "Inloggningsaktivitet")

Dialogrutan **Kolumner** ger dig åtkomst till de valbara attributen. I en inloggningsrapport kan du inte ha fält som har fler än ett värde för en viss inloggningsbegäran som kolumn. Detta gäller till exempel för autentiseringsinformation, villkorliga åtkomstdata och nätverksplats.   

![Skärmbild som visar dialogrutan Kolumner där du kan välja attribut.](./media/concept-sign-ins/columns.png "Inloggningsaktivitet")

Välj ett objekt i listvyn för att få mer detaljerad information.

![Skärmbild som visar en detaljerad informationsvy.](./media/concept-sign-ins/basic-sign-in.png "Inloggningsaktivitet")

> [!NOTE]
> Kunder kan nu felsöka principer för villkorlig åtkomst via alla inloggningsrapporter. Genom att  klicka på fliken Villkorsstyrd åtkomst för en inloggningspost kan kunder granska status för villkorsstyrd åtkomst och fördjupa sig i informationen om de principer som tillämpas på inloggningen och resultatet för varje princip.
> Mer information finns i [Vanliga frågor och svar om CA-information i alla inloggningar.](reports-faq.md#conditional-access)


## <a name="sign-in-error-code"></a>Felkod för inloggning

Om inloggningen misslyckades kan du få mer information om orsaken i avsnittet **Grundläggande information** för det relaterade loggobjektet. 

![inloggningsfelkod](./media/concept-all-sign-ins/error-code.png)
 
Loggobjektet ger dig en felorsak, men det finns fall där du kan få mer information med hjälp av verktyget [för att söka efter inloggningsfel.](https://login.microsoftonline.com/error) Om det till exempel är tillgängligt ger det här verktyget dig reparationssteg.  

![Felkodsuppslagsverktyg](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities&quot;></a>Filtrera inloggningsaktivitet

Först begränsar du de rapporterade data till en nivå som passar dig. För det andra filtrerar du inloggningsdata med datumfältet som standardfilter. Azure AD ger dig en mängd ytterligare filter som du kan ange:

![Skärmbild som visar alternativet Lägg till filter.](./media/concept-sign-ins/04.png &quot;Inloggningsaktivitet")

**Begärande-ID** – ID:t för den begäran som du bryr dig om.

**Användare** – Användarens huvudnamn (UPN) för den användare som du bryr dig om.

**Program** – namnet på målprogrammet.
 
**Status** – Den inloggningsstatus som du bryr dig om:

- Klart

- Fel

- Avbruten


**IP-adress** – IP-adressen för den enhet som används för att ansluta till din klientorganisation.

Plats **–** Platsen som anslutningen initierades från:

- City

- Delstat/provins

- Land/region


**Resurs** – Namnet på den tjänst som används för inloggningen.


**Resurs-ID** – ID:t för den tjänst som används för inloggningen.


**Klientapp** – Typen av klientapp som används för att ansluta till din klientorganisation:

![Klientappfilter](./media/concept-sign-ins/client-app-filter.png)


|Name|Modern autentisering|Description|
|---|:-:|---|
|Autentiserad SMTP| |Används av POP- och IMAP-klienter för att skicka e-postmeddelanden.|
|Autodiscover| |Används av Outlook- och EAS-klienter för att hitta och ansluta till postlådor i Exchange Online.|
|Exchange ActiveSync| |Det här filtret visar alla inloggningsförsök där EAS-protokollet har försökts.|
|Webbläsare|![Blå bockmarkering.](./media/concept-sign-ins/check.png)|Visar alla inloggningsförsök från användare som använder webbläsare|
|Exchange ActiveSync| | Visar alla inloggningsförsök från användare med klientappar som använder Exchange ActiveSync för att ansluta till Exchange Online|
|Exchange Online PowerShell| |Används för att ansluta till Exchange Online med fjärr-PowerShell. Om du blockerar grundläggande autentisering för Exchange Online PowerShell måste du använda Exchange Online PowerShell-modulen för att ansluta. Anvisningar finns i Ansluta [till Exchange Online PowerShell med multifaktorautentisering.](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)|
|Exchange-webbtjänster| |Ett programmeringsgränssnitt som används av Outlook, Outlook för Mac och appar från tredje part.|
|IMAP4| |En äldre e-postklient som använder IMAP för att hämta e-post.|
|MAPI över HTTP| |Används av Outlook 2010 och senare.|
|Mobilappar och skrivbordsklienter|![Blå bockmarkering.](./media/concept-sign-ins/check.png)|Visar alla inloggningsförsök från användare som använder mobilappar och skrivbordsklienter.|
|Offlineadressbok| |En kopia av samlingar med adresslistor som laddas ned och används av Outlook.|
|Outlook Anywhere (RPC över HTTP)| |Används av Outlook 2016 och tidigare.|
|Outlook-tjänst| |Används av e-post- och kalenderappen för Windows 10.|
|POP3| |En äldre e-postklient som använder POP3 för att hämta e-post.|
|Reporting Web Services| |Används för att hämta rapportdata i Exchange Online.|
|Övriga klienter| |Visar alla inloggningsförsök från användare där klientappen inte ingår eller är okänd.|



**Operativsystem –** Operativsystemet som körs på enheten använde inloggning till din klientorganisation. 


**Enhetswebbläsare** – Om anslutningen har initierats från en webbläsare kan du filtrera efter webbläsarnamn i det här fältet.


**Korrelations-ID** – korrelations-ID för aktiviteten.




**Villkorlig åtkomst** – Status för de tillämpade reglerna för villkorlig åtkomst

- **Tillämpas inte:** Ingen princip tillämpas på användaren och programmet under inloggningen.

- **Lyckades:** En eller flera principer för villkorlig åtkomst som tillämpas på användaren och programmet (men inte nödvändigtvis de andra villkoren) under inloggningen. 

- **Fel:** Inloggningen uppfyllt användar- och programvillkoret för minst en princip för villkorlig åtkomst och beviljandekontroller är antingen inte uppfyllda eller inställda på att blockera åtkomst.









## <a name="download-sign-in-activities"></a>Ladda ned inloggningsaktivitet

Klicka på **alternativet** Ladda ned för att skapa en CSV- eller JSON-fil med de senaste 250 000 posterna. Börja med [att ladda ned inloggningsdata](quickstart-download-sign-in-report.md) om du vill arbeta med dem utanför Azure Portal.  

![Ladda ned](./media/concept-sign-ins/71.png "Ladda ned")

> [!IMPORTANT]
> Antalet poster som du kan ladda ned begränsas av de principer [Azure Active Directory rapportbevarande](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Genvägar till inloggningsdata

Azure AD och Azure Portal ger dig ytterligare startpunkter för inloggningsdata:

- Översikt över Identity Security Protection
- Användare
- Grupper
- Företagsprogram

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Användare loggar in data i Identity Security Protection

Diagrammet över användarindata på **översiktssidan för Identity Security Protection** visar veckovisa sammanställningar av inloggningar. Standardvärdet för tidsperioden är 30 dagar.

![Skärmbild som visar ett diagram över inloggningar under en månad.](./media/concept-sign-ins/06.png "Inloggningsaktivitet")

När du klickar på en dag i inloggningsdiagrammet visas en översikt över inloggningsaktiviteterna för den dagen.

Det här visas på varje rad i listan med inloggningsaktiviteter:

* Vem har loggat in?
* Vilket program var målet för inloggningen?
* Vilken status har inloggningen?
* Vilken MFA-status har inloggningen?

När du klickar på ett objekt visas mer information om inloggningen:

- Användar-ID
- Användare
- Användarnamn
- Program-ID:t
- Program
- Klient
- Location
- IP-adress
- Datum
- MFA krävs
- Inloggningsstatus

> [!NOTE]
> IP-adresser utfärdas på ett sådant sätt att det inte finns någon slutgiltig anslutning mellan en IP-adress och var datorn med den adressen finns fysiskt. Mappning av IP-adresser är komplicerat eftersom mobilleverantörer och VPN:er utfärdar IP-adresser från centrala pooler som ofta är mycket långt från där klientenhet faktiskt används. För närvarande i Azure AD-rapporter är det bäst att konvertera IP-adresser till en fysisk plats baserat på spårningar, registerdata, omvända upp- och annan information.

På sidan **Användare** visas en fullständig översikt över alla användarinloggningar om du klickar på **Inloggningar** i avsnittet **Aktivitet**.

![Skärmbild som visar avsnittet Aktivitet där du kan välja Inloggningar.](./media/concept-sign-ins/08.png "Inloggningsaktivitet")

## <a name="usage-of-managed-applications"></a>Användning av hanterade program

Med en programcentrerad vy över dina inloggningsuppgifter kan du få svar på frågor som:

* Vem använder mina program?
* Vilka är de tre främsta programmen i din organisation?
* Hur går det för mitt senaste program?

Startpunkten för dessa data är de tre främsta programmen i din organisation. Data finns i rapporten för de senaste 30 dagarna i avsnittet **Översikt** under **Företagsprogram**.

![Skärmbild som visar var du kan välja Översikt.](./media/concept-sign-ins/10.png "Inloggningsaktivitet")

Appanvändningsdiagrammen visar veckovisa sammanställningar av inloggningar för de tre främsta programmen under en viss tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Skärmbild som visar appanvändningen under en månad.](./media/concept-sign-ins/graph-chart.png "Inloggningsaktivitet")

Om du vill kan du ange att fokusera på ett visst program.

![Rapportering](./media/concept-sign-ins/single-app-usage-graph.png "Rapportera")

När du klickar på en dag i programanvändningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.

Alternativet **Inloggningar** ger dig en fullständig översikt över alla inloggningshändelser för dina program.

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 aktivitetsloggar

Du kan visa Microsoft 365 aktivitetsloggar från [Administrationscenter för Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). Tänk på att Microsoft 365 och Azure AD-aktivitetsloggar delar ett stort antal katalogresurser. Endast Administrationscenter för Microsoft 365 ger en fullständig vy över de Microsoft 365 aktivitetsloggarna. 

Du kan också komma åt Microsoft 365 aktivitetsloggar programmatiskt med hjälp av [Office 365 Management-API:erna](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Nästa steg

* [Felkoder för inloggningsaktivitetsrapport](reference-sign-ins-error-codes.md)
* [Kvarhållningsprinciper för Azure AD-data](reference-reports-data-retention.md)
* [Svarstider för Azure AD-rapporter](reference-reports-latencies.md)