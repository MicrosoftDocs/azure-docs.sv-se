---
title: Skapa Azure Functions på Linux med en anpassad avbildning
description: Lär dig hur du skapar en Azure Functions som körs på en anpassad Linux-avbildning.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: 1c7a9fd83131ea6282d2ef4860b744fa348153ed
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070925"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Skapa en funktion i Linux med en anpassad container

I den här självstudien skapar du och distribuerar din kod till Azure Functions som en anpassad Docker-behållare med hjälp av en Linux-bas avbildning. Du använder vanligt vis en anpassad bild när funktionerna kräver en specifik språk version eller har ett särskilt beroende eller en specifik konfiguration som inte tillhandahålls av den inbyggda avbildningen.

::: zone pivot="programming-language-other"
Azure Functions stöder alla språk och körnings miljöer med [anpassade hanterare](functions-custom-handlers.md). För vissa språk, till exempel det R-programmeringsspråk som används i den här självstudien, måste du installera körningen eller ytterligare bibliotek som beroenden som kräver att en anpassad behållare används.
::: zone-end

Distribution av funktions koden i en anpassad Linux-behållare kräver [Premium-plan](functions-premium-plan.md) eller [dedikerad (App Service) plan-](dedicated-plan.md) värd. Genom att slutföra den här självstudien debiteras du kostnader för några amerikanska dollar i ditt Azure-konto, som du kan minimera genom att [Rensa resurser](#clean-up-resources) när du är klar.

Du kan också använda en standard-Azure App Service-behållare enligt beskrivningen i [skapa din första funktion på Linux](./create-first-function-cli-csharp.md?pivots=programming-language-python). Bas avbildningar som stöds för Azure Functions finns i [Azure Functions Base images-lagrings platsen](https://hub.docker.com/_/microsoft-azure-functions-base).

I de här självstudierna får du lära dig att

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Skapa en Function-app och Dockerfile med hjälp av Azure Functions Core Tools.
> * skapa en anpassad avbildning med Docker
> * Publicera en anpassad avbildning till ett containerregister.
> * Skapa stöd resurser i Azure för Function-appen
> * distribuera en funktionsapp från Docker Hub
> * lägga till programinställningar i funktionsappen.
> * Aktivera kontinuerlig distribution.
> * Aktivera SSH-anslutningar till behållaren.
> * Lägg till en utgående bindning för kö Storage. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Skapa en Function-app och Dockerfile med hjälp av Azure Functions Core Tools.
> * skapa en anpassad avbildning med Docker
> * Publicera en anpassad avbildning till ett containerregister.
> * Skapa stöd resurser i Azure för Function-appen
> * distribuera en funktionsapp från Docker Hub
> * lägga till programinställningar i funktionsappen.
> * Aktivera kontinuerlig distribution.
> * Aktivera SSH-anslutningar till behållaren.
::: zone-end

Du kan följa den här självstudien på alla datorer som kör Windows, macOS eller Linux. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Ett [Docker-ID](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Kör `docker login` för att logga in på Docker. Detta kommando Miss lyckas om Docker inte körs, i vilket fall startar Docker och försöker köra kommandot igen.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Skapa och testa det lokala Functions-projektet

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
I en terminal-eller kommando tolk kör du följande kommando för ditt valda språk för att skapa ett Function app-projekt i en mapp med namnet `LocalFunctionsProject` .  
::: zone-end  
::: zone pivot="programming-language-csharp"  
```console
func init LocalFunctionsProject --worker-runtime dotnet --docker
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init LocalFunctionsProject --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init LocalFunctionsProject --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init LocalFunctionsProject --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init LocalFunctionsProject --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
Kör följande kommando i en tom mapp för att skapa ett funktionsprojekt utifrån en [Maven-arketyp](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

`-DjavaVersion`Parametern visar de funktioner som används för att använda Java-versionen. Använd `-DjavaVersion=11` om du vill att funktionerna ska köras på Java 11. När du inte anger `-DjavaVersion` maven standardvärdet Java 8. Mer information finns i [Java-versioner](functions-reference-java.md#java-versions).

> [!IMPORTANT]
> `JAVA_HOME`Miljövariabeln måste anges till installations platsen för rätt version av JDK för att slutföra den här artikeln.

Maven ber dig om värden som behövs för att slutföra genereringen av projektet vid distribution.   
Ange följande värden när du uppmanas till det:

| Prompt | Värde | Beskrivning |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Ett värde som unikt identifierar ditt projekt i alla projekt, efter [paket namngivnings regler](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) för Java. |
| **artifactId** | `fabrikam-functions` | Ett värde som är jar-namn, utan versions nummer. |
| **2.0.1** | `1.0-SNAPSHOT` | Välj standardvärdet. |
| **paketfilerna** | `com.fabrikam.functions` | Ett värde som är Java-paketet för den genererade funktions koden. Använd standardvärdet. |

Skriv `Y` eller tryck på RETUR för att bekräfta.

Maven skapar projektfilerna i en ny mapp med namnet _artifactId_, som i det här exemplet är `fabrikam-functions` . 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init LocalFunctionsProject --worker-runtime custom --docker
```
::: zone-end

`--docker`Alternativet genererar en `Dockerfile` för projektet, som definierar en lämplig anpassad behållare för användning med Azure Functions och den valda körningen.

Navigera till projektmappen:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
```console
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Lägg till en funktion i projektet med hjälp av följande kommando, där `--name` argumentet är det unika namnet för din funktion och `--template` argumentet anger funktionens utlösare. `func new` skapar en undermapp som matchar funktions namnet som innehåller en kod fil som är lämplig för projektets valda språk och en konfigurations fil med namnet *function.jspå*.

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end

::: zone pivot="programming-language-other" 
Lägg till en funktion i projektet med hjälp av följande kommando, där `--name` argumentet är det unika namnet för din funktion och `--template` argumentet anger funktionens utlösare. `func new` skapar en undermapp som matchar funktions namnet som innehåller en konfigurations fil med namnet *function.jspå*.

```console
func new --name HttpExample --template "HTTP trigger"
```

I en text redigerare skapar du en fil i projektmappen med namnet *hanterare. R*. Lägg till följande innehåll.

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

I *host.jspå*, ändra `customHandler` avsnittet för att konfigurera den anpassade hanterarens start kommando.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

Om du vill testa funktionen lokalt startar du den lokala Azure Functions körnings värd i roten för projektmappen: 
::: zone pivot="programming-language-csharp"  
```console
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

När du ser `HttpExample` slut punkten som visas i utdata går du till `http://localhost:7071/api/HttpExample?name=Functions` . Webbläsaren ska visa ett "Hello"-meddelande som ekon tillbaka `Functions` , värdet som anges till `name` Frågeparametern.

Använd **CTRL** - **C** för att stoppa värden.

## <a name="build-the-container-image-and-test-locally"></a>Bygg behållar avbildningen och testa lokalt

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
Valfritt Granska *Dockerfile* i roten för projektmappen. Dockerfile beskriver den miljö som krävs för att köra Function-appen på Linux.  Den fullständiga listan med bas avbildningar som stöds för Azure Functions finns på [sidan för Azure Functions Base-avbildning](https://hub.docker.com/_/microsoft-azure-functions-base).
::: zone-end

::: zone pivot="programming-language-other"
Granska *Dockerfile* i roten för projektmappen. Dockerfile beskriver den miljö som krävs för att köra Function-appen på Linux. Program för anpassade hanterare använder `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` avbildningen som bas.

Ändra *Dockerfile* för att installera R. Ersätt innehållet i *Dockerfile* med följande.

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

Kör kommandot [Docker build](https://docs.docker.com/engine/reference/commandline/build/) i rotmappen för rotmappen och ange ett namn, och en `azurefunctionsimage` tagg `v1.0.0` . Ersätt `<DOCKER_ID>` med ditt konto-ID för Docker Hub. Det här kommandot skapar Docker-avbildningen för containern.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

När kommandot har slutförts kan du köra den nya behållaren lokalt.
    
Testa versionen genom att köra avbildningen i en lokal behållare med hjälp av kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , ersätta `<DOCKER_ID` med ditt Docker-ID och lägga till port argumentet `-p 8080:80` :

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
När avbildningen körs i en lokal behållare öppnar du en webbläsare till `http://localhost:8080` , som ska Visa platshållarbilden som visas nedan. Avbildningen visas nu eftersom din funktion körs i den lokala behållaren, som i Azure, vilket innebär att den skyddas av en åtkomst nyckel som definierats i *function.jspå* med `"authLevel": "function"` egenskapen. Behållaren har inte publicerats i en Function-app i Azure, men nyckeln är inte tillgänglig ännu. Om du vill testa mot den lokala behållaren, stoppa Docker, ändra egenskapen Authorization till `"authLevel": "anonymous"` , återskapa avbildningen och starta om Docker. Återställ sedan `"authLevel": "function"` i *function.jspå*. Mer information finns i [Authorization Keys](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Platshållarbild som visar att behållaren körs lokalt](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
När avbildningen körs i en lokal behållare bläddrar du till `http://localhost:8080/api/HttpExample?name=Functions` , som ska visa samma "Hej"-meddelande som tidigare. Eftersom maven-archetype genererar en HTTP-utlöst funktion som använder anonym auktorisering, kan du fortfarande anropa funktionen även om den körs i behållaren. 
::: zone-end  

När du har verifierat Function-appen i behållaren, stoppa Docker med **CTRL** + **+ C**.

## <a name="push-the-image-to-docker-hub"></a>Push-överför avbildningen till Docker Hub

Docker Hub är ett behållar register som är värd för avbildningar och tillhandahåller avbildnings-och container tjänster. Om du vill dela din avbildning, som inkluderar distribution till Azure, måste du skicka den till ett register.

1. Om du inte redan har loggat in på Docker gör du det med kommandot [Docker login](https://docs.docker.com/engine/reference/commandline/login/) och ersätter `<docker_id>` med ditt Docker-ID. Med det här kommandot uppmanas du att ange ditt användar namn och lösen ord. Meddelandet "inloggningen lyckades" bekräftar att du är inloggad.

    ```console
    docker login
    ```
    
1. När du har loggat in, push-överför avbildningen till Docker Hub med hjälp av kommandot [Docker push](https://docs.docker.com/engine/reference/commandline/push/) , och Ersätt `<docker_id>` med ditt Docker-ID.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Beroende på din nätverks hastighet kan det ta några minuter att skicka avbildningen första gången (det går mycket snabbare att överföra efterföljande ändringar). Medan du väntar kan du fortsätta till nästa avsnitt och skapa Azure-resurser i en annan terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa stöd för Azure-resurser för din funktion

Om du vill distribuera funktions koden till Azure måste du skapa tre resurser:

- En resurs grupp, som är en logisk behållare för relaterade resurser.
- Ett Azure Storage konto som upprätthåller tillstånd och annan information om dina projekt.
- En Function-app som tillhandahåller miljön för att köra funktions koden. En Function-app mappar till ditt lokala Function-projekt och gör att du kan gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Du kan använda Azure CLI-kommandon för att skapa dessa objekt. Varje kommando innehåller JSON-utdata när den har slutförts.

1. Logga in på Azure med kommandot [AZ login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp med namnet `AzureFunctionsContainers-rg` i `westeurope` regionen. (Du skapar vanligt vis resurs gruppen och resurserna i en region nära dig, med hjälp av en tillgänglig region från `az account list-locations` kommandot.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Du kan inte vara värd för Linux-och Windows-appar i samma resurs grupp. Om du har en befintlig resurs grupp med namnet `AzureFunctionsContainers-rg` med en Windows Function-app eller webbapp måste du använda en annan resurs grupp.
    
1. Skapa ett allmänt lagrings konto i din resurs grupp och region med hjälp av kommandot [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) . I följande exempel ersätter `<storage_name>` du med ett globalt unikt namn som passar dig. Namn måste innehålla mellan 3 och 24 tecken och endast små bokstäver. `Standard_LRS` anger ett typiskt allmänt syftes konto.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Lagrings kontot förväntar sig bara några USD cent för den här självstudien.
    
1. Använd kommandot för att skapa en Premium-plan för Azure Functions med namnet `myPremiumPlan` på pris nivån **elastisk Premium 1** ( `--sku EP1` ) i regionen Europa, västra (), `-location westeurope` eller Använd en lämplig region nära dig) och i en Linux-behållare ( `--is-linux` ).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Vi använder Premium planen här, som kan skalas efter behov. Mer information om värdfunktioner finns i [Azure Functions hosting plans comparison](functions-scale.md) (Jämförelse av Azure Functions-värdplaner). Information om hur du beräknar kostnader finns i [prissättnings sidan för funktioner](https://azure.microsoft.com/pricing/details/functions/).

    Kommandot etablerar även en associerad Azure Application insikts-instans i samma resurs grupp, som du kan använda för att övervaka din Function-app och Visa loggar. Mer information finns i [övervaka Azure Functions](functions-monitoring.md). Instansen debiteras inga kostnader förrän du aktiverar den.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Skapa och konfigurera en Function-app på Azure med avbildningen

En Function-app på Azure hanterar körningen av dina funktioner i värd planen. I det här avsnittet använder du Azure-resurserna från föregående avsnitt för att skapa en Function-app från en avbildning på Docker Hub och konfigurera den med en anslutnings sträng till Azure Storage.

1. Skapa Functions-appen med kommandot [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . I följande exempel ersätter `<storage_name>` du med det namn som du använde i föregående avsnitt för lagrings kontot. Ersätt också `<app_name>` med ett globalt unikt namn som passar dig, och `<docker_id>` med ditt Docker-ID.

    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime <functions runtime stack> --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    ::: zone pivot="programming-language-other"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime custom --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    
    Parametern *Deployment-container-Image-Name* anger vilken avbildning som ska användas för Function-appen. Du kan använda kommandot [AZ functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) för att visa information om den avbildning som används för distribution. Du kan också använda kommandot [AZ functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) för att distribuera från en annan avbildning.

1. Visa anslutnings strängen för det lagrings konto som du skapade med kommandot [AZ Storage Account show-Connection-String](/cli/azure/storage/account) . Ersätt `<storage-name>` med namnet på det lagrings konto som du skapade ovan:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Lägg till den här inställningen i Function-appen med hjälp av kommandot [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) . I följande kommando ersätter du `<app_name>` med namnet på din Function-app och ersätter `<connection_string>` med anslutnings strängen från föregående steg (en lång kodad sträng som börjar med "DefaultEndpointProtocol ="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

    > [!TIP]
    > I bash kan du använda en gränssnitts variabel för att avbilda anslutnings strängen i stället för att använda Urklipp. Använd först följande kommando för att skapa en variabel med anslutnings strängen:
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > Referera sedan till variabeln i det andra kommandot:
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

1. Funktionen kan nu använda den här anslutnings strängen för att komma åt lagrings kontot.

> [!NOTE]    
> Om du publicerar en anpassad avbildning till ett privat behållar konto bör du i stället använda miljövariabler i Dockerfile för anslutnings strängen. Mer information finns i avsnittet om [miljö](https://docs.docker.com/engine/reference/builder/#env). Du bör också ange variablerna `DOCKER_REGISTRY_SERVER_USERNAME` och `DOCKER_REGISTRY_SERVER_PASSWORD` . Om du vill använda värdena måste du återskapa avbildningen, push-överför avbildningen till registret och sedan starta om Function-appen på Azure.

## <a name="verify-your-functions-on-azure"></a>Verifiera dina funktioner i Azure

Med den avbildning som distribueras till Function-appen på Azure kan du nu anropa funktionen via HTTP-begäranden. Eftersom *function.js* definitionen innehåller egenskapen `"authLevel": "function"` måste du först hämta åtkomst nyckeln (kallas även "funktions nyckel") och ta med den som en URL-parameter i alla begär anden till slut punkten.

1. Hämta funktions webb adressen med åtkomst nyckeln (Function) med hjälp av Azure Portal eller med hjälp av Azure CLI med `az rest` kommandot.)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Logga in på Azure Portal och Sök sedan efter och välj **Funktionsapp**.

    1. Välj den funktion som du vill verifiera.

    1. Välj **funktioner** i den vänstra navigerings panelen och välj sedan den funktion som du vill verifiera.

        ![Välj din funktion i Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. Välj **Hämta funktions webb adress**.

        ![Hämta funktions webb adressen från Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. I popup-fönstret väljer du **Standard (funktions nyckel)** och kopierar sedan webb adressen till Urklipp. Nyckeln är en sträng med tecken som följer `?code=` .

        ![Välj standard åtkomst nyckel för funktionen](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > Eftersom din Function-app distribueras som en behållare kan du inte göra ändringar i funktions koden i portalen. Du måste i stället uppdatera projektet i den lokala avbildningen, push-överför avbildningen till registret igen och sedan distribuera om till Azure. Du kan ställa in kontinuerlig distribution i ett senare avsnitt.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. Skapa en URL-sträng i följande format, ersätta `<subscription_id>` , `<resource_group>` och `<app_name>` med ditt Azure-prenumerations-ID, resurs gruppen för din Function-app och namnet på din Function-app:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        URL-adressen kan till exempel se ut så här:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > För enkelhetens skull kan du i stället tilldela URL: en till en miljö variabel och använda den i `az rest` kommandot.
    
    1. Kör följande `az rest` kommando (tillgängligt i Azure CLI-versionen 2.0.77 och senare) och Ersätt `<uri>` med Uri-strängen från det sista steget, inklusive citat tecken:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Kommandots utdata är funktions tangenten. URL: en för fullständig funktion är sedan att `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>` ersätta `<app_name>` , `<function_name>` och `<key>` med dina egna värden.
    
        > [!NOTE]
        > Nyckeln som hämtas här är *värd* nyckeln som fungerar för alla funktioner i functions-appen. metoden som visas för portalen hämtar bara nyckeln för den enda funktionen.

    ---

1. Klistra in funktions webb adressen i webbläsarens Adress fält och Lägg till parametern `&name=Azure` i slutet av den här URL: en. Text som "Hello, Azure" ska visas i webbläsaren.

    ![Funktionssvar i webbläsaren.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Om du vill testa auktorisering tar `code=` du bort parametern från URL: en och kontrollerar att du inte får något svar från funktionen.


## <a name="enable-continuous-deployment-to-azure"></a>Aktivera kontinuerlig distribution till Azure

Du kan aktivera Azure Functions att automatiskt uppdatera distributionen av en avbildning varje gång du uppdaterar avbildningen i registret.

1. Aktivera kontinuerlig distribution med hjälp av kommandot [AZ functionapp Deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) och Ersätt `<app_name>` med namnet på din Function-app:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Det här kommandot aktiverar kontinuerlig distribution och returnerar distributions-webhook-URL: en. (Du kan hämta denna URL vid ett senare tillfälle genom att använda kommandot [AZ functionapp Deployment container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) .)

1. Kopiera distributions-webhook-URL: en till Urklipp.

1. Öppna [Docker Hub](https://hub.docker.com/), logga in och välj **databaser** i navigerings fältet. Leta upp och välj bild, Välj fliken **Webhooks** , ange ett **webhook-namn**, klistra in URL: en i **webhook-URL** och välj sedan **skapa**:

    ![Lägg till webhooken i din DockerHub-lagrings platsen](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Med webhook-uppsättningen, Azure Functions distribuerar avbildningen igen när du uppdaterar den i Docker Hub.

## <a name="enable-ssh-connections"></a>Aktivera SSH-anslutningar

SSH möjliggör säker kommunikation mellan en container och en klient. Med SSH aktiverat kan du ansluta till din behållare med hjälp av App Service Advanced tools (kudu). För att göra det enkelt att ansluta till din behållare med hjälp av SSH tillhandahåller Azure Functions en bas avbildning med SSH redan aktive rad. Du behöver bara redigera din Dockerfile, sedan återskapa och omdistribuera avbildningen. Sedan kan du ansluta till behållaren via avancerade verktyg (kudu)

1. I din Dockerfile lägger du till strängen i `-appservice` bas avbildningen i din `FROM` instruktion:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. Återskapa avbildningen med hjälp av `docker build` kommandot igen och Ersätt `<docker_id>` med ditt Docker-ID:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Push-överför den uppdaterade avbildningen till Docker Hub, vilket bör ta avsevärt kortare tid än den första push-gränsen som bara de uppdaterade segmenten i bilden behöver överföras.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions distribuerar automatiskt om avbildningen till Functions-appen. processen äger rum på mindre än en minut.

1. Öppna i en webbläsare och `https://<app_name>.scm.azurewebsites.net/` Ersätt `<app_name>` med ditt unika namn. Den här URL: en är kudu-slutpunkten (Advanced tools) för din Function app-behållare.

1. Logga in på ditt Azure-konto och välj sedan **SSH** för att upprätta en anslutning till behållaren. Det kan ta en stund att ansluta, om Azure fortfarande uppdaterar behållar avbildningen.

1. När en anslutning har upprättats med din behållare kör du `top` kommandot för att visa de processer som körs. 

    ![Linux Top-kommando som körs i en SSH-session](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-an-azure-storage-queue"></a>Skriv till en Azure Storage kö

Med Azure Functions kan du ansluta dina funktioner till andra Azure-tjänster och-resurser utan att behöva skriva din egen integrerings kod. Dessa *bindningar*, som representerar både indata och utdata, deklareras i funktions definitionen. Data från bindningar skickas som parametrar till funktionen. En *utlösare* är en särskild typ av ingående bindning. Även om en funktion bara har en utlösare, kan den ha flera indata och utdata-bindningar. Mer information finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

I det här avsnittet visas hur du integrerar din funktion med en Azure Storage kö. Den utgående bindning som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>Lägg till kod för att använda utgående bindning

När du har definierat kökvoter kan du nu uppdatera din funktion för att ta emot `msg` Utdataparametern och skriva meddelanden till kön.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>Uppdatera avbildningen i registret

1. I rotmappen kör du `docker build` igen, och den här gången uppdaterar du versionen i taggen till `v1.0.1` . Som tidigare ersätter `<docker_id>` du med ditt Docker Hub-konto-ID:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Skicka tillbaka den uppdaterade avbildningen till lagrings platsen med `docker push` :

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Eftersom du konfigurerade kontinuerlig leverans uppdaterar avbildningen i registret igen automatiskt din Function-app i Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visa meddelandet i Azure Storage kön

I en webbläsare använder du samma URL som innan du anropar din funktion. Webbläsaren ska visa samma svar som tidigare eftersom du inte har ändrat den delen av funktions koden. Den tillagda koden skrev dock ett meddelande med URL- `name` parametern till `outqueue` lagrings kön.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta att arbeta med Azure-funktionen med hjälp av de resurser som du skapade i den här självstudien kan du lämna alla dessa resurser på plats. Eftersom du har skapat en Premium-plan för Azure Functions får du en eller två USD per dag i löpande kostnad.

Ta bort `AzureFunctionsContainer-rg` resurs gruppen för att rensa alla resurser i gruppen för att undvika löpande kostnader: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Nästa steg

+ [Övervaknings funktioner](functions-monitoring.md)
+ [Skalnings-och värd alternativ](functions-scale.md)
+ [Kubernetes-baserad server lös värd](functions-kubernetes-keda.md)
