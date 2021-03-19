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
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 19f6b0601afe9ad84f02c93d7f6e1ae3a71a06a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585102"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Integrera enkel inloggning med Azure AD med Maverics Identity Orchestrator SAML Connector

Strata Maverics Identity Orchestrator är ett enkelt sätt att integrera lokala program med Azure Active Directory (Azure AD) för autentisering och åtkomst kontroll. Maverics Orchestrator kan värma av autentisering och auktorisering för appar som för närvarande är beroende av huvuden, cookies och andra tillverkarspecifika autentiseringsmetoder. Maverics Orchestrator-instanser kan distribueras lokalt eller i molnet. 

Den här självstudien om hybrid åtkomst visar hur du migrerar en lokal webbapp som för närvarande skyddas av en äldre hanterings produkt för webb åtkomst för att använda Azure AD för autentisering och åtkomst kontroll. Här är de grundläggande stegen:

1. Konfigurera Maverics Orchestrator
1. Proxy ett program
1. Registrera ett företags program i Azure AD
1. Autentisera via Azure och ge åtkomst till programmet
1. Lägg till rubriker för sömlös program åtkomst
1. Arbeta med flera program

## <a name="prerequisites"></a>Förutsättningar

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Maverics Identity Orchestrator SAML Connector SSO-aktiverad prenumeration. Kontakta [Strata Sales](mailto:sales@strata.io)om du vill hämta Maverics-programvaran.
* Minst ett program som använder huvud-baserad autentisering. Exemplen fungerar mot ett program med namnet Sonar, som finns i https://app.sonarsystems.com och ett program som heter Connectulum, som finns på https://app.connectulum.com .
* En Linux-dator som är värd för Maverics Orchestrator
  * OS: RHEL 7,7 eller senare, CentOS 7 +
  * Disk: >= 10 GB
  * Minne: >= 4 GB
  * Portar: 22 (SSH/SCP), 443, 7474
  * Rot åtkomst för installation/administrativa uppgifter
  * Nätverket utgång från den server som är värd för Maverics Identity Orchestrator till ditt skyddade program

## <a name="step-1-set-up-the-maverics-orchestrator"></a>Steg 1: Konfigurera Maverics Orchestrator

### <a name="install-maverics"></a>Installera Maverics

1. Hämta den senaste Maverics-RPM. Kopiera paketet till systemet där du vill installera Maverics-programvaran.

1. Installera Maverics-paketet och ersätt fil namnet i stället för `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   När du har installerat Maverics kommer den att köras som en tjänst under `systemd` . Verifiera att tjänsten körs genom att köra följande kommando:

   `sudo systemctl status maverics`

1. Om du vill starta om Orchestrator och följa loggarna kan du köra följande kommando:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

När du har installerat Maverics skapas standard `maverics.yaml` filen i `/etc/maverics` katalogen. Innan du redigerar konfigurationen för att inkludera `appgateways` och `connectors` , kommer konfigurations filen att se ut så här:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>Konfigurera DNS

DNS kommer att vara användbart så att du inte behöver komma ihåg Orchestrator-serverns IP-adress.

Redigera webbläsarens dators värd fil med en hypotetisk Orchestrator-IP för 12.34.56.78. I Linux-baserade operativ system finns den här filen i `/etc/hosts` . I Windows finns den på `C:\windows\system32\drivers\etc` .

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

För att bekräfta att DNS är konfigurerat som förväntat kan du göra en begäran till Orchestrator: s status-slutpunkt. Från din webbläsare, begäran http://sonar.maverics.com:7474/status .

### <a name="configure-tls"></a>Konfigurera TLS

Att kommunicera via säkra kanaler för att kommunicera med din Orchestrator är viktigt för att upprätthålla säkerheten. Du kan lägga till ett certifikat/nyckel par i `tls` avsnittet för att åstadkomma detta.

Om du vill skapa ett självsignerat certifikat och en nyckel för Orchestrator-servern kör du följande kommando i `/etc/maverics` katalogen:

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> För produktions miljöer vill du förmodligen använda ett certifikat som signerats av en känd certifikat utfärdare för att undvika varningar i webbläsaren. [Vi](https://letsencrypt.org/) rekommenderar att kryptera är ett bra och kostnads fritt alternativ om du letar efter en betrodd certifikat utfärdare.

Använd nu det nyligen genererade certifikatet och nyckeln för Orchestrator. Config-filen bör nu innehålla den här koden:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

För att bekräfta att TLS är konfigurerat som förväntat startar du om Maverics-tjänsten och gör en begäran till status slut punkten. Från din webbläsare, begäran https://sonar.maverics.com/status .

## <a name="step-2-proxy-an-application"></a>Steg 2: proxy ett program

Konfigurera sedan grundläggande proxy i Orchestrator med hjälp av `appgateways` . Det här steget hjälper dig att verifiera att Orchestrator har nödvändig anslutning till det skyddade programmet.

Config-filen bör nu innehålla den här koden:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

Om du vill kontrol lera att proxyn fungerar som förväntat startar du om Maverics-tjänsten och gör en begäran till programmet via Maverics-proxyn. Från din webbläsare, begäran https://sonar.maverics.com . Du kan också göra en begäran till specifika program resurser, till exempel `https://sonar.maverics.com/RESOURCE` , där `RESOURCE` är en giltig program resurs för den skyddade överordnade appen.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>Steg 3: registrera ett företags program i Azure AD

Nu ska du skapa ett nytt företags program i Azure AD som ska användas för att autentisera slutanvändare.

> [!NOTE]
> När du använder Azure AD-funktioner som villkorlig åtkomst är det viktigt att skapa ett företags program per lokalt program. Detta tillåter villkorlig åtkomst per app, per app-riskbedömning, tilldelade behörigheter per app och så vidare. I allmänhet mappar ett företags program i Azure AD till en Azure-anslutning i Maverics.

Registrera ett företags program i Azure AD:

1. I Azure AD-klienten går du till **företags program** och väljer sedan **nytt program**. Sök efter **Maverics Identity Orchestrator SAML Connector** i Azure AD-galleriet och välj sedan det.

1. I fönstret **Egenskaper** för Maverics Identity Orchestrator SAML Connector anger du **användar tilldelning krävs?** till **Nej** om du vill att programmet ska fungera för alla användare i din katalog.

1. I **översikts** fönstret Maverics Identity Orchestrator SAML Connector väljer du **Konfigurera enkel inloggning** och väljer sedan **SAML**.

1. Redigera den **grundläggande SAML-konfigurationen** genom att välja knappen **Redigera** (Penn ikonen) i fönstret Maverics Identity Orchestrator SAML **-baserad inloggning** .

   ![Skärm bild av redigerings knappen "grundläggande SAML-konfiguration".](common/edit-urls.png)

1. Ange ett **entitets-ID** för `https://sonar.maverics.com` . Entitets-ID: t måste vara unikt i alla appar i klienten, och det kan vara ett godtyckligt värde. Du använder det här värdet när du definierar `samlEntityID` fältet för din Azure-koppling i nästa avsnitt.

1. Ange en **svars-URL** för `https://sonar.maverics.com/acs` . Du använder det här värdet när du definierar `samlConsumerServiceURL` fältet för din Azure-koppling i nästa avsnitt.

1. Ange en **inloggnings-URL** för `https://sonar.maverics.com/` . Det här fältet används inte av Maverics, men det krävs i Azure AD för att användarna ska kunna få åtkomst till programmet via Azure AD My Apps-portalen.

1. Välj **Spara**.

1. I avsnittet **SAML-signeringscertifikat** väljer du **kopierings** knappen för att kopiera URL-värdet för **appens federationsmetadata** och sparar det sedan på datorn.

   ![Skärm bild av kopierings knappen "SAML signerings certifikat".](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>Steg 4: autentisera via Azure och ge åtkomst till programmet

Lägg sedan till det företags program som du nyss skapade för att använda genom att konfigurera Azure-anslutaren i Maverics. Den här `connectors` konfigurationen som är kopplad till `idps` blocket gör det möjligt för Orchestrator att autentisera användare.

Config-filen bör nu innehålla följande kod. Se till att ersätta `METADATA_URL` med URL-värdet för app Federation-metadata från föregående steg.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Om du vill bekräfta att autentisering fungerar som förväntat startar du om Maverics-tjänsten och gör en begäran till en program resurs via Maverics-proxyn. Du bör omdirigera till Azure för autentisering innan du ansluter till resursen.

## <a name="step-5-add-headers-for-seamless-application-access"></a>Steg 5: Lägg till rubriker för sömlös program åtkomst

Du skickar inte huvuden till det överordnade programmet ännu. Låt oss lägga till i `headers` begäran när den passerar genom Maverics-proxyn för att aktivera det överordnade programmet för att identifiera användaren.

Config-filen bör nu innehålla den här koden:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Bekräfta att autentisering fungerar som förväntat genom att göra en begäran till en program resurs via Maverics proxy. Det skyddade programmet bör nu ta emot rubriker på begäran. 

Du kan redigera rubrik nycklarna om ditt program förväntar sig olika sidhuvud. Alla anspråk som kommer tillbaka från Azure AD som en del av SAML-flödet är tillgängliga för användning i sidhuvuden. Du kan till exempel inkludera en annan rubrik för `secondary_email: azureSonarApp.email` , där `azureSonarApp` är kopplings namnet och `email` är ett anspråk som returneras från Azure AD. 

## <a name="step-6-work-with-multiple-applications"></a>Steg 6: arbeta med flera program

Nu ska vi ta en titt på vad som krävs för proxy till flera program som finns på olika värdar. För att uppnå det här steget konfigurerar du en annan app Gateway, ett annat företags program i Azure AD och en annan anslutning.

Config-filen bör nu innehålla den här koden:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

Du kanske har märkt att koden lägger till ett `host` fält i definitionerna för app Gateway. `host`Fältet gör att Maverics-Orchestrator kan särskilja den överordnade värden till proxy-trafik till.

Bekräfta att den nyligen tillagda app-gatewayen fungerar som förväntat genom att göra en begäran till https://connectulum.maverics.com .

## <a name="advanced-scenarios"></a>Avancerade scenarier

### <a name="identity-migration"></a>Identitets migrering

Kan du inte använda ditt hanterings verktyg för hantering av webb åtkomst från slut punkt till liv, men du har inte något sätt att migrera dina användare utan att återställa Mass lösen ord? Maverics Orchestrator stöder identitets migrering med hjälp av `migrationgateways` .

### <a name="web-server-gateways"></a>Webb server-gatewayer

Vill du inte återanvända ditt nätverk och din proxyvärd via Maverics Orchestrator? Inte ett problem. Maverics-Orchestrator kan kombineras med webbserver-gatewayer (moduler) för att erbjuda samma lösningar utan proxy.

## <a name="wrap-up"></a>Bryt upp

Nu har du installerat Maverics Orchestrator, skapat och konfigurerat ett företags program i Azure AD och konfigurerat Orchestrator till proxy till ett skyddat program samtidigt som du kräver autentisering och tillämpar principer. Om du vill veta mer om hur Maverics-Orchestrator kan användas för användnings fall med distribuerad identitets hantering [kontaktar du Strata](mailto:sales@strata.io).

## <a name="next-steps"></a>Nästa steg

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)
