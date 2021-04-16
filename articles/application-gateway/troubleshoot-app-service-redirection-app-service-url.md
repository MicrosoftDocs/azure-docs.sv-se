---
title: Felsöka omdirigering till App Service URL
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller information om hur du felsöker omdirigeringsproblemet när Azure Application Gateway används med Azure App Service
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/15/2021
ms.author: jaysoni
ms.openlocfilehash: 6aad1cf1269a7c3dc082482c39fdc4a079fc3240
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514894"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Felsöka App Service i Application Gateway

Lär dig hur du diagnostiserar och löser problem som kan uppstå när Azure App Service används som ett backend-mål med Azure Application Gateway.

## <a name="overview"></a>Översikt

I den här artikeln får du lära dig hur du felsöker följande problem:

* App Service-URL:en exponeras i webbläsaren när det finns en omdirigering.
* App service ARRAffinity-cookiedomänen är inställd på apptjänstens värdnamn, example.azurewebsites.net, i stället för den ursprungliga värden.

När ett backend-program skickar ett omdirigeringssvar kanske du vill omdirigera klienten till en annan URL än den som anges av backend-programmet. Du kanske vill göra detta när en apptjänst finns bakom en programgateway och kräver att klienten gör en omdirigering till den relativa sökvägen. Ett exempel är en omdirigering från contoso.azurewebsites.net/path1 till contoso.azurewebsites.net/path2. 

När App Service skickar ett omdirigeringssvar använder den samma värdnamn i platshuvudet för svaret som i begäran som tas emot från Programgatewayen. Klienten skickar till exempel begäran direkt till contoso.azurewebsites.net/path2 i stället för att gå via programgatewayen contoso.com/path2. Du vill inte kringgå programgatewayen.

Det här problemet kan inträffa av följande huvudorsaker:

- Du har konfigurerat omdirigering i apptjänsten. Omdirigering kan vara så enkelt som att lägga till ett avslutande snedstreck i begäran.
- Du har Azure Active Directory autentisering, vilket orsakar omdirigeringen.

När du använder apptjänster bakom en programgateway, skiljer sig domännamnet som är associerat med programgatewayen (example.com) från apptjänstens domännamn (till exempel example.azurewebsites.net). Domänvärdet för ARRAffinity-cookien som angetts av apptjänsten innehåller example.azurewebsites.net domännamnet, vilket inte är önskvärt. Det ursprungliga värdnamnet, example.com, ska vara domännamnsvärdet i cookien.

## <a name="sample-configuration"></a>Exempelkonfiguration

- HTTP-lyssnare: Grundläggande eller flera webbplatser
- Backend-adresspool: App Service
- HTTP-inställningar: **Välj Värdnamn från Backend-adress aktiverat**
- Avsökning: **Välj Värdnamn från HTTP-inställningar aktiverat**

## <a name="cause"></a>Orsak

App Service är en tjänst för flera klienttjänster, så den använder värdhuvudet i begäran för att dirigera begäran till rätt slutpunkt. Standarddomännamnet för App Services, *.azurewebsites.net (till exempel contoso.azurewebsites.net), skiljer sig från programgatewayens domännamn (till exempel contoso.com). 

Den ursprungliga begäran från klienten har programgatewayens domännamn, contoso.com, som värdnamn. Du måste konfigurera programgatewayen för att ändra värdnamnet i den ursprungliga begäran till App Service-värdnamnet när den dirigerar begäran till App Service-servern. Använd växeln **Välj Värdnamn från Serveradress i** programgatewayens HTTP-inställningskonfiguration. Använd växeln **Pick Hostname from Backend HTTP Settings (Välj värdnamn från BACKEND HTTP Settings)** i hälsoavsökningens konfiguration.



![Programgatewayen ändrar värdnamnet](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

När apptjänsten gör en omdirigering används det åsidosatta värdnamnet contoso.azurewebsites.net i platsrubriken i stället för det ursprungliga värdnamnet som contoso.com, om inget annat har konfigurerats. Kontrollera följande exempel på begärande- och svarshuvuden.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
I föregående exempel ser du att svarshuvudet har statuskoden 301 för omdirigering. Platsrubriken har apptjänstens värdnamn i stället för det ursprungliga värdnamnet `www.contoso.com` .

## <a name="solution-rewrite-the-location-header"></a>Lösning: Skriva om platsrubriken

Ange värdnamnet i platsrubriken till programgatewayens domännamn. Det gör du genom att skapa [en omskrivningsregel](./rewrite-http-headers.md) med ett villkor som utvärderar om platsrubriken i svaret innehåller azurewebsites.net. Den måste också utföra en åtgärd för att skriva om platsrubriken så att den har programgatewayens värdnamn. Mer information finns i instruktioner om [hur du skriver om platsrubriken](./rewrite-http-headers.md#modify-a-redirection-url).

> [!NOTE]
> Stöd för omskrivning av HTTP-huvud är endast tillgängligt för Standard_v2 och [WAF_v2 SKU](./application-gateway-autoscaling-zone-redundant.md) för Application Gateway. Vi rekommenderar [att du migrerar till v2](./migrate-v1-v2.md) för omskrivning av huvud och [andra avancerade funktioner](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) som är tillgängliga med v2 SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternativ lösning: Använd ett anpassat domännamn

Med App Service är Custom Domain en annan lösning för att alltid omdirigera trafiken till Application Gateway domännamnet ( `www.contoso.com` i vårt exempel). Den här konfigurationen fungerar också som en lösning på problemet med ARR-tillhörighetscookie. Som standard är ARRAffinity-cookiedomänen inställd på App Service:s standardvärdnamn (example.azurewebsites.net) i stället för Application Gateway-domänens domännamn. Därför avvisar webbläsaren i sådana fall cookien på grund av skillnaden i domännamnen för begäran och cookien.

Du kan följa den angivna metoden för både omdirigerings- och ARRAffinity-problem med matchningsfel för cookiedomäner. Med den här metoden måste du ha åtkomst till din anpassade domäns DNS-zon.

**Steg 1:** Ange en Custom Domain i App Service verifiera domänägarskapet genom att lägga till [CNAME-& TXT DNS-posterna](../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id).
Posterna skulle se ut ungefär så här
-  `www.contoso.com` I CNAME `contoso.azurewebsite.net`
-  `asuid.www.contoso.com` I TXT `<verification id string>` " "


**Steg 2:** CNAME-posten i föregående steg behövdes bara för domänverifieringen. I slutändan behöver vi att trafiken dirigeras via Application Gateway. Du kan därför ändra `www.contoso.com` CNAME nu så att det pekar på Application Gateway FQDN. Om du vill ange ett FQDN för Application Gateway navigerar du till dess offentliga IP-adressresurs och tilldelar den en "DNS-namnetikett". Den uppdaterade CNAME-posten bör nu se ut som 
-  `www.contoso.com` I CNAME `contoso.eastus.cloudapp.azure.com`


**Steg 3:** Inaktivera "Välj värdnamn från backend-adress" för den associerade HTTP-inställningen.

Använd inte växeln i kommandot `-PickHostNameFromBackendAddress` i PowerShell. `Set-AzApplicationGatewayBackendHttpSettings`


**Steg4:** För att avsökningarna ska fastställa att backend är felfri och drifttrafik anger du en anpassad hälsoavsökning med fältet Värd som anpassad domän eller standarddomän för App Service.

Använd inte växeln i kommandot i PowerShell och använd antingen den anpassade domänen eller standarddomänen för App Service i växeln `-PickHostNameFromBackendHttpSettings` `Set-AzApplicationGatewayProbeConfig` -HostName för avsökningen.

Om du vill implementera föregående steg med Hjälp av PowerShell för en befintlig installation använder du powershell-exempelskriptet som följer. Observera att vi inte har använt växlarna **-PickHostname** i konfigurationen av avsöknings- och HTTP-inställningar.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Nästa steg

Om ovanstående steg inte löste problemet öppnar du en [supportbiljett.](https://azure.microsoft.com/support/options/)
