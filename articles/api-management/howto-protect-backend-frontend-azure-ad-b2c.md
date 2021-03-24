---
title: Skydda SPA-backend i Azure API Management med Active Directory B2C
description: Skydda ett API med OAuth 2,0 med hjälp av Azure Active Directory B2C, Azure API Management och enkel autentisering som ska anropas från ett Java Script SPA med det PKCE-aktiverade SPA-auth-flödet.
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
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: baa6a0a6995e206924d14de25b98700e450f3a0c
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954929"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Skydda SPA-backend med OAuth 2,0, Azure Active Directory B2C och Azure API Management

Det här scenariot visar hur du konfigurerar Azure API Management-instansen för att skydda ett API.
Vi använder Azure AD B2C SPA (auth Code + PKCE)-flödet för att hämta en token, tillsammans med API Management för att skydda en Azure Functions Server del med EasyAuth.

## <a name="aims"></a>Syftar

Vi ska se hur API Management kan användas i ett förenklat scenario med Azure Functions och Azure AD B2C. Du ska skapa en JavaScript-app (JS) som anropar en API, som loggar in användare med Azure AD B2C. Sedan använder du API Management validera-JWT-, CORS-och Rate-begränsningar med hjälp av nyckel princip funktioner för att skydda Server dels-API: et.

För att få skydd i djupet använder vi EasyAuth för att validera token igen inuti Server dels-API: et och se till att API Management är den enda tjänst som kan anropa den Azure Functions Server delen.

## <a name="what-will-you-learn"></a>Vad kommer du att lära dig

> [!div class="checklist"]
> * Installation av en enda sida-app och Server dels-API i Azure Active Directory B2C
> * Skapa en Azure Functions Server dels-API
> * Importera ett Azure Functions-API till Azure API Management
> * Skydda API: et i Azure API Management
> * Anropar slut punkterna för Azure Active Directory B2C-auktorisering via biblioteken för Microsoft Identity Platform (MSAL.js)
> * Lagra ett program med en HTML/vanilj JS-sida och betjäna det från en Azure Blob Storage-slutpunkt

## <a name="prerequisites"></a>Förutsättningar

För att följa stegen i den här artikeln måste du ha:

* Ett Azure-Generell användning (StorageV2) v2-lagrings konto som värd för frontend JS-appen med en sida.
* En Azure API Management-instans (vilken nivå som helst fungerar, inklusive "förbrukning", men vissa funktioner som är tillämpliga för det fullständiga scenariot är inte tillgängliga i den här nivån (hastighets begränsning och dedikerad virtuell IP), dessa begränsningar benämns nedan i artikeln där det är lämpligt).
* En tom Azure Function-app (som kör V 3.1 .NET Core runtime, i en förbruknings plan) som värd för den anropade API: n
* En Azure AD B2C klient, som är länkad till en prenumeration.

Även om du i praktiken använder resurser i samma region i produktions arbets belastningar, för den här instruktions artikeln är distributions området inte viktigt.

## <a name="overview"></a>Översikt

Här är en illustration av de komponenter som används och flödet mellan dem när den här processen har slutförts.
![Komponenter som används och Flow](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Komponenter som används och Flow")

Här är en snabb översikt över stegen:

1. Skapa Azure AD B2C anropa (frontend, API Management) och API-program med omfattningar och bevilja API-åtkomst
1. Skapa registrerings-och inloggnings principer så att användarna kan logga in med Azure AD B2C 
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

   > [!TIP]
   > Vi kommer att samla in några få informations-och nyckel uppgifter när vi vägleder det här dokumentet. det kan vara praktiskt att ha en text redigerare öppen för att lagra följande konfigurations objekt tillfälligt.  
   >
   > KLIENT-ID FÖR B2C-SERVER:  
   > HEMLIG NYCKEL FÖR B2C-BACKEND-KLIENT:  
   > URI FÖR B2C BACKEND API-OMFATTNING:  
   > KLIENT-ID FÖR B2C-KLIENT:  
   > SLUT PUNKTS-URI FÖR ANVÄNDAR FLÖDE FÖR B2C:  
   > B2C-VÄLKÄND OPENID-SLUTPUNKT:   
   > B2C-princip namn: URL för Frontendapp_signupandsignin funktion:  
   > APIM API-BAS-URL: URL FÖR PRIMÄR SLUT PUNKT FÖR LAGRING:  

## <a name="configure-the-backend-application"></a>Konfigurera Server dels programmet

Öppna bladet Azure AD B2C på portalen och utför följande steg.

1. Välj fliken **registrerings program**
1. Klicka på knappen ny registrering.
1. Välj webb från val rutan omdirigerings-URI.
1. Ange nu visnings namnet och välj något unikt och relevant för den tjänst som skapas. I det här exemplet kommer vi att använda namnet "backend-program".
1. Använd plats hållare för svars-URL: er, t. ex. https://jwt.ms (en underkodad token från Microsoft som ägs av Microsoft), och uppdatera dessa URL: er senare.
1. Se till att du har valt alternativet "konton i valfri identitets leverantör eller organisations katalog (för att autentisera användare med användar flöden)"
1. I det här exemplet avmarkerar du kryss rutan "bevilja administrativt medgivande", eftersom vi inte behöver offline_access behörigheter idag.
1. Klicka på Registrera.
1. Registrera klient-ID för backend-programmet för senare användning (visas under program (klient) ID).
1. Välj fliken *certifikat och hemligheter* (under hantera) och klicka sedan på ny klient hemlighet för att generera en auth-nyckel (acceptera standardinställningarna och klicka på Lägg till).
1. När du klickar på "Lägg till" kopierar du nyckeln (under "värde") någon säker för senare användning som "backend-klientens hemlighet" – Observera att den här dialog rutan är den enda chansen att du måste kopiera den här nyckeln.
1. Välj nu fliken *exponera en API* (under hantera).
1. Du uppmanas att ange AppID URI, välja och registrera standardvärdet.
1. Skapa och namnge omfånget "Hello" för funktions-API: et. du kan använda frasen "Hej" för alla alternativ som kan väljas, registrera det ifyllda fullständiga scope-värdet URI och klicka på Lägg till omfång.
1. Gå tillbaka till roten för bladet Azure AD B2C genom att välja "Azure AD B2C"-spåret överst till vänster i portalen.

   > [!NOTE]
   > Azure AD B2C omfattningar är effektiva behörigheter inom ditt API som andra program kan begära åtkomst till via bladet för API-åtkomst från sina program, vilket effektivt precis har skapat program behörigheter för ditt anropade API.

## <a name="configure-the-frontend-application"></a>Konfigurera klient dels programmet

1. Välj fliken **registrerings program**
1. Klicka på knappen ny registrering.
1. Välj "enskild sida" (SPA) "från val rutan omdirigerings-URI.
1. Ange nu visnings namnet och AppID URI och välj något unikt och relevant för klient dels programmet som ska använda den här AAD B2C app-registreringen. I det här exemplet kan du använda "frontend-program"
1. När du har registrerat den första appen lämnar du valet av konto typer som stöds till standard (autentisera användare med användar flöden)
1. Använd plats hållare för svars-URL: er, t. ex. https://jwt.ms (en underkodad token från Microsoft som ägs av Microsoft), och uppdatera dessa URL: er senare.
1. Lämna rutan bevilja administratörs medgivande
1. Klicka på Registrera.
1. Registrera klient-ID för klient dels programmet för senare användning (visas under program (klient) ID).
1. Växla till fliken *API-behörigheter* .
1. Bevilja åtkomst till backend-programmet genom att klicka på Lägg till en behörighet, sedan på mina API: er, Välj backend-program, Välj behörigheter, Välj det omfång som du skapade i föregående avsnitt och klicka på Lägg till behörigheter
1. Klicka på bevilja administrativt medgivande för {Tenant} och klicka på Ja i popup-dialog rutan. Den här popup-rutan skickar "klient dels programmet" för att använda behörigheten "Hello" som definieras i "backend-programmet" som skapades tidigare.
1. Alla behörigheter ska nu visas för appen som ett grönt skal under kolumnen Status

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Skapa ett användar flöde för "Registrera dig och logga in"

1. Gå tillbaka till roten för bladet B2C genom att välja Azure AD B2C dynamiska länkar.
1. Växla till fliken Användarflöden (under principer).
1. Klicka på "nytt användar flöde"
1. Välj användar flödes typen "Registrera dig och logga in" och välj "rekommenderas" och sedan ' skapa '
1. Ge principen ett namn och registrera den för senare. I det här exemplet kan du använda "Frontendapp_signupandsignin", Tänk på att detta ska föregås av "B2C_1_" för att göra "B2C_1_Frontendapp_signupandsignin"
1. Under "identitets leverantörer" och "lokala konton" markerar du "e-postregistrering" (eller "användar-ID-registrering" beroende på konfigurationen av B2C-klienten) och klickar på OK. Den här konfigurationen är att registrera lokala B2C-konton, inte att härleda till en annan identitetsprovider (t. ex. en social identitetsprovider) att använda en användares befintliga konton för sociala medier.
1. Lämna standardinställningarna för MFA-och villkorlig åtkomst.
1. Under "användarattribut och anspråk" klickar du på "Visa fler..." Välj sedan de anspråks alternativ som du vill att användarna ska ange och ha returnerat i token. Kontrol lera åtminstone visnings namnet och e-postadressen för att samla in, med visnings namnet och e-postadresserna för att returnera (Läs noggrant noga på det faktum att du samlar in EmailAddress, singular och ber att returnera e-postadresser, flera) och klicka på "skapa".
1. Klicka på det användar flöde som du skapade i listan och klicka sedan på knappen Kör användar flöde.
1. Den här åtgärden öppnar bladet kör användar flöde, väljer klient dels programmet, kopierar slut punkten för användar flödet och sparar den för senare.
1. Kopiera och lagra länken överst och registrera dig som den välkända OpenID konfigurations slut punkt för senare användning.

   > [!NOTE]
   > Med B2C-principer kan du exponera Azure AD B2C inloggnings slut punkter för att kunna avbilda olika data komponenter och logga in användare på olika sätt.
   > 
   > I det här fallet konfigurerade vi ett registrerings-eller inloggnings flöde (princip). Detta exponerade även en välkänd konfigurations slut punkt, i båda fallen har vår skapade princip identifierats i URL: en med frågesträngparametern "p =".  
   >
   > När detta är gjort har du nu en fungerande verksamhet till en konsument identitets plattform som ska logga in användare i flera program.

## <a name="build-the-function-api"></a>Bygg funktions-API: et

1. Växla tillbaka till Azure AD-standardklienten i Azure Portal så att vi kan konfigurera objekt i prenumerationen igen.
1. Gå till bladet Function apps i Azure Portal, öppna din tomma Function-app, klicka på functions, klicka på Lägg till.
1. I den utfällande som visas väljer du "utveckla i portalen" under "Välj en mall" och sedan "HTTP-utlösare" under mal LINY information Name ' Hej ' med autentiseringsnivå ' function ' och väljer sedan Lägg till.
1. Växla till bladet kod + test och kopiera – klistra in exempel koden från nedan *över den befintliga koden* som visas.
1. Välj Spara

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

   > [!TIP]
   > Den c#-skript funktions kod som du nyss klistrade in loggar bara in en rad till funktions loggarna och returnerar texten "Hello World" med vissa dynamiska data (datum och tid).

1. Välj "integration" på bladet till vänster och klicka sedan på http-länken (req) i rutan utlösare.
1. I list rutan "markerade HTTP-metoder" avmarkerar du http POST-metoden och lämnar bara markerat och klickar sedan på Spara.
1. Växla tillbaka till fliken kod + test, klicka på Hämta funktions webb adress och kopiera sedan den URL som visas och spara den för senare.

   > [!NOTE]
   > Bindningarna som du nyss skapade ger dig bara funktioner för att svara på anonyma HTTP GET-begäranden till den URL som du precis kopierade ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ). Nu har vi ett skalbart Server lös https API som kan returnera en mycket enkel nytto Last.
   >
   > Nu kan du testa att anropa detta API från en webbläsare med din version av URL: en som du precis kopierade och sparade. Du kan också ta bort frågesträngparametern "? Code = secretkey" i URL-adressen och testa igen för att bevisa att Azure Functions returnerar ett 401-fel.

## <a name="configure-and-secure-the-function-api"></a>Konfigurera och skydda funktions-API: et

1. Två extra områden i Function-appen måste konfigureras (auktoriserings-och nätverks begränsningar).
1. Vi börjar med att konfigurera autentisering/auktorisering, så gå tillbaka till rot bladet i Function-appen via dynamiska objekt.
1. Välj sedan ' autentisering/auktorisering ' (under ' Inställningar ').
1. Aktivera funktionen för App Service autentisering.
1. Ange den åtgärd som ska vidtas när förfrågan inte är autentiserad att logga in med Azure Active Directory.
1. Under "autentiseringsproviders" väljer du "Azure Active Directory".
1. Välj Avancerat från växeln hanterings läge.
1. Klistra in Server dels programmets [Application]-klient-ID (från Azure AD B2C) i rutan klient-ID
1. Klistra in den välkända konfigurations slut punkten med öppen ID från registrerings-och inloggnings principen i rutan utfärdar-URL (vi har spelat in den här konfigurationen tidigare).
1. Klicka på "Visa hemlighet" och klistra in backend-programmets klient hemlighet i lämplig ruta.
1. Välj OK, som tar dig tillbaka till bladet för val av identitetsprovider/skärm.
1. Lämna [token Store](../app-service/overview-authentication-authorization.md#token-store) aktiverat under avancerade inställningar (standard).
1. Klicka på Spara (längst upp till vänster på bladet).

   > [!IMPORTANT]
   > Nu distribueras funktions-API: et och ska resultera i 401 svar om rätt JWT inte anges som en auktorisering: Bearer-huvud och bör returnera data när en giltig begäran visas.  
   > Du har lagt till ytterligare skydd mot djupgående säkerhet i EasyAuth genom att konfigurera alternativet "logga in med Azure AD" för att hantera oautentiserade begär Anden. Tänk på att detta ändrar beteendet för obehöriga begär Anden mellan backend-Funktionsapp och klient delens SPA som EasyAuth utfärdar en 302-omdirigering till AAD i stället för en 401 som inte har auktoriserats, korrigerar vi detta genom att använda API Management senare.  
   >
   > Vi har fortfarande ingen IP-säkerhet tillämpad, om du har en giltig nyckel-och OAuth2-token, kan vem som helst anropa detta från var som helst, så vi vill tvinga alla förfrågningar att komma via API Management.  
   > 
   > Om du använder APIM förbruknings nivå [finns det ingen dedikerad Azure API Management virtuell IP-adress](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) för att tillåta-lista med funktionen åtkomst begränsningar. I Azure API Management standard SKU: n och över [VIP är en enskild klient och för resursens livs längd](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses). För användnings nivån Azure API Management kan du låsa API-anropen via den delade hemliga funktions nyckeln i den del av URI: n som du kopierade ovan. För förbruknings nivån är steg 12-17 inte heller tillämpliga.

1. Stäng bladet "autentisering/auktorisering" 
1. Öppna *bladet API Management i portalen* och öppna sedan *instansen*.
1. Registrera den privata VIP som visas på fliken Översikt.
1. Gå tillbaka till *bladet Azure Functions på portalen* och öppna sedan *instansen* igen.
1. Välj nätverk och välj sedan konfigurera åtkomst begränsningar
1. Klicka på Lägg till regel och ange den VIP som kopierades i steg 3 ovan i formatet xx. xx. xx. xx/32.
1. Om du vill fortsätta att interagera med funktions portalen och utföra de valfria stegen nedan, bör du lägga till din egen offentliga IP-adress eller CIDR-intervall här.
1. När det finns en Tillåt-post i listan lägger Azure till en implicit neka-regel för att blockera alla andra adresser.

Du måste lägga till CIDR-formaterade block med adresser i panelen IP-begränsningar. När du behöver lägga till en enskild adress, till exempel API Management VIP, måste du lägga till den i formatet xx. xx. xx. xx/32.

   > [!NOTE]
   > Nu ska funktions-API: et inte kunna anropas från någon annan stans än via API Management eller din adress.

1. Öppna *bladet API Management* och öppna sedan *instansen*.
1. Välj bladet API: er (under API: er).
1. I fönstret Lägg till ett nytt API väljer du Funktionsapp och väljer sedan fullständig längst upp i popup-fönstret.
1. Klicka på Bläddra, Välj den app som du är värd för API: et i och klicka på Välj. Klicka sedan på Välj igen.
1. Ge API: et ett namn och en beskrivning för API Managementens interna användning och Lägg till det i den obegränsade produkten.
1. Kopiera och registrera API: s bas-URL och klicka på Skapa.
1. Klicka på fliken Inställningar, under prenumeration – Stäng av kryss rutan "prenumeration krävs" eftersom vi ska använda OAuth JWT-token i det här fallet för hastighets begränsning. Observera att om du använder förbruknings nivån krävs det fortfarande i en produktions miljö. 

   > [!TIP]
   > Om du använder förbruknings nivån APIM är den obegränsade produkten inte tillgänglig som en del av lådan. Gå i stället till "Products" under "API: er" och tryck på "Lägg till".  
   > Skriv "obegränsat" som produkt namn och beskrivning och välj det API som du nyss lade till från bild texten "+" längst ned till vänster på skärmen. Markera kryss rutan publicerad. Lämna resten som standard. Klicka slutligen på knappen "skapa". Detta skapade den "obegränsad" produkten och tilldelade den till ditt API. Du kan anpassa den nya produkten senare.

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>Konfigurera och avbilda rätt inställningar för lagrings slut punkt

1. Öppna bladet lagrings konton i Azure Portal 
1. Välj det konto som du skapade och välj bladet "statisk webbplats" från avsnittet Inställningar (om du inte ser alternativet "statisk webbplats" kontrollerar du att du har skapat ett v2-konto).
1. Ställ in funktionen för statisk webb värd till "aktive rad" och ange index dokument namnet till "index.html" och klicka sedan på "Spara".
1. Anteckna innehållet i den primära slut punkten för senare, eftersom den här platsen är den plats där klient dels platsen finns.

   > [!TIP]
   > Du kan använda Azure Blob Storage + CDN-omskrivning eller Azure App Service som värd för funktionen SPA-men Blob Storage som värd för en statisk webbplats ger oss en standard behållare för att hantera statiskt webb innehåll/HTML/JS/CSS från Azure Storage och kommer att härleda en standard sida för oss för noll arbete.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Konfigurera **CORS** **-och validate-JWT-** principer

> Följande avsnitt bör följas oavsett vilken APIM-nivå som används. Lagrings kontots URL är från det lagrings konto som du har gjort tillgängligt från kraven överst i den här artikeln.
1. Växla till API Management-bladet i portalen och öppna instansen.
1. Välj API: er och välj sedan alla API: er.
1. Under "inkommande bearbetning" klickar du på knappen kodvy "</>" för att Visa princip redigeraren.
1. Redigera avsnittet inkommande och klistra in nedanstående XML så att det ser ut ungefär så här.
1. Ersätt följande parametrar i principen
1. {PrimaryStorageEndpoint} (Den primära lagrings slut punkten som du kopierade i föregående avsnitt), {b2cpolicy-välkänd-OpenID} (den välkända OpenID konfigurations slut punkten som du kopierade tidigare) och {backend-API-application-Client-ID} (B2C-program/klient-ID för **Server dels-API: et**) med rätt värden sparade tidigare.
1. Om du använder förbruknings nivån för API Management bör du ta bort både principen för begränsning efter nyckel eftersom den här principen inte är tillgänglig när du använder användnings nivån i Azure API Management.

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > Nu kan Azure API Management svara på kors ursprungs begär Anden från dina JavaScript SPA-appar och den utför begränsning, hastighets begränsning och för validering av JWT auth-token som skickas innan du vidarebefordrar begäran till Function API.
   > 
   > Grattis, nu har du Azure AD B2C, API Management och Azure Functions samar beta för att publicera, skydda och använda en API!

   > [!TIP]
   > Om du använder API Management förbruknings nivå i stället för hastighets begränsning av JWT-ämnet eller inkommande IP-adress (begränsa anrops frekvensen per nyckel princip stöds inte idag för nivån "förbrukning"), kan du begränsa efter anrops frekvens kvoten [här](./api-management-access-restriction-policies.md#LimitCallRate).  
   > Det här exemplet är ett program med en enda sida med Java Script, men vi använder bara API Management nyckeln för Rate-Limiting-och fakturerings anrop. Den faktiska auktoriseringen och autentiseringen hanteras av Azure AD B2C och kapslas in i JWT, som verifieras två gånger, en gång av API Management, och sedan av Server delen Azure function.

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>Ladda upp exempel skriptet SPA till statisk lagring

1. På bladet lagrings konto väljer du bladet behållare från avsnittet BLOB service och klickar på den $web behållare som visas i den högra rutan.
1. Spara koden nedan till en fil lokalt på datorn som index.html och överför sedan filen index.html till $web-behållaren.

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. Bläddra till den primära slut punkten för den statiska webbplatsen som du sparade tidigare i det sista avsnittet.

   > [!NOTE]
   > Grattis, du har precis distribuerat en JavaScript-app med ett enda sidfel till Azure Storage statiskt innehåll som värd.  
   > Eftersom vi inte har konfigurerat JS-appen med Azure AD B2C information ännu – sidan fungerar inte ännu om du öppnar den.

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>Konfigurera Java Script SPA för Azure AD B2C

1. Nu vet vi var allt är: vi kan konfigurera SPA med lämplig API Management-API-adress och rätt Azure AD B2C program/klient-ID.
1. Gå tillbaka till bladet Azure Portal lagring 
1. Välj behållare (under Inställningar) 
1. Välj behållaren $web i listan
1. Välj index.html BLOB i listan 
1. Klicka på Redigera 
1. Uppdatera auth-värdena i msal config-avsnittet så att de matchar ditt *klient* program som du registrerade i B2C tidigare. Använd kod kommentarer för tips om hur konfigurations värden ska se ut.
*Auktoritet* svärdet måste vara i formatet:-https://{b2ctenantname}. b2clogin. com/TFP/{b2ctenantname}. onmicrosoft. com}/{signupandsigninpolicyname}, om du har använt våra exempel namn och din B2C-klient kallas "contoso", förväntar du att utfärdaren ska vara https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin .
1. Ange API-värdena som matchar din server dels adress (API-bas-URL: en som du registrerade tidigare och värdena "b2cScopes" registrerades tidigare för *Server dels programmet*).
1. Klicka på Spara

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Ange omdirigerings-URI: er för Azure AD B2C frontend-appen

1. Öppna bladet Azure AD B2C och navigera till program registreringen för program varan för JavaScript-frontend.
1. Klicka på omdirigering av URI: er och ta bort plats hållaren som https://jwt.ms du angav tidigare.
1. Lägg till en ny URI för den primära (lagring) slut punkten (minus det avslutande snedstrecket).

   > [!NOTE]
   > Den här konfigurationen leder till att en klient med klient dels programmet tar emot en åtkomsttoken med lämpliga anspråk från Azure AD B2C.  
   > SPA kommer att kunna lägga till detta som en Bearer-token i https-huvudet i anropet till Server dels-API: et.  
   > 
   > API Management validerar token, hastighets begränsnings anrop till slut punkten av både ämnet för det JWT som utfärdats av Azure ID (användaren) och IP-adressen för anroparen (beroende på tjänst nivå för API Management, se kommentaren ovan) innan du skickar genom förfrågan till den mottagande Azure Function API, så att du lägger till funktions säkerhets nyckeln.  
   > SPA kommer att återge svaret i webbläsaren.
   >
   > *Grattis, du har konfigurerat Azure AD B2C, Azure API Management Azure Functions, Azure App Service behörighet att arbeta med perfekt harmoniregeln!*

Nu har vi en enkel app med en enkel säker API som vi testar.

## <a name="test-the-client-application"></a>Testa klient programmet

1. Öppna den exempel-App-URL som du antecknade från det lagrings konto som du skapade tidigare.
1. Klicka på "logga in" i det övre högra hörnet, så öppnas det Azure AD B2C registrera dig eller logga in i profilen.
1. Appen bör Välkommen till ditt B2C-profil namn.
1. Klicka på anropa API och uppdatera med värdena som skickas tillbaka från ditt säkra API.
1. Om du *upprepade gånger* klickar på anrops-API-knappen och du kör på Developer-nivån eller ovanför API Management bör du tänka på att din lösning börjar bedöma API: et och den här funktionen bör rapporteras i appen med ett lämpligt meddelande.

## <a name="and-were-done"></a>Och vi är klara

Stegen ovan kan anpassas och redige ras för att tillåta många olika användningar av Azure AD B2C med API Management.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Active Directory och OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md).
* Se fler [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API Management.
* Andra sätt att skydda Server dels tjänsten finns i [ömsesidig certifikatautentisering](api-management-howto-mutual-certificates.md).
* [Skapa en API Management tjänst instans](get-started-create-service-instance.md).
* [Hantera ditt första API](import-and-publish.md).