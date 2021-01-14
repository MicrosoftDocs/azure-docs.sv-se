---
title: 'Självstudie: integrera Azure Active Directory enkel inloggning (SSO) med Maverics Identity Orchestrator SAML Connector | Microsoft Docs'
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
ms.openlocfilehash: 31392c1fa3d14d6f1e01a8b302575e9b592e42cd
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183157"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Självstudie: integrera enkel inloggning med Azure AD med Maverics Identity Orchestrator SAML Connector

Strata är ett enkelt sätt att integrera lokala program med Azure Active Directory (Azure AD) för autentisering och åtkomst kontroll.

Den här artikeln beskriver hur du konfigurerar Maverics Identity Orchestrator för att:
* Migrera användare stegvis från ett lokalt identitets system till Azure AD under inloggningen till ett äldre lokalt program.
* Dirigera inloggnings förfrågningar från en äldre webb åtkomst hanterings produkt, till exempel CA SiteMinder eller Oracle Access Manager, till Azure AD.
* Autentisera användare till lokala program som skyddas med hjälp av HTTP-huvuden eller patentskyddade sessionscookies efter autentisering av användaren mot Azure AD.

Strata tillhandahåller program vara som du kan distribuera lokalt eller i molnet. Det hjälper dig att upptäcka, ansluta och dirigera mellan identitets leverantörer för att skapa distribuerad identitets hantering för Hybrid-och multicloud-företag.

Den här självstudien visar hur du migrerar en lokal webbapp som för närvarande skyddas av en äldre webb åtkomst hanterings produkt (CA SiteMinder) för att använda Azure AD för autentisering och åtkomst kontroll. Här är de grundläggande stegen:
1. Installera Maverics Identity Orchestrator.
2. Registrera ditt företags program med Azure AD och konfigurera det att använda Maverics Azure AD SAML Zero Code Connector för SAML-baserad enkel inloggning (SSO).
3. Integrera Maverics med SiteMinder och LDAP-användarkonto (Lightweight Directory Access Protocol).
4. Konfigurera ett Azure Key Vault och konfigurera Maverics så att det används som sin hemliga hanterings leverantör.
5. Demonstrera användarmigrering och abstraktion av sessioner genom att använda Maverics för att ge åtkomst till ett lokalt Java-webbprogram.

Ytterligare installations-och konfigurations anvisningar finns på [Strata-webbplatsen](https://www.strata.io).

## <a name="prerequisites"></a>Krav

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
- En Maverics Identity Orchestrator SAML Connector SSO-aktiverad prenumeration. Kontakta [Strata Sales](mailto:sales@strata.io)om du vill hämta Maverics-programvaran.

## <a name="install-maverics-identity-orchestrator"></a>Installera Maverics Identity Orchestrator

Information om hur du kommer igång med installationen av Maverics-identiteten finns i [installations anvisningarna](https://www.strata.io).

### <a name="system-requirements"></a>Systemkrav
* Operativsystem som stöds
  * RHEL 7+
  * CentOS 7+

* Beroenden
  * system

### <a name="installation"></a>Installation

1. Hämta det senaste Maverics-paketet för Redhat Package Manager (RPM). Kopiera paketet till systemet där du vill installera Maverics-programvaran.

2. Installera Maverics-paketet och ersätt fil namnet i stället för `maverics.rpm` .

    `sudo rpm -Uvf maverics.rpm`

3. När du har installerat Maverics kommer den att köras som en tjänst under `systemd` . Verifiera att tjänsten körs genom att köra följande kommando:

    `sudo systemctl status maverics`

Som standard installeras Maverics i katalogen */usr/local/bin* .

När du har installerat Maverics skapas standard filen *Maverics. yaml* i */etc/maverics* -katalogen. Innan du redigerar konfigurationen för att inkludera `workflows` och `connectors` , kommer konfigurations filen att se ut så här:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>Konfigurationsalternativ
### <a name="version"></a>Version
`version`Fältet deklarerar vilken version av konfigurations filen som används. Om versionen inte anges används den senaste konfigurations versionen.

```yaml
version: 0.1
```
### <a name="listenaddress"></a>listenAddress
`listenAddress` deklarerar vilken adress Orchestrator lyssnar på. Om värd avsnittet i adressen är tomt, kommer Orchestrator att lyssna på alla tillgängliga unicast-och anycast-IP-adresser för det lokala systemet. Om Port avsnittet i adressen är tomt väljs ett port nummer automatiskt.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

`tls`Fältet deklarerar en karta över Transport Layer Security-objekt (TLS). TLS-objekten kan användas av anslutningar och Orchestrator-servern. Information om alla tillgängliga TLS-alternativ finns i `transport` paket dokumentationen.

Microsoft Azure kräver kommunikation via TLS när du använder SAML-baserad SSO. Information om hur du skapar certifikat finns på webbplatsen för att [kryptera](https://letsencrypt.org/getting-started/).

`maverics`Nyckeln är reserverad för Orchestrator-servern. Alla andra nycklar är tillgängliga och kan användas för att mata in ett TLS-objekt i en specifik anslutning.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Inkludera filer

Du kan definiera `connectors` och `workflows` i sina egna, separata konfigurationsfiler och referera till dem i filen *maverics. yaml* med hjälp av `includeFiles` , enligt följande exempel:

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

I den här självstudien används en enda konfigurations fil för *maverics. yaml* .

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>Använd Azure Key Vault som din hemligheter-Provider

### <a name="manage-secrets"></a>Hantera hemligheter

För att läsa in hemligheter kan Maverics integreras med olika hemliga hanterings lösningar. De aktuella integreringarna inkluderar en fil, Hashicorp-valv och Azure Key Vault. Om ingen lösning för hemlig hantering anges, Maverics standardinställningarna att läsa in hemligheter i klartext från filen *Maverics. yaml* .

Om du vill deklarera ett värde som en hemlighet i en *maverics. yaml* -konfigurationsfil, omger du hemligheten med vinkelparenteser:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>Läsa in hemligheter från en fil

1. Om du vill läsa in hemligheter från en fil lägger du till miljövariabeln `MAVERICS_SECRET_PROVIDER` i */etc/maverics/maverics.env* -filen genom att använda:

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. Starta om Maverics-tjänsten genom att köra:

   `sudo systemctl restart maverics`

Innehållet i filen *hemligheter. yaml* kan fyllas i med valfritt antal `secrets` .

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>Konfigurera ett Azure Key Vault

Du kan konfigurera ett Azure Key Vault med hjälp av antingen Azure Portal eller Azure CLI.

**Använda Azure-portalen**
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. [Skapa ett nytt nyckel valv](../../key-vault/general/quick-create-portal.md).
1. [Lägg till hemligheterna i nyckel valvet](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).
1. [Registrera ett program med Azure AD](../develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
1. [Auktorisera ett program för att använda en hemlighet](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).

**Använda Azure CLI**

1. Öppna [Azure CLI](/cli/azure/install-azure-cli)och ange följande kommando:

    ```azurecli
    az login
    ```

1. Skapa ett nytt nyckel valv genom att köra följande kommando:
    ```azurecli
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. Lägg till hemligheterna i nyckel valvet genom att köra följande kommando:
    ```azurecli
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. Registrera ett program med Azure AD genom att köra följande kommando:
    ```azurecli
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. Ge ett program behörighet att använda en hemlighet genom att köra följande kommando:
    ```azurecli
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. Om du vill läsa in hemligheter från ditt Azure Key Vault ställer du in miljövariabeln `MAVERICS_SECRET_PROVIDER` i */etc/maverics/maverics.env* -filen med hjälp av autentiseringsuppgifterna som finns i *azure-credentials.jspå* filen i följande format:
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. Starta om Maverics-tjänsten: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Konfigurera ditt program i Azure AD för SAML-baserad SSO

1. Gå till **företags program** i Azure AD-klienten, Sök efter **MAVERICS Identity Orchestrator SAML Connector** och markera den.

1. I fönstret **Egenskaper** för Maverics Identity Orchestrator SAML Connector anger du **användar tilldelning krävs?** till **Nej** om du vill att programmet ska fungera för nyligen migrerade användare.

1. I **översikts** fönstret Maverics Identity Orchestrator SAML Connector väljer du **Konfigurera enkel inloggning** och väljer sedan **SAML**.

1. Redigera den **grundläggande SAML-konfigurationen** genom att välja knappen **Redigera** (Penn ikonen) i fönstret Maverics Identity Orchestrator SAML **-baserad inloggning** .

   ![Skärm bild av redigerings knappen "grundläggande SAML-konfiguration".](common/edit-urls.png)

1. Ange **entitets-ID: t** genom att skriva in en URL i följande format: `https://<SUBDOMAIN>.maverics.org` . Entitets-ID: t måste vara unikt i alla appar i klienten. Spara värdet som anges här för att inkluderas i konfigurationen av Maverics.

1. Ange **svars-URL** i följande format: `https://<AZURECOMPANY.COM>/<MY_APP>/` . 

1. Ange **inloggnings-URL** i följande format: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` . 

1. Välj **Spara**.

1. I avsnittet **SAML-signeringscertifikat** väljer du **kopierings** knappen för att kopiera **URL: en för appens federationens metadata** och sparar den sedan på din dator.

    ![Skärm bild av kopierings knappen "SAML signerings certifikat".](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>Konfigurera Maverics Identity Orchestrator Azure AD SAML Connector

Maverics Identity Orchestrator Azure AD Connector stöder OpenID Connect och SAML Connect. Gör så här för att konfigurera anslutningen: 

1. Ange om du vill aktivera SAML-baserad SSO `authType: saml` .

1. Skapa värdet `samlMetadataURL` i följande format: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>` .

1. Definiera den URL som Azure ska omdirigeras tillbaka till i din app när användare har loggat in med sina Azure-autentiseringsuppgifter. Använd följande format: `samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>` .

1. Kopiera värdet från den tidigare konfigurerade EntityID: `samlEntityID: https://<SUBDOMAIN>.maverics.org` .

1. Kopiera värdet från svars-URL: en som Azure AD ska använda för att publicera SAML-svaret: `samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>` .

1. Generera en JSON Web Token-signerad (JWT)-signerings nyckel som används för att skydda Maverics med [openssl-verktyget](https://www.openssl.org/source/):

    ```console 
    openssl rand 64 | base64
    ```
1. Kopiera svaret till `jwtSigningKey` konfigurations egenskapen: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==` .

## <a name="attributes-and-attribute-mapping"></a>Attribut och attributmappning
Mappning av attribut används för att definiera mappningen av användarattribut från en lokal användar katalog till en Azure AD-klient när användaren har kon figurer ATS.

Attribut avgör vilka användar data som kan returneras till ett program i ett anspråk, skickas till sessionscookies eller skickas till programmet i HTTP-huvudvariabler.

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>Konfigurera YAML-filen för Maverics Identity Orchestrator Azure AD SAML Connector

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

## <a name="migrate-users-to-an-azure-ad-tenant"></a>Migrera användare till en Azure AD-klient

Följ den här konfigurationen för att stegvis migrera användare från en webb åtkomst hanterings produkt, till exempel CA SiteMinder, Oracle Access Manager eller IBM Tivoli. Du kan också migrera dem från en LDAP-katalog (Lightweight Directory Access Protocol) eller en SQL-databas.

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Konfigurera dina program behörigheter i Azure AD för att skapa användare

1. I Azure AD-klienten går du till `App registrations` och väljer **Maverics Identity Orchestrator SAML Connector** Application.

1. På **Maverics Identity Orchestrator SAML Connector | Fönstret certifikat & hemligheter** väljer du `New client secret` och väljer sedan alternativet förfallo datum. Välj **kopierings** knappen för att kopiera hemligheten och spara den på din dator.

1. På **Maverics Identity Orchestrator SAML Connector | Rutan API-behörigheter** väljer du **Lägg till behörighet** och i rutan **begär API-behörigheter** väljer du **Microsoft Graph** -och **program behörigheter**. 

1. Välj **User. readwrite. all** på nästa skärm och välj sedan **Lägg till behörigheter**. 

1. Gå tillbaka till rutan **API-behörigheter** och välj **bevilja administratörs medgivande**.

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>Konfigurera YAML-filen för Maverics Identity Orchestrator SAML Connector för användarmigrering

Om du vill aktivera arbets flödet för användarmigrering lägger du till följande ytterligare egenskaper i konfigurations filen:
1. Ange **URL: en för Azure Graph** i följande format: `graphURL: https://graph.microsoft.com` .
1. Ange **URL för OAuth-token** i följande format: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>` .
1. Ange tidigare genererad klient hemlighet i följande format: `oauthClientSecret: <CLIENT SECRET>` .


Din slutgiltiga Maverics Identity Orchestrator Azure AD Connector-konfigurations fil ser ut så här:

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

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>Konfigurera Maverics Zero Code Connector för SiteMinder

Du kan använda SiteMinder-anslutningen för att migrera användare till en Azure AD-klient. Du loggar användarna i äldre lokala program som skyddas av SiteMinder med hjälp av de nyligen skapade Azure AD-identiteterna och autentiseringsuppgifterna.

I den här självstudien har SiteMinder kon figurer ATS för att skydda det äldre programmet med formulärbaserad autentisering och `SMSESSION` cookien. Om du vill integrera med en app som använder autentiserings-och sessionsinformation via HTTP-huvuden måste du lägga till huvudemulerings konfigurationen för anslutningen.

Det här exemplet mappar `username` attributet till `SM_USER` http-huvudet:

```yaml
  headers:
    SM_USER: username
```

Ange `proxyPass` till den plats som begär proxy till. Den här platsen är vanligt vis värd för det skyddade programmet.

`loginPage` ska matcha URL: en för det inloggnings formulär som för närvarande används av SiteMinder när den omdirigerar användare för autentisering.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>Konfigurera Maverics Zero Code Connector för LDAP

När program skyddas av en webb åtkomst hanterings produkt (WAM) som SiteMinder, lagras användar identiteter och attribut vanligt vis i en LDAP-katalog.

Den här anslutnings konfigurationen visar hur du ansluter till LDAP-katalogen. Anslutningen är konfigurerad som användar Arkiv för SiteMinder så att rätt användar profil information kan samlas in under migreringen av arbets flödet och en motsvarande användare kan skapas i Azure AD.

* `baseDN` anger platsen i katalogen som LDAP-sökningen ska utföras mot.

* `url` är adressen och porten för den LDAP-server som du vill ansluta till.

* `serviceAccountUsername` är det användar namn som används för att ansluta till LDAP-servern, vanligt vis uttrycks som ett unikt namn för bindning (till exempel `CN=Directory Manager` ).

* `serviceAccountPassword` är det lösen ord som används för att ansluta till LDAP-servern. Det här värdet lagras i den tidigare konfigurerade Azure Key Vault-instansen.  

* `userAttributes` definierar listan över användarspecifika attribut att fråga efter. Dessa attribut mappas senare till motsvarande Azure AD-attribut.

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

### <a name="configure-the-migration-workflow"></a>Konfigurera arbets flödet för migrering

Arbets flödes konfigurationen för migrering avgör hur Maverics migrerar användare från SiteMinder eller LDAP till Azure AD.

Det här arbets flödet:
- Använder SiteMinder-anslutningen för att proxya SiteMinder-inloggningen. Användarautentiseringsuppgifter verifieras genom SiteMinder-autentisering och skickas sedan till efterföljande steg i arbets flödet.
- Hämtar attribut för användar profil från användar arkivet för SiteMinder.
- Gör en begäran till Microsoft Graph API för att skapa användaren i din Azure AD-klient.

Gör så här för att konfigurera migrerings arbets flödet:

1. Ge arbets flödet ett namn (till exempel **SiteMinder till Azure AD migration**).
1. Ange `endpoint` , vilket är en HTTP-sökväg som arbets flödet exponeras på, vilket utlöser `actions` arbets flödet som svar på begär Anden. `endpoint`Motsvarar vanligt vis appen som är proxy (till exempel `/my_app` ). Värdet måste innehålla både inledande och avslutande snedstreck.
1. Lägg till lämplig `actions` i arbets flödet.

   a. Definiera `login` metoden för SiteMinder-anslutningen. Koppling svärdet måste matcha name-värdet i anslutnings konfigurationen.

   b. Definiera `getprofile` metoden för LDAP-anslutningen.

   c.  Definiera `createuser` metoden för AzureAD-anslutningen.

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

1. Om Maverics-tjänsten inte redan körs startar du den genom att köra följande kommando: 

   `sudo systemctl start maverics`

1. Gå till inloggnings-URL: en för proxy `http://host.company.com/my_app` .
1. Ange de autentiseringsuppgifter som används för att logga in på programmet när det skyddas av SiteMinder.
4. Gå till **hem**  >  **användare | Alla användare** kan verifiera att användaren har skapats i Azure AD-klienten.  

### <a name="configure-the-session-abstraction-workflow"></a>Konfigurera arbets flödet för abstraktion av sessioner

Arbets flödet för sessions-abstraktion flyttar autentisering och åtkomst kontroll för det äldre lokala webb programmet till Azure AD-klienten.

Azure-anslutaren använder `login` metoden för att omdirigera användaren till inloggnings-URL: en förutsatt att ingen session finns.

När den har autentiserats skickas den sessionstoken som skapas som ett resultat till Maverics. SiteMinder-kopplingens `emulate` metod används för att emulera den cookie-baserade sessionen eller den huvudbaserade sessionen och dekorera sedan begäran med eventuella ytterligare attribut som krävs av programmet.

1. Ge arbets flödet ett namn (till exempel **SiteMinder-abstraktion**).
1. Ange `endpoint` , som motsvarar den app som proxy. Värdet måste innehålla både inledande och avslutande snedstreck (till exempel `/my_app/` ).
1. Lägg till lämplig `actions` i arbets flödet.

   a. Definiera `login` metoden för Azure-anslutaren. `connector`Värdet måste matcha `name` värdet i anslutnings konfigurationen.

   b. Definiera `emulate` metoden för SiteMinder-anslutningen.

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

1. Gå till programmets URL för proxy `https://<AZURECOMPANY.COM>/<MY_APP>` . 
    
    Du är Omdirigerad till inloggnings sidan för proxy.

1. Ange autentiseringsuppgifter för Azure AD-användare.

   Du bör omdirigeras till programmet på samma sätt som om du autentiserades direkt av SiteMinder.
