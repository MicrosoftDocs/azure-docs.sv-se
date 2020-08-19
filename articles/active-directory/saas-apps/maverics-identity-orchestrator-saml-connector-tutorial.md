---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Maverics Identity Orchestrator SAML Connector | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: ec5368427f50f548be965bb883683c859759bbf3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518954"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maverics-identity-orchestrator-saml-connector"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Maverics Identity Orchestrator SAML Connector

## <a name="introduction"></a>Introduktion

Strata är ett enkelt sätt att integrera lokala program med Azure AD för autentisering och åtkomst kontroll.

Den här guiden hjälper dig att konfigurera Maverics Identity Orchestrator &trade; till:
* Migrera användare stegvis från ett lokalt identitets system till Azure AD under inloggningen till ett äldre lokalt program.
* Dirigera inloggnings förfrågningar från en äldre webb åtkomst hanterings produkt, till exempel CA SiteMinder eller Oracle Access Manager till Azure AD.
* Autentisera användare till lokala program som skyddas med HTTP-huvuden eller patentskyddade sessionscookies efter autentisering av användaren mot Azure AD.

Strata tillhandahåller program vara som distribuerar lokalt eller i molnet för att upptäcka, ansluta och dirigera mellan identitets leverantörer för att skapa distribuerad identitets hantering för Hybrid-och multicloud-företag.

Den här självstudien visar hur du migrerar ett lokalt webb program som skyddas av en äldre webb åtkomst hanterings produkt (CA SiteMinder) för att använda Azure AD för autentisering och åtkomst kontroll.
1. Installera Maverics Identity Orchestrator&trade;
2. Registrera ditt företags program med Azure AD och konfigurera det att använda Maverics Azure AD SAML Zero Code Connector &trade; för SAML-baserad SSO.
3. Integrera Maverics med SiteMinder och LDAP-användarkontot.
4. Konfigurera Azure Key Vault och konfigurera Maverics så att det används som sin hemliga hanterings leverantör.
5. Demonstrera användarmigrering och abstraktion av sessioner med hjälp av Maverics för att ge åtkomst till ett lokalt Java-webbprogram.

Mer information om installation och konfiguration finns på https://strata.io/docs

## <a name="prerequisites"></a>Förutsättningar

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
- Maverics Identity Orchestrator SAML Connector enkel inloggning (SSO) aktive rad prenumeration. För att få Maverics-programvaran kontaktar du sales@strata.io

## <a name="install-maverics-identity-orchestratortrade"></a>Installera Maverics Identity Orchestrator&trade;

För att komma igång med installationen av Maverics-identiteten kan du läsa installationsinstruktioner på https://strata.io/docs

## <a name="system-requirements"></a>Systemkrav
### <a name="supported-operating-systems"></a>Operativsystem som stöds
* RHEL 7+
* CentOS 7+

### <a name="dependencies"></a>Beroenden
* system

## <a name="installation"></a>Installation

1. Hämta det senaste Maverics RPM-paketet. Kopiera paketet till systemet där du vill installera Maverics-programvaran.

2. Installera Maverics-paketet och ersätt ditt fil namn i stället för `maverics.rpm` .

    `sudo rpm -Uvf maverics.rpm`

3. När du har installerat Maverics kommer den att köras som en tjänst under `systemd` . Kör följande kommando för att kontrol lera att tjänsten körs.

    `sudo systemctl status maverics`

Som standard installeras Maverics i `/usr/local/bin` katalogen.

När du har installerat Maverics `maverics.yaml` skapas standard filen i `/etc/maverics` katalogen. Innan du redigerar konfigurationen för att inkludera `workflows` och `connectors` , kommer konfigurations filen att se ut så här:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="config-options"></a>Konfigurations alternativ
### <a name="version"></a>Version
`version`Fältet deklarerar vilken version av konfigurations filen som används. Om inget anges används den senaste konfigurations versionen.

```yaml
version: 0.1
```
### <a name="listen-address"></a>Lyssna på adress
`listenAddress` deklarerar vilken adress som Orchestrator lyssnar på. Om värd avsnittet i adressen är tomt, kommer Orchestrator att lyssna på alla tillgängliga unicast-och anycast-IP-adresser för det lokala systemet. Om Port avsnittet i adressen är tomt väljs ett port nummer automatiskt.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

`tls`Fältet deklarerar en karta över säkerhets objekt i transport skiktet. TLS-objekten kan användas av anslutningar och Orchestrator-servern. Information om alla tillgängliga TLS-alternativ finns i `transport` paket dokumentationen.

Microsoft Azure kräver kommunikation via TLS när du använder SAML-baserad SSO, hittar du mer information [här](https://letsencrypt.org/getting-started/) för att generera dina certifikat.

`maverics`Nyckeln är reserverad för Orchestrator-servern. Alla andra nycklar är tillgängliga och kan användas för att mata in ett TLS-objekt i en specifik anslutning.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Ta med filer

`connectors` och `workflows` kan definieras i egna, separata konfigurationsfiler och refereras till med hjälp av `maverics.yaml` `includeFiles` följande exempel.
```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

I den här självstudien används en enda `maverics.yaml` konfigurations fil.

## <a name="using-azure-key-vault-as-your-secrets-provider"></a>Använda Azure Key Vault som din hemligheter-Provider

### <a name="secret-management"></a>Hemlig hantering

Maverics kan integreras med olika hemliga hanterings lösningar för att läsa in hemligheter. De aktuella integreringarna innehåller en fil, Hashicorp-valv och Azure Key Vault. Om ingen lösning för hemlig hantering har angetts kommer Maverics att läsa in hemligheter i klartext från `maverics.yaml` .
Om du vill deklarera ett värde som en hemlighet i en `maverics.yaml` konfigurations fil, radbryter du hemligheten med vinkelparenteser:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="file"></a>Fil

Om du vill läsa in hemligheter från en fil lägger du till miljövariabeln `MAVERICS_SECRET_PROVIDER` i filen  `/etc/maverics/maverics.env` med:

`MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

Starta sedan om maverics-tjänsten: `sudo systemctl restart maverics`

`secrets.yaml`Fil innehållet kan fyllas i med valfritt antal `secrets` .
```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="azure-key-vault"></a>Azure Key Vault

Följande steg visar hur du konfigurerar en Azure Key Vault med hjälp av [Azure Portal](https://portal.azure.com) eller med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest):

1. [Logga in](https://portal.azure.com) med Azure Portal eller med CLI-kommandot:
    ```shell
    az login
    ```

2. [Skapa ett nytt valv](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)eller Använd CLI-kommandot:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

3. [Lägg till hemligheterna i Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)eller med ett CLI-kommando:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

4. [Registrera ett program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)eller med ett CLI-kommando:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

5. [Auktorisera ett program för att använda en hemlighet](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)eller med ett CLI-kommando:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

Om du vill läsa in hemligheter från Azure-valv ställer du in miljövariabeln `MAVERICS_SECRET_PROVIDER` i filen `/etc/maverics/maverics.env` med autentiseringsuppgifterna som finns i azure-credentials.jspå filen med följande mönster: `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

Starta sedan om maverics-tjänsten: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Konfigurera ditt program i Azure AD för SAML-baserad SSO

1. I Azure Active Directory klient navigerar du till `Enterprise applications` , söker efter `Maverics Identity Orchestrator SAML Connector` och väljer den.

2. På Maverics Identity Orchestrator SAML Connector | Sidan Egenskaper, Ställ in `User assignment required?` på Nej om du vill att programmet ska fungera för nyligen migrerade användare.

3. På Maverics Identity Orchestrator SAML Connector | Översikts sida väljer `Setup single sign-on` du och väljer `SAML` .

4. På Maverics Identity Orchestrator SAML Connector | SAML-baserad inloggning, redigera den grundläggande SAML-konfigurationen.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

5. Ange `Entity ID` en URL för inmatning med följande mönster: `https://<SUBDOMAIN>.maverics.org` . `Entity ID`Måste vara unik i alla appar i klienten. Spara värdet som anges här för att inkluderas i konfigurationen av Maverics.

6. Ange svars-URL med följande mönster: `https://<AZURECOMPANY.COM>/<MY_APP>/` . 

7. Ange inloggnings-URL med följande mönster: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` och klicka på Spara.

8. Gå till avsnittet SAML-signeringscertifikat och klicka på knappen Kopiera för att kopiera URL: en för appens Federations-metadata och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

## <a name="maverics-identity-orchestrator-azure-ad-saml-connector-configuration"></a>Konfiguration av Maverics Identity Orchestrator Azure AD SAML Connector

Maverics Identity Orchestrator Azure AD Connector stöder: 
- OpenID Connect
- SAML Connect 

1. Ange om du vill aktivera SAML-baserad SSO `authType: saml` .

1. Skapa värdet för `samlMetadataURL` : `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`

1. Definiera nu den URL som Azure omdirigerar tillbaka till i din app när de har loggat in med sina Azure-autentiseringsuppgifter.
`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. Kopiera värdet från EntityID som kon figurer ATS ovan: `samlEntityID: https://<SUBDOMAIN>.maverics.org`

1. Kopiera värdet från svars-URL: en som Azure AD ska använda för att publicera SAML-svaret.
`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. Generera en JWT-signeringscertifikat som används för att skydda Maverics-information om identitetens Orchestrator &trade; -session med hjälp av [openssl-verktyget](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Kopiera svaret till `jwtSigningKey` konfigurations egenskapen: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`

## <a name="attributes-and-attribute-mapping"></a>Attribut och attributmappning
Attributmappning används för att definiera mappningen av användarattribut från en lokal käll användar katalog till Azure AD när användare är etablerade.

Attribut avgör vilka användar data som kan returneras till ett program i ett anspråk, skickas till sessionscookies eller skickas till programmet i http-huvudvariabler.

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector-yaml"></a>Konfigurera Maverics Identity Orchestrator Azure AD SAML Connector yaml

Din Maverics Identity Orchestrator Azure AD Connector-konfiguration kommer att se ut så här:
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-azure-ad"></a>Migrera användare till Azure AD

Följ den här konfigurationen för att stegvis migrera användare från en webb åtkomst hanterings produkt, till exempel CA SiteMinder, Oracle Access Manager eller IBM Tivoli; en LDAP-katalog; eller en SQL-databas.

## <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Konfigurera dina program behörigheter i Azure AD för att skapa användare

1. I Azure Active Directory klient navigerar du till `App registrations` och väljer programmet "Maverics Identity Orchestrator SAML Connector".

2. På Maverics Identity Orchestrator SAML Connector | Certifikat & hemligheter väljer du `New client secret` och väljer sedan alternativet förfallo datum. Klicka på knappen Kopiera för att kopiera hemligheten och spara den på din dator.

3. På Maverics Identity Orchestrator SAML Connector | API-behörigheter väljer du `Add permission` och sedan på begär API-behörigheter väljer `Microsoft Graph` och sedan `Application permissions` . Välj på nästa skärm `User.ReadWrite.All` och välj sedan `Add permissions` . Detta leder dig tillbaka till API-behörigheter, där du väljer `Grant admin consent` .


## <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-for-user-migration"></a>Konfigurera Maverics Identity Orchestrator SAML Connector yaml för användarmigrering

Om du vill aktivera arbets flödet för användarmigrering lägger du till följande ytterligare egenskaper i konfigurations filen:
1. Ange URL: en för Azure Graph: `graphURL: https://graph.microsoft.com`
1. Ange URL för OAuth-token enligt mönstret: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`
1. Ange klient hemligheten som genereras ovan: `oauthClientSecret: <CLIENT SECRET>`


Din slutgiltiga Maverics Identity Orchestrator Azure AD Connector-konfiguration ser ut så här:
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-siteminder"></a>Konfigurera Maverics Zero Code Connector &trade; för SiteMinder

SiteMinder-anslutningen används för att migrera användare till Azure AD och för att logga in användare på äldre program som skyddas av SiteMinder med hjälp av de nyligen skapade Azure AD-identiteterna och autentiseringsuppgifterna.

I den här självstudien har SiteMinder kon figurer ATS för att skydda det äldre programmet med formulärbaserad autentisering och med hjälp av `SMSESSION` cookien. Om du vill integrera med en app som använder autentisering och session via HTTP-huvuden måste du lägga till huvudemulerings konfigurationen i anslutningen.

Det här exemplet mappar `username` attributet till `SM_USER` http-huvudet:
```yaml
  headers:
    SM_USER: username
```

Ange `proxyPass` till den plats som begär proxy till. Detta är vanligt vis värden för det skyddade programmet.

`loginPage` ska matcha URL: en för det inloggnings formulär som för närvarande används av SiteMinder när användare omdirigeras för autentisering.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-ldap"></a>Konfigurera Maverics Zero Code Connector &trade; för LDAP

När program skyddas av en WAM-produkt som SiteMinder lagras användar identiteter och attribut vanligt vis i en LDAP-katalog.

Den här anslutnings konfigurationen visar hur du ansluter till LDAP-katalogen som har kon figurer ATS som användar Arkiv för SiteMinder så att rätt användar profil information kan samlas in under migreringen av arbets flödet och en motsvarande användare kan skapas i Azure AD.

* `baseDN` anger platsen i katalogen som LDAP-sökningen ska utföras mot.

* `url` är adressen och porten för den LDAP-server som du vill ansluta till.

* `serviceAccountUsername` är det användar namn som används för att ansluta till LDAP-servern, vanligt vis uttryckt som ett DN-DN, till exempel `CN=Directory Manager` .

* `serviceAccountPassword` lösen ordet som används för att ansluta till LDAP-servern. Det här värdet lagras i Azure Key Vault-instansen som kon figurer ATS tidigare.  

* `userAttributes` definierar listan över användare relaterade attribut att fråga efter. Dessa attribut mappas senare till motsvarande Azure AD-attribut.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

## <a name="configure-the-migration-workflow"></a>Konfigurera arbets flödet för migrering

Arbets flödes konfigurationen för migrering avgör hur Maverics kommer att migrera användare från SiteMinder/LDAP till Azure AD.

Det här arbets flödet:
- Använder SiteMinder-anslutningen för att proxya SiteMinder-inloggningen. Användarautentiseringsuppgifter verifieras genom SiteMinder-autentisering och skickas sedan till efterföljande steg i arbets flödet.
- Hämtar attribut för användar profil från användar arkivet för SiteMinder.
- Gör en begäran till Microsoft Graph API för att skapa användaren i din Azure AD-klient.

Steg:
1. Ge arbets flödet ett namn, t. ex. SiteMinder till Azure AD-migrering.
2. Ange `endpoint` , som är en HTTP-sökväg där arbets flödet exponeras, som utlöser arbets `actions` flödet som svar på begär Anden. `endpoint`Motsvarar vanligt vis appen som proxys, t. ex. `/my_app` . Värdet måste innehålla både inledande och avslutande snedstreck.
3. Lägg till lämplig `actions` i arbets flödet.
    - Definiera `login` metoden för SiteMinder-anslutningen. Koppling svärdet måste matcha name-värdet i anslutnings konfigurationen.
     - Definiera `getprofile` metoden för LDAP-anslutningen.
     - Definiera `createuser` för AzureAD-anslutningen.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>Kontrol lera arbets flödet för migrering

1. Om Maverics-tjänsten inte redan körs startar du den genom att köra följande kommando: `sudo systemctl start maverics`

2. Navigera till inloggnings webb adressen för proxy: `http://host.company.com/my_app` .
3. Ange användarautentiseringsuppgifter som används för att logga in på programmet medan det skyddas av SiteMinder.
4. Navigera till Start > användare | Alla användare kan verifiera att användaren har skapats i Azure AD-klienten.  

## <a name="configure-the-session-abstraction-workflow"></a>Konfigurera arbets flödet för abstraktion av sessioner

Arbets flödet för sessions-abstraktion flyttar autentisering och åtkomst kontroll för det äldre lokala webb programmet till Azure AD.

Azure-anslutaren använder `login` metoden för att omdirigera användaren till inloggnings-URL: en, förutsatt att ingen session finns.

När det har autentiserats skickas sessionstoken som skapats som ett resultat till Maverics och SiteMinder-kopplingens `emulate` metod används för att emulera den cookie-baserade sessionen och/eller den huvudbaserade sessionen och sedan dekorera begäran med eventuella ytterligare attribut som krävs av programmet.

1. Ge arbets flödet ett namn, t. ex. SiteMinder.
2. Ange `endpoint` , som motsvarar appen som proxy. Värdet måste innehålla både inledande och avslutande snedstreck, t. ex. `/my_app/` .
3. Lägg till lämplig `actions` i arbets flödet.
    - Definiera `login` metoden för Azure-anslutaren. `connector`Värdet måste matcha `name` värdet i anslutnings konfigurationen.
    - Definiera `emulate` metoden för SiteMinder-anslutningen.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>Verifiera arbets flödet för abstraktion av sessioner

1. Navigera till programmets URL för proxy: `https://<AZURECOMPANY.COM>/<MY_APP>` . Användaren kommer att omdirigeras till inloggnings sidan för proxy.
2. Ange autentiseringsuppgifter för Azure AD-användare.
3. Användaren ska omdirigeras till programmet som om det autentiseras direkt av SiteMinder.
