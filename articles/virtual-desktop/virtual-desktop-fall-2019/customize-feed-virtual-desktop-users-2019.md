---
title: Anpassa feed för Windows Virtual Desktop (klassiska) användare – Azure
description: Så här anpassar du feed för Windows Virtual Desktop (klassiska) användare med PowerShell-cmdletar.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd7496690ec88fbe4297386c32d1b8a2c3234577
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91540769"
---
# <a name="customize-feed-for-windows-virtual-desktop-classic-users"></a>Anpassa feed för Windows Virtual Desktop (klassiska) användare

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../customize-feed-for-virtual-desktop-users.md).

Du kan anpassa flödet så att RemoteApp-och fjärr skrivbords resurserna visas på ett igenkännbart sätt för dina användare.

Börja med att [Hämta och importera Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som ska användas i PowerShell-sessionen om du inte redan gjort det. Sedan kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Anpassa visnings namnet för en RemoteApp

Du kan ändra visnings namnet för en publicerad RemoteApp genom att ange det egna namnet. Som standard är det egna namnet samma som namnet på RemoteApp-programmet.

Om du vill hämta en lista över publicerade RemoteApp-program för en app-grupp kör du följande PowerShell-cmdlet:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![En skärm bild av PowerShell-cmdleten Get-RDSRemoteApp med namnet och FriendlyName markerat för att anpassa visnings namn.](../media/get-rdsremoteapp.png)

Om du vill tilldela ett eget namn till en RemoteApp kör du följande PowerShell-cmdlet:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![En skärm bild av PowerShell-cmdleten Set-RDSRemoteApp med namnet och nytt FriendlyName markerat för att anpassa visnings namn.](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Anpassa visnings namnet för ett fjärr skrivbord

Du kan ändra visnings namnet för ett publicerat fjärr skrivbord genom att ange ett eget namn. Om du har skapat en pool för värdar och skriv bord manuellt via PowerShell är standard namnet "session Desktop". Om du har skapat en adresspool och en grupp för Skriv bords appar via GitHub Azure Resource Manager-mallen eller Azure Marketplace-erbjudandet, är standard namnet samma som namnet på värddatorn.

Kör följande PowerShell-cmdlet för att hämta fjärr skrivbords resursen:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![En skärm bild av PowerShell-cmdleten Get-RDSRemoteApp med namnet och FriendlyName markerat.](../media/get-rdsremotedesktop.png)

Om du vill tilldela fjärr skrivbords resursen ett eget namn kör du följande PowerShell-cmdlet:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![En skärm bild av PowerShell-cmdleten Set-RDSRemoteApp med namnet och nytt FriendlyName markerat.](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har anpassat feeden för användare kan du logga in på en Windows Virtual Desktop-klient för att testa den. Det gör du genom att fortsätta till Windows Virtual Desktop-instruktionen how-TOS:

 * [Ansluta från Windows 10 eller Windows 7](connect-windows-7-10-2019.md)
 * [Ansluta från en webbläsare](connect-web-2019.md)
