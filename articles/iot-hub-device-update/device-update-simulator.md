---
title: Uppdatering av enhets uppdateringar för Azure IoT Hub med hjälp av Ubuntu (18,04 x64) Simulator referens agent | Microsoft Docs
description: Kom igång med enhets uppdatering för Azure IoT Hub med hjälp av Ubuntu (18,04 x64) Simulator referens agent.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 4740bf02c33314dd7c887356f2ef1ed12bea44cf
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443819"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Uppdatering av enhets uppdateringar för Azure IoT Hub med hjälp av Ubuntu (18,04 x64) Simulator referens agent

Enhets uppdatering för IoT Hub stöder två typer av uppdateringar – bildbaserad och paket-baserad.

Avbildnings uppdateringar ger en högre säkerhets nivå i enhetens slut tillstånd. Det är vanligt vis enklare att replikera resultaten av en avbildnings uppdatering mellan en för produktions miljö och en produktions miljö, eftersom den inte innebär samma utmaningar som paket och deras beroenden. På grund av deras atomiska natur kan en enkel-/B-redundansrelation också användas.

Den här självstudien vägleder dig genom stegen för att slutföra en bildbaserad avbildnings uppdatering från slut punkt till slut punkt med hjälp av enhets uppdatering för IoT Hub. 

I den här självstudien får du lära dig hur man
> [!div class="checklist"]
> * Ladda ned och installera avbildning
> * Lägg till en tagg till din IoT-enhet
> * Importera en uppdatering
> * Skapa en enhetsgrupp.
> * Distribuera en avbildnings uppdatering
> * Övervaka uppdaterings distributionen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
* Åtkomst till en IoT Hub. Vi rekommenderar att du använder en S1-nivå (standard) eller högre.

### <a name="download-and-install"></a>Hämta och installera

* AZ-cmdlet: ar (Azure CLI) för PowerShell:
  * Öppna PowerShell > installera Azure CLI ("Y" för att uppmanas att installera från "obetrodd" källa)

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>Aktivera WSL på din Windows-enhet (Windows-undersystem för Linux)

1. Öppna PowerShell som administratör på datorn och kör följande kommando (du kan uppmanas att starta om efter varje steg. Starta om när du uppmanas till det):

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(*Du kan uppmanas att starta om efter det här steget*)

2. Gå till Microsoft Store på webben och installera [Ubuntu 18,04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`).

3. Starta "Ubuntu 18,04 LTS" och installera.

4. När du har installerat uppmanas du att ange rot namn (användar namn) och lösen ord. Se till att du använder ett lösen ord för lösen ords återminnesning.

5. I PowerShell kör du följande kommando för att ange Ubuntu som standard-Linux-distribution:

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. Visa en lista med alla Linux-distributioner, kontrol lera att Ubuntu är standard.

```powershell
PS> wsl --list
```

7. Du bör se: **Ubuntu-18,04 (standard)**

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>Hämta enhets uppdaterings Ubuntu (18,04 x64) Simulator referens agent

Uppdaterings avbildningen för Ubuntu kan laddas ned från avsnittet *till gångar* från versions anteckningar [här](https://github.com/Azure/iot-hub-device-update/releases).

Det finns två versioner av agenten. Om du använder ett bildbaserat scenario använder du AducIotAgentSim-Microsoft-swupdate och använder AducIotAgentSim-Microsoft-apt om du utnyttjar paketbaserade scenarier.

## <a name="install-device-update-agent-simulator"></a>Installera agent Simulator för enhets uppdatering

1. Starta Ubuntu WSL och ange följande kommando (Observera att extra utrymme och punkt i slutet).

  ```shell
  explorer.exe .
  ```

2. Kopiera AducIotAgentSim-Microsoft-swupdate (eller AducIotAgentSim-Microsoft-apt) från den lokala mappen där den hämtades under/mnt till din arbetsmapp i WSL.

3. Kör följande kommando för att göra binärfilerna körbara.

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  eller

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
Enhets uppdatering för Azure IoT Hub-programvara omfattas av följande licens villkor:
   * [Enhets uppdatering för IoT Hub licens](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Klient licens för leverans optimering](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Läs licens villkoren innan du använder agenten. Din installation och användning utgör ditt godkännande av dessa villkor. Om du inte accepterar licens villkoren ska du inte använda enhets uppdateringen för IoT Hub-agenten.

## <a name="add-device-to-azure-iot-hub"></a>Lägg till enhet i Azure IoT Hub

När enhets uppdaterings agenten körs på en IoT-enhet måste enheten läggas till i Azure-IoT Hub.  Inifrån Azure IoT Hub genereras en anslutnings sträng för en viss enhet.

1. Starta enhets uppdaterings IoT Hub från Azure Portal.
2. Skapa en ny enhet.
3. Till vänster på sidan navigerar du till "Explorer" > IoT-enheter > väljer "nytt".
4. Ange ett namn på enheten under enhets-ID: kontrol lera att kryss rutan AutoGenerate Keys är markerad.
5. Välj Spara.
6. Nu kommer du tillbaka till sidan "enheter" och enheten som du skapade ska finnas med i listan. Välj den enheten.
7. I vyn enhet väljer du ikonen "Kopiera" bredvid "primär anslutnings sträng".
8. Klistra in de kopierade tecknen någonstans för senare användning i stegen nedan. **Den här kopierade strängen är enhets anslutnings strängen**.

## <a name="add-connection-string-to-simulator"></a>Lägg till anslutnings sträng i simulatorn

Starta enhets uppdaterings agenten på dina nya program varu enheter.

1. Starta Ubuntu.
2. Kör enhets uppdaterings agenten och ange anslutnings strängen för enheten från föregående avsnitt omsluten med apostrofer:

Ersätt `<device connection string>` med anslutnings strängen
```shell
./AducIotAgentSim-microsoft-swupdate -c '<device connection string>'
```

eller

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. Bläddra uppåt och leta efter strängen som anger att enheten är i tillståndet "inaktiv". Tillståndet "Idle" betyder att enheten är redo för tjänst kommandon:

```markdown
Agent running. [main]
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

1. Skapa ett import manifest genom att följa dessa [anvisningar](import-update.md).
2. Välj alternativet enhets uppdateringar under automatisk enhets hantering från det vänstra navigerings fältet.

3. Välj fliken uppdateringar.

4. Välj "+ Importera ny uppdatering".

5. Välj mappikonen eller text rutan under "Välj en import manifest fil". Du kommer att se en dialog ruta för fil väljaren. Välj det import manifest som du skapade ovan.  Välj sedan mappikonen eller text rutan under "Välj en eller flera uppdateringsfiler". Du kommer att se en dialog ruta för fil väljaren. Välj den Ubuntu-uppdaterings avbildning som du laddade ned tidigare. 

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

Du har nu slutfört en lyckad avbildnings uppdatering från slut punkt till slut punkt med hjälp av enhets uppdatering för IoT Hub med hjälp av Ubuntu (18,04 x64) Simulator referens agent.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver kan du rensa enhets uppdaterings kontot, instansen IoT Hub och IoT-enheten. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Felsökning](troubleshoot-device-update.md)

