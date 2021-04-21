---
title: 'Självstudie: Distribuera en Python Django-app med Postgres'
description: Skapa en Python-webbapp med en PostgreSQL-databas och distribuera den till Azure. I självstudien används Django-ramverket och appen finns på Azure App Service på Linux.
ms.devlang: python
ms.topic: tutorial
ms.date: 02/02/2021
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 882a9fb0f8d528ca21cdc8149c60b9d5bdaf1723
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767103"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Självstudie: Distribuera en Django-webbapp med PostgreSQL i Azure App Service

Den här självstudien visar hur du distribuerar en datadriven Python [Django-webbapp](https://www.djangoproject.com/) [till Azure App Service](overview.md) och ansluter den till en Azure Database for Postgres-databas. App Service en mycket skalbar och självkorrigeringstjänst för webbvärdtjänster.

I den här självstudien använder du Azure CLI för att utföra följande uppgifter:

> [!div class="checklist"]
> * Konfigurera din första miljö med Python och Azure CLI
> * Skapa en Azure Database for PostgreSQL databas
> * Distribuera kod till Azure App Service och ansluta till PostgreSQL
> * Uppdatera koden och distribuera om
> * Visa diagnostikloggar
> * Hantera webbappen i Azure Portal

Du kan också använda den Azure Portal [av den här självstudien.](/azure/developer/python/tutorial-python-postgresql-app-portal)


## <a name="1-set-up-your-initial-environment"></a>1. Konfigurera din första miljö

1. Ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
1. Installera <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 eller senare.</a>
1. Installera <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.18.0 eller senare, med vilken du kör kommandon i val annat gränssnitt för att etablera och konfigurera Azure-resurser.

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

Kontrollera att din Azure CLI-version är 2.18.0 eller senare:

```azurecli
az --version
```

Om du behöver uppgradera kan du prova kommandot `az upgrade` (kräver version 2.11+) eller se <a href="/cli/azure/install-azure-cli" target="_blank">Installera Azure CLI.</a>

Logga sedan in på Azure via CLI:

```azurecli
az login
```

Det här kommandot öppnar en webbläsare för att samla in dina autentiseringsuppgifter. När kommandot har avslutats visas JSON-utdata som innehåller information om dina prenumerationer.

När du har loggat in kan du köra Azure-kommandon med Azure CLI för att arbeta med resurser i din prenumeration.

Har du problem? [Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="2-clone-or-download-the-sample-app"></a>2. Klona eller ladda ned exempelappen

# <a name="git-clone"></a>[Git-klon](#tab/clone)

Klona exempeldatabasen:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Navigera sedan till den mappen:

```terminal
cd djangoapp
```

# <a name="download"></a>[Ladda ned](#tab/download)

Besök [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) , välj **Klona** och välj sedan **Ladda ned ZIP.** 

Packa upp ZIP-filen i en mapp med *namnet djangoapp*. 

Öppna sedan ett terminalfönster i mappen *djangoapp.*

---

Djangoapp-exemplet innehåller den datadrivna Django-avsökningsapp som du får genom att följa Skriva din första [Django-app](https://docs.djangoproject.com/en/3.1/intro/tutorial01/) i Django-dokumentationen. Den färdiga appen finns här för din bekvämlighet.

Exemplet ändras också för att köras i en produktionsmiljö som App Service:

- Produktionsinställningarna finns i *filen azuresite/production.py.* Utvecklingsinställningarna finns i *azuresite/settings.py.*
- Appen använder produktionsinställningar när `WEBSITE_HOSTNAME` miljövariabeln anges. Azure App Service anger automatiskt den här variabeln till webbappens URL, till exempel `msdocs-django.azurewebsites.net` .

Produktionsinställningarna är specifika för att konfigurera Django att köras i valfri produktionsmiljö och är inte specifika för App Service. Mer information finns i checklistan [för Django-distribution.](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) Se även [Produktionsinställningar för Django på Azure](configure-language-python.md#production-settings-for-django-apps) för mer information om några av ändringarna.

Har du problem? [Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="3-create-postgres-database-in-azure"></a>3. Skapa En Postgres-databas i Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

Installera `db-up` tillägget för Azure CLI:

```azurecli
az extension add --name db-up
```

Om kommandot `az` inte känns igen kontrollerar du att du har Installerat Azure CLI enligt beskrivningen [i Konfigurera din första miljö.](#1-set-up-your-initial-environment)

Skapa sedan Postgres-databasen i Azure med [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) kommandot :

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- **Ersätt** *\<postgres-server-name>* med ett namn som är unikt **i hela Azure** (serverslutpunkten blir `https://<postgres-server-name>.postgres.database.azure.com` ). Ett bra mönster är att använda en kombination av företagets namn och ett annat unikt värde.
- För *\<admin-username>* och anger du *\<admin-password>* autentiseringsuppgifter för att skapa en administratörsanvändare för den här Postgres-servern. Användarnamnet för administratören kan inte *azure_superuser*, *azure_pg_admin,* *admin,* *administratör,* *rot,* *gäst* eller *offentlig*. Det kan inte börja med *att pg_*. Lösenordet måste innehålla 8 till **128** tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0 till 9) och icke-alfanumeriska tecken (till exempel !, #, %). Lösenordet får inte innehålla användarnamn.
- Använd inte tecknet `$` i användarnamnet eller lösenordet. Senare skapar du miljövariabler med dessa värden där tecknet `$` har särskild betydelse i Linux-containern som används för att köra Python-appar.
- Prisnivån B_Gen5_1 (Basic, Gen5, 1 [kärna)](../postgresql/concepts-pricing-tiers.md) som används här är den billigaste. För produktionsdatabaser utelämnar du argumentet för `--sku-name` att GP_Gen5_2 (Generell användning, Gen 5, 2 kärnor) i stället.

Det här kommandot utför följande åtgärder, vilket kan ta några minuter:

- Skapa en [resursgrupp](../azure-resource-manager/management/overview.md#terminology) med `DjangoPostgres-tutorial-rg` namnet , om den inte redan finns.
- Skapa en Postgres-server med namnet av `--server-name` argumentet .
- Skapa ett administratörskonto med `--admin-user` argumenten `--admin-password` och . Du kan utelämna de här argumenten så att kommandot kan generera unika autentiseringsuppgifter åt dig.
- Skapa en `pollsdb` databas med namnet av argumentet `--database-name` .
- Aktivera åtkomst från din lokala IP-adress.
- Aktivera åtkomst från Azure-tjänster.
- Skapa en databasanvändare med åtkomst till `pollsdb` databasen.

Du kan göra alla steg separat med andra `az postgres` kommandon och , men gör alla steg `psql` `az postgres up` tillsammans.

När kommandot har slutförts matar det ut ett JSON-objekt som innehåller olika anslutningssträngar för databasen tillsammans med server-URL:en, ett genererat användarnamn (till exempel " ") och joyfulKoala@msdocs-djangodb-12345 ett GUID-lösenord. **Kopiera användarnamnet och lösenordet till en temporär textfil eftersom du** behöver dem senare i den här självstudien.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`, kan anges till vilken som helst av [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/). Du kan hämta de regioner som är tillgängliga för din prenumeration med [`az account list-locations`](/cli/azure/account#az_account_list_locations) kommandot . För produktionsappar ska du placera din databas och din app på samma plats.

Har du problem? [Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="4-deploy-the-code-to-azure-app-service"></a>4. Distribuera koden till Azure App Service

I det här avsnittet skapar du en appvärd i App Service app, ansluter den här appen till Postgres-databasen och distribuerar sedan koden till den värden.

### <a name="41-create-the-app-service-app"></a>4.1 Skapa App Service appen

Kontrollera att du är i mappen *djangoapp-lagringsplats* som innehåller appkoden i terminalen.

Skapa en App Service -app (värdprocessen) med [`az webapp up`](/cli/azure/webapp#az_webapp_up) kommandot :

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- För `--location` argumentet använder du samma plats som du gjorde för databasen i föregående avsnitt.
- **Ersätt** *\<app-name>* med ett unikt namn i hela Azure (serverslutpunkten är `https://<app-name>.azurewebsites.net` ). Tillåtna tecken *\<app-name>* för `A` - `Z` är , `0` - `9` och `-` . Ett bra mönster är att använda en kombination av företagets namn och en appidentifierare.

Det här kommandot utför följande åtgärder, vilket kan ta några minuter:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Skapa [resursgruppen](../azure-resource-manager/management/overview.md#terminology) om den inte redan finns. (I det här kommandot använder du samma resursgrupp som du skapade databasen i tidigare.)
- Skapa [App Service plan](overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* på prisnivån Basic (B1), om den inte finns. `--plan` och `--sku` är valfria.
- Skapa App Service app om den inte finns.
- Aktivera standardloggning för appen, om det inte redan är aktiverat.
- Ladda upp lagringsplatsen med ZIP-distribution med byggautomatisering aktiverat.
- Cachelagra vanliga parametrar, till exempel namnet på resursgruppen och App Service plan, i *filen .azure/config*. Därför behöver du inte ange samma parameter med senare kommandon. Om du till exempel vill distribuera om appen när du har gjort ändringar kan du bara köra `az webapp up` igen utan några parametrar. Kommandon som kommer från CLI-tillägg, till exempel , använder dock för närvarande inte cacheminnet, vilket är anledningen till att du behövde ange resursgruppen och platsen här med `az postgres up` den inledande användningen av `az webapp up` .

Vid lyckad distribution genererar kommandot JSON-utdata som i följande exempel:

![Exempel på utdata från az webapp up command](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

Har du problem? Läs först i [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="42-configure-environment-variables-to-connect-the-database"></a>4.2 Konfigurera miljövariabler för att ansluta databasen

När koden nu distribueras till App Service är nästa steg att ansluta appen till Postgres-databasen i Azure.

Appkoden förväntar sig att hitta databasinformation i fyra miljövariabler med `DBHOST` namnet , , och `DBNAME` `DBUSER` `DBPASS` .

Om du vill ange miljövariabler App Service skapar du "appinställningar" med följande [az webapp config appsettings set-kommando.](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set)

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- Ersätt *\<postgres-server-name>* med det namn som du använde tidigare med kommandot `az postgres up` . Koden i *azuresite/production.py läggs* automatiskt till för att `.postgres.database.azure.com` skapa den fullständiga Postgres-server-URL:en.
- Ersätt *\<username>* och *\<password>* med administratörsautentiseringsuppgifterna som du använde med `az postgres up` det tidigare kommandot, eller de som `az postgres up` genererades åt dig. Koden i *azuresite/production.py* skapar automatiskt det fullständiga Postgres-användarnamnet från och , så `DBUSER` ta inte med `DBHOST` `@server` delen. (Som tidigare nämnts bör du inte heller använda tecknet i något av dessa värden eftersom det har en `$` särskild betydelse för Linux-miljövariabler.)
- Resursgruppen och appnamnen hämtas från de cachelagrade värdena i *.azure/config-filen.*

I Din Python-kod kommer du åt de här inställningarna som miljövariabler med instruktioner som `os.environ.get('DBHOST')` . Mer information finns i [Åtkomst till miljövariabler.](configure-language-python.md#access-environment-variables)

Har du problem? Läs först i [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="43-run-django-database-migrations"></a>4.3 Kör Django-databasmigrering

Django-databasmigreringarna ser till att schemat i PostgreSQL på Azure-databasen matchar de som beskrivs i koden.

1. Öppna en SSH-session **i webbläsaren genom** att gå till följande URL och logga in med autentiseringsuppgifterna för ditt Azure-konto (inte autentiseringsuppgifterna för databasservern).

    ```
    https://<app-name>.scm.azurewebsites.net/webssh/host
    ```

    Ersätt `<app-name>` med det namn som användes tidigare i `az webapp up` kommandot.

    Du kan alternativt ansluta till en SSH-session med [`az webapp ssh`](/cli/azure/webapp#az_webapp_ssh) kommandot . I Windows kräver det här kommandot Azure CLI 2.18.0 eller senare.

    Om du inte kan ansluta till SSH-sessionen har det inte gått att starta själva appen. [Mer information finns i](#6-stream-diagnostic-logs) diagnostikloggarna. Om du till exempel inte har skapat de nödvändiga appinställningarna i föregående avsnitt visar loggarna `KeyError: 'DBNAME'` .

1. I SSH-sessionen kör du följande kommandon (du kan klistra in kommandon med **Ctrl** + **Skift** + **V**):

    ```bash
    # Change to the app folder
    cd $APP_PATH
    
    # Activate the venv
    source /antenv/bin/activate

    # Install requirements
    pip install -r requirements.txt

    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

    Om du stöter på fel som rör anslutning till databasen kontrollerar du värdena för de programinställningar som skapades i föregående avsnitt.

1. Kommandot `createsuperuser` uppmanar dig att ange autentiseringsuppgifter för superanvändare. I den här självstudien använder du standardnamnet , trycker på Retur för e-postadressen för att lämna den tom `root` och anger som  `Pollsdb1` lösenord.

1. Om du ser ett fel om att databasen är låst kontrollerar du att du körde `az webapp settings` kommandot i föregående avsnitt. Utan dessa inställningar kan migreringskommandot inte kommunicera med databasen, vilket resulterar i felet.

Har du problem? Gå först till [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss](https://aka.ms/DjangoCLITutorialHelp).
    
### <a name="44-create-a-poll-question-in-the-app"></a>4.4 Skapa en omröstningsfråga i appen

1. Öppna URL:en i en `http://<app-name>.azurewebsites.net` webbläsare. Appen bör visa meddelandet "Appen Avsöker" och "Inga omröstningar är tillgängliga" eftersom det inte finns några specifika omröstningar ännu i databasen.

    Om du ser "Programfel" är det troligt att du antingen inte skapade de obligatoriska inställningarna i föregående [steg,](#42-configure-environment-variables-to-connect-the-database)Konfigurera miljövariabler för att ansluta databasen eller att dessa värden innehåller fel. Kör kommandot `az webapp config appsettings list` för att kontrollera inställningarna. Du kan också [kontrollera diagnostikloggarna för](#6-stream-diagnostic-logs) att se specifika fel när appen startas. Om du till exempel inte har skapat inställningarna visar loggarna felet `KeyError: 'DBNAME'` .

    När du har uppdaterat inställningarna för att åtgärda eventuella fel ger du appen en minut för att starta om och uppdaterar sedan webbläsaren.

1. Gå till `http://<app-name>.azurewebsites.net/admin`. Logga in med Django superuser-autentiseringsuppgifter från föregående avsnitt ( `root` och `Pollsdb1` ). Under Omröstningar väljer du **Lägg till** bredvid **Frågor och** skapar en **omröstningsfråga** med vissa alternativ.

1. Bläddra igen `http://<app-name>.azurewebsites.net` till för att bekräfta att frågorna nu visas för användaren. Besvara frågor men du vill generera data i databasen.

**Grattis!** Du kör en Python Django-webbapp i Azure App Service För Linux, med en aktiv Postgres-databas.

Har du problem? [Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> App Service identifierar ett Django-projekt genom att leta *efter en wsgi.py* i varje undermapp, vilket skapas som `manage.py startproject` standard. När App Service hittar filen läses Django-webbappen in. Mer information finns i Konfigurera [den inbyggda Python-avbildningen](configure-language-python.md).

## <a name="5-make-code-changes-and-redeploy"></a>5. Gör kodändringar och distribuera om

I det här avsnittet gör du lokala ändringar i appen och distribuerar om koden till App Service. I processen ställer du in en virtuell Python-miljö som stöder pågående arbete.

### <a name="51-run-the-app-locally"></a>5.1 Kör appen lokalt

Kör följande kommandon i ett terminalfönster. Se till att följa anvisningarna när du skapar superanvändaren:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install dependencies
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install dependencies
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

När webbappen har lästs in fullständigt tillhandahåller Django-utvecklingsservern den lokala app-URL:en i meddelandet "Startar utvecklingsservern på http://127.0.0.1:8000/ . Avsluta servern med CTRL-BREAK".

![Exempel på utdata för Django-utvecklingsserver](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Testa appen lokalt med följande steg:

1. Gå till `http://localhost:8000` i en webbläsare, som ska visa meddelandet "Inga omröstningar är tillgängliga". 

1. Gå till `http:///localhost:8000/admin` och logga in med den administratörsanvändare som du skapade tidigare. Under Omröstningar väljer du återigen **Lägg** till bredvid **Frågor och** skapar en **omröstningsfråga** med några alternativ. 

1. Gå till *http: \/ /localhost:8000* igen och besvara frågan för att testa appen. 

1. Stoppa Django-servern genom att trycka **på** + **Ctrl C**.

När appen körs lokalt använder den en lokal Sqlite3-databas och stör inte produktionsdatabasen. Du kan också använda en lokal PostgreSQL-databas om du vill för att simulera din produktionsmiljö på ett bättre sätt.

Har du problem? [Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="52-update-the-app"></a>5.2 Uppdatera appen

I `polls/models.py` letar du upp den rad som börjar med och ändrar `choice_text` `max_length` parametern till 100:

```python
# Find this line of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Eftersom du ändrade datamodellen skapar du en ny Django-migrering och migrerar databasen:

```
python manage.py makemigrations
python manage.py migrate
```

Kör utvecklingsservern igen med `python manage.py runserver` och testa appen på till *http: \/ /localhost:8000/admin*:

Stoppa Django-webbservern igen med **Ctrl** + **C**.

Har du problem? Gå först till [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss](https://aka.ms/DjangoCLITutorialHelp).

### <a name="53-redeploy-the-code-to-azure"></a>5.3 Distribuera om koden till Azure

Kör följande kommando i lagringsplatsens rot:

```azurecli
az webapp up
```

Det här kommandot använder de parametrar som cachelagras *i .azure/config-filen.* Eftersom App Service upptäcker att appen redan finns distribuerar den bara om koden.

Har du problem? Läs först i [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="54-rerun-migrations-in-azure"></a>5.4 Kör om migreringar i Azure

Eftersom du har gjort ändringar i datamodellen måste du köra databasmigreringarna på App Service.

Öppna en SSH-session igen i webbläsaren genom att gå till `https://<app-name>.scm.azurewebsites.net/webssh/host` . Kör sedan följande kommandon:

```
cd $APP_PATH
source /antenv/bin/activate
pip install -r requirements.txt
python manage.py migrate
```

Har du problem? Läs först i [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="55-review-app-in-production"></a>5.5 Granska appen i produktion

Bläddra till `http://<app-name>.azurewebsites.net` och testa appen igen i produktion. (Eftersom du bara ändrade längden på ett databasfält är ändringen bara märkbar om du försöker ange ett längre svar när du skapar en fråga.)

Har du problem? Läs först i [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="6-stream-diagnostic-logs"></a>6. Strömma diagnostikloggar

Du kan komma åt konsolloggarna som genereras inifrån containern som är värd för appen i Azure.

Kör följande Azure CLI-kommando för att se loggströmmen. Det här kommandot använder parametrar som cachelagras *i .azure/config-filen.*

```azurecli
az webapp log tail
```

Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

Om du vill stoppa loggströmningen när som helst skriver du **Ctrl** + **C**.

Har du problem? [Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> Du kan även granska loggfilerna från din webbläsare via `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
>
> `az webapp up` aktiverar standardloggningen åt dig. Av prestandaskäl stängs den här loggningen av efter en stund, men sätts på igen varje gång du kör `az webapp up` igen. Kör följande kommando för att aktivera det manuellt:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="7-manage-your-app-in-the-azure-portal"></a>7. Hantera din app i Azure Portal

I [Azure Portal](https://portal.azure.com)söker du efter appnamnet och väljer appen i resultatet.

![Gå till din Python Django-app i Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Som standard visar portalen appens **översiktssida,** som ger en allmän prestandavy. Här kan du också utföra grundläggande hanteringsuppgifter som att bläddra, stoppa, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![Hantera din Python Django-app på översiktssidan i Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

Har du problem? Gå först till [felsökningsguiden,](configure-language-python.md#troubleshooting)annars kan [du berätta för oss](https://aka.ms/DjangoCLITutorialHelp).

## <a name="8-clean-up-resources"></a>8. Rensa resurser

Om du vill behålla appen eller fortsätta till de ytterligare självstudierna kan du gå vidare till [Nästa steg.](#next-steps) Annars kan du ta bort resursgruppen som skapas för den här självstudien för att undvika löpande avgifter:

```azurecli
az group delete --no-wait
```

Kommandot använder resursgruppens namn som cachelagras i *.azure/config-filen.* Genom att ta bort resursgruppen kan du också allokera och ta bort alla resurser som ingår i den.

Det kan ta lite tid att ta bort alla resurser. Argumentet `--no-wait` gör att kommandot kan returnera omedelbart.

Har du problem? [Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du mappar ett anpassat DNS-namn till din app:

> [!div class="nextstepaction"]
> [Självstudie: Mappa anpassat DNS-namn till din app](app-service-web-tutorial-custom-domain.md)

Lär dig App Service kör en Python-app:

> [!div class="nextstepaction"]
> [Konfigurera Python-app](configure-language-python.md)
