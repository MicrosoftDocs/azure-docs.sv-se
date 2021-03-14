---
title: 'Självstudie: skapa och distribuera anpassade moduler – Machine Learning på Azure IoT Edge'
description: Den här självstudien visar hur du skapar och distribuerar IoT Edge moduler som bearbetar data från löv enheter via en maskin inlärnings modell och sedan skickar insikter till IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 5c096b26f31bdf92bb5ab91c8dad7876f228ff14
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462804"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Självstudie: skapa och distribuera anpassade IoT Edge-moduler

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

I den här artikeln skapar vi tre IoT Edge moduler som tar emot meddelanden från löv IoT-enheter, kör data via din Machine Learning-modell och vidarebefordrar insikter till IoT Hub.

IoT Edge hubb underlättar modulen kommunikation. Om du använder IoT Edge hubben som en meddelande utjämning lagras moduler oberoende av varandra. Moduler behöver bara ange de indata som de accepterar meddelanden och de utdata som de skriver meddelanden till.

Vi vill att IoT Edge-enheten ska utföra fyra saker för oss:

* Ta emot data från löv enheterna.
* Förutsäg återstående livs längd (RUL) för enheten som skickade data.
* Skicka ett meddelande med RUL för enheten till IoT Hub. Den här funktionen kan bara ändras för att skicka data om RUL sjunker under en angiven nivå.
* Spara löv enhets data till en lokal fil på den IoT Edge enheten. Den här data filen laddas regelbundet upp till IoT Hub för att förfina utbildningen för Machine Learning-modellen. Att använda fil uppladdning i stället för konstant strömning av meddelanden är mer kostnads effektivt.

Vi använder tre anpassade moduler för att utföra dessa uppgifter:

* **RUL-klassificerare:** TurboFanRulClassifier-modulen som vi skapade i [träna och distribuera en Azure Machine Learning modell](tutorial-machine-learning-edge-04-train-model.md) är en standardmodul för maskin inlärning som visar indata som kallas "amlInput" och utdata som kallas "amlOutput". "AmlInput" förväntar sig att indatamängden ska se exakt likadant ut som de inmatade som vi skickade till den ACI-baserade webb tjänsten. På samma sätt returnerar "amlOutput" samma data som webb tjänsten.

* **Avro-skrivare:** Den här modulen tar emot meddelanden på avroModuleInput-indatamängden och sparar meddelandet i Avro-format till disk för senare uppladdning till IoT Hub.

* **Router-modul:** Modulen router tar emot meddelanden från underordnade löv enheter och formaterar och skickar sedan meddelandena till klassificeraren. Modulen tar sedan emot meddelanden från klassificeraren och vidarebefordrar meddelandet till Avro Writer-modulen. Slutligen skickar modulen bara RUL förutsägelse till IoT Hub.

  * Tillför
    * **deviceInput**: tar emot meddelanden från löv enheter
    * **rulInput:** tar emot meddelanden från "amlOutput"

  * Utdata
    * **klassificera:** skickar meddelanden till "amlInput"
    * **writeAvro:** skickar meddelanden till "avroModuleInput"
    * **toIotHub:** skickar meddelanden till $upstream som skickar meddelandena till den anslutna IoT Hub

Följande diagram visar moduler, indata, utdata och IoT Edge hubb vägar för den fullständiga lösningen:

![Arkitektur diagram för IoT Edge tre moduler](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Stegen i den här artikeln utförs vanligt vis av en molnbaserad utvecklare.

I det här avsnittet av självstudien får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en IoT Edge-modul från anpassad kod.
> * Skapa en Docker-avbildning från din anpassade modul.
> * Konfigurera om IoT Hub routning för att stödja dina anpassade moduler.
> * Bygg, publicera och distribuera dina anpassade moduler.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln ingår i en serie för självstudier om hur du använder Azure Machine Learning på IoT Edge. Varje artikel i serien bygger på arbetet i föregående artikel. Om du har kommit till den här artikeln direkt kan du gå till den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien.

## <a name="create-a-new-iot-edge-solution"></a>Skapa en ny IoT Edge-lösning

Under körningen av den andra av våra två Azure Notebooks har vi skapat och publicerat en behållar avbildning som innehåller vår RUL-modell. Azure Machine Learning som en del av processen för att skapa avbildningar paketeras modellen så att avbildningen kan distribueras som en Azure IoT Edge modul.

I det här steget ska vi skapa en Azure IoT Edge-lösning med hjälp av modulen "Azure Machine Learning" och peka modulen till den avbildning som vi publicerade med Azure Notebooks.

1. Öppna en fjärrskrivbordssession till din utvecklings-VM.

1. Öppna mapp **C: \\ käll \\ IoTEdgeAndMlSample** i Visual Studio Code.

1. Högerklicka på Explorer-panelen (i det tomma utrymmet) och välj **ny IoT Edge lösning**.

    ![Skapa ny IoT Edge-lösning](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

1. Godkänn standard lösnings namnet **EdgeSolution**.

1. Välj **Azure Machine Learning** som mall.

1. Namnge modulen **turbofanRulClassifier**.

1. Välj din Machine Learning-arbetsyta. Den här arbets ytan är arbets ytan **turboFanDemo** som du skapade i [Självstudier: träna och distribuera en Azure Machine Learning modell](tutorial-machine-learning-edge-04-train-model.md)

1. Välj den avbildning som du skapade när du körde Azure-anteckningsboken.

1. Titta på lösningen och Lägg märke till de filer som har skapats:

   * **deployment.template.jspå:** Den här filen innehåller definitionen av varje modul i lösningen. Det finns tre avsnitt som ska åtgärdas i den här filen:

     * **Autentiseringsuppgifter för registret:** Definierar en uppsättning anpassade behållar register som du använder i din lösning. Just nu bör den innehålla registret från din Machine Learning-arbetsyta, som är den Azure Machine Learning avbildningen lagrad. Du kan ha valfritt antal behållar register, men för enkelhetens skull kommer vi att använda det här registret för alla moduler.

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Moduler:** Det här avsnittet innehåller en uppsättning användardefinierade moduler som ingår i den här lösningen. TurbofanRulClassifier-modulens definition pekar på avbildningen i behållar registret. När vi lägger till fler moduler i lösningen visas de i det här avsnittet.

       ```json
        "modules": {
          "turbofanRulClassifier": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "turbofandemo2cd74296.azurecr.io/edgemlsample:1",
              "createOptions": {}
            }
          }
        }
       ```

     * **Vägar:** vi kommer att arbeta med vägar i den här självstudien. Vägar definierar hur moduler kommunicerar med varandra. Den befintliga vägen som definieras av mallen stämmer inte överens med den routning vi behöver. Ta bort `turbofanRulClassifierToIoTHub` vägen.

       ```json
        "$edgeHub": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
              "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/* INTO $upstream"
            },
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
       ```

   * **deployment.debug.template.jspå:** den här filen är fel söknings versionen av deployment.template.jspå. Normalt bör den här filen synkroniseras med innehållet i deployment.template.jsfilen, men detta krävs inte för den här självstudien.

   * **. kuvert:** den här filen är den plats där du anger användar namn och lösen ord för att komma åt ditt register.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

1. Högerklicka på filen deployment.template.jsi Visual Studio Code Explorer och välj **Build IoT Edge-lösning**.

1. Observera att det här kommandot skapar en config-mapp med en deployment.amd64.jsi filen. Den här filen är mallen för konkret distribution för lösningen.

## <a name="add-router-module"></a>Lägg till router-modul

Sedan lägger vi till modulen router i vår lösning. Modulen router hanterar flera ansvars områden för vår lösning:

* **Ta emot meddelanden från löv enheter:** när meddelanden kommer till den IoT Edge enheten från efterföljande enheter tar modulen router emot meddelandet och börjar dirigera meddelandets dirigering.
* **Skicka meddelanden till RUL-klassificerings modulen:** när ett nytt meddelande tas emot från en underordnad enhet omvandlar modulen router meddelandet till det format som RUL-klassificeraren förväntar sig. Routern skickar meddelandet till RUL-klassificeraren för en RUL förutsägelse. När klassificeraren har gjort en förutsägelse skickas meddelandet tillbaka till modulen router.
* **Skicka RUL-meddelanden till IoT Hub:** när routern tar emot meddelanden från klassificeraren omvandlas meddelandet så att det endast innehåller nödvändig information, enhets-ID och RUL och skickar det förkortade meddelandet till IoT Hub. En ytterligare förfining, som vi inte har gjort här, skickar bara meddelanden till IoT Hub när den RUL förutsägelsen sjunker under ett tröskelvärde (till exempel när RUL är mindre än 100 cykler). Genom att filtrera på det här sättet kan du minska mängden meddelanden och minska kostnaderna för IoT Hub.
* **Skicka meddelande till Avro Writer-modulen:** om du vill bevara alla data som skickas av den underordnade enheten skickar modulen router hela meddelandet som tas emot från klassificeraren till Avro Writer-modulen, som behåller och laddar upp data med hjälp av IoT Hub fil uppladdning.

Modulen router är en viktig del av lösningen som säkerställer att meddelanden bearbetas i rätt ordning.

### <a name="create-the-module-and-copy-files"></a>Skapa modulen och kopiera filer

1. Högerklicka på mappen moduler i Visual Studio Code och välj **Lägg till IoT Edge modul**.

1. Välj **C#-modul** för modulens mall.

1. Namnge modulen **turbofanRouter**.

1. När du uppmanas att ange lagrings platsen för Docker-avbildningen använder du registret från Machine Learning-arbetsytan (du hittar registret i noden registryCredentials i *deployment.template.jsi* filen). Det här värdet är den fullständigt kvalificerade adressen till registret, till exempel **\<your registry\> . azurecr.io/turbofanrouter**.

    > [!NOTE]
    > I den här artikeln använder vi Azure Container Registry som skapades av arbets ytan Azure Machine Learning. Detta är rent för enkelhetens skull. Vi kan ha skapat ett nytt behållar register och publicerat våra moduler där.

1. I terminalen med hjälp av ett kommando tolks gränssnitt kopierar du filerna från exempel modulen till lösningen.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

1. Godkänn frågan om du vill skriva över program.cs-filen.

### <a name="build-router-module"></a>Bygga router-modul

1. I Visual Studio Code väljer du **Terminal**  >  **Konfigurera standard build-uppgift**.

1. Välj **skapa tasks.jspå fil från mall**.

1. Välj **.net Core**.

1. Ersätt innehållet i tasks.jspå med följande kod.

    ```json
    {
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

1. Spara och Stäng tasks.jspå.

1. Kör skapa med `Ctrl + Shift + B` eller kör build-aktivitet för **Terminal**  >  .

### <a name="set-up-module-routes"></a>Konfigurera modul vägar

Som nämnts ovan använder IoT Edge runtime vägar som kon figurer ATS i *deployment.template.jspå* fil för att hantera kommunikation mellan löst kopplade moduler. I det här avsnittet visar vi hur du ställer in vägarna för turbofanRouter-modulen. Vi tar upp inmatnings vägarna först och flyttar sedan på utdata.

#### <a name="inputs"></a>Indata

1. I metoden init () i Program.cs registrerar vi två återanrop för modulen:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Det första återanropet lyssnar efter meddelanden som skickas till **deviceInput** -mottagaren. I diagrammet ovan ser vi att vi vill dirigera meddelanden från valfri löv enhet till den här ingången. I *deployment.template.jspå* fil lägger du till en väg som anger att Edge Hub ska dirigera eventuella meddelanden som tas emot av IoT Edge-enheten som inte har skickats av en IoT Edge modul till indata med namnet "deviceInput" i turbofanRouter-modulen:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Lägg sedan till en väg för meddelanden från rulClassifier-modulen till turbofanRouter-modulen:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Utdata

Lägg till ytterligare fyra vägar i $edgeHub Route-parametern för att hantera utdata från modulen router.

1. Program.cs definierar metoden SendMessageToClassifier (), som använder module-klienten för att skicka ett meddelande till RUL-klassificeraren med hjälp av vägen:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub () använder module-klienten för att skicka bara RUL-data för enheten till IoT Hub via vägen:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter () använder modulen klient för att skicka meddelandet med RUL-data som har lagts till i avroFileWriter-modulen.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage () skickar misslyckade meddelanden överströmmar IoT Hub där de kan vidarebefordras till senare.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

När alla vägar som tagits samman är "$edgeHub"-noden bör se ut som följande JSON:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

  > [!NOTE]
  > Att lägga till turbofanRouter-modulen skapade följande ytterligare väg: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream` . Ta bort den här vägen och lämna bara de vägar som anges ovan i deployment.template.jspå filen.

## <a name="add-avro-writer-module"></a>Lägg till Avro Writer-modul

Avro Writer-modulen har två ansvars områden i vår lösning för att lagra meddelanden och ladda upp filer.

* **Lagra meddelanden**: när modulen Avro Writer tar emot ett meddelande, skrivs meddelandet till det lokala fil systemet i Avro-format. Vi använder en bind-montering, som monterar en katalog (i det här fallet/data/avrofiles) i en sökväg i modulens behållare. Den här monteringen gör att modulen kan skriva till en lokal sökväg (/avrofiles) och de filer som är tillgängliga direkt från den IoT Edge enheten.

* **Ladda upp filer**: modulen Avro Writer använder Azure IoT Hub File Upload-funktionen för att ladda upp filer till ett Azure Storage-konto. När en fil har laddats upp tar modulen bort filen från disken

### <a name="create-module-and-copy-files"></a>Skapa modul och kopiera filer

1. I Visual Studio Code väljer du **Visa**  >  **kommando-palett** och söker sedan efter och väljer **python: Välj tolk**.

1. Välj den installerade python version 3,7 eller senare.

1. Högerklicka på mappen moduler i Visual Studio Code och välj **Lägg till IoT Edge modul**.

1. Välj **Python-modul**.

1. Namnge modulen `avroFileWriter` .

1. När du uppmanas att ange lagrings platsen för Docker-avbildningen använder du samma register som du använde när du lade till modulen router.

1. Kopiera filer från exempel modulen till lösningen.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Godkänn Överskrivningen av main.py.

1. Observera att filemanager.py och schema.py har lagts till i lösningen och att main.py har uppdaterats.

> [!NOTE]
> När du öppnar en python-fil kan du uppmanas att installera pylint. Du behöver inte installera en luddfrie för att slutföra den här självstudien.

### <a name="bind-mount-for-data-files"></a>Bind montering för datafiler

Som tidigare nämnts är modulen Writer beroende av att en bind-montering för att skriva Avro-filer till enhetens fil system.

#### <a name="add-directory-to-device"></a>Lägg till katalog till enhet

1. I Azure Portal startar du IoT Edge Device VM om den inte körs. Anslut till den med SSH. Anslutningen kräver det DNS-namn som du kan kopiera från översikts sidan för den virtuella datorn i Azure Portal.

   ```cmd
   ssh -l <user>@<vm name>.<region>.cloudapp.azure.com
   ```

1. När du har loggat in ska du skapa den katalog som innehåller de sparade löv enhets meddelandena.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

1. Uppdatera katalog behörigheter för att göra den skrivbar av containern.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

1. Verifiera att katalogen nu har Skriv behörighet (w) för användare, grupp och ägare.

   ```bash
   ls -la /data
   ```

   ![Katalog behörigheter för avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Lägg till katalogen i modulen

För att lägga till katalogen i modulens behållare ändrar vi Dockerfiles som är associerad med avroFileWriter-modulen. Det finns tre Dockerfiles kopplade till modulen: Dockerfile. amd64, Dockerfile. amd64. debug och Dockerfile. arm32v7. De här filerna ska sparas i Sync om vi vill felsöka eller distribuera till en ARM32-enhet. I den här artikeln kan du endast fokusera på Dockerfile. amd64.

1. Öppna **C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\Dockerfile.amd64** -filen på din virtuella utvecklings dator.

1. Ändra filen så att ser ut som i följande exempel:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   `mkdir` `chown` Kommandona och instruerar Docker-build-processen att skapa en katalog på den översta nivån som heter/avrofiles i avbildningen och sedan göra moduleuser till ägare av katalogen. Det är viktigt att dessa kommandon infogas efter att modulens användare har lagts till i avbildningen med `useradd` kommandot och innan kontexten växlar till moduleuser (User moduleuser).

1. Om det behövs gör du motsvarande ändringar i Dockerfile. amd64. debug och Dockerfile. arm32v7.

#### <a name="add-bind-configuration-to-the-avrofilewriter"></a>Lägg till bind-konfiguration till avroFileWriter

Det sista steget i att skapa bindningen är att uppdatera deployment.template.jspå (och deployment.debug.template.jspå)-filer med bindnings informationen.

1. Öppna deployment.template.jspå.

2. Ändra modultypen för avroFileWriter genom att lägga till `Binds` parametern som pekar på behållar katalogen/avrofiles till den lokala katalogen på gräns enheten. Din modul definition ska matcha det här exemplet:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

### <a name="bind-mount-for-access-to-configyaml"></a>Bind montering för åtkomst till config. yaml

Vi måste lägga till ytterligare en bindning för modulen skrivare. Den här bindningen ger modulen åtkomst till att läsa anslutnings strängen från/etc/iotedge/config.yaml-filen på den IoT Edge enheten. Vi behöver anslutnings strängen för att skapa en IoTHubClient så att vi kan anropa metoden upload \_ BLOB \_ async för att ladda upp filer till IoT Hub. Stegen för att lägga till denna bindning liknar dem i föregående avsnitt.

#### <a name="update-directory-permission"></a>Uppdatera katalog behörighet

1. Anslut till din IoT Edge-enhet med SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Lägg till Läs behörighet i filen config. yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

1. Kontrol lera att behörigheterna är rätt inställda.

   ```bash
   ls -la /etc/iotedge/
   ```

1. Kontrol lera att behörigheterna för config. yaml är **-r--r--r**--.

#### <a name="add-directory-to-module"></a>Lägg till katalog i modulen

1. Öppna filen **Dockerfile. amd64** på din utvecklings dator.

1. Lägg till ytterligare en uppsättning `mkdir` och `chown` kommandon i filen så att ser ut så här:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

1. Gör motsvarande ändringar i Dockerfile. amd64. debug och Dockerfile. arm32v7.

#### <a name="update-the-module-configuration"></a>Uppdatera konfiguration av modul

1. Öppna filen **deployment.template.js** .

1. Ändra avroFileWriter genom att lägga till en andra rad i den `Binds` parameter som pekar på behållar katalogen (/app/iotconfig) till den lokala katalogen på enheten (/etc/iotedge).

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

1. Gör motsvarande ändringar i deployment.debug.template.js.

## <a name="install-dependencies"></a>Installera beroenden

Modulen skrivare tar ett beroende på två python-bibliotek, fastavro och PyYAML. Vi måste installera beroenden på vår utvecklings dator och instruera Docker-build-processen att installera dem i vår moduls avbildning.

### <a name="pyyaml"></a>PyYAML

1. Öppna filen på utvecklings datorn `C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\requirements.txt` och Lägg till "pyyaml" på en ny rad i filen.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

1. Öppna filen **Dockerfile. amd64** och Lägg till ett `pip install` kommando för att uppgradera installations verktyg.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

1. I en kommando tolk installerar du pyyaml på din utvecklings dator.

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. I requirements.txt lägger du till fastavro efter pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

1. Installera fastavro på din utvecklings dator.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Konfigurera om IoT Hub

Genom att introducera IoT Edge enhet och moduler i systemet har vi ändrat våra förväntningar om vilka data som ska skickas till hubben och för vilket ändamål. Vi behöver konfigurera om routningen i hubben för att hantera vår nya verklighet.

> [!NOTE]
> Vi konfigurerar om navet innan du distribuerar moduler på grund av vissa Hubbs inställningar, måste vara korrekt konfigurerade för att avroFileWriter-modulen ska fungera korrekt

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Konfigurera routning för RUL-meddelanden i IoT Hub

Med routern och klassificeraren på plats förväntar vi dig att ta emot vanliga meddelanden som bara innehåller enhets-ID och RUL förutsägelse för enheten. Vi vill dirigera RUL-data till en egen lagrings plats där vi kan övervaka status för enheterna, bygga rapporter och utlösa aviseringar efter behov. Samtidigt vill vi använda alla enhets data som fortfarande skickas direkt av en löv enhet som ännu inte har anslutits till vår IoT Edge-enhet för att fortsätta att dirigera till den aktuella lagrings platsen.

#### <a name="create-a-rul-message-route"></a>Skapa en RUL meddelande väg

1. I Azure Portal navigerar du till IoT Hub.

1. I menyn i den vänstra rutan, under **meddelanden**, väljer du **meddelanderoutning.**

1. På fliken **vägar** väljer du **Lägg till**.

1. Namnge Route- **RulMessageRoute**.

1. Välj **Lägg till slut punkt** till höger om **slut punkts** väljaren och välj **lagring**.

1. På sidan **Lägg till en lagrings slut punkt** namnger du slut punkten **ruldata**.

1. Välj **Välj en behållare**.

1. På sidan **lagrings konton** letar du reda på det lagrings konto som du använder i den här självstudien, som heter som **iotedgeandml \<unique suffix\>**.

1. Välj behållaren **ruldata** och klicka på **Välj**.

1. På sidan **Lägg till en lagrings slut punkt** väljer du **skapa** för att skapa lagrings slut punkten.

1. Gå tillbaka till sidan **Lägg till en väg** för **cirkulations frågan** och Ersätt `true` med följande fråga:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

1. Expandera avsnittet **test** och sedan avsnittet **meddelande text** . Ersätt meddelande texten med det här exemplet med förväntade meddelanden:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

1. Välj **test väg**. Om testet lyckas visas meddelandet "meddelandet matchade frågan".

1. Klicka på **Spara**.

#### <a name="update-turbofandevicedatatostorage-route"></a>Uppdatera turbofanDeviceDataToStorage-flöde

Vi vill inte dirigera de nya förutsägelse data till vår gamla lagrings plats, så uppdatera vägen för att förhindra det.

1. På **sidan IoT Hub meddelanderoutning väljer** du fliken **vägar** .

1. Välj **turbofanDeviceDataToStorage**, eller det namn du gav till din första enhets data väg.

1. Uppdatera cirkulations frågan till

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

1. Expandera avsnittet **test** och sedan avsnittet **meddelande text** . Ersätt meddelandet med det här exemplet på förväntade meddelanden:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

1. Välj **test väg**. Om testet lyckas visas meddelandet "meddelandet matchade frågan".

1. Välj **Spara**.

### <a name="configure-file-upload"></a>Konfigurera filuppladdning

Konfigurera den IoT Hub fil överförings funktionen så att modulen File Writer kan överföra filer till lagrings utrymme.

1. Från menyn i den vänstra rutan i IoT Hub väljer du **fil uppladdning** under **meddelanden**.

1. Välj **Azure Storage behållare**.

1. Välj ditt lagrings konto i listan.

1. Välj den behållare som börjar med **azureml-blobstore** och som har lagts till med ett GUID och klicka på **Välj**.

1. Välj **Spara**. Portalen meddelar dig när Spara är klar.

> [!Note]
> Vi aktiverar inte överförings aviseringar för den här självstudien, men se [ta emot ett meddelande om fil uppladdning](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) för information om hur du hanterar fil överförings aviseringar.

## <a name="build-publish-and-deploy-modules"></a>Bygga, publicera och distribuera moduler

Nu när vi har gjort konfigurations ändringarna är vi redo att bygga avbildningarna och publicera dem i vårt Azure Container Registry. Bygg processen använder deployment.template.jsfilen för att avgöra vilka moduler som behöver skapas. Inställningarna för varje modul, inklusive version, finns i filen module.jsi mappen modul. Bygg processen kör först en Docker-version på Dockerfiles som matchar den aktuella konfigurationen som finns i module.jspå filen för att skapa en avbildning. Sedan publicerar den avbildningen i registret från module.jspå fil med en versions tagg som matchar den i module.jsfilen. Slutligen genererar den ett konfigurations bara distributions manifest (till exempel deployment.amd64.jspå) som vi ska distribuera till IoT Edge enheten. Den IoT Edge enheten läser informationen från distributions manifestet, och utifrån anvisningarna hämtas modulerna, konfigurerar vägarna och anger önskade egenskaper. Den här distributions metoden har två sido effekter som du bör vara medveten om:

* **Distributions fördröjning:** eftersom den IoT Edge körningen måste identifiera ändringen av de önskade egenskaperna innan den börjar omkonfigureras, kan det ta lite tid efter att du har distribuerat modulerna tills körningen har valt att uppdatera IoT Edge enheten.

* **Versioner av moduler:** om du publicerar en ny version av en moduls behållare till behållar registret med hjälp av samma versions taggar som i föregående modul, laddas inte den nya versionen av modulen ned i körnings miljön. Den jämför med versions tag gen för den lokala avbildningen och den önskade avbildningen från distributions manifestet. Om dessa versioner stämmer vidtas ingen åtgärd i körnings miljön. Därför är det viktigt att öka versionen av modulen varje gången du vill distribuera nya ändringar. Öka versionen genom att ändra egenskapen **version** under egenskapen **tag** i module.jspå fil för den modul som du ändrar. Sedan skapar och publicerar du modulen.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Bygg och publicera

1. Starta Docker om den inte körs på din virtuella utvecklings dator.

1. Starta en ny terminal med en kommando tolk i Visual Studio Code och logga in på Azure Container Registry (ACR).

  Du hittar de nödvändiga användar namnen, lösen orden och inloggnings Server värdena i Azure Portal. Behållar register namnet har formatet "turbofandemo \<unique id\> ". I menyn i den vänstra rutan under **Inställningar** väljer du **åtkomst nycklar** för att visa dem.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. I Visual Studio Code högerklickar du på deployment.template.jspå och väljer **build och Push IoT Edge-lösning**.

### <a name="view-modules-in-the-registry"></a>Visa moduler i registret

När skapandet har slutförts kommer vi att kunna använda Azure Portal för att granska våra publicerade moduler.

1. Öppna Azure Container Registry för den här självstudien. Behållar register namnet har formatet "turbofandemo \<unique id\> ". 

1. Från menyn i den vänstra rutan under **tjänster** väljer du **databaser**.

1. Observera att båda modulerna du skapade, **avrofilewriter** och **turbofanrouter**, visas som databaser.

1. Välj **turbofanrouter** och Observera att du har publicerat en avbildning som taggats som 0.0.1-amd64.

   ![Visa den första taggade versionen av turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Distribuera moduler till IoT Edge enhet

Vi har skapat och konfigurerat modulerna i vår lösning. nu ska vi distribuera modulerna till den IoT Edge enheten.

1. I Visual Studio Code högerklickar du på filen **deployment.amd64.js** i mappen config.

1. Välj **skapa distribution för en enskild enhet**.

1. Välj din IoT Edge enhet, **aaTurboFanEdgeDevice**.

1. Uppdatera panelen Azure IoT Hub enheter i Visual Studio Code Explorer. Du bör se att de tre nya modulerna har distribuerats men ännu inte körs.

1. Uppdatera igen efter några minuter så visas modulerna som körs.

   ![Visa moduler som körs i Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Det kan ta flera minuter innan modulerna startar och kvittas i ett stabilt kör tillstånd. Under den tiden kan du se att moduler startar och slutar när de försöker upprätta en anslutning med modulen IoT Edge Hub.

## <a name="diagnosing-failures"></a>Diagnostisera fel

I det här avsnittet delar vi några metoder för att förstå vad som har varit fel med en modul eller moduler. Ofta kan ett haveri Spotted först från status i Visual Studio Code.

### <a name="identify-failed-modules"></a>Identifiera moduler som misslyckats

* **Visual Studio-kod:** Titta på panelen Azure IoT Hub enheter. Om de flesta moduler är i ett körnings tillstånd men en stoppas, måste du undersöka den stoppade modulen ytterligare. Om alla moduler är i ett stoppat tillstånd under en lång tids period, kan det indikera fel även.

* **Azure Portal:** Genom att navigera till din IoT-hubb i portalen och sedan hitta sidan med enhets information (under IoT Edge, gå in på din enhet) kan det hända att en modul har rapporterat ett fel eller aldrig rapporterat något till IoT Hub.

### <a name="diagnosing-from-the-device"></a>Diagnostisera från enheten

Genom att logga in på den IoT Edge enheten (Linux VM i vårt fall) kan du få till gång till en mer information om status för dina moduler. Huvud mekanismen vi använder är Docker-kommandona som gör att vi kan granska behållare och avbildningar på enheten.

1. Logga in på din IoT Edge-enhet:

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Lista alla behållare som körs. Vi förväntar dig att se en behållare för varje modul med ett namn som motsvarar modulen. Detta kommando visar också den exakta avbildningen för behållaren, inklusive version så att du kan matcha den förväntade. Du kan också lista avbildningar genom att ersätta "bild" för "container" i kommandot.

   ```bash
   sudo docker container ls
   ```

1. Hämta loggarna för en behållare. Det här kommandot matar ut vad som har skrivits till StdErr och StdOut i behållaren. Det här kommandot används för behållare som har startats och sedan dött av någon anledning. Det är också användbart för att förstå vad som händer med edgeAgent-eller edgeHub-behållare.

   ```bash
   sudo docker container logs <container id>
   ```

1. Inspektera en behållare. Det här kommandot ger en ton med information om avbildningen. Data kan filtreras beroende på vad du letar efter. Om du till exempel vill se om bindningarna i avroFileWriter är korrekta kan du använda kommandot:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

1. Anslut till en behållare som körs. Det här kommandot kan vara användbart om du vill undersöka behållaren när den körs:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien är en del av en uppsättning där varje artikel bygger på det arbete som utförts i föregående avsnitt. Vänta tills du är klar med att rensa alla resurser tills du är klar med den sista självstudien.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har vi skapat en IoT Edge-lösning i Visual Studio Code med tre moduler: en klassificerare, en router och en fil skrivare/uppladdning. Vi konfigurerar vägarna så att modulerna kan kommunicera med varandra på gräns enheten. Vi ändrade konfigurationen för gräns enheten och uppdaterade Dockerfiles för att installera beroenden och lägga till bind-monteringar i modulernas behållare. 

Därefter uppdaterade vi konfigurationen av IoT Hub för att dirigera våra meddelanden baserat på typ och för att hantera fil överföringar. Med allt på plats har vi distribuerat modulerna till den IoT Edge enheten och kontrollerat att modulerna kördes korrekt.

Fortsätt till nästa artikel för att börja skicka data och se hur lösningen fungerar.

> [!div class="nextstepaction"]
> [Skicka data via transparent Gateway](tutorial-machine-learning-edge-07-send-data-to-hub.md)