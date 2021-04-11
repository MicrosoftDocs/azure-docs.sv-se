---
title: Hantera app-grupper för Windows Virtual Desktop PowerShell – Azure
description: Hantera Windows-appar för virtuella skriv bord med PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 1652048da8084007bc86a6793f14f10b4a1f2e0f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446051"
---
# <a name="manage-app-groups-using-powershell"></a>Hantera program grupper med PowerShell

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

Standard-app-gruppen som skapas för en ny Windows-pool för virtuella skriv bord publicerar också hela Skriv bordet. Dessutom kan du skapa en eller flera RemoteApp-programgrupper för poolen. Följ den här självstudien för att skapa en RemoteApp-app-grupp och publicera enskilda **Start** meny-appar.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en RemoteApp-grupp.
> * Bevilja åtkomst till RemoteApp-program.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har följt anvisningarna i [Konfigurera PowerShell-modulen](powershell-module.md) för att konfigurera din PowerShell-modul och logga in på ditt Azure-konto.

## <a name="create-a-remoteapp-group"></a>Skapa en RemoteApp-grupp

Så här skapar du en RemoteApp-grupp med PowerShell:

1. Kör följande PowerShell-cmdlet för att skapa en ny tom RemoteApp-app.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. Valfritt För att kontrol lera att app-gruppen har skapats kan du köra följande cmdlet för att se en lista över alla app-grupper för poolen.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Kör följande cmdlet för att hämta en lista över **Start** -menyns appar på värddatorns avbildning av den virtuella datorn. Skriv ned värdena för **sökväg**, **IconPath**, **IconIndex** och annan viktig information för det program som du vill publicera.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   Utdata ska visa alla Start meny alternativ i ett format som detta:

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. Kör följande cmdlet för att installera programmet baserat på `AppAlias` . `AppAlias` blir synlig när du kör utdata från steg 3.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. Valfritt Kör följande cmdlet för att publicera ett nytt RemoteApp-program till den program grupp som skapades i steg 1.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. Verifiera att appen har publicerats genom att köra följande cmdlet.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Upprepa steg 1 – 5 för varje program som du vill publicera för den här program gruppen.
8. Kör följande cmdlet för att ge användarna åtkomst till RemoteApp-programmen i app-gruppen.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>Nästa steg

Om du kommer till den här instruktions guiden från våra självstudier kan du ta [en titt på skapa en adresspool för att verifiera uppdateringar av tjänsten](create-validation-host-pool.md). Du kan använda en pool för validerings värdar för att övervaka tjänst uppdateringar innan du återställer dem till din produktions miljö.