---
title: Konfigurera lokal utveckling för Azures statiska Web Apps
description: Lär dig hur du ställer in din lokala utvecklings miljö för Azures statiska Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 8a45d490d060febc18d77c8487c9f562fd2a914a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275524"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Konfigurera lokal utveckling för för hands versionen av Azure statisk Web Apps

När den har publicerats i molnet har Azures statiska Web Apps-plats många tjänster som fungerar tillsammans som om de är samma program. Här är några exempel på sådana tjänster:

- Den statiska webbappen
- Azure Functions-API
- Autentiserings-och auktoriserings tjänster
- Tjänster för Routning och konfiguration

Dessa tjänster måste kommunicera med varandra, och Azure static Web Apps hanterar denna integrering åt dig i molnet.

Körs lokalt, men dessa tjänster är inte automatiskt kopplade till varandra.

För att ge en liknande upplevelse som du får i Azure tillhandahåller [Azures statiska Web Apps CLI](https://github.com/Azure/static-web-apps-cli) följande tjänster:

- En lokal statisk plats Server
- En proxy till utvecklings servern för front-end Framework
- En proxy till dina API-slutpunkter – tillgängligt via Azure Functions Core Tools
- En modell för autentisering och auktorisering av en server
- Tvingande lokala vägar och konfigurations inställningar

## <a name="how-it-works"></a>Så här fungerar det

Följande diagram visar hur förfrågningar hanteras lokalt.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Azures statiska webb program CLI-begäran och svars flöde":::

> [!IMPORTANT]
> Navigera till [http://localhost:4280](http://localhost:4280) för att komma åt programmet som hanteras av cli.

- **Begär Anden** som görs till porten `4280` vidarebefordras till lämplig server beroende på typ av begäran.

- Begär Anden om **statiskt innehåll** , t. ex. html eller CSS, hanteras antingen av den interna CLI-servern för statiskt innehåll eller av front-end Framework-servern för fel sökning.

- **Autentiserings-och auktoriserings** begär Anden hanteras av en emulator som tillhandahåller en falsk identitets profil till din app.

- **Functions Core tools runtime** hanterar begär anden till webbplatsens API.

- **Svar** från alla tjänster returneras till webbläsaren som om de var ett enda program.

## <a name="prerequisites"></a>Förutsättningar

- **Befintlig Azure-statisk Web Apps webbplats**: om du inte har en sådan börjar du med Start programmet [vanilj-API](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate) .
- **[Node.js](https://nodejs.org) med NPM**: kör [Node.js LTS](https://nodejs.org) -versionen, som innehåller åtkomst till [NPM](https://www.npmjs.com/).
- **[Visual Studio Code](https://code.visualstudio.com/)**: används för fel sökning av API-programmet, men krävs inte för cli.

## <a name="get-started"></a>Kom igång

Öppna en terminal till rotmappen för din befintliga Azure-statiska Web Apps webbplats.

1. Installera CLI.

    `npm install -g @azure/static-web-apps-cli`

1. Bygg din app om det behövs av ditt program.

    Kör `npm run build` eller motsvarande kommando för ditt projekt.

1. Ändra till utdata-katalogen för din app. Mappar med utdata kallas ofta _build_ eller något liknande.

1. Starta CLI.

    `swa start`

1. Navigera till [http://localhost:4280](http://localhost:4280) om du vill visa appen i webbläsaren.

### <a name="other-ways-to-start-the-cli"></a>Andra sätt att starta CLI

| Beskrivning | Kommando |
|--- | --- |
| Hantera en speciell mapp | `swa start ./output-folder` |
| Använda en Ramverks utvecklings server som körs | `swa start http://localhost:3000` |
| Starta en Functions-app i en mapp | `swa start ./output-folder --api ./api` |
| Använda en app som körs Functions | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Emulering av auktorisering och autentisering

Den statiska Web Apps CLI emulerar det [säkerhets flöde](./authentication-authorization.md) som implementeras i Azure. När en användare loggar in kan du definiera en falsk identitets profil som returneras till appen.

När du till exempel försöker navigera till `/.auth/login/github` , returneras en sida som gör att du kan definiera en identitets profil.

> [!NOTE]
> Emulatorn fungerar med valfri säkerhetsprovider, inte bara GitHub.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Lokala autentiserings-och auktoriserings-emulator":::

Emulatorn tillhandahåller en sida som gör att du kan ange följande [klient huvud](./user-information.md#client-principal-data) värden:

| Värde | Beskrivning |
| --- | --- |
| **Användarnamn** | Konto namnet som är associerat med säkerhets leverantören. Det här värdet visas som- `userDetails` egenskapen i klientens huvud namn och genereras automatiskt om du inte anger något värde. |
| **Användar-ID** | Värdet genereras automatiskt av CLI.  |
| **Roller** | En lista över roll namn, där varje namn finns på en ny rad.  |

När du har loggat in:

- Du kan använda `/.auth/me` slut punkten eller en funktions slut punkt för att hämta användarens [klient huvud namn](./user-information.md).

- När du navigerar för att `./auth/logout` ta bort klientens huvud namn och loggar ut den blå användaren.

## <a name="debugging"></a>Felsökning

Det finns två fel söknings kontexter i en statisk webbapp. Det första är för den statiska innehålls platsen och den andra är för API-funktioner. Lokal fel sökning är möjlig genom att tillåta att den statiska Web Apps CLI använder utvecklings servrar för en eller båda dessa kontexter.

Följande steg visar ett vanligt scenario som använder utvecklings servrar för både fel söknings kontexter.

1. Starta den statiska plats utvecklings servern. Det här kommandot är särskilt för det front-end-ramverk som du använder, men de kommer ofta i form av kommandon som `npm run build` , `npm start` eller `npm run dev` .

1. Öppna mappen API-app i Visual Studio Code och starta en felsökningssession.

1. Skicka adresserna för den statiska servern och API-servern till `swa start` kommandot genom att lista dem i ordning.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

Följande skärm bilder visar terminaler för ett typiskt fel söknings scenario:

Den statiska innehålls platsen körs via `npm run dev` .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Statisk plats utvecklings Server":::

Azure Functions API-programmet kör en felsökningssession i Visual Studio Code.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Visual Studio Code API-felsökning":::

Den statiska Web Apps CLI startas med båda utvecklings servrarna.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Azures statiska Web Apps CLI-Terminal":::

Begär Anden som går via porten `4280` dirigeras till antingen den statiska innehålls utvecklings servern eller API-felsökningssessionen.

Mer information om olika fel söknings scenarier, med anvisningar om hur du anpassar portar och Server adresser, finns i [Azures statiska Web Apps CLI-lagringsplats](https://github.com/Azure/static-web-apps-cli).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera ditt program](configuration.md)
