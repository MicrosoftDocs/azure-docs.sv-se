---
title: Vad är Azure Static Web Apps?
description: Viktiga funktioner och funktioner i Azures statiska Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: f4e900c8bcdcb5febec39421e098b19ad10b8b00
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558898"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Vad är Azures statiska Web Apps för hands version?

Azures statiska Web Apps är en tjänst som automatiskt skapar och distribuerar fullständiga stack-webbappar till Azure från en GitHub-lagringsplats.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Översikt över statiska Web Apps":::

Arbets flödet för den statiska Azure-Web Apps är anpassat till en utvecklares dagliga arbets flöde. Appar skapas och distribueras baserat på GitHub-interaktioner.

När du skapar en Azure Static Web Apps-resurs skapar Azure ett GitHub Actions-arbetsflöde på lagringsplatsen för appens källkod som övervakar den gren som du har valt. Varje gång du push-skickar eller accepterar pull-begäranden till den bevakade grenen skapar och distribuerar GitHub-åtgärden automatiskt appen och dess API till Azure.

Statiska webbappar skapas ofta med hjälp av bibliotek och ramverk som vinkel, reagera, svelte, Vue eller blixt där rendering på Server sidan inte krävs. Dessa appar består av HTML, CSS, JavaScript och bildtillgångar. Med en traditionell webb server betjänas dessa till gångar från en enda server tillsammans med eventuella nödvändiga API-slutpunkter.

Med statiska Web Apps separeras statiska till gångar från en traditionell webb server och betjänas i stället från platser geografiskt fördelade över hela världen. Den här distributionen gör att det går mycket snabbare att hantera filer eftersom filerna är fysiskt närmare slutanvändarna. Dessutom är API-slutpunkterna värdbaserade med en [arkitektur utan server](../azure-functions/functions-overview.md), vilket gör att du inte behöver ha en fullständig backend-server samtidigt.

## <a name="key-features"></a>Huvudfunktioner

- **Webb värd** för statiskt innehåll som HTML, CSS, Java Script och avbildningar.
- **Inbyggt API** -stöd som tillhandahålls av Azure Functions.
- **GitHub-integrering i första klass** där lagrings plats ändringar utlöser versioner och distributioner.
- **Globalt distribuerat** statiskt innehåll, vilket ger innehållet närmare dina användare.
- **Kostnads fria SSL-certifikat**, som automatiskt förnyas.
- **Anpassade domäner** för att tillhandahålla anpassade anpassningar till din app.
- **Sömlös säkerhets modell** med en omvänd proxy vid anrop till API: er som inte kräver CORS-konfiguration.
- **Integrering av autentiseringsprovider** med Azure Active Directory, Facebook, Google, GitHub och Twitter.
- **Anpassningsbar roll definition** och tilldelningar för auktorisering.
- **Regler för routning på Server sidan** möjliggör fullständig kontroll över det innehåll och de vägar som du hanterar.
- **Genererade produktions versioner** som drivs av pull-begäranden som aktiverar för hands versioner av din webbplats innan publicering.

## <a name="what-you-can-do-with-static-web-apps"></a>Vad du kan göra med statiska Web Apps

- Skapa **moderna webb program** med JavaScript-ramverk och bibliotek som [vinkel](getting-started.md?tabs=angular), [reagera](getting-started.md?tabs=react), [svelte](/learn/modules/publish-app-service-static-web-app-api/), [Vue](getting-started.md?tabs=react)eller med hjälp av [blixt](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) för att skapa WebAssembly-program med en [Azure Functions](apis.md) backend.
- **Publicera statiska platser** med ramverk som [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Distribuera webb program** med ramverk som [Next.js](deploy-nextjs.md) och [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bygg din första statiska app](getting-started.md)