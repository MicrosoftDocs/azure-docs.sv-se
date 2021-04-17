---
title: Migrera till Bridge to Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Beskriver migreringsprocessen från Azure Dev Spaces till Bridge till Kubernetes
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Bridge to Kubernetes
ms.openlocfilehash: 8ffb7693ff223a9cb952964ded1e6967ceeb326e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499299"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrera till Bridge to Kubernetes

> [!IMPORTANT]
> Azure Dev Spaces dras tillbaka den 31 oktober 2023. Kunder bör gå över till att använda Bridge to Kubernetes, ett klientutvecklarverktyg.
>
> Syftet med Azure Dev Spaces var att underlätta för användare att utveckla på Kubernetes. En betydande kompromiss i Metoden med Azure Dev Spaces var att lägga extra tid på användarna på att förstå Docker- och Kubernetes-konfigurationer samt Begrepp för Kubernetes-distribution. Med tiden blev det också tydligt att metoden med Azure Dev Spaces inte effektivt minskade hastigheten för utveckling av inre loopar på Kubernetes. Bridge to Kubernetes minskar effektivt hastigheten för utveckling av inre loopar och undviker onödig belastning på användarna.
>
> Kärnuppdraget förblir oförändrat: Skapa de bästa upplevelserna för att utveckla, testa och felsöka mikrotjänstkod i kontexten för det större programmet.

Bridge to Kubernetes är ett enklare alternativ till många av de utvecklingsscenarier som fungerar med Azure Dev Spaces. Bridge to Kubernetes är en upplevelse på klientsidan som endast använder [tillägg i Visual Studio][vs]och Visual Studio    [Code][vsc].  

Bridge to Kubernetes hjälper din utvecklingsupplevelse genom att låta ett etablerat Kubernetes-program inkludera en tjänst som körs på din lokala utvecklingsarbetsdator. Till skillnad från Dev Spaces minskar Bridge to Kubernetes den inre loopens komplexitet genom att stega behovet av att skapa Docker- och Kubernetes-konfigurationer, så att utvecklare kan fokusera enbart på affärslogiken i sin mikrotjänstkod.

Bridge to Kubernetes hjälper dig att iterera på kod som körs på utvecklingsdatorn samtidigt som du förbrukar beroenden och befintlig konfiguration från Kubernetes-miljön. Däremot distribuerar Azure Dev Spaces din mikrotjänst till Kubernetes-miljön innan du fjärrfelsöker tjänsten och itererar koden.

Den här artikeln innehåller en jämförelse mellan Azure Dev Spaces och Bridge to Kubernetes samt anvisningar för att migrera från Azure Dev Spaces till Bridge till Kubernetes.

## <a name="development-approaches"></a>Utvecklingsmetoder

![Utvecklingsmetoder](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces hjälpte Kubernetes-utvecklare att arbeta med kod som körs direkt i sina AKS-kluster, vilket gjorde att de inte behövde en lokal miljö som inte liknade den distribuerade miljön. Den här metoden förbättrade vissa aspekter av utvecklingen, men införde också en förutsättning för att lära sig och underhålla ytterligare begrepp som Docker, Kubernetes och Helm innan du kunde börja använda Azure Dev Spaces.

Med Bridge to Kubernetes kan utvecklare arbeta direkt på sina utvecklingsdatorer samtidigt som de interagerar med resten av klustret. Den här metoden utnyttjar den bekanta och snabba körningen av kod direkt på utvecklingsdatorerna och delar de beroenden och den miljö som tillhandahålls av klustret. Den här metoden drar också nytta av den återgivning och skalning som kommer från att köras i Kubernetes.

## <a name="feature-comparison"></a>Funktionsjämförelse

Azure Dev Spaces och Bridge to Kubernetes har liknande funktioner. De skiljer sig också åt inom flera områden:

| Krav  | Azure Dev Spaces  | Bridge to Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | I 15 Azure-regioner | Valfri AKS-tjänstregion    |
| **Säkerhet** |
| Säkerhetsåtkomst krävs i klustret  | AKS-klusterdeltagare  | Kubernetes RBAC – distributionsuppdatering   |
| Säkerhetsåtkomst krävs på utvecklingsdatorn  | Ej tillämpligt  | Lokal administratör/sudo   |
| **Användbarhet** |
| Oberoende av Kubernetes- och Docker-artefakter  | Inga  | Ja   |
| Automatisk återställning av ändringar, efter felsökning  | Inga  | Ja   |
| **Klientverktyg som stöds** |
| Fungerar med Visual Studio 2019  | Ja  | Ja   |
| Fungerar med Visual Studio Code  | Ja  | Ja   |
| Fungerar med cli  | Ja  | Inga   |
| **Kompatibilitet för operativsystem** |
| Fungerar på Windows 10  | Ja  | Ja  |
| Fungerar i Linux  | Ja  | Ja  |
| Fungerar i macOS  | Ja  | Ja  |
| **Funktioner** |
| Isolering för utvecklare eller teamutveckling  | Ja  | Ja  |
| Skriva över miljövariabler selektivt  | Inga  | Ja  |
| Skapa Dockerfile- och Helm-diagram  | Ja  | Inga  |
| Beständig distribution av kod till Kubernetes  | Ja  | Inga  |
| Fjärrfelsökning i en Kubernetes-podd  | Ja  | Inga  |
| Lokal felsökning, ansluten till Kubernetes  | Inga  | Ja  |
| Felsöka flera tjänster samtidigt på samma arbetsstation  | Ja  | Ja  |

## <a name="kubernetes-inner-loop-development"></a>Utveckling av inre Kubernetes-loopar

Den största skillnaden mellan Azure Dev Spaces och Bridge to Kubernetes är var din kod körs. Azure Dev Spaces hjälper till att utveckla och felsöka din mikrotjänstkod, men kräver att du kör den koden i klustret. Med Bridge to Kubernetes kan du utveckla och felsöka din mikrotjänstkod direkt på utvecklingsdatorn samtidigt som det större programmet körs i Kubernetes. Bridge to Kubernetes utökar kubernetes-klustrets perimeter och gör att lokala processer kan ärva konfigurationen från Kubernetes.

![Utveckling av inre loopar](media/migrate-to-btk/btk-graphic-non-isolated.gif)

När du använder Bridge to Kubernetes upprättas en nätverksanslutning mellan utvecklingsdatorn och klustret.Under hela anslutningens livslängd läggs en proxy till i klustret i stället för kubernetes-distributionen som omdirigerar begäranden till tjänsten till utvecklingsdatorn. När du kopplar från återgår programdistributionen till att använda den ursprungliga versionen av distributionen som körs i klustret. Den här metoden skiljer sig från hur Azure Dev Spaces fungerar där koden synkroniseras till klustret, byggs och sedan körs. Azure Dev Spaces återställer inte heller koden.

Bridge to Kubernetes har flexibiliteten att arbeta med program som körs i Kubernetes oavsett distributionsmetod. Om du använder CI/CD för att skapa och köra ditt program, oavsett om du använder etablerade verktyg eller anpassade skript, kan du fortfarande använda Bridge to Kubernetes för att utveckla och felsöka din kod.

> [!TIP]
> Med [Microsoft Kubernetes-tillägget][kubernetes-extension] kan du snabbt utveckla Kubernetes-manifest med IntelliSense och få hjälp med att skapa Helm-diagram.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Övergång till Bridge to Kubernetes från Azure Dev Spaces

1. Om du använder Visual Studio uppdaterar du din Visual Studio IDE till version 16.7 eller senare och installerar tillägget Bridge to Kubernetes [från Visual Studio Marketplace][vs-marketplace]. Om du använder Visual Studio Code installerar du [tillägget Bridge to Kubernetes][vsc-marketplace].
1. Inaktivera Azure Dev Spaces-kontrollanten med hjälp Azure Portal eller [Azure Dev Spaces CLI][azds-delete].
1. Använd [Azure Cloud Shell](https://shell.azure.com). Eller öppna en bash shell-kommandotolk på Mac, Linux eller Windows med bash installerat. Kontrollera att följande verktyg är tillgängliga i kommandoradsmiljön: Azure CLI, docker, kubectl, curl, tar och gunzip.
1. Skapa ett containerregister eller använd ett befintligt. Du kan skapa ett containerregister i Azure med [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) eller med [hjälp av Docker Hub](https://hub.docker.com/). När du Azure Cloud Shell är endast Azure Container Registry tillgänglig som värd för Docker-avbildningar.
1. Kör migreringsskriptet för att konvertera Azure Dev Spaces-tillgångar till Bridge till Kubernetes-tillgångar. Skriptet skapar en ny avbildning som är kompatibel med Bridge to Kubernetes, laddar upp den till det avsedda registret och använder sedan [Helm](https://helm.sh) för att uppdatera klustret med avbildningen. Du måste ange resursgruppen, namnet på AKS-klustret och ett containerregister. Det finns andra kommandoradsalternativ som visas här:

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

1. Migrera anpassningar manuellt, till exempel inställningar för miljövariabler, i *azds.yaml* till projektets *values.yml-fil.*
1. (valfritt) Ta bort `azds.yaml` filen från projektet.
1. Konfigurera Bridge to Kubernetes i ditt distribuerade program. Mer information om hur du använder Bridge to Kubernetes i Visual Studio finns i [Använda Bridge to Kubernetes i Visual Studio][use-btk-vs]. För VS Code, se [Använda Bridge to Kubernetes i VS Code][use-btk-vsc].
1. Börja felsöka med hjälp av den nyligen skapade felsöknings-/startprofilen för Bridge to Kubernetes.
1. Du kan köra skriptet igen vid behov för att distribuera om till klustret.

## <a name="team-development-in-a-shared-cluster"></a>Teamutveckling i ett delat kluster

Du kan också använda utvecklarspecifik routning med Bridge till Kubernetes. Utvecklingsscenariot för Azure Dev Spaces-teamet använder flera Kubernetes-namnrymder för att isolera en tjänst från resten av programmet med hjälp av begreppet överordnade och underordnade namnrymder. Bridge to Kubernetes erbjuder samma funktion, men med förbättrade prestandaegenskaper och inom samma programnamnområde.

Både Bridge to Kubernetes och Azure Dev Spaces kräver att HTTP-huvuden finns och sprids i hela programmet. Om du redan har konfigurerat ditt program för att hantera rubrikspridning för Azure Dev Spaces måste huvudet uppdateras. Om du vill övergå till Bridge to Kubernetes från Azure Dev Spaces uppdaterar du det konfigurerade huvudet från *azds-route-as* till *kubernetes-route-as*.

## <a name="evaluate-bridge-to-kubernetes"></a>Utvärdera bridge till Kubernetes

Om du vill experimentera med Bridge to Kubernetes innan du inaktiverar Azure Dev Spaces i klustret är det enklaste sättet att använda ett nytt kluster. Om du försöker använda Azure Dev Spaces och Bridge to Kubernetes samtidigt i samma kluster får du problem när du använder routningsfunktionerna på båda.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Använda Visual Studio för att utvärdera Bridge to Kubernetes

1. Uppdatera din Visual Studio IDE till version 16.7 eller senare och installera tillägget Bridge to Kubernetes [från Visual Studio Marketplace][vs-marketplace].
1. Skapa ett nytt AKS-kluster och distribuera programmet. Du kan också använda ett [exempelprogram][btk-sample-app].
1. Konfigurera Bridge to Kubernetes i ditt distribuerade program. Mer information om hur du använder Bridge to Kubernetes i Visual Studio finns i [Använda Bridge to Kubernetes][use-btk-vs].
1. Starta felsökningen i Visual Studio den nyligen skapade felsökningsprofilen Bridge to Kubernetes.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Använda Visual Studio Code för att utvärdera Bridge to Kubernetes

1. Installera tillägget [Bridge to Kubernetes][vsc-marketplace].
1. Skapa ett nytt AKS-kluster och distribuera programmet. Du kan också använda ett [exempelprogram][btk-sample-app].
1. Konfigurera Bridge to Kubernetes i ditt distribuerade program. Mer information om hur du använder Bridge to Kubernetes i Visual Studio Code finns i [Använda Bridge to Kubernetes][use-btk-vsc].
1. Starta felsökningen i Visual Studio med hjälp av den nyligen skapade startprofilen för Bridge to Kubernetes.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur Bridge to Kubernetes fungerar.

> [!div class="nextstepaction"]
> [Så här fungerar Bridge to Kubernetes][how-it-works-bridge-to-kubernetes]


[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
