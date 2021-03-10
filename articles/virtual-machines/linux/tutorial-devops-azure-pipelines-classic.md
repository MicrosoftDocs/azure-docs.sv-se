---
title: Självstudie – konfigurera löpande distributioner för virtuella Azure Linux-datorer
description: I den här självstudien får du lära dig hur du konfigurerar en pipeline för kontinuerlig distribution (CD). Den här pipelinen uppdaterar en grupp med virtuella Azure Linux-datorer med hjälp av strategin för rullande distribution.
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: dd47250989be5c31d5f0ade2b602b9d6af535d83
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564007"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Självstudie – konfigurera strategin för rullande distribution för virtuella Azure Linux-datorer

Azure DevOps är en inbyggd Azure-tjänst som automatiserar varje del av DevOps-processen för alla Azure-resurser. Oavsett om din app använder virtuella datorer, webbappar, Kubernetes eller någon annan resurs, kan du implementera infrastruktur som kod (IaC), kontinuerlig integrering, kontinuerlig testning, kontinuerlig leverans och kontinuerlig övervakning med Azure och Azure DevOps.

![Azure Portal med Azure-DevOps valt under tjänster](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>IaaS (Infrastructure as a Service) – Konfigurera CI/CD

Azure-pipeliner ger en helt aktuell uppsättning CI/CD Automation-verktyg för distribution till virtuella datorer. Du kan konfigurera en pipeline för kontinuerlig leverans för en virtuell Azure-dator från Azure Portal.

Den här artikeln visar hur du konfigurerar en CI/CD-pipeline för rullande MultiMachine-distributioner från Azure Portal. Azure Portal stöder också andra strategier som [Kanarie](./tutorial-azure-devops-canary-strategy.md) och [blå – grönt](./tutorial-azure-devops-blue-green-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>Konfigurera CI/CD på virtuella datorer

Du kan lägga till virtuella datorer som mål i en [distributions grupp](/azure/devops/pipelines/release/deployment-groups). Du kan sedan rikta dem till MultiMachine-uppdateringar. När du har distribuerat till datorer, Visa **distributions historik** i en distributions grupp. I den här vyn kan du spåra från virtuell dator till pipelinen och sedan till commit.

### <a name="rolling-deployments"></a>Löpande distributioner

I varje iteration ersätter en rullande distribution instanser av ett programs tidigare version. Den ersätter dem med instanser av den nya versionen på en fast uppsättning datorer (rullande uppsättning). I följande genom gång visas hur du konfigurerar en rullande uppdatering på virtuella datorer.

Med alternativet för kontinuerlig leverans kan du konfigurera rullande uppdateringar av de virtuella datorerna i Azure Portal. Här är stegvisa steg-för-steg-anvisningar:

1. Logga in på Azure Portal och navigera till en virtuell dator.
1. I rutan längst till vänster i VM-inställningarna väljer du **kontinuerlig leverans**. Välj sedan **Konfigurera**.

   ![Fönstret kontinuerlig leverans med knappen Konfigurera](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. I konfigurations panelen väljer du **Azure DevOps-organisation** för att välja ett befintligt konto eller skapa ett nytt. Välj sedan det projekt som du vill konfigurera pipelinen för.  

   ![Panelen kontinuerlig leverans](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. En distributions grupp är en logisk uppsättning distributions mål datorer som representerar de fysiska miljöerna. Utveckling, testning, UAT och produktion är exempel. Du kan skapa en ny distributions grupp eller välja en befintlig.
1. Välj den build-pipeline som publicerar paketet som ska distribueras till den virtuella datorn. Det publicerade paketet måste ha ett distributions skript med namnet deploy.ps1 eller deploy.sh i mappen deployscripts i paketets rotmapp. Pipelinen kör det här distributions skriptet.
1. I **distributions strategi** väljer du **rullande**.
1. Du kan också tagga varje dator med dess roll. Taggarna "Web" och "DB" är exempel. Med de här taggarna kan du bara rikta in virtuella datorer som har en speciell roll.
1. Välj **OK** för att konfigurera pipeline för kontinuerlig leverans.
1. När konfigurationen är klar har du en pipeline för kontinuerlig leverans som har kon figurer ATS för distribution till den virtuella datorn.  

   ![Panelen för kontinuerlig leverans visar distributions historik](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. Distributions informationen för den virtuella datorn visas. Du kan välja länken för att gå till pipelinen, **Release-1** om du vill visa distributionen eller **Redigera** för att ändra definitionen för release-pipeline.

1. Om du konfigurerar flera virtuella datorer upprepar du steg 2 till och med 4 för andra virtuella datorer som ska läggas till i distributions gruppen. Om du väljer en distributions grupp som redan har en pipeline-körning, läggs de virtuella datorerna bara till i distributions gruppen. Inga nya pipeliner skapas.
1. När konfigurationen är färdig väljer du pipeline-definitionen, navigerar till Azure DevOps-organisationen och väljer **Redigera** för versions pipelinen.

   ![Redigera den rullande pipelinen](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. Välj **1 jobb, 1 aktivitet** i **dev** -fasen. Välj **distributions** fas.

   ![Löpande pipeline-uppgifter med uppgiften distribuera vald](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. I konfigurations fönstret längst till höger kan du ange antalet datorer som du vill distribuera parallellt i varje iteration. Om du vill distribuera till flera datorer i taget kan du ange antalet datorer i procent med hjälp av skjutreglaget.  

1. Med skript aktiviteten kör distribution som standard körs distributions skriptet deploy.ps1 eller deploy.sh. Skriptet finns i mappen deployscripts i rotmappen för det publicerade paketet.

   ![Fönster rutan artefakter som visar deploy.sh i mappen deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Andra distributions strategier

- [Konfigurera strategin för Kanarie-distribution](./tutorial-azure-devops-canary-strategy.md)
- [Konfigurera den blå gröna distributions strategin](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Du kan enkelt komma igång med Azure. Med Azure DevOps Projects börjar du köra ditt program på valfri Azure-tjänst i tre steg genom att välja:

- Ett program språk
- En körning
- En Azure-tjänst
 
[Läs mer](https://azure.microsoft.com/features/devops-projects/).
 
## <a name="additional-resources"></a>Ytterligare resurser

- [Distribuera till virtuella Azure-datorer med hjälp av Azure DevOps Projects](../../devops-project/azure-devops-project-vms.md)
- [Implementera en kontinuerlig distribution av din app till en skalnings uppsättning för en virtuell Azure-dator](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)