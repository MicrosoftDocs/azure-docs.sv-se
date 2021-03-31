---
title: Skapa FSLogix Profile container Azure Files Active Directory Domain Services – Azure
description: Den här artikeln beskriver hur du skapar en FSLogix profil behållare med Azure Files och Azure Active Directory Domain Services.
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70a56b7efc34ba2fd3c06521c6e4cac6ea28778f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96302475"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>Skapa en profil behållare med Azure Files och Azure AD DS

I den här artikeln visas hur du skapar en FSLogix profil behållare med Azure Files och Azure Active Directory Domain Services (AD DS).

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har konfigurerat en Azure AD DS-instans. Om du inte har en ännu, följ instruktionerna i [skapa en grundläggande hanterad domän](../active-directory-domain-services/tutorial-create-instance.md) först och gå sedan tillbaka hit.

## <a name="add-azure-ad-ds-admins"></a>Lägg till Azure AD DS-administratörer

Om du vill lägga till ytterligare administratörer skapar du en ny användare och tilldelar dem behörigheter.

Så här lägger du till en administratör:

1. Välj **Azure Active Directory** på sid panelen, Välj **alla användare** och välj sedan **ny användare**.

2.  Ange användar informationen i fälten.

3. I fönstret Azure Active Directory till vänster på skärmen väljer du **grupper**.

4. Välj **Administratörs** gruppen för AAD-domänkontrollant.

5. I den vänstra rutan väljer du **medlemmar** och väljer sedan **Lägg till medlemmar** i huvud fönstret. Då visas en lista över alla användare som är tillgängliga i Azure AD. Välj namnet på den användar profil som du nyss skapade.

## <a name="set-up-an-azure-storage-account"></a>Konfigurera ett Azure Storage konto

Nu är det dags att aktivera Azure AD DS-autentisering över Server Message Block (SMB).

Så här aktiverar du autentisering:

1. Om du inte redan har gjort det konfigurerar och distribuerar du ett allmänt-syfte v2 Azure Storage-konto genom att följa anvisningarna i [skapa ett Azure Storage konto](../storage/common/storage-account-create.md).

2. När du har konfigurerat ditt konto väljer du **gå till resurs**.

3. Välj **konfiguration** i rutan till vänster på skärmen och aktivera **Azure Active Directory autentisering för Azure Files** i huvud fönstret. När du är klar väljer du **Spara**.

4. Välj **Översikt** i rutan till vänster på skärmen och välj sedan **filer** i huvud fönstret.

5. Välj **fil resurs** och ange **namn** och **kvot** i fälten som visas på höger sida av skärmen.

## <a name="assign-access-permissions-to-an-identity"></a>Tilldela åtkomst behörigheter till en identitet

Andra användare behöver åtkomst behörighet för att få åtkomst till fil resursen. För att göra detta måste du tilldela varje användare en roll med rätt åtkomst behörighet.

Så här tilldelar du användare åtkomst behörigheter:

1. Från Azure Portal öppnar du fil resursen som du skapade i [skapa ett Azure Storage-konto](#set-up-an-azure-storage-account).

2. Välj **Access Control (IAM)**.

3. Välj **Lägg till en roll tilldelning**.

4. På fliken **Lägg till roll tilldelning** väljer du lämplig inbyggd roll i listan roll. Du måste minst välja **Storage File data SMB Share Contributor** för att kontot ska få rätt behörighet.

5. För **tilldela åtkomst till** väljer du **Azure Active Directory användare, grupp eller tjänstens huvud namn**.

6. Välj ett namn eller en e-postadress för målets Azure Active Directory identitet.

7. Välj **Spara**.

## <a name="get-the-storage-account-access-key"></a>Hämta lagrings kontots åtkomst nyckel

Sedan måste du hämta åtkomst nyckeln för ditt lagrings konto.

Så här hämtar du åtkomst nyckeln för lagrings kontot:

1. Välj **lagrings konton** från Azure Portal sid panelen.

2. I listan över lagrings konton väljer du det konto som du har aktiverat Azure AD DS för och skapade de anpassade rollerna i stegen ovan.

3. Under **Inställningar** väljer du **åtkomst nycklar** och kopierar nyckeln från **KEY1**.

4. Gå till fliken **Virtual Machines** och leta upp alla virtuella datorer som kommer att bli en del av din värddator.

5. Välj namnet på den virtuella datorn (VM) under **Virtual Machines (adVM)** och välj **Anslut**

    En RDP-fil laddas ned så att du kan logga in på den virtuella datorn med sina egna autentiseringsuppgifter.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av fliken RDP i fönstret Anslut till virtuell dator.](media/rdp-tab.png)

6. När du har loggat in på den virtuella datorn kör du en kommando tolk som administratör.

7. Kör följande kommando:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
     ```

    - Ersätt `<desired-drive-letter>` med en valfri enhets beteckning (till exempel `y:` ).
    - Ersätt alla instanser av `<storage-account-name>` med namnet på det lagrings konto du angav tidigare.
    - Ersätt `<share-name>` med namnet på resursen som du skapade tidigare.
    - Ersätt `<storage-account-key>` med lagrings konto nyckeln från Azure.

    Exempel:

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Kör följande kommandon för att låta dina virtuella Windows-skrivbord skapa egna profil behållare samtidigt som du blockerar åtkomsten till profil behållarna från andra användare.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - Ersätt `<mounted-drive-letter>` med bokstaven för den enhet som du använde för att mappa enheten.
    - Ersätt `<user-email>` med UPN för den användare eller Active Directory grupp som innehåller de användare som ska ha åtkomst till resursen.

    Exempel:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>Skapa en profilcontainer

Nu när dina profiler är redo att sätta igång kan vi skapa en FSLogix profil behållare.

Så här konfigurerar du en FSLogix profil behållare:

1. Logga in på den virtuella dator som du konfigurerade i början av den här artikeln och [Ladda ned och installera FSLogix-agenten](/fslogix/install-ht/).

2. Zippa upp FSLogix agent-filen som du laddade ned och gå till **x64**  >  -**versioner** och öppna **FSLogixAppsSetup.exe**.

3. När installations programmet har startat väljer **du jag accepterar licens villkoren.** Ange en ny nyckel om det är tillämpligt.

4. Välj **installera**.

5. Öppna **enhet C** och gå sedan till **program filer**  >  **FSLogix**-  >  **appar** för att kontrol lera att FSLogix-agenten har installerats korrekt.

     >[!NOTE]
     > Om det finns flera virtuella datorer i värd gruppen måste du upprepa steg 1 till 5 för varje virtuell dator.

6. Kör **Registereditorn** (regedit) som administratör.

7. Navigera till **datorn**  >  **HKEY_LOCAL_MACHINE**  >  **program vara**  >  **FSLogix**, högerklicka på **FSLogix**, Välj **ny** och välj sedan **nyckel**.

8. Skapa en ny nyckel med namnet **profiler**.

9.  Högerklicka på **profiler**, Välj **ny** och välj sedan **DWORD (32-bitars) värde.** Ge värdet värdet **aktiverat** och ange värdet **1** för **datavärdet** .

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av profil nyckeln. REG_DWORD-filen markeras och dess data värde är inställt på 1.](media/dword-value.png)

10. Högerklicka på **profiler**, Välj **ny** och sedan **Multiple String-värde**. Namnge värdet **VHDLocations** och ange en URI för Azure Files-resursen `\\fsprofile.file.core.windows.net\share` som datavärdet.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av profil nyckeln som visar VHDLocations-filen. Dess data värde visar URI för den Azure Files resursen.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Tilldela användare till en session-värd

Nu måste du tilldela användare till din session-värd.

Så här tilldelar du användare:

1. Kör Windows PowerShell som administratör och kör sedan följande cmdlet för att logga in på Windows Virtual Desktop med PowerShell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   När du uppmanas att ange autentiseringsuppgifter anger du samma användare som har beviljats rollen TenantCreator, RDS-ägare eller RDS-deltagare på Windows-klienten för virtuella skriv bord.

2. Kör följande cmdlets för att tilldela användaren till fjärr skrivbords gruppen:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Precis som de tidigare cmdletarna, se till att ersätta `<your-wvd-tenant>` , `<wvd-pool>` och `<user-principal>` med relevanta värden.

    Exempel:

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Kontrol lera att din profil fungerar

Nu behöver du bara se till att profilen du skapade finns och fungerar som den ska.

Så här verifierar du din profil:

1. Öppna en webbläsare och gå till [Windows-webbklienten för virtuella skriv bord](https://rdweb.wvd.microsoft.com/arm/webclient).

2. Logga in med det användar konto som har tilldelats till fjärr skrivbords gruppen.

3. När användarsessionen har upprättats öppnar du Azure Portal och loggar in med ett administratörs konto.

4. Välj **lagrings konton** från sido panelen.

5. Välj det lagrings konto som du konfigurerade som fil resurs för din sessions värd pool och aktive rad med Azure AD DS.

6. Välj ikonen **filer** och expandera sedan resursen.

    Om allt är korrekt konfigurerat bör du se en **katalog** med ett namn som är formaterat så här: `<user SID>-<username>` .

## <a name="next-steps"></a>Nästa steg

Om du vill ha alternativa sätt att skapa FSLogix profil behållare kan du läsa följande artiklar:

- [Skapa en profil behållare för en värdbaserad pool med en fil resurs](create-host-pools-user-profile.md).
- [Skapa en FSLogix profil behållare för en värd-pool med hjälp av Azure NetApp Files](create-fslogix-profile-container.md)

Du hittar mer detaljerad information om begrepp som rör FSlogix-behållare för Azure Files i [FSlogix profil behållare och Azure Files](fslogix-containers-azure-files.md).
