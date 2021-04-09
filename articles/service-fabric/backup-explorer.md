---
title: Läsa och uppdatera en säkerhets kopia av pålitliga samlingar lokalt
description: Använd Backup Explorer i Azure Service Fabric för att läsa och uppdatera en lokal säkerhets kopia av pålitliga samlingar.
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: a0131960d356e4862d1379c308e240e19e76205c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048076"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>Läsa och uppdatera en säkerhets kopia av pålitliga samlingar med hjälp av backup Explorer

Azure Service Fabric backup Explorer hjälper till med data korrigering om data är skadade i Service Fabric pålitliga samlingar. Det aktuella data läget kan skadas av eventuella fel som introduceras i ett program eller av felaktiga poster i ett Live-kluster.

Med hjälp av backup Explorer kan du utföra följande uppgifter:
-   Fråga metadata för samlingen.
-   Visa det aktuella läget och dess poster i samlingen för den säkerhets kopia som lästs in.
-   Lista de transaktioner som har utförts sedan den senaste kontroll punkten.
-   Uppdatera samlingen genom att lägga till, uppdatera eller ta bort poster i samlingen.
-   Ta en ny säkerhets kopia genom att använda det uppdaterade läget.

> [!NOTE]
> Service Fabric backup Explorer stöder för närvarande endast visning och redigering av pålitliga samlingar i säkerhets kopian.
>

## <a name="access-the-backup"></a>Åtkomst till säkerhets kopian

Service Fabric backup Explorer kan användas på något av följande sätt för att visa eller uppdatera pålitliga samlingar i säkerhets kopian:
-   **Binär**: Använd ett NuGet-paket för att visa och ändra pålitliga samlingar.
-   **Http/rest**: Använd en HTTP-baserad rest-Server för att visa och ändra pålitliga samlingar.
-   **bkpctl**: Använd kommando rads gränssnittet i Service Fabric backup Explorer för att visa och ändra en tillförlitlig säkerhets kopiering.

Backup Explorer har ett C#-bibliotek för avancerade användare. Du kan använda biblioteket i programmet direkt för att arbeta med pålitliga samlingar på liknande sätt som kunderna arbetar med tillstånds hanteraren i befintliga tillstånds känsliga tjänster. För grundläggande användare och i ett grundläggande användnings fall har Explorer också en fristående REST-server som exponerar API: er för att kontrol lera säkerhets kopiorna. Bkpctl CLI-verktyget fungerar ovanpå REST-API: erna och baseras på python. Du kan använda CLI-verktyget för att läsa och uppdatera säkerhets kopior och för att göra nya säkerhets kopior via kommando raden.

Mer information finns i [Service Fabric backup Explorer](https://github.com/microsoft/service-fabric-backup-explorer) GitHub-lagringsplatsen. Databasen innehåller information om källa och version samt installations anvisningar.

Du kan också bygga lagrings platsen lokalt och arbeta med säkerhets kopior.
 
NuGet för backup Explorer (Microsoft. ServiceFabric. ReliableCollectionBackup. parser) kommer att vara tillgänglig på [NuGet.org](https://www.nuget.org/). 

## <a name="next-steps"></a>Nästa steg

* Läs mer om [pålitliga samlingar i Azure Service Fabric tillstånds känsliga tjänster](service-fabric-reliable-services-reliable-collections.md).
* Granska [Service Fabric bästa praxis](./service-fabric-best-practices-security.md).