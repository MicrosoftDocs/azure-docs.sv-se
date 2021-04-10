---
title: Aktivera Azure Disk Encryption för virtuella Windows-datorer
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure disk kryptering för virtuella Windows-datorer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 8e95f770a3335d66eae0a690e148c4d6ddc22d5c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555337"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure Disk Encryption för virtuella Windows-datorer

Azure Disk Encryption hjälper till att skydda dina data så att din verksamhet uppfyller både interna och externa krav och åtaganden. Den använder [BitLocker](https://en.wikipedia.org/wiki/BitLocker) -funktionen i Windows för att tillhandahålla volym kryptering för operativ system och data diskar för virtuella Azure-datorer (VM) och är integrerat med [Azure Key Vault](../../key-vault/index.yml) som hjälper dig att styra och hantera disk krypterings nycklar och hemligheter.

Azure Disk Encryption är zon elastisk, på samma sätt som Virtual Machines. Mer information finns i [Azure-tjänster som stöder Tillgänglighetszoner](../../availability-zones/az-region.md).

Om du använder [Azure Security Center](../../security-center/index.yml)får du en avisering om du har virtuella datorer som inte är krypterade. Aviseringarna visas med hög allvarlighets grad och rekommendationen är att kryptera de virtuella datorerna.

![Azure Security Center disk krypterings avisering](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Mer information finns i [Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-overview-aad.md) . 
> - Vissa rekommendationer kan öka användningen av data, nätverk eller beräknings resurser, vilket resulterar i ytterligare licens-eller prenumerations kostnader. Du måste ha en giltig aktiv Azure-prenumeration för att kunna skapa resurser i Azure i de regioner som stöds.

Du kan lära dig grunderna i Azure Disk Encryption för Windows på bara några minuter med guiden [skapa och kryptera en virtuell Windows-dator med Azure CLI snabb start](disk-encryption-cli-quickstart.md) eller [skapa och kryptera en virtuell Windows-dator med Azure PowerShell snabb start](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Virtuella datorer och operativ system som stöds

### <a name="supported-vms"></a>Virtuella datorer som stöds

Virtuella Windows-datorer är tillgängliga i [flera storlekar](../sizes-general.md). Azure Disk Encryption stöds på virtuella datorer i generation 1 och generation 2. Azure Disk Encryption är också tillgängligt för virtuella datorer med Premium Storage.

Azure Disk Encryption är inte tillgänglig på virtuella datorer med [Basic, A-serien](https://azure.microsoft.com/pricing/details/virtual-machines/series/)eller på virtuella datorer med mindre än 2 GB minne.  Azure Disk Encryption är inte heller tillgängligt på VM-avbildningar utan temporära diskar (DV4, Dsv4, Ev4 och Esv4).  Se [storlekar för virtuella Azure-datorer utan lokal temporär disk](../azure-vms-no-temp-disk.md).  Mer undantag finns i [Azure Disk Encryption: scenarier som inte stöds](disk-encryption-windows.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Operativsystem som stöds

- Windows-klient: Windows 8 och senare.
- Windows Server: Windows Server 2008 R2 och senare.  
 
> [!NOTE]
> Windows Server 2008 R2 kräver att .NET Framework 4,5 installeras för kryptering. installera den från Windows Update med den valfria uppdateringen Microsoft .NET Framework 4.5.2 för Windows Server 2008 R2 x64-baserade system ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core och Windows Server 2016 Core kräver att BdeHdCfg-komponenten installeras på den virtuella datorn för kryptering.


## <a name="networking-requirements"></a>Nätverkskrav
Om du vill aktivera Azure Disk Encryption måste de virtuella datorerna uppfylla följande konfigurations krav för nätverks slut punkt:
  - Om du vill hämta en token för att ansluta till ditt nyckel valv måste den virtuella Windows-datorn kunna ansluta till en Azure Active Directory-slutpunkt, \[ login.microsoftonline.com \] .
  - Om du vill skriva krypterings nycklarna till ditt nyckel valv måste den virtuella Windows-datorn kunna ansluta till Key Vault-slutpunkten.
  - Den virtuella Windows-datorn måste kunna ansluta till en Azure Storage-slutpunkt som är värd för Azure Extension-lagringsplatsen och ett Azure Storage-konto som är värd för VHD-filerna.
  -  Om säkerhets principen begränsar åtkomsten från virtuella Azure-datorer till Internet kan du matcha föregående URI och konfigurera en speciell regel för att tillåta utgående anslutning till IP-adresserna. Mer information finns i [Azure Key Vault bakom en brand vägg](../../key-vault/general/access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>grupprincip krav

Azure Disk Encryption använder BitLockers externa nyckel skydd för virtuella Windows-datorer. För domänanslutna virtuella datorer ska du inte skicka några grup principer som tillämpar TPM-skydd. Information om grup principen för "Tillåt BitLocker utan en kompatibel TPM" finns i [BitLocker Grupprincip Reference](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

BitLocker-principen på domänanslutna virtuella datorer med anpassad grup princip måste innehålla följande inställning: [Konfigurera användar lagring av BitLocker-återställningsinformation – > tillåt 256-bitars återställnings nyckel](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption Miss fungerar när anpassade grup princip inställningar för BitLocker inte är kompatibla. Tillämpa den nya principen på datorer som inte har rätt princip inställning, tvinga den nya principen att uppdatera (gpupdate.exe/Force) och starta sedan om.

Azure Disk Encryption Miss fungerar om grup principen på domän nivå blockerar AES-CBC-algoritmen som används av BitLocker.

## <a name="encryption-key-storage-requirements"></a>Lagrings krav för krypterings nyckel  

Azure Disk Encryption kräver ett Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter. Ditt nyckel valv och virtuella datorer måste finnas i samma Azure-region och prenumeration.

Mer information finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologi
I följande tabell definieras några vanliga termer som används i dokumentationen för Azure Disk Encryption:

| Terminologi | Definition |
| --- | --- |
| Azure Key Vault | Key Vault är en kryptografisk nyckel hanterings tjänst som är baserad på FIPS-validerade (Federal Information Processing Standards) moduler för maskin varu säkerhet. Dessa standarder hjälper till att skydda dina kryptografiska nycklar och känsliga hemligheter. Mer information finns i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -dokumentationen och [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) är optimerat för att hantera och administrera Azure-resurser från kommando raden.|
| BitLocker |[BitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831713(v=ws.11)) är en bransch känd Windows Volume Encryption-teknik som används för att aktivera disk kryptering på virtuella Windows-datorer. |
| Nyckel krypterings nyckel (KEK) | Den asymmetriska nyckeln (RSA 2048) som du kan använda för att skydda eller figursätta hemligheten. Du kan ange en HSM-skyddad nyckel eller en skyddad nyckel för program vara. Mer information finns i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -dokumentationen och [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-cmdletar | Mer information finns i [Azure PowerShell-cmdletar](/powershell/azure/). |

## <a name="next-steps"></a>Nästa steg

- [Snabb start – skapa och kryptera en virtuell Windows-dator med Azure CLI ](disk-encryption-cli-quickstart.md)
- [Snabb start – skapa och kryptera en virtuell Windows-dator med Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-scenarier på virtuella Windows-datorer](disk-encryption-windows.md)
- [Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started) 
- [PowerShell-skript för Azure Disk Encryption krav](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md)