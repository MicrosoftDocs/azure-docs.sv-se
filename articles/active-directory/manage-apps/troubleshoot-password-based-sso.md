---
title: Felsöka lösenordsbaserad enkel inloggning i Azure Active Directory
description: Felsöka problem med en Azure AD-app som har konfigurerats för lösenordsbaserad enkel inloggning.
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.openlocfilehash: 2d9bcbdcb292f33d4b1f9dd1d0f6779d8d6cdd49
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376485"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Felsöka lösenordsbaserad enkel inloggning i Azure AD

Om du vill använda lösenordsbaserad enkel inloggning (SSO) Mina appar måste webbläsartillägget installeras. Tillägget laddas ned automatiskt när du väljer en app som har konfigurerats för lösenordsbaserad enkel inloggning. Mer information om hur Mina appar från ett slutanvändarperspektiv finns i [Mina appar hjälp för portalen.](../user-help/my-apps-portal-end-user-access.md)

## <a name="my-apps-browser-extension-not-installed"></a>Mina appar webbläsartillägget har inte installerats
Kontrollera att webbläsartillägget är installerat. Mer information finns i Planera [en Azure Active Directory Mina appar distribution.](my-apps-deployment-plan.md) 

## <a name="single-sign-on-not-configured"></a>Enkel inloggning har inte konfigurerats
Kontrollera att lösenordsbaserad enkel inloggning har konfigurerats. Mer information finns i [Konfigurera lösenordsbaserad enkel inloggning.](configure-password-single-sign-on-non-gallery-applications.md)

## <a name="users-not-assigned"></a>Användare som inte har tilldelats
Kontrollera att användaren är tilldelad till appen. Mer information finns i Tilldela [en användare eller grupp till en app.](assign-user-or-group-access-portal.md)

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Autentiseringsuppgifterna fylls i, men tillägget skickar dem inte

Det här problemet inträffar vanligtvis om programleverantören nyligen har ändrat sin inloggningssida för att lägga till ett fält, ändrat en identifierare som används för att identifiera fälten för användarnamn och lösenord eller ändrat hur inloggningen fungerar för programmet. Lyckligtvis kan Microsoft i många fall arbeta med programleverantörer för att snabbt lösa dessa problem.

Microsoft har tekniker för att automatiskt upptäcka när integreringar bryts, men det kanske inte går att hitta problemen direkt, eller så tar det lite tid att åtgärda problemen. Om någon av dessa integreringar inte fungerar korrekt öppnar du ett supportfall så att det kan åtgärdas så snabbt som möjligt.

**Om du har kontakt med programmets** leverantör skickar du dem på vårt sätt så att Microsoft kan arbeta med dem för att integrera programmet inbyggt med Azure Active Directory. Du kan skicka leverantören till Listning [av ditt program i Azure Active Directory för att](../develop/v2-howto-app-gallery-listing.md) komma igång.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Autentiseringsuppgifterna fylls i och skickas, men sidan indikerar att autentiseringsuppgifterna är felaktiga

Prova följande för att lösa problemet:

- Försök först att logga **in på programwebbplatsen direkt med de** autentiseringsuppgifter som lagras åt dem.

  * Om inloggningen fungerar kan användaren klicka på knappen  Uppdatera autentiseringsuppgifter  på programpanelen i avsnittet Appar i [Mina appar](https://myapps.microsoft.com/) för att uppdatera dem till det senaste kända fungerande användarnamnet och lösenordet. 

  * Om du eller någon annan administratör har tilldelat autentiseringsuppgifterna för den här användaren, hittar du användarens eller gruppens programtilldelning genom att gå till fliken Användare **& Grupper** i programmet, välja tilldelningen och klicka på knappen Uppdatera **autentiseringsuppgifter.**

- Om användaren har tilldelat sina egna autentiseringsuppgifter ska användaren kontrollera att lösenordet inte har  upphört att gälla i programmet. I så fall uppdaterar du lösenordet som har upphört att gälla genom att logga **in** i programmet direkt.

  * När lösenordet har uppdaterats i programmet kan du  be användaren  att klicka  på knappen Uppdatera autentiseringsuppgifter på programpanelen i avsnittet Appar i [Mina appar](https://myapps.microsoft.com/) för att uppdatera dem till det senaste kända fungerande användarnamnet och lösenordet.

  * Om du eller någon annan administratör har tilldelat autentiseringsuppgifterna för den här användaren, hittar du användarens eller gruppens programtilldelning genom att gå till fliken Användare **& Grupper** i programmet, välja tilldelningen och klicka på knappen Uppdatera **autentiseringsuppgifter.**

- Kontrollera att Mina appar-webbläsartillägget körs och är aktiverat i användarens webbläsare.

- Se till att användarna inte försöker logga in på programmet från Mina appar i **inkognitoläge, inPrivate- eller privat läge.** Tillägget Mina appar stöds inte i dessa lägen.

Om de tidigare förslagen inte fungerar kan det vara så att en ändring har gjorts på programsidan som tillfälligt har brutit programmets integrering med Azure AD. Detta kan till exempel inträffa när programleverantören introducerar ett skript på sidan som beter sig annorlunda för manuella kontra automatiserade indata, vilket gör att den automatiserade integreringen, som vår egen, bryts. Lyckligtvis kan Microsoft i många fall arbeta med programleverantörer för att snabbt lösa dessa problem.

Microsoft har tekniker för att automatiskt upptäcka när programintegreringar bryts, men det kanske inte går att hitta problemen direkt, eller så kan problemen ta lite tid att åtgärda. När en integrering inte fungerar korrekt kan du öppna ett supportfall för att åtgärda det så snabbt som möjligt. 

Om du dessutom har kontakt med programmets leverantör **skickar**  du dem på vårt sätt så att vi kan arbeta med dem för att integrera programmet inbyggt med Azure Active Directory. Du kan skicka leverantören till Listning [av ditt program i Azure Active Directory för att](../develop/v2-howto-app-gallery-listing.md) komma igång.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Kontrollera om programmets inloggningssida har ändrats nyligen eller kräver ytterligare ett fält

Om programmets inloggningssida har ändrats drastiskt kan det leda till att våra integreringar bryts. Ett exempel på detta är när en programleverantör lägger till ett inloggningsfält, en captcha eller multifaktorautentisering i sina upplevelser. Lyckligtvis kan Microsoft i många fall arbeta med programleverantörer för att snabbt lösa dessa problem.

Microsoft har tekniker för att automatiskt upptäcka när programintegreringar bryts, men det kanske inte går att hitta problemen direkt, eller så kan problemen ta lite tid att åtgärda. När en integrering inte fungerar korrekt kan du öppna ett supportfall för att åtgärda det så snabbt som möjligt. 

Om du dessutom har kontakt med programmets leverantör **skickar**  du dem på vårt sätt så att vi kan arbeta med dem för att integrera programmet inbyggt med Azure Active Directory. Du kan skicka leverantören till [listningsprogrammet i Azure Active Directory för](../develop/v2-howto-app-gallery-listing.md) att komma igång.

## <a name="capture-sign-in-fields-for-an-app"></a>Avbilda inloggningsfält för en app

Infångade inloggningsfält stöds endast för HTML-aktiverade inloggningssidor. Det stöds inte för icke-standardbaserade inloggningssidor, som de som använder Adobe Flash eller andra icke-HTML-aktiverade tekniker.

Det finns två sätt att samla in inloggningsfält för dina anpassade appar:

- **Automatisk registrering av inloggningsfält** fungerar bra med de flesta HTML-aktiverade inloggningssidor, om de använder välkända *DIV-ID:n* för fälten användarnamn och lösenord. HTML-koden på sidan är uppskrasad för att hitta DIV-ID:er som matchar vissa kriterier. Dessa metadata sparas så att de kan spelas upp igen till appen senare.

- **Manuell registrering av inloggningsfält** används om appleverantören *inte etiketterar inloggningsfälten*. Manuell insamling används också om leverantören *återger flera fält som inte kan identifieras automatiskt.* Azure Active Directory (Azure AD) kan lagra data för så många fält som det finns på inloggningssidan, om du berättar var fälten finns på sidan.

Om automatisk fältinfångst för inloggning inte fungerar kan du prova det manuella alternativet.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Avbilda inloggningsfält automatiskt för en app

Följ dessa steg om du vill konfigurera lösenordsbaserad enkel inloggning med hjälp av automatisk fältinfångst för inloggning:
1. Öppna [Azure-portalen](https://portal.azure.com/). Logga in som global administratör eller medadministratör.
2. I navigeringsfönstret till vänster väljer du Alla tjänster **för att** öppna Azure AD-tillägget.
3. Skriv **Azure Active Directory** i filtersökrutan och välj **sedan Azure Active Directory**.
4. Välj **Företagsprogram** i Azure AD-navigeringsfönstret.
5. Välj **Alla program** för att visa en lista över dina appar.
   > [!NOTE]
   > Om du inte ser den app som du vill använda använder du **filterkontrollen** överst i **listan Alla** program. Ange alternativet **Visa** till "Alla program".
6. Välj den app som du vill konfigurera för enkel inloggning.
7. När appen har laddats **väljer du Enkel** inloggning i navigeringsfönstret till vänster.
8. Välj **Lösenordsbaserat inloggningsläge.**
9. Ange **Inloggnings-URL,** som är URL:en för den sida där användarna anger sitt användarnamn och lösenord för att logga in. *Kontrollera att inloggningsfälten visas på sidan för den URL som du anger.*
10. Välj **Spara**.
    Sidan skrasas automatiskt för rutorna användarnamn och lösenord. Du kan nu använda Azure AD för att på ett säkert sätt överföra lösenord till appen med hjälp Mina appar webbläsartillägget.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Avbilda inloggningsfält för en app manuellt

Om du vill avbilda inloggningsfält manuellt måste du ha Mina appar webbläsartillägget installerat. Webbläsaren kan inte heller köras i *inPrivate,* *incognito* eller *privat* läge.

Följ dessa steg om du vill konfigurera lösenordsbaserad enkel inloggning för en app med hjälp av manuell infångning av inloggningsfält:
1. Öppna [Azure-portalen](https://portal.azure.com/). Logga in som global administratör eller medadministratör.
2. I navigeringsfönstret till vänster väljer du Alla tjänster **för att** öppna Azure AD-tillägget.
3. Skriv **Azure Active Directory** i filtersökrutan och välj sedan **Azure Active Directory**.
4. Välj **Företagsprogram** i Azure AD-navigeringsfönstret.
5. Välj **Alla program** för att visa en lista över dina appar.
   > [!NOTE] 
   > Om du inte ser den app som du vill använda kan du använda **filterkontrollen** överst i **listan Alla** program. Ange alternativet **Visa** till "Alla program".
6. Välj den app som du vill konfigurera för enkel inloggning.
7. När appen har laddats **väljer du Enkel** inloggning i navigeringsfönstret till vänster.
8. Välj **Lösenordsbaserat inloggningsläge.**
9. Ange **inloggnings-URL:en**, som är den sida där användarna anger sitt användarnamn och lösenord för att logga in. *Kontrollera att inloggningsfälten är synliga på sidan för den URL som du anger.*
10. Välj **Konfigurera inställningar för enkel *&lt; &gt;* inloggning med appnamn lösenord.**
11. Välj **Identifiera inloggningsfält manuellt.**
14. Välj **OK**.
15. Välj **Spara**.
16. Följ anvisningarna för att använda Mina appar.


## <a name="troubleshoot-problems"></a>Felsöka problem

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Jag får felmeddelandet "Det gick inte att hitta några inloggningsfält på den URL:en"

Du får det här felmeddelandet när automatisk identifiering av inloggningsfält misslyckas. Prova att identifiera fältet för manuell inloggning för att lösa problemet. Se avsnittet [Avbilda inloggningsfält manuellt för ett program i](#manually-capture-sign-in-fields-for-an-app) den här artikeln.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Jag får ett felmeddelande om att det inte går att spara konfiguration av enkel inloggning

Det går sällan att uppdatera SSO-konfigurationen. Prova att spara konfigurationen igen för att lösa problemet.

Om du får felet kan du öppna ett supportfall. Ta med informationen som beskrivs [](#view-portal-notification-details) i visa portalmeddelandeinformation och Skicka meddelandeinformation till en supporttekniker för att [få hjälpavsnitt](#send-notification-details-to-a-support-engineer-to-get-help) i den här artikeln.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Jag kan inte identifiera inloggningsfält för min app manuellt

Du kan se följande beteenden när manuell identifiering inte fungerar:
- Den manuella insamlingsprocessen verkade fungera, men de avbildade fälten är inte korrekta.
- Rätt fält markeras inte när avskiljningsprocessen körs.
- Av insamlingsprocessen tar dig till appens inloggningssida som förväntat, men ingenting händer.
- Manuell insamling verkar fungera, men enkel inloggning sker inte när användare navigerar till appen från Mina appar.

Om du får något av dessa problem kan du göra följande:
- Kontrollera att du har den senaste versionen av Mina appar webbläsartillägget *installerat och aktiverat*.
- Kontrollera att webbläsaren inte är i *inkognitoläge,* *inPrivate-* eller *privat* läge under avskiljningsprocessen. Tillägget Mina appar stöds inte i dessa lägen.
- Kontrollera att användarna inte försöker logga in i appen från Mina appar i *inkognitoläge,* *inPrivate-* eller *privat läge.*
- Försök att avbilda manuellt igen. Kontrollera att de röda markörerna finns över rätt fält.
- Om den manuella insamlingsprocessen verkar sluta svara eller om inloggningssidan inte svarar kan du försöka med den manuella insamlingsprocessen igen. Men den här gången, när du har slutfört processen, trycker du på F12-tangenten för att öppna webbläsarens utvecklarkonsol. Välj **konsolfliken.** Skriv **window.location=" den *&lt; inloggnings-URL &gt;*** som du angav när du konfigurerade appen och tryck sedan på Retur. Detta tvingar en sidomdirigering som avslutar insamlingsprocessen och lagrar de fält som har avbildats.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Jag kan inte lägga till en annan användare i min lösenordsbaserade SSO-app

Lösenordsbaserad SSO-app har en gräns på 48 användare. Därför har den en gräns på 48 nycklar för par med användarnamn/lösenord per app.
Om du vill lägga till ytterligare användare kan du antingen:
-   Lägg till ytterligare instans av appen
-   Ta bort användare som inte längre använder appen först

## <a name="request-support"></a>Begära support 
Om du får ett felmeddelande när du ställer in enkel inloggning och tilldelar användare öppnar du en supportbiljett. Ta med så mycket av följande information som möjligt:

-   Korrelationsfel-ID
-   UPN (användarens e-postadress)
-   TenantID
-   Typ av webbläsare
-   Tidszon och tid/tidsram när felet inträffade
-   Fiddler-spårningar

### <a name="view-portal-notification-details"></a>Visa information om portalmeddelanden

Följ dessa steg om du vill se information om alla portalmeddelanden:
1. Välj **meddelandeikonen** (klockan) i det övre högra hörnet av Azure Portal.
2. Välj ett meddelande som visar ett *feltillstånd.* (De har en röd "!".)
   > [!NOTE]
   > Du kan inte välja meddelanden som har *statusen Lyckades* *eller Pågår.*
3. Fönstret **Meddelandeinformation** öppnas. Läs informationen om du vill veta mer om problemet.
5. Om du fortfarande behöver hjälp kan du dela informationen med en supporttekniker eller produktgruppen. Välj **kopieringsikonen** till höger om rutan **Kopiera fel för** att kopiera meddelandeinformationen för delning.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Skicka meddelandeinformation till en supporttekniker för att få hjälp

Det är viktigt att du delar *all information* som anges i det här avsnittet med support så att de kan hjälpa dig snabbt. Om du vill spela in den kan du ta en skärmbild eller välja **Kopiera fel.**

Följande information förklarar vad varje meddelandeobjekt innebär och innehåller exempel.

#### <a name="essential-notification-items"></a>Viktiga meddelandeobjekt

- **Rubrik:** meddelandets beskrivande rubrik.

   Exempel: *Programproxyinställningar*

- **Beskrivning**: vad som inträffade som ett resultat av åtgärden.

   Exempel: *Intern URL som angetts används redan av ett annat program.*

- **Meddelande-ID:** det unika ID:t för meddelandet.

    Exempel: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Id för klientbegäran:** det specifika begärande-ID som webbläsaren har gjort.

    Exempel: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Tidsstämpel UTC:** tidsstämpeln för när meddelandet inträffade i UTC.

    Exempel: *2017-03-23T19:50:43.7583681Z*

- **Internt transaktions-ID:** det interna ID som används för att leta upp felet i våra system.

    Exempel: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN:** Den användare som körde åtgärden.

    Exempel: *\@ tperkins-f128.info*

- **Klientorganisations-ID:** Det unika ID:t för den klientorganisation som användaren som körde åtgärden är medlem i.

    Exempel: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Användarobjekt-ID:** Det unika ID:t för den användare som körde åtgärden.

    Exempel: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Detaljerade meddelandeobjekt

- **Visningsnamn:**(kan vara tomt) ett mer detaljerat visningsnamn för felet.

    Exempel: *Programproxyinställningar*

- **Status:** meddelandets specifika status.

    Exempel: *Misslyckades*

- **Objekt-ID:**(kan vara tomt) objekt-ID:t som åtgärden har körts mot.

   Exempel: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Information:** En detaljerad beskrivning av vad som inträffade till följd av åtgärden.

    Exempel: *Intern URL är ogiltig eftersom den redan <https://bing.com/> används.*

- **Kopieringsfel:** Gör att  du kan välja kopieringsikonen till höger om **textrutan** Kopiera fel för att kopiera meddelandeinformationen för att få hjälp med supporten.

    Exempel:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Nästa steg
* [Snabbstartsserie om programhantering](view-applications-portal.md)
* [Planera distribution av mina appar](my-apps-deployment-plan.md)
