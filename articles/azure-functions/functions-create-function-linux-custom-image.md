---
title: Skapa Azure Functions i Linux med en anpassad avbildning
description: Lär dig hur du skapar en Azure Functions som körs på en anpassad Linux-avbildning.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: 7950bfb4a57db812da87f4e5f76f3075d50a8293
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782285"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Skapa en funktion i Linux med en anpassad container

I den här självstudien skapar och distribuerar du din kod till Azure Functions en anpassad Docker-container med hjälp av en Linux-basavbildning. Du använder vanligtvis en anpassad avbildning när dina funktioner kräver en specifik språkversion eller har ett specifikt beroende eller en specifik konfiguration som inte tillhandahålls av den inbyggda avbildningen.

::: zone pivot="programming-language-other"
Azure Functions har stöd för alla språk eller körningar [med hjälp av anpassade hanterare](functions-custom-handlers.md). För vissa språk, till exempel det R-programmeringsspråk som används i den här självstudien, måste du installera körningen eller ytterligare bibliotek som beroenden som kräver användning av en anpassad container.
::: zone-end

Distribution av funktionskoden i en anpassad Linux-container kräver [en Premium-plan](functions-premium-plan.md) eller en [dedikerad (App Service) planvärd.](dedicated-plan.md) Den här självstudien medför kostnader för några amerikanska dollar i ditt [Azure-konto,](#clean-up-resources) vilket du kan minimera genom att rensa resurser när du är klar.

Du kan också använda en standardcontainer Azure App Service enligt beskrivningen [i Skapa din första funktion som finns på Linux.](./create-first-function-cli-csharp.md?pivots=programming-language-python) Basavbildningar som stöds för Azure Functions finns på [lagringsplatsen Azure Functions basavbildningar.](https://hub.docker.com/_/microsoft-azure-functions-base)

I den här guiden får du lära dig att:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Skapa en funktionsapp och Dockerfile med hjälp av Azure Functions Core Tools.
> * skapa en anpassad avbildning med Docker
> * Publicera en anpassad avbildning till ett containerregister.
> * Skapa stödresurser i Azure för funktionsappen
> * distribuera en funktionsapp från Docker Hub
> * lägga till programinställningar i funktionsappen.
> * Aktivera kontinuerlig distribution.
> * Aktivera SSH-anslutningar till containern.
> * Lägg till en Queue Storage-utdatabindning. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Skapa en funktionsapp och Dockerfile med hjälp av Azure Functions Core Tools.
> * skapa en anpassad avbildning med Docker
> * Publicera en anpassad avbildning till ett containerregister.
> * Skapa stödresurser i Azure för funktionsappen
> * distribuera en funktionsapp från Docker Hub
> * lägga till programinställningar i funktionsappen.
> * Aktivera kontinuerlig distribution.
> * Aktivera SSH-anslutningar till containern.
::: zone-end

Du kan följa den här självstudiekursen på valfri dator som kör Windows, macOS eller Linux. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Ett [Docker-ID](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Kör `docker login` för att logga in på Docker. Det här kommandot misslyckas om Docker inte körs. I så fall startar du Docker och försöker köra kommandot igen.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Skapa och testa det lokala funktionsprojekt

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Kör följande kommando för det valda språket i en terminal eller kommandotolk för att skapa ett funktionsappsprojekt i en mapp med namnet `LocalFunctionsProject` .  
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

Parametern `-DjavaVersion` talar om för Functions körning vilken version av Java som ska användas. Använd `-DjavaVersion=11` om du vill att funktionerna ska köras på Java 11. När du inte anger `-DjavaVersion` får Maven java 8 som standard. Mer information finns i [Java-versioner.](functions-reference-java.md#java-versions)

> [!IMPORTANT]
> `JAVA_HOME`Miljövariabeln måste anges till installationsplatsen för rätt version av JDK för att slutföra den här artikeln.

Maven ber dig om de värden som behövs för att slutföra genereringen av projektet vid distributionen.   
Ange följande värden när du tillfrågas:

| Prompt | Värde | Beskrivning |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Ett värde som unikt identifierar projektet i alla projekt och som följer [namngivningsreglerna för](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) Java. |
| **artifactId** | `fabrikam-functions` | Ett värde som är namnet på jar-et, utan ett versionsnummer. |
| **Version** | `1.0-SNAPSHOT` | Välj standardvärdet. |
| **Paket** | `com.fabrikam.functions` | Ett värde som är Java-paketet för den genererade funktionskoden. Använd standardvärdet. |

Skriv `Y` eller tryck på Retur för att bekräfta.

Maven skapar projektfilerna i en ny mapp med namnet _artifactId_, som i det här exemplet är `fabrikam-functions` . 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init LocalFunctionsProject --worker-runtime custom --docker
```
::: zone-end

Alternativet genererar en för projektet, som definierar en lämplig anpassad `--docker` container för användning med Azure Functions och den valda `Dockerfile` körningen.

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
Lägg till en funktion i projektet med hjälp av följande kommando, där argumentet är det unika namnet på funktionen och argumentet anger `--name` `--template` funktionens utlösare. `func new` skapar en undermapp som matchar funktionsnamnet som innehåller en kodfil som är lämplig för projektets valda språk och en konfigurationsfil med *namnetfunction.jspå*.

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end

::: zone pivot="programming-language-other" 
Lägg till en funktion i projektet med hjälp av följande kommando, där argumentet är det unika namnet på funktionen och argumentet anger `--name` `--template` funktionens utlösare. `func new` skapar en undermapp som matchar funktionsnamnet som innehåller en konfigurationsfil med *namnetfunction.jspå*.

```console
func new --name HttpExample --template "HTTP trigger"
```

Skapa en fil i projektmappen med namnet handler i en *textredigerare. R*. Lägg till följande som innehåll.

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

I *host.jspå* ändrar du avsnittet för att konfigurera den anpassade `customHandler` hanterarens startkommando.

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

Om du vill testa funktionen lokalt startar du den lokala Azure Functions-körningsvärden i projektmappens rot: 
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

När du ser `HttpExample` slutpunkten i utdata navigerar du till `http://localhost:7071/api/HttpExample?name=Functions` . Webbläsaren bör visa ett "hello"-meddelande som returnerar `Functions` , värdet som angetts till `name` frågeparametern.

Använd **Ctrl** - **C** för att stoppa värden.

## <a name="build-the-container-image-and-test-locally"></a>Skapa containeravbildningen och testa lokalt

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(Valfritt) Granska *Dockerfile i* roten av projektmappen. Dockerfile beskriver den miljö som krävs för att köra funktionsappen i Linux.  Den fullständiga listan över basavbildningar som stöds för Azure Functions finns på Azure Functions [basavbildningssidan](https://hub.docker.com/_/microsoft-azure-functions-base).
::: zone-end

::: zone pivot="programming-language-other"
Granska *Dockerfile i* roten av projektmappen. Dockerfile beskriver den miljö som krävs för att köra funktionsappen i Linux. Anpassade hanterarprogram använder `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` avbildningen som bas.

Ändra *Dockerfile för* att installera R. Ersätt innehållet i *Dockerfile* med följande.

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

I rotprojektmappen kör du [kommandot docker build](https://docs.docker.com/engine/reference/commandline/build/) och anger ett namn, `azurefunctionsimage` , och tagg, `v1.0.0` . Ersätt `<DOCKER_ID>` med ditt konto-ID för Docker Hub. Det här kommandot skapar Docker-avbildningen för containern.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

När kommandot har slutförts kan du köra den nya containern lokalt.
    
Testa bygget genom att köra avbildningen i en lokal container med kommandot [docker run.](https://docs.docker.com/engine/reference/commandline/run/) Ersätt igen med ditt Docker-ID och lägg till `<DOCKER_ID` ports-argumentet, `-p 8080:80` :

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
När avbildningen körs i en lokal container öppnar du en webbläsare till `http://localhost:8080` , som ska visa platshållarbilden som visas nedan. Avbildningen visas nu eftersom funktionen körs i den lokala containern, precis som i Azure, vilket innebär att den skyddas av en åtkomstnyckel enligt definitionen i *function.jspå* med `"authLevel": "function"` egenskapen . Containern har ännu inte publicerats till en funktionsapp i Azure, så nyckeln är inte tillgänglig ännu. Om du vill testa mot den lokala containern stoppar du Docker, ändrar auktoriseringsegenskapen till , återskapar `"authLevel": "anonymous"` avbildningen och startar om Docker. Återställ sedan `"authLevel": "function"` *ifunction.jspå*. Mer information finns i [auktoriseringsnycklar.](functions-bindings-http-webhook-trigger.md#authorization-keys)

![Platshållarbild som anger att containern körs lokalt](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
När avbildningen körs i en lokal container bläddrar du till `http://localhost:8080/api/HttpExample?name=Functions` , som ska visa samma "hello"-meddelande som tidigare. Eftersom Maven-arketypen genererar en HTTP-utlöst funktion som använder anonym auktorisering kan du fortfarande anropa funktionen även om den körs i containern. 
::: zone-end  

När du har verifierat funktionsappen i containern stoppar du docker med **Ctrl** + **C**.

## <a name="push-the-image-to-docker-hub"></a>Push-skicka avbildningen till Docker Hub

Docker Hub är ett containerregister som är värd för avbildningar och tillhandahåller avbildnings- och containertjänster. Om du vill dela avbildningen, vilket innefattar distribution till Azure, måste du push-installera den i ett register.

1. Om du inte redan har loggat in på Docker gör du det med [kommandot docker login](https://docs.docker.com/engine/reference/commandline/login/) och ersätter `<docker_id>` med ditt Docker-ID. Det här kommandot uppmanar dig att ange ditt användarnamn och lösenord. Meddelandet "Inloggningen lyckades" bekräftar att du är inloggad.

    ```console
    docker login
    ```
    
1. När du har loggat in kan du push-installera avbildningen Docker Hub kommandot [docker push](https://docs.docker.com/engine/reference/commandline/push/) och återigen ersätta `<docker_id>` med ditt Docker-ID.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Beroende på nätverkets hastighet kan det ta några minuter att push-pusha avbildningen första gången (det går mycket snabbare att push-pusha efterföljande ändringar). Medan du väntar kan du gå vidare till nästa avsnitt och skapa Azure-resurser i en annan terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa azure-stödresurser för din funktion

Om du vill distribuera funktionskoden till Azure måste du skapa tre resurser:

- En resursgrupp, som är en logisk container för relaterade resurser.
- Ett Azure Storage konto, som upprätthåller tillstånd och annan information om dina projekt.
- En funktionsapp som tillhandahåller miljön för att köra funktionskoden. En funktionsapp mappar till ditt lokala funktionsprojekt och gör att du kan gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Du använder Azure CLI-kommandon för att skapa dessa objekt. Varje kommando tillhandahåller JSON-utdata när de har slutförts.

1. Logga in på Azure med [kommandot az login:](/cli/azure/reference-index#az_login)

    ```azurecli
    az login
    ```
    
1. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med `AzureFunctionsContainers-rg` namnet i `westeurope` regionen . (Vanligtvis skapar du din resursgrupp och dina resurser i en region nära dig med hjälp av en tillgänglig region från `az account list-locations` kommandot .)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Du kan inte vara värd för Linux- och Windows-appar i samma resursgrupp. Om du har en befintlig resursgrupp med `AzureFunctionsContainers-rg` namnet med en Windows-funktionsapp eller webbapp måste du använda en annan resursgrupp.
    
1. Skapa ett allmänt lagringskonto i resursgruppen och regionen med kommandot [az storage account](/cli/azure/storage/account#az_storage_account_create) create. I följande exempel ersätter du `<storage_name>` med ett globalt unikt namn som passar dig. Namn får endast innehålla tre till 24 tecken och gemener. `Standard_LRS` anger ett typiskt konto för generell användning.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Lagringskontot ådrar sig bara några cent för den här självstudien.
    
1. Använd kommandot för att skapa en Premium-plan för Azure Functions med namnet på prisnivån Elastic Premium 1 ( ), i regionen Europa, västra ( , eller använd en lämplig region nära dig) och i en `myPremiumPlan`  `--sku EP1` `-location westeurope` Linux-container ( `--is-linux` ).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Vi använder Premium-planen här, som kan skalas efter behov. Mer information om värdfunktioner finns i [Azure Functions hosting plans comparison](functions-scale.md) (Jämförelse av Azure Functions-värdplaner). Om du vill beräkna kostnader kan du gå [till prissättningssidan för Functions.](https://azure.microsoft.com/pricing/details/functions/)

    Kommandot tiller även en associerad Azure Application Insights-instans i samma resursgrupp som du kan använda för att övervaka funktionsappen och visa loggar. Mer information finns i [Övervaka Azure Functions](functions-monitoring.md). Instansen medför inga kostnader förrän du aktiverar den.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Skapa och konfigurera en funktionsapp i Azure med avbildningen

En funktionsapp i Azure hanterar körningen av dina funktioner i din värdplan. I det här avsnittet använder du Azure-resurserna från föregående avsnitt för att skapa en funktionsapp från en bild på Docker Hub och konfigurera den med en anslutningssträng som ska Azure Storage.

1. Skapa Functions-appen med kommandot [az functionapp](/cli/azure/functionapp#az_functionapp_create) create. I följande exempel ersätter du `<storage_name>` med namnet som du använde i föregående avsnitt för lagringskontot. Ersätt också `<app_name>` med ett globalt unikt namn som passar dig och med ditt `<docker_id>` Docker-ID.

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
    
    Parametern *deployment-container-image-name* anger vilken avbildning som ska användas för funktionsappen. Du kan använda kommandot [az functionapp config container show för](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) att visa information om avbildningen som används för distribution. Du kan också använda kommandot [az functionapp config container set för](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) att distribuera från en annan avbildning.

1. Visa anslutningssträngen för lagringskontot som du skapade med kommandot [az storage account show-connection-string.](/cli/azure/storage/account) Ersätt `<storage-name>` med namnet på lagringskontot som du skapade ovan:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Lägg till den här inställningen i funktionsappen med kommandot [az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings#az_functionapp_config_ppsettings_set) I följande kommando ersätter du med namnet på funktionsappen och ersätter med anslutningssträngen från föregående steg (en lång kodad sträng som börjar med `<app_name>` `<connection_string>` "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

    > [!TIP]
    > I Bash kan du använda en gränssnittsvariabel för att avbilda anslutningssträngen i stället för att använda Urklipp. Använd först följande kommando för att skapa en variabel med anslutningssträngen:
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > Se sedan variabeln i det andra kommandot:
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

1. Funktionen kan nu använda den här anslutningssträngen för att komma åt lagringskontot.

> [!NOTE]    
> Om du publicerar din anpassade avbildning till ett privat containerkonto bör du i stället använda miljövariabler i Dockerfile för anslutningssträngen. Mer information finns i [ENV-instruktionen](https://docs.docker.com/engine/reference/builder/#env). Du bör också ange variablerna `DOCKER_REGISTRY_SERVER_USERNAME` och `DOCKER_REGISTRY_SERVER_PASSWORD` . Om du vill använda värdena måste du återskapa avbildningen, push-skicka avbildningen till registret och sedan starta om funktionsappen i Azure.

## <a name="verify-your-functions-on-azure"></a>Verifiera dina funktioner i Azure

När avbildningen har distribuerats till funktionsappen i Azure kan du nu anropa funktionen via HTTP-begäranden. Eftersom *function.js* på-definitionen innehåller egenskapen måste du först hämta åtkomstnyckeln (kallas även "funktionsnyckeln") och inkludera den som en URL-parameter i alla förfrågningar till `"authLevel": "function"` slutpunkten.

1. Hämta funktions-URL:en med åtkomstnyckeln (funktion) med hjälp Azure Portal eller med hjälp av Azure CLI med `az rest` kommandot .)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Logga in på Azure Portal och sök sedan efter och välj **Funktionsapp**.

    1. Välj den funktion som du vill verifiera.

    1. I den vänstra navigeringspanelen väljer **du Funktioner** och sedan den funktion som du vill verifiera.

        ![Välj din funktion i Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. Välj **Hämta funktions-URL.**

        ![Hämta funktions-URL:en från Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. I popup-fönstret väljer du **standard (funktionsnyckel)** och kopierar sedan URL:en till Urklipp. Nyckeln är strängen med tecken som följer `?code=` .

        ![Välj standardåtkomstnyckeln för funktionen](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > Eftersom funktionsappen distribueras som en container kan du inte göra ändringar i funktionskoden i portalen. I stället måste du uppdatera projektet i den lokala avbildningen, push-installera avbildningen till registret igen och sedan distribuera om till Azure. Du kan konfigurera kontinuerlig distribution i ett senare avsnitt.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. Skapa en URL-sträng i följande format och ersätt , och med ditt Prenumerations-ID för Azure, resursgruppen för funktionsappen och namnet på `<subscription_id>` `<resource_group>` din `<app_name>` funktionsapp:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        URL:en kan till exempel se ut så här:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > För enkelhetens skull kan du i stället tilldela URL:en till en miljövariabel och använda den i `az rest` kommandot .
    
    1. Kör följande kommando (tillgängligt i `az rest` Azure CLI version 2.0.77 och senare) och ersätt med URI-strängen från det sista steget, inklusive `<uri>` citattecken:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Kommandots utdata är funktionsnyckeln. Den fullständiga funktions-URL:en `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>` är sedan , där du `<app_name>` `<function_name>` ersätter , och med dina specifika `<key>` värden.
    
        > [!NOTE]
        > Nyckeln som hämtas här är *värdnyckeln som* fungerar för alla funktioner i functions-appen. metoden som visas för portalen hämtar bara nyckeln för den enda funktionen.

    ---

1. Klistra in funktions-URL:en i webbläsarens adressfält och lägg till `&name=Azure` parametern i slutet av url:en. Text som "Hello, Azure" bör visas i webbläsaren.

    ![Funktionssvar i webbläsaren.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Om du vill testa auktoriseringen `code=` tar du bort parametern från URL:en och kontrollerar att du inte får något svar från funktionen.


## <a name="enable-continuous-deployment-to-azure"></a>Aktivera kontinuerlig distribution till Azure

Du kan aktivera Azure Functions att automatiskt uppdatera distributionen av en avbildning när du uppdaterar avbildningen i registret.

1. Aktivera kontinuerlig distribution med kommandot [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_config) och ersätt `<app_name>` med namnet på funktionsappen:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Det här kommandot aktiverar kontinuerlig distribution och returnerar url:en för distributionswebhooken. (Du kan hämta den här URL:en vid ett senare tillfälle med hjälp av kommandot [az functionapp deployment container show-cd-url.)](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_show_cd_url)

1. Kopiera url:en för distributionswebhooken till Urklipp.

1. Öppna [Docker Hub,](https://hub.docker.com/)logga in och **välj Lagringsplatsen** i navigeringsfältet. Leta upp och välj bild, välj fliken **Webhooks,** ange ett **Webhook-namn,** klistra in url:en i **Webhook-URL:en** och välj sedan **Skapa:**

    ![Lägga till webhooken i dockerHub-lagringsplatsen](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. När webhooken har angetts Azure Functions distribuerar om avbildningen när du uppdaterar den i Docker Hub.

## <a name="enable-ssh-connections"></a>Aktivera SSH-anslutningar

SSH möjliggör säker kommunikation mellan en container och en klient. Med SSH aktiverat kan du ansluta till din container med hjälp App Service Avancerade verktyg (Kudu). För att göra det enkelt att ansluta till containern med hjälp av SSH Azure Functions en basavbildning som redan har SSH aktiverat. Du behöver bara redigera din Dockerfile och sedan återskapa och distribuera om avbildningen. Du kan sedan ansluta till containern via Avancerade verktyg (Kudu)

1. I din Dockerfile lägger du till strängen `-appservice` i basavbildningen i `FROM` instruktionen:

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
    
1. Återskapa avbildningen med kommandot `docker build` igen och ersätt med ditt `<docker_id>` Docker-ID:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Push-överför den uppdaterade avbildningen till Docker Hub, vilket bör ta betydligt kortare tid än den första push-överföringen som endast de uppdaterade segmenten i avbildningen behöver laddas upp.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions automatiskt om avbildningen till din functions-app; processen sker på mindre än en minut.

1. Öppna i en webbläsare `https://<app_name>.scm.azurewebsites.net/` och ersätt med ditt unika `<app_name>` namn. Den här URL:en är kudu-slutpunkten (Advanced Tools) för din funktionsappcontainer.

1. Logga in på ditt Azure-konto och välj sedan **SSH för** att upprätta en anslutning till containern. Det kan ta en stund att ansluta om Azure fortfarande uppdaterar containeravbildningen.

1. När en anslutning har upprättats med containern kör du kommandot `top` för att visa de processer som körs. 

    ![Linux-toppkommando som körs i en SSH-session](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-an-azure-storage-queue"></a>Skriva till en Azure Storage kö

Azure Functions kan du ansluta dina funktioner till andra Azure-tjänster och -resurser utan att behöva skriva din egen integreringskod. Dessa bindningar , som representerar både indata och utdata, *deklareras* i funktionsdefinitionen. Data från bindningar skickas som parametrar till funktionen. En *utlösare* är en särskild typ av indatabindning. Även om en funktion bara har en utlösare kan den ha flera indata- och utdatabindningar. Mer information finns i Azure Functions [utlösare och bindningar.](functions-triggers-bindings.md)

Det här avsnittet visar hur du integrerar din funktion med en Azure Storage kö. Den utdatabindning som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

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

## <a name="add-code-to-use-the-output-binding"></a>Lägga till kod för att använda utdatabindningen

När köbindningen har definierats kan du uppdatera funktionen så att den tar emot `msg` utdataparametern och skriver meddelanden till kön.
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

1. I rotmappen kör du `docker build` igen och uppdaterar den här gången versionen i taggen till `v1.0.1` . Som tidigare ersätter du `<docker_id>` med ditt Docker Hub-ID:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Skicka tillbaka den uppdaterade avbildningen till lagringsplatsen med `docker push` :

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Eftersom du har konfigurerat kontinuerlig leverans uppdaterar automatiskt uppdatering av avbildningen i registret automatiskt funktionsappen i Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visa meddelandet i Azure Storage kön

I en webbläsare använder du samma URL som tidigare för att anropa funktionen. Webbläsaren bör visa samma svar som tidigare, eftersom du inte har modifierat den delen av funktionskoden. Den tillagda koden skrev dock ett meddelande med `name` URL-parametern till `outqueue` lagringskön.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta arbeta med Azure Function med hjälp av de resurser som du skapade i den här självstudien kan du lämna alla dessa resurser på plats. Eftersom du har skapat en Premium-plan Azure Functions en eller två USD per dag i löpande kostnader.

För att undvika löpande kostnader tar du `AzureFunctionsContainer-rg` bort resursgruppen för att rensa alla resurser i gruppen: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Nästa steg

+ [Övervakningsfunktioner](functions-monitoring.md)
+ [Skalning och värdalternativ](functions-scale.md)
+ [Kubernetes-baserad serverlös värd](functions-kubernetes-keda.md)
