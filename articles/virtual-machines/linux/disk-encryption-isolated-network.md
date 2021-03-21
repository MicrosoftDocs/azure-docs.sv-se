---
title: Azure Disk Encryption i ett isolerat nätverk
description: I den här artikeln får du veta mer om fel söknings tips för Microsoft Azure disk kryptering på virtuella Linux-datorer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 8d8d2b88251f837a23c4e82a90eb4d4eb0043702
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553059"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Azure Disk Encryption i ett isolerat nätverk

När anslutningen begränsas av en brand vägg, ett krav för en brand vägg eller en nätverks säkerhets grupp (NSG), kan det hända att tilläggets möjlighet att utföra nödvändiga åtgärder avbryts. Detta avbrott kan resultera i status meddelanden som "tilläggs status är inte tillgänglig på den virtuella datorn".

## <a name="package-management"></a>Pakethantering

Azure Disk Encryption är beroende av ett antal komponenter, som vanligt vis installeras som en del av ADE-aktiveringen om de inte redan finns. När du bakom en brand vägg eller på annat sätt är isolerad från Internet måste dessa paket vara förinstallerade eller tillgängliga lokalt.

Här är de paket som krävs för varje distribution. En fullständig lista över distributioner och volym typer som stöds finns i [virtuella datorer och operativ system som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14,04, 16,04, 18,04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-sex, procps, grub-PC-bin
- **CentOS 7,2-7,7**: lsscsi, psmisc, lvm2, UUID, at, patch, cryptsetup, cryptsetup-recrypting, pyparted, procps-ng, util-linux
- **CentOS 6,8**: lsscsi, psmisc, lvm2, UUID, at, cryptsetup-Encrypting, pyparted, python-sex
- **RedHat 7,2-7,7**: lsscsi, psmisc, lvm2, UUID, at, patch, cryptsetup, cryptsetup-Encrypting, procps-ng, util-linux
- **RedHat 6,8**: lsscsi, psmisc, lvm2, UUID, at, patch, cryptsetup-Encrypting
- **openSUSE 42,3, SLES 12 – SP4, 12-SP3**: lsscsi, cryptsetup

I Red Hat måste du, när en proxyserver krävs, se till att prenumerations-och yum har kon figurer ATS korrekt. Mer information finns i [så här felsöker du problem med prenumerations hanteraren och yum](https://access.redhat.com/solutions/189533).  

När paket installeras manuellt måste de också uppgraderas manuellt när nya versioner släpps.

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
Alla inställningar för nätverks säkerhets grupper som tillämpas måste fortfarande tillåta slut punkten att uppfylla de dokumenterade nätverks konfigurations kraven för disk kryptering.  Se [Azure Disk Encryption: nätverks krav](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption med Azure AD (tidigare version)

Om du använder [Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-overview-aad.md)måste [Azure Active Directorys biblioteket](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) installeras manuellt för alla distributioner (utöver de paket som är lämpliga för distribution, enligt [listan ovan](#package-management)).

När kryptering aktive ras med [autentiseringsuppgifter för Azure AD](disk-encryption-linux-aad.md)måste den virtuella mål datorn tillåta anslutning till både Azure Active Directory slut punkter och Key Vault slut punkter. Nuvarande Azure Active Directory autentiserings slut punkter underhålls i avsnitten 56 och 59 i dokumentationen för [Microsoft 365 URL: er och IP-adressintervall](/microsoft-365/enterprise/urls-and-ip-address-ranges) . Key Vault-instruktioner finns i dokumentationen om hur du [får åtkomst till Azure Key Vault bakom en brand vägg](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 

Den virtuella datorn måste kunna komma åt [Azure instance metadata service](instance-metadata-service.md) -slutpunkten, som använder en välkänd icke-flyttbar IP-adress ( `169.254.169.254` ) som bara kan nås från den virtuella datorn.  Proxykonfigurationen som ändrar lokal HTTP-trafik till den här adressen (till exempel att lägga till ett X-vidarebefordrat-för-huvud) stöds inte.

## <a name="next-steps"></a>Nästa steg

- Se fler steg för [fel sökning av Azure Disk Encryption](disk-encryption-troubleshooting.md)
- [Azure-datakryptering i vila](../../security/fundamentals/encryption-atrest.md)
