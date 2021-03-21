---
title: Självstudie för att konfigurera Azure Active Directory B2C med ping identitet
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar Azure AD B2C-autentisering med ping-identitet
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 94e7ae93d05ae8ee35028882e14d8da74814d833
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650234"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Självstudie: konfigurera ping identitet med Azure Active Directory B2C för säker hybrid åtkomst

I den här exempel självstudien lär du dig att utöka Azure Active Directory (AD) B2C med  [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) och [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) för att aktivera säker hybrid åtkomst.

Många befintliga webb egenskaper, till exempel eCommerce-platser och webb program som är exponerade för Internet, distribueras bakom ett proxy-system, som ibland kallas för ett reverse proxy-system. Dessa proxyservrar tillhandahåller olika funktioner, inklusive förautentisering, princip tvång och trafik dirigering. Exempel på användnings områden är att skydda webb programmet från inkommande webb trafik och tillhandahålla en enhetlig sessions hantering över distribuerade Server distributioner.

I de flesta fall innehåller den här konfigurationen ett översättnings lager för autentisering som visar autentiseringen från webb programmet. Omvänd proxy i sin tur ger de autentiserade användarnas kontext till webb programmen, i ett enklare formulär, till exempel ett huvud värde i Clear eller Digest form. I en sådan konfiguration använder programmen inte några branschstandardiserade token, till exempel Security Assertion Markup Language (SAML), OAuth eller Open ID Connect (OIDC), i stället beroende av proxyn för att tillhandahålla autentiserings kontexten och upprätthålla sessionen med slutanvändarens agent, till exempel webbläsare eller internt program. Som en tjänst som körs i "man-in-the-Middle" kan proxyservrar tillhandahålla den ultimata sessionen. Det innebär också att proxy-tjänsten bör vara mycket effektiv och skalbar, inte bli en Flask hals eller en enskild felpunkt för programmen bakom proxy-tjänsten. Diagrammet är en illustration av en typisk implementering av omvända proxy och flödet av kommunikation.

![bild som visar den omvända proxyns implementering](./media/partner-ping/reverse-proxy.png)

Om du är i en situation där du vill modernisera identitets plattformen i sådana konfigurationer uppstår följande problem.

- Hur kan insatsen för program modernisering frikopplas från modernisering för identitets plattformen?

- Hur kan en miljö för samexistens upprättas med modern och äldre autentisering med hjälp av den moderna identitets tjänst leverantören?

  a. Hur kan du köra konsekvens för slutanvändaren?

  b. Hur ger du en enda inloggnings upplevelse över befintliga program?

Vi diskuterar en metod för att lösa sådana problem genom att integrera Azure AD B2C med [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) -och [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) -teknik.

## <a name="coexistence-environment"></a>Samexistens miljö

En tekniskt praktisk enkel lösning som också är kostnads effektiv är att konfigurera det omvända proxy-systemet att använda det moderna identitets systemet och delegera autentiseringen.  
Proxyservrar i det här fallet stöder moderna autentiseringsprotokoll och använder den omdirigerade (passiva) autentisering som skickar användare till den nya identitets leverantören (IdP).

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C som identitets leverantör

Azure AD B2C kan definiera **principer** som driver olika användar upplevelser och beteenden som också kallas **användar resor** som dirigeras från serverns slut. Varje sådan princip visar en protokoll slut punkt som kan utföra autentiseringen som om den vore en IdP. Det krävs ingen särskild hantering av program sidan för specifika principer. Programmet gör helt enkelt en autentiseringsbegäran för en begäran till den protokoll-specifika autentiserings slut punkt som exponeras av principen av intresse.  
Azure AD B2C kan konfigureras för att dela samma utfärdare över flera principer eller unik utfärdare för varje princip. Varje program kan peka på en eller flera av dessa principer genom att göra en begäran om protokoll intern autentisering och köra önskade användar beteenden, till exempel inloggning, registrering och profil redigeringar. Diagrammet visar OIDC och SAML Application-arbetsflöden.

![bild visar OIDC-och SAML-implementering](./media/partner-ping/azure-ad-identity-provider.png)

Även om scenariot som nämns fungerar bra för moderna program, kan det vara svårt att använda de äldre programmen för att på lämpligt sätt omdirigera användaren eftersom åtkomstbegäran till programmen inte innehåller kontexten för användar upplevelsen. I de flesta fall fångar proxy-lagret eller en integrerad agent i webb programmet åtkomstbegäran.

### <a name="pingaccess-as-a-reverse-proxy"></a>PingAccess som en omvänd proxy

Många kunder har distribuerat PingAccess som den omvända proxyn för att spela upp en eller flera roller som nämnts tidigare i den här artikeln. PingAccess kan fånga upp en direkt begäran med hjälp av man som är man-in-the-Middle eller som en omdirigering som kommer från en agent som körs på webb program servern.

PingAccess kan konfigureras med OIDC, OAuth2 eller SAML för att utföra autentisering mot en överordnad autentiseringsprovider. En enda överordnad IdP kan konfigureras för det här syftet på PingAccess-servern. Du ser konfigurationen i följande diagram:

![bild visar PingAccess med OIDC-implementering](./media/partner-ping/authorization-flow.png)

I en typisk Azure AD B2C-distribution där flera principer exponerar flera **IDP: er**, innebär det en utmaning. Eftersom PingAccess bara kan konfigureras med en enda överordnad IdP.  

### <a name="pingfederate-as-a-federation-proxy"></a>PingFederate som Federations-proxy

PingFederate är en företags identitets brygga som kan konfigureras fullständigt som en autentiseringsprovider eller en proxy för andra multipla överordnade IDP: er vid behov. Följande diagram visar den här funktionen.

![bild visar PingFederate-implementeringen](./media/partner-ping/pingfederate.png)

Den här funktionen kan användas för att sammanhangsbaseradt/dynamiskt eller ändra en inkommande begäran till en speciell Azure AD B2C princip. Följande är en presentation av protokollsekvensen för den här konfigurationen.

![bild visar arbets flödet PingAccess och PingFederate](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure-prenumeration. Om du inte har något kan du skaffa ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- En [Azure AD B2C klient](./tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

- PingAccess och PingFederate distribuerade i Docker-behållare eller direkt på virtuella Azure-datorer.

## <a name="connectivity"></a>Anslutning

Kontrol lera att följande är anslutet.

- **PingAccess Server** – kan kommunicera med PingFederate-servern, klientens webbläsare, OIDC, välkänd och identifierad identifiering av OAuth som publicerats av Azure AD B2C-tjänsten och PingFederate-servern.

- **PingFederate-Server** – kan kommunicera med PingAccess-servern, klientens webbläsare, OIDC, välkänd och identifierad identifiering av OAuth och nycklar som publiceras av Azure AD B2C-tjänsten.

- **Äldre eller huvudbaserade authn-program** – kan kommunicera till och från PingAccess-servern.

- **SAML-förlitande parts program** – kan komma åt webb läsar trafiken från klienten. Kan komma åt SAML-federationsmetadata som publicerats av Azure AD B2C-tjänsten.

- **Modernt program** – kan komma åt webb läsar trafiken från klienten. Åtkomst till OIDC, OAuth välkänd och identifiering av nycklar som publicerats av tjänsten Azure AD B2C.

- **REST API** – kan komma åt trafiken från en ursprunglig eller webb klient. Åtkomst till OIDC, OAuth välkänd och identifiering av nycklar som publicerats av tjänsten Azure AD B2C.

## <a name="configure-azure-ad-b2c"></a>Konfigurera Azure AD B2C

Du kan använda grundläggande användar flöden eller IEF-principer (Advanced Identity Enterprise Framework) för detta ändamål. PingAccess genererar metadata-slutpunkten baserat på **utfärdarens** värde med hjälp av [webfinger](https://tools.ietf.org/html/rfc7033) -baserad identifierings konvention.
Om du vill följa den här konventionen uppdaterar du Azure AD B2C Issuer-uppdateringen med hjälp av princip egenskaperna i användar flöden.

![bild visar inställningarna för token](./media/partner-ping/token-setting.png)

I de avancerade principerna kan detta konfigureras med **IssuanceClaimPattern** -metadata-elementet till **AuthorityWithTfp** -värdet i den [tekniska profilen för JWT-token](./jwt-issuer-technical-profile.md).

## <a name="configure-pingaccesspingfederate"></a>Konfigurera PingAccess/PingFederate

I följande avsnitt beskrivs den konfiguration som krävs.
Diagrammet visar det övergripande användar flödet för integreringen.

![bild visar integreringen av PingAccess och PingFederate](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>Konfigurera PingFederate som token-providern

Om du vill konfigurera PingFederate som token-Provider för PingAccess kontrollerar du att anslutningen från PingFederate till PingAccess upprättas följt av anslutningen från PingAccess till PingFederate.  
Se [den här artikeln](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) för konfigurations steg.

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>Konfigurera ett PingAccess-program för huvud-baserad autentisering

Ett PingAccess-program måste skapas för mål webb programmet för huvud-baserad autentisering. Gör så här:

#### <a name="step-1--create-a-virtual-host"></a>Steg 1 – Skapa en virtuell värd

>[!IMPORTANT]
>Om du vill konfigurera för den här lösningen måste du skapa en virtuell värd för varje program. Mer information om konfigurations överväganden och deras påverkan finns i [viktiga överväganden](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html).

Följ de här stegen för att skapa en virtuell värd:

1. Gå till **Inställningar**  >  **åtkomst till**  >  **virtuella värdar**

2. Välj **Lägg till virtuell värd**

3. I fältet värd anger du FQDN-delen av programmets URL

4. I fältet Port anger du **443**

5. Välj **Spara**

#### <a name="step-2--create-a-web-session"></a>Steg 2 – Skapa en webbsession

Följ de här stegen för att skapa en webbsession:

1. Navigera till **Inställningar**  >  **åtkomst till**  >  **webbsessioner**

2. Välj **Lägg till webbsession**

3. Ange ett **namn** för webbsessionen.

4. Välj **cookie-typ**, antingen **signerat JWT** eller **krypterat JWT**

5. Ange ett unikt värde för **mål gruppen**

6. I fältet **klient-ID** anger du **Azure AD-programmets ID**

7. I fältet **klient hemlighet** anger du den **nyckel** som du skapade för programmet i Azure AD.

8. Valfritt – du kan skapa och använda anpassade anspråk med Microsoft Graph-API: et. Om du väljer att göra det väljer du **Avancerat** och avmarkerar alternativet **begär profil** och uppdaterar alternativ för **användarattribut** . Mer information om hur du använder anpassade anspråk finns i [Använd ett anpassat anspråk](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-headers.md).

9. Välj **Spara**

#### <a name="step-3--create-identity-mapping"></a>Steg 3 – skapa identitets mappning

>[!NOTE]
>Identitets mappning kan användas med mer än ett program om fler än ett program förväntar sig samma data i rubriken.

Följ de här stegen för att skapa identitets mappning:

1. Gå till **Inställningar**  >  **åtkomst till**  >  **identitets mappningar**

2. Välj **Lägg till identitets mappning**

3. Ange ett **namn**

4. Välj identitets mappnings **typ för huvud identitets mappning**

5. I tabellen **Attribute-mappning** anger du de mappningar som krävs. Exempel:

   Attributnamn | Huvudnamn |
   |-------|--------|
   |UPN | x-userprinciplename |
   |e-post   |    x-e-post  |
   |OID   | x-OID  |
   |punkten   |     x-omfattning |
   |AMR    |    x-AMR    |

6. Välj **Spara**

#### <a name="step-4--create-a-site"></a>Steg 4 – skapa en plats

>[!NOTE]
>I vissa konfigurationer är det möjligt att en plats kan innehålla fler än ett program. En plats kan användas med mer än ett program, där det är lämpligt.

Följ de här stegen för att skapa en webbplats:

1. Gå till **huvud**  >  **platser**

2. Välj **Lägg till webbplats**

3. Ange ett **namn** för platsen

4. Ange plats **målet**. Målet är värd namnet: port paret för den server som är värd för programmet. Ange inte sökvägen för programmet i det här fältet. Ett program på har till exempel https://mysite:9999/AppName ett mål värde för min webbplats: 9999

5. Ange om målet förväntar sig säkra anslutningar.

6. Om målet förväntar sig säkra anslutningar ska du ange den betrodda certifikat gruppen som **betrodd**.

7. Välj **Spara**

#### <a name="step-5--create-an-application"></a>Steg 5 – skapa ett program

Följ de här stegen för att skapa ett program i PingAccess för varje program i Azure som du vill skydda.

1. Gå till **huvud**  >  **program**

2. Välj **Lägg till program**

3. Ange ett **namn** för programmet

4. Du kan också ange en **Beskrivning** av programmet

5. Ange **kontext roten** för programmet. Till exempel har ett program på https://mysite:9999/AppName en kontext rot för/APPNAME. Kontext roten måste börja med ett snedstreck (/), får inte sluta med ett snedstreck (/), och kan vara mer än en nivå djup, till exempel/Apps/MyApp.

6. Välj den **virtuella värd** som du har skapat

   >[!NOTE]
   >Kombinationen av virtuell värd och kontext-rot måste vara unik i PingAccess.

7. Välj den **webbsession** som du har skapat

8. Välj den **plats** som du skapade som innehåller programmet

9. Välj den **identitets mappning** som du skapade

10. Välj **aktive rad** för att aktivera platsen när du sparar

11. Välj **Spara**

### <a name="configure-the-pingfederate-authentication-policy"></a>Konfigurera PingFederate för autentisering

Konfigurera PingFederate för att federera till flera IDP: er som tillhandahålls av Azure AD B2C klienter

1. Skapa ett kontrakt för att överbrygga attributen mellan IDP: er och SP. Mer information finns i [avtal för Federations hubb och autentiseringsprincip](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html). Du behöver troligen bara ett kontrakt om inte SP kräver en annan uppsättning attribut från varje IdP.

2. För varje IdP skapar du en IdP-anslutning mellan IdP och PingFederate, Federations navet som SP.

3. I fönstret **Mappning av målnod** lägger du till tillämpliga princip kontrakt för autentisering i IDP-anslutningen.

4. I fönstret **väljare** konfigurerar du en väljare för autentisering. Se till exempel en instans av det **första kortet identifierare** för att mappa varje IDP till motsvarande IDP-anslutning i en autentiseringsprincip.

5. Skapa en SP-anslutning mellan PingFederate, Federations navet som IdP och SP.

6. Lägg till motsvarande policy kontrakt för autentisering till SP-anslutningen i **mappnings fönstret för autentiserings källa** .

7. Arbeta med varje IdP för att ansluta till PingFederate, Federations navet som SP.

8. Arbeta med SP för att ansluta till PingFederate, Federations navet som IdP.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
