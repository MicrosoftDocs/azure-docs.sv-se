---
title: Självstudie – Lägga till Azure CDN i en Azure App Service-webbapp | Microsoft Docs
description: I den här självstudien lägger du till ett Content Delivery Network (CDN) i en Azure App Service-webbapp för att cachelagra och leverera statiska filer från servrar nära dina kunder runtom i världen.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 3f1dacb57931913edfb181f023bdf98717777d50
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97562250"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Självstudie: Lägga till Azure CDN i en Azure App Service-webbapp

Den här självstudiekursen beskriver hur du lägger till [Azure Content Delivery Network (CDN)](cdn-overview.md) till en [webbapp i Azure App Service](../app-service/overview.md). Web Apps är en värdtjänst för webbprogram, REST API:er och mobila backend-servrar. 

Här är startsidan för den statiska exempel-HTML-platsen som du kommer att arbeta med:

![Exempelstartsida för app](media/cdn-add-to-web-app/sample-app-home-page.png)

Detta får du får lära dig:

> [!div class="checklist"]
> * Skapa en CDN-slutpunkt.
> * Uppdatera cachelagrade tillgångar.
> * Använda frågesträngar för att styra cachelagrade versioner.


## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- [Installera Git](https://git-scm.com/)
- [Installera Azure CLI](/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Skapa webbappen

För att skapa webbappen som du ska arbeta med följer du [snabbstarten för statisk HTML](../app-service/quickstart-html.md) via steget **Bläddra till appen**.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com).

### <a name="dynamic-site-acceleration-optimization"></a>Optimering för acceleration av dynamisk webbplats
Om du vill optimera CDN-slutpunkten för acceleration av dynamisk webbplats (DSA, Dynamic Site Acceleration) bör du skapa slutpunkten direkt på [CDN-portalen](cdn-create-new-endpoint.md). Prestanda på webbsidor med dynamiskt innehåll förbättras avsevärt med [DSA-optimering](cdn-dynamic-site-acceleration.md). Mer anvisningar om hur du optimerar en CDN-slutpunkt för DSA från CDN-portalen finns i informationen om [CDN-slutpunktskonfiguration för snabbare leverans av dynamiska filer](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files). Om du inte vill optimera din nya slutpunkt kan du använda webbapp-portalen för att skapa den genom att följa stegen i nästa avsnitt. Observera att för **Azure CDN från Verizon**-profiler kan du inte ändra optimeringen av en CDN-slutpunkt när den väl har skapats.

## <a name="create-a-cdn-profile-and-endpoint"></a>Skapa en CDN-profil och en slutpunkt

I det vänstra navigeringsfönstret väljer du **App Services** och sedan väljer du den app som du skapade i [static HTML quickstart](../app-service/quickstart-html.md) (snabbstart för statisk HTML).

![Välj App Service-app i portalen](media/cdn-add-to-web-app/portal-select-app-services.png)

På sidan **App Service** i avsnittet **Inställningar** väljer du **Networking > Configure Azure CDN for your app** (Nätverk > Konfigurera Azure CDN för din app).

![Välj CDN i portalen](media/cdn-add-to-web-app/portal-select-cdn.png)

På sidan **Azure Content Delivery Network** anger du inställningarna för **Ny slutpunkt** som anges i tabellen.

![Skapa en profil och en slutpunkt i portalen](media/cdn-add-to-web-app/portal-new-endpoint.png)

| Inställning | Föreslaget värde | Beskrivning |
| ------- | --------------- | ----------- |
| **CDN-profil** | myCDNProfile | En CDN-profil är en samling CDN-slutpunkter på samma prisnivå. |
| **Prisnivå** | Standard Akamai | [Prisnivån](cdn-features.md) anger providern och funktioner som är tillgängliga. I den här självstudien används *Standard Akamai*. |
| **CDN-slutpunktsnamn** | Vilket namn som helst som är unikt för domänen azureedge.net | Du kommer åt dina cachelagrade resurser på domänen *&lt; endpointname &gt;*. azureedge.net.

Välj **Skapa ny** för att skapa en CDN-profil.

Azure skapar en profil och en slutpunkt. Den nya slutpunkten visas i listan **Slutpunkter** och när den har etablerats är statusen **Körs**.

![Ny slutpunkt i listan](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Testa CDN-slutpunkten

 Slutpunkten kan inte användas direkt, eftersom det tar tid för registreringen att sprida sig: 
   - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
   - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
   - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 90 minuter. 

Exempelappen har en *index.html*-fil och *css*-, *img* och *js*-mappar som innehåller andra statiska tillgångar. Sökvägarna till innehållet för alla de här filerna är samma i CDN-slutpunkten. Båda följande URL:er har till exempel åtkomst till filen *bootstrap.css* i mappen *css*:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Öppna en webbläsare och navigera till följande adress:

```
http://<endpointname>.azureedge.net/index.html
```

![Exempelstartsida för app som hämtats från CDN](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 Du ser samma sida som du körde tidigare i en Azure-webbapp. Azure CDN har hämtat den ursprungliga webbappens tillgångar och fungerar som värd för dem från CDN-slutpunkten

Uppdatera sidan för att se till att den cachelagras i CDN. Ibland krävs två begäranden för samma tillgång för att CDN ska cachelagra det begärda innehållet.

Mer information om hur du skapar Azure CDN-profiler och slutpunkter finns i [Komma igång med Azure CDN](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Rensa CDN

CDN uppdaterar regelbundet dess resurser från den ursprungliga webbappen baserat på konfigurationen för TTL (time to live). Standard-TTL är sju dagar.

Ibland kan du behöva uppdatera CDN innan TTL upphör, exempelvis när du distribuerar uppdaterat innehåll till webbappen. För att utlösa en uppdatering kan du rensa CDN-resurser manuellt. 

I det här avsnittet av självstudien distribuerar du en ändring till webbappen och rensar CDN för att utlösa en uppdatering av cache i CDN.

### <a name="deploy-a-change-to-the-web-app"></a>Distribuera en ändring till webbappen

Öppna filen *index.html* och lägg till *- V2* till rubriken H1, som i följande exempel: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Spara ändringen och distribuera den till webbappen.

```bash
git commit -am "version 2"
git push azure main
```

När distributionen är färdig bläddrar du till webbappens URL där du kan se ändringen.

```
http://<appname>.azurewebsites.net/index.html
```

![V2 i rubriken i webbappen](media/cdn-add-to-web-app/v2-in-web-app-title.png)

Om du bläddrar till URL:en för CDN-slutpunkten för startsidan så ser du inte ändringen eftersom den cachelagrade versionen i CDN inte har upphört ännu. 

```
http://<endpointname>.azureedge.net/index.html
```

![Ingen V2 i rubriken i CDN](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Rensa CDN i portalen

Rensa CDN för att utlösa uppdateringen av den cachelagrade versionen av CDN.

I det vänstra navigeringsfönstret i portalen väljer du **Resursgrupper**, och sedan väljer du den resursgrupp som du skapade för din webbapp (myResourceGroup).

![Välj resursgrupp](media/cdn-add-to-web-app/portal-select-group.png)

Välj din CDN-slutpunkt i listan över resurser.

![Välj slutpunkt](media/cdn-add-to-web-app/portal-select-endpoint.png)

Längst upp på sidan **Slutpunkt** väljer du **Rensa**.

![Välj Rensa](media/cdn-add-to-web-app/portal-select-purge.png)

Ange de innehållssökvägar du vill rensa. Du kan skicka en fullständig filsökväg för att rensa en enskild fil eller ett vägsegment för att rensa och uppdatera allt innehåll i en viss mapp. Se till att *index.html* finns på en av sökvägarna, eftersom du ändrade den.

Välj **Rensa** längst ned på sidan.

![Rensa sida](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Bekräfta att CDN har uppdaterats

Vänta till bearbetningen av rensningsbegäran slutförs, vilket brukar ta några minuter. Välj klockikonen längst upp på sidan för att se aktuell status. 

![Rensningsavisering](media/cdn-add-to-web-app/portal-purge-notification.png)

När du bläddrar till CDN-slutpunktens URL för *index.html*, ser du den *V2* som du lade i rubriken på startsidan, vilket indikerar att CDN-cachen har uppdaterats.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 i rubriken i CDN](media/cdn-add-to-web-app/v2-in-cdn-title.png)

Mer information finns i [Purge an Azure CDN endpoint](../cdn/cdn-purge-endpoint.md) (Rensa en Azure CDN-slutpunkt). 

## <a name="use-query-strings-to-version-content"></a>Använda frågesträngar för versioninnehåll

Azure CDN erbjuder följande beteendealternativ för cachelagring:

* Ignorera frågesträngar
* Kringgå cachelagring för frågesträngar
* Cachelagra varje unik URL 

Det första av dessa alternativ är standard, vilket innebär att det endast finns en cachelagrad version av en tillgång, oberoende av frågesträngen i webbadressen. 

I det här avsnittet i självstudien kan du ändra beteendet för cachelagring för att cachelagra alla unika URL:er.

### <a name="change-the-cache-behavior"></a>Ändra beteendet för cachelagring

På sidan **CDN-slutpunkt** i Azure Portal väljer du **Cache**.

Välj **Cachelagra varje unik URL** i listrutan för **Beteende för cachelagring av frågesträngar**.

Välj **Spara**.

![Välj beteende för cachelagring av frågesträngar](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Kontrollera att unika URL:er cachelagras separat

Gå till startsidan i CDN-slutpunkten i en webbläsare, och inkludera en frågesträng: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Azure CDN returnerar det aktuella webbappsinnehållet som innehåller *V2* i rubriken. 

Uppdatera sidan för att se till att den cachelagras i CDN. 

Öppna *index.html*, ändra *V2* till *V3* och distribuera sedan ändringen. 

```bash
git commit -am "version 3"
git push azure main
```

Gå till CDN-slutpunktens URL i en webbläsare med en ny frågesträng, som `q=2`. Azure CDN hämtar den aktuella *index.html*-filen och visar *V3*. Om du däremot navigerar till CDN-slutpunkten med frågesträngen `q=1` ser du *V2*.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 i rubriken i CDN, frågesträng 2](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 i rubriken i CDN, frågesträng 1](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

Det här resultatet visar att varje frågesträng behandlas olika:

* q=1 användes innan, så cachelagrat innehåll returneras (V2).
* q=2 är nytt, så det senaste webbappinnehållet hämtas och returneras (V3).

Mer information finns i [Kontrollera cachelagringsbeteendet med frågesträngar](../cdn/cdn-query-string.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Skapa en CDN-slutpunkt.
> * Uppdatera cachelagrade tillgångar.
> * Använda frågesträngar för att styra cachelagrade versioner.

Lär dig hur du optimerar CDN-prestanda i följande artiklar:

> [!div class="nextstepaction"]
> [Självstudier: Lägga till en anpassad domän i Azure CDN-slutpunkten](cdn-map-content-to-custom-domain.md)