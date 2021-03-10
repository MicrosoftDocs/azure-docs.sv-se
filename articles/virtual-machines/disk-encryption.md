---
title: Kryptering på Server sidan av Azure Managed disks
description: Azure Storage skyddar dina data genom att kryptera dem i vila innan du sparar dem i lagrings kluster. Du kan använda Kundhanterade nycklar för att hantera kryptering med dina egna nycklar, eller så kan du lita på Microsoft-hanterade nycklar för kryptering av dina hanterade diskar.
author: roygara
ms.date: 03/02/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 5695b21a09199a46ab38d887f2127a61507426d3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562800"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Kryptering på Server sidan av Azure-disklagring

Server Side Encryption (SSE) skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. SSE krypterar automatiskt dina data som lagras på Azure Managed disks (OS-och data diskar) i vila som standard när de sparas i molnet. 

Data i Azure Managed disks krypteras transparent med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Mer information om de kryptografiska modulerna underliggande Azure Managed disks finns i [Cryptography-API: nästa generation](/windows/desktop/seccng/cng-portal)

Kryptering på Server sidan påverkar inte prestanda för hanterade diskar och det finns ingen ytterligare kostnad. 

> [!NOTE]
> Temporära diskar är inte hanterade diskar och krypteras inte av SSE, om du inte aktiverar kryptering på värden.

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Du kan förlita dig på plattforms hanterade nycklar för kryptering av din hanterade disk, eller så kan du hantera kryptering med hjälp av dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar kan du ange en *kundhanterad nyckel* som ska användas för att kryptera och dekryptera alla data i Managed disks. 

I följande avsnitt beskrivs de olika alternativen för nyckel hantering i större detalj.

### <a name="platform-managed-keys"></a>Plattforms hanterade nycklar

Som standard använder hanterade diskar plattforms hanterade krypterings nycklar. Alla hanterade diskar, ögonblicks bilder, avbildningar och data som skrivs till befintliga hanterade diskar krypteras automatiskt i vila med plattforms hanterade nycklar.

### <a name="customer-managed-keys"></a>Kundhanterade nycklar

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Begränsningar

För närvarande har Kundhanterade nycklar följande begränsningar:

- Om den här funktionen är aktive rad för disken kan du inte inaktivera den.
    Om du behöver kringgå detta måste du kopiera alla data med hjälp av [Azure PowerShell-modulen](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) eller [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)till en helt annan hanterad disk som inte använder Kundhanterade nycklar.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Regioner som stöds

Kundhanterade nycklar är tillgängliga i alla regioner som Managed disks är tillgängliga.

Automatisk nyckel rotation är i för hands version och endast tillgängligt i följande regioner:

- East US
- USA, östra 2
- USA, södra centrala
- USA, västra
- USA, västra 2
- Europa, norra
- Europa, västra
- Frankrike, centrala

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar Kundhanterade nycklar, tilldelas en hanterad identitet automatiskt till dina resurser under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till hanterade diskar till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i [överföra en prenumeration mellan Azure AD-kataloger](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

Om du vill aktivera Kundhanterade nycklar för hanterade diskar kan du läsa våra artiklar som beskriver hur du aktiverar den med antingen [Azure PowerShell-modulen](windows/disks-enable-customer-managed-keys-powershell.md), [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) eller [Azure Portal](disks-enable-customer-managed-keys-portal.md). Information om hur du aktiverar Kundhanterade nycklar med automatisk nyckel rotation finns i [Konfigurera ett Azure Key Vault och DiskEncryptionSet med automatisk nyckel rotation (för hands version)](windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview).

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Kryptering vid värd-slutpunkt-till-slutpunkt-kryptering för dina VM-data

När du aktiverar kryptering på värden startar krypteringen på själva VM-värden, den Azure-server som den virtuella datorn är tilldelad till. Data för dina temporära diskar och OS/datadisk-cachen lagras på den virtuella dator värden. När du har aktiverat kryptering på värden krypteras alla dessa data i vila och flöden krypteras till lagrings tjänsten, där de är bestående. Kryptering på värden krypterar i princip dina data från slut punkt till slut punkt. Kryptering på värden använder inte den virtuella datorns CPU och påverkar inte den virtuella datorns prestanda. 

Temporära diskar och tillfälliga OS-diskar är krypterade i vila med plattforms hanterade nycklar när du aktiverar kryptering från slut punkt till slut punkt. Cacheminnen för operativ system och datadisk krypteras i vila med antingen Kundhanterade eller plattforms hanterade nycklar, beroende på vilken disk krypterings typ som valts. Om en disk till exempel krypteras med Kundhanterade nycklar, krypteras cachen för disken med Kundhanterade nycklar, och om en disk krypteras med plattforms hanterade nycklar krypteras cachen för disken med plattforms hanterade nycklar.

### <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Regioner som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>VM-storlekar som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Om du vill aktivera kryptering från slut punkt till slut punkt med kryptering på värden, Se våra artiklar som beskriver hur du aktiverar det med antingen [Azure PowerShell-modulen](windows/disks-enable-host-based-encryption-powershell.md), [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)eller [Azure Portal](disks-enable-host-based-encryption-portal.md).

## <a name="double-encryption-at-rest"></a>Dubbel kryptering i vila

Hög säkerhets känsliga kunder som är intresserade av den risk som är kopplad till en viss krypteringsalgoritm, implementering eller nyckel som komprometteras kan nu välja ytterligare lager av kryptering med hjälp av en annan krypteringsalgoritm/läge på infrastruktur lagret med hjälp av plattforms hanterade krypterings nycklar. Det nya lagret kan tillämpas på beständiga operativ system och data diskar, ögonblicks bilder och avbildningar, som är krypterade i vila med dubbel kryptering.

### <a name="supported-regions"></a>Regioner som stöds

Double Encryption är tillgängligt i alla regioner som hanterade diskar är tillgängliga.

Om du vill aktivera dubbel kryptering i vila för hanterade diskar kan du läsa våra artiklar som beskriver hur du aktiverar det med antingen [Azure PowerShell-modulen](windows/disks-enable-double-encryption-at-rest-powershell.md), [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) eller [Azure Portal](disks-enable-double-encryption-at-rest-portal.md).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kryptering på Server sidan jämfört med Azure Disk Encryption

[Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) använder antingen funktionen [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux eller [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) -funktionen i Windows för att kryptera hanterade diskar med Kundhanterade nycklar i den virtuella gäst datorn.  Kryptering på Server sidan med Kundhanterade nycklar förbättrar på ADE genom att du kan använda alla OS-typer och avbildningar för dina virtuella datorer genom att kryptera data i lagrings tjänsten.
> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar Kundhanterade nycklar, tilldelas en hanterad identitet automatiskt till dina resurser under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till hanterade diskar till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i [överföra en prenumeration mellan Azure AD-kataloger](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Nästa steg

- Aktivera kryptering från slut punkt till slut punkt med hjälp av kryptering på värd med antingen [Azure PowerShell-modulen](windows/disks-enable-host-based-encryption-powershell.md), [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)eller [Azure Portal](disks-enable-host-based-encryption-portal.md).
- Aktivera dubbel kryptering i vila för hanterade diskar med antingen [Azure PowerShell-modulen](windows/disks-enable-double-encryption-at-rest-powershell.md), [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) eller [Azure Portal](disks-enable-double-encryption-at-rest-portal.md).
- Aktivera Kundhanterade nycklar för hanterade diskar med antingen [Azure PowerShell-modulen](windows/disks-enable-customer-managed-keys-powershell.md), [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) eller [Azure Portal](disks-enable-customer-managed-keys-portal.md).
- [Utforska Azure Resource Manager mallar för att skapa krypterade diskar med Kundhanterade nycklar](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Vad är Azure Key Vault?](../key-vault/general/overview.md)