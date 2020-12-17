---
title: Microsoft Azure Data Box Disk – vanliga frågor | Microsoft Docs i data
description: Innehåller vanliga frågor och svar kring Azure Data Box Disk, en molnlösning som gör det möjligt att överföra stora mängder data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: alkohli
ms.openlocfilehash: 3df8f75a726639e87157a1aa7fd9ff4d35c0ef4f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654633"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk: vanliga frågor och svar

Med Microsoft Azure Data Box Disk-lösningen kan du skicka terabyte med lokala data till Azure på ett snabbt, kostnadseffektivt och tillförlitligt sätt. Här hittar du vanliga frågor och svar om att använda Data Box-diskar i Azure-portalen. 

Frågor och svar är ordnade i följande kategorier:

- Om tjänsten
- Konfigurera och ansluta 
- Spåra status
- Migrera data 
- Verifiera och ladda upp data 


## <a name="about-the-service"></a>Om tjänsten

### <a name="q-what-is-azure-data-box-service"></a>F. Vad är Azure Data Box-tjänsten? 
A.  Azure Data Box-tjänsten är utformad för offlineinmatning av data. Tjänsten hanterar en rad produkter som alla har skräddarsytts för dataöverföring för olika lagringskapaciteter. 

### <a name="q-what-are-azure-data-box-disks"></a>F. Vad är Azure Data Box-diskar?
A. Azure Data Box-diskar möjliggör snabb, prisvärd och säker överföring av terabytes data till och från Azure. Microsoft skickar 1 till 5 diskar till dig, med en maximal lagringskapacitet på 35 TB. Du kan enkelt konfigurera, ansluta och låsa upp diskarna via Data Box-tjänsten i Azure-portalen.  

Diskarna krypteras med hjälp av Microsoft BitLocker-enhetskryptering, och dina krypteringsnycklar hanteras på Azure-portalen. Sedan kopierar du data från kundens servrar. I datacentret migrerar Microsoft dina data från enheten till molnet via en snabb, privat nätverksöverföringslänk. De överförs till Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>F. När ska jag använda Data Box-diskar?
A. Om du har högst 40 TB data som du vill överföra till Azure kan du använda Data Box-diskar.

### <a name="q-what-is-the-price-of-data-box-disks"></a>F. Hur mycket kostar Data Box-diskarna?
A. Information om priset för Data Box-diskar finns på [sidan med prisinformation](https://azure.microsoft.com/pricing/details/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>F. Hur får jag Data Box-diskarna? 
A.  Du skaffar Azure Data Box-diskar genom att logga in på Azure-portalen och skapa en Data Box-beställning på diskar. Ange din kontakt- och aviseringsinformation. Beroende på tillgängligheten skickas diskarna till dig inom tio dagar.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>F. Vad är den maximala mängden data kan överföra med Data Box-diskar?
A. Om du får fem diskar om vardera 8 TB (7 TB användbar kapacitet) är den maximala användbara kapaciteten 35 TB. Du kan då överföra 35 TB data. Om du vill överföra en större datamängd måste du beställa fler diskar.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>F. Hur ser jag om Data Box-diskar finns tillgängliga i min region? 
A.  Om du vill se var Data Box-enhet diskarna är tillgängliga går du till [regionens tillgänglighet](data-box-disk-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>F. I vilka regioner kan jag lagra data med Data Box-diskar?
A. Data Box Disk stöds för alla regioner i USA, Kanada, Australien, Västeuropa och Nord Europa, Korea och Japan. Endast regionerna med offentligt Azure-moln stöds. Azure Government och andra begränsade moln stöds inte.

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>F. I vilka regioner kan jag lagra data med Data Box-diskar?
A. Data Box Disk stöds för alla regioner i USA, Kanada, Australien, Västeuropa och Nord Europa, Korea och Japan. Endast regionerna med offentligt Azure-moln stöds. Azure Government och andra begränsade moln stöds inte.

### <a name="q-how-can-i-import-source-data-present-at-my-location-in-one-countryregion-to-an-azure-region-in-a-different-country"></a>F. Hur kan jag importera källdata som finns på min plats i ett land/region till en Azure-region i ett annat land?
A. Data Box Disk har endast stöd för data inmatning inom samma land/region som deras destination och kommer inte att korsa några internationella gränser. Det enda undantaget är för beställningar i Europeiska unionen (EU), där Data Box-enhet diskar kan levereras till och från valfritt EU-land/-region.

Om du till exempel vill flytta data på din plats i Kanada till ett lagrings konto med Azure västra USA kan du uppnå det på följande sätt:

### <a name="option-1"></a>Alternativ 1: 

Leverera en [disk som stöds](../storage/common/storage-import-export-requirements.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#supported-disks) och som innehåller data med [Azure import/export-tjänsten](../storage/common/storage-import-export-service.md) från käll platsen i Kanada till Azures Data Center i USA, västra.

### <a name="option-2"></a>Alternativ 2:

1. Beställ Data Box Disk i Kanada genom att välja ett lagrings konto, till exempel i Kanada, centrala. SSD-diskarna levereras från Azure-datacentret i Kanada Central till leverans adressen (i Kanada) som tillhandahålls när ordern skapas.

2. När du har kopierat data från din lokala server till diskarna kan du returnera dem till Azure-datacentret i Kanada med Microsoft-angivna retur etiketter. De data som finns på Data Box Disken laddas sedan upp till mål lagrings kontot i Azure-regionen som du valde när du skapade ordningen.

3. Du kan sedan använda ett verktyg som AzCopy för att kopiera data till ett lagrings konto i USA, västra. I det här steget debiteras standard [avgifterna](https://azure.microsoft.com/pricing/details/bandwidth/) för [lagring](https://azure.microsoft.com/pricing/details/storage/) och bandbredd som inte ingår i data Box disk fakturering.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>F. Vem kontaktar jag om jag får problem med Data Box-diskarna?
A. [Kontakta Microsoft Support](./data-box-disk-contact-microsoft-support.md)om du stöter på problem med data Box-enhet diskar.

## <a name="order-device"></a>Beställa en enhet

### <a name="q-how-do-i-get-data-box-disk"></a>F. Hur gör jag för att hämta Data Box Disk? 
A.  Om du vill hämta Azure Data Box Disk loggar du in på Azure Portal och skapar en Data Box Disk order. Ange din kontakt- och aviseringsinformation. När du har placerat en beställning, baserat på tillgängligheten, levereras Data Box Disk till dig inom 10 dagar. Mer information finns på sidan [Beställa en Data Box](data-box-disk-deploy-ordered.md).

### <a name="q-i-couldnt-create-a-data-box-disk-order-in-the-azure-portal-why"></a>F. Jag kunde inte skapa en Data Box Disk ordning i Azure Portal. Varför?
A. Om du inte kan skapa en Data Box Disks ordning är det ett problem med prenumerations typen eller åtkomsten.

Kontrollera din prenumeration. Data Box Disk är endast tillgängligt för prenumerations erbjudanden för Enterprise-avtal (EA) och Cloud Solution Provider (CSP). Om du inte har någon av dessa prenumerations typer kontaktar du Microsoft Support för att uppgradera prenumerationen.

Om du har en prenumerationstyp som stöds kontrollerar du din åtkomstnivå för prenumerationen. Du måste vara en Deltagare eller Ägare i prenumerationen för att kunna skapa en order.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>F. Hur lång tid tar det att skapa beställningar på data som överförs till Azure?

A. Följande beräknade produktions tider för varje fas i order bearbetningen ger dig en bra uppfattning om vad du kan förväntar dig.  

Dessa produktions tider är *uppskattningar*. Tiden för varje steg i order bearbetningen påverkas av belastningen på data centret, samtidiga beställningar och andra miljö villkor.

**Uppskattade produktions tider för en Data Box Disk ordning:**

1. Beställ Data Box Disk: några minuter från portalen
2. Diskallokering och-förberedelse: upp till fem arbets dagar, beroende på inventerings tillgänglighet och antalet väntande order som ska bearbetas
3. Frakt: 2–3 arbetsdagar
4. Data kopiering på kund webbplatsen: beror på data, storlek och antal filer.
5. Returleverans: 2–3 arbetsdagar
6. Bearbetning i data centret och uppladdning till Azure: data uppladdning börjar i data centret så snart den operativa bearbetningen är klar och disken är ansluten. Uppladdnings tiden beror på data, storlek och antal filer.

## <a name="configure-and-connect"></a>Konfigurera och ansluta
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>F. Kan jag ange antal Data Box-diskar i beställningen?
A.  Nej. Du får 8 TB diskar (högst 5 diskar) beroende på dina data storlek och tillgänglighet för diskarna.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>F. Hur låser jag upp Data Box-diskarna? 
A.  Öppna Azure-portalen och öppna din Data Box-diskbeställning. Navigera till **Enhetsinformation**. Kopiera supportnyckeln. Ladda ned och extrahera upplåsningsverktyget för Data Box Disk från Azure-portalen för ditt operativsystem. Kör verktyget på den dator som har de data du vill kopiera till diskarna. Ange nyckeln för att låsa upp diskarna. Samma nyckel låser upp alla diskar. 

Stegvisa instruktioner finns i [Låsa upp diskar på en Windows-klient](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) eller [Låsa upp diskar på en Linux-klient](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>F. Kan jag använda en Linux-dator som värd och ansluta och kopiera data till Data Box-diskar?
A.  Ja. Både Linux-klienten och Windows-klienten kan användas till att ansluta och kopiera data på Data Box-diskarna. Mer information finns i listan över [operativsystem som stöds](data-box-disk-system-requirements.md) för värddatorn.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>F. Mina diskar har skickats men jag vill avbryta beställningen. Varför kan jag inte trycka på knappen och avbryta?
A.  Det går bara att avbryta redan beställda diskar innan de skickas till dig. När diskarna har skickats iväg går det inte att avbryta beställningen. Du kan dock skicka tillbaka diskarna mot en kostnad. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>F. Kan jag ansluta flera Data Box-diskar samtidigt till värddatorn och överföra data?
A. Ja. Du kan ansluta flera Data Box-diskar till samma värddator och överföra data, och du kan köra flera kopieringsjobb parallellt.

## <a name="track-status"></a>Spåra status

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>F. Hur gör jag för att spåra diskarna, från beställningen till det att jag skickar tillbaka dem? 
A.  Du kan spåra statusen för Data Box Disk-beställningen i Azure-portalen. När du skapar beställningen blir du ombedd att ange en e-postadress dit aviseringar kan skickas. Om du angav en sådan e-postadress meddelas du om alla statusändringar för beställningen. Mer information om hur du [konfigurerar e-postaviseringar](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>F. Hur gör jag för att skicka tillbaka diskarna? 
A.  Microsoft skickar med en adressetikett i paketet med Data Box-diskarna. Fäst etiketten på leveranskartongen och lämna in det förseglade paketet hos transportföretaget. Om etiketten är skadad eller har försvunnit kan du hämta en ny via **Översikt > Ladda ned adressetikett**.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>Kan jag hämta min Data Box Disk beställningen själv? Kan jag returnera diskarna via en operatör som jag väljer?
A. Ja. Microsoft erbjuder även självhanterad leverans i US Gov region. När du placerar Data Box Disks ordningen kan du välja alternativ för själv hantering av leverans. Gör så här för att hämta din Data Box Disks ordning:
    
1. När du har placerat ordern bearbetas ordern och diskarna förbereds. Du kommer att meddelas via ett e-postmeddelande om att din beställning är klar för hämtning. 
2. När ordern är klar för hämtning går du till din beställning i Azure Portal och navigerar till **översikts** bladet. 
3. Ett meddelande visas med en kod i Azure Portal. E-posta [Azure Data boxs drifts teamet](mailto:adbops@microsoft.com) och ange koden. Teamet tillhandahåller platsen och schemalägger hämtnings datum och-tid. Du måste ringa teamet inom fem arbets dagar efter att du fått e-postaviseringen.

När data kopieringen och verifieringen har slutförts utför du följande steg för att returnera disken:

1. När dataverifieringen har genomförts kan du koppla från diskarna. Ta bort anslutningskablarna.
2. Packa upp alla diskar och de anslutande kablarna med en bubbla och placera dem i rutan leverera. Avgifter kan tillkomma om tillbehören saknas.

    - Återanvänd förpackningen från den första leveransen. Vi rekommenderar att du förpackar diskarna i ordentligt fäst cellplast.
    - Packa tätt så att den förpackade enheten inte rör sig i kartongen.
3. Gå till **bladet översikt** för din beställning i Azure Portal. Du bör se ett meddelande med en kod.
4. Använd koden och skicka e-postmeddelandet till den [Azure Data Box drifts gruppen](mailto:adbops@microsoft.com) och ange koden. De ger dig information om var och när du ska ta bort diskarna.


## <a name="migrate-data"></a>Migrera data

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>F. Hur stor mängd data får plats på Data Box-diskarna?  
A.  Du kan få upp till 5 diskar med en användningsbar kapacitet på totalt 35 TB. Diskarna är 8 TB (användbart utrymme 7 TB).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>F. Vilken är den största blockblob- och sidblobstorleken som stöds av Data Box Disks? 
A.  De maximala storlekarna regleras av begränsningarna för Azure Storage. Den maximala blockbloben är ungefär 4,768 TiB och den maximala sidblobstorleken är 8 TiB. Mer information finns i [skalbarhets-och prestanda mål för Blob Storage](../storage/blobs/scalability-targets.md).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>F. Vilken är dataöverföringshastigheten för Data Box-diskar?
A. Vid tester med diskar ansluta via USB 3.0 var diskprestandan upp till 430 MB/s. De faktiska hastigheterna varierar beroende på filstorleken. För mindre filer kan du se lägre prestanda.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>F. Hur vet jag att mina data är säkra under överföringen? 
A.  Data Box-diskarna krypteras med BitLocker-AES-128-bitarskryptering och nyckeln är endast tillgänglig i Azure-portalen. Logga in på Azure-portalen med autentiseringsuppgifterna och hämta nyckeln. Ange den här nyckeln när du kör upplåsningsverktyget för Data Box Disk.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>F. Hur gör jag för att kopiera data till Data Box-diskarna? 
A.  Använd ett SMB-kopierings verktyg som `Robocopy` , `Diskboss` eller till och med Windows Utforskaren dra och släpp för att kopiera data till diskar.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>F. Finns det några tips för att påskynda datakopieringen?
A.  Påskynda kopieringsprocessen:

- Använd flera dataströmmar. Med `Robocopy` kan du till exempel använda det multitrådade alternativet. Mer information om de exakta kommandon som används finns i [Självstudie: Kopiera data till en Azure Data Box-disk och verifiera](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Använda flera sessioner.
- Kontrollera att dina data finns lokalt på datorn som diskarna är ansluta till, i stället för att kopiera via nätverk (där du kan begränsas av nätverkshastigheterna).
- Se till att du använder USB 3.0 eller senare under kopieringen. Hämta och Använd [ `USBView` verktyget](/windows-hardware/drivers/debugger/usbview) för att identifiera de USB-styrenheter och USB-enheter som är anslutna till datorn.
- Analysera prestanda för den dator som används för att kopiera data. Hämta och Använd [Bluestop- `FIO` verktyget](https://ci.appveyor.com/project/axboe/fio) för att mäta server maskin varans prestanda. Välj den senaste x86-eller x64-versionen, Välj fliken **artefakter** och ladda ned MSI.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>F. Hur skyndar jag på dataöverföringen om källdata består av små filer (KB eller få MB)?
A.  Påskynda kopieringsprocessen:

- Skapa en lokal VHDx på snabb lagring eller skapa en tom virtuell hårddisk på HDD/SSD (långsammare).
- Montera på en virtuell dator.
- Kopiera filer till den virtuella datorns disk.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>F. Kan jag använda flera lagringskonton med Data Box-diskar?
A.  Nej. För närvarande går det endast att använda ett lagringskonto, allmänt eller klassiskt, med Data Box-diskar. Det går att använda både högfrekvent och lågfrekvent blob. För närvarande stöds endast lagringskontona i USA och i Europa, västra och Europa, norra i det offentliga Azure-molnet.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>F. Vilken uppsättning verktyg är tillgängliga för mina data med Data Box-diskar?
A. Verktygsuppsättningen som är tillgänglig med Data Box-diskar består av tre verktyg:
 - **Data Box disk Lås upp verktyg**: Använd det här verktyget för att låsa upp krypterade diskar som levereras från Microsoft. När du låser upp diskarna med verktyget måste du ange en nyckel som du hittar i Data Box Disk-beställningen på Azure-portalen. 
 - **Data Box disk validerings verktyg**: Använd det här verktyget för att verifiera storlek, format och blob-namn enligt namngivnings konventionerna i Azure. Den genererar också kontroll summor för kopierade data som sedan används för att verifiera de data som överförs till Azure.
 - **Data Box disk verktyget för delad kopiering**: Använd det här verktyget när du använder flera diskar och har en stor data uppsättning som måste delas upp och kopieras på alla diskar. Det här verktyget är för närvarande tillgängligt för Windows. Det här verktyget stöds inte med hanterade diskar. Det här verktyget verifieras också när data kopieras, och därför kan du hoppa över validerings steget när du använder det här verktyget.

Verktygsuppsättningen är tillgänglig både för Windows och Linux. Du kan hämta verktygen här:
- [Ladda ned Data Box Disk-verktyg för Windows](https://aka.ms/databoxdisktoolswin) 
- [Ladda ned Data Box Disk-verktyg för Linux](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>F. Kan jag använda Data Box Disk för att överföra data till Azure Files och sedan använda data med Azure File Sync? 
A. Azure Files stöds med Data Box Disk men fungerar inte bra med Azure File Sync. Metadata bevaras inte om fil data används med Azure File Sync.


## <a name="verify-and-upload"></a>Verifiera och ladda upp

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>F. Hur snart kan jag få åtkomst till mina data i Azure när jag har skickat tillbaka diskarna? 
A.  När orderstatusen för datakopieringen visas som slutförd bör du komma åt dina data direkt.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>F. Var i Azure finns mina data efter uppladdningen?
A.  När du kopierar data under mapparna *BlockBlob* och *PageBlob* på disken skapas en container i Azure-lagringskontot för varje undermapp under mappen *BlockBlob* och *PageBlob*. Om du kopierade filerna i mapparna *BlockBlob* och *PageBlob* direkt, finns filerna i en standard behållare *$root* under Azure Storage kontot. När du kopierar data till en mapp under mappen *AzureFile* skapas en fileshare.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>F. Jag har precis märkt att jag inte följde namngivningskraven för min container. Misslyckas dataöverföringen till Azure?
A. Versaler i behållar namn konverteras automatiskt till gemener. Om namnen inte är kompatibla på andra sätt, t. ex. de innehåller specialtecken eller andra språk – uppladdningen Miss fungerar. Mer information finns i [Azure-namngivningskonventioner](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>F. Hur verifierar jag de data jag kopierade till flera Data Box-diskar?
A.  När kopieringen är klar kan du köra `DataBoxDiskValidation.cmd` i mappen *DataBoxDiskImport* för att generera kontrollsummor för verifiering. Om du har flera diskar behöver du öppna ett kommandofönster per disk och kör det här kommandot. Tänk på att den här åtgärden kan ta lång tid (~ timmar) beroende på storleken på dina data.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>F. Vad händer med mina data när jag har skickat tillbaka diskarna?
A.  När datakopieringen till Azure är klar raderas alla data på disken på ett säkert sätt enligt riktlinjerna NIST SP 800-88 Revision 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>F. Hur skyddade är mina data under transporten? 
A.  Data Box-enhet diskarna krypteras med AES-128 Microsoft BitLocker-kryptering och en enda nyckel krävs för att låsa upp alla diskar och få åtkomst till data.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>F. Måste jag köra validering av kontrollsumma om jag lägger till mer data på Data Box-diskarna?
A. Ja. Om du vill verifiera dina data (vi rekommenderar att du gör det!), måste du köra verifieringen om du har lagt till ytterligare data på diskarna.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>F. Jag har använt samtliga diskar och behöver beställa fler diskar. Går det att beställa dem snabbt?
A. Du kan klona din tidigare beställning. En kloning skapar samma ordning som tidigare och du kan redigera beställningsinformationen utan att behöva ange adress, kontaktuppgifter och aviseringsadress.

### <a name="q-i-copied-data-to-the-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-how-can-i-locate-it"></a>F. Jag har kopierat data till mappen ManagedDisk Jag ser inte några hanterade diskar med resurs gruppen som angetts för hanterade diskar. Har mina data laddats upp till Azure? Hur hittar jag det?
A. Ja. Dina data har laddats upp till Azure, men om du inte ser några hanterade diskar med de angivna resurs grupperna beror det förmodligen på att data inte var giltiga. Om Page blobbar, block blobbar, Azure Files eller hanterade diskar inte är giltiga, kommer de att gå till följande mappar:
 - Page blobbar skickas till en Block-Blob-behållare som börjar med *databoxdisk-ogiltig-PB-*.
 - Azure Files skickas till en Block Blob-behållare som börjar med *databoxdisk-ogiltig-AF-*.
 - Hanterade diskar skickas till en Block Blob-behållare som börjar med *databoxdisk-ogiltig-MD-*.

## <a name="next-steps"></a>Nästa steg

- Granska [data Box disk system krav](data-box-disk-system-requirements.md).
- Förstå [Data Box Disk-begränsningarna](data-box-disk-limits.md).
- Distribuera snabbt [Azure Data Box Disk](data-box-disk-quickstart-portal.md) på Azure-portalen.