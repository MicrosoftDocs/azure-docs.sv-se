---
title: Käll kontroll i Synapse Studio
description: Lär dig hur du konfigurerar käll kontroll i Azure Synapse Studio
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 3564609d869bef090f0a3db5e6040ba0f5ad80b0
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796964"
---
# <a name="source-control-in-azure-synapse-studio"></a>Käll kontroll i Azure Synapse Studio

Som standard redigerar Azure Synapse Studio direkt mot Synapse-tjänsten. Om du behöver samar beta med Git för käll kontroll gör Synapse Studio att du kan koppla din arbets yta till en git-lagringsplats, Azure DevOps eller GitHub. 

Den här artikeln beskriver hur du konfigurerar och arbetar i en Synapse-arbetsyta med git-lagringsplats aktiverat. Och vi markerar också några metod tips och en fel söknings guide.

> [!NOTE]
> Azure Synapse Studio git-integrering är inte tillgänglig i Azure Government molnet.

## <a name="configure-git-repository-in-synapse-studio"></a>Konfigurera git-lagringsplatsen i Synapse Studio 

När du har startat Synapse Studio kan du konfigurera en git-lagringsplats i din arbets yta. En Synapse Studio-arbetsyta kan bara kopplas till en git-lagringsplats i taget. 

### <a name="configuration-method-1-global-bar"></a>Konfigurations metod 1: globalt fält

I det globala fältet i Synapse Studio väljer du List rutan **Synapse Live** och väljer sedan **Konfigurera kod lagrings plats**.

![Konfigurera kod lagrings inställningarna från redigering](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Konfigurations metod 2: hantera hubb

Gå till hantera hubben för Synapse Studio. Välj **git-konfiguration** i avsnittet **käll kontroll** . Om du inte har någon databas ansluten klickar du på **Konfigurera**.

![Konfigurera kod lagrings inställningarna från hanterings hubben](media/configure-repo-2.png)

> [!NOTE]
> Användare som beviljats som Workspace-deltagare, ägare eller högre nivå roller kan konfigurera, redigera inställning och koppla från git-lagringsplatsen i Azure Synapse Studio 

Du kan ansluta antingen Azure DevOps eller GitHub git-lagringsplatsen på din arbets yta.

## <a name="connect-with-azure-devops-git"></a>Ansluta till Azure DevOps git 

Du kan associera en Synapse-arbetsyta med en Azure DevOps-lagringsplats för käll kontroll, samarbete, versions hantering och så vidare. Om du inte har en Azure DevOps-lagringsplats följer du [de här anvisningarna](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) för att skapa dina lagrings resurser.

### <a name="azure-devops-git-repository-settings"></a>Inställningar för Azure DevOps git-lagringsplats

När du ansluter till git-lagringsplatsen väljer du först din databas typ som Azure DevOps git och väljer sedan en Azure AD-klient i list rutan och klickar på **Fortsätt**.

![Konfigurera kod lagrings inställningarna](media/connect-with-azuredevops-repo-selected.png)

I konfigurations fönstret visas följande git-inställningar för Azure DevOps:

| Inställning | Beskrivning | Värde |
|:--- |:--- |:--- |
| **Typ av databas** | Typen av Azure databaser Code-lagringsplatsen.<br/> | Azure DevOps git eller GitHub |
| **Azure Active Directory** | Ditt Azure AD-klient namn. | `<your tenant name>` |
| **Azure DevOps-konto** | Ditt Azure databaser-organisations namn. Du kan hitta ditt Azure databaser-organisations namn på `https://{organization name}.visualstudio.com` . Du kan [Logga in på din Azure databaser-organisation](https://www.visualstudio.com/team-services/git/) för att få åtkomst till din Visual Studio-profil och se dina databaser och projekt. | `<your organization name>` |
| **ProjectName** | Ditt Azure databaser-projekt namn. Du kan hitta ditt Azure databaser-projekts namn på `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **RepositoryName** | Ditt namn på din Azure databaser Code-lagringsplats. Azure databaser-projekt innehåller git-lagringsplatser för att hantera din käll kod när projektet växer. Du kan skapa en ny databas eller använda en befintlig databas som redan finns i ditt projekt. | `<your Azure Repos code repository name>` |
| **Samarbets gren** | Din Azure databaser Collaboration-gren som används för publicering. Som standard används dess `master` . Ändra den här inställningen om du vill publicera resurser från en annan gren. Du kan välja befintliga grenar eller skapa nya | `<your collaboration branch name>` |
| **Rotmapp** | Rotmappen i din Azure databaser-samarbets gren. | `<your root folder name>` |
| **Importera befintliga resurser till lagrings platsen** | Anger om befintliga resurser ska importeras från Synapse Studio till en Azure databaser git-lagringsplats. Markera kryss rutan om du vill importera arbets ytans resurser (förutom pooler) till den associerade git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt. När den här rutan inte är markerad importeras inte de befintliga resurserna. | Markerat (standard) |
| **Importera resurs till den här grenen** | Välj vilken gren som resurserna (SQL-skript, Notebook, Spark jobb definition, data uppsättning, data flöde osv.) importeras till. 

Du kan också använda databas länken för att snabbt peka på den git-lagringsplats som du vill ansluta till. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Använd en annan Azure Active Directory klient

Azure databaser git-lagrings platsen kan finnas i en annan Azure Active Directory klient. För att kunna ange en annan Azure AD-klientorganisation måste du ha administratörsbehörighet för den prenumeration som du använder. Mer information finns i [ändra prenumerations administratör](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)

> [!IMPORTANT]
> För att ansluta till en annan Azure Active Directory måste användaren som är inloggad vara en del av Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Använd din personliga Microsoft-konto

Om du vill använda en personlig Microsoft-konto för git-integrering kan du länka din personliga Azure-lagrings platsen till din organisations Active Directory.

1. Lägg till din personliga Microsoft-konto till din organisations Active Directory som gäst. Mer information finns i [Lägg till Azure Active Directory B2B-samarbets användare i Azure Portal](../../active-directory/external-identities/add-users-administrator.md).

2. Logga in på Azure Portal med din personliga Microsoft-konto. Växla sedan till din organisations Active Directory.

3. Gå till avsnittet Azure-DevOps där du nu ser din personliga lagrings platsen. Välj lagrings platsen och Anslut med Active Directory.

Efter dessa konfigurations steg är din personliga lagrings platsen tillgänglig när du konfigurerar git-integrering i Synapse Studio.

Mer information om hur du ansluter Azure-databaser till din organisations Active Directory finns i [ansluta din organisation till Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="connect-with-github"></a>Anslut med GitHub 

 Du kan associera en arbets yta med en GitHub-lagringsplats för käll kontroll, samarbete, versions hantering. Om du inte har ett GitHub-konto eller en lagrings plats, följer du [dessa anvisningar](https://github.com/join) för att skapa dina resurser.

GitHub-integreringen med Synapse Studio stöder både offentliga GitHub (det vill säga [https://github.com](https://github.com) ) och GitHub Enterprise. Du kan använda både offentliga och privata GitHub-databaser så länge du har Läs-och Skriv behörighet till lagrings platsen i GitHub.

### <a name="github-settings"></a>GitHub-inställningar

När du ansluter till git-lagringsplatsen väljer du först din databas typ som GitHub och anger sedan ditt GitHub-konto eller GitHub Enterprise Server-URL om du använder GitHub Enterprise Server och klickar på **Fortsätt**.

![Inställningar för GitHub-lagringsplats](media/connect-with-github-repo-1.png)

I konfigurations fönstret visas följande inställningar för GitHub-lagringsplats:

| **Inställning** | **Beskrivning**  | **Värde**  |
|:--- |:--- |:--- |
| **Typ av databas** | Typen av Azure databaser Code-lagringsplatsen. | GitHub |
| **Använda GitHub Enterprise** | Kryss ruta för att välja GitHub Enterprise | omarkerat (standard) |
| **GitHub Enterprise-URL** | GitHub Enterprise-rot-URL (måste vara HTTPS för den lokala GitHub Enterprise Server). Exempel: `https://github.mydomain.com`. Krävs endast om **Använd GitHub Enterprise** är valt | `<your GitHub enterprise url>` |                                                           
| **GitHub-konto** | Namnet på GitHub-kontot. Det här namnet kan hittas från https: \/ /GitHub.com/{account Name}/{repository Name}. Om du navigerar till den här sidan uppmanas du att ange GitHub OAuth-autentiseringsuppgifter för ditt GitHub-konto. | `<your GitHub account name>` |
| **Namn på databas**  | Ditt GitHub kod lagrings namn. GitHub-konton innehåller git-databaser för att hantera din käll kod. Du kan skapa en ny databas eller använda en befintlig databas som redan finns i ditt konto. | `<your repository name>` |
| **Samarbets gren** | Din GitHub Collaboration-gren som används för publicering. Som standard är originalet. Ändra den här inställningen om du vill publicera resurser från en annan gren. | `<your collaboration branch>` |
| **Rotmapp** | Rotmappen i din GitHub-samarbets gren. |`<your root folder name>` |
| **Importera befintliga resurser till lagrings platsen** | Anger om befintliga resurser ska importeras från Synapse Studio till en git-lagringsplats. Markera kryss rutan om du vill importera arbets ytans resurser (förutom pooler) till den associerade git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt. När den här rutan inte är markerad importeras inte de befintliga resurserna. | Vald (standard) |
| **Importera resurs till den här grenen** | Välj vilken gren resurserna (SQL-skript, Notebook, Spark jobb definition, data uppsättning, data flöde osv.) ska importeras. 

### <a name="github-organizations"></a>GitHub-organisationer

Att ansluta till en GitHub-organisation kräver att organisationen ger behörighet till Synapse Studio. En användare med ADMINISTRATÖRs behörighet för organisationen måste utföra stegen nedan.

#### <a name="connecting-to-github-for-the-first-time"></a>Ansluter till GitHub för första gången

Om du ansluter till GitHub från Synapse Studio för första gången, följer du dessa steg för att ansluta till en GitHub organisation.

1. I fönstret git-konfiguration anger du organisations namnet i fältet *GitHub-konto* . En uppvarning om att logga in på GitHub visas. 

1. Logga in med dina användarautentiseringsuppgifter.

1. Du uppmanas att auktorisera Synapse som ett program som kallas *Azure Synapse*. På den här skärmen visas ett alternativ för att bevilja behörighet för Synapse att få åtkomst till organisationen. Om du inte ser alternativet för att bevilja behörighet ber du en administratör att bevilja behörighet manuellt via GitHub.

När du följer de här stegen kan arbets ytan ansluta till både offentliga och privata lagrings platser i din organisation. Om du inte kan ansluta kan du försöka rensa webbläsarens cacheminne och försöka igen.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Redan ansluten till GitHub med ett personligt konto

Om du redan har anslutit till GitHub och bara har beviljats behörighet att komma åt ett personligt konto följer du stegen nedan för att ge behörighet till en organisation.

1. Gå till GitHub och öppna **Inställningar**.

    ![Öppna GitHub-inställningar](media/github-settings.png)

1. Välj **program**. På fliken **auktoriserade OAuth-appar** bör du se *Azure-Synapse*.

    ![Auktorisera OAuth-appar](media/authorize-app.png)

1. Välj *Azure-Synapse* och ge åtkomst till din organisation.

    ![Bevilja organisations behörighet](media/grant-organization-permission.png)

När du har slutfört de här stegen kan arbets ytan ansluta till både offentliga och privata lagrings platser i din organisation.

## <a name="version-control"></a>Versionskontroll

Med versions kontroll system (även kallat _käll kontroll_) kan utvecklare samar beta med kod och spåra ändringar. Käll kontroll är ett viktigt verktyg för projekt med flera utvecklare.

### <a name="creating-feature-branches"></a>Skapa funktions grenar

Varje git-lagringsplats som är associerad med en Synapse Studio har en samarbets gren. ( `main` eller `master` är standard grenen för samarbete). Användare kan också skapa funktions grenar genom att klicka på **+ ny gren** i rutan förgrening. När fönstret ny förgrening visas anger du namnet på din funktions gren.

![Skapa en ny gren](media/create-new-branch.png)

När du är redo att sammanfoga ändringarna från din funktions gren till samarbets grenen klickar du på grenen gren och väljer **skapa pull-begäran**. Den här åtgärden tar dig till git-providern där du kan generera pull-begäranden, göra kod granskningar och slå samman ändringar i samarbets grenen. Du får bara publicera till Synapse-tjänsten från samarbets grenen. 

![Skapa en ny pull-begäran](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurera publicerings inställningar

Som standard genererar Synapse Studio mallarna för arbets ytor och sparar dem i en gren som kallas `workspace_publish` . Om du vill konfigurera en anpassad publicerings gren lägger du till en `publish_config.json` fil i rotmappen i samarbets grenen. Vid publicering läser Synapse Studio filen, letar efter fältet `publishBranch` och sparar mallfiler för arbets ytor på den angivna platsen. Om grenen inte finns, skapas den automatiskt av Synapse Studio. Och exempel på hur den här filen ser ut som är nedan:

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure Synapse Studio kan bara ha en publicerings gren i taget. När du anger en ny publicerings gren skulle den tidigare publicerings grenen inte tas bort. Om du vill ta bort den tidigare publicerings grenen tar du bort den manuellt.


### <a name="publish-code-changes"></a>Publicera kod ändringar

När du har sammanfogat ändringar i samarbets grenen klickar du på **publicera** för att manuellt publicera dina kod ändringar i samarbets grenen till Synapse-tjänsten.

![Publicera ändringar](media/gitmode-publish.png)

En sida i fönstret öppnas där du bekräftar att publicerings grenen och de väntande ändringarna är korrekta. När du har verifierat ändringarna klickar du på **OK** för att bekräfta publiceringen.

![Bekräfta rätt publicerings gren](media/publish-change.png)

> [!IMPORTANT]
> Samarbets grenen är inte representativ för vad som distribueras i tjänsten. Ändringarna i samarbets grenen *måste* publiceras manuellt.

## <a name="switch-to-a-different-git-repository"></a>Växla till en annan git-lagringsplats

Om du vill växla till en annan git-lagringsplats går du till konfigurations sidan för git i hanterings navet under **käll kontroll**. Välj **Koppla från**. 

![Git-ikon](media/remove-repository.png)

Ange arbets ytans namn och klicka på **Koppla från** för att ta bort git-lagringsplatsen som är kopplad till din arbets yta.

När du har tagit bort associationen med den aktuella lagrings platsen kan du konfigurera git-inställningarna så att de använder en annan lagrings platsen och sedan importera befintliga resurser till den nya lagrings platsen.

> [!IMPORTANT]
> Om du tar bort git-konfigurationen från en arbets yta tas inte något bort från lagrings platsen. Synapse-arbetsytan kommer att innehålla alla publicerade resurser. Du kan fortsätta att redigera arbets ytan direkt mot tjänsten.

## <a name="best-practices-for-git-integration"></a>Metod tips för git-integrering

-   **Behörigheter**. När du har en git-lagringsplats ansluten till din arbets yta, kommer alla som har åtkomst till din git-lagrings platsen med vilken roll som helst på arbets ytan att kunna uppdatera artefakter, som SQL-skript, Notebook, Spark jobb definition, data uppsättning, data flöde och pipeline i git-läge. Normalt vill du inte att alla grupp medlemmar ska ha behörighet att uppdatera arbets ytan. Bevilja endast git-lagringsplatsen behörighet till Synapse-ytans artefakt författare. 
-   **Samarbete**. Vi rekommenderar att du inte tillåter direkta incheckningar till samarbets grenen. Den här begränsningen kan hjälpa till att förhindra buggar som varje incheckning går igenom en gransknings process för pull-begäran som beskrivs i [skapa funktions grenar](source-control.md#creating-feature-branches).
-   **Synapse live-läge**. När du har publicerat i git-läge visas alla ändringar i Synapse live-läge. I Synapse live-läge är publicering inaktiverat. Och du kan visa, köra artefakter i Live-läge om du har beviljats rätt behörighet. 
-   **Redigera artefakter i Studio**. Synapse Studio är den enda plats där du kan aktivera käll kontroll för arbets ytan och synkronisera ändringar till git automatiskt. Alla ändringar via SDK, PowerShell, kommer inte att synkroniseras till git. Vi rekommenderar att du alltid redigerar artefakt i Studio när git är aktiverat.

## <a name="troubleshooting-git-integration"></a>Felsöka git-integrering

### <a name="access-to-git-mode"></a>Åtkomst till git-läge 

Om du har beviljats behörighet till den git-lagringsplatsen GitHub som är länkad till din arbets yta, men du inte har åtkomst till git-läge: 

1. Rensa cacheminnet och uppdatera sidan. 

1. Logga in ditt GitHub-konto.

### <a name="stale-publish-branch"></a>Inaktuell publicerings gren

Om publicerings grenen inte är synkroniserad med samarbets grenen och innehåller inaktuella resurser trots en senaste publicering, prova följande steg:

1. Ta bort din aktuella git-lagringsplats

1. Konfigurera om git med samma inställningar, men se till att **Importera befintliga resurser till databasen** är markerat och välj samma gren.  

1. Skapa en pull-begäran för att sammanslå ändringarna till samarbetsgrenen 

## <a name="unsupported-features"></a>Funktioner som inte stöds

- Synapse Studio tillåter inte körsbär-plockning av incheckningar eller selektiv publicering av resurser. 
- Synapse Studio stöder inte ett anpassat inchecknings meddelande.
- Efter design kommer borttagnings åtgärder i Studio att allokeras till git direkt

## <a name="next-steps"></a>Nästa steg

* För att implementera kontinuerlig integrering och distribution, se [kontinuerlig integrering och leverans (CI/CD)](continuous-integration-deployment.md).