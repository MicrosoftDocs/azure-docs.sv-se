---
title: Hantera app-grupper för Windows Virtual Desktop (klassisk) – Azure
description: Lär dig hur du konfigurerar Windows Virtual Desktop (klassiska) klienter i Azure Active Directory (AD).
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e2a1f38918b2ea6af8a334b6648a463753f5c7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91295217"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-classic"></a>Självstudie: hantera program grupper för Windows Virtual Desktop (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../manage-app-groups.md).

Standard-app-gruppen som skapas för en ny Windows-pool för virtuella skriv bord publicerar också hela Skriv bordet. Dessutom kan du skapa en eller flera RemoteApp-programgrupper för poolen. Följ den här självstudien för att skapa en RemoteApp-app-grupp och publicera enskilda **Start** meny-appar.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en RemoteApp-grupp.
> * Bevilja åtkomst till RemoteApp-program.

Innan du börjar kan du [Hämta och importera Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som ska användas i PowerShell-sessionen om du inte redan gjort det. Sedan kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Skapa en RemoteApp-grupp

1. Kör följande PowerShell-cmdlet för att skapa en ny tom RemoteApp-app.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. Valfritt För att kontrol lera att app-gruppen har skapats kan du köra följande cmdlet för att se en lista över alla app-grupper för poolen.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Kör följande cmdlet för att hämta en lista över **Start** -menyns appar på värddatorns avbildning av den virtuella datorn. Skriv ned värdena för **sökväg**, **IconPath**, **IconIndex** och annan viktig information för det program som du vill publicera.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```

4. Kör följande cmdlet för att installera programmet baserat på `AppAlias` . `AppAlias` blir synlig när du kör utdata från steg 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. Valfritt Kör följande cmdlet för att publicera ett nytt RemoteApp-program till den program grupp som skapades i steg 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Verifiera att appen har publicerats genom att köra följande cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Upprepa steg 1 – 5 för varje program som du vill publicera för den här program gruppen.
8. Kör följande cmdlet för att ge användarna åtkomst till RemoteApp-programmen i app-gruppen.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en app-grupp, fyller den med RemoteApp-program och tilldelar användare till app-gruppen. Information om hur du skapar en pool för validerings värdar finns i följande självstudie. Du kan använda en pool för validerings värdar för att övervaka tjänst uppdateringar innan du återställer dem till din produktions miljö.

> [!div class="nextstepaction"]
> [Skapa en värdpool för att verifiera tjänstuppdateringar](create-validation-host-pool-2019.md)
