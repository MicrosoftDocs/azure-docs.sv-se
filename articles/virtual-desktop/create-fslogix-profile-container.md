---
title: FSLogix profil behållare NetApp Windows Virtual Desktop – Azure
description: Så här skapar du en FSLogix profil behållare med hjälp av Azure NetApp Files i det virtuella Windows-skrivbordet.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6a9f2c62d8e7f17f6ea8377982c79fef3dfbb97c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016836"
---
# <a name="create-a-profile-container-with-azure-netapp-files-and-ad-ds"></a>Skapa en profil behållare med Azure NetApp Files och AD DS

Vi rekommenderar att du använder FSLogix profil behållare som en användar profils lösning för [Windows Virtual Desktop-tjänsten](overview.md). FSLogix profil behållare lagrar en fullständig användar profil i en enda behållare och är utformad för att ge centrala profiler i icke-beständiga data behandlings miljöer som Windows Virtual Desktop. När du loggar in ansluter behållaren dynamiskt till dator miljön med hjälp av en virtuell hård disk som stöds lokalt och en virtuell hård disk för Hyper-V (VHDX). Med dessa avancerade filter driv rutins tekniker kan användar profilen vara omedelbart tillgänglig och visas i systemet precis som en lokal användar profil. Mer information om FSLogix profil behållare finns i [FSLogix profil behållare och Azure Files](fslogix-containers-azure-files.md).

Du kan skapa FSLogix profil behållare med hjälp av [Azure NetApp Files](https://azure.microsoft.com/services/netapp/), en lättanvänd Azure Native Platform-tjänst som hjälper kunder att snabbt och tillförlitligt tillhandahålla SMB-volymer i företags klass för sina Windows-miljöer för virtuella datorer. Mer information om Azure NetApp Files finns i [Vad är Azure NetApp Files?](../azure-netapp-files/azure-netapp-files-introduction.md)

I den här guiden visas hur du konfigurerar ett Azure NetApp Files konto och skapar FSLogix profil behållare i Windows Virtual Desktop.

Den här artikeln förutsätter att du redan har konfigurerat [lagringspooler](create-host-pools-azure-marketplace.md) och grupper ATS i en eller flera klienter i din Windows-miljö för virtuella datorer. Information om hur du konfigurerar klient organisationer finns i [skapa en klient i det virtuella Windows-skrivbordet](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) och [vårt tekniska blogg](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)inlägg.

Anvisningarna i den här hand boken är specifika för användare av virtuella Windows-datorer. Om du vill ha mer allmän vägledning om hur du konfigurerar Azure NetApp Files och skapar FSLogix profil behållare utanför Windows Virtual Desktop, kan du läsa snabb starten för att [skapa Azure NetApp Files och skapa en NFS-volym](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>Den här artikeln beskriver inte metod tips för att skydda åtkomsten till Azure NetApp Filess resursen.

>[!NOTE]
>Om du letar efter jämförelse material om de olika lagrings alternativen för FSLogix Profile container på Azure, se [lagrings alternativ för FSLogix profil behållare](store-fslogix-profile.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du kan skapa en FSLogix profil behållare för en adresspool måste du:

- Konfigurera och konfigurera virtuellt Windows-skrivbord
- Etablera en Windows-pool för virtuella skriv bord
- [Aktivera din Azure NetApp Files-prenumeration](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Konfigurera ditt Azure NetApp Files-konto

För att komma igång måste du konfigurera ett Azure NetApp Files-konto.

1. Logga in på [Azure-portalen](https://portal.azure.com). Kontrol lera att ditt konto har behörighet som deltagare eller administratör.

2. Välj **Azure Cloud Shell ikonen** till höger om Sök fältet för att öppna Azure Cloud Shell.

3. När Azure Cloud Shell är öppen väljer du **PowerShell**.

4. Om det här är första gången du använder Azure Cloud Shell skapar du ett lagrings konto i samma prenumeration som du behåller Azure NetApp Files och det virtuella Windows-skrivbordet.

   > [!div class="mx-imgBorder"]
   > ![I fönstret lagrings konto med knappen Skapa lagring längst ned i fönstret markerat i rött.](media/create-storage-button.png)

5. Kör följande två cmdlets när Azure Cloud Shell har lästs in.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. Välj **alla tjänster** på vänster sida i fönstret. Ange **Azure NetApp Files** i sökrutan som visas överst på menyn.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av en användare som anger "Azure NetApp Files" i sökrutan alla tjänster. Sök resultatet visar Azure NetApp Files resursen.](media/azure-netapp-files-search-box.png)


7. Välj **Azure NetApp Files** i Sök resultaten och välj sedan **skapa**.

8. Välj knappen **Lägg till**.
9. När fliken **nytt NetApp-konto** öppnas anger du följande värden:

    - I **namn** anger du namnet på NetApp-kontot.
    - För **prenumeration** väljer du prenumerationen för det lagrings konto som du ställer in i steg 4 i den nedrullningsbara menyn.
    - För **resurs grupp** väljer du antingen en befintlig resurs grupp på den nedrullningsbara menyn eller skapar en ny genom att välja **Skapa ny**.
    - För **plats** väljer du regionen för ditt NetApp-konto på den nedrullningsbara menyn. Den här regionen måste vara samma region som dina sessions värdar för virtuella datorer.

   >[!NOTE]
   >Azure NetApp Files stöder för närvarande inte montering av en volym i flera regioner.

10. När du är klar väljer du **skapa** för att skapa ditt NetApp-konto.

## <a name="create-a-capacity-pool"></a>Skapa en pool för kapacitet

Skapa sedan en ny kapacitets grupp:

1. Gå till Azure NetApp Files-menyn och välj ditt nya konto.
2. I menyn konto väljer du **kapacitets grupper** under lagrings tjänsten.
3. Välj **Lägg till pool**.
4. När fliken **ny pool för kapacitet** öppnas anger du följande värden:

    - I **namn** anger du ett namn för den nya kapacitets gruppen.
    - För **service nivå** väljer du önskat värde på den nedrullningsbara menyn. Vi rekommenderar **Premium** för de flesta miljöer.
       >[!NOTE]
       >Premium-inställningen ger det lägsta data flöde som är tillgängligt för en Premium service-nivå, som är 256 Mbit/s. Du kan behöva justera data flödet för en produktions miljö. Slutligt data flöde baseras på relationen som beskrivs i [data flödes gränser](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - I **storlek (TIB)** anger du den storlek på kapacitets bassänger som bäst passar dina behov. Den minsta storleken är 4 TiB.

5. Välj **OK** när du är klar.

## <a name="join-an-active-directory-connection"></a>Anslut till en Active Directory anslutning

Därefter måste du ansluta till en Active Directory-anslutning.

1. Välj **Active Directory anslutningar** på menyn till vänster på sidan och välj sedan knappen **Anslut** för att öppna sidan **Anslut Active Directory** .

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av menyn Anslut till Active Directory anslutningar.](media/active-directory-connections-menu.png)

2. Ange följande värden på sidan **anslut Active Directory** för att ansluta till en anslutning:

    - För **primär DNS** anger du IP-adressen för DNS-servern i din miljö som kan matcha domän namnet.
    - För **domän** anger du det fullständigt kvalificerade domän namnet (FQDN).
    - För **SMB server-prefix (dator konto)** anger du den sträng som du vill lägga till i datorns konto namn.
    - För **användar** namn anger du namnet på det konto som har behörighet att utföra domän anslutning.
    - För **lösen ord** anger du kontots lösen ord.

## <a name="create-a-new-volume"></a>Skapa en ny volym

Därefter måste du skapa en ny volym.

1. Välj **volymer** och välj sedan **Lägg till volym**.

2. När fliken **skapa en volym** öppnas anger du följande värden:

    - Ange ett namn på den nya volymen för **volym namn**.
    - För **kapacitets grupp** väljer du den kapacitets uppsättning som du nyss skapade på den nedrullningsbara menyn.
    - För **kvot (GIB)** anger du den volym storlek som passar din miljö.
    - För **virtuellt nätverk** väljer du ett befintligt virtuellt nätverk som har anslutning till domänkontrollanten från den nedrullningsbara menyn.
    - Under **undernät** väljer du **Skapa nytt**. Tänk på att det här under nätet ska delegeras till Azure NetApp Files.

3.  Välj **Nästa: protokoll \> \>** för att öppna fliken protokoll och konfigurera dina volym åtkomst parametrar.

## <a name="configure-volume-access-parameters"></a>Konfigurera parametrar för volym åtkomst

När du har skapat volymen konfigurerar du parametrarna för volym åtkomst.

1.  Välj **SMB** som protokoll typ.
2.  Under konfiguration i den nedrullningsbara menyn **Active Directory** väljer du samma katalog som du ursprungligen anslöt i [ansluta till en Active Directory anslutning](create-fslogix-profile-container.md#join-an-active-directory-connection). Tänk på att det finns en gräns på en Active Directory per prenumeration.
3.  I text rutan **resurs namn** anger du namnet på den resurs som används av Session Host-poolen och dess användare.

4.  Välj **Granska + skapa** längst ned på sidan. Validerings sidan öppnas. När du har verifierat volymen väljer du **skapa**.

5.  Den nya volymen kommer nu att börja distribueras. När distributionen är klar kan du använda Azure NetApp Files-resursen.

6.  Om du vill se monterings Sök vägen väljer du **gå till resurs** och letar efter den på fliken Översikt.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av översikts skärmen med en röd pil som pekar på monterings banan.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Konfigurera FSLogix på sessioner som är värdar för virtuella datorer (VM)

Det här avsnittet baseras på [skapa en profil behållare för en värdbaserad pool med hjälp av en fil resurs](create-host-pools-user-profile.md).

1. [Ladda ned FSLogix agent. zip-filen](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) när du fortfarande är fjärran sluten till den virtuella sessionen.

2. Zippa upp den hämtade filen.

3. I filen går du till **x64**-  >  **versioner** och kör **FSLogixAppsSetup.exe**. Installations menyn öppnas.

4.  Om du har en produkt nyckel anger du den i text rutan produkt nyckel.

5. Markera kryss rutan bredvid **Jag accepterar licens villkoren**.

6. Välj **installera**.

7. Gå till **C: \\ Program Files \\ FSLogix- \\ appar** för att bekräfta att agenten är installerad.

8. Från Start-menyn kör du **regedit** som administratör.

9. Navigera till **datorn \\ HKEY_LOCAL_MACHINE \\ program vara \\ FSLogix**.

10. Skapa en nyckel med namnet **profiler**.

11.  Skapa ett värde med namnet **Enabled** med en **REG_DWORD** -typ inställd på värdet **1**.

12. Skapa ett värde med namnet **VHDLocations** med en **multisträng-** typ och ange dess datavärde till URI: n för den Azure NetApp Files resursen.

13. Skapa ett värde med namnet **DeleteLocalProfileWhenVHDShouldApply** med DWORD-värdet 1 för att undvika problem med befintliga lokala profiler innan du loggar in.

     >[!WARNING]
     >Var försiktig när du skapar DeleteLocalProfileWhenVHDShouldApply-värdet. När FSLogix Profiles-systemet bestämmer att en användare ska ha en FSLogix-profil, men det redan finns en lokal profil, tar profil containern bort den lokala profilen permanent. Användaren loggas sedan in med den nya FSLogix-profilen.

## <a name="assign-users-to-session-host"></a>Tilldela användare till Session Host

1. Öppna **POWERSHELL ISE** som administratör och logga in på det virtuella Windows-skrivbordet.

2. Kör följande cmdlets:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. När du uppmanas att ange autentiseringsuppgifter anger du autentiseringsuppgifterna för användaren med rollerna skapare eller fjärr skrivbords ägare/RDS-deltagare på Windows-klienten för virtuella skriv bord.

4. Kör följande cmdlets för att tilldela en användare till en fjärr skrivbords grupp:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Se till att användarna kan komma åt Azure-NetApp fil resurs

1. Öppna webbläsaren och gå till <https://rdweb.wvd.microsoft.com/arm/webclient> .

2. Logga in med autentiseringsuppgifterna för en användare som har tilldelats till fjärr skrivbords gruppen.

3. När du har upprättat användarsessionen loggar du in på Azure Portal med ett administratörs konto.

4. Öppna **Azure NetApp Files**, välj ditt Azure NetApp Files-konto och välj sedan **volymer**. När menyn volymer öppnas väljer du motsvarande volym.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av NetApp-kontot som du ställer in tidigare i Azure Portal med knappen volymer valt.](media/netapp-account.png)

5. Gå till fliken **Översikt** och bekräfta att FSLogix profil container använder utrymme.

6. Anslut direkt till en virtuell dator del av den värdbaserade poolen med hjälp av fjärr skrivbord och öppna **Utforskaren.** Navigera sedan till **monterings Sök vägen** (i följande exempel är monterings sök vägen \\ \\ ANF-SMB-3863.gt1107.onmicrosoft.com \\ ANF-VOL).

   I den här mappen bör det finnas en profil-VHD (eller VHDX) som den som finns i följande exempel.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av innehållet i mappen i monterings Sök vägen. Inuti är en enda VHD-fil med namnet "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Nästa steg

Du kan använda FSLogix profil behållare för att konfigurera en användar profil resurs. Information om hur du skapar användar profil resurser med dina nya behållare finns i [skapa en profil behållare för en värd-pool med en fil resurs](create-host-pools-user-profile.md).

Du kan också skapa en Azure Files fil resurs för att lagra din FSLogix-profil i. Mer information finns i [skapa en Azure Files fil resurs med en](create-file-share.md)domänkontrollant.