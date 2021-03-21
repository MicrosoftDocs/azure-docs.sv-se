---
title: Rapporter för Azure Active Directory inloggnings aktivitet – för hands version | Microsoft Docs
description: Introduktion till rapporterna om inloggnings aktiviteter i Azure Active Directory-portalen
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
ms.date: 03/16/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 185638d683699403c304603d968cfe84e32a55b5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574568"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Rapporter för Azure Active Directory inloggnings aktivitet – för hands version

Rapporterings arkitekturen i Azure Active Directory (Azure AD) består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – information om när användare, program och hanterade resurser loggar in på Azure AD för att komma åt resurser.
    - **Gransknings loggar**  -  [Gransknings loggar](concept-audit-logs.md) innehåller information om system aktivitet för användare och grupp hantering, hanterade program och katalog aktiviteter.
- **Säkerhet** 
    - **Riskfyllda inloggningar** – en [riskfylld inloggning](../identity-protection/overview-identity-protection.md) är en indikator för ett inloggnings försök av någon som inte är legitim ägare till ett användar konto.
    - **Användare som har flaggats för risk** – en [riskfylld användare](../identity-protection/overview-identity-protection.md) är en indikator för ett användar konto som kan ha komprometterats.

I den klassiska inloggnings rapporten i Azure Active Directory får du en översikt över interaktiva användar inloggningar. Dessutom har du nu till gång till tre ytterligare inloggnings rapporter som nu finns i för hands version:

- Icke-interaktiva användar inloggningar

- Inloggnings program för tjänst huvud

- Hanterade identiteter för Azures resurs inloggningar

Den här artikeln ger en översikt över inloggnings aktivitets rapporten med förhands granskningen av icke-interaktiva, program och hanterade identiteter för Azure-resursers inloggningar. Information om inloggnings rapporten utan förhands gransknings funktioner finns i  [rapporter om inloggnings aktiviteter i Azure Active Directory-portalen](concept-sign-ins.md).



## <a name="prerequisites"></a>Förutsättningar

Innan du kan börja använda den här funktionen bör du känna till svaren på:

- Vem som kan komma åt data?

- Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?

### <a name="who-can-access-the-data"></a>Vem som kan komma åt data?

- Användare i rollerna säkerhets administratör, säkerhets läsare och rapport läsare

- Globala administratörer

- Alla användare (icke-administratörer) kan komma åt sina egna inloggningar 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?

Klienten måste ha en Azure AD Premium licens kopplad till sig för att se inloggnings aktiviteter. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. Det tar några dagar för data att visas i rapporterna efter att du har uppgraderat till en Premium-licens utan data aktiviteter före uppgraderingen.



## <a name="sign-ins-report"></a>Rapport över inloggningar

Inloggnings rapporten innehåller svar på följande frågor:

- Vad är inloggnings mönstret för en användare, ett program eller en tjänst?
- Hur många användare, appar eller tjänster har loggat in under en vecka?
- Vad är status för dessa inloggningar?


I rapport bladet inloggningar kan du växla mellan:

- **Interaktiva användar inloggningar** -inloggningar där en användare tillhandahåller en autentiseringsnivå, till exempel ett lösen ord, ett svar via en MFA-app, en bio metrisk faktor eller en QR-kod.

- **Icke-interaktiva användar inloggningar** -inloggningar som utförs av en klient för en användares räkning. Dessa inloggningar kräver ingen interaktion eller autentisering från användaren. Till exempel autentisering och auktorisering med hjälp av uppdaterings-och åtkomsttoken som inte kräver att en användare anger autentiseringsuppgifter.

- **Inloggnings loggar för tjänstens huvud namn** -inloggningar av appar och tjänstens huvud namn som inte omfattar någon användare. I dessa inloggningar ger appen eller tjänsten en autentiseringsuppgift för det egna ombudet att autentisera eller komma åt resurser.

- **Hanterade identiteter för Azure-resurser inloggningar** -inloggningar av Azure-resurser som har hemligheter som hanteras av Azure. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md) 


![Inloggnings rapport typer](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins"></a>Användar inloggningar

På varje flik i inloggnings bladet visas standard kolumnerna nedan. Vissa flikar har ytterligare kolumner:

- Inloggningsdatum

- ID för begäran

- Användar namn eller användar-ID

- Program namn eller program-ID

- Status för inloggningen

- IP-adress för enheten som används för inloggning



### <a name="interactive-user-sign-ins"></a>Interaktiva användar inloggningar


Interaktiva användar inloggningar är inloggningar där en användare tillhandahåller en autentiseringsnivå för Azure AD eller interagerar direkt med Azure AD eller en hjälp program, till exempel Microsoft Authenticator-appen. De faktorer som användarna tillhandahåller inkluderar lösen ord, svar på MFA-utmaningar, bio metriska faktorer eller QR-koder som en användare tillhandahåller för Azure AD eller till en hjälp program.

> [!NOTE]
> Den här rapporten inkluderar även federerade inloggningar från identitets leverantörer som är federerade till Azure AD.  



Obs! rapporten över interaktiva användar inloggningar som används för att innehålla vissa icke-interaktiva inloggningar från Microsoft Exchange-klienter. Även om dessa inloggningar inte var interaktiva fanns de med i rapporten över interaktiva användar inloggningar för ytterligare synlighet. När rapporten icke-interaktiva användare loggar in som offentlig för hands version i november 2020 flyttades de icke-interaktiva inloggnings händelse loggarna till den icke-interaktiva användar inloggnings rapporten för ökad noggrannhet. 


**Rapport storlek:** liten <br> 
**Fler**

- En användare anger användar namn och lösen ord på inloggnings skärmen för Azure AD.

- En användare skickar en SMS MFA-utmaning.

- En användare ger en bio metrisk gest för att låsa upp sina Windows-datorer med Windows Hello för företag.

- En användare är federerad till Azure AD med en AD FS SAML-kontroll.


Förutom standard fälten visar rapporten över interaktiva inloggningar också: 

- Inloggnings platsen

- Om villkorlig åtkomst har tillämpats



Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Interaktiva användares inloggnings kolumner](./media/concept-all-sign-ins/columns-interactive.png "Interaktiva användares inloggnings kolumner")





Genom att anpassa vyn kan du Visa ytterligare fält eller ta bort fält som redan visas.

![Alla interaktiva kolumner](./media/concept-all-sign-ins/all-interactive-columns.png)


Välj ett objekt i listvyn för att få mer detaljerad information om den relaterade inloggningen.

![Inloggnings aktivitet](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Interaktiva användar inloggningar")



### <a name="non-interactive-user-sign-ins"></a>Icke-interaktiva användar inloggningar

Icke-interaktiva användar inloggningar är inloggningar som utfördes av en klient app eller OS-komponenter för en användares räkning. Precis som interaktiva användar inloggningar utförs dessa inloggningar för en användares räkning. Till skillnad från interaktiva användar inloggningar kräver dessa inloggningar inte att användaren anger en autentiseringsnivå. I stället använder enheten eller klient programmet en token eller kod för att autentisera eller komma åt en resurs för en användares räkning. I allmänhet uppfattar användaren dessa inloggningar som i bakgrunden för användarens aktivitet.


**Rapport storlek:** Förstoring <br>
**Exempel:** 

- En klient app använder en OAuth 2,0-uppdateringstoken för att hämta en åtkomsttoken.

- En klient använder en OAuth 2,0-auktoriseringskod för att hämta en åtkomsttoken och en uppdaterad token.

- En användare utför enkel inloggning (SSO) till en webb-eller Windows-app på en Azure AD-ansluten dator.

- En användare loggar in på en andra Microsoft Office app medan de har en session på en mobil enhet med hjälp av FOCI (familj med klient-ID: n).




Förutom standard fälten visas även rapporten icke-interaktiv inloggnings program: 

- Resurs-ID

- Antal grupperade inloggningar




Du kan inte anpassa fälten som visas i den här rapporten.


![Inaktiverade kolumner](./media/concept-all-sign-ins/disabled-columns.png "Inaktiverade kolumner")

För att göra det enklare att sammanställa data, grupperas icke-interaktiva inloggnings händelser. Klienter skapar ofta många icke-interaktiva inloggningar för samma användare under en kort tids period, som delar samma egenskaper, förutom den tid då inloggningen gjordes. En klient kan till exempel få en åtkomsttoken en gång per timme å en användares vägnar. Om användaren eller klienten inte ändrar tillstånd, är IP-adressen, resursen och all annan information densamma för varje åtkomstbegäran. När Azure AD loggar flera inloggningar som är identiska med tiden och datumet, kommer dessa inloggningar från samma entitet att aggregeras till en enda rad. En rad med flera identiska inloggningar (förutom datum och tid som utfärdats) har ett värde som är större än 1 i kolumnen # Sign-ins. Du kan expandera raden för att se alla olika inloggningar och deras olika tidsstämplar. Inloggningar sammanställs i icke-interaktiva användare när följande data matchar:


- Program

- User

- IP-adress

- Status

- Resurs-ID


Du kan:

- Expandera en nod för att se de enskilda objekten i en grupp.  

- Klicka på ett enskilt objekt om du vill visa all information 


![Inloggnings information för icke-interaktiv användare](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Inloggnings program för tjänst huvud

Till skillnad från interaktiva och icke-interaktiva användar inloggningar omfattar inte tjänstens huvud inloggnings objekt en användare. De är i stället inloggnings tillägg för ett icke-användarkonto, till exempel appar eller tjänstens huvud namn (förutom hanterad identitets inloggning) som bara ingår i rapporten hanterad identitets inloggning. I dessa inloggningar tillhandahåller appen eller tjänsten sina egna autentiseringsuppgifter, till exempel ett certifikat eller en app-hemlighet för att autentisera eller komma åt resurser.


**Rapport storlek:** Förstoring <br>
**Exempel:**

- Ett tjänst huvud namn använder ett certifikat för att autentisera och komma åt Microsoft Graph. 

- Ett program använder en klient hemlighet för att autentisera i flödet för OAuth-klientautentiseringsuppgifter. 


Den här rapporten har en Standardlistvy som visar:

- Inloggningsdatum

- ID för begäran

- Namn eller ID för tjänstens huvud namn

- Status

- IP-adress

- Resursnamn

- Resurs-ID

- Antal inloggningar

Du kan inte anpassa fälten som visas i den här rapporten.

![Inaktiverade kolumner](./media/concept-all-sign-ins/disabled-columns.png "Inaktiverade kolumner")

För att göra det enklare att sammanfattad data i inloggnings loggarna för tjänstens huvud grupper grupperas inloggnings händelser för tjänstens huvud namn. Inloggningar från samma entitet under samma villkor sammanställs till en enda rad. Du kan expandera raden för att se alla olika inloggningar och deras olika tidsstämplar. Inloggningar sammanställs i tjänstens huvud namns rapport när följande data matchar:

- Namn eller ID för tjänstens huvud namn

- Status

- IP-adress

- Resurs namn eller ID

Du kan:

- Expandera en nod för att se de enskilda objekten i en grupp.  

- Klicka på ett enskilt objekt så visas all information 


![Kolumn information](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Kolumn information")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Hanterad identitet för Azure-resurser inloggningar 

Hanterad identitet för Azure-resurser är inloggningar som utförts av resurser som har sina hemligheter som hanteras av Azure för att förenkla hanteringen av autentiseringsuppgifter.

**Rapport storlek:** Små <br> 
**Fler**

En virtuell dator med hanterade autentiseringsuppgifter använder Azure AD för att hämta en åtkomsttoken.   


Den här rapporten har en Standardlistvy som visar:


- ID för hanterad identitet

- Namn på hanterad identitet

- Resurs

- Resurs-ID

- Antal grupperade inloggningar

Du kan inte anpassa fälten som visas i den här rapporten.

För att göra det enklare att sammanställa data, hanterade identiteter för Azure-resurser loggar du in loggar, icke-interaktiva inloggnings händelser grupperas. Inloggningar från samma entitet aggregeras i en enda rad. Du kan expandera raden för att se alla olika inloggningar och deras olika tidsstämplar. Inloggningar sammanställs i rapporten hanterade identiteter när alla följande data matchar:

- Namn eller ID för hanterad identitet

- Status

- IP-adress

- Resurs namn eller ID

Välj ett objekt i list visningen för att visa alla inloggningar som är grupperade under en nod.

Välj ett grupperat objekt om du vill visa all information om inloggningen. 


## <a name="filter-sign-in-activities"></a>Filtrera inloggningsaktivitet

Genom att ange ett filter kan du begränsa omfattningen av de returnerade inloggnings data. Azure AD ger dig ett brett utbud av ytterligare filter som du kan ange. När du ställer in filtret bör du alltid ägna särskild uppmärksamhet åt det konfigurerade **datum** intervall filtret. Ett giltigt datum intervall filter garanterar att Azure AD bara returnerar de data som du bryr dig om.     

Med filtret för **datum** intervall kan du definiera en tidsram för de data som returneras.
Möjliga värden:

- En månad

- Sju dagar

- 20 – fyra timmar

- Anpassad

![Filter för datum intervall](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Filtrera användar inloggningar

Filtret för interaktiva och icke-interaktiva inloggningar är detsamma. Därför sparas filtret som du har konfigurerat för interaktiva inloggningar för icke-interaktiva inloggningar och vice versa. 






## <a name="access-the-new-sign-in-activity-reports"></a>Öppna de nya rapporterna om inloggnings aktiviteter 

Rapporten inloggnings aktiviteter i Azure Portal ger dig en enkel metod för att växla mellan-och inaktive rings rapporten. Om du har aktiverat förhands gransknings rapporter får du en ny meny som ger dig åtkomst till alla rapport typer för inloggnings aktiviteter.     


För att komma åt de nya inloggnings rapporterna med icke-interaktiva och program inloggningar: 

1. Välj **Azure Active Directory** i [Azure-portalen](https://portal.azure.com).

    ![Välj Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. I avsnittet **övervakning** klickar du på **inloggningar**.

    ![Välj inloggningar](./media/concept-all-sign-ins/sign-ins.png)

3. Klicka på **förhands gransknings** fältet.

    ![Aktivera ny vy](./media/concept-all-sign-ins/enable-new-preview.png)

4. Klicka på för **hands versions** fältet igen om du vill växla tillbaka till standardvyn. 

    ![Återställ klassisk vy](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Hämta rapporter om inloggnings aktiviteter

När du laddar ned en rapport om inloggnings aktiviteter är följande sant:

- Du kan ladda ned inloggnings rapporten som CSV-eller JSON-fil.

- Du kan hämta upp till 100-K-poster. Om du vill hämta mer data använder du rapporterings-API: et.

- Nedladdningen baseras på det filter val som du har gjort.

- Antalet poster som du kan hämta begränsas av [Azure Active Directory bevarande principer för rapporter](reference-reports-data-retention.md). 


![Ladda ned rapporter](./media/concept-all-sign-ins/download-reports.png "Ladda ned rapporter")


Varje CSV-nedladdning består av sex olika filer:

- Interaktiva inloggningar

- Information om autentisering av interaktiva inloggningar

- Icke-interaktiva inloggningar

- Information om autentisering av de icke-interaktiva inloggnings programmen

- Inloggnings program för tjänst huvud

- Hanterad identitet för Azure-resurser inloggningar

Varje JSON-nedladdning består av fyra olika filer:

- Interaktiva inloggningar (innehåller information om autentisering)

- Icke-interaktiva inloggningar (innehåller information om autentisering)

- Inloggnings program för tjänst huvud

- Hanterad identitet för Azure-resurser inloggningar

![Ladda ned filer](./media/concept-all-sign-ins/download-files.png "Ladda ned filer")




## <a name="next-steps"></a>Nästa steg

* [Felkoder för inloggnings aktivitet](reference-sign-ins-error-codes.md)
* [Principer för data lagring i Azure AD](reference-reports-data-retention.md)
* [Azure AD-rapportens fördröjning](reference-reports-latencies.md)