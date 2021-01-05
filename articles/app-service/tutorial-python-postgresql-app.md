---
title: 'Självstudie: Distribuera en python django-app med postgres'
description: Skapa en python-webbapp med en PostgreSQL-databas och distribuera den till Azure. I självstudien används django-ramverket och appen finns på Azure App Service på Linux.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/04/2021
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: ffde74a0567661d6b9f77e45a80bfd585e5c7212
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898597"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Själv studie kurs: Distribuera en django-webbapp med PostgreSQL i Azure App Service

Den här självstudien visar hur du distribuerar en data driven python [django](https://www.djangoproject.com/) -webbapp för att [Azure App Service](overview.md) och ansluta den till en Azure Database för postgres-databas. App Service ger en mycket skalbar och automatisk korrigering av webb värd tjänst.

I den här självstudien använder du Azure CLI för att utföra följande uppgifter:

> [!div class="checklist"]
> * Konfigurera din första miljö med python och Azure CLI
> * Skapa en Azure Database for PostgreSQL databas
> * Distribuera kod till Azure App Service och ansluta till PostgreSQL
> * Uppdatera din kod och distribuera om
> * Visa diagnostikloggar
> * Hantera webbappen i Azure Portal

Du kan också använda [Azure Portal versionen av den här själv studie kursen](/azure/developer/python/tutorial-python-postgresql-app-portal).


## <a name="1-set-up-your-initial-environment"></a>1. Konfigurera din första miljö

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

Har du problem? [Berätta för oss](https://aka.ms/DjangoCLITutorialHelp).

## <a name="2-clone-or-download-the-sample-app"></a>2. klona eller ladda ned exempel appen

# <a name="git-clone"></a>[Git-klon](#tab/clone)

Klona exempel lagrings platsen:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Navigera sedan till mappen:

```terminal
cd djangoapp
```

# <a name="download"></a>[Ladda ned](#tab/download)

Besök [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) , Välj **klona** och välj sedan **Hämta zip**. 

Packa upp ZIP-filen i en mapp med namnet *djangoapp*. 

Öppna sedan ett terminalfönster i den *djangoapp* -mappen.

---

Djangoapp-exemplet innehåller den data drivna django avsöknings appen som du får genom att följa de [första django-apparna](https://docs.djangoproject.com/en/3.1/intro/tutorial01/) i Django-dokumentationen. Den färdiga appen tillhandahålls här för din bekvämlighet.

Exemplet ändras också till att köras i en produktions miljö som App Service:

- Produktions inställningarna finns i filen *azuresite/productation. py* . Utvecklings inställningarna är i *azuresite/Settings. py*.
- Appen använder produktions inställningar när `WEBSITE_HOSTNAME` miljövariabeln har angetts. Azure App Service anger automatiskt den här variabeln till webbappens webb adress, till exempel `msdocs-django.azurewebsites.net` .

Produktions inställningarna är specifika för att konfigurera django som ska köras i en produktions miljö och är inte särskilt App Service. Mer information finns i [Check lista för django-distribution](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/). Se även [produktions inställningar för Django på Azure](configure-language-python.md#production-settings-for-django-apps) för information om några av ändringarna.

Har du problem? [Berätta för oss](https://aka.ms/DjangoCLITutorialHelp).

## <a name="3-create-postgres-database-in-azure"></a>3. skapa postgres-databas i Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

Installera `db-up` tillägget för Azure CLI:

```azurecli
az extension add --name db-up
```

Om `az` kommandot inte känns igen kontrollerar du att du har installerat Azure CLI enligt beskrivningen i [Konfigurera din inledande miljö](#1-set-up-your-initial-environment).

Skapa sedan postgres-databasen i Azure med [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) kommandot:

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- **Ersätt** *\<postgres-server-name>* med ett namn som är **unikt för alla Azure** (Server slut punkten blir `https://<postgres-server-name>.postgres.database.azure.com` ). Ett utmärkt mönster är att använda en kombination av ditt företags namn och ett annat unikt värde.
- För *\<admin-username>* och *\<admin-password>* anger du autentiseringsuppgifter för att skapa en administratörs användare för den här postgres-servern. Administratörens användar namn får inte vara *azure_superuser*, *azure_pg_admin*, *admin*, *administratör*, *rot*, *gäst* eller *offentlig*. Den kan inte börja med *PG_*. Lösen ordet måste innehålla **8 till 128 tecken** från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0 till 9) och icke-alfanumeriska tecken (till exempel!, #,%). Lösen ordet får inte innehålla användar namn.
- Använd inte `$` tecknen i användar namnet eller lösen ordet. Senare skapar du miljövariabler med dessa värden där det `$` finns en speciell innebörd i Linux-behållaren som används för att köra python-appar.
- [Pris nivån](../postgresql/concepts-pricing-tiers.md) B_Gen5_1 (Basic, Gen5, 1 kärna) som används här är den minst dyra. För produktions databaser utelämnar du `--sku-name` argumentet för att använda GP_Gen5_2-nivån (generell användning, generation 5, 2 kärnor) i stället.

Detta kommando utför följande åtgärder, vilket kan ta några minuter:

- Skapa en [resurs grupp](../azure-resource-manager/management/overview.md#terminology) med namnet `DjangoPostgres-tutorial-rg` , om den inte redan finns.
- Skapa en postgres-server med namnet som `--server-name` argument.
- Skapa ett administratörs konto med hjälp av `--admin-user` `--admin-password` argumenten och. Du kan utelämna dessa argument för att tillåta att kommandot genererar unika autentiseringsuppgifter åt dig.
- Skapa en `pollsdb` databas som namngetts av `--database-name` argumentet.
- Aktivera åtkomst från din lokala IP-adress.
- Aktivera åtkomst från Azure-tjänster.
- Skapa en databas användare med åtkomst till `pollsdb` databasen.

Du kan utföra alla steg separat med andra `az postgres` och `psql` -kommandon, men `az postgres up` gör alla steg tillsammans.

När kommandot har slutförts, matar den ut ett JSON-objekt som innehåller olika anslutnings strängar för databasen tillsammans med Server-URL, ett genererat användar namn (t. ex. " joyfulKoala@msdocs-djangodb-12345 ") och ett GUID-lösenord. **Kopiera användar namnet och lösen ordet till en tillfällig textfil** när du behöver dem senare i den här självstudien.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`, kan ställas in på en av [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/). Du kan hämta de regioner som är tillgängliga för din prenumeration med [`az account list-locations`](/cli/azure/account#az-account-list-locations) kommandot. För produktion av appar sätter du din databas och din app på samma plats.

Har du problem? [Berätta för oss](https://aka.ms/DjangoCLITutorialHelp).

## <a name="4-deploy-the-code-to-azure-app-service"></a>4. distribuera koden till Azure App Service

I det här avsnittet skapar du app Host i App Service app, ansluter den här appen till postgres-databasen och distribuerar sedan koden till den värden.

### <a name="41-create-the-app-service-app"></a>4,1 Skapa App Service-appen

I terminalen ser du till att du befinner dig i *djangoapp* -lagringsplatsen som innehåller appens kod.

Skapa en App Service app (värd processen) med [`az webapp up`](/cli/azure/webapp#az-webapp-up) kommandot:

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- För `--location` argumentet använder du samma plats som du gjorde för databasen i föregående avsnitt.
- **Ersätt** *\<app-name>* med ett unikt namn på alla Azure (Server slut punkten är `https://<app-name>.azurewebsites.net` ). Tillåtna tecken för *\<app-name>* är `A` - `Z` , `0` - `9` och `-` . Ett utmärkt mönster är att använda en kombination av företagets namn och en app-ID.

Detta kommando utför följande åtgärder, vilket kan ta några minuter:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Skapa [resurs gruppen](../azure-resource-manager/management/overview.md#terminology) om den inte redan finns. (I det här kommandot använder du samma resurs grupp där du skapade databasen tidigare.)
- Skapa [App Service plan](overview-hosting-plans.md) *DjangoPostgres-självstudie – planera* i den grundläggande pris nivån (B1) om den inte finns. `--plan` och `--sku` är valfria.
- Skapa App Service-appen om den inte finns.
- Aktivera standard loggning för appen om den inte redan är aktive rad.
- Ladda upp lagrings platsen med hjälp av ZIP-distribution med build Automation aktiverat.
- Cachelagra vanliga parametrar, till exempel namnet på resurs gruppen och App Service plan, i filen *. Azure/config*. Därför behöver du inte ange samma parameter med senare kommandon. Om du till exempel vill distribuera om appen när du har gjort ändringar kan du bara köra `az webapp up` igen utan parametrar. Kommandon som kommer från CLI-tillägg, till exempel, `az postgres up` används inte för närvarande, vilket är anledningen till att du måste ange resurs gruppen och platsen här med den inledande användningen av `az webapp up` .

Vid lyckad distribution genererar kommandot JSON-utdata som i följande exempel:

![Exempel på AZS webapp-utdata](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/DjangoCLITutorialHelp).

### <a name="42-configure-environment-variables-to-connect-the-database"></a>4,2 Konfigurera miljövariabler för att ansluta databasen

Med koden nu distribuerad till App Service är nästa steg att ansluta appen till postgres-databasen i Azure.

App-koden förväntar sig att hitta databas information i fyra miljövariabler med namnet `DBHOST` ,, `DBNAME` `DBUSER` och `DBPASS` .

Om du vill ange miljövariabler i App Service skapar du "appinställningar" med följande kommando för [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) .

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- Ersätt *\<postgres-server-name>* med det namn som du använde tidigare med `az postgres up` kommandot. Koden i *azuresite/Product. py* lägger automatiskt `.postgres.database.azure.com` till för att skapa den fullständiga postgres-serverns URL.
- Ersätt *\<username>* och *\<password>* med de administratörsautentiseringsuppgifter som du använde med det tidigare `az postgres up` kommandot eller de som `az postgres up` genererats åt dig. Koden i *azuresite/Product. py* skapar automatiskt det fullständiga postgres-användarnamnet från `DBUSER` och `DBHOST` , så inkluderar inte `@server` delen. (Som vi nämnt tidigare bör du inte använda `$` tecknen i något värde eftersom det har en särskild betydelse för Linux-miljövariabler.)
- Resurs gruppen och app-namnen hämtas från de cachelagrade värdena i *Azure/config-* filen.

I python-koden får du åtkomst till de här inställningarna som miljövariabler med uttryck som `os.environ.get('DBHOST')` . Mer information finns i [Access Environment-variabler](configure-language-python.md#access-environment-variables).

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/DjangoCLITutorialHelp).

### <a name="43-run-django-database-migrations"></a>4,3 kör django Database-migreringar

Django Database-migreringar ser till att schemat i PostgreSQL i Azure Database matchar de som beskrivs i din kod.

1. Öppna en SSH-session **i webbläsaren** genom att gå till följande URL och logga in med dina autentiseringsuppgifter för Azure-kontot (inte databas serverns autentiseringsuppgifter).

    ```
    https://<app-name>.scm.azurewebsites.net/webssh/host
    ```

    Ersätt `<app-name>` med det namn som användes tidigare i `az webapp up` kommandot.

    På macOS och Linux kan du också ansluta till en SSH-session med [`az webapp ssh`](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az_webapp_ssh) kommandot.

    Om du inte kan ansluta till SSH-sessionen har själva appen inte startats. Mer information [finns i diagnostikloggar](#6-stream-diagnostic-logs) . Om du till exempel inte har skapat de nödvändiga inställningarna för appen i föregående avsnitt, visar loggarna `KeyError: 'DBNAME'` .

1. Kör följande kommandon i SSH-sessionen (du kan klistra in kommandon med **CTRL** + **Shift** + **V**):

    ```bash
    # Change to the app folder
    cd $APP_PATH
    
    # Activate the venv (requirements.txt is installed automatically)
    source /antenv/bin/activate

    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

    Om du stöter på fel som rör anslutning till databasen, kontrollerar du värdena för de program inställningar som skapades i föregående avsnitt.

1. `createsuperuser`Kommandot efterfrågar behörigheter för superanvändare. I den här självstudien använder du standard användar namnet `root` , trycker på **RETUR** för e-postadressen för att lämna den tom och anger `Pollsdb1` lösen ordet.

1. Om du ser ett fel som databasen är låst, se till att du körde `az webapp settings` kommandot i föregående avsnitt. Utan de här inställningarna kan inte Migrate-kommandot kommunicera med databasen, vilket resulterar i felet.

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/DjangoCLITutorialHelp).
    
### <a name="44-create-a-poll-question-in-the-app"></a>4,4 skapa en avsöknings fråga i appen

1. Öppna webb adressen i en webbläsare `http://<app-name>.azurewebsites.net` . Appen ska visa meddelandet "avsöknings app" och "inga avsökningar är tillgängliga" eftersom det inte finns några angivna avsökningar ännu i databasen.

    Om du ser "program fel" är det troligt att du antingen inte har skapat de nödvändiga inställningarna i föregående steg, [konfigurerat miljövariabler för att ansluta databasen](#42-configure-environment-variables-to-connect-the-database)eller att dessa värden innehåller fel. Kör kommandot `az webapp config appsettings list` för att kontrol lera inställningarna. Du kan också [kontrol lera diagnostikloggar](#6-stream-diagnostic-logs) för att se vissa fel under appens start. Om du till exempel inte har skapat inställningarna visas fel meddelandet i loggarna `KeyError: 'DBNAME'` .

    När du har uppdaterat inställningarna för att korrigera eventuella fel, ge appen en minut för att starta om och uppdatera sedan webbläsaren.

1. Gå till `http://<app-name>.azurewebsites.net/admin`. Logga in med django superusers-autentiseringsuppgifter från föregående avsnitt ( `root` och `Pollsdb1` ). Under **avsökningar** väljer du **Lägg till** bredvid **frågor** och skapar en avsöknings fråga med några val.

1. Bläddra igen för `http://<app-name>.azurewebsites.net` att bekräfta att frågorna nu visas för användaren. Besvara frågor som du vill skapa vissa data i databasen.

**Grattis!** Du kör en python django-webbapp i Azure App Service för Linux med en aktiv postgres-databas.

Har du problem? [Berätta för oss](https://aka.ms/DjangoCLITutorialHelp).

> [!NOTE]
> App Service identifierar ett django-projekt genom att söka efter en *wsgi.py* -fil i varje undermapp som `manage.py startproject` skapas som standard. När App Service hittar filen laddas django-webbappen. Mer information finns i [Konfigurera inbyggd python-avbildning](configure-language-python.md).

## <a name="5-make-code-changes-and-redeploy"></a>5. gör kod ändringar och distribuera om

I det här avsnittet ska du göra lokala ändringar i appen och distribuera om koden till App Service. I processen ställer du in en virtuell python-miljö som har stöd för pågående arbete.

### <a name="51-run-the-app-locally"></a>5,1 kör appen lokalt

Kör följande kommandon i ett terminalfönster. Se till att följa anvisningarna när du skapar superanvändaren:

# <a name="bash"></a>[bash](#tab/bash)

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

# <a name="cmd"></a>[KOMMANDOT](#tab/cmd)

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

När webbappen har lästs in är django-utvecklings servern den lokala appens URL i meddelandet "startar utvecklings servern på http://127.0.0.1:8000/ . Avsluta servern med CTRL-BREAK ".

![Exempel på utdata för django Development Server](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Testa appen lokalt med följande steg:

1. Gå till `http://localhost:8000` i en webbläsare, som visar meddelandet "det finns inga avsökningar". 

1. Gå till `http:///localhost:8000/admin` och logga in med administratörs användaren som du skapade tidigare. Under **avsökningar** väljer du **Lägg till** bredvid **frågor** och skapar en avsöknings fråga med några val. 

1. Gå till *http: \/ /localhost: 8000* igen och besvara frågan för att testa appen. 

1. Stoppa django-servern genom att trycka på **CTRL** + **C**.

När du kör lokalt använder appen en lokal sqlite3-databas och stör inte din produktions databas. Du kan också använda en lokal PostgreSQL-databas, om du vill, för att bättre simulera produktions miljön.

Har du problem? [Berätta för oss](https://aka.ms/DjangoCLITutorialHelp).

### <a name="52-update-the-app"></a>5,2 uppdatera appen

I `polls/models.py` , letar du upp raden som börjar med `choice_text` och ändrar `max_length` parametern till 100:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Eftersom du har ändrat data modellen, skapar du en ny django-migrering och migrerar databasen:

```
python manage.py makemigrations
python manage.py migrate
```

Kör utvecklings servern igen med `python manage.py runserver` och testa appen på *http: \/ /localhost: 8000/admin*:

Stoppa django-webbservern igen med **CTRL** + **+ C**.

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/DjangoCLITutorialHelp).

### <a name="53-redeploy-the-code-to-azure"></a>5,3 distribuera om koden till Azure

Kör följande kommando i databas roten:

```azurecli
az webapp up
```

Det här kommandot använder parametrarna som är cachelagrade i *. Azure/config-* filen. Eftersom App Service upptäcker att appen redan finns, distribuerar den bara om koden.

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/DjangoCLITutorialHelp).

### <a name="54-rerun-migrations-in-azure"></a>5,4 köra om migreringar i Azure

Eftersom du har gjort ändringar i data modellen måste du köra om migreringen av databasen i App Service.

Öppna en SSH-session igen i webbläsaren genom att gå till `https://<app-name>.scm.azurewebsites.net/webssh/host` . Kör sedan följande kommandon:

```
cd $APP_PATH
source /antenv/bin/activate
python manage.py migrate
```

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/DjangoCLITutorialHelp).

### <a name="55-review-app-in-production"></a>5,5 granska app i produktion

Bläddra till `http://<app-name>.azurewebsites.net` och testa appen igen i produktion. (Eftersom du bara ändrade längden på ett databas fält är ändringen bara märkbart om du försöker ange ett längre svar när du skapar en fråga.)

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/DjangoCLITutorialHelp).

## <a name="6-stream-diagnostic-logs"></a>6. strömmar med diagnostikloggar

Du kan komma åt konsol loggar som genereras inifrån den behållare som är värd för appen på Azure.

Kör följande Azure CLI-kommando för att se logg strömmen. Det här kommandot använder parametrarna som är cachelagrade i *. Azure/config-* filen.

```azurecli
az webapp log tail
```

Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

Om du vill stoppa logg strömningen när som helst, skriver du **CTRL** + **C**.

Har du problem? [Berätta för oss](https://aka.ms/DjangoCLITutorialHelp).

> [!NOTE]
> Du kan även granska loggfilerna från din webbläsare via `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
>
> `az webapp up` aktiverar standard loggning för dig. Av prestanda skäl stänger den här loggningen av sig själv efter en stund, men inaktive ras varje gång du kör `az webapp up` igen. Kör följande kommando för att aktivera det manuellt:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="7-manage-your-app-in-the-azure-portal"></a>7. hantera din app i Azure Portal

I [Azure Portal](https://portal.azure.com)söker du efter appens namn och väljer appen i resultaten.

![Navigera till din python django-app i Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Som standard visar portalen appens **översikts** sida, som ger en allmän prestanda vy. Här kan du också utföra grundläggande hanterings åtgärder som att bläddra, stoppa, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![Hantera din python django-app på sidan Översikt i Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/DjangoCLITutorialHelp).

## <a name="8-clean-up-resources"></a>8. Rensa resurser

Om du vill behålla appen eller fortsätta med de extra självstudierna kan du gå vidare till [Nästa steg](#next-steps). Annars kan du ta bort resurs gruppen som du skapar för den här självstudien för att undvika löpande kostnader:

```azurecli
az group delete --no-wait
```

Kommandot använder resurs grupps namnet cachelagrat i *. Azure/config-* filen. Genom att ta bort resurs gruppen frigör och tar du även bort alla resurser som finns i den.

Det kan ta en stund att ta bort alla resurser. `--no-wait`Argumentet tillåter att kommandot returnerar omedelbart.

Har du problem? [Berätta för oss](https://aka.ms/DjangoCLITutorialHelp).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du mappar ett anpassat DNS-namn till din app:

> [!div class="nextstepaction"]
> [Självstudie: mappa ett anpassat DNS-namn till din app](app-service-web-tutorial-custom-domain.md)

Lär dig hur App Service kör en python-app:

> [!div class="nextstepaction"]
> [Konfigurera python-app](configure-language-python.md)
