---
title: Konfigurera GitHub Enterprise Server på din Azure VMware Solution privata moln
description: Lär dig hur du ställer in GitHub Enterprise Server på Azure VMware Solution privata molnet.
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 0ff7ab87d7401cd3faaecf149fb1b07be3f8db42
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862947"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Konfigurera GitHub Enterprise Server på din Azure VMware Solution privata moln

I den här artikeln går vi igenom stegen för att konfigurera GitHub Enterprise Server, den "lokala" versionen [av GitHub.com](https://github.com/), i ditt Azure VMware Solution privata moln. Det scenario som vi ska ta upp är en GitHub Enterprise Server-instans som kan hjälpa upp till 3 000 utvecklare att köra upp till 25 jobb per minut på GitHub Actions. Den innehåller konfiguration av (när detta skrivs) *förhandsgranskningsfunktioner,* till exempel GitHub Actions. Om du vill anpassa konfigurationen för dina specifika behov granskar du kraven som anges [i Installera GitHub Enterprise Server på VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations).

## <a name="before-you-begin"></a>Innan du börjar

GitHub Enterprise Server kräver en giltig licensnyckel. Du kan registrera dig för en [utvärderingslicens.](https://enterprise.github.com/trial) Om du vill utöka funktionerna i GitHub Enterprise Server via en integrering kan du kvalificera dig för en kostnadsfri utvecklarlicens med fem platser. Ansöka om den här licensen [via GitHubs partnerprogram](https://partner.github.com/).

## <a name="installing-github-enterprise-server-on-vmware"></a>Installera GitHub Enterprise Server på VMware

Ladda [ned den aktuella versionen av GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) för VMware ESXi/vSphere (OVA) och distribuera [OVA-mallen som](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) du laddade ned.

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Välj att köra GitHub lokalt eller i molnet.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Distribuera OVA-mallen.":::  

Ange ett identifierbart namn för den nya virtuella datorn, till exempel GitHubEnterpriseServer. Du behöver inte inkludera versionsinformation i namnet på den virtuella datorn eftersom informationen blir inaktuell när instansen uppgraderas. Välj alla standardvärden för tillfället (vi redigerar den här informationen snart) och vänta tills OVA har importerats.

När den har [importerats justerar du maskinvarukonfigurationen](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) baserat på dina behov. I vårt exempelscenario behöver vi följande konfiguration.

| Resurs | Standardinstallation | Standard set up + "Beta Features" (Åtgärder) |
| --- | --- | --- |
| Virtuella processorer | 4 | 8 |
| Minne | 32 GB | 61 GB |
| Ansluten lagring | 250 GB | 300 GB |
| Rotlagring | 200 GB | 200 GB |

Dina behov kan dock variera. Se vägledningen om maskinvaruöverväganden [i Installera GitHub Enterprise Server på VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations). Se även [Lägga till PROCESSOR- eller minnesresurser för VMware](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) för att anpassa maskinvarukonfigurationen baserat på din situation.

## <a name="configuring-the-github-enterprise-server-instance"></a>Konfigurera GitHub Enterprise Server-instansen

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Installera GitHub Enterprise.":::  

När den nyligen etablerade virtuella datorn (VM) har etablerats [konfigurerar du den via webbläsaren](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). Du måste ladda upp licensfilen och ange ett lösenord för hanteringskonsolen. Se till att skriva ned lösenordet på en säker plats.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Få åtkomst till administrationsgränssnittet via SSH.":::    

Vi rekommenderar att du åtminstone gör följande:

1. Ladda upp en offentlig SSH-nyckel till hanteringskonsolen så att du kan [komma åt det administrativa gränssnittet via SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh). 

2. [Konfigurera TLS på din instans så](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) att du kan använda ett certifikat som signerats av en betrodd certifikatutfärdare.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Konfigurera din instans.":::

Tillämpa dina inställningar.  När instansen startas om kan du fortsätta med nästa steg, **Konfigurera Blob Storage för GitHub Actions**.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Skapa ditt administratörskonto.":::

När instansen har startats om kan du skapa ett nytt administratörskonto på instansen. Se även till att anteckna den här användarens lösenord.

### <a name="other-configuration-steps"></a>Andra konfigurationssteg

Följande valfria konfigurationssteg rekommenderas för att härda din instans för produktionsanvändning:

1. Konfigurera [hög tillgänglighet](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) för skydd mot:

    - Programvarukrasch (operativsystem- eller programnivå)
    - Maskinvarufel (lagring, CPU, RAM och så vidare)
    - Systemfel för virtualiseringsvärdar
    - Logiskt eller fysiskt avsade nätverk

2. [Konfigurera](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) [säkerhetskopieringsverktyg, som](https://github.com/github/backup-utils)tillhandahåller versionsögonblicksbilder för haveriberedskap med tillgänglighet som är separat från den primära instansen.
3. [Konfigurera underdomänisolering med](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)hjälp av ett giltigt TLS-certifikat för att minimera skriptning mellan webbplatser och andra relaterade säkerhetsrisker.

## <a name="configuring-blob-storage-for-github-actions"></a>Konfigurera bloblagring för GitHub Actions

> [!NOTE]
> GitHub Actions är [för närvarande tillgänglig som en begränsad betaversion på GitHub Enterprise Server version 2.22.](https://docs.github.com/en/enterprise/admin/github-actions)

Extern bloblagring krävs för att aktivera GitHub Actions på GitHub Enterprise Server (för närvarande tillgänglig som en "beta"-funktion). Den här externa bloblagringen används av Åtgärder för att lagra artefakter och loggar. Åtgärder på GitHub Enterprise Server [stöder Azure Blob Storage som lagringsprovider](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (och vissa andra). Därför etablerar vi ett nytt Azure Storage-konto med [lagringskontotypen](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) BlobStorage:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Etablera Azure Blob Storage konto.":::

När distributionen av den nya BlobStorage-resursen har slutförts kopierar du och anteckningen av anslutningssträngen (tillgänglig under Åtkomstnycklar). Vi behöver den här strängen inom kort.

## <a name="setting-up-the-github-actions-runner"></a>Konfigurera GitHub Actions start

> [!NOTE]
> GitHub Actions är [för närvarande tillgänglig som en begränsad betaversion på GitHub Enterprise Server version 2.22.](https://docs.github.com/en/enterprise/admin/github-actions)

Nu bör du ha en instans av GitHub Enterprise Server som körs, med ett administratörskonto skapat. Du bör också ha extern bloblagring som GitHub Actions för beständighet.

Nu ska vi skapa någonstans där GitHub Actions ska köras. igen använder vi Azure VMware Solution.

Först etablerar vi en ny virtuell dator i klustret. Vi baserar den virtuella datorn på [en nyligen genomförd version av Ubuntu Server](http://releases.ubuntu.com/20.04.1/).

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Etablera en ny virtuell dator.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Etablera en ny virtuell dator steg 2.":::

När den virtuella datorn har skapats kan du starta den och ansluta till den via SSH.

Installera sedan programmet [Actions runner,](https://github.com/actions/runner) som kör ett jobb från ett GitHub Actions arbetsflöde. Identifiera och ladda ned den senaste versionen av Actions-körningen för Linux x64, antingen från sidan [med](https://github.com/actions/runner/releases) versioner eller genom att köra följande snabbskript. Det här skriptet kräver att både curl [och jq](https://stedolan.github.io/jq/) finns på den virtuella datorn.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

Nu bör du ha en fil lokalt på den virtuella datorn, actions-runner-linux-arm64- \* .tar.gz. Extrahera denna tarball lokalt:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Den här extraheringen packar upp några filer lokalt, inklusive ett `config.sh` - och -skript, som vi snart ska gå tillbaka `run.sh` till.

## <a name="enabling-github-actions"></a>Aktivera GitHub Actions

> [!NOTE]
> GitHub Actions är [för närvarande tillgänglig som en begränsad betaversion på GitHub Enterprise Server version 2.22.](https://docs.github.com/en/enterprise/admin/github-actions)

Nästan där! Nu ska vi konfigurera och aktivera GitHub Actions på GitHub Enterprise Server-instansen. Vi behöver komma åt [GitHub Enterprise Server-instansens administrativa gränssnitt via SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)och sedan köra följande kommandon:

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

Nästa körning:

`ghe-actions-check -s blob`

Du bör se utdata: "Blob Storage är felfri".

Nu när GitHub Actions har konfigurerats aktiverar du det för dina användare. Logga in på din GitHub Enterprise Server-instans som administratör och välj ![ raketikonen.](media/github-enterprise-server/rocket-icon.png) i det övre högra hörnet på valfri sida. I den vänstra sidopanelen väljer **du Företagsöversikt** och **sedan Principer,** **Åtgärder** och sedan alternativet för att aktivera Åtgärder för **alla organisationer.**

Konfigurera sedan din löpning **från fliken Med egen** värd. Välj **Lägg till** ny och sedan **Ny** löpning i listrutan.

På nästa sida visas en uppsättning kommandon som ska köras. Vi behöver bara  kopiera kommandot för att konfigurera körningen, till exempel:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Kopiera kommandot `config.sh` och klistra in det i en session i ditt Actions-löpning (skapat tidigare).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Åtgärds runner.":::

Använd kommandot run.sh för att *köra* körningskörningen:

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Kör körningen.":::

Om du vill göra det här löpet tillgängligt för organisationer i företaget redigerar du dess organisationsåtkomst:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Redigera åtkomst till löpning.":::

Här gör vi den tillgänglig för alla organisationer, men du kan begränsa åtkomsten till en delmängd av organisationer och till och med till specifika lagringsplatsen.

## <a name="optional-configuring-github-connect"></a>(Valfritt) Konfigurera GitHub Connect

Även om det här steget är valfritt rekommenderar vi det om du planerar att använda åtgärder med öppen källkod som är GitHub.com. Det gör att du kan bygga vidare på andras arbete genom att referera till dessa återanvändbara åtgärder i dina arbetsflöden.

Om du vill aktivera GitHub Connect följer du stegen i [Aktivera automatisk åtkomst till GitHub.com åtgärder med GitHub Connect](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect).

När GitHub Connect är aktiverat väljer du den **server som ska använda åtgärder GitHub.com i arbetsflödeskörningar.**

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Aktivera med åtgärder från GitHub.com i arbetsflödeskörningar.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Konfigurera och köra ditt första arbetsflöde

Nu när Actions och GitHub Connect har ställts in ska vi använda allt det här arbetet på ett bra sätt. Här är ett exempelarbetsflöde som refererar till utmärkt [octokit/request-action](https://github.com/octokit/request-action)så att vi kan "skripta" GitHub via interaktioner med GitHub-API:et, som drivs av GitHub Actions.

I det här grundläggande arbetsflödet använder vi för `octokit/request-action` att bara öppna ett ärende på GitHub med hjälp av API:et.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Exempelarbetsflöde.":::

>[!NOTE]
>GitHub.com är värd för åtgärden, men när den körs på GitHub Enterprise Server använder *den* automatiskt GitHub Enterprise Server-API:et.

Om du väljer att inte aktivera GitHub Connect kan du använda följande alternativa arbetsflöde.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Alternativt exempelarbetsflöde.":::

Gå till en lagringsplatsen på din instans och lägg till arbetsflödet ovan som: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Ett annat exempelarbetsflöde.":::

På fliken **Åtgärder** för lagringsplatsen väntar du tills arbetsflödet har körts.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Kör exempelarbetsflöde.":::

Du kan också se när den bearbetas av löpning.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Arbetsflöde som bearbetas av runner.":::

Om allt har körts korrekt bör du se ett nytt problem i lagringsplatsen med titeln "Hello world".

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Exempel i lagringsplatsen.":::

Grattis! Du har precis slutfört ditt första Actions-arbetsflöde på GitHub Enterprise Server, som körs Azure VMware Solution privata molnet.

I den här artikeln ska vi konfigurera en ny instans av GitHub Enterprise Server, den egenvärdbaserade motsvarigheten till GitHub.com, ovanpå ditt Azure VMware Solution privata moln. Den här instansen har stöd för GitHub Actions och använder Azure Blob Storage för beständighet av loggar och artefakter. Men vi håller bara på att repa ytan av vad du kan göra med GitHub Actions. Kolla in listan över åtgärder på [GitHub Marketplace](https://github.com/marketplace)eller [skapa en egen](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Nästa steg

Nu när du har gått in på att konfigurera GitHub Enterprise Server Azure VMware Solution privata molnet kanske du vill veta mer om: 

- [Så här kommer du igång med GitHub Actions](https://docs.github.com/en/actions)
- [Så här går du med i betaprogrammet](https://resources.github.com/beta-signup/)
- [Administration av GitHub Enterprise Server](https://githubtraining.github.io/admin-training/#/00_getting_started)
