---
title: Konfigurera GitHub Enterprise Server i ditt privata moln i Azure VMware-lösningen
description: Lär dig hur du konfigurerar GitHub Enterprise Server i ditt privata moln i Azure VMware-lösningen.
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 59a76c3976f6fcda88423b7b78344f2abed1ea84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382029"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Konfigurera GitHub Enterprise Server i ditt privata moln i Azure VMware-lösningen

I den här artikeln går vi igenom stegen för att konfigurera GitHub Enterprise Server, den lokala versionen av [GitHub.com](https://github.com/), i ditt privata moln i Azure VMware-lösningen. Scenariot är en GitHub Enterprise Server-instans som kan hantera upp till 3 000 utvecklare som kör upp till 25 jobb per minut på GitHub åtgärder. Den innehåller konfigurationen av (vid tidpunkten för skrivning) för *hands versions* funktioner, till exempel GitHub-åtgärder. Om du vill anpassa konfigurationen för dina specifika behov granskar du kraven som anges i [Installera GitHub Enterprise Server på VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations).

## <a name="before-you-begin"></a>Innan du börjar

GitHub Enterprise Server kräver en giltig licens nyckel. Du kan registrera dig för en [utvärderings licens](https://enterprise.github.com/trial). Om du vill utöka funktionerna i GitHub Enterprise Server via en integrering kan du kvalificera dig för en kostnads fri licens för webbaserade utvecklare. Ansök för den här licensen via [GitHub-partner program](https://partner.github.com/).

## <a name="installing-github-enterprise-server-on-vmware"></a>Installera GitHub Enterprise Server på VMware

Ladda ned [den aktuella versionen av GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) för VMware ESXi/VSPHERE (ägg) och [distribuera den ägg-mall](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) som du laddade ned.

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Välj att köra GitHub lokalt eller i molnet.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Distribuera mallen för ägg.":::  

Ange ett identifierbart namn för din nya virtuella dator, till exempel GitHubEnterpriseServer. Du behöver inte inkludera versions informationen i namnet på den virtuella datorn eftersom informationen blir inaktuell när instansen uppgraderas. Välj alla standardvärden nu (vi redigerar informationen strax) och väntar på att de ägg som ska importeras.

När du har importerat [justerar du maskin varu konfigurationen](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) utifrån dina behov. I vårt exempel scenario behöver vi följande konfiguration.

| Resurs | Standard konfiguration | Standard konfigurera + "beta funktioner" (åtgärder) |
| --- | --- | --- |
| Virtuella processorer | 4 | 8 |
| Minne | 32 GB | 61 GB |
| Bifogad lagring | 250 GB | 300 GB |
| Rot lagring | 200 GB | 200 GB |

Dina behov kan dock variera. Läs rikt linjerna för maskin varu överväganden vid [installation av GitHub Enterprise Server på VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations). Se även [lägga till processor-eller minnes resurser för VMware](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) för att anpassa maskin varu konfigurationen utifrån din situation.

## <a name="configuring-the-github-enterprise-server-instance"></a>Konfigurera GitHub Enterprise Server-instansen

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Installera GitHub Enterprise.":::  

När den nyligen etablerade virtuella datorn (VM) har påbörjats [konfigurerar du den via din webbläsare](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). Du måste ladda upp licens filen och ange ett lösen ord för hanterings konsolen. Se till att skriva ned det här lösen ordet någonstans säkert.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Få åtkomst till admin Shell via SSH.":::    

Vi rekommenderar att du vidtar minst följande steg:

1. Ladda upp en offentlig SSH-nyckel till hanterings konsolen så att du kan [komma åt det administrativa gränssnittet via SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh). 

2. [Konfigurera TLS på din instans](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) så att du kan använda ett certifikat som har signerats av en betrodd certifikat utfärdare.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Konfigurerar din instans.":::

Använd dina inställningar.  När instansen startas om kan du fortsätta med nästa steg, **konfigurera Blob Storage för GitHub-åtgärder**.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Skapa ditt administratörs konto.":::

När instansen har startats om kan du skapa ett nytt administratörs konto på instansen. Glöm inte att anteckna den här användarens lösen ord.

### <a name="other-configuration-steps"></a>Andra konfigurations steg

Följande valfria installations steg rekommenderas för att förstärka din instans för produktions användning:

1. Konfigurera [hög tillgänglighet](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) för skydd mot:

    - Program krascher (OS eller program nivå)
    - Maskin varu problem (lagring, CPU, RAM-minne och så vidare)
    - Systemfel för Virtualiseringsvärd
    - Logiskt eller fysiskt allvarligt nätverk

2. [Konfigurera](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) [säkerhets kopiering – verktyg](https://github.com/github/backup-utils)som tillhandahåller versioner av ögonblicks bilder för haveri beredskap och som är i tillgänglighet som är åtskilda från den primära instansen.
3. [Konfigurera under domän isolering](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)med ett giltigt TLS-certifikat, för att minimera skript körning av flera webbplatser och andra relaterade sårbarheter.

## <a name="configuring-blob-storage-for-github-actions"></a>Konfigurera Blob Storage för GitHub-åtgärder

> [!NOTE]
> GitHub-åtgärder är [för närvarande tillgängliga som en begränsad beta version på GitHub Enterprise Server version 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Extern blob-lagring krävs för att aktivera GitHub-åtgärder på GitHub Enterprise Server (som för närvarande är tillgänglig som en beta funktion). Den här externa blob-lagringen används av åtgärder för att lagra artefakter och loggar. Åtgärder på GitHub Enterprise Server [har stöd för Azure Blob Storage som en lagrings leverantör](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (och andra). Vi etablerar ett nytt Azure Storage-konto med [lagrings konto typen](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) BlobStorage:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Etablera Azure Blob Storage-konto.":::

När distributionen av den nya BlobStorage-resursen har slutförts, kopiera och anteckna anslutnings strängen (tillgänglig under åtkomst nycklar). Vi behöver den här strängen inom kort.

## <a name="setting-up-the-github-actions-runner"></a>Konfigurera GitHub-åtgärds löpare

> [!NOTE]
> GitHub-åtgärder är [för närvarande tillgängliga som en begränsad beta version på GitHub Enterprise Server version 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Nu bör du ha en instans av GitHub Enterprise Server som kör med ett administratörs konto som skapats. Du bör också ha en extern blob-lagring som GitHub-åtgärder kommer att använda för persistence.

Nu ska vi skapa någonstans för att GitHub åtgärder ska kunna köras; Vi använder Azure VMware-lösningen igen.

Först ska vi etablera en ny virtuell dator i klustret. Vi kommer att basera vår virtuella dator på [en ny version av Ubuntu-servern](http://releases.ubuntu.com/20.04.1/).

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Etablera en ny virtuell dator.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Etablera en ny VM steg 2.":::

När den virtuella datorn har skapats kan du starta den och ansluta till den via SSH.

Sedan installerar [du åtgärden löpare](https://github.com/actions/runner) , som kör ett jobb från ett arbets flöde för GitHub-åtgärder. Identifiera och hämta den mest aktuella linux x64-versionen av åtgärds utlöpareen, antingen från [sidan versioner](https://github.com/actions/runner/releases) eller genom att köra följande snabb skript. Det här skriptet kräver att både sväng-och [JQ](https://stedolan.github.io/jq/) finns på den virtuella datorn.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

Nu bör du ha en fil lokalt på den virtuella datorn, Actions-löpare-Linux-arm64- \* . tar. gz. Extrahera den här tarball lokalt:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Den här extraheringen packar upp några filer lokalt, inklusive ett `config.sh` och `run.sh` skript, som vi kommer tillbaka till kort.

## <a name="enabling-github-actions"></a>Aktivera GitHub-åtgärder

> [!NOTE]
> GitHub-åtgärder är [för närvarande tillgängliga som en begränsad beta version på GitHub Enterprise Server version 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Nästan där! Vi konfigurerar och aktiverar GitHub-åtgärder på GitHub Enterprise Server-instansen. Vi behöver åtkomst till [GitHub Enterprise Server-instansens administrativa gränssnitt via SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)och kör sedan följande kommandon:

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

Nu när GitHub-åtgärder har kon figurer ATS kan du aktivera det för dina användare. Logga in på din GitHub Enterprise Server-instans som administratör och välj Rocket- ![ ikonen.](media/github-enterprise-server/rocket-icon.png) i det övre högra hörnet på en sida. I den vänstra sid panelen väljer du **företags översikt**, sedan **principer**, **åtgärder** och väljer alternativet för att **Aktivera åtgärder för alla organisationer**.

Konfigurera sedan din löpare från fliken **egna värdar** . Välj **Lägg till ny** och sedan **ny löpare** i list rutan.

På nästa sida visas en uppsättning kommandon som ska köras, vi behöver bara kopiera kommandot för att **Konfigurera** löpare, till exempel:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Kopiera `config.sh` kommandot och klistra in det i en session på din åtgärds löpare (skapad tidigare).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Åtgärds löpare.":::

Använd kommandot run.sh för att *köra* löpare:

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Kör löpare.":::

Om du vill göra den här utlöpareen tillgänglig för organisationer i företaget redigerar du dess organisations åtkomst:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Redigera löpare-åtkomst.":::

Här kommer vi att göra den tillgänglig för alla organisationer, men du kan begränsa åtkomsten till en delmängd organisationer och till och med vissa databaser.

## <a name="optional-configuring-github-connect"></a>Valfritt Konfigurera GitHub Connect

Även om det här steget är valfritt rekommenderar vi det om du planerar att använda åtgärder med öppen källkod som finns på GitHub.com. Det gör att du kan bygga vidare på arbetet med andra genom att referera till dessa återanvändbara åtgärder i dina arbets flöden.

Om du vill aktivera GitHub Connect följer du stegen i [Aktivera automatisk åtkomst till GitHub.com-åtgärder med GitHub Connect](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect).

När GitHub Connect har Aktiver ATS väljer du alternativet **Server för att använda åtgärder från GitHub.com i arbets flödets körnings** alternativ.

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Aktivera med åtgärder från GitHub.com i arbets flödes körningar.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Konfigurera och köra ditt första arbets flöde

Nu när åtgärderna och GitHub Connect har kon figurer ATS är det dags att börja med allt detta arbete. Här är ett exempel på ett arbets flöde som refererar till den utmärkta [octokit/begäran-åtgärden](https://github.com/octokit/request-action), så att vi kan "skriva" skript "GitHub via interaktioner med hjälp av GITHUB-API, som drivs av GitHub åtgärder.

I det här grundläggande arbets flödet kommer vi `octokit/request-action` att använda för att bara öppna ett problem på GitHub med hjälp av API: et.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Exempel arbets flöde.":::

>[!NOTE]
>GitHub.com är värd för åtgärden, men när den körs på GitHub Enterprise Server använder den *automatiskt* GitHub Enterprise Server-API: et.

Om du väljer att inte aktivera GitHub Connect kan du använda följande alternativa arbets flöde.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Alternativt exempel arbets flöde.":::

Navigera till en lagrings platsen på din instans och Lägg till arbets flödet ovan som: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Ett annat exempel på ett arbets flöde.":::

På fliken **åtgärder** för din lagrings platsen väntar du på att arbets flödet ska köras.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Utfört exempel arbets flöde.":::

Du kan också se att den bearbetas av löpare.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Arbets flöde som bearbetas av löpare.":::

Om allt har körts utan problem bör du se ett nytt ärende i din lagrings platsen, med titeln "Hello World".

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Exempel i lagrings platsen.":::

Grattis! Du har precis slutfört ditt första åtgärds arbets flöde på GitHub Enterprise Server, som körs på ditt privata moln i Azure VMware-lösningen.

I den här artikeln ställer vi in en ny instans av GitHub Enterprise Server, som är den GitHub.com som är egen värd, ovanpå ditt privata moln i Azure VMware-lösningen. Den här instansen innehåller stöd för GitHub-åtgärder och använder Azure Blob Storage för att beständige loggar och artefakter. Men vi skapar bara ytan på vad du kan göra med GitHub-åtgärder. Kolla in listan med åtgärder på [GitHubs Marketplace](https://github.com/marketplace)eller [skapa en egen](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Nästa steg

Nu när du har täckt GitHub Enterprise Server i ditt privata moln i Azure VMware-lösningen kanske du vill lära dig mer om: 

- [Komma igång med GitHub-åtgärder](https://docs.github.com/en/actions).
- [Delta i beta programmet](https://resources.github.com/beta-signup/).
- [Administration av GitHub Enterprise Server](https://githubtraining.github.io/admin-training/#/00_getting_started).
