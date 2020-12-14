---
title: Windows Virtual Desktop förbereda MSIX-appen bifoga avbildning för hands version – Azure
description: Så här skapar du en MSIX app Attach-avbildning för en Windows-pool för virtuella skriv bord.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 204cc9a05d62caf62179100fa3496be422a3ec0c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97426015"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Förbereda en MSIX-avbildning för Windows Virtual Desktop

> [!IMPORTANT]
> MSIX app Attach är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

MSIX app attach (för hands version) är en lösning för program skikt som gör att du kan koppla appar dynamiskt från ett MSIX-paket till en användarsession. MSIX-paket systemet separerar appar från operativ systemet, vilket gör det enklare att skapa avbildningar för virtuella datorer. MSIX-paket ger dig också bättre kontroll över vilka appar dina användare kan komma åt på sina virtuella datorer. Du kan till och med separera appar från huvud avbildningen och ge dem till användare senare.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>Skapa ett VHD-eller VHDX-paket för MSIX

MSIX-paket måste vara i ett VHD-eller VHDX-format för att fungera korrekt. Det innebär att du måste skapa ett VHD-eller VHDX-paket för att komma igång.

>[!NOTE]
>Om du inte redan har gjort det kontrollerar du att du aktiverar Hyper-V genom att följa anvisningarna i [Installera Hyper-v på Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

Så här skapar du ett VHD-eller VHDX-paket för MSIX:

1. Börja med att öppna PowerShell.
2. Kör sedan följande cmdlet för att skapa en virtuell hård disk:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Se till att den virtuella hård disken är tillräckligt stor för att rymma det expanderade MSIX-paketet.

3. Kör följande cmdlet för att montera den virtuella hård disken som du nyss skapade:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Kör sedan denna cmdlet för att initiera den monterade virtuella hård disken:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Sedan kör du denna cmdlet för att skapa en ny partition för den initierade virtuella hård disken:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Kör denna cmdlet för att formatera partitionen:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Skapa slutligen en överordnad mapp på den monterade virtuella hård disken. Det här steget är obligatoriskt eftersom MSIX-paketet måste ha en överordnad mapp för att fungera korrekt. Det spelar ingen roll vad du namnger den överordnade mappen, så länge den överordnade mappen finns.

## <a name="expand-msix"></a>Expandera MSIX

Därefter måste du expandera MSIX-avbildningen genom att "packa upp" sina filer på den virtuella hård disken.

Expandera MSIX-avbildningen:

1. [Ladda ned msixmgr-verktyget](https://aka.ms/msixmgr) och spara zip-mappen i en mapp i en VM-session.

2. Zippa upp mappen msixmgr-verktyget. zip.

3. Lägg till käll MSIX-paketet i samma mapp där du zippade msixmgr-verktyget.

4. Öppna en kommando tolk som administratör och navigera till den mapp där du laddade ned och zippa upp msixmgr-verktyget.

5. Kör följande cmdlet för att packa upp MSIX i den virtuella hård disken som du skapade i föregående avsnitt.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Följande meddelande ska visas när du är klar med att packa upp:

    > Har packat upp och tillämpat ACL: er för paket: <package name> . msix

    >[!NOTE]
    > Om du använder paket från Microsoft Store för företag eller utbildning i nätverket eller på enheter som inte är anslutna till Internet, måste du ladda ned och installera paket licenser från Microsoft Store för att köra apparna. Information om hur du hämtar licenserna finns i [använda paket offline](app-attach.md#use-packages-offline).

6. Gå till den monterade virtuella hård disken och öppna mappen app för att se till att paket innehållet finns där.

7. Demontera den virtuella hårddisken.

## <a name="upload-msix-image-to-share"></a>Ladda upp MSIX-avbildningen till resursen

När du har skapat MSIX-paketet måste du ladda upp den resulterande VHD-, VHDX-eller CIM-filen till en resurs där användarnas virtuella datorer kan komma åt den.

## <a name="next-steps"></a>Nästa steg

Fråga våra Community-frågor om den här funktionen på [Windows-TechCommunity för virtuella datorer](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Du kan också lämna feedback för virtuella Windows-datorer i [hubben Windows Virtual Desktop feedback](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Här följer några andra artiklar som du kan ha nytta av:

- [Ord lista för MSIX-appen](app-attach-glossary.md)
- [Vanliga frågor och svar om MSIX app](app-attach-faq.md)