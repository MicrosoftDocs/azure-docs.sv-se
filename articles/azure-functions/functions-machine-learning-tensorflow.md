---
title: Använd python och TensorFlow för Machine Learning i Azure
description: Använd python, TensorFlow och Azure Functions med en maskin inlärnings modell för att klassificera en bild baserat på dess innehåll.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc, devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: ca9ce27583168dfee1a597fce559afad38a3a8c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994612"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Självstudie: använda Machine Learning-modeller i Azure Functions med python och TensorFlow

I den här artikeln får du lära dig hur du använder python, TensorFlow och Azure Functions med en maskin inlärnings modell för att klassificera en bild baserat på dess innehåll. Eftersom du utför allt arbete lokalt och skapar inga Azure-resurser i molnet, finns det ingen kostnad för att slutföra den här kursen.

> [!div class="checklist"]
> * Initiera en lokal miljö för att utveckla Azure Functions i python.
> * Importera en anpassad TensorFlow Machine Learning-modell till en Function-app.
> * Bygg en server lös HTTP API för att klassificera en avbildning som innehåller en hund eller en katt.
> * Använda API:et från en webbapp.

## <a name="prerequisites"></a>Förutsättningar 

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python-3.7.4](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 och python 3.6. x verifieras med Azure Functions. Python 3,8 och senare versioner stöds inte ännu.)
- [Azure Functions Core tools](functions-run-local.md#install-the-azure-functions-core-tools)
- En kodredigerare som t.ex. [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Krav kontroll

1. I ett terminalfönster eller kommando fönster, kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 2.7.1846 eller senare.
1. Kör `python --version` (Linux/MacOS) eller `py --version` (Windows) för att kontrol lera dina python-versions rapporter 3.7. x.

## <a name="clone-the-tutorial-repository"></a>Klona självstudiernas databas

1. I ett terminalfönster eller kommando fönster klonar du följande lagrings plats med git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Navigera till mappen och granska dess innehåll.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *Start* är din arbetsmapp för självstudien.
    - *End* är slut resultatet och fullständig implementering för din referens.
    - *resurser* innehåller Machine Learning-modellen och hjälp program bibliotek.
    - *frontend* är en webbplats som anropar Function-appen.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Skapa och aktivera en virtuell python-miljö

Navigera till mappen *Start* och kör följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.venv` . Se till att använda python 3,7, som stöds av Azure Functions.


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Om python inte installerade venv-paketet på din Linux-distribution kör du följande kommando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -3.7 -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -3.7 -m venv .venv
```

```cmd
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


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>Importera TensorFlow-modellen och Lägg till hjälp kod

Om du vill ändra `classify` funktionen för att klassificera en bild baserat på dess innehåll använder du en fördefinierad TensorFlow-modell som har tränats med och exporter ATS från Azure Custom vision service. Modellen, som finns i mappen *resurser* i exemplet som du klonade tidigare, klassificerar en avbildning baserat på om den innehåller en hund eller en katt. Du lägger sedan till viss hjälp kod och beroenden till ditt projekt.

Om du vill bygga en egen modell med den kostnads fria nivån av Custom Vision Service följer du anvisningarna i [exempel projektets lagrings plats](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

> [!TIP]
> Om du vill vara värd för din TensorFlow-modell oberoende av Function-appen kan du i stället montera en fil resurs som innehåller din modell till din Linux Function-app. Mer information finns i [montera en fil resurs till en python Function-app med hjälp av Azure CLI](./scripts/functions-cli-mount-files-storage-linux.md).

1. I mappen *Start* kör du följande kommando för att kopiera modell filen till mappen *klassificera* . Se till att inkludera `\*` i kommandot. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. Kontrol lera att mappen *klassificera* innehåller filer med namnet *Model. PB* och *labels.txt*. Om inte, kontrol lera att du körde kommandot i mappen *Start* .

1. I mappen *Start* kör du följande kommando för att kopiera en fil med hjälp kod till mappen *klassificera* :

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. Kontrol lera att mappen *klassificera* nu innehåller en fil med namnet *predict.py*.

1. Öppna *Start/requirements.txt* i en text redigerare och Lägg till följande beroenden som krävs av hjälp koden:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. Spara *requirements.txt*.

1. Installera beroendena genom att köra följande kommando i mappen *Start* . Installationen kan ta några minuter, då du kan fortsätta med att ändra funktionen i nästa avsnitt.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    I Windows kan du stöta på felet "Det gick inte att installera paket på grund av en EnvironmentError: [errno 2] det finns ingen sådan fil eller katalog:" följt av en lång sökväg till en fil som *sharded_mutable_dense_hashtable. cpython-37. pyc*. Detta fel uppstår vanligt vis på grund av att mappsökvägen är för lång. I det här fallet anger du register nyckeln `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` till `1` för att aktivera långa sökvägar. Alternativt kan du kontrol lera var python-tolken är installerad. Om den platsen har en lång sökväg kan du försöka att installera om i en mapp med en kortare sökväg.

> [!TIP]
> När du anropar på *predict.py* för att göra dess första förutsägelse, kommer en funktion som heter `_initialize` läsa in TensorFlow-modellen från disken och cachelagra den i globala variabler. Denna cachelagring påskyndar efterföljande förutsägelser. Mer information om hur du använder globala variabler finns i [Azure Functions python Developer Guide](functions-reference-python.md#global-variables).

## <a name="update-the-function-to-run-predictions"></a>Uppdatera funktionen för att köra förutsägelser

1. Öppna *klassificera/ \_ \_ init \_ \_ . py* i en text redigerare och Lägg till följande rader efter de befintliga- `import` instruktionerna för att importera standard-JSON-biblioteket och *förutsägelse* hjälp:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Ersätt hela innehållet i `main` funktionen med följande kod:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Den här funktionen tar emot en bild-URL i en frågesträngparametern med namnet `img` . Den anropar sedan `predict_image_from_url` från hjälp bibliotek för att ladda ned och klassificera avbildningen med hjälp av TensorFlow-modellen. Funktionen returnerar sedan ett HTTP-svar med resultatet. 

    > [!IMPORTANT]
    > Eftersom den här HTTP-slutpunkten anropas av en webb sida som finns på en annan domän, innehåller svaret en `Access-Control-Allow-Origin` rubrik för att uppfylla webbläsarens krav på resurs delning mellan ursprung (CORS).
    >
    > I ett produktions program ändrar `*` du till webb sidans specifika ursprung för ytterligare säkerhet.

1. Spara dina ändringar och antar att beroenden har installerats, starta den lokala funktions värden igen med `func start` . Se till att köra värden i mappen *Start* med den virtuella miljön aktive rad. Annars startar värden, men du får fel meddelanden när du anropar funktionen.

    ```
    func start
    ```

1. I en webbläsare öppnar du följande URL för att anropa funktionen med URL: en för en Cat-bild och bekräftar att den returnerade JSON klassificerar avbildningen som en katt.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
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

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. Välj **Skicka** för att anropa funktions slut punkten för att klassificera avbildningen.

    ![Skärm bild av färdig projekt](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Om webbläsaren rapporterar ett fel när du skickar bild-URL: en, kontrollerar du den terminal där du kör Function-appen. Om du ser ett fel som "Det gick inte att hitta PIL" i modulen kan du ha startat funktionen app i *startmappen utan* att först aktivera den virtuella miljö som du skapade tidigare. Om du fortfarande ser fel kan du köra `pip install -r requirements.txt` igen med den virtuella miljön aktive rad och leta efter fel.

> [!NOTE]
> Modellen klassificerar alltid innehållet i bilden som en katt eller en hund, oavsett om avbildningen innehåller antingen, som standard. Bilder av Tigers och Panthers, till exempel vanligt vis klassificeras som katt, men bilder av elefanter, Carrots eller luftplaner klassificeras som hund.

## <a name="clean-up-resources"></a>Rensa resurser

Eftersom hela den här självstudien körs lokalt på datorn finns det inga Azure-resurser eller-tjänster att rensa.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar och anpassar en HTTP API-slutpunkt med Azure Functions för att klassificera bilder med hjälp av en TensorFlow modell. Du har också lärt dig hur du anropar API: et från en webbapp. Du kan använda metoderna i den här självstudien för att bygga ut API: er för all komplexitet, samtidigt som du kör på den serverbaserade beräknings modellen som tillhandahålls av Azure Functions.

> [!div class="nextstepaction"]
> [Distribuera funktionen till Azure Functions med hjälp av Azure CLI-guiden](./functions-run-local.md#publish)

Se även:

- [Distribuera funktionen till Azure med Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Guide för Azure Functions python-utvecklare](./functions-reference-python.md)
- [Montera en fil resurs i en python Function-app med hjälp av Azure CLI](./scripts/functions-cli-mount-files-storage-linux.md)
