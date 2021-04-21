---
title: Migrera data till Azure File Sync med Azure Data Box
description: Migrera massdata offline som är kompatibla med Azure File Sync. Undvik filkonflikter och bevara fil- och mapp-ACL:er och tidsstämplar när du har aktivera synkronisering.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 453a6f9c83b992df62681f366fcf95a77cda9f25
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797100"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Migrera bulkdata till Azure File Sync med Azure DataBox
Du kan migrera bulkdata till Azure File Sync två sätt:

* **Ladda upp filer med hjälp av Azure File Sync.** Det här är den enklaste metoden. Flytta filerna lokalt till Windows Server 2012 R2 eller senare och installera Azure File Sync agenten. När du har ställt in synkroniseringen laddas filerna upp från servern. (Våra kunder upplever för närvarande en genomsnittlig uppladdningshastighet på 1 TiB ungefär varannan dag.) För att säkerställa att servern inte använder för mycket bandbredd för ditt datacenter kanske du vill konfigurera ett schema [för bandbreddsbegränsning.](file-sync-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)
* **Överför dina filer offline.** Om du inte har tillräckligt med bandbredd kanske du inte kan ladda upp filer till Azure inom rimlig tid. Utmaningen är den inledande synkroniseringen av hela uppsättningen filer. Du kan lösa den här utmaningen genom att använda offline-massmigreringsverktyg [som Azure Data Box familjen](https://azure.microsoft.com/services/storage/databox). 

Den här artikeln förklarar hur du migrerar filer offline på ett sätt som är kompatibelt med Azure File Sync. Följ dessa anvisningar för att undvika filkonflikter och för att bevara åtkomstkontrollistor för filer och mappar (ACL:er) och tidsstämplar när du har aktivera synkroniseringen.

## <a name="migration-tools"></a>Migreringsverktyg
Processen som beskrivs i den här artikeln fungerar inte bara för Data Box-enhet utan även för andra offlinemigreringsverktyg. Det fungerar också för verktyg som AzCopy, Robocopy eller partnerverktyg och -tjänster som fungerar direkt via Internet. Men för att lösa den första uppladdningsutmaningen följer du stegen i den här artikeln för att använda dessa verktyg på ett sätt som är kompatibelt med Azure File Sync.

I vissa fall måste du flytta från en Windows Server till en annan Windows Server innan du börjar Azure File Sync. [Storage Migration Service](/windows-server/storage/storage-migration-service/overview) (SMS) kan hjälpa dig med det. Oavsett om du behöver migrera till en serveroperativsystemversion som stöds av Azure File Sync (Windows Server 2012R2 och senare) eller om du bara behöver migrera eftersom du köper ett nytt system för Azure File Sync har SMS flera funktioner och fördelar som hjälper dig att få migreringen att fungera smidigt.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Fördelar med att använda ett verktyg för att överföra data offline
Här är de främsta fördelarna med att använda ett överföringsverktyg som Data Box-enhet för offlinemigrering:

- Du behöver inte ladda upp alla dina filer via nätverket. För stora namnrymder kan det här verktyget spara betydande nätverksbandbredd och -tid.
- När du använder Azure File Sync, oavsett vilket överföringsverktyg du använder (Data Box-enhet, Azure Import/Export-tjänsten och så vidare), laddar din liveserver bara upp de filer som ändras när du har flyttat data till Azure.
- Azure File Sync synkroniserar fil- och mapp-ACL:er även om offline-massmigreringsverktyget inte transporterar ACL:er.
- Data Box-enhet och Azure File Sync krävs ingen stilleståndstid. När du använder Data Box-enhet för att överföra data till Azure använder du nätverksbandbredden effektivt och bevarar filåtergivningen. Du håller också ditt namnområde uppdaterat genom att bara ladda upp de filer som ändras när du har flyttat data till Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Förutsättningar för överföring av offlinedata
Du bör inte aktivera synkronisering på den server som du migrerar innan du slutför dataöverföring offline. Andra saker att tänka på innan du börjar är följande:

- Om du planerar att använda Data Box-enhet massmigrering: Granska [distributionens krav för att Data Box-enhet](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planera din Azure File Sync topologi: [Planera för en Azure File Sync distribution](file-sync-planning.md)
- Välj Azure Storage-konton som ska innehålla de filresurser som du vill synkronisera med. Se till att massmigrering sker till tillfälliga mellanlagringsresurser i samma lagringskonto. Massmigrering kan bara aktiveras med hjälp av en slutlig- och mellanlagringsresurs som finns i samma lagringskonto.
- Massmigrering kan bara användas när du skapar en ny synkroniseringsrelation med en serverplats. Du kan inte aktivera en massmigrering med en befintlig synkroniseringsrelation.


## <a name="process-for-offline-data-transfer"></a>Process för överföring av offlinedata
Så här ställer du in Azure File Sync på ett sätt som är kompatibelt med massmigreringsverktyg som Azure Data Box:

![Diagram som visar hur du Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Steg | Detalj |
|---|---------------------------------------------------------------------------------------|
| ![Steg 1](media/storage-sync-files-offline-data-transfer/bullet-1.png) | [Beställ Data Box-enhet](../../databox/data-box-deploy-ordered.md). Den Data Box-enhet familjen erbjuder [flera produkter](https://azure.microsoft.com/services/storage/databox/data) som uppfyller dina behov. När du får ditt Data Box-enhet följer du dokumentationen för att kopiera [dina data](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) till den här UNC-sökvägen på Data Box-enhet: *\\<DeviceIPAddres \> \<StorageAccountName_AzFile\> \<ShareName\>*. Här är *ShareName* namnet på mellanlagringsresursen. Skicka Data Box-enhet tillbaka till Azure. |
| ![Steg 2](media/storage-sync-files-offline-data-transfer/bullet-2.png) | Vänta tills filerna visas i de Azure-filresurser som du valde som tillfälliga mellanlagringsresurser. *Aktivera inte synkronisering till dessa resurser.* |
| ![Steg 3](media/storage-sync-files-offline-data-transfer/bullet-3.png) | <ul><li>Skapa en ny tom resurs för varje filresurs som Data Box-enhet har skapats åt dig. Den nya resursen ska finnas i samma lagringskonto som den nya Data Box-enhet resursen. [Så här skapar du en ny Azure-filresurs.](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)</li><li>[Skapa en synkroniseringsgrupp i](file-sync-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) en tjänst för synkronisering av lagring. Referera till den tomma resursen som en molnslutpunkt. Upprepa det här steget för varje Data Box-enhet-filresurs. [Konfigurera Azure File Sync](file-sync-deployment-guide.md).</li></ul> |
| ![Steg 4](media/storage-sync-files-offline-data-transfer/bullet-4.png) | [Lägg till din live-serverkatalog som en serverslutpunkt.](file-sync-deployment-guide.md#create-a-server-endpoint) I processen anger du att du har flyttat filerna till Azure och refererar till mellanlagringsresurser. Du kan aktivera eller inaktivera molnnivåindelad lagring efter behov. När du skapar en serverslutpunkt på liveservern refererar du till mellanlagringsresursen. På **bladet Lägg till serverslutpunkt** under **Offline dataöverföring** väljer du Aktiverad och sedan den mellanlagringsresurs som måste finnas i samma lagringskonto som molnslutpunkten. Här filtreras listan över tillgängliga resurser efter lagringskonto och resurser som inte redan synkroniseras. Skärmbilden efter den här tabellen visar hur du refererar till DataBox-resursen när serverslutpunkten skapas i Azure Portal. |
| ![Steg 5](media/storage-sync-files-offline-data-transfer/bullet-5.png) | När du har lagt till serverslutpunkten i föregående steg börjar data flöda automatiskt från rätt källa. Avsnittet [Synkronisera resursen förklarar när](#syncing-the-share) data flödar antingen från DataBox-resursen eller från Windows Server |
| |

![Skärmbild av Azure Portal användargränssnittet som visar hur du aktiverar dataöverföring offline när du skapar en ny serverslutpunkt](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synkronisera resursen
När du har skapat serverslutpunkten startar synkroniseringen. Synkroniseringsprocessen avgör om varje fil på servern också finns i mellanlagringsresursen där Data Box-enhet har satt in filerna. Om filen finns där kopierar synkroniseringsprocessen filen från mellanlagringsresursen i stället för att ladda upp den från servern. Om filen inte finns i mellanlagringsresursen, eller om en nyare version är tillgänglig på den lokala servern, laddar synkroniseringsprocessen upp filen från den lokala servern.

När du synkroniserar resursen sammanfogar synkroniseringen eventuella saknade filattribut, behörigheter eller tidsstämplar från filvarianterna på den lokala servern och kombinerar dem med deras filmotsvarningar från DataBox-resursen. Detta säkerställer att varje fil och mapp tas emot med all möjlig filåtergivning i Azure-filresursen.

> [!IMPORTANT]
> Du kan bara aktivera massmigreringsläget när du skapar en serverslutpunkt. När du har upprättat en serverslutpunkt kan du inte integrera mass migrerade data från en redan synkroniserad server i namnområdet.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL:er och tidsstämplar för filer och mappar
Azure File Sync ser till att fil- och mapp-ACL:er synkroniseras från live-servern även om massmigreringsverktyget som du använde ursprungligen inte transporterade ACL:er. Därför behöver mellanlagringsresursen inte innehålla några ACL:er för filer och mappar. När du aktiverar funktionen för datamigrering offline när du skapar en ny serverslutpunkt synkroniseras alla fil-ACL:er på servern. Tidsstämplar som skapats och ändrats synkroniseras också.

## <a name="shape-of-the-namespace"></a>Namnområdets form
När du aktiverar synkroniseringen avgör serverns innehåll namnområdets form. Om filerna tas bort från den lokala servern när Data Box-enhet ögonblicksbilden och migreringen är klar flyttas inte dessa filer till det live- och synkroniserar namnområdet. De stannar kvar i mellanlagringsresursen, men de kopieras inte. Detta är nödvändigt eftersom synkroniseringen behåller namnområdet enligt live-servern. Ögonblicksbilden Data Box-enhet *bara* en mellanlagringsplats för effektiv filkopiering. Det är inte auktoriteten för formen på det levande namnområdet.

## <a name="cleaning-up-after-bulk-migration"></a>Rensa upp efter massmigrering 
När servern har slutfört den första synkroniseringen av namnområdet använder Data Box-enhet massindelade filer den mellanlagrade filresursen. På **bladet Egenskaper för** serverslutpunkt i Azure Portal i avsnittet **Offline-dataöverföring** ändras statusen från **Pågår** till **Slutförd.** 

![Skärmbild av bladet Egenskaper för serverslutpunkt, där status och inaktivering av kontroller för överföring av offlinedata finns](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Nu kan du rensa mellanlagringsresursen för att spara kostnader:

1. På **bladet Egenskaper för serverslutpunkt** väljer du **Inaktivera** offline dataöverföring när **statusen är Slutförd.**
2. Överväg att ta bort mellanlagringsresursen för att spara kostnader. Mellanlagringsresursen innehåller förmodligen inte fil- och mapp-ACL:er, så det är osannolikt att den är användbar. Skapa en verklig ögonblicksbild av den synkroniserade Azure-filresursen för [säkerhetskopiering till tidpunkt.](../files/storage-snapshots-files.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) Du kan [konfigurera Azure Backup att ta ögonblicksbilder]( ../../backup/backup-afs.md) enligt ett schema.

Inaktivera endast läget för offline-dataöverföring när tillståndet är **Slutfört** eller om du vill avbryta på grund av en felaktig konfiguration. Om du inaktiverar läget under en distribution börjar filerna överföras från servern även om mellanlagringsresursen fortfarande är tillgänglig.

> [!IMPORTANT]
> När du har inaktiverat offlineläget för dataöverföring kan du inte aktivera det igen, även om mellanlagringsresursen från massmigrering fortfarande är tillgänglig.

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Azure File Sync och förinstallerade filer i molnet

Om du har seedade filer i en Azure-filresurs på andra sätt än DataBox , t.ex. via AzCopy, RoboCopy från en molnsäkerhetskopia eller någon annan metod, bör du fortfarande följa processen för överföring av [offlinedata](#process-for-offline-data-transfer) som beskrivs i den här artikeln. Du behöver bara ignorera DataBox när metoden dina filer flyttas till molnet. Det är dock mycket viktigt att du fortfarande följer processen  med att seeda filerna till en mellanlagringsresurs och inte den slutliga, Azure File Sync anslutna resursen.

> [!WARNING]
> **Följ processen med att seeda filer till en mellanlagringsresurs och inte den slutliga**, eller Azure File Sync anslutna resursen. Om du inte gör det kan filkonflikter uppstå (båda filversionerna lagras) samt filer som tas bort på live-servern kan komma tillbaka om de fortfarande finns i din äldre, seedade uppsättning filer. Dessutom sammanfogas mappändringar med varandra, vilket gör det mycket svårt att separera namnområdet efter ett sådant misstag.

## <a name="next-steps"></a>Nästa steg
- [Planera för en Azure File Sync distribution](file-sync-planning.md)
- [Distribuera Azure File Sync](file-sync-deployment-guide.md)