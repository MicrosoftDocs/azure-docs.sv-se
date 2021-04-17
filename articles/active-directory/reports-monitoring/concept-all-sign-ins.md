---
title: Azure Active Directory rapporter om inloggningsaktiviteter – förhandsversion | Microsoft Docs
description: Introduktion till rapporter om inloggningsaktiviteter i Azure Active Directory portalen
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
ms.date: 04/16/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 781cafd9b382868d0aa4f6b77ff7338c4ee15ed2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589677"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Azure Active Directory rapporter om inloggningsaktiviteter – förhandsversion

Med Azure Active Directory-portalen får du åtkomst till tre aktivitetsloggar:

- **Inloggningar –** Information om inloggningar och hur dina resurser används av användarna.
- **[Granskning](concept-audit-logs.md)** – Information om ändringar som tillämpas på din klientorganisation, till exempel användare och grupphantering eller uppdateringar som tillämpas på klientorganisationens resurser.
- **[Etablering –](concept-provisioning-logs.md)** Aktiviteter som utförs av etableringstjänsten, till exempel skapande av en grupp i ServiceNow eller en användare som importerats från Workday.


Rapporten om klassiska inloggningar i Azure Active Directory ger dig en översikt över interaktiva användar inloggningar. Dessutom har du nu åtkomst till ytterligare tre inloggningsrapporter som nu är i förhandsversion:

- Inloggningar för icke-interaktiva användare

- Inloggningar för tjänstens huvudnamn

- Hanterade identiteter för inloggningar med Azure-resurser

Den här artikeln ger dig en översikt över inloggningsaktivitetsrapporten med förhandsversionen av icke-interaktiva, program och hanterade identiteter för inloggningar för Azure-resurser. Information om inloggningsrapporten utan förhandsgranskningsfunktioner finns i [Inloggningsaktivitetsrapporter i Azure Active Directory portalen.](concept-sign-ins.md)



## <a name="prerequisites"></a>Förutsättningar

Innan du kan börja använda den här funktionen bör du känna till svaren på:

- Vem som kan komma åt data?

- Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?

### <a name="who-can-access-the-data"></a>Vem som kan komma åt data?

- Användare med rollerna Säkerhetsadministratör, Säkerhetsläsare och Rapportläsare

- Globala administratörer

- Alla användare (icke-administratörer) kan komma åt sina egna inloggningar 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?

Klientorganisationen måste ha en associerad Azure AD Premium licens för att kunna se inloggningsaktiviteter. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. Det tar några dagar innan data visas i rapporterna när du har uppgraderat till en Premium-licens utan dataaktiviteter innan uppgraderingen.



## <a name="sign-ins-report"></a>Rapport över inloggningar

Inloggningsrapporten innehåller svar på följande frågor:

- Vad är inloggningsmönstret för en användare, ett program eller en tjänst?
- Hur många användare, appar eller tjänster har loggat in under en vecka?
- Vad är status för dessa inloggningar?


På rapportbladet inloggningar kan du växla mellan:

- **Interaktiva användar inloggningar** – Inloggningar där en användare tillhandahåller en autentiseringsfaktor, till exempel ett lösenord, ett svar via en MFA-app, en biometrisk faktor eller en QR-kod.

- **Icke-interaktiva användar inloggningar –** Inloggningar som utförs av en klient för en användares räkning. Dessa inloggningar kräver ingen interaktion eller autentiseringsfaktor från användaren. Till exempel autentisering och auktorisering med uppdaterings- och åtkomsttoken som inte kräver att en användare anger autentiseringsuppgifter.

- **Inloggningar för tjänstens huvudnamn** – Inloggningar av appar och tjänstens huvudnamn som inte omfattar någon användare. I dessa inloggningar tillhandahåller appen eller tjänsten autentiseringsuppgifter för egen räkning för att autentisera eller komma åt resurser.

- **Hanterade identiteter för inloggningar för Azure-resurser** – Inloggningar av Azure-resurser som har hemligheter som hanteras av Azure. Mer information finns i Vad [är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md) 


![Rapporttyper för inloggningar](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins&quot;></a>Användar inloggningar

Varje flik på inloggningsbladet visar standardkolumnerna nedan. Vissa flikar har ytterligare kolumner:

- Inloggningsdatum

- ID för begäran

- Användarnamn eller användar-ID

- Programnamn eller program-ID

- Status för inloggningen

- IP-adressen för den enhet som används för inloggningen



### <a name=&quot;interactive-user-sign-ins&quot;></a>Interaktiva användar inloggningar


Interaktiva användar inloggningar är inloggningar där en användare tillhandahåller en autentiseringsfaktor till Azure AD eller interagerar direkt med Azure AD eller en hjälpapp, till exempel Microsoft Authenticator appen. De faktorer som användarna anger är lösenord, svar på MFA-utmaningar, biometriska faktorer eller QR-koder som en användare tillhandahåller till Azure AD eller till en hjälpapp.

> [!NOTE]
> Den här rapporten innehåller även federerade inloggningar från identitetsproviders som är federerade till Azure AD.  



> [!NOTE] 
> Rapporten över interaktiva användar inloggningar som används för att innehålla vissa icke-interaktiva inloggningar från Microsoft Exchange-klienter. Även om dessa inloggningar inte var interaktiva inkluderades de i rapporten för interaktiva användar inloggningar för ytterligare synlighet. När rapporten för icke-interaktiva användarinloggningar blev offentlig förhandsversion i november 2020 flyttades dessa icke-interaktiva inloggningshändelseloggar till den icke-interaktiva användarinloggningsrapporten för ökad noggrannhet. 


**Rapportstorlek:** liten <br> 
**Exempel:**

- En användare anger användarnamn och lösenord på azure AD-inloggningsskärmen.

- En användare skickar en SMS MFA-utmaning.

- En användare ger en biometrisk gest för att låsa upp sin Windows-dator med Windows Hello för företag.

- En användare federeras till Azure AD med en AD FS SAML-försäkran.


Förutom standardfälten visas även följande i rapporten för interaktiva inloggningar: 

- Inloggningsplatsen

- Om villkorlig åtkomst har tillämpats



Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Kolumner för interaktiv användar inloggning](./media/concept-all-sign-ins/columns-interactive.png &quot;Kolumner för interaktiv användar inloggning")





Genom att anpassa vyn kan du visa ytterligare fält eller ta bort fält som redan visas.

![Alla interaktiva kolumner](./media/concept-all-sign-ins/all-interactive-columns.png)


Välj ett objekt i listvyn för att få mer detaljerad information om relaterad inloggning.

![Inloggningsaktivitet](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Interaktiva användar inloggningar")



### <a name="non-interactive-user-sign-ins"></a>Inloggningar för icke-interaktiva användare

Icke-interaktiva användar inloggningar är inloggningar som utfördes av en klientapp eller os-komponenter för en användares räkning. Precis som interaktiva användar inloggningar görs dessa inloggningar för en användares räkning. Till skillnad från interaktiva användar inloggningar kräver dessa inloggningar inte att användaren anger en autentiseringsfaktor. I stället använder enheten eller klientappen en token eller kod för att autentisera eller komma åt en resurs för en användares räkning. I allmänhet uppfattar användaren dessa inloggningar som att de sker i bakgrunden av användarens aktivitet.


**Rapportstorlek:** Stora <br>
**Exempel:** 

- En klientapp använder en OAuth 2.0-uppdateringstoken för att hämta en åtkomsttoken.

- En klient använder en OAuth 2.0-auktoriseringskod för att hämta en åtkomsttoken och uppdatera token.

- En användare utför enkel inloggning (SSO) till en webb- eller Windows-app på en Azure AD-ansluten dator.

- En användare loggar in på en andra Microsoft Office-app när de har en session på en mobil enhet med HJÄLP av FOCI (Family of Client-IDs).




Förutom standardfälten visar rapporten för icke-interaktiva inloggningar också: 

- Resurs-ID

- Antal grupperade inloggningar




Du kan inte anpassa de fält som visas i den här rapporten.


![Inaktiverade kolumner](./media/concept-all-sign-ins/disabled-columns.png "Inaktiverade kolumner")

För att göra det enklare att sammanfatta data grupperas icke-interaktiva inloggningshändelser. Klienter skapar ofta många icke-interaktiva inloggningar för samma användares räkning under en kort tidsperiod, som delar samma egenskaper förutom den tid då inloggningen gjordes. En klient kan till exempel få en åtkomsttoken en gång i timmen för en användares räkning. Om användaren eller klienten inte ändrar tillstånd är IP-adressen, resursen och all annan information densamma för varje begäran om åtkomsttoken. När Azure AD loggar flera inloggningar som är identiska förutom tid och datum aggregeras dessa inloggningar från samma entitet till en enda rad. En rad med flera identiska inloggningar (förutom datum och tid som utfärdas) har ett värde som är större än 1 i kolumnen #-inloggningar. Du kan expandera raden om du vill se alla olika inloggningar och deras olika tidsstämplar. Inloggningar sammanställs i icke-interaktiva användare när följande data matchar:


- Program

- Användare

- IP-adress

- Status

- Resurs-ID


Du kan:

- Expandera en nod för att se enskilda objekt i en grupp.  

- Klicka på ett enskilt objekt om du vill se all information 


![Inloggningsinformation för icke-interaktiv användare](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Inloggningar för tjänstens huvudnamn

Till skillnad från interaktiva och icke-interaktiva användar inloggningar involverar inte inloggningar med tjänstens huvudnamn någon användare. De loggas i stället in av alla icke-användarkonton, till exempel appar eller tjänstens huvudnamn (förutom inloggning med hanterad identitet, som endast ingår i inloggningsrapporten för hanterade identiteter). I dessa inloggningar tillhandahåller appen eller tjänsten sina egna autentiseringsuppgifter, till exempel ett certifikat eller en apphemlighet för att autentisera eller komma åt resurser.


**Rapportstorlek:** Stora <br>
**Exempel:**

- Tjänstens huvudnamn använder ett certifikat för att autentisera och komma åt Microsoft Graph. 

- Ett program använder en klienthemlighet för att autentisera i flödet för OAuth-klientautentiseringsuppgifter. 


Den här rapporten har en standardlistvy som visar:

- Inloggningsdatum

- ID för begäran

- Tjänstens huvudnamn eller ID

- Status

- IP-adress

- Resursnamn

- Resurs-ID

- Antal inloggningar

Du kan inte anpassa de fält som visas i den här rapporten.

![Inaktiverade kolumner](./media/concept-all-sign-ins/disabled-columns.png "Inaktiverade kolumner")

För att göra det enklare att sammanfatta data i inloggningsloggarna för tjänstens huvudnamn grupperas inloggningshändelser för tjänstens huvudnamn. Inloggningar från samma entitet under samma villkor aggregeras i en enda rad. Du kan expandera raden om du vill se alla olika inloggningar och deras olika tidsstämplar. Inloggningar aggregeras i rapporten för tjänstens huvudnamn när följande data matchar:

- Tjänstens huvudnamn eller ID

- Status

- IP-adress

- Resursnamn eller ID

Du kan:

- Expandera en nod för att se enskilda objekt i en grupp.  

- Klicka på ett enskilt objekt så att all information visas 


![Kolumninformation](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Kolumninformation")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Inloggningar med hanterade identiteter för Azure-resurser 

Inloggningar med hanterade identiteter för Azure-resurser är inloggningar som utförts av resurser där deras hemligheter hanteras av Azure för att förenkla hanteringen av autentiseringsuppgifter.

**Rapportstorlek:** Liten <br> 
**Exempel:**

En virtuell dator med hanterade autentiseringsuppgifter använder Azure AD för att hämta en åtkomsttoken.   


Den här rapporten har en standardlistvy som visar:


- Id för hanterad identitet

- Namn på hanterad identitet

- Resurs

- Resurs-ID

- Antal grupperade inloggningar

Du kan inte anpassa de fält som visas i den här rapporten.

För att göra det enklare att sammanfatta data grupperas hanterade identiteter för inloggningsloggar för Azure-resurser, icke-interaktiva inloggningshändelser. Inloggningar från samma entitet sammanställs till en enda rad. Du kan expandera raden om du vill se alla olika inloggningar och deras olika tidsstämplar. Inloggningar sammanställs i rapporten över hanterade identiteter när alla följande data matchar:

- Namn eller ID för hanterad identitet

- Status

- IP-adress

- Resursnamn eller ID

Välj ett objekt i listvyn för att visa alla inloggningar som är grupperade under en nod.

Välj ett grupperat objekt för att se all information om inloggningen. 


## <a name="sign-in-error-code"></a>Felkod för inloggning

Om inloggningen misslyckades kan du få mer information om orsaken i avsnittet **Grundläggande information** för det relaterade loggobjektet. 

![Skärmbild som visar en detaljerad informationsvy.](./media/concept-all-sign-ins/error-code.png)
 
Medan loggobjektet ger dig en felorsak finns det fall där du kan få mer information med hjälp av [sökverktyget för inloggningsfel.](https://login.microsoftonline.com/error) Om det till exempel är tillgängligt ger det här verktyget dig reparationssteg.  

![Felkod för sökningsverktyg](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>Filtrera inloggningsaktivitet

Genom att ange ett filter kan du begränsa omfånget för de returnerade inloggningsdata. Azure AD ger dig ett brett utbud av ytterligare filter som du kan ange. När du ställer in filtret bör du alltid vara särskilt uppmärksam på det **konfigurerade** datumintervallfiltret. Ett korrekt datumintervallfilter säkerställer att Azure AD endast returnerar de data som du verkligen bryr dig om.     

Med **filtret** Datumintervall kan du definiera en tidsram för returnerade data.
Möjliga värden:

- En månad

- Sju dagar

- Tjugofyra timmar

- Anpassad

![Datumintervallfilter](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Filtrera användar inloggningar

Filtret för interaktiva och icke-interaktiva inloggningar är detsamma. På grund av detta bevaras filtret som du har konfigurerat för interaktiva inloggningar för icke-interaktiva inloggningar och vice versa. 






## <a name="access-the-new-sign-in-activity-reports"></a>Få åtkomst till de nya inloggningsaktivitetsrapporterna 

Rapporten för inloggningsaktivitet i Azure Portal ger dig en enkel metod för att slå på och av förhandsgranskningsrapporten. Om du har aktiverat förhandsgranskningsrapporterna får du en ny meny som ger dig åtkomst till alla typer av inloggningsaktiviteter.     


Så här kommer du åt de nya inloggningsrapporterna med icke-interaktiva inloggningar och program inloggningar: 

1. Välj **Azure Active Directory** i [Azure-portalen](https://portal.azure.com).

    ![Välj Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. I **avsnittet Övervakning** klickar du **på Inloggningar.**

    ![Välj inloggningar](./media/concept-all-sign-ins/sign-ins.png)

3. Klicka på **förhandsgranskningsfältet.**

    ![Aktivera ny vy](./media/concept-all-sign-ins/enable-new-preview.png)

4. Om du vill växla tillbaka till standardvyn klickar du på **förhandsgranskningsfältet** igen. 

    ![Återställa klassisk vy](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Ladda ned rapporter om inloggningsaktiviteter

När du laddar ned en inloggningsaktivitetsrapport gäller följande:

- Du kan ladda ned inloggningsrapporten som CSV- eller JSON-fil.

- Du kan ladda ned upp till 100 000 poster. Om du vill ladda ned mer data använder du rapporterings-API:et.

- Nedladdningen baseras på det filterval som du har gjort.

- Antalet poster som du kan ladda ned begränsas av de principer Azure Active Directory [kvarhållningsprinciper för rapporter.](reference-reports-data-retention.md) 


![Ladda ned rapporter](./media/concept-all-sign-ins/download-reports.png "Ladda ned rapporter")


Varje CSV-nedladdning består av sex olika filer:

- Interaktiva inloggningar

- Autentiseringsinformation för de interaktiva inloggningarna

- Icke-interaktiva inloggningar

- Autentiseringsinformation för icke-interaktiva inloggningar

- Inloggningar för tjänstens huvudnamn

- Inloggningar för hanterad identitet för Azure-resurser

Varje JSON-nedladdning består av fyra olika filer:

- Interaktiva inloggningar (inklusive autentiseringsinformation)

- Icke-interaktiva inloggningar (inklusive autentiseringsinformation)

- Inloggningar för tjänstens huvudnamn

- Inloggningar för hanterade identiteter för Azure-resurser

![Ladda ned filer](./media/concept-all-sign-ins/download-files.png "Ladda ned filer")




## <a name="next-steps"></a>Nästa steg

* [Felkoder för inloggningsaktivitetsrapport](reference-sign-ins-error-codes.md)
* [Principer för databevarande i Azure AD](reference-reports-data-retention.md)
* [Svarstider för Azure AD-rapporter](reference-reports-latencies.md)
