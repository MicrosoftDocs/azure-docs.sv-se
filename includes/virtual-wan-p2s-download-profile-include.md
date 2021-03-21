---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99628961"
---
1. På sidan för ditt virtuella WAN väljer du **användare VPN-konfigurationer**.
1. På sidan **VPN-konfigurationer för användare** väljer du en konfiguration och väljer sedan **Hämta VPN-profil för virtuell WAN-användare**. När du hämtar WAN-nivå konfigurationen får du en inbyggd Traffic Manager-baserad VPN-profil för användare. Mer information om globala profiler eller en nav baserad profil finns i [hubb profiler](../articles/virtual-wan/global-hub-profile.md). Failover-scenarier är förenklade med global profil.

   
   Om en hubb av någon anledning inte är tillgänglig, garanterar den inbyggda trafik hanteringen som tjänsten tillhandahåller anslutning (via en annan hubb) till Azure-resurser för punkt-till-plats-användare. Du kan alltid hämta en hubb-speciell VPN-konfiguration genom att navigera till hubben. Under **användar-VPN (peka på plats)** hämtar du **VPN-** profilen för den virtuella hubben användare.
1. På sidan **Hämta virtuell WAN-profil för virtuellt WAN** väljer du **autentiseringstypen** och väljer sedan **skapa och ladda ned profil**. Profil paketet skapas och en zip-fil som innehåller konfigurations inställningarna hämtas.
