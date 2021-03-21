---
title: Vanliga frågor och svar om Azure import/export-tjänsten | Microsoft Docs
description: Läs svar på vanliga frågor om Azure import export-tjänsten.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: be6c48efc77880addf814b1609d4a371c7c5c73b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706746"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Azure import/export-tjänsten: vanliga frågor och svar

Följande är frågor och svar som du kan ha när du använder Azure import/export-tjänsten för att överföra data till Azure Storage. Frågor och svar är ordnade i följande kategorier:

- Om import/export-tjänsten
- Förbereda diskarna för import/export
- Import/export-jobb
- Leverans diskar
- Övriga farliga ämnen

## <a name="about-importexport-service"></a>Om import/export-tjänsten

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Kan jag kopiera Azure File Storage med Azure import/export-tjänsten?

Ja. Tjänsten Azure import/export stöder import till Azure File Storage. Den stöder inte export av Azure Files för tillfället.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Är Azure import/export-tjänsten tillgänglig för CSP-prenumerationer?

Ja. Azure import/export-tjänsten stöder prenumerationer av moln lösnings leverantörer (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-microsoft-365"></a>Kan jag använda Azure import/export-tjänsten för att kopiera PST-postlådor och SharePoint-data till Microsoft 365?

Ja. Mer information finns i [Översikt över att importera din organisations PST-filer](/microsoft-365/compliance/importing-pst-files-to-office-365).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Kan jag använda Azure import/export-tjänsten för att kopiera mina säkerhets kopior offline till den Azure Backup tjänsten?

Ja. Mer information finns [i arbets flödet offline-säkerhetskopiering i Azure Backup](../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Kan jag köpa enheter för import/export-jobb från Microsoft?

Nej. Du måste skicka dina egna enheter till import-och export jobb.

## <a name="preparing-disks-for-importexport"></a>Förbereda diskar för import/export

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Kan jag hoppa över steget förberedelse av enhet för ett import jobb? Kan jag förbereda en enhet utan att kopiera?

Nej. Alla enheter som används för att importera data måste förberedas med Azure WAImportExport-verktyget. Använd verktyget för att även kopiera data till enheten.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Måste jag utföra någon disk förberedelse när jag skapar ett export jobb?

Nej. Vissa för kontroller rekommenderas. Om du vill kontrol lera antalet diskar som krävs använder du WAImportExport-verktygets PreviewExport-kommando. Mer information finns i [för hands Visa enhets användning för ett export jobb](/previous-versions/azure/storage/common/storage-import-export-tool-previewing-drive-usage-export-v1). Med kommandot kan du förhandsgranska enhets användningen för de valda Blobbarna, baserat på storleken på de enheter som du kommer att använda. Kontrol lera också att du kan läsa från och skriva till den hård disk som levereras för export jobbet.

## <a name="importexport-jobs"></a>Import/export-jobb

### <a name="can-i-cancel-my-job"></a>Kan jag avbryta mitt jobb?

Ja. Du kan avbryta ett jobb när dess status **skapas** eller **levereras**. Utanför de här stegen går det inte att avbryta jobbet och fortsätter tills det sista steget.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Hur länge kan jag se statusen för slutförda jobb i Azure Portal?

Du kan visa statusen för slutförda jobb i upp till 90 dagar. Slutförda jobb tas bort efter 90 dagar.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Vad ska jag göra om jag vill importera eller exportera fler än 10 enheter?

Ett import-eller export jobb kan endast referera till 10 enheter i ett enda jobb. Om du vill skicka fler än 10 enheter bör du skapa flera jobb. Enheter som är associerade med samma jobb måste levereras tillsammans i samma paket.
Kontakta Microsoft Support om du vill ha mer information och vägledning när data kapaciteten sträcker sig över flera disk import jobb.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>Den uppladdade blobben visar status som "lån förfallet". Vad ska jag göra?

Du kan ignorera fältet "lån upphör att gälla". Import/export tar lån i blobben under överföring för att se till att ingen annan process kan uppdatera blobben parallellt. Lånet upphör att gälla innebär att import/export inte längre överförs till den och att blobben är tillgängligt för din användning.

## <a name="shipping-disks"></a>Leverans diskar

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Vilket är det maximala antalet hård diskar för i en försändelse?

Det finns ingen gräns för antalet hård diskar i en försändelse. Om diskarna tillhör flera jobb rekommenderar vi att du:

- förse diskarna med motsvarande jobb namn.
- uppdatera jobben med ett spårnings nummer suffix med-1,-2 osv.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Bör jag ta med allt annat än hård disken i mitt paket?

Leverera endast dina hård diskar i leverans paketet. Ta inte med objekt som strömförsörjnings kablar eller USB-kablar.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Måste jag leverera mina enheter med FedEx eller DHL?

Du kan leverera enheter till Azure-datacenter med hjälp av valfri känd bärvåg som FedEx, DHL, UPS eller US Posting service. För att returnera leverans av enheter till dig från data centret måste du dock tillhandahålla:

- Ett FedEx konto nummer i USA och EU, eller
- Ett DHL konto nummer i regionerna Asien och Australien.

> [!NOTE]
> Data centret i Indien kräver en deklarations bokstav på ditt brevhuvuden (leverans Challan) för att returnera enheterna. För att kunna ordna det obligatoriska post passet, måste du också boka med din valda operatör och dela informationen med data centret.

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>Finns det några begränsningar med att leverera och returnera enheten internationellt?

Observera att det fysiska medium som du levererar kan behövas mellan internationella gränser. Du ansvarar för att säkerställa att dina fysiska medier och data importeras och/eller exporteras i enlighet med tillämpliga lagar. Innan du levererar det fysiska mediet kan du kontakta dina rådgivare för att kontrol lera att dina medier och data kan skickas till det identifierade data centret. På så sätt kan du se till att det når Microsoft i rätt tid.

När uppladdningen är klar kan processen för att returnera enhet (er) till en internationell adress ta längre tid än de typiska 2-3 dagarna som krävs för lokal leverans. Under den fas som anges i Azure Portal som paketering, säkerställer Data Box-enhets teamet att rätt dokumentation tillhandahålls för att säkerställa att leveransen uppfyller de olika internationella import-och export kraven.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Finns det särskilda krav för att leverera mina diskar till ett Data Center?

Kraven beror på de begränsningar som gäller för Azure Data Center.

- Det finns några webbplatser, till exempel Australien, Tyskland och Storbritannien, södra, som kräver att ett Microsoft Data Center-inkommande ID-nummer skrivs på skiftena av säkerhets skäl. Innan du levererar dina enheter eller diskar till data centret kan du kontakta Azure Data Center-åtgärder ( adbops@microsoft.com ) för att få det här numret. Utan det här antalet kommer paketet att avvisas.
- Data centret i Indien kräver personliga uppgifter för driv rutinen, till exempel kortet för myndighets-ID eller bevis nummer. (t. ex. panorering, AADHAR, DL), namn, kontakt och bil plåt numret för att få en grind post pass. Informera operatören om dessa krav för att undvika leverans fördröjningar.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>När du skapar ett jobb är leverans adressen en plats som skiljer sig från min lagrings konto plats. Vad ska jag göra?

Vissa lagrings konto platser mappas till alternativa leverans platser. Tidigare tillgängliga leverans platser kan också temporärt mappas till alternativa platser. Kontrol lera alltid leverans adressen som angavs när jobbet skapades innan du levererar dina enheter.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>När du levererar min enhet ber operatören efter data Center att kontakta adress och telefonnummer. Vad ska jag ge?

Telefonnumret och DC-adressen anges som en del av jobb skapandet.

## <a name="miscellaneous"></a>Övriga farliga ämnen

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Vad händer om jag skickar en hård disk av misstag som inte uppfyller de krav som stöds?

Azure Data Center returnerar den enhet som inte uppfyller de krav som stöds för dig. Om bara några av enheterna i paketet uppfyller support kraven kommer enheterna att bearbetas och de enheter som inte uppfyller kraven returneras till dig.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Formaterar tjänsten enheterna innan de returneras?

Nej. Alla enheter är krypterade med BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Hur kan jag komma åt data som importeras av den här tjänsten?

Använd Azure Portal eller [Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att få åtkomst till data under ditt Azure Storage-konto.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>När importen är klar ser mina data ut som i lagrings kontot? Är min katalog-hierarki bevarad?

När du förbereder en hård disk för ett import jobb anges målet i fältet DstBlobPathOrPrefix i data uppsättningen CSV. Det här är mål behållaren i lagrings kontot som data från hård disken kopieras till. I den här mål behållaren skapas virtuella kataloger för mappar från hård disken och blobbar skapas för filer.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Om en enhet har filer som redan finns i mitt lagrings konto skriver tjänsten över befintliga blobbar eller filer?

Väggen. När du förbereder enheten kan du ange om målfiler ska skrivas över eller ignoreras med hjälp av fältet i data uppsättningens CSV-fil som kallas disposition: <Byt namn | No-overwrite | overwrite>. Som standard byter tjänsten namn på nya filer i stället för att skriva över befintliga blobbar eller filer.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Är WAImportExport-verktyget kompatibelt med 32-bitars operativ system?

Nej. WAImportExport-verktyget är bara kompatibelt med 64-bitars Windows-operativsystem. En fullständig lista över operativ system som stöds finns i [operativ system som stöds](./storage-import-export-requirements.md).

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Vad är den största Block Blob-och sid-BLOB-storlek som stöds av Azure import/export?

- Maximal Block Blob-storlek är ungefär 4.768 TB eller 5 000 000 MB.
- Maximal sid-BLOB-storlek är 8TB.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>Stöder Azure import/export AES-256-kryptering?

Ja. Azure import/export-tjänsten använder AES-256 BitLocker-kryptering.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure Import/Export för något?](storage-import-export-service.md)