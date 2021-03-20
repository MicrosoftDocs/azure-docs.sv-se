---
title: Smidig utveckling av data vetenskaps projekt – team data science process
description: Kör ett data vetenskaps projekt i en systematisk, version som styrs och samar beta i ett projekt team med hjälp av processen för team data vetenskap.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 113c11b749b42bbaf9e5e40cdf63623288997a92
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89440294"
---
# <a name="agile-development-of-data-science-projects"></a>Smidig utveckling av data vetenskaps projekt

Det här dokumentet beskriver hur utvecklare kan köra ett data vetenskaps projekt i en systematisk, version som styrs och samar beta i ett projekt team med hjälp av TDSP ( [team data science process](overview.md) ). TDSP är ett ramverk som utvecklats av Microsoft och som ger en strukturerad serie aktiviteter för att effektivt köra molnbaserade, förutsägelse analys lösningar. En översikt över de roller och uppgifter som hanteras av ett data vetenskaps team som är standardiserade på TDSP finns i [process roller och uppgifter för team data vetenskap](roles-tasks.md). 

Den här artikeln innehåller anvisningar om hur du: 

- *Planera Sprint* för arbets objekt som ingår i ett projekt.
- Lägg till *arbets objekt* i sprintar.
- Skapa och Använd en *smidigare mall för arbets objekt* som specifikt anpassas med TDSP livs cykel stadier.

Följande anvisningar beskriver de steg som krävs för att konfigurera en TDSP team miljö med Azure-kort och Azure-databaser i Azure DevOps. Anvisningarna använder Azure-DevOps eftersom det är hur du implementerar TDSP på Microsoft. Om din grupp använder en annan kod värd plattform, ändras inte gruppens ledar aktiviteter ofta, men sättet att slutföra uppgifterna är annorlunda. Att länka ett arbets objekt till en git-gren kan exempelvis inte vara detsamma med GitHub eftersom det är med Azure databaser.

Följande bild illustrerar ett typiskt arbets flöde för Sprint planering, kodning och käll kontroll för ett data vetenskaps projekt:

![TDSP (Team Data Science Process)](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Arbets objekts typer

I TDSP Sprint planerings ramverk finns fyra typer av *arbets objekt* som används ofta: *funktioner*, *användar berättelser*, *uppgifter* och *buggar*. Efter släpning för alla arbets uppgifter finns på projekt nivå, inte på git-lagringsplatsen. 

Här är definitionerna för arbets objekts typerna:

- **Funktion**: en funktion motsvarar ett projekt engagemang. Olika engagemang med en klient är olika funktioner, och det är bäst att överväga olika faser i ett projekt som olika funktioner. Om du väljer ett schema, till exempel *\<ClientName>-\<EngagementName>* för att namnge dina funktioner, kan du enkelt identifiera projektets kontext och engagemang från själva namnen.
  
- **Användar berättelse**: användar berättelser är arbets objekt som behövs för att slutföra en funktion från slut punkt till slut punkt. Exempel på användar berättelser är:
  - Hämta data 
  - Utforska data 
  - Generera funktioner
  - Skapa modeller
  - Operationalisera modeller 
  - Omtrimningsmodeller
  
- **Uppgift**: uppgifter är tilldelnings bara arbets objekt som måste utföras för att slutföra en speciell användar berättelse. Till exempel kan uppgifter i användar artikeln *Hämta data* :
  - Hämta SQL Server autentiseringsuppgifter
  - Ladda upp data till Azure Synapse Analytics
  
- **Fel**: fel är problem i befintlig kod eller dokument som måste åtgärdas för att slutföra en uppgift. Om buggar orsakas av saknade arbets objekt kan de eskalera för att vara användar berättelser eller uppgifter. 

Data experter kan känna sig smidigare med en flexibel mall som ersätter funktioner, användar berättelser och uppgifter med TDSP livs cykel steg och under faser. Om du vill skapa en Agile-härledd mall som är specifikt justerad med TDSP livs cykel stadier, se [Använd en smidig TDSP arbets mal len](#set-up-agile-dsp-6).

> [!NOTE]
> TDSP lånar koncepten med funktioner, användares berättelser, uppgifter och buggar från program kod hantering (SCM). TDSP-begreppen kan skilja sig något från de konventionella SCM-definitionerna.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Planera sprintar

Många data tekniker är engagerade i flera projekt, vilket kan ta månader att slutföra och fortsätta i olika takt. Sprint planering är användbart för projekt prioritering och resurs planering och allokering. I Azure-kort kan du enkelt skapa, hantera och spåra arbets uppgifter för dina projekt och göra Sprint planeringen för att se till att projekt flyttas framåt som förväntat.

Mer information om Sprint planering finns i [Scrum Sprints](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint). 

Mer information om Sprint planering i Azure-kort finns i [tilldela efter släpning objekt till en sprint](/azure/devops/boards/sprints/assign-work-sprint). 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Lägga till en funktion i efter släpning 

När projektet och projekt kod lagrings platsen har skapats kan du lägga till en funktion i efter släpandet för att representera arbetet för projektet.

1. Från projekt sidan väljer du **bakkorts**  >  **loggar** i det vänstra navigerings fältet. 
   
1. På fliken efter **släpning** , om arbets objekts typen i det översta fältet är **artiklar**, list rutor och välj **funktioner**. Välj sedan **nytt arbets objekt.**
   
   ![Välj nytt arbets objekt](./media/agile-development/2-sprint-team-overview.png)
   
1. Ange en rubrik för funktionen, vanligt vis ditt projekt namn, och välj sedan **Lägg till överst**. 
   
   ![Ange en rubrik och välj Lägg till överst](./media/agile-development/3-sprint-team-add-work.png)
   
1. I listan efter **släpning** väljer du och öppnar den nya funktionen. Fyll i beskrivningen, tilldela en grupp medlem och ange planerings parametrar. 
   
   Du kan också länka funktionen till projektets Azure databaser Code-lagringsplats genom att välja **Lägg till länk** under avsnittet **utveckling** . 
   
   När du har redigerat funktionen väljer du **spara & Stäng**.
   
   ![Redigera funktion och välj Spara & Stäng](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Lägg till en användar berättelse till funktionen 

Under funktionen kan du lägga till användar berättelser som beskriver de viktigaste stegen som krävs för att slutföra projektet. 

Så här lägger du till en ny användar berättelse i en funktion:

1. På fliken efter **släpning** väljer du **+** till vänster om funktionen. 
   
   ![Lägg till en ny användar berättelse under funktionen](./media/agile-development/4-sprint-add-story.png)
   
1. Ge användar berättelsen en rubrik och redigera information som tilldelning, status, beskrivning, kommentarer, planering och prioritet. 
   
   Du kan också länka användar berättelsen till en gren i projektets Azure databaser Code-lagringsplats genom att välja **Lägg till länk** under avsnittet **utveckling** . Välj den lagrings plats och gren som du vill länka arbets uppgiften till och välj sedan **OK**.
   
   ![Lägg till länk](./media/agile-development/5-sprint-edit-story.png)
   
1. När du är klar med redigeringen av användar artikeln väljer du **spara & Stäng**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Lägg till en uppgift i en användar berättelse 

Uppgifter är särskilda detaljerade steg som behövs för att slutföra varje användar berättelse. När alla uppgifter i en användar berättelse har slutförts bör användar berättelsen vara slutförd. 

Om du vill lägga till en uppgift i en användar berättelse väljer du **+** Nästa artikel och väljer **uppgift**. Fyll i rubriken och annan information i uppgiften.

![Lägg till en uppgift i en användar berättelse](./media/agile-development/7-sprint-add-task.png)

När du har skapat funktioner, användar berättelser och uppgifter kan du visa dem i vyerna **för efter** släpning eller **kort** för att spåra deras status.

![Vy över efter släpning](./media/agile-development/8-sprint-backlog-view.png)

![Vyn kort](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Använd en smidig TDSP arbets mal len

Data experter kan känna sig smidigare med en flexibel mall som ersätter funktioner, användar berättelser och uppgifter med TDSP livs cykel steg och under faser. I Azure-kort kan du skapa en Agile-härledd mall som använder TDSP livs cykel stadier för att skapa och spåra arbets objekt. Följande steg beskriver hur du konfigurerar en data vetenskaps bestämd mall och skapar data vetenskaps arbets objekt baserat på mallen.

### <a name="set-up-an-agile-data-science-process-template"></a>Konfigurera en smidig mall för data vetenskaps process

1. Från din Azure DevOps-organisations huvud sida väljer du **organisations inställningar** i det vänstra navigerings fältet. 
   
1. I **organisations inställningarna** till vänster navigering, under **kort**, väljer du **process**. 
   
1. I fönstret **alla processer** väljer du **...** bredvid **Agile** och väljer sedan **skapa ärvd process**.
   
   ![Skapa ärvd process från Agile](./media/agile-development/10-settings.png) 
   
1. I dialog rutan **skapa ärvd process från Agile** anger du namnet *AgileDataScienceProcess* och väljer **Skapa process**.
   
   ![Skapa AgileDataScienceProcess-process](./media/agile-development/11-agileds.png)
   
1. I **alla processer** väljer du den nya **AgileDataScienceProcess**. 
   
1. På fliken **arbets objekts typer** inaktiverar du **episka**, **funktion**, **användar berättelse** och **uppgift** genom att markera **...** bredvid varje objekt och sedan välja **inaktivera**. 
   
   ![Inaktivera arbets objekts typer](./media/agile-development/12-disable.png)
   
1. I **alla processer** väljer du fliken efter **släpning** . Under **portföljer-avloggar** väljer du **...** bredvid **episka (inaktive rad)** och väljer sedan **Redigera/Byt namn**. 
   
1. I dialog rutan **Redigera efter släpning** :
   1. Ersätt **episka** med *TDSP-projekt* under **namn**. 
   1. Under **arbets objekts typer på den här nivån för efter släpning** väljer du **ny typ av arbets objekt**, anger *TDSP-projekt* och väljer **Lägg till**. 
   1. Under **standard arbets objekts typ**, släpp och välj **TDSP-projekt**. 
   1. Välj **Spara**.
   
   ![Ange efter släpning-nivå för portföljen](./media/agile-development/13-rename.png)  
   
1. Följ samma steg för att byta namn på **funktioner** till *TDSP-faser* och Lägg till följande nya arbets objekts typer:
   
   - *Affärs förståelse*
   - *Data hämtning*
   - *Modellering*
   - *Distribution*
   
1. Under **krav på efter släpning**, byter du namn på **berättelser** till *TDSP under steg*, lägger till den nya arbets objekt typen *TDSP under steget* och anger standard typen av arbets objekt till **TDSP under steg**.
   
1. Lägg till en ny arbets objekt typ *TDSP uppgift* under **iteration efter släpning** och Ställ in den som standard typ av arbets objekt. 
   
När du har slutfört stegen bör du se efter i efter släpande nivåer:
   
 ![Nivåer för TDSP-mallens efter släpning](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Skapa arbets objekt för smidig data vetenskaps process

Du kan använda mallen för data vetenskaps process för att skapa TDSP-projekt och spåra arbets objekt som motsvarar TDSP livs cykel stadier.

1. Från din Azure DevOps-organisations huvud sida väljer du **nytt projekt**. 
   
1. I dialog rutan **Skapa nytt projekt** ger du ditt projekt ett namn och väljer sedan **Avancerat**. 
   
1. Under **arbets uppgifts process** klickar du på list rutan och väljer **AgileDataScienceProcess**. Välj sedan **skapa**.
   
   ![Skapa ett TDSP-projekt](./media/agile-development/15-newproject.png)
   
1. I det nyskapade projektet väljer du **kort**-  >  **loggfiler** i det vänstra navigerings fältet.
   
1. Om du vill göra TDSP-projekt synliga väljer du ikonen **Konfigurera team inställningar** . I fönstret **Inställningar** markerar du kryss rutan **TDSP-projekt** och väljer sedan **Spara och Stäng**.
   
   ![Markera kryss rutan TDSP-projekt](./media/agile-development/16-enabledsprojects1.png)
   
1. Om du vill skapa ett data science-särskilt TDSP-projekt väljer du **TDSP-projekt** i det översta fältet och väljer sedan **nytt arbets objekt**. 
   
1. I popup-fönstret ger du TDSP-projektet ett namn och väljer Lägg till **överst**.
   
   ![Skapa arbets objekt för data vetenskaps projekt](./media/agile-development/17-dsworkitems0.png)
   
1. Om du vill lägga till ett arbets objekt i TDSP-projektet väljer du intill **+** projektet och väljer sedan den typ av arbets objekt som ska skapas. 
   
   ![Välj typ av arbets objekt för data vetenskap](./media/agile-development/17-dsworkitems1.png)
   
1. Fyll i informationen i det nya arbets objektet och välj **spara & Stäng**.
   
1. Fortsätt genom att välja **+** symbolerna bredvid arbets objekt för att lägga till nya TDSP-faser, under steg och uppgifter. 
   
Här är ett exempel på hur arbets objekt i data vetenskaps arbetet ska visas i vyerna för efter **släpning** :

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Nästa steg

[Samarbete med git](collaborative-coding-with-git.md) beskriver hur du utför samarbets kod utveckling för data vetenskaps projekt med git som delad kod utvecklings ramverk och hur du länkar dessa kodnings aktiviteter till arbetet som planeras med den flexibla processen.

I [exempel genom gångar](walkthroughs.md) visas en genom gång av vissa scenarier med länkar och miniatyr beskrivningar. De länkade scenarierna illustrerar hur du kombinerar molnet och lokala verktyg och tjänster i arbets flöden eller pipelines för att skapa intelligenta program.
  
Ytterligare resurser på Agile-processer:

- [Agile-processen](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Agile bearbeta arbets objekt typer och arbets flöde](/azure/devops/boards/work-items/guidance/agile-process-workflow)

