---
title: Källkontroll i Synapse Studio
description: Lär dig hur du konfigurerar källkontroll i Azure Synapse Studio
author: liud
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 8f1b459c2644472463004c231f5827ff653d2da1
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567850"
---
# <a name="source-control-in-azure-synapse-studio"></a>Källkontroll i Azure Synapse Studio

Som standard Azure Synapse Studio-författare direkt mot Synapse-tjänsten. Om du behöver samarbeta med Git för källkontroll kan Synapse Studio associera din arbetsyta med en Git-lagringsplats, Azure DevOps eller GitHub. 

Den här artikeln beskriver hur du konfigurerar och arbetar på en Synapse-arbetsyta med git-lagringsplatsen aktiverad. Vi tar även upp några metodtips och en felsökningsguide.

> [!NOTE]
> Azure Synapse Studio git-integrering är inte tillgängligt i Azure Government Cloud.

## <a name="configure-git-repository-in-synapse-studio"></a>Konfigurera Git-lagringsplatsen i Synapse Studio 

När du har Synapse Studio kan du konfigurera en git-lagringsplats på din arbetsyta. En Synapse Studio-arbetsyta kan bara associeras med en git-lagringsplats i taget. 

### <a name="configuration-method-1-global-bar"></a>Konfigurationsmetod 1: globalt fält

I det Synapse Studio globala fältet väljer du listrutan **Synapse Live** och sedan **Konfigurera koddatabas.**

![Konfigurera inställningarna för koddatabasen från redigering](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Konfigurationsmetod 2: Hantera hubb

Gå till hantera hubben för Synapse Studio. Välj **Git-konfiguration** i **avsnittet Källkontroll.** Om du inte har någon ansluten lagringsplats klickar du på **Konfigurera**.

![Konfigurera inställningarna för koddatabasen från hanteringshubben](media/configure-repo-2.png)

> [!NOTE]
> Användare som beviljas rollen som arbetsytedeltagare, ägare eller högre nivå kan konfigurera, redigera inställning och koppla från git-lagringsplatsen i Azure Synapse studio 

Du kan ansluta antingen Azure DevOps- eller GitHub git-lagringsplatsen på din arbetsyta.

## <a name="connect-with-azure-devops-git"></a>Ansluta med Azure DevOps Git 

Du kan associera en Synapse-arbetsyta med en Azure DevOps-lagringsplats för källkontroll, samarbete, versionshantering och så vidare. Om du inte har en Azure DevOps-lagringsplats följer du dessa [anvisningar för att](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) skapa dina lagringsplatsresurser.

### <a name="azure-devops-git-repository-settings"></a>Inställningar för Git-lagringsplatsen för Azure DevOps

När du ansluter till git-lagringsplatsen väljer du först din lagringsplatstyp som Azure DevOps git. Välj sedan en Azure AD-klientorganisation i listrutan och klicka **på Fortsätt.**

![Konfigurera inställningarna för koddatabasen](media/connect-with-azuredevops-repo-selected.png)

Konfigurationsfönstret visar följande Git-inställningar för Azure DevOps:

| Inställning | Beskrivning | Värde |
|:--- |:--- |:--- |
| **Typ av lagringsplats** | Typ av kodlagringsplats för Azure Repos.<br/> | Azure DevOps Git eller GitHub |
| **Azure Active Directory** | Namnet på din Azure AD-klientorganisation. | `<your tenant name>` |
| **Azure DevOps-konto** | Ditt organisationsnamn för Azure Repos. Du hittar organisationsnamnet för Azure Repos på `https://{organization name}.visualstudio.com` . Du kan [logga in på din Azure Repos-organisation](https://www.visualstudio.com/team-services/git/) för att få åtkomst Visual Studio din profil och se dina lagringsplatsen och projekten. | `<your organization name>` |
| **ProjectName** | Namnet på ditt Azure Repos-projekt. Du hittar ditt Azure Repos-projektnamn på `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **RepositoryName** | Namnet på din Azure Repos-kodlagringsplats. Azure Repos-projekt innehåller Git-lagringsplatsen för att hantera källkoden när projektet växer. Du kan skapa en ny lagringsplats eller använda en befintlig lagringsplats som redan finns i projektet. | `<your Azure Repos code repository name>` |
| **Samarbetsgren** | Din Azure Repos-samarbetsgren som används för publicering. Som standard är dess `master` . Ändra den här inställningen om du vill publicera resurser från en annan gren. Du kan välja befintliga grenar eller skapa nya | `<your collaboration branch name>` |
| **Rotmappen** | Rotmappen i din Azure Repos-samarbetsgren. | `<your root folder name>` |
| **Importera befintliga resurser till lagringsplatsen** | Anger om du vill importera befintliga resurser från Synapse Studio till en Azure Repos Git-lagringsplats. Markera kryssrutan för att importera dina arbetsyteresurser (utom pooler) till den associerade Git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt. När den här rutan inte är markerad importeras inte befintliga resurser. | Markerad (standard) |
| **Importera resursen till den här grenen** | Välj vilken gren resurserna (sql script, notebook, spark-jobbdefinition, datauppsättning, dataflöde osv.) importeras till. 

Du kan också använda länken till lagringsplatsen för att snabbt peka på den git-lagringsplats som du vill ansluta till. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Använda en annan Azure Active Directory klientorganisation

Git-lagringsplatsen för Azure-lagringsplatsen kan finnas i en Azure Active Directory klientorganisation. För att kunna ange en annan Azure AD-klientorganisation måste du ha administratörsbehörighet för den prenumeration som du använder. Mer information finns i Ändra [prenumerationsadministratör](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)

> [!IMPORTANT]
> Om du vill ansluta Azure Active Directory en annan användare måste den inloggade användaren vara en del av den Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Använda din personliga Microsoft-konto

Om du vill använda en Microsoft-konto för Git-integrering kan du länka din personliga Azure-lagringsplatsen till din organisations Active Directory.

1. Lägg till dina Microsoft-konto till din organisations Active Directory som gäst. Mer information finns i Lägga [till Azure Active Directory B2B-samarbetsanvändare i Azure Portal](../../active-directory/external-identities/add-users-administrator.md).

2. Logga in på Azure Portal med ditt personliga Microsoft-konto. Växla sedan till organisationens Active Directory.

3. Gå till avsnittet Azure DevOps, där du nu ser din personliga lagringsplatsen. Välj lagringsplatsen och anslut med Active Directory.

Efter de här konfigurationsstegen är din personliga lagringsplatsen tillgänglig när du ställer in Git-integrering i Synapse Studio.

Mer information om hur du ansluter Azure-lagringsplatsen till din organisations Active Directory finns i [Ansluta din organisation till Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="connect-with-github"></a>Ansluta med GitHub 

 Du kan associera en arbetsyta med en GitHub-lagringsplats för källkontroll, samarbete och versionshantering. Om du inte har ett GitHub-konto eller en lagringsplats följer du dessa [anvisningar för](https://github.com/join) att skapa dina resurser.

GitHub-integreringen med Synapse Studio stöder både offentlig GitHub (det vill [https://github.com](https://github.com) säga ) och GitHub Enterprise. Du kan använda både offentliga och privata GitHub-lagringsplatsen så länge du har läs- och skrivbehörighet till lagringsplatsen i GitHub.

### <a name="github-settings"></a>GitHub-inställningar

När du ansluter till git-lagringsplatsen väljer du först din lagringsplatstyp som GitHub och anger sedan ditt GitHub-konto eller GitHub Enterprise Server-URL om du använder GitHub Enterprise Server och klickar på **Fortsätt.**

![Inställningar för GitHub-lagringsplatsen](media/connect-with-github-repo-1.png)

Konfigurationsfönstret visar följande GitHub-lagringsplatsinställningar:

| **Inställning** | **Beskrivning**  | **Värde**  |
|:--- |:--- |:--- |
| **Typ av lagringsplats** | Typ av Kodlagringsplats för Azure Repos. | GitHub |
| **Använda GitHub Enterprise** | Kryssruta för att välja GitHub Enterprise | omarkerad (standard) |
| **GitHub Enterprise-URL** | Rot-URL:en för GitHub Enterprise (måste vara HTTPS för lokal GitHub Enterprise-server). Exempel: `https://github.mydomain.com`. Krävs endast om **Använd GitHub Enterprise** har valts | `<your GitHub enterprise url>` |                                                           
| **GitHub-konto** | Ditt GitHub-kontonamn. Du hittar det här namnet på https: \/ /github.com/{account name}/{repository name}. När du går till den här sidan uppmanas du att ange autentiseringsuppgifter för GitHub OAuth till ditt GitHub-konto. | `<your GitHub account name>` |
| **Namn på lagringsplats**  | Namnet på din GitHub-koddatabas. GitHub-konton innehåller Git-lagringsplatsen för att hantera din källkod. Du kan skapa en ny lagringsplats eller använda en befintlig lagringsplats som redan finns i ditt konto. | `<your repository name>` |
| **Samarbetsgren** | Din GitHub-samarbetsgren som används för publicering. Som standard dess huvud. Ändra den här inställningen om du vill publicera resurser från en annan gren. | `<your collaboration branch>` |
| **Rotmappen** | Rotmappen i din GitHub-samarbetsgren. |`<your root folder name>` |
| **Importera befintliga resurser till lagringsplatsen** | Anger om du vill importera befintliga resurser från Synapse Studio till en Git-lagringsplats. Markera kryssrutan för att importera dina arbetsyteresurser (utom pooler) till den associerade Git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt. När den här rutan inte är markerad importeras inte de befintliga resurserna. | Vald (standard) |
| **Importera resursen till den här grenen** | Välj vilken gren resurserna (sql-skript, anteckningsbok, spark-jobbdefinition, datauppsättning, dataflöde osv.) ska importeras. 

### <a name="github-organizations"></a>GitHub-organisationer

Anslutning till en GitHub-organisation kräver att organisationen beviljar behörighet att Synapse Studio. En användare med administratörsbehörighet för organisationen måste utföra stegen nedan.

#### <a name="connecting-to-github-for-the-first-time"></a>Ansluta till GitHub för första gången

Om du ansluter till GitHub från Synapse Studio första gången följer du dessa steg för att ansluta till en GitHub-organisation.

1. I fönstret Git-konfiguration anger du organisationsnamnet i fältet *GitHub-konto.* En uppmaning om att logga in på GitHub visas. 

1. Logga in med dina användarautentiseringsuppgifter.

1. Du uppmanas att auktorisera Synapse som ett program med namnet *Azure Synapse*. På den här skärmen visas ett alternativ för att bevilja behörighet för Synapse att komma åt organisationen. Om du inte ser alternativet för att bevilja behörighet ber du en administratör att bevilja behörigheten manuellt via GitHub.

När du följer de här stegen kan arbetsytan ansluta till både offentliga och privata lagringsplatsen i din organisation. Om du inte kan ansluta kan du försöka rensa webbläsarens cacheminne och försöka igen.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Redan ansluten till GitHub med ett personligt konto

Om du redan har anslutit till GitHub och endast beviljats behörighet att komma åt ett personligt konto följer du stegen nedan för att bevilja behörigheter till en organisation.

1. Gå till GitHub och öppna **Inställningar.**

    ![Öppna GitHub-inställningar](media/github-settings.png)

1. Välj **Program**. På fliken **Auktoriserade OAuth-appar** bör du se *Azure Synapse*.

    ![Auktorisera OAuth-appar](media/authorize-app.png)

1. Välj *Azure Synapse* och ge åtkomst till din organisation.

    ![Bevilja organisationsbehörighet](media/grant-organization-permission.png)

När du har slutfört de här stegen kommer arbetsytan att kunna ansluta till både offentliga och privata lagringsplatsen i din organisation.

## <a name="version-control"></a>Versionskontroll

Med versionskontrollsystem (även kallat _källkontroll)_ kan utvecklare samarbeta kring kod och spåra ändringar. Källkontroll är ett viktigt verktyg för projekt med flera utvecklare.

### <a name="creating-feature-branches"></a>Skapa funktionsgrenar

Varje Git-lagringsplats som är associerad med en Synapse Studio har en samarbetsgren. ( `main` eller `master` är standardgrenen för samarbete). Användare kan också skapa funktionsgrenar genom att **klicka på + Ny gren** i listrutan gren. När det nya grenfönstret visas anger du namnet på funktionsgrenen.

![Skapa en ny gren](media/create-new-branch.png)

När du är redo att sammanslå ändringarna från funktionsgrenen till din samarbetsgren klickar du på listrutan gren och väljer **Skapa pull-begäran.** Den här åtgärden tar dig till Git-providern där du kan skicka pull-begäranden, göra kodgranskningar och sammanslå ändringar till din samarbetsgren. Du får bara publicera till Synapse-tjänsten från din samarbetsgren. 

![Skapa en ny pull-begäran](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurera publiceringsinställningar

Som standard genererar Synapse Studio arbetsytemallarna och sparar dem i en gren som heter `workspace_publish` . Om du vill konfigurera en anpassad publiceringsgren lägger du `publish_config.json` till en fil i rotmappen i samarbetsgrenen. När du publicerar Synapse Studio den här filen, söker efter fältet `publishBranch` och sparar arbetsytemallfiler på den angivna platsen. Om grenen inte finns skapar Synapse Studio automatiskt den. Och ett exempel på hur den här filen ser ut finns nedan:

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure Synapse Studio kan bara ha en publiceringsgren i taget. När du anger en ny publiceringsgren tas inte den tidigare publiceringsgrenen bort. Om du vill ta bort den tidigare publiceringsgrenen tar du bort den manuellt.


### <a name="publish-code-changes"></a>Publicera kodändringar

När du har sammanfogat ändringarna  i samarbetsgrenen klickar du på Publicera för att manuellt publicera kodändringarna i samarbetsgrenen till Synapse-tjänsten.

![Publicera ändringar](media/gitmode-publish.png)

Ett sidofönster öppnas där du bekräftar att publiceringsgrenen och väntande ändringar är korrekta. När du har verifierat ändringarna klickar du **på OK** för att bekräfta publiceringen.

![Bekräfta rätt publiceringsgren](media/publish-change.png)

> [!IMPORTANT]
> Samarbetsgrenen är inte representativ för vad som distribueras i tjänsten. Ändringarna i samarbetsgrenen *måste* publiceras manuellt.

## <a name="switch-to-a-different-git-repository"></a>Växla till en annan Git-lagringsplats

Om du vill växla till en annan Git-lagringsplats går du till Git-konfigurationssidan i hanteringshubben under **Källkontroll**. Välj **Koppla från.** 

![Git-ikon](media/remove-repository.png)

Ange namnet på arbetsytan och klicka på **Koppla från för att** ta bort git-lagringsplatsen som är associerad med din arbetsyta.

När du har tagit bort associationen med den aktuella lagringsplatsen kan du konfigurera dina Git-inställningar så att de använder en annan lagringsplatsen och sedan importera befintliga resurser till den nya lagringsplatsen.

> [!IMPORTANT]
> Att ta bort Git-konfiguration från en arbetsyta tar inte bort något från lagringsplatsen. Synapse-arbetsytan innehåller alla publicerade resurser. Du kan fortsätta att redigera arbetsytan direkt mot tjänsten.

## <a name="best-practices-for-git-integration"></a>Metodtips för Git-integrering

-   **Behörigheter**. När du har en git-lagringsplats som är ansluten till din arbetsyta kan alla som har åtkomst till din git-lagringsplats med valfri roll på din arbetsyta uppdatera artefakter som sql script, notebook, spark-jobbdefinition, datauppsättning, dataflöde och pipeline i git-läge. Vanligtvis vill du inte att alla teammedlemmar ska ha behörighet att uppdatera arbetsytan. Bevilja endast behörighet för git-lagringsplats till Synapse-arbetsytans artefaktförfattare. 
-   **Samarbete**. Vi rekommenderar att du inte tillåter direkta incheckningar till samarbetsgrenen. Den här begränsningen kan hjälpa till att förhindra buggar eftersom varje incheckning går igenom en granskningsprocess för pull-begäran som beskrivs i [Skapa funktionsgrenar.](source-control.md#creating-feature-branches)
-   **Synapse-liveläge**. När du har publicerat i git-läge visas alla ändringar i Synapse-liveläge. Publicering är inaktiverat i Synapse-liveläge. Och du kan visa, köra artefakter i liveläge om du har beviljats rätt behörighet. 
-   **Redigera artefakter i Studio**. Synapse Studio är den enda plats där du kan aktivera källkontroll för arbetsytan och synkronisera ändringar till git automatiskt. Ändringar via SDK, PowerShell, synkroniseras inte med git. Vi rekommenderar att du alltid redigerar artefakt i Studio när git är aktiverat.

## <a name="troubleshooting-git-integration"></a>Felsöka Git-integrering

### <a name="access-to-git-mode"></a>Åtkomst till git-läge 

Om du har beviljats behörighet till GitHub Git-lagringsplatsen som är länkad till din arbetsyta, men du inte kan komma åt Git-läge: 

1. Rensa cacheminnet och uppdatera sidan. 

1. Logga in på ditt GitHub-konto.

### <a name="stale-publish-branch"></a>Inaktuell publiceringsgren

Om publiceringsgrenen inte är synkroniserad med samarbetsgrenen och innehåller inaktuella resurser trots en nyligen genomförd publicering kan du prova följande steg:

1. Ta bort din aktuella Git-lagringsplats

1. Konfigurera om Git med samma inställningar, men se till att Importera befintliga resurser till **lagringsplatsen** är markerat och välj samma gren.  

1. Skapa en pull-begäran för att sammanslå ändringarna till samarbetsgrenen 

## <a name="unsupported-features"></a>Funktioner som inte stöds

- Synapse Studio tillåter inte urval av genomföranden eller selektiv publicering av resurser. 
- Synapse Studio stöder inte anpassning av genomför-meddelanden.
- Borttagningsåtgärden i Studio kommer att skickas direkt till git

## <a name="next-steps"></a>Nästa steg

* Information om hur du implementerar kontinuerlig integrering och [distribution finns i Kontinuerlig integrering och leverans (CI/CD).](continuous-integration-deployment.md)