---
title: Rubrikbaserad autentisering med PingAccess för Azure AD-Programproxy
description: Publicera program med PingAccess och App Proxy för att stödja huvudbaserad autentisering.
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
ms.openlocfilehash: 23008d785c27b901f3487d3eddff7cb8e7529f6e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600088"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Huvudbaserad autentisering för enkel inloggning med Programproxy och PingAccess

Azure Active Directory (Azure AD) Programproxy partner med PingAccess så att dina Azure AD-kunder kan komma åt fler av dina program. PingAccess ger ett annat alternativ än [integrerad rubrikbaserad enkel inloggning.](application-proxy-configure-single-sign-on-with-headers.md)

## <a name="whats-pingaccess-for-azure-ad"></a>Vad är PingAccess för Azure AD?

Med PingAccess för Azure AD kan du ge användare åtkomst och enkel inloggning (SSO) till program som använder huvuden för autentisering. Programproxy hanterar dessa program som andra, med hjälp av Azure AD för att autentisera åtkomst och sedan skicka trafik via anslutningstjänsten. PingAccess finns framför programmen och översätter åtkomsttoken från Azure AD till ett -huvud. Programmet får sedan autentiseringen i det format som det kan läsa.

Användarna märker inte något annorlunda när de loggar in för att använda företagets program. De kan fortfarande fungera var som helst på alla enheter. Anslutningsapparna Programproxy fjärrtrafik till alla appar utan hänsyn till deras autentiseringstyp, så de balanserar fortfarande belastningen automatiskt.

## <a name="how-do-i-get-access"></a>Hur gör jag för att få åtkomst?

Eftersom det här scenariot kommer från ett partnerskap mellan Azure Active Directory och PingAccess behöver du licenser för båda tjänsterna. I Azure Active Directory Premium ingår dock en grundläggande PingAccess-licens som omfattar upp till 20 program. Om du behöver publicera fler än 20 huvudbaserade program kan du köpa ytterligare en licens från PingAccess.

Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publicera ditt program i Azure

Den här artikeln är till för personer som publicerar ett program med det här scenariot för första gången. Förutom att beskriva publiceringsstegen hjälper den dig att komma igång med både Programproxy och PingAccess. Om du redan har konfigurerat båda tjänsterna men vill uppdatera publiceringsstegen går du vidare till avsnittet Lägg till ditt program [i Azure AD med Programproxy.](#add-your-application-to-azure-ad-with-application-proxy)

> [!NOTE]
> Eftersom det här scenariot är ett partnerskap mellan Azure AD och PingAccess finns några av anvisningarna på webbplatsen pingidentitet.

### <a name="install-an-application-proxy-connector"></a>Installera en Programproxy anslutningsapp

Om du redan har aktiverat Programproxy installerat en anslutningsapp kan du hoppa över det här avsnittet och gå till Lägg till ditt program i [Azure AD med Programproxy](#add-your-application-to-azure-ad-with-application-proxy).

Anslutningsappen Programproxy är en Windows Server-tjänst som dirigerar trafiken från fjärrpersonal till dina publicerade program. Mer detaljerade installationsanvisningar finns i [Självstudie: Lägga](application-proxy-add-on-premises-application.md)till ett lokalt program för fjärråtkomst via Programproxy i Azure Active Directory .

1. Logga in på [Azure Active Directory-portalen](https://aad.portal.azure.com/) som programadministratör. Sidan **Azure Active Directory administrationscenter** visas.
1. Välj **Azure Active Directory Hämta**  >  anslutningstjänst för **programproxy.**  >   Hämtningssidan **Programproxy anslutningsappen** visas.

   ![Nedladdning av anslutningsprogram för programproxy](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Följ installationsinstruktionerna.

När du laddar ned anslutningsappen bör Programproxy för din katalog, men om inte kan du **välja Aktivera Programproxy**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Lägga till ditt program i Azure AD med Programproxy

Det finns två åtgärder som du måste utföra i Azure Portal. Först måste du publicera ditt program med Programproxy. Sedan måste du samla in viss information om programmet som du kan använda under PingAccess-stegen.

#### <a name="publish-your-application"></a>Publicera programmet

Du måste först publicera ditt program. Den här åtgärden omfattar:

- Lägga till ditt lokala program i Azure AD
- Tilldela en användare för att testa programmet och välja rubrikbaserad enkel inloggning
- Konfigurera programmets omdirigerings-URL
- Bevilja behörigheter för användare och andra program att använda ditt lokala program

Så här publicerar du ett eget lokalt program:

1. Om du inte gjorde det i det sista avsnittet loggar du in på [Azure Active Directory-portalen](https://aad.portal.azure.com/) som programadministratör.
1. Välj **Företagsprogram**  >  **Nytt program** Lägg till ett lokalt  >  **program**. Sidan **Lägg till ett eget lokalt program** visas.

   ![Lägga till ett eget lokalt program](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Fyll i de obligatoriska fälten med information om det nya programmet. Använd vägledningen nedan för inställningarna.

   > [!NOTE]
   > En mer detaljerad genomgång av det här steget finns i [Lägga till en lokal app i Azure AD.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)

   1. **Intern URL:** Normalt anger du den URL som tar dig till appens inloggningssida när du är i företagsnätverket. I det här scenariot måste anslutningsappen behandla PingAccess-proxyn som programmets förstasida. Använd det här formatet: `https://<host name of your PingAccess server>:<port>` . Porten är 3 000 som standard, men du kan konfigurera den i PingAccess.

      > [!WARNING]
      > För den här typen av enkel inloggning måste den interna URL:en `https` använda och kan inte använda `http` . Det finns också en begränsning när du konfigurerar ett program att inga två appar ska ha samma interna URL eftersom det gör att appproxyn kan upprätthålla skillnaden mellan program.

   1. **Förautentiseringsmetod:** Välj **Azure Active Directory**.
   1. **Översätt URL i rubriker:** Välj **Nej.**

   > [!NOTE]
   > Om det här är ditt första program använder du port 3000 för att starta och komma tillbaka för att uppdatera den här inställningen om du ändrar PingAccess-konfigurationen. För efterföljande program måste porten matcha lyssnaren som du har konfigurerat i PingAccess. Läs mer om [lyssnare i PingAccess.](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)

1. Välj **Lägg till**. Översiktssidan för det nya programmet visas.

Tilldela nu en användare för programtestning och välj rubrikbaserad enkel inloggning:

1. Från programmets sidopanel väljer du **Användare och grupper Lägg** till  >  **användare** Användare och  >  **grupper \<Number> (Valda).** En lista över användare och grupper visas där du kan välja mellan.

   ![Visar listan över användare och grupper](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Välj en användare för programtestning och välj **Välj**. Kontrollera att det här testkontot har åtkomst till det lokala programmet.
1. Välj **Tilldela**.
1. I programmets sidofält väljer **du Enkel inloggning**  >  **Rubrikbaserad**.

   > [!TIP]
   > Om det här är första gången du använder huvudbaserad enkel inloggning måste du installera PingAccess. Kontrollera att din Azure-prenumeration automatiskt associeras med pingaccess-installationen genom att använda länken på den här sidan för enkel inloggning för att ladda ned PingAccess. Du kan öppna nedladdningswebbplatsen nu eller komma tillbaka till den här sidan senare.

   ![Visar rubrikbaserad inloggningsskärm och PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Välj **Spara**.

Kontrollera sedan att omdirigerings-URL:en är inställd på din externa URL:

1. I **sidofältet Azure Active Directory administrationscenter** väljer du **Azure Active Directory**  >  **Appregistreringar**. En lista över program visas.
1. Välj ditt program.
1. Välj länken bredvid **Omdirigerings-URI:er,** som visar antalet omdirigerings-URI:er som har ställts in för webbklienter och offentliga klienter. Sidan **\<application name> – Autentisering** visas.
1. Kontrollera om den externa URL:en som du tilldelade till ditt program tidigare finns i listan **Omdirigerings-URI:er.** Om den inte är det lägger du till den externa URL:en nu med hjälp av omdirigerings-URI-typen **Webb** och väljer **Spara**.

Förutom den externa URL:en ska en auktorisera slutpunkt för Azure Active Directory på den externa URL:en läggas till i listan omdirigerings-URI:er.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Konfigurera slutligen ditt lokala program så att användarna har läsbehörighet och andra program har läs-/skrivåtkomst:

1. Från **sidofältet Appregistreringar** för ditt program väljer du **API-behörigheter Lägg** till en behörighet För  >    >  **Microsoft-API:Microsoft Graph**  >  . Sidan **Begär API-behörigheter** för **Microsoft Graph** visas, som innehåller API:erna för Windows Azure Active Directory.

   ![Visar sidan Begär API-behörigheter](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Välj **Delegerade behörigheter**  >  **User**  >  **User.Read**.
1. Välj **Programbehörigheter**  >    >  **Application.ReadWrite.All**.
1. Välj **Lägg till behörigheter.**
1. På sidan **API-behörigheter** väljer du **Bevilja \<your directory name> administratörsmedgivande för**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Samla in information för PingAccess-stegen

Du måste samla in dessa tre typer av information (alla GUID:er) för att konfigurera ditt program med PingAccess:

| Namn på Azure AD-fält | Namnet på pingaccess-fältet | Dataformat |
| --- | --- | --- |
| **Program-ID (klient)** | **Klient-ID** | GUID |
| **Katalog-ID (klient)** | **Emittenten** | GUID |
| `PingAccess key` | **Klienthemlighet** | Slumpmässig sträng |

Så här samlar du in den här informationen:

1. I **sidofältet Azure Active Directory administrationscenter** väljer du **Azure Active Directory**  >  **Appregistreringar**. En lista över program visas.
1. Välj ditt program. Sidan **Appregistreringar** för ditt program visas.

   ![Registreringsöversikt för ett program](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Bredvid värdet **program-ID (klient)** väljer du ikonen **Kopiera till Urklipp** och kopierar och sparar den. Du anger det här värdet senare som Klient-ID för PingAccess.
1. Bredvid värdet **katalog-ID (klient)** väljer du även **Kopiera till Urklipp** och kopierar och sparar det. Du anger det här värdet senare som PingAccess-utfärdare.
1. Välj Certifikat och hemligheter Ny **klienthemlighet Appregistreringar** i sidopanelen **i**  >  programmet. Sidan **Lägg till en klienthemlighet** visas.

   ![Visar sidan Lägg till en klienthemlighet](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. I **Beskrivning** skriver du `PingAccess key` .
1. Under **Upphör att** gälla väljer du hur du ställer in PingAccess-nyckeln: Om **1 år,** **Om 2 år** eller **Aldrig**.
1. Välj **Lägg till**. PingAccess-nyckeln visas i tabellen med klienthemligheter, med en slumpmässig sträng som fylls i automatiskt i **fältet VALUE.**
1. Bredvid fältet VÄRDE i **PingAccess-nyckeln** väljer du ikonen **Kopiera till Urklipp** och kopierar och sparar den. Du anger det här värdet senare som PingAccesss klienthemlighet.

**Uppdatera `acceptMappedClaims` fältet:**

1. Logga in på [Azure Active Directory-portalen](https://aad.portal.azure.com/) som programadministratör.
1. Välj **Azure Active Directory**  >  **Appregistreringar**. En lista över program visas.
1. Välj ditt program.
1. I sidopanelen på **Appregistreringar** för ditt program väljer du **Manifest**. JSON-manifestkoden för programmets registrering visas.
1. Sök efter `acceptMappedClaims` fältet och ändra värdet till `True` .
1. Välj **Spara**.

### <a name="use-of-optional-claims-optional"></a>Användning av valfria anspråk (valfritt)

Med valfria anspråk kan du lägga till standard-men-inte-inkluderade anspråk som varje användare och klientorganisation har. Du kan konfigurera valfria anspråk för ditt program genom att ändra programmanifestet. Mer information finns i artikeln [Understanding the Azure AD application manifest (Förstå Azure AD-programmanifestet)](../develop/reference-app-manifest.md)

Exempel på att inkludera e-postadress i access_token som PingAccess kommer att använda:

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

### <a name="use-of-claims-mapping-policy-optional"></a>Användning av princip för anspråksmappning (valfritt)

[Princip för anspråksmappning (förhandsversion)](../develop/reference-claims-mapping-policy-type.md#claims-mapping-policy-properties) för attribut som inte finns i AzureAD. Med anspråksmappning kan du migrera gamla, molnbaserade appar till molnet genom att lägga till ytterligare anpassade anspråk som backas upp av dina ADFS- eller användarobjekt

Om du vill att ditt program ska använda ett anpassat anspråk och inkludera ytterligare fält måste du även ha skapat en anpassad princip för [anspråksmappning](../develop/active-directory-claims-mapping.md)och tilldelat den till programmet .

> [!NOTE]
> Om du vill använda ett anpassat anspråk måste du också ha en anpassad princip definierad och tilldelad till programmet. Den här principen bör innehålla alla nödvändiga anpassade attribut.
>
> Du kan göra principdefinition och tilldelning via PowerShell eller Microsoft Graph. Om du gör dem i PowerShell kan du behöva använda och `New-AzureADPolicy` sedan tilldela det till programmet med `Add-AzureADServicePrincipalPolicy` . Mer information finns i [Principtilldelning för anspråksmappning.](../develop/active-directory-claims-mapping.md)

Exempel:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Aktivera PingAccess för att använda anpassade anspråk

Det är valfritt att aktivera PingAccess för att använda anpassade anspråk, men det krävs om du förväntar dig att programmet ska använda ytterligare anspråk.

När du konfigurerar PingAccess i följande steg måste webbsessionen du skapar (Settings->Access-> Web Sessions) ha Begärandeprofil avmarkerad och **Uppdatera** användarattribut inställt på **Nej**

## <a name="download-pingaccess-and-configure-your-application"></a>Ladda ned PingAccess och konfigurera ditt program

Nu när du har slutfört alla Azure Active Directory konfigurationsstegen kan du gå vidare till att konfigurera PingAccess.

De detaljerade stegen för PingAccess-delen av det här scenariot fortsätter i dokumentationen om pingidentitet. Följ anvisningarna i [Konfigurera PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) för Azure AD för att skydda program som publicerats med hjälp Microsoft Azure AD Programproxy på webbplatsen Ping Identity och ladda ned den senaste [versionen av PingAccess.](https://www.pingidentity.com/en/lp/azure-download.html?)

De här stegen hjälper dig att installera PingAccess och konfigurera ett PingAccess-konto (om du inte redan har ett). Om du sedan vill skapa en Azure AD OpenID Connect-anslutning (OIDC) ställer du in en tokenprovider med **katalog-ID-värdet (klient)** som du kopierade från Azure AD-portalen. För att skapa en webbsession i PingAccess använder du sedan **program-ID:t (klienten) och** `PingAccess key` värdena. Därefter kan du konfigurera identitetsmappning och skapa en virtuell värd, webbplats och ett program.

### <a name="test-your-application"></a>Testa ditt program

När du har slutfört alla de här stegen bör programmet vara igång. Testa den genom att öppna en webbläsare och gå till den externa URL som du skapade när du publicerade programmet i Azure. Logga in med det testkonto som du tilldelade till programmet.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera PingAccess för Azure AD för att skydda program som publicerats med Microsoft Azure AD Programproxy](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md)
- [Felsöka problem med programproxy och felmeddelanden](application-proxy-troubleshoot.md)