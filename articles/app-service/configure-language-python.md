---
title: Konfigurera Linux Python-appar
description: Lär dig hur du konfigurerar Python-containern där webbappar körs med både Azure Portal och Azure CLI.
ms.topic: quickstart
ms.date: 03/16/2021
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 605d1e0f67ac959d2c7325e04e2fd10d9d2419be
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829502"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Konfigurera en Linux Python-app för Azure App Service

Den här artikeln [beskriver Azure App Service](overview.md) kör Python-appar, hur du kan migrera befintliga appar till Azure och hur du kan anpassa beteendet för App Service när det behövs. Python-appar måste distribueras med alla nödvändiga [pip-moduler.](https://pypi.org/project/pip/)

Distributionsmotorn App Service automatiskt en virtuell miljö och körs åt dig när du distribuerar en Git-lagringsplats `pip install -r requirements.txt` , eller ett [zip-paket](deploy-zip.md). [](deploy-local-git.md)

Den här guiden innehåller viktiga begrepp och instruktioner för Python-utvecklare som använder en inbyggd Linux-container i App Service. Om du aldrig har använt Azure App Service följer du först [självstudien Python-snabbstart](quickstart-python.md) [och Python med PostgreSQL.](tutorial-python-postgresql-app.md)

Du kan använda antingen [Azure Portal](https://portal.azure.com) eller Azure CLI för konfiguration:

- **Azure Portal** använder du appens **inställningskonfigurationssida** enligt beskrivningen i  >   Konfigurera [en App Service-app i Azure Portal](configure-common.md).

- **Azure CLI:** du har två alternativ.

    - Kör kommandon i [Azure Cloud Shell](../cloud-shell/overview.md).
    - Kör kommandon lokalt genom att installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli)och logga sedan in på Azure med [az login](/cli/azure/reference-index#az_login).
    
> [!NOTE]
> Linux är för närvarande det rekommenderade alternativet för att köra Python-appar i App Service. Information om Windows-alternativet finns i [Python på Windows-versionen av App Service](/visualstudio/python/managing-python-on-azure-app-service).

## <a name="configure-python-version"></a>Konfigurera Python-version

- **Azure Portal:** Använd fliken **Allmänna inställningar på sidan** **Konfiguration** enligt beskrivningen i [Konfigurera allmänna inställningar för](configure-common.md#configure-general-settings) Linux-containrar.

- **Azure CLI:**

    -  Visa den aktuella Python-versionen [med az webapp config show](/cli/azure/webapp/config#az_webapp_config_show):
    
        ```azurecli
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Ersätt `<resource-group-name>` och med de namn som är lämpliga för din `<app-name>` webbapp.
    
    - Ange Python-versionen med [az webapp config set](/cli/azure/webapp/config#az_webapp_config_set)
        
        ```azurecli
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - Visa alla Python-versioner som stöds i Azure App Service med [az webapp list-runtimes:](/cli/azure/webapp#az_webapp_list_runtimes)
    
        ```azurecli
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
Du kan köra en version av Python genom att skapa en egen containeravbildning i stället. Mer information finns i [Använda anpassad Docker-avbildning](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>Anpassa byggautomatisering

App Service:s byggsystem, som kallas Oryx, utför följande steg när du distribuerar din app med Git- eller zip-paket:

1. Kör ett anpassat förbyggskript om det anges av `PRE_BUILD_COMMAND` inställningen. (Skriptet kan själv köra andra Python- och Node.js-skript, pip- och npm-kommandon och Node-baserade verktyg som yarn, till exempel `yarn install` och `yarn build` .)

1. Kör `pip install -r requirements.txt`. Filen *requirements.txt* måste finnas i projektets rotmapp. Annars rapporterar byggprocessen felet: "Det gick inte att hitta setup.py eller requirements.txt; Kör inte pip install."

1. Om *manage.py* finns i lagringsplatsens rot (som anger en Django-app) kör *du manage.py collectstatic*. Men om inställningen `DISABLE_COLLECTSTATIC` är hoppas det här steget `true` över.

1. Kör ett anpassat post-build-skript om det anges av `POST_BUILD_COMMAND` inställningen. (Skriptet kan återigen köra andra Python- och Node.js-skript, pip- och npm-kommandon och Node-baserade verktyg.)

Som standard är `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` inställningarna , och `DISABLE_COLLECTSTATIC` tomma. 

- Om du vill inaktivera körning av collectstatic när du skapar Django-appar anger `DISABLE_COLLECTSTATIC` du inställningen till true.

- Om du vill köra förbyggkommandon anger du att inställningen ska innehålla antingen ett kommando, till exempel , eller en sökväg till en skriptfil i förhållande till `PRE_BUILD_COMMAND` `echo Pre-build command` projektroten, till exempel `scripts/prebuild.sh` . Alla kommandon måste använda relativa sökvägar till projektets rotmapp.

- Om du vill köra post-build-kommandon anger du att inställningen ska innehålla antingen ett kommando, till exempel , eller en sökväg till en skriptfil i förhållande till `POST_BUILD_COMMAND` `echo Post-build command` projektroten, till exempel `scripts/postbuild.sh` . Alla kommandon måste använda relativa sökvägar till projektets rotmapp.

Ytterligare inställningar som anpassar byggautomatisering finns i [Oryx-konfiguration.](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md) 

Information om hur du kommer åt build- och distributionsloggarna finns [i Åtkomst till distributionsloggar.](#access-deployment-logs)

Mer information om hur App Service kör och skapar Python-appar i Linux finns i Så identifierar och skapar [Oryx Python-appar.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md)

> [!NOTE]
> Inställningarna `PRE_BUILD_SCRIPT_PATH` och `POST_BUILD_SCRIPT_PATH` är identiska med och och stöds i `PRE_BUILD_COMMAND` äldre `POST_BUILD_COMMAND` syften.
> 
> En inställning med `SCM_DO_BUILD_DURING_DEPLOYMENT` namnet , om den innehåller eller `true` 1, utlöser en Oryx-version under distributionen. Inställningen är sann när du distribuerar med git, Azure CLI-kommandot `az webapp up` och Visual Studio Code.

> [!NOTE]
> Använd alltid relativa sökvägar i alla för- och efterbyggskript eftersom byggcontainern där Oryx körs skiljer sig från körningscontainern där appen körs. Förlita dig aldrig på den exakta placeringen av appens projektmapp i containern (till exempel att den placeras under *site/wwwroot*).

## <a name="migrate-existing-applications-to-azure"></a>Migrera befintliga program till Azure

Befintliga webbprogram kan omdistribueras till Azure på följande sätt:

1. **Källdatabas:** Behåll källkoden på en lämplig lagringsplats som GitHub, vilket gör att du kan konfigurera kontinuerlig distribution senare i den här processen.
    1. Din *requirements.txt* måste finnas i lagringsplatsens rot för att App Service installera de nödvändiga paketen automatiskt.    

1. **Databas:** Om din app är beroende av en databas måste du även etablera nödvändiga resurser i Azure. Se [Självstudie: Distribuera en Django-webbapp med PostgreSQL – skapa en databas](tutorial-python-postgresql-app.md#3-create-postgres-database-in-azure) för ett exempel.

1. **App Service-resurser:** Skapa en resursgrupp, App Service plan och skapa App Service som värd för ditt program. Det är enklast att göra detta genom att göra en inledande distribution av koden via Azure CLI-kommandot , som du ser i Självstudie: Distribuera en `az webapp up` [Django-webbapp med PostgreSQL – distribuera koden](tutorial-python-postgresql-app.md#4-deploy-the-code-to-azure-app-service). Ersätt namnen på resursgruppen, App Service Plan och webbappen så att de passar ditt program bättre.

1. **Miljövariabler:** Om programmet kräver miljövariabler skapar du motsvarande [App Service programinställningar](configure-common.md#configure-app-settings). De App Service inställningarna visas i koden som miljövariabler, enligt beskrivningen i [Åtkomst till miljövariabler](#access-app-settings-as-environment-variables).
    - Databasanslutningar hanteras till exempel ofta via sådana inställningar, som du ser i Självstudie: Distribuera en [Django-webbapp med PostgreSQL –](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database)konfigurera variabler för att ansluta databasen .
    - Se [Produktionsinställningar för Django-appar för](#production-settings-for-django-apps) specifika inställningar för typiska Django-appar.

1. **Appstart:** Läs avsnittet Start av [container senare i](#container-startup-process) den här artikeln för att förstå hur App Service försöker köra din app. App Service använder Gunicorn-webbservern som standard, som måste kunna hitta ditt appobjekt eller *wsgi.py* mapp. Om det behövs kan du [anpassa startkommandot](#customize-startup-command).

1. **Kontinuerlig distribution:** Konfigurera kontinuerlig distribution, enligt beskrivningen i Kontinuerlig distribution till [Azure App Service](deploy-continuous-deployment.md) om du använder Azure Pipelines eller Kudu-distribution, eller Distribuera till App Service med [GitHub Actions om](./deploy-continuous-deployment.md) du använder GitHub-åtgärder.

1. **Anpassade åtgärder:** Om du vill utföra åtgärder i App Service-containern som är värd för din app, till exempel Django-databasmigrering, kan du ansluta till [containern via SSH](configure-linux-open-ssh-session.md). Ett exempel på hur du kör Django-databasmigrering finns i [Självstudie: Distribuera en Django-webbapp med PostgreSQL – köra databasmigrering.](tutorial-python-postgresql-app.md#43-run-django-database-migrations)
    - När du använder kontinuerlig distribution kan du utföra dessa åtgärder med hjälp av kommandon som beskrivs tidigare under [Anpassa byggautomatisering.](#customize-build-automation)

När de här stegen har slutförts bör du kunna genomföra ändringar i källdatabasen och få dessa uppdateringar automatiskt distribuerade till App Service.

### <a name="production-settings-for-django-apps"></a>Produktionsinställningar för Django-appar

För en produktionsmiljö som Azure App Service bör Django-appar följa Djangos [checklista för](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) distribution (djangoproject.com).

I följande tabell beskrivs de produktionsinställningar som är relevanta för Azure. De här inställningarna definieras i appens *setting.py* fil.

| Django-inställning | Instruktioner för Azure |
| --- | --- |
| `SECRET_KEY` | Lagra värdet i en App Service enligt beskrivningen i [Åtkomst till appinställningar som miljövariabler](#access-app-settings-as-environment-variables). Du kan alternativt [lagra värdet som en "hemlighet" i Azure Key Vault](../key-vault/secrets/quick-create-python.md). |
| `DEBUG` | Skapa en `DEBUG` inställning på App Service värdet 0 (falskt) och läs sedan in värdet som en miljövariabel. I utvecklingsmiljön skapar du en `DEBUG` miljövariabel med värdet 1 (sant). |
| `ALLOWED_HOSTS` | I produktion kräver Django att du inkluderar appens URL i `ALLOWED_HOSTS` matrisen med *settings.py*. Du kan hämta den här URL:en vid körning med koden `os.environ['WEBSITE_HOSTNAME']` . App Service anger automatiskt `WEBSITE_HOSTNAME` miljövariabeln till appens URL. |
| `DATABASES` | Definiera inställningarna i App Service för databasanslutningen och läs in dem som miljövariabler för att fylla [`DATABASES`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES) i ordlistan. Alternativt kan du lagra värdena (särskilt användarnamnet och lösenordet) som Azure Key Vault [hemligheter](../key-vault/secrets/quick-create-python.md). |

## <a name="serve-static-files-for-django-apps"></a>Servera statiska filer för Django-appar

Om din Django-webbapp innehåller statiska frontend-filer följer du först anvisningarna i Hantera statiska [filer](https://docs.djangoproject.com/en/3.1/howto/static-files/) i Django-dokumentationen.

För App Service gör du följande ändringar:

1. Överväg att använda miljövariabler (för lokal utveckling) och appinställningar (vid distribution till molnet) för att dynamiskt ange Django `STATIC_URL` och `STATIC_ROOT` variabler. Exempel:    

    ```python
    STATIC_URL = os.environ.get("DJANGO_STATIC_URL", "/static/")
    STATIC_ROOT = os.environ.get("DJANGO_STATIC_ROOT", "./static/")    
    ```

    `DJANGO_STATIC_URL` och `DJANGO_STATIC_ROOT` kan ändras efter behov för dina lokala miljöer och molnmiljöer. Om till exempel byggprocessen för dina statiska filer placerar dem i en mapp med namnet kan du ange till för `django-static` att undvika att använda `DJANGO_STATIC_URL` `/django-static/` standardvärdet.

1. Om du har ett förbyggskript som genererar statiska filer i en annan mapp ska du inkludera mappen i Django-variabeln så `STATICFILES_DIRS` att Djangos `collectstatic` process hittar dem. Om du till exempel kör i din frontend-mapp och yarn genererar en mapp som innehåller statiska filer inkluderar du `yarn build` den mappen på följande `build/static` sätt:

    ```python
    FRONTEND_DIR = "path-to-frontend-folder" 
    STATICFILES_DIRS = [os.path.join(FRONTEND_DIR, 'build', 'static')]    
    ```

    Här skapar `FRONTEND_DIR` du en sökväg till platsen där ett byggverktyg som yarn körs. Du kan återigen använda en miljövariabel och appinställning efter behov.

1. Lägg `whitenoise` till i *requirements.txt* fil. [Whitenoise](http://whitenoise.evans.io/en/stable/) (whitenoise.evans.io) är ett Python-paket som gör det enkelt för en produktions-Django-app att betjäna sina egna statiska filer. Whitenoise betjänar specifikt de filer som finns i mappen som anges av Django-variabeln. `STATIC_ROOT`

1. I din *settings.py* lägger du till följande rad för Whitenoise:

    ```python
    STATICFILES_STORAGE = ('whitenoise.storage.CompressedManifestStaticFilesStorage')
    ```

1. Ändra även `MIDDLEWARE` listorna `INSTALLED_APPS` och så att de inkluderar Whitenoise:

    ```python
    MIDDLEWARE = [                                                                   
        'django.middleware.security.SecurityMiddleware',
        # Add whitenoise middleware after the security middleware                             
        'whitenoise.middleware.WhiteNoiseMiddleware',
        # Other values follow
    ]

    INSTALLED_APPS = [
        "whitenoise.runserver_nostatic",
        # Other values follow
    ]
    ```

## <a name="container-characteristics"></a>Containeregenskaper

När de distribueras till App Service körs Python-appar i en Linux Docker-container som definieras i App Service [Python GitHub-lagringsplatsen](https://github.com/Azure-App-Service/python). Du hittar avbildningskonfigurationerna i de versionsspecifika katalogerna.

Den här containern har följande egenskaper:

- Appar körs med hjälp av [Gunicorn WSGI HTTP Server](https://gunicorn.org/), med de ytterligare argumenten `--bind=0.0.0.0 --timeout 600`.
    - Du kan ange konfigurationsinställningar för Gunicorn via *en gunicorn.conf.py-fil* i projektroten, enligt beskrivningen i Översikt över [Gunicorn-konfiguration](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.gunicorn.org). Du kan också [anpassa startkommandot](#customize-startup-command).

    - För att skydda din webbapp från oavsiktliga eller avsiktliga DDOS-attacker körs Gunicorn bakom en omvänd Nginx-proxy enligt beskrivningen i Distribuera [Gunicorn](https://docs.gunicorn.org/en/latest/deploy.html) (docs.gunicorn.org).

- Som standard innehåller bascontaineravbildningen endast Flask-webbramverket, men containern stöder andra ramverk som är WSGI-kompatibla och kompatibla med Python 3.6+, till exempel Django.

- Om du vill installera ytterligare paket, till exempel Django, [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) en fil i projektroten som anger dina direkta beroenden. App Service sedan automatiskt dessa beroenden när du distribuerar projektet.

    Filen *requirements.txt* *måste* finnas i projektroten för att beroenden ska installeras. Annars rapporterar byggprocessen felet: "Det gick inte att hitta setup.py eller requirements.txt; Kör inte pip install." Om du stöter på det här felet kontrollerar du platsen för din kravfil.

- App Service definierar automatiskt en miljövariabel `WEBSITE_HOSTNAME` med namnet med webbappens URL, till exempel `msdocs-hello-world.azurewebsites.net` . Den definierar `WEBSITE_SITE_NAME` också med namnet på din app, till exempel `msdocs-hello-world` . 
   
- npm och Node.js installeras i containern så att du kan köra Node-baserade byggverktyg, till exempel yarn.

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

Om du vill ha mer specifik kontroll [](#customize-startup-command)över startkommandot använder du ett anpassat startkommando, ersätter med namnet på mappen som innehåller wsgi.py och lägger till ett argument om modulen inte finns i `<module>`  `--chdir` projektroten. Om ditt konto till *exempel wsgi.py* under *knboard/backend/config* från projektroten använder du argumenten `--chdir knboard/backend config.wsgi` .

Om du vill aktivera produktionsloggning lägger du till `--access-logfile` `--error-logfile` parametrarna och enligt exemplen för [anpassade startkommandon](#customize-startup-command).

### <a name="flask-app"></a>Flask-app

För Flask letar App Service efter en fil med namnet *application.py* eller *app.py* och startar Gunicorn enligt följande:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app

# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Om din huvudappmodul finns i en annan fil använder du ett annat namn för appobjektet, eller om du vill ange ytterligare argument till Gunicorn, använder du ett anpassat [startkommando](#customize-startup-command).

### <a name="default-behavior"></a>Standardbeteende

Om App Service inte hittar ett anpassat kommando, en Django-app eller en Flask-app kör den en skrivskyddad standardapp som finns i mappen _opt/defaultsite_ och visas i följande bild.

Om du har distribuerat kod och fortfarande ser standardappen kan [du se Felsökning – appen visas inte.](#app-doesnt-appear)

[![Standardmässig App Service på Linux-webbplats](media/configure-language-python/default-python-app.png)](#app-doesnt-appear)

Om du förväntar dig att se en distribuerad app i stället för standardappen kan du återigen [se Felsökning – Appen visas inte.](#app-doesnt-appear)

## <a name="customize-startup-command"></a>Anpassa startkommando

Som vi nämnde tidigare i den här artikeln kan du ange konfigurationsinställningar för Gunicorn via *en gunicorn.conf.py-fil* i projektroten enligt beskrivningen i [Gunicorn-konfigurationsöversikten.](https://docs.gunicorn.org/en/stable/configure.html#configuration-file)

Om konfigurationen inte är tillräcklig kan du styra containerns startbeteende genom att ange antingen ett anpassat startkommando eller flera kommandon i en startkommandofil. En startkommandofil kan använda det namn du väljer, till exempel *startup.sh,* *startup.cmd,* *startup.txt* och så vidare.

Alla kommandon måste använda relativa sökvägar till projektets rotmapp.

Så här anger du ett startkommando eller en kommandofil:

- **Azure Portal:** välj appens **konfigurationssida** och välj sedan **Allmänna inställningar.** I fältet **Startkommando** placerar du antingen den fullständiga texten i startkommandot eller namnet på startkommandofilen. Välj sedan **Spara** för att tillämpa ändringarna. Se [Konfigurera allmänna inställningar för](configure-common.md#configure-general-settings) Linux-containrar.

- **Azure CLI:** Använd kommandot [az webapp config set](/cli/azure/webapp/config#az_webapp_config_set) med `--startup-file` parametern för att ange startkommandot eller filen:

    ```azurecli
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    Ersätt `<custom-command>` med antingen den fullständiga texten i startkommandot eller namnet på startkommandofilen.
        
App Service ignorerar eventuella fel som inträffar vid bearbetning av ett anpassat startkommando eller en anpassad startfil och fortsätter sedan sin startprocess genom att leta efter Django- och Flask-appar. Om du inte ser det beteende som du förväntar dig kontrollerar du att startkommandot eller filen är felfri och att en startkommandofil distribueras till App Service tillsammans med din appkod. Du kan också kontrollera [diagnostikloggarna för](#access-diagnostic-logs) ytterligare information. Kontrollera även appens sida **Diagnostisera och lösa problem** på sidan [Azure Portal](https://portal.azure.com).

### <a name="example-startup-commands"></a>Exempel på startkommandon

- **Gunicorn-argument har lagts** till: I följande exempel läggs till i `--workers=4` en Gunicorn-kommandorad för att starta en Django-app: 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    Mer information finns i [Köra Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

- **Aktivera produktionsloggning för Django:** Lägg till `--access-logfile '-'` `--error-logfile '-'` argumenten och på kommandoraden:

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    Dessa loggar visas i den [App Service loggströmmen](#access-diagnostic-logs).

    Mer information finns i [Gunicorn-loggning](https://docs.gunicorn.org/en/stable/settings.html#logging) (docs.gunicorn.org).
    
- **Anpassad Flask-huvudmodul:** som App Service förutsätter att flask-appens huvudmodul är *application.py* eller *app.py*. Om huvudmodulen använder ett annat namn måste du anpassa startkommandot. Om du till exempel har en Flask-app vars huvudmodul *är hello.py* och Flask-appobjektet i filen heter `myapp` så här:

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    Om din huvudmodul är i en undermapp, till exempel `website`, anger du den mappen med argumentet `--chdir`:
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- **Använd en icke-Gunicorn-server:** Om du vill använda en annan webbserver, till exempel [aiohttp,](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)använder du lämpligt kommando som startkommando eller i startkommandofilen:

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>Få åtkomst till appinställningar som miljövariabler

Appinställningar är värden som lagras i molnet specifikt för din app enligt beskrivningen i [Konfigurera appinställningar.](configure-common.md#configure-app-settings) De här inställningarna är tillgängliga för din appkod som miljövariabler och nås med hjälp av [standardmönstret os.environ.](https://docs.python.org/3/library/os.html#os.environ)

Om du till exempel har skapat appinställningen med namnet `DATABASE_SERVER` hämtar följande kod den inställningens värde:

```python
db_server = os.environ['DATABASE_SERVER']
```

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service [sker SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) (wikipedia.org) vid lastbalanserarna i nätverket, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din applogik behöver kontrollera om användarbegäranden är krypterade eller inte kan du kontrollera `X-Forwarded-Proto`-rubriken.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Med populära ramverk får du åtkomst till `X-Forwarded-*` information i standardappens mönster. I [CodeIgniter](https://codeigniter.com/) kontrollerar [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) värdet för `X_FORWARDED_PROTO` som standard.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Om du vill komma åt loggar Azure Portal väljer du  >  **Övervakningsloggström** på menyn till vänster för din app.

## <a name="access-deployment-logs"></a>Få åtkomst till distributionsloggar

När du distribuerar din kod App Service den byggprocess som beskrivs tidigare i avsnittet [Anpassa byggautomatisering](#customize-build-automation). Eftersom bygget körs i en egen container lagras byggloggarna separat från appens diagnostikloggar.

Använd följande steg för att komma åt distributionsloggarna:

1. På Azure Portal för webbappen väljer du  >  **Distributionsdistributionscenter (förhandsversion)** på den vänstra menyn.
1. På fliken **Loggar** väljer du Commit **ID (Genomförande-ID)** för den senaste genomfören.
1. På sidan **Logginformation** som visas väljer du länken **Visa loggar...** som visas bredvid "Kör oryx build...".

Problem med att skapa, till exempel *felaktiga beroenden irequirements.txt* och fel i för- eller efterbyggskript, visas i dessa loggar. Fel visas också om din kravfil inte har exakt *requirements.txt* namn eller inte visas i rotmappen i projektet.

## <a name="open-ssh-session-in-browser"></a>Öppna en SSH-session i webbläsaren

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

När du har anslutit till SSH-sessionen bör du se meddelandet "SSH CONNECTION ESTABLISHED" längst ned i fönstret. Om du ser fel som "SSH_CONNECTION_CLOSED" eller ett meddelande om att containern startas om kan ett fel hindra appcontainern från att starta. Se [Felsökning för](#troubleshooting) anvisningar för att undersöka möjliga problem.

## <a name="troubleshooting"></a>Felsökning

I allmänhet är det första steget i felsökning att använda App Service Diagnostics:

1. På Azure Portal för webbappen väljer du **Diagnostisera och lösa problem** på den vänstra menyn.
1. Välj **Tillgänglighet och prestanda.**
1. Granska informationen i alternativen **Programloggar,** **Containerkrasch** och **Containerproblem,** där de vanligaste problemen visas.

Granska sedan både [distributionsloggarna och](#access-deployment-logs) [apploggarna för](#access-diagnostic-logs) att se om det finns några felmeddelanden. De här loggarna identifierar ofta specifika problem som kan förhindra appdistribution eller appstart. Bygget kan till exempel misslyckas om *dinrequirements.txt* har fel filnamn eller inte finns i rotmappen för projektet.

Följande avsnitt innehåller ytterligare vägledning för specifika problem.

- [Appen visas inte – standardappen visas](#app-doesnt-appear)
- [Appen visas inte – meddelandet "tjänsten är inte tillgänglig"](#service-unavailable)
- [Det gick inte att hitta setup.py eller requirements.txt](#could-not-find-setuppy-or-requirementstxt)
- [ModuleNotFoundError vid start](#modulenotfounderror-when-app-starts)
- [Databasen är låst](#database-is-locked)
- [Lösenord visas inte i SSH-sessionen när de har skrivits](#other-issues)
- [Kommandon i SSH-sessionen verkar vara avklippta](#other-issues)
- [Statiska tillgångar visas inte i en Django-app](#other-issues)
- [Oåterkallelig SSL-anslutning krävs](#other-issues)

#### <a name="app-doesnt-appear"></a>Appen visas inte

- **Du ser standardappen när du har distribuerat din egen appkod.** [Standardappen](#default-behavior) visas eftersom du antingen inte har distribuerat din appkod till App Service eller App Service kunde inte hitta din appkod och körde standardappen i stället.

    - Starta om App Service, vänta 15–20 sekunder och kontrollera appen igen.
    
    - Se till att du använder App Service för Linux i stället för en Windows-baserad instans. Från Azure-CLI kör du kommandot `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind`. Byt ut `<resource-group-name>` och `<app-name>` därefter. Du bör se `app,linux` som utdata; annars återskapar du App Service och väljer Linux.
    
    - Använd [SSH](#open-ssh-session-in-browser) för att ansluta direkt App Service containern och kontrollera att dina filer finns under *site/wwwroot*. Om filerna inte finns kan du använda följande steg:
      1. Skapa en appinställning med namnet med värdet 1, distribuera om koden, vänta några minuter och `SCM_DO_BUILD_DURING_DEPLOYMENT` försök sedan komma åt appen igen. Mer information om hur du skapar appinställningar finns [i Konfigurera en App Service-app i Azure Portal](configure-common.md).
      1. Granska distributionsprocessen, [kontrollera distributionsloggarna,](#access-deployment-logs)korrigera eventuella fel och distribuera om appen.
    
    - Om filerna finns kunde App Service inte identifiera din specifika startfil. Kontrollera att din app är strukturerad som App Service förväntar sig för [Django](#django-app) [eller Flask,](#flask-app)eller använd ett [anpassat startkommando](#customize-startup-command).

- <a name="service-unavailable"></a>**Du ser meddelandet "Tjänsten är inte tillgänglig" i webbläsaren.** Webbläsaren har gått ut i väntan på svar från App Service, vilket betyder att App Service startade Gunicorn-servern, men själva appen startade inte. Det här villkoret kan tyda på att Gunicorn-argumenten är felaktiga eller att det finns ett fel i appkoden.

    - Uppdatera webbläsaren, särskilt om du använder de lägsta prisnivåerna i din App Service-plan. Till exempel kan appen ta längre tid att starta om du använder de kostnadsfria nivåerna och blir tillgänglig när du uppdaterar webbläsaren.

    - Kontrollera att din app är strukturerad som App Service förväntar sig för [Django](#django-app) [eller Flask,](#flask-app)eller använd ett [anpassat startkommando](#customize-startup-command).

    - Granska [apploggströmmen och](#access-diagnostic-logs) se om det finns några felmeddelanden. Loggarna visar eventuella fel i appkoden.

#### <a name="could-not-find-setuppy-or-requirementstxt"></a>Det gick inte att setup.py eller requirements.txt

- **Loggströmmen visar "Det gick inte att hitta setup.py eller requirements.txt; Kör inte pip install."**: Oryx-byggprocessen kunde inte hitta *dinrequirements.txtfil.*

    - Anslut till webbappens container via [SSH](#open-ssh-session-in-browser) och kontrollera *attrequirements.txt* har rätt namn och finns direkt under *site/wwwroot*. Om den inte finns gör du så att filen finns på lagringsplatsen och ingår i distributionen. Om den finns i en separat mapp flyttar du den till roten.

#### <a name="modulenotfounderror-when-app-starts"></a>ModuleNotFoundError när appen startas

Om du ser ett fel som `ModuleNotFoundError: No module named 'example'` , innebär det att Python inte kunde hitta en eller flera av dina moduler när programmet startades. Detta inträffar oftast om du distribuerar din virtuella miljö med din kod. Virtuella miljöer är inte portabla, så en virtuell miljö bör inte distribueras med programkoden. Låt i stället Oryx skapa en virtuell miljö och installera dina paket på webbappen genom att skapa en appinställning, , och `SCM_DO_BUILD_DURING_DEPLOYMENT` ange den till `1` . Detta tvingar Oryx att installera dina paket när du distribuerar till App Service. Mer information finns i den här [artikeln om portabilitet för virtuella miljöer.](https://azure.github.io/AppService/2020/12/11/cicd-for-python-apps.html)

### <a name="database-is-locked"></a>Databasen är låst

När du försöker köra databasmigrering med en Django-app kan du se "sqlite3. OperationalError: databasen är låst." Felet anger att programmet använder en SQLite-databas som Django är konfigurerat som standard för, i stället för att använda en molndatabas som PostgreSQL för Azure.

Kontrollera variabeln i appens fil settings.py att appen använder en `DATABASES` molndatabas i stället för SQLite. 

Om du stöter på det här felet med exemplet i Självstudie: Distribuera en [Django-webbapp med PostgreSQL](tutorial-python-postgresql-app.md)kontrollerar du att du har slutfört stegen i Konfigurera miljövariabler för att ansluta [databasen](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database).

#### <a name="other-issues"></a>Andra problem

- **Lösenord visas inte i SSH-sessionen** när du skriver : Av säkerhetsskäl håller SSH-sessionen lösenordet dolt när du skriver. Tecknen registreras, så skriv ditt lösenord som vanligt och tryck på **Retur när** du är klar.

- **Kommandon i SSH-sessionen verkar** vara avklippta: Redigeraren kanske inte är kommandon för radbrytning, men de bör fortfarande köras korrekt.

- **Statiska tillgångar visas inte i en Django-app:** Kontrollera att du har aktiverat [whitenmodulen](http://whitenoise.evans.io/en/stable/django.html)

- **Meddelandet "Oåterkallelig SSL-anslutning krävs"** visas: Kontrollera eventuella användarnamn och lösenord som används för att komma åt resurser (till exempel databaser) inifrån appen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Python-app med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Självstudie: Distribuera från privat containerdatabas](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service på Linux](faq-app-service-linux.md)
