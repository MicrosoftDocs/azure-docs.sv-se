---
title: Migrera data till Azure File Sync med Azure Data Box
description: 'Migrera data offline som är kompatibla med Azure File Sync. Undvik fil konflikter och bevara ACL: er för filer och mappar och tidsstämplar när du har aktiverat synkronisering.'
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 94abb33d39765a19306a013576d43fb2602d1c37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017635"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Migrera bulkdata till Azure File Sync med Azure DataBox
Du kan migrera Mass data till Azure File Sync på två sätt:

* **Ladda upp filer med hjälp av Azure File Sync.** Detta är den enklaste metoden. Flytta dina filer lokalt till Windows Server 2012 R2 eller senare och installera Azure File Sync-agenten. När du har konfigurerat synkroniseringen kommer dina filer att överföras från servern. (Våra kunder upplever för närvarande en genomsnittlig uppladdnings hastighet på 1 TiB varannan dag.) För att säkerställa att servern inte använder för mycket bandbredd för ditt data Center, kanske du vill konfigurera ett [schema för bandbredds begränsning](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Överför dina filer offline.** Om du inte har tillräckligt med bandbredd kanske du inte kan ladda upp filer till Azure på en rimlig tids period. Utmaningen är den första synkroniseringen av hela uppsättningen filer. För att undvika den här utmaningen använder du Migreringsverktyg för flera offline-verktyg som [Azure Data Box-serien](https://azure.microsoft.com/services/storage/databox). 

Den här artikeln förklarar hur du migrerar filer offline på ett sätt som är kompatibelt med Azure File Sync. Följ dessa anvisningar för att undvika fil konflikter och bevara åtkomst kontrol listorna för filer och mappar (ACL: er) och tidsstämplar när du har aktiverat synkroniseringen.

## <a name="migration-tools"></a>Migreringsverktyg
Processen som vi beskriver i den här artikeln fungerar inte bara för Data Box-enhet utan även för andra verktyg för offline-migrering. Det fungerar också med verktyg som AzCopy, Robocopy, eller partner verktyg och tjänster som fungerar direkt via Internet. Men för att lösa den inledande uppladdnings utmaningen följer du stegen i den här artikeln för att använda dessa verktyg på ett sätt som är kompatibelt med Azure File Sync.

I vissa fall måste du flytta från en Windows-Server till en annan Windows-Server innan du börjar Azure File Sync. [Tjänsten Storage Migration service](/windows-server/storage/storage-migration-service/overview) (SMS) kan hjälpa dig med det. Oavsett om du behöver migrera till en server-OS-version som stöds av Azure File Sync (Windows Server 2012R2 och uppåt) eller om du bara behöver migrera eftersom du köper ett nytt system för Azure File Sync, har SMS flera funktioner och fördelar som gör att migreringen blir smidigt.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Fördelar med att använda ett verktyg för att överföra data offline
Här är de främsta fördelarna med att använda ett överförings verktyg som Data Box-enhet för migrering offline:

- Du behöver inte ladda upp alla filer över nätverket. För stora namn rymder kan det här verktyget spara betydande nätverks bandbredd och-tid.
- När du använder Azure File Sync oavsett vilket överförings verktyg du använder (Data Box-enhet, Azure import/export-tjänsten och så vidare) laddar din Live-server bara de filer som ändras när du flyttar data till Azure.
- Azure File Sync synkroniserar dina filer och mappar med ACL: er även om verktyget för offline-Migreringsverktyg inte transport-ACL: er.
- Data Box-enhet och Azure File Sync kräver ingen stillestånds tid. När du använder Data Box-enhet för att överföra data till Azure kan du använda nätverks bandbredden effektivt och bevara filens åter givning. Du håller också ditt namn område uppdaterat genom att bara ladda upp de filer som ändras när du flyttar data till Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Krav för data överföring offline
Du bör inte aktivera synkronisering på den server som du migrerar innan du slutför data överföringen offline. Andra saker att tänka på innan du börjar är följande:

- Om du planerar att använda Data Box-enhet för din Mass migrering: granska [distributions kraven för data Box-enhet](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planera din slutgiltiga Azure File Sync topologi: [Planera för en Azure File Sync distribution](storage-sync-files-planning.md)
- Välj Azure Storage-konton som ska innehålla de fil resurser som du vill synkronisera med. Se till att din Mass migrering sker för tillfälliga mellanlagrings resurser i samma lagrings konto (n). Mass migrering kan bara aktive ras med en slutgiltig-och en mellanlagrings resurs som finns i samma lagrings konto.
- En Mass migrering kan endast användas när du skapar en ny synkroniseringsrelation med en server plats. Du kan inte aktivera en Mass migrering med en befintlig synkroniseringsrelation.


## <a name="process-for-offline-data-transfer"></a>Process för data överföring offline
Så här konfigurerar du Azure File Sync på ett sätt som är kompatibelt med verktyg för Mass migrering, till exempel Azure Data Box:

![Diagram som visar hur du konfigurerar Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Steg | Detalj |
|---|---------------------------------------------------------------------------------------|
| ![Steg 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Beställ dina data Box-enhet](../../databox/data-box-deploy-ordered.md). Data Box-enhets familjen erbjuder [flera produkter](https://azure.microsoft.com/services/storage/databox/data) som passar dina behov. När du får Data Box-enhet ska du följa [dokumentationen för att kopiera data](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) till den här UNC-sökvägen på Data Box-enhet: *\\<DeviceIPAddres \> \<StorageAccountName_AzFile\> \<ShareName\>*. Här är *resurs* namn namnet på den mellanlagrings resursen. Skicka Data Box-enhet tillbaka till Azure. |
| ![Steg 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Vänta tills filerna visas i de Azure-filresurser som du valde som tillfälliga mellanlagrings resurser. *Aktivera inte synkronisering till dessa resurser.* |
| ![Steg 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>Skapa en ny tom resurs för varje fil resurs som Data Box-enhet skapats åt dig. Den här nya resursen ska finnas i samma lagrings konto som Data Box-enhet resursen. [Så här skapar du en ny Azure-filresurs](storage-how-to-create-file-share.md).</li><li>[Skapa en Sync-grupp](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) i en tjänst för synkronisering av lagring. Referera till den tomma resursen som en moln slut punkt. Upprepa det här steget för alla Data Box-enhet fil resurser. [Konfigurera Azure File Sync](storage-sync-files-deployment-guide.md).</li></ul> |
| ![Steg 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Lägg till din Live Server-katalog som en server slut punkt](storage-sync-files-deployment-guide.md#create-a-server-endpoint). I processen anger du att du har flyttat filerna till Azure och hänvisar till mellanlagrings resurserna. Du kan aktivera eller inaktivera moln nivåer efter behov. När du skapar en server slut punkt på din Live-Server refererar du till mellanlagrings resursen. På bladet **Lägg till Server slut punkt** , under **offline-dataöverföring**, väljer du **aktive rad** och väljer sedan den mellanlagrings resurs som måste finnas i samma lagrings konto som moln slut punkten. Här filtreras listan över tillgängliga resurser efter lagrings konto och resurser som inte redan synkroniseras. Skärm bilden som följer efter den här tabellen visar hur du refererar till data delning när Server slut punkten skapas i Azure Portal. |
| ![Steg 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | När du har lagt till Server slut punkten i föregående steg börjar data flöda automatiskt från den högra källan. Avsnittet [Synkronisera resurs](#syncing-the-share) förklarar när data flödar antingen från data-eller Windows Server |
| |

![Skärm bild av Azure Portal användar gränssnitt, som visar hur du aktiverar data överföring offline när du skapar en ny server slut punkt](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synkronisera resursen
När du har skapat din server slut punkt startar synkroniseringen. Sync-processen avgör om varje fil på servern också finns i den fristående resurs där Data Box-enhet deponerade filerna. Om filen finns där kopierar Sync-processen filen från mellanlagrings resursen i stället för att ladda upp den från servern. Om filen inte finns i den fristående resursen, eller om det finns en nyare version på den lokala servern, laddar Sync-processen upp filen från den lokala servern.

När du synkroniserar resursen, slås synkroniseringen samman alla filattribut, behörigheter eller tidsstämplar som saknas i filens varianter på den lokala servern, vilket kombinerar dem med deras fil motsvarigheter från data delningen. Detta säkerställer att varje fil och mapp tas emot med alla möjliga fil åter givning i Azure-filresursen.

> [!IMPORTANT]
> Du kan bara aktivera läget för Mass migrering när du skapar en server slut punkt. När du har upprättat en server slut punkt kan du inte integrera Mass migrerade data från en redan synkroniserad server i namn området.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL: er och tidsstämplar på filer och mappar
Azure File Sync säkerställer att ACL: er för filer och mappar synkroniseras från Live-servern även om verktyget för Mass migrering som du använde inte ursprungligen transport-ACL: er. Därför behöver inte mellanlagrings resursen innehålla några ACL: er för filer och mappar. När du aktiverar funktionen offline-datamigrering när du skapar en ny server slut punkt synkroniseras alla ACL: er för filer på servern. Nyligen skapade och ändrade tidsstämplar synkroniseras också.

## <a name="shape-of-the-namespace"></a>Namn områdets form
När du aktiverar synkroniseringen bestämmer serverns innehåll formen för namn området. Om filerna tas bort från den lokala servern efter att Data Box-enhet ögonblicks bilden och migreringen är slutförd, flyttas dessa filer inte till Live och synkronisering av namn området. De finns kvar i mellanlagrings resursen, men de kopieras inte. Detta är nödvändigt eftersom synkroniseringen behåller namn området enligt Live-servern. Data Box-enhet *ögonblicks bilden* är bara ett mellanlagrings underlag för effektiv fil kopiering. Det är inte auktoritet för formen på det aktiva namn området.

## <a name="cleaning-up-after-bulk-migration"></a>Rensa efter Mass migrering 
När servern har slutfört den första synkroniseringen av namn rummet använder Data Box-enhet Mass-migrerade filer den mellanlagrings fil resursen. På bladet **Server slut punkt egenskaper** i Azure Portal i avsnittet **offline-dataöverföring** ändras **statusen från** pågår till **slutförd**. 

![Skärm bild av bladet Server slut punkts egenskaper där status och inaktivera kontroller för data överföring offline finns](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Nu kan du rensa mellanlagrings resursen för att spara kostnader:

1. På bladet **Egenskaper för Server slut punkt** när statusen är **slutförd** väljer du **inaktivera offline-dataöverföring**.
2. Överväg att ta bort mellanlagrings resursen för att spara kostnader. Mellanlagrings resursen innehåller antagligen inte ACL: er för filer och mappar, så det är osannolikt att vara användbart. För säkerhets kopiering av tidpunkter för säkerhets kopiering skapar du en riktig [ögonblicks bild av synkroniseringen av Azure-filresursen](storage-snapshots-files.md). Du kan [konfigurera Azure Backup att ta ögonblicks bilder]( ../../backup/backup-afs.md) enligt ett schema.

Inaktivera data överförings läget offline endast när statusen är **slutförd** eller när du vill avbryta på grund av en felaktig konfiguration. Om du inaktiverar läget under en distribution kommer filerna att börja överföras från servern även om din mellanlagrings resurs fortfarande är tillgänglig.

> [!IMPORTANT]
> När du har inaktiverat data överförings läget offline kan du inte aktivera det igen, även om den tillfälliga delningen från Mass migreringen fortfarande är tillgänglig.

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Azure File Sync och förinstallerade filer i molnet

Om du har dirigerade filer i en Azure-filresurs på annat sätt än data, t. ex. via AzCopy, RoboCopy från en säkerhets kopiering i molnet eller någon annan metod, bör du fortfarande följa [processen Offline dataöverföring](#process-for-offline-data-transfer) som beskrivs i den här artikeln. Du behöver bara ignorera datarutan som metoden dina filer flyttas till molnet. Det är dock ytterst viktigt att se till att du fortfarande följer processen med att dirigera filerna till en *mellanlagrings resurs* , inte den sista Azure File Sync anslutna resursen.

> [!WARNING]
> **Följ processen för att dirigera filer till en mellanlagringsplats och inte den sista** Azure File Sync anslutna resursen. Om du inte gör det kan fil konflikter uppstå (både fil versioner kommer att lagras) och filer som tas bort på Live-servern kan komma tillbaka, om de fortfarande finns i din äldre, dirigerade uppsättning filer. Dessutom sammanfogas mappar med varandra, vilket gör det mycket svårt att separera namn området efter sådana misstag.

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)