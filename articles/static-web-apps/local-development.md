---
title: Konfigurera lokal utveckling för Azure Static Web Apps
description: Lär dig hur du ställer in din lokala utvecklingsmiljö för Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: e19d39a32d48ec55473bb957595d47ec5148e74b
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588793"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Konfigurera lokal utveckling för Azure Static Web Apps förhandsversion

När den publiceras till molnet Azure Static Web Apps en webbplats många tjänster som fungerar tillsammans som om de vore samma program. Här är några exempel på sådana tjänster:

- Den statiska webbappen
- Azure Functions API
- Autentiserings- och auktoriseringstjänster
- Routnings- och konfigurationstjänster

Dessa tjänster måste kommunicera med varandra och Azure Static Web Apps hanterar den här integreringen åt dig i molnet.

Men de här tjänsterna kopplas inte ihop automatiskt när de körs lokalt.

För att ge en liknande upplevelse som du får i Azure tillhandahåller [Azure Static Web Apps CLI](https://github.com/Azure/static-web-apps-cli) följande tjänster:

- En lokal statisk platsserver
- En proxy till utvecklingsservern för frontend-ramverket
- En proxy till dina API-slutpunkter – tillgänglig via Azure Functions Core Tools
- En fingerad autentiserings- och auktoriseringsserver
- Lokala vägar och tvingande konfigurationsinställningar

## <a name="how-it-works"></a>Så här fungerar det

Följande diagram visar hur begäranden hanteras lokalt.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Azure Static Web App CLI-begäran och svarsflöde":::

> [!IMPORTANT]
> Gå till `http://localhost:4280` för att få åtkomst till programmet som betjänas av CLI.

- **Begäranden** som görs till `4280` porten vidarebefordras till lämplig server beroende på typ av begäran.

- **Begäranden om** statiskt innehåll, till exempel HTML eller CSS, hanteras antingen av den interna CLI-servern för statiskt innehåll eller av frontend-ramverksservern för felsökning.

- **Autentiserings- och** auktoriseringsbegäranden hanteras av en emulator som tillhandahåller en falsk identitetsprofil till din app.

- **Functions Core Tools-körningen** hanterar begäranden till webbplatsens API.

- **Svar** från alla tjänster returneras till webbläsaren som om de vore ett enda program.

## <a name="prerequisites"></a>Förutsättningar

- **Befintlig Azure Static Web Apps webbplats:** Om du inte har någon kan du börja med [startappen vanilla-api.](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate)
- **[Node.js](https://nodejs.org) med npm:** Kör [ denNode.js LTS-versionen,](https://nodejs.org) som innehåller åtkomst till [npm](https://www.npmjs.com/).
- **[Visual Studio:](https://code.visualstudio.com/)** Används för felsökning av API-programmet, men krävs inte för CLI.

## <a name="get-started"></a>Kom igång

Öppna en terminal till rotmappen för din befintliga Azure Static Web Apps webbplats.

1. Installera CLI.

    `npm install -g @azure/static-web-apps-cli`

1. Skapa din app om det behövs av ditt program.

    Kör `npm run build` , eller motsvarande kommando för projektet.

1. Ändra till utdatakatalogen för din app. Utdatamappar kallas ofta _build eller_ liknande.

1. Starta CLI.

    `swa start`

1. Gå till http://localhost:4280 för att visa appen i webbläsaren.

### <a name="other-ways-to-start-the-cli"></a>Andra sätt att starta CLI

| Description | Kommando |
|--- | --- |
| Visa en specifik mapp | `swa start ./output-folder` |
| Använda en utvecklingsserver för ramverk som körs | `swa start http://localhost:3000` |
| Starta en Functions-app i en mapp | `swa start ./output-folder --api ./api` |
| Använda en Functions-app som körs | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Auktoriserings- och autentiseringsemulering

Den Static Web Apps CLI emulerar det [säkerhetsflöde som implementeras](./authentication-authorization.md) i Azure. När en användare loggar in kan du definiera en falsk identitetsprofil som returneras till appen.

När du till exempel försöker navigera till `/.auth/login/github` returneras en sida som gör att du kan definiera en identitetsprofil.

> [!NOTE]
> Emulatorn fungerar med alla säkerhetsleverantörer, inte bara GitHub.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Lokal autentisering och auktoriseringsemulator":::

Emulatorn tillhandahåller en sida där du kan ange följande värden [för klientens huvudnamn:](./user-information.md#client-principal-data)

| Värde | Beskrivning |
| --- | --- |
| **Användarnamn** | Kontonamnet som är associerat med säkerhetsprovidern. Det här värdet visas `userDetails` som egenskapen i klientens huvudnamn och genererades automatiskt om du inte anger något värde. |
| **Användar-ID** | Värdet genererades automatiskt av CLI.  |
| **Roller** | En lista över rollnamn, där varje namn finns på en ny rad.  |

När du har loggat in:

- Du kan använda `/.auth/me` slutpunkten eller en funktionsslutpunkt för att hämta användarens [klienthuvudnamn](./user-information.md).

- Om du `./auth/logout` går till rensas klientens huvudnamn och den fingerade användaren loggas ut.

## <a name="debugging"></a>Felsökning

Det finns två felsökningskontext i en statisk webbapp. Den första är för webbplatsen för statiskt innehåll och den andra är för API-funktioner. Lokal felsökning är möjligt genom att tillåta att Static Web Apps CLI använder utvecklingsservrar för en eller båda av dessa kontexter.

Följande steg visar ett vanligt scenario som använder utvecklingsservrar för båda felsökningskontexterna.

1. Starta den statiska servern för platsutveckling. Det här kommandot är specifikt för det frontend-ramverk som du använder, men kommer ofta i form av kommandon som `npm run build` `npm start` , eller `npm run dev` .

1. Öppna API-programmappen i Visual Studio Code och starta en felsökningssession.

1. Skicka adresserna för den statiska servern och API-servern till `swa start` kommandot genom att visa dem i ordning.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

Följande skärmbilder visar terminalerna för ett typiskt felsökningsscenario:

Webbplatsen för statiskt innehåll körs via `npm run dev` .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Server för utveckling av statiska webbplatser":::

API Azure Functions programmet kör en felsökningssession i Visual Studio Code.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="felsökning Visual Studio KOD-API:et":::

Den Static Web Apps CLI startas med båda utvecklingsservrarna.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Azure Static Web Apps CLI-terminal":::

Nu dirigeras begäranden som går via porten till antingen utvecklingsservern för statiskt `4280` innehåll eller API-felsökningssessionen.

Mer information om olika felsökningsscenarier, med vägledning om hur du anpassar portar och serveradresser, finns i [cli-Azure Static Web Apps CLI-lagringsplatsen](https://github.com/Azure/static-web-apps-cli).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera ditt program](configuration.md)
