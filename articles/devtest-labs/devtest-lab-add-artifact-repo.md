---
title: Lägga till en git-lagringsplats i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till en GitHub-eller Azure DevOps-tjänst git-lagringsplats för din anpassade artefakter-källa i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 225aae5e0a017b711f29a47829b06f8bb7a6a8ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483150"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Lägg till en git-lagringsplats för att lagra anpassade artefakter och Resource Manager-mallar

Du kan [skapa anpassade artefakter](devtest-lab-artifact-author.md) för de virtuella datorerna i labbet eller [använda Azure Resource Manager mallar för att skapa en anpassad test miljö](devtest-lab-create-environment-from-arm.md). Du måste lägga till en privat git-lagringsplats för de artefakter eller Resource Manager-mallar som ditt team skapar. Lagrings platsen kan ligga på [GitHub](https://github.com) eller på [Azure DevOps-tjänster](https://visualstudio.com).

Vi erbjuder ett [GitHub-lager med artefakter](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) som du kan distribuera i befintligt skick, eller så kan du anpassa dem till dina labb. När du anpassar eller skapar en artefakt kan du inte lagra artefakten i den offentliga lagrings platsen. Du måste skapa en egen privat lagrings platsen för anpassade artefakter och för artefakter som du skapar. 

När du skapar en virtuell dator kan du spara Resource Manager-mallen, anpassa den om du vill och sedan använda den senare för att skapa fler virtuella datorer. Du måste skapa en egen privat lagrings plats för att lagra dina anpassade Resource Manager-mallar.  

* Information om hur du skapar en GitHub-lagringsplats finns i [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Information om hur du skapar ett Azure DevOps Services-projekt som har en git-lagringsplats finns i [ansluta till Azure DevOps Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Följande figur är ett exempel på hur en lagrings plats med artefakter kan se ut i GitHub:  

![Exempel på GitHub artefakter lagrings platsen](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Hämta information om lagrings platsen och autentiseringsuppgifterna
Om du vill lägga till en lagrings plats i labbet hämtar du först viktig information från din lagrings plats. I följande avsnitt beskrivs hur du får nödvändig information för databaser som finns på GitHub-eller Azure DevOps-tjänster.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Hämta URL för kloning av GitHub-lagringsplats och personlig åtkomst-token

1. Gå till start sidan för GitHub-lagringsplatsen som innehåller definitionerna för artefakt-eller Resource Manager-mallen.
2. Välj **Klona eller ladda ned**.
3. Kopiera URL: en till Urklipp genom att välja URL-knappen **https-kloning** . Spara URL: en för senare användning.
4. I det övre högra hörnet av GitHub väljer du profil avbildningen och väljer sedan **Inställningar**.
5. I menyn **personliga inställningar** till vänster väljer du **personliga åtkomsttoken**.
6. Välj **generera ny token**.
7. På sidan **ny personlig åtkomst-token** under **token-Beskrivning** anger du en beskrivning. Godkänn standard objekt under **Välj omfång** och välj sedan **skapa token**.
8. Spara den genererade token. Du använder token senare.
9. Stäng GitHub.   
10. Fortsätt till avsnittet [Anslut ditt labb till databasen](#connect-your-lab-to-the-repository) .

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Hämta URL för Azure databaser-kloning och personlig åtkomst-token

1. Gå till start sidan för din grupp samling (till exempel `https://contoso-web-team.visualstudio.com` ) och välj sedan ditt projekt.
2. På projektets start sida väljer du **kod**.
3. Om du vill visa klon-URL: en på sidan projekt **kod** väljer du **klona**.
4. Spara URL: en. Du använder URL: en senare.
5. Om du vill skapa en personlig åtkomsttoken går du till den nedrullningsbara menyn användar konto och väljer **min profil**.
6. På sidan profil information väljer du **säkerhet**.
7. På fliken **säkerhet** väljer du **Lägg till**.
8. På sidan **skapa en personlig** åtkomsttoken:
   1. Ange en **Beskrivning** för token.
   2. I listan **förfaller i** väljer du **180 dagar**.
   3. I listan **konton** väljer du **alla tillgängliga konton**.
   4. Välj alternativet **Skriv skydd** .
   5. Välj **skapa token**.
9. Den nya token visas i listan med **personliga åtkomst-token** . Välj **Kopiera token** och spara sedan token-värdet för senare användning.
10. Fortsätt till avsnittet [Anslut ditt labb till databasen](#connect-your-lab-to-the-repository) .

## <a name="connect-your-lab-to-the-repository"></a>Anslut ditt labb till lagrings platsen
1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **fler tjänster** och välj sedan **DevTest Labs** i listan över tjänster.
3. I listan med labb väljer du ditt labb. 
4. Välj **konfiguration och principer**  >  **databaser**  >  **+ Lägg till**.

    ![Knappen Lägg till lagrings plats](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. På sidan andra **databaser** anger du följande information:
   1. **Namn**. Ange ett namn för lagrings platsen.
   2. **Git Clone-URL**. Ange den git HTTPS-klon-URL som du kopierade tidigare från antingen GitHub eller Azure DevOps Services.
   3. **Gren**. Ange grenen för att hämta dina definitioner.
   4. **Personlig åtkomsttoken**. Ange den personliga åtkomsttoken som du tidigare fick från antingen GitHub eller Azure DevOps Services.
   5. **Mappsökvägar.** Ange minst en mappsökväg i förhållande till klon-URL: en som innehåller dina definitioner för artefakt-eller Resource Manager-mallar. När du anger en under katalog kontrollerar du att du inkluderar snedstrecket i mappsökvägen.

      ![Områden i databaser](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Välj **Spara**.

### <a name="related-blog-posts"></a>Relaterade blogg inlägg
* [Felsöka fel i artefakter i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Anslut en virtuell dator till en befintlig Active Directory domän med hjälp av en Resource Manager-mall i DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat din privata git-lagringsplats kan du göra något av följande, beroende på dina behov:
* Lagra dina [anpassade artefakter](devtest-lab-artifact-author.md). Du kan använda dem senare för att skapa nya virtuella datorer.
* [Skapa miljöer med flera virtuella datorer och PaaS-resurser med hjälp av Resource Manager-mallar](devtest-lab-create-environment-from-arm.md). Sedan kan du lagra mallarna i din privata lagrings platsen.

När du skapar en virtuell dator kan du kontrol lera att artefakterna eller mallarna läggs till i git-lagringsplatsen. De är omedelbart tillgängliga i listan över artefakter eller mallar. Namnet på din privata lagrings platsen visas i kolumnen som anger källan. 
