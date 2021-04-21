---
title: Anpassade allokeringsprinciper med Azure IoT Hub Device Provisioning Service
description: Så här använder du anpassade allokeringsprinciper med Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/26/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 1432aee341509d8a5bdc9fffe89dd9bad33fc7de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766505"
---
# <a name="how-to-use-custom-allocation-policies"></a>Så här använder du anpassade allokeringsprinciper

En anpassad allokeringsprincip ger dig mer kontroll över hur enheter tilldelas till en IoT-hubb. Detta åstadkoms genom att använda anpassad kod i [en Azure-funktion för](../azure-functions/functions-overview.md) att tilldela enheter till en IoT-hubb. Enhetsetableringstjänsten anropar din Azure-funktionskod och ger all relevant information om enheten och registreringen. Funktionskoden körs och returnerar IoT Hub-informationen som används för att etablera enheten.

Genom att använda anpassade allokeringsprinciper definierar du egna allokeringsprinciper när principerna som tillhandahålls av enhetsetableringstjänsten inte uppfyller kraven för ditt scenario.

Du kanske till exempel vill undersöka certifikatet som en enhet använder under etableringen och tilldela enheten till en IoT-hubb baserat på en certifikategenskap. Eller så kanske du har information som lagras i en databas för dina enheter och behöver fråga databasen för att avgöra vilken IoT-hubb en enhet ska tilldelas till.

Den här artikeln visar en anpassad allokeringsprincip med hjälp av en Azure-funktion som skrivits i C#. Två nya IoT-hubbar skapas som representerar *en Contoso Toasters Division* och *Contoso Heat Pump Division*. Enheter som begär etablering måste ha ett registrerings-ID med något av följande suffix för att godkännas för etablering:

* **-contoso-tstrsd-007:** Contoso Toasters Division
* **-contoso-hpsd-088:** Contoso Heat Pump Division

Enheterna etableras baserat på något av dessa suffix som krävs för registrerings-ID:t. Dessa enheter simuleras med hjälp av ett etableringsexempel som ingår i [Azure IoT C SDK.](https://github.com/Azure/azure-iot-sdk-c)

Du utför följande steg i den här artikeln:

* Använd Azure CLI för att skapa två Contoso-divisions-IoT-hubbar **(Contoso Toasters Division** och **Contoso Heat Pump Division)**
* Skapa en ny gruppregistrering med hjälp av en Azure-funktion för den anpassade allokeringsprincipen
* Skapa enhetsnycklar för två enhetssimuleringar.
* Konfigurera utvecklingsmiljön för Azure IoT C SDK
* Simulera enheterna och kontrollera att de har etablerats enligt exempelkoden i den anpassade allokeringsprincipen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Följande krav gäller för en Windows-utvecklingsmiljö. För Linux eller macOS, se lämpligt avsnitt i [Förbereda utvecklingsmiljön](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) i SDK-dokumentationen.

- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 med [arbetsbelastningen "Desktop development with C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) (Skrivbordsutveckling med C++) aktiverad. Visual Studio 2015 och Visual Studio 2017 stöds också.

- Senaste versionen av [Git](https://git-scm.com/download/) installerad.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Skapa etableringstjänsten och två divisionella IoT-hubbar

I det här avsnittet använder du Azure Cloud Shell för att skapa en etableringstjänst och två IoT-hubbar som representerar **Contosos popup-avdelning** och **Contosos heatpump-avdelning**.

> [!TIP]
> Kommandona som används i den här artikeln skapar etableringstjänsten och andra resurser på platsen USA, västra. Vi rekommenderar att du skapar dina resurser i den region närmast dig som har stöd för Enhetsetableringstjänsten. Du kan se en lista med tillgängliga platser genom att köra kommandot `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`, eller genom att gå till sidan [Azure-status](https://azure.microsoft.com/status/) och söka efter ”enhetsetableringstjänst”. I kommandon kan platser anges antingen i ett eller flera ord. till exempel: USA, västra, USA, västra osv. Värdet är inte fallkänsligt. Om du använder flera ord för att ange platsen skriver du värdet inom citattecken; till exempel `-- location "West US"`.
>

1. Använd Azure Cloud Shell för att skapa en resursgrupp med [kommandot az group](/cli/azure/group#az_group_create) create. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

    I följande exempel skapas en resursgrupp med *namnet contoso-us-resource-group* i *regionen westus.* Vi rekommenderar att du använder den här gruppen för alla resurser som skapas i den här artikeln. Den här metoden gör rensningen enklare när du är klar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Använd Azure Cloud Shell för att skapa en enhetsetableringstjänst (DPS) med [kommandot az iot dps create.](/cli/azure/iot/dps#az_iot_dps_create) Etableringstjänsten läggs till i *contoso-us-resource-group*.

    I följande exempel skapas en etableringstjänst med namnet *contoso-provisioning-service-1098* på *platsen westus.* Du måste använda ett unikt tjänstnamn. Skapa ett eget suffix i tjänstnamnet i stället för **1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Det kan ta några minuter att slutföra kommandot.

3. Använd den Azure Cloud Shell för att skapa **Contoso Toasters Division** IoT-hubben [med kommandot az iot hub create.](/cli/azure/iot/hub#az_iot_hub_create) IoT-hubben läggs till i *contoso-us-resource-group*.

    I följande exempel skapas en IoT-hubb med namnet *contoso-toasters-hub-1098* på *platsen usa,* västra. Du måste använda ett unikt hubbnamn. Skapa ett eget suffix i hubbnamnet i stället för **1098**. 

    > [!CAUTION]
    > Azure Function-exempelkoden för den anpassade allokeringsprincipen kräver `-toasters-` delsträngen i hubbnamnet. Se till att använda ett namn som innehåller den nödvändiga understrängen för popup-meddelanden.
    
    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Det kan ta några minuter att slutföra kommandot.

4. Använd den Azure Cloud Shell för att skapa **Contoso Heat Pump Division** IoT-hubben med [kommandot az iot hub create.](/cli/azure/iot/hub#az_iot_hub_create) Den här IoT-hubben läggs också till *i contoso-us-resource-group*.

    I följande exempel skapas en IoT-hubb med namnet *contoso-heatpumps-hub-1098* på *platsen usa,* västra. Du måste använda ett unikt hubbnamn. Skapa ett eget suffix i hubbnamnet i stället för **1098**. 

    > [!CAUTION]
    > Azure Function-exempelkoden för den anpassade allokeringsprincipen kräver `-heatpumps-` delsträngen i hubbnamnet. Se till att använda ett namn som innehåller den nödvändiga delsträngen för heatpumps.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Det kan ta några minuter att slutföra kommandot.

5. IoT-hubbarna måste vara länkade till DPS-resursen. 

    Kör följande två kommandon för att hämta anslutningssträngarna för hubbarna som du nyss skapade. Ersätt hubbresursnamnen med de namn som du valde i varje kommando:

    ```azurecli-interactive 
    hubToastersConnectionString=$(az iot hub connection-string show --hub-name contoso-toasters-hub-1098 --key primary --query connectionString -o tsv)
    hubHeatpumpsConnectionString=$(az iot hub connection-string show --hub-name contoso-heatpumps-hub-1098 --key primary --query connectionString -o tsv)
    ```

    Kör följande kommandon för att länka hubbarna till DPS-resursen. Ersätt DPS-resursnamnet med det namn som du valde i varje kommando:

    ```azurecli-interactive 
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubToastersConnectionString --location westus
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubHeatpumpsConnectionString --location westus
    ```




## <a name="create-the-custom-allocation-function"></a>Skapa den anpassade allokeringsfunktionen

I det här avsnittet skapar du en Azure-funktion som implementerar din anpassade allokeringsprincip. Den här funktionen avgör vilken divisionell IoT-hubb en enhet ska registreras på baserat på om dess registrerings-ID innehåller strängen **-contoso-tstrsd-007** eller **-contoso-hpsd-088.** Den anger även enhetstvillingens initiala tillstånd baserat på om enheten är en brödrosten eller en värmepump.

1. Logga in på [Azure-portalen](https://portal.azure.com). På startsidan väljer du **+ Skapa en resurs**.

2. I *sökrutan Sök på Marketplace* skriver du "Funktionsapp". I listrutan väljer du **Funktionsapp** och sedan **Skapa.**

3. På **sidan Skapa** funktionsapp går du till fliken **Grundläggande** inställningar, anger följande inställningar för den nya funktionsappen och väljer Granska **+ skapa:**

    **Resursgrupp:** Välj **gruppen contoso-us-resource-group för** att hålla ihop alla resurser som skapats i den här artikeln.

    **Funktionsappens namn:** Ange ett unikt funktionsappnamn. I det här **exemplet används contoso-function-app-1098**.

    **Publicera:** Kontrollera att **Kod** har valts.

    **Körningsstack:** **Välj .NET Core** i listrutan.

    **Version:** **Välj 3.1** i listrutan.

    **Region:** Välj samma region som resursgruppen. I det här exemplet **används USA, västra**.

    > [!NOTE]
    > Som standard är Application Insights aktiverat. Application Insights är inte nödvändigt för den här artikeln, men det kan hjälpa dig att förstå och undersöka eventuella problem som uppstår med den anpassade allokeringen. Om du vill kan du inaktivera Application Insights genom att välja **fliken Övervakning** och sedan välja **Nej för** Aktivera **Application Insights**.

    ![Skapa en Azure-funktionsapp som värd för den anpassade allokeringsfunktionen](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. På sidan **Sammanfattning** väljer du **Skapa för** att skapa funktionsappen. Distributionen kan ta flera minuter. När det är klart väljer du **Gå till resurs**.

5. I den vänstra rutan på funktionsappens **översiktssida** klickar du på **Funktioner** och sedan **på + Lägg till för** att lägga till en ny funktion.

6. På sidan **Lägg till funktion** klickar du på **HTTP-utlösare** och sedan på **knappen Lägg** till.

7. På nästa sida klickar du på **Kod + Test.** På så sätt kan du redigera koden för funktionen **httpTrigger1.** Kodfilen **run.csx** bör öppnas för redigering.

8. Referera till nödvändiga NuGet-paket. För att skapa den första enhetstvillingen använder den anpassade allokeringsfunktionen klasser som definieras i två NuGet-paket som måste läsas in i värdmiljön. Med Azure Functions refereras NuGet-paket med hjälp av en *function.proj-fil.* I det här steget sparar och laddar du upp en *function.proj-fil* för de nödvändiga sammansättningarna.  Mer information finns i Använda [NuGet-paket med Azure Functions](../azure-functions/functions-reference-csharp.md#using-nuget-packages).

    1. Kopiera följande rader till din favoritredigerare och spara filen på datorn som *function.proj*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.16.3" />
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.27.0" />
            </ItemGroup>  
        </Project>
        ```

    2. Klicka på **knappen** Ladda upp ovanför kodredigeraren för att ladda upp *filen function.proj.* När du har överfört väljer du filen i kodredigeraren med hjälp av listrutan för att verifiera innehållet.

9. Kontrollera att *run.csx* för **HttpTrigger1** är markerat i kodredigeraren. Ersätt koden för funktionen **HttpTrigger1** med följande kod och välj **Spara:**

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>Skapa registreringen

I det här avsnittet skapar du en ny registreringsgrupp som använder den anpassade allokeringsprincipen. För enkelhetens skull använder den här [artikeln attestation för symmetrisk](concepts-symmetric-key-attestation.md) nyckel med registreringen. För en säkrare lösning kan du överväga att [använda X.509-certifikatattestning](concepts-x509-attestation.md) med en förtroendekedja.

1. Öppna [etableringstjänsten Azure Portal](https://portal.azure.com)på den nya datorn.

2. Välj **Hantera registreringar** i det vänstra fönstret och välj sedan **knappen Lägg till** registreringsgrupp högst upp på sidan.

3. På **Lägg till registreringsgrupp** anger du följande information och väljer **knappen** Spara.

    **Gruppnamn:** Ange **contoso-custom-allocated-devices**.

    **Attestationstyp:** Välj **Symmetrisk nyckel.**

    **Generera nycklar automatiskt:** Den här kryssrutan bör redan vara markerad.

    **Välj hur du vill tilldela enheter till hubbar:** Välj **Anpassad (Använd Azure-funktion).**

    **Prenumeration:** Välj den prenumeration där du skapade din Azure-funktion.

    **Funktionsapp:** Välj funktionsappen efter namn. **contoso-function-app-1098** användes i det här exemplet.

    **Funktion:** Välj **funktionen HttpTrigger1.**

    ![Lägga till anpassad allokeringsregistreringsgrupp för symmetrisk nyckel atterstation](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. När du har sparat registreringen öppnar du den igen och anteckning om **primärnyckeln**. Du måste spara registreringen först för att få nycklarna genererade. Den här nyckeln används för att generera unika enhetsnycklar för simulerade enheter senare.

## <a name="derive-unique-device-keys"></a>Härled unika enhetsnycklar

I det här avsnittet skapar du två unika enhetsnycklar. En nyckel används för en simulerad popup-enhet. Den andra nyckeln används för en simulerad värmepumpenhet.

För att generera enhetsnyckeln  använder du den primärnyckel som du antecknade tidigare för att beräkna [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) för enhetsregistrerings-ID:t för varje enhet och konvertera resultatet till Base64-format. Mer information om hur du skapar härledda enhetsnycklar med registreringsgrupper finns i avsnittet om gruppregistreringar [i Attestation för symmetrisk nyckel.](concepts-symmetric-key-attestation.md)

I exemplet i den här artikeln använder du följande två enhetsregistrerings-ID:er och beräknar en enhetsnyckel för båda enheterna. Båda registrerings-ID:na har ett giltigt suffix för att fungera med exempelkoden för den anpassade allokeringsprincipen:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**


# <a name="windows"></a>[Windows](#tab/windows)

Om du använder en Windows-baserad arbetsstation kan du använda PowerShell för att generera den härledda enhetsnyckeln enligt följande exempel.

Ersätt värdet för **KEY med** den **primärnyckel som** du antecknade tidigare.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='breakroom499-contoso-tstrsd-007'
$REG_ID2='mainbuilding167-contoso-hpsd-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

# <a name="linux"></a>[Linux](#tab/linux)

Om du använder en Linux-arbetsstation kan du använda openssl för att generera dina härledda enhetsnycklar enligt följande exempel.

Ersätt värdet för **KEY med** den **primärnyckel som** du antecknade tidigare.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=breakroom499-contoso-tstrsd-007
REG_ID2=mainbuilding167-contoso-hpsd-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---

De simulerade enheterna använder de härledda enhetsnycklarna med varje registrerings-ID för att utföra symmetrisk nyckelaterering.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Förbereda en utvecklingsmiljö för Azure IoT C SDK

I det här avsnittet förbereder du utvecklingsmiljön som används för att skapa [Azure IoT C SDK.](https://github.com/Azure/azure-iot-sdk-c) SDK:n innehåller exempelkoden för den simulerade enheten. Den här simulerade enheten försöker etablera under enhetens startsekvens.

Det här avsnittet är inriktat på en Windows-baserad arbetsstation. Ett Linux-exempel finns i konfigurera de virtuella datorerna i [Så här etablerar du för flera innehavare.](how-to-provision-multitenant.md)

1. Ladda ned [CMake-byggsystemet](https://cmake.org/download/).

    Det är viktigt att förutsättningarna för Visual Studio (Visual Studio och arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++)) är installerade på datorn **innan** installationen av `CMake` påbörjas. När förutsättningarna är uppfyllda och nedladdningen har verifierats installerar du CMake-byggesystemet.

2. Leta reda på taggnamnet för [den senaste versionen](https://github.com/Azure/azure-iot-sdk-c/releases/latest) av SDK.

3. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommandon för att klona den senaste versionen av [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen. Använd taggen som du hittade i föregående steg som värde för `-b` parametern :

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

4. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. Kör följande kommandon från `azure-iot-sdk-c` katalogen :

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Kör följande kommando som skapar en version av SDK:t som är specifik för plattformen i din utvecklingsklient. En Visual Studio-lösning för den simulerade enheten genereras i `cmake`-katalogen. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Om `cmake` inte hittar din C++-kompilator kan du få kompileringsfel när du kör kommandot. Om det händer kan du prova att köra kommandot i [Visual Studio kommandotolken](/dotnet/framework/tools/developer-command-prompt-for-vs).

    När bygget är klart ser de sista utdataraderna ut ungefär som följande utdata:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="simulate-the-devices"></a>Simulera enheterna

I det här avsnittet uppdaterar du ett etableringsexempel med namnet **prov dev client sample \_ \_ \_ som** finns i den Azure IoT C SDK som du konfigurerade tidigare.

Den här exempelkoden simulerar en enhetsstartsekvens som skickar etableringsbegäran till instansen av enhetsetableringstjänsten. Startsekvensen gör att popup-enheten identifieras och tilldelas till IoT-hubben med hjälp av den anpassade allokeringsprincipen.

1. I Azure-portalen väljer du fliken **Översikt** för enhetsetableringstjänsten och noterar värdet för **_ID-omfång_**.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. I Visual Studio du **lösningsfilen azure_iot_sdks.sln** som genererades av att CMake kördes tidigare. Lösningsfilen bör vara på följande plats:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. I fönstret *Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Exempel**. Expandera exempelprojektet som heter **prov\_dev\_client\_sample**. Expandera **Källfiler** och öppna **prov\_dev\_client\_sample.c**.

4. Hitta konstanten `id_scope` och ersätt värdet med ditt värde för **ID-omfång** som du kopierade tidigare. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Hitta definitionen för funktionen `main()` i samma fil. Kontrollera att variabeln `hsm_type` är inställd på `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` enligt nedan:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Högerklicka på projektet **prov\_dev\_client\_sample** och välj **Set as Startup Project** (Ange som startprojekt).

### <a name="simulate-the-contoso-toaster-device"></a>Simulera Contosos popup-enhet

1. Om du vill simulera popup-enheten hittar du anropet till `prov_dev_set_symmetric_key_info()` i prov dev client **\_ \_ \_ sample.c** som är utkommenterat.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Avkommentera funktionsanropet och ersätt platshållarvärdena (inklusive vinkelparenteserna) med registrerings-ID:t för popup-brödtexten och den härledda enhetsnyckeln som du genererade tidigare. Nyckelvärdet **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** som visas nedan anges endast som ett exempel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Spara filen.

2. På Visual Studio väljer du **Felsök** Starta utan felsökning  >  **för att** köra lösningen. I kommandotolken för att återskapa projektet väljer du **Ja** för att återskapa projektet innan du kör det.

    Följande utdata är ett exempel på när den simulerade popup-enheten har startats och anslutits till etableringstjänstinstansen som ska tilldelas till popup-ioT-hubben av den anpassade allokeringsprincipen:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simulera Contosos värmepumpenhet

1. För att simulera enheten för värmepump uppdaterar du anropet till i `prov_dev_set_symmetric_key_info()` **prov dev client \_ \_ \_ sample.c** igen med registrerings-ID:t för värmepumpen och den härledda enhetsnyckeln som du genererade tidigare. Nyckelvärdet **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** som visas nedan anges också som ett exempel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Spara filen.

2. På Visual Studio väljer du **Felsök**  >  **Starta utan felsökning för** att köra lösningen. I kommandotolken för att återskapa projektet väljer du **Ja för** att återskapa projektet innan du kör det.

    Följande utdata är ett exempel på att den simulerade värmepumpenheten har startats och anslutits till etableringstjänstinstansen som ska tilldelas Contosos heat pumpar IoT Hub av den anpassade allokeringsprincipen:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Felsöka anpassade allokeringsprinciper

I följande tabell visas förväntade scenarier och de felkoder som du kan få. Använd den här tabellen för att felsöka fel med anpassade allokeringsprinciper Azure Functions.

| Scenario | Registreringsresultat från etableringstjänsten | Resultat för etablerings-SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Webhooken returnerar 200 OK med "iotHubHostName" inställt på ett giltigt IoT Hub-värdnamn | Resultatstatus: Tilldelad  | SDK returnerar PROV_DEVICE_RESULT_OK tillsammans med hubbinformation |
| Webhooken returnerar 200 OK med "iotHubHostName" i svaret, men inställd på en tom sträng eller null | Resultatstatus: Misslyckades<br><br> Felkod: CustomAllocationIotHubNotSpecified (400208) | SDK returnerar PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhooken returnerar 401 Unauthorized (Obehörig) | Resultatstatus: Misslyckades<br><br>Felkod: CustomAllocationUnauthorizedAccess (400209) | SDK returnerar PROV_DEVICE_RESULT_UNAUTHORIZED |
| En enskild registrering har skapats för att inaktivera enheten | Resultatstatus: Inaktiverad | SDK returnerar PROV_DEVICE_RESULT_DISABLED |
| Webhooken returnerar felkoden >= 429 | DPS-orkestrering försöker igen ett antal gånger. Återförsöksprincipen är för närvarande:<br><br>&nbsp;&nbsp;– Antal återförsök: 10<br>&nbsp;&nbsp;- Inledande intervall: 1:e<br>&nbsp;&nbsp;- Ökning: 9:e | SDK ignorerar felet och skickar ett nytt get-statusmeddelande under den angivna tiden |
| Webhooken returnerar annan statuskod | Resultatstatus: Misslyckades<br><br>Felkod: CustomAllocationFailed (400207) | SDK returnerar PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med resurserna som skapats i den här artikeln kan du lämna dem. Om du inte planerar att fortsätta använda resurserna kan du använda följande steg för att ta bort alla resurser som skapats i den här artikeln för att undvika onödiga kostnader.

Stegen här förutsätter att du har skapat alla resurser i den här artikeln enligt anvisningarna i samma resursgrupp med namnet **contoso-us-resource-group**.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Så här tar du bort resursgruppen efter namn:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.

2. I **textrutan Filtrera efter namn...** skriver du namnet på resursgruppen som innehåller dina **resurser, contoso-us-resource-group**. 

3. Till höger om resursgruppen i resultatlistan väljer du ... och **sedan** Ta **bort resursgrupp**.

4. Du uppmanas att bekräfta borttagningen av resursgruppen. Skriv namnet på resursgruppen igen för att bekräfta och välj sedan Ta **bort.** Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

* Mer information om ometablering finns i [IoT Hub avetableringsbegrepp för enheter](concepts-device-reprovision.md) 
* Mer information om avetablering finns i [Avetablera enheter som tidigare har avetableats automatiskt](how-to-unprovision-devices.md)