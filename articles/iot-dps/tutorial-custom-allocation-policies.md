---
title: Självstudie för att använda anpassade allokeringsregler med Azure IoT Hub Device Provisioning Service (DPS)
description: Självstudie för att använda anpassade allokeringsregler med Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f19f43b89cd2527a67827d7434f2e054ee40001e
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227389"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>Självstudie: Använd principer för anpassad allokering med enhets etablerings tjänsten (DPS)

Med en anpassad resursallokeringsprincip får du mer kontroll över hur enheter tilldelas till en IoT-hubb. Detta åstadkommer du genom att använda anpassad kod i en [Azure-funktion](../azure-functions/functions-overview.md) som körs under etableringen för att tilldela enheter till en IoT-hubb. Enhets etablerings tjänsten anropar din Azure Function-kod och ger all relevant information om enheten och registreringen. Funktions koden körs och returnerar IoT Hub-informationen som används för att tillhandahålla enheten.

Genom att använda anpassade allokeringsregler definierar du egna allokeringsregler när principerna som tillhandahålls av enhets etablerings tjänsten inte uppfyller kraven för ditt scenario.

Till exempel kanske du vill undersöka certifikatet som en enhet använder under etableringen och tilldela enheten till en IoT-hubb baserat på en certifikat egenskap. Eller så kanske du har information lagrad i en databas för dina enheter och behöver fråga databasen för att avgöra vilken IoT-hubb en enhet ska tilldelas till.

Den här artikeln visar en registrerings grupp med en anpassad resursallokeringsprincip som använder en Azure-funktion som skrivits i C# för att etablera toaster-enheter med hjälp av symmetriska nycklar. Alla enheter som inte identifieras som en toaster-enhet tillhandahålls inte till en IoT-hubb.

Enheter kommer att begära etablering med hjälp av etablerings exempel kod som ingår i [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).


I den här självstudien ska du göra följande:

> [!div class="checklist"]
> * Skapa en ny Azure-Funktionsapp för att stödja en anpassad fördelnings funktion
> * Skapa en ny grupp registrering med en Azure-funktion för den anpassade allokeringsregeln
> * Skapa enhets nycklar för två enheter
> * Konfigurera utvecklings miljön för exempel enhets kod från [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)
> * Kör enheterna och kontrol lera att de är etablerade enligt principen för anpassad allokering


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* I den här artikeln förutsätter vi att du har slutfört stegen i [konfigurera IoT Hub Device Provisioning service med Azure Portal](./quick-setup-auto-provision.md) för att skapa IoT Hub och DPS-instansen.

* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

* För en Windows-utvecklings miljö krävs [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 med arbets belastningen [Skriv bords utveckling med C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) aktiverat. Visual Studio 2015 och Visual Studio 2017 stöds också.

* För Linux eller macOS, se lämpligt avsnitt i [förbereda din utvecklings miljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) i [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -dokumentationen.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>Skapa en anpassad tilldelnings funktion

I det här avsnittet skapar du en Azure-funktion som implementerar din anpassade resursallokeringsprincip. Den här funktionen avgör om en enhet ska registreras på din IoT Hub baserat på om dess registrerings-ID innehåller strängvärdet **contoso-toaster**.

1. Logga in på [Azure-portalen](https://portal.azure.com). Från start sidan väljer du **+ skapa en resurs**.

2. Skriv "Funktionsapp" i Sök rutan *Sök i Marketplace* . Välj **Funktionsapp** i list rutan och välj sedan **skapa**.

3. På sidan för **Funktionsapp** skapa, under fliken **grundläggande** , anger du följande inställningar för den nya Function-appen och väljer **Granska + skapa**:

    **Prenumeration**: om du har flera prenumerationer och den önskade prenumerationen inte är markerad väljer du den prenumeration som du vill använda.

    **Resurs grupp**: med det här fältet kan du skapa en ny resurs grupp eller välja en befintlig som innehåller Function-appen. Välj samma resurs grupp som innehåller IoT-hubben som du skapade för testning tidigare, till exempel **TestResources**. Genom att lägga till alla relaterade resurser i en grupp kan du hantera dem tillsammans.

    **Funktionsapp namn**: Ange ett unikt namn för Function-appen. I det här exemplet används **contoso-Function-app**.

    **Publicera**: kontrol lera att **koden** är markerad.

    **Körnings stack**: Välj **.net Core** i list rutan.

    **Region**: Välj samma region som din resurs grupp. I det här exemplet används **västra USA**.

    > [!NOTE]
    > Som standard är Application Insights aktive rad. Application Insights krävs inte för den här artikeln, men det kan hjälpa dig att förstå och undersöka eventuella problem med den anpassade allokeringen. Om du vill kan du inaktivera Application Insights genom att välja fliken **övervakning** och sedan välja **nej** för **Aktivera Application Insights**.

    ![Skapa en Azure-Funktionsapp som värd för den anpassade tilldelnings funktionen](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. På sidan **Sammanfattning** väljer du **skapa** för att skapa Function-appen. Distributionen kan ta flera minuter. När den är klar väljer **du gå till resurs**.

5. I den vänstra rutan under **funktioner** klickar du på **funktioner** och sedan **+ Lägg** till för att lägga till en ny funktion.

6. På sidan mallar väljer du panelen **http-utlösare** och välj sedan **skapa funktion**. En funktion med namnet **HttpTrigger1** skapas och portalen visar översikts sidan för din funktion.

7. Klicka på **kod + test** för den nya funktionen. Portalen visar innehållet i kod filen **Run. CSX** . 

8. Ersätt koden för **HttpTrigger1** -funktionen med följande kod och välj **Spara**. Den anpassade allokeringsregeln är klar att användas.

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

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
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
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
    }
    ```

9. Klicka på **loggar** längst ned i filen **Run. CSX** och övervaka loggningen från den anpassade tilldelnings funktionen. 


## <a name="create-the-enrollment"></a>Skapa registreringen

I det här avsnittet ska du skapa en ny registrerings grupp som använder den anpassade allokeringsregeln. För enkelhetens skull använder den här artikeln [symmetrisk nyckel attestering](concepts-symmetric-key-attestation.md) med registreringen. För en säkrare lösning bör du överväga att använda [X. 509 certifikat attestering](concepts-x509-attestation.md) med en förtroende kedja.

1. Öppna etablerings tjänsten fortfarande på [Azure Portal](https://portal.azure.com).

2. Välj **Hantera registreringar** i den vänstra rutan och välj sedan knappen **Lägg till registrerings grupp** överst på sidan.

3. I **Lägg till registrerings grupp** anger du informationen i tabellen nedan och klickar på knappen **Spara** .

    | Fält | Beskrivning och/eller föreslaget värde |
    | :---- | :----------------------------- |
    | **Gruppnamn** | Ange **contoso-Custom-allokerade – enheter** |
    | **Attesterings typ** | Välj **symmetrisk nyckel** |
    | **Generera nycklar automatiskt** | Den här kryss rutan bör redan vara markerad. |
    | **Välj hur du vill tilldela enheter till hubbar** | Välj **Anpassad (Använd Azure Function)** |
    | **Välj de IoT-hubbar som den här gruppen kan tilldelas** | Välj den IoT-hubb som du skapade tidigare när du slutförde snabb starten. |
    | **Välj Azure Function** | Välj den prenumeration som innehåller den Function-app som du skapade. Välj sedan funktionen **contoso-Function-app** och **HttpTrigger1** för funktionen. |

    ![Lägg till anpassad grupp registrerings grupp för symmetrisk nyckel attestering](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. När du har sparat registreringen kan du öppna den igen och anteckna den **primära nyckeln**. Du måste spara registreringen innan du genererar nycklarna. Den här primära symmetriska nyckeln kommer att användas för att generera unika enhets nycklar för enheter som försöker etablering senare. 

## <a name="derive-unique-device-keys"></a>Härled unika enhets nycklar

Enheter använder inte den primära symmetriska nyckeln direkt. I stället använder du den primära nyckeln för att härleda en enhets nyckel för varje enhet. I det här avsnittet skapar du två unika enhets nycklar. En nyckel kommer att användas för en simulerad toaster-enhet. Den andra nyckeln kommer att användas för en simulerad värme Pumps enhet. Nycklarna som genereras gör att båda enheterna kan försöka registrera sig. Endast ett enhets registrerings-ID har ett giltigt suffix som ska godkännas av exempel koden för en anpassad resursallokeringsprincip. Det innebär att en accepteras och att den andra avvisas

För att härleda enhets nyckeln använder du den symmetriska nyckeln som du noterade tidigare för att beräkna [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) för enhets registrerings-ID: t för varje enhet och konvertera resultatet till base64-format. Mer information om hur du skapar härledda enhets nycklar med registrerings grupper finns i avsnittet grupp registrering i [symmetrisk nyckel attestering](concepts-symmetric-key-attestation.md).

I exemplet i den här artikeln använder du följande två enhets registrerings-ID: n med koden nedan för att beräkna en enhets nyckel för båda enheterna:

* **Contoso-toaster – 007**
* **Contoso-heatpump – 088**

Ersätt värdet för **nyckel** variabeln med den **primära nyckel** som du noterade tidigare när du skapade registrerings gruppen. Nyckelvärdet och utdata som visas med koden nedan är bara ett exempel.

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du använder en Windows-baserad arbets Station kan du använda PowerShell för att generera en härledd enhets nyckel som visas i följande exempel.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

#### <a name="bash"></a>[Bash](#tab/bash)

Om du använder en Linux-arbetsstation kan du använda OpenSSL för att generera dina härledda enhets nycklar som visas i följande bash-exempel.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Förbereda en utvecklingsmiljö för Azure IoT C SDK

Enheter kommer att begära etablering med hjälp av etablerings exempel kod som ingår i [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

I det här avsnittet förbereder du utvecklings miljön som används för att skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). SDK innehåller exempel koden för den simulerade enheten. Den här simulerade enheten försöker etablera under enhetens startsekvens.

Det här avsnittet är riktat mot en Windows-baserad arbets Station. Ett Linux-exempel finns i konfiguration av de virtuella datorerna i [hur du etablerar för flera innehavare](how-to-provision-multitenant.md).

1. Ladda ned [cmake build-systemet](https://cmake.org/download/).

    Det är viktigt att förutsättningarna för Visual Studio (Visual Studio och arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++)) är installerade på datorn **innan** installationen av `CMake` påbörjas. När förutsättningarna är uppfyllda och nedladdningen har verifierats installerar du CMake-byggesystemet.

2. Hitta taggnamnet för den [senaste versionen](https://github.com/Azure/azure-iot-sdk-c/releases/latest) av SDK.

3. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommandon för att klona den senaste versionen av [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen. Använd taggen som du hittade i föregående steg som `-b` parameter värde:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

4. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. Kör följande kommandon från `azure-iot-sdk-c` katalogen:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Kör följande kommando som skapar en version av SDK:t som är specifik för plattformen i din utvecklingsklient. En Visual Studio-lösning för den simulerade enheten genereras i `cmake`-katalogen. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Om `cmake` du inte hittar din C++-kompilator kan du få build-fel när du kör kommandot. Om det händer kan du försöka köra kommandot i [kommando tolken för Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

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

I det här avsnittet uppdaterar du ett etablerings exempel med namnet **\_ test dev \_ client- \_ exempel** som finns i Azure IoT C SDK som du har skapat tidigare.

Den här exempel koden simulerar en enhets startsekvens som skickar etablerings förfrågan till din enhets etablerings tjänst instans. Startsekvensen gör att toaster-enheten identifieras och tilldelas IoT-hubben med hjälp av den anpassade principen för tilldelning.

1. I Azure-portalen väljer du fliken **Översikt** för enhetsetableringstjänsten och noterar värdet för **_ID-omfång_**.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Öppna lösnings filen **azure_iot_sdks. SLN** som genererades genom att köra cmake tidigare i Visual Studio. Lösningsfilen bör vara på följande plats:

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

6. I `main()` funktionen hittar du anropet till `Prov_Device_Register_Device()` . Precis före det anropet lägger du till följande rader med kod som använder [`Prov_Device_Set_Provisioning_Payload()`](/azure/iot-hub/iot-c-sdk-ref/prov-device-client-h/prov-device-set-provisioning-payload) för att skicka en anpassad JSON-nyttolast under etableringen. Detta kan användas för att ge mer information till dina anpassade fördelnings funktioner. Detta kan också användas för att överföra enhets typen i stället för att undersöka registrerings-ID: t. Mer information om hur du skickar och tar emot anpassade data nytto laster med DPS finns i [så här överför du nytto laster mellan enheter och DPS](how-to-send-additional-data.md).

    ```c
    // An example custom payload
    const char* custom_json_payload = "{\"MyDeviceFirmwareVersion\":\"12.0.2.5\",\"MyDeviceProvisioningVersion\":\"1.0.0.0\"}";

    prov_device_result = Prov_Device_Set_Provisioning_Payload(prov_device_handle, custom_json_payload);
    if (prov_device_result != PROV_DEVICE_RESULT_OK)
    {
        (void)printf("\r\nFailure setting provisioning payload: %s\r\n", MU_ENUM_TO_STRING(PROV_DEVICE_RESULT, prov_device_result));
    }
    ```

7. Högerklicka på projektet **prov\_dev\_client\_sample** och välj **Set as Startup Project** (Ange som startprojekt).

### <a name="simulate-the-contoso-toaster-device"></a>Simulera contoso toaster-enheten

1. För att simulera toaster-enheten hittar du anropet till `prov_dev_set_symmetric_key_info()` i **\_ test av dev- \_ klient \_ . c** som är kommenterad.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Ta bort kommentarer till funktions anropet och ersätt plats hållarnas värden (inklusive vinkelparenteser) med toaster registrerings-ID och härledd enhets nyckel som du skapade tidigare. Nyckelvärdet **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** som visas nedan anges bara som ett exempel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Spara filen.

2. På Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra lösningen. I prompten för att återskapa projektet väljer du **Ja** för att återskapa projektet innan det körs.

    Följande text är ett exempel på att logga utdata från den anpassade fördelnings funktions koden som körs för toaster-enheten. Observera att ett nav har valts för en toaster-enhet. Observera också att den `payload` medlem som innehåller det anpassade JSON-innehåll som du har lagt till i koden. Detta är tillgängligt för din kod att använda i `deviceRuntimeContext` .

    Den här loggningen är tillgänglig genom att klicka på **loggar** under funktions koden i portalen:

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    Följande exempel på enhets utdata visar att den simulerade toaster-enheten har startats och ansluter till etablerings tjänst instansen som ska tilldelas till popups IoT Hub med den anpassade allokeringsregeln:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simulera enheten contoso värme pump

1. Om du vill simulera värme Pumps enheten uppdaterar du anropet till `prov_dev_set_symmetric_key_info()` i **test av dev- \_ \_ klienten \_ . c** igen med registrerings-ID: t för värme pumpen och den härledda enhets nyckeln som du skapade tidigare. Nyckel värdet **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** som visas nedan anges också bara som ett exempel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Spara filen.

2. På Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra lösningen. I prompten för att återskapa projektet väljer du **Ja** för att återskapa projektet innan det körs.

    Följande text är ett exempel på att logga utdata från den anpassade funktionen för tilldelnings funktionen som körs för värme Pumps enheten. Den anpassade allokeringsregeln avvisar registreringen med HTTP-fel 400 Felaktig begäran. Lägg märke till `payload` medlemmen som innehåller det anpassade JSON-innehåll som du har lagt till i koden. Detta är tillgängligt för din kod att använda i `deviceRuntimeContext` .

    Den här loggningen är tillgänglig genom att klicka på **loggar** under funktions koden i portalen:

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    Följande exempel på enhets utdata visar den simulerade värme pump enheten som startar och ansluter till etablerings tjänst instansen för att försöka registrera till en IoT-hubb med hjälp av den anpassade allokeringsregeln. Detta Miss lyckas med felet ( `Custom allocation failed with status code: 400` ) eftersom den anpassade allokeringsregeln har utformats för att endast tillåta toaster-enheter:


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
    Press enter key to exit:    
    ```
    
## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med resurserna som du skapade i den här artikeln kan du lämna dem. Om du inte planerar att fortsätta använda resurserna kan du använda följande steg för att ta bort alla resurser som skapats i den här artikeln för att undvika onödiga kostnader.

Stegen här förutsätter att du har skapat alla resurser i den här artikeln enligt anvisningarna i samma resurs grupp med namnet **contoso-US-Resource-Group**.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Så här tar du bort resurs gruppen efter namn:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.

2. I text rutan **Filtrera efter namn...** skriver du namnet på den resurs grupp som innehåller dina resurser, **contoso-US-Resource-Group**. 

3. Till höger om resurs gruppen i resultat listan väljer du **...** och sedan **ta bort resurs grupp**.

4. Du uppmanas att bekräfta borttagningen av resurs gruppen. Skriv namnet på resurs gruppen igen för att bekräfta och välj sedan **ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

Ett mer ingående princip exempel för anpassad fördelning finns i 

> [!div class="nextstepaction"]
> [Så här använder du anpassade allokeringsprinciper](how-to-use-custom-allocation-policies.md)

* Mer information finns i

> [!div class="nextstepaction"]
> [Metoder för att etablera IoT Hub enhet](concepts-device-reprovision.md)

* Mer information om hur du avetablerar finns i

> [!div class="nextstepaction"]
> [Så här avetablerar du enheter som tidigare har etablerats](how-to-unprovision-devices.md)