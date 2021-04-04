---
title: Konfigurera ett labb för att lära dig mer om stor data analys med Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb för att lära sig big data Analytics med Docker-distribution av Hortonworks Data Platform (HDP).
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5eb9cd00350c41645d4427e30a6f25a6c163358c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659904"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Konfigurera ett labb för Big data Analytics med Docker-distribution av HortonWorks Data Platform

Den här artikeln visar hur du konfigurerar ett labb för att lära en stor data analys klass.  Med den här typen av klass kan studenter lära sig att hantera stora mängder data och använda algoritmer för maskin-och statistiska inlärning för att härleda data insikter.  Ett viktigt mål för studenter är att lära sig att använda verktyg för data analys som [Apache Hadoop programmets program varu paket med öppen källkod](https://hadoop.apache.org/) som innehåller verktyg för att lagra, hantera och bearbeta Big data.

I det här labbet använder studenter en populär kommersiell version av Hadoop från [Cloudera](https://www.cloudera.com/), som kallas [Hortonworks Data Platform (HDP)](https://www.cloudera.com/products/hdp.html).  Mer specifikt använder studenter [HDP sandbox-3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) som är en förenklad, lättanvänd version av plattformen som är kostnads fri och avsedd för utbildning och experimentering.  Även om den här klassen kan använda antingen virtuella Windows-eller Linux-datorer (VM) med HDP sandbox, kommer den här artikeln att visa hur du använder Windows.

En annan intressant aspekt av det här labbet är att vi kommer att distribuera HDP sandbox på virtuella labb datorer med [Docker](https://www.docker.com/) -behållare.  Varje Docker-behållare tillhandahåller sin egen isolerade miljö för att program vara ska kunna köras inuti.  Docker är Docker-behållare som kapslade virtuella datorer och kan användas för att enkelt distribuera och köra en mängd olika program varu program baserat på behållar avbildningar som finns på [Docker Hub](https://www.docker.com/products/docker-hub).  Cloudera för HDP-sandbox hämtar automatiskt [HDP sandbox 3.0.1 Docker-avbildningen](https://hub.docker.com/r/hortonworks/sandbox-hdp) från Docker Hub och kör två Docker-behållare:
  - Sandbox – HDP
  - Sandbox-proxy

## <a name="lab-configuration"></a>Labb konfiguration

För att kunna konfigurera det här labbet behöver du ett Azure-prenumerations-och labb konto för att komma igång. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har skaffat en Azure-prenumeration kan du skapa ett nytt labb konto i Azure Lab Services. Mer information om hur du skapar ett nytt labb konto finns i [självstudier för att konfigurera ett labb konto](tutorial-setup-lab-account.md).  Du kan också använda ett befintligt labb konto.

### <a name="lab-account-settings"></a>Labb konto inställningar

Aktivera inställningarna som beskrivs i tabellen nedan för labb kontot. Mer information om hur du aktiverar Marketplace-avbildningar finns i [Ange Marketplace-avbildningar som är tillgängliga för labb skapare](./specify-marketplace-images.md).

| Inställning för labb konto | Instruktioner |
| ------------------- | ------------ |
|Marketplace-avbildning| Aktivera Windows 10 Pro-avbildningen för användning i ditt labb konto.|

### <a name="lab-settings"></a>Labb inställningar

Använd inställningarna i tabellen nedan när du konfigurerar ett klass rums labb.  Mer information om hur du skapar ett klass rums labb finns i [Konfigurera en labb Guide för klass rummet](tutorial-setup-classroom-lab.md).

| Labb inställningar | Värde/anvisningar |
| ------------ | ------------------ |
|Storlek på virtuell dator| Medium (kapslad virtualisering). Den här virtuella dator storleken passar bäst för Relations databaser, minnes intern cachelagring och analys.  Den här storleken stöder även kapslad virtualisering.|  
|Avbildning av virtuell dator| Windows 10 Pro|

> [!NOTE] 
> Vi måste använda medium (kapslad virtualisering) sedan distribution av HDP sandbox med Docker kräver:
>   - Windows Hyper-V med kapslad virtualisering
>   - Minst 10 GB RAM-minne

## <a name="template-machine-configuration"></a>Mall dator konfiguration

För att ställa in mallen Machine kommer vi att:
- Installera Docker
- Distribuera HDP sandbox
- Använd PowerShell och Windows Schemaläggaren för att automatiskt starta Docker-behållare

### <a name="install-docker"></a>Installera Docker

Stegen i det här avsnittet baseras på [Cloudera-instruktioner för distribution med Docker-behållare](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Om du vill använda Docker-behållare måste du först installera Docker Desktop på den virtuella mallen:

1. Följ stegen i [avsnittet krav](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) för att installera [Docker för Windows](https://docs.docker.com/docker-for-windows/install/). 

    > [!IMPORTANT] 
    > Se till att **Använd Windows-behållare i stället för** konfigurations alternativet för Linux-behållare lämnas omarkerat.

1. Se till att **Windows-behållare och Hyper-V-funktioner** är aktiverade.
   ![Aktivera eller inaktivera Windows-funktioner](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Följ stegen i avsnittet [minne för Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) för att konfigurera Docker-minneskortets minnes konfiguration.

    > [!WARNING]
    > Om du oavsiktligt markerar alternativet **Använd Windows-behållare i stället för Linux-behållare** när du installerar Docker visas inte inställningarna för minnes konfigurationen.  Du kan åtgärda detta genom att växla till att använda Linux-behållare genom [att klicka på Docker-ikonen i Windows system tray](https://docs.docker.com/docker-for-windows/#docker-settings-dialog); När menyn Docker-skrivbordet öppnas väljer **du växla till Linux-behållare**.
 
### <a name="deploy-hdp-sandbox"></a>Distribuera HDP sandbox

I det här avsnittet ska du distribuera HDP Sandbox och sedan komma åt HDP sandbox med hjälp av webbläsaren.

1. Kontrol lera att du har installerat [git-bash](https://gitforwindows.org/) enligt listan i [avsnittet krav](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) i guiden eftersom detta rekommenderas för att slutföra nästa steg.

1. Följ anvisningarna i följande avsnitt för att använda [Cloudera distributions-och installations guide för Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html):
   
   -    Distribuera HDP sandbox
   -    Verifiera HDP sandbox

    > [!WARNING] 
    > När du laddar ned den senaste. zip-filen för HDP bör du se till att du *inte* sparar. zip-filen i en katalog Sök väg som innehåller blank steg.

    > [!NOTE] 
    > Om du får ett undantag under distributionen som anger att **enheten inte har delats**, måste du dela din C-enhet med Docker så att HDPs Linux-behållare kan komma åt lokala Windows-filer.  Åtgärda detta genom att [Klicka på Docker-ikonen i Windows system tray](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) för att öppna Docker Desktop-menyn och välja **Inställningar**.  När dialog rutan **Docker-inställningar** öppnas väljer du **resurser > fildelning** och kontrollerar **C** -enheten.  Du kan sedan upprepa stegen för att distribuera HDP sandbox.

1. När Docker-behållare för HDP-sandbox har distribuerats och körs kan du komma åt miljön genom att starta webbläsaren och följa Cloudera instruktioner för att öppna [Välkomst sidan för sandbox](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) och starta HDP-instrumentpanelen.

    > [!NOTE] 
    > Dessa instruktioner förutsätter att du först har mappat den lokala IP-adressen för sandbox-miljön till sandbox-hdp.hortonworks.com i värd filen på den virtuella mallen.  Om du **inte** gör den här mappningen kan du komma åt Välkomst sidan för sandbox genom att gå till `http://localhost:8080` .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Starta Docker-behållare automatiskt när eleverna loggar in

Vi använder ett PowerShell-skript som automatiskt för att tillhandahålla en lättanvänd upplevelse för studenter:
  - Startar HDP sand Box Docker-behållare när en student startar och ansluter till sin virtuella labb dator.
  - Startar webbläsaren och navigerar till Välkomst sidan för sandbox.
Vi använder också Schemaläggaren i Windows för att köra skriptet automatiskt när en student loggar in på den virtuella datorn.
Följ dessa steg för att konfigurera detta: [skript för Big data analys](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Kostnads uppskattning

Om du vill uppskatta kostnaden för det här labbet kan du använda följande exempel.

För en klass av 25 studenter med 20 timmar schemalagda klass tider och 10 timmars kvot för läxor eller tilldelningar, skulle priset för labbet vara:
  - 25 studenter * (20 + 10) timmar * 55 lab-enheter * 0,01 USD per timme = 412,50 USD

Mer detaljerad information om priser finns [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats

Den här artikeln gick dig igenom de steg som krävs för att skapa ett labb för en stor data analys klass som använder Hortonworks-dataplattformen som distribueras med Docker.  Installationen av den här klass typen kan användas för liknande data analys klasser.  Den här installationen kan också tillämpas på andra typer av klasser som använder Docker för distribution.

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in alla labb.

- [Skapa och hantera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users)