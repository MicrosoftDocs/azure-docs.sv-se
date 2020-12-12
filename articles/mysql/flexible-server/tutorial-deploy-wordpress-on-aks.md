---
title: 'Självstudie: Distribuera WordPress på AKS-kluster med MySQL-flexibel server med hjälp av Azure CLI'
description: Lär dig hur du snabbt skapar och distribuerar WordPress på AKS med Azure Database for MySQL-flexibel Server.
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: mvc
ms.openlocfilehash: 7713b7596b21e02e941a19f64d3658ab0f5f51f5
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359021"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>Självstudie: Distribuera WordPress-app på AKS med Azure Database for MySQL-flexibel Server

I den här snabb starten distribuerar du ett WordPress-program på Azure Kubernetes service (AKS)-kluster med Azure Database for MySQL-flexibel Server (för hands version) med Azure CLI. 
**[AKS](../../aks/intro-kubernetes.md)** är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. **[Azure Database for MySQL-flexibel Server (för hands version)](overview.md)** är en fullständigt hanterad databas tjänst som är utformad för att ge mer detaljerad kontroll och flexibilitet för funktioner i databas hantering och konfigurations inställningar. Den flexibla servern är i för hands version.

> [!NOTE]
> - Azure Database for MySQL flexibel Server är för närvarande en offentlig för hands version
> - Den här snabb starten förutsätter grundläggande kunskaper om Kubernetes-koncept, WordPress och MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver den senaste versionen av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

> [!NOTE]
> Om du kör kommandona i den här snabb starten lokalt (i stället för Azure Cloud Shell), måste du kontrol lera att du kör kommandona som administratör.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. Nu ska vi skapa en resurs grupp, *WordPress-projekt* med [AZ Group Create] [AZ-Group-Create]-kommandot på den *östra* platsen.

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> Platsen för resurs gruppen är den plats där resurs gruppens metadata lagras. Det är också var dina resurser körs i Azure om du inte anger någon annan region när du skapar en resurs.

Följande exempelutdata visar den resursgrupp som skapats:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Skapa AKS-kluster

Använd kommandot [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-create) för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* och en enda nod. Det tar flera minuter att slutföra.

```azurecli-interactive
az aks create --resource-group wordpress-project --name wordpresscluster--node-count 1 --generate-ssh-keys
```

Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret.

> [!NOTE]
> När du skapar ett AKS-kluster skapas en andra resurs grupp automatiskt för att lagra AKS-resurserna. Se [Varför är två resurs grupper skapade med AKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Anslut till klustret

För att hantera Kubernetes-kluster använder du [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), Kubernetes kommandoradsklient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. För att installera `kubectl` lokalt använder du kommandot [az aks install-cli](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-install-cli):

```azurecli-interactive
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials). Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name wordpresscluster
```

> [!NOTE]
> Kommandot ovan använder standard platsen för [konfigurations filen Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), som är `~/.kube/config` . Du kan ange en annan plats för Kubernetes-konfigurations filen med *--File*.

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) för att returnera en lista över klusternoderna.

```azurecli-interactive
kubectl get nodes
```

Följande exempelutdata visar den enskilda nod som skapades i föregående steg. Kontrollera att status för noden är *Klar*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Skapa en Azure Database for MySQL-flexibel Server
Skapa en flexibel server med kommandot [AZ MySQL Flexible-Server Create](/cli/azure/mysql/flexible-server?view=azure-cli-latest&preserve-view=true). Följande kommando skapar en server som använder tjänstens standardinställningar och värden från Azure CLI: s lokala kontext:

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

Servern som har skapats har följande attribut:
- En ny tom databas ```flexibleserverdb``` skapas när servern först konfigureras. I den här snabb starten kommer vi att använda den här databasen.
- Automatiskt genererad Server namn, administratörs användar namn, administratörs lösen ord, resurs grupp namn (om det inte redan har angetts i den lokala kontexten) och på samma plats som resurs gruppen
- Standardinställningar för tjänsten för återstående serverkonfigurationer: beräknings nivå (burst), Compute size/SKU (B1MS), kvarhållning av säkerhets kopior (7 dagar) och MySQL-version (5,7)
- Med hjälp av ett offentligt-Access-argument kan du skapa en server med offentlig åtkomst som skyddas av brand Väggs regler. Genom att ange IP-adressen för att lägga till brand Väggs regeln för att tillåta åtkomst från klient datorn.
- Eftersom kommandot använder en lokal kontext skapas servern i resurs gruppen ```wordpress-project``` och i regionen ```eastus``` .


### <a name="build-your-wordpress-docker-image"></a>Bygg din WordPress Docker-avbildning

Hämta den [senaste WordPress](https://wordpress.org/download/) -versionen. Skapa en ny katalog ```my-wordpress-app``` för projektet och Använd den här enkla mappstrukturen

```
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
          . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```


Byt namn ```wp-config-sample.php```  till ```wp-config.php``` och ersätt raderna 21 till 32 med det här kodfragmentet. Kodfragmentet nedan läser databasens värd, användar namn och lösen ord från manifest filen för Kubernetes.

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>Hur du skapar en Dockerfile
Skapa en ny Dockerfile och kopiera det här kodfragmentet. Den här Dockerfile konfigurerar Apache-webbservern med PHP och aktiverar mysqli-tillägget.

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>Bygg din Docker-avbildning
Se till att du befinner dig i katalogen ```my-wordpress-app``` i en Terminal med hjälp av ```cd``` kommandot. Kör följande kommando för att skapa avbildningen:

``` bash

docker build --tag myblog:latest .

```

Distribuera avbildningen till [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) eller [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md).

> [!IMPORTANT]
>Om du använder Azure Container regdistry (ACR) kör du ```az aks update``` kommandot för att ansluta ACR-konto till AKS-klustret.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Skapa Kubernetes manifest fil

En Kubernetes-manifestfil definierar ett önskat tillstånd för klustret, till exempel vilka containeravbildningar som ska köras. Nu ska vi skapa en manifest fil med namnet `mywordpress.yaml` och kopiera i följande yaml-definition.

>[!IMPORTANT]
> - Ersätt ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` med ditt faktiska WordPress Docker-avbildnings namn och tagg, till exempel ```docker-hub-user/myblog:latest``` .
> - Uppdaterings ```env``` avsnittet nedan med din ```SERVERNAME``` , ```YOUR-DATABASE-USERNAME``` , ```YOUR-DATABASE-PASSWORD``` av din MySQL-flexibla Server.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>Distribuera WordPress till AKS-kluster
Distribuera programmet med kommandot [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f mywordpress.yaml
```

Följande exempelutdata visar de distributioner och tjänster som skapats:

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) med argumentet `--watch`.

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

Den *externa IP-adressen* för *WordPress-blogg* tjänsten visas som *väntar*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När *EXTERNAL-IP*-adressen ändras från *väntande* till en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa `kubectl`-övervakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>Bläddra i WordPress

Öppna en webbläsare till den externa IP-adressen för din tjänst för att se din WordPress-installations sida.

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="WordPress-installationen lyckades på AKS och MySQL-flexibel Server":::

>[!NOTE]
> - För närvarande används inte HTTPS för WordPress-webbplatsen. Vi rekommenderar att du [aktiverar TLS med dina egna certifikat](../../aks/ingress-own-tls.md).
> - Du kan aktivera [http-routning](../../aks/http-application-routing.md) för klustret.

## <a name="clean-up-the-resources"></a>Rensa resurserna

För att undvika Azure-avgifter bör du rensa resurser som inte behövs.  När klustret inte längre behövs kan du använda kommandot [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) för att ta bort resursgruppen, containertjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten](../../aks/kubernetes-service-principal.md#additional-considerations). Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [kommer åt Kubernetes-webbinstrumentpanelen](../../aks/kubernetes-dashboard.md) för ditt AKS-kluster
- Lär dig hur du [skalar klustret](../../aks/tutorial-kubernetes-scale.md)
- Lär dig hur du hanterar din [MySQL-flexibla Server](./quickstart-create-server-cli.md)
- Lär dig hur du [konfigurerar Server parametrar](./how-to-configure-server-parameters-cli.md) för din databas server.

