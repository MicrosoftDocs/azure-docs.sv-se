---
title: Självstudie – Skapa användar flöden och anpassade principer – Azure Active Directory B2C
description: Följ den här självstudien för att lära dig hur du skapar användar flöden och anpassade principer i Azure Portal för att aktivera registrering, inloggning och användar profil redigering för dina program i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e41c1e74dbe428ee38d4480a1587050b7f96a55f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226234"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Självstudie: skapa användar flöden i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

I dina program kan du ha användar flöden som gör det möjligt för användare att registrera, logga in eller hantera sina profiler. Du kan skapa flera användar flöden av olika typer i din Azure Active Directory B2C-klient (Azure AD B2C) och använda dem i dina program efter behov. Användar flöden kan återanvändas i olika program.

::: zone pivot="b2c-user-flow"
Med ett användar flöde kan du bestämma hur användare interagerar med ditt program när de gör saker som att logga in, registrera sig, redigera en profil eller återställa ett lösen ord. I den här artikeln kan du se hur du:
::: zone-end

::: zone pivot="b2c-custom-policy"
[Anpassade principer](custom-policy-overview.md) är konfigurationsfiler som definierar beteendet för din Azure Active Directory B2C (Azure AD B2C)-klient. I den här artikeln kan du se hur du:
::: zone-end

> [!div class="checklist"]
> * Skapa ett användar flöde för registrering och inloggning
> * Aktivera lösenordsåterställning via självbetjäning
> * Skapa ett användarflöde för profilredigering

::: zone pivot="b2c-user-flow"
> [!IMPORTANT]
> Vi har ändrat hur vi refererar till användarflödesversioner. Tidigare erbjöd vi V1-versioner (produktionsklara), V1.1 och V2 (förhandsversion). Nu har vi konsoliderat användar flöden till **Rekommenderad** (nästa generations för hands version) och **standard** versioner (allmänt tillgängliga). Alla tidigare förhands gransknings användar flöden för V 1.1 och v2 finns på en sökväg till utfasningen den **1 augusti 2021**. Mer information finns [i användar flödes versioner i Azure AD B2C](user-flow-versions.md).
::: zone-end

## <a name="prerequisites"></a>Förutsättningar

::: zone pivot="b2c-user-flow"
- Om du inte redan har en, [skapar du en Azure AD B2C klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.
- [Registrera ditt program](tutorial-register-applications.md) i klient organisationen som du skapade så att den kan kommunicera med Azure AD B2C.
::: zone-end

::: zone pivot="b2c-custom-policy"
- Om du inte redan har en, [skapar du en Azure AD B2C klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.
- [Registrera ditt program](tutorial-register-applications.md) i klient organisationen som du skapade så att den kan kommunicera med Azure AD B2C.
- Slutför stegen i Konfigurera [registrering och inloggning med ett Facebook-konto](identity-provider-facebook.md) för att konfigurera ett Facebook-program. Även om ett Facebook-program inte krävs för att använda anpassade principer, används det i den här genom gången för att demonstrera aktivering av sociala inloggningar i en anpassad princip.
::: zone-end

::: zone pivot="b2c-user-flow"
## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Skapa ett användar flöde för registrering och inloggning

Användar flödet för registrering och inloggning hanterar både registrering och inloggnings upplevelser med en enda konfiguration. Användare av ditt program är i rätt sökväg beroende på kontexten.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

    ![B2C-klient, katalog och prenumerations fönstret Azure Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **principer** väljer du **användar flöden** och väljer sedan **nytt användar flöde**.

    ![Sidan användar flöden i portalen med knappen nytt användar flöde markerat](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. På sidan **skapa ett användar flöde** väljer du användar flödet **Registrera och logga** in.

    ![Välj sidan för användar flöde med registrering och inloggnings flöde markerat](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. Under **Välj en version** väljer du **rekommenderas** och väljer sedan **skapa**. ([Läs mer](user-flow-versions.md) om användar flödes versioner.)

    ![Sidan skapa användar flöde i Azure Portal med egenskaper markerat](./media/tutorial-create-user-flows/select-version.png)

1. Ange ett **Namn** för användarflödet. Till exempel *signupsignin1*.
1. För **identitets leverantörer** väljer du **e-postregistrering**.
1. För användarattribut **och anspråk** väljer du de anspråk och attribut som du vill samla in och skicka från användaren under registreringen. Välj till exempel **Visa mer** och välj sedan attribut och anspråk för **land/region**, **visnings namn** och **post nummer**. Klicka på **OK**.

    ![Sidan för attribut och val av anspråk med tre anspråk valda](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Klicka på **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* anpassningsprefix automatiskt till namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde** och välj sedan **Registrera dig nu**.

    ![Sidan kör användar flöde i portalen med knappen Kör användar flöde markerat](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Ange en giltig e-postadress, klicka på **Skicka verifierings kod**, ange den verifierings kod som du får och välj sedan **verifiera kod**.
1. Ange ett nytt lösen ord och bekräfta lösen ordet.
1. Välj land och region, ange det namn som du vill visa, ange ett post nummer och klicka sedan på **skapa**. Token returneras till `https://jwt.ms` och ska visas för dig.
1. Nu kan du köra användar flödet igen och du bör kunna logga in med det konto som du skapade. Den returnerade token innehåller de anspråk som du har valt land/region, namn och post nummer.

> [!NOTE]
> Upplevelsen "kör användar flöde" är för närvarande inte kompatibel med URL-typen SPA-svar med hjälp av Authorization Code Flow. Om du vill använda upplevelsen "kör användar flöde" med dessa typer av appar registrerar du en svars-URL av typen "Web" och aktiverar det implicita flödet enligt beskrivningen [här](tutorial-register-spa.md).

## <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Aktivera [självbetjäning för återställning av lösen ord](add-password-reset-policy.md) för registrerings-eller inloggnings användar flödet:

1. Välj det användar flöde för registrering eller inloggning som du skapade.
1. Under **Inställningar** i den vänstra menyn väljer du **Egenskaper**.
1. Under **lösen ords komplexitet** väljer du **självbetjäning för återställning av lösen ord**.
1. Välj **Spara**.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj **Kör användar flöde**.
1. På sidan registrering eller inloggning väljer du **glömt lösen ordet?**.
1. Kontrol lera e-postadressen för det konto som du skapade tidigare och välj sedan **Fortsätt**.
1. Nu har du möjlighet att ändra lösen ordet för användaren. Ändra lösen ordet och välj **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

## <a name="create-a-profile-editing-user-flow"></a>Skapa ett användarflöde för profilredigering

Om du vill att användarna ska kunna redigera sin profil i ditt program använder du en profil som redigerar användar flöde.

1. På menyn på sidan för Azure AD B2C klient översikt väljer du **användar flöden** och väljer sedan **nytt användar flöde**.
1. På sidan **skapa ett användar flöde** väljer du **profil redigering** användar flöde. 
1. Under **Välj en version** väljer du **rekommenderas** och väljer sedan **skapa**.
1. Ange ett **Namn** för användarflödet. Till exempel *profileediting1*.
1. För **identitets leverantörer** väljer du **lokal konto inloggning**.
2. För **användarattribut** väljer du de attribut som du vill att kunden ska kunna redigera i profilen. Välj till exempel **Visa mer** och välj sedan både attribut och anspråk för **visnings namn** och **befattning**. Klicka på **OK**.
3. Klicka på **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* läggs automatiskt till i namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde** och logga sedan in med det konto som du skapade tidigare.
1. Nu har du möjlighet att ändra visnings namn och befattning för användaren. Klicka på **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.
::: zone-end

::: zone pivot="b2c-custom-policy"
> [!TIP]
> Den här artikeln förklarar hur du konfigurerar din klient organisation manuellt. Du kan automatisera hela processen från den här artikeln. Automatiserad distribution av Azure AD B2C [SocialAndLocalAccountsWithMFA startpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack), som ger registrering och inloggning, lösen ords återställning och profil redigerings körningar. Om du vill automatisera genom gången nedan går du till installations programmet för [IEF](https://aka.ms/iefsetup) och följer anvisningarna.


## <a name="add-signing-and-encryption-keys"></a>Lägg till signerings-och krypterings nycklar

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. På sidan Översikt, under **principer**, väljer du **Identity Experience Framework**.

### <a name="create-the-signing-key"></a>Skapa signerings nyckeln

1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du `Generate` .
1. I **namn** anger du `TokenSigningKeyContainer` . Prefixet `B2C_1A_` kan läggas till automatiskt.
1. För **nyckel typ** väljer du **RSA**.
1. För **nyckel användning** väljer du **signatur**.
1. Välj **Skapa**.

### <a name="create-the-encryption-key"></a>Skapa krypterings nyckeln

1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du `Generate` .
1. I **namn** anger du `TokenEncryptionKeyContainer` . Prefixet `B2C_1A` _ kan läggas till automatiskt.
1. För **nyckel typ** väljer du **RSA**.
1. För **nyckel användning** väljer du **kryptering**.
1. Välj **Skapa**.

### <a name="create-the-facebook-key"></a>Skapa Facebook-nyckeln

Lägg till ditt Facebook-programs [app-hemlighet](identity-provider-facebook.md) som en princip nyckel. Du kan använda appens hemlighet för det program som du skapade som en del av den här artikelns krav.

1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du `Manual` .
1. Som **namn** anger du `FacebookSecret` . Prefixet `B2C_1A_` kan läggas till automatiskt.
1. I **hemlighet** anger du din Facebook- *appens program hemlighet* från Developers.Facebook.com. Det här värdet är hemligheten, inte program-ID: t.
1. För **nyckel användning** väljer du **signatur**.
1. Välj **Skapa**.

## <a name="register-identity-experience-framework-applications"></a>Registrera program för identitets upplevelse Framework

Azure AD B2C kräver att du registrerar två program som används för att registrera dig och logga in användare med lokala konton: *IdentityExperienceFramework*, ett webb-API och *ProxyIdentityExperienceFramework*, en inbyggd app med delegerad behörighet till IdentityExperienceFramework-appen. Användarna kan registrera sig med en e-postadress eller ett användar namn och ett lösen ord för att få åtkomst till dina klient registrerade program, vilket skapar ett "lokalt konto". Lokala konton finns bara i Azure AD B2C-klienten.

Du behöver bara registrera dessa två program i Azure AD B2C klient organisationen en gång.

### <a name="register-the-identityexperienceframework-application"></a>Registrera IdentityExperienceFramework-programmet

Om du vill registrera ett program i din Azure AD B2C klient kan du använda **Appregistreringar** upplevelsen.

1. Välj **Appregistreringar** och välj sedan **ny registrering**.
1. Som **namn** anger du `IdentityExperienceFramework` .
1. Under **konto typer som stöds** väljer du **konton endast i den här organisations katalogen**.
1. Under **omdirigerings-URI** väljer du **webb** och anger sedan `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` , där `your-tenant-name` är ditt Azure AD B2C klient domän namn.
1. Under **behörigheter** markerar du kryss rutan *bevilja administratörs medgivande till OpenID och offline_access behörighet* .
1. Välj **Register** (Registrera).
1. Registrera **program-ID: t (Client)** för användning i ett senare steg.

Sedan exponerar du API: et genom att lägga till ett omfång:

1. I den vänstra menyn, under **Hantera**, väljer du **exponera ett API**.
1. Välj **Lägg till ett omfång** och välj sedan **Spara och fortsätt** att acceptera standard-ID: t för program-ID.
1. Ange följande värden för att skapa en omfattning som tillåter anpassad princip körning i Azure AD B2C klient organisation:
    * **Omfattnings namn**: `user_impersonation`
    * **Visnings namn för administratörs medgivande**: `Access IdentityExperienceFramework`
    * **Beskrivning av administratörs medgivande**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Välj **Lägg till omfattning**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrera ProxyIdentityExperienceFramework-programmet

1. Välj **Appregistreringar** och välj sedan **ny registrering**.
1. Som **namn** anger du `ProxyIdentityExperienceFramework` .
1. Under **konto typer som stöds** väljer du **konton endast i den här organisations katalogen**.
1. Under **omdirigerings-URI** använder du List rutan för att välja **offentlig klient/ursprunglig (mobil & Desktop)**.
1. För **omdirigerings-URI** anger du `myapp://auth` .
1. Under **behörigheter** markerar du kryss rutan *bevilja administratörs medgivande till OpenID och offline_access behörighet* .
1. Välj **Register** (Registrera).
1. Registrera **program-ID: t (Client)** för användning i ett senare steg.

Ange sedan att programmet ska behandlas som en offentlig klient:

1. På den vänstra menyn, under **Hantera**, väljer du **autentisering**.
1. Under **Avancerade inställningar** i avsnittet **Tillåt offentliga klient flöden** anger du **Aktivera följande mobila och skriv bords flöden** till **Ja**. Se till att **"allowPublicClient": true** har angetts i applikations manifestet. 
1. Välj **Spara**.

Bevilja nu behörighet till API-omfånget som du visade tidigare i *IdentityExperienceFramework* -registreringen:

1. I den vänstra menyn, under **Hantera**, väljer du **API-behörigheter**.
1. Under **konfigurerade behörigheter** väljer du **Lägg till en behörighet**.
1. Välj fliken **Mina API: er** och välj sedan **IdentityExperienceFramework** -programmet.
1. Under **behörighet** väljer du den **user_impersonation** definition som du definierade tidigare.
1. Välj **Lägg till behörigheter**. Vänta några minuter innan du fortsätter till nästa steg.
1. Välj **bevilja administrativt godkännande för (ditt klient namn)**.
1. Välj ditt inloggade administratörs konto eller logga in med ett konto i Azure AD B2C-klienten som har tilldelats minst administratörs rollen för *moln program* .
1. Välj **Acceptera**.
1. Välj **Uppdatera** och verifiera sedan att "beviljat..." visas under **status** för omfattningarna-offline_access, openid och user_impersonation. Det kan ta några minuter innan behörigheterna har spridits.

* * *

## <a name="custom-policy-starter-pack"></a>Start paket för anpassad princip

Anpassade principer är en uppsättning XML-filer som du överför till din Azure AD B2C-klient för att definiera tekniska profiler och användar resor. Vi tillhandahåller start paket med flera fördefinierade principer för att komma igång snabbt. Vart och ett av dessa start paket innehåller det minsta antalet tekniska profiler och användar resor som krävs för att uppnå de scenarier som beskrivs:

- **LocalAccounts** – tillåter endast lokala konton.
- **SocialAccounts** – tillåter endast användning av sociala (eller federerade) konton.
- **SocialAndLocalAccounts** – möjliggör användning av både lokala och sociala konton.
- **SocialAndLocalAccountsWithMFA** – aktiverar alternativ för sociala, lokala och Multi-Factor Authentication.

Varje start paket innehåller:

- **Grundläggande fil** – några ändringar krävs för basen. Exempel: *TrustFrameworkBase.xml*
- **Tilläggs fil** – den här filen är den plats där de flesta konfigurations ändringar görs. Exempel: *TrustFrameworkExtensions.xml*
- **Förlitande part-filer** – verksamhetsspecifika filer som anropas av ditt program. Exempel: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

I den här artikeln redigerar du anpassade XML-principfiler i **SocialAndLocalAccounts** -startpaketet. Om du behöver en XML-redigerare kan du prova [Visual Studio Code](https://code.visualstudio.com/download), en förenklad plattforms oberoende redigerare.

### <a name="get-the-starter-pack"></a>Hämta start paketet

Hämta start paketen för anpassad princip från GitHub och uppdatera sedan XML-filerna i SocialAndLocalAccounts-startpaketet med ditt Azure AD B2C klient namn.

1. [Hämta zip-filen](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) eller klona databasen:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. I alla filer i katalogen **SocialAndLocalAccounts** ersätter du strängen `yourtenant` med namnet på din Azure AD B2C-klient.

    Om namnet på din B2C-klient till exempel är *contosotenant*, blir alla instanser av `yourtenant.onmicrosoft.com` `contosotenant.onmicrosoft.com` .

### <a name="add-application-ids-to-the-custom-policy"></a>Lägga till program-ID: n i den anpassade principen

Lägg till program-ID: n i fil namns filen *TrustFrameworkExtensions.xml*.

1. Öppna `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** och hitta elementet `<TechnicalProfile Id="login-NonInteractive">` .
1. Ersätt båda instanserna av `IdentityExperienceFrameworkAppId` med program-ID: t för det IdentityExperienceFramework-program som du skapade tidigare.
1. Ersätt båda instanserna av `ProxyIdentityExperienceFrameworkAppId` med program-ID: t för det ProxyIdentityExperienceFramework-program som du skapade tidigare.
1. Spara filen.

## <a name="upload-the-policies"></a>Överför principerna

1. Välj meny alternativet för **identitets miljö** i B2C-klienten i Azure Portal.
1. Välj **överför anpassad princip**.
1. I den här ordningen laddar du upp principfiler:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

När du överför filerna lägger Azure till prefixet `B2C_1A_` i varje.

> [!TIP]
> Om XML-redigeraren stöder verifiering, verifierar du filerna mot det `TrustFrameworkPolicy_0.3.0.0.xsd` XML-schema som finns i rot katalogen i Start paketet. Verifiering av XML-schema identifierar fel innan den laddas upp.

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Under **anpassade principer** väljer du **B2C_1A_signup_signin**.
1. För **Välj program** på sidan Översikt i den anpassade principen väljer du det webb program som heter *webapp1* som du tidigare har registrerat.
1. Se till att **svars-URL: en** är `https://jwt.ms` .
1. Välj **Kör nu**.
1. Registrera dig med en e-postadress.
1. Välj **Kör nu** igen.
1. Logga in med samma konto för att bekräfta att du har rätt konfiguration.

## <a name="add-facebook-as-an-identity-provider"></a>Lägg till Facebook som en identitets leverantör

Som nämnts i [förutsättningar](#prerequisites)krävs *inte* Facebook för att använda anpassade principer, men används här för att demonstrera hur du kan aktivera federerad social inloggning i en anpassad princip.

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** Ersätt värdet för `client_id` med Facebook-programmets ID i filen:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Ladda upp *TrustFrameworkExtensions.xml* -filen till din klient organisation.
1. Under **anpassade principer** väljer du **B2C_1A_signup_signin**.
1. Välj **Kör nu** och välj Facebook för att logga in med Facebook och testa den anpassade principen.

::: zone-end
## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa ett användar flöde för registrering och inloggning
> * Skapa ett användarflöde för profilredigering
> * Skapa ett användarflöde för återställning av lösenord

Nu ska du lära dig hur du använder Azure AD B2C för att logga in och registrera användare i ett program. Följ ASP.NET-webbprogrammet som är länkat nedan eller navigera till ett annat program i innehålls förteckningen under **autentisera användare**.

> [!div class="nextstepaction"]
> [Självstudie: aktivera autentisering i ett webb program med hjälp av Azure AD B2C >](tutorial-web-app-dotnet.md)
