---
title: Uppgifter för team ledare i process teamet för team data vetenskap
description: En detaljerad genom gång av aktiviteterna för ett Teams lead i en team data vetenskap process-grupp
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df7d2278487c1b098615a14562c498b9187c56eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000036"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Uppgifter för team ledare i en team data vetenskap process-grupp

I den här artikeln beskrivs de uppgifter som en *grupp ledare* har slutfört för sitt data vetenskaps team. Teamets ledar mål är att upprätta en samarbets grupp miljö som är standardiserad på [team data science-processen](overview.md) (TDSP). TDSP är utformad för att hjälpa till att förbättra samarbetet och team inlärningen. 

TDSP är en flexibel, iterativ data vetenskaps metod för att effektivt leverera lösningar för förutsägelse analys och intelligenta program. Processen från Microsoft och branschen till bästa praxis och strukturer.  Målet är framgångs rik implementering av data vetenskaps initiativ och kompletterar fördelarna med sina analys program. En översikt över personal roller och associerade uppgifter för ett data vetenskaps team som är standardiserade på TDSP finns i [process roller och uppgifter för team data vetenskap](roles-tasks.md).

En grupp ledare hanterar ett team som består av flera data experter på data vetenskaps enheten i ett företag. Beroende på data vetenskaps enhetens storlek och struktur kan [grupp chefen](group-manager-tasks.md) och teamets lead vara samma person, eller så kan de delegera sina uppgifter till surrogat. Men själva uppgifterna ändras inte. 

Följande diagram visar arbets flödet för de uppgifter som team ledare har slutfört för att konfigurera en team miljö:

![Arbets flöde för grupp ledar uppgift](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Skapa ett **team projekt** i gruppens organisation i Azure DevOps. 
  
1. Byt namn på standard team-lagringsplatsen till **TeamUtilities**.
  
1. Skapa en ny **TeamTemplate** -lagringsplats i team projektet. 
  
1. Importera innehållet i gruppens **GroupUtilities** -och **GroupProjectTemplate** -databaser till **TeamUtilities** -och **TeamTemplate** -databaserna. 
  
1. Konfigurera **säkerhets kontroll** genom att lägga till team medlemmar och konfigurera deras behörigheter.
  
1. Om det behövs skapar du team data och analys resurser:
   - Lägg till Team-/regionsspecifika verktyg i **TeamUtilities** -lagringsplatsen. 
   - Skapa **Azure File Storage** för att lagra data till gångar som kan vara användbara för hela teamet. 
   - Montera Azure File Storage i teamets lead **data science Virtual Machine** (DSVM) och Lägg till data till gångar till den.

I följande självstudie går vi igenom stegen i detalj.

> [!NOTE] 
> I den här artikeln används Azure DevOps och en DSVM för att skapa en TDSP-grupp miljö, eftersom det är hur du implementerar TDSP på Microsoft. Om ditt team använder andra kod värdar eller utvecklings plattformar, är grupp ledar aktiviteterna samma, men det kan vara olika att slutföra dem.

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att följande resurser och behörigheter har skapats av din [grupp hanterare](group-manager-tasks.md):

- Azure DevOps- **organisationen** för din data enhet
- **GroupProjectTemplate** och **GroupUtilities** -lagringsplatser, ifyllda med innehållet i Microsoft TDSP-teamets **ProjectTemplate** -och **Utilities** -databaser
- Behörigheter för ditt organisations konto så att du kan skapa projekt och databaser för ditt team

För att kunna klona lagrings utrymmen och ändra innehållet på din lokala dator eller DSVM, eller konfigurera Azure File Storage och montera den på din DSVM, behöver du följande:

- En Azure-prenumeration.
- Git installerat på datorn. Om du använder en DSVM är git förinstallerat. I annat fall, se [bilagan plattform och verktyg](platforms-and-tools.md#appendix).
- Om du vill använda en DSVM skapas och konfigureras Windows-eller Linux-DSVM i Azure. Mer information och anvisningar finns i Data Science Virtual Machine- [dokumentationen](../data-science-virtual-machine/index.yml).
- För en Windows-DSVM är [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerad på datorn. Rulla ned till avsnittet **Ladda ned och installera** i *Readme.MD* -filen och välj det **senaste installations programmet**. Hämta *exe* -installationsprogrammet från installations sidan och kör det. 
- För en Linux-DSVM, konfigureras en offentlig SSH-nyckel på din DSVM och läggs till i Azure DevOps. Mer information och instruktioner finns i avsnittet **skapa offentlig SSH-nyckel** i [tillägget plattformar och verktyg](platforms-and-tools.md#appendix). 

## <a name="create-a-team-project-and-repositories"></a>Skapa ett grup projekt och databaser

I det här avsnittet skapar du följande resurser i din grupps Azure DevOps-organisation:

- Mitt **team** -projektet i Azure DevOps
- **TeamTemplate** -lagringsplatsen
- **TeamUtilities** -lagringsplatsen

Namnen som anges för databaserna och katalogerna i den här självstudien förutsätter att du vill skapa ett separat projekt för ditt eget team i din större data vetenskaps organisation. Hela gruppen kan dock välja att arbeta under ett enskilt projekt som skapats av grupp chefen eller organisationens administratör. Sedan skapar alla data vetenskaps team databaser under det här projektet. Det här scenariot kan vara giltigt för:
- En liten data vetenskaps grupp som inte har flera data vetenskaps team. 
- En större data vetenskaps grupp med flera data vetenskaps team som ändå vill optimera samarbetet mellan grupper med aktiviteter som till exempel på grupp-nivå – Sprint planering. 

Om teamen väljer att ha sina teambaserade databaser under ett enda grupp projekt, bör teamet skapa lagrings platser med namn som *\<TeamName> mall* och *\<TeamName> verktyg*. Till exempel: *TeamATemplate* och *TeamAUtilities*. 

I alla fall måste grupp ledare låta sina team medlemmar veta vilka mallar för mallar och verktyg som ska ställas in och klonas. Projekt leads bör följa [projektets ledar aktiviteter för ett data vetenskaps team](project-lead-tasks.md) för att skapa projekt databaser, oavsett om de finns under separata projekt eller i ett enda projekt. 

### <a name="create-the-myteam-project"></a>Skapa mitt team-projekt

Så här skapar du ett separat projekt för ditt team:

1. I webbläsaren går du till din grupps Azure DevOps-organisations start sida på URL *https: \/ / \<server name> / \<organization name>* och väljer **nytt projekt**. 
   
   ![Välj nytt projekt](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. I dialog rutan **skapa projekt** anger du ditt grupp namn, till exempel min *team*, under **projekt namn** och väljer sedan **Avancerat**. 
   
1. Under **versions kontroll** väljer du **git** och under **arbets objekts process** väljer du **Agile**. Välj sedan **Skapa**. 
   
   ![Skapa projekt](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
Sidan **Sammanfattning** av grup projekt öppnas med sidans URL *https: \/ / \<server name> / \<organization name> / \<team name>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Byt namn på standard lagrings platsen för min team till TeamUtilities

1. På sidan **Sammanfattning** av **projekt projekt under** **vilken tjänst vill du börja med? väljer du** **databaser**. 
   
   ![Välj databaser](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. På sidan lagrings platsen för mina **team** väljer du arbets platsen för min **team** överst på sidan och väljer sedan **Hantera databaser** i list rutan. 
   
   ![Välj Hantera databaser](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. På sidan **projekt inställningar** väljer du **...** bredvid min **team** -lagringsplats och väljer sedan **Byt namn på lagrings plats**. 
   
   ![Välj Byt namn på lagrings plats](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. I popup-fönstret för **att byta namn på min team-lagringsplats anger du** *TeamUtilities* och väljer sedan **Byt namn**. 

### <a name="create-the-teamtemplate-repository"></a>Skapa TeamTemplate-lagringsplatsen

1. På sidan **projekt inställningar** väljer du **ny lagrings plats.** 
   
   ![Välj ny lagrings plats](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Eller Välj **databaser** i det vänstra navigerings fönstret på sidan projekt **Sammanfattning** för min **grupp** , Välj en lagrings plats överst på sidan och välj sedan **ny lagrings plats** i list rutan.
   
1. I dialog rutan **skapa en ny lagrings plats** kontrollerar du att **git** är markerat under **typ**. Skriv *TeamTemplate* under **databas namn** och välj sedan **skapa**.
   
   ![Skapa lagrings plats](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Bekräfta att du kan se de två databaserna **TeamUtilities** och **TeamTemplate** på sidan projekt inställningar. 
   
   ![Två team databaser](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Importera innehållet i gruppen delade databaser

För att fylla i dina team databaser med innehållet i gruppen gemensamma databaser som har kon figurer ATS av din grupp hanterare:

1. Välj **databaser** i det vänstra navigerings fönstret på Start sidan för ditt **team** -projekt. Om du får ett meddelande om att mallen för min **team** inte hittas väljer du länken i **annat fall, navigerar till standard lagrings platsen för TeamTemplate.** 
   
   Standard- **TeamTemplate** -lagringsplatsen öppnas. 
   
1. På sidan **TeamTemplate är tom** väljer du **Importera**. 
   
   ![Välj Importera](./media/team-lead-tasks/import-repo.png)
   
1. I dialog rutan **Importera en git-lagringsplats** väljer du **git** som **käll typ** och anger URL: en för din grupps gemensamma mall lagrings plats under **klon-URL**. URL: en är *https: \/ / \<server name> / \<organization name> /_git \<repository name> /*. Till exempel: *https: \/ /dev.Azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Välj **Importera**. Innehållet i din gruppmalls lagrings plats importeras till lagrings platsen för din grupp. 
   
   ![Importera grupp common Template-lagringsplats](./media/team-lead-tasks/import-repo-2.png)
   
1. Längst upp på projektets **databaser** -sida, list rutan och välj **TeamUtilities** -lagringsplatsen.
   
1. Upprepa import processen för att importera innehållet i din grupp vanliga verktyg-lagringsplats, till exempel *GroupUtilities*, till din **TeamUtilities** -lagringsplats. 
   
Var och en av dina två team-databaser innehåller nu filerna från motsvarande grupps gemensamma lagrings plats. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Anpassa innehållet i team databaserna

Om du vill anpassa innehållet i teamets databaser så att det passar ditt teams behov kan du göra det nu. Du kan ändra filer, ändra katalog strukturen eller lägga till filer och mappar.

Ändra, ladda upp eller skapa filer eller mappar direkt i Azure DevOps:

1. På sidan projekt **Sammanfattning** för min **team** väljer du **databaser**. 
   
1. Välj den lagrings plats som du vill anpassa längst upp på sidan.

1. I katalog strukturen lagrings platsen navigerar du till den mapp eller fil som du vill ändra. 
   
   - Om du vill skapa nya mappar eller filer väljer du pilen bredvid **ny**. 
     
     ![Skapa ny fil](./media/team-lead-tasks/new-file.png)
     
   - Om du vill ladda upp filer väljer du **Ladda upp fil (er)**. 
     
     ![Ladda upp filer](./media/team-lead-tasks/upload-files.png)
     
   - Om du vill redigera befintliga filer navigerar du till filen och väljer sedan **Redigera**. 
     
     ![Redigera en fil](./media/team-lead-tasks/edit-file.png)
     
1. När du har lagt till eller redigerat filer väljer du **genomför**.
   
   ![Genomför ändringar](./media/team-lead-tasks/commit.png)

Om du vill arbeta med databaser på din lokala dator eller DSVM kopierar du eller *klonar* lagrings platsen till den lokala datorn och bekräftar och skickar sedan dina ändringar till de delade team databaserna. 

Klona databaser:

1. På sidan projekt **Sammanfattning** för min **team** väljer du **databaser** och väljer den lagrings plats som du vill klona längst upp på sidan.
   
1. På sidan lagrings platsen väljer du **klona** längst upp till höger.
   
1. I dialog rutan **klona databas** väljer du **https** för en HTTP-anslutning eller **SSH** för en SSH-anslutning under **kommando rad** och kopierar klon-URL: en till Urklipp.
   
   ![Kopiera klon-URL](./media/team-lead-tasks/clone.png)
   
1. Skapa följande kataloger på den lokala datorn:
   
   - För Windows: **C:\GitRepos\MyTeam**
   - För Linux, **$Home/gitrepos/myTeam** 
   
1. Ändra till den katalog som du har skapat.
   
1. I git-bash kör du kommandot `git clone <clone URL>` , där \<clone URL> är den URL som du kopierade från dialog rutan **klon** .
   
   Du kan till exempel använda ett av följande kommandon för att klona **TeamUtilities** -lagringsplatsen till min *team* -katalogen på den lokala datorn. 
   
   **HTTPS-anslutning:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH-anslutning:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

När du har gjort de ändringar som du vill ha i den lokala lagringen av din lagrings plats ska du spara och push-överföra ändringarna till delade team-databaser. 

Kör följande git bash-kommandon från din lokala **GitRepos\MyTeam\TeamTemplate** -eller **GitRepos\MyTeam\TeamUtilities** -katalog.

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

## <a name="add-team-members-and-configure-permissions"></a>Lägg till team medlemmar och konfigurera behörigheter

Så här lägger du till medlemmar i teamet:

1. I Azure DevOps väljer du **projekt inställningar** från den vänstra navigeringen på Start sidan för mitt **team** projekt. 
   
1. I det vänstra navigerings fältet i **Project** väljer du **team** och på sidan **team** väljer **du teamet team.** 
   
   ![Konfigurera team](./media/team-lead-tasks/teams.png)
   
1. På sidan **team profil** väljer du **Lägg till**.
   
   ![Lägg till i teamet](./media/team-lead-tasks/add-to-team.png)
   
1. I dialog rutan **Lägg till användare och grupper** söker du efter och väljer medlemmar som ska läggas till i gruppen. Välj sedan **Spara ändringar**. 
   
   ![Lägg till användare och grupper](./media/team-lead-tasks/add-users.png)
   

Så här konfigurerar du behörigheter för grupp medlemmar:

1. Välj **behörigheter** från det vänstra navigerings fältet i **Project** . 
   
1. På sidan **behörigheter** väljer du den grupp som du vill lägga till medlemmar i. 
   
1. Välj **medlemmar** på sidan för gruppen och välj sedan **Lägg till**. 
   
1. I popup-fönstret **Bjud in medlemmar** söker du efter och väljer medlemmar som ska läggas till i gruppen. Välj sedan **Spara**. 
   
   ![Bevilja behörigheter till medlemmar](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Skapa team data och analys resurser

Det här steget är valfritt, men delning av data och analys resurser med hela teamet har prestanda-och kostnads besparingar. Grupp medlemmar kan köra sina projekt på delade resurser, Spara på budgetar och samar beta effektivare. Du kan skapa Azure File Storage och montera den på din DSVM för att dela med grupp medlemmar. 

Information om hur du delar andra resurser med ditt team, till exempel Azure HDInsight Spark kluster, finns i [plattformar och verktyg](platforms-and-tools.md). Det här avsnittet innehåller vägledning från ett data vetenskaps perspektiv för att välja resurser som är lämpliga för dina behov och länkar till produkt sidor och andra relevanta och användbara självstudier.

>[!NOTE]
> För att undvika att överföra data över data Center, vilket kan vara långsamt och dyrt, se till att din Azure-resurs, ditt lagrings konto och DSVM alla finns i samma Azure-region. 

### <a name="create-azure-file-storage"></a>Skapa Azure File Storage

1. Kör följande skript för att skapa Azure File Storage för data till gångar som är användbara för hela teamet. I skriptet uppmanas du att ange din Azure-prenumerations information så att du kan ange den. 

   - Kör skriptet från PowerShell-Kommandotolken på en Windows-dator:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Kör skriptet från Linux-gränssnittet på en Linux-dator:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Logga in på ditt Microsoft Azure-konto när du uppmanas till det och välj den prenumeration som du vill använda.
   
1. Välj det lagrings konto som du vill använda, eller skapa ett nytt under den valda prenumerationen. Du kan använda gemener, siffror och bindestreck för Azure File Storage-namnet.
   
1. För att under lätta montering och delning av lagringen, tryck på RETUR eller ange *Y* för att spara informationen om Azure File Storage i en textfil i den aktuella katalogen. Du kan kontrol lera text filen i **TeamTemplate** -lagringsplatsen, helst under **Docs\DataDictionaries**, så att alla projekt i teamet kan komma åt den. Du behöver också fil informationen för att montera Azure File Storage i Azure-DSVM i nästa avsnitt. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Montera Azure File Storage på din lokala dator eller DSVM

1. Använd följande skript för att montera Azure File Storage till din lokala dator eller DSVM.
   
   - Kör skriptet från PowerShell-Kommandotolken på en Windows-dator:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Kör skriptet från Linux-gränssnittet på en Linux-dator:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Fortsätt genom att trycka på RETUR eller ange *Y* om du sparade en informations fil för Azure File Storage i föregående steg. Ange den fullständiga sökvägen till och namnet på den fil som du har skapat. 
   
   Om du inte har en fil lagrings informations fil för Azure anger du *n* och följer anvisningarna för att ange din prenumeration, ditt Azure Storage-konto och information om Azure File Storage.
   
1. Ange namnet på en lokal eller TDSP enhet för att montera fil resursen på. På skärmen visas en lista över befintliga enhets namn. Ange ett enhets namn som inte redan finns.
   
1. Bekräfta att den nya enheten och lagringen har monterats på datorn.

## <a name="next-steps"></a>Nästa steg

Här följer länkar till detaljerade beskrivningar av de andra rollerna och uppgifterna som definieras av team data science-processen:

- [Grupp chefs aktiviteter för ett data vetenskaps team](group-manager-tasks.md)
- [Projekt ledar uppgifter för ett data vetenskaps team](project-lead-tasks.md)
- [Projicera enskilda deltagar uppgifter för ett data vetenskaps team](project-ic-tasks.md)