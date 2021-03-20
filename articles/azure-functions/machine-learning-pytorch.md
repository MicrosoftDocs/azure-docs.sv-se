---
title: Distribuera en PyTorch-modell som ett Azure Functions program
description: Använd ett förtränat ResNet 18 djup neurala-nätverk från PyTorch med Azure Functions för att tilldela 1 av 1000 ImageNet-etiketter till en bild.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 8891c29e5d8d06df6292d06ec06e5e57fb9880e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422849"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>Självstudie: Distribuera en förtränad bild klassificerings modell till Azure Functions med PyTorch

I den här artikeln får du lära dig hur du använder python, PyTorch och Azure Functions för att läsa in en förtränad modell för att klassificera en avbildning baserat på dess innehåll. Eftersom du utför allt arbete lokalt och skapar inga Azure-resurser i molnet, finns det ingen kostnad för att slutföra den här kursen.

> [!div class="checklist"]
> * Initiera en lokal miljö för att utveckla Azure Functions i python.
> * Importera en förtränad PyTorch Machine Learning-modell till en Function-app.
> * Bygg ett Server lös HTTP API för att klassificera en avbildning som en av 1000 ImageNet- [klasser](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a).
> * Använda API:et från en webbapp.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4 eller senare](https://www.python.org/downloads/release/python-374/). (Python 3.8. x och python 3.6. x verifieras också med Azure Functions.)
- [Azure Functions Core tools](functions-run-local.md#install-the-azure-functions-core-tools)
- En kodredigerare som t.ex. [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Krav kontroll

1. I ett terminalfönster eller kommando fönster, kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 2.7.1846 eller senare.
1. Kör `python --version` (Linux/MacOS) eller `py --version` (Windows) för att kontrol lera dina python-versions rapporter 3.7. x.

## <a name="clone-the-tutorial-repository"></a>Klona självstudiernas databas

1. I ett terminalfönster eller kommando fönster klonar du följande lagrings plats med git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. Navigera till mappen och granska dess innehåll.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *Start* är din arbetsmapp för självstudien.
    - *End* är slut resultatet och fullständig implementering för din referens.
    - *resurser* innehåller Machine Learning-modellen och hjälp program bibliotek.
    - *frontend* är en webbplats som anropar Function-appen.

## <a name="create-and-activate-a-python-virtual-environment"></a>Skapa och aktivera en virtuell python-miljö

Navigera till mappen *Start* och kör följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.venv` .


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Om python inte installerade venv-paketet på din Linux-distribution kör du följande kommando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
.venv\scripts\activate
```

---

Du kör alla efterföljande kommandon i den här aktiverade virtuella miljön. (Du avslutar den virtuella miljön genom att köra `deactivate` .)


## <a name="create-a-local-functions-project"></a>Skapa ett lokalt Functions-projekt

I Azure Functions är ett funktions projekt en behållare för en eller flera enskilda funktioner som varje svarar på en viss utlösare. Alla funktioner i ett projekt delar samma lokala och värdbaserade konfigurationer. I det här avsnittet skapar du ett funktions projekt som innehåller en enda formaterad funktion med namnet `classify` som tillhandahåller en HTTP-slutpunkt. Du lägger till mer detaljerad kod i ett senare avsnitt.

1. I mappen *Start* använder du Azure Functions Core Tools för att initiera en python Function-app:

    ```
    func init --worker-runtime python
    ```

    Efter initieringen innehåller *startmappen olika* filer för projektet, inklusive konfigurationsfiler som heter [local.settings.jspå](functions-run-local.md#local-settings-file) och [host.jspå](functions-host-json.md). Eftersom *local.settings.jspå* kan innehålla hemligheter som hämtats från Azure, undantas filen från käll kontroll som standard i *. gitignore* -filen.

    > [!TIP]
    > Eftersom ett funktions projekt är knutet till en viss körnings miljö måste alla funktioner i projektet skrivas med samma språk.

1. Lägg till en funktion i projektet med hjälp av följande kommando, där `--name` argumentet är det unika namnet för din funktion och `--template` argumentet anger funktionens utlösare. `func new` skapa en undermapp som matchar funktions namnet som innehåller en kod fil som är lämplig för projektets valda språk och en konfigurations fil med namnet *function.jspå*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Det här kommandot skapar en mapp som matchar namnet på funktionen, *klassificera*. I mappen finns två filer: *\_ \_ init \_ \_ . py*, som innehåller funktions koden och *function.jspå*, som beskriver funktionens utlösare och dess indata och utdata-bindningar. Mer information om innehållet i de här filerna finns i [Granska fil innehållet](./create-first-function-cli-python.md#optional-examine-the-file-contents) i python-snabb starten.


## <a name="run-the-function-locally"></a>Köra funktionen lokalt

1. Starta funktionen genom att starta den lokala Azure Functions körnings värden i *startmappen:*

    ```
    func start
    ```

1. När du ser `classify` slut punkten som visas i utdata går du till URL: en ```http://localhost:7071/api/classify?name=Azure``` . Meddelandet "Hej Azure!" ska visas i utdata.

1. Använd **CTRL** - **C** för att stoppa värden.


## <a name="import-the-pytorch-model-and-add-helper-code"></a>Importera PyTorch-modellen och Lägg till hjälp kod

Om du vill ändra `classify` funktionen för att klassificera en bild baserat på dess innehåll använder du en förtränad [ResNet](https://arxiv.org/abs/1512.03385) -modell. Den förtränade modellen, som kommer från [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/), klassificerar en bild i 1 av 1000 [ImageNet-klasser](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a). Du lägger sedan till viss hjälp kod och beroenden till ditt projekt.

1. I mappen *Start* kör du följande kommando för att kopiera förutsägelse koden och etiketter till mappen *klassificera* .

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. Kontrol lera att mappen *klassificera* innehåller filer med namnet *predict.py* och *labels.txt*. Om inte, kontrol lera att du körde kommandot i mappen *Start* .

1. Öppna *Start/requirements.txt* i en text redigerare och Lägg till de beroenden som krävs av hjälp koden, som bör se ut så här:

    ```txt
    azure-functions
    requests
    -f https://download.pytorch.org/whl/torch_stable.html
    torch==1.5.0+cpu
    torchvision==0.6.0+cpu
    ```

1. Spara *requirements.txt* och kör sedan följande kommando från mappen *Start* för att installera beroenden.


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

Installationen kan ta några minuter, då du kan fortsätta med att ändra funktionen i nästa avsnitt.
> [!TIP]
> >I Windows kan du stöta på felet "Det gick inte att installera paket på grund av en EnvironmentError: [errno 2] det finns ingen sådan fil eller katalog:" följt av en lång sökväg till en fil som *sharded_mutable_dense_hashtable. cpython-37. pyc*. Detta fel uppstår vanligt vis på grund av att mappsökvägen är för lång. I det här fallet anger du register nyckeln `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` till `1` för att aktivera långa sökvägar. Alternativt kan du kontrol lera var python-tolken är installerad. Om den platsen har en lång sökväg kan du försöka att installera om i en mapp med en kortare sökväg.

## <a name="update-the-function-to-run-predictions"></a>Uppdatera funktionen för att köra förutsägelser

1. Öppna *klassificera/ \_ \_ init \_ \_ . py* i en text redigerare och Lägg till följande rader efter de befintliga- `import` instruktionerna för att importera standard-JSON-biblioteket och *förutsägelse* hjälp:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Ersätt hela innehållet i `main` funktionen med följande kod:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    Den här funktionen tar emot en bild-URL i en frågesträngparametern med namnet `img` . Den anropar sedan `predict_image_from_url` från hjälp bibliotek för att ladda ned och klassificera avbildningen med hjälp av PyTorch-modellen. Funktionen returnerar sedan ett HTTP-svar med resultatet.

    > [!IMPORTANT]
    > Eftersom den här HTTP-slutpunkten anropas av en webb sida som finns på en annan domän, innehåller svaret en `Access-Control-Allow-Origin` rubrik för att uppfylla webbläsarens krav på resurs delning mellan ursprung (CORS).
    >
    > I ett produktions program ändrar `*` du till webb sidans specifika ursprung för ytterligare säkerhet.

1. Spara dina ändringar och antar att beroenden har installerats, starta den lokala funktions värden igen med `func start` . Se till att köra värden i mappen *Start* med den virtuella miljön aktive rad. Annars startar värden, men du får fel meddelanden när du anropar funktionen.

    ```
    func start
    ```

1. I en webbläsare öppnar du följande URL för att anropa funktionen med URL: en för en Bernese mountainbike hund-bild och bekräftar att den returnerade JSON klassificerar bilden som en Bernese Mountain hund.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
    ```

1. Låt värden vara igång eftersom du använder den i nästa steg.

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Kör den lokala Web App-klient delen för att testa funktionen

Om du vill testa att anropa funktions slut punkten från en annan webbapp finns det en enkel app i platsens *frontend* -mapp.

1. Öppna en ny terminal eller kommando tolk och aktivera den virtuella miljön (enligt beskrivningen tidigare under [skapa och aktivera en virtuell python-miljö](#create-and-activate-a-python-virtual-environment)).

1. Gå till lagrings platsens mapp för *klient delen* .

1. Starta en HTTP-server med python:

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    python -m http.server
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. I en webbläsare, navigerar du till `localhost:8000` och anger sedan någon av följande bild-URL: er i text rutan eller Använd URL: en för en offentligt tillgänglig bild.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. Välj **Skicka** för att anropa funktions slut punkten för att klassificera avbildningen.

    ![Skärm bild av färdig projekt](media/machine-learning-pytorch/screenshot.png)

    Om webbläsaren rapporterar ett fel när du skickar bild-URL: en, kontrollerar du den terminal där du kör Function-appen. Om du ser ett fel som "Det gick inte att hitta PIL" i modulen kan du ha startat funktionen app i *startmappen utan* att först aktivera den virtuella miljö som du skapade tidigare. Om du fortfarande ser fel kan du köra `pip install -r requirements.txt` igen med den virtuella miljön aktive rad och leta efter fel.

## <a name="clean-up-resources"></a>Rensa resurser

Eftersom hela den här självstudien körs lokalt på datorn finns det inga Azure-resurser eller-tjänster att rensa.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar och anpassar en HTTP API-slutpunkt med Azure Functions för att klassificera bilder med hjälp av en PyTorch modell. Du har också lärt dig hur du anropar API: et från en webbapp. Du kan använda metoderna i den här självstudien för att bygga ut API: er för all komplexitet, samtidigt som du kör på den serverbaserade beräknings modellen som tillhandahålls av Azure Functions.

Se även:

- [Distribuera funktionen till Azure med Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Guide för Azure Functions python-utvecklare](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [Distribuera funktionen till Azure Functions med hjälp av Azure CLI-guiden](./functions-run-local.md#publish)
