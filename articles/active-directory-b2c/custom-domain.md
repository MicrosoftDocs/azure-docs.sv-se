---
title: Aktivera Azure AD B2C anpassade domäner
titleSuffix: Azure AD B2C
description: 'Lär dig hur du aktiverar anpassade domäner i dina omdirigerings-URL: er för Azure Active Directory B2C.'
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2de419885938b27ebce4a934db5ef966965b3dbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580172"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Aktivera anpassade domäner för Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

I den här artikeln beskrivs hur du aktiverar anpassade domäner i dina omdirigerings-URL: er för Azure Active Directory B2C (Azure AD B2C). Att använda en anpassad domän med ditt program ger en mer sömlös användar upplevelse. Från användarens perspektiv finns de kvar i din domän under inloggnings processen i stället för att omdirigera till Azure AD B2C standard domän *<klient organisations namnet>. b2clogin.com*.

![Anpassad användar upplevelse för domänen](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Översikt över anpassad domän

Du kan aktivera anpassade domäner för Azure AD B2C med [Azures frontend-dörr](https://azure.microsoft.com/services/frontdoor/). Azures front dörr är en global, skalbar start punkt som använder Microsoft Global Edge-nätverket för att skapa snabba, säkra och mycket skalbara webb program. Du kan rendera Azure AD B2C innehåll bakom Azures front dörr och sedan konfigurera ett alternativ i Azures front dörr för att leverera innehållet via en anpassad domän i programmets URL.

Följande diagram illustrerar integreringen med Azures front dörr:

1. Från ett program klickar användaren på inloggnings knappen, som tar dem till Azure AD B2C inloggnings sida. På den här sidan anges ett anpassat domän namn.
1. Webbläsaren löser det anpassade domän namnet till Azures IP-adress för front dörren. Vid DNS-matchning pekar en kanoniskt namn (CNAME) med ett anpassat domän namn till din frontend-standardfrontend-standardvärd (till exempel `contoso.azurefd.net` ). 
1. Trafiken som är adresserad till den anpassade domänen (till exempel `login.contoso.com` ) dirigeras till den angivna frontend-standardvärdet på klient sidan ( `contoso.azurefd.net` ).
1. Azures front dörr anropar Azure AD B2C innehåll med hjälp av Azure AD B2C `<tenant-name>.b2clogin.com` standard domän. Begäran till Azure AD B2C-slutpunkten innehåller ett anpassat HTTP-huvud som innehåller det ursprungliga anpassade domän namnet.
1. Azure AD B2C svarar på begäran genom att visa det relevanta innehållet och den ursprungliga anpassade domänen.

![Nätverks diagram för anpassade domäner](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> Anslutningen från webbläsaren till Azures frontend-dörr bör alltid använda IPv4 i stället för IPv6.

Tänk på följande när du använder anpassade domäner:

- Du kan konfigurera flera anpassade domäner. För det maximala antalet anpassade domäner som stöds, se [Azure AD-tjänstens gränser och begränsningar](../active-directory/enterprise-users/directory-service-limits-restrictions.md) för Azure AD B2C och [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) för Azures front dörr.
- Azures front dörr är en separat Azure-tjänst, så ytterligare avgifter kommer att debiteras. Mer information finns i [priser för front dörren](https://azure.microsoft.com/pricing/details/frontdoor).
- Om du vill använda [brand väggen för webbaserade webb program](../web-application-firewall/afds/afds-overview.md)i Azure måste du bekräfta att brand Väggs konfigurationen och reglerna fungerar korrekt med dina Azure AD B2C användar flöden.
- När du har konfigurerat anpassade domäner kommer användarna fortfarande att kunna komma åt Azure AD B2C standard domän namn *<klient namn>. b2clogin.com* (om du inte använder en anpassad princip och du [blockerar åtkomsten](#block-access-to-the-default-domain-name).
- Om du har flera program kan du migrera dem till den anpassade domänen eftersom webbläsaren lagrar Azure AD B2C-sessionen under det domän namn som används för närvarande.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>Lägg till ett anpassat domän namn till din klient organisation

Följ anvisningarna för hur du [lägger till och validerar din anpassade domän i Azure AD](../active-directory/fundamentals/add-custom-domain.md). När domänen har verifierats tar du bort den DNS TXT-post som du skapade.

> [!IMPORTANT]
> För de här stegen måste du logga in på din **Azure AD B2C** -klient och välja tjänsten **Azure Active Directory** .

Verifiera varje under domän som du planerar att använda. Det räcker inte att verifiera domänen på den översta nivån. Om du till exempel vill kunna logga in med *login.contoso.com* och *Account.contoso.com* måste du verifiera både under domäner och inte bara domän *contoso.com* på den översta nivån.  

## <a name="create-a-new-azure-front-door-instance"></a>Skapa en ny instans av Azure frontend-dörren

Följ stegen för att [skapa en klients dörren för ditt program](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) med hjälp av standardinställningarna för klient dels värden och routningsregler. 

> [!IMPORTANT]
> När du har loggat in på Azure Portal i steg 1 väljer du **katalog + prenumeration** och väljer den katalog som innehåller den Azure-prenumeration som du vill använda för Azures front dörr. Detta bör *inte* vara den katalog som innehåller din Azure AD B2C-klient. 

I steget **Lägg till en server** del använder du följande inställningar:

* För **värd typen för backend** väljer du **anpassad värd**.  
* För **värd namn för backend** väljer du värd namnet för Azure AD B2C slut punkten, <klient namnet>. b2clogin.com. Till exempel contoso.b2clogin.com. 
* För **värd rubrik för Server** del väljer du samma värde som du valde för **Server dels värd namnet**.

![Lägg till en server del](./media/custom-domain/add-a-backend.png)

När du har lagt till **Server delen** i **backend-poolen** inaktiverar du **hälso avsökningarna**.

![Lägg till en serverdelspool](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Konfigurera din anpassade domän i Azures front dörr

Följ stegen för att [lägga till en anpassad domän i din frontend-dörr](../frontdoor/front-door-custom-domain.md). När du skapar `CNAME` posten för din anpassade domän använder du det anpassade domän namn som du kontrollerade tidigare i steget [Lägg till ett anpassat domän namn i Azure AD](#add-a-custom-domain-name-to-your-tenant) . 

När det anpassade domän namnet har verifierats väljer du **anpassat domän namn https**. Under **certifikat hanterings typen** väljer du [front dörr hantering](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)eller använder ett [eget certifikat](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate). 

Följande skärm bild visar hur du lägger till en anpassad domän och aktiverar HTTPS med hjälp av ett Azure-certifikat för frontend-dörren.

![Konfigurera en anpassad domän för Azures front dörr](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>Konfigurera CORS

Om du [anpassar Azure AD B2C användar gränssnitt](customize-ui-with-html.md) med en HTML-mall måste du [Konfigurera CORS](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) med din anpassade domän.

Konfigurera Azure Blob Storage för resurs delning mellan ursprung med följande steg:

1. Navigera till ditt lagringskonto på [Azure-portalen](https://portal.azure.com).
1. I menyn väljer du **CORS**.
1. För **tillåtna ursprung** anger du `https://your-domain-name` . Ersätt `your-domain-name` med ditt domän namn. Till exempel `https://login.contoso.com`. Använd små bokstäver när du anger ditt klient namn.
1. För **tillåtna metoder** väljer du både `GET` och `OPTIONS` .
1. För **tillåtna huvuden** anger du en asterisk (*).
1. För **exponerade rubriker** anger du en asterisk (*).
1. Ange 200 för **högsta ålder**.
1. Välj **Spara**.

## <a name="configure-your-identity-provider"></a>Konfigurera din identitetsprovider

När en användare väljer att logga in med en social identitetsprovider, initierar Azure AD B2C en auktoriseringsbegäran och tar användaren till den valda identitets leverantören för att slutföra inloggnings processen. Auktoriseringsbegäran anger `redirect_uri` med Azure AD B2C standard domän namn: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

Om du har konfigurerat principen för att tillåta inloggning med en extern identitetsprovider, uppdaterar du OAuth-omdirigerings-URI: er med den anpassade domänen. Med de flesta identitets leverantörer kan du registrera flera omdirigerings-URI: er. Vi rekommenderar att du lägger till omdirigerings-URI: er istället för att ersätta dem så att du kan testa den anpassade principen utan att påverka program som använder Azure AD B2C standard domän namn 

I följande omdirigerings-URI:

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- Ersätt **<anpassade domän namn>** med ditt anpassade domän namn.
- Ersätt **<klient namn>** med namnet på din klient organisation eller ditt klient-ID.

I följande exempel visas en giltig OAuth-omdirigerings-URI:

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

Om du väljer att använda [klient-ID: t](#optional-use-tenant-id)skulle en giltig OAuth-omdirigerings-URI se ut så här:

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

[SAML Identity Provider](saml-identity-provider-technical-profile.md) metadata skulle se ut så här:

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>Testa din anpassade domän

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **principer** väljer du **användar flöden (principer)**.
1. Välj ett användar flöde och välj sedan **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kopiera till Urklipp**.

    ![Kopiera URI: n för begäran om auktorisering](./media/custom-domain/user-flow-run-now.png)

1. I URL: en för **körning av användar flödet** ersätter du Azure AD B2C domän (<klient organisations namnet>. b2clogin.com) med din anpassade domän.  
    Till exempel i stället för:

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    Använd

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. Välj **Kör användar flöde**. Din Azure AD B2Cs princip ska läsas in.
1. Logga in med både lokala och sociala konton.
1. Upprepa testet med resten av dina principer.

## <a name="configure-your-application"></a>Konfigurera ditt program 

När du har konfigurerat och testat den anpassade domänen kan du uppdatera dina program för att läsa in URL: en som anger din anpassade domän som värdnamn i stället för den Azure AD B2C domänen. 

Den anpassade domän integreringen gäller för autentiserings-slutpunkter som använder Azure AD B2C-principer (användar flöden eller anpassade principer) för att autentisera användare. Dessa slut punkter kan se ut ungefär så här:

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Ersätt:
- **anpassad domän** med din anpassade domän
- **klient organisations namn** med klient namn eller klient-ID
- **princip-namn** med ditt princip namn. [Läs mer om Azure AD B2C-principer](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


Metadata för [SAML-tjänstprovidern](./saml-service-provider.md) kan se ut så här: 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>Valfritt Använd klient-ID

Du kan ersätta ditt B2C-klient namn i URL: en med ditt klient-ID-GUID så att alla referenser till "B2C" tas bort i URL: en. Du hittar ditt klient-ID-GUID på översikts sidan för B2C i Azure Portal.
Du kan till exempel ändra `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` till `https://account.contosobank.co.uk/<tenant ID GUID>/`

Om du väljer att använda klient-ID i stället för klient organisations namn måste du uppdatera identitets leverantörens **OAuth-omdirigerings-URI: er** . Mer information finns i [Konfigurera din identitets leverantör](#configure-your-identity-provider).

### <a name="token-issuance"></a>Utfärdande av token

Tillägget för token utfärdarens namn (ISS) ändras baserat på den anpassade domänen som används. Exempel:

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Blockera åtkomst till standard domän namnet

När du har lagt till den anpassade domänen och konfigurerat ditt program kommer användarna fortfarande att ha åtkomst till <klient organisations namn>. b2clogin.com-domänen. För att förhindra åtkomst kan du konfigurera principen för att kontrol lera auktoriseringsbegäran "värdnamn" mot en lista över tillåtna domäner. Värd namnet är domän namnet som visas i URL: en. Värd namnet är tillgängligt via `{Context:HostName}` [anspråk matchare](claim-resolver-overview.md). Sedan kan du presentera ett anpassat fel meddelande. 

1. Hämta exemplet på en princip för villkorlig åtkomst som kontrollerar värd namnet från [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name).
1. Ersätt strängen `yourtenant` med namnet på din Azure AD B2C-klient i varje fil. Om namnet på din B2C-klient till exempel är *contosob2c*, blir alla instanser av `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .
1. Ladda upp principfiler i följande ordning: `B2C_1A_TrustFrameworkExtensions_HostName.xml` och sedan `B2C_1A_signup_signin_HostName.xml` .

::: zone-end

## <a name="troubleshooting"></a>Felsökning

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C returnerar fel meddelandet Det gick inte att hitta sidan

- **Symptom** – när du försöker logga in med den anpassade domänen visas ett HTTP 404-fel meddelande när du försöker logga in med den anpassade domänen.
- **Möjliga orsaker** – det här problemet kan vara relaterat till DNS-konfigurationen eller konfiguration av klient delen för Azures frontend-dörr. 
- **Lösning**:  
    1. Kontrol lera att den anpassade domänen är [registrerad och har verifierats](#add-a-custom-domain-name-to-your-tenant) i Azure AD B2C klient organisationen.
    1. Kontrol lera att den [anpassade domänen](../frontdoor/front-door-custom-domain.md) är korrekt konfigurerad. `CNAME`Posten för din anpassade domän måste peka på din Azure-frontend-standardklients värd (till exempel contoso.azurefd.net).
    1. Se till att [konfigurationen för Azure frontend-adresspoolen](#set-up-your-custom-domain-on-azure-front-door) pekar på den klient där du ställer in det anpassade domän namnet och var ditt användar flöde eller anpassade principer lagras.

### <a name="identify-provider-returns-an-error"></a>Identifiera Provider returnerar ett fel

- **Symptom** – när du har konfigurerat en anpassad domän kan du logga in med lokala konton. Men när du loggar in med autentiseringsuppgifter från externa [sociala eller företags identitets leverantörer](add-identity-provider.md), visar identitets leverantörerna ett fel meddelande.
- **Möjliga orsaker** – när Azure AD B2C tar användaren att logga in med en federerad identitets leverantör, anges omdirigerings-URI. Omdirigerings-URI: n är den slut punkt som identitets leverantören returnerar token till. Omdirigerings-URI: n är samma domän som programmet använder med begäran om auktorisering. Om omdirigerings-URI: n inte har registrerats i identitets leverantören, kanske den inte har förtroende för den nya omdirigerings-URI: n, vilket resulterar i ett fel meddelande. 
- **Lösning** – Följ stegen i [Konfigurera din identitets leverantör](#configure-your-identity-provider) för att lägga till den nya omdirigerings-URI: n. 


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Kan jag använda Avancerad konfiguration av Azure front dörr, till exempel *brand Väggs regler för webb program*? 
  
Även om avancerade konfigurations inställningar för Azures front dörr inte stöds officiellt kan du använda dem på egen risk. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>Varför kan jag inte se den anpassade domänen när jag använder kör nu för att försöka köra min princip?

Kopiera URL: en, ändra domän namnet manuellt och klistra sedan in det i webbläsaren igen.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Vilken IP-adress presenteras för Azure AD B2C? Användarens IP-adress eller IP-adress för Azures frontend-dörr?

Azures front dörr skickar användarens ursprungliga IP-adress. Detta är den IP-adress som du ser i gransknings rapporteringen eller den anpassade principen.

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>Kan jag använda en brand vägg för webbaserade program från tredje part (WAF) med B2C?

Om du vill använda din egen brand vägg för webbaserade program framför Azures front dörr måste du konfigurera och kontrol lera att allt fungerar korrekt med dina Azure AD B2C användar flöden.

## <a name="next-steps"></a>Nästa steg

Läs om [OAuth-auktoriseringsbegäran](protocols-overview.md).