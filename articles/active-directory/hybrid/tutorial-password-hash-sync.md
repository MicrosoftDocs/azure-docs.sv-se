---
title: 'Självstudie: integrera en enda AD-skog med Azure med PHS'
description: Visar hur du ställer in en hybrididentitetsmiljö med synkronisering av lösenordshash.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b82dcc633f0f2bb13cb84f80873117f9eb94a24f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96176128"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Självstudie: integrera en enda AD-skog med Password hash Sync (PHS)

![Skapa](media/tutorial-password-hash-sync/diagram.png)

Följande självstudie vägleder dig genom att skapa en hybrid identitets miljö med hjälp av hash-synkronisering av lösen ord.  Den här miljön kan sedan användas för testning eller för att få bättre kunskaper om hur en hybrid identitet fungerar.

## <a name="prerequisites"></a>Förutsättningar
Följande är förutsättningar som krävs för den här självstudien
- En dator med [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) installerat.  Vi rekommenderar att du gör detta på en dator med antingen [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) eller [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- Ett [externt nätverkskort](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) så att den virtuella datorn kan kommunicera med Internet.
- En [Azure-prenumeration](https://azure.microsoft.com/free)
- En kopia av Windows Server 2016

> [!NOTE]
> Den här självstudien använder PowerShell-skript så att du kan skapa självstudiemiljön så snabbt som möjligt.  Varje skript använder variabler som deklareras i början av skripten.  Du kan och bör ändra variablerna så att de speglar din miljö.
>
>Skripten används för skapa en allmän Active Directory-miljö innan du installerar Azure AD Connect.  De är relevanta för alla självstudierna.
>
> Kopior av de PowerShell-skript som används i den här självstudien finns på GitHub [här](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Det första som vi behöver göra för att få igång vår hybrididentitetsmiljö är att skapa en virtuell dator som ska användas som vår lokala Active Directory-server.  Gör följande:

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
7. Markera **I accept the license terms (Jag godkänner licensvillkoren) och klicka på **Nästa**.
8. Välj **Anpassad: Installera endast Windows (avancerat)**
9. Klicka på **Nästa**
10. När installationen är klar startar du om den virtuella datorn, loggar in och kör Windows-uppdateringar för att säkerställa att den virtuella datorn är den mest aktuella.  Installera de senaste uppdateringarna.

## <a name="install-active-directory-prerequisites"></a>Installera förutsättningar för Active Directory
Nu när vi har fått igång en virtuell dator kan behöva vi göra några saker innan vi installerar Active Directory.  Vi behöver alltså byta namn på den virtuella datorn, ange en statisk IP-adress och DNS-information och installera verktyg för fjärrserveradministration.   Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

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
$DomaninNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = "Pass1w0rd"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
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

## <a name="download-and-install-azure-ad-connect"></a>Ladda ned och installera Azure AD Connect
Nu är det dags att ladda ned och installera Azure AD Connect.  När det har installerats kör vi snabbinstallationen.  Gör följande:

1. Ladda ned [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Navigera till och dubbelklicka på **AzureADConnect.msi**.
3. På välkomstskärmen markerar du kryssrutan för att acceptera licensvillkoren och klickar sedan på **Fortsätt**.  
4. På skärmen Standardinställningar klickar du på **Använd standardinställningar**.</br>  
![Skärm bild som visar skärmen för Express inställningar och knappen Använd Express-inställningar.](media/tutorial-password-hash-sync/express1.png)</br>
5. På sidan Anslut till Azure AD anger du användarnamnet och lösenordet för en global administratör för Azure AD. Klicka på **Nästa**.  
6. På skärmen Anslut till AD DS anger du användarnamnet och lösenordet för ett företagsadministratörskonto. Klicka på **Nästa**.  
7. Klicka på **Installera** på skärmen Klart att konfigurera.
8. När installationen är klar klickar du på **Avsluta**.
9. När installationen har slutförts loggar du ut och loggar in igen innan du använder Synchronization Service Manager eller Synchronization Rule Editor.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verifiera att användare skapas och att synkronisering sker
Vi kommer nu att verifiera att de användare som vi hade i vår lokala katalog har synkroniserats och nu finns i Azure AD-klientorganisationen.  Observera att det kan ta några timmar att slutföra.  För att verifiera att användarna synkroniseras gör du följande.


1. Bläddra till [Azure-portalen](https://portal.azure.com) och logga in med ett konto som har en Azure-prenumeration.
2. Välj **Azure Active Directory** till vänster
3. Under **Hantera** väljer du **Användare**.
4. Kontrollera att du ser de nya användarna i vår klient</br>
![Synch](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testa att logga in med någon av våra användare

1. Gå till [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Logga in med ett användarkonto som har skapats i vår nya klientorganisation.  Du behöver logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.</br>
   ![Verifiera](media/tutorial-password-hash-sync/verify1.png)</br>

Du har nu har installerat en hybrididentitetsmiljö som du kan använda för att testa och bekanta dig med allt som Azure har att erbjuda.

## <a name="next-steps"></a>Nästa steg


- [Maskin vara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Hash-synkronisering av lösen ord](how-to-connect-password-hash-synchronization.md)|