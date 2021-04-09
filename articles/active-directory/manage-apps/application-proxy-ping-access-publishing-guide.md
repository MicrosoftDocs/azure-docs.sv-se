---
title: Huvud-baserad autentisering med PingAccess för Azure AD-programproxy
description: Publicera program med PingAccess och App proxy för att stödja huvud-baserad autentisering.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 4abef9c848a32d9fa6a34eabe407c4d10f913797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643804"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Huvudbaserad autentisering för enkel inloggning med Programproxy och PingAccess

Azure Active Directory (Azure AD) Application Proxy har samarbetat med PingAccess så att dina Azure AD-kunder kan komma åt fler av dina program. PingAccess tillhandahåller ett annat alternativ än integrerad [rubrik-baserad enkel inloggning](application-proxy-configure-single-sign-on-with-headers.md).

## <a name="whats-pingaccess-for-azure-ad"></a>Vad är PingAccess för Azure AD?

Med PingAccess för Azure AD kan du ge användare åtkomst och enkel inloggning (SSO) till program som använder rubriker för autentisering. Application Proxy behandlar dessa program på samma sätt som andra, med hjälp av Azure AD för att autentisera åtkomst och sedan skicka trafik via anslutnings tjänsten. PingAccess placeras framför programmen och översätter åtkomsttoken från Azure AD till en rubrik. Programmet tar sedan emot autentiseringen i det format som det kan läsa.

Användarna märker inte något annorlunda när de loggar in för att använda dina företags program. De kan fortfarande arbeta var som helst på valfri enhet. Application Proxy-anslutningarna dirigerar fjärrtrafik till alla appar utan hänsyn till autentiseringstypen, så de kommer fortfarande att utjämna belastningen automatiskt.

## <a name="how-do-i-get-access"></a>Hur gör jag för att få åtkomst?

Eftersom det här scenariot kommer från ett partnerskap mellan Azure Active Directory och PingAccess behöver du licenser för båda tjänsterna. Azure Active Directory Premium prenumerationer innehåller dock en grundläggande PingAccess-licens som omfattar upp till 20 program. Om du behöver publicera fler än 20 huvudbaserade program kan du köpa ytterligare en licens från PingAccess.

Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publicera ditt program i Azure

Den här artikeln är för användare att publicera ett program med det här scenariot för första gången. Utöver informationen i publicerings stegen får du hjälp med att komma igång med både programproxy-och PingAccess. Om du redan har konfigurerat båda tjänsterna men vill ha en uppdatering av publicerings stegen går du vidare till avsnittet [Lägg till ditt program i Azure AD med Application Proxy](#add-your-application-to-azure-ad-with-application-proxy) .

> [!NOTE]
> Eftersom det här scenariot är ett partnerskap mellan Azure AD och PingAccess finns det några av anvisningarna på webbplatsen för ping-identitet.

### <a name="install-an-application-proxy-connector"></a>Installera en Application Proxy-koppling

Om du har aktiverat Application Proxy aktiverat och installerat en koppling redan, kan du hoppa över det här avsnittet och gå till [Lägg till ditt program i Azure AD med Application Proxy](#add-your-application-to-azure-ad-with-application-proxy).

Application Proxy Connector är en Windows Server-tjänst som dirigerar trafiken från dina fjärranslutna anställda till dina publicerade program. Mer detaljerad Installationsinstruktioner finns i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com/) som program administratör. Sidan **Azure Active Directory administrations Center** visas.
1. Välj **Azure Active Directory**  >  **Application Proxy**  >  **Download Connector service**. Sidan **hämtning av Application Proxy Connector** visas.

   ![Hämtning av Application Proxy Connector](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Följ installationsinstruktionerna.

Om du hämtar anslutningen ska du automatiskt aktivera programproxyn för din katalog, men om du inte gör det kan du välja **Aktivera programproxy**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Lägg till ditt program i Azure AD med Application Proxy

Det finns två åtgärder som du måste utföra i Azure Portal. Först måste du publicera ditt program med Application Proxy. Sedan måste du samla in viss information om programmet som du kan använda under PingAccess-stegen.

#### <a name="publish-your-application"></a>Publicera programmet

Du måste först publicera ditt program. Den här åtgärden omfattar:

- Lägga till ditt lokala program i Azure AD
- Tilldela en användare för att testa programmet och välja rubrik-baserad SSO
- Konfigurera programmets omdirigerings-URL
- Bevilja behörigheter för användare och andra program att använda ditt lokala program

Så här publicerar du ett eget lokalt program:

1. Om du inte gjorde det sista avsnittet loggar du in på [Azure Active Directory Portal](https://aad.portal.azure.com/) som program administratör.
1. Välj **företags program**  >  **nytt program**  >  **Lägg till ett lokalt program**. Sidan **Lägg till ett eget lokalt program** visas.

   ![Lägga till ett eget lokalt program](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Fyll i de obligatoriska fälten med information om det nya programmet. Använd rikt linjerna nedan för inställningarna.

   > [!NOTE]
   > En mer detaljerad genom gång av det här steget finns i [lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Intern URL**: du anger vanligt vis den URL som tar dig till appens inloggnings sida när du är i företags nätverket. I det här scenariot måste anslutningen behandla PingAccess-proxyn som den första sidan i programmet. Använd det här formatet: `https://<host name of your PingAccess server>:<port>` . Porten är 3000 som standard, men du kan konfigurera den i PingAccess.

      > [!WARNING]
      > Den interna URL: en måste använda `https` och kan inte använda för den här typen av enkel inloggning `http` . Det finns också ett villkor när du konfigurerar ett program som inte har två appar som ska ha samma interna URL-adress som det gör att App proxy kan behålla åtskillnad mellan program.

   1. **Metod för förautentisering**: Välj **Azure Active Directory**.
   1. **Översätt URL i rubriker**: Välj **Nej**.

   > [!NOTE]
   > Om det här är ditt första program använder du Port 3000 för att starta och återgår till att uppdatera den här inställningen om du ändrar PingAccess-konfigurationen. För efterföljande program måste porten matcha den lyssnare som du har konfigurerat i PingAccess. Läs mer om [lyssnare i PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Välj **Lägg till**. Översikts sidan för det nya programmet visas.

Tilldela nu en användare för program testning och välj rubrik-baserad enkel inloggning:

1. I program sid panelen väljer **du användare och grupper**  >  **Lägg till användar**  >  **användare och grupper ( \<Number> valda)**. En lista över användare och grupper visas där du kan välja bland.

   ![Visar en lista över användare och grupper](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Välj en användare för program testning och välj **Välj**. Kontrol lera att det här test kontot har åtkomst till det lokala programmet.
1. Välj **Tilldela**.
1. Från program sid panelen väljer du rubrik baserad **på enkel inloggning**  >  .

   > [!TIP]
   > Om det här är första gången du använder en rubrik baserad enkel inloggning måste du installera PingAccess. För att se till att din Azure-prenumeration automatiskt associeras med din PingAccess-installation använder du länken på den här sidan för enkel inloggning för att hämta PingAccess. Du kan öppna hämtnings platsen nu eller gå tillbaka till den här sidan senare.

   ![Visar rubrikbaserade inloggnings skärmen och PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Välj **Spara**.

Kontrol lera att din URL för omdirigering har angetts till din externa URL:

1. I **Azure Active Directory administrations centerns** sid panelen väljer du **Azure Active Directory**  >  **Appregistreringar**. En lista över program visas.
1. Välj ditt program.
1. Välj länken bredvid **omdirigerings-URI**: er som visar antalet omdirigerings-URI: er som kon figurer ATS för webb-och offentliga klienter. Sidan **\<application name> – autentisering** visas.
1. Kontrol lera om den externa URL: en som du tilldelade ditt program tidigare finns i listan **omdirigerings-URI: er** . Om den inte är det lägger du till den externa URL: en nu, använder en omdirigerings **-URI-** typ och väljer **Spara**.

Förutom den externa URL: en ska en auktoriserad slut punkt för Azure Active Directory på den externa URL: en, läggas till i listan omdirigerings-URI: er.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Slutligen konfigurerar du ditt lokala program så att användare har Läs-och Skriv behörighet till Läs-och Skriv behörighet:

1. Välj **API-behörigheter** i **Appregistreringar** marginal list för ditt program,  >  **Lägg till en behörighet**  >  **Microsoft API: er**  >  **Microsoft Graph**. Sidan **begär API-behörigheter** för **Microsoft Graph** visas, som innehåller API: er för Windows Azure Active Directory.

   ![Visar sidan begär API-behörigheter](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Välj **delegerade behörigheter**  >  **användaren**  >  **användare. Läs**.
1. Välj **program behörigheter**  >  **program**  >  **program. readwrite. all**.
1. Välj **Lägg till behörigheter**.
1. På sidan **API-behörigheter** väljer du **bevilja administratörs medgivande \<your directory name> för**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Samla in information för PingAccess-stegen

Du måste samla in dessa tre informations delar (alla GUID) för att konfigurera ditt program med PingAccess:

| Namn på Azure AD-fält | Namn på fältet PingAccess | Dataformat |
| --- | --- | --- |
| **Program-ID (klient)** | **Klient-ID** | GUID |
| **Katalog-ID (klient)** | **Utfärdare** | GUID |
| `PingAccess key` | **Klient hemlighet** | Slumpmässig sträng |

Samla in den här informationen:

1. I **Azure Active Directory administrations centerns** sid panelen väljer du **Azure Active Directory**  >  **Appregistreringar**. En lista över program visas.
1. Välj ditt program. **Appregistreringar** sidan för ditt program visas.

   ![Registrerings översikt för ett program](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Klicka på ikonen **Kopiera till Urklipp** bredvid **programmets (klient) ID-** värde och kopiera och spara den sedan. Du anger det här värdet senare som PingAccess-klient-ID.
1. Klicka på **Kopiera till Urklipp** bredvid **ID-värdet för katalogen (klient)** och kopiera och spara det. Du anger det här värdet senare som PingAccesss utfärdare.
1. Välj **certifikat och hemligheter**   >  **ny klient hemlighet** från List rutan för Appregistreringar för ditt program. Sidan **Lägg till en klient hemlighet** visas.

   ![Visar sidan Lägg till en klient hemlighet](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. I **Beskrivning** skriver du `PingAccess key` .
1. Under **upphör ande** väljer du hur du vill ange PingAccess-nyckeln: **om 1 år**, **i två år** eller **aldrig**.
1. Välj **Lägg till**. PingAccess-nyckeln visas i tabellen med klient hemligheter, med en slumpmässig sträng som fyller i fältet **värde** .
1. Klicka på ikonen **Kopiera till Urklipp** bredvid **värde** fältet för PingAccess-nyckeln och kopiera och spara den. Du anger det här värdet senare som PingAccess-klient hemlighet.

**Uppdatera `acceptMappedClaims` fältet:**

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com/) som program administratör.
1. Välj **Azure Active Directory**  >  **Appregistreringar**. En lista över program visas.
1. Välj ditt program.
1. Välj **manifest** i list rutan på **Appregistreringar** sidan för ditt program. Manifest-JSON-koden för programmets registrering visas.
1. Sök efter `acceptMappedClaims` fältet och ändra värdet till `True` .
1. Välj **Spara**.

### <a name="use-of-optional-claims-optional"></a>Användning av valfria anspråk (valfritt)

Med valfria anspråk kan du lägga till standard-, men inte inkluderade, som standard-anspråk som varje användare och klient har. Du kan konfigurera valfria anspråk för programmet genom att ändra applikations manifestet. Mer information finns i [artikeln förstå Azure AD Application manifest](../develop/reference-app-manifest.md)

Exempel för att inkludera e-postadress i access_token som PingAccess kommer att använda:

```json
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Användning av anspråks mappnings princip (valfritt)

[Princip för anspråks mappning (för hands version)](../develop/active-directory-claims-mapping.md#claims-mapping-policy-properties) för attribut som inte finns i AzureAD. Med anspråks mappning kan du migrera gamla lokal appar till molnet genom att lägga till ytterligare anpassade anspråk som backas upp av ADFS-eller användar objekt

Om du vill att programmet ska använda ett anpassat anspråk och inkludera ytterligare fält, se till att du även har [skapat en anpassad princip för anspråks mappning och tilldelat den till programmet](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Om du vill använda ett anpassat anspråk måste du också ha en anpassad princip definierad och tilldelad till programmet. Den här principen ska innehålla alla obligatoriska anpassade attribut.
>
> Du kan utföra princip definition och tilldelning via PowerShell eller Microsoft Graph. Om du gör det i PowerShell kan du behöva först använda `New-AzureADPolicy` och sedan tilldela den till programmet med `Add-AzureADServicePrincipalPolicy` . Mer information finns i [princip tilldelning för anspråks mappning](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Exempel:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Aktivera PingAccess för att använda anpassade anspråk

Att aktivera PingAccess för att använda anpassade anspråk är valfritt, men krävs om du förväntar dig att programmet ska använda ytterligare anspråk.

När du ska konfigurera PingAccess i följande steg måste webbsessionen du skapar (inställningar->åtkomst >webbsessioner) ha **begär ande profil** avmarkerad och **Uppdatera användarattribut** inställda på **Nej**

## <a name="download-pingaccess-and-configure-your-application"></a>Ladda ned PingAccess och konfigurera ditt program

Nu när du har slutfört alla installations steg för Azure Active Directory kan du gå vidare till konfigurera PingAccess.

De detaljerade stegen för PingAccess-delen av det här scenariot fortsätter i identitets dokumentationen för ping. Följ anvisningarna i [Konfigurera PingAccess för Azure AD för att skydda program som publicerats med Microsoft Azure AD Application Proxy](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) på webbplatsen för ping Identity och ladda ned den [senaste versionen av PingAccess](https://www.pingidentity.com/en/lp/azure-download.html?).

Dessa steg hjälper dig att installera PingAccess och skapa ett PingAccess-konto (om du inte redan har ett). Om du sedan vill skapa en Azure AD OpenID Connect-anslutning (OIDC), ställer du in en token **-Provider med katalogen (klient) ID-** värdet som du kopierade från Azure AD-portalen. Sedan, för att skapa en webbsession på PingAccess, använder du **program-ID och-** `PingAccess key` värden. Därefter kan du konfigurera identitets mappning och skapa en virtuell värd, en webbplats och ett program.

### <a name="test-your-application"></a>Testa ditt program

När du har slutfört alla dessa steg bör programmet vara igång. Testa det genom att öppna en webbläsare och navigera till den externa URL: en som du skapade när du publicerade programmet i Azure. Logga in med det test konto som du har tilldelat till programmet.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera PingAccess för Azure AD för att skydda program som publiceras med Microsoft Azure AD Application Proxy](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md)
- [Felsöka problem med programproxy och felmeddelanden](application-proxy-troubleshoot.md)