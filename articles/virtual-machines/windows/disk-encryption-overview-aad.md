---
title: Azure Disk Encryption med Azure AD (tidigare version)
description: Den här artikeln innehåller förutsättningar för att använda Microsoft Azure disk kryptering för virtuella IaaS-datorer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8db9aad279c151073f8c674854d139276a84b064
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555388"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption med Azure AD (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure AD-programparameter för att aktivera disk kryptering för virtuella datorer. Med den nya versionen behöver du inte längre ange autentiseringsuppgifter för Azure AD under steget aktivera kryptering. Alla nya virtuella datorer måste krypteras utan Azure AD-programmets parametrar med den nya versionen. Om du vill visa instruktioner för att aktivera disk kryptering för virtuella datorer med den nya versionen, se [Azure Disk Encryption för virtuella Windows-datorer](disk-encryption-overview.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att behållas med AAD-syntaxen.**

Den här artikeln kompletterar [Azure Disk Encryption för virtuella Windows-datorer](disk-encryption-overview.md) med ytterligare krav och krav för Azure Disk Encryption med Azure AD (tidigare version). Avsnittet [virtuella datorer och operativ system som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems) förblir desamma.

## <a name="networking-and-group-policy"></a>Nätverks-och grupprincip

**Om du vill aktivera Azure Disk Encryption funktionen med hjälp av den äldre AAD-parameterns syntax måste de virtuella IaaS-datorerna uppfylla följande konfigurations krav för nätverks slut punkt:** 
  - Om du vill hämta en token för att ansluta till ditt nyckel valv måste den virtuella datorn IaaS kunna ansluta till en Azure Active Directory-slutpunkt, \[ login.microsoftonline.com \] .
  - Om du vill skriva krypterings nycklarna till ditt nyckel valv måste den virtuella datorn IaaS kunna ansluta till Key Vault-slutpunkten.
  - Den virtuella datorn IaaS måste kunna ansluta till en Azure Storage-slutpunkt som är värd för Azure Extension-lagringsplatsen och ett Azure Storage-konto som är värd för VHD-filerna.
  -  Om säkerhets principen begränsar åtkomsten från virtuella Azure-datorer till Internet kan du matcha föregående URI och konfigurera en speciell regel för att tillåta utgående anslutning till IP-adresserna. Mer information finns i [Azure Key Vault bakom en brand vägg](../../key-vault/general/access-behind-firewall.md).
  - Den virtuella dator som ska krypteras måste konfigureras att använda TLS 1,2 som standard protokoll. Om TLS 1,0 har inaktiverats explicit och .NET-versionen inte har uppdaterats till 4,6 eller högre, kommer följande register ändring att aktivera ADE för att välja den senaste TLS-versionen:

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**grupprincip:**
 - Azure Disk Encryption-lösningen använder BitLockers externa nyckel skydd för virtuella Windows IaaS-datorer. För domänanslutna virtuella datorer ska du inte skicka några grup principer som tillämpar TPM-skydd. Information om grup principen för "Tillåt BitLocker utan en kompatibel TPM" finns i [BitLocker Grupprincip Reference](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  BitLocker-principen på domänanslutna virtuella datorer med anpassad grup princip måste innehålla följande inställning: [Konfigurera användar lagring av BitLocker-återställningsinformation – > tillåt 256-bitars återställnings nyckel](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption Miss fungerar när anpassade grup princip inställningar för BitLocker inte är kompatibla. Tillämpa den nya principen på datorer som inte har rätt princip inställning, tvinga den nya principen att uppdatera (gpupdate.exe/Force) och starta sedan om.  

## <a name="encryption-key-storage-requirements"></a>Lagrings krav för krypterings nyckel  

Azure Disk Encryption kräver ett Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter. Ditt nyckel valv och virtuella datorer måste finnas i samma Azure-region och prenumeration.

Mer information finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Nästa steg

- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md)
- [Aktivera Azure Disk Encryption med Azure AD på virtuella Windows-datorer (tidigare version)](disk-encryption-windows-aad.md)
- [Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started)
- [PowerShell-skript för Azure Disk Encryption krav](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
