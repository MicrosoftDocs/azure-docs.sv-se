---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3c76988557bfa338bcfb606f568036422a536c1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98798590"
---
Navigera till det lagrings konto för vilket du vill begränsa den offentliga slut punkten till vissa virtuella nätverk. Välj **nätverk** i innehålls förteckningen för lagrings kontot. 

Längst upp på sidan väljer du alternativ knappen **valda nätverk** . Detta gör att det inte går att dölja ett antal inställningar för att kontrol lera begränsningen för den offentliga slut punkten. Klicka på **+ Lägg till befintligt virtuellt nätverk** för att välja det enskilda virtuella nätverk som ska få åtkomst till lagrings kontot via den offentliga slut punkten. Detta kräver att du väljer ett virtuellt nätverk och ett undernät för det virtuella nätverket. 

Markera **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här tjänst kontot** för att tillåta betrodda Microsoft-tjänster från första part, till exempel Azure File Sync att komma åt lagrings kontot.

[![Skärm bild av bladet nätverk med ett enskilt virtuellt nätverk som har tillåtelse att komma åt lagrings kontot via den offentliga slut punkten](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)