---
title: Twilio verifiera appen med Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 'Lär dig hur du integrerar en exempel-Twilio i Azure AD B2C med verifierings-API: et för. Följ PSD2 (Payment Services direktiv 2) transaktions krav via dynamisk länkning och stark kundautentisering.'
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a7f22b05238fb495422a02edd6ca382b4df041a
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257814"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>Integrera Twilio verifiera appen med Azure Active Directory B2C

I den här genom gången lär du dig hur du integrerar en exempel-app för online-betalning i Azure Active Directory B2C (Azure AD B2C) med Twilio verifiera API. Genom att använda Twilio verifiera appen kan Azure AD B2C kunder följa PSD2 (Payment Services-direktiv 2) transaktions krav via dynamisk länkning och stark kundautentisering.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* [En Azure AD B2C klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.
* Ett [utvärderings konto](https://www.twilio.com/try-twilio) på Twilio.

## <a name="scenario-description"></a>Scenariobeskrivning

Följande komponenter utgör Twilio-lösningen:

- .NET [PSD2 demo-webbappen](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App), som ger möjlighet att logga in eller registrera sig och utföra en stor risk transaktion.
- Azure AD B2C kombinerad [inloggnings-och registrerings princip](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy).
- Azure AD B2C princip integrerad med Twilio verifiera API med hjälp av `id_token_hint` .
- .NET-webbapp som är värd för en `.well-known` OpenIdConnect-slutpunkt för att tillåta validering av en `id_token_hint` .


    ![Twilio-flöde](media/partner-twilio/twilio-flow.png)

| Steg | Beskrivning |
|------|------|
| 1     | Användaren initierar inloggning eller registrera dig till PSD2 demo-appen. Användaren autentiseras via Azure AD B2C kombinerad inloggnings-och registrerings princip. En token returneras till programmet. Vid registreringen verifieras användarens telefonnummer via SMS/telefon och registreras på deras Azure AD B2C-konto.     |
| 2     | Användaren initierar en transaktion med hög risk, till exempel överföring av $50,00. Användarens aktuella åtkomsttoken utvärderas för PolicyId för att avgöra om användaren redan har autentiserats via en Step-Up anpassad princip.     |
| 3     | Programmet registrerar transaktion svärdet och betalnings mottagaren, $50,00 och John berg och genererar en signerad token. Denna token kallas en `id_token_hint` och innehåller anspråket `amount:$500, payee:john doe` . `id_token_hint`Skickas tillsammans med begäran till den anpassade principen Azure AD B2C, som är integrerad med Twilio.     |
| 4     | Azure AD B2C verifierar signaturen för id_token_hint genom att kontrol lera program `/.well-known` OpenID Connect-slutpunkten. Efter verifieringen extraheras anspråken från denna token, särskilt `amount` och `payee` . Användaren ser en sida för att verifiera mobiltelefon numret via SMS-meddelandet.     |
| 5     | Användaren begär att verifiera sina telefonnummer via SMS-meddelandet och Azure AD B2C gör en REST API-begäran till Twilio för att verifiera API-slutpunkten. Den skickar även transaktionen `amount` och `payee` som en del av PSD2-processen för att generera eng ång slö sen ordet (eng ång slö sen ord). Twilio skickar ett SMS-meddelande till användarens registrerade telefonnummer.     |
| 6     |  Användaren anger den eng ång slö sen ord som tagits emot i SMS-meddelandet och skickar den till Azure AD B2C. Azure AD B2C gör en API-begäran med denna eng ång slö sen ord till Twilio för att kontrol lera att eng ång slö sen ord är korrekt. Slutligen utfärdas en token till programmet, med en ny PolicyId som indikerar att användaren har mellanliggande sin autentisering.    |
|      |      |

## <a name="onboard-with-twilio"></a>Publicera med Twilio

1. Skaffa ett [utvärderings konto](https://www.twilio.com/try-twilio) på Twilio.

2. Köp ett telefonnummer på Twilio enligt beskrivningen i [den här artikeln](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console)

3. Gå till [Verifiera API](https://www.twilio.com/console/verify/services) i Twilio-konsolen och följ [instruktionerna](https://www.twilio.com/docs/verify/verifying-transactions-psd2) för att skapa en tjänst och aktivera alternativet PSD2.  

## <a name="configure-the-psd2-demo-app"></a>Konfigurera PSD2 demo-appen

1. Öppna B2C-WebAPI-DotNet-lösningen och Ersätt följande värden med dina egna klient specifika värden i web.config:

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. [Webbappen](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App) är också värd för ID-token-generatorn och metadata-slutpunkten.
   - Skapa ditt signerings certifikat enligt beskrivningen i den här [exempel beskrivningen](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate).
   - Uppdatera följande rader baserat på ditt certifikat i web.config:
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. Ladda upp demonstrations programmet till den värd leverantör som du önskar. Vägledning för Azure App Service finns i [den här exempel beskrivningen](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service), inklusive instruktioner för att ladda upp certifikatet.

4. Uppdatera din Azure AD B2C program registrering genom att lägga till en svars-URL som motsvarar URL: en där programmet finns.

5. Öppna [principfiler](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy) och Ersätt alla instanser av  `contoso` med ditt klient namn.

6. Hitta Twilio-REST API teknisk profil **anpassad-SMS-registrera**. Uppdatera  `ServiceURL`   med din Twilio-AccountSID och från numret till ditt köpta telefonnummer.

7. Hitta Twilio-REST API tekniska profiler, **TwilioRestAPI-verify-steg 1**   och **TwilioRestAPI-verify-Step2** och uppdatera  `ServiceURL`   med Twilio AccountSID.

## <a name="integrate-with-azure-ad-b2c"></a>Integrera med Azure AD B2C

Lägg till principfiler till Azure AD B2C:

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.

2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.

4. Navigera till   >  princip nycklar för Azure AD B2C **Identity Experience Framework**  >  .

5. Lägg till en ny nyckel med namnet **B2cRestTwilioClientId**. Välj **manuell** och ange värdet för Twilio-AccountSID.

6. Lägg till en ny nyckel med namnet **B2cRestTwilioClientSecret**. Välj **manuell** och ange värdet för Twilio auth-token.

7. Ladda upp alla principfiler till din klient organisation.

8. Anpassa strängen i GenerateOTPMessageEnrol Claims-transformering för din registrering SMS-text.

## <a name="test-the-solution"></a>Testa lösningen

* Bläddra till ditt program och testa åtgärder för att logga in, registrera och skicka pengar.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- Se GitHub för [Twilio integrations kod exempel](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2)  

- [Anpassade principer i AAD B2C](custom-policy-overview.md)

- [Kom igång med anpassade principer i AAD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
