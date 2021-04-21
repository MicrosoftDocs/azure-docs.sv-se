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
ms.openlocfilehash: 6c54bdc2738f9455926882c439ff8942c1c0a5a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759776"
---
Azure-filresurser distribueras till *lagringskonton*, som är objekt på den översta nivån som representerar en delad lagringspool. Den här lagringspoolen kan användas för att distribuera flera filresurser, samt andra lagringsresurser som blobcontainrar, köer eller tabeller. Alla lagringsresurser som distribueras till ett lagringskonto delar de gränser som gäller för det lagringskontot. Information om aktuella begränsningar för lagringskontot [finns Azure Files för skalbarhet och prestanda.](../articles/storage/files/storage-files-scale-targets.md)

Det finns två huvudsakliga typer av lagringskonton som du använder för Azure Files distributioner: 
- GPv2-lagringskonton för generell användning **version 2 (GPv2):** Med GPv2-lagringskonton kan du distribuera Azure-filresurser på standard-/hårddiskbaserad (HDD-baserad) maskinvara. Förutom att lagra Azure-filresurser kan GPv2-lagringskonton lagra andra lagringsresurser, till exempel blobcontainrar, köer eller tabeller. 
- **FileStorage-lagringskonton:** Med FileStorage-lagringskonton kan du distribuera Azure-filresurser på premium-/solid state-diskbaserad (SSD-baserad) maskinvara. FileStorage-konton kan bara användas för att lagra Azure-filresurser. inga andra lagringsresurser (blobcontainrar, köer, tabeller osv.) kan distribueras i ett FileStorage-konto. Endast FileStorage-konton kan distribuera både SMB- och NFS-filresurser.

Det finns flera andra typer av lagringskonto som du kan stöta på Azure Portal, PowerShell eller CLI. Två lagringskontotyper, BlockBlobStorage- och BlobStorage-konton, får inte innehålla Azure-filresurser. De andra två lagringskontotyperna som du kan se är generell användning version 1 (GPv1) och klassiska lagringskonton, som båda kan innehålla Azure-filresurser. Även om GPv1- och klassiska lagringskonton kan innehålla Azure-filresurser, är de flesta nya funktioner i Azure Files endast tillgängliga i GPv2- och FileStorage-lagringskonton. Därför rekommenderar vi att du endast använder GPv2- och FileStorage-lagringskonton för nya distributioner och att du uppgraderar GPv1- och klassiska lagringskonton om de redan finns i din miljö.  