---
title: storage-files-create-storage-account-portal
description: Skapa ett lagringskonto för Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33159ab6dd014a153c8fd317fd291aeca033d6e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717877"
---
Ett lagringskonto är en delad lagringspool i vilken du kan distribuera en Azure-filresurs eller andra lagringsresurser, t.ex. blobar eller köer. Ett lagringskonto kan innehålla ett obegränsat antal resurser. En resurs kan lagra ett obegränsat antal filer, upp till kapacitetsbegränsningen för lagringskontot.

Skapa ett lagringskonto:

1. I den vänstra menyn väljer du **+** för att skapa en resurs.
1. Välj **Lagringskonto** för att skapa ett lagringskonto.

    :::image type="content" source="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png" alt-text="En skärmbild av lagringskontoalternativet på bladet Skapa en resurs." lightbox="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png":::

1. Skriv *mystorageacct* i **Namn**, följt av några slumptal tills du ser en grön bockmarkering som indikerar att det är ett unikt namn. Ett lagringskontonamn får bara bestå av gemener och måste vara globalt unikt. Anteckna namnet på ditt lagringskonto. Du ska använda det senare. 
1. I **Prestanda** behåller du standardvärdet **Standard**.
1. Välj **lokalt redundant lagring (LRS)** i **Replikering**.
1. Välj den prenumeration som användes för att skapa lagringskontot i **Prenumeration**. Om du bara har en prenumeration bör den vara standard.
1. I **Resursgrupp** väljer du **Skapa ny**. Ange *myResourceGroup* för namnet.
1. Välj **USA, östra** i **Plats**.
1. När du är klar startar du distributionen genom att välja **Skapa**.