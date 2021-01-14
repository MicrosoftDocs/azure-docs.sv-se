---
title: Windows Virtual Desktop MSIX-appen koppla Portal Preview – Azure
description: Så här konfigurerar du MSIX-appen för att ansluta till virtuella Windows-datorer med hjälp av Azure Portal.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 34bcef24d5e7fbda53984f14a2307859c9210262
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185962"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Konfigurera MSIX-appbifogning med Azure Portal

> [!IMPORTANT]
> MSIX app Attach är för närvarande en offentlig för hands version.
> Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln beskriver hur du konfigurerar MSIX app attach (för hands version) i en Windows Virtual Desktop-miljö.

## <a name="requirements"></a>Krav

>[!IMPORTANT]
>Innan du börjar ska du fylla i och skicka [det här formuläret](https://aka.ms/enablemsixappattach) för att aktivera MSIX app Attach i din prenumeration. Om du inte har en godkänd begäran fungerar inte MSIX-appens koppling. Godkännande av förfrågningar kan ta upp till 24 timmar under arbets dagar. Du får ett e-postmeddelande när din begäran har godkänts och slutförts.

Det här behöver du för att konfigurera MSIX app Attach:

- En fungerande distribution av virtuella Windows-datorer. Information om hur du distribuerar virtuella Windows-datorer (klassisk) finns i [skapa en klient i Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Information om hur du distribuerar virtuella Windows-datorer med Azure Resource Manager-integrering finns i [skapa en adresspool med Azure Portal](./create-host-pools-azure-marketplace.md).
- En Windows-adresspool för virtuella skriv bord med minst en aktiv session-värd.
- Den här poolen måste vara i validerings miljön. 
- MSIX-packnings verktyget.
- Ett MSIX-paketerat program expanderat till en MSIX-avbildning som laddas upp till en fil resurs.
- En fil resurs i Windows-distributionen för virtuella skriv bord där MSIX-paketet ska lagras.
- Fil resursen där du laddade upp MSIX-avbildningen måste också vara tillgänglig för alla virtuella datorer i poolen. Användare behöver skrivskyddade behörigheter för att komma åt avbildningen.

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Inaktivera automatiska uppdateringar för MSIX app attaching Applications

Innan du börjar måste du inaktivera automatiska uppdateringar för MSIX app attaching Applications. Om du vill inaktivera automatiska uppdateringar måste du köra följande kommandon i en upphöjd kommando tolk:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

>[!NOTE]
>Vi rekommenderar att du startar om den virtuella datorn när du har aktiverat Hyper-V.

## <a name="configure-the-msix-app-attach-management-interface"></a>Konfigurera MSIX app Attach Management Interface

Därefter måste du ladda ned och konfigurera MSIX-appen för att ansluta hanterings gränssnittet för Azure Portal.

Konfigurera hanterings gränssnittet:

1. [Öppna Preview-portalen](https://preview.portal.azure.com/?feature.msixapplications=true#home).
2. Om du får en uppmaning om att fråga om du vill ha det betrodda tillägget väljer du **Tillåt**.

      > [!div class="mx-imgBorder"]
      > ![En skärm bild av fönstret ej betrodda tillägg. "Tillåt" är markerat i rött.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Lägg till en MSIX-avbildning i poolen värd

Härnäst måste du lägga till MSIX-avbildningen i din värdbaserade pool.

Så här lägger du till MSIX-avbildningen:

1. Öppna Azure-portalen.

2. Ange det **virtuella Windows-skrivbordet** i Sök fältet och välj sedan tjänst namnet.

      > [!div class="mx-imgBorder"]
      > ![En skärm bild av en användare som väljer "virtuellt Windows-skrivbord" från den nedrullningsbara menyn i Sök fältet i Azure Portal. "Windows Virtual Desktop" är markerat i rött.](media/find-and-select.png)

3. Välj den värd pool där du planerar att placera MSIX-apparna.

4. Välj **MSIX-paket** för att öppna data rutnätet med alla **MSIX-paket** som för närvarande har lagts till i värd poolen.

5. Välj **+ Lägg** till för att öppna fliken **Lägg till MSIX-paket** .

6. Ange följande värden på fliken **Lägg till MSIX-paket** :

      - För **MSIX avbildnings Sök väg** anger du en giltig UNC-sökväg som pekar på MSIX-avbildningen på fil resursen. (Till exempel `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` .) När du är klar väljer du **Lägg till** för att checka in behållaren MSIX för att kontrol lera om sökvägen är giltig.

      - För **MSIX-paket** väljer du relevant MSIX-paket namn på den nedrullningsbara menyn. Den här menyn kommer bara att fyllas i om du har angett en giltig avbildnings Sök väg i **sökvägen till MSIX-avbildningen**.

      - För **paket program** kontrollerar du att listan innehåller alla MSIX-program som du vill ska vara tillgängliga för användare i ditt MSIX-paket.

      - Du kan också ange ett **visnings namn** om du vill att ditt paket ska ha ett mer användarvänligt användar distributioner.

      - Kontrol lera att **versionen** har rätt versions nummer.

      - Välj den **registrerings typ** som du vill använda. Vilken du använder beror på dina behov:

    - **Registreringen på begäran** skjuter upp den fullständiga registreringen av MSIX-programmet tills användaren startar programmet. Detta är den registrerings typ som vi rekommenderar att du använder.

    - **Logga in blockerar** endast registreringar när användaren loggar in. Vi rekommenderar inte den här typen eftersom den kan leda till längre inloggnings tider för användare.

7.  I **tillstånd** väljer du önskat tillstånd.
    -  Med **aktiv** status kan användare interagera med paketet.
    -  **Inaktiv** status gör att det virtuella Windows-skrivbordet ignorerar paketet och levererar det inte till användarna.

8. När du är klar väljer du **Lägg till**.

## <a name="publish-msix-apps-to-an-app-group"></a>Publicera MSIX-appar i en app-grupp

Sedan måste du publicera apparna i paketet. Du måste göra detta för både Station ära och fjärranslutna program grupper.

Om du redan har en MSIX-avbildning kan du gå vidare till [publicera MSIX-appar i en app-grupp](#publish-msix-apps-to-an-app-group). Om du vill testa äldre program följer du instruktionerna i [skapa ett MSIX-paket från ett Skriv bords installations program på en virtuell dator](/windows/msix/packaging-tool/create-app-package-msi-vm/) för att konvertera det äldre programmet till ett MSIX-paket.

Så här publicerar du apparna:

1. I resurs leverantören för Windows virtuella skriv bord väljer du fliken **program grupper** .

2. Välj den program grupp som du vill publicera apparna på.

   >[!NOTE]
   >MSIX-program kan levereras med MSIX-appen bifoga till både fjärrappar och skriv bords grupps grupper

3. När du är i app-gruppen väljer du fliken **program** . I **program** rutnätet visas alla befintliga appar i app-gruppen.

4. Välj **+ Lägg** till för att öppna fliken **Lägg till program** .

      > [!div class="mx-imgBorder"]
      > ![En skärm bild av användaren som väljer + Lägg till för att öppna fliken Lägg till program](media/select-add.png)

5. För **program källa** väljer du källan för ditt program.
    - Om du använder en app för Skriv bords grupp väljer du **MSIX-paket**.
      
      > [!div class="mx-imgBorder"]
      > ![En skärm bild av en kund som väljer MSIX-paket på den nedrullningsbara menyn för program källan. MSIX-paketet är markerat i rött.](media/select-source.png)
    
    - Välj något av följande alternativ om du använder en fjärran sluten app-grupp:
        
        - Start-menyn
        - Sökväg till app
        - MSIX-paket

    - För **program namn** anger du ett beskrivande namn för programmet.

    Du kan också konfigurera följande valfria funktioner:
   
    - I **visnings namn** anger du ett nytt namn för det paket som användarna ser.

    - I **Beskrivning** anger du en kort beskrivning av Appaketet.

    - Om du använder en fjärran sluten app-grupp kan du också konfigurera följande alternativ:

        - **Sökväg till ikon**
        - **Ikon index**
        - **Visa i webb flöde**

6. När du är klar väljer du **Spara**.

>[!NOTE]
>När en användare tilldelas till en fjärran sluten app-grupp och skriv bords grupp från samma adresspool, visas gruppen Skriv bords grupp i matningen.

## <a name="assign-a-user-to-an-app-group"></a>Tilldela en användare till en app-grupp

När du har tilldelat MSIX-appar till en app-grupp måste du ge användarna åtkomst till dem. Du kan tilldela åtkomst genom att lägga till användare eller användar grupper i en app-grupp med publicerade MSIX-program. Följ anvisningarna i [Hantera app-grupper med Azure Portal](manage-app-groups.md) för att tilldela dina användare till en app-grupp.

## <a name="change-msix-package-state"></a>Ändra paket status för MSIX

Därefter måste du ändra MSIX-paketets tillstånd till antingen **aktiv** eller **inaktiv**, beroende på vad du vill göra med paketet. Aktiva paket är paket som användarna kan interagera med när de har publicerats. Inaktiva paket ignoreras av det virtuella Windows-skrivbordet, så användarna kan inte interagera med apparna i.

### <a name="change-state-with-the-applications-list"></a>Ändra tillstånd med program listan

Ändra paket tillstånd med program listan:

1. Gå till din värd pool och välj **MSIX-paket**. Du bör se en lista över alla befintliga MSIX-paket i Host-poolen.

2. Välj de MSIX-paket vars tillstånd du vill ändra och välj sedan **ändra tillstånd**.

### <a name="change-state-with-update-package"></a>Ändra tillstånd med uppdaterings paket

Ändra paket tillstånd med ett uppdaterings paket:

1. Gå till din värd pool och välj **MSIX-paket**. Du bör se en lista över alla befintliga MSIX-paket i Host-poolen.

2. Välj namnet på det paket vars tillstånd du vill ändra från listan MSIX paket. Nu öppnas fliken **uppdaterings paket** .

3. Växla **tillstånds** växeln till antingen **inaktiv** eller **aktiv** och välj sedan **Spara.**

## <a name="change-msix-package-registration-type"></a>Ändra registrerings typ för MSIX-paket

Ändra paketets registrerings typ:

1. Välj **MSIX-paket**. Du bör se en lista över alla befintliga MSIX-paket i Host-poolen.

2. Välj **paket namn i** **rutnätet MSIX-paket** . Då öppnas bladet där du kan uppdatera paketet.

3. Växla **registrerings typen** via knappen **på begäran/logga in på spärren** efter behov och välj **Spara.**

## <a name="remove-an-msix-package"></a>Ta bort ett MSIX-paket

Ta bort ett MSIX-paket från din värd pool:

1. Välj **MSIX-paket**.  Du bör se en lista över alla befintliga MSIX-paket i Host-poolen.

2. Välj ellipsen på höger sida av namnet på det paket som du vill ta bort och välj sedan **ta bort**.

## <a name="remove-msix-apps"></a>Ta bort MSIX-appar

Ta bort enskilda MSIX-appar från paketet:

1. Gå till Host-poolen och välj **program grupper**.

2. Välj den program grupp som du vill ta bort MSIX-appar från.

3. Öppna fliken **program** .

4. Välj den app som du vill ta bort och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

Fråga våra Community-frågor om den här funktionen på [Windows-TechCommunity för virtuella datorer](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Du kan också lämna feedback för virtuella Windows-datorer i [hubben Windows Virtual Desktop feedback](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Här följer några andra artiklar som du kan ha nytta av:

- [Ord lista för MSIX-appen](app-attach-glossary.md)
- [Vanliga frågor och svar om MSIX app](app-attach-faq.md)
