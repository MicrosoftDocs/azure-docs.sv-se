---
title: Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge enheter – Azure IoT Edge
description: Konfigurera kontinuerlig integrering och kontinuerlig distribution med YAML-Azure IoT Edge med Azure DevOps, Azure-pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9db9997af20fee226214eb12ad32729cab55caa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199243"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge enheter

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Du kan enkelt införa DevOps med dina Azure IoT Edge-program med de inbyggda Azure IoT Edge uppgifterna i Azure-pipelines. Den här artikeln visar hur du kan använda funktionerna för kontinuerlig integrering och kontinuerlig distribution i Azure pipelines för att bygga, testa och distribuera program snabbt och effektivt till din Azure IoT Edge med YAML. Alternativt kan du [använda den klassiska redigeraren](how-to-continuous-integration-continuous-deployment-classic.md).

![Diagram – CI-och CD-grenar för utveckling och produktion](./media/how-to-continuous-integration-continuous-deployment/model.png)

I den här artikeln får du lära dig hur du använder de inbyggda [Azure IoT Edge uppgifterna](/azure/devops/pipelines/tasks/build/azure-iot-edge) för Azure-pipelines för att skapa build-och release-pipeliner för din IoT Edge-lösning. Varje Azure IoT Edge aktivitet som läggs till i din pipeline implementerar någon av följande fyra åtgärder:

 | Åtgärd | Beskrivning |
 | --- | --- |
 | Bygga modul avbildningar | Tar din IoT Edge lösnings kod och skapar behållar avbildningarna.|
 | Avbildningar av push-modul | Push-överför modul avbildningar till det behållar register som du har angett. |
 | Generera distributions manifest | Tar en deployment.template.jspå filen och variablerna och genererar sedan den slutliga IoT Edge distributions manifest filen. |
 | Distribuera till IoT Edge-enheter | Skapar IoT Edge-distributioner till en eller flera IoT Edge enheter. |

Om inget annat anges utforskar procedurerna i den här artikeln inte alla funktioner som är tillgängliga via aktivitets parametrar. Mer information finns i följande:

* [Uppgifts version](/azure/devops/pipelines/process/tasks?tabs=yaml#task-versions)
* **Avancerat** – om tillämpligt, anger du moduler som du inte vill ska skapas.
* [Kontroll alternativ](/azure/devops/pipelines/process/tasks?tabs=yaml#task-control-options)
* [Miljövariabler](/azure/devops/pipelines/process/variables?tabs=yaml#environment-variables)
* [Variabler för utdata](/azure/devops/pipelines/process/variables?tabs=yaml#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Förutsättningar

* En Azure databaser-lagringsplats. Om du inte har någon kan du [skapa en ny git-lagrings platsen i projektet](/azure/devops/repos/git/create-new-repo). I den här artikeln har vi skapat ett lagrings lager med namnet **IoTEdgeRepo**.
* En IoT Edge lösning har allokerats och skickas till din lagrings plats. Om du vill skapa en ny exempel lösning för att testa den här artikeln följer du stegen i [utveckla och felsöka moduler i Visual Studio Code](how-to-vs-code-develop-module.md) eller [utveckla och felsöka C#-moduler i Visual Studio](./how-to-visual-studio-develop-module.md). I den här artikeln har vi skapat en lösning i vårt lagrings lager som kallas **IoTEdgeSolution**, som har koden för en modul med namnet **filtermodule**.

   Allt du behöver i den här artikeln är mappen Solution som skapats av IoT Edge mallar i antingen Visual Studio Code eller Visual Studio. Du behöver inte bygga, skicka, distribuera eller Felsöka den här koden innan du fortsätter. Du konfigurerar dessa processer i Azure-pipeliner.

   Om du skapar en ny lösning ska du klona din lagrings plats lokalt först. När du sedan skapar lösningen kan du välja att skapa den direkt i databasmappen. Du kan enkelt bekräfta och skicka de nya filerna därifrån.

* Ett behållar register där du kan push-modul avbildningar. Du kan använda [Azure Container Registry](../container-registry/index.yml) eller ett register från en tredje part.
* En aktiv Azure [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) med minst två IoT Edge enheter för att testa de separata distributions faserna för test och produktion. Du kan följa snabb starts artiklarna för att skapa en IoT Edge-enhet i [Linux](quickstart-linux.md) eller [Windows](quickstart.md)

Mer information om hur du använder Azure-databaser finns i [dela din kod med Visual Studio och Azure databaser](/azure/devops/repos/git/share-your-code-in-git-vs)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Skapa en pipeline för att skapa en kontinuerlig integrering

I det här avsnittet skapar du en ny versions pipeline. Du konfigurerar pipelinen så att den körs automatiskt när du checkar in eventuella ändringar i exempel IoT Edge lösning och publicera build-loggar.

1. Logga in på din Azure DevOps-organisation ( `https://dev.azure.com/{your organization}` ) och öppna projektet som innehåller lagrings platsen för din IoT Edge lösning.

   ![Öppna ditt DevOps-projekt](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. I den vänstra rutan i projektet väljer du **pipeliner**. Välj **skapa pipeline** i mitten av sidan. Eller, om du redan har skapat pipeliner, väljer du knappen **ny pipeline** överst till höger.

    ![Skapa en ny versions pipeline med knappen Ny pipeline](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. På sidan **var finns din kod?** väljer du **Azure databaser git `YAML`**. Om du vill använda den klassiska redigeraren för att skapa ett projekts versions pipeline, se den [klassiska redigerings guiden](how-to-continuous-integration-continuous-deployment-classic.md).

4. Välj den lagrings plats som du skapar en pipeline för.

    ![Välj lagrings plats för din build-pipeline](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. På sidan **Konfigurera din pipeline väljer du** Start- **pipeline**. Om du har en befintlig YAML-fil för Azure pipelines som du vill använda för att skapa den här pipelinen kan du välja **befintliga yaml-filer för Azure pipelines** och ange grenen och sökvägen i lagrings platsen för filen.

    ![Välj Start pipelinen eller befintliga YAML-filer i Azure pipelines för att börja bygga pipelinen](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. På sidan **Granska din pipeline-yaml** kan du klicka på standard namnet om du `azure-pipelines.yml` vill byta namn på din pipelines konfigurations fil.

   Välj **Visa assistent** för att öppna paletten **aktiviteter** .

    ![Välj Visa assistent för att öppna paletten aktiviteter](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. Om du vill lägga till en aktivitet placerar du markören i slutet av YAML eller var du vill att instruktionerna för aktiviteten ska läggas till. Sök efter och välj **Azure IoT Edge**. Fyll i aktivitetens parametrar enligt följande. Välj sedan **Lägg till**.

   | Parameter | Beskrivning |
   | --- | --- |
   | Åtgärd | Välj **build module-avbildningar**. |
   | .template.jspå fil | Ange sökvägen till **deployment.template.jspå** filen i lagrings platsen som innehåller din IoT Edge-lösning. |
   | Standard plattform | Välj lämpligt operativ system för dina moduler baserat på din riktade IoT Edge-enhet. |

    ![Använd paletten uppgifter för att lägga till uppgifter i din pipeline](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > När varje uppgift har lagts till markeras automatiskt de tillagda raderna i redigeraren. Om du vill förhindra oavsiktlig överskrivning avmarkerar du raderna och anger ett nytt utrymme för nästa uppgift innan du lägger till ytterligare aktiviteter.

8. Upprepa processen för att lägga till tre aktiviteter med följande parametrar:

   * Uppgift: **Azure IoT Edge**

       | Parameter | Beskrivning |
       | --- | --- |
       | Åtgärd | Välj **push module-avbildningar**. |
       | Behållarens register typ | Använd standard typen: **Azure Container Registry**. |
       | Azure-prenumeration | Välj din prenumeration. |
       | Azure Container Registry | Välj det register som du vill använda för pipelinen. |
       | .template.jspå fil | Ange sökvägen till **deployment.template.jspå** filen i lagrings platsen som innehåller din IoT Edge-lösning. |
       | Standard plattform | Välj lämpligt operativ system för dina moduler baserat på din riktade IoT Edge-enhet. |

   * Uppgift: **Kopiera filer**

       | Parameter | Beskrivning |
       | --- | --- |
       | Källmapp | Källmappen att kopiera från. Tom är roten i lagrings platsen. Använd variabler om filerna inte finns i lagrings platsen. Exempel: `$(agent.builddirectory)`.
       | Innehåll | Lägg till två rader: `deployment.template.json` och `**/module.json` . |
       | Målmapp | Ange variabeln `$(Build.ArtifactStagingDirectory)` . Se [build-variabler](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) för att lära dig mer om beskrivningen. |

   * Uppgift: **publicera Bygg artefakter**

       | Parameter | Beskrivning |
       | --- | --- |
       | Sökväg för att publicera | Ange variabeln `$(Build.ArtifactStagingDirectory)` . Se [build-variabler](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) för att lära dig mer om beskrivningen. |
       | Artefaktnamn | Ange standard namnet: `drop` |
       | Plats för artefakt publicering | Använd standard platsen: `Azure Pipelines` |

9. Välj **Spara** i list rutan **Spara och kör** i det övre högra hörnet.

10. Utlösaren för kontinuerlig integrering är aktive rad som standard för din YAML-pipeline. Om du vill redigera inställningarna väljer du din pipeline och klickar på **Redigera** i det övre högra hörnet. Välj **fler åtgärder** bredvid knappen **Kör** längst upp till höger och gå till **utlösare**. **Kontinuerlig integrering** visas som aktive rad i din pipelines namn. Om du vill se information om utlösaren markerar du kryss rutan **Åsidosätt den kontinuerliga integreringen med yaml** .

    ![Information om hur du granskar inställningarna för pipelinen finns i utlösare under fler åtgärder](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

Fortsätt till nästa avsnitt för att bygga lanserings pipelinen.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Nästa steg

* Exempel på IoT Edge DevOps metod tips i [Azure DevOps starter för IoT Edge](how-to-devops-starter.md)
* Förstå IoT Edge distribution i [förstå IoT Edge distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge moduler i stor skala](how-to-deploy-at-scale.md).