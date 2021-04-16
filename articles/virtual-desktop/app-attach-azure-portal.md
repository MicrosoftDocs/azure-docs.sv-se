---
title: Windows Virtual Desktop msix-app–portal – Azure
description: Konfigurera MSIX-app bifoga för Windows Virtual Desktop med hjälp av Azure Portal.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 21dbab6c8d4fb12fe79434a6994dd7f5b8a49190
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502716"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Konfigurera MSIX-appbifogning med Azure Portal

Den här artikeln beskriver hur du ställer in MSIX-app bifoga i en Windows Virtual Desktop miljö.

## <a name="requirements"></a>Krav

Det här behöver du för att konfigurera MSIX-app bifoga:

- En fungerande Windows Virtual Desktop distribution. Information om hur du distribuerar Windows Virtual Desktop (klassisk) finns [i Skapa en klientorganisation i Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Information om hur du distribuerar Windows Virtual Desktop med Azure Resource Manager-integrering finns i Skapa en [värdpool med Azure Portal](./create-host-pools-azure-marketplace.md).
- En Windows Virtual Desktop värdpool med minst en aktiv sessionsvärd.
- MSIX-paketeringsverktyget.
- Ett MSIX-paketerat program expanderat till en MSIX-avbildning som laddas upp till en filresurs.
- En filresurs i Windows Virtual Desktop distribution där MSIX-paketet ska lagras.
- Filresursen där du laddade upp MSIX-avbildningen måste också vara tillgänglig för alla virtuella datorer (VM) i värdpoolen. Användarna behöver skrivskyddade behörigheter för att få åtkomst till avbildningen.
- Om certifikatet inte är offentligt betrott följer du anvisningarna i [Installera certifikat](app-attach.md#install-certificates).

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Inaktivera automatiska uppdateringar för MSIX-apptillämpningar

Innan du börjar måste du inaktivera automatiska uppdateringar för att koppla program till MSIX-appar. Om du vill inaktivera automatiska uppdateringar måste du köra följande kommandon i en upphöjd kommandotolk:

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
>Vi rekommenderar att du startar om den virtuella datorn när du har aktivera Hyper-V.

## <a name="configure-the-msix-app-attach-management-interface"></a>Konfigurera hanteringsgränssnittet för att koppla MSIX-appen

Därefter måste du ladda ned och konfigurera hanteringsgränssnittet för hantering av MSIX-appen för Azure Portal.

Konfigurera hanteringsgränssnittet:

1. [Öppna Azure Portal](https://portal.azure.com).
2. Om du får en fråga som frågar om du anser att tillägget är tillförlitligt väljer du **Tillåt**.

      > [!div class="mx-imgBorder"]
      > ![En skärmbild av fönstret ej betrodda tillägg. "Tillåt" är markerat i rött.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Lägga till en MSIX-avbildning i värdpoolen

Därefter måste du lägga till MSIX-avbildningen i värdpoolen.

Så här lägger du till MSIX-avbildningen:

1. Öppna Azure-portalen.

2. Ange **Windows Virtual Desktop** i sökfältet och välj sedan namnet på tjänsten.

      > [!div class="mx-imgBorder"]
      > ![En skärmbild av en användare som Windows Virtual Desktop "Windows Virtual Desktop" i sökfältets nedrullningsbar meny i Azure Portal. "Windows Virtual Desktop" är markerat i rött.](media/find-and-select.png)

3. Välj den värdpool där du planerar att placera MSIX-apparna.

4. Välj **MSIX-paket** för att öppna datarutnätet med alla **MSIX-paket som** för närvarande har lagts till i värdpoolen.

5. Välj **+ Lägg till** för att öppna fliken Lägg till **MSIX-paket.**

6. På fliken **Lägg till MSIX-paket** anger du följande värden:

      - För **MSIX-avbildningssökväg** anger du en giltig UNC-sökväg som pekar på MSIX-avbildningen på filresursen. (Till exempel `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` .) När du är klar väljer du Lägg **till för** att fråga MSIX-containern för att kontrollera om sökvägen är giltig.

      - För **MSIX-paket** väljer du relevant MSIX-paketnamn i listrutan. Den här menyn fylls bara i om du har angett en giltig avbildningssökväg i **MSIX-avbildningssökvägen**.

      - För **Paketprogram** kontrollerar du att listan innehåller alla MSIX-program som du vill ska vara tillgängliga för användare i ditt MSIX-paket.

      - Du kan också ange ett **Visningsnamn** om du vill att paketet ska ha ett mer användarvänligt i dina användardistributioner.

      - Kontrollera att **Versionen** har rätt versionsnummer.

      - Välj den **registreringstyp** som du vill använda. Vilken du använder beror på dina behov:

    - **Registrering på begäran skjuter** upp den fullständiga registreringen av MSIX-programmet tills användaren startar programmet. Det här är den registreringstyp som vi rekommenderar att du använder.

    - **Inloggningen blockerar** endast registreringar när användaren loggar in. Vi rekommenderar inte den här typen eftersom den kan leda till längre inloggningstider för användarna.

7.  För **Delstat** väljer du önskat tillstånd.
    -  Med **statusen** Aktiv kan användarna interagera med paketet.
    -  Statusen **Inaktiv** gör att Windows Virtual Desktop ignorerar paketet och inte levererar det till användarna.

8. När du är klar väljer du Lägg **till**.

## <a name="publish-msix-apps-to-an-app-group"></a>Publicera MSIX-appar till en appgrupp

Därefter måste du publicera apparna i paketet. Du måste göra detta för både programgrupper för skrivbordsprogram och fjärrappar.

Om du redan har en MSIX-avbildning kan du gå vidare [till Publicera MSIX-appar till en appgrupp.](#publish-msix-apps-to-an-app-group) Om du vill testa äldre program följer du anvisningarna i Skapa ett [MSIX-paket](/windows/msix/packaging-tool/create-app-package-msi-vm/) från ett installationsprogram på en virtuell dator för att konvertera det äldre programmet till ett MSIX-paket.

Så här publicerar du apparna:

1. I den Windows Virtual Desktop resursprovidern väljer du **fliken Programgrupper.**

2. Välj den programgrupp som du vill publicera apparna till.

   >[!NOTE]
   >MSIX-program kan levereras med MSIX-appen ansluts till både fjärrapp- och skrivbordsappgrupper

3. När du är i appgruppen väljer du **fliken** Program. **Rutnätet** Program visar alla befintliga appar i appgruppen.

4. Välj **+ Lägg till** för att öppna fliken Lägg **till** program.

      > [!div class="mx-imgBorder"]
      > ![En skärmbild av användaren som väljer + Lägg till för att öppna fliken Lägg till program](media/select-add.png)

5. För **Programkälla** väljer du källa för ditt program.
    - Om du använder en skrivbordsappgrupp väljer du **MSIX-paket**.
      
      > [!div class="mx-imgBorder"]
      > ![En skärmbild av en kund som väljer MSIX-paket från den nedrullningsna menyn programkälla. MSIX-paketet är markerat i rött.](media/select-source.png)
    
    - Om du använder en fjärrappgrupp väljer du något av följande alternativ:
        
        - Start-menyn
        - Appsökväg
        - MSIX-paket

    - I **Programnamn** anger du ett beskrivande namn för programmet.

    Du kan också konfigurera följande valfria funktioner:
   
    - I **Visningsnamn** anger du ett nytt namn för det paket som användarna ser.

    - I **Beskrivning** anger du en kort beskrivning av appaketet.

    - Om du använder en fjärrappgrupp kan du också konfigurera följande alternativ:

        - **Ikonsökväg**
        - **Ikonindex**
        - **Visa i webbfeed**

6. När du är klar väljer du **Spara**.

>[!NOTE]
>När en användare tilldelas till en fjärrappgrupp och en skrivbordsappgrupp från samma värdpool visas skrivbordsappgruppen i flödet.

## <a name="assign-a-user-to-an-app-group"></a>Tilldela en användare till en appgrupp

När du har tilldelar MSIX-appar till en appgrupp måste du ge användarna åtkomst till dem. Du kan tilldela åtkomst genom att lägga till användare eller användargrupper i en appgrupp med publicerade MSIX-program. Följ anvisningarna i [Hantera appgrupper med Azure Portal](manage-app-groups.md) för att tilldela användarna till en appgrupp.

>[!NOTE]
>MSIX-appens anslutning av fjärrappar kan försvinna från flödet när du testar fjärrappar under den offentliga förhandsversionen. Apparna visas inte eftersom värdpoolen som du använder i utvärderingsmiljön betjänas av en koordinator för fjärrskrivbord i produktionsmiljön. Eftersom RD Broker i produktionsmiljön inte registrerar förekomsten av ATT MSIX-appen kopplar fjärrappar visas inte apparna i flödet.

## <a name="change-msix-package-state"></a>Ändra MSIX-pakettillstånd

Därefter måste du ändra MSIX-pakettillståndet till antingen **Aktiv** eller **Inaktiv**, beroende på vad du vill göra med paketet. Aktiva paket är paket som användarna kan interagera med när de har publicerats. Inaktiva paket ignoreras av Windows Virtual Desktop, så användarna kan inte interagera med apparna i dem.

### <a name="change-state-with-the-applications-list"></a>Ändra tillstånd med programlistan

Så här ändrar du pakettillståndet med programlistan:

1. Gå till värdpoolen och välj **MSIX-paket.** Du bör se en lista över alla befintliga MSIX-paket i värdpoolen.

2. Välj de MSIX-paket vars tillstånd du behöver ändra och välj sedan **Ändra tillstånd.**

### <a name="change-state-with-update-package"></a>Ändra tillstånd med uppdateringspaket

Så här ändrar du pakettillståndet med ett uppdateringspaket:

1. Gå till din värdpool och välj **MSIX-paket.** Du bör se en lista över alla befintliga MSIX-paket i värdpoolen.

2. Välj namnet på det paket vars tillstånd du vill ändra från MSIX-paketlistan. Då öppnas fliken **Uppdateringspaket.**

3. Växla **tillståndsväxeln** till antingen **Inaktiv** **eller Aktiv** och välj sedan **Spara.**

## <a name="change-msix-package-registration-type"></a>Ändra msix-paketregistreringstyp

Så här ändrar du paketets registreringstyp:

1. Välj **MSIX-paket**. Du bör se en lista över alla befintliga MSIX-paket i värdpoolen.

2. Välj **Paketnamn i** **msix-paketrutnätet.** Då öppnas bladet för att uppdatera paketet.

3. Växla **Registreringstyp** via knappen **På begäran/Logga in blockering efter** behov och välj **Spara.**

## <a name="remove-an-msix-package"></a>Ta bort ett MSIX-paket

Så här tar du bort ett MSIX-paket från värdpoolen:

1. Välj **MSIX-paket**.  Du bör se en lista över alla befintliga MSIX-paket i värdpoolen.

2. Välj ellipsen på höger sida av namnet på det paket som du vill ta bort och välj sedan Ta **bort**.

## <a name="remove-msix-apps"></a>Ta bort MSIX-appar

Så här tar du bort enskilda MSIX-appar från paketet:

1. Gå till värdpoolen och välj **Programgrupper.**

2. Välj den programgrupp som du vill ta bort MSIX-appar från.

3. Öppna **fliken** Program.

4. Välj den app som du vill ta bort och välj sedan **Ta bort.**

## <a name="next-steps"></a>Nästa steg

Ställ våra communityfrågor om den här funktionen på [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Du kan också lämna feedback för Windows Virtual Desktop på Windows Virtual Desktop [feedbackhubben](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Här är några andra artiklar som kan vara till hjälp:

- [Bifoga MSIX-appordlistor](app-attach-glossary.md)
- [Vanliga frågor och svar om att bifoga MSIX-appar](app-attach-faq.md)
