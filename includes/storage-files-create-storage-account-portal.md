---
title: storage-files-create-storage-account-portal
description: Skapa ett lagringskonto för Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 03cf20e5c796a7092dc16c466934f377c945ad48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96509126"
---
Ett lagringskonto är en delad lagringspool i vilken du kan distribuera en Azure-filresurs eller andra lagringsresurser, t.ex. blobar eller köer. Ett lagringskonto kan innehålla ett obegränsat antal resurser. En resurs kan lagra ett obegränsat antal filer, upp till kapacitetsbegränsningen för lagringskontot.

Skapa ett lagringskonto:

1. På den vänstra menyn väljer **+** du för att skapa en resurs.
1. Skriv **lagringskonto** i sökrutan, välj **Lagringskonto – blob, fil, tabell, kö** och klicka sedan på **Skapa**.
    ![En skärmbild av hur lagringskontoposten bör se ut i dialogrutan för resurssökning](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

1. Skriv *mystorageacct* i **Namn**, följt av några slumptal tills du ser en grön bockmarkering som indikerar att det är ett unikt namn. Ett lagringskontonamn får bara bestå av gemener och måste vara globalt unikt. Anteckna namnet på ditt lagringskonto. Du ska använda det senare. 
1. I **distributions modell** lämnar du standardvärdet **Resource Manager**. Mer information om skillnaderna mellan Azure Resource Manager och den klassiska distributionsmodellen finns i [Understand deployment models and the state of your resources](../articles/azure-resource-manager/management/deployment-models.md) (Distributionsmodeller och dina resursers tillstånd).
1. Behåll standardvärdet som **standard** i **prestanda**.
    
    > [!NOTE]
    > Den här snabb starten skapar en standard fil resurs, men om du vill använda Premium fil resurser väljer du **Premium** i stället.

1. Välj **StorageV2** i **Kontotyp**. Mer information om de olika typerna av lagringskonton finns i [Alternativ för Azure Storage-konton](../articles/storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

    > [!NOTE]
    > Den här snabb starten skapar ett allmänt-Purpose v2-konto. Om du vill använda Premium fil resurser väljer du **FileStorage** i stället.

1. Välj **lokalt redundant lagring (LRS)** i **Replikering**. 
1. Vi rekommenderar att du alltid väljer **Aktiverad** i **Säker överföring krävs**. Mer information om det här alternativet finns i [Understand encryption in-transit](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Kryptering under överföring).
1. Välj den prenumeration som användes för att skapa lagringskontot i **Prenumeration**. Om du bara har en prenumeration bör den vara standard.
1. I **Resursgrupp** väljer du **Skapa ny**. Ange *myResourceGroup* för namnet.
1. Välj **USA, östra** i **Plats**.
1. Lämna standardalternativet som **Inaktiverat** i **Virtuella nätverk**. 
1. Välj **Fäst på instrumentpanelen** så att det blir lättare att hitta lagringskontot.
1. När du är klar startar du distributionen genom att välja **Skapa**.