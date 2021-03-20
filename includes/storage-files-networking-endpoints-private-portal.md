---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 5/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09723030f0da0252120f66f36347cad2e3a3546a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93376200"
---
Navigera till lagrings kontot som du vill skapa en privat slut punkt för. I innehålls förteckningen för lagrings kontot väljer du **anslutningar för privata slut punkter** och sedan **+ privat slut punkt** för att skapa en ny privat slut punkt. 

[![En skärm bild av objektet privata slut punkts anslutningar i innehålls förteckningen för lagrings kontot](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

Den resulterande guiden har flera sidor att slutföra.

På bladet **grundläggande** väljer du önskad resurs grupp, namn och region för din privata slut punkt. Det kan vara vad du vill, de behöver inte matcha lagrings kontot på något sätt, även om du måste skapa den privata slut punkten i samma region som det virtuella nätverk som du vill skapa den privata slut punkten i.

![En skärm bild av grunderna i avsnittet Skapa privat slut punkt](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

På **resurs** bladet väljer du alternativ knappen för **att ansluta till en Azure-resurs i min katalog**. Under **resurs typ** väljer du **Microsoft. Storage/storageAccounts** som resurs typ. **Resurs** fältet är lagrings kontot med den Azure-filresurs som du vill ansluta till. Mål under resurs är **fil**, eftersom det är för Azure Files.

På bladet **konfiguration** kan du välja det angivna virtuella nätverk och undernät som du vill lägga till din privata slut punkt till. Du måste välja ett distinkt undernät från under nätet som du har lagt till tjänst slut punkten till ovan. Konfigurations bladet innehåller också information om hur du skapar/uppdaterar den privata DNS-zonen. Vi rekommenderar att du använder standard `privatelink.file.core.windows.net` zonen.

![En skärm bild av konfigurations avsnittet](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

Klicka på **Granska + skapa** för att skapa den privata slut punkten. 
