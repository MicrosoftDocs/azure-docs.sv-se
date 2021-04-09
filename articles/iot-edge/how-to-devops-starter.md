---
title: CI/CD-pipeline med Azure DevOps starter – Azure IoT Edge | Microsoft Docs
description: Med Azure DevOps Starter är det enkelt att komma igång med Azure. Det hjälper dig att starta en Azure IoT Edge app som du väljer i några enkla steg.
author: kgremban
ms.author: kgremban
ms.date: 08/25/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8a23041a714089c447ab35ee05aae0de0c9c33d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201161"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-starter"></a>Skapa en CI/CD-pipeline för IoT Edge med Azure DevOps starter

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Konfigurera kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för ditt IoT Edge program med DevOps Projects. DevOps starter fören klar den inledande konfigurationen av en pipeline för build och release i Azure pipelines.

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps starter skapar en CI/CD-pipeline i Azure DevOps. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps starter skapar också Azure-resurser i den Azure-prenumeration du väljer.

1. Logga in på [Microsoft Azure-portalen](https://portal.azure.com).

1. Välj **skapa en resurs** i den vänstra rutan och Sök sedan efter **DevOps starter**.  

1. Välj **Skapa**.

1. Som standard konfigureras Start programmet för DevOps med GitHub. Om du vill använda funktionerna i den här instruktionen kan du växla DevOps starter för att konfigurera med Azure DevOps. Följ länken **ändra inställningar här** .

   ![Välj Ändra inställningar här för att växla från GitHub till Azure DevOps](./media/how-to-devops-starter/create-with-github-change-settings.png)

1. I den högra rutan väljer du panelen **Azure-DevOps** och sedan **slutförd**.

   ![Välj Azure-DevOps för att konfigurera dina DevOps-startstarter](./media/how-to-devops-starter/select-azure-devops.png)

   Nu bör du se att DevOps starter konfigureras med Azure DevOps.

## <a name="create-a-new-application-pipeline"></a>Skapa en ny program pipeline

1. Azure IoT Edge modul (er) kan skrivas i [C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [python](tutorial-python-module.md), [C](tutorial-c-module.md) och [Java](tutorial-java-module.md). Välj önskat språk för att starta ett nytt program: **.net**, **Node.js**, **python**, **C** eller **Java**. Fortsätt genom att välja **Nästa**.

   ![Välj språk för att skapa ett nytt program](./media/how-to-devops-starter/select-language.png)

2. Välj **enkel IoT** som Application Framework och välj sedan **Nästa**.

   ![Välj enkelt IoT-ramverk](media/how-to-devops-starter/select-iot.png)

3. Välj **IoT Edge** som den Azure-tjänst som distribuerar ditt program och välj sedan **Nästa**.

   ![Välj IoT Edge tjänst](media/how-to-devops-starter/select-iot-edge.png)

4. Skapa en ny kostnadsfri Azure DevOps-organisation eller använd en befintlig organisation.

   1. Ange ett namn för projektet.

   2. Välj din Azure DevOps-organisation. Om du inte har en befintlig organisation väljer du **ytterligare inställningar** för att skapa en ny.

   3. Välj din Azure-prenumeration.

   4. Använd IoT Hub namn som skapats av ditt projekt namn eller ange ditt eget.

   5. Acceptera standard platsen eller Välj ett nära dig.

   6. Välj **ytterligare inställningar** för att konfigurera de Azure-resurser som DevOps starter skapar för din räkning.

   7. Slutför skapandet av projektet genom att välja **klart** .

   ![Namnge och skapa projekt](media/how-to-devops-starter/create-project.png)

Efter några minuter visas DevOps starter-instrumentpanelen i Azure Portal. Välj ditt projekt namn om du vill se förloppet. Du kan behöva uppdatera sidan. Ett exempel på ett IoT Edge program har kon figurer ATS i en lagrings plats i din Azure DevOps-organisation, en version körs och programmet distribueras till IoT Edges enheten. Den här instrumentpanelen ger insyn i kodlagringsplatsen, CI/CD-pipelinen och ditt program i Azure.

   ![Visa projekt i Azure Portal](./media/how-to-devops-starter/portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

DevOps starter skapade en git-lagringsplats för ditt projekt i Azure databaser. I det här avsnittet visar du lagrings platsen och gör kod ändringar i programmet.

1. Om du vill navigera till lagrings platsen som skapats för projektet väljer du **Arkiv** i menyn för instrument panelen för projektet. Den här länken öppnar en flik i webbläsaren och Azure DevOps-lagringsplatsen för det nya projektet.

   ![Visa lagrings plats som skapats i Azure databaser](./media/how-to-devops-starter/view-repositories.png)

   > [!NOTE]
   > Följande steg vägleder dig genom att använda webbläsaren för att göra kod ändringar. Om du vill klona lagrings platsen lokalt väljer du **klona** i det övre högra hörnet i fönstret. Använd den tillhandahållna URL: en för att klona git-lagringsplatsen i Visual Studio Code eller det föredragna utvecklingsverktyg.

2. Databasen innehåller redan kod för en modul med namnet **FilterModule** baserat på det programspråk som du valde i skapande processen. Öppna filen **modules/FilterModule/module.jsi** filen.

   ![Öppna module.jspå filen i Azure databaser](./media/how-to-devops-starter/open-module-json.png)

3. Observera att den här filen använder [Azure DevOps build-variabler](/azure/devops/pipelines/build/variables#build-variables) i **versions** parametern. Den här konfigurationen säkerställer att en ny version av modulen skapas varje gång en ny version körs.

## <a name="examine-the-cicd-pipeline"></a>Granska CI/CD-pipelinen

I föregående avsnitt konfigurerade Azure DevOps starter automatiskt en fullständigt CI/CD-pipeline för ditt IoT Edge-program. Nu ska du utforska och anpassa pipelinen efter behov. Använd följande steg för att bekanta dig med pipeline för Azure DevOps build och release.

1. Om du vill visa Bygg pipelines i DevOps-projektet väljer du **Bygg pipelines** i menyn för instrument panelen för projektet. Länken öppnar en webbläsarflik och Azure DevOps-bygg-pipelinen för det nya projektet.

   ![Visa Bygg pipelines i Azure-pipeline](./media/how-to-devops-starter/view-build-pipelines.png)

2. Öppna den automatiskt genererade pipeline för bygge och välj **Redigera** längst upp till höger.

    ![Redigera Bygg pipeline](media/how-to-devops-starter/click-edit-button.png)

3. I panelen som öppnas kan du undersöka de uppgifter som inträffar när din build-pipeline körs. Bygg pipelinen utför olika uppgifter, till exempel hämtning av källor från git-lagringsplatsen, skapa IoT Edge module-avbildningar, skicka IoT Edge moduler till ett behållar register och publicera utdata som används för distributioner. Mer information om Azure IoT Edge uppgifter i Azure DevOps finns i [Konfigurera Azure-pipelines för kontinuerlig integrering](how-to-continuous-integration-continuous-deployment-classic.md#create-a-build-pipeline-for-continuous-integration).

4. Välj **pipeline** -rubriken överst i skapa pipeline för att öppna pipeline-informationen. Ändra namnet på din bygg-pipeline till något mer beskrivande.

   ![Redigera pipeline-information](./media/how-to-devops-starter/edit-build-pipeline.png)

5. Välj **spara & kö** och välj sedan **Spara**. Det är valfritt att kommentera.

6. Välj **utlösare** från menyn Bygg pipeline. DevOps starter skapade automatiskt en CI-utlösare och varje incheckning till databasen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

7. Välj **Kvarhållning**. Följ länken för att omdirigera dig till projekt inställningarna där lagrings principerna finns. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

8. Välj **Historik**. Historik panelen innehåller en gransknings historik för de senaste ändringarna i versionen. Azure Pipelines spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

9. När du är klar med att utforska build-pipeline navigerar du till motsvarande versions pipeline. Välj **versioner** under **pipelines** och välj sedan **Redigera** för att visa information om pipelinen.

    ![Visa versions pipeline](media/how-to-devops-starter/release-pipeline.png)

10. Under **Artefakter** väljer du **Släpp**. Källan som den här artefakten bevakar är resultatet av den build-pipeline som du undersökte i föregående steg.

11. Bredvid **Drop** -ikonen väljer du **utlösaren för kontinuerlig distribution** som ser ut som en blixt. Den här versionen av pipelinen har aktiverat utlösaren, som kör en distribution varje gång det finns en ny versions artefakt tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning.  

12. I menyn för din versions pipeline väljer du **aktiviteter** och väljer sedan **dev** -fasen i list rutan. DevOps Projects skapade en versions fas för dig som skapar en IoT-hubb, skapar en IoT Edge-enhet i hubben, distribuerar exempel modulen från build-pipeline och etablerar en virtuell dator som ska köras som din IoT Edge enhet. Mer information om Azure IoT Edge uppgifter för CD finns i [Konfigurera Azure-pipelines för kontinuerlig distribution](how-to-continuous-integration-continuous-deployment-classic.md#create-a-release-pipeline-for-continuous-deployment).

    ![Visa kontinuerliga distributions uppgifter](media/how-to-devops-starter/choose-release.png)

13. Till höger väljer du alternativet för att **visa versioner**. Den här vyn visar en historik över versioner.

14. Välj namnet på en version om du vill visa mer information om det.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser som du skapade när de inte längre behövs. Använd **borttagnings** funktionen på DevOps starter-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om aktiviteterna för Azure IoT Edge på Azure DevOps i [kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* Förstå IoT Edge distribution i [förstå IoT Edge distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge moduler i stor skala](how-to-deploy-at-scale.md).
