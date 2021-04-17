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
ms.openlocfilehash: e8ec7faf1562381288aeef630bf2076ce413017a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531530"
---
Den senaste generationens VM-storlekar stöder kryptering på värden:

|Typ  |Stöds inte  |Stöds  |
|---------|---------|---------|
|Generellt syfte     | Dv3, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dav4, Dasv4        |
|Beräkningsoptimerad     |         | Fsv2        |
|Minnesoptimerad     | Ev3        | DSv2, Esv3, M, Mv2, Eav4, Easv4        |
|Lagringsoptimerad     |         | Ls, Lsv2 (NVMe-diskar som inte är krypterade)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (förhandsversion)        |
|Databehandling med höga prestanda     | H        | HB, HC, HBv2        |
|Tidigare generationer     | F, A, D, L, G        | DS, GS, Fs, NVv2        |
