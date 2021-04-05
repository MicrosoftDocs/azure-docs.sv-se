---
title: Samarbets kodning med Git-Team data science process
description: Hur man gör en gemensam kod utveckling för data vetenskaps projekt med git med smidig planering.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ca24a781f4f3ad5c210813dabbb896de35056ed6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97588717"
---
# <a name="collaborative-coding-with-git"></a>Samarbetsbaserad kodning med Git

Den här artikeln beskriver hur du använder git som utvecklings ramverk för samarbets koder för data vetenskaps projekt. Artikeln beskriver hur du länkar kod i Azure databaser för att göra det [smidigare att utveckla](agile-development.md) arbets objekt i Azure-kort, hur du gör kod granskningar och hur du skapar och sammanfogar pull-begäranden för ändringar.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Länka ett arbets objekt till en Azure databaser-gren 

Azure DevOps är ett bekvämt sätt att ansluta till en användar berättelse i Azure-kort eller uppgifts arbete med en lokal Azure databaser git-lagringsplats. Du kan länka din användar berättelse eller uppgift direkt till den kod som är kopplad till den. 

Om du vill ansluta ett arbets objekt till en ny gren väljer du ellipsen **åtgärder** (**...**) bredvid arbetsobjektet. rulla sedan till och välj **ny gren** på snabb menyn.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

I dialog rutan **skapa en gren** anger du det nya gren namnet och den grundläggande Azure databaser git-lagringsplatsen och grenen. Bas lagrings platsen måste finnas i samma Azure DevOps-projekt som arbets uppgiften. Bas grenen kan vara vilken befintlig gren som helst. Välj **skapa gren**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Du kan också skapa en ny gren med följande git bash-kommando i Windows eller Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Om du inte anger någon \<base branch name> , baseras den nya grenen på `main` . 

Kör följande kommando för att växla till din arbets gren: 

```bash
git checkout <working branch name>
```

När du har växlat till arbets grenen kan du börja utveckla kod-eller dokumentations artefakter för att slutföra arbets uppgiften. Kör `git checkout main` växlar som du tillbaka till `main` grenen.

Det är en bra idé att skapa en git-gren för varje arbets objekt i användar artikeln. För varje aktivitets arbets objekt kan du sedan skapa en gren som baseras på grenen för användar artikeln. Organisera grenarna i en hierarki som motsvarar användaren Story-Task relationen när du har flera personer som arbetar med olika användar berättelser för samma projekt, eller på olika uppgifter i samma användar berättelse. Du kan minimera konflikter genom att låta varje grupp medlem arbeta på en annan gren eller på en annan kod eller andra artefakter när du delar en gren. 

I följande diagram visas den rekommenderade gren strategin för TDSP. Du kanske inte behöver så många grenar som visas här, särskilt när endast en eller två personer arbetar med ett projekt, eller bara en person arbetar på alla uppgifter i en användar berättelse. Men att åtskilja utvecklings grenen från den primära grenen är alltid en bra metod och kan hjälpa till att förhindra att versions grenen avbryts av utvecklings aktiviteter. En fullständig beskrivning av Git-grenens modell finns i [en lyckad git-förgrenings modell](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Du kan också länka ett arbets objekt till en befintlig gren. Välj **Lägg till länk** på sidan **information** i ett arbets objekt. Välj sedan en befintlig gren för att länka arbets uppgiften till och välj **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Arbeta på grenen och genomför ändringar 

När du har gjort en ändring för arbets uppgiften, till exempel att lägga till en R-skriptfil till den lokala datorns `script` gren, kan du spara ändringen från din lokala gren till den överordnade arbets grenen med hjälp av följande git bash-kommandon:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Skapa en pull-begäran

När du är redo att slå samman din aktuella arbets gren till dess bas gren, kan du skapa och skicka en *pull-begäran* i Azure databaser efter en eller flera genomförande och push-meddelanden. 

Från huvud sidan i ditt Azure DevOps-projekt pekar du på **databaser**  >  **pull-begäranden** i det vänstra navigerings fältet. Välj sedan någon av de **nya** knapparna för pull-begäran eller länken **skapa en pull-begäran** .

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

På sidan **ny pull-begäran** , om det behövs, navigerar du till den git-lagringsplats och gren som du vill sammanfoga ändringarna i. Lägg till eller ändra övrig information som du vill ha. Lägg till namnen på granskarna under **granskare** och välj sedan **skapa**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>Granska och sammanfoga

När du har skapat pull-begäran får dina granskare ett e-postmeddelande om att granska pull-begäran. Granskarna testar om ändringarna fungerar och kontrollerar ändringarna med beställaren om det är möjligt. Granskarna kan göra kommentarer, begära ändringar och godkänna eller avvisa pull-begäran utifrån deras bedömning. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

När granskarna har godkänt ändringarna kan du eller någon annan med sammanslagnings behörighet sammanfoga arbets grenen till dess bas gren. Välj **Slutför** och välj sedan **Slutför sammanslagning** i dialog rutan **fullständig pull-begäran** . Du kan välja att ta bort arbets grenen när den har slagits samman. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Bekräfta att begäran har marker ATS som **slutförd**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

När du går tillbaka till **databaser** i det vänstra navigerings fönstret kan du se att du har växlat till huvud grenen eftersom `script` grenen togs bort.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Du kan också använda följande git bash-kommandon för att slå samman `script` arbets grenen till dess bas gren och ta bort arbets grenen efter sammanslagning:

```bash
git checkout main
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Nästa steg

[Kör data vetenskaps uppgifter](execute-data-science-tasks.md) visar hur du använder verktyg för att utföra flera vanliga uppgifter för data vetenskap, till exempel interaktiv data granskning, data analys, rapportering och skapande av modeller.

I [exempel genom gångar](walkthroughs.md) visas en genom gång av vissa scenarier med länkar och miniatyr beskrivningar. De länkade scenarierna illustrerar hur du kombinerar molnet och lokala verktyg och tjänster i arbets flöden eller pipelines för att skapa intelligenta program. 

