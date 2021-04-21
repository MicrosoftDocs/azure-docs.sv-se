---
title: 'Självstudie: Distribuera Django-app med App Service och Azure Database for PostgreSQL – flexibel server (förhandsversion) i virtuellt nätverk'
description: Distribuera Django-app med App Serice och Azure Database for PostgreSQL – flexibel server (förhandsversion) i virtuellt nätverk
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 84fd0c368bcf39af1bae90dc83336ffdb8489768
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791525"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Självstudie: Distribuera Django-app med App Service och Azure Database for PostgreSQL – flexibel server (förhandsversion)

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexibel server är en förhandsversion

I den här självstudien lär du dig att distribuera ett Django-program i Azure med hjälp av App Services och Azure Database for PostgreSQL – flexibel server i ett virtuellt nätverk.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här artikeln kräver att du kör Azure CLI version 2.0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med kommandot [az login.](/cli/azure/authenticate-azure-cli) Observera egenskapen **ID** från kommandoutdata för det motsvarande prenumerationsnamnet.

```azurecli
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account). Ersätt egenskapen **prenumerations-ID** från **az login-utdata** för din prenumeration i platshållaren för prenumerations-ID.

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>Klona eller ladda ned exempelappen

# <a name="git-clone"></a>[Git-klon](#tab/clone)

Klona exempeldatabasen:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Gå sedan till den mappen:

```terminal
cd djangoapp
```

# <a name="download"></a>[Ladda ned](#tab/download)

Besök [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) , välj **Klona** och välj sedan **Ladda ned ZIP.**

Packa upp ZIP-filen i en mapp med *namnet djangoapp*.

Öppna sedan ett terminalfönster i mappen *djangoapp.*

---

Djangoapp-exemplet innehåller den datadrivna Django-avsökningsapp som du får genom att följa Skriva din första [Django-app](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) i Django-dokumentationen. Den färdiga appen finns här för din bekvämlighet.

Exemplet ändras också för att köras i en produktionsmiljö som App Service:

- Produktionsinställningarna finns i *filen azuresite/production.py.* Utvecklingsinformation finns i *azuresite/settings.py.*
- Appen använder produktionsinställningar när `DJANGO_ENV` miljövariabeln är inställd på "produktion". Du skapar den här miljövariabeln senare i självstudien tillsammans med andra som används för postgreSQL-databaskonfigurationen.

Dessa ändringar är specifika för att konfigurera Django att köras i alla produktionsmiljöer och är inte specifika för App Service. Mer information finns i checklistan [för Django-distribution.](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Skapa en flexibel PostgreSQL-server i ett nytt virtuellt nätverk

Skapa en privat flexibel server och en databas i ett virtuellt nätverk (VNET) med följande kommando:
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

Det här kommandot utför följande åtgärder, vilket kan ta några minuter:

- Skapa resursgruppen om den inte redan finns.
- Genererar ett servernamn om det inte anges.
- Skapa ett nytt virtuellt nätverk för din nya postgreSQL-server. **Anteckna namnet på det virtuella nätverket och undernätets** namn som skapats för servern eftersom du måste lägga till webbappen i samma virtuella nätverk.
- Skapar administratörens användarnamn , lösenord för servern om inget annat anges. **Anteckna användarnamnet och lösenordet som** ska användas i nästa steg.
- Skapa en databas ```postgres``` som kan användas för utveckling. Du kan köra [ **psql för** att ansluta till databasen för](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) att skapa en annan databas.

> [!NOTE]
> Anteckna lösenordet som ska genereras åt dig om du inte har angett det. Om du glömmer lösenordet måste du återställa lösenordet med ``` az postgres flexible-server update``` kommandot


## <a name="deploy-the-code-to-azure-app-service"></a>Distribuera koden till Azure App Service

I det här avsnittet skapar du en appvärd i App Service app, ansluter den här appen till Postgres-databasen och distribuerar sedan koden till den värden.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>Skapa App Service webbapp i ett virtuellt nätverk

I terminalen kontrollerar du att du är i lagringsplatsens rot ( `djangoapp` ) som innehåller appkoden.

Skapa en App Service -app (värdprocessen) med [`az webapp up`](/cli/azure/webapp#az_webapp_up) kommandot :

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- För `--location` argumentet använder du samma plats som du gjorde för databasen i föregående avsnitt.
- Ersätt *\<app-name>* med ett unikt namn i hela Azure (serverslutpunkten är `https://\<app-name>.azurewebsites.net` ). Tillåtna tecken *\<app-name>* för `A` - `Z` är , `0` - `9` och `-` . Ett bra mönster är att använda en kombination av företagets namn och en appidentifierare.
- Skapa [App Service plan](../../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* på prisnivån Basic (B1), om den inte finns. `--plan` och `--sku` är valfria.
- Skapa App Service app om den inte finns.
- Aktivera standardloggning för appen, om det inte redan är aktiverat.
- Ladda upp lagringsplatsen med ZIP-distribution med byggautomatisering aktiverat.
- **az webapp vnet-integration-kommandot** lägger till webbappen i samma virtuella nätverk som postgres-servern.
- Appkoden förväntar sig att hitta databasinformation i ett antal miljövariabler. Om du vill ange miljövariabler i App Service skapar du "appinställningar" med [kommandot az webapp config appsettings set.](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set)

> [!TIP]
> Många Azure CLI-kommandon cachelagrar vanliga parametrar, till exempel namnet på resursgruppen och App Service plan, i filen *.azure/config*. Därför behöver du inte ange samma parameter med senare kommandon. Om du till exempel vill distribuera om appen när du har gjort ändringar kan du bara köra `az webapp up` igen utan några parametrar.

### <a name="run-django-database-migrations"></a>Köra Django-databasmigrering

Django-databasmigreringarna ser till att schemat i PostgreSQL på Azure-databasen matchar de som beskrivs i din kod.

1. Öppna en SSH-session i webbläsaren genom att *gå till https:// \<app-name> .scm.azurewebsites.net/webssh/host* och logga in med autentiseringsuppgifterna för ditt Azure-konto (inte databasserverns autentiseringsuppgifter).

1. I SSH-sessionen kör du följande kommandon (du kan klistra in kommandon med **Ctrl** + **Skift** + **V**):

    ```bash
    cd site/wwwroot

    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

1. Kommandot `createsuperuser` uppmanar dig att ange autentiseringsuppgifter för superanvändare. I den här självstudien använder du standardnamnet , trycker på Retur för e-postadressen för att lämna den tom `root` och anger som  `postgres1` lösenord.

### <a name="create-a-poll-question-in-the-app"></a>Skapa en omröstningsfråga i appen

1. Öppna URL:en *http: \/ / \<app-name> .azurewebsites.net* i en webbläsare. Appen bör visa meddelandet "Inga omröstningar är tillgängliga" eftersom det ännu inte finns några specifika omröstningar i databasen.

1. Bläddra till *http: \/ / \<app-name> .azurewebsites.net/admin*. Logga in med superanvändarautentiseringsuppgifter från föregående avsnitt ( `root` och `postgres1` ). Under Omröstningar väljer du **Lägg till** bredvid **Frågor och** skapar en **omröstningsfråga** med några alternativ.

1. Bläddra igen *till http: \/ / \<app-name> .azurewebsites.net/* för att bekräfta att frågorna nu visas för användaren. Besvara frågor men du vill generera data i databasen.

**Grattis!** Du kör en Python Django-webbapp i Azure App Service Linux, med en aktiv Postgres-databas.

> [!NOTE]
> App Service identifierar ett Django-projekt genom att *leta efter wsgi.py* fil i varje undermapp, vilket skapas som `manage.py startproject` standard. När App Service hittar filen läses Django-webbappen in. Mer information finns i Konfigurera [inbyggd Python-avbildning.](../../app-service/configure-language-python.md)

## <a name="make-code-changes-and-redeploy"></a>Göra kodändringar och distribuera om

I det här avsnittet gör du lokala ändringar i appen och distribuerar om koden till App Service. I processen ställer du in en virtuell Python-miljö som stöder kontinuerligt arbete.

### <a name="run-the-app-locally"></a>Köra appen lokalt

Kör följande kommandon i ett terminalfönster. Se till att följa anvisningarna när du skapar superanvändaren:

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```
När webbappen har lästs in fullständigt anger Django-utvecklingsservern den lokala app-URL:en i meddelandet "Startar utvecklingsservern på http://127.0.0.1:8000/ . Avsluta servern med CTRL+BREAK".

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Exempel på utdata från Django-utvecklingsservern":::

Testa appen lokalt med följande steg:

1. Gå till *http: \/ /localhost:8000* i en webbläsare, som ska visa meddelandet "Inga omröstningar är tillgängliga".

1. Gå till *http: \/ /localhost:8000/admin och* logga in med den administratörsanvändare som du skapade tidigare. Under Omröstningar väljer du **återigen Lägg** till bredvid **Frågor och** skapar en **omröstningsfråga** med vissa alternativ.

1. Gå till *http: \/ /localhost:8000* igen och besvara frågan för att testa appen.

1. Stoppa Django-servern genom att trycka **på Ctrl** + **C**.

När appen körs lokalt använder den en lokal Sqlite3-databas och stör inte produktionsdatabasen. Du kan också använda en lokal PostgreSQL-databas om du vill för att bättre simulera produktionsmiljön.



### <a name="update-the-app"></a>Uppdatera appen

I `polls/models.py` letar du upp den rad som börjar med och ändrar `choice_text` `max_length` parametern till 100:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Eftersom du ändrade datamodellen skapar du en ny Django-migrering och migrerar databasen:

```
python manage.py makemigrations
python manage.py migrate
```

Kör utvecklingsservern igen med `python manage.py runserver` och testa appen på till *http: \/ /localhost:8000/admin*:

Stoppa Django-webbservern igen med **Ctrl** + **C**.


### <a name="redeploy-the-code-to-azure"></a>Distribuera om koden till Azure

Kör följande kommando i lagringsplatsens rot:

```azurecli
az webapp up
```

Det här kommandot använder de parametrar som cachelagras i *.azure/config-filen.* Eftersom App Service upptäcker att appen redan finns distribuerar den bara om koden.



### <a name="rerun-migrations-in-azure"></a>Köra migreringar igen i Azure

Eftersom du har gjort ändringar i datamodellen måste du köra databasmigreringarna på App Service.

Öppna en SSH-session igen i webbläsaren genom att gå till *https:// \<app-name> .scm.azurewebsites.net/webssh/host*. Kör sedan följande kommandon:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Granska appen i produktion

Bläddra till *http: \/ / \<app-name> .azurewebsites.net* och testa appen igen i produktion. (Eftersom du bara ändrade längden på ett databasfält är ändringen bara märkbar om du försöker ange ett längre svar när du skapar en fråga.)

> [!TIP]
> Du kan använda [django-lagring för](https://django-storages.readthedocs.io/en/latest/backends/azure.html) att lagra statiska & medietillgångar i Azure Storage. Du kan använda Azure CDN för gzipping för statiska filer.


## <a name="manage-your-app-in-the-azure-portal"></a>Hantera din app i Azure Portal

I [Azure Portal](https://portal.azure.com)söker du efter appnamnet och väljer appen i resultatet.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Gå till din Python Django-app i Azure Portal":::

Som standard visar portalen appens **översiktssida,** som ger en allmän prestandavy. Här kan du också utföra grundläggande hanteringsuppgifter som att bläddra, stoppa, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Hantera din Python Django-app på översiktssidan i Azure Portal":::


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill behålla appen eller fortsätta till nästa självstudie kan du gå vidare till [Nästa steg.](#next-steps) Annars kan du ta bort resursgruppen som skapas för den här självstudien för att undvika löpande avgifter:

```azurecli
az group delete -g myresourcegroup
```

Kommandot använder resursgruppens namn som cachelagras i *.azure/config-filen.* Genom att ta bort resursgruppen kan du också allokera och ta bort alla resurser som ingår i den.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du mappar ett anpassat DNS-namn till din app:

> [!div class="nextstepaction"]
> [Självstudie: Mappa anpassat DNS-namn till din app](../../app-service/app-service-web-tutorial-custom-domain.md)

Lär dig App Service kör en Python-app:

> [!div class="nextstepaction"]
> [Konfigurera Python-app](../../app-service/configure-language-python.md)
