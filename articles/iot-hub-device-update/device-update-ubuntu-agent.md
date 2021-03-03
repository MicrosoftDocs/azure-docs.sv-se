---
title: Enhets uppdatering för Azure IoT Hub-självstudier med Ubuntu Server 18,04 x64 Package agent | Microsoft Docs
description: Kom igång med enhets uppdatering för Azure IoT Hub med hjälp av Ubuntu Server 18,04 x64 Package agent.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 19be3b141fb663ea0f4f11d811bf6ffc33252504
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665180"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-server-1804-x64-package-agent"></a>Enhets uppdatering för Azure IoT Hub-självstudier med Ubuntu Server 18,04 x64 Package agent

Enhets uppdatering för IoT Hub stöder två typer av uppdateringar – bildbaserad och paket-baserad.

Paketbaserade uppdateringar är riktade uppdateringar som bara ändrar en specifik komponent eller ett program på enheten. Detta leder till lägre konsumtion av bandbredd och hjälper till att minska tiden för att ladda ned och installera uppdateringen. Paket uppdateringar tillåter normalt mindre stillestånd av enheter när en uppdatering tillämpas och du slipper att skapa avbildningar.

Den här självstudien vägleder dig genom stegen för att slutföra en paket-baserad uppdatering från början till slut genom enhets uppdatering för IoT Hub. Vi kommer att använda en exempel paket agent för Ubuntu Server 18,04 x64 för den här självstudien. Även om du planerar att använda en annan konfiguration av operativ system plattformen är den här självstudien fortfarande användbar för att lära dig om verktygen och begreppen i enhets uppdatering för IoT Hub. Slutför den här introduktionen till en uppdaterings process från slut punkt till slut punkt och välj sedan önskad typ av uppdatering och operativ system plattform för att få information. Du kan använda enhets uppdatering för IoT Hub för att uppdatera en Azure IoT-eller Azure IoT Edge-enhet med hjälp av den här självstudien. 

I den här självstudien får du lära dig hur man
> [!div class="checklist"]
> * Konfigurera lagrings paket för enhets uppdatering
> * Hämta och installera enhets uppdaterings agenten och dess beroenden
> * Lägg till en tagg till din IoT-enhet
> * Importera en uppdatering
> * Skapa en enhetsgrupp.
> * Distribuera en paket uppdatering
> * Övervaka uppdaterings distributionen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
* Åtkomst till en IoT Hub. Vi rekommenderar att du använder en S1-nivå (standard) eller högre.
* En Azure IoT-eller Azure IoT Edge-enhet som kör Ubuntu Server 18,04 x64, ansluten till IoT Hub.
   * Om du använder en Azure IoT Edge-enhet ser du till att den är på v-1.2.0 för Edge-körningsmiljön eller högre 
* Om du inte använder en Azure IoT Edge-enhet installerar du [det senaste `aziot-identity-service` paketet (förhands granskning) på din IoT-enhet](https://github.com/Azure/iot-identity-service/actions/runs/575919358) 
* [Enhets uppdaterings konto och instans länkad till samma IoT Hub som ovan.](create-device-update-account.md)

## <a name="configure-device-update-package-repository"></a>Konfigurera lagrings paket för enhets uppdatering

1. Installera den lagrings plats konfiguration som matchar enhetens operativ system. I den här självstudien kommer detta att vara Ubuntu Server 18,04. 
   
   ```shell
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

2. Kopiera den genererade listan till katalogen sources. list. d.

   ```shell
      sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```
   
3. Installera den offentliga nyckeln för Microsoft GPG.

   ```shell
      curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
      sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-device-update-deb-agent-packages"></a>Installera enhets uppdatering. deb agent-paket

1. Uppdatera paket listor på din enhet

   ```shell
      sudo apt-get update
   ```

2. Installera deviceupdate-agent-paketet och dess beroenden

   ```shell
      sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
   ```

Enhets uppdatering för Azure IoT Hub program varu paket omfattas av följande licens villkor:
   * [Enhets uppdatering för IoT Hub licens](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Klient licens för leverans optimering](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Läs licens villkoren innan du använder ett paket. Din installation och användning av ett paket utgör ditt godkännande av dessa villkor. Om du inte godkänner licens villkoren ska du inte använda det paketet.

## <a name="configure-device-update-agent-using-azure-iot-identity-service-preview"></a>Konfigurera enhets uppdaterings agenten med hjälp av Azure IoT Identity service (för hands version)

När du har installerat de nödvändiga paketen måste du etablera enheten med dess moln identitet och autentiseringsinformation.

1. Öppna konfigurations filen

   ```shell
      sudo nano /etc/aziot/config.toml
   ```

2. Hitta konfigurations avsnittet för etablering i filen. Ta bort kommentaren till avsnittet manuell etablering med anslutnings strängen. Uppdatera värdet för connection_string med anslutnings strängen för din IoT-enhet (Edge). Se till att alla andra etablerings avsnitt är kommenterade.


   ```toml
      # Manual provisioning configuration using a connection string
      [provisioning]
      source = "manual"
      iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
      device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
      dynamic_reprovisioning = false 
   ```

3. Spara och Stäng filen med Ctrl + X, Y

4. Tillämpa konfigurationen. 

   Använd följande kommando om du använder en IoT Edge enhet. 
   
   ```shell
      sudo iotedge config apply
   ```
   
   Om du använder en IoT-enhet, med `aziot-identity-service` paketet installerat, använder du följande kommando. 
      
   ```shell
      sudo aziotctl config apply
   ```

5. Du kan också kontrol lera att tjänsterna körs av

    ```shell
       sudo systemctl list-units --type=service | grep 'adu-agent\.service\|deliveryoptimization-agent\.service'
    ```

    Utdata ska läsa:

    ```markdown
       adu-agent.service                   loaded active running Device Update for IoT Hub Agent daemon.

       deliveryoptimization-agent.service               loaded active running deliveryoptimization-agent.service: Performs content delivery optimization tasks   `
    ```

## <a name="add-a-tag-to-your-device"></a>Lägg till en tagg till din enhet

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till IoT Hub.

2. Från IoT-enheter eller IoT Edge i det vänstra navigerings fönstret hittar du din IoT-enhet och navigerar till enheten med dubbla.

3. Ta bort eventuella befintliga enhets uppdaterings märken i enhets numret genom att ställa in dem på null.

4. Lägg till ett nytt märkes värde för enhets uppdatering som visas nedan.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Importera uppdatering

1. Ladda ned följande [apt manifest fil](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json) och [Importera manifest filen](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json). Det här apt-manifestet kommer att installera den senaste tillgängliga versionen av `libcurl4-doc package` till din IoT-enhet. 

   Du kan också ladda ned [manifest filen för apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json) och [Importera manifest filen](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json). Detta kommer att installera en speciell version v-7.58.0 av `libcurl4-doc package` till din IoT-enhet. 

2. I Azure Portal väljer du alternativet enhets uppdateringar under automatisk enhets hantering från det vänstra navigerings fältet i IoT Hub.

3. Välj fliken uppdateringar.

4. Välj "+ Importera ny uppdatering".

5. Välj mappikonen eller text rutan under "Välj en import manifest fil". Du kommer att se en dialog ruta för fil väljaren. Välj det import manifest som du laddade ned tidigare. Välj sedan mappikonen eller text rutan under "Välj en eller flera uppdateringsfiler". Du kommer att se en dialog ruta för fil väljaren. Välj den apt manifest uppdaterings fil som du laddade ned tidigare.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Skärm bild som visar val av uppdaterings fil." lightbox="media/import-update/select-update-files.png":::

6. Välj mappikonen eller text rutan under "Välj en lagrings behållare". Välj sedan lämpligt lagrings konto.

7. Om du redan har skapat en behållare kan du återanvända den. (Annars väljer du "+ behållare" för att skapa en ny lagrings behållare för uppdateringar.).  Välj den behållare som du vill använda och klicka på Välj.

   :::image type="content" source="media/import-update/container.png" alt-text="Skärm bild som visar val av behållare." lightbox="media/import-update/container.png":::

8. Välj "Skicka" för att starta import processen.

9. Import processen påbörjas och skärmen ändras till avsnittet "import historik". Välj Uppdatera om du vill visa förloppet tills importen är klar. Beroende på Uppdateringens storlek kan detta slutföras på några minuter, men det kan ta längre tid.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Skärm bild som visar import ordningen för uppdateringar." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. När kolumnen status visar att importen har slutförts väljer du rubriken "klar att distribuera". Du bör se den importerade uppdateringen i listan nu.

[Läs mer](import-update.md) om att importera uppdateringar.

## <a name="create-update-group"></a>Skapa uppdaterings grupp

1. Gå till den IoT Hub du tidigare anslöt till din enhets uppdaterings instans.

2. Välj alternativet enhets uppdateringar under automatisk enhets hantering från det vänstra navigerings fältet.

3. Välj fliken grupper överst på sidan. 

4. Välj knappen Lägg till för att skapa en ny grupp.

5. Välj den IoT Hub-tagg som du skapade i föregående steg från listan. Välj Skapa uppdaterings grupp.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Skärm bild som visar val av tagg." lightbox="media/create-update-group/select-tag.PNG":::

[Lär dig mer](create-update-group.md) om att lägga till taggar och skapa uppdaterings grupper


## <a name="deploy-update"></a>Distribuera uppdatering

1. När gruppen har skapats bör du se en ny uppdatering som är tillgänglig för enhets gruppen, med en länk till uppdateringen under väntande uppdateringar. Du kan behöva uppdatera en gång. 

2. Klicka på den tillgängliga uppdateringen.

3. Bekräfta att rätt grupp har valts som mål grupp. Schemalägg distributionen och välj sedan distribuera uppdatering.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Välj uppdatera" lightbox="media/deploy-update/select-update.png":::

4. Visa diagrammet efterlevnad. Nu bör du se att uppdateringen pågår. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Uppdatering pågår" lightbox="media/deploy-update/update-in-progress.png":::

5. När enheten har uppdaterats bör du se att ditt Compliance-diagram och distributions information uppdateras för att avspegla samma. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Uppdateringen är klar" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Övervaka en uppdaterings distribution

1. Välj fliken distributioner högst upp på sidan.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Fliken distributioner" lightbox="media/deploy-update/deployments-tab.png":::

2. Välj den distribution du skapade för att Visa distributions informationen.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Distributionsinformation" lightbox="media/deploy-update/deployment-details.png":::

3. Välj Uppdatera för att visa den senaste statusinformationen. Fortsätt med den här processen tills statusen har ändrats till slutfört.

Du har nu slutfört en lyckad paket uppdatering från slut punkt till slut punkt med enhets uppdatering för IoT Hub på en Ubuntu Server 18,04 x64-enhet. 

## <a name="bonus-steps"></a>Bonus steg

1. Ladda ned följande [apt manifest fil](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json) och [Importera manifest filen](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json). Detta apt-manifest tar bort det som installeras `libcurl4-doc package` från din IoT-enhet. 

2. Upprepa avsnitten "Importera uppdatering" och "distribuera uppdatering"

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver kan du rensa enhets uppdaterings kontot, instansen IoT Hub och IoT-enheten. Du kan göra det genom att gå till varje enskild resurs och välja "ta bort". Observera att du måste rensa en enhets uppdaterings instans innan du rensar enhets uppdaterings kontot. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bild uppdatering på Raspberry Pi 3 B + självstudie](device-update-raspberry-pi.md)

