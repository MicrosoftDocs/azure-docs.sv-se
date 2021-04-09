---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4b708e80bf335ba8bdce074285857a6f8b77b972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103439197"
---
I för hands versionen har NFS följande begränsningar:

- NFS 4,1 stöder för närvarande bara de flesta funktioner från [protokoll specifikationen](https://tools.ietf.org/html/rfc5661). Vissa funktioner som delegering och motringning av alla typer, Lås uppgraderingar och nedgradering, Kerberos-autentisering och kryptering stöds inte.
- Om de flesta av dina begär Anden är metadata-koncentriska kommer svars tiden att bli sämre vid jämförelse med Läs-/skriv-/uppdaterings åtgärder.
- Du måste skapa ett nytt lagrings konto för att kunna skapa en NFS-resurs.
- Endast hanterings planens REST-API: er stöds. REST-API: er för data plan är inte tillgängliga, vilket innebär att verktyg som Storage Explorer inte fungerar med NFS-resurser eller att du inte kan bläddra i NFS dela data i Azure Portal.
- AzCopy stöds inte för närvarande.
- Endast tillgängligt för Premium-nivån.
- NFS-resurser accepterar bara numeriskt UID/GID. För att undvika att klienter skickar alfanumeriska UID/GID bör du inaktivera ID-mappning.
- Resurser kan bara monteras från ett lagrings konto på en enskild virtuell dator, när privata länkar används. Försök att montera resurser från andra lagrings konton kommer att Miss lyckas.
- Det är bäst att förlita dig på de behörigheter som tilldelats den primära gruppen. Ibland kan behörigheter som allokerats till den icke-primära gruppen av användaren leda till åtkomst nekas på grund av ett känt fel.

### <a name="azure-storage-features-not-yet-supported"></a>Azure Storage funktioner som ännu inte stöds

Dessutom är följande Azure Files funktioner inte tillgängliga med NFS-resurser:

- Identitets baserad autentisering
- Azure Backup support
- Ögonblicksbilder
- Mjuk borttagning
- Fullständig stöd för kryptering under överföring (mer information finns i [NFS-säkerhet](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (endast tillgängligt för Windows-klienter som NFS 4,1 inte stöder)
