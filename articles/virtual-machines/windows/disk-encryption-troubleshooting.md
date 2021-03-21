---
title: Azure Disk Encryption fel söknings guide
description: Den här artikeln innehåller fel söknings tips för Microsoft Azure disk kryptering för virtuella Windows-datorer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 057aba3e49d32694563f412101be499342f2aad0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550611"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure Disk Encryption fel söknings guide

Den här guiden är för IT-proffs, informations säkerhets analytiker och moln administratörer vars organisationer använder Azure Disk Encryption. Den här artikeln är att hjälpa till med fel sökning av problem med disk kryptering.

Innan du vidtar stegen nedan måste du först se till att de virtuella datorer som du försöker kryptera finns bland de [VM-storlekar och operativ system som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems)och att du uppfyller alla krav:

- [Nätverkskrav](disk-encryption-overview.md#networking-requirements)
- [Grup princip krav](disk-encryption-overview.md#group-policy-requirements)
- [Lagrings krav för krypterings nyckel](disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Felsöka Azure Disk Encryption bakom en brand vägg

När anslutningen begränsas av en brand vägg, ett krav för en brand vägg eller en nätverks säkerhets grupp (NSG), kan det hända att tilläggets möjlighet att utföra nödvändiga åtgärder avbryts. Detta avbrott kan resultera i status meddelanden som "tilläggs status är inte tillgänglig på den virtuella datorn". I förväntat scenario slutförs inte krypteringen. Avsnitten som följer innehåller några vanliga brand Väggs problem som du kan undersöka.

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
Alla inställningar för nätverks säkerhets grupper som tillämpas måste fortfarande tillåta slut punkten att uppfylla de dokumenterade nätverks konfigurations [kraven](disk-encryption-overview.md#networking-requirements) för disk kryptering.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault bakom en brand vägg

När kryptering aktive ras med [autentiseringsuppgifter för Azure AD](disk-encryption-windows-aad.md#)måste den virtuella mål datorn tillåta anslutning till både Azure Active Directory slut punkter och Key Vault slut punkter. Nuvarande Azure Active Directory autentiserings slut punkter underhålls i avsnitten 56 och 59 i dokumentationen för [Microsoft 365 URL: er och IP-adressintervall](/microsoft-365/enterprise/urls-and-ip-address-ranges) . Key Vault-instruktioner finns i dokumentationen om hur du [får åtkomst till Azure Key Vault bakom en brand vägg](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 
Den virtuella datorn måste kunna komma åt [Azure instance metadata service](../windows/instance-metadata-service.md) -slutpunkten som använder en välkänd icke-flyttbar IP-adress ( `169.254.169.254` ) som bara kan nås från den virtuella datorn.  Proxykonfigurationen som ändrar lokal HTTP-trafik till den här adressen (till exempel att lägga till ett X-vidarebefordrat-för-huvud) stöds inte.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Felsöka Windows Server 2016 Server Core

BdeHdCfg-komponenten är inte tillgänglig som standard på Windows Server 2016 Server Core. Den här komponenten krävs av Azure Disk Encryption. Den används för att dela upp system volymen från en operativ system volym, som bara utförs en gång för den virtuella datorns livs längd. Dessa binärfiler krävs inte vid senare krypterings åtgärder.

Undvik det här problemet genom att kopiera följande fyra filer från en virtuell Windows Server 2016 Data Center-dator till samma plats på Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Ange följande kommando:

   ```
   bdehdcfg.exe -target default
   ```

1. Det här kommandot skapar en systempartition på 550 MB. Starta om systemet.

1. Använd DiskPart för att kontrol lera volymerna och fortsätt sedan.  

Exempel:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Felsöka krypterings status 

Portalen kan visa en disk som krypterad även efter att den har krypterats på den virtuella datorn.  Detta kan inträffa när lågnivå kommandon används för att direkt dekryptera disken inifrån den virtuella datorn, i stället för att använda Azure Disk Encryption hanterings kommandon på högre nivå.  Med kommandona på högre nivå kan du inte bara dekryptera disken inifrån den virtuella datorn, men utanför den virtuella datorn uppdaterar de också viktiga krypterings inställningar och tilläggs inställningar som är associerade med den virtuella datorn.  Om dessa inte behålls i justeringen kommer plattformen inte att kunna rapportera krypterings status eller upprätta den virtuella datorn korrekt.

Om du vill inaktivera Azure Disk Encryption med PowerShell använder du [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) följt av [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Det går inte att köra Remove-AzVMDiskEncryptionExtension innan krypteringen har inaktiverats.

Om du vill inaktivera Azure Disk Encryption med CLI använder du [inaktivera AZ VM-kryptering](/cli/azure/vm/encryption). 

## 



## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig mer om några vanliga problem i Azure Disk Encryption och hur du felsöker problemen. Mer information om den här tjänsten och dess funktioner finns i följande artiklar:

- [Använd disk kryptering i Azure Security Center](../../security-center/asset-inventory.md)
- [Azure-datakryptering i vila](../../security/fundamentals/encryption-atrest.md)