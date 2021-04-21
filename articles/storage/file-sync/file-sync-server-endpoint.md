---
title: Lägga till/ta bort Azure File Sync serverslutpunkt för | Microsoft Docs
description: Lär dig att lägga till eller ta bort en serverslutpunkt med Azure File Sync. En serverslutpunkt är en viss plats på en registrerad server, till exempel en mapp på en servervolym.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bcb346782b8d158dab5371a583c5a2576f0a09c9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797070"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Lägga till/ta bort Azure File Sync en serverslutpunkt
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Det gör du genom att omvandla Dina Windows-servrar till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

En *serverslutpunkt* representerar en specifik plats på *en registrerad server,* till exempel en mapp på en servervolym eller volymens rot. Flera serverslutpunkter kan finnas på samma volym om deras namnområden inte överlappar (till exempel F:\sync1 och F:\sync2) och varje slutpunkt synkroniserar till en unik synkroniseringsgrupp. Du kan konfigurera principer för molnnivåindelad lagring individuellt för varje serverslutpunkt. Om du lägger till en serverplats med en befintlig uppsättning filer som en serverslutpunkt i en synkroniseringsgrupp, sammanfogas filerna med andra filer som redan finns på andra slutpunkter i synkroniseringsgruppen.

Se [Så här distribuerar Azure File Sync](file-sync-deployment-guide.md) information om hur du distribuerar Azure File Sync från end-to-end.

## <a name="prerequisites"></a>Förutsättningar
Om du vill skapa en serverslutpunkt måste du först se till att följande villkor är uppfyllda: 
- Servern har den Azure File Sync agenten installerad och har registrerats. Anvisningar för hur du installerar Azure File Sync Agent finns i artikeln [Registrera/avregistrera en server Azure File Sync](file-sync-server-registration.md) server. 
- Kontrollera att en tjänst för synkronisering av lagring har distribuerats. Se [Så här distribuerar Azure File Sync](file-sync-deployment-guide.md) information om hur du distribuerar en tjänst för synkronisering av lagring. 
- Kontrollera att en synkroniseringsgrupp har distribuerats. Lär dig hur [du skapar en synkroniseringsgrupp](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Kontrollera att servern är ansluten till Internet och att Azure är tillgängligt. Vi använder port 443 för all kommunikation mellan servern och vår tjänst.

## <a name="add-a-server-endpoint"></a>Lägga till en serverslutpunkt
Om du vill lägga till en serverslutpunkt går du till önskad synkroniseringsgrupp och väljer "Lägg till serverslutpunkt".

![Lägga till en serverslutpunkt i fönstret för synkroniseringsgruppen](media/storage-sync-files-server-endpoint/add-server-endpoint.png)

Följande information krävs under Lägg till **serverslutpunkt:**

- **Registrerad server:** Namnet på den server eller det kluster där serverslutpunkten ska skapas.
- **Sökväg:** Sökvägen på Windows Server som ska synkroniseras som en del av synkroniseringsgruppen.
- **Molnnivåindelad:** En växel för att aktivera eller inaktivera molnnivåindelad lagring. När den här inställningen är aktiverad *nivåindelar molnnivåindelningen* filer till dina Azure-filresurser. Detta konverterar lokala filresurser till ett cacheminne i stället för en fullständig kopia av datauppsättningen för att hjälpa dig att hantera utrymmeseffektiviteten på servern.
- **Ledigt volymutrymme:** mängden ledigt utrymme som ska reserveras på den volym som serverslutpunkten finns på. Om det lediga volymens lediga utrymme till exempel är inställt på 50 % på en volym med en enda serverslutpunkt kommer ungefär hälften av mängden data att nivåindelad till Azure Files. Oavsett om molnnivåindelning är aktiverat har Azure-filresursen alltid en fullständig kopia av data i synkroniseringsgruppen.

Välj **Skapa för** att lägga till serverslutpunkten. Filerna i ett namnområde för en synkroniseringsgrupp kommer nu att synkroniseras. 

## <a name="remove-a-server-endpoint"></a>Ta bort en serverslutpunkt
Om du vill sluta använda Azure File Sync för en viss serverslutpunkt kan du ta bort serverslutpunkten. 

> [!Warning]  
> Försök inte felsöka problem med synkronisering, molnnivåindelad lagring eller någon annan aspekt av Azure File Sync genom att ta bort och återskapa serverslutpunkten om du inte uttryckligen uppmanas av en Microsoft-tekniker. Att ta bort en serverslutpunkt är en destruktiv åtgärd, och nivåindelade filer i serverslutpunkten kommer inte att "återanslutas" till deras platser på Azure-filresursen när serverslutpunkten har återskapats, vilket resulterar i synkroniseringsfel. Observera också att nivåindelade filer som finns utanför serverslutpunktens namnområde kan förloras permanent. Nivåindelade filer kan finnas i serverslutpunkten även om molnnivåindelade aldrig har aktiverats.

För att säkerställa att alla nivåindelade filer återkallas innan du tar bort serverslutpunkten inaktiverar du molnnivåindelade på serverslutpunkten och kör sedan följande PowerShell-cmdlet för att återkalla alla nivåindelade filer i serverslutpunktens namnområde:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
Om `-Order CloudTieringPolicy` du anger kommer de senast ändrade filerna att återkallas först.
Andra valfria men användbara parametrar att tänka på är:
* `-ThreadCount` avgör hur många filer som kan återkallas parallellt.
* `-PerFileRetryCount`anger hur ofta ett återkallelseförsök görs för en fil som för närvarande är blockerad.
* `-PerFileRetryDelaySeconds`bestämmer tiden i sekunder mellan återförsök att återkalla försök och bör alltid användas i kombination med föregående parameter.

> [!Note]  
> Om den lokala volymen som är värd för servern inte har tillräckligt med ledigt utrymme för att återkalla alla nivåindelade data misslyckas `Invoke-StorageSyncFileRecall` cmdleten.  

Så här tar du bort serverslutpunkten:

1. Gå till tjänsten för synkronisering av lagring där servern är registrerad.
1. Gå till önskad synkroniseringsgrupp.
1. Ta bort den serverslutpunkt som du vill ha i synkroniseringsgruppen i tjänsten för synkronisering av lagring. Detta kan åstadkommas genom att högerklicka på relevant serverslutpunkt i fönstret synkroniseringsgrupp.

    ![Ta bort en serverslutpunkt från en synkroniseringsgrupp](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Nästa steg
- [Registrera/avregistrera en server med Azure File Sync](file-sync-server-registration.md)
- [Planera för distribution av Azure File Sync](file-sync-planning.md)
- [Övervaka Azure File Sync](file-sync-monitoring.md)
