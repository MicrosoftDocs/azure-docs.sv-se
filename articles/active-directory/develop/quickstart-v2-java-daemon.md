---
title: 'Snabb start: anropa Microsoft Graph från en Java-daemon | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur en Java-app kan få en åtkomsttoken och anropa ett API som skyddas av Microsoft Identity Platform-slutpunkten med appens egen identitet
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/22/2021
ms.author: nacanuma
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 196b80a704b8a270a4cbb7d3505d5f9be1e23479
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820332"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>Snabb start: Hämta en token och anropa Microsoft Graph API från en Java-konsol med appens identitet

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur ett Java-program kan få en åtkomsttoken med hjälp av appens identitet för att anropa Microsoft Graph-API: et och visa en [lista över användare](/graph/api/user-list) i katalogen. Kod exemplet visar hur ett obevakat jobb eller en Windows-tjänst kan köras med en program identitet, i stället för en användares identitet. 

> [!div renderon="docs"]
> ![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-java-daemon/java-console-daemon.svg)

## <a name="prerequisites"></a>Förutsättningar

Om du vill köra det här exemplet behöver du:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 eller senare
- [Maven](https://maven.apache.org/)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen

> [!div renderon="docs" class="sxs-lookup"]
>
> Det finns två alternativ för att starta ditt snabb starts program: Express (alternativ 1 nedan) och manuell (alternativ 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till snabb starts upplevelsen för <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaDaemonQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Appregistreringar</a> .
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. Ange ett **namn** för programmet, till exempel `Daemon-console` . Användare av appen kan se det här namnet och du kan ändra det senare.
> 1. Välj **Register** (Registrera).
> 1. Under **Hantera** väljer du  **certifikat & hemligheter**.
> 1. Under **klient hemligheter** väljer du **ny klient hemlighet**, anger ett namn och väljer sedan **Lägg till**. Registrera det hemliga värdet på en säker plats för användning i ett senare steg.
> 1. Under **Hantera** väljer du **API-behörigheter**  >  **Lägg till en behörighet**. Välj **Microsoft Graph**.
> 1. Välj **Programbehörigheter**.
> 1. Under noden **användare** väljer du **User. Read. all** och väljer sedan **Lägg till behörigheter**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-quickstart-app"></a>Hämta och konfigurera snabb starts appen
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Steg 1: konfigurera programmet i Azure Portal
> För att kodexemplet för den här snabbstarten ska fungera måste du skapa en klienthemlighet och lägga till Graph-API:ts programbehörighet **User.Read.All**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-netcore-daemon/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-java-project"></a>Steg 2: Hämta Java-projektet

> [!div renderon="docs"]
> [Ladda ned Java daemon-projektet](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-the-java-project"></a>Steg 3: Konfigurera Java-projektet
>
> 1. Extrahera zip-filen i en lokal mapp nära diskens rot, till exempel *C:\Azure-Samples*.
> 1. Navigera till undermappen **msal-client-Credential-Secret**.
> 1. Redigera *src\main\resources\application.Properties* och ersätt värdena för fälten `AUTHORITY` , `CLIENT_ID` och `SECRET` med följande kodfragment:
>
>    ```
>    AUTHORITY=https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/
>    CLIENT_ID=Enter_the_Application_Id_Here
>    SECRET=Enter_the_Client_Secret_Here
>    ```
>    Plats:
>    - `Enter_the_Application_Id_Here` – är **program-ID (klient)** för programmet som du har registrerat.
>    - `Enter_the_Tenant_Id_Here` – Ersätt värdet med **klient-ID:** t eller **klient namnet** (till exempel contoso.Microsoft.com).
>    - `Enter_the_Client_Secret_Here` – ersätt det här värdet med klienthemligheten som skapades i steg 1.
>
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och går du till appens **översiktssida** i Azure-portalen. Generera en ny nyckel genom att gå till sidan **Certifikat och hemligheter**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Steg 3: administratörs medgivande

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Steg 4: Administratörsmedgivande

Om du försöker köra programmet nu får du ett *HTTP 403-otillåtet* fel: `Insufficient privileges to complete the operation` . Det här felet beror på att en *app-only-behörighet* kräver administratörs medgivande: en global administratör för din katalog måste ge ditt program medgivande. Välj ett av alternativen nedan beroende på din roll:

##### <a name="global-tenant-administrator"></a>Global innehavaradministratör

> [!div renderon="docs"]
> Om du är global innehavaradministratör går du till sidan med **API-behörigheter** i **Appregistreringar** i Azure Portal och väljer **bevilja administratörs medgivande för {klient organisationens namn}** (där {klient organisationens namn} är namnet på din katalog).

> [!div renderon="portal" class="sxs-lookup"]
> Om du är global administratör går du till sidan **API-behörigheter** och väljer **bevilja administrativt medgivande för Enter_the_Tenant_Name_Here**.
> > [!div id="apipermissionspage"]
> > [Gå till sidan API-behörigheter]()

##### <a name="standard-user"></a>Standardanvändare

Om du är en standard användare av din klient organisation måste du be en global administratör att bevilja administrativt medgivande för ditt program. Gör detta genom att ge följande URL till administratören:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Plats:
>> * `Enter_the_Tenant_Id_Here` – ersätt det här värdet med **klient-ID** eller **klientnamn** (t.ex. contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` – är **program-ID (klient)** för programmet som du har registrerat.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Steg 4: kör programmet

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Steg 5: Köra appen

Du kan testa exemplet direkt genom att köra main-metoden för ClientCredentialGrant. Java från din IDE.

Från gränssnittet eller kommando raden:

```
$ mvn clean compile assembly:single
```

Detta skapar en msal-client-Credential-Secret--1.0.0. jar-fil i din/Targets-katalog. Kör det här med din Java-körbara fil som nedan:

```
$ java -jar msal-client-credential-secret-1.0.0.jar
```

Efter körningen ska programmet Visa listan över användare i den konfigurerade klient organisationen.


> [!IMPORTANT]
> Det här snabbstartsprogrammet använder en klienthemlighet för att identifiera sig som en konfidentiell klient. Eftersom klienthemligheten läggs till som oformaterad text till dina projektfiler rekommenderar vi att du av säkerhetsskäl använder ett certifikat i stället för en klienthemlighet innan programmet används som produktionsprogram. Mer information om hur du använder ett certifikat finns i [följande instruktioner](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate) i samma GitHub-lagringsplats för det här exemplet, men i den andra mappen **msal-client-Credential-Certificate**.

## <a name="more-information"></a>Mer information

### <a name="msal-java"></a>MSAL Java

[MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java) är det bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Som beskrivs i den här snabb starten begär denna token genom att använda programmets egna identitet i stället för delegerade behörigheter. Autentiseringsflödet som används i det här fallet kallas för *[oauth-flöde för klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md)*. Mer information om hur du använder MSAL Java med daemon-appar finns i [den här artikeln](scenario-daemon-overview.md).

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

### <a name="msal-initialization"></a>MSAL-initiering

Lägg till en referens till MSAL för Java genom att lägga till följande kod överst i filen där du kommer att använda MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

Initiera sedan MSAL med hjälp av följande kod:

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

> | Plats: |Description |
> |---------|---------|
> | `CLIENT_SECRET` | Är klienthemligheten som skapats för appen i Azure-portalen. |
> | `CLIENT_ID` | Är **Program-ID (klient)** för det program som registrerats på Azure-portalen. Du hittar det här värdet på appens **översiktssida** på Azure-portalen. |
> | `AUTHORITY`    | STS-slutpunkten för autentisering av användaren. Vanligtvis `https://login.microsoftonline.com/{tenant}` för offentligt moln, där {klient} är namnet på klientorganisationen eller klient-ID:t.|

### <a name="requesting-tokens"></a>Begära token

Om du vill begära en token med appens identitet använder du metoden `acquireToken`:

```Java
IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
```

> |Plats:| Description |
> |---------|---------|
> | `SCOPE` | Innehåller omfattningarna som begärdes. För konfidentiella klienter bör det använda formatet som liknar `{Application ID URI}/.default` för att indikera att de omfattningar som begärs är de statiskt definierade i app-objektet som anges i Azure Portal (för Microsoft Graph `{Application ID URI}` pekar på `https://graph.microsoft.com` ). För anpassade webb-API: er `{Application ID URI}` definieras under avsnittet **exponera ett API** i **Appregistreringar** i Azure-portalen.|

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om daemon-program finns på sidan om scenario landning.

> [!div class="nextstepaction"]
> [Daemon-program som anropar webb-API: er](scenario-daemon-overview.md)
