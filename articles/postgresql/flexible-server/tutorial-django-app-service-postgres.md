---
title: 'Självstudie: Distribuera django-app med App Service och Azure Database for PostgreSQL-flexibel Server (för hands version) i virtuellt nätverk'
description: Distribuera django-appen med app service och Azure Database for PostgreSQL-flexibel Server (för hands version) i virtuellt nätverk
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bcc9ca0175e0e03c62c2ce2b91d8ec337756a3cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92490109"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Självstudie: Distribuera django-app med App Service och Azure Database for PostgreSQL-flexibel Server (för hands version)

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

I den här självstudien får du lära dig hur du distribuerar ett django-program i Azure med hjälp av App Services och Azure Database for PostgreSQL-flexibel server i ett virtuellt nätverk.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här artikeln kräver att du kör Azure CLI version 2,0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med kommandot [AZ login](/cli/azure/authenticate-azure-cli) . Observera egenskapen **ID** från kommandoutdata för det motsvarande prenumerationsnamnet.

```azurecli
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account). Ersätt egenskapen **prenumerations-ID** från **AZ-inloggnings** resultatet för din prenumeration till plats hållaren för prenumerations-ID.

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>Klona eller hämta exempel appen

# <a name="git-clone"></a>[Git-klon](#tab/clone)

Klona exempel lagrings platsen:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Gå sedan till mappen:

```terminal
cd djangoapp
```

# <a name="download"></a>[Ladda ned](#tab/download)

Besök [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) , Välj **klona** och välj sedan **Hämta zip**.

Packa upp ZIP-filen i en mapp med namnet *djangoapp*.

Öppna sedan ett terminalfönster i den *djangoapp* -mappen.

---

Djangoapp-exemplet innehåller den data drivna django avsöknings appen som du får genom att följa de [första django-apparna](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) i Django-dokumentationen. Den färdiga appen tillhandahålls här för din bekvämlighet.

Exemplet ändras också till att köras i en produktions miljö som App Service:

- Produktions inställningarna finns i filen *azuresite/productation. py* . Utvecklings information finns i *azuresite/Settings. py*.
- Appen använder produktions inställningar när `DJANGO_ENV` miljövariabeln är inställd på "produktion". Du skapar den här miljövariabeln senare i självstudien tillsammans med andra som används för PostgreSQL-databas konfigurationen.

Dessa ändringar är specifika för att konfigurera django för att köras i alla produktions miljöer och är inte särskilt App Service. Mer information finns i [Check lista för django-distribution](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Skapa en PostgreSQL-flexibel server i ett nytt virtuellt nätverk

Skapa en privat flexibel Server och en databas i ett virtuellt nätverk (VNET) med hjälp av följande kommando:
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

Detta kommando utför följande åtgärder, vilket kan ta några minuter:

- Skapa resurs gruppen om den inte redan finns.
- Genererar ett server namn om det inte har angetts.
- Skapa ett nytt virtuellt nätverk för den nya postgreSQL-servern. **Anteckna namnet på det virtuella nätverket och under nätet** som skapats för servern eftersom du måste lägga till webbappen i samma virtuella nätverk.
- Skapar administratörs användar namn, lösen ord för servern om inget anges. **Anteckna användar namnet och lösen ordet** som ska användas i nästa steg.
- Skapa en databas ```postgres``` som kan användas för utveckling. Du kan köra [ **psql** för att ansluta till databasen](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) för att skapa en annan databas.

> [!NOTE]
> Anteckna ditt lösen ord som ska genereras för dig om inget anges. Om du glömmer bort lösen ordet måste du återställa lösen ordet med hjälp av ``` az postgres flexible-server update``` kommandot


## <a name="deploy-the-code-to-azure-app-service"></a>Distribuera koden till Azure App Service

I det här avsnittet skapar du app Host i App Service app, ansluter den här appen till postgres-databasen och distribuerar sedan koden till den värden.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>Skapa App Service-webbappen i ett virtuellt nätverk

I terminalen kontrollerar du att du befinner dig i lagrings platsen ( `djangoapp` ) som innehåller appens kod.

Skapa en App Service app (värd processen) med [`az webapp up`](/cli/azure/webapp#az-webapp-up) kommandot:

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
- Ersätt *\<app-name>* med ett unikt namn i alla Azure (Server slut punkten är `https://\<app-name>.azurewebsites.net` ). Tillåtna tecken för *\<app-name>* är `A` - `Z` , `0` - `9` och `-` . Ett utmärkt mönster är att använda en kombination av företagets namn och en app-ID.
- Skapa [App Service plan](../../app-service/overview-hosting-plans.md) *DjangoPostgres-självstudie – planera* i den grundläggande pris nivån (B1) om den inte finns. `--plan` och `--sku` är valfria.
- Skapa App Service-appen om den inte finns.
- Aktivera standard loggning för appen om den inte redan är aktive rad.
- Ladda upp lagrings platsen med hjälp av ZIP-distribution med build Automation aktiverat.
- **AZ webapp VNet-integration** kommando lägger till webbappen i samma virtuella nätverk som postgres-servern.
- App-koden förväntar sig att hitta databas information i ett antal miljövariabler. Om du vill ange miljövariabler i App Service skapar du "appinställningar" med kommandot [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) .

> [!TIP]
> Många Azure CLI-kommandon cachelagrar vanliga parametrar, till exempel namnet på resurs gruppen och App Service plan, i filen *. Azure/config*. Därför behöver du inte ange samma parameter med senare kommandon. Om du till exempel vill distribuera om appen när du har gjort ändringar kan du bara köra `az webapp up` igen utan parametrar.

### <a name="run-django-database-migrations"></a>Köra django Database-migreringar

Django Database-migreringar ser till att schemat i PostgreSQL i Azure Database matchar de som beskrivs i din kod.

1. Öppna en SSH-session i webbläsaren genom att gå till *https:// \<app-name> . scm.azurewebsites.net/webssh/Host* och logga in med dina autentiseringsuppgifter för Azure-kontot (inte databas serverns autentiseringsuppgifter).

1. Kör följande kommandon i SSH-sessionen (du kan klistra in kommandon med **CTRL** + **Shift** + **V**):

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

1. `createsuperuser`Kommandot efterfrågar behörigheter för superanvändare. I den här självstudien använder du standard användar namnet `root` , trycker på **RETUR** för e-postadressen för att lämna den tom och anger `postgres1` lösen ordet.

### <a name="create-a-poll-question-in-the-app"></a>Skapa en avsöknings fråga i appen

1. Öppna webb adressen *http: \/ / \<app-name> . azurewebsites.net* i en webbläsare. Appen ska visa meddelandet "inga avsökningar är tillgängliga" eftersom det inte finns några speciella avsökningar än i databasen.

1. Bläddra till *http: \/ / \<app-name> . azurewebsites.net/admin*. Logga in med behörigheter för superanvändare från föregående avsnitt ( `root` och `postgres1` ). Under **avsökningar** väljer du **Lägg till** bredvid **frågor** och skapar en avsöknings fråga med några val.

1. Bläddra igen till *http: \/ / \<app-name> . azurewebsites.net/* för att bekräfta att frågorna nu visas för användaren. Besvara frågor som du vill skapa vissa data i databasen.

**Grattis!** Du kör en python django-webbapp i Azure App Service för Linux med en aktiv postgres-databas.

> [!NOTE]
> App Service identifierar ett django-projekt genom att söka efter en *wsgi.py* -fil i varje undermapp som `manage.py startproject` skapas som standard. När App Service hittar filen laddas django-webbappen. Mer information finns i [Konfigurera inbyggd python-avbildning](../../app-service/configure-language-python.md).

## <a name="make-code-changes-and-redeploy"></a>Gör kod ändringar och distribuera om

I det här avsnittet ska du göra lokala ändringar i appen och distribuera om koden till App Service. I processen ställer du in en virtuell python-miljö som har stöd för pågående arbete.

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
När webbappen har lästs in är django-utvecklings servern den lokala appens URL i meddelandet "startar utvecklings servern på http://127.0.0.1:8000/ . Avsluta servern med CTRL-BREAK ".

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Exempel på utdata för django Development Server":::

Testa appen lokalt med följande steg:

1. Gå till *http: \/ /localhost: 8000* i en webbläsare, som ska visa meddelandet "det finns inga avsökningar".

1. Gå till *http: \/ /localhost: 8000/admin* och logga in med administratörs användaren som du skapade tidigare. Under **avsökningar** väljer du **Lägg till** bredvid **frågor** och skapar en avsöknings fråga med några val.

1. Gå till *http: \/ /localhost: 8000* igen och besvara frågan för att testa appen.

1. Stoppa django-servern genom att trycka på **CTRL** + **C**.

När du kör lokalt använder appen en lokal sqlite3-databas och stör inte din produktions databas. Du kan också använda en lokal PostgreSQL-databas, om du vill, för att bättre simulera produktions miljön.



### <a name="update-the-app"></a>Uppdatera appen

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


### <a name="redeploy-the-code-to-azure"></a>Distribuera om koden till Azure

Kör följande kommando i databas roten:

```azurecli
az webapp up
```

Det här kommandot använder parametrarna som är cachelagrade i *. Azure/config-* filen. Eftersom App Service upptäcker att appen redan finns, distribuerar den bara om koden.



### <a name="rerun-migrations-in-azure"></a>Kör om migreringar i Azure

Eftersom du har gjort ändringar i data modellen måste du köra om migreringen av databasen i App Service.

Öppna en SSH-session igen i webbläsaren genom att gå till *https:// \<app-name> . scm.azurewebsites.net/webssh/Host*. Kör sedan följande kommandon:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Granska appen i produktion

Bläddra till *http: \/ / \<app-name> . azurewebsites.net* och testa appen igen i produktion. (Eftersom du bara har ändrat längden på ett databas fält är ändringen bara märkbart om du försöker ange ett längre svar när du skapar en fråga.)

> [!TIP]
> Du kan använda [django-lagring](https://django-storages.readthedocs.io/en/latest/backends/azure.html) för att lagra statiska & medie till gångar i Azure Storage. Du kan använda Azure CDN för gzipping för statiska filer.


## <a name="manage-your-app-in-the-azure-portal"></a>Hantera din app i Azure Portal

I [Azure Portal](https://portal.azure.com)söker du efter appens namn och väljer appen i resultaten.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Navigera till din python django-app i Azure Portal":::

Som standard visar portalen appens **översikts** sida, som ger en allmän prestanda vy. Här kan du också utföra grundläggande hanterings åtgärder som att bläddra, stoppa, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Hantera din python django-app på sidan Översikt i Azure Portal":::


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill behålla appen eller fortsätta till nästa självstudie kan du gå vidare till [Nästa steg](#next-steps). Annars kan du ta bort resurs gruppen som du skapar för den här självstudien för att undvika löpande kostnader:

```azurecli
az group delete -g myresourcegroup
```

Kommandot använder resurs grupps namnet cachelagrat i *. Azure/config-* filen. Genom att ta bort resurs gruppen frigör och tar du även bort alla resurser som finns i den.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du mappar ett anpassat DNS-namn till din app:

> [!div class="nextstepaction"]
> [Självstudie: mappa ett anpassat DNS-namn till din app](../../app-service/app-service-web-tutorial-custom-domain.md)

Lär dig hur App Service kör en python-app:

> [!div class="nextstepaction"]
> [Konfigurera python-app](../../app-service/configure-language-python.md)
