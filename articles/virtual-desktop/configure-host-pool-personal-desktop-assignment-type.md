---
title: Windows Virtual Desktop, personlig Skriv bords typ – Azure
description: Så här konfigurerar du automatisk eller direkt tilldelning för en Windows-dator med egen Skriv bords värd för virtuella skriv bord.
author: Heidilohr
ms.topic: how-to
ms.date: 07/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 11fbbe4d816c9f50afc879d74991976a467a9013
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448142"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Konfigurera tilldelnings typen egen Skriv bords värd pool

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).

Du kan konfigurera tilldelnings typen för din personliga Skriv bords värd för att anpassa din Windows Virtual Desktop-miljö så att den bättre passar dina behov. I det här avsnittet visar vi hur du konfigurerar automatisk eller direkt tilldelning för dina användare.

>[!NOTE]
> Anvisningarna i den här artikeln gäller bara för personliga Skriv bords värd pooler, inte pooler med pooler, eftersom användare i pooler värdar inte har tilldelats till vissa värdar för fjärrskrivbordssessioner.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har laddat ned och installerat Windows Virtual Desktop PowerShell-modulen. Om du inte har gjort det följer du anvisningarna i [Konfigurera PowerShell-modulen](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Konfigurera automatisk tilldelning

Automatisk tilldelning är standard tilldelnings typen för nya personliga Skriv bords värdar som skapats i din Windows Virtual Desktop-miljö. Automatisk tilldelning av användare kräver ingen speciell värd för sessionen.

Om du vill tilldela användare automatiskt måste du först tilldela dem till den personliga Skriv bords värds adresspoolen så att de kan se Skriv bordet i deras flöde. När en tilldelad användare startar Skriv bordet i sitt flöde ansluts de till en tillgänglig sessionsgräns om de inte redan har anslutit till den, vilket slutför tilldelnings processen.

Kör följande PowerShell-cmdlet för att konfigurera en värddator för att automatiskt tilldela användare till virtuella datorer:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Kör följande PowerShell-cmdlet för att tilldela en användare till den personliga Skriv bords värd poolen:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Konfigurera direkt tilldelning

Till skillnad från automatisk tilldelning måste du tilldela användaren till både den personliga Skriv bords värd poolen och en speciell sessionsnyckel innan de kan ansluta till sitt personliga skriv bord när du använder direkt tilldelning. Om användaren bara är tilldelad till en adresspool utan tilldelning av en sessionsgräns, kommer de inte att kunna komma åt resurser.

Kör följande PowerShell-cmdlet för att konfigurera en adresspool så att den kräver direkt tilldelning av användare till sessionsbaserade värdar:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Kör följande PowerShell-cmdlet för att tilldela en användare till den personliga Skriv bords värd poolen:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Kör följande PowerShell-cmdlet om du vill tilldela en användare till en speciell värd för sessionen:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

Så här tilldelar du en användare direkt till en sessions värd i Azure Portal:

1. Logga in på Azure Portal på <https://portal.azure.com>.
2. Ange **det virtuella Windows-skrivbordet** i Sök fältet.
3. Under **tjänster** väljer du **virtuellt Windows-skrivbord**.
4. På sidan Windows Virtual Desktop, gå till menyn till vänster i fönstret och välj **värdar för pooler**.
5. Välj namnet på den modempool som du vill uppdatera.
6. Gå sedan till menyn till vänster i fönstret och välj **program grupper**.
7. Välj namnet på den Skriv bords grupp som du vill redigera och välj sedan **tilldelningar** i menyn på vänster sida av fönstret.
8. Välj **+ Lägg till** och välj sedan de användare eller användar grupper som du vill publicera den här Skriv bords gruppen till.
9. Välj **tilldela virtuell dator** i informations fältet för att tilldela en sessions värd till en användare.
10. Välj den värd för sessionen som du vill tilldela till användaren och välj sedan **tilldela**.
11. Välj den användare som du vill tilldela sessionens värd till från listan över tillgängliga användare.
12. När du är klar väljer du **Välj**.

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat den personliga Skriv bords typen kan du logga in på en Windows Virtual Desktop-klient för att testa den som en del av en användarsession. Följande två instruktioner visar hur du ansluter till en session med valfri klient:

- [Ansluta med Windows-skrivbordsklienten](connect-windows-7-10.md)
- [Ansluta med webbklienten](connect-web.md)
- [Ansluta med Android-klienten](connect-android.md)
- [Ansluta med iOS-klienten](connect-ios.md)
- [Ansluta med macOS-klienten](connect-macos.md)