---
title: Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge enheter (klassisk redigerare) – Azure IoT Edge
description: Konfigurera kontinuerlig integrering och kontinuerlig distribution med den klassiska redigeraren – Azure IoT Edge med Azure DevOps, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f7c28ecbaa58731c528a9ecb5f869eba2bc0c99f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484429"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge (klassisk redigerare)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Du kan enkelt använda DevOps med Azure IoT Edge program med de inbyggda Azure IoT Edge i Azure Pipelines. Den här artikeln visar hur du kan använda funktionerna för kontinuerlig integrering och kontinuerlig distribution i Azure Pipelines för att skapa, testa och distribuera program snabbt och effektivt till din Azure IoT Edge med hjälp av den klassiska redigeraren. Du kan också använda [YAML](how-to-continuous-integration-continuous-deployment.md).

![Diagram – CI- och CD-grenar för utveckling och produktion](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

I den här artikeln får du lära dig hur du använder de inbyggda [Azure IoT Edge](/azure/devops/pipelines/tasks/build/azure-iot-edge) för Azure Pipelines för att skapa bygg- och lanseringspipelines för din IoT Edge lösning. Varje Azure IoT Edge som läggs till i din pipeline implementerar någon av följande fyra åtgärder:

 | Åtgärd | Beskrivning |
 | --- | --- |
 | Skapa modulavbildningar | Tar koden IoT Edge din lösning och skapar containeravbildningarna.|
 | Push-modulavbildningar | Push-modulens avbildningar till det containerregister som du angav. |
 | Generera distributionsmanifest | Tar en deployment.template.jsfilen och variablerna och genererar sedan den slutliga IoT Edge distributionsmanifestfilen. |
 | Distribuera till IoT Edge-enheter | Skapar IoT Edge distributioner till en eller flera IoT Edge enheter. |

Om inget annat anges utforskar inte procedurerna i den här artikeln alla funktioner som är tillgängliga via aktivitetsparametrar. Mer information finns i följande:

* [Uppgiftsversion](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Avancerat** – Ange moduler som du inte vill ska byggas om det är tillämpligt.
* [Kontrollalternativ](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Miljövariabler](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Utdatavariabler](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Förutsättningar

* En Azure Repos-lagringsplats. Om du inte har någon kan du skapa en [ny Git-lagringsplatsen i projektet](/azure/devops/repos/git/create-new-repo). I den här artikeln har vi skapat en lagringsplats med **namnet IoTEdgeRepo.**
* En IoT Edge-lösning har utförts och push-push-skickas till din lagringsplats. Om du vill skapa en ny exempellösning för att testa den här artikeln följer du stegen i Utveckla och felsöka moduler i [Visual Studio Code](how-to-vs-code-develop-module.md) eller Utveckla och felsöka [C#-moduler i Visual Studio](./how-to-visual-studio-develop-module.md). I den här artikeln har vi skapat en lösning i vår lagringsplats som heter **IoTEdgeSolution,** som innehåller koden för en modul med namnet **filtermodule**.

   I den här artikeln är allt du behöver lösningsmappen som skapats av IoT Edge mallar i antingen Visual Studio Code eller Visual Studio. Du behöver inte skapa, skicka, distribuera eller felsöka den här koden innan du fortsätter. Du kommer att konfigurera dessa processer i Azure Pipelines.

   Om du skapar en ny lösning klonar du lagringsplatsen lokalt först. När du sedan skapar lösningen kan du välja att skapa den direkt i lagringsplatsmappen. Du kan enkelt genomföra och push-överföra de nya filerna därifrån.

* Ett containerregister där du kan push-skicka modulavbildningar. Du kan [använda Azure Container Registry](../container-registry/index.yml) eller ett register från tredje part.
* En aktiv Azure [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) med minst två IoT Edge enheter för att testa de separata test- och produktionsdistributionsstegen. Du kan följa snabbstartsartiklarna för att skapa en IoT Edge på [Linux](quickstart-linux.md) eller [Windows](quickstart.md)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Skapa en bygg-pipeline för kontinuerlig integrering

I det här avsnittet skapar du en ny bygg-pipeline. Du konfigurerar pipelinen så att den körs automatiskt när du checkar in ändringar i IoT Edge och publicerar byggloggar.

1. Logga in på Din Azure DevOps-organisation ( `https://dev.azure.com/{your organization}` ) och öppna projektet som innehåller din IoT Edge-lösningslagringsplats.

    ![Öppna ditt DevOps-projekt](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. Välj Pipelines på menyn till vänster i **projektet.** Välj **Skapa pipeline** i mitten av sidan. Eller, om du redan har bygg-pipelines, väljer **du knappen Ny pipeline** längst upp till höger.

    ![Skapa en ny bygg-pipeline](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. Längst ned på sidan **Var finns din kod?** väljer du Använd **den klassiska redigeraren**. Om du vill använda YAML för att skapa projektets bygg-pipelines kan du gå till [YAML-guiden](how-to-continuous-integration-continuous-deployment.md).

    ![Välj Använd den klassiska redigeraren](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Följ anvisningarna för att skapa din pipeline.

   1. Ange källinformationen för din nya bygg-pipeline. Välj Azure Repos Git som källa och välj sedan projektet, **lagringsplatsen** och grenen där IoT Edge-lösningskoden finns. Välj sedan **Fortsätt.**

      ![Välj din pipelinekälla](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Välj **Tomt jobb i** stället för en mall.

      ![Börja med ett tomt jobb för bygg-pipelinen](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. När pipelinen har skapats kommer du till pipelineredigeraren. Här kan du ändra pipelinens namn, agentpool och agentspecifikation.

   Du kan välja en Microsoft-värdpool eller en pool med egen värd som du hanterar.

   I din pipelinebeskrivning väljer du rätt agentspecifikation baserat på målplattformen:

   * Om du vill skapa modulerna på plattformen amd64 för Linux-containrar väljer du **ubuntu-16.04**

   * Om du vill skapa modulerna på plattformen amd64 för Windows 1809-containrar måste du konfigurera en agent med egen [värd i Windows](/azure/devops/pipelines/agents/v2-windows).

   * Om du vill skapa moduler i plattformen arm32v7 eller arm64 för [Linux-containrar](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent)måste du konfigurera en agent med egen värd på Linux .

    ![Konfigurera specifikation för bygga agent](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. Din pipeline är förkonfigurerad med ett jobb som **heter Agentjobb 1.** Välj plustecknet ( ) för att lägga till fyra uppgifter i jobbet: Azure IoT Edge två gånger, Kopiera filer en gång **+** och Publicera **byggartefakter en** gång.   Sök efter varje uppgift och hovra över aktivitetens namn för att se **knappen Lägg** till.

   ![Lägga Azure IoT Edge uppgift](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   När alla fyra uppgifterna har lagts till ser agentjobbet ut som i följande exempel:

   ![Fyra uppgifter i bygg-pipelinen](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Välj den första **Azure IoT Edge** att redigera den. Den här uppgiften skapar alla moduler i lösningen med målplattformen som du anger. Redigera uppgiften med följande värden:

    | Parameter | Beskrivning |
    | --- | --- |
    | Visningsnamn | Visningsnamnet uppdateras automatiskt när fältet Åtgärd ändras. |
    | Action | Välj **Skapa modulavbildningar.** |
    | .template.jspå fil | Välj ellipsen (**...**) och gå **till filendeployment.template.jspå** den lagringsplats som innehåller din IoT Edge lösning. |
    | Standardplattform | Välj lämpligt operativsystem för dina moduler baserat på din IoT Edge enhet. |
    | Utdatavariabler | Ange ett referensnamn som ska associeras med den filsökväg deployment.jsfilen genererar, till exempel **edge**. |

   De här konfigurationerna använder avbildningsdatabasen och taggen som definieras i `module.json` filen för att namnge och tagga modulavbildningen. **Skapa modulavbildningar** hjälper också till att ersätta variablerna med det exakta värde som du definierar i `module.json` filen. I Visual Studio eller Visual Studio Code anger du det faktiska värdet i en `.env` fil. I Azure Pipelines anger du värdet på fliken **Pipelinevariabler.** Välj fliken **Variabler på** menyn för pipelineredigeraren och konfigurera namn och värde enligt följande:

    * **ACR_ADDRESS:** Värdet Azure Container Registry **inloggningsserver.** Du kan hämta inloggningsservern från sidan Översikt i containerregistret i Azure Portal.

    Om du har andra variabler i projektet kan du ange namn och värde på den här fliken. **Build module images** recognizes only variables that are in format (Skapa modulavbildningar identifierar endast variabler i `${VARIABLE}` formatet). Se till att du använder det här formatet i dina `**/module.json` filer.

8. Välj den andra **Azure IoT Edge** att redigera den. Den här uppgiften push-lar alla modulavbildningar till det containerregister som du väljer.

    | Parameter | Beskrivning |
    | --- | --- |
    | Visningsnamn | Visningsnamnet uppdateras automatiskt när fältet Åtgärd ändras. |
    | Action | Välj **Push-modulavbildningar.** |
    | Containerregistertyp | Använd standardtypen: `Azure Container Registry` . |
    | Azure-prenumeration | Välj din prenumeration. |
    | Azure Container Registry | Välj den typ av containerregister som du använder för att lagra modulavbildningarna. Beroende på vilken registertyp du väljer ändras formuläret. Om du **Azure Container Registry** väljer du Azure-prenumerationen och namnet på ditt containerregister i listrutan. Om du väljer **Allmänt Container Registry väljer** du Nytt för **att** skapa en anslutning till registertjänsten. |
    | .template.jspå fil | Välj ellipsen (**...**) och gå **till filendeployment.template.jspå** den lagringsplats som innehåller din IoT Edge lösning. |
    | Standardplattform | Välj lämpligt operativsystem för dina moduler baserat på din IoT Edge enhet. |
    | Lägga till autentiseringsuppgifter för registret i distributionsmanifestet | Ange sant för att lägga till autentiseringsuppgifter för att push-pusha Docker-avbildningar till distributionsmanifestet. |

   Om du har flera containerregister som värd för modulavbildningarna måste du duplicera den här  uppgiften, välja ett annat containerregister och använda Kringgå moduler i avancerade inställningar för att kringgå de avbildningar som inte är för det här specifika registret. 

9. Välj aktiviteten **Kopiera filer** för att redigera den. Använd den här uppgiften för att kopiera filer till mellanlagringskatalogen för artefakter.

    | Parameter | Beskrivning |
    | --- | --- |
    | Visningsnamn | Använd standardnamnet eller anpassa |
    | Källmapp | Mappen med de filer som ska kopieras. |
    | Innehåll | Lägg till två rader: `deployment.template.json` och `**/module.json` . Dessa två filer fungerar som indata för att generera IoT Edge distributionsmanifestet. |
    | Målmapp | Ange variabeln `$(Build.ArtifactStagingDirectory)` . Mer [information om beskrivningen](/azure/devops/pipelines/build/variables#build-variables) finns i Skapa variabler. |

10. Välj uppgiften **Publicera byggartefakter** för att redigera den. Ange sökvägen till artefaktens mellanlagringskatalog till aktiviteten så att sökvägen kan publiceras till lanseringspipelinen.

    | Parameter | Beskrivning |
    | --- | --- |
    | Visningsnamn | Använd standardnamnet eller anpassa. |
    | Sökväg för publicering | Ange variabeln `$(Build.ArtifactStagingDirectory)` . Mer [information finns i Skapa](/azure/devops/pipelines/build/variables#build-variables) variabler. |
    | Artefaktnamn | Använd standardnamnet: **drop** |
    | Publiceringsplats för artefakt | Använd standardplatsen: **Azure Pipelines** |

11. Öppna fliken **Utlösare** och markera kryssrutan aktivera **kontinuerlig integrering.** Kontrollera att grenen som innehåller din kod ingår.

    ![Aktivera utlösare för kontinuerlig integrering](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Välj **Spara** från **listrutan & i** kön.

Den här pipelinen är nu konfigurerad för att köras automatiskt när du push-erar ny kod till lagringsplatsen. Den sista uppgiften, som publicerar pipeline-artefakterna, utlöser en lanseringspipeline. Fortsätt till nästa avsnitt för att skapa lanseringspipelinen.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Om du vill använda **lagerdistributioner** i din pipeline stöds inte lagerdistributioner i flera lager än i Azure IoT Edge uppgifter i Azure DevOps.
>
>Du kan dock använda en [Azure CLI-uppgift i Azure DevOps](/azure/devops/pipelines/tasks/deploy/azure-cli) för att skapa distributionen som en skiktad distribution. För värdet **infogade skript** kan du använda kommandot [az iot edge deployment create:](/cli/azure/iot/edge/deployment)
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Nästa steg

* IoT Edge DevOps-metodtipsexempel [i Azure DevOps Starter för IoT Edge](how-to-devops-starter.md)
* Förstå IoT Edge distribution i [Förstå IoT Edge distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i Distribuera och övervaka IoT Edge [moduler i stor skala.](how-to-deploy-at-scale.md)