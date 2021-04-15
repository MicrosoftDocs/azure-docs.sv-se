---
title: 'Snabbstart: Skapa en Python-app'
description: Kom igång med Azure App Service genom att distribuera din första Python-app till en Linux-container i App Service.
ms.topic: quickstart
ms.date: 11/10/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
zone_pivot_groups: python-frameworks-01
adobe-target: true
adobe-target-activity: DocsExp–393165–A/B–Docs/PythonQuickstart–CLIvsPortal–FY21Q4
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-python-portal
ms.openlocfilehash: d8af7a9744fe136f482fc007dbd0e51a680df932
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379493"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux"></a>Snabbstart: Skapa en Python-app med Azure App Service på Linux

I den här snabbstarten distribuerar du en [Python-webbapp till App Service på Linux](overview.md#app-service-on-linux), Azures mycket skalbara och självkorrigeringsbara webbvärdtjänst. Du använder det lokala Azure-kommandoradsgränssnittet (CLI) på en Mac-, Linux- eller [Windows-dator](/cli/azure/install-azure-cli) för att distribuera ett exempel med antingen Flask- eller Django-ramverken. Den webbapp som du konfigurerar använder en grundläggande App Service som medför en liten kostnad i din Azure-prenumeration.

## <a name="set-up-your-initial-environment"></a>Konfigurera din första miljö

1. Ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
1. Installera <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 eller senare.</a>
1. Installera <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 eller senare, med vilken du kör kommandon i alla gränssnitt för att etablera och konfigurera Azure-resurser.

Öppna ett terminalfönster och kontrollera att din Python-version är 3.6 eller senare:

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

Kontrollera att din Azure CLI-version är 2.0.80 eller senare:

```azurecli
az --version
```

Logga sedan in på Azure via CLI:

```azurecli
az login
```

Det här kommandot öppnar en webbläsare för att samla in dina autentiseringsuppgifter. När kommandot har avslutats visas JSON-utdata som innehåller information om dina prenumerationer.

När du har loggat in kan du köra Azure-kommandon med Azure CLI för att arbeta med resurser i din prenumeration.

Har du problem? [Berätta för oss.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>Klona exemplet

Klona exempeldatabasen med följande kommando och navigera till exempelmappen. (Installera[git](https://git-scm.com/downloads) om du inte redan har git.)

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

Exemplet innehåller ramverksspecifik kod som Azure App Service identifierar när appen startas. Mer information finns i [Startprocessen för containrar.](configure-language-python.md#container-startup-process)

Har du problem? [Berätta för oss.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>Kör exemplet

::: zone pivot="python-framework-flask"
1. Navigera till i *mappen python-docs-hello-world:*

    ```terminal
    cd python-docs-hello-world
    ```

1. Skapa en virtuell miljö och installera beroenden:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Om du stöter på "[Errno 2] Ingen sådan fil eller katalog: "requirements.txt"." kontrollerar du att du är i *mappen python-docs-hello-world.*

1. Kör utvecklingsservern.

    ```terminal  
    flask run
    ```
    
    Som standard förutsätter servern att appens inmatningsmodul finns *i app.py*, som används i exemplet.

    Om du använder ett annat modulnamn anger du `FLASK_APP` miljövariabeln till det namnet.

    Om du stöter på felet "Det gick inte att hitta ett Flask-program. Du har inte ange miljövariabeln "FLASK_APP" och modulen "wsgi.py" eller "app.py" hittades inte i den aktuella katalogen." kontrollerar du att du är i mappen som innehåller `python-docs-hello-world` exemplet.

1. Öppna en webbläsare och gå till exempelappen på `http://localhost:5000/` . Appen visar meddelandet **Hello, World!**.

    ![Köra en Python-exempelapp lokalt](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Tryck på Ctrl C i **terminalfönstret** + **för** att avsluta utvecklingsservern.
::: zone-end

::: zone pivot="python-framework-django"
1. Navigera till mappen *python-docs-hello-django:*

    ```terminal
    cd python-docs-hello-django
    ```

1. Skapa en virtuell miljö och installera beroenden:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Om du stöter på "[Errno 2] Ingen sådan fil eller katalog: "requirements.txt"." kontrollerar du att du är i *mappen python-docs-hello-django.*
    
1. Kör utvecklingsservern.

    ```terminal
    python manage.py runserver
    ```

1. Öppna en webbläsare och gå till exempelappen på `http://localhost:8000/` . Appen visar meddelandet **Hello, World!**.

    ![Köra en Python-exempelapp lokalt](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Tryck på Ctrl C i **terminalfönstret** + **för** att avsluta utvecklingsservern.
::: zone-end

Har du problem? [Berätta för oss.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>Distribuera exemplet

Distribuera koden i den lokala mappen (*python-docs-hello-world*) med hjälp av `az webapp up` kommandot :

```azurecli
az webapp up --sku B1 --name <app-name>
```

- Om kommandot inte känns igen måste du ha Azure CLI installerat enligt `az` beskrivningen i [Konfigurera din första miljö.](#set-up-your-initial-environment)
- Om kommandot `webapp` inte känns igen, eftersom din Azure CLI-version är 2.0.80 eller senare. Om inte installerar [du den senaste versionen](/cli/azure/install-azure-cli).
- Ersätt `<app_name>` med ett namn som är unikt för hela Azure ( giltiga tecken är ,*`a-z` `0-9` och `-`*). Ett bra mönster är att använda en kombination av företagets namn och en appidentifierare.
- Argumentet `--sku B1` skapar webbappen på prisnivån Basic, vilket medför en liten kostnad per timme. Utelämna det här argumentet för att använda en snabbare premiumnivå.
- Du kan också inkludera argumentet där `--location <location-name>` är `<location_name>` en tillgänglig Azure-region. Du kan hämta en lista över tillåtna regioner för ditt Azure-konto genom att köra [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) kommandot .
- Om du ser felet "Det gick inte att automatiskt identifiera körningsstacken för din app" kontrollerar du att du kör kommandot i mappen *python-docs-hello-world* (Flask) eller mappen *python-docs-hello-django (Django)* som innehåller *requirements.txt-filen.* (Se [Felsöka problem med automatisk identifiering med az webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub).)

Det kan ta några minuter att slutföra kommandot. När du kör får du meddelanden om att skapa resursgruppen, App Service plan värdappen, konfigurera loggning och sedan utföra ZIP-distribution. Sedan visas meddelandet "Du kan starta appen på http:// &lt; appnamn .azurewebsites.net", som är &gt; appens URL på Azure.

![Exempel på utdata från kommandot az webapp up](./media/quickstart-python/az-webapp-up-output.png)

Har du problem? Läs först i [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss.](https://aka.ms/FlaskCLIQuickstartHelp)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till det distribuerade programmet i webbläsaren på URL:en `http://<app-name>.azurewebsites.net` . Det kan ta någon minut innan appen startar, så om du ser en standardappsida väntar du en minut och uppdaterar webbläsaren.

Python-exempelkoden kör en Linux-container i App Service med hjälp av en inbyggd avbildning.

![Köra en Python-exempelapp i Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Grattis!** Du har distribuerat Python-appen till App Service.

Har du problem? Läs först i [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="redeploy-updates"></a>Distribuera om uppdateringar

I det här avsnittet gör du en liten kodändring och distribuerar sedan om koden till Azure. Kodändringen innehåller en `print` -instruktion för att generera loggningsutdata som du arbetar med i nästa avsnitt.

::: zone pivot="python-framework-flask"
Öppna *app.py* i ett redigeringsprogram och uppdatera `hello` funktionen så att den matchar följande kod. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
Öppna *hello/views.py* i ett redigeringsprogram och uppdatera `hello` funktionen så att den matchar följande kod.

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
Spara ändringarna och distribuera sedan appen igen med `az webapp up` kommandot igen:

```azurecli
az webapp up
```

Det här kommandot använder värden som cachelagras lokalt i *.azure/config-filen,* inklusive appnamn, resursgrupp och App Service plan.

När distributionen är klar växlar du tillbaka till webbläsarfönstret och `http://<app-name>.azurewebsites.net` öppnar . Uppdatera sidan, som ska visa det ändrade meddelandet:

![Köra en uppdaterad Python-exempelapp i Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

Har du problem? Gå först till [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss](https://aka.ms/FlaskCLIQuickstartHelp).

> [!TIP]
> Visual Studio Code innehåller kraftfulla tillägg för Python och Azure App Service, vilket förenklar distributionen av Python-webbappar till App Service. Mer information finns i Distribuera [Python-appar till App Service från Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Strömningsloggar

Du kan komma åt konsolloggarna som genereras inifrån appen och containern som den körs i. Loggarna innehåller alla utdata som genereras med hjälp av `print` instruktioner.

Om du vill strömma loggar kör du [kommandot az webapp log tail:](/cli/azure/webapp/log#az_webapp_log_tail)

```azurecli
az webapp log tail
```

Du kan också inkludera `--logs` parametern med kommandot `az webapp up` för att automatiskt öppna loggströmmen vid distributionen.

Uppdatera appen i webbläsaren för att generera konsolloggar, som innehåller meddelanden som beskriver HTTP-begäranden till appen. Om inga utdata visas omedelbart kan du försöka igen om 30 sekunder.

Du kan även granska loggfilerna från din webbläsare via `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Om du vill stoppa loggströmningen när som helst trycker du **på** + **Ctrl C** i terminalen.

Har du problem? Gå först till [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> för att hantera den app som du skapade. Sök efter och välj **App Services**.

![Gå till App Services i Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Välj namnet på din Azure-app.

![Gå till Python-appen i App Services i Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

När du väljer appen öppnas **sidan Översikt** där du kan utföra grundläggande hanteringsuppgifter som att bläddra, stoppa, starta, starta om och ta bort.

![Hantera Python-appen på översiktssidan i Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Menyn App Service innehåller olika sidor för att konfigurera din app.

Har du problem? Läs först i [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Resursgruppen har ett namn som "appsvc_rg_Linux_CentralUS" beroende på din plats. Om du fortsätter att köra webbappen medför det vissa löpande kostnader (se [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Om du inte förväntar dig att behöva dessa resurser i framtiden tar du bort resursgruppen genom att köra följande kommando:

```azurecli
az group delete --no-wait
```

Kommandot använder resursgruppens namn som cachelagras i *.azure/config-filen.*

Argumentet `--no-wait` gör att kommandot kan returnera innan åtgärden har slutförts.

Har du problem? [Berätta för oss.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Python-webbapp (Django) med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurera Python-app](configure-language-python.md)

> [!div class="nextstepaction"]
> [Lägga till användar inloggning till en Python-webbapp](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Självstudie: Köra Python-appen i en anpassad container](tutorial-custom-container.md)
