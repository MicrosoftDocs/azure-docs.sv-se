---
title: Använda CI/CD med Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Lär dig hur du konfigurerar kontinuerlig integrering/kontinuerlig distribution med Azure DevOps med Azure dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
ms.openlocfilehash: 142f01a04066b919a81e1ad1ea95efa31f51f6ca
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548741"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Använda CI/CD med Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Den här artikeln vägleder dig genom att konfigurera kontinuerlig integrering/kontinuerlig distribution (CI/CD) till Azure Kubernetes service (AKS) med dev-utrymmen aktiverade. Med CI/CD till AKS distribueras uppdateringar automatiskt när allokerad kod skickas till käll lagrings platsen. Att använda CI/CD tillsammans med ett dev Spaces-kluster är användbart eftersom det kan hålla en bas linje i programmet uppdaterat för att teamet ska arbeta med.

![CI/CD-exempeldiagram](../media/common/ci-cd-simple.png)

Även om den här artikeln vägleder dig med Azure DevOps, gäller samma koncept för CI/CD-system som Jenkins, TeamCity osv.

## <a name="prerequisites"></a>Förutsättningar
* Azure Kubernetes service-kluster (AKS) med Azure dev-platser aktiverat
* [Azure dev Spaces CLI installerat](upgrade-tools.md)
* [Azure DevOps-organisation med ett projekt](/azure/devops/user-guide/sign-up-invite-teammates)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Azure Container Registry information om [Administratörs kontot](../../container-registry/container-registry-authentication.md#admin-account) tillgängliga
* [Auktorisera ditt AKS-kluster för att hämta från din Azure Container Registry](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Hämta exempel kod
Nu ska vi skapa en förgrening av vår exempel kod GitHub-lagringsplats. Gå till https://github.com/Azure/dev-spaces och välj **förgrening**. När processen är klar kan du **klona** den förgrenade versionen av lagrings platsen lokalt. Som standard är _huvud_ grenen utcheckad, men vi har inkluderat några tids besparingar i _azds_updates_ gren, som också bör ha överförts under din förgrening. _Azds_updates_ grenen innehåller uppdateringar som vi ber dig att göra manuellt i själv studie kursen om dev Spaces, samt vissa färdiga yaml-och JSON-filer för att effektivisera distributionen av CI/CD-systemet. Du kan använda ett kommando som `git checkout -b azds_updates origin/azds_updates` för att ta en titt på _azds_updates_ grenen i din lokala lagrings plats.

## <a name="dev-spaces-setup"></a>Installation av dev Spaces
Skapa ett nytt Space med namnet _dev_ med `azds space select` kommandot. _Dev_ -utrymmet kommer att användas av CI/CD-pipeline för att skicka kod ändringarna. Den kommer också att användas för att skapa _underordnade utrymmen_ baserat på _dev_.

```cmd
azds space select -n dev
```

När du uppmanas att välja ett överordnat dev-utrymme väljer du _\<none\>_ .

När du har skapat ditt utvecklings utrymme måste du bestämma suffixet för värden. Använd `azds show-context` kommandot för att Visa värd suffixet för ingångs styrenheten för Azure dev Spaces.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

I ovanstående exempel är värd suffixet _fedcba098.EUs.azds.io_. Det här värdet används senare när du skapar en versions definition.

_Dev_ -området innehåller alltid det senaste tillstånd för lagrings platsen, en bas linje, så att utvecklare kan skapa _underordnade utrymmen_ från _dev_ för att testa sina isolerade ändringar inom ramen för den större appen. Det här konceptet beskrivs mer detaljerat i självstudierna för dev Spaces.

## <a name="creating-the-build-definition"></a>Skapar build-definitionen
Öppna ditt Azure DevOps team-projekt i en webbläsare och gå till avsnittet _pipelines_ . Börja med att klicka på ditt profil Foto längst upp till höger på Azure DevOps-webbplatsen, öppna fönstret för hands versions funktioner och inaktivera den _nya upplevelsen för att skapa yaml pipeline_:

![Öppna fönstret för hands versions funktioner](../media/common/preview-feature-open.png)

Alternativet att inaktivera:

![Nya alternativ för att skapa YAML pipelines](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Azure DevOps _New yaml pipeline för skapande av pipeliner_ är i konflikt med att skapa fördefinierade Bygg pipeliner just nu. Du måste inaktivera det för tillfället för att kunna distribuera vår fördefinierade build-pipeline.

I _azds_updates_ Branch har vi inkluderat en enkel [Azure pipeline-yaml](/azure/devops/pipelines/yaml-schema?tabs=schema) som definierar de build-steg som krävs för *mywebapi* och *webfrontend*.

Beroende på vilket språk du har valt har pipelinen YAML checkats in i en sökväg som liknar: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Så här skapar du en pipeline från den här filen:
1. På DevOps-projektets huvud sida navigerar du till pipelines > builds.
1. Välj alternativet för att skapa en **ny** versions pipeline.
1. Välj **GitHub** som källa, auktorisera med ditt GitHub-konto om det behövs och välj den _azds_updates_ grenen från den förgrenade versionen av exempel program lagrings platsen för _dev-Spaces_ .
1. Välj **konfiguration som kod** eller **yaml** som mall.
1. Nu visas en konfigurations sida för din build-pipeline. Som vi nämnt ovan navigerar du till den språkspecifika sökvägen för sökvägen till **yaml-filen** med hjälp av **...** -knappen. Till exempel `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Gå till fliken **variabler** .
1. Lägg till _dockerId_ manuellt som en variabel, vilket är användar namnet för ditt [Azure Container Registry administratörs konto](../../container-registry/container-registry-authentication.md#admin-account). (Anges i artikel förutsättningar)
1. Lägg till _dockerPassword_ manuellt som en variabel, vilket är lösen ordet för ditt [Azure Container Registry administratörs konto](../../container-registry/container-registry-authentication.md#admin-account). Se till att ange _dockerPassword_ som en hemlighet (genom att välja Lås ikonen) av säkerhets synpunkt.
1. Välj **spara & kö**.

Nu har du en CI-lösning som automatiskt skapar *mywebapi* och *webfrontend* för alla uppdateringar som skickas till _azds_updates_ grenen i GitHub-gaffel. Du kan kontrol lera att Docker-avbildningarna har flyttats genom att gå till Azure Portal, välja din Azure Container Registry och bläddra i fliken **databaser** . Det kan ta flera minuter innan avbildningarna skapas och visas i behållar registret.

![Azure Container Registry-databaser](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Skapar versions definitionen

1. På huvud sidan för DevOps-projektet navigerar du till pipelines >-versioner
1. Om du arbetar i ett helt nytt DevOps-projekt som ännu inte innehåller en versions definition måste du först skapa en tom versions definition innan du fortsätter. Alternativet importera visas inte i användar gränssnittet förrän du har en befintlig versions definition.
1. Klicka på knappen **+ ny** till vänster och klicka sedan på **Importera en pipeline**.
1. Klicka på **Bläddra** och välj `samples/release.json` från ditt projekt.
1. Klicka på **OK**. Lägg märke till att pipeline-fönstret har lästs in med sidan för versions definitions redigering. Observera också att det finns några röda varnings ikoner som anger kluster information som fortfarande måste konfigureras.
1. Klicka på bubblan **Lägg till en artefakt** till vänster i rutan pipelines.
1. I list rutan **källa** väljer du den versions pipeline som du skapade tidigare.
1. För **standard versionen** väljer du **senaste från standard grenen build pipeline med Taggar**.
1. Lämna **taggar** tomma.
1. Ange **käll Ali Aset** till `drop` . Värdet för **käll Ali Aset** används av de fördefinierade versions aktiviteterna så att det måste anges.
1. Klicka på **Lägg till**.
1. Klicka nu på blixt ikonen på den nyligen skapade `drop` artefakt källan, som du ser nedan:

    ![Installations program för kontinuerlig distribution av versions artefakt](../media/common/release-artifact-cd-setup.png)
1. Aktivera den **kontinuerliga distributions utlösaren**.
1. Hovra över fliken **aktiviteter** bredvid **pipelinen** och klicka på _dev_ för att redigera aktiviteterna för _dev_ -fasen.
1. Verifiera **Azure Resource Manager** har marker ATS under **Anslutnings typ.** och du ser de tre List rutorna markerade i rött: ![ installations programmet för versions definition](../media/common/release-setup-tasks.png)
1. Välj den Azure-prenumeration du använder med Azure dev Spaces. Du kan också behöva klicka på **auktorisera**.
1. Välj den resurs grupp och det kluster som du använder med Azure dev Spaces.
1. Klicka på **Agent jobb**.
1. Välj **värdbaserad Ubuntu 1604** under **agenten**.
1. Hovra över **aktivitets** väljaren överst, klicka på _Prod_ för att redigera uppgifterna för _produktions_ stadiet.
1. Verifiera **Azure Resource Manager** har marker ATS under **Anslutnings typ.** och välj den Azure-prenumeration, resurs grupp och det kluster som du använder med Azure dev Spaces.
1. Klicka på **Agent jobb**.
1. Välj **värdbaserad Ubuntu 1604** under **agenten**.
1. Klicka på fliken **variabler** om du vill uppdatera variablerna för din version.
1. Uppdatera värdet för **DevSpacesHostSuffix** från **UPDATE_ME** till ditt värdnamn. Värddatorns suffix visas när du körde `azds show-context` kommandot tidigare.
1. Klicka på **Spara** längst upp till höger och **OK**.
1. Klicka på **+ släpp** (bredvid knappen Spara) och **skapa en version**.
1. Under **artefakter** kontrollerar du att den senaste versionen från din build-pipeline är markerad.
1. Klicka på **Skapa**.

En automatiserad versions process börjar nu, distribuerar *mywebapi* -och *webfrontend* -diagrammen till ditt Kubernetes-kluster i den översta nivån i _dev_ -området. Du kan övervaka förloppet för din version på webb portalen för Azure DevOps:

1. Gå till avsnittet **versioner** under **pipelines**.
1. Klicka på versions pipelinen för exempel programmet.
1. Klicka på namnet på den senaste versionen.
1. Hovra över **dev** Box under **faser** och klicka på **loggar**.

Versionen görs när alla aktiviteter har slutförts.

> [!TIP]
> Om din version Miss lyckas med ett fel meddelande som *uppgradering misslyckades: tids gränsen* nåddes i väntan på villkoret, försök att inspektera poddar i klustret [med hjälp av Kubernetes-instrumentpanelen](../../aks/kubernetes-dashboard.md). Om du ser att poddar Miss lyckas med att starta med fel meddelanden som *det inte gick att hämta avbildningen "azdsexample.azurecr.io/mywebapi:122": RPC-fel: kod = okänd DESC = fel svar från daemon: Hämta https: \/ /azdsexample.azurecr.io/v2/mywebapi/Manifests/122: obehörig: autentisering krävs*, det kan bero på att klustret inte har behörighet att hämta från din Azure Container Registry. Se till att du har slutfört [auktoriseringen av ditt AKS-kluster för att hämta från din Azure Container Registry](../../aks/cluster-container-registry-integration.md) -förutsättning.

Nu har du en helt automatiserad CI/CD-pipeline för GitHub-delen av dev Spaces-exempel appar. Varje gång du genomför och push-koden skapar och skickar du en pipeline för att bygga och push- *mywebapi* och *webfrontend* -avbildningar till din anpassade ACR-instans. Sedan distribuerar pipelinen Helm-diagrammet för varje app till _dev_ -utrymmet på ditt dev Spaces-aktiverade kluster.

## <a name="accessing-your-_dev_-services"></a>Åtkomst till dina _utvecklings_ tjänster
Efter distributionen kan du komma åt _dev_ -versionen av *webfrontend* med en offentlig URL som: `http://dev.webfrontend.fedcba098.eus.azds.io` . Du kan hitta den här URL: en genom att köra `azds list-uri` kommandot: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Distribuera till produktion

För att manuellt befordra en viss version till _Prod_ med hjälp av CI/CD-systemet som skapats i den här självstudien:
1. Gå till avsnittet **versioner** under **pipelines**.
1. Klicka på versions pipelinen för exempel programmet.
1. Klicka på namnet på den senaste versionen.
1. Hovra över rutan **Prod** under **etapper** och klicka på **distribuera**.
    ![Flytta upp till produktion](../media/common/prod-promote.png)
1. Hovra över rutan **Prod** igen under **steg** och klicka på **loggar**.

Versionen görs när alla aktiviteter har slutförts.

I _produktions_ fasen för CI/CD-pipelinen används en belastningsutjämnare i stället för en ingångs enhet för dev-utrymmen för att ge åtkomst till _produktions_ tjänsterna. Tjänster som distribueras i _produktions_ stadiet är tillgängliga som IP-adresser i stället för DNS-namn. I en produktions miljö kan du välja att skapa en egen ingångs kontroll som är värd för dina tjänster baserat på din egen DNS-konfiguration.

Om du vill fastställa IP-adressen för webfrontend-tjänsten klickar du på det  **offentliga IP-steget Skriv ut webfrontend** för att expandera loggens utdata. Använd IP-adressen som visas i loggens utdata för att få åtkomst till **webfrontend** -programmet.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Dev Spaces instrumentering i produktion
Även om dev Spaces instrumentering har utformats för att _inte_ komma i vägen för normal drift av ditt program, rekommenderar vi att du kör produktions arbets belastningar i ett Kubernetes-namnområde som inte är aktiverat med dev Spaces. Med den här typen av Kubernetes-namnrymd kan du antingen skapa ett namn område för produktion med hjälp av `kubectl` CLI, eller tillåta att ditt CI/CD-system skapar det under den första Helm-distributionen. Om du _väljer_ eller på annat sätt skapar ett blank steg med verktyget dev Spaces läggs det till i namn området.

Här är ett exempel på en namn områdes struktur som stöder funktions utveckling, utvecklings miljön _och_ produktion, allt i ett enda Kubernetes-kluster:

![Exempel på namn områdes struktur](../media/common/cicd-namespaces.png)

> [!Tip]
> Om du redan har skapat ett `prod` utrymme och bara vill undanta det från dev Spaces Instrumentation (utan att ta bort det!) kan du göra det med följande dev Spaces CLI-kommando:
>
> `azds space remove -n prod --no-delete`
>
> Du kan behöva ta bort alla poddar i `prod` namn området när du har gjort detta så att de kan återskapas utan dev Spaces Instrumentation.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure dev Spaces fungerar.

> [!div class="nextstepaction"]
> [Så här fungerar Azure Dev Spaces](../how-dev-spaces-works.md)
