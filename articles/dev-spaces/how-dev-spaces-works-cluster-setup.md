---
title: Så här konfigurerar du ett kluster för Azure dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver hur du konfigurerar ett Azure Kubernetes service-kluster för Azure dev Spaces
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 841e67b96e95aa251fa5bf1ef469b68de30f54d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91972687"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Så här konfigurerar du ett kluster för Azure dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Med Azure dev Spaces får du flera sätt att snabbt iterera och felsöka Kubernetes-program och samar beta med ditt team i ett Azure Kubernetes service-kluster (AKS). Ett sätt är att aktivera Azure dev Spaces på ditt AKS-kluster så att du kan [köra tjänster direkt i klustret][how-it-works-up] och använda [ytterligare funktioner för nätverk och routning][how-it-works-routing]. I den här artikeln beskrivs vad som händer när du förbereder klustret och aktiverar Azure dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Förbereda ditt AKS-kluster

För att förbereda ditt AKS-kluster för dev Spaces kontrollerar du att ditt AKS-kluster finns i en region [som stöds av Azure dev Spaces][supported-regions] och du kör Kubernetes 1.10.3 eller senare. Du kan aktivera Azure dev Spaces från Azure CLI genom att köra `az aks use-dev-spaces` .

När du har aktiverat Azure dev Spaces på ditt AKS-kluster installeras kontrollanten för klustret. Kontrollanten finns utanför ditt AKS-kluster. Det styr beteende och kommunikation mellan klient sidans verktyg och AKS-klustret. När den är aktive rad kan du interagera med kontrollanten med hjälp av verktyg på klient sidan.

Kontrollanten utför följande åtgärder:

* Hanterar skapande och val av dev-utrymme.
* Installerar programmets Helm-diagram och skapar Kubernetes-objekt.
* Skapar programmets behållar avbildning.
* Distribuerar ditt program till AKS.
* Skapar stegvisa versioner och startar om när käll koden ändras.
* Hanterar loggar och HTTP-spår.
* Vidarebefordrar STDOUT och stderr till verktyg på klient sidan.
* Konfigurerar routning för program inom ett utrymme samt över över-och underordnade utrymmen.

Kontrollanten är en separat Azure-resurs utanför klustret och gör följande för resurser i klustret:

* Skapar eller anger ett Kubernetes-namnområde som ska användas som ett dev-utrymme.
* Tar bort alla Kubernetes-namnområden med namnet *azds*, om det finns, och skapar ett nytt.
* Distribuerar en Kubernetes webhook-konfiguration.
* Distribuerar en server för webhook-åtkomstkontroll.

Den använder samma tjänst huvud namn som ditt AKS-kluster använder för att skapa tjänst anrop till andra Azure dev Space-komponenter.

![Azure dev Spaces prepare-kluster](media/how-dev-spaces-works/prepare-cluster.svg)

För att kunna använda Azure dev Spaces måste det finnas minst ett dev-utrymme. I Azure dev Spaces används Kubernetes-namnområden i ditt AKS-kluster för dev Spaces. När en kontrollant installeras, blir du ombedd att skapa ett nytt Kubernetes-namnområde eller välja en befintlig namnrymd som ska användas som ditt första utvecklings utrymme. Som standard erbjuder kontrollanten att uppgradera det befintliga *standard* namn området Kubernetes till ditt första utvecklings utrymme.

När ett namn område anges som ett dev-utrymme lägger kontrollanten till *azds.io/Space=True* -etiketten i namn området för att identifiera den som ett dev-utrymme. Det inledande dev-utrymmet som du skapar eller anger är markerat som standard när du förbereder klustret. När ett blank steg är markerat används det av Azure dev Spaces för att skapa nya arbets belastningar.

Du kan använda klient sidans verktyg för att skapa nya dev-Spaces och ta bort befintliga dev-sidor. Det går inte att ta bort *standard* dev-utrymmet på grund av en begränsning i Kubernetes. Kontrollanten tar också bort alla befintliga Kubernetes-namnområden med namnet *azds* för att undvika konflikter med `azds` kommandot som används av klient sidans verktyg.

Kubernetes webhook-åtkomstkontroll används för att injicera poddar med tre behållare under distribution för instrumentering: en devspaces-proxy-behållare, en devspaces-proxy-init-behållare och en devspaces-build-behållare. **Alla tre dessa behållare körs med rot åtkomst i ditt AKS-kluster.** De använder också samma tjänst huvud namn som ditt AKS-kluster använder för att skapa tjänst anrop till andra Azure dev Spaces-komponenter.

![Azure dev Spaces Kubernetes webhook Admission Server](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Devspaces-proxy-behållaren är en sidvagn-behållare som hanterar all TCP-trafik till och från program behållaren och gör det möjligt att utföra routning. Devspaces-proxyn dirigerar om HTTP-meddelanden om vissa blank steg används. Det kan till exempel hjälpa att dirigera HTTP-meddelanden mellan program i överordnade och underordnade utrymmen. All icke-HTTP-trafik passerar genom devspaces-proxy oförändrad. Devspaces-proxy-behållaren loggar också alla inkommande och utgående HTTP-meddelanden och skickar dem till klient sidans verktyg som spår. Dessa spår kan sedan visas av utvecklaren för att kontrol lera programmets beteende.

Devspaces-proxy-init-behållaren är en [init-behållare](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) som lägger till ytterligare regler för routning baserat på utrymmes-hierarkin till programmets behållare. Den lägger till regler för routning genom att uppdatera program behållarens */etc/resolv.conf* -fil och program varan iptables-konfiguration innan den startas. Uppdateringarna av */etc/resolv.conf* tillåter DNS-matchning av tjänster i överordnade utrymmen. Konfigurations uppdateringarna för program varan iptables säkerställer att all TCP-trafik till och från programmets behållare dirigeras även om devspaces-proxy. Alla uppdateringar från devspaces-proxy-init sker utöver de regler som Kubernetes lägger till.

Devspaces-build-behållaren är en init-behållare och har projektets käll kod och Docker-socket monterad. Projekt käll koden och åtkomst till Docker gör att program behållaren kan skapas direkt av pod.

> [!NOTE]
> Azure dev Spaces använder samma nod för att skapa programmets behållare och köra den. Det innebär att Azure dev Spaces inte behöver ett externt behållar register för att skapa och köra ditt program.

Kubernetes webhook-åtkomstkontroll lyssnar efter nya Pod som skapats i AKS-klustret. Om Pod har distribuerats till ett namn område med *azds.io/Space=True* -etiketten injiceras Pod med ytterligare behållare. Devspaces-build-behållaren matas bara in om programmets behållare körs med klient sidans verktyg.

När du har för berett ditt AKS-kluster kan du använda verktyg på klient sidan för att förbereda och köra din kod i ditt utvecklings utrymme.

## <a name="client-side-tooling"></a>Verktyg på klient Sidan

Med verktyg på klient sidan kan användaren:
* Skapa ett Dockerfile-, Helm-diagram och konfigurations filen för Azure dev Spaces för programmet.
* Skapa överordnade och underordnade dev-sidor.
* Be kontrollanten att skapa och starta ditt program.

När ditt program körs, kan verktyg på klient sidan också:
* Tar emot och visar STDOUT och stderr från ditt program som körs i AKS.
* Använder [Port-Forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) för att tillåta webb åtkomst till ditt program med http: \/ /localhost.
* Bifogar en fel sökare till det program som körs i AKS.
* Synkroniserar käll koden till ditt dev-utrymme när en ändring identifieras för stegvisa versioner, vilket möjliggör snabb iteration.
* Gör att du kan ansluta din utvecklings dator direkt till ditt AKS-kluster.

Du kan använda klient sidans verktyg från kommando raden som en del av `azds` kommandot. Du kan också använda verktyget på klient sidan med:

* Visual Studio Code med [tillägget Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio med arbets belastningen Azure Development.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder verktyg för klient sidan för att förbereda och köra din kod i ditt utvecklings utrymme finns i [så här förbereder du ett projekt för Azure dev Spaces][how-it-works-prep].


[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service