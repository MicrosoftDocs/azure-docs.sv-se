---
title: Kryptering på serversidan av Azure-hanterade diskar
description: Azure Storage dina data genom att kryptera dem i vila innan de bevaras i lagringskluster. Du kan använda kundhanterade nycklar för att hantera kryptering med dina egna nycklar, eller så kan du förlita dig på Microsoft-hanterade nycklar för kryptering av dina hanterade diskar.
author: roygara
ms.date: 04/15/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4607778c78b8b062b265a5754337c09c41ba83f1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531523"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Kryptering på serversidan av Azure-disklagring

De flesta Azure-hanterade diskar krypteras med Azure Storage-kryptering, som använder kryptering på serversidan (SSE) för att skydda dina data och för att hjälpa dig att uppfylla organisationens säkerhets- och efterlevnadsåtaganden. Azure Storage krypterar automatiskt data som lagras på Azure-hanterade diskar (operativsystem och datadiskar) i vila när de sparas i molnet. Diskar med kryptering på värddatorn aktiverad krypteras dock inte via Azure Storage. För diskar med kryptering på värden aktiverad tillhandahåller servern som är värd för den virtuella datorn krypteringen för dina data och som krypterade data flödar till Azure Storage.

Data i Azure-hanterade diskar krypteras transparent med 256-bitars [AES-kryptering,](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)ett av de starkaste blockchiffreringarna som är tillgängliga och är FIPS 140-2-kompatibel. Mer information om de kryptografiska moduler som underliggande Azure-hanterade diskar finns i [Kryptografi-API: Nästa generation](/windows/desktop/seccng/cng-portal)

Azure Storage kryptering påverkar inte prestanda för hanterade diskar och det finns ingen extra kostnad. Mer information om hur Azure Storage kryptering finns i [Azure Storage kryptering.](/azure/storage/common/storage-service-encryption)

> [!NOTE]
> Temporära diskar hanteras inte och krypteras inte av SSE, såvida du inte aktiverar kryptering på värden.

## <a name="about-encryption-key-management"></a>Om hantering av krypteringsnycklar

Du kan förlita dig på plattformshanterade nycklar för kryptering av din hanterade disk eller hantera kryptering med dina egna nycklar. Om du väljer att hantera kryptering med dina  egna nycklar kan du ange en kundhanterad nyckel som ska användas för kryptering och dekryptering av alla data i hanterade diskar. 

I följande avsnitt beskrivs vart och ett av alternativen för nyckelhantering i detalj.

### <a name="platform-managed-keys"></a>Plattforms hanterade nycklar

Som standard använder hanterade diskar plattform hanterade krypteringsnycklar. Alla hanterade diskar, ögonblicksbilder, avbildningar och data som skrivs till befintliga hanterade diskar krypteras automatiskt i vila med plattform hanterade nycklar.

### <a name="customer-managed-keys"></a>Kundhanterade nycklar

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Begränsningar

För tillfället har kund hanterade nycklar följande begränsningar:

- Om den här funktionen är aktiverad för disken kan du inte inaktivera den.
    Om du behöver komma runt detta måste du kopiera alla data med antingen [Azure PowerShell-modulen](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) eller [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)till en helt annan hanterad disk som inte använder kund hanterade nycklar.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Regioner som stöds

Kund hanterade nycklar är tillgängliga i alla regioner som hanterade diskar är tillgängliga.

Automatisk nyckelrotation är en förhandsversion och är endast tillgänglig i följande regioner:

- East US
- USA, östra 2
- USA, södra centrala
- USA, västra
- USA, västra 2
- Europa, norra
- Europa, västra
- Frankrike, centrala

> [!IMPORTANT]
> Kund hanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar kund hanterade nycklar tilldelas en hanterad identitet automatiskt till dina resurser under omslaget. Om du senare flyttar prenumerationen, resursgruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identiteten som är associerad med hanterade diskar till den nya klientorganisationen, så kund-hanterade nycklar kanske inte längre fungerar. Mer information finns i [Överföra en prenumeration mellan Azure AD-kataloger.](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

Om du vill aktivera kund hanterade nycklar för hanterade diskar kan du läsa våra artiklar om hur du aktiverar det med antingen [Azure PowerShell-modulen](windows/disks-enable-customer-managed-keys-powershell.md), [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) [eller Azure Portal](disks-enable-customer-managed-keys-portal.md). Information om hur du aktiverar kund hanterade nycklar med automatisk nyckelrotation finns i Konfigurera en Azure Key Vault och [DiskEncryptionSet med automatisk nyckelrotation (förhandsversion).](windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview)

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Kryptering på värd – kryptering från slutpunkt till slutpunkt för dina VM-data

När du aktiverar kryptering på värden startar krypteringen på själva vm-värden, den Azure-server som den virtuella datorn har allokerats till. Data för den tillfälliga disken och OS/datadiskcacheminnen lagras på den virtuella datorvärden. När kryptering har möjliggörs på värden krypteras alla dessa data i vila och flöden krypteras till lagringstjänsten, där de bevaras. Kryptering på värden krypterar i princip dina data från slutpunkt till slutpunkt. Kryptering på värden använder inte den virtuella datorns PROCESSOR och påverkar inte den virtuella datorns prestanda. 

Temporära diskar och tillfälliga OS-diskar krypteras i vila med plattformsbaserade nycklar när du aktiverar kryptering från slutpunkt till slutpunkt. Operativsystemet och datadiskcacheminnen krypteras i vila med antingen kund-hanterade eller plattformsbaserade nycklar, beroende på den valda diskkrypteringstypen. Om en disk till exempel krypteras med kund hanterade nycklar krypteras cacheminnet för disken med kund hanterade nycklar, och om en disk krypteras med plattform hanterade nycklar krypteras cacheminnet för disken med plattform hanterade nycklar.

### <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-vm-sizes"></a>VM-storlekar som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Du kan också hitta VM-storlekarna programmatiskt. Information om hur du hämtar dem programmatiskt finns i avsnittet Hitta VM-storlekar som stöds i antingen [Azure PowerShell modulen](windows/disks-enable-host-based-encryption-powershell.md#finding-supported-vm-sizes) eller [Azure CLI-artiklarna.](linux/disks-enable-host-based-encryption-cli.md#finding-supported-vm-sizes)

Om du vill aktivera kryptering från slutpunkt till slutpunkt med kryptering på värden kan du läsa våra artiklar om hur du aktiverar det med antingen [Azure PowerShell-modulen](windows/disks-enable-host-based-encryption-powershell.md), [Azure CLI](linux/disks-enable-host-based-encryption-cli.md) [eller Azure Portal](disks-enable-host-based-encryption-portal.md).

## <a name="double-encryption-at-rest"></a>Dubbel kryptering i vila

Kunder med hög säkerhetskänsliga problem som är kopplade till en viss krypteringsalgoritm, implementering eller nyckel som komprometteras kan nu välja ytterligare ett lager med kryptering med hjälp av en annan krypteringsalgoritm/-läge på infrastrukturnivå med plattforms hanterade krypteringsnycklar. Det här nya lagret kan tillämpas på bestående operativsystem och datadiskar, ögonblicksbilder och avbildningar. Alla krypteras i vila med dubbel kryptering.

### <a name="supported-regions"></a>Regioner som stöds

Dubbel kryptering är tillgängligt i alla regioner som hanterade diskar är tillgängliga.

Om du vill aktivera dubbel kryptering i vila för hanterade diskar kan du läsa våra artiklar om hur du aktiverar det med [antingen Azure PowerShell-modulen](windows/disks-enable-double-encryption-at-rest-powershell.md), [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) eller [Azure Portal](disks-enable-double-encryption-at-rest-portal.md).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kryptering på serversidan jämfört med Azure-diskkryptering

[Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) använder antingen [DM-Crypt-funktionen](https://en.wikipedia.org/wiki/Dm-crypt) i Linux eller [BitLocker-funktionen](/windows/security/information-protection/bitlocker/bitlocker-overview) i Windows för att kryptera hanterade diskar med kund hanterade nycklar på den virtuella gästdatorn.  Kryptering på serversidan med kund hanterade nycklar förbättrar ADE genom att göra det möjligt för dig att använda alla typer av operativsystem och avbildningar för dina virtuella datorer genom att kryptera data i lagringstjänsten.
> [!IMPORTANT]
> Kund hanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar kund hanterade nycklar tilldelas en hanterad identitet automatiskt till dina resurser under omslaget. Om du senare flyttar prenumerationen, resursgruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identiteten som är associerad med hanterade diskar till den nya klientorganisationen, så kund-hanterade nycklar kanske inte längre fungerar. Mer information finns i [Överföra en prenumeration mellan Azure AD-kataloger.](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

## <a name="next-steps"></a>Nästa steg

- Aktivera kryptering från slutpunkt till slutpunkt med kryptering på värden med [antingen Azure PowerShell modulen](windows/disks-enable-host-based-encryption-powershell.md), [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)eller [Azure Portal](disks-enable-host-based-encryption-portal.md).
- Aktivera dubbel kryptering i vila för hanterade diskar med [antingen Azure PowerShell modulen](windows/disks-enable-double-encryption-at-rest-powershell.md), [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) eller [Azure Portal](disks-enable-double-encryption-at-rest-portal.md).
- Aktivera kund hanterade nycklar för hanterade diskar med [antingen Azure PowerShell modulen](windows/disks-enable-customer-managed-keys-powershell.md), [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) eller [Azure Portal](disks-enable-customer-managed-keys-portal.md).
- [Utforska de Azure Resource Manager mallarna för att skapa krypterade diskar med kund hanterade nycklar](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Vad är Azure Key Vault?](../key-vault/general/overview.md)
