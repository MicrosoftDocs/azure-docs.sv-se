---
title: 'Självstudie: Distribuera Django i AKS-kluster med PostgreSQL – flexibel server med hjälp av Azure CLI'
description: Lär dig hur du snabbt skapar och distribuerar Django på AKS med Azure Database for PostgreSQL – flexibel server.
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: b79b470a25a63c0a46ddef94ee65f47f37c560cb
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477816"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>Självstudie: Distribuera Django-app på AKS med Azure Database for PostgreSQL – flexibel server

I den här snabbstarten distribuerar du ett Django-program på Azure Kubernetes Service-kluster (AKS) med Azure Database for PostgreSQL – flexibel server (förhandsversion) med hjälp av Azure CLI.

**[AKS](../../aks/intro-kubernetes.md)** är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. **[Azure Database for PostgreSQL – Flexibel server (förhandsversion)](overview.md)** är en fullständigt hanterad databastjänst som har utformats för att ge mer detaljerad kontroll och flexibilitet över databashanteringsfunktioner och konfigurationsinställningar.

> [!NOTE]
> - Azure Database for PostgreSQL Flexibel server är för närvarande i offentlig förhandsversion
> - Den här snabbstarten förutsätter grundläggande kunskaper om Kubernetes-begreppen Django och PostgreSQL.

## <a name="pre-requisites"></a>Förutsättningar
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- Använd [Azure Cloud Shell](../../cloud-shell/quickstart.md) med bash-miljön.

   [![Inbäddad start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)  
- Om du vill kan du [installera](/cli/azure/install-azure-cli) Azure CLI för att köra CLI-referenskommandon.
  - Om du använder en lokal installation loggar du in med Azure CLI med hjälp av kommandot [az login](/cli/azure/reference-index#az-login).  Slutför autentiseringsprocessen genom att följa stegen som visas i terminalen.  Fler inloggningsalternativ finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).
  - När du uppmanas till det installerar du Azure CLI-tillägg vid första användning.  Mer information om tillägg finns i [Använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).
  - Kör [az version](/cli/azure/reference-index?#az_version) om du vill hitta versionen och de beroende bibliotek som är installerade. Om du vill uppgradera till den senaste versionen kör du [az upgrade](/cli/azure/reference-index?#az_upgrade). Den här artikeln kräver den senaste versionen av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

> [!NOTE]
> Om du kör kommandona i den här snabbstarten lokalt (i stället för Azure Cloud Shell) måste du köra kommandona som administratör.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. Nu ska vi skapa en resursgrupp, *django-project* med kommandot [az group create][az-group-create] på *platsen eastus.*

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> Platsen för resursgruppen är där resursgruppens metadata lagras. Det är också här dina resurser körs i Azure om du inte anger någon annan region när resursen skapas.

Följande exempelutdata visar den resursgrupp som skapats:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Skapa AKS-kluster

Använd kommandot [az aks create](/cli/azure/aks#az-aks-create) för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* och en enda nod. Det tar flera minuter att slutföra.

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
```

Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret.

> [!NOTE]
> När du skapar ett AKS-kluster skapas automatiskt en andra resursgrupp för att lagra AKS-resurserna. Se [Varför skapas två resursgrupper med AKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Anslut till klustret

För att hantera Kubernetes-kluster använder du [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), Kubernetes kommandoradsklient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. För att installera `kubectl` lokalt använder du kommandot [az aks install-cli](/cli/azure/aks#az-aks-install-cli):

```azurecli-interactive
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials](/cli/azure/aks#az-aks-get-credentials). Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group django-project --name djangoappcluster
```

> [!NOTE]
> Kommandot ovan använder standardplatsen för [Kubernetes-konfigurationsfilen](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), som är `~/.kube/config` . Du kan ange en annan plats för Kubernetes-konfigurationsfilen med *hjälp av --file*.

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) för att returnera en lista över klusternoderna.

```azurecli-interactive
kubectl get nodes
```

Följande exempelutdata visar den enskilda nod som skapades i föregående steg. Kontrollera att status för noden är *Klar*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Skapa en Azure Database for PostgreSQL – flexibel server
Skapa en flexibel server med [kommandot az postgreSQL flexible-server create.](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) Följande kommando skapar en server med tjänstens standardinställningar och värden från din lokala Azure CLI-kontext:

```azurecli-interactive
az postgres flexible-server create --public-access <YOUR-IP-ADDRESS>
```

Servern som skapas har attributen nedan:
- En ny tom databas ```postgres``` skapas när servern först etableras. I den här snabbstarten använder vi den här databasen.
- Automatiskt genererat servernamn, administratörsnamn, administratörslösenord, resursgruppsnamn (om det inte redan har angetts i lokal kontext) och på samma plats som resursgruppen
- Tjänstens standardinställningar för återstående serverkonfigurationer: beräkningsnivå (Generell användning), beräkningsstorlek/SKU (Standard_D2s_v3 som använder 2 virtuella kärnor), kvarhållningsperiod för säkerhetskopior (7 dagar) och PostgreSQL-version (12)
- Med argumentet för offentlig åtkomst kan du skapa en server med offentlig åtkomst som skyddas av brandväggsregler. Genom att ange din IP-adress för att lägga till brandväggsregeln för att tillåta åtkomst från klientdatorn.
- Eftersom kommandot använder lokal kontext skapas servern i resursgruppen ```django-project``` och i regionen ```eastus``` .


## <a name="build-your-django-docker-image"></a>Skapa din Django Docker-avbildning

Skapa ett nytt [Django-program](https://docs.djangoproject.com/en/3.1/intro/) eller använd ditt befintliga Django-projekt. Kontrollera att koden finns i den här mappstrukturen.

```
â””â”€â”€â”€my-djangoapp
    â””â”€â”€â”€views.py
    â””â”€â”€â”€models.py
    â””â”€â”€â”€forms.py
    â”œâ”€â”€â”€templates
          . . . . . . .
    â”œâ”€â”€â”€static
         . . . . . . .
â””â”€â”€â”€my-django-project
    â””â”€â”€â”€settings.py
    â””â”€â”€â”€urls.py
    â””â”€â”€â”€wsgi.py
        . . . . . . .
    â””â”€â”€â”€ Dockerfile
    â””â”€â”€â”€ requirements.txt
    â””â”€â”€â”€ manage.py
    
```
Uppdatera ```ALLOWED_HOSTS``` i för att se till att ```settings.py``` Django-programmet använder den externa IP-adress som tilldelas kubernetes-appen.

```python
ALLOWED_HOSTS = ['*']
```

```DATABASES={ }```Uppdateringsavsnittet i ```settings.py``` filen. Kodfragmentet nedan läser databasvärden, användarnamn och lösenord från Kubernetes-manifestfilen.

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>Generera en requirements.txt fil
Skapa en ```requirements.txt``` fil för att visa en lista över beroenden för Django-programmet. Här är en ```requirements.txt``` exempelfil. Du kan använda [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) för att generera en requirements.txt-fil för ditt befintliga program.

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Hur du skapar en Dockerfile
Skapa en ny fil med ```Dockerfile``` namnet och kopiera kodfragmentet nedan. Den här Dockerfile vid installation av Python 3.8 och installation av alla krav som anges i requirements.txt fil.

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>Skapa avbildningen
Kontrollera att du är i katalogen i ```my-django-app``` en terminal med hjälp av kommandot ```cd``` . Kör följande kommando för att skapa anslagstavlans bild:

``` bash

docker build --tag myblog:latest .

```

Distribuera avbildningen till [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) eller [Azure Container Registry.](../../container-registry/container-registry-get-started-azure-cli.md)

> [!IMPORTANT]
>Om du använder Azure Container Regdistry (ACR) kör du kommandot för att koppla ```az aks update``` ACR-kontot till AKS-klustret.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Skapa Kubernetes-manifestfil

En Kubernetes-manifestfil definierar ett önskat tillstånd för klustret, till exempel vilka containeravbildningar som ska köras. Nu ska vi skapa en manifestfil med ```djangoapp.yaml``` namnet och kopiera följande YAML-definition.

>[!IMPORTANT]
> - Ersätt ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` med ditt faktiska Django Docker-avbildningsnamn och tagg, till exempel ```docker-hub-user/myblog:latest``` .
> - Uppdatera ```env``` avsnittet nedan med din , , för din flexibla ```SERVERNAME``` ```YOUR-DATABASE-USERNAME``` ```YOUR-DATABASE-PASSWORD``` postgres-server.


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>Distribuera Django till AKS-kluster
Distribuera programmet med kommandot [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f djangoapp.yaml
```

Följande exempelutdata visar de distributioner och tjänster som skapats:

```output
deployment "django-app" created
service "python-svc" created
```

Med en distribution kan du beskriva information om din distribution, till exempel vilka avbildningar som ska användas för appen, antalet ```django-app``` poddar och poddkonfiguration. En tjänst ```python-svc``` skapas för att exponera programmet via en extern IP-adress.

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) med argumentet `--watch`.

```azurecli-interactive
kubectl get service django-app --watch
```

Inledningsvis *visas EXTERNAL-IP* *för django-apptjänsten* som *väntande*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När *EXTERNAL-IP*-adressen ändras från *väntande* till en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa `kubectl`-övervakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Öppna nu en webbläsare till den externa IP-adressen för din tjänst och visa Django-programmet.  

>[!NOTE]
> - För närvarande använder inte Django-webbplatsen HTTPS. Vi rekommenderar att du [AKTIVERAR TLS med dina egna certifikat.](../../aks/ingress-own-tls.md)
> - Du kan aktivera [HTTP-routning](../../aks/http-application-routing.md) för klustret. När HTTP-routning har aktiverats konfigurerar den en ingress-kontrollant i ditt AKS-kluster. När program distribueras skapar lösningen även offentligt tillgängliga DNS-namn för programslutpunkter.

## <a name="run-database-migrations"></a>Köra databasmigrering

För alla django-program skulle du behöva köra databasmigrering eller samla in statiska filer. Du kan köra dessa django shell-kommandon med hjälp av ```$ kubectl exec <pod-name> -- [COMMAND]``` .  Innan du kör kommandot måste du hitta poddens namn med hjälp av ```kubectl get pods``` . 

```bash
$ kubectl get pods
```

Du ser utdata som liknar detta
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

När poddnamnet har hittats kan du köra django-databasmigreringarna med kommandot ```$ kubectl exec <pod-name> -- [COMMAND]``` . Observera ```/code/``` att är arbetskatalogen för projektet som definieras ```Dockerfile``` i ovan.

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

Utdata skulle se ut så här 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

Om du får problem kan du köra för ```kubectl logs <pod-name>```  att se vilket undantag som utlösts av ditt program. Om programmet fungerar visas utdata som liknar detta när du kör ```kubectl logs``` .

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>Rensa resurserna

För att undvika Azure-avgifter bör du rensa onödiga resurser.  När klustret inte längre behövs kan du använda kommandot [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, containertjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten](../../aks/kubernetes-service-principal.md#additional-considerations). Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [kommer åt Kubernetes-webbinstrumentpanelen](../../aks/kubernetes-dashboard.md) för ditt AKS-kluster
- Lär dig hur du [aktiverar kontinuerlig distribution](../../aks/deployment-center-launcher.md)
- Lär dig hur du [skalar klustret](../../aks/tutorial-kubernetes-scale.md)
- Lär dig hur du hanterar din [flexibla Postgres-server](./quickstart-create-server-cli.md)
- Lär dig hur [du konfigurerar serverparametrar](./howto-configure-server-parameters-using-cli.md) för databasservern.
