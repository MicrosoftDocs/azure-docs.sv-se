---
title: Vad är Azure Static Web Apps?
description: Viktiga funktioner och funktioner i Azures statiska Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 884aa18b1a29b1c4f1bbf1159a5d48870529c72b
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565714"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Vad är Azures statiska Web Apps för hands version?

Azure Static Web Apps är en tjänst som automatiskt skapar och distribuerar fullstack webbappar till Azure från ett GitHub repository.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Översikt över statiska Web Apps":::

Arbetsflödet för Azure Static Web Apps är anpassat till en utvecklares dagliga arbetsflöde. Appar skapas och distribueras baserat på GitHub interaktioner.

När du skapar en Azure Static Web Apps-resurs skapar Azure ett GitHub Actions-arbetsflöde på lagringsplatsen för appens källkod (repository) som övervakar den gren som du har valt. Varje gång du push-skickar eller accepterar pull-begäranden till den bevakade grenen skapar och distribuerar GitHub Actions automatiskt appen och dess API till Azure.

Statiska webbappar skapas vanligtvis med hjälp av bibliotek och ramverk som Angular, React, Svelte eller Vue. Dessa appar består av HTML, CSS, JavaScript och bildtillgångar. Med en traditionell webbserver betjänas dessa tillgångar från en enda server tillsammans med eventuella nödvändiga API-ändpunkter.

Med Azure Static Web Apps separeras statiska tillgångar från en traditionell webbserver och betjänas i stället från platser geografiskt fördelade över hela världen. Den här distributionen gör att det går mycket snabbare att hantera filer eftersom filerna är fysiskt närmare slutanvändarna. Dessutom är API-ändpunkterna hostade med en [serverless arkitektur](../azure-functions/functions-overview.md), vilket gör att du inte behöver ha en fullständig backend-server samtidigt.

## <a name="key-features"></a>Huvudfunktioner

- **Webbvärd** för statiskt innehåll som HTML, CSS, Java Script och bilder.
- **Inbyggt API** -stöd som tillhandahålls av Azure Functions.
- **GitHub-integrering i första klass** där lagringsplats ändringar utlöser versioner och distributioner.
- **Globalt distribuerat** statiskt innehåll, vilket gör att innehållet serveras närmare dina användare.
- **Kostnads fria SSL-certifikat**, som automatiskt förnyas.
- **Självvalda domäner** för att styrka appens varumärke.
- **Sömlös säkerhets modell** med en omvänd proxy vid anrop till API: er som inte kräver CORS-konfiguration.
- **Integrering av autentiseringsprovider** med Azure Active Directory, Facebook, Google, GitHub och Twitter.
- **Anpassningsbar roll definition** och tilldelningar för auktorisering.
- **Regler för routning på serversidan** möjliggör fullständig kontroll över det innehåll och routes som du hanterar.
- **Genererade produktions versioner** som drivs av pull-begäranden som aktiverar för hands versioner av din webbplats innan publicering.

## <a name="what-you-can-do-with-static-web-apps"></a>Vad du kan göra med Static Web Apps

- **Bygg moderna JavaScript-program** med ramverk och bibliotek som [vinkel](getting-started.md?tabs=angular), [reagera](getting-started.md?tabs=react), [svelte](https://docs.microsoft.com/learn/modules/publish-app-service-static-web-app-api/), [Vue](getting-started.md?tabs=react) med en [Azure Functions](apis.md) backend.
- **Publicera statiska sidor** med ramverk som [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Distribuera webb program** med ramverk som [Next.js](deploy-nextjs.md) och [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bygg din första statiska app](getting-started.md)
