---
title: Så här etablerar du enheter för flera innehavare i Azure IoT Hub Device Provisioning Service
description: Så här etablerar du enheter för flera innehavare med din DPS-instans (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0b88923ff6447785a4ef5a7c80e1ff44d1a2b9cb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777391"
---
# <a name="how-to-provision-for-multitenancy"></a>Så här etablerar du för flera klientorganisationer 

Den här artikeln visar hur du på ett säkert sätt etablerar flera symmetriska nyckelenheter till en grupp med IoT Hubs med hjälp av en [allokeringsprincip.](concepts-service.md#allocation-policy) Allokeringsprinciper som definieras av etableringstjänsten stöder en mängd olika allokeringsscenarier. Två vanliga scenarier är:

* **Geoplats/GeoLatency:** När en enhet flyttas mellan platser förbättras nätverksfördröjningen genom att enheten etableras till den IoT-hubb som är närmast varje plats. I det här scenariot väljs en grupp med IoT-hubbar, som sträcker sig över flera regioner, för registreringar. Principen **för allokering med** lägst svarstid har valts för dessa registreringar. Den här principen gör att Device Provisioning Service utvärderar enhetens svarstid och fastställer IoT-closet-hubben från gruppen med IoT-hubbar. 

* **Flera innehavare:** Enheter som används i en IoT-lösning kan behöva tilldelas till en specifik IoT-hubb eller grupp med IoT-hubbar. Lösningen kan kräva att alla enheter för en viss klientorganisation kommunicerar med en specifik grupp av IoT-hubbar. I vissa fall kan en klient äga IoT-hubbar och kräva att enheter tilldelas till sina IoT-hubbar.

Det är vanligt att kombinera dessa två scenarier. En IoT-lösning för flera innehavare tilldelar till exempel ofta klientenheter med hjälp av en grupp IoT-hubbar som är utspridda i olika regioner. Dessa klientenheter kan tilldelas till IoT-hubben i den gruppen, som har kortast svarstid baserat på geografisk plats.

Den här artikeln använder ett simulerat enhetsexempel från [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) för att visa hur du etablerar enheter i ett scenario med flera klientenheter i flera regioner. Du utför följande steg i den här artikeln:

> [!div class="checklist"]
> * Använd Azure CLI för att skapa två regionala IoT-hubbar (**USA, västra** och **USA, östra**)
> * Skapa en registrering för flera samma år
> * Använd Azure CLI för att skapa två regionala virtuella Linux-datorer som fungerar som enheter i samma regioner **(USA, västra** och **USA, östra)**
> * Konfigurera utvecklingsmiljön för Azure IoT C SDK på båda virtuella Linux-datorerna
> * Simulera enheterna för att se att de har etablerats för samma klientorganisation i den närmaste regionen.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Förutsättningar

- Slutförande av [snabbstarten Konfigurera IoT Hub Device Provisioning Service med Azure Portal.](./quick-setup-auto-provision.md)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Skapa två regionala IoT-hubbar

I det här avsnittet använder du Azure Cloud Shell för att skapa två nya  regionala IoT-hubbar i regionerna USA, västra och **USA,** östra för en klientorganisation.


1. Använd Azure Cloud Shell för att skapa en resursgrupp med [kommandot az group](/cli/azure/group#az_group_create) create. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

    I följande exempel skapas en resursgrupp med *namnet contoso-us-resource-group* i *regionen eastus.* Vi rekommenderar att du använder den här gruppen för alla resurser som skapas i den här artikeln. Detta gör rensningen enklare när du är klar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Använd Azure Cloud Shell för att skapa en IoT-hubb i **regionen eastus** med [kommandot az iot hub](/cli/azure/iot/hub#az_iot_hub_create) create. IoT-hubben läggs till i *gruppen contoso-us-resource-group*.

    I följande exempel skapas en IoT-hubb med namnet *contoso-east-hub* på *platsen eastus.* Du måste använda ditt eget unika hubbnamn i stället för **contoso-east-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Det här kommandot kan ta några minuter att slutföra.

3. Använd kommandot Azure Cloud Shell för att skapa en IoT-hubb i regionen **usa,** västra med [kommandot az iot hub](/cli/azure/iot/hub#az_iot_hub_create) create. Den här IoT-hubben läggs också till i *gruppen contoso-us-resource-group*.

    I följande exempel skapas en IoT-hubb med *namnet contoso-west-hub* på *platsen westus.* Du måste använda ditt eget unika hubbnamn i stället för **contoso-west-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Det här kommandot kan ta några minuter att slutföra.



## <a name="create-the-multitenant-enrollment"></a>Skapa multitenant-registreringen

I det här avsnittet skapar du en ny registreringsgrupp för klientenheterna.  

För enkelhetens skull använder den här [artikeln symmetrisk nyckel atterstation](concepts-symmetric-key-attestation.md) med registreringen. För en säkrare lösning bör du överväga att [använda X.509-certifikatattestning](concepts-x509-attestation.md) med en förtroendekedja.

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna instansen av enhetsetableringstjänsten.

2. Välj fliken **Hantera registreringar** och klicka sedan på **knappen Lägg till** registreringsgrupp överst på sidan. 

3. På **Lägg till registreringsgrupp** anger du följande information och klickar på **knappen** Spara.

    **Gruppnamn:** Ange **contoso-us-devices**.

    **Attestationstyp:** Välj **Symmetrisk nyckel.**

    **Generera nycklar automatiskt:** Den här kryssrutan bör redan vara markerad.

    **Välj hur du vill tilldela enheter till hubbar:** Välj **Lägsta svarstid.**

    ![Lägg till registreringsgrupp för flera i samma system för symmetrisk nyckel atterstation](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. På **Lägg till registreringsgrupp** klickar du **på Länka en ny IoT-hubb** för att länka båda dina regionala hubbar.

    **Prenumeration:** Om du har flera prenumerationer väljer du den prenumeration där du skapade de regionala IoT-hubbarna.

    **IoT-hubb:** Välj en av de regionala hubbar som du skapade.

    **Åtkomstprincip:** Välj **iothubowner**.

    ![Länka de regionala IoT-hubbarna med etableringstjänsten](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. När båda regionala IoT-hubbarna har länkats måste du  välja dem för registreringsgruppen och klicka på Spara för att skapa den regionala IoT-hubbgruppen för registreringen.

    ![Skapa den regionala hubbgruppen för registreringen](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. När du har sparat registreringen öppnar du den igen och noterar **primärnyckeln**. Du måste spara registreringen först för att få nycklarna genererade. Den här nyckeln används för att generera unika enhetsnycklar för båda simulerade enheterna senare.


## <a name="create-regional-linux-vms"></a>Skapa regionala virtuella Linux-datorer

I det här avsnittet skapar du två regionala virtuella Linux-datorer (VM). De här virtuella datorerna kör ett exempel på enhetssimulering från varje region för att demonstrera enhetsetablering för klientenheter från båda regionerna.

För att göra rensningen enklare läggs dessa virtuella datorer till i samma resursgrupp som innehåller de IoT-hubbar som skapades, *contoso-us-resource-group*. De virtuella datorerna körs dock i separata regioner (**USA, västra** och **USA, östra**).

1. I den Azure Cloud Shell du följande kommando för att skapa en virtuell dator i regionen **USA,** östra när du har gjort följande parameterändringar i kommandot:

    **--name:** Ange ett unikt namn för den regionala **enhetens virtuella** dator i USA, östra. 

    **--admin-username:** Använd ditt eget administratörsanvändarnamn.

    **--admin-password:** Använd ditt eget administratörslösenord.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Kommandot tar fem minuter att slutföra. När kommandot har slutförts anteckning du värdet **publicIpAddress för den** virtuella datorn i regionen USA, östra.

1. I den Azure Cloud Shell du kommandot för att  skapa en virtuell dator i regionen USA, västra när du har gjort följande parameterändringar i kommandot:

    **--name:** Ange ett unikt namn för din virtuella dator **i regionen** USA, västra. 

    **--admin-username:** Använd ditt eget administratörsanvändarnamn.

    **--admin-password:** Använd ditt eget administratörslösenord.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Kommandot tar fem minuter att slutföra. När kommandot har slutförts anteckning du värdet **publicIpAddress för den** virtuella datorn i regionen USA, västra.

1. Öppna två kommandoradsgränssnitt. Anslut till en av de regionala virtuella datorerna i varje gränssnitt med hjälp av SSH. 

    Skicka ditt administratörsnamn och den offentliga IP-adress som du antecknade för den virtuella datorn som parametrar till SSH. Ange administratörslösenordet när du tillfrågas.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Förbereda utvecklingsmiljön för Azure IoT C SDK

I det här avsnittet ska du klona Azure IoT C SDK på varje virtuell dator. SDK:n innehåller ett exempel som simulerar en klientorganisations enhetsetablering från varje region.

1. För varje virtuell dator installerar **du CMake,** **g++**, **gcc** och [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) med följande kommandon:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Leta reda på taggnamnet för [den senaste versionen](https://github.com/Azure/azure-iot-sdk-c/releases/latest) av SDK.

1. Klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) på båda virtuella datorerna.  Använd taggen som du hittade i föregående steg som värde för `-b` parametern :

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

1. För båda de virtuella datorerna skapar du en ny **cmake-mapp** i lagringsplatsen och ändrar till den mappen.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. För båda de virtuella datorerna kör du följande kommando, som skapar en version av SDK:n som är specifik för din utvecklingsklientplattform. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    När bygget är klart ser de sista utdataraderna ut ungefär som följande utdata:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Härled unika enhetsnycklar

När du använder symmetrisk nyckel attestation med gruppregistreringar använder du inte registreringsgruppens nycklar direkt. I stället skapar du en unik härledd nyckel för varje enhet och anges [i Gruppregistreringar med symmetriska nycklar.](concepts-symmetric-key-attestation.md#group-enrollments)

Generera enhetsnyckeln genom att använda huvudnyckeln för gruppen för att beräkna [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) för enhetens unika registrerings-ID och konvertera resultatet till Base64-format.

Inkludera inte din huvudnyckel för gruppen i enhetskoden.

Använd Bash-gränssnittsexempel för att skapa en härledd enhetsnyckel för varje enhet **med openssl**.

- Ersätt värdet för **KEY med** den **primärnyckel som du** antecknade tidigare för din registrering.

- Ersätt värdet för **REG_ID** med ditt eget unika registrerings-ID för varje enhet. Använd alfanumeriska gemener och bindestreck (-) för att definiera båda ID:erna.

Exempel på generering av *enhetsnyckel för contoso-simdevice-east:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Exempel på generering av *enhetsnyckel för contoso-simdevice-west:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Klientenheterna använder var och en sin härledda enhetsnyckel och unika registrerings-ID för att utföra symmetrisk nyckel attestation med registreringsgruppen under etableringen till klientens IoT-hubbar.




## <a name="simulate-the-devices-from-each-region"></a>Simulera enheterna från varje region


I det här avsnittet uppdaterar du ett etableringsexempel i Azure IoT C SDK för båda de regionala virtuella datorerna. 

Exempelkoden simulerar en enhetsstartsekvens som skickar etableringsbegäran till instansen av enhetsetableringstjänsten. Startsekvensen gör att enheten identifieras och tilldelas till den IoT-hubb som är närmast baserat på svarstiden.

1. I Azure-portalen väljer du fliken **Översikt** för enhetsetableringstjänsten och noterar värdet för **_ID-omfång_**.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Öppna **~/azure-iot-sdk-c/provisioning \_ client/samples/prov \_ dev client \_ \_ sample/prov \_ dev client \_ \_ sample.c** för redigering på båda virtuella datorerna.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Hitta konstanten `id_scope` och ersätt värdet med ditt värde för **ID-omfång** som du kopierade tidigare. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Hitta definitionen för funktionen `main()` i samma fil. Kontrollera att `hsm_type` variabeln är inställd på `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` enligt nedan så att den matchar registreringsgruppens atterstationsmetod. 

    Spara ändringarna i filerna på båda de virtuella datorerna.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. På båda de virtuella datorerna letar du upp `prov_dev_set_symmetric_key_info()` anropet till **i prov dev client \_ \_ \_ sample.c** som har kommenterats bort.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Avkommentera funktionens anrop och ersätt platshållarvärdena (inklusive vinkelparenteserna) med unika registrerings-ID:n och härledda enhetsnycklar för varje enhet. Nycklarna som visas nedan är endast i exempelsyfte. Använd de nycklar som du genererade tidigare.

    USA, östra:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    USA, västra:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Spara filerna.

1. På båda virtuella datorerna navigerar du till exempelmappen som visas nedan och skapar exemplet.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. När bygget har lyckats kör du **prov \_ dev client \_ \_sample.exe** på båda virtuella datorerna för att simulera en klientenhet från varje region. Observera att varje enhet allokeras till den klientorganisations-IoT-hubb som är närmast den simulerade enhetens regioner.

    Kör simuleringen:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Exempel på utdata från den virtuella datorn USA, östra:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Exempel på utdata från den virtuella datorn i USA, västra:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med resurser som skapats i den här artikeln kan du lämna dem. Om du inte planerar att fortsätta använda resursen kan du använda följande steg för att ta bort alla resurser som skapats av den här artikeln för att undvika onödiga kostnader.

Stegen här förutsätter att du har skapat alla resurser i den här artikeln enligt anvisningarna i samma resursgrupp med namnet **contoso-us-resource-group**.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Så här tar du bort resursgruppen efter namn:

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

2. I **textrutan Filtrera efter namn...** skriver du namnet på resursgruppen som innehåller dina **resurser, contoso-us-resource-group**. 

3. Till höger av din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp igen för att bekräfta och klicka sedan på **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

* Mer information om ometablering finns i

> [!div class="nextstepaction"]
> [IoT Hub ometableringsbegrepp för enheter](concepts-device-reprovision.md)

* Mer information om avetablering finns i
> [!div class="nextstepaction"]
> [Avetablera enheter som tidigare har etablerats automatiskt](how-to-unprovision-devices.md)