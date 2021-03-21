---
title: ta med fil
description: ta med fil
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178392"
---
- Burst-överföring på begäran kan inte aktive ras på en Premium SSD som har mindre än 512 GiB. Premium-SSD som är mindre än 512 GiB använder alltid en kredit baserad burst-överföring.
- Burst-överföring på begäran stöds bara på Premium-SSD. Om en Premium SSD med aktive rad burst på begäran växlar till en annan disktyp, är disk burst inaktive rad.
- Burst-överföring på begäran inaktive ras automatiskt när prestanda nivån ändras. Om du vill ändra prestanda nivån men inte vill ha disk-burst måste du inaktivera den.
- Burst-överföring på begäran kan bara aktive ras när disken är frånkopplad från en virtuell dator eller när den virtuella datorn har stoppats. Burst-överföring på begäran kan inaktive ras 12 timmar efter det att det har Aktiver ATS.