---
title: Köra en avbildnings fabrik från Azure DevOps i Azure DevTest Labs
description: Den här artikeln beskriver alla förberedelser som behövs för att köra avbildnings fabriken från Azure DevOps (tidigare Visual Studio Team Services).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa7050bae1ff8681e04b6ab38220be9eaf38a64a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85476146"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Köra en avbildningsfabrik från Azure DevOps
Den här artikeln beskriver alla förberedelser som behövs för att köra avbildnings fabriken från Azure DevOps (tidigare Visual Studio Team Services).

> [!NOTE]
> Alla Orchestration-motor fungerar! Azure-DevOps är inte obligatoriskt. Avbildnings fabriken körs med Azure PowerShell skript, så den kan köras manuellt med hjälp av Schemaläggaren för Windows, andra CI/CD-system och så vidare.

## <a name="create-a-lab-for-the-image-factory"></a>Skapa ett labb för avbildnings fabriken
Det första steget i att konfigurera avbildnings fabriken är att skapa ett labb i Azure DevTest Labs. Det här labbet är image Factory-labbet där vi skapar de virtuella datorerna och sparar anpassade avbildningar. Det här labbet betraktas som en del av den övergripande avbildnings fabriks processen. När du har skapat ett labb, se till att spara namnet eftersom du behöver det senare.

## <a name="scripts-and-templates"></a>Skript och mallar
Nästa steg i att anta avbildnings fabriken för ditt team är att förstå vad som är tillgängligt. Avbildnings fabriks skripten och mallarna är tillgängliga offentligt i [DevTest Labs GitHub lagrings platsen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Här är en disposition av delarna:

- Avbildnings fabrik. Det är rotmappen.
    - Inställningarna. Indata till avbildnings fabriken
        - GoldenImages. Den här mappen innehåller JSON-filer som representerar definitionerna för anpassade avbildningar.
        - Labs.jspå. Fil där team registrerar sig för att ta emot vissa anpassade avbildningar.
- Alfabet. Avbildnings fabrikens motor.

Artiklarna i det här avsnittet innehåller mer information om dessa skript och mallar.

## <a name="create-an-azure-devops-team-project"></a>Skapa ett Azure DevOps team-projekt
Med Azure DevOps kan du lagra käll koden, köra Azure PowerShell på ett och samma ställe. Du kan schemalägga återkommande körningar så att avbildningarna hålls aktuella. Det finns funktioner för att logga resultaten för att diagnostisera eventuella problem.  Att använda Azure DevOps är inte ett krav, men du kan använda vilket nät/vilken motor som helst som kan ansluta till Azure och köra Azure PowerShell.

Hoppa över det här steget om du har ett befintligt DevOps-konto eller projekt som du vill använda i stället.

Kom igång genom att skapa ett kostnads fritt konto i Azure DevOps. Besök https://www.visualstudio.com/ och välj **Kom igång kostnads fritt** direkt under **Azure DevOps** (tidigare VSTS). Du måste välja ett unikt konto namn och kontrol lera att du väljer att hantera kod med git. När det har skapats sparar du webb adressen till ditt team projekt. Här är en exempel-URL: `https://<accountname>.visualstudio.com/MyFirstProject` .

## <a name="check-in-the-image-factory-to-git"></a>Checka in image Factory till git
Alla PowerShell, mallar och konfiguration för avbildnings fabriken finns i den [offentliga DevTest Labs GitHub lagrings platsen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Det snabbaste sättet att hämta koden i det nya team projektet är att importera en lagrings plats. Detta hämtar hela DevTest Labs-databasen (så att du får extra dokument och exempel).

1. Besök det Azure DevOps-projekt som du skapade i föregående steg (URL: en ser ut som **https: \/ / \<accountname> . VisualStudio.com/MyFirstProject**).
2. Välj **Importera en lagrings plats**.
3. Ange **klon-URL: en** för DevTest Labs-lagrings platsen: `https://github.com/Azure/azure-devtestlab` .
4. Välj **Importera**.

    ![Importera git-lagrings platsen](./media/set-up-devops-lab/import-git-repo.png)

Om du bara vill kontrol lera exakt vad som behövs (avbildningen Factory-filerna) följer du stegen [här](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) för att klona git-lagrings platsen och push-överför bara de filer som finns i katalogen **scripts/ImageFactory** .

## <a name="create-a-build-and-connect-to-azure"></a>Skapa en build och Anslut till Azure
Nu har du källfiler lagrade i en git-lagrings platsen i Azure DevOps. Nu måste du konfigurera en pipeline för att köra Azure PowerShell. Det finns många alternativ för att utföra dessa steg. I den här artikeln använder du Bygg definitionen för enkelhetens skull, men det fungerar med DevOps build, DevOps release (en eller flera miljöer), andra körnings motorer som Windows Task Scheduler eller något annat nät som kan köra Azure PowerShell.

> [!NOTE]
> En viktig punkt för att komma ihåg att några av PowerShell-filerna tar lång tid att köra när det finns många anpassade avbildningar (10 +) att skapa. De kostnads fria DevOps build/release-agenterna har en tids gräns på 30 minuter, så du kan inte använda den kostnads fria, värdbaserade agenten när du börjar skapa flera avbildningar. Denna timeout-utmaning gäller det som du väljer att använda, det är bäst att kontrol lera att du kan utöka de vanligaste tids gränserna för tids krävande Azure PowerShell skript. Om du använder Azure DevOps kan du antingen använda betalda värdbaserade agenter eller använda din egen build-agent.

1. Starta genom att välja **Konfigurera build** på Start sidan för ditt DevOps-projekt:

    ![Installations knappen](./media/set-up-devops-lab/setup-build-button.png)
2. Ange ett **namn** på versionen (till exempel: skapa och leverera avbildningar till DevTest Labs).
3. Välj en **Tom** build-definition och välj **Använd** för att skapa din version.
4. I det här skedet kan du välja **värd** för build-agenten.
5. **Spara** build-definitionen.

    ![Build-definition](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Konfigurera Bygg variablerna
För att förenkla kommando rads parametrarna kapslar du in de nyckel värden som driver avbildnings fabriken till en uppsättning med build-variabler. Välj fliken **variabler** så visas en lista över flera standardvariabler. Här är listan med variabler som du kan ange i Azure-DevOps:


| Variabelnamn | Värde | Obs! |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Detta är den fullständiga sökvägen i lagrings platsen till mappen **konfiguration** . Om du har importerat hela lagrings platsen ovan är värdet till vänster rätt. Uppdatera annars så att den pekar på konfigurations platsen. |
| DevTestLabName | MyImageFactory | Namnet på labbet i Azure DevTest Labs används som fabrik för att skapa avbildningar. Om du inte har ett kan du skapa en. Se till att labbet finns i samma prenumeration som tjänst slut punkten har åtkomst till. |
| ImageRetention | 1 | Det antal avbildningar som du vill spara av varje typ. Ange standardvärdet till 1. |
| MachinePassword | ******* | Det inbyggda administratörs konto lösen ordet för de virtuella datorerna. Det här är ett tillfälligt konto, så se till att det är säkert. Välj den lilla hänglås ikonen till höger för att säkerställa att det är en säker sträng. |
| MachineUserName | ImageFactoryUser | Det inbyggda användar namnet för administratörs kontot för de virtuella datorerna. Detta är ett tillfälligt konto. |
| StandardTimeoutMinutes | 30 | Tids gränsen bör vänta på vanliga Azure-åtgärder. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | ID: t för prenumerationen där labbet finns och som tjänst slut punkten har åtkomst till. |
| VMSize | Standard_A3 | Storleken på den virtuella dator som ska användas för steget **skapa** . De virtuella datorerna som skapats är tillfälliga. Storleken måste vara den som är [aktive rad för labbet](devtest-lab-set-lab-policy.md). Bekräfta att det finns tillräckligt med [kvot för prenumerations kärnor](../azure-resource-manager/management/azure-subscription-service-limits.md).

![Bygg variabler](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Anslut till Azure
Nästa steg är att konfigurera tjänstens huvud namn. Detta är en identitet i Azure Active Directory som gör att DevOps-agenten kan köras i Azure för användarens räkning. Börja med att lägga till dig första Azure PowerShell build-steget för att konfigurera det.

1. Välj **Lägg till aktivitet**.
2. Sök efter **Azure PowerShell**.
3. När du har hittat det väljer du **Lägg** till för att lägga till uppgiften i versionen. När du gör det visas uppgiften på den vänstra sidan som lagts till.

![Konfigurera PowerShell-steg](./media/set-up-devops-lab/set-up-powershell-step.png)

Det snabbaste sättet att konfigurera ett huvud namn för tjänsten är att låta Azure DevOps göra det åt oss.

1. Välj den **uppgift** som du nyss lade till.
2. För **Anslutnings typ för Azure** väljer du **Azure Resource Manager**.
3. Välj länken **Hantera** för att konfigurera tjänstens huvud namn.

Se det här [blogginlägget](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) för mer information. När du väljer länken **Hantera** hamnar du på rätt plats i DevOps (andra skärm bilden i blogg inlägget) för att konfigurera anslutningen till Azure. Se till att välja **Azure Resource Manager tjänst slut punkt** när du konfigurerar den här inställningen.

## <a name="complete-the-build-task"></a>Slutför Bygg aktiviteten
Om du väljer build-uppgiften visas all information i den högra rutan som ska fyllas i.

1. Börja med att namnge build-uppgiften: **skapa Virtual Machines**.
2. Välj **tjänstens huvud namn** som du skapade genom att välja **Azure Resource Manager**
3. Välj **tjänstens slut punkt**.
4. För **skript Sök väg** väljer du **... (tre punkter)** till höger.
5. Navigera till **MakeGoldenImageVMs.ps1** skript.
6. Skript parametrar bör se ut så här: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Slutför build-definitionen](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Köa versionen
Vi ska kontrol lera att allt är korrekt konfigurerat genom att köa en ny version. När versionen körs växlar du till [Azure Portal](https://portal.azure.com) och väljer på **alla Virtual Machines** i Image Factory-labbet för att bekräfta att allt fungerar som det ska. Du bör se tre virtuella datorer som skapas i labbet.

![Virtuella datorer i labbet](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Nästa steg
Det första steget i att konfigurera avbildnings fabriken baserat på Azure DevTest Labs har slutförts. I nästa artikel i serien får du de virtuella datorerna generaliserade och sparade i anpassade avbildningar. Sedan har de distribuerats till alla andra labb. Se nästa artikel i serien: [Spara anpassade bilder och distribuera dem till flera labb](image-factory-save-distribute-custom-images.md).
