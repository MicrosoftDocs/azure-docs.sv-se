---
title: Windows Virtual Desktop MSIX-appen kopplar PowerShell – Azure
description: Konfigurera MSIX-app attach för Windows Virtual Desktop powershell.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: ebc403553443a9ea04525323b751fbdb51d23c6e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500591"
---
# <a name="set-up-msix-app-attach-using-powershell"></a>Konfigurera MSIX-app bifoga med Hjälp av PowerShell

Förutom den här Azure Portal du även konfigurera MSIX-appen för att ansluta manuellt med PowerShell. Den här artikeln beskriver hur du använder PowerShell för att konfigurera MSIX-app bifoga.

## <a name="requirements"></a>Krav

Det här behöver du för att konfigurera MSIX-app bifoga:

- En fungerande Windows Virtual Desktop distribution. Information om hur du distribuerar Windows Virtual Desktop (klassisk) finns [i Skapa en klientorganisation i Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Information om hur du distribuerar Windows Virtual Desktop med Azure Resource Manager-integrering finns i Skapa en [värdpool med Azure Portal](./create-host-pools-azure-marketplace.md).
- En Windows Virtual Desktop värdpool med minst en aktiv sessionsvärd.
- En fjärrskrivbordsappgrupp.
- MSIX-paketeringsverktyget.
- Ett MSIX-paketerat program expanderat till en MSIX-avbildning som laddas upp till en filresurs.
- En filresurs i Windows Virtual Desktop distribution där MSIX-paketet ska lagras.
- Filresursen där du laddade upp MSIX-avbildningen måste också vara tillgänglig för alla virtuella datorer (VM) i värdpoolen. Användarna behöver skrivskyddade behörigheter för att få åtkomst till avbildningen.
- Ladda ned och installera PowerShell Core.
- Ladda ned den offentliga Azure PowerShell modulen och expandera den till en lokal mapp.
- Installera Azure-modulen genom att köra följande cmdlet:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Logga in på Azure och importera modulen

När du är klar med alla krav öppnar du PowerShell Core i en upphöjd kommandotolk och kör denna cmdlet:

```powershell
Connect-AzAccount
```

När du har kört det autentiserar du ditt konto med dina autentiseringsuppgifter. I det här fallet kan du bli ombedd att ange en enhets-URL eller en token.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Importera modulen Az.WindowsVirtualDesktop

Du behöver modulen Az.DesktopVirtualization för att följa anvisningarna i den här artikeln.

>[!NOTE]
>För den offentliga förhandsversionen tillhandahåller vi modulen som separata ZIP-filer som du måste importera manuellt.

Innan du börjar kan du köra följande cmdlet för att se om modulen Az.DesktopVirtualization redan är installerad på din session eller virtuella dator:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Om du vill avinstallera en befintlig kopia av modulen och börja om från början kör du den här cmdleten:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Om modulen är blockerad på den virtuella datorn kör du denna cmdlet för att avblockera den:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Nu när rensningen är över är det dags att importera modulen.

1. Kör följande cmdlet och tryck sedan på **R-tangenten** när du uppmanas att godkänna att den anpassade koden körs.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. När du har kört import-cmdleten kontrollerar du om den har cmdletarna för MSIX genom att köra följande cmdlet:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Om cmdletarna finns där bör utdata se ut så här:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Om du inte ser dessa utdata stänger du alla PowerShell- och PowerShell Core sessioner och försöker igen.

## <a name="set-up-helper-variables"></a>Konfigurera hjälpvariabler

När du har importerat modulen måste du konfigurera hjälpvariablerna. I följande exempel visas hur du gör var och en.

Så här hämtar du ditt prenumerations-ID:

```powershell
Get-AzContext -ListAvailable | fl
```

Så här väljer du kontexten för en Azure-klientorganisation och -prenumeration med ett namn:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

Så här anger du prenumerationsvariabeln:

```powershell
$subId = $obj.Subscription.Id
```

Så här anger du arbetsytans namn:

```powershell
$ws = "<WorksSpaceName>"
```

Så här anger du värdpoolens namn:

```powershell
$hp = "<HostPoolName>"
```

Så här konfigurerar du resursgruppen där de virtuella sessionsvärdarna konfigureras:

```powershell
$rg = "<ResourceGroupName>"
```

Och slutligen, för att bekräfta att du har angett alla variabler korrekt:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Lägga till ett MSIX-paket till en värdpool

När du har ställt in allt är det dags att lägga till MSIX-paketet i en värdpool. För att göra det måste du först hämta UNC-sökvägen till MSIX-avbildningen.

Använd UNC-sökvägen och kör den här cmdleten för att expandera MSIX-avbildningen:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Kör den här cmdleten för att lägga till MSIX-paketet i önskad värdpool:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

När du är klar bekräftar du att paketet har skapats med denna cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Ta bort ett MSIX-paket från en värdpool

Så här tar du bort ett paket från en värdpool:

Hämta en lista över alla paket som är associerade med en värdpool med denna cmdlet och leta upp namnet på det paket som du vill ta bort i utdata:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

Du kan också hämta ett visst paket baserat på dess visningsnamn med denna cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Ta bort paketet genom att köra den här cmdleten:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>Publicera MSIX-appar till en appgrupp

Du kan bara följa anvisningarna i det här avsnittet om du är klar med att följa anvisningarna i föregående avsnitt. Om du har en värdpool med en aktiv sessionsvärd, minst en skrivbordsappgrupp och har lagt till ett MSIX-paket i värdpoolen, är du redo att gå.

Om du vill publicera en app från MSIX-paketet till en appgrupp måste du hitta dess namn och sedan använda det namnet i publicerings-cmdleten.

Så här publicerar du en app:

Kör den här cmdleten för att visa en lista över alla tillgängliga appgrupper:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

När du har hittat namnet på den appgrupp som du vill publicera appar till använder du dess namn i denna cmdlet:

```powershell
$grName = "<AppGroupName>"
```

Slutligen måste du publicera appen.

- Kör den här cmdleten för att publicera MSIX-programmet till en skrivbordsappgrupp:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Om du vill publicera appen till en fjärrappgrupp kör du denna cmdlet i stället:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Om en användare har tilldelats både en fjärrappgrupp och en skrivbordsappgrupp i samma värdpool visas MSIX-appar från båda grupperna när de ansluter till fjärrskrivbordet.

## <a name="next-steps"></a>Nästa steg

Ställ våra communityfrågor om den här funktionen på [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Du kan också lämna feedback för Windows Virtual Desktop på Windows Virtual Desktop [feedbackhubben](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Här är några andra artiklar som kan vara till hjälp:

- [Bifoga MSIX-appordlistor](app-attach-glossary.md)
- [Vanliga frågor och svar om att bifoga MSIX-appar](app-attach-faq.md)
