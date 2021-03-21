---
title: Aktiviteter för en enskild deltagare i team data science-processen
description: En detaljerad genom gång av aktiviteterna för en enskild deltagare i ett data vetenskaps team projekt.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ecb5fef9c9b14bde72de29a45e29d7e16131bd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001005"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Aktiviteter för en enskild deltagare i team data science-processen

Det här avsnittet beskriver de uppgifter som en *enskild deltagare* har slutfört för att skapa ett projekt i [team data science process](overview.md) (TDSP). Målet är att arbeta i en samarbets grupp miljö som är standardiserad på TDSP. TDSP är utformad för att hjälpa till att förbättra samarbetet och team inlärningen. En översikt över personal rollerna och deras associerade uppgifter som hanteras av ett data vetenskaps team som är standardiserade på TDSP finns i [process roller och uppgifter för team data vetenskap](roles-tasks.md).

Följande diagram visar de aktiviteter som Projects enskilda deltagare (data experter) har slutfört för att konfigurera sin grupp miljö. Instruktioner för hur du kör ett data vetenskaps projekt under TDSP finns i [körningen av data vetenskaps projekt](./agile-development.md). 

![Enskilda bidrags uppgifter](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** är den lagrings plats som ditt projekt grupp hanterar för att dela projektmallar och till gångar.
- **TeamUtilities** är den arbets plats i teamet som ditt team hanterar för ditt team. 
- **GroupUtilities** är lagrings platsen som din grupp hanterar för att dela användbara verktyg i hela gruppen. 

> [!NOTE] 
> I den här artikeln används Azure databaser och en Data Science Virtual Machine (DSVM) för att konfigurera en TDSP-miljö, eftersom det är hur du implementerar TDSP på Microsoft. Om ditt team använder andra kod värdar eller utvecklings plattformar, är de enskilda deltagar uppgifterna samma, men sättet att slutföra dem kan vara olika.

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien förutsätter vi att följande resurser och behörigheter har kon figurer ATS av din [grupp chef](group-manager-tasks.md), [grupp ledare](team-lead-tasks.md)och [projekt ledare](project-lead-tasks.md):

- Azure DevOps- **organisationen** för din data vetenskaps enhet
- En **projekt databas** som skapats av ditt projekt leder till delning av projektmallar och till gångar
- **GroupUtilities** -och **TeamUtilities** -databaser som ställts in av grupp chefen och teamets lead, om tillämpligt
- Azure **File Storage** är konfigurerat för delade till gångar för ditt team eller projekt, om tillämpligt
- **Behörigheter** som du kan använda för att klona från och skicka tillbaka till projekt databasen 

Om du vill klona databaser och ändra innehåll på din lokala dator eller DSVM, eller montera Azure File Storage till din DSVM, måste du tänka på den här check listan:

- En Azure-prenumeration.
- Git installerat på datorn. Om du använder en DSVM är git förinstallerat. I annat fall, se [bilagan plattform och verktyg](platforms-and-tools.md#appendix).
- Om du vill använda en DSVM skapas och konfigureras Windows-eller Linux-DSVM i Azure. Mer information och anvisningar finns i Data Science Virtual Machine- [dokumentationen](../data-science-virtual-machine/index.yml).
- För en Windows-DSVM är [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerad på datorn. Rulla ned till avsnittet **Ladda ned och installera** i *Readme.MD* -filen och välj det **senaste installations programmet**. Hämta *exe* -installationsprogrammet från installations sidan och kör det. 
- För en Linux-DSVM, konfigureras en offentlig SSH-nyckel på din DSVM och läggs till i Azure DevOps. Mer information och instruktioner finns i avsnittet **skapa offentlig SSH-nyckel** i [tillägget plattformar och verktyg](platforms-and-tools.md#appendix). 
- Azure File Storage-information för alla Azure File Storage som du behöver montera till din DSVM. 

## <a name="clone-repositories"></a>Klona databaser

Om du vill arbeta med databaserna lokalt och skicka dina ändringar till det delade teamet och projekt arkiven, kopierar du först eller *klonar* lagrings platsen till din lokala dator. 

1. I Azure DevOps går du till din grupps projekt sammanfattnings sida på *https \/ / \<server name> / \<organization name> / \<team name> :*, till exempel **https: \/ /dev.Azure.com/DataScienceUnit/myTeam**.
   
1. Välj **databaser** i det vänstra navigerings fönstret och välj den lagrings plats som du vill klona längst upp på sidan.
   
1. På sidan lagrings platsen väljer du **klona** längst upp till höger.
   
1. I dialog rutan **klona lagrings plats** väljer du **https** för en HTTP-anslutning eller **SSH** för en SSH-anslutning och kopierar klon-URL: en under **kommando raden** till Urklipp.
   
   ![Klona lagrings platsen](./media/project-ic-tasks/clone.png)
   
1. Skapa följande kataloger på din lokala dator eller DSVM:
   
   - För Windows: **C:\GitRepos**
   - För Linux: **$Home/gitrepos**
   
1. Ändra till den katalog som du har skapat.
   
1. Kör kommandot `git clone <clone URL>` för varje lagrings plats som du vill klona i git-bash. 
   
   Följande kommando klonar till exempel **TeamUtilities** -lagringsplatsen till min *team* -katalogen på den lokala datorn. 
   
   **HTTPS-anslutning:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH-anslutning:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Bekräfta att du kan se mapparna för klonade databaser i den lokala projekt katalogen.
   
   ![Tre mappar för lokal lagring](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Montera Azure File Storage till din DSVM

Om ditt team eller projekt har delade resurser i Azure File Storage monterar du fil lagringen till din lokala dator eller DSVM. Följ anvisningarna i [montera Azure File Storage på din lokala dator eller DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Nästa steg

Här följer länkar till detaljerade beskrivningar av de andra rollerna och uppgifterna som definieras av team data science-processen:

- [Grupp chefs aktiviteter för ett data vetenskaps team](group-manager-tasks.md)
- [Grupp ledar uppgifter för ett data vetenskaps team](team-lead-tasks.md)
- [Projekt ledar uppgifter för ett data vetenskaps team](project-lead-tasks.md)