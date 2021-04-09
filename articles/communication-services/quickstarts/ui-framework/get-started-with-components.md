---
title: Kom igång med bas komponenter för Azure Communication Services UI Framework
titleSuffix: An Azure Communication Services quickstart
description: I den här snabb starten får du lära dig hur du kommer igång med bas komponenter i UI Framework
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 336059007cfca40a74ad5a4395c6f9a59215bb58
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105825789"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>Snabb start: kom igång med grundläggande komponenter i UI Framework

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Kom igång med Azure Communication Services genom att använda UI-ramverket för att snabbt integrera kommunikations upplevelser i dina program. I den här snabb starten får du lära dig hur du integrerar grundläggande komponenter i GRÄNSSNITTs ramverket i programmet för att bygga kommunikations upplevelser. 

UI Framework-komponenter levereras i två varianter: Base och Composite.

- **Bas komponenter** representerar diskreta kommunikations funktioner; de är de grundläggande Bygg stenarna som kan användas för att skapa komplexa kommunikations upplevelser. 
- **Sammansatta komponenter** är insikter om vanliga kommunikations scenarier som har skapats med **bas komponenter** som bygg stenar och paketerade för att enkelt integreras i program.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktiva LTS-och underhålls LTS-versioner (nod 12 rekommenderas).
- En Active Communication Services-resurs. [Skapa en kommunikations tjänst resurs](./../create-communication-resource.md).
- En token för användar åtkomst för att instansiera anrops klienten. Lär dig hur du [skapar och hanterar användar åtkomst-token](./../access-tokens.md).

## <a name="setting-up"></a>Konfigurera

UI-ramverket kräver att en reagerar-miljö ska installeras. Nu ska vi göra det. Om du redan har en reagerar-app kan du hoppa över det här avsnittet.

### <a name="set-up-react-app"></a>Konfigurera appen reagera

Vi använder mallen Create-reagerar-app för den här snabb starten. Mer information finns i: [Kom igång med reagera](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

I slutet av den här processen bör du ha ett fullständigt program i mappen `my-app` . I den här snabb starten kommer vi att ändra filer i `src` mappen.

### <a name="install-the-package"></a>Installera paketet

Använd `npm install` kommandot för att installera Azure Communication Services som anropar SDK för Java Script. Flytta den angivna tarball (privat förhands granskning) till katalogen My-app.

```console

//For Private Preview install tarball

npm install --save ./{path for tarball}

```

I det här `--save` alternativet visas biblioteket som ett beroende i **package.jsi** filen.

### <a name="run-create-react-app"></a>Kör skapa reakta app

Låt oss testa installationen av att reagera på appen genom att köra:

```console

npm run start   

```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services UI SDK:

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Leverantör| Fluent UI-provider som gör det möjligt för utvecklare att ändra underliggande komponenter i Fluent UI|
| CallingProvider| Anropar Provider för att instansiera ett anrop. Krävs för att lägga till extra komponenter|
| ChatProvider | Chat-providern för att instansiera en chatt-tråd. Krävs för att lägga till extra komponenter|
| MediaGallery | Bas komponent som visar samtals deltagare och deras fjärranslutna video strömmar |
| MediaControls | Bas komponent för att styra anrop, inklusive ljud av, video, dela skärm |
| ChatThread | Bas komponent som återger en chatt med Skriv indikatorer, Läs kvitton osv. |
| Skicka | Bas komponent som låter användaren skicka meddelanden som skickas till den anslutna tråden|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Initiera anropande och chatt-providers med Azure Communication Services-autentiseringsuppgifter

Gå till `src` mappen i `my-app` och leta efter filen `app.js` . Här ska vi ta bort följande kod för att initiera våra leverantörer av samtal och chatt. Dessa leverantörer ansvarar för att upprätthålla kontexten för samtals-och chatt upplevelser. Du kan välja vilken som ska användas beroende på vilken typ av kommunikations upplevelse som du skapar. Om det behövs kan du använda båda samtidigt. För att initiera komponenterna behöver du en åtkomsttoken som hämtats från Azure Communication Services. Mer information om hur du hämtar åtkomsttoken finns i: [skapa och hantera åtkomsttoken](./../access-tokens.md).

> [!NOTE]
> Komponenterna genererar inte åtkomsttoken, grupp-ID eller tråd-ID. Dessa element kommer från tjänster som går igenom de rätta stegen för att generera dessa ID: n och skicka dem till klient programmet. Mer information finns i: [klient server arkitektur](./../../concepts/client-and-server-architecture.md).
> 
> Exempel: chatt-providern förväntar sig att den `userId` associerade till den som `token` används för att initiera den redan är ansluten till den som `threadId` tillhandahålls. Om token inte har anslutits till tråd-ID: t kommer chatt-providern att Miss sen. Mer information om chatt finns i: [komma igång med chatt](./../chat/get-started.md)

Vi använder ett Fluent UI-tema för att förbättra utseendet och känslan av programmet:

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

När den här providern har initierats kan du bygga en egen layout med hjälp av grundläggande komponenter i GRÄNSSNITTs ramverket och eventuell extra layout logik. Providern tar hand om att initiera all underliggande logik och korrekt ansluta de olika komponenterna till varandra. Härnäst ska vi använda olika bas komponenter som tillhandahålls av UI Framework för att bygga kommunikations upplevelser. Du kan anpassa layouten för dessa komponenter och lägga till andra anpassade komponenter som du vill återge med dem. 

## <a name="build-ui-framework-calling-component-experiences"></a>Bygg användar gränssnitts ramverk som anropar komponent upplevelser

För att anropa ska vi använda- `MediaGallery` och- `MediaControls` komponenterna. Mer information om dem finns i [UI Framework-funktioner](./../../concepts/ui-framework/ui-sdk-features.md). Starta genom att `src` skapa en ny fil med namnet i mappen `CallingComponents.js` . Här initierar vi en funktions komponent som innehåller våra bas komponenter och sedan importerar i `app.js` . Du kan lägga till extra layout och formatering runt komponenterna. 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

Längst ned i den här filen exporterade vi de anropande komponenterna med hjälp av-  `connectFuncsToContext` metoden från UI-ramverket för att ansluta de anropande gränssnitts komponenterna till det underliggande läget med hjälp av mappnings funktionen `MapToCallingSetupProps` . Den här metoden ger komponenten en ifylld egenskap, som vi använder för att kontrol lera status och ansluta till anropet. `isCallInitialized`Använd egenskapen för att kontrol lera om `CallAgent` är redo och Använd sedan `joinCall` metoden för att ansluta i. UI Framework stöder anpassade mappnings funktioner som används för scenarier där utvecklare vill styra hur data skickas till komponenterna.

## <a name="build-ui-framework-chat-component-experiences"></a>Utveckla gränssnitt för användar gränssnitts ramverkets Chat

För chatt kommer vi att använda- `ChatThread` och- `SendBox` komponenterna. Mer information om dessa komponenter finns i [UI Framework-funktioner](./../../concepts/ui-framework/ui-sdk-features.md). Starta genom att `src` skapa en ny fil med namnet i mappen `ChatComponents.js` . Här initierar vi en funktions komponent som innehåller våra bas komponenter och sedan importerar i `app.js` .

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>Lägga till uppringnings-och chatt komponenter i huvud programmet

I `app.js` filen kommer vi nu att lägga till komponenterna i `CallingProvider` och `ChatProvider` som visas nedan.

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

```

## <a name="run-quickstart"></a>Kör snabb start

Om du vill köra koden ovan använder du kommandot:

```console

npm run start   

```

Om du vill testa funktionerna fullständigt behöver du en andra klient med anropa och chatta för att ansluta till anrops-och chatt-tråden. Se vårt exempel på [samtals hjälte](./../../samples/calling-hero-sample.md) och [chatt-exempel](./../../samples/chat-hero-sample.md) som möjliga alternativ.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa sammansatta komponenter i UI Framework](./get-started-with-composites.md)

Mer information finns i följande resurser:
- [Översikt över UI Framework](../../concepts/ui-framework/ui-sdk-overview.md)
- [Funktioner i UI Framework](./../../concepts/ui-framework/ui-sdk-features.md)
