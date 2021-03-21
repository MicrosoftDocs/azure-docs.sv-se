---
title: Vanliga frågor och svar – Azure Disk Encryption för virtuella Linux-datorer
description: Den här artikeln innehåller svar på vanliga frågor om Microsoft Azure disk kryptering för virtuella Linux IaaS-datorer.
author: msmbaldwin
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 6a85af1a5e0603d78dc9bc233fef56417e19c50e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553093"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>Vanliga frågor och svar om Azure Disk Encryption för virtuella Linux-datorer

Den här artikeln innehåller svar på vanliga frågor och svar om Azure Disk Encryption för virtuella Linux-datorer (VM: ar). Mer information om den här tjänsten finns i [Azure Disk Encryption översikt](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>Vad är Azure Disk Encryption för virtuella Linux-datorer?

Azure Disk Encryption för virtuella Linux-datorer använder dm-crypt-funktionen i Linux för att tillhandahålla fullständig disk kryptering av OS-diskarna * och data diskar. Dessutom ger den en kryptering av den temporära disken när du använder [funktionen EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms). Innehållet flödar från den virtuella datorn till lagrings Server delen. Därmed kan du tillhandahålla kryptering från slut punkt till slut punkt med en kundhanterad nyckel.
 
Se [virtuella datorer och operativ system som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Var är Azure Disk Encryption allmänt tillgänglig (GA)?

Azure Disk Encryption för virtuella Linux-datorer är allmänt tillgängliga i alla offentliga Azure-regioner.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Vilka användar upplevelser är tillgängliga med Azure Disk Encryption?

Azure Disk Encryption GA stöder Azure Resource Manager mallar, Azure PowerShell och Azure CLI. De olika användar upplevelserna ger dig flexibilitet. Du har tre olika alternativ för att aktivera disk kryptering för dina virtuella datorer. Mer information om användar upplevelsen och stegvisa vägledningar som är tillgängliga i Azure Disk Encryption finns [Azure Disk Encryption scenarier för Linux](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Hur mycket kostar Azure Disk Encryption?

Det kostar inget att kryptera virtuella dator diskar med Azure Disk Encryption, men det finns kostnader som är kopplade till användningen av Azure Key Vault. Mer information om Azure Key Vault kostnader finns på sidan för [Key Vault priser](https://azure.microsoft.com/pricing/details/key-vault/) .

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hur kan jag börja använda Azure Disk Encryption?

Läs [Azure Disk Encryption översikt](disk-encryption-overview.md)för att komma igång.

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Vilka VM-storlekar och operativ system stöder Azure Disk Encryption?

I artikeln [Azure Disk Encryption översikt](disk-encryption-overview.md) visas de [VM-storlekar](disk-encryption-overview.md#supported-vms) och [virtuella dator operativ system](disk-encryption-overview.md#supported-operating-systems) som stöder Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kan jag kryptera både start-och data volymer med Azure Disk Encryption?

Ja, du kan kryptera både start-och data volymer, eller så kan du kryptera data volymen utan att först behöva kryptera operativ system volymen. 

När du har krypterat operativ system volymen stöds inte kryptering på operativ system volymen. För virtuella Linux-datorer i en skalnings uppsättning kan endast data volymen krypteras.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Kan jag kryptera en demonterad volym med Azure Disk Encryption?

Nej, Azure Disk Encryption krypterar bara monterade volymer.

## <a name="what-is-storage-server-side-encryption"></a>Vad är kryptering av lagring på Server Sidan?

Kryptering av lagring på Server sidan krypterar Azure Managed disks i Azure Storage. Hanterade diskar krypteras som standard med kryptering på Server sidan med en plattforms-hanterad nyckel (från och med den 10 juni 2017). Du kan hantera kryptering av hanterade diskar med dina egna nycklar genom att ange en kundhanterad nyckel. Mer information finns i: [kryptering på Server sidan av Azure Managed disks](../disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Hur skiljer sig Azure Disk Encryption från lagrings Server sidans kryptering med kundhanterad nyckel och när ska jag använda varje lösning?

Azure Disk Encryption tillhandahåller kryptering från slut punkt till slut punkt för OS-disken, data diskarna och den tillfälliga disken med hjälp av en kundhanterad nyckel.
- Om dina krav omfattar kryptering av alla ovanstående och kryptering från slut punkt till slut punkt använder du Azure Disk Encryption. 
- Om dina krav inkluderar kryptering av endast data i vila med kundhanterad nyckel använder du [kryptering på Server sidan med Kundhanterade nycklar](../disk-encryption.md). Det går inte att kryptera en disk med både Azure Disk Encryption-och kryptering av lagring på Server sidan med Kundhanterade nycklar. 
- Om Linux-distribution inte visas under [operativ system som stöds för Azure Disk Encryption](disk-encryption-overview.md#supported-operating-systems) eller om du använder ett scenario som kallas i [scenarier som inte stöds för Windows](disk-encryption-linux.md#unsupported-scenarios), kan du överväga [kryptering på Server sidan med Kundhanterade nycklar](../disk-encryption.md).
- Om din organisations princip tillåter att du krypterar innehåll i vila med en Azure-hanterad nyckel krävs ingen åtgärd – innehållet krypteras som standard. För hanterade diskar krypteras innehållet i lagrings utrymmet som standard med kryptering på Server sidan med en plattforms-hanterad nyckel. Nyckeln hanteras av Azure Storages tjänsten. 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hur gör jag för att rotera hemligheter eller krypterings nycklar?

Om du vill rotera hemligheter anropar du bara samma kommando som du använde ursprungligen för att aktivera disk kryptering, vilket anger en annan Key Vault. Om du vill rotera nyckel krypterings nyckeln anropar du samma kommando som du använde ursprungligen för att aktivera disk kryptering och anger den nya nyckel krypteringen. 

>[!WARNING]
> - Om du tidigare har använt [Azure Disk Encryption med Azure AD-appen](disk-encryption-linux-aad.md) genom att ange autentiseringsuppgifter för Azure AD för att kryptera den här virtuella datorn måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Du kan inte använda Azure Disk Encryption på den här krypterade virtuella datorn eftersom detta inte stöds, vilket innebär att det inte finns stöd för att växla bort från AAD-program för den här krypterade virtuella datorn än.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Hur gör jag för att lägga till eller ta bort en nyckel krypterings nyckel om jag inte ursprungligen använde den?

Om du vill lägga till en nyckel krypterings nyckel anropar du kommandot Aktivera igen och skickar nyckel krypterings nyckel parametern. Om du vill ta bort en nyckel krypterings nyckel anropar du kommandot Aktivera igen utan parametern nyckel krypterings nyckel.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Tillåter Azure Disk Encryption att du kan ta med din egen nyckel (BYOK)?

Ja, du kan ange dina egna nyckel krypterings nycklar. Nycklarna skyddas i Azure Key Vault, som är nyckel lagret för Azure Disk Encryption. Mer information om nyckel krypterings nycklarna stöder scenarier finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Kan jag använda en nyckel krypterings nyckel som skapats av Azure?

Ja, du kan använda Azure Key Vault för att generera en nyckel krypterings nyckel för användning av Azure Disk Encryption. Nycklarna skyddas i Azure Key Vault, som är nyckel lagret för Azure Disk Encryption. Mer information om nyckel krypterings nyckeln finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Kan jag använda en lokal nyckel hanterings tjänst eller HSM för att skydda krypterings nycklarna?

Du kan inte använda lokal nyckel hanterings tjänst eller HSM för att skydda krypterings nycklarna med Azure Disk Encryption. Du kan bara använda tjänsten Azure Key Vault för att skydda krypterings nycklarna. Mer information om stöd scenarier för nyckel krypterings nyckel finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Vilka är kraven för att konfigurera Azure Disk Encryption?

Det finns förutsättningar för Azure Disk Encryption. Se Skapa [och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md) artikel om du vill skapa ett nytt nyckel valv, eller konfigurera ett befintligt nyckel valv för disk krypterings åtkomst för att aktivera kryptering och skydda hemligheter och nycklar. Mer information om stöd scenarier för nyckel krypterings nyckel finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Vilka är kraven för att konfigurera Azure Disk Encryption med en Azure AD-App (tidigare version)?

Det finns förutsättningar för Azure Disk Encryption. Se [Azure Disk Encryption med Azure AD](disk-encryption-linux-aad.md) -innehåll för att skapa ett Azure Active Directory program, skapa ett nytt nyckel valv eller konfigurera ett befintligt nyckel valv för disk krypterings åtkomst för att aktivera kryptering och skydda hemligheter och nycklar. Mer information om stöd scenarier för nyckel krypterings nyckel finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Stöds Azure Disk Encryption med en Azure AD-App (tidigare version)?
Ja. Disk kryptering med hjälp av en Azure AD-app stöds fortfarande. Men när du krypterar nya virtuella datorer rekommenderar vi att du använder den nya metoden i stället för att kryptera med en Azure AD-App. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Kan jag migrera virtuella datorer som har krypterats med en Azure AD-App till kryptering utan en Azure AD-App?
  För närvarande finns det ingen sökväg för direkt migrering för datorer som har krypterats med en Azure AD-App till kryptering utan en Azure AD-App. Det finns dessutom ingen direkt sökväg från kryptering utan en Azure AD-App för kryptering med en AD-App. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Vilken version av Azure PowerShell stöder Azure Disk Encryption?

Använd den senaste versionen av Azure PowerShell SDK för att konfigurera Azure Disk Encryption. Ladda ned den senaste versionen av [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption stöds *inte* av Azure SDK-versionen 1.1.0.

> [!NOTE]
> Linux Azure Disk Encryption Preview-tillägget "Microsoft. OSTCExtension. AzureDiskEncryptionForLinux" är föråldrat. Tillägget publicerades för Azure Disk Encryption Preview-versionen. Du bör inte använda för hands versionen av tillägget i test-eller produktions distributionen.

> För distributions scenarier som Azure Resource Manager (ARM), där du behöver Distribuera Azure Disk Encryption-tillägg för virtuella Linux-datorer för att aktivera kryptering på den virtuella Linux-IaaS, måste du använda tillägget för Azure Disk Encryption som stöds "Microsoft. Azure. Security. AzureDiskEncryptionForLinux".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Kan jag använda Azure Disk Encryption på min anpassade Linux-avbildning?

Du kan inte använda Azure Disk Encryption på din anpassade Linux-avbildning. Endast Galleri Linux-avbildningarna för de distributioner som stöds och som har anropats tidigare stöds. Anpassade Linux-avbildningar stöds inte för närvarande.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Kan jag tillämpa uppdateringar på en virtuell Linux hat-dator som använder yum-uppdateringen?

Ja, du kan utföra en yum uppdatering på en Red Hat Linux-dator.  Mer information finns i [Azure Disk Encryption på ett isolerat nätverk](disk-encryption-isolated-network.md).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Vad är det rekommenderade Azure Disk Encryption-arbetsflödet för Linux?

Följande arbets flöde rekommenderas för att få bästa möjliga resultat i Linux:
* Starta från den ej ändrade lager Galleri avbildningen som motsvarar nödvändiga OS-distribution och version
* Säkerhetskopiera alla monterade enheter som ska krypteras.  Den här säkerhets kopieringen tillåter återställning om det uppstår ett problem, till exempel om den virtuella datorn startas om innan krypteringen har slutförts.
* Kryptera (kan ta flera timmar eller till och med dagar beroende på VM-egenskaper och storlek på anslutna data diskar)
* Anpassa och Lägg till program till avbildningen efter behov.

Om det här arbets flödet inte är möjligt kan förlitande på [kryptering för lagringstjänst](../../storage/common/storage-service-encryption.md) (SSE) på plattforms lagrings konto lagret vara ett alternativ till fullständig disk kryptering med hjälp av dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Vad är disken "Bek Volume" eller "/mnt/azure_bek_disk"?

"Bek Volume" är en lokal data volym som lagrar krypterings nycklarna på ett säkert sätt för krypterade virtuella Azure-datorer.
> [!NOTE]
> Ta inte bort eller redigera något innehåll på disken. Demontera inte disken eftersom det krävs krypterings nyckel närvaro för alla krypterings åtgärder på den virtuella IaaS-datorn.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Vilken krypterings metod använder Azure Disk Encryption?

Azure Disk Encryption använder dekrypteringen standardvärdet AES-XTS-plain64 med en huvud nyckel på 256-bitars volym.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Om jag använder EncryptFormatAll och anger alla volym typer, kommer den att radera data på de data enheter som vi redan har krypterat?
Nej, data raderas inte från data enheter som redan är krypterade med Azure Disk Encryption. På liknande sätt som EncryptFormatAll inte krypterade om OS-enheten, krypterar den inte om den redan krypterade data enheten. Mer information finns i EncryptFormatAll- [kriterier](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>Stöds XFS-filsystem?
Kryptering av XFS OS-diskar stöds.

Kryptering av XFS-datadiskar stöds bara när parametern EncryptFormatAll används. Då formateras volymen och eventuella data raderas tidigare. Mer information finns i EncryptFormatAll- [kriterier](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Kan jag säkerhetskopiera och återställa en krypterad virtuell dator? 

Azure Backup tillhandahåller en mekanism för att säkerhetskopiera och återställa krypterade virtuella datorer i samma prenumeration och region.  Anvisningar finns i [säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup](../../backup/backup-azure-vms-encryption.md).  Det finns för närvarande inte stöd för att återställa en krypterad virtuell dator till en annan region.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Var kan jag ställa frågor eller lämna feedback?

Du kan ställa frågor eller lämna feedback på [sidan Microsoft Q&en fråga för Azure Disk Encryption](/answers/topics/azure-disk-encryption.html).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig mer om de vanligaste frågorna som rör Azure Disk Encryption. Mer information om den här tjänsten finns i följande artiklar:

- [Översikt av Azure Disk Encryption](disk-encryption-overview.md)
- [Använd disk kryptering i Azure Security Center](../../security-center/asset-inventory.md)
- [Azure-datakryptering i vila](../../security/fundamentals/encryption-atrest.md)