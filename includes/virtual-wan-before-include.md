---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4940bce453729d13ba76071fa59bcf95c7672a24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359511"
---
* Du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Du har ett virtuellt nätverk som du vill ansluta till. Kontrol lera att inget av under näten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln om [snabb start](../articles/virtual-network/quick-create-portal.md) .

* Det virtuella nätverket får inte ha några befintliga virtuella nätverks-gatewayer. Om det virtuella nätverket redan har gatewayer (VPN eller ExpressRoute) måste du ta bort alla gatewayer innan du fortsätter. Den här konfigurationen kräver att virtuella nätverk endast ansluter till den virtuella WAN Hub-gatewayen.

* En virtuell hubb är ett virtuellt nätverk som skapas och används av virtuellt WAN. Det är kärnan i ditt virtuella WAN-nätverk i en region. Hämta ett IP-adressintervall för din virtuella hubb region. Det adress intervall som du anger för hubben får inte överlappa något av de befintliga virtuella nätverk som du ansluter till. Den kan inte heller överlappa de lokala adress intervall som du ansluter till. Om du inte känner till IP-adressintervall som finns i din lokala nätverks konfiguration, koordinerar du med någon som kan ge den informationen åt dig.
