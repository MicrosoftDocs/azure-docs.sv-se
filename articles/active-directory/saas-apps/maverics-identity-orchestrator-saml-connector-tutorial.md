---
title: 'Självstudie: Integrera Azure Active Directory enkel inloggning (SSO) med Maverics Identity Orchestrator SAML Connector | Microsoft Docs'
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
ms.openlocfilehash: 402f6cd6961108cdf1e9c94fb4f93309fbf15ead
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599034"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Integrera enkel inloggning i Azure AD med Maverics Identity Orchestrator SAML Connector

Stratas Maverics Identity Orchestrator är ett enkelt sätt att integrera lokala program med Azure Active Directory (Azure AD) för autentisering och åtkomstkontroll. Maverics Orchestrator kan modernisera autentisering och auktorisering för appar som för närvarande förlitar sig på huvuden, cookies och andra egna autentiseringsmetoder. Maverics Orchestrator-instanser kan distribueras lokalt eller i molnet. 

Den här självstudien om hybridåtkomst visar hur du migrerar ett lokalt webbprogram som för närvarande skyddas av en äldre produkt för webbåtkomsthantering för att använda Azure AD för autentisering och åtkomstkontroll. Här är de grundläggande stegen:

1. Konfigurera Maverics Orchestrator
1. Proxy för ett program
1. Registrera ett företagsprogram i Azure AD
1. Autentisera via Azure och auktorisera åtkomst till programmet
1. Lägga till huvuden för sömlös programåtkomst
1. Arbeta med flera program

## <a name="prerequisites"></a>Förutsättningar

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* En Maverics Identity Orchestrator SAML Connector SSO-aktiverad prenumeration. Om du vill hämta Maverics-programvaran kontaktar du [Strata sales](mailto:sales@strata.io).
* Minst ett program som använder huvudbaserad autentisering. Exemplen fungerar mot ett program som heter Connectulum, som finns på `https://app.connectulum.com` .
* En Linux-dator som värd för Maverics Orchestrator
  * OS: RHEL 7.7 eller senare, CentOS 7+
  * Disk: >= 10 GB
  * Minne: >= 4 GB
  * Portar: 22 (SSH/SCP), 443, 7474
  * Rotåtkomst för installation/administrativa uppgifter
  * Utgående nätverkstrafik från servern som är värd för Maverics Identity Orchestrator till det skyddade programmet

## <a name="step-1-set-up-the-maverics-orchestrator"></a>Steg 1: Konfigurera Maverics Orchestrator

### <a name="install-maverics"></a>Installera Maverics

1. Hämta senaste Maverics RPM. Kopiera paketet till det system där du vill installera Maverics-programvaran.

1. Installera Maverics-paketet och ersätt ditt filnamn i stället för `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   När du har installerat Maverics körs den som en tjänst under `systemd` . Kontrollera att tjänsten körs genom att köra följande kommando:

   `sudo systemctl status maverics`

1. Om du vill starta om Orchestrator och följa loggarna kan du köra följande kommando:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

När du har installerat Maverics `maverics.yaml` skapas standardfilen i `/etc/maverics` katalogen . Innan du redigerar konfigurationen så att den `appgateways` inkluderar `connectors` och ser konfigurationsfilen ut så här z:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>Konfigurera DNS

DNS är användbart så att du inte behöver komma ihåg Orchestrator-serverns IP-adress.

Redigera webbläsardatorns (din bärbara dators) värdfil med hjälp av en hypotetisk Orchestrator-IP-adress på 12.34.56.78. På Linux-baserade operativsystem finns den här filen i `/etc/hosts` . I Windows finns den på `C:\windows\system32\drivers\etc` .

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Du kan bekräfta att DNS är konfigurerat som förväntat genom att skicka en begäran till Orchestrators statusslutpunkt. Begär från http://sonar.maverics.com:7474/status webbläsaren.

### <a name="configure-tls"></a>Konfigurera TLS

Att kommunicera via säkra kanaler för att kommunicera med Orchestrator är viktigt för att upprätthålla säkerheten. Du kan lägga till ett certifikat/nyckelpar i avsnittet `tls` för att åstadkomma detta.

Generera ett själv signerat certifikat och en nyckel för Orchestrator-servern genom att köra följande kommando från `/etc/maverics` katalogen :

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> För produktionsmiljöer vill du förmodligen använda ett certifikat som signerats av en känd certifikatutfärdare för att undvika varningar i webbläsaren. [Let's Encrypt](https://letsencrypt.org/) är ett bra och kostnadsfritt alternativ om du letar efter en betrodd certifikatutfärdare.

Använd nu det nyligen genererade certifikatet och nyckeln för Orchestrator. Konfigurationsfilen bör nu innehålla den här koden:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Bekräfta att TLS är konfigurerat som förväntat genom att starta om Maverics-tjänsten och skicka en begäran till statusslutpunkten.

## <a name="step-2-proxy-an-application"></a>Steg 2: Proxy för ett program

Konfigurera sedan grundläggande proxy i Orchestrator med hjälp av `appgateways` . Det här steget hjälper dig att verifiera att Orchestrator har den anslutning som krävs till det skyddade programmet.

Konfigurationsfilen bör nu innehålla den här koden:

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

Bekräfta att proxyn fungerar som förväntat genom att starta om Maverics-tjänsten och skicka en begäran till programmet via Maverics-proxyn. Du kan också göra en begäran till specifika programresurser.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>Steg 3: Registrera ett företagsprogram i Azure AD

Skapa nu ett nytt företagsprogram i Azure AD som ska användas för att autentisera slutanvändare.

> [!NOTE]
> När du använder Azure AD-funktioner som villkorsstyrd åtkomst är det viktigt att skapa ett företagsprogram per lokalt program. Detta tillåter villkorlig åtkomst per app, riskutvärdering per app, tilldelade behörigheter per app och så vidare. I allmänhet mappar ett företagsprogram i Azure AD till en Azure-anslutningsapp i Maverics.

Registrera ett företagsprogram i Azure AD:

1. I din Azure AD-klientorganisation går **du till Företagsprogram** och väljer **sedan Nytt program.** I Azure AD-galleriet söker du efter **Maverics Identity Orchestrator SAML Connector** och väljer det sedan.

1. I fönstret Egenskaper för Maverics Identity Orchestrator SAML-anslutningsprogram anger du Användartilldelning **krävs?** till Nej för att programmet ska fungera för alla användare i din katalog.  

1. I fönstret Översikt över Maverics  Identity Orchestrator SAML **Connector** väljer du Konfigurera enkel inloggning och sedan **SAML**.

1. I fönstret **SamL-baserad** inloggning med Maverics Identity Orchestrator SAML Connector redigerar du den grundläggande **SAML-konfigurationen** genom att välja knappen **Redigera** (pennikonen).

   ![Skärmbild av redigeringsknappen "Grundläggande SAML-konfiguration".](common/edit-urls.png)

1. Ange **entitets-ID** för `https://sonar.maverics.com` . Entitets-ID:t måste vara unikt för apparna i klientorganisationen och kan vara ett godtyckligt värde. Du använder det här värdet när du definierar `samlEntityID` fältet för Azure-anslutningsappen i nästa avsnitt.

1. Ange **svars-URL:en** för `https://sonar.maverics.com/acs` . Du använder det här värdet när du definierar `samlConsumerServiceURL` fältet för Azure-anslutningsappen i nästa avsnitt.

1. Ange **inloggnings-URL:en** för `https://sonar.maverics.com/` . Det här fältet används inte av Maverics, men det krävs i Azure AD för att ge användarna åtkomst till programmet via Azure AD Mina appar portalen.

1. Välj **Spara**.

1. I avsnittet **SAML-signeringscertifikat** väljer du knappen Kopiera för att kopiera **URL-värdet för appfederationsmetadata** och sparar det sedan på datorn. 

   ![Skärmbild av kopieringsknappen "SAML-signeringscertifikat".](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>Steg 4: Autentisera via Azure och auktorisera åtkomst till programmet

Placera sedan det företagsprogram som du nyss skapade för användning genom att konfigurera Azure-anslutningsappen i Maverics. Den `connectors` här konfigurationen i par med `idps` blocket gör att Orchestrator kan autentisera användare.

Konfigurationsfilen bör nu innehålla följande kod. Se till att ersätta `METADATA_URL` med värdet för URL:en för appfederationmetadata från föregående steg.

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

Bekräfta att autentiseringen fungerar som förväntat genom att starta om Maverics-tjänsten och skicka en begäran till en programresurs via Maverics-proxyn. Du bör omdirigeras till Azure för autentisering innan du kommer åt resursen.

## <a name="step-5-add-headers-for-seamless-application-access"></a>Steg 5: Lägga till huvuden för sömlös programåtkomst

Du skickar inte huvuden till det överordnade programmet ännu. Vi lägger till i `headers` begäran när den passerar genom Maverics-proxyn för att göra det möjligt för det överordnade programmet att identifiera användaren.

Konfigurationsfilen bör nu innehålla den här koden:

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

Bekräfta att autentiseringen fungerar som förväntat genom att skicka en begäran till en programresurs via Maverics-proxyn. Det skyddade programmet bör nu ta emot huvuden för begäran. 

Du kan redigera huvudnycklarna om ditt program förväntar sig olika huvuden. Alla anspråk som kommer tillbaka från Azure AD som en del av SAML-flödet är tillgängliga för användning i rubriker. Du kan till exempel inkludera ett annat huvud i `secondary_email: azureSonarApp.email` , där `azureSonarApp` är anslutningsappens namn och `email` är ett anspråk som returneras från Azure AD. 

## <a name="step-6-work-with-multiple-applications"></a>Steg 6: Arbeta med flera program

Nu ska vi ta en titt på vad som krävs för att proxy till flera program som finns på olika värdar. För att uppnå det här steget konfigurerar du App Gateway, ett annat företagsprogram i Azure AD och en annan anslutningsapp.

Konfigurationsfilen bör nu innehålla den här koden:

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

Du kanske har lagt märke till att koden lägger till `host` ett fält i App Gateway definitionerna. Med `host` fältet kan Maverics Orchestrator särskilja vilken överordnad värd som proxytrafik ska dirigeras till.

Bekräfta att den nyligen tillagda App Gateway fungerar som förväntat genom att skicka en begäran till `https://connectulum.maverics.com` .

## <a name="advanced-scenarios"></a>Avancerade scenarier

### <a name="identity-migration"></a>Identitetsmigrering

Kan du inte hantera webbåtkomsthanteringen i slutet av livscykeln, men du kan inte migrera dina användare utan massåterställning av lösenord? Maverics Orchestrator stöder identitetsmigrering med hjälp av `migrationgateways` .

### <a name="web-server-gateways"></a>Webbservergatewayer

Vill du inte omarbeta nätverks- och proxytrafiken via Maverics Orchestrator? Inte ett problem. Maverics Orchestrator kan paras ihop med webbservergatewayer (moduler) för att erbjuda samma lösningar utan proxy.

## <a name="wrap-up"></a>Sammanfattning

Nu har du installerat Maverics Orchestrator, skapat och konfigurerat ett företagsprogram i Azure AD och konfigurerat Orchestrator för proxy till ett skyddat program samtidigt som autentisering och tvingande princip krävs. Om du vill veta mer om hur Maverics Orchestrator kan användas för användningsfall för distribuerad identitetshantering [kontaktar du Strata](mailto:sales@strata.io).

## <a name="next-steps"></a>Nästa steg

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)
