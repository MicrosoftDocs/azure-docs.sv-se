---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010843"
---
Om du arbetar med distributions modellen för Resource Manager kan du ändra till de nya gateway-SKU: erna. När du ändrar från en äldre Gateway-SKU till en ny SKU tar du bort den befintliga VPN-gatewayen och skapar en ny VPN-gateway.

Arbets flöde

1. Ta bort flera anslutningar till en virtuell nätverksgateway.
2. Ta bort VPN gatewayen.
3. Skapa den nya VPN gatewayen.
4. Uppdatera din lokala VPN-enhet med den nya IP-adressen för VPN-gatewayen (för plats-till-plats-anslutningar).
5. Uppdatera gateway IP-adressvärdet för alla lokala nätverket VNet-till-VNet-gateways som ska ansluta till den här gatewayen.
6. Hämta den nya klientpaket för VPN-konfiguration för P2S-klienter som ansluter till det virtuella nätverket via den här VPN-gatewayen.
7. Skapa om flera anslutningar till en virtuell nätverksgateway.

Överväganden:

* Om du vill flytta till de nya SKU: erna måste din VPN-gateway vara i distributions modellen för Resource Manager.
* Om du har en klassisk VPN-gateway måste du fortsätta att använda äldre SKU: er för denna gateway, men du kan ändra storlek mellan de äldre SKU: erna. Du kan inte ändra till nya SKU: er.
* Du får anslutnings avbrott när du ändrar från en äldre SKU till en ny SKU.
* När du byter till en ny Gateway-SKU kommer den offentliga IP-adressen för din VPN-gateway att ändras. Detta inträffar även om du anger samma offentliga IP-adress objekt som du använde tidigare.