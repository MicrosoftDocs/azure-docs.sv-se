---
title: Självstudie för att konfigurera Azure Active Directory B2C med Strata
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar Azure AD B2C-autentisering med whoIam för användar verifiering
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 69355b58e36f435b5b5dbe94af72d55d9806b156
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557170"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>Självstudie för att utöka Azure AD B2C för att skydda lokala program med hjälp av Strata

I den här självstudien får du lära dig att integrera Azure Active Directory (AD) B2C med Strata [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/).
Maverics Identity Orchestrator utökar Azure AD B2C för att skydda lokala program. Den ansluter till ett identitets system, transparent migrerar användare och autentiseringsuppgifter, synkroniserar principer och konfigurationer och abstraktar autentisering och sessionshantering. Att använda Strata företag kan snabbt övergå från äldre till Azure AD B2C utan att behöva skriva om program. Lösningen har följande fördelar:

- **Kund-Single Sign-On (SSO) till lokala hybrid appar**: Azure AD B2C stöder kundsso med Maverics Identity Orchestrator. Användarna loggar in med sina konton som finns i Azure AD B2C eller sociala identitets leverantör (IdP). Maverics utökar SSO till appar som tidigare har skyddats av gamla identitets system som Symantec SiteMinder.

- **Utöka standardbaserad SSO till appar utan att skriva om dem**: Använd Azure AD B2C för att hantera användar åtkomst och aktivera SSO med Maverics Identity Orchestrator SAML eller OIDC-anslutningar.

- **Enkel konfiguration**: Azure AD B2C ger ett enkelt steg-för-steg-användar gränssnitt för att ansluta Maverics Identity Orchestrator SAML eller OIDC connectors till Azure AD B2C.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- En [Azure AD B2C klient](./tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

- En instans av [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) att lagra hemligheter som används av Maverics Identity Orchestrator. Den används för att ansluta till Azure AD B2C eller andra attribut leverantörer, till exempel en LDAP-katalog (Lightweight Directory Access Protocol) eller en databas.

- En instans av [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/) som är installerad och körs på en virtuell Azure-dator eller den lokala server du väljer. Om du vill ha mer information om hur du hämtar program varan och har åtkomst till installations-och konfigurations dokumentationen kontaktar du [Strata](https://www.strata.io/contact/)

- Ett lokalt program som du kommer över från ett äldre identitets system till Azure AD B2C.

## <a name="scenario-description"></a>Scenariobeskrivning

Strata Maverics-integrering innehåller följande komponenter:

- **Azure AD B2C**: den auktoriserade server som ansvarar för att verifiera användarens autentiseringsuppgifter. Autentiserade användare kan komma åt lokala appar med hjälp av ett lokalt konto som lagras i Azure AD B2C-katalogen.

- **En extern social-eller företags IDP**: kan vara valfri OpenID Connect-Provider, Facebook, Google eller GitHub. Se information om hur du använder [externa IDP: er](./technical-overview.md#external-identity-providers) med Azure AD B2C.  

- **Strata för Maverics-identitet**: tjänsten som dirigerar användar inloggning och skickar transparent identitet till appar via HTTP-huvuden.

I följande arkitektur diagram visas implementeringen.

![Bild visar arkitekturen för en Azure AD B2C-integrering med Strata Maverics för att ge åtkomst till hybrid program](./media/partner-strata/strata-architecture-diagram.png)

| Steg | Description |
|:-------|:---------------|
| 1. | Användaren gör en begäran om åtkomst till det lokala värd programmet. Maverics Identity Orchestrator-proxyservrar begär Anden som gjorts av användaren till programmet.|
| 2. | Orchestrator kontrollerar användarens autentiserings tillstånd. Om den inte får en sessionstoken, eller om den angivna sessionstoken är ogiltig, skickar den användaren till Azure AD B2C för autentisering.|
| 3. | Azure AD B2C skickar autentiseringsbegäran till den konfigurerade sociala IdP.|
| 4. | IdP utmanar användaren om autentiseringsuppgifter. Beroende på IdP kan användaren behöva utföra Multi-Factor Authentication (MFA).|
| 5. | IdP skickar autentiserings svaret tillbaka till Azure AD B2C. Användaren kan eventuellt skapa ett lokalt konto i Azure AD B2C-katalogen under det här steget.|
| 6. | Azure AD B2C skickar Användarbegäran till den slut punkt som anges under Orchestrator-appens registrering i Azure AD B2C klient organisationen.|
| 7. | Orchestrator utvärderar åtkomst principer och beräknar attributvärden som ska ingå i HTTP-huvuden som vidarebefordras till appen. Under det här steget kan Orchestrator anropa ytterligare attribut för att hämta den information som krävs för att ange huvud värden korrekt. Orchestrator anger huvud värden och skickar begäran till appen.|
| 8. | Användaren är nu autentiserad och har åtkomst till appen.|

## <a name="get-maverics-identity-orchestrator"></a>Hämta Maverics Identity Orchestrator
Kontakta [Strata](https://www.strata.io/contact/)för att få den program vara som du använder för att integrera din äldre lokala app med Azure AD B2C. När du har skaffat program varan följer du stegen nedan för att fastställa Orchestrator-särskilda krav och utför nödvändiga installations-och konfigurations steg.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Konfigurera Azure AD B2C klient

1. **Registrera ditt program**

   a. [Registrera Orchestrator som ett program](./tutorial-register-applications.md?tabs=app-reg-ga) i Azure AD B2C klient organisationen.
   >[!Note]
   >Du behöver klient namnet och identifieraren, klient-ID, klient hemlighet, konfigurerade anspråk och omdirigerings-URI senare när du konfigurerar Orchestrator-instansen.

   b. Ge Microsoft MS Graph API-behörigheter till dina program. Programmet behöver följande behörigheter: `offline_access` , `openid` .

   c. Lägg till en omdirigerings-URI för programmet. Denna URI matchar `oauthRedirectURL` parametern för Orchestrator: s Azure AD B2C anslutnings konfiguration, till exempel `https://example.com/oidc-endpoint` .

2. **Skapa ett användar flöde**: skapa ett [användar flöde för registrering och inloggning](./tutorial-create-user-flows.md).

3. **Lägg till en IDP**: Välj att logga in din användare med antingen ett lokalt konto eller en social-eller företags [IDP](./add-identity-provider.md).

4. **Definiera** användarattribut: definiera de attribut som ska samlas in vid registreringen.

5. **Ange program anspråk**: Ange de attribut som ska returneras till programmet via din Orchestrator-instans. Orchestrator använder attribut från anspråk som returneras av Azure AD B2C och kan hämta ytterligare attribut från andra anslutna identitets system som LDAP-kataloger och databaser. Dessa attribut anges i HTTP-huvuden och skickas till det överordnade lokala programmet.

## <a name="configure-maverics-identity-orchestrator"></a>Konfigurera Maverics Identity Orchestrator

I följande avsnitt vägleder vi dig genom de steg som krävs för att konfigurera Orchestrator-instansen. Kontakta [Strata](https://www.strata.io/contact/)om du behöver ytterligare support och dokumentation.

### <a name="maverics-identity-orchestrator-server-requirements"></a>Krav för Maverics-identitetens Orchestrator-Server

Du kan köra Orchestrator-instansen på vilken server som helst, oavsett om den är lokal eller i en offentlig moln infrastruktur per leverantör, till exempel Azure, AWS eller GCP.

- OS: REHL 7,7 eller senare, CentOS 7 +

- Disk: 10 GB (liten)

- Minne: 16 GB

- Portar: 22 (SSH/SCP), 443, 80

- Rot åtkomst för installation/administrativa uppgifter

- Maverics Identity Orchestrator körs som användare `maverics` under `systemd`

- Nätverket utgångs från den server som är värd för Maverics identitet med möjlighet att ansluta till din Azure AD-klient.

### <a name="install-maverics-identity-orchestrator"></a>Installera Maverics Identity Orchestrator

1. Hämta det senaste Maverics RPM-paketet. Placera paketet i systemet som du vill installera Maverics på. Om du kopierar filen till en fjärran sluten värd är [SCP](https://www.ssh.com/ssh/scp/) ett användbart verktyg.

2. Om du vill installera Maverics-paketet kör du följande kommando och ersätter ditt fil namn i stället för `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Som standard installeras Maverics i `/usr/local/bin` katalogen.

3. När du har installerat Maverics kommer den att köras som en tjänst under `systemd` .  Kör följande kommando för att kontrol lera att Maverics-tjänsten körs:

   `sudo service maverics status`

  Om Orchestrator-installationen lyckades bör du se ett meddelande som liknar detta:

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Om Maverics-tjänsten inte startar kör du följande kommando för att undersöka problemet:

   `journalctl --unit=maverics.service --reverse`

   Den senaste logg posten kommer att visas i början av utdata.

När du har installerat Maverics `maverics.yaml` skapas standard filen i `/etc/maverics` katalogen.

Konfigurera din Orchestrator för att skydda programmet. Integrera med Azure AD B2C, lagra och hämta hemligheter från [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9). Definiera platsen där Orchestrator ska läsa konfigurationen från.

### <a name="supply-configuration-using-environment-variables"></a>Ange konfiguration med miljövariabler

Ange konfiguration för dina Orchestrator-instanser via miljövariabler.

`MAVERICS_CONFIG`

Den här miljövariabeln talar om för Orchestrator-instansen vilka YAML som ska användas och var de finns under start eller omstart. Ange miljövariabeln i `/etc/maverics/maverics.env` .

### <a name="create-the-orchestrators-tls-configuration"></a>Skapa Orchestrators TLS-konfiguration

`tls`Fältet i `maverics.yaml` deklarerar transport skiktets säkerhetskonfigurationer som används av Orchestrator-instansen. Kopplingar kan använda TLS-objekt och Orchestrator-servern.

`maverics`Nyckeln är reserverad för Orchestrator-servern. Alla andra nycklar är tillgängliga och kan användas för att mata in ett TLS-objekt i en specifik anslutning.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>Konfigurera Azure AD B2C-anslutningen

Dirigerare använder kopplingar för att integrera med autentiserings-och attribut-providers. I det här fallet använder den här Dirigerings-gatewayen Azure AD B2C-anslutningen som en provider för autentisering och attribut. Azure AD B2C använder det sociala IdP för autentisering och sedan agerar som en Attribute Provider till Orchestrator och skickar attribut i anspråk som anges i HTTP-huvuden.  

Den här anslutningens konfiguration motsvarar appen som är registrerad i Azure AD B2C klient organisationen.

1. Kopiera klient-ID, hemlighet och omdirigerings-URI från appens konfiguration i din klient organisation.

2. Ge din koppling ett namn, visas här som `azureADB2C` och ange `type` att kopplingen ska vara `azure` . Anteckna kopplings namnet eftersom det här värdet används i andra konfigurations parametrar nedan.

3. För den här integrationen `authType` ska ska vara inställd på `oidc` .

4. Ange det klient-ID som du kopierade i steg 1 som värde för `oauthClientID` parametern.

5. Ange den klient hemlighet som du kopierade i steg 1 som värde för `oauthClientSecret` parametern.

6. Ange omdirigerings-URI: n som du kopierade i steg 1 som `oauthRedirectURL` parameter värde.

7. Azure AD B2C OIDC-anslutningsprogrammet använder den välkända OIDC-slutpunkten för att upptäcka metadata, inklusive URL: er och signerings nycklar. Ange värdet för `oidcWellKnownURL` klientens slut punkt.

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>Definiera Azure AD B2C som autentiseringsprovider

En autentiseringsprovider bestämmer hur autentiseringen ska utföras för en användare som inte har uppvisat en giltig session som en del av appens resurs förfrågan. Konfigurationen i Azure AD B2C klient organisationen bestämmer hur du ska anropa en användare för autentiseringsuppgifter och tillämpa ytterligare autentiseringsprinciper. Om du till exempel vill kräva en andra faktor för att slutföra autentiseringsprocessen och bestämma vilka anspråk som ska returneras till Orchestrator app gateway när autentiseringen har lyckats.

Värdet för `authProvider` måste matcha ditt anslutnings `name` värde.

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>Skydda din lokala app med en Orchestrator app Gateway

Konfiguration av Orchestrator-programgatewayen deklarerar hur Azure AD B2C bör skydda ditt program och hur användarna ska ha åtkomst till appen.

1. Skapa ett namn för din app-Gateway. Du kan använda ett eget namn eller ett fullständigt kvalificerat värdnamn som identifierare för din app.

2. Ange `location` . Exemplet här använder appens rot `/` , men kan vara valfri URL-sökväg till programmet.

3. Definiera det skyddade programmet i `upstream` med hjälp av värden: port konvention: `https://example.com:8080` .

4. Ange värden för fel och otillåtna sidor.

5. Definiera de HTTP-huvud namn och attributvärden som måste tillhandahållas programmet för att upprätta autentisering och kontrol lera åtkomsten till appen. Huvud namn är godtyckliga och motsvarar vanligt vis appens konfiguration. Attributvärden har ett namn som anger den anslutning som tillhandahåller dem. I exemplet nedan är de värden som returneras från Azure AD B2C föregås av anslutnings namnet `azureADB2C` där suffixet är namnet på attributet som innehåller det obligatoriska värdet, till exempel `given_name` .

6. Ange de principer som ska utvärderas och tillämpas. Tre åtgärder har definierats: `allowUnauthenticated` , `allowAnyAuthenticated` och `allowIfAny` . Varje åtgärd är associerad med en `resource` och principen utvärderas för den `resource` .

>[!NOTE]
>Både `headers` och `policies` använder Java Script-eller GoLang service-tillägg för att implementera godtycklig logik som avsevärt förbättrar standard funktionerna.

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>Använd Azure Key Vault som din hemligheter-Provider

Det är viktigt att skydda de hemligheter som används i Orchestrator för att ansluta till Azure AD B2C och andra identitets system. Maverics kommer som standard att läsa in hemligheter i vanlig text av `maverics.yaml` , men i den här självstudien använder du Azure Key Vault som hemligheter-Provider.

Följ instruktionerna för att [skapa en ny Key Vault](../key-vault/secrets/quick-create-portal.md) som Orchestrator-instansen kommer att använda som en hemligheter-Provider. Lägg till dina hemligheter i valvet och anteckna för `SECRET NAME` varje hemlighet. Till exempel `AzureADB2CClientSecret`.

Om du vill deklarera ett värde som en hemlighet i en `maverics.yaml` konfigurations fil, radbryter du hemligheten med vinkelparenteser:

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

Värdet som anges inom vinkelparenteser måste motsvara `SECRET NAME` hemligheten som anges i Azure Key Vault.

Om du vill läsa in hemligheter från Azure Key Vault anger du miljövariabeln `MAVERICS_SECRET_PROVIDER` i filen `/etc/maverics/maverics.env` , med autentiseringsuppgifterna som finns i azure-credentials.jspå filen, med följande mönster:

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>Lägg samman allt

Så här ser Orchestrator-konfigurationen ut när du slutför de konfigurationer som beskrivs ovan.

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>Testa flödet

1. Navigera till den lokala program-URL: en `https://example.com/sonar/dashboard` .

2. Orchestrator ska omdirigera till sidan som du konfigurerade i ditt användar flöde.

3. Välj IdP i listan på sidan.

4. När du har omdirigerats till IdP anger du dina autentiseringsuppgifter som begär Anden, inklusive en MFA-token om det krävs av den IdP.

5. När du har autentiserat bör du omdirigeras till Azure AD B2C, som vidarebefordrar appens begäran till Orchestrator omdirigerings-URI.

6. Orchestrator utvärderar principer, beräknar rubriker och skickar användaren till det överordnade programmet.  

7. Du bör se det begärda programmet.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
