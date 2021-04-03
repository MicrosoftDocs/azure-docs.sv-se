---
title: Migrera till Bridge to Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Beskriver migreringsprocessen från Azure dev Spaces till Bridge till Kubernetes
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes-tjänsten, behållare, bro till Kubernetes
ms.openlocfilehash: d48814df30c17f9b51d8642efa0960a26bbd24f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94888529"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrera till Bridge to Kubernetes

> [!IMPORTANT]
> Azure dev Spaces kommer att dras tillbaka den 31 oktober 2023. Kunderna bör övergå till att använda Bridge till Kubernetes, ett verktyg för klient utvecklare.
>
> Syftet med Azure dev Spaces var att över gångs användare till utveckling på Kubernetes. En betydande kompromiss i tillvägagångs sättet med Azure dev Spaces gjorde extra bördan för användare för att förstå Docker-och Kubernetes-konfigurationer samt Kubernetes-distributions koncept. Med tiden är det också uppenbart att metoden för Azure dev Spaces inte på ett effektivt sätt minskade hastigheten för inre loop-utveckling på Kubernetes. Brygga till Kubernetes minskar hastigheten på inre loop-utveckling och undviker onödig börda för användare.
>
> Kärn uppdraget är oförändrat: skapa de bästa upplevelserna för att utveckla, testa och felsöka mikrotjänst kod i samband med det större programmet.

Bridge to Kubernetes ger ett lättare vikt alternativ till många av de utvecklings scenarier som fungerar med Azure dev Spaces. Bridge till Kubernetes är bara en upplevelse på klient sidan som använder tillägg i [Visual Studio][vs]   och [Visual Studio Code][vsc].  

Bridge to Kubernetes hjälper din utvecklings upplevelse genom att göra det möjligt för ett etablerat Kubernetes-program att inkludera en tjänst som körs på din lokala utvecklings arbets Station. Till skillnad från dev-Spaces minskar Kubernetes av inre slingor genom att de behöver skapa Docker-och Kubernetes-konfigurationer, så att utvecklare kan fokusera rent på affärs logiken i sin mikroservice-kod.

Med Bridge to Kubernetes kan du iterera på kod som körs på din utvecklings dator samtidigt som du använder beroenden och befintlig konfiguration från din Kubernetes-miljö. Azure dev-utrymmen distribuerar däremot mikrotjänsten i Kubernetes-miljön innan du kan fjärrfelsöka tjänsten och iterera på din kod.

Den här artikeln innehåller en jämförelse mellan Azure dev Spaces och Bridge to Kubernetes samt anvisningar för att migrera från Azure dev Spaces till Bridge till Kubernetes.

## <a name="development-approaches"></a>Utvecklings metoder

![Utvecklings metoder](media/migrate-to-btk/development-approaches.svg)

Azure dev Spaces hjälpte Kubernetes-utvecklare att arbeta med kod som körs direkt i sitt AKS-kluster, vilket undviker behovet av en lokal miljö som inte liknar den distribuerade miljön. Den här metoden har förbättrat vissa aspekter av utveckling, men har också infört en förutsättning för att lära sig och underhålla ytterligare begrepp som Docker, Kubernetes och Helm innan du kan börja använda Azure dev Spaces.

Med Bridge till Kubernetes kan utvecklare arbeta direkt på sina utvecklings datorer samtidigt som du interagerar med resten av klustret. Den här metoden drar nytta av den välbekanta och snabba körnings koden för att köra kod direkt på sina utvecklings datorer och dela de beroenden och den miljö som tillhandahålls av klustret. Den här metoden drar också nytta av den åter givning och skalning som kommer att köras i Kubernetes.

## <a name="feature-comparison"></a>Jämförelse av funktioner

Azure dev Spaces och Bridge till Kubernetes har liknande funktioner, men de skiljer sig också på flera områden:

| Krav  | Azure Dev Spaces  | Bridge to Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | I 15 Azure-regioner | Alla AKS-tjänsteregion    |
| **Säkerhet** |
| Säkerhets åtkomst krävs i klustret  | AKS-kluster deltagare  | Kubernetes RBAC – distributions uppdatering   |
| Säkerhets åtkomst krävs på din utvecklings dator  | Ej tillämpligt  | Lokal administratör/sudo   |
| **Användbarhet** |
| Oberoende av Kubernetes och Docker-artefakter  | Inga  | Ja   |
| Automatisk återställning av ändringar, efter fel sökning  | Inga  | Ja   |
| **Klient verktyg som stöds** |
| Fungerar med Visual Studio 2019  | Ja  | Ja   |
| Fungerar med Visual Studio Code  | Ja  | Ja   |
| Fungerar med CLI  | Ja  | Inga   |
| **Kompatibilitet för operativ system** |
| Fungerar i Windows 10  | Ja  | Ja  |
| Fungerar på Linux  | Ja  | Ja  |
| Fungerar på macOS  | Ja  | Ja  |
| **Funktioner** |
| Isolering av utvecklare eller grupp utveckling  | Ja  | Ja  |
| Skriv över miljövariabler selektivt  | Inga  | Ja  |
| Skapa Dockerfile-och Helm-diagram  | Ja  | Inga  |
| Beständig distribution av kod till Kubernetes  | Ja  | Inga  |
| Fjärrfelsökning i en Kubernetes-Pod  | Ja  | Inga  |
| Lokal fel sökning, ansluten till Kubernetes  | Inga  | Ja  |
| Felsöka flera tjänster samtidigt, på samma arbets Station  | Ja  | Ja  |

## <a name="kubernetes-inner-loop-development"></a>Kubernetes inre loop-utveckling

Den största skillnaden mellan Azure dev Spaces och Bridge till Kubernetes är den plats där koden körs. Azure dev Spaces hjälper till att utveckla och felsöka din mikroservice-kod, men kräver att du kör koden i klustret. Med Bridge to Kubernetes kan du utveckla och felsöka din mikroservice-kod direkt på din utvecklings dator samtidigt som du fortfarande är i samma kontext som det större programmet som körs i Kubernetes. Bridge to Kubernetes utökar omkretsen för Kubernetes-klustret och tillåter lokala processer att ärva konfigurationen från Kubernetes.

![Inre loop-utveckling](media/migrate-to-btk/btk-graphic-non-isolated.gif)

När du använder Bridge på Kubernetes upprättas en nätverks anslutning mellan din utvecklings dator och ditt kluster.Under den här anslutningens livs längd läggs en proxy till i klustret i stället för din Kubernetes-distribution som omdirigerar begär anden till tjänsten till utvecklings datorn. När du kopplar från så återgår program distributionen till att använda den ursprungliga versionen av distributionen som körs i klustret. Den här metoden skiljer sig från hur Azure dev Spaces fungerar i vilken kod som är synkroniserad med klustret, vilket skapas och körs. Azure dev-utrymmen återställer inte heller koden.

Brygga till Kubernetes har flexibiliteten att arbeta med program som körs i Kubernetes, oavsett vilken distributions metod som används. Om du använder CI/CD för att skapa och köra ditt program, oavsett om du använder etablerade verktyg eller anpassade skript, kan du fortfarande använda Bridge för att Kubernetes för att utveckla och felsöka din kod.

> [!TIP]
> Med [Microsoft Kubernetes-tillägget][kubernetes-extension] kan du snabbt utveckla Kubernetes-manifest med IntelliSense och hjälpa Autogenerera Helm-diagram.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Över gång till Bridge till Kubernetes från Azure dev Spaces

1. Om du använder Visual Studio uppdaterar du Visual Studio IDE till version 16,7 eller senare och installerar bryggan till Kubernetes-tillägget från [Visual Studio Marketplace][vs-marketplace]. Om du använder Visual Studio Code installerar du [bryggan till Kubernetes-tillägget][vsc-marketplace].
1. Inaktivera Azure dev Spaces-styrenheten med hjälp av Azure Portal eller [Azure dev Spaces CLI][azds-delete].
1. Använd [Azure Cloud Shell](https://shell.azure.com). Eller på Mac, Linux eller Windows med bash installerat öppnar du en bash shell-prompt. Se till att följande verktyg är tillgängliga i din kommando rads miljö: Azure CLI, Docker, kubectl, sväng, tjära och gunzip.
1. Skapa ett behållar register eller Använd ett befintligt. Du kan skapa ett behållar register i Azure med [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) eller med hjälp av [Docker Hub](https://hub.docker.com/). När du använder Azure Cloud Shell är det bara Azure Container Registry tillgängligt för Docker-avbildningar.
1. Kör skriptet för migrering för att konvertera Azure dev Spaces-tillgångar till Bridge till Kubernetes-tillgångar. Skriptet skapar en ny avbildning som är kompatibel med Bridge till Kubernetes, laddar upp den till det angivna registret och använder sedan [Helm](https://helm.sh) för att uppdatera klustret med avbildningen. Du måste ange resurs gruppen, namnet på AKS-klustret och ett behållar register. Det finns andra kommando rads alternativ som du ser här:

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   Skriptet stöder följande flaggor:

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. Migrera manuellt anpassningar, till exempel miljövariabel inställningar, i *azds. yaml* till projektets *Values. yml* -fil.
1. valfritt Ta bort `azds.yaml` filen från projektet.
1. Konfigurera brygga till Kubernetes i det distribuerade programmet. Mer information om hur du använder Bridge för att Kubernetes i Visual Studio finns i [använda Bridge för att Kubernetes i Visual Studio][use-btk-vs]. För VS Code, se [Använd Bridge för att Kubernetes i vs Code][use-btk-vsc].
1. Starta fel sökningen med den nyskapade bryggan för att Kubernetes fel söknings-/start profil.
1. Du kan köra skriptet igen om det behövs för att distribuera om till klustret.

## <a name="team-development-in-a-shared-cluster"></a>Grupp utveckling i ett delat kluster

Du kan också använda utvecklare-speciell routning med Bridge till Kubernetes. Azure dev Spaces Team Development-scenariot använder flera Kubernetes-namnområden för att isolera en tjänst från resten av programmet med hjälp av begreppet över-och underordnade namn områden. Bridge to Kubernetes erbjuder samma funktion, men med förbättrade prestanda egenskaper och inom samma program namn område.

Både Bridge till Kubernetes och Azure dev Spaces kräver att HTTP-huvuden finns och sprids i hela programmet. Om du redan har konfigurerat ditt program för att hantera huvud spridningen för Azure dev Spaces måste rubriken uppdateras. Om du vill gå över till Bridge till Kubernetes från Azure dev Spaces, uppdaterar du konfigurerad rubrik från *azds-Route – som* till *Kubernetes-Route-as*.

## <a name="evaluate-bridge-to-kubernetes"></a>Utvärdera brygga till Kubernetes

Om du vill experimentera med Bridge till Kubernetes innan du inaktiverar Azure dev Spaces i klustret, är det enklaste sättet att använda ett nytt kluster. Om du försöker använda Azure dev Spaces och Bridge för att Kubernetes samtidigt i samma kluster, kan du stöta på problem när du använder routningsfunktioner i båda.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Använd Visual Studio för att utvärdera Bridge till Kubernetes

1. Uppdatera Visual Studio IDE till version 16,7 eller senare och installera bryggan till Kubernetes-tillägget från [Visual Studio Marketplace][vs-marketplace].
1. Skapa ett nytt AKS-kluster och distribuera ditt program. Du kan också använda ett [exempel program][btk-sample-app].
1. Konfigurera brygga till Kubernetes i det distribuerade programmet. Mer information om hur du använder Bridge för att Kubernetes i Visual Studio finns i [använda Bridge till Kubernetes][use-btk-vs].
1. Starta fel sökning i Visual Studio med den nyskapade bryggan för att Kubernetes fel söknings profil.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Använd Visual Studio Code för att utvärdera Bridge till Kubernetes

1. Installera [bryggan till Kubernetes-tillägget][vsc-marketplace].
1. Skapa ett nytt AKS-kluster och distribuera ditt program. Du kan också använda ett [exempel program][btk-sample-app].
1. Konfigurera brygga till Kubernetes i det distribuerade programmet. Mer information om hur du använder Bridge för att Kubernetes i Visual Studio Code finns i [använda Bridge till Kubernetes][use-btk-vsc].
1. Starta fel sökning i Visual Studio med den nyskapade bryggan för att Kubernetes start profil.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du Kubernetes fungerar i Bridge.

> [!div class="nextstepaction"]
> [Så här fungerar brygga till Kubernetes][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
