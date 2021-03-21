---
title: 'Självstudie: skapa en statisk webbapp med blixt i Azure static Web Apps'
description: Lär dig att bygga en Azure-statisk Web Apps webbplats med en blixt.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: cshoe
ms.openlocfilehash: 0086f7f68fd05d6925d19c7ab457fbc125e36be4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350236"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>Självstudie: skapa en statisk webbapp med blixt i Azure static Web Apps

Azures statiska Web Apps publicerar en webbplats i en produktions miljö genom att bygga appar från en GitHub-lagringsplats. I den här självstudien distribuerar du ett webb program till Azures statiska webbappar med hjälp av Azure Portal.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Förutsättningar

- [GitHub](https://github.com)-konto
- [Azure](https://portal.azure.com) -konto

## <a name="application-overview"></a>Programöversikt

Med Azures statiska Web Apps kan du skapa statiska webb program som stöds av en server lös Server del. Följande självstudie visar hur du distribuerar C#-webb programmet i C# som returnerar väder data.

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="Slutför blixt app":::

Appen som används i den här självstudien består av tre olika Visual Studio-projekt:

- **API**: C# Azure Functions program som IMPLEMENTERAr API-slutpunkten som tillhandahåller väder information till den statiska appen. **WeatherForecastFunction** returnerar en matris med `WeatherForecast` objekt.

- **Klient**: frontend-webbsammansättnings projekt för front-end. En [återställnings väg](#fallback-route) implementeras för att se till att alla vägar betjänar _index.html_ -filen.

- **Delad**: innehåller gemensamma klasser som refereras av både API-och klient projekt, vilket gör att data kan flöda från API-slutpunkten till frontend-webbappen. [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs)Klassen delas mellan båda apparna.

Tillsammans utgör dessa projekt de delar som krävs för att skapa ett blixt webb sammansättnings program som körs i den webbläsare som stöds av en API-Server del.

## <a name="fallback-route"></a>Reserv väg

Programmet visar URL: er som _/Counter_ och _/fetchdata_ som mappar till specifika vägar i programmet. Eftersom den här appen implementeras som ett enda sid program, behandlas varje väg med _index.html_ -filen. För att se till att begäran om sökväg returneras _index.html_ , är en [återställnings väg](./routes.md#fallback-routes) implementerad i _routes.jsför_ filen som finns i mappen _wwwroot_ i klient projektet.

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

Konfigurationen ovan garanterar att förfrågningar till alla vägar i appen returnerar _index.html_ -sidan.

## <a name="create-a-repository"></a>Klona en lagringsplats

Den här artikeln använder en lagrings plats för GitHub-mallar för att göra det enkelt för dig att komma igång. Mallen innehåller en start-app som distribueras till Azures statiska Web Apps.

1. Se till att du är inloggad på GitHub och navigera till följande plats för att skapa en ny lagrings plats:
    - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. Namnge din lagrings plats för **min-första statiska-blixte-app**

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

Nu när lagrings platsen har skapats skapar du en statisk webbapp från Azure Portal.

1. Navigera till [Azure Portal](https://portal.azure.com)
1. Välj **skapa en resurs**
1. Sök efter **Statiska webbappar**
1. Välj **statisk Web Apps (förhands granskning)**
1. Välj **Skapa**

I avsnittet _grundläggande_ börjar du med att konfigurera din nya app och länka den till en GitHub-lagringsplats.

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="Fliken Grundläggande":::

1. Välj din _Azure-prenumeration_
1. Välj eller skapa en ny _resurs grupp_
1. Namnge appens **mitt-första statiska – blixt-app**
    - Giltiga tecken är `a-z` (skiftlägesokänsligt), `0-9` och `-`.
1. Välj en _region_ som är närmast dig
1. Välj den **kostnads fria** _SKU: n_
1. Välj knappen **Logga in med GitHub** och autentisera med GitHub

När du har loggat in med GitHub anger du lagrings plats informationen.

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="Lagrings information":::

1. Välj önskad _organisation_
1. Välj **min-första statiska-blixte-app** från List rutan _databas_
1. Välj **huvud** från List rutan _gren_

    Om du inte ser några databaser kan du behöva auktorisera Azures statiska Web Apps i GitHub. Bläddra till GitHub-lagringsplatsen och gå till **inställningar > program > auktoriserade OAuth-appar**, Välj **Azure static Web Apps** och välj sedan **bevilja**. Du måste vara ägare till organisationen för att kunna bevilja behörighet för organisations databaser.

1. I avsnittet _versions information_ lägger du till konfigurations information med blixt information.

    - Välj **blixt** i list rutan _skapa för inställningar_ och Behåll alla standardvärden.

1. Välj **Granska + skapa**.

    :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="Knappen granska skapande":::

1. Välj **Skapa**.

    :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="Knappen Skapa":::

1. Välj **Gå till resurs**.

    :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="Knappen Gå till resurs":::

## <a name="view-the-website"></a>Visa webbplatsen

Det finns två aspekter av att distribuera en statisk app. Den första etablerar de underliggande Azure-resurserna som utgör din app. Den andra är ett GitHub Actions-arbetsflöde som bygger och publicerar din app.

Innan du kan navigera till den nya statiska platsen måste distributions versionen först slutföras.

I fönstret Översikt över statisk Web Apps visas en serie länkar som hjälper dig att interagera med din webbapp.

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="Översikts fönster":::

1. _Klicka här om du vill kontrol lera statusen för dina GitHub-åtgärder_ för att gå till de GitHub-åtgärder som körs mot din lagrings plats. När du har kontrollerat att distributions jobbet är klart kan du gå till webbplatsen via den genererade URL: en.

2. När GitHub-åtgärds arbets flödet är klart kan du välja _URL_ -länken för att öppna webbplatsen på ny flik.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet kan du ta bort den statiska Azure-Web Apps-instansen genom följande steg:

1. Öppna [Azure-portalen](https://portal.azure.com)
1. Sök efter **min-First-static-coolor-app** från det övre Sök fältet
1. Välj på appens namn
1. Välj på knappen **ta bort**
1. Välj **Ja** för att bekräfta borttagnings åtgärden

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Autentisering och auktorisering](./authentication-authorization.md)
