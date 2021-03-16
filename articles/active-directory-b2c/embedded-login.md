---
title: Bädda in Azure Active Directory B2C användar gränssnitt i din app med en anpassad princip
titleSuffix: Azure AD B2C
description: Lär dig hur du bäddar in Azure Active Directory B2C användar gränssnitt i din app med en anpassad princip
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 526d8bfa0124fba75eb33518acd83169ff020698
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555436"
---
# <a name="embedded-sign-in-experience"></a>Inbäddad inloggnings upplevelse

För en enklare inloggnings upplevelse kan du undvika att omdirigera användare till en separat inloggnings sida eller skapa ett popup-fönster. Genom att använda det infogade ram `<iframe>` -elementet kan du bädda in användar gränssnittet för Azure AD B2C inloggning direkt i ditt webb program.

## <a name="web-application-embedded-sign-in"></a>Inbäddad inloggning för webb program

Det infogade ram `<iframe>` -elementet används för att bädda in ett dokument på en HTML5-webb sida. Du kan använda IFRAME-elementet för att bädda in Azure AD B2C inloggnings användar gränssnitt direkt i ditt webb program, som visas i följande exempel:

![Logga in med hovring för DIV-upplevelse](media/embedded-login/login-hovering.png)

Tänk på följande när du använder iframe:

- Inbäddad inloggning stöder endast lokala konton. De flesta sociala identitets leverantörer (till exempel Google och Facebook) blockerar sina inloggnings sidor från att återges i infogade ramar.
- Eftersom Azure AD B2C sessionscookies i en iframe betraktas som cookies från tredje part, kan vissa webbläsare (till exempel Safari eller Chrome i Incognito-läge) antingen blockera eller rensa dessa cookies, vilket resulterar i en oönskad användar upplevelse. Undvik det här problemet genom att kontrol lera att ditt program domän namn och din Azure AD B2C domän har *samma ursprung*. Till exempel har ett program som finns på https://app.contoso.com samma ursprung som Azure AD B2C körs på https://login.contoso.com .
 
## <a name="configure-your-policy"></a>Konfigurera principen

Om du vill tillåta att ditt Azure AD B2C användar gränssnitt bäddas in i en iframe måste du ta med en innehålls säkerhets princip `Content-Security-Policy` och ram alternativ `X-Frame-Options` i Azure AD B2C HTTP-svarshuvuden. Med dessa huvuden kan Azure AD B2C användar gränssnitt köras under ditt program domän namn.

Lägg till ett **JourneyFraming** -element inuti [RelyingParty](relyingparty.md) -elementet.  **UserJourneyBehaviors** -elementet måste följa **DefaultUserJourney**. Ditt **UserJourneyBehaviors** -element bör se ut som i det här exemplet:

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

Attributet **sources** innehåller URI för ditt webb program. Lägg till ett blank steg mellan URI: er. Varje URI måste uppfylla följande krav:

- URI: n måste vara betrodd och ägd av ditt program.
- URI: n måste använda https-schemat.  
- Du måste ange en fullständig URI för webb programmet. Jokertecken stöds inte.

Dessutom rekommenderar vi att du även blockerar ditt eget domän namn från att bäddas in i en iframe genom att ange rubrikerna Content-Security-Policy och X-Frame-Options på dina program sidor. Detta minimerar säkerhets problem kring äldre webbläsare som är relaterade till kapslade inbäddade iframes.

## <a name="adjust-policy-user-interface"></a>Ändra princip användar gränssnitt

Med Azure AD B2C [anpassning av användar gränssnitt](customize-ui.md)har du nästan fullständig kontroll över HTML-och CSS-innehåll som presenteras för användarna. Följ anvisningarna för att anpassa en HTML-sida med hjälp av innehålls definitioner. Om du vill anpassa Azure AD B2C användar gränssnittet till iframe-storleken, kan du ange en ren HTML-sida utan bakgrund och extra blank steg.  

Följande CSS-kod döljer Azure AD B2C HTML-element och justerar storleken på panelen så att den fyller iframe.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

I vissa fall kanske du vill meddela till ditt program som Azure AD B2C sidan visas för tillfället. När en användare exempelvis väljer alternativet registrera kanske du vill att programmet ska svara genom att dölja länkarna för att logga in med ett socialt konto eller justera iframe-storleken.

Om du vill meddela ditt program om sidan nuvarande Azure AD B2C [aktiverar du principen för Java Script](javascript-samples.md)och använder sedan HTML5 post meddelanden. Följande JavaScript-kod skickar ett post-meddelande till appen med `signUp` :

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>Konfigurera ett webb program

När en användare väljer inloggnings knappen genererar [webbappen](code-samples.md#web-apps-and-apis) en auktoriseringsbegäran som gör att användaren kan Azure AD B2C inloggnings upplevelsen. När inloggningen är klar returnerar Azure AD B2C en ID-token eller en auktoriseringskod till den konfigurerade omdirigerings-URI: n i ditt program.

För att stöda inbäddad inloggning pekar iframe **src** -egenskapen på inloggnings styrenheten, till exempel `/account/SignUpSignIn` , som genererar auktoriseringsbegäran och omdirigerar användaren till att Azure AD B2C princip.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

När ID-token tas emot och verifieras av programmet slutförs auktoriseringsarkivet och programmet känner igen och litar på användaren. Eftersom auktoriseringsarkivet sker inuti iframe måste du läsa in huvud sidan igen. När sidan har lästs in ändras inloggnings knappen till "Logga ut" och användar namnet visas i användar gränssnittet.  

Följande är ett exempel som visar hur den omdirigerings-URI: n kan uppdatera huvud sidan:

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>Lägg till inloggning med sociala konton till en webbapp

Leverantörer av sociala identitet blockerar sina inloggnings sidor från att återge i infogade ramar. Du kan använda en separat princip för sociala konton, eller så kan du använda en enda princip för både inloggning och registrering med lokala och sociala konton. Sedan kan du använda frågesträngparametern `domain_hint` . Parametern domän tips tar användaren direkt till den sociala identitets leverantörens inloggnings sida.

Lägg till inloggnings knapparna med sociala konton i ditt program. När en användare klickar på ett av de sociala konto knapparna måste kontrollen Ändra princip namnet eller ange parametern domän tips.

<!-- TBD: add a diagram -->

Omdirigerings-URI: n kan vara samma omdirigerings-URI som används av iframe. Du kan hoppa över att ladda om sidan.

## <a name="configure-a-single-page-application"></a>Konfigurera ett program med en enda sida

För ett program med en enda sida måste du också ha en andra "inloggnings" HTML-sida som läses in i iframe. Den här inloggnings sidan är värd för den verifierings biblioteks kod som genererar auktoriseringskod och returnerar token.

När ett program med en enskild sida behöver åtkomsttoken använder du JavaScript-kod för att hämta åtkomsttoken från den iframe och det objekt som innehåller den.

> [!NOTE]
> Det finns för närvarande inte stöd för att köra MSAL 2,0 i en iframe.

Följande kod är ett exempel som körs på huvud sidan och anropar en iframes JavaScript-kod:

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>Nästa steg

Se följande relaterade artiklar:

- [Anpassning av användargränssnittet](customize-ui.md)
- [RelyingParty](relyingparty.md) element referens
- [Aktivera din princip för Java Script](javascript-samples.md)
- [Kodexempel](code-samples.md)
