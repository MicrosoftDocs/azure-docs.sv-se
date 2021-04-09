---
title: Kom igång med Azure Communication Services UI Framework SDK sammansatt komponenter
titleSuffix: An Azure Communication Services quickstart
description: I den här snabb starten får du lära dig hur du kommer igång med sammansatta GRÄNSSNITTs Ramverks komponenter
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7d6b0d01ec3de1cb4ce91b1c81cd8a0ccd73b1d1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105825806"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>Snabb start: kom igång med sammansatta UI Framework-komponenter

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Kom igång med Azure Communication Services genom att använda UI-ramverket för att snabbt integrera kommunikations upplevelser i dina program. I den här snabb starten får du lära dig hur du integrerar integrerade GRÄNSSNITTs Ramverks komponenter i ditt program för att bygga kommunikations upplevelser.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktiva LTS-och underhålls LTS-versioner (nod 12 rekommenderas).
- En Active Communication Services-resurs. [Skapa en kommunikations tjänst resurs](./../create-communication-resource.md).
- En åtkomsttoken för att instansiera anropet. Lär dig hur du [skapar och hanterar användar åtkomst-token](./../access-tokens.md).

## <a name="setting-up"></a>Konfigurera

UI-ramverket kräver att en reagerar-miljö ska installeras. Nu ska vi göra det. Om du redan har en reagerar-app kan du hoppa över det här avsnittet.

### <a name="set-up-react-app"></a>Konfigurera appen reagera

Vi kommer att använda mallen Create-reakta-app för den här snabb starten. Mer information finns i: [Kom igång med reagera](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

I slutet av den här processen bör du ha ett fullständigt program i mappen `my-app` . I den här snabb starten kommer vi att ändra filer i `src` mappen.

### <a name="install-the-package"></a>Installera paketet

Använd `npm install` kommandot för att installera Azure Communication Services som anropar SDK för Java Script. Flytta den angivna tarball (privat förhands granskning) till katalogen My-app.

```console

//Private Preview install tarball

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
| GroupCall | Sammansatt komponent som återger en grupp som anropar erfarenhet med deltagar galleriet och kontroller. |
| GroupChat | Sammansatt komponent som återger en grupps Chat-upplevelse med Chat-tråd och-ingångar |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>Initiera grupp anrop och sammansatta grupp Chat-komponenter

Gå till `src` mappen i `my-app` och leta efter filen `app.js` . Här ska vi ta bort följande kod för att initiera våra sammansatta komponenter för grupp-chatt och anropa. Du kan välja vilken som ska användas beroende på vilken typ av kommunikations upplevelse som du skapar. Om det behövs kan du använda båda samtidigt. För att initiera komponenterna behöver du en åtkomsttoken som hämtats från Azure Communication Services. Mer information om hur du hämtar åtkomsttoken finns i: [skapa och hantera token för användar åtkomst](./../access-tokens.md).

> [!NOTE]
> Komponenterna genererar inte åtkomsttoken, grupp-ID eller tråd-ID. Dessa element kommer från tjänster som går igenom de rätta stegen för att generera dessa ID: n och skicka dem till klient programmet. Mer information finns i: [klient server arkitektur](./../../concepts/client-and-server-architecture.md).
> 
> Till exempel: gruppen chatt `userId` sammanfattar att den som är kopplad till den som `token` används för att initiera den redan är ansluten till den som `threadId` tillhandahålls. Om token inte har anslutits till tråd-ID: t kommer gruppens Chat sammansatt att Miss sen. Mer information om chatt finns i: [komma igång med chatt](./../chat/get-started.md)


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

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
> [Testa bas komponenter för UI Framework](./get-started-with-components.md)

Mer information finns i följande resurser:
- [Översikt över UI Framework](../../concepts/ui-framework/ui-sdk-overview.md)
- [Funktioner i UI Framework](./../../concepts/ui-framework/ui-sdk-features.md)
