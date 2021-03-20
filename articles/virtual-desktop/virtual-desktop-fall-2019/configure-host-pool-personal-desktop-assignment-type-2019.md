---
title: Windows Virtual Desktop (klassisk) egen Skriv bords tilldelning typ – Azure
description: Så här konfigurerar du tilldelnings typen för en Windows Virtual Desktop (klassisk) egen Skriv bords värd pool.
author: Heidilohr
ms.topic: how-to
ms.date: 05/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 669010d2de90498c98fc685fe931b084a11cd104
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008516"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type-for-windows-virtual-desktop-classic"></a>Konfigurera tilldelnings typen för den personliga Skriv bords värd för Windows Virtual Desktop (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../configure-host-pool-personal-desktop-assignment-type.md).

Du kan konfigurera tilldelnings typen för din personliga Skriv bords värd för att anpassa din Windows Virtual Desktop-miljö så att den bättre passar dina behov. I det här avsnittet visar vi hur du konfigurerar automatisk eller direkt tilldelning för dina användare.

>[!NOTE]
> Anvisningarna i den här artikeln gäller bara för personliga Skriv bords värd pooler, inte pooler med pooler, eftersom användare i pooler värdar inte har tilldelats till vissa värdar för fjärrskrivbordssessioner.

## <a name="configure-automatic-assignment"></a>Konfigurera automatisk tilldelning

Automatisk tilldelning är standard tilldelnings typen för nya personliga Skriv bords värdar som skapats i din Windows Virtual Desktop-miljö. Automatisk tilldelning av användare kräver ingen speciell värd för sessionen.

Om du vill tilldela användare automatiskt måste du först tilldela dem till den personliga Skriv bords värds adresspoolen så att de kan se Skriv bordet i deras flöde. När en tilldelad användare startar Skriv bordet i sitt flöde ansluts de till en tillgänglig sessionsgräns om de inte redan har anslutit till den, vilket slutför tilldelnings processen.

Innan du börjar kan du [Hämta och importera Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) om du inte redan har gjort det.

> [!NOTE]
> Kontrol lera att du har installerat PowerShell-modulen version 1.0.1534.2001 eller senare av Windows Virtual Desktop innan du följer dessa anvisningar.

Sedan kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Kör följande PowerShell-cmdlet för att konfigurera en värddator för att automatiskt tilldela användare till virtuella datorer:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Kör följande PowerShell-cmdlet för att tilldela en användare till den personliga Skriv bords värd poolen:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Konfigurera direkt tilldelning

Till skillnad från automatisk tilldelning måste du tilldela användaren till både den personliga Skriv bords värd poolen och en speciell sessionsnyckel innan de kan ansluta till sitt personliga skriv bord när du använder direkt tilldelning. Om användaren bara är tilldelad till en adresspool utan tilldelning av en sessionsgräns, kommer de inte att kunna komma åt resurser.

Kör följande PowerShell-cmdlet för att konfigurera en adresspool så att den kräver direkt tilldelning av användare till sessionsbaserade värdar:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Kör följande PowerShell-cmdlet för att tilldela en användare till den personliga Skriv bords värd poolen:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Kör följande PowerShell-cmdlet om du vill tilldela en användare till en speciell värd för sessionen:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="remove-a-user-assignment"></a>Ta bort en användar tilldelning

Du kanske vill ta bort en användar tilldelning eftersom användaren inte längre behöver det personliga Skriv bordet, användaren har lämnat företaget eller vill återanvända Skriv bordet för någon annan.

För närvarande är det enda sättet som du kan ta bort användar tilldelningen för ett personligt skriv bord att ta bort värd för sessionen fullständigt. Kör denna cmdlet för att ta bort värd för sessionen:

```powershell
Remove-RdsSessionHost
```

Om du behöver lägga till sessionsnyckeln i den personliga Skriv bords poolen igen avinstallerar du Windows Virtual Desktop på datorn och följer sedan stegen i [skapa en adresspool med PowerShell](create-host-pools-powershell-2019.md) för att omregistrera värd för sessionen.

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat den personliga Skriv bords typen kan du logga in på en Windows Virtual Desktop-klient för att testa den som en del av en användarsession. Följande två instruktioner visar hur du ansluter till en session med valfri klient:

- [Ansluta med Windows-skrivbordsklienten](connect-windows-7-10-2019.md)
- [Ansluta med webbklienten](connect-web-2019.md)
