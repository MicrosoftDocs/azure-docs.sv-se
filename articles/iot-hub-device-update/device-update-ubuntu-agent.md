---
title: Enhets uppdatering för Azure IoT Hub-självstudier med Ubuntu Server 18,04 x64 Package agent | Microsoft Docs
description: Kom igång med enhets uppdatering för Azure IoT Hub med hjälp av Ubuntu Server 18,04 x64 Package agent.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 245998fb7229c483fb7f664ea000b62abf07eda9
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149791"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Uppdatering av enhets uppdateringar för Azure IoT Hub med paket agenten på Ubuntu Server 18,04 x64

Enhets uppdatering för IoT Hub stöder två typer av uppdateringar – bildbaserad och paket-baserad.

Paketbaserade uppdateringar är riktade uppdateringar som bara ändrar en specifik komponent eller ett program på enheten. Detta leder till lägre konsumtion av bandbredd och hjälper till att minska tiden för att ladda ned och installera uppdateringen. Paket uppdateringar tillåter normalt mindre stillestånd av enheter när en uppdatering tillämpas och du slipper att skapa avbildningar.

Den här självstudien vägleder dig genom stegen för att slutföra en paket-baserad uppdatering från början till slut genom enhets uppdatering för IoT Hub. I den här självstudien använder vi en Ubuntu Server 18,04 x64 som kör Azure IoT Edge och uppdaterings paket agenten för enheten. I självstudien visas hur du uppdaterar ett exempel paket, men med liknande steg kan du uppdatera andra paket som Azure IoT Edge eller den behållar motor som används.

Verktygen och begreppen i den här självstudien gäller fortfarande även om du planerar att använda en annan konfiguration av operativ system plattformen. Slutför den här introduktionen till en uppdaterings process från slut punkt till slut punkt och välj sedan önskad typ av uppdatering och operativ system plattform för att få information.

I den här självstudien får du lära dig hur man
> [!div class="checklist"]
> * Hämta och installera enhets uppdaterings agenten och dess beroenden
> * Lägg till en tagg till din enhet
> * Importera en uppdatering
> * Skapa en enhetsgrupp.
> * Distribuera en paket uppdatering
> * Övervaka uppdaterings distributionen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Åtkomst till en IoT Hub. Vi rekommenderar att du använder en S1-nivå (standard) eller högre.
* En instans av enhets uppdatering och ett konto som är länkat till IoT Hub.
  * Följ guiden för att [skapa och länka ett enhets uppdaterings konto](create-device-update-account.md) om du inte har gjort det tidigare.
* [Anslutnings strängen för en IoT Edge enhet](../iot-edge/how-to-register-device.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-connection-strings).

## <a name="prepare-a-device"></a>Förbered en enhet
### <a name="using-the-automated-deploy-to-azure-button"></a>Använda knappen automatiserad distribution till Azure

För enkelhetens skull använder den här självstudien en [Cloud-Init](../virtual-machines/linux/using-cloud-init.md)-baserad [Azure Resource Manager-mall](../azure-resource-manager/templates/overview.md) som hjälper dig att snabbt skapa en virtuell dator med Ubuntu 18,04 LTS. Den installerar både Azure IoT Edge Runtime och enhets uppdaterings paket agenten och konfigurerar sedan automatiskt enheten med etablerings information med hjälp av enhets anslutnings strängen för en IoT Edge enhet (krav) som du anger. Detta gör att du inte behöver starta en SSH-session för att slutföra installationen.

1. Börja genom att klicka på knappen nedan:

   [![Knappen Distribuera till Azure för iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2F1.2.0-rc4%2FedgeDeploy.json)

1. Fyll i de tillgängliga formulär fälten i fönstret nyligen startade:

    > [!div class="mx-imgBorder"]
    > [![Skärmbild med mallen iotedge-vm-deploy](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Prenumeration**: den aktiva Azure-prenumerationen för att distribuera den virtuella datorn till.

    **Resurs grupp**: en befintlig eller nyligen skapad resurs grupp som innehåller den virtuella datorn och dess associerade resurser.

    **DNS-etikett-prefix**: ett obligatoriskt värde som används för att använda den virtuella datorns värdnamn som prefix.

    **Admin-användar namn**: ett användar namn som kommer att tillhandahålla rot behörigheter för distribution.

    **Enhets anslutnings sträng**: en [enhets anslutnings sträng](../iot-edge/how-to-register-device.md) för en enhet som har skapats i den avsedda [IoT Hub](../iot-hub/about-iot-hub.md).

    **VM-storlek**: [storleken](../cloud-services/cloud-services-sizes-specs.md) på den virtuella dator som ska distribueras

    **Ubuntu OS-version**: den version av Ubuntu-operativsystem som ska installeras på den virtuella bas datorn. Lämna standardvärdet oförändrat eftersom det är inställt på Ubuntu 18,04-LTS redan.

    **Plats**: det [geografiska område](https://azure.microsoft.com/global-infrastructure/locations/) som används för att distribuera den virtuella datorn till, standardvärdet är platsen för den valda resurs gruppen.

    **Autentiseringstyp**: Välj **sshPublicKey** eller **lösen ord** beroende på dina inställningar.

    **Administratörs lösen ord eller nyckel**: värdet för den offentliga SSH-nyckeln eller värdet för lösen ordet beroende på valet av autentiseringstyp.

    När alla fält har fyllts i markerar du kryss rutan längst ned på sidan för att acceptera villkoren och väljer **köp** för att starta distributionen.

1. Verifiera att distributionen slutfördes korrekt. Vänta några minuter efter att distributionen har slutförts efter installationen och konfigurationen för att slutföra installationen av IoT Edge och enhets paketets uppdaterings agent.

   En VR-resurs bör ha distribuerats till den valda resursgruppen.  Anteckna dator namnet. formatet bör vara i formatet `vm-0000000000000` . Notera även det associerade **DNS-namnet**, som ska ha formatet `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    **DNS-namnet** kan hämtas från avsnittet **Översikt** i den nyligen distribuerade virtuella datorn i Azure-portalen.

    > [!div class="mx-imgBorder"]
    > [![Skärm bild som visar DNS-namnet för den virtuella iotedge-datorn](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > Om du vill använda SSH till den här virtuella datorn efter installationen använder du det associerade **DNS-namnet** med kommandot: `ssh <adminUsername>@<DNS_Name>`

### <a name="optional-manually-prepare-a-device"></a>Valfritt Förbereda en enhet manuellt
Följande manuella steg för att installera och konfigurera enheten motsvarar de som automatiserades av det här [Cloud-Init-skriptet](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt). De kan användas för att förbereda en fysisk enhet.

1. Följ anvisningarna för att [installera Azure IoT Edge runtime](../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true).
   > [!NOTE]
   > Enhets uppdaterings paketets agent är inte beroende av IoT Edge. Men det är beroende av IoT Identity service daemon som installeras med IoT Edge (1.2.0 och senare) för att hämta en identitet och ansluta till IoT Hub.
   >
   > Även om de inte beskrivs i den här självstudien [kan IoT Identity service daemon installeras fristående på Linux-baserade IoT-enheter](https://azure.github.io/iot-identity-service/packaging.html). Sekvensen av installations frågor. Enhets uppdaterings paketets agent måste installeras _efter_ IoT Identity service. Annars kommer paket agenten inte att registreras som en auktoriserad komponent för att upprätta en anslutning till IoT Hub.

1. Installera sedan enhets uppdaterings agenten. deb-paket.

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

Enhets uppdatering för Azure IoT Hub program varu paket omfattas av följande licens villkor:
  * [Enhets uppdatering för IoT Hub licens](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [Klient licens för leverans optimering](https://github.com/microsoft/do-client/blob/main/LICENSE)

Läs licens villkoren innan du använder ett paket. Din installation och användning av ett paket utgör ditt godkännande av dessa villkor. Om du inte godkänner licens villkoren ska du inte använda det paketet.

## <a name="add-a-tag-to-your-device"></a>Lägg till en tagg till din enhet

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till IoT Hub.

2. Från IoT Edge i det vänstra navigerings fönstret hittar du IoT Edge-enheten och navigerar till enheten med dubbla.

3. Ta bort eventuella befintliga enhets uppdaterings märken i enhets numret genom att ställa in dem på null.

4. Lägg till ett nytt märkes värde för enhets uppdatering som visas nedan.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>Importera uppdatering

1. Ladda ned följande [apt manifest fil](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json) och [Importera manifest filen](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json). Det här apt-manifestet kommer att installera den senaste tillgängliga versionen av `libcurl4-doc package` på enheten.

   Du kan också ladda ned [manifest filen för apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json) och [Importera manifest filen](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json). Detta kommer att installera en speciell version v-7.58.0 av på `libcurl4-doc package` enheten.

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

1. Välj alternativet enhets uppdateringar under automatisk enhets hantering från det vänstra navigerings fältet.

1. Välj fliken grupper överst på sidan.

1. Välj knappen Lägg till för att skapa en ny grupp.

1. Välj den IoT Hub-tagg som du skapade i föregående steg från listan. Välj Skapa uppdaterings grupp.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Skärm bild som visar val av tagg." lightbox="media/create-update-group/select-tag.PNG":::

[Lär dig mer](create-update-group.md) om att lägga till taggar och skapa uppdaterings grupper

## <a name="deploy-update"></a>Distribuera uppdatering

1. När gruppen har skapats bör du se en ny uppdatering tillgänglig för enhets gruppen, med en länk till uppdateringen i kolumnen _tillgängliga uppdateringar_ . Du kan behöva uppdatera en gång.

1. Klicka på länken till den tillgängliga uppdateringen.

1. Bekräfta att rätt grupp har valts som mål grupp och Schemalägg din distribution

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Välj uppdatera" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > Som standard är start datum/tid 24 timmar från din aktuella tid. Se till att välja ett annat datum/tid om du vill att distributionen ska starta tidigare.

1. Välj distribuera uppdatering.

1. Visa diagrammet efterlevnad. Nu bör du se att uppdateringen pågår. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Uppdatering pågår" lightbox="media/deploy-update/update-in-progress.png":::

1. När enheten har uppdaterats bör du se att ditt Compliance-diagram och distributions information uppdateras för att avspegla samma. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Uppdateringen är klar" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Övervaka en uppdaterings distribution

1. Välj fliken distributioner högst upp på sidan.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Fliken distributioner" lightbox="media/deploy-update/deployments-tab.png":::

1. Välj den distribution du skapade för att Visa distributions informationen.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Distributionsinformation" lightbox="media/deploy-update/deployment-details.png":::

1. Välj Uppdatera för att visa den senaste statusinformationen. Fortsätt med den här processen tills statusen har ändrats till slutfört.

Du har nu slutfört en lyckad paket uppdatering från slut punkt till slut punkt med enhets uppdatering för IoT Hub på en Ubuntu Server 18,04 x64-enhet. 

## <a name="bonus-steps"></a>Bonus steg

1. Ladda ned följande [apt manifest fil](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json) och [Importera manifest filen](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json). Detta apt-manifest tar bort installerade `libcurl4-doc package` från enheten.

1. Upprepa avsnitten "Importera uppdatering" och "distribuera uppdatering"

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver kan du rensa enhets uppdaterings kontot, instansen IoT Hub och IoT Edges enheten (om du har skapat den virtuella datorn via knappen distribuera till Azure). Du kan göra det genom att gå till varje enskild resurs och välja "ta bort". Observera att du måste rensa en enhets uppdaterings instans innan du rensar enhets uppdaterings kontot.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bild uppdatering på Raspberry Pi 3 B + självstudie](device-update-raspberry-pi.md)
