---
title: Så här fungerar fjärrfelsökning av din kod med Azure dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver processer för fjärrfelsökning i Azure Kubernetes service med Azure dev Spaces
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 0487b80d23974a66bafe93ee1fbdf9b796d0ab53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975050"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Så här fungerar fjärrfelsökning av din kod med Azure dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Med Azure dev Spaces får du flera sätt att snabbt iterera och felsöka Kubernetes-program och samar beta med ditt team i ett Azure Kubernetes service-kluster (AKS). När projektet körs i ett dev-utrymme är Azure dev Spaces ett sätt att ansluta till och felsöka ett program som körs i AKS.

Den här artikeln beskriver hur fjärrfelsökning med dev Spaces fungerar.

## <a name="debug-your-code"></a>Felsök koden

För Java-, .NET Core-och Node.js-program kan du felsöka ditt program som körs direkt i ditt utvecklings utrymme med Visual Studio Code eller Visual Studio. Visual Studio Code och Visual Studio ger verktyg för att ansluta till ditt utvecklings utrymme, starta ditt program och koppla en fel sökare. När du har kört `azds prep` kan du öppna projektet i Visual Studio Code eller Visual Studio. Visual Studio Code eller Visual Studio genererar egna konfigurationsfiler för anslutning som är åtskilda från att köras `azds prep` . I Visual Studio Code eller Visual Studio kan du ange Bryt punkter och starta ditt program i ditt utvecklings utrymme.

![Felsöka koden](media/get-started-node/debug-configuration-nodejs2.png)

När du startar ditt program med Visual Studio Code eller Visual Studio för fel sökning, hanterar de start och ansluter till ditt dev-utrymme på samma sätt som körs `azds up` . Verktyget på klient sidan i Visual Studio Code och Visual Studio tillhandahåller dessutom ytterligare en parameter med detaljerad information för fel sökning. Parametern innehåller namnet på fel söknings avbildningen, platsen för fel söknings programmet i fel söknings avbildningen och mål platsen i programmets behållare för att montera fel söknings mappen.

Fel söknings avbildningen bestäms automatiskt av klient sidans verktyg. Den använder en metod som liknar den som används under Dockerfile och Helm-diagrammet genereras när den körs `azds prep` . När fel söknings programmet är monterat i programmets avbildning körs det med `azds exec` .

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure dev Spaces fungerar.

> [!div class="nextstepaction"]
> [Så här fungerar Azure Dev Spaces](how-dev-spaces-works.md)
