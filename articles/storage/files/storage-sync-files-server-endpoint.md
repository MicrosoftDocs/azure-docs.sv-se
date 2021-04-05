---
title: Lägg till/ta bort en Azure File Sync Server slut punkt | Microsoft Docs
description: Lär dig att lägga till eller ta bort en server slut punkt med Azure File Sync. En server slut punkt är en viss plats på en registrerad Server, till exempel en mapp på en server volym.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f75f0d1ae12db11590f8ce62f3c7b4c0f3e12817
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013946"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Lägga till/ta bort en Azure File Sync Server slut punkt
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Det gör du genom att omvandla dina Windows-servrar till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

En *Server slut punkt* representerar en angiven plats på en *registrerad Server*, till exempel en mapp på en server volym eller volymens rot. Det kan finnas flera Server slut punkter på samma volym om deras namn områden inte överlappar varandra (till exempel F:\sync1 och F:\sync2) och varje slut punkt synkroniseras med en unik Sync-grupp. Du kan konfigurera principer för moln nivåer individuellt för varje server slut punkt. Om du lägger till en server plats med en befintlig uppsättning filer som en server slut punkt till en Sync-grupp, kommer dessa filer att slås samman med andra filer som redan finns på andra slut punkter i Sync-gruppen.

Information om hur du distribuerar Azure File Sync slut punkt till slut punkt finns i [distribuera Azure File Sync](storage-sync-files-deployment-guide.md) .

## <a name="prerequisites"></a>Förutsättningar
Om du vill skapa en server slut punkt måste du först se till att följande villkor är uppfyllda: 
- Servern har installerat Azure File Sync-agenten och har registrerats. Instruktioner för att installera Azure File Sync agenten finns i artikeln [Registrera/avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md) . 
- Se till att en tjänst för synkronisering av lagring har distribuerats. Information om hur du distribuerar en synkroniseringstjänst för lagring finns i [så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md) . 
- Se till att en Sync-grupp har distribuerats. Lär dig hur du [skapar en Sync-grupp](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Kontrol lera att servern är ansluten till Internet och att Azure är tillgängligt. Vi använder port 443 för all kommunikation mellan servern och vår tjänst.

## <a name="add-a-server-endpoint"></a>Lägga till en serverslutpunkt
Om du vill lägga till en server slut punkt navigerar du till önskad Sync-grupp och väljer Lägg till Server slut punkt.

![Lägga till en serverslutpunkt i fönstret för synkroniseringsgruppen](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

Följande information krävs under **Lägg till Server slut punkt**:

- **Registrerad Server**: namnet på servern eller klustret som server slut punkten ska skapas på.
- **Sökväg**: sökvägen på Windows Server som ska synkroniseras som en del av Sync-gruppen.
- **Moln nivå**: en växel för att aktivera eller inaktivera moln nivåer. När den är aktive *rad kommer moln nivån att filer* till dina Azure-filresurser. Detta konverterar lokala fil resurser till en cache, i stället för en fullständig kopia av data uppsättningen, som hjälper dig att hantera utrymmes effektiviteten på servern.
- **Ledigt utrymme på volym**: mängden ledigt utrymme som ska reserveras på volymen som server slut punkten finns på. Om till exempel volym ledigt utrymme är inställt på 50% på en volym med en enda server slut punkt kommer mängden data att nivåas till Azure Files. Oavsett om moln nivån är aktive rad, har Azure-filresursen alltid en fullständig kopia av data i Sync-gruppen.

Välj **skapa** för att lägga till Server slut punkten. Filerna i ett namn område i en Sync-grupp kommer nu att synkroniseras. 

## <a name="remove-a-server-endpoint"></a>Ta bort en server slut punkt
Om du vill avbryta användningen av Azure File Sync för en specifik server slut punkt kan du ta bort Server slut punkten. 

> [!Warning]  
> Försök inte att felsöka problem med synkronisering, moln nivåer eller någon annan aspekt av Azure File Sync genom att ta bort och återskapa Server slut punkten om du inte uttryckligen instrueras till av en Microsoft-tekniker. Att ta bort en server slut punkt är en destruktiv åtgärd och nivåbaserade filer i Server slut punkten kommer inte att återanslutas till sina platser på Azure-filresursen efter att Server slut punkten har återskapats, vilket leder till synkroniseringsfel. Observera också att de nivåbaserade filer som finns utanför serverns slut punkts namn område kan gå förlorade permanent. Skiktade filer kan finnas i Server slut punkten även om moln nivåer aldrig har Aktiver ATS.

För att säkerställa att alla nivåbaserade filer återkallas innan du tar bort Server slut punkten inaktiverar du moln skiktning på Server slut punkten och kör sedan följande PowerShell-cmdlet för att återkalla alla skiktade filer i Server slut punktens namnrymd:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
`-Order CloudTieringPolicy`Om du anger så kommer de senast ändrade filerna att återställas först.
Andra valfria men användbara parametrar att överväga är:
* `-ThreadCount` anger hur många filer som kan återkallas parallellt.
* `-PerFileRetryCount`anger hur ofta ett återställnings försök ska göras för en fil som för närvarande är blockerad.
* `-PerFileRetryDelaySeconds`fastställer tiden i sekunder mellan försök att återkalla försök och bör alltid användas i kombination med föregående parameter.

> [!Note]  
> Om den lokala volym som är värd för servern inte har tillräckligt med ledigt utrymme för att återkalla alla data på nivån, `Invoke-StorageSyncFileRecall` Miss lyckas cmdleten.  

Så här tar du bort Server slut punkten:

1. Navigera till tjänsten Storage Sync där servern är registrerad.
2. Navigera till önskad Sync-grupp.
3. Ta bort den server slut punkt som du vill ha i Sync-gruppen i tjänsten för synkronisering av lagring. Detta kan åstadkommas genom att högerklicka på relevant Server slut punkt i fönstret synkronisera grupp.

    ![Ta bort en server slut punkt från en synkroniseringsresurs](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Nästa steg
- [Registrera/avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md)
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
