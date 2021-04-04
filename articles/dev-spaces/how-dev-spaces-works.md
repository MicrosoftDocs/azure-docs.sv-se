---
title: Så här fungerar Azure Dev Spaces
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Beskriver de processer som Power Spaces för Azure dev
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 8de2c27ce03c871e60b6437656ad630fc8de8408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91963711"
---
# <a name="how-azure-dev-spaces-works"></a>Så här fungerar Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Det kan vara svårt att utveckla ett Kubernetes-program. Du behöver Docker-och Kubernetes-konfigurationsfiler. Du måste ta reda på hur du testar programmet lokalt och interagerar med andra beroende tjänster. Du kan behöva hantera utveckling och testning av flera tjänster samtidigt och med en grupp utvecklare.

Med Azure dev Spaces får du flera sätt att snabbt iterera och felsöka Kubernetes-program och samar beta med ditt team. I den här artikeln beskrivs vad Azure dev Spaces kan göra och hur det fungerar.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Iterera snabbt och Felsök ditt Kubernetes-program

Azure dev Spaces minskar arbetet för att utveckla, testa och iterera ditt Kubernetes-program i samband med ditt AKS-kluster. Den här minskningen av ansträngning gör att utvecklare kan fokusera på affärs logiken i sina program och inte konfigurera deras tjänster för att köras i Kubernetes.

### <a name="bridge-to-kubernetes"></a>Bridge to Kubernetes

Med Bridge till Kubernetes kan du ansluta din utvecklings dator till ditt Kubernetes-kluster, så att du kan köra och felsöka kod på din utvecklings dator som om den kördes i klustret. Bridge to Kubernetes omdirigerar trafiken mellan det anslutna klustret genom att köra en POD på klustret som fungerar som en fjärragent för att dirigera om trafiken mellan din utvecklings dator och klustret. Med den här trafiken i trafiken kan kod på din utvecklings dator och tjänster som körs i klustret kommunicera som om de var i samma kluster. Mer information om hur du ansluter din utvecklings dator till ett Kubernetes-kluster finns i [så här fungerar brygga till Kubernetes][how-it-works-bridge-to-kubernetes].

### <a name="run-your-code-in-aks"></a>Kör koden i AKS

Förutom att omdirigera trafik mellan din utvecklings dator och ditt AKS-kluster med Azure dev Spaces kan du konfigurera och snabbt köra koden direkt i AKS. Med Visual Studio, Visual Studio Code eller Azure dev Spaces CLI laddar Azure dev Spaces din kod till klustret och skapar och kör den sedan. Azure dev Spaces kan också intelligent synkronisera kod ändringar och starta om tjänsten för att avspegla ändringar vid behov. När du kör din kod strömmas build-loggar och HTTP-spår tillbaka till klienten så att du kan övervaka förloppet och diagnostisera eventuella problem. Du kan också använda Azure dev Spaces för att koppla fel sökaren i Visual Studio och Visual Studio Code till Java, Node.js och .NET Core-tjänster. Mer information finns i [så här förbereder du ett projekt för Azure dev Spaces][how-it-works-prep], [hur du kör din kod med Azure dev Spaces][how-it-works-up]och [hur fjärrfelsökar din kod med Azure dev Spaces fungerar][how-it-works-remote-debugging].

## <a name="team-development"></a>Teamutveckling

Azure dev Spaces hjälper teamen att arbeta effektivt med sitt program i samma AKS-kluster utan att störas.

### <a name="intelligent-routing-between-dev-spaces"></a>Intelligent routning mellan dev Spaces

Med Azure dev Spaces kan ett team dela ett enda AKS-kluster som kör ett moln program och skapa isolerade dev-utrymmen där teamet kan utveckla, testa och felsöka utan att störa de andra dev Spaces. En bas linje version av programmet körs i ett rot utvecklings utrymme. Grupp medlemmar skapar sedan oberoende underordnade dev-rymder baserat på rot utrymmet för utveckling, testning och fel sökning av program ändringar. Med hjälp av routningsfunktioner i dev Spaces kan underordnade dev-rymder dirigera förfrågningar mellan tjänster som körs i det underordnade dev-utrymmet och det överordnade dev-utrymmet. Med den här routningen kan utvecklare köra sin egen version av en tjänst samtidigt som de använder beroende tjänster från det överordnade utrymmet. Varje underordnat utrymme har sin egen unika URL, som kan delas och användas av andra för samarbete. Mer information om hur routning fungerar i Azure dev Spaces finns i [så här fungerar routning med Azure dev Spaces][how-it-works-routing].

### <a name="live-testing-an-open-pull-request"></a>Live testar en öppen pull-begäran

Du kan också använda GitHub åtgärder med Azure dev Spaces för att testa ändringar i programmet i en pull-begäran direkt i klustret innan sammanfogning. Azure dev Spaces kan automatiskt distribuera en gransknings version av programmet till klustret, så att både författaren och andra team medlemmar kan granska ändringarna i hela programmets sammanhang. Med hjälp av routningsfunktioner i Azure dev Spaces, distribueras den här gransknings versionen av programmet också till klustret utan att andra dev-rymder påverkas. Med alla dessa funktioner kan du på ett säkert sätt godkänna och slå samman pull-begäranden. Om du vill se ett exempel på GitHub-åtgärder och Azure dev Spaces, se [GitHub åtgärder & Azure Kubernetes service][pr-flow].

## <a name="next-steps"></a>Nästa steg

För att komma igång med att ansluta din lokala utvecklings dator till ditt AKS-kluster, se [ansluta din utvecklings dator till ett AKS-kluster][connect].


[connect]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[routing]: #team-development