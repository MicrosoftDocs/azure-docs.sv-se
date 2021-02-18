---
title: Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge enheter (klassisk redigerare) – Azure IoT Edge
description: Konfigurera kontinuerlig integrering och kontinuerlig distribution med hjälp av klassisk redigerare – Azure IoT Edge med Azure-DevOps, Azure-pipeline
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e38b3c617ded9c0001b01e481d4d3c1120be62ef
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634761"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge enheter (klassisk redigerare)

Du kan enkelt införa DevOps med dina Azure IoT Edge-program med de inbyggda Azure IoT Edge uppgifterna i Azure-pipelines. Den här artikeln visar hur du kan använda funktionerna för kontinuerlig integrering och kontinuerlig distribution i Azure pipelines för att bygga, testa och distribuera program snabbt och effektivt till din Azure IoT Edge med hjälp av den klassiska redigeraren. Du kan också [använda yaml](how-to-continuous-integration-continuous-deployment.md).

![Diagram – CI-och CD-grenar för utveckling och produktion](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

I den här artikeln får du lära dig hur du använder de inbyggda [Azure IoT Edge uppgifterna](/azure/devops/pipelines/tasks/build/azure-iot-edge) för Azure-pipelines för att skapa build-och release-pipeliner för din IoT Edge-lösning. Varje Azure IoT Edge aktivitet som läggs till i din pipeline implementerar någon av följande fyra åtgärder:

 | Åtgärd | Beskrivning |
 | --- | --- |
 | Bygga modul avbildningar | Tar din IoT Edge lösnings kod och skapar behållar avbildningarna.|
 | Avbildningar av push-modul | Push-överför modul avbildningar till det behållar register som du har angett. |
 | Generera distributions manifest | Tar en deployment.template.jspå filen och variablerna och genererar sedan den slutliga IoT Edge distributions manifest filen. |
 | Distribuera till IoT Edge-enheter | Skapar IoT Edge-distributioner till en eller flera IoT Edge enheter. |

Om inget annat anges utforskar procedurerna i den här artikeln inte alla funktioner som är tillgängliga via aktivitets parametrar. Mer information finns i följande:

* [Uppgifts version](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Avancerat** – om tillämpligt, anger du moduler som du inte vill ska skapas.
* [Kontroll alternativ](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Miljövariabler](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Variabler för utdata](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Förutsättningar

* En Azure databaser-lagringsplats. Om du inte har någon kan du [skapa en ny git-lagrings platsen i projektet](/azure/devops/repos/git/create-new-repo). I den här artikeln har vi skapat ett lagrings lager med namnet **IoTEdgeRepo**.
* En IoT Edge lösning har allokerats och skickas till din lagrings plats. Om du vill skapa en ny exempel lösning för att testa den här artikeln följer du stegen i [utveckla och felsöka moduler i Visual Studio Code](how-to-vs-code-develop-module.md) eller [utveckla och felsöka C#-moduler i Visual Studio](./how-to-visual-studio-develop-module.md). I den här artikeln har vi skapat en lösning i vårt lagrings lager som kallas **IoTEdgeSolution**, som har koden för en modul med namnet **filtermodule**.

   Allt du behöver i den här artikeln är mappen Solution som skapats av IoT Edge mallar i antingen Visual Studio Code eller Visual Studio. Du behöver inte bygga, skicka, distribuera eller Felsöka den här koden innan du fortsätter. Du konfigurerar dessa processer i Azure-pipeliner.

   Om du skapar en ny lösning ska du klona din lagrings plats lokalt först. När du sedan skapar lösningen kan du välja att skapa den direkt i databasmappen. Du kan enkelt bekräfta och skicka de nya filerna därifrån.

* Ett behållar register där du kan push-modul avbildningar. Du kan använda [Azure Container Registry](../container-registry/index.yml) eller ett register från en tredje part.
* En aktiv Azure [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) med minst två IoT Edge enheter för att testa de separata distributions faserna för test och produktion. Du kan följa snabb starts artiklarna för att skapa en IoT Edge-enhet i [Linux](quickstart-linux.md) eller [Windows](quickstart.md)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Skapa en pipeline för att skapa en kontinuerlig integrering

I det här avsnittet skapar du en ny versions pipeline. Du konfigurerar pipelinen så att den körs automatiskt när du checkar in eventuella ändringar i exempel IoT Edge lösning och publicera build-loggar.

1. Logga in på din Azure DevOps-organisation ( `https://dev.azure.com/{your organization}` ) och öppna projektet som innehåller lagrings platsen för din IoT Edge lösning.

    ![Öppna ditt DevOps-projekt](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. I den vänstra rutan i projektet väljer du **pipeliner**. Välj **skapa pipeline** i mitten av sidan. Eller, om du redan har skapat pipeliner, väljer du knappen **ny pipeline** överst till höger.

    ![Skapa en ny bygg-pipeline](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. Längst ned på sidan **var finns din kod?** väljer **du Använd den klassiska redigeraren**. Om du vill använda YAML för att skapa ett projekts pipeliner går du till [yaml-handboken](how-to-continuous-integration-continuous-deployment.md).

    ![Välj Använd klassiskt redigerings program](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Följ anvisningarna för att skapa din pipeline.

   1. Ange käll information för den nya bygg pipelinen. Välj **Azure databaser git** som källa och välj sedan projektet, databasen och grenen där din IoT Edge lösnings kod finns. Välj sedan **Fortsätt**.

      ![Välj din pipeline-källa](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Välj **tomt jobb** i stället för en mall.

      ![Börja med ett tomt jobb för din Bygg-pipeline](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. När din pipeline har skapats, kommer du till pipeline-redigeraren. Här kan du ändra pipeline: s namn, agent-pool och agent specifikation.

   Du kan välja en Microsoft-värdbaserad pool eller en egen värdbaserad pool som du hanterar.

   I din pipeline-Beskrivning väljer du rätt agent specifikation baserat på din mål plattform:

   * Om du vill bygga dina moduler i plattform amd64 för Linux-behållare väljer du **Ubuntu-16,04**

   * Om du vill skapa moduler i plattforms-amd64 för Windows 1809-behållare måste du konfigurera en lokal [agent i Windows](/azure/devops/pipelines/agents/v2-windows).

   * Om du vill skapa moduler i plattforms-arm32v7 eller arm64 för Linux-behållare måste du konfigurera en lokal [agent på Linux](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent).

    ![Konfigurera build agent-specifikation](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. Din pipeline är förkonfigurerad med ett jobb som kallas **Agent jobb 1**. Välj plus tecknet ( **+** ) för att lägga till fyra aktiviteter i jobbet: **Azure IoT Edge** två gånger, **Kopiera filer** en gång och **publicera Bygg artefakter** en gång. Sök efter varje aktivitet och hovra över uppgiftens namn för att se knappen **Lägg till** .

   ![Lägg till Azure IoT Edge uppgift](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   När alla fyra aktiviteter läggs till ser ditt Agent jobb ut som i följande exempel:

   ![Fyra uppgifter i build-pipeline](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Välj den första **Azure IoT Edge** uppgiften för att redigera den. Den här uppgiften skapar alla moduler i lösningen med den mål plattform som du anger. Redigera uppgiften med följande värden:

    | Parameter | Beskrivning |
    | --- | --- |
    | Visningsnamn | Visnings namnet uppdateras automatiskt när åtgärds fältet ändras. |
    | Action | Välj **build module-avbildningar**. |
    | .template.jspå fil | Välj ellipsen (**...**) och navigera till **deployment.template.jspå** filen i lagrings platsen som innehåller din IoT Edge-lösning. |
    | Standard plattform | Välj lämpligt operativ system för dina moduler baserat på din riktade IoT Edge-enhet. |
    | Variabler för utdata | Ange ett referens namn som ska associeras med den fil Sök väg som deployment.jspå filen genererar, till exempel **Edge**. |

   Dessa konfigurationer använder avbildnings lagrings platsen och taggen som definieras i `module.json` filen för att namnge och tagga avbildningen av modulen. Med hjälp av **module-avbildningar** kan du även ersätta variablerna med det exakta värdet som du definierar i `module.json` filen. I Visual Studio eller Visual Studio Code anger du det faktiska värdet i en `.env` fil. I Azure-pipeline ställer du in värdet på fliken **pipeline-variabler** . Välj fliken **variabler** på menyn för pipeline-redigeraren och konfigurera namnet och värdet enligt följande:

    * **ACR_ADDRESS**: ditt Azure Container Registry **inloggnings Server** värde. Du kan hämta inloggnings servern från sidan Översikt i behållar registret i Azure Portal.

    Om du har andra variabler i ditt projekt kan du ange namn och värde på den här fliken. **Bygg avbildningar** identifierar endast variabler i `${VARIABLE}` format. Se till att du använder det här formatet i dina `**/module.json` filer.

8. Välj den andra **Azure IoT Edge** uppgiften för att redigera den. Den här uppgiften push-överför alla modulblad till det behållar register som du väljer.

    | Parameter | Beskrivning |
    | --- | --- |
    | Visningsnamn | Visnings namnet uppdateras automatiskt när åtgärds fältet ändras. |
    | Action | Välj **push module-avbildningar**. |
    | Behållarens register typ | Använd standard typen: `Azure Container Registry` . |
    | Azure-prenumeration | Välj din prenumeration. |
    | Azure Container Registry | Välj den typ av behållar register som du använder för att lagra dina modulblad. Formuläret ändras beroende på vilken register typ du väljer. Om du väljer **Azure Container Registry** använder du List rutan för att välja Azure-prenumerationen och namnet på behållar registret. Om du väljer **allmän container Registry** väljer du **ny** för att skapa en anslutning till en register tjänst. |
    | .template.jspå fil | Välj ellipsen (**...**) och navigera till **deployment.template.jspå** filen i lagrings platsen som innehåller din IoT Edge-lösning. |
    | Standard plattform | Välj lämpligt operativ system för dina moduler baserat på din riktade IoT Edge-enhet. |
    | Lägg till autentiseringsuppgifter för registret i distributions manifestet | Ange True för att lägga till registrets autentiseringsuppgifter för att överföra Docker-avbildningar till distributions manifestet. |

   Om du har flera behållar register som ska vara värdar för dina modulblad, måste du duplicera den här uppgiften, välja ett annat behållar register och använda **bypass-modulen** i de **avancerade** inställningarna för att kringgå de avbildningar som inte är för det aktuella registret.

9. Välj aktiviteten **Kopiera filer** för att redigera den. Använd den här aktiviteten för att kopiera filer till en artefakt uppsamlings katalog.

    | Parameter | Beskrivning |
    | --- | --- |
    | Visningsnamn | Använd standard namnet eller anpassa |
    | Källmapp | Mappen med de filer som ska kopieras. |
    | Innehåll | Lägg till två rader: `deployment.template.json` och `**/module.json` . Dessa två filer fungerar som indata för att generera distributions manifestet för IoT Edge. |
    | Målmapp | Ange variabeln `$(Build.ArtifactStagingDirectory)` . Se [build-variabler](/azure/devops/pipelines/build/variables#build-variables) för att lära dig mer om beskrivningen. |

10. Välj aktiviteten **publicera Bygg artefakter** för att redigera den. Tillhandahåll katalog Sök vägen för artefakt mellanlagring till aktiviteten så att sökvägen kan publiceras i en versions pipeline.

    | Parameter | Beskrivning |
    | --- | --- |
    | Visningsnamn | Använd standard namnet eller anpassa. |
    | Sökväg för att publicera | Ange variabeln `$(Build.ArtifactStagingDirectory)` . Mer information finns i [bygga variabler](/azure/devops/pipelines/build/variables#build-variables) . |
    | Artefaktnamn | Använd standard namnet: **Drop** |
    | Plats för artefakt publicering | Använd standard platsen: **Azure-pipeline** |

11. Öppna fliken **utlösare** och markera kryss rutan för att **aktivera kontinuerlig integrering**. Se till att grenen som innehåller din kod ingår.

    ![Aktivera kontinuerlig integrations utlösare](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Välj **Spara** i list rutan **Spara & kö** .

Den här pipelinen har nu kon figurer ATS för att köras automatiskt när du push-överför ny kod till din lagrings platsen. Den senaste aktiviteten, publicering av pipeline-artefakter, utlöser en versions pipeline. Fortsätt till nästa avsnitt för att bygga lanserings pipelinen.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Om du vill använda **lager distributioner** i pipelinen stöds inte lager distributioner ännu i Azure IoT Edge uppgifter i Azure dataDevOpss.
>
>Du kan dock använda en [Azure CLI-uppgift i Azure DevOps](/azure/devops/pipelines/tasks/deploy/azure-cli) för att skapa distributionen som en lager distribution. För det **infogade skript** svärdet kan du använda [kommandot AZ IoT Edge Deployment Create](/cli/azure/ext/azure-iot/iot/edge/deployment):
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Nästa steg

* Exempel på IoT Edge DevOps metod tips i [Azure DevOps starter för IoT Edge](how-to-devops-starter.md)
* Förstå IoT Edge distribution i [förstå IoT Edge distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge moduler i stor skala](how-to-deploy-at-scale.md).