---
title: Uppdatering av enhets uppdateringar för Azure IoT Hub med hjälp av Raspberry Pi 3 B + Reference yocto-avbildning | Microsoft Docs
description: Kom igång med enhets uppdatering för Azure IoT Hub med hjälp av Raspberry Pi 3 B + Reference yocto avbildning.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ca689df97e7268a5c0f7c0479e6514b98ffda9f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443462"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-raspberry-pi-3-b-reference-image"></a>Enhets uppdatering för Azure IoT Hub själv studie kurs med Raspberry Pi 3 B + referens avbildning

Enhets uppdatering för IoT Hub stöder två typer av uppdateringar – bildbaserad och paket-baserad.

Avbildnings uppdateringar ger en högre säkerhets nivå i enhetens slut tillstånd. Det är vanligt vis enklare att replikera resultaten av en avbildnings uppdatering mellan en för produktions miljö och en produktions miljö, eftersom den inte innebär samma utmaningar som paket och deras beroenden. På grund av deras atomiska natur kan en enkel-/B-redundansrelation också användas.

Den här självstudien vägleder dig genom stegen för att slutföra en bildbaserad avbildnings uppdatering från slut punkt till slut punkt med hjälp av enhets uppdatering för IoT Hub. 

I den här självstudien får du lära dig hur man
> [!div class="checklist"]
> * Ladda ned bild
> * Lägg till en tagg till din IoT-enhet
> * Importera en uppdatering
> * Skapa en enhetsgrupp.
> * Distribuera en avbildnings uppdatering
> * Övervaka uppdaterings distributionen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
* Åtkomst till en IoT Hub. Vi rekommenderar att du använder en S1-nivå (standard) eller högre.

## <a name="download-image"></a>Ladda ned bild

Det finns tre avbildningar som är tillgängliga som en del av "till gångar" i en specifik [version av enhets uppdaterings GitHub](https://github.com/Azure/iot-hub-device-update/releases). Bas avbildningen (ADU-Base-image) och en uppdaterings avbildning (ADU-Update-image) tillhandahålls så att du kan prova att distribuera till olika versioner utan att behöva blinka med SD-kortet på enheten. För att göra det måste du ladda upp uppdaterings avbildningarna till enhets uppdateringen för IoT Hub-tjänsten som en del av importen.

## <a name="flash-sd-card-with-image"></a>Flash SD-kort med bild

Använd ditt favorit verktyg för blinkande operativ system och installera enhets uppdaterings bas avbildningen (ADU-Base-image) på SD-kortet som ska användas i Raspberry Pi 3 B +-enheten.

### <a name="using-bmaptool-to-flash-sd-card"></a>Använda bmaptool till Flash SD-kort

1. Om du inte redan har gjort det installerar du `bmaptool` verktyget.

   ```shell
   sudo apt-get install bmap-tools
   ```

2. Leta upp sökvägen för SD-kortet i `/dev` . Sökvägen bör se ut ungefär som `/dev/sd*` eller `/dev/mmcblk*` . Du kan använda `dmesg` verktyget för att hitta rätt sökväg.

3. Du måste demontera alla monterade partitioner innan du börjar blinka.

   ```shell
   sudo umount /dev/<device>
   ```

4. Kontrol lera att du har Skriv behörighet till enheten.

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. Valfritt. För snabbare blinkande, laddar du ned bimap-filen tillsammans med avbildnings filen och placerar den i samma katalog.

6. Blinkar SD-kortet.

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
Enhets uppdatering för Azure IoT Hub-programvara omfattas av följande licens villkor:
   * [Enhets uppdatering för IoT Hub licens](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Klient licens för leverans optimering](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Läs licens villkoren innan du använder agenten. Din installation och användning utgör ditt godkännande av dessa villkor. Om du inte accepterar licens villkoren ska du inte använda enhets uppdateringen för IoT Hub-agenten.

## <a name="create-device-in-iot-hub-and-get-connection-string"></a>Skapa enhet i IoT Hub och hämta anslutnings sträng

Enheten måste nu läggas till i Azure-IoT Hub.  Inifrån Azure IoT Hub genereras en anslutnings sträng för enheten.

1. Starta enhets uppdaterings IoT Hub från Azure Portal.
2. Skapa en ny enhet.
3. Till vänster på sidan navigerar du till "Explorer" > IoT-enheter > väljer "nytt".
4. Ange ett namn på enheten under enhets-ID: kontrol lera att kryss rutan AutoGenerate Keys är markerad.
5. Välj Spara.
6. Nu kommer du tillbaka till sidan "enheter" och enheten som du skapade ska finnas i listan. Välj den enheten.
7. I vyn enhet väljer du ikonen "Kopiera" bredvid "primär anslutnings sträng".
8. Klistra in de kopierade tecknen någonstans för senare användning i stegen nedan.
   **Den här kopierade strängen är enhets anslutnings strängen**.

## <a name="provision-connection-string-on-sd-card"></a>Etablera anslutnings sträng på SD-kort

1. Kontrol lera att Raspberry-Pi3 är ansluten till nätverket.
2. I PowerShell använder du kommandot nedan för att använda SSH i enheten
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. Ange login som rot och lösen ordet måste lämnas tomt.
5. När du har lyckats använda SSH i enheten kör du följande kommandon
 
Ersätt `<device connection string>` med anslutnings strängen
 ```markdown
    echo "connection_string=<device connection string>" > adu-conf.txt  
    echo "aduc_manufacturer=ADUTeam" >> adu-conf.txt
    echo "aduc_model=RefDevice" >> adu-conf.txt
   ```

## <a name="connect-the-device-in-device-update-iot-hub"></a>Anslut enheten i enhets uppdaterings IoT Hub

1. På vänster sida av sidan väljer du IoT-enheter under Explorer.
2. Välj länken med enhets namnet.
3. Välj enhets adressen högst upp på sidan.
4. Leta upp Linux-kernel-versionen under avsnittet rapporterat i enhetens dubbla egenskaper.
För en ny enhet, som inte har tagit emot en uppdatering från enhets uppdateringen, kommer värdet [DeviceManagement: DeviceInformation: 1. swVersion](device-update-plug-and-play.md) att representera den inbyggda program varan som körs på enheten.  När en uppdatering har tillämpats på en enhet använder enhets uppdateringen [AzureDeviceUpdateCore: ClientMetadata: 4. installedUpdateId](device-update-plug-and-play.md) egenskap svärdet som representerar den inbyggda program varans version som körs på enheten.
5. Bas-och uppdaterings avbildnings filerna har ett versions nummer i fil namnet.

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

Använd det versions numret i steget importera uppdatering nedan.

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
5. Välj mappikonen eller text rutan under "Välj en import manifest fil". Du kommer att se en dialog ruta för fil väljaren. Välj det import manifest som du skapade ovan.  Välj sedan mappikonen eller text rutan under "Välj en eller flera uppdateringsfiler". Du kommer att se en dialog ruta för fil väljaren. Välj den uppdaterings fil som du vill distribuera till dina IoT-enheter.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Skärm bild som visar val av uppdaterings fil." lightbox="media/import-update/select-update-files.png":::

5. Välj mappikonen eller text rutan under "Välj en lagrings behållare". Välj sedan lämpligt lagrings konto.

6. Om du redan har skapat en behållare kan du återanvända den. (Annars väljer du "+ behållare" för att skapa en ny lagrings behållare för uppdateringar.).  Välj den behållare som du vill använda och klicka på Välj.
  
  :::image type="content" source="media/import-update/container.png" alt-text="Skärm bild som visar val av behållare." lightbox="media/import-update/container.png":::

7. Välj "Skicka" för att starta import processen.

8. Import processen påbörjas och skärmen ändras till avsnittet "import historik". Välj Uppdatera om du vill visa förloppet tills importen är klar. Beroende på Uppdateringens storlek kan detta slutföras på några minuter, men det kan ta längre tid.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Skärm bild som visar import ordningen för uppdateringar." lightbox="media/import-update/update-publishing-sequence-2.png":::

9. När kolumnen status visar att importen har slutförts väljer du rubriken "klar att distribuera". Du bör se den importerade uppdateringen i listan nu.

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

Du har nu slutfört en lyckad avbildnings uppdatering från slut punkt till slut punkt med enhets uppdatering för IoT Hub på en Raspberry Pi 3 B +-enhet. 

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver kan du rensa enhets uppdaterings kontot, instansen IoT Hub och IoT-enheten. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Simulator referens agent](device-update-simulator.md)
