---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8d26b6c92462abc13c42257d2c6e571156eacc0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011786"
---
Azure-filresurser distribueras till *lagrings konton* som är toppnivå objekt som representerar en delad lagringspool. Den här lagringspoolen kan användas för att distribuera flera fil resurser, samt andra lagrings resurser som BLOB-behållare, köer och tabeller. Alla lagrings resurser som distribueras till ett lagrings konto delar de gränser som gäller för det lagrings kontot. Om du vill se de aktuella gränserna för ett lagrings konto kan du läsa [Azure Files skalbarhets-och prestanda mål](../articles/storage/files/storage-files-scale-targets.md).

Det finns två huvud typer av lagrings konton som du kommer att använda för Azure Files distributioner: 
- **GPv2-lagrings konton (General Purpose version 2)**: med GPv2 Storage-konton kan du Distribuera Azure-filresurser på standard-/hård diskbaserade (HDD-baserade) maskin vara. Förutom att lagra Azure-filresurser kan GPv2 lagrings konton lagra andra lagrings resurser, till exempel BLOB-behållare, köer och tabeller. 
- **FileStorage lagrings konton**: med FileStorage Storage-konton kan du Distribuera Azure-filresurser i Premium/Solid-State-baserad (SSD) maskin vara. FileStorage-konton kan bara användas för att lagra Azure-filresurser. inga andra lagrings resurser (BLOB-behållare, köer, tabeller osv.) kan distribueras i ett FileStorage-konto. Endast FileStorage-konton kan distribuera både SMB-och NFS-filresurser.

Det finns flera andra lagrings konto typer som du kan komma åt i Azure Portal, PowerShell eller CLI. Två lagrings konto typer, BlockBlobStorage-och BlobStorage-lagrings konton kan inte innehålla Azure-filresurser. De andra två typerna av lagrings konton som du kan se är General Purpose version 1 (GPv1) och klassiska lagrings konton, som båda kan innehålla Azure-filresurser. Även om GPv1 och klassiska lagrings konton kan innehålla Azure-filresurser, är de flesta nya funktioner i Azure Files endast tillgängliga i GPv2-och FileStorage-lagringsenheter. Vi rekommenderar därför att endast använda GPv2-och FileStorage-lagrings konton för nya distributioner och för att uppgradera GPv1 och klassiska lagrings konton om de redan finns i din miljö.  