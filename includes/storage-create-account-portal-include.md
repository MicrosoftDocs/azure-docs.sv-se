---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5798ad8721d8bf2924aa97876d0c8354681d3568
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718284"
---
Följ de här stegen för att skapa ett GPv2-konto för generell användning i Azure Portal:

1. Välj **Alla tjänster** på menyn i Azure-portalen. Skriv **lagringskonton** i listan över resurser. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Lagringskonton**.
1. I fönstret **Lagringskonton** som visas väljer du **+ Nytt.**
1. På **bladet Grundläggande** väljer du den prenumeration där du vill skapa lagringskontot.
1. Under fältet **Resursgrupp** väljer du önskad resursgrupp eller skapar en ny resursgrupp.  Mer information om Azure-resursgrupper finns i [Azure Resource Manager översikt.](../articles/azure-resource-manager/management/overview.md)
1. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
1. Välj en region för ditt lagringskonto eller använd standardregionen.
1. Välj en prestandanivå. Standardnivån är *Standard*.
1. Ange hur lagringskontot ska replikeras. Standardalternativet för redundans är *Geo-redundant lagring (GRS).* Mer information om tillgängliga replikeringsalternativ finns i [Azure Storage redundans](../articles/storage/common/storage-redundancy.md).
1. Ytterligare alternativ finns på **bladet** **Avancerat,** **Nätverk,** Dataskydd **och** Taggar. Om du Azure Data Lake Storage väljer du **bladet Avancerat** och ställer **sedan in Hierarkisk namnrymd på** **Aktiverad.** Mer information finns i [Azure Data Lake Storage Gen2 introduktion](../articles/storage/blobs/data-lake-storage-introduction.md)
1. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.
1. Välj **Skapa**.

Följande bild visar inställningarna på bladet **Grundläggande inställningar för** ett nytt lagringskonto:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Skärmbild som visar hur du skapar ett lagringskonto i Azure Portal." lightbox="media/storage-create-account-portal-include/account-create-portal.png":::
