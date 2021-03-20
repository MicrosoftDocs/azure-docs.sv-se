---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86230998"
---
All den senaste generationens VM-storlekar stöder kryptering på värden:

|Typ  |Stöds inte  |Stöds  |
|---------|---------|---------|
|Generellt syfte     | Dv3, Dav4, Dv2, AV2        | B, DSv2, Dsv3, DC, DCv2, Dasv4        |
|Beräkningsoptimerad     |         | Fsv2        |
|Minnesoptimerad     | Ev3, Eav4        | DSv2, Esv3, M, Mv2, Easv4        |
|Lagringsoptimerad     |         | LS, Lsv2 (NVMe-diskar inte krypterade)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (för hands version)        |
|Databehandling med höga prestanda     | H        | HB, HC, HBv2        |
|Tidigare generationer     | F, A, D, L, G        | DS, GS, FS, NVv2        |

Uppgradering av den virtuella datorns storlek leder till verifiering för att kontrol lera om den nya VM-storleken stöder funktionen EncryptionAtHost.
