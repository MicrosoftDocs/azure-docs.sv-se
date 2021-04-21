---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2f50a1d046b7b7d3fd7507ef87f8f29096a4faa4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774576"
---
Nej: att ta bort en serverslutpunkt är inte som att starta om en server! Att ta bort och återskapa serverslutpunkten är nästan aldrig en lämplig lösning för att åtgärda problem med synkronisering, molnnivåindelad lagring eller andra aspekter av Azure File Sync. Att ta bort en serverslutpunkt är en destruktiv åtgärd. Det kan leda till dataförlust om nivåindelade filer finns utanför serverslutpunktens namnområde. Mer [information finns i Varför finns nivåindelade filer](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) utanför serverslutpunktens namnområde? Eller så kan det resultera i otillgängliga filer för nivåindelade filer som finns i serverslutpunktens namnområde. De här problemen löses inte när serverslutpunkten återskapas. Nivåindelade filer kan finnas i serverslutpunktens namnområde även om du aldrig har aktiverat molnnivåindelade. Därför rekommenderar vi att du inte tar bort serverslutpunkten om du inte vill sluta använda Azure File Sync med den här mappen eller uttryckligen har instruerats att göra det av en Microsoft-tekniker. Mer information om hur du tar bort serverslutpunkter finns i [Ta bort en serverslutpunkt.](../articles/storage/file-sync/file-sync-server-endpoint.md#remove-a-server-endpoint)    
