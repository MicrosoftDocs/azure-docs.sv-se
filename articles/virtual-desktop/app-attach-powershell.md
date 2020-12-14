---
title: Windows Virtual Desktop MSIX-appen koppla Preview PowerShell – Azure
description: Så här konfigurerar du MSIX-appen Anslut för Windows Virtual Desktop med PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8aa6a2168bff6e90d636770804900fa93f081ced
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97426005"
---
# <a name="set-up-msix-app-attach-preview-using-powershell"></a>Konfigurera MSIX app attach (för hands version) med PowerShell

> [!IMPORTANT]
> MSIX app Attach är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Förutom Azure Portal kan du också konfigurera MSIX app attach (för hands version) manuellt med PowerShell. Den här artikeln beskriver hur du använder PowerShell för att konfigurera MSIX app Attach.

## <a name="requirements"></a>Krav

>[!IMPORTANT]
>Innan du börjar ska du fylla i och skicka [det här formuläret](https://aka.ms/enablemsixappattach) för att aktivera MSIX app Attach i din prenumeration. Om du inte har en godkänd begäran fungerar inte MSIX-appens koppling. Godkännande av förfrågningar kan ta upp till 24 timmar under arbets dagar. Du får ett e-postmeddelande när din begäran har godkänts och slutförts.

Det här behöver du för att konfigurera MSIX app Attach:

- En fungerande distribution av virtuella Windows-datorer. Information om hur du distribuerar virtuella Windows-datorer (klassisk) finns i [skapa en klient i Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Information om hur du distribuerar virtuella Windows-datorer med Azure Resource Manager-integrering finns i [skapa en adresspool med Azure Portal](./create-host-pools-azure-marketplace.md).
- En Windows-adresspool för virtuella skriv bord med minst en aktiv session-värd.
- En fjärran sluten grupp för fjärr skrivbord.
- MSIX-packnings verktyget.
- Ett MSIX-paketerat program expanderat till en MSIX-avbildning som laddas upp till en fil resurs.
- En fil resurs i Windows-distributionen för virtuella skriv bord där MSIX-paketet ska lagras.
- Fil resursen där du laddade upp MSIX-avbildningen måste också vara tillgänglig för alla virtuella datorer i poolen. Användare behöver skrivskyddade behörigheter för att komma åt avbildningen.
- Ladda ned och installera PowerShell Core.
- Hämta den offentliga för hands versionen Azure PowerShell-modulen och expandera den till en lokal mapp.
- Installera Azure-modulen genom att köra följande cmdlet:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Logga in på Azure och importera modulen

När du har fått alla krav som är klara öppnar du PowerShell core i en upphöjd kommando tolk och kör denna cmdlet:

```powershell
Connect-AzAccount
```

När du har kört det autentiserar du ditt konto med dina autentiseringsuppgifter. I det här fallet kan du bli ombedd att ange en enhets-URL eller en token.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Importera modulen AZ. WindowsVirtualDesktop

Du behöver modulen AZ. DesktopVirtualization för att följa anvisningarna i den här artikeln.

>[!NOTE]
>För den allmänt tillgängliga för hands versionen kommer vi att tillhandahålla modulen som separata ZIP-filer som du måste importera manuellt.

Innan du börjar kan du köra följande cmdlet för att se om modulen AZ. DesktopVirtualization redan är installerad på sessionen eller den virtuella datorn:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Om du använder WAN för att avinstallera en befintlig kopia av modulen och börja om, kör du denna cmdlet:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Om modulen är blockerad på den virtuella datorn kör du denna cmdlet för att avblockera den:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Med den här rensningen är det dags att importera modulen.

1. Kör följande cmdlet och tryck sedan på **R** -tangenten när du uppmanas att godkänna att köra den anpassade koden.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. När du har kört import-cmdleten kan du kontrol lera om den har cmdletar för MSIX genom att köra följande cmdlet:

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

   Om du inte ser dessa utdata stänger du alla PowerShell-och PowerShell-kärnor och försöker igen.

## <a name="set-up-helper-variables"></a>Konfigurera hjälp variabler

När du har importerat modulen måste du konfigurera hjälp-variablerna. I följande exempel visas hur du gör var och en.

Så här hämtar du ditt prenumerations-ID:

```powershell
Get-AzContext -ListAvailable | fl
```

Så här väljer du kontexten för en Azure-klient och-prenumeration med ett namn:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

Ange prenumerations variabeln:

```powershell
$subId = $obj.Subscription.Id
```

Ange arbets ytans namn:

```powershell
$ws = "<WorksSpaceName>"
```

Ange värdnamn:

```powershell
$hp = "<HostPoolName>"
```

Konfigurera resurs gruppen där de virtuella datorerna i sessionen är konfigurerade:

```powershell
$rg = "<ResourceGroupName>"
```

Och slutligen för att bekräfta att du har angett alla variabler korrekt:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Lägga till ett MSIX-paket i en adresspool

När du har konfigurerat allt är det dags att lägga till MSIX-paketet i en adresspool. För att göra det måste du först hämta UNC-sökvägen till MSIX-avbildningen.

Använd UNC-sökvägen och kör denna cmdlet för att expandera MSIX-avbildningen:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Kör denna cmdlet för att lägga till MSIX-paketet till önskad adresspool:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

När du är klar kontrollerar du att paketet har skapats med denna cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Ta bort ett MSIX-paket från en värdbaserad pool

Ta bort ett paket från en värdbaserad pool:

Hämta en lista över alla paket som är associerade med en adresspool med denna cmdlet och leta sedan reda på namnet på det paket som du vill ta bort i utdata:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

Du kan också hämta ett visst paket baserat på dess visnings namn med denna cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Om du vill ta bort paketet kör du denna cmdlet:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>Publicera MSIX-appar i en app-grupp

Du kan bara följa instruktionerna i det här avsnittet om du har följt anvisningarna i föregående avsnitt. Om du har en adresspool med en aktiv session-värd, minst en Skriv bords grupp och har lagt till ett MSIX-paket till poolen, är du redo att sätta igång.

Om du vill publicera en app från MSIX-paketet till en app-grupp måste du hitta namnet och sedan använda namnet i publicerings-cmdleten.

Så här publicerar du en app:

Kör denna cmdlet för att visa en lista över alla tillgängliga app-grupper:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

När du har hittat namnet på den app-grupp som du vill publicera appar till, använder du dess namn i denna cmdlet:

```powershell
$grName = "<AppGroupName>"
```

Slutligen måste du publicera appen.

- Om du vill publicera MSIX-programmet i en app-app-grupp kör du denna cmdlet:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Om du vill publicera appen i en fjärran sluten app-grupp kör du denna cmdlet i stället:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Om en användare är tilldelad både en fjärran sluten app och en Skriv bords grupp i samma adresspool, kommer de att se MSIX-appar från båda grupperna när användaren ansluter till sina fjärr skrivbord.

## <a name="next-steps"></a>Nästa steg

Fråga våra Community-frågor om den här funktionen på [Windows-TechCommunity för virtuella datorer](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Du kan också lämna feedback för virtuella Windows-datorer i [hubben Windows Virtual Desktop feedback](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Här följer några andra artiklar som du kan ha nytta av:

- [Ord lista för MSIX-appen](app-attach-glossary.md)
- [Vanliga frågor och svar om MSIX app](app-attach-faq.md)