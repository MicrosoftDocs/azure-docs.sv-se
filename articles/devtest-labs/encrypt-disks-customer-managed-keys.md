---
title: Kryptera OS-diskar med Kundhanterade nycklar i Azure DevTest Labs
description: Lär dig hur du krypterar operativ system diskar (OS) med Kundhanterade nycklar i Azure DevTest Labs.
ms.topic: article
ms.date: 07/28/2020
ms.openlocfilehash: 209ab1f74dce0982af66777f211c41066d53b8f9
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566207"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Kryptera operativ system diskar (OS) med Kundhanterade nycklar i Azure DevTest Labs
Server Side Encryption (SSE) skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. SSE krypterar automatiskt dina data som lagras på hanterade diskar i Azure (OS-och data diskar) i vila som standard när de sparas i molnet. Lär dig mer om [disk kryptering](../virtual-machines/windows/disk-encryption.md) i Azure. 

I DevTest Labs krypteras alla OS-diskar och data diskar som skapats som en del av ett labb med hjälp av plattforms hanterade nycklar. Men som en labb ägare kan du välja att kryptera virtuella dator diskar för labbet med dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar kan du ange en **kundhanterad nyckel** som ska användas för kryptering av data i labb OS-diskar. Mer information om kryptering på Server sidan med Kundhanterade nycklar och andra typer av hanterade diskar finns i [Kundhanterade nycklar](../virtual-machines/windows/disk-encryption.md#customer-managed-keys). Se även [begränsningar med att använda Kundhanterade nycklar](../virtual-machines/windows/disks-enable-customer-managed-keys-portal.md#restrictions).


> [!NOTE]
> - För närvarande stöds inte disk kryptering med en kundhanterad nyckel för OS-diskar i DevTest Labs. 
> 
> - Inställningen gäller för nyligen skapade OS-diskar i labbet. Om du väljer att ändra disk krypterings uppsättningen vid någon tidpunkt fortsätter äldre diskar i labbet att förbli krypterade med den tidigare disk krypterings uppsättningen. 

I följande avsnitt visas hur en labb ägare kan konfigurera kryptering med hjälp av en kundhanterad nyckel.

## <a name="pre-requisites"></a>Förutsättningar

1. Om du inte har en disk krypterings uppsättning följer du den här artikeln för att [Konfigurera en Key Vault och en disk krypterings uppsättning](../virtual-machines/windows/disks-enable-customer-managed-keys-portal.md#set-up-your-azure-key-vault). Observera följande krav för disk krypterings uppsättningen: 

    - Disk krypterings uppsättningen måste finnas **i samma region och prenumeration som din labb miljö**. 
    - Se till att du (labb ägare) har minst en **åtkomst på läsar nivå** till den disk krypterings uppsättning som ska användas för att kryptera labb OS-diskar. 
2. För labb som skapats före 8/1/2020 måste labb ägaren se till att identiteten för labb systemets tilldelade identitet är aktive rad. För att göra det kan labb ägaren gå till sitt labb, klicka på **konfiguration och principer**, klicka på **(för hands version)** blad, ändra systemtilldelad identitets **status** till **på** och klicka på **Spara**. För nya labb som skapats efter 8/1/2020-Labbets tilldelade identitet, aktive ras som standard. 
3. För att labbet ska kunna hantera kryptering för alla labb-OS-diskar måste labb ägaren uttryckligen ge labb rollen **system tilldelad identitets** läsare på disk krypterings uppsättningen och rollen virtuell dator deltagare på den underliggande Azure-prenumerationen. Labb ägaren kan göra detta genom att utföra följande steg:

   
    1. Se till att du är medlem i [rollen administratör för användar åtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator) på Azures prenumerations nivå så att du kan hantera användar åtkomst till Azure-resurser. 
    1. På sidan **disk krypterings uppsättning** väljer du **åtkomst kontroll (IAM)** på den vänstra menyn. 
    1. Välj **+ Lägg till** i verktygsfältet och välj **Lägg till en roll tilldelning**.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="Lägg till roll hantering-menyn":::
    1. På sidan **Lägg till roll tilldelning** väljer du rollen **läsare** eller en roll som tillåter mer åtkomst. 
    1. Ange namnet på labbet som disk krypterings uppsättningen ska användas för och välj labb namnet (tilldelad identitet för labbet) i list rutan. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="Välj Systemhanterad identitet för labbet":::        
    1. Välj **Spara** i verktygsfältet. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="Spara roll tilldelning":::
3. Lägg till Labbets **systemtilldelade identitet** i rollen **virtuell dator deltagare** med hjälp av sidan för **prenumerations**  ->  **åtkomst kontroll (IAM)** . Stegen liknar dem i föregående steg. 

    
    1. Gå till **prenumerations** sidan i Azure Portal. 
    1. Välj **Åtkomstkontroll (IAM)** . 
    1. Välj **+ Lägg till** i verktygsfältet och välj **Lägg till en roll tilldelning**. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="Prenumerations > åtkomst kontroll (IAM) Sidan":::
    1. På sidan **Lägg till roll tilldelning** väljer du **virtuell dator deltagare** för rollen.
    1. Skriv labb namnet och välj **labb namnet** (tilldelad identitet för labbet) i list rutan. 
    1. Välj **Spara** i verktygsfältet. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>Kryptera labb operativ system diskar med en kundhanterad nyckel 

1. På Start sidan för ditt labb i Azure Portal väljer du **konfiguration och principer** på den vänstra menyn. 
1. På sidan **konfiguration och principer** väljer du **diskar (förhands granskning)** i avsnittet **kryptering** . **Krypterings typen** är som standard inställd på **kryptering i vila med en plattforms hanterad nyckel**.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="Fliken diskar på sidan konfiguration och principer":::
1. För **krypterings typ**väljer du **kryptering i vila med en kundhanterad nyckel** i list rutan. 
1. För **disk krypterings uppsättning**väljer du den disk krypterings uppsättning som du skapade tidigare. Det är samma disk krypterings uppsättning som den systemtilldelade identiteten för labbet kan komma åt.
1. Välj **Spara** i verktygsfältet. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="Aktivera kryptering med kundhanterad nyckel":::
1. I meddelande rutan med följande text: *den här inställningen gäller för nyligen skapade datorer i labbet. Den gamla OS-disken fortsätter att vara krypterad med den gamla disk krypterings uppsättningen*. Välj **OK**. 

    När den har kon figurer ATS krypteras Lab OS-diskar med den Kundhanterade nyckeln som anges med disk krypterings uppsättningen. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Azure Disk Encryption](../virtual-machines/windows/disk-encryption.md). 
- [Kundhanterade nycklar](../virtual-machines/windows/disk-encryption.md#customer-managed-keys) 
