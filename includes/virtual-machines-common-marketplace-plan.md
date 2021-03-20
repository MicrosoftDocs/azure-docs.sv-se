---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "71174996"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Distribuera en avbildning med Marketplace-villkor

Vissa VM-avbildningar på Azure Marketplace har ytterligare licens-och Köp villkor som du måste acceptera innan du kan distribuera dem program mässigt.  

Om du vill distribuera en virtuell dator från en sådan avbildning måste du både acceptera avbildningens villkor och aktivera program distribution. Du behöver bara göra detta en gång per prenumeration. Efteråt, varje gången du distribuerar en virtuell dator program mässigt från avbildningen, måste du också ange *inköps plan* parametrar.

I följande avsnitt visas hur du:

* Ta reda på om en Marketplace-avbildning har ytterligare licens villkor 
* Godkänn villkoren program mässigt
* Ange parametrar för inköps planer när du distribuerar en virtuell dator program mässigt

