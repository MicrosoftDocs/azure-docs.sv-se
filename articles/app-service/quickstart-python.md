---
title: 'Snabb start: skapa en python-app'
description: Kom igång med Azure App Service genom att distribuera din första python-app till en Linux-behållare i App Service.
ms.topic: quickstart
ms.date: 11/10/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python, devx-track-azurecli
zone_pivot_groups: python-frameworks-01
adobe-target: true
adobe-target-activity: DocsExp–377467–A/B– Quickstarts/Python App–12.11
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-python-1
ms.openlocfilehash: 36c1b112127fadd5149c62a92ca469322db65edd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592361"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux"></a>Snabb start: skapa en python-app med Azure App Service på Linux

I den här snabb starten distribuerar du en python-webbapp till [app service på Linux](overview.md#app-service-on-linux), Azures mycket skalbara, självkorrigerande webb värd tjänst. Du använder det lokala [Azures kommando rads gränssnitt (CLI)](/cli/azure/install-azure-cli) på en Mac-, Linux-eller Windows-dator för att distribuera ett prov med antingen flaska eller django-ramverk. Webbappen som du konfigurerar använder en kostnads fri App Service nivå, så du debiteras inte några kostnader i samband med den här artikeln.

> [!TIP]
> Om du föredrar att använda Visual Studio Code i stället följer du vår **[Visual Studio code App Service snabb start](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)**.

## <a name="set-up-your-initial-environment"></a>Konfigurera din inledande miljö

1. Ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Installera <a href="https://www.python.org/downloads/" target="_blank">Python 3,6 eller högre</a>.
1. Installera <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> -2.0.80 eller högre, med vilken du kör kommandon i alla gränssnitt för att etablera och konfigurera Azure-resurser.

Öppna ett terminalfönster och kontrol lera att python-versionen är 3,6 eller högre:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Kontrol lera att din Azure CLI-version är 2.0.80 eller högre:

```azurecli
az --version
```

Logga sedan in på Azure via CLI:

```azurecli
az login
```

Det här kommandot öppnar en webbläsare för att samla in dina autentiseringsuppgifter. När kommandot har slutförts visas JSON-utdata som innehåller information om dina prenumerationer.

När du har loggat in kan du köra Azure-kommandon med Azure CLI för att arbeta med resurser i din prenumeration.

Har du problem? [Berätta för oss](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clone-the-sample"></a>Klona exemplet

Klona exempel lagrings platsen med följande kommando och navigera till mappen exempel. ([Installera git](https://git-scm.com/downloads) om du inte redan har git.)

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```
::: zone-end

Exemplet innehåller en Framework-speciell kod som Azure App Service känner igen när appen startas. Mer information finns i [behållare start process](configure-language-python.md#container-startup-process).

Har du problem? [Berätta för oss](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="run-the-sample"></a>Kör exemplet

::: zone pivot="python-framework-flask"
1. Navigera till i mappen *python-dok-Hello-World* :

    ```terminal
    cd python-docs-hello-world
    ```

1. Skapa en virtuell miljö och installera beroenden:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Om du stöter på "[errno 2] ingen sådan fil eller katalog:" requirements.txt ". se till att du är i mappen *python-dok-Hello-World* .

1. Kör utvecklings servern.

    ```terminal  
    flask run
    ```
    
    Som standard förutsätter servern att appens inmatnings modul är i *app.py*, som används i exemplet.

    Om du använder ett annat Modulnamn anger du `FLASK_APP` miljövariabeln till det namnet.

    Om du får fel meddelandet "Det gick inte att hitta något kolv program. Du angav inte miljövariabeln FLASK_APP, och det gick inte att hitta en wsgi.py-eller app.py-modul i den aktuella katalogen. kontrol lera att du är i `python-docs-hello-world` mappen som innehåller exemplet.

1. Öppna en webbläsare och gå till exempel appen på `http://localhost:5000/` . Appen visar meddelandet **Hej, World!**.

    ![Kör en exempel-python-app lokalt](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Tryck på **CTRL** + **C** i terminalfönstret för att avsluta utvecklings servern.
::: zone-end

::: zone pivot="python-framework-django"
1. Navigera till mappen *python-dok-Hello-django* :

    ```terminal
    cd python-docs-hello-django
    ```

1. Skapa en virtuell miljö och installera beroenden:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Om du stöter på "[errno 2] ingen sådan fil eller katalog:" requirements.txt ". se till att du är i mappen *python-dokument-Hello-django* .
    
1. Kör utvecklings servern.

    ```terminal
    python manage.py runserver
    ```

1. Öppna en webbläsare och gå till exempel appen på `http://localhost:8000/` . Appen visar meddelandet **Hej, World!**.

    ![Kör en exempel-python-app lokalt](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Tryck på **CTRL** + **C** i terminalfönstret för att avsluta utvecklings servern.
::: zone-end

Har du problem? [Berätta för oss](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="deploy-the-sample"></a>Distribuera exemplet

Distribuera koden i din lokala mapp (*python-dok-Hello-World*) med hjälp av `az webapp up` kommandot:

```azurecli
az webapp up --sku B1 --name <app-name>
```

- Om `az` kommandot inte känns igen kontrollerar du att Azure CLI är installerat enligt beskrivningen i [Konfigurera din första miljö](#set-up-your-initial-environment).
- Om `webapp` kommandot inte känns igen, eftersom din Azure CLI-version är 2.0.80 eller högre. Om inte, [installerar du den senaste versionen](/cli/azure/install-azure-cli).
- Ersätt `<app_name>` med ett namn som är unikt för alla Azure (*giltiga tecken är `a-z` , `0-9` och `-`*). Ett utmärkt mönster är att använda en kombination av företagets namn och en app-ID.
- `--sku B1`Argumentet skapar webbappen på den grundläggande pris nivån, vilket innebär en liten Tim kostnad. Utelämna det här argumentet om du vill använda en snabbare Premium-nivå.
- Du kan också inkludera argumentet `--location <location-name>` där `<location_name>` är en tillgänglig Azure-region. Du kan hämta en lista över tillåtna regioner för ditt Azure-konto genom att köra [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) kommandot.
- Om du ser felet "Det gick inte att automatiskt identifiera körnings stacken för appen" måste du kontrol lera att du kör kommandot i mappen *python-dok-Hello-World* (kolv) eller *python-django-Hello-* folder (django) som innehåller *requirements.txt* -filen. (Mer information finns i [Felsöka problem med automatisk identifiering med AZ webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub).)

Det kan ta några minuter att slutföra kommandot. Under körningen innehåller den meddelanden om att skapa resurs gruppen, App Service plan och värd appen, Konfigurera loggning och sedan utföra ZIP-distribution. Det ger meddelandet "du kan starta appen på http:// &lt; App-Name &gt; . azurewebsites.net", som är appens URL på Azure.

![Exempel på utdata från kommandot AZ webapp up](./media/quickstart-python/az-webapp-up-output.png)

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/FlaskCLIQuickstartHelp).

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till det distribuerade programmet i webbläsaren på webb adressen `http://<app-name>.azurewebsites.net` . Det kan ta en minut eller två innan appen startas, så om du ser en standard-app-sida väntar du en minut och uppdaterar webbläsaren.

Python-exempel koden kör en Linux-behållare i App Service med hjälp av en inbyggd avbildning.

![Köra en python-app i Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Grattis!** Du har distribuerat din python-app till App Service.

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="redeploy-updates"></a>Distribuera om uppdateringar

I det här avsnittet gör du en liten kod ändring och distribuerar sedan om koden till Azure. Kod ändringen innehåller en `print` instruktion för att generera logga utdata som du arbetar med i nästa avsnitt.

::: zone pivot="python-framework-flask"
Öppna *app.py* i en redigerare och uppdatera `hello` funktionen så att den matchar följande kod. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
Öppna *Hej/vyer. py* i en redigerare och uppdatera `hello` funktionen så att den matchar följande kod.

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
Spara ändringarna och distribuera sedan om appen med `az webapp up` kommandot igen:

```azurecli
az webapp up
```

Det här kommandot använder värden som cachelagras lokalt i *Azure/config* -filen, inklusive app-namn, resurs grupp och App Service plan.

När distributionen är klar går du tillbaka till webbläsarfönstret öppna till `http://<app-name>.azurewebsites.net` . Uppdatera sidan, som ska visa det ändrade meddelandet:

![Köra en uppdaterad exempel-python-app i Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/FlaskCLIQuickstartHelp).

> [!TIP]
> Visual Studio Code tillhandahåller kraftfulla tillägg för python och Azure App Service, vilket fören klar processen att distribuera python-webbappar till App Service. Mer information finns i [distribuera python-appar till App Service från Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Strömningsloggar

Du kan komma åt konsol loggarna som genereras inifrån appen och den behållare där den körs. Loggar innehåller alla utdata som genereras med hjälp av `print` instruktioner.

Om du vill strömma loggar kör du kommandot [AZ webapp log pilslut](/cli/azure/webapp/log#az_webapp_log_tail) :

```azurecli
az webapp log tail
```

Du kan också ta med `--logs` parametern med `az webapp up` kommandot för att automatiskt öppna logg strömmen vid distributionen.

Uppdatera appen i webbläsaren för att generera konsol loggar som innehåller meddelanden som beskriver HTTP-begäranden till appen. Om inga utdata visas omedelbart, försök igen om 30 sekunder.

Du kan även granska loggfilerna från din webbläsare via `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Tryck på **CTRL** + **C** i terminalen om du vill stoppa logg strömningen när som helst.

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> för att hantera den app som du skapade. Sök efter och välj **app Services**.

![Navigera till App Services i Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Välj namnet på din Azure-App.

![Navigera till python-appen i App Services i Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

När du väljer appen öppnas sidan **Översikt** där du kan utföra grundläggande hanterings åtgärder som att bläddra, stoppa, starta, starta om och ta bort.

![Hantera din python-app på sidan Översikt i Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

App Service-menyn innehåller olika sidor för att konfigurera din app.

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Resurs gruppen har ett namn som "appsvc_rg_Linux_CentralUS" beroende på din plats. Om du håller webbappen igång kommer du att få några pågående kostnader (se [App Service priser](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Om du inte tror att du behöver dessa resurser i framtiden tar du bort resurs gruppen genom att köra följande kommando:

```azurecli
az group delete --no-wait
```

Kommandot använder resurs grupps namnet cachelagrat i *. Azure/config-* filen.

`--no-wait`Argumentet tillåter att kommandot returneras innan åtgärden har slutförts.

Har du problem? [Berätta för oss](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: python (django)-webbapp med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurera python-app](configure-language-python.md)

> [!div class="nextstepaction"]
> [Lägga till användar inloggning till en python-webbapp](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Självstudie: köra python-app i en anpassad behållare](tutorial-custom-container.md)
