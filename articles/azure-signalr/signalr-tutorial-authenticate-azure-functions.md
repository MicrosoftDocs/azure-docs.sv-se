---
title: 'Självstudie: autentisering med Azure Functions – Azure-SignalR'
description: I den här självstudien får du lära dig att autentisera Azure SignalR service-klienter för Azure Functions bindning
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: zhshang
ms.custom: devx-track-js
ms.openlocfilehash: 6df47d3fd62083a5d0940a1d6da50ac5d7d955f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92150910"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Självstudier: Azure SignalR Service-autentisering med Azure Functions

En stegvis självstudiekurs som beskriver hur du skapar ett chattrum med autentisering och privata meddelanden med Azure Functions, App Service-autentisering och SignalR Service.

## <a name="introduction"></a>Introduktion

### <a name="technologies-used"></a>Tekniker som används

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Serverdels-API för att autentisera användare och skicka chattmeddelanden
* [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Skicka nya meddelanden till anslutna chattklienter
* [Azure Storage](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Lagra den statiska webbplatsen för chattklientens användargränssnitt

### <a name="prerequisites"></a>Förutsättningar

Följande programvara krävs för den här självstudien.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (version 10.x)
* [.NET SDK](https://www.microsoft.com/net/download) (version 2.x, krävs för Functions-tillägg)
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) (version 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) med följande tillägg
  * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) – Arbeta med Azure Functions i VS Code
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) – Betjäna webbsidor lokalt för testning

[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsauth)

## <a name="sign-into-the-azure-portal"></a>Logga in på Azure-portalen

Gå till [Azure-portalen](https://portal.azure.com/) och logga in med dina autentiseringsuppgifter.

[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsauth)

## <a name="create-an-azure-signalr-service-instance"></a>Skapa en Azure SignalR Service-instans

Du kan skapa och testa Azure Functions-appen lokalt. Appen använder en SignalR Service-instans i Azure som måste skapas i förväg.

1. Klicka på knappen **skapa en resurs** ( **+** ) för att skapa en ny Azure-resurs.

1. Sök efter och välj **SignalR Service**. Klicka på **Skapa**.

    ![Ny SignalR-tjänst](media/signalr-tutorial-authenticate-azure-functions/signalr-quickstart-new.png)

1. Ange följande information.

    | Name | Värde |
    |---|---|
    | Resursnamn | Ett unikt namn för SignalR Service-instansen |
    | Resursgrupp | Skapa en ny resurs grupp med ett unikt namn |
    | Location | Välj en plats nära dig |
    | Prisnivå | Kostnadsfri |

1. Klicka på **Skapa**.

1. När instansen har distribuerats öppnar du den i portalen och letar upp dess inställnings sida. Ändra inställningen för tjänst läge till *Server* lös.

    ![SignalR service läge](media/signalr-concept-azure-functions/signalr-service-mode.png)
    
[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsauth)

## <a name="initialize-the-function-app"></a>Initiera funktionsappen

### <a name="create-a-new-azure-functions-project"></a>Skapa ett nytt Azure Functions-projekt

1. Använd `File > Open Folder` på menyn i ett nytt VS Code-fönster för att skapa och öppna en tom mapp på lämplig plats. Det här är den primära projektmappen för programmet som du skapar.

1. Använd Azure Functions-tilläggen i VS Code för att initiera en funktionsapp i den primära projektmappen.
   1. Öppna kommandopaletten i VS Code genom att välja **Visa > Kommandopalett** från menyn (genväg `Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).
   1. Leta upp och välj kommandot **Azure Functions: Create New Project** (Azure Functions: Skapa nytt projekt).
   1. Den primära projektmappen bör visas. Välj den (eller använd ”Bläddra” för att leta upp den).
   1. När du uppmanas att välja ett språk väljer du **JavaScript**.

      ![Skapa en funktionsapp](media/signalr-tutorial-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>Installera funktionsapptillägg

I den här självstudien används Azure Functions-bindningar för att interagera med Azure SignalR Service. Som de flesta andra bindningar är SignalR Service-bindningarna tillgängliga som tillägg som måste installeras med hjälp av Azure Functions Core Tools CLI innan de kan användas.

1. Öppna en Terminal i VS Code genom att välja **visa > Terminal** på menyn (Ctrl- \` ).

1. Kontrollera att den primära projektmappen är den aktuella katalogen.

1. Installera SignalR Service-funktionsapptillägget.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0
    ```

### <a name="configure-application-settings"></a>Konfigurera programinställningar

När du kör och felsöker Azure Functions-körningsmiljön lokalt läser Azure Functions programinställningar från **local.settings.json**. Uppdatera den här filen med anslutningssträngen för den SignalR Service-instans som du skapade tidigare.

1. Välj **local.settings.json** i Explorer-fönstret i VS Code för att öppna filen.

1. Ersätt filens innehåll med följande.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.14.1",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "http://127.0.0.1:5500",
            "CORSCredentials": true
        }
    }
    ```

   * Ange anslutningssträngen för Azure SignalR Service i en inställning med namnet `AzureSignalRConnectionString`. Hämta värdet från sidan **Nycklar** i Azure SignalR Service-resursen på Azure-portalen. Du kan använda antingen den primära eller den sekundära anslutningssträngen.
   * Inställningen `WEBSITE_NODE_DEFAULT_VERSION` används inte lokalt, men krävs när du distribuerar till Azure.
   * Avsnittet `Host` konfigurerar porten och CORS-inställningarna för den lokala Functions-värden (den här inställningen har ingen effekt när du kör i Azure).

       > [!NOTE]
       > Live Server är vanligt vis konfigurerat för att hantera innehåll från `http://127.0.0.1:5500` . Om du upptäcker att den använder en annan URL eller om du använder en annan HTTP-Server ändrar du `CORS` inställningen så att den återspeglar rätt ursprung.

     ![Hämta SignalR Service-nyckeln](media/signalr-tutorial-authenticate-azure-functions/signalr-get-key.png)

1. Spara filen.

[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsauth)

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Skapa en funktion för att autentisera användare till SignalR Service

När chattappen öppnas i webbläsaren krävs giltiga autentiseringsuppgifter för att ansluta till Azure SignalR Service. Du ska skapa en HTTP-utlöst funktion med namnet *Negotiate* i din Function-app för att returnera den här anslutnings informationen.

> [!NOTE]
> Den här funktionen måste vara *Negotiate* eftersom signal klienten kräver en slut punkt som slutar med `/negotiate` .

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Leta upp och välj kommandot **Azure Functions: Kommandot Skapa funktion**.

1. När du uppmanas att göra det anger du följande information.

    | Namn | Värde |
    |---|---|
    | Mappen för funktionsappen | Välj den primära projektmappen |
    | Mall | HTTP-utlösare |
    | Namn | negotiate |
    | Auktoriseringsnivå | Anonym |

    En mapp med namnet **Negotiate** skapas som innehåller den nya funktionen.

1. Öppna **Negotiate/function.jspå om** du vill konfigurera bindningar för funktionen. Ändra innehållet i filen till följande. En indatabindning läggs till som genererar giltiga autentiseringsuppgifter för en klient så att den kan ansluta till en Azure SignalR Service-hubb med namnet `chat`.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    Egenskapen `userId` i bindningen `signalRConnectionInfo` används för att skapa en autentiserad SignalR Service-anslutning. Lämna egenskapen tom för lokal utveckling. Du ska använda den när funktionsappen har distribuerats till Azure.

1. Öppna **Negotiate/index.js** för att Visa bröd texten i funktionen. Ändra innehållet i filen till följande.

    ```javascript
    module.exports = async function (context, req, connectionInfo) {
        context.res.body = connectionInfo;
    };
    ```

    Den här funktionen hämtar SignalR-anslutningsinformationen från indatabindningen och returnerar den till klienten i HTTP-svarstexten. Signal klienten kommer att använda den här informationen för att ansluta till signal tjänst instansen.

[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsauth)

## <a name="create-a-function-to-send-chat-messages"></a>Skapa en funktion för att skicka chattmeddelanden

Webbappen kräver också ett HTTP-API för att skicka chattmeddelanden. Du ska skapa en HTTP-utlöst funktion med namnet *SendMessage* som skickar meddelanden till alla anslutna klienter som använder SignalR Service.

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Leta upp och välj kommandot **Azure Functions: Kommandot Skapa funktion**.

1. När du uppmanas att göra det anger du följande information.

    | Namn | Värde |
    |---|---|
    | Mappen för funktionsappen | Välj den primära projektmappen |
    | Mall | HTTP-utlösare |
    | Namn | SendMessage |
    | Auktoriseringsnivå | Anonym |

    En mapp med namnet **SendMessage** skapas som innehåller den nya funktionen.

1. Öppna **SendMessage/function.json** för att konfigurera bindningar för funktionen. Ändra innehållet i filen till följande.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "$return",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Två ändringar görs i den ursprungliga funktionen:
    * Ändrar vägen till `messages` och begränsar HTTP-utlösaren till **POST** HTTP-metoden.
    * Lägger till en utgående bindning för signal tjänst som skickar ett meddelande som returneras av funktionen till alla klienter som är anslutna till en signal tjänst hubb med namnet `chat` .

1. Spara filen.

1. Öppna **SendMessage/index.js** för att visa själva innehållet i funktionen. Ändra innehållet i filen till följande.

    ```javascript
    module.exports = async function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        return {
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        };
    };
    ```

    Den här funktionen hämtar innehållet i HTTP-begäran och skickar det till klienter som är anslutna till SignalR Service, och anropar en funktion med namnet `newMessage` på varje klient.

    Funktionen kan läsa avsändarens identitet och kan acceptera ett *mottagarvärde* i meddelandetexten så att ett meddelande kan skickas privat till en enskild användare. Vi kommer att använda dessa funktioner senare under kursen.

1. Spara filen.

[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsauth)

## <a name="create-and-run-the-chat-client-web-user-interface"></a>Skapa och köra chattklientens webbaserade användargränssnitt

Chattprogrammets användargränssnitt är en enkel ensidesapplikation (SPA) som skapas med Vue JavaScript-ramverket. Det lagras separat från funktionsappen. Lokalt kör du webbgränssnittet med VS Code-tillägget Live Server.

1. Skapa en ny mapp i VS Code med namnet **content** i roten till den primära projektmappen.

1. Skapa en ny fil med namnet **index.html** i mappen **content**.

1. Kopiera och klistra in innehållet i **[index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/2720a9a565e925db09ef972505e1c5a7a3765be4/docs/demo/chat-with-auth/index.html)**.

1. Spara filen.

1. Tryck på **F5** för att köra funktionsappen lokalt och koppla en felsökare.

1. När filen **index.html** är öppen startar du Live Server genom att öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) och välja **Live Server: Open with Live Server** (Live servern: Öppna med Live Server). Live Server öppnar programmet i en webbläsare.

1. Programmet öppnas. Ange ett meddelande i chattrutan och tryck på Retur. Uppdatera programmet för att se nya meddelanden. Eftersom ingen autentisering har konfigurerats skickas alla meddelanden som anonyma.

[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsauth)

## <a name="deploy-to-azure-and-enable-authentication"></a>Distribuera till Azure och aktivera autentisering

Du har kört funktionsappen och chattprogrammet lokalt. Nu ska du distribuera dem till Azure och aktivera autentisering och privata meddelanden i programmet.

### <a name="log-into-azure-with-vs-code"></a>Logga in i Azure med VS Code

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Leta upp och välj kommandot **Azure: Sign in** (Azure: Logga in).

1. Följ instruktionerna för att slutföra inloggningsprocessen i webbläsaren.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Ett Azure Storage konto krävs av en Function-app som körs i Azure. Du kommer också att vara värd för webb sidan för chatt-gränssnittet med hjälp av funktionen för statiska webbplatser i Azure Storage.

1. Klicka på knappen **skapa en resurs** () i Azure Portal **+** för att skapa en ny Azure-resurs.

1. Välj **lagrings** kategori och välj sedan **lagrings konto**.

1. Ange följande information.

    | Name | Värde |
    |---|---|
    | Prenumeration | Välj den prenumeration som innehåller SignalR tjänst instansen |
    | Resursgrupp | Välj samma resurs grupp |
    | Resursnamn | Ett unikt namn för lagrings kontot |
    | Location | Välj samma plats som dina andra resurser |
    | Prestanda | Standard |
    | Typ av konto | StorageV2 (generell användning v2) |
    | Replikering | Lokalt redundant lagring (LRS) |
    | Åtkomst nivå | Frekvent |

1. Klicka på **Granska + skapa** och sedan på **skapa**.

### <a name="configure-static-websites"></a>Konfigurera statiska webbplatser

1. När lagrings kontot har skapats öppnar du det i Azure Portal.

1. Välj **statisk webbplats**.

1. Välj **aktive rad** för att aktivera funktionen för statisk webbplats.

1. I **index dokument namn** anger *index.html*.

1. Klicka på **Spara**.

1. En **primär slut punkt** visas. Observera det här värdet. Du måste konfigurera Function-appen.

### <a name="configure-function-app-for-authentication"></a>Konfigurera funktionsappen för autentisering

Hittills fungerar chattappen anonymt. Du ska använda [App Service-autentisering](../app-service/overview-authentication-authorization.md) i Azure för att autentisera användaren. Användar-ID:t eller användarnamnet för den autentiserade användaren kan skickas till *SignalRConnectionInfo*-bindningen för att generera anslutningsinformation som autentiseras som användaren.

När ett meddelande skickas kan appen bestämma om det ska skickas till alla anslutna klienter, eller endast till de klienter som har autentiserats för en viss användare.

1. I VS Code öppnar du **Negotiate/function.jspå**.

1. Infoga ett [bindningsuttryck](../azure-functions/functions-triggers-bindings.md) i egenskapen *userId* för *SignalRConnectionInfo*-bindningen: `{headers.x-ms-client-principal-name}`. Detta anger värdet till användarnamnet för den autentiserade användaren. Attributet bör nu se ut så här.

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Spara filen.


### <a name="deploy-function-app-to-azure"></a>Distribuera Function-appen till Azure

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) och välj **Azure Functions: Deploy to Function App** (Azure Functions: Distribuera till funktionsapp).

1. När du uppmanas att göra det anger du följande information.

    | Namn | Värde |
    |---|---|
    | Mapp som ska distribueras | Välj den primära projektmappen |
    | Prenumeration | Välj din prenumeration |
    | Funktionsapp | Välj **Create New Function App** (Skapa ny funktionsapp) |
    | Funktionsappens namn | Ange ett unikt namn |
    | Resursgrupp | Välj samma resursgrupp som SignalR Service-instansen |
    | Lagringskonto | Välj det lagrings konto som du skapade tidigare |

    En ny funktionsapp skapas i Azure och distributionen börjar. Vänta tills distributionen har slutförts.

### <a name="upload-function-app-local-settings"></a>Ladda upp lokala inställningar för funktionsappen

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Leta upp och välj kommandot **Azure Functions: Upload local settings** (Azure Functions: Ladda upp lokala inställningar).

1. När du uppmanas att göra det anger du följande information.

    | Namn | Värde |
    |---|---|
    | Fil för lokala inställningar | local.settings.json |
    | Prenumeration | Välj din prenumeration |
    | Funktionsapp | Välj den tidigare distribuerade funktionsappen |

Lokala inställningar laddas upp till funktionsappen i Azure. Om du uppmanas att skriva över befintliga inställningar väljer du **Ja till allt**.


### <a name="enable-app-service-authentication"></a>Aktivera App Service-autentisering

App Service-autentisering har stöd för autentisering med Azure Active Directory, Facebook, Twitter, ett Microsoft-konto och Google.

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Leta upp och välj kommandot **Azure Functions: Open in portal** (Azure Functions: Öppna på portalen).

1. Välj prenumerationen och funktionsappens namn för att öppna funktionsappen på Azure-portalen.

1. I Function-appen som öppnades i portalen, letar du upp fliken **plattforms funktioner** , väljer **autentisering/auktorisering**.

1. **Aktivera** App Service-autentisering.

1. I **Åtgärd att vidta när en begäran inte har autentiserats** väljer du ”Logga in med {autentiseringsprovidern som du valde tidigare}”.

1. I **Tillåtna externa omdirigeringswebbadresser** anger du URL:en för den primära webbslutpunkten för lagringskontot som du skrev ned tidigare.

1. Slutför konfigurationen genom att följa anvisningarna i dokumentationen för din valda inloggningsprovider.

    - [Azure Active Directory](../app-service/configure-authentication-provider-aad.md)
    - [Facebook](../app-service/configure-authentication-provider-facebook.md)
    - [Twitter](../app-service/configure-authentication-provider-twitter.md)
    - [Microsoft-konto](../app-service/configure-authentication-provider-microsoft.md)
    - [Google](../app-service/configure-authentication-provider-google.md)

### <a name="update-the-web-app"></a>Uppdatera webbappen

1. Navigera till funktionsappens översiktssida på Azure-portalen.

1. Kopiera funktionsappens URL.

    ![Hämta URL:en](media/signalr-tutorial-authenticate-azure-functions/signalr-get-url.png)

1. Öppna **index.html** i VS Code och ersätt värdet `apiBaseUrl` med funktionsappens URL.

1. Programmet kan konfigureras med autentisering med hjälp av Azure Active Directory, Facebook, Twitter, ett Microsoft-konto eller Google. Välj den autentiseringsprovider som du ska använda genom att ange värdet för `authProvider`.

1. Spara filen.

### <a name="deploy-the-web-application-to-blob-storage"></a>Distribuera webbprogrammet till bloblagring

Webbprogrammet ska hanteras av funktionen för statiska webbplatser i Azure Blob Storage.

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Leta upp och välj kommandot **Azure Storage: Deploy to Static Website** (Azure Storage: Distribuera till statisk webbplats).

1. Ange följande värden:

    | Name | Värde |
    |---|---|
    | Prenumeration | Välj din prenumeration |
    | Lagringskonto | Välj det lagrings konto som du skapade tidigare |
    | Mapp som ska distribueras | Välj **Bläddra** och välj mappen *innehåll* |

Filerna i mappen *Content* bör nu distribueras till den statiska webbplatsen.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Aktivera CORS (Cross Origin Resource Sharing) för funktionsappen

Även om det finns en CORS-inställning i **local.settings.json** sprids den inte till funktionsappen i Azure. Du måste ställa in den separat.

1. Öppna funktionsappen på Azure Portal.

1. På fliken **plattforms funktioner** väljer du **CORS**.

    ![Leta upp CORS](media/signalr-tutorial-authenticate-azure-functions/signalr-find-cors.png)

1. I avsnittet *tillåtna ursprung* lägger du till en post med den statiska platsens *primära slut punkt* som värde (ta bort det avslutande */* ).

1. För att signalera JavaScript SDK ska kunna anropa din Function-app från en webbläsare måste stöd för autentiseringsuppgifter i CORS vara aktiverat. Markera kryss rutan Aktivera åtkomst-Control-Allow-credentials.

    ![Aktivera åtkomst-Control-Allow-credentials](media/signalr-tutorial-authenticate-azure-functions/signalr-cors-credentials.png)

1. Klicka på **Spara** för att spara CORS-inställningarna.

### <a name="try-the-application"></a>Testa programmet

1. Gå till lagringskontots primära webbslutpunkt i en webbläsare.

1. Välj **Logga in** för att autentisera med din valda autentiseringsprovider.

1. Skicka offentliga meddelanden genom att skriva dem i den primära chattrutan.

1. Skicka privata meddelanden genom att klicka på ett användarnamn i chatthistoriken. Endast den valda mottagaren får dessa meddelanden.

Grattis! Du har distribuerat en serverlös realtidschattapp.

![Demo](media/signalr-tutorial-authenticate-azure-functions/signalr-serverless-chat.gif)

[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsauth)

## <a name="clean-up-resources"></a>Rensa resurser

Rensa resurserna som skapats i den här självstudien genom att ta bort resursgruppen via Azure-portalen.

[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsauth)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder Azure Functions med Azure SignalR Service. Läs mer om hur du skapar serverlösa realtidsprogram med SignalR Service-bindningar för Azure Functions.

> [!div class="nextstepaction"]
> [Bygg appar i real tid med Azure Functions](signalr-concept-azure-functions.md)

[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsauth)