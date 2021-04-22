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
ms.openlocfilehash: 6e819e1078ac90ef16070702e7961122b06c1d6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107879852"
---
I förhandsversionen har NFS följande begränsningar:

- NFS 4.1 stöder för närvarande endast de flesta funktioner från [protokollspecifikationen](https://tools.ietf.org/html/rfc5661). Vissa funktioner, till exempel delegeringar och återanrop av alla typer, låsuppgraderingar och nedgradering, Kerberos-autentisering och kryptering stöds inte.
- Om de flesta av dina begäranden är metadatacentrerade blir svarstiden sämre jämfört med läs-/skriv-/uppdateringsåtgärder.
- NFS-resurser kan bara aktiveras/skapas på nya lagringskonto/s och inte på befintliga
- Endast hanteringsplanets REST API:er stöds. REST-API:er för dataplanet är inte tillgängliga, vilket innebär att verktyg som Storage Explorer inte fungerar med NFS-resurser och inte heller kan bläddra bland NFS-resursdata i Azure Portal.
- AzCopy stöds inte för närvarande.
- Endast tillgängligt för Premium-nivån.
- NFS-resurser accepterar endast numerisk UID/GID. För att undvika att klienter skickar alfanumeriska UID/GID bör du inaktivera ID-mappning.
- Resurser kan bara monteras från ett lagringskonto på en enskild virtuell dator när du använder privata länkar. Det går inte att montera resurser från andra lagringskonton.
- Det är bäst att förlita sig på de behörigheter som tilldelats till den primära gruppen. Ibland kan behörigheter som allokerats till användarens icke-primära grupp resultera i åtkomst nekad på grund av en känd bugg.

### <a name="azure-storage-features-not-yet-supported"></a>Azure Storage funktioner som inte stöds ännu

Dessutom är följande Azure Files tillgängliga med NFS-resurser:

- Identitetsbaserad autentisering
- Azure Backup support
- Ögonblicksbilder
- Mjuk borttagning
- Fullständigt stöd för kryptering under överföring (mer information finns i [NFS-säkerhet](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (endast tillgängligt för Windows-klienter som NFS 4.1 inte stöder)
