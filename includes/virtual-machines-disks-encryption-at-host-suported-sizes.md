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
ms.openlocfilehash: e9d7b39ce267202503b90e84e934d31501d45478
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732358"
---
Den senaste generationens VM-storlekar stöder kryptering på värden:

|Typ  |Stöds inte  |Stöds  |
|---------|---------|---------|
|Generellt syfte     | Dv3, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dav4, Dasv4, Ddv4, Ddsv4       |
|Beräkningsoptimerad     |         | Fsv2        |
|Minnesoptimerad     | Ev3        | Esv3, M, Mv2, Eav4, Easv4, Edv4, Edsv4        |
|Lagringsoptimerad     |         | Ls, Lsv2 (NVMe-diskar som inte är krypterade)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (förhandsversion)        |
|Databehandling med höga prestanda     | H        | HB, HC, HBv2        |
|Tidigare generationer     | F, A, D, L, G        | DS, GS, Fs, NVv2        |
