---
title: Aktiviteter för projektets lead i team data science-processen
description: En detaljerad genom gång av aktiviteterna för ett projekts ledare i en team data vetenskap process-grupp
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9d9b2546553e03d1555cf4c587d699d9a4ea7e51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000988"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Projekt ledar uppgifter i team data science process

I den här artikeln beskrivs aktiviteter som ett *projekt lead* har slutfört för att skapa en lagrings plats för deras projekt team i TDSP ( [team data science process](overview.md) ). TDSP är ett ramverk som utvecklats av Microsoft och som ger en strukturerad serie aktiviteter för att effektivt köra molnbaserade, förutsägelse analys lösningar. TDSP är utformad för att hjälpa till att förbättra samarbetet och team inlärningen. En översikt över personal roller och associerade uppgifter för ett data vetenskaps team som är standardiserade på TDSP finns i [process roller och uppgifter för team data vetenskap](roles-tasks.md).

Ett projekt ledare hanterar dagliga aktiviteter för enskilda data experter på ett visst data vetenskaps projekt i TDSP. Följande diagram visar arbets flödet för projekt ledar uppgifter:

![Arbets flöde för projekt ledar uppgift](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Den här självstudien beskriver steg 1: skapa projekt databas och steg 2: dirigera projekt lagrings plats från din team ProjectTemplate-lagringsplats. 

För steg 3: skapa funktions arbets objekt för projektet och steg 4: Lägg till berättelser för projekt faser, se [smidig utveckling av data vetenskaps projekt](agile-development.md).

Steg 5: skapa och anpassa lagrings-och analys resurser och dela, om det behövs, finns i [Skapa team data och analys resurser](team-lead-tasks.md#create-team-data-and-analytics-resources).

Steg 6: Konfigurera säkerhets kontroll för projekt lagrings plats, se [Lägg till team medlemmar och konfigurera behörigheter](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> I den här artikeln används Azure databaser för att skapa ett TDSP-projekt, eftersom det är hur du implementerar TDSP på Microsoft. Om ditt team använder en annan kod värd plattform är projektets lead-uppgifter samma, men det kan vara olika att slutföra dem.

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien förutsätter vi att [grupp chefen](group-manager-tasks.md) och [teamets lead](team-lead-tasks.md) har ställt in följande resurser och behörigheter:

- Azure DevOps- **organisationen** för din data enhet
- Ett team **projekt** för ditt data vetenskaps team
- Team mallar och hjälpmedels **databaser**
- **Behörigheter** för ditt organisations konto som du kan använda för att skapa och redigera databaser för ditt projekt

Om du vill klona lagrings utrymmen och ändra innehåll på din lokala dator eller Data Science Virtual Machine (DSVM), eller konfigurera Azure File Storage och montera det på din DSVM, måste du också tänka på den här check listan:

- En Azure-prenumeration.
- Git installerat på datorn. Om du använder en DSVM är git förinstallerat. I annat fall, se [bilagan plattform och verktyg](platforms-and-tools.md#appendix).
- Om du vill använda en DSVM skapas och konfigureras Windows-eller Linux-DSVM i Azure. Mer information och anvisningar finns i Data Science Virtual Machine- [dokumentationen](../data-science-virtual-machine/index.yml).
- För en Windows-DSVM är [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerad på datorn. Rulla ned till avsnittet **Ladda ned och installera** i *Readme.MD* -filen och välj det **senaste installations programmet**. Hämta *exe* -installationsprogrammet från installations sidan och kör det. 
- För en Linux-DSVM, konfigureras en offentlig SSH-nyckel på din DSVM och läggs till i Azure DevOps. Mer information och instruktioner finns i avsnittet **skapa offentlig SSH-nyckel** i [tillägget plattformar och verktyg](platforms-and-tools.md#appendix). 

## <a name="create-a-project-repository-in-your-team-project"></a>Skapa en projekt databas i ditt team projekt

Så här skapar du en projekt databas i teamets **team projekt:**

1. Gå till din grupps projekt **sammanfattnings** sida på *https \/ / \<server name> / \<organization name> / \<team name> :*, till exempel **https: \/ /dev.Azure.com/DataScienceUnit/myTeam**, och välj **databaser** i det vänstra navigerings fältet. 
   
1. Välj namn på databasen överst på sidan och välj sedan **ny lagrings plats** i list rutan.
   
   ![Välj ny lagrings plats](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. I dialog rutan **skapa en ny lagrings plats** kontrollerar du att **git** är markerat under **typ**. Skriv *DSProject1* under **databas namn** och välj sedan **skapa**.
   
   ![Skapa lagrings plats](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Bekräfta att du kan se den nya **DSProject1** -lagringsplatsen på sidan projekt inställningar. 
   
   ![Projekt lagrings plats i projekt inställningar](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Importera team mal len till projekt databasen

För att fylla i ditt projekts lagrings plats med innehållet i team mal len lagrings plats:

1. Välj **databaser** i det vänstra navigerings fönstret på din grupps projekt **Sammanfattning** . 
   
1. Välj namn på databasen överst på sidan och välj **DSProject1** i list rutan.
   
1. På sidan **DSProject1 är tom** väljer du **Importera**. 
   
   ![Välj Importera](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. I dialog rutan **Importera en git-lagringsplats** väljer **du git** som **käll typ** och anger URL: en för **TEAMTEMPLATE** -lagringsplatsen under klon- **URL**. URL: en är *https: \/ / \<server name> / \<organization name> / \<team name> /_git \<team template repository name> /*. Till exempel: **https: \/ /dev.Azure.com/DataScienceUnit/myTeam/_git/TeamTemplate**. 
   
1. Välj **Importera**. Innehållet i lagrings platsen för team mal len importeras till projekt lagrings platsen. 
   
   ![Importera team mal len lagrings plats](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Om du behöver anpassa innehållet i projekt lagrings platsen för att uppfylla projektets speciella behov, kan du lägga till, ta bort eller ändra databasfiler och mappar. Du kan arbeta direkt i Azure databaser eller klona lagrings platsen till din lokala dator eller DSVM, göra ändringar och spara och skicka dina uppdateringar till den delade projekt lagrings platsen. Följ anvisningarna i [anpassa innehållet i teamets databaser](team-lead-tasks.md#customize-the-contents-of-the-team-repositories).

## <a name="next-steps"></a>Nästa steg

Här följer länkar till detaljerade beskrivningar av de andra rollerna och uppgifterna som definieras av team data science-processen:

- [Grupp chefs aktiviteter för ett data vetenskaps team](group-manager-tasks.md)
- [Grupp ledar uppgifter för ett data vetenskaps team](team-lead-tasks.md)
- [Enskilda deltagar uppgifter för ett data vetenskaps team](project-ic-tasks.md)