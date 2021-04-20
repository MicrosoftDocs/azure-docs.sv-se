---
title: Snabbstart för att skapa en Azure IoT Edge-enhet i Linux | Microsoft Docs
description: I den här snabbstarten lär du dig hur du skapar en IoT Edge-enhet i Linux och sedan distribuerar förbyggd kod via fjärrstyrning från Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cdc5dd2df0dc6ac682d37aea3328545fcb7e5ad2
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728616"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Snabbstart: Distribuera din första IoT Edge till en virtuell Linux-enhet

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Testa Azure IoT Edge i den här snabbstarten genom att distribuera containerkod till en virtuell Linux IoT Edge enhet. IoT Edge kan du fjärrhantera kod på dina enheter så att du kan skicka fler av dina arbetsbelastningar till gränsen. För den här snabbstarten rekommenderar vi att du använder en virtuell Azure-dator för din IoT Edge-enhet, vilket gör att du snabbt kan skapa en testdator och sedan ta bort den när du är klar.

I den här snabbstarten lär du dig att:

* Skapa en IoT Hub.
* Registrera en IoT Edge-enhet till din IoT Hub.
* Installera och starta IoT Edge på en virtuell enhet.
* Fjärrdistribuera en modul till en IoT Edge-enhet.

![Diagram – Snabbstart av arkitektur för enhet och moln](./media/quickstart-linux/install-edge-full.png)

Den här snabbstarten visar hur du skapar en virtuell Linux-dator som är konfigurerad att vara en IoT Edge enhet. Sedan distribuerar du en modul från Azure Portal till din enhet. Modulen som används i den här snabbstarten är en simulerad sensor som genererar temperatur-, fuktighets- och tryckdata. De andra Azure IoT Edge självstudierna bygger på det arbete du gör här genom att distribuera ytterligare moduler som analyserar simulerade data för affärsinsikter.

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Förbered din miljö för Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Molnresurser:

* En resursgrupp som du använder för att hantera alla resurser i den här snabbstarten. Vi använder exempelresursgruppens namn **IoTEdgeResources i den** här snabbstarten och i följande självstudier.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Starta snabbstarten genom att skapa en IoT-hubb med Azure CLI.

![Diagram – Skapa en IoT-hubb i molnet](./media/quickstart-linux/create-iot-hub.png)

Den kostnadsfria nivån för IoT Hub fungerar för den här snabbstarten. Om du har använt IoT Hub tidigare och redan har skapat en hubb kan du använda den IoT-hubben.

Följande kod skapar en kostnadsfri **F1-hubb** i resursgruppen **IoTEdgeResources**. Ersätt `{hub_name}` med ett unikt namn för din IoT-hubb. Det kan ta några minuter att skapa en IoT Hub.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Om du får ett felmeddelande eftersom det redan finns en kostnadsfri hubb i din prenumeration ändrar du SKU till **S1**. Varje prenumeration kan bara ha en kostnadsfri IoT Hub. Om du får ett felmeddelande om att IoT Hub-namnet inte är tillgängligt innebär det att någon annan redan har en hubb med det namnet. Prova med ett nytt namn.

## <a name="register-an-iot-edge-device"></a>Registrera en IoT Edge-enhet

Registrera en IoT Edge-enhet med IoT-hubben som du nyss skapade.

![Diagram – Registrera en enhet med en IoT Hub-identitet](./media/quickstart-linux/register-device.png)

Skapa en enhetsidentitet för din IoT Edge-enhet så att den kan kommunicera med din IoT Hub. Enhetsidentiteten finns i molnet, och du använder en unik enhetsanslutningssträng för att associera en fysisk enhet med en enhetsidentitet.

Eftersom IoT Edge-enheter fungerar och kan hanteras på annat sätt än typiska IoT-enheter deklarerar du den här identiteten till att höra till en IoT Edge-enhet med flaggan `--edge-enabled`.

1. I den Azure Cloud Shell du följande kommando för att skapa en enhet med namnet **myEdgeDevice** i hubben.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Om du får ett felmeddelande om iothubowner-principnycklar kontrollerar du att Cloud Shell kör den senaste versionen av tillägget azure-iot.

2. Visa anslutningssträngen för enheten, som länkar den fysiska enheten med dess identitet i IoT Hub. Den innehåller namnet på din IoT-hubb, namnet på din enhet och sedan en delad nyckel som autentiserar anslutningar mellan dem. Vi refererar till den här anslutningssträngen igen i nästa avsnitt när du ställer in din IoT Edge enhet.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![Visa anslutningssträng från CLI-utdata](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Konfigurera din IoT Edge-enhet

Skapa en virtuell dator med Azure IoT Edge körtid på den.

![Diagram – Starta körningen på enheten](./media/quickstart-linux/start-runtime.png)

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Den har tre komponenter. Den *IoT Edge säkerhetsdaemonen* startar varje gång en IoT Edge startar och startar enheten genom att starta IoT Edge agenten. Agenten *IoT Edge* distribution och övervakning av moduler på den IoT Edge enheten, inklusive IoT Edge hubb. Hubben *IoT Edge* hanterar kommunikationen mellan moduler på IoT Edge enhet, samt mellan enheten och IoT Hub.

Under körningskonfigurationen anger du en enhetsanslutningssträng. Det här är strängen som du hämtade från Azure CLI. Den här strängen associerar den fysiska enheten med IoT Edge-enhetsidentiteten i Azure.

### <a name="deploy-the-iot-edge-device"></a>Distribuera IoT Edge enhet

Det här avsnittet använder Azure Resource Manager mall för att skapa en ny virtuell dator och installera IoT Edge på den. Om du vill använda din egen Linux-enhet i stället kan du följa installationsstegen i Installera [Azure IoT Edge runtime](how-to-install-iot-edge.md)och sedan gå tillbaka till den här snabbstarten.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Använd följande CLI-kommando för att skapa IoT Edge enhet baserat på den fördefinierade [mallen iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy)

* För bash- Cloud Shell-användare kopierar du följande kommando till en textredigerare, ersätter platshållartexten med din information och kopierar sedan till bash- eller Cloud Shell-fönstret:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* För PowerShell-användare kopierar du följande kommando till PowerShell-fönstret och ersätter sedan platshållartexten med din egen information:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://aka.ms/iotedge-vm-deploy" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Använd följande CLI-kommando för att skapa IoT Edge enhet baserat på den fördefinierade [mallen iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy/tree/1.2.0)

* För bash- Cloud Shell-användare kopierar du följande kommando till en textredigerare, ersätter platshållartexten med din information och kopierar sedan till bash- eller Cloud Shell-fönstret:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* För PowerShell-användare kopierar du följande kommando till PowerShell-fönstret och ersätter sedan platshållartexten med din egen information:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```
:::moniker-end
<!-- end 1.2 -->

Den här mallen använder följande parametrar:

| Parameter | Beskrivning |
| --------- | ----------- |
| **resursgrupp** | Den resursgrupp där resurserna ska skapas. Använd **standard-IoTEdgeResources** som vi har använt i hela den här artikeln eller ange namnet på en befintlig resursgrupp i din prenumeration. |
| **template-uri** | En pekare till Resource Manager som vi använder. |
| **dnsLabelPrefix** | En sträng som ska användas för att skapa den virtuella datorns värdnamn. Ersätt platshållartexten med ett namn för den virtuella datorn. |
| **adminUsername** | Ett användarnamn för den virtuella datorns administratörskonto. Använd exemplet **azureUser eller** ange ett nytt användarnamn. |
| **deviceConnectionString** | Anslutningssträngen från enhetsidentiteten i IoT Hub, som används för att konfigurera IoT Edge på den virtuella datorn. CLI-kommandot i den här parametern hämtar anslutningssträngen åt dig. Ersätt platshållartexten med namnet på din IoT-hubb. |
| **authenticationType** | Autentiseringsmetoden för administratörskontot. Den här snabbstarten **använder** lösenordsautentisering, men du kan också ange den här parametern till **sshPublicKey**. |
| **adminPasswordOrKey** | Lösenordet eller värdet för SSH-nyckeln för administratörskontot. Ersätt platshållartexten med ett säkert lösenord. Lösenordet måste vara minst 12 tecken långt och innehålla tre av fyra av följande: gemener, versaler, siffror och specialtecken. |

När distributionen är klar bör du få JSON-formaterade utdata i CLI som innehåller SSH-information för att ansluta till den virtuella datorn. Kopiera värdet för den offentliga **SSH-posten** i **utdataavsnittet:**

   ![Hämta offentligt SSH-värde från utdata](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>Visa status för IoT Edge-körningen

Resten av kommandona i den här snabbstarten sker på din IoT Edge-enhet så att du kan se vad som händer på enheten. Om du använder en virtuell dator ansluter du till den datorn nu med det administratörsnamn som du konfigurerade och DNS-namnet som utdata från distributionskommandot. Du hittar även DNS-namnet på den virtuella datorns översiktssida i Azure Portal. Använd följande kommando för att ansluta till din virtuella dator. Ersätt `{admin username}` och med dina egna `{DNS name}` värden.

   ```console
   ssh {admin username}@{DNS name}
   ```

När du har anslutit till den virtuella datorn kontrollerar du att körningen har installerats och konfigurerats IoT Edge enheten.

<!--1.1 -->
:::moniker range="iotedge-2018-06"

1. Kontrollera att den IoT Edge säkerhetsdaemonen körs som en systemtjänst.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Se IoT Edge daemon som körs som en systemtjänst](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >Förhöjd behörighet krävs för att köra `iotedge`-kommandon. När du loggar ut från datorn och sedan loggar in igen för första gången efter installationen av IoT Edge-körningen, så uppdateras dina behörigheter automatiskt. Tills dess `sudo` ska du använda framför kommandona.

2. Hämta tjänstloggar om du behöver felsöka tjänsten.

   ```bash
   journalctl -u iotedge
   ```

3. Visa alla moduler som körs på din IoT Edge-enhet. Eftersom det är första gången du startar tjänsten, bör du bara kunna se den **edgeAgent**-modul som körs. EdgeAgent-modulen körs som standard och hjälper till att installera och starta eventuella ytterligare moduler som du distribuerar till enheten.

   ```bash
   sudo iotedge list
   ```

   ![Visa en modul på din enhet](./media/quickstart-linux/iotedge-list-1.png)
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Kontrollera att IoT Edge körs. Följande kommando ska returnera statusen **OK** om IoT Edge körs eller ange tjänstfel.

   ```bash
   sudo iotedge system status
   ```

   >[!TIP]
   >Förhöjd behörighet krävs för att köra `iotedge`-kommandon. När du loggar ut från datorn och sedan loggar in igen för första gången efter installationen av IoT Edge-körningen, så uppdateras dina behörigheter automatiskt. Tills dess `sudo` ska du använda framför kommandona.

2. Hämta tjänstloggar om du behöver felsöka tjänsten.

   ```bash
   sudo iotedge system logs
   ```

3. Visa alla moduler som körs på din IoT Edge-enhet. Eftersom det är första gången du startar tjänsten, bör du bara kunna se den **edgeAgent**-modul som körs. EdgeAgent-modulen körs som standard och hjälper till att installera och starta eventuella ytterligare moduler som du distribuerar till enheten.

   ```bash
   sudo iotedge list
   ```

:::moniker-end
<!-- end 1.2 -->

IoT Edge-enheten har nu konfigurerats. Den är redo att köra molndistribuerade moduler.

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera din Azure IoT Edge-enhet från molnet för att distribuera en modul som ska skicka telemetridata till IoT Hub.

![Diagram – Distribuera en modul från ett moln till en enhet](./media/quickstart-linux/deploy-module.png)

<!-- [!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2 -->

En av de viktigaste funktionerna i Azure IoT Edge är att distribuera kod till IoT Edge enheter från molnet. *IoT Edge moduler är* körbara paket som implementeras som containrar. I det här avsnittet distribuerar du en förbyggd modul från [avsnittet IoT Edge Modules i Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) direkt från Azure IoT Hub.

Den modul som du distribuerar i det här avsnittet simulerar en sensor och skickar genererade data. Modulen är användbar kod när du kör igång med IoT Edge eftersom du kan använda simulerade data för utveckling och testning. Om du vill se exakt vad den här modulen gör kan du visa [källkoden för den simulerade temperatursensorn](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Följ de här stegen för att **starta guiden Konfigurera** moduler för att distribuera din första modul från Azure Marketplace.

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din IoT-hubb.

1. På menyn till vänster, under **Automatisk Enhetshantering** väljer du **IoT Edge**.

1. Välj målenhetens enhets-ID i listan över enheter.

1. Välj Ange moduler i det **övre fältet.**

   ![Skärmbild som visar hur du väljer Ange moduler.](./media/quickstart/select-set-modules.png)

### <a name="modules"></a>Moduler

Det första steget i guiden är att välja vilka moduler som du vill köra på enheten.

Under **IoT Edge Modules**(Moduler) öppnar **du** listrutan Add (Lägg till) och väljer sedan Marketplace **Module (Marketplace-modul).**

   ![Skärmbild som visar den nedrullningade menyn Lägg till.](./media/quickstart/add-marketplace-module.png)

I **IoT Edge-modulen Marketplace** söker du efter och väljer `Simulated Temperature Sensor` modulen. Modulen läggs till i avsnittet IoT Edge Moduler med önskad **körningsstatus.**

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Välj **Körningsinställningar för** att öppna inställningarna för edgeHub- och edgeAgent-modulerna. I det här inställningsavsnittet kan du hantera körningsmodulerna genom att lägga till miljövariabler eller ändra alternativen för att skapa.

Uppdatera fältet **Avbildning** för både edgeHub- och edgeAgent-modulerna så att versionstaggen 1.2 används. Exempel:

* `mcr.microsoft.com/azureiotedge-hub:1.2`
* `mcr.microsoft.com/azureiotedge-agent:1.2`

Välj **Spara** för att tillämpa ändringarna i körningsmodulerna.

:::moniker-end
<!--end 1.2-->

Välj **Nästa: Vägar** för att fortsätta till nästa steg i guiden.

   ![Skärmbild som visar hur du fortsätter till nästa steg efter att modulen har lagts till.](./media/quickstart/view-temperature-sensor-next-routes.png)

### <a name="routes"></a>Vägar

På fliken **Vägar** tar du bort standardvägen, dirigerar och väljer sedan **Nästa: Granska +** skapa för att fortsätta till nästa steg i guiden.

   >[!Note]
   >Vägar skapas med hjälp av namn- och värdepar. Du bör se två vägar på den här sidan. Standardvägen, **route**, skickar alla meddelanden till IoT Hub (som kallas `$upstream` ). En andra väg, **SimulatedTemperatureSensorToIoTHub,** skapades automatiskt när du lade till modulen från Azure Marketplace. Den här vägen skickar alla meddelanden från modulen för simulerad temperatur till IoT Hub. Du kan ta bort standardvägen eftersom den är redundant i det här fallet.

   ![Skärmbild som visar hur du tar bort standardvägen och sedan går vidare till nästa steg.](./media/quickstart/delete-route-next-review-create.png)

### <a name="review-and-create"></a>Granska och skapa

Granska JSON-filen och välj sedan **Skapa.** JSON-filen definierar alla moduler som du distribuerar till din IoT Edge enhet. Du ser modulen **SimulatedTemperatureSensor och** de två körningsmodulerna **edgeAgent** och **edgeHub.**

   >[!Note]
   >När du skickar en ny distribution till en IoT Edge-enhet distribueras ingenting till enheten. I stället frågar enheten regelbundet IoT Hub efter nya instruktioner. Om enheten hittar ett uppdaterat distributionsmanifest använder den informationen om den nya distributionen för att hämta modulavbildningarna från molnet och börjar sedan köra modulerna lokalt. Den här processen kan ta några minuter.

När du har skapat information om moduldistributionen återgår guiden till sidan med enhetsinformation. Visa distributionsstatusen på **fliken** Moduler.

Du bör se tre moduler: **$edgeAgent**, **$edgeHub** och **SimulatedTemperatureSensor.** Om en eller flera av modulerna har **YES** (JA) under **SPECIFIED IN DEPLOYMENT** (ANGES I DISTRIBUTION) men inte under REPORTED BY **DEVICE**(RAPPORTERAS AV ENHET) IoT Edge enheten fortfarande startar dem. Vänta några minuter och uppdatera sedan sidan.

   ![Skärmbild som visar Simulerad temperatursensor i listan över distribuerade moduler.](./media/quickstart/view-deployed-modules.png)

## <a name="view-generated-data"></a>Visa genererade data

I den här snabbstarten skapade du en ny IoT Edge-enhet och installerade IoT Edge-körningsmiljön på den. Sedan använde du Azure-portalen för att distribuera en IoT Edge-modul som ska köras på enheten utan att några ändringar behövs göras i själva enheten.

I det här fallet genererar modulen som du push-pushar exempelmiljödata som du kan använda för att testa senare. Den simulerade sensorn övervakar både en dator och miljön runt datorn. Den här sensorn kan till exempel finnas i ett serverrum, på fabriksgolvet eller på en vindturbin. Meddelandet innehåller rumstemperatur och fuktighet, maskintemperatur och tryck samt en tidsstämpel. I IoT Edge-självstudierna används de data som skapas av den här modulen som testdata för analys.

Öppna kommandotolken på IoT Edge-enheten igen eller använd SSH-anslutningen från Azure CLI. Kontrollera att modulen distribuerades från molnet som körs på IoT Edge-enheten:

   ```bash
   sudo iotedge list
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
   ![Visa tre moduler på enheten](./media/quickstart-linux/iotedge-list-2-version-201806.png)
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
   ![Visa tre moduler på enheten](./media/quickstart-linux/iotedge-list-2-version-202011.png)
:::moniker-end

Visa de meddelanden som skickas från temperatursensor-modulen:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge-kommandon är skiftlägeskänsliga när det gäller modulnamnen.

   ![Visa data från modulen](./media/quickstart-linux/iotedge-logs.png)

Du kan också se när meddelandena kommer till din IoT-hubb med [hjälp av Azure IoT Hub för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med IoT Edge-självstudierna kan du använda enheten du registrerade och konfigurerade i den här snabbstarten. Annars kan du ta bort de Azure-resurser som du skapade för att undvika kostnader.

Om du skapade den virtuella datorn och IoT-hubben i en ny resursgrupp kan du ta bort den gruppen och alla associerade resurser. Kontrollera att det inte finns något du vill behålla i innehållet i resursgruppen. Om du inte vill ta bort hela gruppen kan du ta bort enskilda resurser i stället.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras.

Ta bort gruppen **IoTEdgeResources**. Det kan ta några minuter att ta bort en resursgrupp.

```azurecli-interactive
az group delete --name IoTEdgeResources --yes
```

Du kan bekräfta att resursgruppen har tagits bort genom att visa listan över resursgrupper.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en IoT Edge-enhet och använde molngränssnittet i Azure IoT Edge för att distribuera kod till enheten. Nu har du en testenhet som genererar rådata för sin miljö.

Nästa steg är att konfigurera din lokala utvecklingsmiljö så att du kan börja skapa IoT Edge moduler som kör din affärslogik.

> [!div class="nextstepaction"]
> [Börja utveckla IoT Edge moduler för Linux-enheter](tutorial-develop-for-linux.md)
