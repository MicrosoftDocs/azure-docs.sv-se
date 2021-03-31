---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80420961"
---
I den här artikeln beskrivs hur du överför en virtuell hård disk från din lokala dator till en Azure-hanterad disk eller kopierar en hanterad disk till en annan region med hjälp av AzCopy. Den här processen, direkt överföring, gör det möjligt att ladda upp en virtuell hård disk på upp till 32 TiB i storlek direkt till en hanterad disk. För närvarande stöds direkt uppladdning för standard hård diskar, standard SSD och Premium SSD-hanterade diskar. Den stöds inte för Ultra disks än.

Om du tillhandahåller en säkerhets kopierings lösning för virtuella IaaS-datorer i Azure rekommenderar vi att du använder direkt överföring för att återställa kundens säkerhets kopior till hanterade diskar. När du laddar upp en virtuell hård disk från en källa som är extern till Azure, är hastigheten beroende av din lokala bandbredd. När du laddar upp eller kopierar från en virtuell Azure-dator skulle bandbredden vara densamma som standard hård diskar.