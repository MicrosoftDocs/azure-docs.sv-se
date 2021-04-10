---
title: Kör container-exempel för kommandot Docker Run
titleSuffix: Azure Cognitive Services
description: Docker körnings kommando för hälso container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/12/2020
ms.author: aahi
ms.openlocfilehash: 3a89ddbc17eab683c658430a64447328ee024a3c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089718"
---
## <a name="install-the-container"></a>Installera behållaren

Det finns flera sätt att installera och köra Textanalys för hälso container. 

- Använd [Azure Portal](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare) för att skapa en textanalys resurs och Använd Docker för att hämta din behållare.
- Använd följande PowerShell-och Azure CLI-skript för att automatisera resurs distributionen och container konfigurationen.

### <a name="run-the-container-locally"></a>Kör containern lokalt

Om du vill köra behållaren i din egen miljö när du har hämtat behållar avbildningen hittar du dess avbildnings-ID:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Kör följande `docker run` kommando. Ersätt plats hållarna nedan med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Nyckeln till din Textanalys-resurs. Du hittar den på resursens nyckel- **och slut punkts** sida på Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Slut punkten för att få åtkomst till API för textanalys. Du hittar den på resursens nyckel- **och slut punkts** sida på Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | Bild-ID för din behållare. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | Indatamängden för behållaren. | Windows: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
rai_terms=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Det här kommandot:

- Förutsätter att den inmatade katalogen finns på värddatorn
- Kör ett Textanalys för hälso container från behållar avbildningen
- Allokerar 6 processor kärnor och 12 gigabyte (GB) minne
- Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
- Accepterar villkoren för licens avtalet (EULA) och den ansvariga AI-RAI ()
- Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

### <a name="demo-ui-to-visualize-output"></a>Demo gränssnitt för att visualisera utdata

> [!NOTE]
> Demonstrationen är endast tillgänglig med Textanalys för hälso container.

Containern innehåller REST-baserade slutpunkts-API:er för frågeförutsägelse.  Vi har också tillhandahållit ett visualiserings verktyg i behållaren som kan nås genom att lägga `/demo` till i slut punkten för behållaren. Exempel:

```
http://<serverURL>:5000/demo
```

Använd exemplet nedan för att skicka en fråga till behållaren som du har distribuerat och som ersätter `serverURL` variabeln med lämpligt värde.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1-preview.4/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Installera behållaren med Azure Web App for Containers

Azure [Web App for containers](https://azure.microsoft.com/services/app-service/containers/) är en Azure-resurs som är dedikerad till att köra behållare i molnet. Den innehåller färdiga funktioner som autoskalning, stöd för Docker-behållare och Docker-form, HTTPS-stöd och mycket mer.

> [!NOTE]
> Med hjälp av Azure Web App får du automatiskt en domän i form av `<appservice_name>.azurewebsites.net`

Kör det här PowerShell-skriptet med Azure CLI för att skapa en Web App for Containers med hjälp av din prenumeration och behållar avbildningen över HTTPS. Vänta tills skriptet har slutförts (cirka 25-30 minuter) innan du skickar den första begäran.

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME 
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Installera behållaren med Azure Container instance

Du kan också använda en Azure Container Instance (ACI) för att förenkla distributionen. ACI är en resurs som gör att du kan köra Docker-behållare på begäran i en hanterad, Server lös Azure-miljö. 

Se [hur du använder Azure Container instances](../../containers/azure-container-instance-recipe.md) för steg som beskriver hur du distribuerar en ACI-resurs med hjälp av Azure Portal. Du kan också använda PowerShell-skriptet nedan med hjälp av Azure CLI, som skapar en ACI i din prenumeration med hjälp av behållar avbildningen.  Vänta tills skriptet har slutförts (cirka 25-30 minuter) innan du skickar den första begäran.  På grund av gränsen för maximalt antal processorer per ACI-resurs väljer du inte det här alternativet om du förväntar dig att skicka fler än 5 stora dokument (cirka 5000 tecken var) per begäran.
Se [ACI regional support](../../../container-instances/container-instances-region-availability.md) artikel för tillgänglighets information. 

> [!NOTE] 
> Azure Container Instances inkluderar inte HTTPS-stöd för de inbyggda domänerna. Om du behöver HTTPS måste du konfigurera den manuellt, inklusive att skapa ett certifikat och registrera en domän. Du hittar anvisningar om hur du gör detta med NGINX nedan.

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Säker ACI-anslutning

Som standard tillhandahålls ingen säkerhet när du använder ACI med container-API. Detta beror på att behållare som vanligt vis körs som en del av en pod som skyddas från utsidan av en nätverks brygga. Du kan dock ändra en behållare med en klient som är klient del och hålla behållarens slut punkt privat. I följande exempel används [nginx](https://www.nginx.com) som en ingress-Gateway för att stödja HTTPS/SSL och autentisering av klient certifikat.

> [!NOTE]
> NGINX är en HTTP-server och proxy med öppen källkod med hög prestanda. En NGINX-behållare kan användas för att avsluta en TLS-anslutning för en enda behållare. Det är också möjligt att använda mer komplexa NGINX ingångs-baserade TLS-lösningar.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Konfigurera NGINX som en ingress-Gateway

NGINX använder [konfigurationsfiler](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) för att aktivera funktioner vid körning. För att aktivera TLS-avslutning för en annan tjänst måste du ange ett SSL-certifikat för att avsluta TLS-anslutningen och  `proxy_pass` Ange en adress för tjänsten. Ett exempel anges nedan.


> [!NOTE]
> `ssl_certificate` förväntar sig att en sökväg anges i NGINX-behållarens lokala fil system. Den angivna adressen `proxy_pass` måste vara tillgänglig i nginx-behållarens nätverk.

NGINX-behållaren läser in alla filer i `_.conf_` som är monterade under `/etc/nginx/conf.d/` i http-konfigurationen.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Exempel på Docker Compose-fil

Exemplet nedan visar hur du kan skapa en [Docker](https://docs.docker.com/compose/reference/overview) -fil för att distribuera NGINX och textanalys för hälso behållarna:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Starta den här Docker-filen genom att köra följande kommando från en-konsol på rot nivån för filen:

```bash
docker-compose up
```

Mer information finns i NGINX-dokumentationen om [nginx SSL-avslutning](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).
