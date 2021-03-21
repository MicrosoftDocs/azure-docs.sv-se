---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750429"
---
- Den här funktionen stöds för närvarande endast för Premium-SSD.
- Du måste antingen frigöra den virtuella datorn eller koppla från disken från en virtuell dator som körs innan du kan ändra diskens nivå.
- Prestanda nivåerna P60, P70 och P80 kan bara användas av diskar som är större än 4 096 GiB.
- En disks prestanda nivå kan bara nedgraderas en gång var 12: e timme.

## <a name="change-performance-tier-without-downtime-preview"></a>Ändra prestanda nivå utan stillestånds tid (för hands version)

Normalt skulle du behöva frigöra den virtuella datorn eller koppla från disken för att ändra prestanda nivån. Men om du aktiverar den här för hands versions funktionen behöver du inte frigöra den virtuella datorn eller koppla från disken för att ändra nivån. Du kan registrera dig för för hands versionen [här](https://aka.ms/liveperftiersignup).

För hands versionen har följande begränsningar:
- Endast tillgängligt i EastUS2EUAP-regionen.
- För närvarande inte tillgängligt för delade diskar
- Måste använda Azure Resource Manager mallar med `2020-12-01` API: et för att ändra prestanda nivåer utan nedtid.