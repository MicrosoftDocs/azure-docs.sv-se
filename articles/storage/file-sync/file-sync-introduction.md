---
title: Introduktion till Azure File Sync | Microsoft Docs
description: En översikt över Azure File Sync, en tjänst som gör att du kan skapa och använda nätverksfilresurser i molnet med hjälp av SMB-protokollet som är branschstandard.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da40783e3d299b0edf27c6d045dbc6dcfc5cf964
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797669"
---
# <a name="what-is-azure-file-sync"></a>Vad är Azure File Sync?
Azure File Sync kan centralisera organisationens filresurser i Azure Files samtidigt som flexibiliteten, prestandan och kompatibiliteten för en Windows-filserver finns kvar. Vissa användare kan välja att behålla en fullständig kopia av sina data lokalt, men Azure File Sync har dessutom möjlighet att omvandla Windows Server till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.   

## <a name="videos"></a>Video
| Introduktion Azure File Sync | Azure Files med Sync (Ignite 2019)  |
|-|-|
| [![Skärmbild av videon Introduktion Azure File Sync – klicka för att spela upp!](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Skärmbild av skärmen Azure Files Sync-presentation – klicka för att spela upp!](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>Fördelar med Azure File Sync

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
När molnnivåindelad lagring är aktiverad cachelagras dina mest använda filer på din lokala server och dina filer som används minst är nivåindelade i molnet. Du kan styra hur mycket lokalt diskutrymme som används för cachelagring. Nivåindelade filer kan snabbt återkallas på begäran, vilket gör upplevelsen sömlös samtidigt som du kan minska kostnaderna eftersom du bara behöver lagra en bråkdel av dina data lokalt. Mer information om molnnivåer finns i Översikt [över molnnivåindelad lagring.](file-sync-cloud-tiering-overview.md) 

### <a name="multi-site-access-and-sync"></a>Åtkomst och synkronisering för flera webbplatser
Azure File Sync är perfekt för scenarier med distribuerad åtkomst. För vart och ett av dina kontor kan du etablera en lokal Windows Server som en del av din Azure File Sync distribution. Ändringar som görs på en server på ett kontor synkroniseras automatiskt till servrarna på alla andra kontor.

### <a name="business-continuity-and-disaster-recovery"></a>Affärskontinuitet och haveriberedskap
Azure File Sync backas upp av Azure Files, som erbjuder flera redundansalternativ för lagring med hög tillgång. Eftersom Azure innehåller motståndskraftiga kopior av dina data blir din lokala server en apparat för engångscachelagring och återställning från en misslyckad server kan göras genom att lägga till en ny server i Azure File Sync distributionen. I stället för att återställa från en lokal säkerhetskopia etablerar du en annan Windows Server, installerar Azure File Sync-agenten på den och lägger sedan till den i Azure File Sync distributionen. Azure File Sync laddar ned ditt filnamnsutrymme innan du laddar ned data, så att servern kan vara igång så snart som möjligt. För ännu snabbare återställning kan du ha ett varmt ställ per server som en del av distributionen, eller så kan du använda Azure File Sync med Windows-klustring.

## <a name="cloud-side-backup"></a>Säkerhetskopiering på molnsidan
Minska dina lokala säkerhetskopieringsutgifter genom att ta centraliserade säkerhetskopior i molnet med hjälp av Azure Backup. Azure Files SMB-resurser har inbyggda funktioner för ögonblicksbilder och processen kan automatiseras med hjälp av Azure Backup för att schemalägga säkerhetskopieringar och hantera deras kvarhållning. Azure Backup integreras med dina lokala servrar, så när du återställer till molnet laddas dessa ändringar ned automatiskt på Dina Windows-servrar.  

## <a name="next-steps"></a>Nästa steg
* [Planera för distribution av Azure File Sync](file-sync-planning.md)
* [Översikt över molnnivåindelad](file-sync-cloud-tiering-overview.md)
* [Övervaka Azure File Sync](file-sync-monitoring.md)