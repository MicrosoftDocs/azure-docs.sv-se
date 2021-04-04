---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023539"
---
Innan du startar konfigurationen kontrollerar du att du uppfyller följande kriterier:

* Om du redan har ett virtuellt nätverk som du vill ansluta till kontrollerar du att inget av under näten i det lokala nätverket överlappar det. Ditt virtuella nätverk kräver inte ett Gateway-undernät och kan inte ha några virtuella nätverksgateway. Om du inte har ett virtuellt nätverk kan du skapa ett med hjälp av stegen i den här artikeln.
* Hämta ett IP-adressintervall för din hubbregion. Hubben är ett virtuellt nätverk och det adress intervall som du anger för Hub-regionen får inte överlappa med ett befintligt virtuellt nätverk som du ansluter till. Det får inte heller överlappa de adress intervall som du ansluter till lokalt. Om du inte känner till IP-adressintervall som finns i din lokala nätverks konfiguration, koordinerar du med någon som kan ge den informationen åt dig.
* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.