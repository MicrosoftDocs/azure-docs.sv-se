---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c5e3a095b312d36cb05a78e84a13bc2a73d95d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91875592"
---
DNS-inställningarna är inte en obligatorisk del av den här konfigurationen, men DNS är nödvändigt om du vill ha namn matchning mellan de virtuella datorerna. Ingen ny DNS-server skapas när du anger ett värde. Den angivna IP-adressen för DNS-servern måste vara en DNS-server som kan matcha namnen för de resurser som du ansluter till.

När du har skapat ditt virtuella nätverk kan du lägga till IP-adressen för en DNS-server för att hantera namnmatchning. Öppna inställningarna för det virtuella nätverket, Välj DNS-servrar och Lägg till IP-adressen för den DNS-server som du vill använda för namn matchning.

1. Leta upp det virtuella nätverket i portalen.
2. Välj **DNS-servrar** under avsnittet **Inställningar** på sidan för ditt virtuella nätverk.
3. Lägg till en DNS-server.
4. Välj **Spara** längst upp på sidan för att spara inställningarna.