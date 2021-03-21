---
title: Skapa en funktion i Go-eller Rust med Visual Studio Code-Azure Functions
description: Lär dig hur du skapar en go-funktion som en Azure Functions anpassad hanterare och sedan publicerar det lokala projektet till Server lös värd i Azure Functions med hjälp av Azure Functions tillägget i Visual Studio Code.
ms.topic: quickstart
ms.date: 12/4/2020
ms.openlocfilehash: 8b53031315cce3651a2de581d71da6ef6fe909c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470378"
---
# <a name="quickstart-create-a-go-or-rust-function-in-azure-using-visual-studio-code"></a>Snabb start: skapa en Go-eller Rust-funktion i Azure med Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

I den här artikeln använder du Visual Studio Code för att skapa en [anpassad hanterings](functions-custom-handlers.md) funktion som svarar på http-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.

Anpassade hanterare kan användas för att skapa funktioner på valfritt språk eller i körnings miljö genom att köra en HTTP-Server process. Den här artikeln stöder både [Go](create-first-function-vs-code-other.md?tabs=go) -och [Rust](create-first-function-vs-code-other.md?tabs=rust).

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

## <a name="configure-your-environment"></a>Konfigurera din miljö

Innan du börjar bör du kontrol lera att du har följande krav på plats:

# <a name="go"></a>[Kör](#tab/go)

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Visual Studio Code](https://code.visualstudio.com/) på någon av [plattformarna som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code.

+ [Azure Functions Core tools](./functions-run-local.md#v2) version 3. x. Använd `func --version` kommandot för att kontrol lera att det är korrekt installerat.

+ [Go](https://golang.org/doc/install), den senaste versionen rekommenderas. Använd `go version` kommandot för att kontrol lera din version.

# <a name="rust"></a>[Rust](#tab/rust)

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Visual Studio Code](https://code.visualstudio.com/) på någon av [plattformarna som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code.

+ [Azure Functions Core tools](./functions-run-local.md#v2) version 3. x. Använd `func --version` kommandot för att kontrol lera att det är korrekt installerat.

+ Rust verktygskedjan med hjälp av [rustup](https://www.rust-lang.org/tools/install). Använd `rustc --version` kommandot för att kontrol lera din version.

---

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Skapa ditt lokala projekt

I det här avsnittet använder du Visual Studio Code för att skapa ett lokalt Azure Functions anpassat hanterings projekt. Senare i den här artikeln ska du publicera funktionskoden till Azure.

1. Välj Azure-ikonen i Aktivitetsfältet och gör sedan följande i **Azure: I området för funktioner** väljer du ikonen **Skapa nytt projekt...**.

    ![Välj Skapa ett nytt projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Välj en katalogplats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > De här stegen har utformats för att slutföras utanför en arbets yta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Ange följande information i meddelanderutorna:

    + **Välj ett språk för ditt funktionsprojekt**: Välj `Custom`.

    + **Välj en mall för projektets första funktion**: Välj `HTTP trigger`.

    + **Ange ett funktionsnamn**: Skriv `HttpExample`.

    + **Autentiseringsnivå**: Välj `Anonymous` , vilket gör att vem som helst kan anropa funktions slut punkten. Mer information om autentiseringsnivå finns i [Authorization Keys](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Välj hur du vill öppna projektet**: Välj `Add to workspace`.

1. Med hjälp av den här informationen genererar Visual Studio Code ett Azure Functions-projekt med en HTTP-utlösnings funktion. Du kan visa de lokala projektfilerna i Utforskaren. Mer information om filer som skapas finns i [genererade projektfiler](functions-develop-vs-code.md#generated-project-files). 

## <a name="create-and-build-your-function"></a>Skapa och skapa din funktion

*function.js* filen i mappen *HttpExample* DEKLARerar en http-utlösnings funktion. Du slutför funktionen genom att lägga till en hanterare och kompilera den till en körbar fil.

# <a name="go"></a>[Kör](#tab/go)

1. Tryck på <kbd>CTRL + n</kbd> (<kbd>cmd + n</kbd> på MacOS) för att skapa en ny fil. Spara som *hanterare. gå* till Function app-roten (i samma mapp som *host.jspå*).

1. I *hanteraren. go* lägger du till följande kod och sparar filen. Det här är din anpassade hanterare.

    ```go
    package main
    
    import (
        "fmt"
        "log"
        "net/http"
        "os"
    )
    
    func helloHandler(w http.ResponseWriter, r *http.Request) {
        message := "This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.\n"
        name := r.URL.Query().Get("name")
        if name != "" {
            message = fmt.Sprintf("Hello, %s. This HTTP triggered function executed successfully.\n", name)
        }
        fmt.Fprint(w, message)
    }
    
    func main() {
        listenAddr := ":8080"
        if val, ok := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT"); ok {
            listenAddr = ":" + val
        }
        http.HandleFunc("/api/HttpExample", helloHandler)
        log.Printf("About to listen on %s. Go to https://127.0.0.1%s/", listenAddr, listenAddr)
        log.Fatal(http.ListenAndServe(listenAddr, nil))
    }
    ```

1. Tryck på <kbd>CTRL + SKIFT + '</kbd> eller Välj *ny terminal* på menyn *Terminal* för att öppna en ny integrerad Terminal i vs Code.

1. Kompilera din anpassade hanterare med hjälp av följande kommando. En körbar fil med namnet `handler` ( `handler.exe` i Windows) är utdata i rotmappen för Function-appen.

    ```bash
    go build handler.go
    ```

    ![VS Code – build go anpassad hanterare](./media/functions-create-first-function-vs-code/functions-vscode-go.png)

# <a name="rust"></a>[Rust](#tab/rust)

1. Tryck på <kbd>CTRL + SKIFT + '</kbd> eller Välj *ny terminal* på menyn *Terminal* för att öppna en ny integrerad Terminal i vs Code.

1. I Function app-roten (samma mapp som *host.jspå*) initierar du ett Rust-projekt med namnet `handler` .

    ```bash
    cargo init --name handler
    ```

1. I *Last. toml* lägger du till följande beroenden som krävs för att slutföra den här snabb starten. Exemplet använder ramverket [Tänj](https://docs.rs/warp/) webb server.

    ```toml
    [dependencies]
    warp = "0.3"
    tokio = { version = "1", features = ["rt", "macros", "rt-multi-thread"] }
    ```

1. I *src/main. rs* lägger du till följande kod och sparar filen. Det här är din anpassade Rust-hanterare.

    ```rust
    use std::collections::HashMap;
    use std::env;
    use std::net::Ipv4Addr;
    use warp::{http::Response, Filter};

    #[tokio::main]
    async fn main() {
        let example1 = warp::get()
            .and(warp::path("api"))
            .and(warp::path("HttpExample"))
            .and(warp::query::<HashMap<String, String>>())
            .map(|p: HashMap<String, String>| match p.get("name") {
                Some(name) => Response::builder().body(format!("Hello, {}. This HTTP triggered function executed successfully.", name)),
                None => Response::builder().body(String::from("This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.")),
            });

        let port_key = "FUNCTIONS_CUSTOMHANDLER_PORT";
        let port: u16 = match env::var(port_key) {
            Ok(val) => val.parse().expect("Custom Handler port is not a number!"),
            Err(_) => 3000,
        };

        warp::serve(example1).run((Ipv4Addr::UNSPECIFIED, port)).await
    }
    ```

1. Kompilera en binärfil för din anpassade hanterare. En körbar fil med namnet `handler` ( `handler.exe` i Windows) är utdata i rotmappen för Function-appen.

    ```bash
    cargo build --release
    cp target/release/handler .
    ```

    ![VS Code – build Rust anpassad hanterare](./media/functions-create-first-function-vs-code/functions-vscode-rust.png)

---

## <a name="configure-your-function-app"></a>Konfigurera din Function-app

Funktions värden måste konfigureras för att köra den anpassade hanterare-binärfilen när den startas.

1. Öppna *host.jspå*.

1. I `customHandler.description` avsnittet ställer du in värdet för `defaultExecutablePath` till `handler` (i Windows, ställer in det på `handler.exe` ).

1. I `customHandler` avsnittet lägger du till en egenskap med namnet `enableForwardingHttpRequest` och anger dess värde till `true` . För funktioner som bara består av en HTTP-utlösare, underlättar den här inställningen programmering genom att du kan arbeta med en typisk HTTP-begäran i stället för den anpassade hanteraren [begär nytto Last](functions-custom-handlers.md#request-payload).

1. Bekräfta att `customHandler` avsnittet ser ut som i det här exemplet. Spara filen.

    ```
    "customHandler": {
      "description": {
        "defaultExecutablePath": "handler",
        "workingDirectory": "",
        "arguments": []
      },
      "enableForwardingHttpRequest": true
    }
    ```

Function-appen har kon figurer ATS för att starta den anpassade hanterarens körbara fil.

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

Du kan köra det här projektet på den lokala utvecklings datorn innan du publicerar till Azure.

1. Starta Function-appen i den integrerade terminalen med hjälp av Azure Functions Core Tools.

    ```bash
    func start
    ```

1. Med kärn verktyg som kör, navigerar du till följande URL för att köra en GET-begäran, som inkluderar `?name=Functions` frågesträng.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Ett svar returneras, vilket ser ut så här i en webbläsare:

    ![Webbläsare – exempel på localhost-utdata](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. Information om begäran visas på panelen **Terminal** .

    ![Start-VS Code-utdata för aktivitets värd](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Tryck på <kbd>CTRL + C</kbd> för att stoppa Core tools.

När du har kontrollerat att funktionen fungerar korrekt på den lokala datorn är det dags att använda Visual Studio Code för att publicera projektet direkt till Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="compile-the-custom-handler-for-azure"></a>Kompilera den anpassade hanteraren för Azure

I det här avsnittet publicerar du ditt projekt till Azure i en Function-app som kör Linux. I de flesta fall måste du kompilera om binärfilen och ändra konfigurationen så att den matchar mål plattformen innan du publicerar den på Azure.

# <a name="go"></a>[Kör](#tab/go)

1. Kompilera hanteraren till linux/x64 i den integrerade terminalen. En binär namngiven namn `handler` skapas i Function app-roten.

    # <a name="macos"></a>[macOS](#tab/macos)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="windows"></a>[Windows](#tab/windows)
    ```cmd
    set GOOS=linux
    set GOARCH=amd64
    go build handler.go
    ```

    Ändra `defaultExecutablePath` i *host.js* `handler.exe` till `handler` . Detta gör att Function-appen kan köra Linux-binärformat.
    
    ---

# <a name="rust"></a>[Rust](#tab/rust)

1. Skapa en fil på *. Last/config*. Lägg till följande innehåll och spara filen.

    ```
    [target.x86_64-unknown-linux-musl]
    linker = "rust-lld"
    ```

1. Kompilera hanteraren till linux/x64 i den integrerade terminalen. Ett binärt namn `handler` har skapats. Kopiera den till Function-app-roten.

    ```bash
    rustup target add x86_64-unknown-linux-musl
    cargo build --release --target=x86_64-unknown-linux-musl
    cp target/x86_64-unknown-linux-musl/release/handler .
    ```

1. Om du använder Windows, ändra `defaultExecutablePath` i *host.js* `handler.exe` till `handler` . Detta gör att Function-appen kan köra Linux-binärformat.

1. Lägg till följande rad i *. funcignore* -filen:

    ```
    target
    ```

    Detta förhindrar publicering av innehållet i *målmappen* .

---

## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

I det här avsnittet ska du skapa en Function-app och relaterade resurser i din Azure-prenumeration och sedan distribuera din kod. 

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure. 


1. Välj Azure-ikonen i aktivitets fältet och välj sedan knappen **distribuera till Function-appen** i avsnittet **Azure:** functions.

    ![Publicera projektet till Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Ange följande information i meddelanderutorna:

    + **Välj mapp**: Välj en mapp från din arbets yta eller bläddra till en mapp som innehåller din Function-app. Du ser inte det här om du redan har en giltig Function-app öppen.

    + **Välj prenumeration**: Välj den prenumeration som ska användas. Du ser inte det här om du bara har en prenumeration.

    + **Välj Funktionsapp i Azure**: Välj `+ Create new Function App (advanced)`. 
    
        > [!IMPORTANT]
        > Med `advanced` alternativet kan du välja det operativ system som din funktions program körs i Azure, vilket i det här fallet är Linux.

        ![VS-kod – Välj avancerad skapa ny function-app](./media/functions-create-first-function-vs-code/functions-vscode-create-azure-advanced.png)

    + **Ange ett globalt unikt namn för Function-appen**: Ange ett namn som är giltigt i en URL-sökväg. Namnet du skriver verifieras för att säkerställa att det är unikt i Azure Functions.

    + **Välj en körnings stack**: Välj `Custom Handler` .

    + **Välj ett operativ system**: Välj `Linux` .

    + **Välj en värd plan**: Välj `Consumption` .

    + **Välj en resurs grupp**: Välj `+ Create new resource group` . Ange ett namn för resurs gruppen. Det här namnet måste vara unikt i din Azure-prenumeration. Du kan använda det namn som föreslås i prompten.

    + **Välj ett lagrings konto**: Välj `+ Create new storage account` . Det här namnet måste vara globalt unikt inom Azure. Du kan använda det namn som föreslås i prompten.

    + **Välj en Application Insights resurs**: Välj `+ Create Application Insights resource` . Det här namnet måste vara globalt unikt inom Azure. Du kan använda det namn som föreslås i prompten.

    + **Välj en plats för nya resurser**: om du vill ha bättre prestanda väljer du en [region](https://azure.microsoft.com/regions/) nära dig. Tillägget visar status för enskilda resurser när de skapas i Azure i meddelande fältet.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Meddelande om skapande av Azure-resurs":::

1. När det är slutfört skapas följande Azure-resurser i din prenumeration:

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. 

4. Välj **Visa utdata** i det här meddelandet för att Visa skapande-och distributions resultaten, inklusive de Azure-resurser som du har skapat. Om du saknar meddelandet väljer du klock ikonen i det nedre högra hörnet för att se den igen.

    ![Skapa fullständig avisering](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om att Azure Functions anpassade hanterare](functions-custom-handlers.md)
