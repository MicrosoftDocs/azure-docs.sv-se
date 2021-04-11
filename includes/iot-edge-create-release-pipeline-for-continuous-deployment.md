---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 8009d98ddbfa778cf5f357248ecd943b810e06e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803422"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>Skapa en versions pipeline för kontinuerlig distribution

I det här avsnittet skapar du en versions pipeline som är konfigurerad för att köras automatiskt när din Bygg pipeline tappar ut artefakter och det visar distributions loggar i Azure-pipelines.

Skapa en ny pipeline och Lägg till ett nytt steg:

1. På fliken **utgåvor** under **pipelines** väljer du **+ ny pipeline**. Eller, om du redan har versions pipeliner, väljer du knappen **+ ny** och väljer **+ ny versions pipeline**.  

    ![Lägg till en versions pipeline med knappen + Ny pipeline](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. När du uppmanas att välja en mall väljer du att starta med ett **tomt jobb**.

    ![Börja med ett tomt jobb för din versions pipeline](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. Din nya versions pipeline initieras med en fas, som kallas **steg 1**. Byt namn på steg 1 till **dev** och behandla den som en pipeline för kontinuerlig distribution för din utvecklings miljö. Vanligt vis har kontinuerliga distributions pipeliner flera steg, inklusive **utveckling**, **mellanlagring** och **Prod**. Du kan använda olika namn och skapa mer baserat på din DevOps-praxis. Stäng fönstret steg information när det har bytt namn.

   Du kan också byta namn på din versions pipeline genom att välja texten "ny version pipelining" längst upp.

4. Länka versionen till de versions artefakter som publiceras av bygg pipelinen. Klicka på **Lägg till** i artefakter.

   ![Klicka på Lägg till i avsnittet artefakter i gränssnittet](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. På **sidan Lägg till en artefakt** väljer du **skapa** som **Källtyp**. Välj det projekt och den build-pipeline som du skapade. Om du vill kan du ändra **käll Ali Aset** till något mer beskrivande. Välj sedan **Lägg till**.

   ![På sidan Lägg till en artefakt väljer du Lägg till för att skapa artefakten](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. Öppna artefakt utlösare och välj växla för att aktivera den kontinuerliga distributions utlösaren. Nu skapas en ny version varje gång en ny version är tillgänglig.

   ![Öppna utlösaren för att aktivera kontinuerlig distribution genom att öppna artefakt utlösare och växla](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. **Dev** -fasen är förkonfigurerad med ett jobb och noll uppgifter. Från pipeline-menyn väljer du **uppgifter** och sedan **dev** -fasen. Välj **Agent jobb** och ändra dess **visnings namn** till **frågor och svar**. Du kan konfigurera information om Agent jobbet, men distributions aktiviteten är plattforms okänslig så att du kan använda vilken **agent specifikation** som helst i den valda **poolen**.

   ![Visa aktiviteterna för din dev-fas under fliken aktiviteter](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. I frågor och svar-jobbet väljer du plus tecknet ( **+** ) för att lägga till två aktiviteter. Sök efter och Lägg till **Azure IoT Edge** två gånger.

9. Välj den första **Azure IoT Edge** aktiviteten och konfigurera den med följande värden:

    | Parameter | Beskrivning |
    | --- | --- |
    | Visningsnamn | Visnings namnet uppdateras automatiskt när åtgärds fältet ändras. |
    | Action | Välj `Generate deployment manifest`. |
    | .template.jspå fil | Ange sökvägen: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` . Sökvägen publiceras från build-pipeline. |
    | Standard plattform | Välj lämpligt operativ system för dina moduler baserat på din riktade IoT Edge-enhet. |
    | Sökväg för utdata| Lägg till sökvägen `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Den här sökvägen är den slutliga IoT Edge distributions manifest filen. |

    De här konfigurationerna ersätter modulens bild-URL: er i `deployment.template.json` filen. Med **generera distributions manifestet** kan du också ersätta variablerna med det exakta värdet som du definierade i `deployment.template.json` filen. I VS/VS Code anger du det faktiska värdet i en `.env` fil. I Azure-pipeliner ställer du in värdet på fliken **släpp pipeline-variabler** . Flytta till fliken **variabler** och konfigurera namnet och värdet enligt följande:

    * **ACR_ADDRESS**: ditt Azure Container Registry **inloggnings Server** värde. Du kan hämta inloggnings servern från sidan Översikt i behållar registret i Azure Portal.
    * **ACR_PASSWORD**: ditt Azure Container Registry lösen ord.
    * **ACR_USER**: ditt Azure Container Registry användar namn.

    Om du har andra variabler i projektet kan du ange namn och värde i den här fliken. **Generera distributions manifestet** kan bara identifiera variabler som är i `${VARIABLE}` smak. Se till att du använder den här smaken i dina `*.template.json` filer.
    
    ```json-interactive
    "registryCredentials": {
      "<ACR name>": { // Your Azure Container Registry **Registry name** value
        "username": "${ACR_USER}",
        "password": "${ACR_PASSWORD}",
        "address": "${ACR_ADDRESS}"
      }
    }
    ```
    
    ![Konfigurera variablerna för din versions pipeline på fliken variabler](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. Välj den andra **Azure IoT Edge** aktiviteten och konfigurera den med följande värden:

    | Parameter | Beskrivning |
    | --- | --- |
    | Visningsnamn | Visnings namnet uppdateras automatiskt när åtgärds fältet ändras. |
    | Action | Välj `Deploy to IoT Edge devices`. |
    | Distributions fil | Lägg till sökvägen `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Den här sökvägen är filen IoT Edge distributions manifest filen. |
    | Azure-prenumeration | Välj den prenumeration som innehåller din IoT Hub.|
    | IoT Hub namn | Välj din IoT-hubb.|
    | Välj en/flera enheter | Välj om du vill att versions pipelinen ska distribueras till en eller flera enheter. Om du distribuerar till en enda enhet anger du **IoT Edge enhets-ID**. Om du distribuerar till flera enheter anger du enhetens **mål villkor**. Mål villkoret är ett filter som matchar en uppsättning IoT Edge enheter i IoT Hub. Om du vill använda enhets koder som villkor måste du uppdatera motsvarande enhets koder med IoT Hub enhets-till-enhet. Uppdatera **IoT Edge distributions-ID** och **IoT Edge distributions prioritet** i de avancerade inställningarna. Mer information om hur du skapar en distribution för flera enheter finns i [förstå IoT Edge automatiska distributioner](../articles/iot-edge/module-deployment-monitoring.md). |
    | Enhets-ID eller mål villkor | Beroende på föregående val anger du ett enhets-ID eller [mål villkor](../articles/iot-edge/module-deployment-monitoring.md#target-condition) som ska distribueras till flera enheter. |
    | Avancerat | För IoT Edge distributions-ID anger du `$(System.TeamProject)-$(Release.EnvironmentName)` . Den här variabeln mappar projektet och versions namnet med ditt IoT Edge-distributions-ID. |
    

    Om din uppgift kräver att du använder en avbildning som finns i ett privat Docker-betrott register som inte är synligt för det offentliga molnet, kan du ange **SKIP_MODULE_IMAGE_VALIDATION** -miljövariabeln för `true` att hoppa över bild validering. 

    ![Lägg till Azure IoT Edge uppgifter i dev-fasen](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. Välj **Spara** för att spara ändringarna i den nya versions pipelinen. Gå tillbaka till vyn pipeline genom att välja **pipeline** -fliken på menyn.
