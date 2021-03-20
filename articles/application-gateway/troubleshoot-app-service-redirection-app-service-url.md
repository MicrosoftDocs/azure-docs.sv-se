---
title: Felsöka omdirigering till App Service-URL
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller information om hur du felsöker problemet vid omdirigering när Azure Application Gateway används med Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 1cc7df755198461643703cac988c8c31f2ac25db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182894"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Felsöka App Service problem i Application Gateway

Lär dig hur du diagnostiserar och löser problem som kan uppstå när Azure App Service används som backend-mål med Azure Application Gateway.

## <a name="overview"></a>Översikt

I den här artikeln får du lära dig hur du felsöker följande problem:

* App Service-URL: en visas i webbläsaren när det finns en omdirigering.
* App Service-ARRAffinity cookie-domän har angetts som App Service-värdnamn, example.azurewebsites.net, i stället för den ursprungliga värden.

När ett backend-program skickar ett svar på omdirigering kanske du vill omdirigera klienten till en annan URL än den som anges av Server dels programmet. Du kanske vill göra detta när en app service finns bakom en Programgateway och kräver att klienten utför en omdirigering till dess relativa sökväg. Ett exempel är en omdirigering från contoso.azurewebsites.net/path1 till contoso.azurewebsites.net/path2. 

När App Service skickar ett svar för omdirigering använder den samma värdnamn i plats huvudet i svaret som det i den begäran som den tar emot från Application Gateway. Klienten gör till exempel begäran direkt till contoso.azurewebsites.net/path2 i stället för att gå igenom Application Gateway-contoso.com/path2. Du vill inte kringgå Application Gateway.

Det här problemet kan inträffa av följande huvudsakliga orsaker:

- Du har konfigurerat en omdirigering som kon figurer ATS i App Service. Omdirigering kan vara lika enkelt som att lägga till ett avslutande snedstreck i begäran.
- Du har Azure Active Directory autentisering, vilket gör omdirigeringen.

När du använder app Services bakom en Programgateway skiljer sig även det domän namn som är associerat med Application Gateway (example.com) från domän namnet för App Service (t. ex. example.azurewebsites.net). Domän värdet för den ARRAffinity-cookie som anges av App Service har domän namnet example.azurewebsites.net, vilket inte är önskvärt. Det ursprungliga värd namnet, example.com, ska vara domän namn svärdet i cookien.

## <a name="sample-configuration"></a>Exempel på konfiguration

- HTTP-lyssnare: grundläggande eller flera platser
- Backend-adresspool: App Service
- HTTP-inställningar: **Välj värdnamn från backend-adressen** aktive rad
- Avsökning: **Välj värdnamn för HTTP-inställningar** aktiverat

## <a name="cause"></a>Orsak

App Service är en tjänst för flera innehavare, så den använder värd huvudet i begäran för att dirigera begäran till rätt slut punkt. Standard domän namnet för App Services, *. azurewebsites.net (t. ex. contoso.azurewebsites.net) skiljer sig från Application gateways domän namn (t. ex. contoso.com). 

Den ursprungliga begäran från klienten har Application gateways domän namn, contoso.com, som värd namn. Du måste konfigurera programgatewayen för att ändra värd namnet i den ursprungliga begäran till App Service-värdnamnet när den dirigerar begäran till App Service-server delen. Använd växeln **Välj värd namnet från Server dels adressen** i konfigurationen för http-konfigurationen för programgatewayen. Använd växeln **Välj värd namn från Server delens HTTP-inställningar** i hälso avsöknings konfigurationen.



![Värddator namn för Application Gateway ändras](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

När App Service utför en omdirigering använder den det åsidosatta värd namnet contoso.azurewebsites.net i plats rubriken i stället för det ursprungliga värd namnet contoso.com, om inget annat har kon figurer ATS. Kontrol lera följande exempel på begäran och svarshuvuden.
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
I föregående exempel ser du att svars huvudet har status kod 301 för omdirigering. Plats huvudet har App Service-värdnamnet i stället för det ursprungliga värd namnet `www.contoso.com` .

## <a name="solution-rewrite-the-location-header"></a>Lösning: Skriv om plats rubriken

Ange värd namnet i plats rubriken till Application gatewayens domän namn. Det gör du genom att skapa en [Rewrite-regel](./rewrite-http-headers.md) med ett villkor som utvärderar om plats rubriken i svaret innehåller azurewebsites.net. Det måste också utföra en åtgärd för att skriva om plats huvudet till Application Gateway-värdnamnet. Mer information finns i anvisningar om [hur du skriver om plats rubriken](./rewrite-http-headers.md#modify-a-redirection-url).

> [!NOTE]
> Stödet för omskrivning av HTTP-huvud är bara tillgängligt för [Standard_v2 och WAF_V2 SKU](./application-gateway-autoscaling-zone-redundant.md) för Application Gateway. Om du använder v1 SKU rekommenderar vi att du [migrerar från v1 till v2](./migrate-v1-v2.md). Du vill använda omskrivning och andra [avancerade funktioner](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) som är tillgängliga med v2-SKU: n.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternativ lösning: Använd ett anpassat domän namn

Om du använder v1 SKU kan du inte skriva om plats rubriken. Den här funktionen är endast tillgänglig för v2 SKU. Lös problemet genom att skicka samma värdnamn som Application Gateway tar emot till App Service, i stället för att utföra en åsidosättning av värden.

App Service gör nu omdirigeringen (om det finns en sådan) på samma ursprungliga värd huvud som pekar på programgatewayen och inte dess egen.

Du måste äga en anpassad domän och följa den här processen:

- Registrera domänen i den anpassade domän listan för App Service. Du måste ha en CNAME-post i din anpassade domän som pekar på App Services FQDN. Mer information finns i [mappa ett befintligt anpassat DNS-namn till Azure App Service](../app-service/app-service-web-tutorial-custom-domain.md).

    ![App Service, anpassad domän lista](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Din app service är redo att acceptera värd namnet `www.contoso.com` . Ändra CNAME-posten i DNS så att den pekar tillbaka till programgatewayens FQDN, till exempel `appgw.eastus.cloudapp.azure.com` .

- Kontrol lera att din domän `www.contoso.com` matchar programgatewayens FQDN-namn när du gör en DNS-fråga.

- Ange anpassad avsökning för att inaktivera **Välj värdnamn från Server delens HTTP-inställningar**. Avmarkera kryss rutan i avsöknings inställningarna i Azure Portal. I PowerShell använder du inte växeln **-PickHostNameFromBackendHttpSettings** i kommandot **set-AzApplicationGatewayProbeConfig** . I fältet värdnamn för avsökningen anger du din app service FQDN, example.azurewebsites.net. Avsöknings begär Anden som skickas från programgatewayen har detta fullständiga domän namn i värd huvudet.

  > [!NOTE]
  > I nästa steg kontrollerar du att den anpassade avsökningen inte är kopplad till dina Server dels-HTTP-inställningar. HTTP-inställningarna har fortfarande **Välj värd namn från Server dels adress** växeln som är aktive rad i det här läget.

- Ange HTTP-inställningarna för programgatewayen för att inaktivera **Välj värdnamn från backend-adressen**. I Azure Portal avmarkerar du kryss rutan. I PowerShell använder du inte växeln **-PickHostNameFromBackendAddress** i kommandot **set-AzApplicationGatewayBackendHttpSettings** .

- Associera den anpassade avsökningen tillbaka till Server delens HTTP-inställningar och kontrol lera att Server delen är felfri.

- Programgatewayen bör nu vidarebefordra samma värdnamn, `www.contoso.com` till App Service. Omdirigeringen sker på samma värdnamn. Kontrol lera följande exempel på begäran och svarshuvuden.

Använd PowerShell-skriptet nedan om du vill implementera föregående steg med PowerShell för en befintlig installation. Observera att vi inte har använt växlarna **-PickHostname** i konfigurationen för avsöknings-och http-inställningar.

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

Om föregående steg inte löste problemet öppnar du ett [support ärende](https://azure.microsoft.com/support/options/).