---
title: Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen | Microsoft Docs
description: Introduktion till rapporter om inloggningsaktiviteter i Azure Active Directory-portalen
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
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d61962667953b20f4b542874e902411bb579b9c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93122851"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen

Rapporterings arkitekturen i Azure Active Directory (Azure AD) består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – information om användningen av hanterade program och användar inloggnings aktiviteter.
    - **Gransknings loggar**  -  [Gransknings loggar](concept-audit-logs.md) innehåller information om system aktivitet för användare och grupp hantering, hanterade program och katalog aktiviteter.
    - **Etablerings loggar**  -  Med [etablerings loggar](./concept-provisioning-logs.md) kan kunder övervaka aktivitet från etablerings tjänsten, till exempel skapa en grupp i ServiceNow eller en användare som importer ATS från Workday. 
- **Säkerhet** 
    - **Riskfyllda inloggningar** – en [riskfylld inloggning](../identity-protection/overview-identity-protection.md) är en indikator för ett inloggnings försök av någon som inte är legitim ägare till ett användar konto.
    - **Användare som har flaggats för risk** – en [riskfylld användare](../identity-protection/overview-identity-protection.md) är en indikator för ett användar konto som kan ha komprometterats.

Den här artikeln ger en översikt över inloggnings rapporten.

## <a name="prerequisites"></a>Förutsättningar

### <a name="who-can-access-the-data"></a>Vem som kan komma åt data?

* Användare i rollerna säkerhets administratör, säkerhets läsare, global läsare och rapport läsare
* Globala administratörer
* Alla användare (icke-administratörer) kan komma åt sina egna inloggningar 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?

Rapporten inloggnings aktivitet är tillgänglig i [alla versioner av Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) och kan också nås via Microsoft Graph API.

## <a name="sign-ins-report"></a>Rapport över inloggningar

Rapporten användar inloggning innehåller svar på följande frågor:

* Vilket inloggningsmönster har en användare?
* Hur många användare har loggat in under en vecka?
* Vad är status för dessa inloggningar?

Välj **Azure Active Directory** på [Azure Portal](https://portal.azure.com) -menyn eller sök efter och välj **Azure Active Directory** från vilken sida som helst.

![Välj Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

Öppna [inloggnings rapporten](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)genom att välja **inloggningar** under **övervakning**.

![Skärm bild som visar inloggningar som valts på menyn för övervakning.](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Inloggningsaktivitet")

Det kan ta upp till två timmar innan några inloggnings poster visas i portalen.

> [!IMPORTANT]
> Inloggnings rapporten visar bara de **interaktiva** inloggningarna, det vill säga inloggningar där användaren manuellt loggar in med sitt användar namn och lösen ord. Icke-interaktiva inloggnings program, till exempel tjänst-till-tjänst-autentisering, visas inte i inloggnings rapporten. 

En inloggningslogg har en standardlistvy som visar:

- inloggningsdatum
- den aktuella användaren
- Programmet som användaren har loggat in på
- inloggningsstatus
- status för riskidentifieringen
- status för MFA-kravet (multifaktorautentisering).

![Skärm bild visar inloggnings programmen för Office 365 SharePoint Online.](./media/concept-sign-ins/sign-in-activity.png "Inloggningsaktivitet")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Skärm bild som visar alternativet kolumner på inloggnings sidan.](./media/concept-sign-ins/19.png "Inloggningsaktivitet")

Dialog rutan **kolumner** ger dig till gång till de valbara attributen. I en inloggnings rapport kan du inte ha fält som har fler än ett värde för en specifik inloggnings förfrågan som kolumn. Detta är till exempel sant för autentiseringsinformation, villkorliga åtkomst data och nätverks plats.   

![Skärm bild som visar dialog rutan kolumner där du kan välja attribut.](./media/concept-sign-ins/columns.png "Inloggningsaktivitet")

Välj ett objekt i listvyn om du vill ha mer detaljerad information.

![Skärm bild som visar en detaljerad informations vy.](./media/concept-sign-ins/basic-sign-in.png "Inloggningsaktivitet")

> [!NOTE]
> Kunder kan nu felsöka principer för villkorlig åtkomst via alla inloggnings rapporter. Genom att klicka på fliken **villkorlig åtkomst** för en inloggnings post kan kunder granska status för villkorlig åtkomst och se information om de principer som tillämpas på inloggningen och resultatet för varje princip.
> Mer information finns i [vanliga frågor och svar om ca-information i alla inloggningar](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrera inloggningsaktivitet

Först begränsar du de rapporterade data till en nivå som passar dig. Andra, filtrera inloggnings data med hjälp av datum fältet som standard filter. Azure AD ger dig ett brett utbud av ytterligare filter som du kan ställa in:

![Skärm bild som visar alternativet Lägg till filter.](./media/concept-sign-ins/04.png "Inloggningsaktivitet")

ID för **begäran** : ID för den begäran du bryr dig om.

**Användare** – namnet eller User Principal Name (UPN) för den användare som du bryr dig om.

**Program** – namnet på mål programmet.
 
**Status** – den inloggnings status du bryr dig om:

- Klart

- Fel

- Avbröts


**IP-adress** – IP-adressen för den enhet som används för att ansluta till din klient organisation.

**Plats** – platsen som anslutningen initierades från:

- City

- Delstat/provins

- Land/region


**Resurs** – namnet på den tjänst som används för inloggningen.


**Resurs-ID** – ID för tjänsten som används för inloggningen.


**Klient program** – typen av klient program som används för att ansluta till din klient organisation:

![Filter för klient program](./media/concept-sign-ins/client-app-filter.png)


|Name|Modern autentisering|Beskrivning|
|---|:-:|---|
|Autentiserad SMTP| |Används av POP-och IMAP-klienten för att skicka e-postmeddelanden.|
|Tjänsten automatisk upptäckt| |Används av Outlook och EAS-klienter för att hitta och ansluta till post lådor i Exchange Online.|
|Exchange ActiveSync| |Det här filtret visar alla inloggnings försök där EAS-protokollet har gjorts.|
|Webbläsare|![Blå bock.](./media/concept-sign-ins/check.png)|Visar alla inloggnings försök från användare som använder webbläsare|
|Exchange ActiveSync| | Visar alla inloggnings försök från användare med klient program som använder Exchange ActiveSync för att ansluta till Exchange Online|
|Exchange Online PowerShell| |Används för att ansluta till Exchange Online med fjärr-PowerShell. Om du blockerar grundläggande autentisering för Exchange Online PowerShell måste du använda Exchange Online PowerShell-modulen för att ansluta. Mer information finns i [ansluta till Exchange Online PowerShell med Multi-Factor Authentication](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Exchange-webbtjänster| |Ett programmerings gränssnitt som används av Outlook, Outlook för Mac och appar från tredje part.|
|IMAP| |En äldre e-postklient som använder IMAP för att hämta e-post.|
|MAPI över HTTP| |Används av Outlook 2010 och senare.|
|Mobilappar och skriv bords klienter|![Blå bock.](./media/concept-sign-ins/check.png)|Visar alla inloggnings försök från användare som använder mobilappar och skriv bords klienter.|
|Offline-adressbok| |En kopia av adress list samlingar som hämtas och används av Outlook.|
|Outlook överallt (RPC över HTTP)| |Används av Outlook 2016 och tidigare.|
|Outlook-tjänst| |Används av appen e-post och kalender för Windows 10.|
|POP3| |En äldre e-postklient som använder POP3 för att hämta e-post.|
|Rapport webb tjänster| |Används för att hämta rapport data i Exchange Online.|
|Övriga klienter| |Visar alla inloggnings försök från användare där klient programmet inte är inkluderat eller okänt.|



**Operativ system** – operativ systemet som körs på enheten som använder inloggning till din klient organisation. 


**Enhets läsare** – om anslutningen initierades från en webbläsare kan du använda det här fältet för att filtrera efter namn på webbläsare.


**Korrelations-ID** – aktivitetens KORRELATIONS-ID.




**Villkorlig åtkomst** – status för tillämpade regler för villkorlig åtkomst

- **Används inte**: ingen princip tillämpas för användaren och programmet under inloggningen.

- **Lyckades**: en eller flera principer för villkorlig åtkomst som tillämpas på användaren och programmet (men inte nödvändigt vis de andra villkoren) under inloggningen. 

- **Fel**: inloggningen uppfyllde användar-och program villkoret för minst en princip för villkorlig åtkomst och beviljade kontroller är antingen inte uppfyllda eller har angetts till blockera åtkomst.









## <a name="download-sign-in-activities"></a>Ladda ned inloggningsaktivitet

Klicka på **hämtnings** alternativet för att skapa en CSV-eller JSON-fil med de senaste 250 000 posterna. Börja med [att hämta inloggnings data](quickstart-download-sign-in-report.md) om du vill arbeta med dem utanför Azure Portal.  

![Ladda ned](./media/concept-sign-ins/71.png "Ladda ned")

> [!IMPORTANT]
> Antalet poster som du kan hämta begränsas av [Azure Active Directory bevarande principer för rapporter](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Genvägar till inloggnings data

Azure AD och Azure Portal ger båda ytterligare start punkter för inloggnings data:

- Översikt över identitetens säkerhets skydd
- Användare
- Grupper
- Företagsprogram

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Användarens inloggnings data i säkerhets skydd för identitet

Användar inloggnings diagrammet på översikts sidan för **identitetens säkerhets skydd** visar vecko agg regeringar för inloggningar. Standardvärdet för tids perioden är 30 dagar.

![Skärm bild som visar ett diagram över inloggningar under en månad.](./media/concept-sign-ins/06.png "Inloggningsaktivitet")

När du klickar på en dag i inloggningsdiagrammet visas en översikt över inloggningsaktiviteterna för den dagen.

Det här visas på varje rad i listan med inloggningsaktiviteter:

* Vem har loggat in?
* Vilket program var målet för inloggningen?
* Vilken status har inloggningen?
* Vilken MFA-status har inloggningen?

När du klickar på ett objekt visas mer information om inloggningen:

- Användar-ID
- User
- Användarnamn
- Program-ID
- Program
- Klient
- Location
- IP-adress
- Datum
- MFA krävs
- Inloggningsstatus

> [!NOTE]
> IP-adresser utfärdas på ett sådant sätt att det inte finns någon slutgiltig anslutning mellan en IP-adress och där datorn med den adressen är fysiskt placerad. Mappning av IP-adresser är krångligt i det faktum att mobila leverantörer och VPN-nätverk utfärdar IP-adresser från centrala pooler som ofta är mycket från var klient enheten faktiskt används. För närvarande i Azure AD-rapporter är det bästa sättet att konvertera IP-adress till en fysisk plats baserat på spår, register data, omvänd sökning och annan information.

På sidan **Användare** visas en fullständig översikt över alla användarinloggningar om du klickar på **Inloggningar** i avsnittet **Aktivitet**.

![Skärm bild som visar avsnittet aktivitet där du kan välja inloggnings program.](./media/concept-sign-ins/08.png "Inloggningsaktivitet")

## <a name="usage-of-managed-applications"></a>Användning av hanterade program

Med en programcentrerad vy över dina inloggningsuppgifter kan du få svar på frågor som:

* Vem använder mina program?
* Vilka är de tre främsta programmen i organisationen?
* Hur fungerar mitt nyaste program?

Start punkten för dessa data är de tre främsta programmen i din organisation. Informationen finns i rapporten de senaste 30 dagarna i **översikts** avsnittet under **företags program**.

![Skärm bild som visar var du kan välja översikt.](./media/concept-sign-ins/10.png "Inloggningsaktivitet")

Grafen för app-Usage vecko Visa insamlingar av inloggningar för de tre främsta programmen under en viss tids period. Standardvärdet för tidsperioden är 30 dagar.

![Skärm bild som visar användning av appar i en månads period.](./media/concept-sign-ins/graph-chart.png "Inloggningsaktivitet")

Om du vill kan du ange att fokusera på ett visst program.

![Rapportering](./media/concept-sign-ins/single-app-usage-graph.png "Rapportera")

När du klickar på en dag i programanvändningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.

Alternativet **Inloggningar** ger dig en fullständig översikt över alla inloggningshändelser för dina program.

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 aktivitets loggar

Du kan visa Microsoft 365 aktivitets loggar från [Microsoft 365 administrations Center](/office365/admin/admin-overview/about-the-admin-center). Överväg den punkt som, Microsoft 365 aktivitet och Azure AD-aktivitets loggar delar ett stort antal katalog resurser. Endast Microsoft 365 administrations Center ger en fullständig översikt över Microsoft 365 aktivitets loggar. 

Du kan också komma åt Microsoft 365 aktivitets loggar via programmering med hjälp av [API: er för Office 365-hantering](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Nästa steg

* [Felkoder för inloggnings aktivitet](reference-sign-ins-error-codes.md)
* [Principer för data lagring i Azure AD](reference-reports-data-retention.md)
* [Azure AD-rapportens fördröjning](reference-reports-latencies.md)