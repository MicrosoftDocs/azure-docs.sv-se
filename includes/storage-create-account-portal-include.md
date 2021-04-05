---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3da4fd26b3f985e034ca60039c09412e8237e965
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98109448"
---
Följ de här stegen för att skapa ett GPv2-konto för generell användning i Azure Portal:

1. Välj **Alla tjänster** på menyn i Azure-portalen. Skriv **lagringskonton** i listan över resurser. När du börjar skriva filtreras listan baserat på det du skriver. Välj **lagrings konton**.
1. På fönstret **lagringskonton** som visas, väljer du **lägg till**.
1. På fliken **grundläggande** väljer du den prenumeration där du vill skapa lagrings kontot.
1. Under fältet **resurs grupp** väljer du önskad resurs grupp eller skapar en ny resurs grupp.  Mer information om Azures resurs grupper finns i [Azure Resource Manager översikt](../articles/azure-resource-manager/management/overview.md).
1. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
1. Välj en plats för ditt lagringskonto eller använd standardplatsen.
1. Välj en prestanda nivå. Standard nivån är *standard*.
1. Ange fältet **Kontotyp** till *Storage v2 (General-Purpose v2)*.
1. Ange hur lagrings kontot ska replikeras. Standard alternativet för replikering är *Read-Access Geo-redundant lagring (RA-GRS)*. Mer information om tillgängliga replikeringsalternativ finns [Azure Storage redundans](../articles/storage/common/storage-redundancy.md).
1. Fler alternativ finns på flikarna **nätverk**, **data skydd**, **Avancerat** och **taggar** . Om du vill använda Azure Data Lake Storage väljer du fliken **Avancerat** och sedan ange **hierarkiskt namn område** till **aktive rad**. Mer information finns i [Azure Data Lake Storage Gen2 introduktion](../articles/storage/blobs/data-lake-storage-introduction.md)
1. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.
1. Välj **Skapa**.

Följande bild visar inställningarna på fliken **grundläggande** för ett nytt lagrings konto:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Skärmbild som visar hur du skapar ett lagringskonto i Azure-portalen":::
