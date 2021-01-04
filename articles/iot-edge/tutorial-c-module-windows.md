---
title: Självstudie – utveckla C-moduler för Windows med hjälp av Azure IoT Edge
description: Den här självstudien visar hur du skapar IoT Edge moduler med C-kod och distribuerar dem till Windows-enheter som kör IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1f346e1b737075fa79dc1146152125a6c5a3ec1a
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704697"
---
# <a name="tutorial-develop-c-iot-edge-modules-for-windows-devices"></a>Självstudie: utveckla C IoT Edge-moduler för Windows-enheter

Den här artikeln visar hur du använder Visual Studio för att utveckla C-kod och distribuera den till en Windows-enhet som kör Azure IoT Edge.

Du kan använda Azure IoT Edge moduler för att distribuera kod som implementerar affärs logiken direkt i dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. 

I de här självstudierna får du lära dig att

> [!div class="checklist"]
>
> * Använd Visual Studio för att skapa en IoT Edge-modul som baseras på C SDK.
> * Använd Visual Studio och Docker för att skapa en Docker-avbildning och publicera den i registret.
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Modulen skickar meddelande överström endast om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien visar hur du utvecklar en modul i C genom att använda Visual Studio 2019 och sedan distribuera den till en Windows-enhet. Om du utvecklar moduler för Linux-enheter går du till [utveckla C IoT Edge-moduler för Linux-enheter](tutorial-csharp-module.md) i stället.

För att förstå alternativen för att utveckla och distribuera C-moduler till Windows-enheter, se följande tabell:

| C | Visual &nbsp; Studio- &nbsp; kod | Visual Studio 2017 &nbsp; och &nbsp; 2019 |
| -- | ------------------ | :------------------: |
| Windows AMD64 |  | ![Utveckla C-moduler för WinAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |

Innan du påbörjar den här självstudien ställer du in utvecklings miljön genom att följa anvisningarna i själv studie kursen [utveckla IoT Edge moduler för Windows-enheter](tutorial-develop-for-windows.md) . När du har slutfört den kommer din miljö att innehålla följande krav:

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) (kostnads fri) eller standard nivå i Azure.
* En [Windows-enhet som kör Azure IoT Edge](quickstart.md).
* Ett behållar register, till exempel [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio), konfigurerat med [Azure IoT Edge tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) -tillägget.
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/), konfigurerat för att köra Windows-behållare.

Installera Azure IoT C SDK för Windows x64 via vcpkg genom att köra följande kommandon:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Om du använder Visual Studio 2017 (version 15,7 eller senare) kan du hämta och installera Azure IoT Edge verktyg för Visual Studio 2017 från [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Skapa ett modul-projekt

I det här avsnittet skapar du ett IoT Edge modul-projekt som baseras på C SDK med hjälp av Visual Studio och tillägget Azure IoT Edge-verktyg. När du har skapat en projektmall lägger du till ny kod så att modulen filtrerar ut meddelanden baserat på deras rapporterade egenskaper.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en C-lösningsmall som du kan anpassa med din egen kod.

1. Öppna Visual Studio 2019 och välj sedan **Skapa nytt projekt**.

1. I fönstret **skapa ett nytt projekt** söker du efter **IoT Edge** och i resultat listan väljer du **Azure IoT Edge (Windows amd64)** -projektet.

   ![Skärm bild av fönstret IoT Edge skapa ett nytt projekt.](./media/tutorial-c-module-windows/new-project.png)

1. Välj **Nästa**.

    Fönstret **Konfigurera ditt nya projekt** öppnas.

   ![Skärm bild av fönstret "Konfigurera ditt nya projekt".](./media/tutorial-c-module-windows/configure-project.png)

1. I fönstret **Konfigurera ditt nya projekt** byter du namn på projektet och lösningen till något mer beskrivande, till exempel **CTutorialApp**. 

1. Välj **Skapa** för att skapa projektet.

   Fönstret **Lägg till modul** öppnas.

   ![Skärm bild av fönstret Lägg till modul för att konfigurera ditt projekt.](./media/tutorial-c-module-windows/add-application-and-module.png)

1. Gör följande på sidan **Konfigurera ditt nya projekt** :

   a. I den vänstra rutan väljer du mallen **C-modul** .  
   b. I rutan **Modulnamn** anger du **CModule**.  
   c. I rutan **lagrings webb adress** ersätter du **localhost: 5000** med **inloggnings serverns** värde från Azure Container Registry i följande format: `<registry name>.azurecr.io/cmodule`

    > [!NOTE]
    > En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Behållar avbildningen är förifylld från modulens projekt namn värde.  Du kan hämta inloggnings servern från sidan Översikt i behållar registret i Azure Portal.

1. Välj **Lägg till** för att skapa projektet.

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Distributions manifestet delar autentiseringsuppgifterna för behållar registret med IoT Edge Runtime. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. Använd autentiseringsuppgifterna från avsnittet **åtkomst nycklar** i Azure Container Registry.

1. I Visual Studio Solution Explorer öppnar du *deployment.template.jspå* filen.

1. Leta efter egenskapen **registryCredentials** i önskade egenskaper för $edgeAgent. Register adressen för egenskapen ska fyllas i med den information som du angav när du skapade projektet. Fälten username och Password måste innehålla variabel namn. Exempel:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Öppna miljö filen (miljö) i modulen lösning. Som standard är filen dold i Solution Explorer, så du kan behöva välja knappen **Visa alla filer** för att visa den. KUVERT filen ska innehålla samma användar namn och lösen ord som du såg i *deployment.template.jsi* filen.

1. Lägg till värdena för **användar namn** och **lösen ord** från Azure Container Registry.

1. Spara ändringarna i kuvert filen.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Koden för standardmodulen tar emot meddelanden i en indatakö och skickar dem vidare via en utgående kö. Nu ska vi lägga till lite mer kod så att modulen bearbetar meddelandena på gränsen innan de vidarebefordrar dem till din IoT-hubb. Uppdatera modulen så att den analyserar temperatur data i varje meddelande och skickar meddelandet till IoT Hub endast om temperaturen överskrider ett visst tröskelvärde.

1. Data från sensorn i det här scenariot kommer i JSON-format. Om du vill filtrera meddelanden i JSON-format importerar du ett JSON-bibliotek för C. Den här självstudien använder Parson.

   a. Ladda ned [Parson GitHub-lagringsplatsen](https://github.com/kgabis/parson).  
   b. Kopiera *Parson. c* -och *Parson. h* -filerna till CModule-projektet.  
   c. Öppna *CMakeLists.txt* -filen från projektmappen CModule i Visual Studio.  
   d. Längst upp i filen importerar du Parson-filerna som ett bibliotek med namnet **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. Lägg till i `my_parson` listan över bibliotek i avsnittet "target_link_libraries" i *CMakeLists.txt* -filen.  
   f. Spara filen *CMakeLists.txt*.  
   ex. Välj **CModule**  >  **main. c**. Lägg till en ny instruktion som ska inkluderas för JSON-stöd längst ned i listan över inkludera-instruktioner `parson.h` :

      ```c
      #include "parson.h"
      ```

1. I filen *main. c* lägger du till en global variabel `temperatureThreshold` som heter bredvid `messagesReceivedByInput1Queue` variabeln. Den här variabeln anger värdet som den uppmätta temperaturen måste överskrida för att data ska skickas till din IoT-hubb.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Hitta `CreateMessageInstance` funktionen i *main. c*. Ersätt den inre *if-else-* instruktionen med följande kod, som lägger till några rader med funktioner:

   ```c
   if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
   {
       free(messageInstance);
       messageInstance = NULL;
   }
   else
   {
       messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
       MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
       if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
       {
          printf("ERROR: Map_AddOrUpdate Failed!\r\n");
       }
   }
   ```

   De nya kodrader i *Else* -instruktionen lägger till en ny egenskap i meddelandet, som namnger meddelandet som en avisering. Den här koden etiketterar alla meddelanden som aviseringar, eftersom vi kommer att lägga till funktioner som skickar meddelanden till IoT Hub endast om de rapporterar höga temperaturer.

1. Hitta `InputQueue1Callback` funktionen och ersätt hela funktionen med följande kod. Den här funktionen implementerar själva meddelandefiltret. När ett meddelande tas emot kontrollerar det om den rapporterade temperaturen överskrider tröskelvärdet. Om temperaturen överskrider tröskelvärdet vidarebefordrar funktionen meddelandet via dess utgående kö. Om den inte överskrider tröskelvärdet ignorerar funktionen meddelandet.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Lägg till en `moduleTwinCallback`-funktion. Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har sin egen modul, som gör att du kan konfigurera den kod som körs i modulen direkt från molnet.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. Sök efter `SetupCallbacksForModule` funktionen. Ersätt funktionen med följande kod, som lägger till en *Else-If-* instruktion för att kontrol lera om modulernas har uppdaterats.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Spara filen *main.c*.

1. Öppna filen *deployment.template.js* .

1. Lägg till **CModule** -modulen till distributions manifestet. Infoga följande JSON-innehåll längst ned i avsnittet `moduleContent` efter `$edgeHub`-modultvillingen:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Skärm bild som visar den modul som har lagts till i distributions mal len.](./media/tutorial-c-module-windows/module-twin.png)

1. Spara filen *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Bygga och pusha din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lagt till kod i **CModule** för att filtrera bort meddelanden där den rapporterade dator temperaturen är under det acceptabla tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

### <a name="sign-in-to-docker"></a>Logga in på Docker

Ange dina autentiseringsuppgifter för behållar registret till Docker på din utvecklings dator så att den kan push-överföra din behållar avbildning till att lagras i registret.

1. Öppna PowerShell eller ett kommando tolks fönster.

1. Logga in på Docker med de autentiseringsuppgifter för Azure Container Registry som du sparade när du skapade registret.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhets varning som rekommenderar användning av `--password-stdin` . Även om vi rekommenderar detta som bästa praxis för produktions scenarier, är det utanför den här självstudien. Mer information finns i [inloggnings referens för Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Bygg och skicka

Nu har din utvecklings dator åtkomst till ditt behållar register, och dina IoT Edge enheter är också. Det är dags att omvandla projekt koden till en behållar avbildning.

1. I Visual Studio Solution Explorer högerklickar du på namnet på det projekt som du vill bygga. Standard namnet är **AzureIotEdgeApp1**. I den här självstudien valde vi namnet **CTutorialApp** och eftersom du skapar en Windows-modul bör tillägget vara **Windows. amd64**.

1. Välj **build-och push-IoT Edge moduler**.

   Kommandot build och push startar tre åtgärder:
   * Först skapas en ny mapp i lösningen med namnet *config*, som innehåller det fullständiga distributions manifestet. Den bygger på information i distributions mal len och andra lösningsfiler. 
   * Sedan körs den `docker build` för att bygga behållar avbildningen baserat på lämpliga Dockerfile för din mål arkitektur. 
   * Slutligen körs den `docker push` för att skicka avbildnings lagrings platsen till behållar registret.

   Den här processen kan ta flera minuter första gången, men den går snabbare nästa gång du kör kommandona.

## <a name="deploy-modules-to-the-device"></a>Distribuera moduler till enheten

Använd Visual Studio Cloud Explorer och tillägget Azure IoT Edge Tools för att distribuera modulfönstret till din IoT Edge-enhet. Du har redan förberett ett distributions manifest för ditt scenario, *deployment.windows-amd64.js* filen i mappen *config* . Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Kontrol lera att din IoT Edges enhet är igång.

1. Expandera resurserna i Visual Studio Cloud Explorer för att visa din lista över IoT-enheter.

1. Högerklicka på namnet på den IoT Edge enhet som du vill ta emot distributionen av.

1. Välj **skapa distribution**.

1. I Visual Studio Utforskaren väljer du *deployment.windows-amd64.js* filen i mappen *config* i din lösning.

1. Uppdatera Cloud Explorer för att visa de distribuerade modulerna som visas under enheten.

## <a name="view-generated-data"></a>Visa genererade data

När du har tillämpat distributions manifestet på IoT Edge-enheten samlar IoT Edge runtime på enheten den nya distributions informationen och börjar köras på den. Alla moduler som körs på enheten men som inte ingår i distributions manifestet stoppas. Alla moduler som saknas från enheten har startats.

Du kan använda tillägget IoT Edge-verktyg för att visa meddelanden när de anländer till din IoT-hubb.

1. I Visual Studio Cloud Explorer väljer du namnet på din IoT Edge-enhet.

1. I listan **åtgärder** väljer du **starta övervakning inbyggd händelse slut punkt**.

1. Visa meddelanden som kommer till din IoT-hubb. Det kan ta en stund innan meddelandena tas emot, eftersom den IoT Edge enheten måste ta emot sin nya distribution och starta alla moduler. Ändringarna i CModule-koden måste vänta tills datorns temperatur når 25 grader innan meddelandena kan skickas. Koden lägger också till meddelande typ **avisering** till alla meddelanden som når detta temperatur tröskelvärde.

   ![Skärm bild av fönstret utdata som visar meddelanden som kommer från IoT Hub.](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Redigera modulens dubbla

Du använde CModule-modulen för att ange temperatur tröskeln vid 25 grader. Du kan använda modulen för att ändra funktionen utan att behöva uppdatera modulens kod.

1. Öppna filen *deployment.windows-amd64.js* i Visual Studio. 

   Öppna *inte* filen *Deployment. template* . Om du inte ser distributions manifestet i *konfigurations* filen i Solution Explorer väljer du ikonen **Visa alla filer** i Solution Explorer verktygsfältet.

1. Leta efter CModule och ändra värdet för parametern **temperatureThreshold** till en ny temperatur som är 5 till 10 grader högre än den senaste rapporterade temperaturen.

1. Spara *deployment.windows-amd64.jspå* filen.

1. Följ distributions anvisningarna igen för att tillämpa det uppdaterade distributions manifestet på enheten.

1. Övervaka inkommande meddelanden från enhet till molnet. Meddelandena ska stoppas tills det nya temperatur tröskelvärdet har uppnåtts.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta till nästa rekommenderade artikel kan du behålla och återanvända de resurser och konfigurationer som du skapade i den här självstudien. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du undvika att debiteras genom att ta bort de lokala konfigurationerna och de Azure-resurser som du använde här.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul med kod för att filtrera rådata som genereras av din IoT Edge-enhet.

Om du vill veta hur Azure IoT Edge kan hjälpa dig att distribuera Azure Cloud Services för att bearbeta och analysera data på gränsen fortsätter du till nästa självstudier.

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md) 
>  [Azure Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Azure Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom vision tjänst](tutorial-deploy-custom-vision.md)
