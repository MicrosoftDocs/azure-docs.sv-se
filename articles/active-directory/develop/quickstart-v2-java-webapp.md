---
title: 'Snabb start: lägga till inloggning med Microsoft i en Java-webbapp | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur du lägger till inloggning med Microsoft i ett Java-webbprogram med hjälp av OpenID Connect.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 91aa6c96c714bff26ea7e0df5b2b6971c68edec0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178579"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Snabb start: lägga till inloggning med Microsoft i en Java-webbapp

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur ett Java-webbprogram kan logga in användare och anropar Microsoft Graph-API: et. Användare från vilken organisation som helst Azure Active Directory (Azure AD) kan logga in i programmet.

 En översikt finns i [diagrammet över hur exemplet fungerar](#how-the-sample-works).

## <a name="prerequisites"></a>Krav

Om du vill köra det här exemplet behöver du:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 eller senare. 
- [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två sätt att starta ditt snabb starts program: Express (alternativ 1) och manuell (alternativ 2).
>
> ### <a name="option-1-register-and-automatically-configure-your-app-and-then-download-the-code-sample"></a>Alternativ 1: registrera och konfigurera appen automatiskt och hämta sedan kod exemplet
>
> 1. Gå till snabb starts upplevelsen för <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Appregistreringar <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
> 1. Ange ett namn för programmet och välj sedan **Registrera**.
> 1. Följ anvisningarna i portalens snabb starts miljö för att ladda ned den automatiskt konfigurerade program koden.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> Följ dessa steg om du vill registrera ditt program och manuellt lägga till appens registrerings information:
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera programmet i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Välj **Appregistreringar** under **Hantera**.
> 1. Välj **ny registrering**.
> 1. Ange ett **namn** för ditt program, till exempel **Java-webapp**. Användare av appen kan se det här namnet. Du kan ändra den senare.
> 1. Välj **Register** (Registrera).
> 1. På sidan **Översikt** noterar du program- **ID: t** och **katalog (klient)-ID: t**. Du behöver dessa värden senare.
> 1. Under **Hantera** väljer du **autentisering**.
> 1. Välj **Lägg till en plattforms**  >  **webbplats**.
> 1. I avsnittet **omdirigerings-URI** anger du `https://localhost:8443/msal4jsample/secure/aad` .
> 1. Välj **Konfigurera**.
> 1. Ange som en andra omdirigerings-URI i avsnittet Webb under **omdirigerings** **-** URI: er `https://localhost:8443/msal4jsample/graph/me` .
> 1. Välj **Certifikat och hemligheter** under **Hantera**. I avsnittet **klient hemligheter** väljer du **ny klient hemlighet**.
> 1. Ange en nyckel Beskrivning (till exempel *app Secret*), lämna standard förfallo datum och välj **Lägg till**.
> 1. Notera **värdet** för klient hemligheten. Du behöver det senare.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
>
> Om du vill använda kod exemplet i den här snabb starten måste du:
>
> 1. Lägg till svars-URL: er `https://localhost:8443/msal4jsample/secure/aad` och `https://localhost:8443/msal4jsample/graph/me` .
> 1. Skapa en klient hemlighet.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-code-sample"></a>Steg 2: Hämta kod exemplet
> [!div renderon="docs"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Hämta projektet och extrahera. zip-filen i en mapp nära roten på enheten. Till exempel *C:\Azure-samples*.
>
> Ange egenskaperna för att använda HTTPS med localhost `server.ssl.key` . Om du vill skapa ett självsignerat certifikat använder du verktyget för verktyg (ingår i JRE).
>
> Här är ett exempel:
>  ```
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Placera den genererade nyckel lagrings filen i mappen *resurser* .

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Steg 3: Konfigurera kod exemplet
> 1. Extrahera zip-filen till en lokal mapp.
> 1. *Valfritt.* Om du använder en Integrated Development Environment öppnar du exemplet i den miljön.
> 1. Öppna filen *Application. Properties* . Du hittar den i mappen *src/main/Resources/* Folders. Ersätt värdena i fälten `aad.clientId` , `aad.authority` och `aad.secretKey` med program-ID, klient-ID och värden för klient hemlighet. Så här ser det ut:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
>    I föregående kod:
>
>    - `Enter_the_Application_Id_here` är program-ID: t för det program som du har registrerat.
>    - `Enter_the_Client_Secret_Here` är den **klient hemlighet** som du skapade i **certifikat & hemligheter** för det program som du har registrerat.
>    - `Enter_the_Tenant_Info_Here` är **katalog-ID-** värdet för det program som du har registrerat.
> 1. Ange egenskaperna för att använda HTTPS med localhost `server.ssl.key` . Om du vill skapa ett självsignerat certifikat använder du verktyget för verktyg (ingår i JRE).
>
>    Här är ett exempel:
>
>     ```
>      keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>      server.ssl.key-store-type=PKCS12
>      server.ssl.key-store=classpath:keystore.p12
>      server.ssl.key-store-password=password
>      server.ssl.key-alias=testCert
>      ```
>   1. Placera den genererade nyckel lagrings filen i mappen *resurser* .


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Steg 3: kör kod exemplet
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Steg 4: kör kod exemplet

Gör något av följande för att köra projektet:

- Kör den direkt från IDE-enheten med hjälp av den inbäddade våren Boot-servern. 
- Paketera det till en WAR-fil med hjälp av [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html)och distribuera den sedan till en J2EE container-lösning som [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-the-project-from-an-ide"></a>Köra projektet från en IDE

Om du vill köra webb programmet från en IDE väljer du kör och går sedan till projektets start sida. För det här exemplet är standard start sidans URL https://localhost:8443 .

1. På den första sidan väljer du knappen **Logga in** för att omdirigera användare till Azure Active Directory och uppmana dem att ange autentiseringsuppgifter.

1. När användarna har autentiserats omdirigeras de till `https://localhost:8443/msal4jsample/secure/aad` . De är nu inloggade och sidan visar information om användar kontot. Exempel gränssnittet har följande knappar:
    - **Logga ut**: loggar den aktuella användaren från programmet och omdirigerar användaren till start sidan.
    - **Visa användar information**: hämtar en token för Microsoft Graph och anropar Microsoft Graph med en begäran som innehåller token, som returnerar grundläggande information om den inloggade användaren.

##### <a name="running-the-project-from-tomcat"></a>Köra projektet från Tomcat

Om du vill distribuera webb exemplet till Tomcat måste du göra några ändringar i käll koden.

1. Öppna *MS-Identity-Java-webapp/pom.xml*.
    - Under `<name>msal-web-sample</name>` lägger du till `<packaging>war</packaging>` .

2. Öppna *MS-Identity-Java-webapp/src/main/Java/com. Microsoft. Azure. msalwebsample/MsalWebSampleApplication*.

    - Ta bort alla käll koder och ersätt den med den här koden:

      ```Java
       package com.microsoft.azure.msalwebsample;

       import org.springframework.boot.SpringApplication;
       import org.springframework.boot.autoconfigure.SpringBootApplication;
       import org.springframework.boot.builder.SpringApplicationBuilder;
       import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

       @SpringBootApplication
       public class MsalWebSampleApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
         SpringApplication.run(MsalWebSampleApplication.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
         return builder.sources(MsalWebSampleApplication.class);
        }
       }
      ```

3.   Tomcat standard-HTTP-port är 8080, men du behöver en HTTPS-anslutning via port 8443. Så här konfigurerar du den här inställningen:
        - Gå till *Tomcat/conf/server.xml*.
        - Sök efter `<connector>` taggen och ersätt den befintliga anslutningen med denna koppling:

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

4. Öppna ett kommandotolksfönster. Gå till rotmappen för det här exemplet (där pom.xml-filen finns) och kör `mvn package` för att skapa projektet.
    - Det här kommandot genererar en *msal-Web-Sample--0.1.0. War* -fil i din */Targets* -katalog.
    - Byt namn på filen till *msal4jsample. War*.
    - Distribuera WAR-filen med hjälp av Tomcat eller någon annan J2EE container-lösning.
        - Du distribuerar filen msal4jsample. War genom att kopiera den till katalogen */webapps/* i Tomcat-installationen och sedan starta Tomcat-servern.

5. När filen har distribuerats går du till https://localhost:8443/msal4jsample med hjälp av en webbläsare.


> [!IMPORTANT]
> I det här snabb starts programmet används en klient hemlighet för att identifiera sig själv som en konfidentiell klient. Eftersom klient hemligheten läggs till som oformaterad text i dina projektfiler rekommenderar vi av säkerhets skäl att du använder ett certifikat i stället för en klient hemlighet innan du använder programmet i en produktions miljö. Mer information om hur du använder ett certifikat finns i [autentiseringsuppgifter för certifikat för programautentisering](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Diagram som visar hur exempel appen som genereras av den här snabb starten fungerar.](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>Hämta MSAL

MSAL for Java (MSAL4J) är det Java-bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform.

Lägg till MSAL4J i ditt program genom att använda maven eller Gradle för att hantera dina beroenden genom att göra följande ändringar i programmets pom.xml-(maven) eller build. Gradle-fil (Gradle).

I pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

I build. gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="initialize-msal"></a>Initiera MSAL

Lägg till en referens till MSAL för Java genom att lägga till följande kod i början av filen där du kommer att använda MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

En mer detaljerad beskrivning av hur du skapar webbappar som loggar in användare på Microsoft Identity Platform finns i listan med flerdelade scenarion:

> [!div class="nextstepaction"]
> [Scenario: webb program som loggar in användare](scenario-web-app-sign-user-overview.md?tabs=java)
