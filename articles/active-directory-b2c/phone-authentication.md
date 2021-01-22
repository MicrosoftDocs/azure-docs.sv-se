---
title: Telefonin loggning och inloggning med anpassade principer
titleSuffix: Azure AD B2C
description: Skicka eng ång slö sen ord (eng ång slö sen ord) i textmeddelanden till dina program användares telefoner med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2600ea3488c643bcf215b058425de42cd439dcff
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660275"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Konfigurera telefonin loggning och inloggning med anpassade principer i Azure AD B2C

Med telefonin loggning och inloggning i Azure Active Directory B2C (Azure AD B2C) kan användarna registrera sig och logga in på dina program genom att använda eng ång slö sen ord (eng ång slö sen ord) som skickas i ett textmeddelande till sin telefon. Eng ång slö sen ord kan minimera risken för att användarna forgetting eller har sina lösen ord komprometterade.

Följ stegen i den här artikeln för att använda anpassade principer för att låta dina kunder registrera sig och logga in på dina program genom att använda ett eng ång slö sen ord som skickas till telefonen.

## <a name="pricing"></a>Prissättning

Eng ång slö sen ord skickas till användarna med SMS-textmeddelanden, och du kan debiteras för varje meddelande som skickas. Information om priser finns i avsnittet **separata avgifter** i [Azure Active Directory B2C prissättning](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>Användar upplevelse för telefon registrering och inloggning

Med telefonin loggning och inloggning kan användaren registrera sig för appen med hjälp av ett telefonnummer som primär identifierare. Användarens upplevelse under registreringen och inloggningen beskrivs nedan.

> [!NOTE]
> Vi föreslår starkt att du inkluderar medgivande information i din registrering och inloggnings upplevelse som liknar exempel texten nedan. Den här exempel texten är endast i informations syfte. I den korta koden för övervakning av program vara på [webbplatsen för CTIA](https://www.ctia.org/programs) kan du läsa mer om din slutgiltiga text och funktions konfiguration för att uppfylla dina krav:
>
> *Genom att ange ditt telefonnummer, godkänner du att du får ett eng ång slö sen ord som skickas av SMS så att du kan logga in för att *&lt; infoga &gt; : ditt program namn*. Standard meddelande och data hastigheter kan tillkomma.*
>
> *&lt;Infoga: en länk till din sekretess policy&gt;*<br/>*&lt;Infoga: en länk till dina användnings villkor&gt;*

För att lägga till din egen medgivande information, anpassa följande exempel. Ta med den i `LocalizedResources` för ContentDefinition som används av den självkontrollerade sidan med visnings kontrollen ( *Phone_Email_Base.xml* -filen i [registrerings-och inloggnings start paketet][starter-pack-phone]):

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard message and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>Telefon registrerings upplevelse

Om användaren inte redan har ett konto för ditt program kan de skapa ett genom att välja länken **Registrera dig nu** . En registrerings sida visas där användaren väljer **land**, anger deras telefonnummer och väljer **Skicka kod**.

![Användaren startar telefon registrering](media/phone-authentication/phone-signup-start.png)

En verifierings kod vid enstaka tidpunkt skickas till användarens telefonnummer. Användaren anger **verifierings koden** på registrerings sidan och väljer sedan **verifiera kod**. (Om användaren inte kunde hämta koden kan han eller hon välja **Skicka ny kod**.)

![Användaren verifierar kod under telefon registreringen](media/phone-authentication/phone-signup-verify-code.png)

Användaren anger all annan information som begärs på registrerings sidan. Till exempel, **visnings namn**, **tilldelat namn** och efter **namn** (land och telefonnummer förblir ifyllda). Om användaren vill använda ett annat telefonnummer kan de välja **ändra nummer** för att starta om registreringen. När du är färdig väljer användaren **Fortsätt**.

![Användaren har ytterligare information](media/phone-authentication/phone-signup-additional-info.png)

Sedan uppmanas användaren att ange ett återställnings-e-postmeddelande. Användaren anger sin e-postadress och väljer sedan **Skicka verifierings kod**. En kod skickas till användarens inkorg för e-post, som de kan hämta och ange i rutan **verifierings kod** . Sedan väljer användaren **verifiera kod**. 

När koden har verifierats väljer användaren **skapa** för att skapa sitt konto. Eller om användaren vill använda en annan e-postadress kan han eller hon välja **ändra e-post**.

![Användaren skapar konto](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>Telefonin loggning

Om användaren har ett befintligt konto med telefonnumret som identifierare, anger användaren sitt telefonnummer och väljer **Fortsätt**. De bekräftar land och telefonnummer genom att välja **Fortsätt** och en verifierings kod för eng ång slö tiden skickas till sin telefon. Användaren anger verifierings koden och väljer **Fortsätt** för att logga in.

![Användar upplevelse för telefon inloggning](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>Ta bort ett användar konto

I vissa fall kanske du måste ta bort en användare och tillhör ande data från Azure AD B2Cs katalogen. Mer information om hur du tar bort ett användar konto via Azure Portal finns i [de här anvisningarna](/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete). 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



## <a name="prerequisites"></a>Förutsättningar

Du behöver följande resurser på plats innan du konfigurerar eng ång slö sen ord.

* [Azure AD B2C klient](tutorial-create-tenant.md)
* [Webb program](tutorial-register-applications.md) som är registrerat i din klient
* [Anpassade principer](custom-policy-get-started.md) har laddats upp till din klient

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Hämta inloggnings start paketet för telefon &

Börja med att uppdatera de anpassade principerna för registrering av telefon och inloggning för att arbeta med din Azure AD B2C-klient.

1. Hitta de [anpassade principerna för registrering och inloggning][starter-pack-phone] i din lokala kloning av start paketets lagrings platsen eller ladda ned dem direkt. XML-principfiler finns i följande katalog:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Ersätt strängen `yourtenant` med namnet på din Azure AD B2C-klient i varje fil. Om namnet på din B2C-klient till exempel är *contosob2c*, blir alla instanser av `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .

1. Slutför stegen i avsnittet [Lägg till program-ID: n i avsnittet anpassad princip](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) i [komma igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md). I det här fallet uppdaterar `/phone-number-passwordless/` **`Phone_Email_Base.xml`** du med **program-ID: n** för de två program som du registrerade när du slutförde kraven, *IdentityExperienceFramework* och *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Ladda upp principfiler

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till Azure AD B2C-klienten.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj **överför anpassad princip**.
1. Ladda upp principfiler i följande ordning:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

När du överför varje fil lägger Azure till prefixet `B2C_1A_` .

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Under **anpassade principer** väljer du **B2C_1A_SignUpOrSignInWithPhone**.
1. Under **Välj program** väljer du det *webapp1* -program som du registrerade när du slutförde kraven.
1. För **Välj svars-URL** väljer du `https://jwt.ms` .
1. Välj **Kör nu** och registrera dig med en e-postadress eller ett telefonnummer.
1. Välj **Kör nu** en gång och logga in med samma konto för att kontrol lera att du har rätt konfiguration.

## <a name="get-user-account-by-phone-number"></a>Hämta användar konto per telefonnummer

En användare som registrerar sig med ett telefonnummer, utan en återställnings-e-postadress registreras i din Azure AD B2C katalog med deras telefonnummer som inloggnings namn. Om du vill ändra telefonnumret måste supportavdelningen eller support teamet först hitta sitt konto och sedan uppdatera sitt telefonnummer.

Du kan hitta en användare med deras telefonnummer (inloggnings namn) genom att använda [Microsoft Graph](microsoft-graph-operations.md):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Till exempel:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Nästa steg

Du kan hitta registrerings-och inloggnings paket för anpassad princip för registrering och inloggning på GitHub: [Azure-samples/Active-Directory-B2C-Custom-policy-starterpack/scenarios/Phone-Number-passwordable][starter-pack-phone] princip filen för Startpaketen använder tekniska profiler för Multi-Factor Authentication och anspråks krav för telefonnummer:
* [Definiera en Azure AD Multi-Factor Authentication teknisk profil](multi-factor-auth-technical-profile.md)
* [Definiera anspråks omvandlingar för telefonnummer](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
