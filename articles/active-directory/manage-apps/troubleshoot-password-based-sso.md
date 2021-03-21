---
title: Felsök lösenordsbaserad enkel inloggning i Azure Active Directory
description: Felsök problem med en Azure AD-app som är konfigurerad för lösenordsbaserad enkel inloggning.
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: fdbbc6c53e43d3ecc9fd4ecb563bd7ec798f8888
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257589"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Felsöka lösenordsbaserad enkel inloggning i Azure AD

Om du vill använda lösenordsbaserad enkel inloggning (SSO) i Mina appar måste webb läsar tillägget vara installerat. Fil namns tillägget hämtas automatiskt när du väljer en app som är konfigurerad för lösenordsbaserad SSO. Mer information om hur du använder Mina appar från en slut användar perspektiv finns i [hjälpen för min Apps-portalen](../user-help/my-apps-portal-end-user-access.md).

## <a name="my-apps-browser-extension-not-installed"></a>Webb läsar tillägget Mina appar är inte installerat
Kontrol lera att webb läsar tillägget är installerat. Mer information finns i [Planera en Azure Active Directory distribution av mina appar](my-apps-deployment-plan.md). 

## <a name="single-sign-on-not-configured"></a>Enkel inloggning har inte kon figurer ATS
Kontrol lera att lösenordsbaserad enkel inloggning har kon figurer ATS. Mer information finns i [Konfigurera lösenordsbaserad enkel inloggning](configure-password-single-sign-on-non-gallery-applications.md).

## <a name="users-not-assigned"></a>Användare har inte tilldelats
Se till att användaren är tilldelad till appen. Läs mer i [tilldela en användare eller grupp till en app](assign-user-or-group-access-portal.md).

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Autentiseringsuppgifterna fylls i, men tillägget skickar dem inte

Det här problemet uppstår vanligt vis om program leverantören har ändrat sin inloggnings sida nyligen för att lägga till ett fält, ändrat en identifierare som används för att identifiera fälten för användar namn och lösen ord eller ändra hur inloggnings upplevelsen fungerar för programmet. I många fall kan Microsoft samar beta med program leverantörer för att snabbt lösa problemen.

Microsoft har teknik för att automatiskt identifiera när integreringen bryts, men det kanske inte går att hitta problemen direkt, eller så tar det en stund att åtgärda problemet. Om någon av dessa integreringar inte fungerar som den ska kan du öppna ett support ärende så att det kan åtgärdas så snabbt som möjligt.

**Om du är kontakt med den här appens leverantör kan du** skicka dem på vårt sätt så att Microsoft kan samar beta med dem för att internt integrera sina program med Azure Active Directory. Du kan skicka leverantören till [listan över ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md) för att komma igång.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Autentiseringsuppgifterna fylls i och skickas, men sidan indikerar att autentiseringsuppgifterna är felaktiga

Prova med följande saker för att lösa problemet:

- Låt användaren först försöka logga in på **program webbplatsen direkt** med de autentiseringsuppgifter som lagras för dem.

  * Om inloggningen fungerar kan du klicka på knappen **uppdatera autentiseringsuppgifter** på **program panelen** i avsnittet **appar** i [Mina appar](https://myapps.microsoft.com/) för att uppdatera dem till det senaste kända fungerande användar namnet och lösen ordet.

  * Om du eller någon annan administratör har tilldelat autentiseringsuppgifterna för den här användaren, hittar du användarens eller gruppens program tilldelning genom att gå till fliken **användare & grupper** i programmet, välja tilldelningen och klicka på knappen **uppdatera autentiseringsuppgifter** .

- Om användaren har tilldelat sina egna autentiseringsuppgifter, måste användaren **kontrol lera att deras lösen ord inte har gått ut i programmet** , och i så fall **uppdatera sitt utgångna lösen ord** genom att logga in till programmet direkt.

  * När lösen ordet har uppdaterats i programmet ber du användaren att klicka på knappen **uppdatera autentiseringsuppgifter** på **program panelen** i avsnittet **appar** i [Mina appar](https://myapps.microsoft.com/) för att uppdatera dem till det senaste kända fungerande användar namn och lösen ord.

  * Om du eller någon annan administratör har tilldelat autentiseringsuppgifterna för den här användaren, hittar du användarens eller gruppens program tilldelning genom att gå till fliken **användare & grupper** i programmet, välja tilldelningen och klicka på knappen **uppdatera autentiseringsuppgifter** .

- Kontrol lera att webb läsar tillägget My Apps körs och är aktiverat i användarens webbläsare.

- Se till att användarna inte försöker logga in på programmet från Mina appar i **Incognito, InPrivate eller privat läge**. Tillägget Mina appar stöds inte i de här lägena.

Om de tidigare förslagen inte fungerar, kan det vara fallet att en ändring har inträffat på den program sida där det tillfälligt har brutits mellan programmets integrering med Azure AD. Detta kan till exempel inträffa när program leverantören introducerar ett skript på sidan som beter sig annorlunda för manuella kontra automatiserade ingångar, vilket innebär att automatiserad integrering, som vår egen, ska brytas. I många fall kan Microsoft samar beta med program leverantörer för att snabbt lösa problemen.

Microsoft har teknik för att automatiskt identifiera när program integreringen bryts, men det kanske inte går att hitta problemen direkt, eller så kan det ta lite tid att åtgärda problemet. När en integrering inte fungerar som den ska kan du öppna ett support ärende för att få den åtgärdad så snabbt som möjligt. 

Förutom detta, **om du är i kontakt med den här appens leverantör, skickar du** **dem på vårt sätt** så att vi kan samar beta med dem för att internt integrera sina program med Azure Active Directory. Du kan skicka leverantören till [listan över ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md) för att komma igång.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Kontrol lera om programmets inloggnings sida har ändrats nyligen eller kräver ett ytterligare fält

Om programmets inloggnings sida har ändrats drastiskt gör det ibland att våra integrationer kan brytas. Ett exempel på detta är när en program leverantör lägger till ett inloggnings fält, en captcha eller Multi-Factor Authentication i deras upplevelser. I många fall kan Microsoft samar beta med program leverantörer för att snabbt lösa problemen.

Microsoft har teknik för att automatiskt identifiera när program integreringen bryts, men det kanske inte går att hitta problemen direkt, eller så kan det ta lite tid att åtgärda problemet. När en integrering inte fungerar som den ska kan du öppna ett support ärende för att få den åtgärdad så snabbt som möjligt. 

Förutom detta, **om du är i kontakt med den här appens leverantör, skickar du** **dem på vårt sätt** så att vi kan samar beta med dem för att internt integrera sina program med Azure Active Directory. Du kan skicka leverantören till [listan över ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md) för att komma igång.

## <a name="capture-sign-in-fields-for-an-app"></a>Avbilda inloggnings fält för en app

Registrering av inloggnings fält stöds bara för HTML-aktiverade inloggnings sidor. Det stöds inte för inloggnings sidor som inte är standard, t. ex. sådana som använder Adobe Flash eller andra icke-HTML-aktiverade tekniker.

Det finns två sätt att registrera inloggnings fält för dina anpassade appar:

- **Automatisk insamling av inloggnings fält** fungerar bra med de flesta HTML-aktiverade inloggnings sidor, *om de använder välkända div-ID: n* för fälten användar namn och lösen ord. HTML på sidan klipps av för att hitta DIV-id: n som matchar vissa villkor. Metadata sparas så att de kan spelas upp till appen senare.

- **Registrering av manuellt inloggnings fält** används om app-leverantören *inte förser inloggnings fälten med inloggnings information*. Manuell fångst används också om leverantören *återger flera fält som inte kan identifieras automatiskt*. Azure Active Directory (Azure AD) kan lagra data för så många fält som finns på inloggnings sidan, om du anger var fälten finns på sidan.

Om det inte går att skapa automatiska inloggnings fält i allmänhet, kan du prova det manuella alternativet.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Registrera inloggnings fält automatiskt för en app

Följ dessa steg om du vill konfigurera lösenordsbaserad SSO med hjälp av automatisk registrering av inloggnings fält:
1. Öppna [Azure-portalen](https://portal.azure.com/). Logga in som global administratör eller medadministratör.
2. I navigerings fönstret till vänster väljer du **alla tjänster** för att öppna tillägget Azure AD.
3. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory**.
4. Välj **företags program** i navigerings fönstret i Azure AD.
5. Välj **alla program** om du vill visa en lista över dina appar.
   > [!NOTE]
   > Om du inte ser den app du vill använda använder du **filter** kontrollen längst upp i listan **alla program** . Ange alternativet **Visa** som "alla program".
6. Välj den app som du vill konfigurera för SSO.
7. När appen har lästs in väljer du **enkel inloggning** i navigerings fönstret till vänster.
8. Välj **lösenordsbaserad inloggnings** läge.
9. Ange **inloggnings-URL**, som är webb adressen till sidan där användarna anger användar namn och lösen ord för att logga in. *Kontrol lera att inloggnings fälten är synliga på sidan för den URL som du anger*.
10. Välj **Spara**.
    Sidan kasseras automatiskt för rutorna användar namn och lösen ord. Nu kan du använda Azure AD för att på ett säkert sätt överföra lösen ord till appen med hjälp av webb läsar tillägget My Apps.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Registrera inloggnings fält manuellt för en app

Om du vill samla in inloggnings fält manuellt måste du ha webb läsar tillägget My Apps installerat. Det går inte heller att köra webbläsaren i *InPrivate*-, *Incognito*-eller *Private* -läge.

Följ dessa steg om du vill konfigurera lösenordsbaserad SSO för en app med hjälp av manuella inloggnings fält för registrering:
1. Öppna [Azure-portalen](https://portal.azure.com/). Logga in som global administratör eller medadministratör.
2. I navigerings fönstret till vänster väljer du **alla tjänster** för att öppna tillägget Azure AD.
3. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory**.
4. Välj **företags program** i navigerings fönstret i Azure AD.
5. Välj **alla program** om du vill visa en lista över dina appar.
   > [!NOTE] 
   > Om du inte ser den app du vill använda använder du **filter** kontrollen längst upp i listan **alla program** . Ange alternativet **Visa** som "alla program".
6. Välj den app som du vill konfigurera för SSO.
7. När appen har lästs in väljer du **enkel inloggning** i navigerings fönstret till vänster.
8. Välj **lösenordsbaserad inloggnings** läge.
9. Ange **inloggnings-URL**, som är sidan där användarna anger användar namn och lösen ord för att logga in. *Kontrol lera att inloggnings fälten är synliga på sidan för den URL som du anger*.
10. Välj **Konfigurera inställningar för lösen ord för enkel inloggning för *&lt; APPNAME &gt;***.
11. Välj **identifiera inloggnings fält manuellt**.
14. Välj **OK**.
15. Välj **Spara**.
16. Följ anvisningarna för att använda Mina appar.


## <a name="troubleshoot-problems"></a>Felsöka problem

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Jag får ett fel meddelande om att det inte gick att hitta några inloggnings fält på den URL: en

Du får det här fel meddelandet när det inte går att identifiera inloggnings fält automatiskt. Försök att lösa problemet genom att försöka med manuell inloggnings fält identifiering. Se [inloggnings fälten manuellt för ett program](#manually-capture-sign-in-fields-for-an-app) i den här artikeln.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Jag får fel meddelandet "det går inte att spara enkel inloggnings konfiguration"

Det går sällan att uppdatera SSO-konfigurationen. Försök att lösa det här problemet genom att spara konfigurationen igen.

Om du fortfarande får felet kan du öppna ett support ärende. Ta med den information som beskrivs i [Visa Portal meddelande information](#view-portal-notification-details) och [Skicka meddelande information till en support tekniker för att få hjälp](#send-notification-details-to-a-support-engineer-to-get-help) avsnitt i den här artikeln.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Jag kan inte identifiera inloggnings fält manuellt för min app

Du kan observera följande beteenden när manuell identifiering inte fungerar:
- Processen för manuell inhämtning verkar fungera, men de insamlade fälten är inte korrekta.
- De korrekta fälten markeras inte när insamlings processen körs.
- Hämtnings processen tar dig till appens inloggnings sida som förväntat, men ingenting händer.
- Manuell infångning verkar fungera, men SSO sker inte när användarna navigerar till appen från Mina appar.

Om du upplever något av dessa problem kan du göra följande:
- Kontrol lera att du har den senaste versionen av webb läsar tillägget My Apps *installerat och aktiverat*.
- Kontrol lera att din webbläsare inte är i *Incognito*, *InPrivate* eller *privat* läge under insamlings processen. Tillägget Mina appar stöds inte i de här lägena.
- Se till att användarna inte försöker logga in i appen från Mina appar i *Incognito*, *InPrivate* eller *privat läge*.
- Försök att utföra manuell inhämtning igen. Se till att de röda markeringarna är över rätt fält.
- Om den manuella hämtningen verkar sluta svara eller om inloggnings sidan inte svarar, kan du försöka att utföra den manuella insamlingen igen. Men den här gången har du slutfört processen genom att trycka på F12-tangenten för att öppna webbläsarens utvecklarverktyg. Välj fliken **konsol** . Skriv **window. location = "*&lt; den inloggnings-URL som du angav när du konfigurerade &gt; appen*"** och tryck sedan på RETUR. Detta innebär en omdirigering av sidan som avslutar insamlings processen och lagrar de fält som har registrerats.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Jag kan inte lägga till en annan användare i min Password-baserade SSO-app

Lösenordsbaserade SSO-appar har en gräns på 48 användare. Därför har den en gräns på 48 nycklar för användar namn/lösen ord par per app.
Om du vill lägga till ytterligare användare kan du antingen:
-   Lägg till ytterligare en instans av appen
-   Ta bort användare som inte längre använder appen först

## <a name="request-support"></a>Support för begäran 
Om du får ett fel meddelande när du konfigurerar SSO och tilldelar användare, öppnar du ett support ärende. Inkludera så mycket som möjligt av följande information:

-   Korrelations fel-ID
-   UPN (användarens e-postadress)
-   TenantID
-   Typ av webbläsare
-   Tidszon och tid/tidsram när felet uppstod
-   Fiddler-spår

### <a name="view-portal-notification-details"></a>Visa information om Portal meddelanden

Följ dessa steg om du vill se information om eventuella Portal meddelanden:
1. Välj **meddelande** ikonen (klock ikonen) i det övre högra hörnet av Azure Portal.
2. Välj ett meddelande som visar ett *fel* tillstånd. (De har ett rött "!".)
   > [!NOTE]
   > Det går inte att välja meddelanden som har statusen *lyckades* eller *pågår* .
3. Fönstret **meddelande information** öppnas. Läs informationen om du vill veta mer om problemet.
5. Om du fortfarande behöver hjälp kan du dela informationen med en support tekniker eller produkt gruppen. Välj **kopierings** ikonen till höger om rutan **Kopiera fel** för att kopiera meddelande informationen till resursen.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Skicka meddelande information till en support tekniker för att få hjälp

Det är viktigt att du delar *all* information som anges i det här avsnittet med support så att de snabbt kan hjälpa dig. Om du vill spela in det kan du ta en skärm bild eller välja **Kopiera fel**.

Följande information förklarar vad varje meddelande objekt innebär och innehåller exempel.

#### <a name="essential-notification-items"></a>Viktiga meddelande objekt

- **Title**: meddelandets beskrivande rubrik.

   Exempel: *Inställningar för programproxy*

- **Beskrivning**: vad som har uppstått till följd av åtgärden.

   Exempel: *en intern URL som angetts används redan av ett annat program.*

- **Meddelande-ID**: det unika ID: t för meddelandet.

    Exempel: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID för klientbegäran**: det angivna ID: t för begäran som webbläsaren har gjort.

    Exempel: *302fd775-3329-4670-a9f3-bea37004f0bc*

- Tidstämpel **UTC: tidsstämpeln** för när meddelandet inträffade, i UTC.

    Exempel: *2017-03-23T19:50:43.7583681 z*

- **Internt transaktions-ID**: det interna ID: t som används för att leta upp felet i våra system.

    Exempel: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: den användare som körde åtgärden.

    Exempel: *tperkins \@ f128.info*

- **Klient-ID**: det unika ID: t för den klient som den användare som körde åtgärden är medlem i.

    Exempel: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Användar objekt-ID**: det unika ID: t för den användare som körde åtgärden.

    Exempel: *17f84be4-51f8-483a-B533-383791227a99*

#### <a name="detailed-notification-items"></a>Detaljerade meddelande objekt

- **Visnings namn**: (kan vara tomt) ett mer detaljerat visnings namn för felet.

    Exempel: *Inställningar för programproxy*

- **Status**: meddelandets aktuella status.

    Exempel: *misslyckades*

- **Objekt-ID**: (kan vara tomt) det objekt-ID som åtgärden kördes mot.

   Exempel: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Information**: den detaljerade beskrivningen av vad som har uppstått till följd av åtgärden.

    Exempel: *den interna URL: en <https://bing.com/> är ogiltig eftersom den redan används.*

- **Kopierings fel**: gör att du kan välja **kopierings ikonen** till höger om text rutan **Kopiera fel** för att kopiera meddelande informationen så att du får hjälp med supporten.

    Exempel   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Nästa steg
* [Snabb starts serie för program hantering](view-applications-portal.md)
* [Planera distribution av mina appar](my-apps-deployment-plan.md)
