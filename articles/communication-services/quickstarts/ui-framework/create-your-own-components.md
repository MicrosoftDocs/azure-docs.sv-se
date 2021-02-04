---
title: Skapa en egen UI Framework-komponent
titleSuffix: An Azure Communication Services quickstart
description: I den här snabb starten får du lära dig hur du skapar en anpassad komponent som är kompatibel med UI-ramverket
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 88de5ca56738e13446be30fb14df6bcdac360827
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539868"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>Snabb start: skapa en egen UI Framework-komponent

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Kom igång med Azure Communication Services genom att använda UI-ramverket för att snabbt integrera kommunikations upplevelser i dina program.

I den här snabb starten får du lära dig hur du skapar dina egna komponenter med hjälp av ett fördefinierat tillstånds gränssnitt som erbjuds av UI Framework. Den här metoden är idealisk för utvecklare som behöver mer anpassning och vill använda sina egna design till gångar för att få hjälp. 

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktiva LTS-och underhålls LTS-versioner (nod 12 rekommenderas).
- En Active Communication Services-resurs. [Skapa en kommunikations tjänst resurs](./../create-communication-resource.md).
- En token för användar åtkomst för att instansiera anrops klienten. Lär dig hur du [skapar och hanterar användar åtkomst-token](./../access-tokens.md).

UI-ramverket kräver att en reagerar-miljö konfigureras. Nu ska vi göra det. Om du redan har en reagerar-app kan du hoppa över det här avsnittet.

### <a name="set-up-react-app"></a>Konfigurera appen reagera

Vi använder mallen Create-reagerar-app för den här snabb starten. Mer information finns i: [Kom igång med reagera](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

I slutet av den här processen bör du ha ett fullständigt program i mappen `my-app` . I den här snabb starten kommer vi att ändra filer i `src` mappen.

### <a name="install-the-package"></a>Installera paketet

Använd `npm install` kommandot för att installera Azure Communication Services som anropar klient bibliotek för Java Script. Flytta den angivna tarball (privat förhands granskning) till katalogen My-app.

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

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i klient biblioteket för Azure Communication Services UI:

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Leverantör| Fluent UI-provider som gör det möjligt för utvecklare att ändra underliggande komponenter i Fluent UI|
| CallingProvider| Anropar Provider för att instansiera ett anrop. Krävs för att lägga till bas komponenter|
| ChatProvider | Chat-providern för att instansiera en chatt-tråd. Krävs för att lägga till bas komponenter|
| connectFuncsToContext | Metod för att ansluta UI Framework-komponenter med underliggande providers med hjälp av mapper |
| MapToChatMessageProps | Chat-meddelande data lager mappning som tillhandahåller komponenter med chatt meddelande propor |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Initiera chatt-providers med Azure Communication Services-autentiseringsuppgifter

I den här snabb starten kommer vi att använda chatt som exempel. mer information om hur du anropar finns i snabb start för [bas komponenter](./get-started-with-components.md) och [sammansatta komponenter](./get-started-with-composites.md).

Gå till `src` mappen i `my-app` och leta efter filen `app.js` . Här ska vi ta bort följande kod för att initiera vår chatt-leverantör. Den här providern ansvarar för att upprätthålla kontexten för samtals-och chatt upplevelser. För att initiera komponenterna behöver du en åtkomsttoken som hämtats från Azure Communication Services. Mer information om hur du skaffar en åtkomsttoken finns i: [skapa och hantera åtkomsttoken](./../access-tokens.md).

UI Framework-komponenter följer samma allmänna arkitektur för resten av tjänsten. Komponenterna genererar inte åtkomsttoken, grupp-ID eller tråd-ID. Dessa element kommer från tjänster som går igenom de rätta stegen för att generera dessa ID: n och skicka dem till klient programmet. Mer information finns i [klient server arkitektur](./../../concepts/client-and-server-architecture.md).

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

När den här providern har initierats kan du bygga en egen layout med hjälp av UI Framework-komponenten och eventuell eventuell eventuell eventuell kod. Providern tar hand om att initiera all underliggande logik och korrekt ansluta de olika komponenterna till varandra. Nu ska vi skapa en anpassad komponent med hjälp av UI Framework-mappningar för att ansluta till vår chatt-leverantör.


## <a name="create-a-custom-component-using-mappers"></a>Skapa en anpassad komponent med hjälp av mappars

Vi börjar med att skapa en ny fil med namnet `SimpleChatThread.js` där vi skapar komponenten. Vi kommer att börja med att importera de UI Framework-komponenter vi behöver. Här kommer vi att använda rutan HTML och reagera på att skapa en helt anpassad komponent för en enkel chatt-tråd. Med hjälp av `connectFuncsToContext` -metoden använder vi `MapToChatMessageProps` Mapper för att mappa propor till  `SimpleChatThread` anpassade komponenter. Dessa propor ger oss åtkomst till de chatt meddelanden som skickas och tas emot för att fylla dem på den enkla tråden.

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>Lägg till din anpassade komponent i programmet

Nu när vår anpassade komponent är klar kommer vi att importera den och lägga till den i vår layout.

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>Kör snabb start

Om du vill köra koden ovan använder du kommandot:

```console

npm run start   

```

Om du vill testa funktionerna fullständigt behöver du en andra klient med chatt-funktionen för att skicka meddelanden som tas emot av vår enkla chatt-tråd. Se vårt exempel på [samtals hjälte](./../../samples/calling-hero-sample.md) och [chatt-exempel](./../../samples/chat-hero-sample.md) som möjliga alternativ.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa sammansatta komponenter i UI Framework](./get-started-with-composites.md)

Mer information finns i följande resurser:
- [Översikt över UI Framework](../../concepts/ui-framework/ui-sdk-overview.md)
- [Funktioner i UI Framework](./../../concepts/ui-framework/ui-sdk-features.md)
- [Snabb start för bas komponenter i UI Framework](./get-started-with-components.md)
