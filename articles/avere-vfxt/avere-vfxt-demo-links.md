---
title: Aver vFXT för Azure demo-projekt
description: 'De här exemplen visar viktiga funktioner och användnings fall för AVERT vFXT för Azure: video åter givning, data behandling med höga prestanda, vFXT prestanda och klient installation.'
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 31e32bfc0a2c3279375148bdf3da7d4a4829af1c
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271081"
---
# <a name="avere-vfxt-demo-projects"></a>Aver vFXT demo projekt

Exempel på självstudier finns på [GitHub](https://github.com/Azure/Avere). Dessa små projekt visar viktiga funktioner och användnings fall för AVERT vFXT för Azure.

## <a name="video-rendering"></a>Video åter givning

* [Åter givning med Azure Batch och AVERT vFXT](https://github.com/Azure/Avere/blob/master/docs/maya_azure_batch_avere_vfxt_demo.md) – ett 60-minuters projekt som visar hur du använder Autodesk Maya med Azure Batch och ett vFXT-kluster för att skapa en animerad film

* [Varför ska jag använda aver-vFXT för rendering?](https://github.com/Azure/Avere/blob/master/docs/why_avere_for_rendering.md) – En demonstration som jämför åter givnings tider från nätverksansluten lagring med och utan ett AVERT vFXT-kluster

## <a name="high-performance-computing"></a>Databehandling med höga prestanda

* [Metod tips för att förbättra start tiden för virtuella datorer i Azure (VM)](https://github.com/Azure/Avere/blob/master/docs/azure_vm_provision_best_practices.md) – ett reproduktivt test som använder AVERT VFXT för Azure för att förbättra start tiderna när man snabbt snurrar upp tusentals beräknings klienter

## <a name="vfxt-performance"></a>vFXT prestanda

* [Mät vFXT prestanda med vdbench](https://github.com/Azure/Avere/blob/master/docs/vdbench.md) – en grundläggande test konfiguration för att generera små och medel stora arbets belastningar för att testa vFXT-minnet och disk under system

## <a name="client-setup"></a>Klient konfiguration

* [Windows 10 Workstation för aver vFXT](https://github.com/Azure/Avere/blob/master/docs/windows_10_avere_vfxt_mounted_workstation.md) – visar hur du konfigurerar en Windows-arbetsstation och monterar den till ett AVERT vFXT-kluster
