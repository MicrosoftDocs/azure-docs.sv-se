---
title: Självstudie – konfigurera Kanarie-distributioner för Azure Virtuella Linux-datorer
description: I den här självstudien får du lära dig hur du konfigurerar en pipeline för kontinuerlig distribution (CD). Den här pipelinen uppdaterar en grupp med virtuella Azure Linux-datorer med hjälp av strategin för Kanarie-distribution.
author: moala
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
ms.openlocfilehash: bbfe6571cf075b2ce4930eea91bfd1e239470c5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552515"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Självstudie – konfigurera strategin för Kanarie-distribution för Azure Virtuella Linux-datorer

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>IaaS (Infrastructure as a Service) – Konfigurera CI/CD

Azure-pipeliner ger en helt aktuell uppsättning CI/CD Automation-verktyg för distribution till virtuella datorer. Du kan konfigurera en pipeline för kontinuerlig leverans för en virtuell Azure-dator från Azure Portal.

Den här artikeln visar hur du konfigurerar en CI/CD-pipeline som använder Kanarie strategin för MultiMachine-distributioner. Azure Portal stöder också andra strategier som [rullande](./tutorial-devops-azure-pipelines-classic.md) och [blå – grönt](./tutorial-azure-devops-blue-green-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>Konfigurera CI/CD på virtuella datorer

Du kan lägga till virtuella datorer som mål i en [distributions grupp](/azure/devops/pipelines/release/deployment-groups). Du kan sedan rikta dem till MultiMachine-uppdateringar. När du har distribuerat till datorer, Visa **distributions historik** i en distributions grupp. I den här vyn kan du spåra från virtuell dator till pipelinen och sedan till commit.

### <a name="canary-deployments"></a>Kanarie-distributioner

En Kanarie-distribution minskar riskerna genom att sakta ner ändringar av en liten del av användarna. När du får förtroende för den nya versionen kan du släppa den på fler servrar i infrastrukturen och dirigera fler användare till den.

Med alternativet för kontinuerlig leverans kan du konfigurera Kanarie-distributioner till dina virtuella datorer från Azure Portal. Här är stegvisa steg-för-steg-anvisningar:

1. Logga in på Azure Portal och navigera till en virtuell dator.
1. I rutan längst till vänster i VM-inställningarna väljer du **kontinuerlig leverans**. Välj sedan **Konfigurera**.

   ![Fönstret kontinuerlig leverans med knappen Konfigurera](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. I konfigurations panelen väljer du **Azure DevOps-organisation** för att välja ett befintligt konto eller skapa ett nytt. Välj sedan det projekt som du vill konfigurera pipelinen för.  

   ![Panelen kontinuerlig leverans](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. En distributions grupp är en logisk uppsättning distributions mål datorer som representerar de fysiska miljöerna. Utveckling, testning, UAT och produktion är exempel. Du kan skapa en ny distributions grupp eller välja en befintlig.
1. Välj den build-pipeline som publicerar paketet som ska distribueras till den virtuella datorn. Det publicerade paketet måste ha ett distributions skript med namnet deploy.ps1 eller deploy.sh i mappen deployscripts i paketets rotmapp. Pipelinen kör det här distributions skriptet.
1. I **distributions strategi** väljer du **Kanarie**.
1. Lägg till en "Kanarie"-tagg till virtuella datorer som ska ingå i Kanarie-distributioner. Lägg till en "Prod"-tagg till virtuella datorer som är en del av distributioner gjorda efter att Kanarie-distributionen lyckades. Med taggar kan du bara rikta in virtuella datorer som har en speciell roll.

   ![Panelen kontinuerlig leverans med det valda Kanarie-värdet för distributions strategin](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Välj **OK** om du vill konfigurera en pipeline för kontinuerlig leverans som ska distribueras till den virtuella datorn.

   ![Kanarie-pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. Distributions informationen för den virtuella datorn visas. Du kan välja länken för att gå till versions pipelinen i Azure DevOps. I versions pipeline väljer du **Redigera** för att Visa pipeline-konfigurationen. Pipelinen har följande tre faser:

   1. Den här fasen är en distributions grupps fas. Program distribueras till virtuella datorer som är märkta som "Kanarie".
   1. I den här fasen pausar och väntar på manuella åtgärder för att återuppta körningen.
   1. Detta är en distributions grupps fas. Uppdateringen distribueras nu till virtuella datorer som märkts som "Prod".

      ![Fönstret distributions grupp för aktiviteten distribuera Kanarie](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. Innan du återupptar pipeline-körningen bör du se till att minst en virtuell dator är märkt som "Prod". I den tredje fasen av pipelinen distribueras program endast till de virtuella datorer som har taggen "Prod".

1. Åtgärden kör distribuera skript kör som standard distributions skriptet deploy.ps1 eller deploy.sh. Skriptet finns i mappen deployscripts i rotmappen för det publicerade paketet. Se till att den valda Bygg pipelinen publicerar distributionen i paketets rotmapp.

   ![Fönster rutan artefakter som visar deploy.sh i mappen deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Andra distributions strategier
- [Konfigurera strategin för löpande distribution](./tutorial-devops-azure-pipelines-classic.md)
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