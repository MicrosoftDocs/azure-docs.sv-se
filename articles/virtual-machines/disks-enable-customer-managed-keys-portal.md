---
title: Azure Portal – aktivera Kundhanterade nycklar med SSE-hanterade diskar
description: Aktivera Kundhanterade nycklar på dina hanterade diskar via Azure Portal.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5452b1e2ffc3ca8a11f3acb591c6288806445e1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499398"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Använd Azure Portal för att aktivera kryptering på Server sidan med Kundhanterade nycklar för hanterade diskar

Med Azure-disklagring kan du hantera dina egna nycklar när du använder SSE (Server Side Encryption) för hanterade diskar, om du väljer. För konceptuell information om SSE med Kundhanterade nycklar, samt andra typer av hanterade disk krypterings typer, se avsnittet **Kundhanterade nycklar** i artikeln om disk kryptering:

- För Linux: [Kundhanterade nycklar](./disk-encryption.md#customer-managed-keys)
- För Windows: [Kundhanterade nycklar](./disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>Begränsningar

För närvarande har Kundhanterade nycklar följande begränsningar:

- Om den här funktionen är aktive rad för disken kan du inte inaktivera den.
    Om du behöver kringgå detta måste du kopiera alla data till en helt annan hanterad disk som inte använder Kundhanterade nycklar:

    - För Linux: [Kopiera en hanterad disk](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - För Windows: [Kopiera en hanterad disk](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

- Endast [program varu-och HSM RSA-nycklar](../key-vault/keys/about-keys.md) med storlekar 2 048-bitar, 3 072-bitars och 4 096-bit stöds, inga andra nycklar eller storlekar.
    - [HSM](../key-vault/keys/hsm-protected-keys.md) -nycklar kräver **Premium** -nivån i Azure Key Vaults.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

Följande avsnitt beskriver hur du aktiverar och använder Kundhanterade nycklar för hanterade diskar:

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Distribuera en virtuell dator

Nu när du har skapat och konfigurerat nyckel valvet och disk krypterings uppsättningen kan du distribuera en virtuell dator med hjälp av krypteringen.
Processen för distribution av virtuella datorer liknar standard distributions processen, de enda skillnaderna är att du måste distribuera den virtuella datorn i samma region som dina andra resurser och du väljer att använda en kundhanterad nyckel.

1. Sök efter **Virtual Machines** och välj **+ Lägg** till för att skapa en virtuell dator.
1. På bladet **grundläggande** väljer du samma region som disk krypterings uppsättning och Azure Key Vault.
1. Fyll i de andra värdena på det **grundläggande** bladet som du vill.

    ![Skärm bild av upplevelsen för att skapa virtuella datorer med värdet region markerat.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. På bladet **diskar** väljer du **kryptering i vila med en kundhanterad nyckel**.
1. Välj disk krypterings uppsättning i list rutan **disk krypterings uppsättning** .
1. Gör de återstående valen som du vill.

    ![Skärm bild av bladet för att skapa virtuella datorer, bladet diskar. Med list rutan disk krypterings uppsättning markerat.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Aktivera på en befintlig disk

> [!CAUTION]
> Att aktivera disk kryptering på alla diskar som är anslutna till en virtuell dator kräver att du stoppar den virtuella datorn.
    
1. Navigera till en virtuell dator som finns i samma region som en av disk krypterings uppsättningarna.
1. Öppna den virtuella datorn och välj **stoppa**.

    ![Skärm bild av huvud överlägg för det virtuella dator exemplet med stopp-knappen markerad.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. När den virtuella datorn har stoppats väljer du **diskar** och väljer sedan den disk som du vill kryptera.

    ![Skärm bild av ditt exempel virtuella dator med bladet disk öppet. OS-disken är markerad som en exempel disk där du kan välja den.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Välj **kryptering** och välj **kryptering i vila med en kundhanterad nyckel** och välj sedan disk krypterings uppsättningen i list rutan.
1. Välj **Spara**.

    ![Skärm bild av exempel-OS-disken. Krypterings bladet är öppet, kryptering i vila med en kundhanterad nyckel har marker ATS, samt ditt exempel Azure Key Vault. När du har gjort dessa val markeras knappen Spara.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Upprepa processen för alla andra diskar som är anslutna till den virtuella dator som du vill kryptera.
1. När diskarna har växlat till Kundhanterade nycklar, och det inte finns några andra anslutna diskar som du vill kryptera, kan du starta den virtuella datorn.

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar Kundhanterade nycklar, tilldelas en hanterad identitet automatiskt till dina resurser under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till de hanterade diskarna till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i [överföra en prenumeration mellan Azure AD-kataloger](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Nästa steg

- [Utforska Azure Resource Manager mallar för att skapa krypterade diskar med Kundhanterade nycklar](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Vad är Azure Key Vault?](../key-vault/general/overview.md)
- [Replikera datorer med Kundhanterade nycklar aktiverade diskar](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Konfigurera haveriberedskap för virtuella VMware-datorer till Azure med PowerShell](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Konfigurera haveriberedskap till Azure för virtuella Hyper-V-datorer med PowerShell och Azure Resource Manager](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)