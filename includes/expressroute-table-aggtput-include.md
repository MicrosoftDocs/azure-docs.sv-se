---
title: ta med fil
description: ta med fil
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 12/05/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: b1465b9bf855d592d195277b616c860a23c179f6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504722"
---
| Gateway-SKU | Anslutningar per sekund | Antal flöden | Megabyte-bitar per sekund | Paket per sekund | Kretsbandbredd | Antal vägar som annonseras av gatewayen (till MSEE: N) | Antal vägar som lärts av gatewayen (från MSEE: N) | Antal virtuella datorer i det virtuella nätverket |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Basic SKU (inaktuell)** | Saknas | Saknas | 500 | Saknas | Saknas | Saknas | Saknas | Saknas |
| **Standard-SKU/ErGw1AZ** | 7 000 | 400,000 | >1 000 | >100 000 | 1 Gbit/s |  500 | 4 000 | 2 000 (minska till 1 000 under underhåll, återställs efteråt.) | 
| **SKU/ErGw2AZ med höga prestanda** | 14 000 | 840 000 | >2 000 | 250 000 | 1 Gbit/s | 500 | ~ 9 500 (minska till 4 000 om fler än 6 500 virtuella datorer finns i det virtuella nätverket.) | 4 500 |
| **SKU/ErGw3AZ med Ultra Performance** | 16 000 | 950 000 | ~ 10 000 | 1,000,000 | 1 Gbit/s | 500 | ~ 9 500 | 11 000 |
