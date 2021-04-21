---
title: Översikt över ACR-uppgifter
description: En introduktion ACR-uppgifter, en uppsättning funktioner i Azure Container Registry som ger säker, automatiserad containeravbildningsbygge, hantering och korrigering i molnet.
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: a42a2bfcdc1621689421940c4db2fcf4f5e64b89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781009"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatisera containeravbildningsbyggen och underhåll med ACR-uppgifter

Containrar ger nya virtualiseringsnivåer som isolerar program- och utvecklarberoenden från infrastruktur- och driftkrav. Det som finns kvar är dock behovet av att hantera hur den här programvirtualiseringen hanteras och korrigeras under containerns livscykel.

## <a name="what-is-acr-tasks"></a>Vad är ACR-uppgifter?

**ACR-uppgifter** är en uppsättning funktioner i Azure Container Registry. Den tillhandahåller molnbaserad containeravbildningsskapande för plattformar som Linux, Windows och ARM och kan automatisera korrigering av [operativsystem](#automate-os-and-framework-patching) och ramverk för dina Docker-containrar. [](#image-platforms) ACR-uppgifter utökar inte bara din "inre loop"-utvecklingscykel till molnet med containeravbildningsbyggen på begäran, utan möjliggör även automatiserade byggen som utlöses av källkodsuppdateringar, uppdateringar av en containers basavbildning eller timers. Med utlösare för uppdatering av basavbildningar kan du till exempel automatisera arbetsflödet för korrigering av operativsystem och programramverk, upprätthålla säkra miljöer samtidigt som du följa principerna för oföränderliga containrar.

## <a name="task-scenarios"></a>Uppgiftsscenarier

ACR-uppgifter flera scenarier för att skapa och underhålla containeravbildningar och andra artefakter. Mer information finns i följande avsnitt i den här artikeln.

* **[Snabbuppgift](#quick-task)** – Skapa och push-installera en enskild containeravbildning till ett containerregister på begäran i Azure, utan att behöva en lokal Docker Engine-installation. Tänk `docker build` på `docker push` i molnet.
* **Uppgifter som utlöses automatiskt** – Aktivera en eller flera *utlösare för* att skapa en avbildning:
  * **[Utlösare vid uppdatering av källkod](#trigger-task-on-source-code-update)** 
  * **[Utlösare vid uppdatering av basavbildning](#automate-os-and-framework-patching)** 
  * **[Utlösare enligt ett schema](#schedule-a-task)** 
* **[Uppgift i flera steg](#multi-step-tasks)** – Utöka build-and-push-funktionen för enskilda avbildningar i ACR-uppgifter med arbetsflöden med flera steg och flera containrar. 

Varje ACR-uppgift har en associerad [källkodskontext](#context-locations) – platsen för en uppsättning källfiler som används för att skapa en containeravbildning eller annan artefakt. Exempelkontexterna är en Git-lagringsplats eller ett lokalt filsystem.

Uppgifter kan också dra nytta av [körningsvariabler](container-registry-tasks-reference-yaml.md#run-variables), så att du kan återanvända uppgiftsdefinitioner och standardisera taggar för avbildningar och artefakter.

## <a name="quick-task"></a>Snabbuppgift

Utvecklingscykeln för den inre loopen, den iterativa processen för att skriva kod, skapa och testa ditt program innan du börjar använda källkontroll, är i början av livscykelhanteringen för containrar.

Innan du genomför din första kodrad kan [](container-registry-tutorial-quick-task.md) ACR-uppgifter snabbuppgiftsfunktionen ge en integrerad utvecklingsupplevelse genom att avlasta dina containeravbildningsbyggen till Azure. Med snabbuppgifter kan du verifiera dina automatiserade byggdefinitioner och fånga upp potentiella problem innan du utför koden.

Med det välbekanta formatet tar kommandot az acr build i Azure CLI en kontext (uppsättningen filer som ska byggas), skickar det till ACR-uppgifter och skickar som standard den skapade avbildningen till sitt register när den har `docker build` slutförts. [][az-acr-build] [](#context-locations)

En introduktion finns i snabbstarten för att [skapa och köra en containeravbildning](container-registry-quickstart-task-cli.md) i Azure Container Registry.  

ACR-uppgifter är utformad som en primitiv containerlivscykel. Du kan till exempel integrera ACR-uppgifter din CI/CD-lösning. Genom att köra [az login med][az-login] ett huvudnamn [för][az-login-service-principal]tjänsten kan din CI/CD-lösning sedan utfärda [az acr build-kommandon][az-acr-build] för att starta avbildningsbyggen.

Lär dig hur du använder snabbuppgifter i den första ACR-uppgifter självstudien [Skapa containeravbildningar i molnet med Azure Container Registry-uppgifter](container-registry-tutorial-quick-task.md).

> [!TIP]
> Om du vill skapa och push-skicka en avbildning direkt från källkoden utan en Dockerfile Azure Container Registry kommandot [az acr pack build][az-acr-pack-build] (förhandsversion). Det här verktyget skapar och push-erar en avbildning från programmets källkod med [hjälp av Cloud Native Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Utlösa aktivitet vid uppdatering av källkod

Utlösa en containeravbildningsbygge eller uppgift i flera steg när kod har utförts, eller när en pull-begäran görs eller uppdateras, till en offentlig eller privat Git-lagringsplats i GitHub eller Azure DevOps. Du kan till exempel konfigurera en bygguppgift med Azure CLI-kommandot [az acr task create][az-acr-task-create] genom att ange en Git-lagringsplats och eventuellt en gren och Dockerfile. När ditt team uppdaterar kod i lagringsplatsen utlöser en ACR-uppgifter webhook en version av containeravbildningen som definierats i lagringsplatsen. 

ACR-uppgifter stöder följande utlösare när du anger en Git-lagringsplatsen som aktivitetens kontext:

| Utlösare | Aktiverat som standard |
| ------- | ------------------ |
| Checka in | Yes |
| Pull-begäran | No |

För att konfigurera en uppdateringsutlösare för källkod måste du ange uppgiften som en personlig åtkomsttoken (PAT) för att ställa in webhooken på den offentliga eller privata GitHub- eller Azure DevOps-lagringsplatsen.

> [!NOTE]
> För närvarande ACR-uppgifter inte utlösare för genomförande eller pull-begäran i GitHub Enterprise-lagringsplatsen.

Lär dig hur du utlöser byggen på källkods genomförande i den andra ACR-uppgifter självstudien [Automatisera containeravbildningsbyggen med Azure Container Registry-uppgifter](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatisera korrigering av operativsystem och ramverk

Kraften i att ACR-uppgifter att verkligen förbättra arbetsflödet för containerbygget kommer från möjligheten att identifiera en uppdatering av en *basavbildning*. En funktion i de flesta containeravbildningar är en basavbildning en överordnad avbildning som en eller flera programavbildningar baseras på. Basavbildningar innehåller vanligtvis operativsystemet och ibland programramverk. 

Du kan konfigurera en ACR-uppgift för att spåra ett beroende till en basavbildning när den skapar en programavbildning. När den uppdaterade basavbildningen skickas till registret eller en basavbildning uppdateras i en offentlig lagringsplatsen, till exempel i Docker Hub, kan ACR-uppgifter automatiskt skapa alla programavbildningar baserat på den.
Med den här automatiska identifieringen och återskapande sparar ACR-uppgifter tid och arbete som normalt krävs för att manuellt spåra och uppdatera varje programavbildning som refererar till den uppdaterade basavbildningen.

Läs mer om [utlösare för uppdatering av basavbildningar](container-registry-tasks-base-images.md) för ACR-uppgifter. Och lär dig hur du utlöser en avbildningsbygge när en basavbildning skickas till ett containerregister i självstudien Automatisera [containeravbildningsbyggen](container-registry-tutorial-base-image-update.md) när en basavbildning uppdateras i ett Azure-containerregister

## <a name="schedule-a-task"></a>Schemalägga en aktivitet

Du kan också schemalägga en aktivitet genom att konfigurera en eller *flera timerutlösare* när du skapar eller uppdaterar uppgiften. Schemaläggning av en uppgift är användbart för att köra containerarbetsbelastningar enligt ett definierat schema, eller för att köra underhållsåtgärder eller tester på avbildningar som skickas regelbundet till registret. Mer information finns i [Köra en ACR-uppgift enligt ett definierat schema.](container-registry-tasks-scheduled.md)

## <a name="multi-step-tasks"></a>Uppgifter i flera steg

Uppgifter i flera steg ger stegvis uppgiftsdefinition och körning för att skapa, testa och korrigera containeravbildningar i molnet. Uppgiftssteg som definieras i [en YAML-fil](container-registry-tasks-reference-yaml.md) anger enskilda bygg- och push-åtgärder för containeravbildningar eller andra artefakter. De kan också definiera körningen av en eller flera container så varje steg använder containern som sin körningsmiljö.

Du kan till exempel skapa en uppgift i flera steg som automatiserar följande:

1. Skapa en avbildning av ett webbprogram
1. Köra webbprogramcontainern
1. Skapa en testavbildning för en webbapp
1. Kör testcontainern för webbprogram, som utför tester mot den programcontainer som körs
1. Om testerna är klara skapar du ett arkivpaket för Helm-diagram
1. Utföra en med `helm upgrade` hjälp av det nya arkivpaketet för Helm-diagram

Med uppgifter i flera steg kan du dela upp byggnaden, köra och testa en bild i mer sammansättningsbara steg med stöd för beroenden mellan steg. Med uppgifter i flera steg i ACR-uppgifter har du mer detaljerad kontroll över arbetsflöden för bildskapande, testning och korrigering av operativsystem och ramverk.

Lär dig mer om uppgifter i flera [steg i Kör uppgifter för att skapa, testa och korrigera](container-registry-tasks-multi-step.md)flera steg i ACR-uppgifter .

## <a name="context-locations"></a>Kontextplatser

I följande tabell visas exempel på kontextplatser som stöds för ACR-uppgifter:

| Kontextplats | Beskrivning | Exempel |
| ---------------- | ----------- | ------- |
| Lokalt filsystem | Filer i en katalog i det lokala filsystemet. | `/home/user/projects/myapp` |
| GitHub-huvudgren | Filer i huvudgrenen (eller någon annan standardgren) på en offentlig eller privat GitHub-lagringsplats.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-gren | Specifik gren av en offentlig eller privat GitHub-lagringsplatsen.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub-undermapp | Filer i en undermapp på en offentlig eller privat GitHub-lagringsplatsen. Exempel som visar en kombination av en specifikation för en gren och undermapp. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub-genomförande | Specifika genomföranden i en offentlig eller privat GitHub-lagringsplatsen. Exempel som visar en kombination av en sha-hash (commit hash) och undermappsspecifikation. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Azure DevOps-undermapp | Filer i en undermapp på en offentlig eller privat Azure-lagringsplatsen. Exempel som visar en kombination av specifikation för gren och undermapp. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Fjärr-tarball | Filer i ett komprimerat arkiv på en fjärrwebbserver. | `http://remoteserver/myapp.tar.gz` |
| Artefakt i containerregistret | [OCI-artefaktfiler](container-registry-oci-artifacts.md) på en containerregisterdatabas. | `oci://myregistry.azurecr.io/myartifact:mytag` |

> [!NOTE]
> När du använder en privat Git-lagringsplatsen som kontext för en uppgift måste du ange en personlig åtkomsttoken (PAT).

## <a name="image-platforms"></a>Bildplattformar

Som standard ACR-uppgifter avbildningar för Linux-operativsystemet och amd64-arkitekturen. Ange taggen `--platform` för att skapa Windows-avbildningar eller Linux-avbildningar för andra arkitekturer. Ange operativsystemet och eventuellt en arkitektur som stöds i OS/arkitekturformat (till exempel `--platform Linux/arm` ). För ARM-arkitekturer kan du välja att ange en variant i OS/arkitektur/variant-format (till exempel `--platform Linux/arm64/v8` ):

| Operativsystem | Arkitektur|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Visa aktivitetens utdata

Varje aktivitetskörning genererar loggutdata som du kan granska för att avgöra om aktivitetsstegen har körts. När du utlöser en aktivitet manuellt strömmas loggutdata för aktivitetskörningen till konsolen och lagras även för senare hämtning. När en aktivitet utlöses automatiskt, till exempel genom en källkodsavbildning eller en uppdatering av basavbildningen, lagras endast aktivitetsloggar. Visa körningsloggarna i Azure Portal eller använd [kommandot az acr task logs.](/cli/azure/acr/task#az_acr_task_logs)

Läs mer om att [visa och hantera aktivitetsloggar.](container-registry-tasks-logs.md)

## <a name="next-steps"></a>Nästa steg

När du är redo att automatisera containeravbildningsbyggen och underhåll i molnet kan du gå ACR-uppgifter [självstudieserien](container-registry-tutorial-quick-task.md).

Du kan också installera [Docker-tillägget för Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) och [Azure-kontotillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) för att arbeta med dina Azure-containerregister. Hämta och push-pusha avbildningar till ett Azure-containerregister eller kör ACR-uppgifter i Visual Studio Code.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
