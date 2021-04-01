---
title: Ändra statisk grupp medlemskap till dynamisk – Azure AD | Microsoft Docs
description: Lär dig att konvertera befintliga grupper från statisk till dynamiskt medlemskap med hjälp av Azure AD Admin Center eller PowerShell-cmdletar.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a46cc3f4a0f2fb25fc693103a64a319dcec0324
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860889"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Ändra statisk grupp medlemskap till dynamisk i Azure Active Directory

Du kan ändra en grupps medlemskap från statiskt till dynamiskt (eller vice versa) i Azure Active Directory (Azure AD). Azure AD behåller samma grupp namn och ID i systemet, så alla befintliga referenser till gruppen är fortfarande giltiga. Om du skapar en ny grupp i stället måste du uppdatera dessa referenser. Dynamiskt grupp medlemskap eliminerar hanterings kostnader för att lägga till och ta bort användare. Den här artikeln beskriver hur du konverterar befintliga grupper från statisk till dynamiskt medlemskap med hjälp av antingen Azure AD Admin Center eller PowerShell-cmdletar.

> [!WARNING]
> När du ändrar en befintlig statisk grupp till en dynamisk grupp tas alla befintliga medlemmar bort från gruppen och medlemskaps regeln bearbetas för att lägga till nya medlemmar. Om gruppen används för att styra åtkomsten till appar eller resurser bör du tänka på att de ursprungliga medlemmarna kan förlora åtkomst tills medlemskaps regeln har bearbetats fullständigt.
>
> Vi rekommenderar att du testar den nya medlemskaps regeln i förväg för att se till att det nya medlemskapet i gruppen är som förväntat.

## <a name="change-the-membership-type-for-a-group"></a>Ändra medlemskaps typ för en grupp

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som är en global administratör, användar administratör eller grupp administratör i din Azure AD-organisation.
2. Välj **grupper**.
3. Öppna den grupp som du vill ändra i listan **alla grupper** .
4. Välj **Egenskaper**.
5. På sidan **Egenskaper** för gruppen väljer du en **medlemskaps typ** för antingen tilldelade (statisk), dynamisk användare eller dynamisk enhet, beroende på vilken medlemskaps typ du vill ha. För dynamiskt medlemskap kan du använda regel verktyget för att välja alternativ för en enkel regel eller skriva en medlemskaps regel själv. 

Följande steg är ett exempel på hur du ändrar en grupp från statisk till dynamiskt medlemskap för en grupp användare.

1. På sidan **Egenskaper** för den valda gruppen väljer du en **medlemskaps typ** för **dynamisk användare** och väljer sedan Ja i dialog rutan som förklarar ändringarna i grupp medlemskapet för att fortsätta. 
  
   ![Välj medlemskaps typ för dynamisk användare](./media/groups-change-type/select-group-to-convert.png)
  
2. Välj **Lägg till dynamisk fråga** och ange sedan regeln.
  
   ![Ange regeln för den dynamiska gruppen](./media/groups-change-type/enter-rule.png)
  
3. När du har skapat regeln väljer du **Lägg till fråga** längst ned på sidan.
4. Spara ändringarna genom att välja **Spara** på sidan **Egenskaper** för gruppen. Gruppens **medlemskaps typ** uppdateras omedelbart i grupp listan.

> [!TIP]
> Grupp konverteringen kan Miss inträffat om medlemskaps regeln du angav är felaktig. Ett meddelande visas i det övre högra hörnet av portalen att det innehåller en förklaring av varför regeln inte kan godkännas av systemet. Läs det noggrant och lär dig hur du kan justera regeln så att den blir giltig. Exempel på regel syntax och en fullständig lista över de egenskaper, operatorer och värden som stöds för en medlemskaps regel finns i [regler för dynamiska medlemskap för grupper i Azure Active Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Ändra medlemskaps typ för en grupp (PowerShell)

> [!NOTE]
> Om du vill ändra dynamiska grupp egenskaper måste du använda cmdletar från för **hands versionen av** [Azure AD PowerShell version 2](/powershell/azure/active-directory/install-adv2). Du kan installera förhands granskningen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureADPreview).

Här är ett exempel på funktioner som växlar medlemskaps hantering i en befintlig grupp. I det här exemplet är det viktigt att ändra egenskapen GroupTypes och bevara alla värden som inte är relaterade till dynamiskt medlemskap.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Så här gör du en grupp statisk:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Så här gör du en grupp dynamisk:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Nästa steg

De här artiklarna innehåller ytterligare information om grupper i Azure Active Directory.

* [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
