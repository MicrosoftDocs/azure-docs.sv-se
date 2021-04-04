---
title: 'Självstudie: federera en enda AD-skogs miljö till Azure | Microsoft Docs'
description: Visar hur du ställer in en hybrididentitetsmiljö med federation.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/16/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4410708905610562feb15804277021950eb1edeb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96176298"
---
# <a name="tutorial-federate-a-single-ad-forest-environment-to-the-cloud"></a>Självstudie: federera en enda AD-skogs miljö i molnet

![Skapa](media/tutorial-federation/diagram.png)

Självstudien vägleder dig genom att skapa en hybrididentitetsmiljö med federation.  Du kan sedan använda miljön för att testa eller bli mer bekant med hur en hybrididentitet fungerar.

## <a name="prerequisites"></a>Förutsättningar
Följande är förutsättningar som krävs för den här självstudien
- En dator med [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) installerat.  Vi rekommenderar att du gör detta på en dator med antingen [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) eller [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- En [Azure-prenumeration](https://azure.microsoft.com/free)
- - Ett [externt nätverkskort](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) så att den virtuella datorn kan kommunicera med Internet.
- En kopia av Windows Server 2016
- En [anpassad domän](../../active-directory/fundamentals/add-custom-domain.md) som kan verifieras

> [!NOTE]
> Den här självstudien använder PowerShell-skript så att du kan skapa självstudiemiljön så snabbt som möjligt.  Varje skript använder variabler som deklareras i början av skripten.  Du kan och bör ändra variablerna så att de speglar din miljö.
>
>Skripten används för skapa en allmän Active Directory-miljö innan du installerar Azure AD Connect.  De är relevanta för alla självstudierna.
>
> Kopior av de PowerShell-skript som används i den här självstudien finns på GitHub [här](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Det första som vi behöver göra för att få igång vår hybrididentitetsmiljö är att skapa en virtuell dator som ska användas som vår lokala Active Directory-server.  

>[!NOTE]
>Om du aldrig har kört ett skript i PowerShell på din värddator måste du köra `Set-ExecutionPolicy remotesigned` och svara Ja i PowerShell innan du kör skript.

Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

```powershell
#Declare variables
$VMName = 'DC1'
$Switch = 'External'
$InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
$Path = 'D:\VM'
$VHDPath = 'D:\VM\DC1\DC1.vhdx'
$VHDSize = '64424509440'

#Create New Virtual Machine
New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

#Set the memory to be non-dynamic
Set-VMMemory $VMName -DynamicMemoryEnabled $false

#Add DVD Drive to Virtual Machine
Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

#Mount Installation Media
$DVDDrive = Get-VMDvdDrive -VMName $VMName

#Configure Virtual Machine to Boot from DVD
Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
```

## <a name="complete-the-operating-system-deployment"></a>Slutföra distributionen av operativsystemet
För att slutföra skapande av den virtuella datorn kan du behöva slutföra installationen av operativsystemet.

1. Hyper-V Manager, dubbelklicka på den virtuella datorn
2. Klicka på knappen Start.
3. Du får uppmaningen ”Press any key to boot from CD or DVD” (Tryck på valfri tangent för att starta från CD eller DVD). Gör så.
4. På Windows Server-startskärmen väljer du språk och klickar på **Nästa**.
5. Klicka på **Installera nu**.
6. Ange licensnyckeln och klicka på **Nästa**.
7. Markera kryss **rutan Jag accepterar licens villkoren** och klicka på **Nästa**.
8. Välj **Anpassad: Installera endast Windows (avancerat)**
9. Klicka på **Nästa**
10. När installationen är klar startar du om den virtuella datorn, loggar in och kör Windows-uppdateringar för att säkerställa att den virtuella datorn är den mest aktuella.  Installera de senaste uppdateringarna.

## <a name="install-active-directory-pre-requisites"></a>Installera förutsättningar för Active Directory
Nu när vi har fått igång en virtuell dator kan behöva vi göra några saker innan vi installerar Active Directory.  Vi behöver alltså byta namn på den virtuella datorn, ange en statisk IP-adress och DNS-information och installera verktyg för fjärrserveradministration.   Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör `Set-ExecutionPolicy remotesigned` och svara Ja på alla [A].  Tryck på Retur.
3. Kör följande skript.

```powershell
#Declare variables
$ipaddress = "10.0.1.117" 
$ipprefix = "24" 
$ipgw = "10.0.1.1" 
$ipdns = "10.0.1.117"
$ipdns2 = "8.8.8.8" 
$ipif = (Get-NetAdapter).ifIndex 
$featureLogPath = "c:\poshlog\featurelog.txt" 
$newname = "DC1"
$addsTools = "RSAT-AD-Tools" 

#Set static IP address
New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

# Set the DNS servers
Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

#Rename the computer 
Rename-Computer -NewName $newname -force 

#Install features 
New-Item $featureLogPath -ItemType file -Force 
Add-WindowsFeature $addsTools 
Get-WindowsFeature | Where installed >>$featureLogPath 

#Restart the computer 
Restart-Computer
```

## <a name="create-a-windows-server-ad-environment"></a>Skapa en Windows Server AD-miljö
Nu när vi har skapat den virtuella datorn och den har bytt namn och har en statisk IP-adress kan vi gå vidare med att installera och konfigurera Active Directory Domain Services.  Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

```powershell 
#Declare variables
$DatabasePath = "c:\windows\NTDS"
$DomainMode = "WinThreshold"
$DomainName = "contoso.com"
$DomainNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = ConvertTo-SecureString "Passw0rd" -AsPlainText -Force

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Skapa en Windows Server AD-användare
Nu när vi har Active Directory-miljön behöver vi ett testkonto.  Det här kontot kommer att skapas i vår lokala AD-miljö och sedan synkroniseras till Azure AD.  Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

```powershell 
#Declare variables
$Givenname = "Allie"
$Surname = "McCray"
$Displayname = "Allie McCray"
$Name = "amccray"
$Password = "Pass1w0rd"
$Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


#Create the user
New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

#Set the password to never expire
Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
```

## <a name="create-a-certificate-for-ad-fs"></a>Skapa ett certifikat för AD FS
Nu ska vi skapa ett TLS/SSL-certifikat som ska användas av AD FS.  Det här är ett självsignerat certifikat och är endast avsett för testning.  Microsoft rekommenderar att du inte använder ett självsignerat certifikat i en produktionsmiljö. Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

```powershell 
#Declare variables
$DNSname = "adfs.contoso.com"
$Location = "cert:\LocalMachine\My"

#Create certificate
New-SelfSignedCertificate -DnsName $DNSname -CertStoreLocation $Location
```

## <a name="create-an-azure-ad-tenant"></a>Skapa en Azure AD-klientorganisation
Nu ska vi skapa en Azure AD-klientorganisation så att vi kan synkronisera våra användare till molnet.  Skapa en ny Azure AD-klientorganisation genom att göra följande.

1. Bläddra till [Azure-portalen](https://portal.azure.com) och logga in med ett konto som har en Azure-prenumeration.
2. Välj **plus-ikonen (+)** och sök efter **Azure Active Directory**.
3. Välj **Azure Active Directory** i sökresultatet.
4. Välj **Skapa**.</br>
![Skärm bild som visar hur du skapar en Azure AD-klient.](media/tutorial-password-hash-sync/create1.png)</br>
5. Ange ett **namn på organisationen** tillsammans med det **ursprungliga domännamnet**. Välj sedan **Skapa**. Detta skapar din katalog.
6. När det här har slutförts klickar du på den **här** länken för att hantera katalogen.

## <a name="create-a-global-administrator-in-azure-ad"></a>Skapa en global administratör i Azure AD
Nu när vi har en Azure AD-klientorganisation ska vi skapa ett globalt administratörskonto.  Det här kontot används för att skapa ett Azure AD-anslutningsappkonto under Azure AD Connect-installationen.  Azure AD-anslutningsappkontot används för att skriva information till Azure AD.   Skapa kontot för den globala administratören genom att göra följande.

1.  Under **Hantera** väljer du **Användare**.</br>
![Skärm bild som visar det användar alternativ som marker ATS i avsnittet hantera där du skapar en global administratör i Azure AD.](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Välj **Alla användare** och sedan **+ Ny användare**.
3.  Ange ett namn och användarnamn för den här användaren. Det här är din globala administratör för klientorganisationen. Du bör även ändra **katalogrollen** till **Global administratör.** Du kan dessutom visa det tillfälliga lösenordet. När du är klar väljer du **Skapa**.</br>
![Skärm bild som visar knappen Skapa som du väljer när du skapar en global administratör i Azure AD.](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. När det här har slutförts öppnar du en ny webbläsare och loggar in på myapps.microsoft.com med hjälp av det nya globala administratörskontot och det tillfälliga lösenordet.
5. Ändra lösenordet för den globala administratören till något som du kommer ihåg.

## <a name="add-the-custom-domain-name-to-your-directory"></a>Lägga till det anpassade domännamnet i din katalog
Nu när vi har en klientorganisation och en global administratör behöver vi lägga till vår egen domän så att Azure kan verifiera den.  Gör följande:

1. När du är i [Azure-portalen](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) igen ska du stänga bladet **Alla användare**.
2. Till vänster väljer du **Anpassade domännamn**.
3. Välj **Lägg till anpassad domän**.</br>
![Skärm bild som visar knappen Lägg till anpassad domän markerad.](media/tutorial-federation/custom1.png)</br>
4. På **Anpassade domännamn** anger du namnet på din anpassade domän i rutan och klickar sedan på **Lägg till domän**.
5. På skärmen för anpassat domännamn får du antingen TXT- eller MX-information.  Den här informationen måste läggas till i DNS-informationen för domänregistratorn under din domän.  Du behöver gå till din domänregistrator. Ange antingen TXT- eller MX-informationen i DNS-inställningarna för din domän.  Detta gör att Azure kan verifiera din domän.  Det kan ta upp till 24 timmar för Azure att verifiera den.  Mer information finns i dokumentationen om att [lägga till en anpassad domän](../../active-directory/fundamentals/add-custom-domain.md).</br>
![Skärm bild som visar var du lägger till TXT-eller MX-informationen.](media/tutorial-federation/custom2.png)</br>
6. För att säkerställa att den har verifierats klickar du på knappen Verifiera.</br>
![Skärm bild som visar ett klart verifierings meddelande när du har valt verifiera.](media/tutorial-federation/custom3.png)</br>

## <a name="download-and-install-azure-ad-connect"></a>Ladda ned och installera Azure AD Connect
Nu är det dags att ladda ned och installera Azure AD Connect.  När det har installerats kör vi snabbinstallationen.  Gör följande:

1. Ladda ned [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Navigera till och dubbelklicka på **AzureADConnect.msi**.
3. På välkomstskärmen markerar du kryssrutan för att acceptera licensvillkoren och klickar sedan på **Fortsätt**.  
4. På skärmen Expressinställningar klickar du på **Anpassa**.  
5. På skärmen Installera nödvändiga komponenter. Klicka på **Installera**.  
6. På användarinloggningsskärmen markerar du **Federation med AD FS** och klickar på **Nästa**.
![Skärm bild som visar var du väljer Federation med AD FS.](media/tutorial-federation/fed1.png)

1. På skärmen Anslut till Azure AD anger du användarnamnet och lösenordet för den globala administratör som vi skapade ovan och klickar på **Nästa**.
2. På skärmen Anslut dina kataloger klickar du på **Lägg till katalog**.  Välj sedan **Skapa nytt AD-konto** och ange användarnamnet och lösenordet contoso\Administrator och klicka på **OK**.
3. Klicka på **Nästa**.
4. På skärmen för Azure AD-inloggningskonfiguration väljer du **Fortsätt utan att matcha alla UPN-suffix till verifierade domäner** och klickar på **Nästa.**
5. På skärmen Domän- och organisationsenhetsfiltrering klickar du på **Nästa**.
6. På skärmen Identifiera användarna unikt klickar du på **Nästa**.
7. Filtrera skärmen Filtrera användare och enheter klickar du på **Nästa**.
8. På skärmen Valfria funktioner klickar du på **Nästa**.
9. På sidan med inloggningsuppgifterna för domänadministratören anger du användarnamnet contoso\Administrator och lösenordet och klickar på **Nästa.**
10. På skärmen för AD FS-servergrupp kontrollerar du att **Konfigurera en ny AD FS-servergrupp** har valts.
11. Välj **Använd ett certifikat som har installerats på federationsservrarna** och klicka på **Bläddra**.
12. Ange DC1 i sökrutan och välj det när det hittas.  Klicka på **OK**.
13. Från listrutan **Certifikatfil** väljer du **adfs.contoso.com**-certifikatet som vi skapade ovan.  Klicka på **Nästa**.
![Skärm bild som visar var du kan välja den certifikat fil som du skapade.](media/tutorial-federation/fed2.png)

1. På skärmen för AD FS-servern klickar du på **Bläddra** och anger DC1 i sökrutan och väljer det när det hittas.  Klicka på **OK**.  Klicka på **Nästa**.
![Federation](media/tutorial-federation/fed3.png)

1. På skärmen Webbappsproxyservrar klickar du på **Nästa**.
2. På AD FS-tjänstekontoskärmen anger du användarnamnet och lösenordet contoso\Administrator och klickar på **Nästa.**
3. På skärmen för Azure AD Domain väljer du din verifierade anpassade domän i listrutan och klickar på **Nästa**.
4. Klicka på **Installera** på skärmen Klart att konfigurera.
5. När installationen är klar klickar du på **Avsluta**.
6. När installationen har slutförts loggar du ut och loggar in igen innan du använder Synchronization Service Manager eller Synchronization Rule Editor.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verifiera att användare skapas och att synkronisering sker
Vi kommer nu att verifiera att de användare som vi hade i vår lokala katalog har synkroniserats och nu finns i Azure AD-klientorganisationen.  Observera att det kan ta några timmar att slutföra.  För att verifiera att användarna synkroniseras gör du följande.


1. Bläddra till [Azure-portalen](https://portal.azure.com) och logga in med ett konto som har en Azure-prenumeration.
2. Välj **Azure Active Directory** till vänster
3. Under **Hantera** väljer du **Användare**.
4. Kontrollera att du ser de nya användarna i vår klientorganisation ![Synch](media/tutorial-password-hash-sync/synch1.png)

## <a name="test-signing-in-with-one-of-our-users"></a>Testa att logga in med någon av våra användare

1. Gå till [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Logga in med ett användarkonto som har skapats i vår nya klientorganisation.  Du behöver logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.
   ![Verifiera](media/tutorial-password-hash-sync/verify1.png)

Du har nu har installerat en hybrididentitetsmiljö som du kan använda för att testa och bekanta dig med allt som Azure har att erbjuda.

## <a name="next-steps"></a>Nästa steg

- [Maskin vara och krav](how-to-connect-install-prerequisites.md) 
- [Anpassade inställningar](how-to-connect-install-custom.md)
- [Azure AD Connect och federation](how-to-connect-fed-whatis.md)
