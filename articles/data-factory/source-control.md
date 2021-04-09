---
title: Källkontroll
description: Lär dig hur du konfigurerar käll kontroll i Azure Data Factory
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/26/2021
ms.openlocfilehash: 7691c285bcc1c490878f5055468b0a57b6248679
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719411"
---
# <a name="source-control-in-azure-data-factory"></a>Käll kontroll i Azure Data Factory
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Som standard redigerar Azure Data Factory användar gränssnitts upplevelsen (UX) direkt mot Data Factory-tjänsten. Den här upplevelsen har följande begränsningar:

- Tjänsten Data Factory innehåller inte en lagrings plats för att lagra JSON-entiteterna för dina ändringar. Det enda sättet att spara ändringar är via knappen **publicera alla** och alla ändringar publiceras direkt till Data Factory-tjänsten.
- Tjänsten Data Factory är inte optimerad för samarbete och versions kontroll.
- Den Azure Resource Manager-mall som krävs för att distribuera Data Factory ingår inte.

För att ge en bättre redigerings upplevelse kan Azure Data Factory konfigurera en git-lagringsplats med antingen Azure-databaser eller GitHub. Git är ett versionskontrollsystem som möjliggör enklare spårning och samarbete. Den här artikeln beskriver hur du konfigurerar och arbetar i en git-lagringsplats tillsammans med att markera bästa praxis och en fel söknings guide.

> [!NOTE]
> För Azure Government Cloud är endast GitHub Enterprise tillgängligt.

Om du vill veta mer om hur Azure Data Factory integreras med git kan du läsa 15-minuters självstudie video nedan:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4GNKv]

## <a name="advantages-of-git-integration"></a>Fördelar med Git-integrering

Nedan visas en lista över några av fördelarna git-integrering som ger upphov till redigerings upplevelsen:

-   **Käll kontroll:** När dina data Factory-arbetsbelastningar blir viktiga, vill du integrera din fabrik med Git för att utnyttja flera fördelar med käll kontroll som följande:
    -   Möjlighet att spåra/granska ändringar.
    -   Möjlighet att återställa ändringar som introducerade buggar.
-   **Delvis sparade:** När du redigerar mot Data Factory-tjänsten kan du inte spara ändringarna som ett utkast och alla publiceringar måste klara data fabriks verifieringen. Oavsett om dina pipelines inte är klara eller om du bara vill förlora ändringar om datorn kraschar kan git-integrering användas för att öka förändringar i Data Factory-resurser oavsett vilket tillstånd de är i. Genom att konfigurera en git-lagringsplats kan du spara ändringar, vilket gör att du bara kan publicera när du har testat dina ändringar.
-   **Samarbete och kontroll:** Om flera team medlemmar bidrar till samma fabrik kanske du vill låta dina medarbetare samar beta med varandra via en kod gransknings process. Du kan också konfigurera din fabrik så att alla deltagare inte har samma behörigheter. Vissa team medlemmar kan bara tillåtas att göra ändringar via git och endast vissa personer i teamet får publicera ändringarna i fabriken.
-   **Bättre CI/CD:**  Om du distribuerar till flera miljöer med en [kontinuerlig leverans process](continuous-integration-deployment.md)gör git-integreringen att vissa åtgärder blir enklare. Några av dessa åtgärder är:
    -   Konfigurera din versions pipeline så att den utlöses automatiskt så snart det finns några ändringar som gjorts i din dev-fabrik.
    -   Anpassa egenskaperna i din fabrik som är tillgängliga som parametrar i Resource Manager-mallen. Det kan vara användbart att endast behålla den obligatoriska uppsättningen egenskaper som parametrar och att allt annat är hårdkodat.
-   **Bättre prestanda:** En genomsnittlig fabrik med git-integrering läser in 10 gånger snabbare än en redigering mot Data Factory-tjänsten. Den här prestanda förbättringen beror på att resurser hämtas via git.

> [!NOTE]
> Redigering direkt med Data Factory tjänsten inaktive ras i Azure Data Factory UX när en git-lagringsplats har kon figurer ATS. Ändringar som görs via PowerShell eller ett SDK publiceras direkt till Data Factory tjänsten och anges inte i git.

## <a name="connect-to-a-git-repository"></a>Anslut till en git-lagringsplats

Det finns fyra olika sätt att ansluta en git-lagringsplats till din data fabrik för både Azure databaser och GitHub. När du har anslutit till en git-lagringsplats kan du Visa och hantera din konfiguration i [hanterings navet](author-management-hub.md) under **git-konfiguration** i avsnittet **käll kontroll**

### <a name="configuration-method-1-home-page"></a>Konfigurations metod 1: Start sida

På sidan Azure Data Factory start väljer du **Konfigurera kod lagrings plats**.

![Konfigurera en kod lagrings plats från start sidan](media/author-visually/configure-repo.png)

### <a name="configuration-method-2-authoring-canvas"></a>Konfigurations metod 2: redigera arbets yta

I Azure Data Factory UX-redigerings arbets ytan väljer du den **Data Factory** list rutan och väljer sedan **Konfigurera kod lagrings plats**.

![Konfigurera kod lagrings inställningarna från redigering](media/author-visually/configure-repo-2.png)

### <a name="configuration-method-3-management-hub"></a>Konfigurations metod 3: hanterings hubb

Gå till hanterings hubben i ADF-UX. Välj **git-konfiguration** i avsnittet **käll kontroll** . Om du inte har någon databas ansluten klickar du på **Konfigurera**.

![Konfigurera kod lagrings inställningarna från hanterings hubben](media/author-visually/configure-repo-3.png)

### <a name="configuration-method-4-during-factory-creation"></a>Konfigurations metod 4: när fabrik skapas

När du skapar en ny data fabrik i Azure Portal kan du konfigurera git-lagringsplatsen på fliken **git-konfiguration** .

> [!NOTE]
> När du konfigurerar git i Azure-portalen måste inställningar som projekt namn och lagrings platsen namn anges manuellt i stället för att ingå i en listruta.

![Konfigurera kod lagrings inställningarna från Azure Portal](media/author-visually/configure-repo-4.png)

## <a name="author-with-azure-repos-git-integration"></a>Skapa med Git-integrering för Azure-lagringsplatser

Visuell redigering med Azure databaser git-integrering stöder käll kontroll och samarbete för arbete på dina data Factory-pipeliner. Du kan associera en data fabrik med en Azure databaser git-organisations databas för käll kontroll, samarbete, versions hantering och så vidare. En enda Azure databaser git-organisation kan ha flera databaser, men en Azure databaser git-lagringsplats kan bara associeras med en data fabrik. Om du inte har någon Azure databaser-organisation eller-databas, följer du [dessa anvisningar](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) för att skapa dina resurser.

> [!NOTE]
> Du kan lagra skript-och datafiler i en git-lagringsplats för Azure databaser. Du måste dock ladda upp filerna manuellt till Azure Storage. En Data Factory-pipeline överför inte automatiskt skript eller datafiler som lagras i en git-lagringsplats i Azure databaser till Azure Storage.

### <a name="azure-repos-settings"></a>Inställningar för Azure-databaser

![Konfigurera kod lagrings inställningarna](media/author-visually/repo-settings.png)

I konfigurations fönstret visas följande inställningar för Azure databaser Code-lagringsplatsen:

| Inställning | Beskrivning | Värde |
|:--- |:--- |:--- |
| **Typ av databas** | Typen av Azure databaser Code-lagringsplatsen.<br/> | Azure DevOps git eller GitHub |
| **Azure Active Directory** | Ditt Azure AD-klient namn. | `<your tenant name>` |
| **Azure databaser-organisation** | Ditt Azure databaser-organisations namn. Du kan hitta ditt Azure databaser-organisations namn på `https://{organization name}.visualstudio.com` . Du kan [Logga in på din Azure databaser-organisation](https://www.visualstudio.com/team-services/git/) för att få åtkomst till din Visual Studio-profil och se dina databaser och projekt. | `<your organization name>` |
| **ProjectName** | Ditt Azure databaser-projekt namn. Du kan hitta ditt Azure databaser-projekts namn på `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **RepositoryName** | Ditt namn på din Azure databaser Code-lagringsplats. Azure databaser-projekt innehåller git-lagringsplatser för att hantera din käll kod när projektet växer. Du kan skapa en ny databas eller använda en befintlig databas som redan finns i ditt projekt. | `<your Azure Repos code repository name>` |
| **Samarbets gren** | Din Azure databaser Collaboration-gren som används för publicering. Som standard är det `main` . Ändra den här inställningen om du vill publicera resurser från en annan gren. | `<your collaboration branch name>` |
| **Rotmapp** | Rotmappen i din Azure databaser-samarbets gren. | `<your root folder name>` |
| **Importera befintliga Data Factory resurser till lagrings platsen** | Anger om befintliga data Factory-resurser ska importeras från UX **redigerings arbets ytan** till en Azure databaser git-lagringsplats. Markera rutan om du vill importera data Factory-resurser till den tillhör ande git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt (det vill säga länkade tjänster och data uppsättningar exporteras till separata JSON-data). När den här rutan inte är markerad importeras inte de befintliga resurserna. | Vald (standard) |
| **Gren att importera resurs till** | Anger i vilken gren Data Factory-resurserna (pipelines, data uppsättningar, länkade tjänster osv.) importeras. Du kan importera resurser till någon av följande grenar: a. Samarbete b. Skapa nytt c. Använd befintlig |  |

> [!NOTE]
> Om du använder Microsoft Edge och inte ser några värden i list rutan för ditt Azure DevOps-konto lägger du till https://*. VisualStudio. com i listan över betrodda platser.

### <a name="use-a-different-azure-active-directory-tenant"></a>Använd en annan Azure Active Directory klient

Azure databaser git-lagrings platsen kan finnas i en annan Azure Active Directory klient. För att kunna ange en annan Azure AD-klientorganisation måste du ha administratörsbehörighet för den prenumeration som du använder. Mer information finns i [ändra prenumerations administratör](../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator)

> [!IMPORTANT]
> För att ansluta till en annan Azure Active Directory måste användaren som är inloggad vara en del av Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Använd din personliga Microsoft-konto

Om du vill använda en personlig Microsoft-konto för git-integrering kan du länka din personliga Azure-lagrings platsen till din organisations Active Directory.

1. Lägg till din personliga Microsoft-konto till din organisations Active Directory som gäst. Mer information finns i [Lägg till Azure Active Directory B2B-samarbets användare i Azure Portal](../active-directory/external-identities/add-users-administrator.md).

2. Logga in på Azure Portal med din personliga Microsoft-konto. Växla sedan till din organisations Active Directory.

3. Gå till avsnittet Azure-DevOps där du nu ser din personliga lagrings platsen. Välj lagrings platsen och Anslut med Active Directory.

Efter dessa konfigurations steg är din personliga lagrings platsen tillgänglig när du konfigurerar git-integrering i Data Factory användar gränssnitt.

Mer information om hur du ansluter Azure-databaser till din organisations Active Directory finns i [ansluta din Azure DevOps-organisation till Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Skapa med GitHub-integrering

Visuell redigering med GitHub-integrering stöder käll kontroll och samarbete för arbete på dina data Factory-pipeliner. Du kan associera en data fabrik med en GitHub-lagringsplats för käll kontroll, samarbete, versions hantering. Ett enskilt GitHub-konto kan ha flera databaser, men en GitHub-lagringsplats kan bara associeras med en data fabrik. Om du inte har ett GitHub-konto eller en lagrings plats, följer du [dessa anvisningar](https://github.com/join) för att skapa dina resurser.

GitHub-integreringen med Data Factory stöder både offentlig GitHub (det vill säga [https://github.com](https://github.com) ) och GitHub Enterprise. Du kan använda både offentliga och privata GitHub-databaser med Data Factory så länge du har Läs-och Skriv behörighet till lagrings platsen i GitHub.

Om du vill konfigurera en GitHub-lagrings platsen måste du ha administratörs behörighet för den Azure-prenumeration som du använder.

### <a name="github-settings"></a>GitHub-inställningar

![Inställningar för GitHub-lagringsplats](media/author-visually/github-integration-image2.png)

I konfigurations fönstret visas följande inställningar för GitHub-lagringsplats:

| **Inställning** | **Beskrivning**  | **Värde**  |
|:--- |:--- |:--- |
| **Typ av databas** | Typen av Azure databaser Code-lagringsplatsen. | GitHub |
| **Använda GitHub Enterprise** | Kryss ruta för att välja GitHub Enterprise | omarkerat (standard) |
| **GitHub Enterprise-URL** | GitHub Enterprise-rot-URL (måste vara HTTPS för den lokala GitHub Enterprise Server). Exempel: `https://github.mydomain.com`. Krävs endast om **Använd GitHub Enterprise** är valt | `<your GitHub enterprise url>` |                                                           
| **GitHub-konto** | Namnet på GitHub-kontot. Det här namnet kan hittas från https: \/ /GitHub.com/{account Name}/{repository Name}. Om du navigerar till den här sidan uppmanas du att ange GitHub OAuth-autentiseringsuppgifter för ditt GitHub-konto. | `<your GitHub account name>` |
| **Namn på databas**  | Ditt GitHub kod lagrings namn. GitHub-konton innehåller git-databaser för att hantera din käll kod. Du kan skapa en ny databas eller använda en befintlig databas som redan finns i ditt konto. | `<your repository name>` |
| **Samarbets gren** | Din GitHub Collaboration-gren som används för publicering. Som standard är den main. Ändra den här inställningen om du vill publicera resurser från en annan gren. | `<your collaboration branch>` |
| **Rotmapp** | Rotmappen i din GitHub-samarbets gren. |`<your root folder name>` |
| **Importera befintliga Data Factory resurser till lagrings platsen** | Anger om befintliga data Factory-resurser ska importeras från UX redigerings arbets ytan till en GitHub-lagringsplats. Markera rutan om du vill importera data Factory-resurser till den tillhör ande git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt (det vill säga länkade tjänster och data uppsättningar exporteras till separata JSON-data). När den här rutan inte är markerad importeras inte de befintliga resurserna. | Vald (standard) |
| **Gren att importera resurs till** | Anger i vilken gren Data Factory-resurserna (pipelines, data uppsättningar, länkade tjänster osv.) importeras. Du kan importera resurser till någon av följande grenar: a. Samarbete b. Skapa nytt c. Använd befintlig |  |

### <a name="github-organizations"></a>GitHub-organisationer

Att ansluta till en GitHub organisation kräver att organisationen beviljar behörighet att Azure Data Factory. En användare med ADMINISTRATÖRs behörighet för organisationen måste utföra stegen nedan för att Data Factory ska kunna ansluta.

#### <a name="connecting-to-github-for-the-first-time-in-azure-data-factory"></a>Ansluter till GitHub för första gången i Azure Data Factory

Om du ansluter till GitHub från Azure Data Factory för första gången ska du följa de här stegen för att ansluta till en GitHub-organisation.

1. I fönstret git-konfiguration anger du organisations namnet i fältet *GitHub-konto* . En uppvarning om att logga in på GitHub visas. 
1. Logga in med dina användarautentiseringsuppgifter.
1. Du uppmanas att auktorisera Azure Data Factory som ett program med namnet *AzureDataFactory*. På den här skärmen kan du se ett alternativ för att ge ADF behörighet att få åtkomst till organisationen. Om du inte ser alternativet för att bevilja behörighet ber du en administratör att bevilja behörighet manuellt via GitHub.

När du följer de här stegen kan fabriken ansluta till både offentliga och privata lagrings platser i din organisation. Om du inte kan ansluta kan du försöka rensa webbläsarens cacheminne och försöka igen.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Redan ansluten till GitHub med ett personligt konto

Om du redan har anslutit till GitHub och bara har beviljats behörighet att komma åt ett personligt konto följer du stegen nedan för att ge behörighet till en organisation. 

1. Gå till GitHub och öppna **Inställningar**.

    ![Öppna GitHub-inställningar](media/author-visually/github-settings.png)

1. Välj **program**. På fliken **auktoriserade OAuth-appar** bör du se *AzureDataFactory*.

    ![Välj OAuth-appar](media/author-visually/github-organization-select-application.png)

1. Välj programmet och ge programmet åtkomst till din organisation.

    ![Bevilja åtkomst](media/author-visually/github-organization-grant.png)

När du följer de här stegen kan fabriken ansluta till både offentliga och privata lagrings platser i din organisation. 

### <a name="known-github-limitations"></a>Kända GitHub-begränsningar

- Du kan lagra skript-och datafiler i en GitHub-lagringsplats. Du måste dock ladda upp filerna manuellt till Azure Storage. En Data Factory pipeline överför inte automatiskt skript eller datafiler som lagras i en GitHub-lagringsplats till Azure Storage.

- GitHub Enterprise med en äldre version än 2.14.0 fungerar inte i Microsoft Edge-webbläsaren.

- GitHub-integrering med Data Factory visuella redigerings verktyg fungerar bara i den allmänt tillgängliga versionen av Data Factory.


- Högst 1 000 entiteter per resurs typ (till exempel pipelines och data uppsättningar) kan hämtas från en enda GitHub-gren. Om den här gränsen uppnås rekommenderar vi att du delar upp resurserna i separata fabriker. Azure DevOps Git har inte den här begränsningen.

## <a name="version-control"></a>Versionskontroll

Med versions kontroll system (även kallat _käll kontroll_) kan utvecklare samar beta om kod och spåra ändringar som görs i kodbasen. Käll kontroll är ett viktigt verktyg för projekt med flera utvecklare.

### <a name="creating-feature-branches"></a>Skapa funktions grenar

Varje Azure databaser git-lagringsplats som är associerad med en data fabrik har en samarbets gren. ( `main` ) är standard grenen för samarbete). Användare kan också skapa funktions grenar genom att klicka på **+ ny gren** i rutan förgrening. När fönstret ny förgrening visas anger du namnet på din funktions gren.

![Skapa en ny gren](media/author-visually/new-branch.png)

När du är redo att sammanfoga ändringarna från din funktions gren till samarbets grenen klickar du på grenen gren och väljer **skapa pull-begäran**. Den här åtgärden tar dig till Azure databaser git där du kan generera pull-förfrågningar, göra kod granskningar och slå samman ändringar i samarbets grenen. ( `main` är standard). Du får bara publicera till tjänsten Data Factory från samarbets grenen. 

![Skapa en ny pull-begäran](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurera publicerings inställningar

Som standard genererar data Factory Resource Manager-mallarna för den publicerade fabriken och sparar dem i en gren som kallas `adf_publish` . Om du vill konfigurera en anpassad publicerings gren lägger du till en `publish_config.json` fil i rotmappen i samarbets grenen. Vid publicering läser ADF filen, letar efter fältet `publishBranch` och sparar alla Resource Manager-mallar på den angivna platsen. Om grenen inte finns skapas den automatiskt av Data Factory. Och exempel på hur den här filen ser ut som är nedan:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure Data Factory kan bara ha en publicerings gren i taget. När du anger en ny publicerings gren tar Data Factory inte bort den tidigare publicerings grenen. Om du vill ta bort den tidigare publicerings grenen tar du bort den manuellt.

> [!NOTE]
> Data Factory läser bara `publish_config.json` filen när den laddar fabriken. Om du redan har en fabrik som är inläst i portalen uppdaterar du webbläsaren för att ändringarna ska börja gälla.

### <a name="publish-code-changes"></a>Publicera kod ändringar

När du har sammanfogat ändringar i samarbets grenen ( `main` är standard) klickar du på **publicera** för att manuellt publicera dina kod ändringar i huvud grenen till tjänsten Data Factory.

![Publicera ändringar i Data Factorys tjänsten](media/author-visually/publish-changes.png)

En sida i fönstret öppnas där du bekräftar att publicerings grenen och de väntande ändringarna är korrekta. När du har verifierat ändringarna klickar du på **OK** för att bekräfta publiceringen.

![Bekräfta rätt publicerings gren](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Huvud grenen är inte representativ för vad som distribueras i Data Factorys tjänsten. Huvud grenen *måste* publiceras manuellt till data Factorys tjänsten.

## <a name="best-practices-for-git-integration"></a>Metod tips för git-integrering

### <a name="permissions"></a>Behörigheter

Normalt vill du inte att alla grupp medlemmar ska ha behörighet att uppdatera Data Factory. Följande behörighets inställningar rekommenderas:

*   Alla grupp medlemmar måste ha Läs behörighet till Data Factory.
*   Endast en urvals uppsättning av personer ska kunna publiceras till Data Factory. För att göra det måste de ha rollen **Data Factory Contributor** i **resurs gruppen** som innehåller data Factory. Mer information om behörigheter finns i [roller och behörigheter för Azure Data Factory](concepts-roles-permissions.md).

Vi rekommenderar att du inte tillåter direkta incheckningar till samarbets grenen. Den här begränsningen kan hjälpa till att förhindra buggar som varje incheckning går igenom en gransknings process för pull-begäran som beskrivs i [skapa funktions grenar](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Använda lösen ord från Azure Key Vault

Vi rekommenderar att du använder Azure Key Vault för att lagra anslutnings strängar eller lösen ord eller hanterad identitetsautentisering för Data Factory länkade tjänster. Av säkerhets skäl lagrar data Factory inte hemligheter i git. Ändringar av länkade tjänster som innehåller hemligheter som lösen ord publiceras direkt till Azure Data Factory tjänsten.

Med hjälp av Key Vault-eller MSI-autentisering kan du också göra kontinuerlig integrering och distribution enklare eftersom du inte behöver tillhandahålla dessa hemligheter under distributionen av Resource Manager-mallar.

## <a name="troubleshooting-git-integration"></a>Felsöka Git-integrering

### <a name="stale-publish-branch"></a>Inaktuell publicerings gren

Om publicerings grenen inte är synkroniserad med huvud grenen och innehåller inaktuella resurser trots en senaste publicering, prova följande steg:

1. Ta bort din aktuella git-lagringsplats
1. Konfigurera om git med samma inställningar, men se till att **Importera befintliga data Factory resurser till databasen** är markerat och välj **ny gren**
1. Skapa en pull-begäran för att sammanslå ändringarna till samarbetsgrenen 

Nedan visas några exempel på situationer som kan orsaka en inaktuell publicerings gren:
- En användare har flera grenar. I en funktions gren har de tagit bort en länkad tjänst som inte är AKV kopplad (icke-AKV länkade tjänster publiceras direkt oavsett om de är i git eller inte) och aldrig sammanfogade funktions grenen i samarbets grenen.
- En användare ändrade data fabriken med hjälp av SDK eller PowerShell
- En användare flyttade alla resurser till en ny gren och försökte publicera för första gången. Länkade tjänster ska skapas manuellt när du importerar resurser.
- En användare laddar upp en icke-AKV länkad tjänst eller en Integration Runtime JSON manuellt. De hänvisar till resursen från en annan resurs, till exempel en data uppsättning, en länkad tjänst eller en pipeline. En icke-AKV länkad tjänst som skapats via UX publiceras direkt eftersom autentiseringsuppgifterna måste krypteras. Om du laddar upp en data uppsättning som refererar till den länkade tjänsten och försöker publicera, så tillåter UX det eftersom det finns i git-miljön. Den kommer att avvisas vid publicerings tiden eftersom den inte finns i Data Factory-tjänsten.

## <a name="switch-to-a-different-git-repository"></a>Växla till en annan git-lagringsplats

Om du vill växla till en annan git-lagringsplats går du till konfigurations sidan för git i hanterings navet under **käll kontroll**. Välj **Koppla från**. 

![Git-ikon](media/author-visually/remove-repository.png)

Ange data fabriks namnet och klicka på **Bekräfta** för att ta bort git-lagringsplatsen som är associerad med din data fabrik.

![Ta bort associationen med den aktuella git-lagrings platsen](media/author-visually/remove-repository-2.png)

När du har tagit bort associationen med den aktuella lagrings platsen kan du konfigurera git-inställningarna så att de använder en annan lagrings platsen och sedan importera befintliga Data Factory resurser till den nya lagrings platsen.

> [!IMPORTANT]
> Om du tar bort git-konfigurationen från en data fabrik raderas inte något från lagrings platsen. Fabriken kommer att innehålla alla publicerade resurser. Du kan fortsätta att redigera fabriken direkt mot tjänsten.

## <a name="next-steps"></a>Nästa steg

* Mer information om övervakning och hantering av pipelines finns i [övervaka och hantera pipelines program mässigt](monitor-programmatically.md).
* För att implementera kontinuerlig integrering och distribution, se [kontinuerlig integrering och leverans (CI/CD) i Azure Data Factory](continuous-integration-deployment.md).
