---
title: Team data science process Group Manager-uppgifter
description: Följ den här detaljerade genom gången av de uppgifter som en grupp hanterare har slutfört på ett data vetenskaps team projekt.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7ab6d6511d1e2cec82b321003c9d663249ddcf49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94740234"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Team data science process Group Manager-uppgifter

I den här artikeln beskrivs de uppgifter som en *grupp hanterare* har slutfört för en data vetenskaps organisation. Grupp hanteraren hanterar hela data vetenskaps enheten i ett företag. En data vetenskaps enhet kan ha flera team, som var och en arbetar med många data vetenskaps projekt i olika affärs lodräta. Grupp hanterarens mål är att upprätta en samarbets grupp miljö som är standardiserad på [team data science-processen](overview.md) (TDSP). En översikt över alla personal roller och tillhör ande uppgifter som hanteras av ett data vetenskaps team som är standardiserade på TDSP finns i [process roller och uppgifter för team data vetenskap](roles-tasks.md).

Följande diagram visar de sex huvudsakliga grupp hanterarens installations uppgifter. Grupp chefer kan delegera sina uppgifter till surrogat, men de uppgifter som är associerade med rollen ändras inte.

![Grupp hanterings uppgifter](./media/group-manager-tasks/tdsp-group-manager.png)

1. Konfigurera en **Azure DevOps-organisation** för gruppen.
2. Skapa standard **projektet för GroupCommon** i Azure DevOps-organisationen.
3. Skapa **GroupProjectTemplate** -lagringsplatsen i Azure databaser.
4. Skapa **GroupUtilities** -lagringsplatsen i Azure databaser.
5. Importera innehållet i Microsoft TDSP-teamets **ProjectTemplate** -och **Utilities** -databaser till de gemensamma gruppernas delade databaser.
6. Konfigurera **medlemskap** och **behörigheter** för grupp medlemmar för åtkomst till gruppen.

I följande självstudie går vi igenom stegen i detalj. 

> [!NOTE] 
> I den här artikeln används Azure DevOps för att konfigurera en TDSP-grupp miljö, eftersom det är så här implementerar du TDSP på Microsoft. Om din grupp använder andra kod värdbaserade eller utvecklings plattformar, är grupp hanterarens aktiviteter samma, men sättet att slutföra dem kan vara olika.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Skapa en organisation och ett projekt i Azure DevOps

1. Gå till [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com), Välj **Logga in** längst upp till höger och logga in på din Microsoft-konto. 
   
   ![Logga in på din Microsoft-konto](./media/group-manager-tasks/signinvs.png)
   
   Om du inte har någon Microsoft-konto väljer du **Registrera dig nu**, skapar en Microsoft-konto och loggar in med det här kontot. Om din organisation har en Visual Studio-prenumeration loggar du in med autentiseringsuppgifterna för den prenumerationen.
   
1. När du har loggat in, längst upp till höger på sidan Azure-DevOps, väljer du **Skapa ny organisation**.
   
   ![Skapa ny organisation](./media/group-manager-tasks/create-organization.png)
   
1. Om du uppmanas att godkänna villkoren för tjänsten, sekretess policyn och uppföranderegler väljer du **Fortsätt**.
   
1. I dialog rutan för registrering namnger du din Azure DevOps-organisation och godkänner tilldelningen av värd området, eller så väljer du en annan region. Välj sedan **Fortsätt**. 

1. Under **skapa ett projekt för att komma igång skriver du** *GroupCommon* och väljer sedan **skapa projekt**. 
   
   ![Skapa projekt](./media/group-manager-tasks/create-project.png)

Sidan **GroupCommon** Project **Summary** öppnas. Sidans URL är *https: \/ / \<servername> / \<organization-name> /GroupCommon*.

![Sidan projekt Sammanfattning](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Konfigurera gruppens gemensamma databaser

Azure-databaser är värd för följande typer av databaser för din grupp:

- **Gruppera vanliga databaser**: allmänna databaser som flera team inom en data vetenskaps enhet kan använda för många data vetenskaps projekt. 
- **Team-databaser**: databaser för vissa team inom en data vetenskaps enhet. De här databaserna är speciella för ett grupp behov och kan användas för flera projekt i teamet, men de är inte allmänt nog att användas i flera team inom en data vetenskaps enhet.
- **Projekt databaser**: databaser för vissa projekt. Sådana databaser får inte vara generella för flera projekt i ett team, eller för andra team i en data vetenskaps enhet.

Om du vill ställa in gemensamma grupper för gruppen i projektet kan du: 
- Byt namn på standard lagrings platsen för **GroupCommon** till **GroupProjectTemplate**
- Skapa en ny **GroupUtilities** -lagringsplats

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Byt namn på Standard projekt lagrings platsen till GroupProjectTemplate

Så här byter du namn på standard **GroupCommon** Project-lagringsplats till **GroupProjectTemplate**:

1. På sidan **GroupCommon** Project **Summary** väljer du **databaser**. Den här åtgärden tar dig till standard **GroupCommon** -lagringsplatsen för GroupCommon-projektet, som är tom för tillfället.
   
1. Klicka på pilen bredvid **GroupCommon** överst på sidan och välj **Hantera databaser**.
   
   ![Hantera databaser](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. På sidan **projekt inställningar** väljer du **...** bredvid **GroupCommon** och väljer sedan **Byt namn på lagrings plats**. 
   
   ![Välj... och välj sedan Byt namn på lagrings plats](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. I popup-fönstret för **att byta namn på GroupCommon-lagringsplatsen anger du** *GroupProjectTemplate* och väljer sedan **Byt namn**. 
   
   ![Byt namn på lagrings plats](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Skapa GroupUtilities-lagringsplatsen

Så här skapar du **GroupUtilities** -lagringsplatsen:

1. På sidan **GroupCommon** Project **Summary** väljer du **databaser**. 
   
1. Längst upp på sidan, nedrullningsbar pilen bredvid **GroupProjectTemplate** och välj **ny lagrings plats**.
   
   ![Välj ny lagrings plats](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. I dialog rutan **skapa en ny lagrings plats** väljer du **git** som **typ**, anger *GroupUtilities* som **namn på databasen** och väljer sedan **skapa**.
   
   ![Skapa GroupUtilities-lagringsplats](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. På sidan **projekt inställningar** väljer du **databaser** under **databaser** i det vänstra navigerings fältet för att se de två grupp databaserna: **GroupProjectTemplate** och **GroupUtilities**.
   
   ![Två grupp databaser](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importera Microsoft TDSP team-databaser

I den här delen av självstudien importerar du innehållet i databaserna **ProjectTemplate** och **Utilities** som hanteras av Microsoft TDSP-teamet till dina **GroupProjectTemplate** -och **GroupUtilities** -databaser. 

Så här importerar du TDSP team-databaser:

1. Från start sidan för **GroupCommon** -projektet väljer du **databaser** i det vänstra navigerings fältet. Standard **GroupProjectTemplate** -lagrings platsen öppnas. 
   
1. På sidan **GroupProjectTemplate är tom** väljer du **Importera**. 
   
   ![Välj Importera](./media/group-manager-tasks/import-repo.png)
   
1. I dialog rutan **Importera en git-lagringsplats** väljer **du git** som **käll typ** och anger *https: \/ /GitHub.com/Azure/Azure-TDSP-ProjectTemplate.git* för klon- **URL: en**. Välj sedan **Importera**. Innehållet i Microsoft TDSP team ProjectTemplate-lagringsplatsen importeras till GroupProjectTemplate-lagringsplatsen. 
   
   ![Importera Microsoft TDSP team-lagringsplats](./media/group-manager-tasks/import-repo-2.png)
   
1. Längst upp på sidan **databaser** , väljer du den **GroupUtilities** databasen.
   
Var och en av dina två grupp databaser innehåller nu alla filer, förutom de i katalogen *. git* , från Microsoft TDSP-teamets motsvarande lagrings plats. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Anpassa innehållet i grupp databaserna

Om du vill anpassa innehållet i dina grupp databaser för att uppfylla de speciella behoven i din grupp kan du göra det nu. Du kan ändra filerna, ändra katalog strukturen eller lägga till filer som gruppen har utvecklat eller som är användbara för din grupp.

### <a name="make-changes-in-azure-repos"></a>Gör ändringar i Azure-databaser

Så här anpassar du lagrings innehållet:

1. På sidan **GroupCommon** Project **Summary** väljer du **databaser**. 
   
1. Välj den lagrings plats som du vill anpassa längst upp på sidan.

1. I katalog strukturen lagrings platsen navigerar du till den mapp eller fil som du vill ändra. 
   
   - Om du vill skapa nya mappar eller filer väljer du pilen bredvid **ny**. 
     
     ![Skapa ny fil](./media/group-manager-tasks/new-file.png)
     
   - Om du vill ladda upp filer väljer du **Ladda upp fil (er)**. 
     
     ![Ladda upp filer](./media/group-manager-tasks/upload-files.png)
     
   - Om du vill redigera befintliga filer navigerar du till filen och väljer sedan **Redigera**. 
     
     ![Redigera en fil](./media/group-manager-tasks/edit-file.png)
     
1. När du har lagt till eller redigerat filer väljer du **genomför**.
   
   ![Genomför ändringar](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Gör ändringar med hjälp av den lokala datorn eller DSVM

Om du vill göra ändringar med hjälp av din lokala dator eller DSVM och skicka ändringarna till grupp databaserna, kontrollerar du att du har förutsättningarna för att arbeta med git och Dsvm:

- En Azure-prenumeration, om du vill skapa en DSVM.
- Git installerat på datorn. Om du använder en DSVM är git förinstallerat. I annat fall, se [bilagan plattform och verktyg](platforms-and-tools.md#appendix).
- Om du vill använda en DSVM skapas och konfigureras Windows-eller Linux-DSVM i Azure. Mer information och anvisningar finns i Data Science Virtual Machine- [dokumentationen](../data-science-virtual-machine/index.yml).
- För en Windows-DSVM är [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerad på datorn. Rulla ned till avsnittet **Ladda ned och installera** i *Readme.MD* -filen och välj det **senaste installations programmet**. Hämta *exe* -installationsprogrammet från installations sidan och kör det. 
- För en Linux-DSVM, konfigureras en offentlig SSH-nyckel på din DSVM och läggs till i Azure DevOps. Mer information och instruktioner finns i avsnittet **skapa offentlig SSH-nyckel** i [tillägget plattformar och verktyg](platforms-and-tools.md#appendix). 

Först kopierar eller *klonar* du lagrings platsen till din lokala dator. 
   
1. På sidan **GroupCommon** Project **Summary** väljer du **databaser**. längst upp på sidan väljer du den lagrings plats som du vill klona.
   
1. På sidan lagrings platsen väljer du **klona** längst upp till höger.
   
1. I dialog rutan **klona lagrings plats** väljer du **https** för en HTTP-anslutning eller **SSH** för en SSH-anslutning och kopierar klon-URL: en under **kommando raden** till Urklipp.
   
   ![Klona lagrings platsen](./media/group-manager-tasks/clone.png)
   
1. Skapa följande kataloger på den lokala datorn:
   
   - För Windows: **C:\GitRepos\GroupCommon**
   - För Linux, **$/GitRepos/GroupCommon** i din arbets katalog 
   
1. Ändra till den katalog som du har skapat.
   
1. Kör kommandot i git bash `git clone <clone URL>.`
   
   Något av följande kommandon klonar till exempel **GroupUtilities** -lagringsplatsen till *GroupCommon* -katalogen på den lokala datorn. 
   
   **HTTPS-anslutning:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH-anslutning:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

När du har gjort de ändringar som du vill ha i den lokala klonen av lagrings platsen kan du skicka ändringarna till delade grupper i delade grupper. 

Kör följande git bash-kommandon från din lokala **GroupProjectTemplate** -eller **GroupUtilities** -katalog.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Om det här är första gången du genomför till en git-lagringsplats kan du behöva konfigurera globala parametrar *User.name* och *User.email* innan du kör `git commit` kommandot. Kör följande två kommandon:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Om du använder flera git-databaser använder du samma namn och e-postadress för alla. Det är praktiskt att använda samma namn och e-postadress när du skapar Power BI instrument paneler för att spåra git-aktiviteter i flera databaser.

## <a name="add-group-members-and-configure-permissions"></a>Lägg till grupp medlemmar och konfigurera behörigheter

Så här lägger du till medlemmar i gruppen:

1. I Azure DevOps väljer du **projekt inställningar** i det vänstra navigerings fönstret på **GroupCommon** -projektets start sida. 
   
1. I det vänstra navigerings fältet i **Project** väljer du **team** och på sidan **team** väljer du sedan **GroupCommon-teamet**. 
   
   ![Konfigurera team](./media/group-manager-tasks/teams.png)
   
1. På sidan **team profil** väljer du **Lägg till**.
   
   ![Lägg till i GroupCommon-teamet](./media/group-manager-tasks/add-to-team.png)
   
1. I dialog rutan **Lägg till användare och grupper** söker du efter och väljer medlemmar som ska läggas till i gruppen. Välj sedan **Spara ändringar**. 
   
   ![Lägg till användare och grupper](./media/group-manager-tasks/add-users.png)
   

Konfigurera behörigheter för medlemmar:

1. Välj **behörigheter** från det vänstra navigerings fältet i **Project** . 
   
1. På sidan **behörigheter** väljer du den grupp som du vill lägga till medlemmar i. 
   
1. Välj **medlemmar** på sidan för gruppen och välj sedan **Lägg till**. 
   
1. I popup-fönstret **Bjud in medlemmar** söker du efter och väljer medlemmar som ska läggas till i gruppen. Välj sedan **Spara**. 
   
   ![Bevilja behörigheter till medlemmar](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Nästa steg

Här följer länkar till detaljerade beskrivningar av de andra rollerna och uppgifterna i team data science-processen:

- [Grupp ledar uppgifter för ett data vetenskaps team](team-lead-tasks.md)
- [Projekt ledar uppgifter för ett data vetenskaps team](project-lead-tasks.md)
- [Projicera enskilda deltagar uppgifter för ett data vetenskaps team](project-ic-tasks.md)
