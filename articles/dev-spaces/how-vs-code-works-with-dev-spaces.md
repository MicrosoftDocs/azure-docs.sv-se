---
title: Hur Visual Studio Code fungerar med Azure dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Lär dig hur Visual Studio Code och Azure dev Spaces hjälper dig att felsöka och snabbt iterera dina Kubernetes-program
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: edfcb8107280bb86144b798da2d5b1c16371528e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960753"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Hur Visual Studio Code fungerar med Azure dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Du kan använda Visual Studio Code och [tillägget Azure dev Spaces][azds-extension] för att förbereda, köra och felsöka dina tjänster med Azure dev Spaces. Med Visual Studio Code och tillägget Azure dev Spaces kan du:

* Generera till gångar för att köra och felsöka tjänster i AKS
* Köra Java-, Node.js-och .NET Core-tjänster i ett dev-utrymme
* Direkt felsöka Java, Node.js och .NET Core-tjänster som körs i ett dev-utrymme

## <a name="generate-assets"></a>Generera till gångar

Visual Studio Code och tillägget Azure dev Spaces genererar följande till gångar för ditt projekt:

* Dockerfiles för Java-program med maven, Node.js program och .NET Core-program
* Helm diagram för nästan alla språk med en Dockerfile
* En `azds.yaml` fil, som är [konfigurations filen för Azure dev Spaces][azds-yaml] för ditt projekt
* En `.vscode` mapp med Visual Studio Code start Configuration för ditt projekt för Java-program med maven, Node.js program och .net Core-program

Dockerfile, Helm-diagrammet och- `azds.yaml` filerna är samma till gångar som genereras när de körs `azds prep` . Dessa filer kan också användas utanför Visual Studio Code för att köra projektet i AKS, som att köra `azds up` . `.vscode`Mappen används endast av Visual Studio Code för att köra projektet i AKS från Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Kör din tjänst i AKS

När du har genererat till gångarna för ditt projekt kan du köra Java-, Node.js-och .NET Core-tjänster i ett befintligt dev-utrymme från Visual Studio Code. På sidan *fel sökning* i Visual Studio Code kan du anropa Starta-konfigurationen från `.vscode` katalogen för att köra projektet.

Du måste skapa ditt AKS-kluster och aktivera Azure dev Spaces i ditt kluster utanför Visual Studio Code. Du kan återanvända befintliga Dockerfiles, Helm-diagram och `azds.yaml` filer som skapats utanför Visual Studio Code, till exempel de till gångar som genererats genom att köra `azds prep` . Om du återanvänder till gångar som skapats utanför Visual Studio Code måste du ändå ha en `.vscode` katalog. Den här `.vscode` katalogen kan återskapas av Visual Studio Code och tillägget Azure dev Spaces och dina befintliga till gångar skrivs inte över.

För .NET Core-projekt måste du ha [C#-tillägget][csharp-extension] installerat för att köra din .net-tjänst från Visual Studio Code. För Java-projekt som använder maven måste du också ha installerat [Java-felsökare för Azure dev Spaces-tillägget][java-extension] samt [maven installerat och konfigurerat][maven] för att köra Java-tjänsten från Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Felsöka tjänsten i AKS

När du har lanserat ditt projekt kan du felsöka Java-, Node.js-och .NET Core-tjänster som körs i ett dev-utrymme direkt från Visual Studio Code. Start konfigurationen i `.vscode` katalogen ger ytterligare felsöknings information för att köra en tjänst med fel sökning aktiverat i ett dev-utrymme. Visual Studio Code bifogar också fel söknings processen i den behållare som körs i dina dev Spaces, så att du kan ange Bryt punkter, granska variabler och utföra andra fel söknings åtgärder.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure dev Spaces fungerar.

> [!div class="nextstepaction"]
> [Så här fungerar Azure Dev Spaces](how-dev-spaces-works.md)

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
