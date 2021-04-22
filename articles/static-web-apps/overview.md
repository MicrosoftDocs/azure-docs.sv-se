---
title: Vad är Azure Static Web Apps?
description: De viktigaste funktionerna i Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: 945979daa3c766b737e5b312a6c14e60204a6a55
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874071"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Vad är Azure Static Web Apps förhandsversion?

Azure Static Web Apps är en tjänst som automatiskt skapar och distribuerar fullständiga stackwebbappar till Azure från en kodlagringsplats.

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Azure Static Web Apps översiktsdiagram":::

Arbetsflödet för Azure Static Web Apps är skräddarsytt för en utvecklares dagliga arbetsflöde. Appar byggs och distribueras baserat på kodändringar.

När du skapar en Azure Static Web Apps resurs interagerar Azure direkt med GitHub eller Azure DevOps för att övervaka en valfri gren. Varje gång du skickar genomföranden eller accepterar pull-begäranden till den övervakade grenen körs en version automatiskt och din app och ditt API distribueras till Azure.

Statiska webbappar byggs ofta med hjälp av bibliotek och ramverk som Angular, React, Svelte, Vue eller Blazor där återgivning på serversidan inte krävs. Dessa appar består av HTML, CSS, JavaScript och bildtillgångar. Med en traditionell webbserver betjänas dessa tillgångar från en enda server tillsammans med alla nödvändiga API-slutpunkter.

Med Static Web Apps separeras statiska tillgångar från en traditionell webbserver och betjänas i stället från platser som är geografiskt fördelade över hela världen. Den här distributionen gör att det går mycket snabbare att hantera filer eftersom filerna är fysiskt närmare slutanvändarna. Dessutom finns API-slutpunkter i en [serverlös](../azure-functions/functions-overview.md)arkitektur, vilket gör att du inte behöver ha en fullständig server i sin helhet.

## <a name="key-features"></a>Huvudfunktioner

- **Webbvärdverktyg** för statiskt innehåll som HTML, CSS, JavaScript och bilder.
- **Integrerat API-stöd** som tillhandahålls av Azure Functions.
- **Förstklassig GitHub- och Azure DevOps-integrering där** ändringar i lagringsplatsen utlöser byggen och distributioner.
- **Globalt distribuerat statiskt** innehåll som ligger närmare dina användare.
- **Kostnadsfria SSL-certifikat**, som förnyas automatiskt.
- **Anpassade domäner** för att tillhandahålla varumärkesanpassningar till din app.
- **Sömlös säkerhetsmodell med** en omvänd proxy vid anrop av API:er, vilket inte kräver någon CORS-konfiguration.
- **Integrering av autentiseringsprovider** med Azure Active Directory, Facebook, Google, GitHub och Twitter.
- **Anpassningsbar definition och tilldelning av** auktoriseringsroller.
- **Regler för serverslutsroutning** som ger fullständig kontroll över det innehåll och de vägar som du betjänar.
- **Genererade mellanlagringsversioner** som drivs av pull-begäranden som aktiverar förhandsversioner av webbplatsen före publicering.

## <a name="what-you-can-do-with-static-web-apps"></a>Vad du kan göra med Static Web Apps

- **Skapa moderna webbprogram** med JavaScript-ramverk och -bibliotek som [Angular,](getting-started.md?tabs=angular) [React,](getting-started.md?tabs=react) [Svelte,](/learn/modules/publish-app-service-static-web-app-api/) [Vue](getting-started.md?tabs=vue)eller använd [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) för att skapa WebAssembly-program med Azure Functions-backend. [](apis.md)
- **Publicera statiska** webbplatser med ramverk som [Gatsby](publish-gatsby.md), [Snip](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Distribuera webbprogram** med ramverk som [Next.js](deploy-nextjs.md) och [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa din första statiska app](getting-started.md)
