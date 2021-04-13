---
title: Windows Virtual Desktop avbildning för att förbereda MSIX-app bifogas – Azure
description: Så här skapar du en MSIX-app bifogad avbildning för en Windows Virtual Desktop-värdpool.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 443f117907381862639564dfbf9752562f4a3564
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363673"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Förbereda en MSIX-avbildning för Windows Virtual Desktop

MSIX-app attach är en lösning för programlagerhantering som gör att du dynamiskt kan koppla appar från ett MSIX-paket till en användarsession. MSIX-paketsystemet separerar appar från operativsystemet, vilket gör det enklare att skapa avbildningar för virtuella datorer. MSIX-paket ger dig också större kontroll över vilka appar som användarna kan komma åt på sina virtuella datorer. Du kan till och med separera appar från huvudavbildningen och ge dem till användarna senare.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>Skapa en VHD- eller VHDX-paket för MSIX

MSIX-paket måste vara i VHD- eller VHDX-format för att fungera korrekt. Det innebär att du måste skapa en VHD- eller VHDX-paket för att komma igång.

>[!NOTE]
>Om du inte redan har gjort det kontrollerar du att du aktiverar Hyper-V genom att följa anvisningarna i Installera [Hyper-V på Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

Så här skapar du en VHD- eller VHDX-paket för MSIX:

1. Öppna först PowerShell.
2. Kör sedan följande cmdlet för att skapa en virtuell hårddisk:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Kontrollera att den virtuella hårddisken är tillräckligt stor för att rymma det expanderade MSIX-paketet.

3. Kör följande cmdlet för att montera den virtuella hårddisken som du nyss skapade:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Kör sedan denna cmdlet för att initiera den monterade virtuella hårddisken:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Därefter kör du denna cmdlet för att skapa en ny partition för den initierade virtuella hårddisken:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Kör den här cmdleten för att formatera partitionen:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Skapa slutligen en överordnad mapp på den monterade virtuella hårddisken. Det här steget krävs eftersom MSIX-paketet måste ha en överordnad mapp för att fungera korrekt. Det spelar ingen roll vad du ger den överordnade mappen namnet, så länge den överordnade mappen finns.

## <a name="expand-msix"></a>Expandera MSIX

Efter det måste du expandera MSIX-avbildningen genom att "packa upp" dess filer till den virtuella hårddisken.

Så här expanderar du MSIX-avbildningen:

1. [Ladda ned verktyget msixmgr och](https://aka.ms/msixmgr) spara .zip-mappen i en mapp på en virtuell sessionsvärddator.

2. Packa upp .zip-mappen för verktyget msixmgr.

3. Placera MSIX-källpaketet i samma mapp där du packade upp verktyget msixmgr.

4. Öppna en kommandotolk som administratör och navigera till mappen där du laddade ned och uppackade verktyget msixmgr.

5. Kör följande cmdlet för att packa upp MSIX:en till den virtuella hårddisken som du skapade i föregående avsnitt.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Följande meddelande bör visas när du är klar med uppackningen:

    > Uppackat och tillämpat ACL:er för paketet: <package name> .msix

    >[!NOTE]
    > Om du använder paket från Microsoft Store för företag eller Education i nätverket eller på enheter som inte är anslutna till Internet måste du ladda ned och installera paketlicenser från Microsoft Store för att köra apparna. Information om hur du hämtar licenserna finns [i Använda paket offline.](app-attach.md#use-packages-offline)

6. Gå till den monterade virtuella hårddisken och öppna appmappen för att kontrollera att paketinnehållet finns där.

7. Demontera den virtuella hårddisken.

## <a name="upload-msix-image-to-share"></a>Ladda upp MSIX-avbildningen för att dela

När du har skapat MSIX-paketet måste du ladda upp den resulterande VHD-, VHDX- eller CIM-filen till en resurs där användarnas virtuella datorer kan komma åt den.

## <a name="next-steps"></a>Nästa steg

Ställ våra communityfrågor om den här funktionen på [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Du kan också lämna feedback för Windows Virtual Desktop på Windows Virtual Desktop [feedbackhubben](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Här är några andra artiklar som kan vara till hjälp:

- [Bifoga MSIX-appordlistor](app-attach-glossary.md)
- [Vanliga frågor och svar om att bifoga MSIX-appar](app-attach-faq.md)