---
title: Azure Disk Encryption med nödvändiga komponenter för Azure AD-appar (tidigare version)
description: Den här artikeln ger komplement till Azure Disk Encryption för virtuella Linux-datorer med ytterligare krav och krav för Azure Disk Encryption med Azure AD.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: e27eb64139c20ac2b8c776edc2d0840b80fddb62
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558346"
---
# <a name="azure-disk-encryption-with-azure-active-directory-ad-previous-release"></a>Azure Disk Encryption med Azure Active Directory (AD) (tidigare version)

Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure Active Directory (Azure AD)-program parameter för att aktivera disk kryptering för virtuella datorer. Med den nya versionen behöver du inte längre ange autentiseringsuppgifter för Azure AD under steget aktivera kryptering. Alla nya virtuella datorer måste krypteras utan Azure AD-programmets parametrar med den nya versionen. Instruktioner för hur du aktiverar VM Disk Encryption med den nya versionen finns [Azure Disk Encryption för virtuella Linux-datorer](disk-encryption-overview.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att behållas med AAD-syntaxen.

Den här artikeln ger tillägg till [Azure Disk Encryption för virtuella Linux-datorer](disk-encryption-overview.md) med ytterligare krav och krav för Azure Disk Encryption med Azure AD (tidigare version).

Informationen i dessa avsnitt är densamma:

- [Virtuella datorer och operativ system som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ytterligare krav för virtuell dator](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a>Nätverks-och grupprincip

Om du vill aktivera Azure Disk Encryption funktionen med hjälp av den äldre AAD-parameterns syntax måste IaaS-virtuella datorer (infrastruktur som en tjänst) uppfylla följande konfigurations krav för nätverks slut punkt: 
  - Om du vill hämta en token för att ansluta till ditt nyckel valv måste den virtuella datorn IaaS kunna ansluta till en Azure AD-slutpunkt, \[ login.microsoftonline.com \] .
  - Om du vill skriva krypterings nycklarna till ditt nyckel valv måste den virtuella datorn IaaS kunna ansluta till Key Vault-slutpunkten.
  - Den virtuella datorn IaaS måste kunna ansluta till en Azure Storage-slutpunkt som är värd för Azure Extension-lagringsplatsen och ett Azure Storage-konto som är värd för VHD-filerna.
  -  Om säkerhets principen begränsar åtkomsten från virtuella Azure-datorer till Internet kan du matcha föregående URI och konfigurera en speciell regel för att tillåta utgående anslutning till IP-adresserna. Mer information finns i [Azure Key Vault bakom en brand vägg](../../key-vault/general/access-behind-firewall.md).
  - Om TLS 1,0 uttryckligen är inaktiverat i Windows och .NET-versionen inte har uppdaterats till 4,6 eller högre, så aktiverar följande register ändring Azure Disk Encryption för att välja den senaste TLS-versionen:

  ```config-registry
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001
    
  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001` 
  ```

### <a name="group-policy"></a>Grupprincip
 - Azure Disk Encryption-lösningen använder BitLockers externa nyckel skydd för virtuella Windows IaaS-datorer. För domänanslutna virtuella datorer ska du inte skicka några grup principer som tillämpar TPM-skydd. Information om grupprincip för alternativet **Tillåt BitLocker utan en kompatibel TPM** finns i [referens för BitLocker-Grupprincip](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- BitLocker-principen på domänanslutna virtuella datorer med en anpassad grupprincip måste innehålla följande inställning: [Konfigurera användar lagring av BitLocker-återställningsinformation – > tillåt 256-bitars återställnings nyckel](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption Miss lyckas när anpassade grupprincip inställningar för BitLocker inte är kompatibla. På datorer som inte har rätt princip inställning tillämpar du den nya principen, tvingar den nya principen att uppdatera (gpupdate.exe/Force) och startar om den om det behövs. 

## <a name="encryption-key-storage-requirements"></a>Lagrings krav för krypterings nyckel 

Azure Disk Encryption kräver Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter. Ditt nyckel valv och virtuella datorer måste finnas i samma Azure-region och prenumeration.

Mer information finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Nästa steg

- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md)
- [Aktivera Azure Disk Encryption med Azure AD på virtuella Linux-datorer (tidigare version)](disk-encryption-linux-aad.md)
- [Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started)
- [PowerShell-skript för Azure Disk Encryption krav](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
