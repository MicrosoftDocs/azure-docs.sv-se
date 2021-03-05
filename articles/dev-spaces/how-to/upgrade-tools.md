---
title: Uppgradera Azure dev Spaces-verktyg
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
ms.custom: devx-track-azurecli
description: Lär dig hur du uppgraderar kommando rads verktyg för Azure dev Spaces, Visual Studio Code extension och Visual Studio extension
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
ms.openlocfilehash: f17643e6130abbc9d5da8b484144c95b0e803f33
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199245"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Uppgradera Azure dev Spaces-verktyg

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Om det finns en ny version och du redan använder Azure dev Spaces kan du behöva uppgradera dina klient verktyg för Azure dev Spaces.

## <a name="update-the-azure-cli"></a>Uppdatera Azure CLI

När du uppdaterar den senaste versionen av Azure CLI får du också den senaste versionen av dev Spaces CLI-tillägget.

Du behöver inte avinstallera den tidigare versionen. du hittar bara lämplig nedladdning på [Azure CLI](/cli/azure/install-azure-cli).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Uppdatera CLI-tillägget för dev Spaces och kommando rads verktygen

Kör följande kommando:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Uppdatera VS Code-tillägget

När du har installerat uppdateras tillägget automatiskt. Du kan behöva läsa in tillägget igen för att använda de nya funktionerna. I VS Code öppnar du fönstret **tillägg** , väljer tillägget **Azure dev Spaces** och väljer **Läs in igen**.

## <a name="update-visual-studio"></a>Uppdatera Visual Studio

Azure dev Spaces är en del av arbets belastningen Azure Development och ingår i alla Visual Studio-uppdateringar.

## <a name="next-steps"></a>Nästa steg

Testa de nya verktygen genom att skapa ett nytt kluster. Prova snabb starter och självstudier på [Azure dev Spaces](../index.yml).
