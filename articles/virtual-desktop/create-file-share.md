---
title: Skapa en Azure Files fil resurs med en domänkontrollant – Azure
description: Konfigurera en FSLogix profil behållare på en Azure-filresurs i en befintlig Windows-adresspool för virtuella skriv bord med din Active Directory-domän.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8f8086aced26fc46fb1430df074082e8c3365baa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746821"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>Skapa en profil behållare med Azure Files och AD DS

I den här artikeln får du lära dig hur du skapar en Azure-filresurs som autentiseras av en domänkontrollant på en befintlig Windows-pool för virtuella skriv bord. Du kan använda den här fil resursen för att lagra lagrings profiler.

Den här processen använder Active Directory Domain Services (AD DS), som är en lokal katalog tjänst. Om du letar efter information om hur du skapar en FSLogix profil behållare med Azure AD DS, se [skapa en FSLogix profil behållare med Azure Files](create-profile-container-adds.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att domänkontrollanten är synkroniserad med Azure och kan matchas från det virtuella Azure-nätverket (VNET) som värdarna för din session är anslutna till.

## <a name="set-up-a-storage-account"></a>Konfigurera ett lagrings konto

Först måste du konfigurera ett Azure Files lagrings konto.

Så här konfigurerar du ett lagrings konto:

1. Logga in på Azure-portalen.

2. Sök efter **lagrings konto** i Sök fältet.

3. Välj **+Lägg till**.

4. Ange följande information på sidan  **skapa lagrings konto** :

    - Skapa en ny resursgrupp.
    - Ange ett unikt namn för lagringskontot.
    - För **plats** rekommenderar vi att du väljer samma plats som Windows-adresspoolen för virtuella skriv bord.
    - För **Prestanda** väljer du **Standard**. (Beroende på dina IOPS-krav. Mer information finns i [lagrings alternativ för FSLogix profil behållare i Windows Virtual Desktop](store-fslogix-profile.md).)
    - För **Kontotyp** väljer du **StorageV2** eller **FileStorage** (endast tillgängligt om prestanda nivån är Premium).
    - För **replikering** väljer du **lokalt REDUNDANT lagring (LRS)**.

5. När du är klar väljer du **Granska + skapa** och väljer sedan **skapa**.

Om du behöver mer detaljerade konfigurations anvisningar, se [regional tillgänglighet](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability).

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Därefter måste du skapa en Azure-filresurs.

Så här skapar du en filresurs:

1. Välj **Gå till resurs**.

2. På sidan Översikt väljer du **Filresurser**.

3. Välj **+ fil resurser**, skapa en ny fil resurs med namnet **profiler**, ange sedan en lämplig kvot eller lämna fältet tomt utan kvot.

4. Välj **Skapa**.

## <a name="enable-active-directory-authentication"></a>Aktivera Active Directory autentisering

Därefter måste du aktivera autentisering med Active Directory (AD). Om du vill aktivera den här principen måste du följa det här avsnittets instruktioner på en dator som redan är domänansluten. Aktivera autentisering genom att följa dessa anvisningar på den virtuella datorn som kör domänkontrollanten:

1. Remote Desktop Protocol till den domänanslutna virtuella datorn.

2. Följ anvisningarna i [Aktivera AD DS-autentisering för Azure-filresurser](../storage/files/storage-files-identity-ad-ds-enable.md) för att installera AzFilesHybrid-modulen och aktivera autentisering.

3.  Öppna Azure Portal, öppna ditt lagrings konto, Välj **konfiguration** och bekräfta **Active Directory (AD)** är inställt på **aktive rad**.

     > [!div class="mx-imgBorder"]
     > ![En skärm bild av konfigurations sidan med Azure Active Directory (AD) aktive rad.](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-windows-virtual-desktop-users"></a>Tilldela Azure RBAC-behörigheter till virtuella Windows-skrivbordet användare

Alla användare som behöver ha FSLogix profiler som lagras på lagrings kontot måste tilldelas rollen lagrings fil data SMB Share Contributor.

Användare som loggar in på Windows Virtual Desktop-sessionsvärdarna behöver åtkomstbehörigheter för att komma åt din filresurs. Beviljande av åtkomst till en Azure-filresurs omfattar konfiguration av behörigheter på både resursnivå och NTFS-nivå, vilket liknar en traditionell Windows-resurs.

Om du vill konfigurera behörigheter på resurs nivå tilldelar du varje användare en roll med rätt åtkomst behörighet. Behörigheter kan tilldelas till antingen enskilda användare eller en Azure AD-grupp. Läs mer i [tilldela åtkomst behörigheter till en identitet](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

>[!NOTE]
>De konton eller grupper som du tilldelar behörigheter bör ha skapats i domänen och synkroniserats med Azure AD. Konton som skapats i Azure AD fungerar inte.

Så här tilldelar du Azure-rollbaserad åtkomst kontroll (Azure RBAC) behörigheter:

1. Öppna Azure-portalen.

2. Öppna det lagrings konto som du skapade i [Konfigurera ett lagrings konto](#set-up-a-storage-account).

3. Välj **fil resurser** och välj sedan namnet på den fil resurs som du planerar att använda.

4. Välj **Access Control (IAM)**.

5. Välj **Lägg till en roll tilldelning**.

6. På fliken **Lägg till roll tilldelning** väljer du **Storage File data SMB resurs upphöjt bidrags givare** för administratörs kontot.

     Följ samma instruktioner för att tilldela användare behörigheter för deras FSLogix-profiler. Men när du går till steg 5, väljer du **Storage File data SMB Share Contributor** i stället.

7. Välj **Spara**.

## <a name="assign-users-permissions-on-the-azure-file-share"></a>Tilldela användare behörigheter på Azure-filresursen

När du har tilldelat Azure RBAC-behörigheter till dina användare måste du konfigurera NTFS-behörigheterna.

Du behöver veta två saker från Azure Portal för att komma igång:

- UNC-sökvägen.
- Nyckeln till lagringskontot.

### <a name="get-the-unc-path"></a>Hämta UNC-sökvägen

Så här hämtar du UNC-sökvägen:

1. Öppna Azure-portalen.

2. Öppna det lagrings konto som du skapade i [Konfigurera ett lagrings konto](#set-up-a-storage-account).

3. Välj **Inställningar** och välj sedan **Egenskaper**.

4. Kopiera den **primära fil tjänstens slut punkts-** URI till valfri text redigerare.

5. När du har kopierat URI: n, gör du följande saker för att ändra den till UNC:

    - Ta bort `https://` och Ersätt med `\\`
    - Ersätt snedstrecket `/` med ett omvänt snedstreck `\` .
    - Lägg till namnet på fil resursen som du skapade i [skapa en Azure-filresurs](#create-an-azure-file-share) i slutet av UNC-filen.

        Exempelvis: `\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>Hämta lagringskontonyckeln

Hämta lagrings konto nyckeln:

1. Öppna Azure-portalen.

2. Öppna det lagrings konto som du skapade i [Konfigurera ett lagrings konto](#set-up-a-storage-account).

3. På fliken **lagrings konto** väljer du **åtkomst nycklar**.

4. Kopiera **KEY1** eller **key2** till en fil på den lokala datorn.

### <a name="configure-ntfs-permissions"></a>Konfigurera NTFS-behörigheter

Så här konfigurerar du NTFS-behörigheter:

1. Öppna en kommando tolk på en domänansluten virtuell dator.

2. Kör följande kommando för att montera Azure-filresursen och tilldela den en enhets beteckning:

     ```cmd
     net use <desired-drive-letter>: <UNC-path> <SA-key> /user:Azure\<SA-name>
     ```

3. Kör följande kommando för att granska åtkomst behörigheterna till Azure-fil resursen:

    ```cmd
    icacls <mounted-drive-letter>:
    ```

    Ersätt `<mounted-drive-letter>` med bokstaven för den enhet som du mappade till.

    Både *NT Instans\Autentiserade-användare* och *BUILTIN\Users* har vissa behörigheter som standard. Dessa standard behörigheter gör att dessa användare kan läsa andra användares profil behållare. Behörigheterna som beskrivs i [Konfigurera lagrings behörigheter för användning med profil behållare och Office-behållare](/fslogix/fslogix-storage-config-ht) låter dock inte användarna läsa de andra "profil behållarna.

4. Kör följande kommandon för att låta dina virtuella Windows-skrivbord skapa egna profil behållare samtidigt som du blockerar åtkomsten till deras profil behållare från andra användare.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - Ersätt <monterade enhets brev> med bokstaven för den enhet som du använde för att mappa enheten.
     - Ersätt <användar-e-> med UPN för den användare eller Active Directory grupp som innehåller de användare som behöver åtkomst till resursen.

     Exempel:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>Konfigurera FSLogix på en session som är värd för virtuella datorer

I det här avsnittet visas hur du konfigurerar en virtuell dator med FSLogix. Du behöver följa de här anvisningarna varje gång du konfigurerar en sessionsvärd. Innan du börjar konfigurera följer du anvisningarna i [Hämta och installera FSLogix](/fslogix/install-ht). Det finns flera tillgängliga alternativ som säkerställer att registernycklarna anges på alla sessionsvärdar. Du kan ange dessa alternativ i en avbildning eller konfigurera en grupprincip.

Så här konfigurerar du FSLogix på den virtuella datorn för sessionsvärd:

1. RDP till sessionen som är värd för den virtuella Windows-poolen för virtuella skriv bord.

2. [Hämta och installera FSLogix](/fslogix/install-ht).

5. Följ instruktionerna i [Konfigurera registerinställningar för profilcontainer](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings):

    - Navigera till **datorn**  >  **HKEY_LOCAL_MACHINE**  >  **program vara**  >  **FSLogix**.

    - Skapa en **profil** nyckel.

    - Skapa **aktiverat, DWORD** med värdet 1.

    - Skapa **VHDLocations, MULTI_SZ**.

    - Ange värdet för **VHDLocations** till den UNC-sökväg som du genererade i [Hämta UNC-sökvägen](#get-the-unc-path).

6. Starta om den virtuella datorn.

## <a name="testing"></a>Testning

När du har installerat och konfigurerat FSLogix kan du testa distributionen genom att logga in med ett användar konto som har tilldelats en app-grupp eller ett skriv bord på den aktuella värddatorn. Se till att det användar konto du loggar in med har behörighet för fil resursen.

Om användaren har loggat in tidigare har de en befintlig lokal profil som ska användas under den här sessionen. Undvik att skapa en lokal profil genom att antingen skapa ett nytt användar konto som ska användas för test eller använda de konfigurations metoder som beskrivs i [själv studie kursen: Konfigurera profil behållare för att omdirigera användar profiler](/fslogix/configure-profile-container-tutorial/).

Kontrol lera dina behörigheter för din session:

1. Starta en session på det virtuella Windows-skrivbordet.

2. Öppna Azure-portalen.

3. Öppna det lagrings konto som du skapade i [Konfigurera ett lagrings konto](#set-up-a-storage-account).

4. Välj **skapa en resurs** på sidan Skapa en Azure-filresurs.

5. Kontrol lera att det finns en mapp som innehåller användar profilen i filerna.

Om du behöver ytterligare testning följer du anvisningarna i [kontrol lera att din profil fungerar](create-profile-container-adds.md#make-sure-your-profile-works).

## <a name="next-steps"></a>Nästa steg

Information om hur du felsöker FSLogix finns i [den här fel söknings guiden](/fslogix/fslogix-trouble-shooting-ht).
