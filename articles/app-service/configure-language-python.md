---
title: Konfigurera Linux python-appar
description: Lär dig hur du konfigurerar python-behållaren där webbappar körs, med hjälp av både Azure Portal och Azure CLI.
ms.topic: quickstart
ms.date: 03/16/2021
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 094755ed6c018b3ac82d6f62a43f17e2536bbd9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953518"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Konfigurera en Linux python-app för Azure App Service

Den här artikeln beskriver hur [Azure App Service](overview.md) kör python-appar, hur du kan migrera befintliga appar till Azure och hur du kan anpassa beteendet för App Service när det behövs. Python-appar måste distribueras med alla nödvändiga [pip](https://pypi.org/project/pip/) -moduler.

App Service distributions motorn aktiverar automatiskt en virtuell miljö och körs `pip install -r requirements.txt` åt dig när du distribuerar en [git-lagringsplats](deploy-local-git.md)eller ett zip- [paket](deploy-zip.md).

Den här guiden innehåller viktiga begrepp och instruktioner för python-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service ska du först följa själv studie kursen [python snabb start](quickstart-python.md) och [python med postgresql](tutorial-python-postgresql-app.md).

Du kan använda antingen [Azure Portal](https://portal.azure.com) eller Azure CLI för konfiguration:

- **Azure Portal** använder du   >  **konfigurations** sidan för appens inställningar enligt beskrivningen i [Konfigurera en app service-app i Azure Portal](configure-common.md).

- **Azure CLI**: du har två alternativ.

    - Kör kommandon i [Azure Cloud Shell](../cloud-shell/overview.md).
    - Kör kommandon lokalt genom att installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli)och logga sedan in på Azure med [AZ-inloggning](/cli/azure/reference-index#az-login).
    
> [!NOTE]
> Linux är för närvarande det rekommenderade alternativet för att köra python-appar i App Service. Information om Windows-alternativet finns i [python på Windows-smak för App Service](/visualstudio/python/managing-python-on-azure-app-service).

## <a name="configure-python-version"></a>Konfigurera python-version

- **Azure Portal**: Använd fliken **allmänna inställningar** på sidan **konfiguration** enligt beskrivningen i [Konfigurera allmänna inställningar](configure-common.md#configure-general-settings) för Linux-behållare.

- **Azure CLI**:

    -  Visa den aktuella python-versionen med [AZ webapp config show](/cli/azure/webapp/config#az_webapp_config_show):
    
        ```azurecli
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Ersätt `<resource-group-name>` och `<app-name>` med de namn som är lämpliga för din webbapp.
    
    - Ange python-versionen med [AZ webapp config set](/cli/azure/webapp/config#az_webapp_config_set)
        
        ```azurecli
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - Visa alla python-versioner som stöds i Azure App Service med [AZ webapp List-Runtimes](/cli/azure/webapp#az_webapp_list_runtimes):
    
        ```azurecli
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
Du kan köra en version av Python genom att skapa en egen containeravbildning i stället. Mer information finns i [Använda anpassad Docker-avbildning](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>Anpassa Bygg automatisering

App Service Bygg systemet, som kallas Oryx, utför följande steg när du distribuerar appen med git-eller zip-paket:

1. Kör ett anpassat för skapande skript om det anges med `PRE_BUILD_COMMAND` inställningen. (Skriptet kan köra andra python och Node.js skript, pip-och NPM-kommandon och Node-baserade verktyg som t. ex. garn, till exempel `yarn install` och `yarn build` .)

1. Kör `pip install -r requirements.txt`. *requirements.txt* -filen måste finnas i projektets rotmapp. Annars rapporterar build-processen felet: "Det gick inte att hitta setup.py eller requirements.txt; Kör inte pip-installation. "

1. Om *Manage.py* finns i roten för lagrings platsen (som anger en django-app) kör du *Manage.py collectstatic*. Men om `DISABLE_COLLECTSTATIC` inställningen är `true` hoppar detta steg över.

1. Kör anpassat efter build-skript om det anges med `POST_BUILD_COMMAND` inställningen. (Skriptet kan köra andra python-och Node.js skript, pip-och NPM-kommandon och Node-baserade verktyg.)

Som standard `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` `DISABLE_COLLECTSTATIC` är inställningarna, och tomma. 

- Om du vill inaktivera körning av collectstatic när du skapar django-appar anger `DISABLE_COLLECTSTATIC` du True för inställningen.

- För att köra kommandon för att skapa för bygge, ange `PRE_BUILD_COMMAND` antingen ett kommando, till exempel `echo Pre-build command` eller en sökväg till en skript fil i förhållande till projekt roten, till exempel `scripts/prebuild.sh` . Alla kommandon måste använda relativa sökvägar för rotmappen för projektet.

- Om du vill köra kommandona efter build anger du `POST_BUILD_COMMAND` att inställningen ska innehålla antingen ett kommando, till exempel `echo Post-build command` eller en sökväg till en skript fil i förhållande till projekt roten, till exempel `scripts/postbuild.sh` . Alla kommandon måste använda relativa sökvägar för rotmappen för projektet.

Ytterligare inställningar som anpassar Bygg automatisering finns i [Oryx-konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md). 

För att få åtkomst till bygg-och distributions loggarna, se [åtkomst till distributions loggar](#access-deployment-logs).

Mer information om hur App Service kör och skapar python-appar i Linux finns i [hur Oryx identifierar och skapar python-appar](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md).

> [!NOTE]
> `PRE_BUILD_SCRIPT_PATH`Inställningarna och `POST_BUILD_SCRIPT_PATH` är identiska med `PRE_BUILD_COMMAND` och `POST_BUILD_COMMAND` och stöds i äldre syfte.
> 
> En inställning `SCM_DO_BUILD_DURING_DEPLOYMENT` som heter, om den innehåller `true` eller 1, utlöser en Oryx-version under distributionen. Inställningen är sann vid distribution med git, Azure CLI `az webapp up` -kommandot och Visual Studio Code.

> [!NOTE]
> Använd alltid relativa sökvägar i alla pre-och post-build-skript eftersom den Bygg behållare i vilken Oryx körs skiljer sig från kör tids behållaren som appen körs i. Förlita dig aldrig på den exakta placeringen av projektmappen i behållaren (till exempel att den är placerad under *plats/wwwroot*).

## <a name="migrate-existing-applications-to-azure"></a>Migrera befintliga program till Azure

Befintliga webb program kan omdistribueras till Azure på följande sätt:

1. **Käll databas**: Behåll din käll kod i en lämplig databas som GitHub, vilket gör att du kan ställa in kontinuerlig distribution senare i den här processen.
    1. Din *requirements.txt* -fil måste finnas i roten på lagrings platsen för App Service för att automatiskt installera de nödvändiga paketen.    

1. **Databas**: om du har en app som är beroende av en databas bör du även etablera de nödvändiga resurserna på Azure. Se [självstudie: Distribuera en django-webbapp med postgresql – skapa en databas](tutorial-python-postgresql-app.md#3-create-postgres-database-in-azure) för ett exempel.

1. **App Service-resurser**: skapa en resurs grupp, App Service Plan och App Service webbapp som värd för ditt program. Du kan enkelt göra detta genom att göra en första distribution av koden via Azure CLI `az webapp up` -kommandot, som visas i [Självstudier: Distribuera en django-webbapp med postgresql – distribuera koden](tutorial-python-postgresql-app.md#4-deploy-the-code-to-azure-app-service). Ersätt namnen på resurs gruppen, App Service planen och att webbappen passar bättre för ditt program.

1. **Miljövariabler**: om programmet kräver miljövariabler bör du skapa motsvarande [app service program inställningar](configure-common.md#configure-app-settings). Dessa App Service inställningar visas i koden som miljövariabler enligt beskrivningen i [Access Environment-variabler](#access-app-settings-as-environment-variables).
    - Databas anslutningar kan till exempel ofta hanteras via sådana inställningar som visas i [Självstudier: Distribuera en django-webbapp med postgresql-konfigurera variabler för att ansluta databasen](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database).
    - Se [produktions inställningar för django-appar](#production-settings-for-django-apps) för vissa inställningar för typiska django-appar.

1. **App-start**: Läs avsnittet, [Start processen för behållaren](#container-startup-process) senare i den här artikeln för att förstå hur App Service försöker köra appen. App Service använder webb servern Gunicorn som standard, som måste kunna hitta app-objektet eller *wsgi.py* -mappen. Om det behövs kan du [Anpassa Start kommandot](#customize-startup-command).

1. **Kontinuerlig distribution**: Konfigurera kontinuerlig distribution, enligt beskrivningen i [kontinuerlig distribution till Azure App Service om du](deploy-continuous-deployment.md) använder Azure-pipeliner eller kudu-distribution, eller [distribuera till App Service med hjälp av GitHub-åtgärder om du](./deploy-continuous-deployment.md) använder GitHub åtgärder.

1. **Anpassade åtgärder**: om du vill utföra åtgärder inom app service behållare som är värd för din app, till exempel Django, kan du [ansluta till behållaren via SSH](configure-linux-open-ssh-session.md). Ett exempel på hur du kör django Database-migreringar finns i [Självstudier: Distribuera en django-webbapp med postgresql-köra Database-migreringar](tutorial-python-postgresql-app.md#43-run-django-database-migrations).
    - När du använder kontinuerlig distribution kan du utföra dessa åtgärder med hjälp av kommandon efter kompilering enligt beskrivningen ovan under [Anpassa Bygg automatisering](#customize-build-automation).

När de här stegen har slutförts bör du kunna genomföra ändringar i käll lagrings platsen och låta de uppdateringarna distribueras automatiskt till App Service.

### <a name="production-settings-for-django-apps"></a>Produktions inställningar för django-appar

För en produktions miljö som Azure App Service bör django-appar följa django: s [distribution check lista](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) (djangoproject.com).

I följande tabell beskrivs de produktions inställningar som är relevanta för Azure. De här inställningarna definieras i appens *Setting.py* -fil.

| Django-inställning | Instruktioner för Azure |
| --- | --- |
| `SECRET_KEY` | Lagra värdet i en App Service-inställning enligt beskrivningen i [åtkomst till appinställningar som miljövariabler](#access-app-settings-as-environment-variables). Du kan också [lagra värdet som en "hemlighet" i Azure Key Vault](../key-vault/secrets/quick-create-python.md). |
| `DEBUG` | Skapa en `DEBUG` inställning på App Service med värdet 0 (falskt) och Läs in värdet som en miljö variabel. I utvecklings miljön skapar du en `DEBUG` miljö variabel med värdet 1 (sant). |
| `ALLOWED_HOSTS` | I produktion kräver django att du inkluderar appens URL i `ALLOWED_HOSTS` matrisen *Settings.py*. Du kan hämta den här URL: en vid körning med koden `os.environ['WEBSITE_HOSTNAME']` . App Service ställer automatiskt in `WEBSITE_HOSTNAME` miljövariabeln till appens URL. |
| `DATABASES` | Definiera inställningarna i App Service för databas anslutningen och Läs in dem som miljövariabler för att fylla i [`DATABASES`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES) ord listan. Du kan också lagra värdena (särskilt användar namn och lösen ord) som [Azure Key Vault hemligheter](../key-vault/secrets/quick-create-python.md). |

## <a name="serve-static-files-for-django-apps"></a>Hantera statiska filer för django-appar

Om din django-webbapp innehåller statiska frontend-filer ska du först följa instruktionerna för att [Hantera statiska filer](https://docs.djangoproject.com/en/3.1/howto/static-files/) i Django-dokumentationen.

För App Service gör du följande ändringar:

1. Överväg att använda miljövariabler (för lokal utveckling) och appinställningar (när du distribuerar till molnet) för att dynamiskt ange django `STATIC_URL` och `STATIC_ROOT` variabler. Exempel:    

    ```python
    STATIC_URL = os.environ.get("DJANGO_STATIC_URL", "/static/")
    STATIC_ROOT = os.environ.get("DJANGO_STATIC_ROOT", "./static/")    
    ```

    `DJANGO_STATIC_URL` och `DJANGO_STATIC_ROOT` kan ändras efter behov för dina lokala miljöer och moln miljöer. Om exempelvis build-processen för dina statiska filer placerar dem i en mapp med namnet `django-static` , kan du välja att `DJANGO_STATIC_URL` undvika att `/django-static/` använda standardvärdet.

1. Om du har ett för-build-skript som genererar statiska filer i en annan mapp, inkluderar du mappen i Django- `STATICFILES_DIRS` variabeln så att django `collectstatic` process hittar dem. Om du till exempel kör `yarn build` i din frontend-mapp och garn genererar en `build/static` mapp som innehåller statiska filer, tar du med mappen på följande sätt:

    ```python
    FRONTEND_DIR = "path-to-frontend-folder" 
    STATICFILES_DIRS = [os.path.join(FRONTEND_DIR, 'build', 'static')]    
    ```

    Här, `FRONTEND_DIR` för att skapa en sökväg till där ett build-verktyg som garn körs. Du kan återigen använda en miljö variabel och en app-inställning som du vill.

1. Lägg till `whitenoise` i *requirements.txt* -filen. [Whitenoise](http://whitenoise.evans.io/en/stable/) (Whitenoise.Evans.IO) är ett python-paket som gör det enkelt för en produktions django-app att betjäna egna statiska filer. Whitenoise hanterar särskilt de filer som finns i mappen som anges av Django- `STATIC_ROOT` variabeln.

1. I *Settings.py* -filen lägger du till följande rad för Whitenoise:

    ```python
    STATICFILES_STORAGE = ('whitenoise.storage.CompressedManifestStaticFilesStorage')
    ```

1. Ändra även `MIDDLEWARE` `INSTALLED_APPS` listorna och för att inkludera Whitenoise:

    ```python
    MIDDLEWARE = [
        "whitenoise.middleware.WhiteNoiseMiddleware",
        # Other values follow
    ]

    INSTALLED_APPS = [
        "whitenoise.runserver_nostatic",
        # Other values follow
    ]
    ```

## <a name="container-characteristics"></a>Containeregenskaper

När du distribuerar till App Service körs python-appar i en Linux Docker-behållare som definieras i [App Service python GitHub-lagringsplatsen](https://github.com/Azure-App-Service/python). Du kan hitta bildkonfigurationerna i de versions bara katalogerna.

Den här containern har följande egenskaper:

- Appar körs med hjälp av [Gunicorn WSGI HTTP Server](https://gunicorn.org/), med de ytterligare argumenten `--bind=0.0.0.0 --timeout 600`.
    - Du kan ange konfigurations inställningar för Gunicorn via en *gunicorn.conf.py* -fil i projekt roten, enligt beskrivningen i [Gunicorn configuration Overview](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.gunicorn.org). Du kan också [Anpassa Start kommandot](#customize-startup-command).

    - För att skydda din webbapp från oavsiktliga eller avsiktliga DDOS-attacker körs Gunicorn bakom en nginx omvänd proxy enligt beskrivningen i [Deploying Gunicorn](https://docs.gunicorn.org/en/latest/deploy.html) (docs.gunicorn.org).

- Som standard innehåller bas behållar avbildningen endast ram-webbramverket, men containern stöder andra ramverk som är grundläggande wsgi-kompatibla och kompatibla med python 3.6 +, till exempel django.

- Om du vill installera ytterligare paket, till exempel Django, skapar du en [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) -fil i roten för ditt projekt som anger dina direkta beroenden. App Service installerar sedan dessa beroenden automatiskt när du distribuerar projektet.

    Den *requirements.txt* filen *måste* finnas i projekt roten för att beroenden ska kunna installeras. Annars rapporterar build-processen felet: "Det gick inte att hitta setup.py eller requirements.txt; Kör inte pip-installation. " Om det här felet uppstår kontrollerar du platsen för din krav fil.

- App Service definierar automatiskt en miljö variabel med namnet `WEBSITE_HOSTNAME` med webbappens webb adress, till exempel `msdocs-hello-world.azurewebsites.net` . Den definierar också `WEBSITE_SITE_NAME` med namnet på din app, till exempel `msdocs-hello-world` . 
   
- NPM och Node.js installeras i behållaren så att du kan köra Node-baserade build-verktyg, till exempel garn.

## <a name="container-startup-process"></a>Startprocessen för container

Under starten kör App Service i Linux-containern följande steg:

1. Använd ett [anpassat startkommando](#customize-startup-command) om sådant tillhandahålls.
2. Kontrollera om finns en [Django-app](#django-app), och starta Gunicorn för den om det finns.
3. Kontrollera om finns en [Flask-app](#flask-app), och starta Gunicorn för den om det finns.
4. Om ingen annan app hittas startar du en standardapp som är inbyggd i containern.

Följande avsnitt innehåller ytterligare information om varje alternativ.

### <a name="django-app"></a>Django-app

För Django-appar letar App Service efter en fil med namnet `wsgi.py` i din appkod och kör sedan Gunicorn med hjälp av följande kommando:

```bash
# <module> is the name of the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Om du vill ha mer detaljerad kontroll över Start kommandot använder du ett [anpassat start kommando](#customize-startup-command), ersätter `<module>` med namnet på den mapp som innehåller *wsgi.py* och lägger till ett `--chdir` argument om den modulen inte finns i projekt roten. Om din *wsgi.py* till exempel finns under *knboard/Server del/config* från projekt roten använder du argumenten `--chdir knboard/backend config.wsgi` .

Om du vill aktivera produktions loggning lägger du till `--access-logfile` `--error-logfile` parametrarna och som visas i exemplen för [anpassade Start kommandon](#customize-startup-command).

### <a name="flask-app"></a>Flask-app

För Flask letar App Service efter en fil med namnet *application.py* eller *app.py* och startar Gunicorn enligt följande:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app

# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Om din huvudsakliga app-modul finns i en annan fil, Använd ett annat namn för app-objektet, eller om du vill ange ytterligare argument för Gunicorn, använder du ett [anpassat start kommando](#customize-startup-command).

### <a name="default-behavior"></a>Standardbeteende

Om App Service inte hittar något anpassat kommando, en django-app eller en kolv-app, körs en skrivskyddad skrivskyddad app som finns i mappen _opt/defaultsite_ och visas i följande bild.

Om du har distribuerat kod och fortfarande ser standardappen, se [fel sökning – appen visas inte](#app-doesnt-appear).

[![Standardmässig App Service på Linux-webbplats](media/configure-language-python/default-python-app.png)](#app-doesnt-appear)

Om du förväntar dig att se en distribuerad app i stället för standardappen, se [fel sökning – appen visas inte](#app-doesnt-appear).

## <a name="customize-startup-command"></a>Anpassa startkommando

Som tidigare nämnts i den här artikeln kan du ange konfigurations inställningar för Gunicorn via en *gunicorn.conf.py* -fil i projekt roten, enligt beskrivningen i [Gunicorn konfigurations översikt](https://docs.gunicorn.org/en/stable/configure.html#configuration-file).

Om den här konfigurationen inte räcker kan du styra behållarens start beteende genom att tillhandahålla antingen ett anpassat start kommando eller flera kommandon i en start kommando fil. En start kommando fil kan använda det namn som du väljer, till exempel *startup.sh*, *startup. cmd*, *startup.txt* och så vidare.

Alla kommandon måste använda relativa sökvägar för rotmappen för projektet.

Ange ett start kommando eller en kommando fil:

- **Azure Portal**: Välj appens **konfigurations** sida och välj sedan **allmänna inställningar**. I fältet **Start kommando** placerar du antingen den fullständiga texten i Start kommandot eller namnet på Start kommando filen. Välj sedan **Spara** för att tillämpa ändringarna. Se [Konfigurera allmänna inställningar](configure-common.md#configure-general-settings) för Linux-behållare.

- **Azure CLI**: Använd kommandot [AZ webapp config set](/cli/azure/webapp/config#az_webapp_config_set) med `--startup-file` parametern för att ange start kommandot eller-filen:

    ```azurecli
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    Ersätt `<custom-command>` med antingen den fullständiga texten för Start kommandot eller namnet på Start kommando filen.
        
App Service ignorerar eventuella fel som uppstår vid bearbetning av ett anpassat start kommando eller en fil och fortsätter sedan att starta processen genom att leta efter Django-och mätkolv-appar. Om du inte ser det förväntade beteendet kan du kontrol lera att Start kommandot eller filen är felfritt och att en start kommando fil distribueras till App Service tillsammans med din app-kod. Du kan också kontrol lera [diagnostikloggar](#access-diagnostic-logs) för ytterligare information. Kontrol lera också appens **diagnos-och lösa problem** -sida på [Azure Portal](https://portal.azure.com).

### <a name="example-startup-commands"></a>Exempel på Start kommandon

- **Tillagda Gunicorn-argument**: följande exempel lägger till `--workers=4` en Gunicorn-kommando rad för att starta en django-app: 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    Mer information finns i [Köra Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

- **Aktivera produktions loggning för django**: Lägg till `--access-logfile '-'` `--error-logfile '-'` argumenten och på kommando raden:

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    Loggarna visas i [App Service logg data ström](#access-diagnostic-logs).

    Mer information finns i [Gunicorn Logging](https://docs.gunicorn.org/en/stable/settings.html#logging) (docs.gunicorn.org).
    
- **Main-modul för anpassade flaskor**: som standard förutsätter App Service att en kolvs huvud-modul är *Application.py* eller *app.py*. Om din huvud-modul använder ett annat namn måste du anpassa Start kommandot. Om du till exempel har en kolv-app vars huvudmodul är *Hello.py* och kolv-YF i filen heter `myapp` , är kommandot följande:

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    Om din huvudmodul är i en undermapp, till exempel `website`, anger du den mappen med argumentet `--chdir`:
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- **Använd en icke-Gunicorn Server**: om du vill använda en annan webb server, till exempel [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), använder du lämpligt kommando som start kommando eller i Start kommando filen:

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>Åtkomst till appinställningar som miljövariabler

Appinställningar är värden som lagras i molnet specifikt för din app enligt beskrivningen i [Konfigurera appinställningar](configure-common.md#configure-app-settings). De här inställningarna är tillgängliga för din app Code som miljövariabler och används med standard- [OS. Environ](https://docs.python.org/3/library/os.html#os.environ) -mönstret.

Om du till exempel har skapat en app-inställning `DATABASE_SERVER` som kallas, hämtar följande kod inställningen värde:

```python
db_server = os.environ['DATABASE_SERVER']
```

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service sker [SSL-terminering](https://wikipedia.org/wiki/TLS_termination_proxy) (wikipedia.org) vid utjämning av nätverks belastning, så alla HTTPS-begäranden når din app som okrypterade HTTP-förfrågningar. Om din applogik behöver kontrollera om användarbegäranden är krypterade eller inte kan du kontrollera `X-Forwarded-Proto`-rubriken.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Med populära ramverk får du åtkomst till `X-Forwarded-*` information i standardappens mönster. I [CodeIgniter](https://codeigniter.com/) kontrollerar [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) värdet för `X_FORWARDED_PROTO` som standard.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Om du vill komma åt loggarna via Azure Portal väljer du **övervaka**  >  **logg ström** på menyn till vänster för din app.

## <a name="access-deployment-logs"></a>Åtkomst till distributions loggar

När du distribuerar din kod utför App Service Bygg processen som beskrivs tidigare i avsnittet [Anpassa Bygg automatisering](#customize-build-automation). Eftersom bygget körs i en egen behållare lagras build-loggar separat från appens diagnostikloggar.

Använd följande steg för att komma åt distributions loggarna:

1. På Azure Portal för din webbapp väljer du **distributions**  >  **distributions Center (för hands version)** på den vänstra menyn.
1. På fliken **loggar** väljer du **inchecknings-ID** : t för den senaste incheckningen.
1. På sidan **logg information** som visas väljer du länken **Visa loggar...** som visas bredvid "kör Oryx build...".

Bygg problem som felaktiga beroenden i *requirements.txt* och fel i för-eller efter build-skript visas i dessa loggar. Fel visas även om din krav fil inte är exakt namngiven *requirements.txt* eller inte visas i rotmappen i projektet.

## <a name="open-ssh-session-in-browser"></a>Öppna en SSH-session i webbläsaren

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

När du har anslutit till SSH-sessionen bör du se meddelandet "SSH-anslutning upprättad" längst ned i fönstret. Om du ser fel som "SSH_CONNECTION_CLOSED" eller ett meddelande om att behållaren startas om kan ett fel förhindra att appens behållare startas. Se [fel sökning](#troubleshooting) för steg för att undersöka möjliga problem.

## <a name="troubleshooting"></a>Felsökning

I allmänhet är det första steget i fel sökning att använda App Service diagnostik:

1. Välj **diagnostisera och lös problem** på den vänstra menyn i Azure Portal för din webbapp.
1. Välj **tillgänglighet och prestanda**.
1. Granska informationen i alternativen för **program loggar**, **behållare krasch** och **container-problem** , där de vanligaste problemen visas.

Granska sedan både [distributions loggarna](#access-deployment-logs) och [app-loggarna](#access-diagnostic-logs) för eventuella fel meddelanden. Dessa loggar identifierar ofta specifika problem som kan förhindra program distribution eller app-start. Build-versionen kan till exempel inte köras om *requirements.txt* filen har fel fil namn eller inte finns i rotmappen för Project.

I följande avsnitt finns ytterligare vägledning för specifika problem.

- [Appen visas inte – standard app visas](#app-doesnt-appear)
- [Appen visas inte-meddelandet "tjänsten är inte tillgänglig"](#service-unavailable)
- [Det gick inte att hitta setup.py eller requirements.txt](#could-not-find-setuppy-or-requirementstxt)
- [ModuleNotFoundError vid start](#modulenotfounderror-when-app-starts)
- [Databasen är låst](#database-is-locked)
- [Lösen ord visas inte i SSH-sessionen när de skrivs](#other-issues)
- [Kommandon i SSH-sessionen förefaller vara avhuggna](#other-issues)
- [Statiska till gångar visas inte i en django-app](#other-issues)
- [En kritisk SSL-anslutning krävs](#other-issues)

#### <a name="app-doesnt-appear"></a>Appen visas inte

- **Du ser standardappen när du har distribuerat din egen appkod.** [Standard programmet](#default-behavior) visas eftersom du inte har distribuerat din app-kod till App Service, eller App Service inte att hitta din app-kod och körde standardappen i stället.

    - Starta om App Service, vänta 15–20 sekunder och kontrollera appen igen.
    
    - Se till att du använder App Service för Linux i stället för en Windows-baserad instans. Från Azure-CLI kör du kommandot `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind`. Byt ut `<resource-group-name>` och `<app-name>` därefter. Du bör se `app,linux` som utdata; annars återskapar du App Service och väljer Linux.
    
    - Använd [SSH](#open-ssh-session-in-browser) för att ansluta direkt till App Service-behållaren och kontrol lera att filerna finns under *plats/wwwroot*. Använd följande steg om dina filer inte finns:
      1. Skapa en app-inställning med `SCM_DO_BUILD_DURING_DEPLOYMENT` värdet 1, distribuera om koden, vänta några minuter och försök sedan att öppna appen igen. Mer information om hur du skapar app-inställningar finns [i Konfigurera en app service-app i Azure Portal](configure-common.md).
      1. Granska distributions processen, [kontrol lera distributions loggarna](#access-deployment-logs), korrigera eventuella fel och distribuera om appen.
    
    - Om filerna finns kunde App Service inte identifiera din specifika startfil. Kontrol lera att din app är strukturerad som App Service förväntar sig för [django](#django-app) eller [flaska](#flask-app), eller Använd ett [anpassat start kommando](#customize-startup-command).

- <a name="service-unavailable"></a>**Meddelandet "tjänsten är inte tillgänglig" visas i webbläsaren.** Tids gränsen för webbläsaren nåddes i väntan på ett svar från App Service, vilket tyder på att App Service startade Gunicorn-servern, men själva appen startades inte. Det här tillståndet kan tyda på att Gunicorn-argumenten är felaktiga eller att det finns ett fel i app-koden.

    - Uppdatera webbläsaren, särskilt om du använder de lägsta prisnivåerna i din App Service-plan. Till exempel kan appen ta längre tid att starta om du använder de kostnadsfria nivåerna och blir tillgänglig när du uppdaterar webbläsaren.

    - Kontrol lera att din app är strukturerad som App Service förväntar sig för [django](#django-app) eller [flaska](#flask-app), eller Använd ett [anpassat start kommando](#customize-startup-command).

    - Granska [program logg strömmen](#access-diagnostic-logs) för eventuella fel meddelanden. Loggarna visar eventuella fel i app-koden.

#### <a name="could-not-find-setuppy-or-requirementstxt"></a>Det gick inte att hitta setup.py eller requirements.txt

- **Logg strömmen visar att det inte gick att hitta setup.py eller requirements.txt. Det gick inte att installera pip. "**: det gick inte att hitta *requirements.txts* filen med Oryx-build-processen.

    - Anslut till webbappens behållare via [SSH](#open-ssh-session-in-browser) och kontrol lera att *requirements.txt* har namnet korrekt och finns direkt under *plats/wwwroot*. Om den inte finns gör du platsen som filen finns i din lagrings plats och ingår i distributionen. Om den finns i en separat mapp flyttar du den till roten.

#### <a name="modulenotfounderror-when-app-starts"></a>ModuleNotFoundError när appen startar

Om du ser ett fel som `ModuleNotFoundError: No module named 'example'` det innebär att python inte kunde hitta en eller flera av dina moduler när programmet startade. Detta inträffar oftast om du distribuerar den virtuella miljön med din kod. Virtuella miljöer är inte bärbara, så en virtuell miljö bör inte distribueras med program koden. Låt Oryx skapa en virtuell miljö och installera dina paket på webbappen genom att skapa en app-inställning, `SCM_DO_BUILD_DURING_DEPLOYMENT` och Ställ in den på `1` . Detta tvingar Oryx att installera dina paket när du distribuerar till App Service. Mer information finns i [den här artikeln om virtuell Miljös portabilitet](https://azure.github.io/AppService/2020/12/11/cicd-for-python-apps.html).

### <a name="database-is-locked"></a>Databasen är låst

När du försöker köra databas migreringar med en django-app kan du se "sqlite3. OperationalError: databasen är låst. " Felet indikerar att programmet använder en SQLite-databas där django är konfigurerat som standard, i stället för att använda en moln databas som PostgreSQL för Azure.

Kontrol lera `DATABASES` variabeln i appens *Settings.py* -fil för att säkerställa att din app använder en moln databas i stället för sqlite.

Om du stöter på det här felet med exemplet i [Självstudier: Distribuera en django-webbapp med postgresql](tutorial-python-postgresql-app.md), kontrollerar du att du har slutfört stegen i [Konfigurera miljövariabler för att ansluta databasen](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database).

#### <a name="other-issues"></a>Andra problem

- **Lösen ord visas inte i SSH-sessionen när de skrivs**: av säkerhets skäl håller SSH-sessionen ditt lösen ord dolt när du skriver. Tecknen registreras men skriv ditt lösen ord som vanligt och tryck på **RETUR** när du är färdig.

- **Kommandon i SSH-sessionen förefaller vara kapade**: redigerings programmet kanske inte är ord brytnings kommandon, men de bör fortfarande köras på rätt sätt.

- **Statiska till gångar visas inte i en django-app**: kontrol lera att du har aktiverat [Whitenoise-modulen](http://whitenoise.evans.io/en/stable/django.html)

- **Meddelandet "en kritisk SSL-anslutning krävs"**: kontrol lera eventuella användar namn och lösen ord som används för att få åtkomst till resurser (till exempel databaser) inifrån appen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: python-app med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Självstudie: Distribuera från privat container-lagringsplats](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service på Linux](faq-app-service-linux.md)