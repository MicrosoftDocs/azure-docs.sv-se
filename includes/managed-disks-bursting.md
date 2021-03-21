---
title: ta med fil
description: ta med fil
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 0f85f2770b98507618d5cbfdec1b55f397c7f514
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750769"
---
Azure ger möjlighet att öka prestanda för disk lagring IOPS och MB/s. Detta kallas för bursting för både virtuella datorer och diskar. Du kan effektivt utnyttja VM och disk-burst för att få bättre prestanda för att överföra både virtuella datorer och diskar.

Burst-överföring för virtuella Azure-datorer och disk resurser är inte beroende av varandra. Du behöver inte ha en burst-kompatibel virtuell dator för en ansluten burst-kompatibel disk till burst. På samma sätt behöver du inte ha en burst-kompatibel disk ansluten till den virtuella datorn med burst-kapacitet för att den virtuella datorn ska kunna överföras till burst.