---
title: Anpassa feed för virtuella Windows-skrivbordet användare – Azure
description: Anpassa feed för virtuella Windows-användare med PowerShell-cmdletar.
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: a2d5a870ee8953a4388e58d21cf941f23434382b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447122"
---
# <a name="customize-the-feed-for-windows-virtual-desktop-users"></a>Anpassa feeden för användare av virtuella Windows-datorer

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md).

Du kan anpassa flödet så att RemoteApp-och fjärr skrivbords resurserna visas på ett igenkännbart sätt för dina användare.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har laddat ned och installerat Windows Virtual Desktop PowerShell-modulen. Om du inte har gjort det följer du anvisningarna i [Konfigurera PowerShell-modulen](powershell-module.md).

## <a name="customize-the-display-name-for-a-remoteapp"></a>Anpassa visnings namnet för en RemoteApp

Du kan ändra visnings namnet för en publicerad RemoteApp genom att ange det egna namnet. Som standard är det egna namnet samma som namnet på RemoteApp-programmet.

Om du vill hämta en lista över publicerade RemoteApp-program för en app-grupp kör du följande PowerShell-cmdlet:

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

Om du vill tilldela ett eget namn till en RemoteApp kör du följande cmdlet med de obligatoriska parametrarna:

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

Anta till exempel att du hämtade de aktuella programmen med följande exempel-cmdlet:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

Utdata skulle se ut så här:

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
Om du vill uppdatera det egna namnet kör du denna cmdlet:

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

Du kan kontrol lera att du har uppdaterat det egna namnet genom att köra denna cmdlet:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

Cmdleten ska ge dig följande utdata:

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Anpassa visnings namnet för ett fjärr skrivbord

Du kan ändra visnings namnet för ett publicerat fjärr skrivbord genom att ange ett eget namn. Om du har skapat en pool för värdar och skriv bord manuellt via PowerShell är standard namnet "session Desktop". Om du har skapat en adresspool och en grupp för Skriv bords appar via GitHub Azure Resource Manager-mallen eller Azure Marketplace-erbjudandet, är standard namnet samma som namnet på värddatorn.

Kör följande PowerShell-cmdlet för att hämta fjärr skrivbords resursen:

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Om du vill tilldela fjärr skrivbords resursen ett eget namn kör du följande PowerShell-cmdlet:

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Anpassa ett visnings namn i Azure Portal

Du kan ändra visnings namnet för ett publicerat fjärr skrivbord genom att ange ett eget namn med hjälp av Azure Portal.

1. Logga in på Azure Portal på <https://portal.azure.com>.

2. Sök efter **virtuellt Windows-skrivbord**.

3. Under tjänster väljer du **virtuellt Windows-skrivbord**.

4. På sidan Windows Virtual Desktop väljer du **program grupper** på vänster sida av skärmen och väljer sedan namnet på den app-grupp som du vill redigera. (Om du till exempel vill redigera visnings namnet på Skriv bords gruppen väljer du app-gruppen med namnet **Desktop**.)

5. Välj **program** i menyn på vänster sida av skärmen.

6. Välj det program som du vill uppdatera och ange sedan ett nytt **visnings namn**.

7. Välj **Spara**. Det program som du redigerade bör nu visa det uppdaterade namnet.

## <a name="next-steps"></a>Nästa steg

Nu när du har anpassat feeden för användare kan du logga in på en Windows Virtual Desktop-klient för att testa den. Det gör du genom att fortsätta till Windows Virtual Desktop-instruktionen how-TOS:

 * [Anslut med Windows 10 eller Windows 7](connect-windows-7-10.md)
 * [Ansluta med webbklienten](connect-web.md)
 * [Ansluta med Android-klienten](connect-android.md)
 * [Ansluta med iOS-klienten](connect-ios.md)
 * [Ansluta med macOS-klienten](connect-macos.md)
