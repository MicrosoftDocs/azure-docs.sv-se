---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bf01df2307796831dee602dad30455a4922ef90b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98873201"
---
| Resurs | Beskrivning|
|----------|------------|
| **Bild källa** | Det här är en resurs som kan användas för att skapa en **avbildnings version** i ett bild galleri. En avbildnings källa kan vara en befintlig virtuell Azure-dator som är antingen [generaliserad eller specialiserad](../articles/virtual-machines/shared-image-galleries.md#generalized-and-specialized-images), en hanterad avbildning, en ögonblicks bild eller en avbildnings version i ett annat bild galleri. |
| **Bild galleri** | Precis som Azure Marketplace är ett **avbildnings Galleri** en lagrings plats för att hantera och dela bilder, men du styr vem som har åtkomst. |
| **Bild definition** | Bild definitionerna skapas i ett galleri och bär information om avbildningen och kraven för att använda den internt. Detta inkluderar om avbildningen är Windows eller Linux, viktig information och minimi-och högsta minnes krav. Det är en definition av en typ av bild. |
| **Avbildningsversion** | En **avbildnings version** är vad du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. Som en hanterad avbildning används avbildnings versionen för att skapa nya diskar för den virtuella datorn när du använder en **avbildnings version** för att skapa en virtuell dator. Avbildnings versioner kan användas flera gånger. |