---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 958c40ed311ebe8b2a83ce0d4d394d00f48d1afa
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732275"
---
Gå till det lagringskonto som du vill skapa en privat slutpunkt för. I innehållsförteckningen för lagringskontot väljer du **Nätverk,** **Privata slutpunktsanslutningar** och **sedan + Privat slutpunkt för** att skapa en ny privat slutpunkt. 

[![En skärmbild av objektet för privata slutpunktsanslutningar i innehållsförteckningen för lagringskontot](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

Den resulterande guiden har flera sidor att slutföra.

På **bladet Grundläggande** väljer du önskad resursgrupp, namn och region för din privata slutpunkt. De kan vara vad du vill, de behöver inte matcha lagringskontot på något sätt, även om du måste skapa den privata slutpunkten i samma region som det virtuella nätverk som du vill skapa den privata slutpunkten i.

![En skärmbild av avsnittet Grundläggande i avsnittet Skapa privat slutpunkt](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

På **bladet** Resurs väljer du alternativknappen för **Anslut till en Azure-resurs i min katalog**. Under **Resurstyp** väljer **du Microsoft.Storage/storageAccounts** som resurstyp. Fältet **Resurs** är lagringskontot med den Azure-filresurs som du vill ansluta till. Målunderresursen är **filen**, eftersom detta är för Azure Files.

På **bladet** Konfiguration kan du välja det specifika virtuella nätverk och undernät som du vill lägga till din privata slutpunkt i. Du måste välja ett distinkt undernät från det undernät som du lade till tjänstslutpunkten till ovan. Bladet Konfiguration innehåller också information om hur du skapar/uppdaterar den privata DNS-zonen. Vi rekommenderar att du använder `privatelink.file.core.windows.net` standardzonen.

![En skärmbild av avsnittet Konfiguration](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

Klicka **på Granska + skapa** för att skapa den privata slutpunkten. 
