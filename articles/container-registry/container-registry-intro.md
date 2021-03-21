---
title: Hanterade behållar register
description: Introduktion till Azure Container Registry-tjänsten, som tillhandahåller molnbaserade, hanterade, privata Docker-register.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: b5c81d8b2e6d7eac2dcd9070bf1f448340ea1a18
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341273"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introduktion till privata Docker-containerregister i Azure

Azure Container Registry är en hanterad privat Docker-registerpost som baseras på Docker-registret 2,0 med öppen källkod. Skapa och underhåll Azure Container register för att lagra och hantera dina privata Docker-behållar avbildningar och relaterade artefakter.

Använd Azures behållar register med din befintliga pipeline för utveckling och distribution av behållare, eller Använd Azure Container Registry uppgifter för att bygga behållar avbildningar i Azure. Bygg på begäran eller gör helt automatiserade versioner med utlösare, till exempel käll kods incheckningar och bas avbildnings uppdateringar.

Mer information om Docker-och Registry-koncept finns i [Docker-översikten](https://docs.docker.com/engine/docker-overview/) och [om register, databaser och avbildningar](container-registry-concepts.md).

## <a name="use-cases"></a>Användningsfall

Hämta avbildningar från ett Azure-containerregister till olika distributionsmål:

* **Skalbart dirigeringssystem** som hanterar program i containrar över kluster med värdar, inklusive [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) och [Docker Swarm](https://docs.docker.com/get-started/swarm-deploy/).
* **Azure-tjänster** som stöder att skapa och köra program i skala, inkluderar [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [ Service Fabric](../service-fabric/index.yml), med mera.

Utvecklare kan även skicka till ett behållarregister som en del av ett arbetsflöde för utveckling av container. Du kan till exempel rikta in ett behållar register från ett kontinuerligt integrerings-och leverans verktyg, till exempel [Azure-pipeliner](/azure/devops/pipelines/ecosystems/containers/acr-template) eller [Jenkins](https://jenkins.io/).

Konfigurera ACR-aktiviteter för att automatiskt återskapa program avbildningar när deras bas avbildningar uppdateras, eller automatisera avbildningar när ditt team gör en kod till en git-lagringsplats. Skapa aktiviteter i flera steg för att automatisera skapandet, testning och korrigering av flera behållar avbildningar parallellt i molnet.

Azure tillhandahåller verktyg, till exempel Azure Command-Line Interface, Azure Portal och API-stöd för att hantera dina Azure Container register. Du kan också installera [Docker-tillägget för Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) och tillägget [Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) för att arbeta med dina Azure Container register. Hämta och push-avbildningar till ett Azure Container Registry, eller kör ACR-aktiviteter, allt i Visual Studio Code.

## <a name="key-features"></a>Huvudfunktioner

* **Register tjänst nivåer** – skapa en eller flera behållar register i din Azure-prenumeration. Register finns på tre nivåer: [Basic, standard och Premium](container-registry-skus.md), som är ett stöd för webhook-integrering, registerscanner med Azure Active Directory och ta bort funktioner. Dra nytta av lokal, nätverksnära lagring av dina containeravbildningar genom att skapa ett register på samma Azure-plats som dina distributioner. Använd funktionen [geo-replikering](container-registry-geo-replication.md) i Premium-register för avancerad replikering och distributionsscenarier för containeravbildningar. 

* **Säkerhet och åtkomst** – du loggar in i ett register med hjälp av Azure CLI eller standard- `docker login` kommandot. Azure Container Registry överför behållar avbildningar över HTTPS och stöder TLS för att skydda klient anslutningar. 

  > [!IMPORTANT]
  > Från och med 13 januari 2020 kräver Azure Container Registry alla säkra anslutningar från servrar och program för att använda TLS 1,2. Aktivera TLS 1,2 med hjälp av en senaste docker-klient (version 18.03.0 eller senare). Stöd för TLS 1,0 och 1,1 kommer att dras tillbaka. 

  Du [styr åtkomsten](container-registry-authentication.md) till en container med hjälp av en Azure-identitet, ett Azure Active Directory-kopplat [tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md) eller ett angivet administratörskonto. Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att tilldela användare eller system detaljerade behörigheter till ett register.

  Säkerhetsfunktionerna i Premium service-nivån är [innehålls förtroende](container-registry-content-trust.md) för bildtagg-signering, och [brand väggar och virtuella nätverk (för hands version)](container-registry-vnet.md) för att begränsa åtkomsten till registret. Azure Security Center kan integreras med Azure Container Registry för att [skanna bilder](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) när en bild skickas till ett register.

* **Bilder och artefakter som stöds** – grupperade i en lagrings plats, är varje avbildning en skrivskyddad ögonblicks bild av en Docker-kompatibel behållare. Azure-containerregister kan innehålla både Windows- och Linux-avbildningar. Du styr avbildningsnamnen för alla containerdistributioner. Använd [Docker-standardkommandon](https://docs.docker.com/engine/reference/commandline/) för att skicka avbildningar till en lagringsplats, eller för att hämta en avbildning från en lagringsplats. Förutom Docker-behållar avbildningar, Azure Container Registry lagrar [relaterade innehålls format](container-registry-image-formats.md) , till exempel [Helm-diagram](container-registry-helm-repos.md) och bilder som skapats i [specifikationen Open container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Automatiserade avbildnings versioner** – Använd [Azure Container Registry uppgifter](container-registry-tasks-overview.md) (ACR-aktiviteter) för att effektivisera skapandet, testning, spridning och distribution av avbildningar i Azure. Du kan till exempel använda ACR-aktiviteter för att utöka din utvecklings inre-slinga till molnet genom att avlasta `docker build` åtgärder till Azure. Konfigurera skaparuppgifter för att automatisera din korrigeringspipeline för operativsystems- och ramverkscontainrar och skapa avbildningar automatiskt när ditt team checkar in kod för källkontroll.

  [Aktiviteter med flera steg](container-registry-tasks-overview.md#multi-step-tasks) innehåller stegvisa aktivitets definitioner och körning för att skapa, testa och korrigera behållar avbildningar i molnet. Uppgiftsstegen definierar enskilda containeravbildningars bygg- och push-åtgärder. De kan också definiera körningen av en eller flera container så varje steg använder containern som sin körningsmiljö.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett containerregister med hjälp av Azure Portal](container-registry-get-started-portal.md)
* [Skapa ett behållar register med hjälp av Azure CLI](container-registry-get-started-azure-cli.md)
* [Automatisera behållar versioner och underhåll med ACR-uppgifter](container-registry-tasks-overview.md)