---
title: Inbjudan om inlösning i B2B-samarbete – Azure AD
description: Beskriver Azure AD B2B-samarbets Inbjudnings upplevelsen för slutanvändare, inklusive avtalets Sekretess villkor.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: df867059a7d4020952f71ca8d663a644ee2428fd
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199636"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Inlösen av inbjudan till Azure Active Directory B2B-samarbete

I den här artikeln beskrivs de olika sätt som gäst användare kan komma åt resurser och medgivande processen som de stöter på. Om du skickar ett e-postmeddelande med inbjudan till gästen innehåller inbjudan en länk som gästen kan lösa in för att få åtkomst till din app eller Portal. E-postinbjudan är bara ett av de sätt som gäster kan få åtkomst till dina resurser. Alternativt kan du lägga till gäster till katalogen och ge dem en direkt länk till portalen eller appen som du vill dela. Oavsett vilken metod de använder, vägleds gästerna genom en process för godkännande av första gången. Den här processen säkerställer att dina gäster samtycker till sekretess villkoren och accepterar de [användnings villkor](../conditional-access/terms-of-use.md) som du har konfigurerat.

När du lägger till en gäst användare i din katalog har gäst användar kontot en medgivande status (synlig i PowerShell) som inlednings vis är inställt på **PendingAcceptance**. Den här inställningen är kvar tills gästen accepterar din inbjudan och accepterar din sekretess policy och användnings villkor. Därefter ändras medgivande statusen till **godkänd** och medgivande sidorna visas inte längre för gästen.

   > [!IMPORTANT]
   > - Från och med den **4 januari 2021** är Google [inaktuell WebView-inloggning support](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Om du använder Google Federation eller självbetjänings registrering med Gmail bör du [testa dina verksamhetsbaserade interna program för kompatibilitet](google-federation.md#deprecation-of-webview-sign-in-support).
   > - **Från den 2021 oktober** kommer Microsoft inte längre att stödja inlösen av inbjudningar genom att skapa ohanterade Azure AD-konton och klienter för B2B-samarbets scenarier. Vi rekommenderar att kunderna väljer [autentisering med e-post med eng ång slö sen ord](one-time-passcode.md). Vi välkomnar din feedback om den här offentliga för hands versionen och är glada att skapa ännu fler sätt att samar beta.

## <a name="redemption-and-sign-in-through-a-common-endpoint"></a>Inlösen och inloggning via en gemensam slut punkt

Gäst användare kan nu logga in på dina appar för flera klienter eller från Microsoft från en gemensam slut punkt (URL) till exempel `https://myapps.microsoft.com` . Tidigare skulle en gemensam URL omdirigera en gäst användare till sin hem klient i stället för resurs klienten för autentisering, så att en innehavaradministratör krävs (till exempel `https://myapps.microsoft.com/?tenantid=<tenant id>` ). Nu kan gäst användaren gå till programmets vanliga URL, välja **inloggnings alternativ** och sedan logga in i **en organisation**. Användaren skriver sedan namnet på din organisation.

![Vanlig slut punkts inloggning](media/redemption-experience/common-endpoint-flow-small.png)

Användaren omdirigeras sedan till din klient slut punkt där de kan antingen logga in med sin e-postadress eller välja en identitetsprovider som du har konfigurerat.

## <a name="redemption-through-a-direct-link"></a>Inlösen via en direkt länk

Som ett alternativ till e-postinbjudan eller ett programs gemensamma URL kan du ge en gäst en direkt länk till din app eller Portal. Du måste först lägga till gäst användaren till din katalog via [Azure Portal](./b2b-quickstart-add-guest-users-portal.md) eller [PowerShell](./b2b-quickstart-invite-powershell.md). Sedan kan du använda något av de [anpassningsbara sätten för att distribuera program till användare](../manage-apps/end-user-experiences.md), inklusive direkt inloggnings länkar. När en gäst använder en direkt länk i stället för e-postinbjudan, kommer de fortfarande att guidas genom den första gången.

> [!NOTE]
> En direkt länk är klient-/regionsspecifika. Med andra ord innehåller den ett klient-ID eller en verifierad domän så att gästen kan autentiseras i din klient, där den delade appen finns. Här följer några exempel på direkta länkar med klient kontext:
 > - Åtkomst panel för appar: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Åtkomst panel för appar för en verifierad domän: `https://myapps.microsoft.com/<;verified domain>`
 > - Azure-portalen: `https://portal.azure.com/<tenant id>`
 > - Enskild app: se så här använder du en [direkt inloggnings länk](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Det finns vissa fall där e-postinbjudan rekommenderas över en direkt länk. Om dessa specialfall är viktiga för din organisation rekommenderar vi att du bjuder in användare genom att använda metoder som fortfarande skickar e-postinbjudan:
 - Användaren har inget Azure AD-konto, ett MSA eller ett e-postkonto i en federerad organisation. Om du inte använder funktionen för eng ång slö sen ord måste gästen lösa in inbjudan via e-post för att vägleda dig genom stegen för att skapa en MSA.
 - Ibland kanske det inbjudna användarobjektet inte har en e-postadress på grund av en konflikt med ett kontakt objekt (till exempel ett Outlook-kontakt objekt). I det här fallet måste användaren klicka på inlösnings-URL: en i e-postinbjudan.
 - Användaren kan logga in med ett alias för den e-postadress som bjudits in. (Ett alias är en ytterligare e-postadress som associeras med ett e-postkonto.) I det här fallet måste användaren klicka på inlösnings-URL: en i e-postinbjudan.

## <a name="redemption-through-the-invitation-email"></a>Inlösen via e-postinbjudan

När du lägger till en gäst användare till din katalog med [hjälp av Azure Portal](./b2b-quickstart-add-guest-users-portal.md)skickas ett e-postmeddelande med inbjudan till gästen i processen. Du kan också välja att skicka e-postinbjudningar när du [använder PowerShell](./b2b-quickstart-invite-powershell.md) för att lägga till gäst användare i din katalog. Här är en beskrivning av gästens upplevelse när de löser in länken i e-postmeddelandet.

1. Gästen får ett [e-postmeddelande om inbjudan](./invitation-email-elements.md) som skickas från **Microsofts inbjudningar**.
2. Gästen väljer **acceptera inbjudan** i e-postmeddelandet.
3. Gästen kommer att använda sina egna autentiseringsuppgifter för att logga in i din katalog. Om gästen inte har ett konto som kan sammanställas till din katalog och funktionen [eng ång slö sen](./one-time-passcode.md) ord för e-post inte är aktive rad. gästen uppmanas att skapa en personlig [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) eller ett självbetjänings [konto för Azure AD](../enterprise-users/directory-self-service-signup.md). Mer information finns i [inlösnings flödet för inbjudan](#invitation-redemption-flow) .
4. Gästen vägleds genom den [godkännande upplevelse](#consent-experience-for-the-guest) som beskrivs nedan.
## <a name="invitation-redemption-flow"></a>Inlösnings flöde för inbjudan

När en användare klickar på länken **acceptera inbjudan** i ett [e-postmeddelande](invitation-email-elements.md), löses Azure AD automatiskt om inbjudan utifrån inlösnings flödet enligt nedan:

![Skärm bild som visar flödes diagrammet för inlösen](media/redemption-experience/invitation-redemption-flow.png)

**Om användarens användar princip namn (UPN) matchar både ett befintligt Azure AD-och personal MSA-konto uppmanas användaren att välja vilket konto de vill lösa in med.*

1. Azure AD utför användarbaserad identifiering för att avgöra om användaren finns i en [befintlig Azure AD-klient](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal).

2. Om en administratör har aktiverat [direkt Federation](./direct-federation.md)kontrollerar Azure AD om användarens domänsuffix matchar domänen för en konfigurerad SAML/WS-utfodras identitets leverantör och omdirigerar användaren till den förkonfigurerade identitets leverantören.

3. Om en administratör har aktiverat [Google Federation](./google-federation.md)kontrollerar Azure AD om användarens domänsuffix är gmail.com eller googlemail.com och omdirigerar användaren till Google.

4. Inlösnings processen kontrollerar om användaren har en befintlig personlig [Microsoft-konto (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) för återställningar just-in-Time (JIT), men inte för inbjudan till inbjudan via e-post. Om användaren redan har en befintlig MSA loggar de in med sina befintliga MSA.

5. När användarens **Hem Katalog** identifieras skickas användaren till motsvarande identitets leverantör för att logga in.  

6. Om steg 1 till 4 inte kan hitta en arbets katalog för den inbjudna användaren, avgör Azure AD om den bjudande klienten har aktiverat funktionen [eng ång slö sen](./one-time-passcode.md) ord för gäster i ett e-postmeddelande.

7. Om [e-post med eng ång slö sen ord för gäster har Aktiver ATS](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)skickas ett lösen ord till användaren via det inbjudna e-postmeddelandet. Användaren kommer att hämta och ange lösen ordet på inloggnings sidan för Azure AD.

8. Om e-postlösenord med eng ång slö sen ord för gäster är inaktiverat, kontrollerar Azure AD domänsuffix för att avgöra om det tillhör ett konsument konto. I så fall uppmanas användaren att skapa en personlig [Microsoft-konto](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create). Annars uppmanas användaren att skapa ett självbetjänings [konto för Azure AD](../enterprise-users/directory-self-service-signup.md).

9. Azure AD försöker skapa ett självbetjänings [konto för Azure AD](../enterprise-users/directory-self-service-signup.md) genom att kontrol lera åtkomsten till e-postmeddelandet. Verifierar att kontot görs genom att skicka en kod till e-postmeddelandet och låta användaren hämta och skicka det till Azure AD. Men om den inbjudna användarens klient är federerad eller om fältet AllowEmailVerifiedUsers är inställt på false i den inbjudna användarens klient organisation, kan inte användaren slutföra inlösningen och flödet resulterar i ett fel. Mer information finns i [felsöka Azure Active Directory B2B-samarbete](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. Användaren uppmanas att skapa en personlig [Microsoft-konto (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

11. Efter autentiseringen till den rätta identitets leverantören omdirigeras användaren till Azure AD för att slutföra [medgivande upplevelsen](#consent-experience-for-the-guest).  

För just-in-Time (JIT)-inlösen, där inlösen sker via en klient kopplings länk, är steg 8 till 10 inte tillgängliga. Om en användare når steg 6 och funktionen email eng ång slö sen ord inte är aktive rad, får användaren ett fel meddelande och kan inte lösa in inbjudan. För att förhindra det här felet bör administratörer antingen [Aktivera eng ång slö sen ord eng ång slö sen ord](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) eller se till att användaren klickar på länken inbjudan.

## <a name="consent-experience-for-the-guest"></a>Godkännande upplevelse för gästen

När en gäst loggar in för att komma åt resurser i en partner organisation för första gången, går de igenom följande sidor. 

1. Gästen granskar sidan **gransknings behörigheter** som beskriver den bjudande organisationens sekretess policy. En användare måste **acceptera** användningen av informationen i enlighet med den bjudande organisationens sekretess policy för att kunna fortsätta.

   ![Skärm bild som visar sidan gransknings behörigheter](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Information om hur du som klient organisations administratör kan länka till din organisations sekretess policy finns i [How-to: Lägg till din organisations sekretess information i Azure Active Directory](../fundamentals/active-directory-properties-area.md).

2. Om användnings villkoren har kon figurer ATS öppnas gästen och granskar användnings villkoren och väljer sedan **acceptera**. 

   ![Skärm bild som visar nya användnings villkor](media/redemption-experience/terms-of-use-accept.png) 

   Du kan konfigurera användnings [villkoren](../conditional-access/terms-of-use.md) i **externa identiteter**  >  **användningsvillkor**.

3. Om inget annat anges omdirigeras gästen till appens åtkomst panel, som innehåller en lista över de program som gästen har åtkomst till.

   ![Skärm bild som visar åtkomst panelen för appar](media/redemption-experience/myapps.png) 

I din katalog har gästens **inbjudan godkänt** värde ändrats till **Ja**. Om en MSA skapades, visar gästens **källa** Microsoft- **konto**. Mer information om egenskaper för gäst användar konton finns i [Egenskaper för en Azure AD B2B-samarbets användare](user-properties.md). 

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Lägg till Azure Active Directory B2B-samarbets användare i Azure Portal](add-users-administrator.md)
- [Hur kan informations arbetare lägga till B2B-samarbets användare för att Azure Active Directory?](add-users-information-worker.md)
- [Lägga till Azure Active Directory B2B-samarbets användare med hjälp av PowerShell](customize-invitation-api.md#powershell)
- [Lämna en organisation som en gästanvändare](leave-the-organization.md)