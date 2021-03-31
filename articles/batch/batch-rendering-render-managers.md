---
title: Stöd för åter givnings hanterare
description: Använda Azure Batch rendering Manager-integrering. Lär dig mer om inbyggda support eller tillägg för populära åter givnings hanterare.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: c44cbf86d8bf2fe83a6dc91dee1c4f58eec156c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83726459"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Använda Azure Batch med rendera Server grupps hanterare

Om du använder en befintlig lokal åter givnings Server grupp, är det mycket troligt att en åter givnings hanterare styr kapaciteten för rendering-servergruppen och rendera jobb.

Azure tillhandahåller antingen inbyggd support eller tillägg för populära åter givnings hanterare. Du kan sedan lägga till och ta bort virtuella Azure-datorer, inklusive virtuella datorer med program licensieringen betala för användning och virtuella datorer med låg prioritet.

Följande åter givnings hanterare stöds:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Omrendera](https://www.royalrender.de/)
* [Tids gräns för att tänka](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure rendera hubb

Azure rendering Hub fören klar skapandet och hanteringen av Azure rendering-servergrupper.  Rendera Hub har inbyggt stöd för PipelineFx Qube och deadline 10.  Mer information och detaljerade anvisningar finns i [GitHub-lagringsplatsen](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Använda Azure med PipelineFX Qube

Azure rendering Hub stöder populära åter givnings hanterare inklusive tids gräns.  För instruktioner om hur du distribuerar och använder rendering Hub ser [du GitHub-lagringsplatsen](https://github.com/Azure/azure-render-hub).

Skript och instruktioner för att aktivera Azure Batch virtuella datorer som ska användas som Qube-anställda är också tillgängliga i [GitHub-lagringsplatsen](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Använda Azure med hjälp av den här funktionen

Den utökade åter givningen har Azure och Azure Batch inbyggd integrering, som gör att du kan utöka en rendering-servergrupp med Azure-baserade virtuella datorer. En sammanfattning finns i [hjälpfilerna](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Ett exempel på en kund som kan återge kunder med hjälp av Azure-integrering finns i [kund berättelse för Jellyfish-bilder](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Använda Azure med tids gräns för att överväga

Azure rendering Hub stöder populära åter givnings hanterare inklusive tids gräns.  För instruktioner om hur du distribuerar och använder rendering Hub ser [du GitHub-lagringsplatsen](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Nästa steg

Prova Azure Batch-integrering för åter givnings hanteraren med hjälp av lämpligt plugin-program och instruktioner på GitHub, i förekommande fall.
