---
title: Skydda SPA-backend med OAuth 2,0 med hjälp av Azure Active Directory B2C och Azure API Management.
description: Skydda ett API med OAuth 2,0 med hjälp av Azure Active Directory B2C, Azure API Management och enkel autentisering som ska anropas från ett Java Script SPA.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new, devx-track-js
ms.openlocfilehash: b157200b03bdc89e00bfa5c8264d78baf24875c1
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609130"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Skydda SPA-backend med OAuth 2,0, Azure Active Directory B2C och Azure API Management

Det här scenariot visar hur du konfigurerar Azure API Management-instansen för att skydda ett API.
Vi använder OAuth 2,0-protokollet med Azure AD B2C, tillsammans med API Management för att skydda en Azure Functions Server del med EasyAuth.

## <a name="aims"></a>Syftar
Vi ska se hur API Management kan användas i ett förenklat scenario med Azure Functions och Azure AD B2C. Du kommer att skapa en JavaScript-app (JS) som anropar en API, som loggar in användare med Azure AD B2C. Sedan använder du API Management validera-JWT-principinställningar för att skydda Server dels-API: et.

För att få skydd i djupet använder vi EasyAuth för att validera token igen inuti Server dels-API: et.

## <a name="prerequisites"></a>Krav
För att följa stegen i den här artikeln måste du ha:
* Ett Azure (StorageV2) Generell användning v2-lagrings konto som värd för frontend JS-appen med en sida
* En Azure API Management-instans 
* En tom Azure Function-app (som kör v2 .NET Core runtime, i en Windows-plan) som värd för den anropade API: n
* En Azure AD B2C klient, länkad till en prenumeration 

Även om du i praktiken använder resurser i samma region i produktions arbets belastningar, för den här instruktions artikeln är distributions området inte viktigt.

## <a name="overview"></a>Översikt
Här är en illustration av de komponenter som används och flödet mellan dem när den här processen har slutförts.
![Komponenter som används och Flow](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Komponenter som används och Flow")

Här är en snabb översikt över stegen:

1. Skapa Azure AD B2C anropa (frontend, API Management) och API-program med omfattningar och bevilja API-åtkomst
1. Skapa registrerings-eller inloggnings principer så att användarna kan logga in med Azure AD B2C 
1. Konfigurera API Management med de nya Azure AD B2C-klient-ID: na och nycklarna för att aktivera OAuth2-användarauktorisering i Developer-konsolen
1. Bygg funktions-API: et
1. Konfigurera funktions-API: et för att aktivera EasyAuth med de nya Azure AD B2C klient-ID: n och nycklarna och lås till APIM VIP 
1. Bygg API-definitionen i API Management
1. Konfigurera OAuth2 för API-konfigurationen för API Management
1. Konfigurera **CORS** -principen och Lägg till **validate-JWT-** principen för att verifiera OAuth-token för varje inkommande begäran
1. Bygg det anropande programmet för att använda API: et
1. Överför SPA-exemplet för JS
1. Konfigurera exempel-Client-appen med de nya Azure AD B2C-klient-ID: n och nycklarna 
1. Testa klient programmet

## <a name="configure-azure-ad-b2c"></a>Konfigurera Azure AD B2C 
Öppna bladet Azure AD B2C på portalen och utför följande steg.
1. Välj fliken **program** 
1. Klicka på knappen Lägg till och skapa tre program i följande syfte
   * Klient dels klienten.
   * Server delens funktions-API.
   * Valfritt API Management Developer-portalen (om du inte kör Azure API Management på förbruknings nivån längre fram i det här scenariot senare).
1. Ange WebApp/webb-API för alla 3 program och ange alternativet Tillåt implicit flöde till Ja endast för klient dels klienten.
1. Nu anger du app-ID-URI: n och väljer något unikt och relevant för den tjänst som skapas.
1. Använd plats hållare för svars-URL: er för tillfället https://localhost , som vi uppdaterar dessa URL: er senare.
1. Klicka på Skapa och upprepa sedan steg 2-5 för var och en av de tre apparna ovan, och registrera AppID URI, namn och program-ID för senare användning för alla tre apparna.
1. Öppna programmet API Management Developer-portalen i listan med program och välj fliken *nycklar* (under Allmänt) och klicka sedan på generera nyckel för att generera en auth-nyckel
1. När du klickar på Spara registrerar du nyckeln på ett säkert sätt för senare användning – Observera att den här platsen är den enda chansen att visa och kopiera den här nyckeln.
1. Klicka nu på fliken *publicerade områden* (under API-åtkomst)
1. Skapa och namnge ett omfång för funktions-API: et och registrera omfattningen och det fullständiga värdet för omfattning och klicka sedan på Spara.
   > [!NOTE]
   > Azure AD B2C omfattningar är effektiva behörigheter inom ditt API som andra program kan begära åtkomst till via bladet för API-åtkomst från sina program, vilket effektivt precis har skapat program behörigheter för ditt anropade API.
1. Öppna de andra två programmen och titta sedan på fliken *API-åtkomst* .
1. Ge dem åtkomst till Server dels-API-omfånget och det som redan finns där ("åtkomst till användarens profil").
1. Skapa en nyckel var och en genom att välja fliken *nycklar* under allmänt för att generera en autentiseringsnyckel och registrera nycklarna någon annan stans.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Skapa ett användar flöde för "Registrera dig eller logga in"
1. Gå tillbaka till roten (eller "översikten") på bladet Azure AD B2C 
1. Välj sedan "Användarflöden (principer)" och klicka på "nytt användar flöde"
1. Välj användar flödes typen "Registrera dig och logga in"
1. Ge principen ett namn och registrera den för senare.
1. Klicka sedan på "identitets leverantörer" och kontrol lera sedan "användar-ID-registrering" (det kan säga e-postregistrering) och klicka på OK. 
1. Under "användarattribut och anspråk" klickar du på "Visa fler..." Välj sedan de anspråks alternativ som du vill att användarna ska ange och ha returnerat i token. Kontrol lera minst visnings namn och e-postadress för att samla in och returnera och klicka på OK och sedan på Skapa.
1. Välj den princip som du skapade i listan och klicka sedan på knappen Kör användar flöde.
1. Den här åtgärden öppnar bladet kör användar flöde, väljer klient delens program och registrerar sedan adressen för b2clogin.com-domänen som visas under List rutan för "Välj domän".
1. Klicka på länken längst upp för att öppna den välkända OpenID konfigurations slut punkt och registrera authorization_endpoint och token_endpoint värden samt värdet för själva länken som den välkända OpenID konfigurations slut punkten.

   > [!NOTE]
   > Med B2C-principer kan du exponera Azure AD B2C inloggnings slut punkter för att kunna avbilda olika data komponenter och logga in användare på olika sätt. I det här fallet konfigurerade vi en registrering eller inloggnings slut punkt, som exponerade en välkänd konfigurations slut punkt, särskilt vår skapade princip identifierades i URL: en med parametern p =.
   > 
   > När detta är gjort – har du nu en funktionell verksamhet för konsument identitets plattformen som loggar användare i flera program. 
   > Om du vill kan du klicka på knappen "kör användar flöde" (för att gå igenom registrerings-eller inloggnings processen) och få en känsla för vad den gör i praktiken, men omdirigeringen fungerar inte eftersom appen ännu inte har distribuerats.

## <a name="build-the-function-api"></a>Bygg funktions-API: et
1. Växla tillbaka till Azure AD-standardklienten i Azure Portal så att vi kan konfigurera objekt i din prenumeration igen 
1. Gå till bladet Function apps i Azure Portal, öppna din tomma Function-app och skapa sedan en ny In-Portal webhook + API-funktion via snabb starten.
1. Klistra in exempel koden nedan i Run. CSX över den befintliga koden som visas.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > Den c#-skript funktions kod som du nyss klistrade in loggar bara in en rad till funktions loggarna och returnerar texten "Hello World" med vissa dynamiska data (datum och tid).

3. Välj "integrera" på bladet till vänster och välj sedan "Avancerad redigerare" i det övre högra hörnet i fönstret.
4. Klistra in exempel koden nedan över den befintliga JSON-filen.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. Växla tillbaka till fliken HttpTrigger1, klicka på Hämta funktions webb adress och kopiera sedan den URL som visas.

   > [!NOTE]
   > Bindningarna som du nyss skapade ger dig bara funktioner för att svara på anonyma HTTP GET-begäranden till den URL som du nyss kopierade. ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ) Nu har vi ett skalbart Server lös https-API som kan returnera en mycket enkel nytto Last.
   > Nu kan du testa att anropa det här API: et från en webbläsare med hjälp av URL: en ovan. du kan också ta bort filen Code = Secret i URL: en och bevisa att Azure Functions returnerar ett 401-fel.

## <a name="configure-and-secure-the-function-api"></a>Konfigurera och skydda funktions-API: et
1. Två extra områden i Function-appen måste konfigureras (autentiserings-och nätverks begränsningar).
1. Börja med att konfigurera autentisering/auktorisering, så klicka på namnet på Function-appen (bredvid &lt; &gt; ikonen Z-funktioner) för att Visa översikts sidan.
1. Välj sedan fliken "plattforms funktioner" och välj "autentisering/auktorisering".
1. Aktivera funktionen för App Service autentisering.
1. Under "autentiseringsproviders" väljer du "Azure Active Directory" och väljer Avancerat från växeln hanterings läge.
1. Klistra in API: t för Server delens funktions-API (från Azure AD B2C i rutan klient-ID)
1. Klistra in den välkända konfigurations slut punkten med öppen ID från registrerings-eller inloggnings principen i rutan utfärdar-URL (vi har spelat in den här konfigurationen tidigare).
1. Välj OK.
1. Ange den åtgärd som ska vidtas när förfrågan inte är autentiserad till "logga in med Azure Active Directory" och klicka sedan på Spara.

   > [!NOTE]
   > Nu distribueras funktions-API: et och ska resultera i 401 svar om rätt nyckel inte anges och ska returnera data när en giltig begäran visas.
   > Du har lagt till ytterligare skydd mot djupgående säkerhet i EasyAuth genom att konfigurera alternativet "logga in med Azure AD" för att hantera oautentiserade begär Anden. Tänk på att detta ändrar beteendet för obehöriga begär Anden mellan backend-Funktionsapp och klient delens SPA som EasyAuth utfärdar en 302-omdirigering till AAD i stället för en 401 som inte har auktoriserats, korrigerar vi detta genom att använda API Management senare.
   > Vi har fortfarande ingen IP-säkerhet tillämpad, om du har en giltig nyckel-och OAuth2-token, kan vem som helst anropa detta från var som helst, så vi vill tvinga alla förfrågningar att komma via API Management.
   > Om du använder API Management förbruknings nivån kommer du inte att kunna utföra den här låsningen av VIP eftersom det inte finns någon dedikerad statisk IP-adress för den nivån. du måste förlita dig på metoden för att låsa API-anropen via den delade hemliga funktions nyckeln, så steg 11-13 kommer inte att vara möjliga.

1. Stäng bladet "autentisering/auktorisering" 
1. Välj nätverk och välj sedan åtkomst begränsningar
1. Sedan kan du låsa IP-adresserna för tillåten Function-app till API Management-instansen VIP. Denna VIP visas i avsnittet API Management-översikt i portalen.
1. Om du vill fortsätta att interagera med funktions portalen och utföra de valfria stegen nedan, bör du lägga till din egen offentliga IP-adress eller CIDR-intervall här.
1. När det finns en Tillåt-post i listan lägger Azure till en implicit neka-regel för att blockera alla andra adresser. 

Du måste lägga till CIDR-formaterade block med adresser i panelen IP-begränsningar. När du behöver lägga till en enskild adress, till exempel API Management VIP, måste du lägga till den i formatet xx. xx. xx. xx.

   > [!NOTE]
   > Nu ska funktions-API: et inte kunna anropas från någon annan stans än via API Management eller din adress.
   
## <a name="import-the-function-app-definition"></a>Importera Function-appens definition
1. Öppna *bladet API Management* och öppna sedan *instansen*.
1. Välj bladet API: er från API Management avsnittet i din instans.
1. I fönstret Lägg till ett nytt API väljer du Funktionsapp och väljer sedan fullständig längst upp i popup-fönstret.
1. Klicka på Bläddra, Välj den app som du är värd för API: et i och klicka på Välj.
1. Ge API: et ett namn och en beskrivning för API Managementens interna användning och Lägg till det i den obegränsade produkten.
1. Se till att du spelar in bas-URL: en för senare användning och klicka sedan på Skapa.

## <a name="configure-oauth2-for-api-management"></a>Konfigurera OAuth2 för API Management

1. Välj sedan det OAuth 2,0-bladet på fliken säkerhet och klicka på Lägg till
1. Ange värden för *visnings namn* och *Beskrivning* för den tillagda OAuth-slutpunkten (dessa värden visas i nästa steg som en OAuth2-slutpunkt).
1. Du kan ange valfritt värde i URL: en för klient registrerings sidan eftersom det här värdet inte används.
1. Kontrol lera den *implicita typen av autentiserings* beviljande och lämna godkännande typen checked.
1. Flytta till fälten för *auktorisering* och *token* -slutpunkt och ange de värden som du hämtade från det välkända XML-dokumentet för konfiguration tidigare.
1. Rulla nedåt och fyll i *ytterligare en Body-parameter* med namnet Resource med Server del FUNKTIONENS API-klientcertifikat från Azure AD B2C app-registreringen
1. Välj klientens autentiseringsuppgifter, ange klient-ID: t till appens app-ID för Developer-konsolen – hoppa över det här steget om du använder förbruknings API Managements modellen.
1. Ange klient hemligheten till nyckeln du registrerade tidigare – hoppa över det här steget om du använder förbruknings API Managements modellen.
1. Slutligen kan du registrera redirect_uri av auth Code-tilldelningen från API Management för senare användning.

## <a name="set-up-oauth2-for-your-api"></a>Konfigurera OAuth2 för ditt API
1. Ditt API visas till vänster i portalen under avsnittet "alla API: er" och öppnar ditt API genom att klicka på det.
1. Välj fliken Inställningar.
1. Uppdatera inställningarna genom att välja "OAuth 2,0" på alternativ knappen för användarautentisering.
1. Välj den OAuth-server som du definierade tidigare.
1. Markera kryss rutan Åsidosätt omfattning och ange den omfattning du registrerade för Server dels-API-anropet tidigare.

   > [!NOTE]
   > Nu har vi en API Management-instans som vet hur man får åtkomst till tokens från Azure AD B2C för att auktorisera begär Anden och förstår vår OAuth2 Azure Active Directory B2C-konfiguration.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Konfigurera **CORS** **-och validate-JWT-** principer

> Följande avsnitt bör följas oavsett vilken APIM-nivå som används. 

1. Växla tillbaka till fliken Design och välj alla API: er och klicka sedan på knappen kodvy för att Visa princip redigeraren.
1. Redigera avsnittet inkommande och klistra in nedanstående XML så att det ser ut ungefär så här.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. Redigera URL: en för OpenID-config så att den matchar den välkända Azure AD B2C slut punkten för registrerings-eller inloggnings principen.
1. Redigera anspråks värdet så att det matchar det giltiga program-ID: t, även kallat klient-ID för Server dels-API-programmet och spara.

   > [!NOTE]
   > Nu kan API Management svara på frågor över olika ursprung till JS SPA-appar, och den utför begränsning, hastighets begränsning och för validering av JWT auth-token som skickas innan begäran skickas till funktions-API: et.

   > [!NOTE]
   > Följande avsnitt är valfritt och gäller inte för **förbruknings** nivån, som inte stöder Developer-portalen.
   > Om du inte tänker använda Developer-portalen eller inte kan använda den eftersom du använder förbruknings nivån kan du hoppa över det här steget och gå direkt till ["skapa Java Script Spa för att använda API: et"](#build-the-javascript-spa-to-consume-the-api).

## <a name="optional-configure-the-developer-portal"></a>Valfritt Konfigurera Developer-portalen

1. Öppna bladet Azure AD B2C och gå till program registreringen för Developer-portalen
1. Ange "svars-URL"-posten till den som du antecknade när du konfigurerade redirect_uri av auth Code-tilldelningen i API Management tidigare.

   Nu när OAuth 2,0-användarauktorisering har Aktiver ATS på `Echo API` , hämtar Developer-konsolen en åtkomsttoken för användaren innan du anropar API: et.

1. Bläddra till en åtgärd under `Echo API` i Developer-portalen och välj **prova** att öppna Developer-konsolen.
1. Observera ett nytt objekt i avsnittet **auktorisering** , som motsvarar den auktoriseringsservern som du nyss lade till.
1. Välj **auktoriseringskod** i list rutan auktorisering och du uppmanas att logga in på Azure AD-klienten. Om du redan har loggat in med kontot kanske du inte uppmanas att göra det.
1. Efter lyckad inloggning `Authorization: Bearer` läggs en rubrik till i begäran med en åtkomsttoken från Azure AD B2C kodad i base64. 
1. Välj **Skicka** och du kan anropa API: et.

   > [!NOTE]
   > Nu kan API Management Hämta token för Developer-portalen för att testa ditt API och kunna förstå dess definition och återge lämplig test sida i dev-portalen.

1. Från översikts bladet i API Managements portalen klickar du på Developer Portal för att logga in som administratör för API: et.
1. Här kan du och andra valda konsumenter av ditt API testa och anropa dem från en-konsol.
1. Välj "produkter" och sedan "obegränsad" och välj sedan det API som vi skapade tidigare och klicka på "prova"
1. Ta fram API-prenumerationens nyckel och kopiera den till en säker plats tillsammans med URL: en för begäran som du behöver senare.
1. Välj även implicit, från List rutan OAuth-autentisering och du kan behöva autentisera detta med ett popup-fönster.
1. Klicka på "Skicka" och i så fall kan din Funktionsapp svara igen med ett hello-meddelande via API Management med ett 200 OK-meddelande och vissa JSON.

   > [!NOTE]
   > Grattis, nu har du Azure AD B2C, API Management och Azure Functions samar beta för att publicera, skydda och använda ett API. Du kanske har lagt märke till att API: t i själva verket skyddas två gånger med den här metoden, en gång med API Management OCP-Subscription-Key-huvud och en gång med auktorisering: Bearer JWT.
   > Du skulle vara korrekt, eftersom det här exemplet är ett program med en enda sida med Java Script, vi använder bara API Management nyckeln för Rate-Limiting-och fakturerings anrop.
   > Den faktiska auktoriseringen och autentiseringen hanteras av Azure AD B2C och kapslas in i JWT, som verifieras två gånger, en gång av API Management och sedan Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Bygg in Java Script SPA för att använda API: et
1. Öppna bladet lagrings konton i Azure Portal 
1. Välj det konto som du skapade och välj bladet "statisk webbplats" från avsnittet Inställningar (om du inte ser alternativet "statisk webbplats" kontrollerar du att du har skapat ett v2-konto).
1. Ställ in funktionen för statisk webb värd till "aktive rad" och ange index dokument namnet till "index.html" och klicka sedan på "Spara".
1. Anteckna innehållet i den primära slut punkten, eftersom den här platsen är den plats där klient dels platsen finns. 

   > [!NOTE]
   > Du kan använda Azure Blob Storage + CDN-omskrivning eller Azure App Service, men Blob Storage är värd för en statisk webbplats som innehåller en standard behållare för att hantera statiskt webb innehåll/HTML/JS/CSS från Azure Storage och kommer att ange en standard sida för oss för noll arbete.

## <a name="upload-the-js-spa-sample"></a>Överför SPA-exemplet för JS
1. På bladet lagrings konto väljer du bladet "blobs" från avsnittet BLOB service och klickar på den $web behållare som visas i den högra rutan.
1. Spara koden nedan till en fil lokalt på datorn som index.html och överför sedan filen index.html till $web-behållaren.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},
                    url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Bläddra till den primära slut punkten för den statiska webbplatsen som du sparade tidigare i det sista avsnittet.

   > [!NOTE]
   > Grattis, du har precis distribuerat en JavaScript-app med en enda sida som Azure Storage eftersom vi inte har konfigurerat JS-appen med dina nycklar för API: et eller konfigurerat JS-appen med din Azure AD B2C information ännu – sidan fungerar inte ännu om du öppnar den.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Konfigurera JS SPA för Azure AD B2C
1. Nu vet vi var allt är: vi kan konfigurera SPA med lämplig API Management-API-adress och rätt Azure AD B2C program/klient-ID
1. Gå tillbaka till bladet Azure Portal lagring och klicka på index.html och välj sedan redigera BLOB 
1. Uppdatera auth-informationen så att den matchar ditt frontend-program som du registrerade i B2C tidigare, och Observera att värdena "b2cScopes" är för API-Server delen.
1. Du hittar webApi-nyckeln och API-URL: en i API Management test fönstret för API-åtgärden.
1. Skapa en prenumerations nyckel för APIM per rubrik till API Management tillbaka till bladet API Management, Välj prenumerationer och klicka på Lägg till prenumeration och spara sedan posten. Om du klickar på ellipsen (...) bredvid den skapade raden kan du Visa nycklarna så att du kan kopiera den primära nyckeln.
1. Det bör se ut ungefär så här:-  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. Klicka på Spara

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Ange omdirigerings-URI: er för Azure AD B2C frontend-appen
1. Öppna bladet Azure AD B2C och gå till program registreringen för JavaScript-frontend-programmet
1. Ange omdirigerings-URL: en till den som du antecknade när du tidigare konfigurerade den statiska platsens primära slut punkt ovan

   > [!NOTE] 
   > Den här konfigurationen leder till att en klient med klient dels programmet tar emot en åtkomsttoken med lämpliga anspråk från Azure AD B2C.
   > SPA kommer att kunna lägga till detta som en Bearer-token i https-huvudet i anropet till Server dels-API: et.
   > API Management validerar token, hastighets begränsnings anrop till slut punkten av prenumerantens nyckel, innan de skickas genom begäran till mottagnings-API: t för Azure function.
   > SPA kommer att återge svaret i webbläsaren.

   > *Grattis, du har konfigurerat Azure AD B2C, Azure API Management Azure Functions, Azure App Service behörighet att arbeta med perfekt harmoniregeln!*

   > [!NOTE]
   > Nu har vi en enkel app med en enkel säker API som vi testar.

## <a name="test-the-client-application"></a>Testa klient programmet
1. Öppna den exempel-App-URL som du antecknade från det lagrings konto som du skapade tidigare
1. Klicka på "logga in" i det övre högra hörnet, så öppnas det Azure AD B2C registrera dig eller logga in i profilen.
1. Efter inloggning i avsnittet "logga in som" på skärmen fylls det från ditt JWT.
1. Klicka på "anropa webb-API" och uppdatera sidan med värdena som skickas tillbaka från ditt säkra API.

## <a name="and-were-done"></a>Och vi är klara
Stegen ovan kan anpassas och redige ras för att tillåta många olika användningar av Azure AD B2C med API Management.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Azure Active Directory och OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md).
* Se fler [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API Management.
* Andra sätt att skydda Server dels tjänsten finns i [ömsesidig certifikatautentisering](api-management-howto-mutual-certificates.md).
* [Skapa en API Management tjänst instans](get-started-create-service-instance.md).
* [Hantera ditt första API](import-and-publish.md).
