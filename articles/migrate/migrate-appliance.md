---
title: Azure Migrate-installation
description: Innehåller en sammanfattning av stödet för den Azure Migrate-enheten.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 17ac10fd4e48043b79db5448fad2f29c5eb3f2a3
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986827"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-installation

I den här artikeln sammanfattas förutsättningarna och support kraven för Azure Migrate-enheten. 

## <a name="deployment-scenarios"></a>Distributionsscenarier

Azure Migrates apparaten används i följande scenarier.

**Scenario** | **Verktyg** | **Används för** 
--- | --- | ---
**Virtuell VMware VM-utvärdering** | Azure Migrate: Server utvärdering | Identifiera virtuella VMware-datorer<br/><br/> Identifiera maskin program och beroenden<br/><br/> Samla in metadata för datorns metadata och prestanda för utvärderingar.
**VMware VM utan agent för migrering** | Azure Migrate: Server-migrering | Identifiera virtuella VMware-datorer <br/><br/> Replikera virtuella VMware-datorer med migrering utan agent.
**Utvärdering av Hyper-V VM** | Azure Migrate: Server utvärdering | Identifiera virtuella Hyper-V-datorer<br/><br/> Samla in metadata för datorns metadata och prestanda för utvärderingar.
**Utvärdering av fysisk dator** |  Azure Migrate: Server utvärdering |  Identifiera fysiska servrar (eller virtuella datorer som du hanterar som fysiska servrar).<br/><br/> Samla in metadata för datorns metadata och prestanda för utvärderingar.

## <a name="deployment-methods"></a>Distributions metoder

Installationen kan distribueras med ett par olika metoder:

- Installationen kan distribueras med hjälp av en mall för virtuella VMware-datorer och virtuella Hyper-V-datorer (ägg-mall för VMware eller VHD för Hyper-V).
- Om du inte vill använda en mall kan du distribuera-enheten för VMware eller Hyper-V med hjälp av ett PowerShell-skript.
- I Azure Government bör du distribuera installationen med hjälp av ett skript.
- För fysiska servrar distribuerar du alltid enheten med hjälp av ett skript.
- Nedladdnings länkarna är tillgängliga i tabellerna nedan.


## <a name="appliance---vmware"></a>Utrustning – VMware 

I följande tabell sammanfattas kraven för Azure Migrate-installationen för VMware.

**Krav** | **VMware** 
--- | ---
**Behörigheter** | Om du vill få åtkomst till appens webbapp lokalt eller via fjärr anslutning måste du ha ett lokalt eller domän användar konto med administratörs behörighet på installations servern.
**Utrustnings komponenter** | Enheten har följande komponenter:<br/><br/> - **Hanterings app**: det här är en webbapp för användarindata under installationen av produkten. Används vid utvärdering av datorer för migrering till Azure.<br/> - **Identifierings agent**: agenten samlar in dator konfigurations data. Används vid utvärdering av datorer för migrering till Azure.<br/>- **Insamlings agent**: agenten samlar in prestanda data. Används vid utvärdering av datorer för migrering till Azure.<br/>- **Tjänsten automatisk uppdatering**: uppdaterar komponenter för komponenter (körs var 24: e timme).<br/>- **Dra-agent**: dirigerar VM-replikering och samordnar kommunikationen mellan replikerade datorer och Azure. Används endast när du replikerar virtuella VMware-datorer till Azure med hjälp av en agent lös migrering.<br/>- **Gateway**: skickar replikerade data till Azure. Används endast när du replikerar virtuella VMware-datorer till Azure med hjälp av en agent lös migrering.
**Distribution som stöds** | Distribuera som virtuell VMware-dator med hjälp av en ägg mall.<br/><br/> Distribuera som en virtuell VMware-dator eller fysisk dator med hjälp av installations skriptet för PowerShell.
**Projekt support** |  En apparat kan associeras med ett enda projekt. <br/> Valfritt antal enheter kan associeras med ett enda projekt.<br/> 
**Identifierings gränser** | En apparat kan identifiera upp till 10 000 virtuella VMware-datorer på en vCenter Server.<br/> En apparat kan ansluta till en enda vCenter Server.
**Mall för ägg** | Ladda ned från portalen eller [härifrån](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> Hämtnings storleken är 11,9 GB.<br/><br/> Mallen för hämtade installationer innehåller en utvärderings licens för Windows Server 2016, som är giltig i 180 dagar. Om utvärderings perioden ligger nära förfallo datum, rekommenderar vi att du laddar ned och distribuerar en ny installation, eller att du aktiverar operativ Systems licensen för den virtuella dator enheten.
**PowerShell-skript** | Läs mer i den här [artikeln](./deploy-appliance-script.md#set-up-the-appliance-for-vmware).<br/><br/> 
**Program vara/maskin vara** |  Installationen ska köras på datorn med Windows Server 2016, 32 GB RAM, 8 virtuella processorer, runt 80 GB disk lagring och en extern virtuell växel.<br/> Enheten kräver Internet åtkomst, antingen direkt eller via en proxyserver.<br/><br/> Om du kör-installationen på en virtuell VMware-dator måste du ha tillräckligt med resurser på vCenter Server för att allokera en virtuell dator som uppfyller kraven.<br/><br/> Om du kör-installationen på en fysisk dator kontrollerar du att den kör Windows Server 2016 och uppfyller maskin varu kraven.
**Krav för VMware** | Om du distribuerar installationen som en virtuell VMware-dator måste den distribueras på en ESXi-värd som kör version 5,5 eller senare.<br/><br/> vCenter Server som kör 5,5, 6,0, 6,5 eller 6,7.
**VDDK (utan agent migrering)** | Om du distribuerar-installationen som en virtuell VMware-dator och du kör en agent utan migrering, måste VMware vSphere VDDK installeras på den virtuella datorn.
**Hash-värde – ägg** | [Verifiera](tutorial-discover-vmware.md#verify-security) hash-värden för en embryo-mall.
**Hash-värde – PowerShell-skript** | [Kontrol lera](deploy-appliance-script.md#verify-file-security) hash-värdena för PowerShell-skriptet.




## <a name="appliance---hyper-v"></a>Apparat-Hyper-V

**Krav** | **Hyper-V** 
--- | ---
**Behörigheter** | Om du vill få åtkomst till appens webbapp lokalt eller via fjärr anslutning måste du ha ett lokalt eller domän användar konto med administratörs behörighet på installations servern.
**Utrustnings komponenter** | Enheten har följande komponenter:<br/><br/>- **Hanterings app**: det här är en webbapp för användarindata under installationen av produkten. Används vid utvärdering av datorer för migrering till Azure.<br/> - **Identifierings agent**: agenten samlar in dator konfigurations data. Används vid utvärdering av datorer för migrering till Azure.<br/>- **Insamlings agent**: agenten samlar in prestanda data. Används vid utvärdering av datorer för migrering till Azure.<br/>- **Tjänsten automatisk uppdatering**: uppdaterar komponenter för komponenter (körs var 24: e timme).
**Distribution som stöds** | Distribuera som virtuell Hyper-V-dator med en VHD-mall.<br/><br/> Distribuera som en virtuell Hyper-V-dator eller fysisk dator med ett PowerShell-installations skript.
**Projekt support** |  En apparat kan associeras med ett enda projekt. <br/> Valfritt antal enheter kan associeras med ett enda projekt.<br/> 
**Identifierings gränser** | En apparat kan identifiera upp till 5000 virtuella Hyper-V-datorer.<br/> En apparat kan ansluta till upp till 300 Hyper-V-värdar.
**VHD-mall** | Zippad mapp inklusive VHD. Ladda ned från portalen eller [härifrån.](https://go.microsoft.com/fwlink/?linkid=2140422)<br/><br/> Hämtnings storleken är 8,91 GB.<br/><br/> Mallen för hämtade installationer innehåller en utvärderings licens för Windows Server 2016, som är giltig i 180 dagar. Om utvärderings perioden ligger nära förfallo datum, rekommenderar vi att du laddar ned och distribuerar en ny installation, eller att du aktiverar operativ Systems licensen för den virtuella dator enheten.
**PowerShell-skript** | Läs mer i den här [artikeln](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).<br/><br/> 
**Program vara/maskin vara** _   |  Installationen ska köras på datorn med Windows Server 2016, 16 GB RAM, 8 virtuella processorer, cirka 80 GB disk lagring och en extern virtuell växel.<br/> Installationen behöver en statisk eller dynamisk IP-adress och kräver Internet åtkomst, antingen direkt eller via en proxyserver.<br/><br/> Om du kör-installationen som en virtuell Hyper-V-dator behöver du tillräckligt med resurser på Hyper-V-värden för att allokera maskin varu krav.<br/><br/> Om du kör-installationen på en fysisk dator kontrollerar du att den kör Windows Server 2016 och uppfyller maskin varu kraven. 
_ *Hyper-V-krav** | Om du distribuerar installationen med VHD-mallen, är den virtuella Azure Migrate datorns virtuella Hyper-V-dator version 5,0.<br/><br/> Hyper-V-värden måste köra Windows Server 2012 R2 eller senare. 
**Hash-värde – VHD** | [Verifiera](tutorial-discover-hyper-v.md#verify-security) Hash-värden för VHD-mall.
**Hash-värde – PowerShell-skript** | [Kontrol lera](deploy-appliance-script.md#verify-file-security) hash-värdena för PowerShell-skriptet.


## <a name="appliance---physical"></a>Apparat-fysisk

**Krav** | **Fysisk** 
--- | ---
**Behörigheter** | Om du vill få åtkomst till appens webbapp lokalt eller via fjärr anslutning måste du ha ett lokalt eller domän användar konto med administratörs behörighet på installations servern.
**Utrustnings komponenter** | Enheten har följande komponenter: <br/><br/> - **Hanterings app**: det här är en webbapp för användarindata under installationen av produkten. Används vid utvärdering av datorer för migrering till Azure.<br/> - **Identifierings agent**: agenten samlar in dator konfigurations data. Används vid utvärdering av datorer för migrering till Azure.<br/>- **Insamlings agent**: agenten samlar in prestanda data. Används vid utvärdering av datorer för migrering till Azure.<br/>- **Tjänsten automatisk uppdatering**: uppdaterar komponenter för komponenter (körs var 24: e timme).
**Distribution som stöds** | Distribuera som dedikerad fysisk dator eller en virtuell dator med hjälp av ett PowerShell-installations skript. Skriptet är tillgängligt för hämtning från portalen.
**Projekt support** |  En apparat kan associeras med ett enda projekt. <br/> Valfritt antal enheter kan associeras med ett enda projekt.<br/> 
**Identifierings gränser** | En apparat kan identifiera upp till 1000 fysiska servrar.
**PowerShell-skript** | Hämta skriptet (AzureMigrateInstaller.ps1) i en zippad [mapp från portalen eller härifrån.](https://go.microsoft.com/fwlink/?linkid=2140334) [Läs mer](tutorial-discover-physical.md).<br/><br/> Hämtnings storleken är 85,8 MB.
**Program vara/maskin vara** |  Installationen ska köras på datorn med Windows Server 2016, 16 GB RAM, 8 virtuella processorer, cirka 80 GB disk lagring.<br/> Installationen behöver en statisk eller dynamisk IP-adress och kräver Internet åtkomst, antingen direkt eller via en proxyserver.<br/><br/> Om du kör-installationen på en fysisk dator kontrollerar du att den kör Windows Server 2016 och uppfyller maskin varu kraven.<br/>_(För närvarande stöds endast distributionen av installations programmet på Windows Server 2016.)_
**Hash-värde** | [Kontrol lera](tutorial-discover-physical.md#verify-security) hash-värdena för PowerShell-skriptet.

## <a name="url-access"></a>URL-åtkomst

Azure Migrate-utrustningen behöver anslutning till Internet.

- När du distribuerar installationen kontrollerar Azure Migrate en anslutning till de begärda URL: erna.
- Du måste tillåta åtkomst till alla webb adresser i listan. Om du bara utför utvärderingen kan du hoppa över de URL: er som marker ATS som obligatoriska endast för VMware-agenten för migrering.
-  Om du använder en URL-baserad proxy för att ansluta till Internet, måste du kontrol lera att proxyn matchar eventuella CNAME-poster som tas emot vid sökning av URL: erna.

### <a name="public-cloud-urls"></a>Offentliga moln-URL: er

**URL** | **Information**  
--- | --- |
*.portal.azure.com  | Gå till Azure-portalen.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *. live.com <br/> *. office.com | Logga in på din Azure-prenumeration.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Skapa Azure Active Directory (AD) appar för att kunna kommunicera med Azure Migrate.
management.azure.com | Skapa Azure AD-appar för att kunna kommunicera med tjänsten Azure Migrate.
*.services.visualstudio.com | Ladda upp program loggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault. Obs: se till att datorer som ska replikeras har åtkomst till detta.
aka.ms/* | Tillåt åtkomst till aka-länkar. Används för uppdateringar av Azure Migrates enheten.
download.microsoft.com/download | Tillåt hämtning från Microsoft Download.
*.servicebus.windows.net | Kommunikation mellan installations programmet och tjänsten Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Anslut till Azure Migrate tjänst-URL: er.
*.hypervrecoverymanager.windowsazure.com | **Används för migrering av VMware-agent**<br/><br/> Anslut till Azure Migrate tjänst-URL: er.
*.blob.core.windows.net |  **Används för migrering av VMware-agent**<br/><br/>Överför data till lagring för migrering.

### <a name="government-cloud-urls"></a>Offentliga moln-URL: er

**URL** | **Information**  
--- | --- |
*. portal.azure.us  | Gå till Azure-portalen.
graph.windows.net | Logga in på din Azure-prenumeration.
login.microsoftonline.us  | Skapa Azure Active Directory (AD) appar för att kunna kommunicera med Azure Migrate.
management.usgovcloudapi.net | Skapa Azure AD-appar för att kunna kommunicera med tjänsten Azure Migrate.
*.services.visualstudio.com | Ladda upp program loggar som används för intern övervakning.
*. vault.usgovcloudapi.net | Hantera hemligheter i Azure Key Vault.
aka.ms/* | Tillåt åtkomst till aka-länkar. Används för uppdateringar av Azure Migrates enheten.
download.microsoft.com/download | Tillåt hämtning från Microsoft Download.
*. servicebus.usgovcloudapi.net  | Kommunikation mellan installations programmet och tjänsten Azure Migrate.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Anslut till Azure Migrate tjänst-URL: er.
*. hypervrecoverymanager.windowsazure.us | **Används för migrering av VMware-agent**<br/><br/> Anslut till Azure Migrate tjänst-URL: er.
*. blob.core.usgovcloudapi.net  |  **Används för migrering av VMware-agent**<br/><br/>Överför data till lagring för migrering.
*. applicationinsights.us | Ladda upp program loggar som används för intern övervakning.





## <a name="collected-data---vmware"></a>Insamlade data – VMware

Enheten samlar in metadata, prestanda data och beroende analys data (om agenten för beroende [analys](concepts-dependency-visualization.md) används).

### <a name="metadata"></a>Metadata

Metadata som upptäcks av Azure Migrate-enheten hjälper dig att ta reda på om datorer och appar är klara för migrering till Azure, datorer med rätt storlek och appar, planerar kostnader och analys av program beroenden. Microsoft använder inte dessa data i någon granskning av licens efterlevnad.

Här är en fullständig lista över de virtuella VMware-metadata som enheten samlar in och skickar till Azure.

**DATA** | **MEDELVÄRDE**
--- | --- 
**Dator information** | 
VM-ID | vm.Config. InstanceUuid 
VM-namn | vm.Config. Namn
vCenter Server-ID | VMwareClient. instance. uuid
Beskrivning av virtuell dator | vm.Summary.Config. Antecknings
Licens produkt namn | VM. client. ServiceContent. about. LicenseProductName
Typ av operativsystem | VM. SummaryConfig. GuestFullName
Start typ | vm.Config. Inbyggd program vara
Antal kärnor | vm.Config. Maskin vara. NumCPU
Minne (MB) | vm.Config. Maskin vara. MemoryMB
Antal diskar | vm.Config. Maskin vara. Device. ToList (). FindAll (x => är VirtualDisk). Count
Lista över disk storlekar | vm.Config. Maskin vara. Device. ToList (). FindAll (x => är VirtualDisk)
Lista med nätverkskort | vm.Config. Maskin vara. Device. ToList (). FindAll (x => är VirtualEthernet). Count
CPU-användning | processor. Usage. genomsnitt
Minnes användning |minnes användning. genomsnitt
**Per disk information** | 
Disk nyckel värde | diskdefragmenter. Knapp
Dikunit-nummer | diskdefragmenter. UnitNumber
Nyckel värde för disk styrenhet | diskdefragmenter. ControllerKey. Value
Gigabyte etablerad | virtualDisk. DeviceInfo. Summary
Disknamn | Värde som genereras med disk. UnitNumber, disk. Nyckel, disk. ControllerKey. VAlue
Läs åtgärder per sekund | virtualDisk. numberReadAveraged. Average
Skriv åtgärder per sekund | virtualDisk. numberWriteAveraged. Average
Läs data flöde (MB per sekund) | virtualDisk. Read. Average
Skriv data flöde (MB per sekund) | virtualDisk. Write. Average
**Per NIC-information** | 
Nätverkskortets namn | NIC. Knapp
MAC-adress | ((VirtualEthernetCard) NIC). MacAddress
IPv4-adresser | vm.Guest.Net
IPv6-adresser | vm.Guest.Net
Läs data flöde (MB per sekund) | net. Received. Average
Skriv data flöde (MB per sekund) | net. överföring. genomsnitt
**Information om lager Sök väg** | 
Name | fönster. GetType (). Namn
Typ av underordnat objekt | fönster. ChildType
Referens information | fönster. MoRef
Överordnad information | Container. parent
Information om mappar per virtuell dator | ((Mapp)-behållare). ChildEntity. Type
Data Center information per virtuell dator | ((Data Center)-behållare). VmFolder
Data Center information per värd-mapp | ((Data Center)-behållare). HostFolder
Kluster information per värd | ((ClusterComputeResource) container). Värd
Värd information per virtuell dator | ((HostSystem) container). DATORN

### <a name="performance-data"></a>Prestandadata


Här är de prestanda data för VMware VM som enheten samlar in och skickar till Azure.

**Data** | **Räknare** | **Utvärderings påverkan**
--- | --- | ---
CPU-användning | processor. Usage. genomsnitt | Rekommenderad storlek/kostnad för virtuell dator
Minnes användning | minnes användning. genomsnitt | Rekommenderad storlek/kostnad för virtuell dator
Disk läsnings data flöde (MB per sekund) | virtualDisk. Read. Average | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Disk skrivnings data flöde (MB per sekund) | virtualDisk. Write. Average | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Disk läsnings åtgärder per sekund | virtualDisk. numberReadAveraged. Average | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Disk skrivnings åtgärder per sekund | virtualDisk. numberWriteAveraged. Average  | Beräkning för disk storlek, lagrings kostnad, VM-storlek
NIC-läst data flöde (MB per sekund) | net. Received. Average | Beräkning för VM-storlek
NÄTVERKSKORT skriver data flöde (MB per sekund) | net. överföring. genomsnitt  |Beräkning för VM-storlek


### <a name="installed-apps-metadata"></a>Metadata för installerade appar

Program identifiering samlar in installerade program och operativ system data.

#### <a name="windows-vm-apps-data"></a>Data för Windows VM-appar

Här är de installerade program data som installationen samlar in från varje virtuell dator som är aktive rad för program identifiering. Dessa data skickas till Azure.

**Data** | **Registerplats** | **Nyckel**
--- | --- | ---
Programnamn  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Version  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
Leverantör  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-vm-features-data"></a>Windows VM features-data

Här är de funktions data som installationen samlar in från varje virtuell dator som är aktive rad för program identifiering. Dessa data skickas till Azure.

**Data**  | **PowerShell-cmdlet** | **Egenskap**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Funktions typ | Get-WindowsFeature  | FeatureType
Överordnad  | Get-WindowsFeature  | Överordnad

#### <a name="windows-vm-sql-server-metadata"></a>Windows VM SQL Server metadata

Här är SQL Server-metadata som enheten samlar in från virtuella datorer som kör Microsoft SQL Server aktiverat för program identifiering. Dessa data skickas till Azure.

**Data**  | **Registerplats**  | **Nyckel**
--- | --- | ---
Name  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Utgåva  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Utgåva 
Service Pack  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | SP
Version  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Version 

#### <a name="windows-vm-operating-system-data"></a>Operativ system data för Windows VM

Här är de operativ Systems data som installations programmet samlar in varje virtuell dator som är aktive rad för program identifiering. Dessa data skickas till Azure.

Data  | WMI-klass  | Egenskap för WMI-klass
--- | --- | ---
Name  | Win32_operatingsystem  | Caption
Version  | Win32_operatingsystem  | Version
Arkitektur  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-vm-apps-data"></a>Data för virtuella Linux-appar

Här är de installerade program data som installationen samlar in från varje virtuell dator som är aktive rad för program identifiering. Ett eller flera av kommandona körs baserat på den virtuella datorns operativ system. Dessa data skickas till Azure.

Data  | Kommando
--- | --- 
Name | RPM, dpkg-fråga, fäst
Version | RPM, dpkg-fråga, fäst
Leverantör | RPM, dpkg-fråga, fäst

#### <a name="linux-vm-operating-system-data"></a>Operativ system data för Linux VM

Här är de operativ Systems data som installations programmet samlar in varje virtuell dator som är aktive rad för program identifiering. Dessa data skickas till Azure.

**Data**  | **Kommando** 
--- | --- | ---
Name <br/> version | Samlas in från en eller flera av följande filer:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Arkitektur | uname


### <a name="app-dependencies-metadata"></a>Metadata för app-beroenden

Beroende analys av agent samlar in anslutnings-och process data.

#### <a name="windows-vm-app-dependencies-data"></a>Windows VM-appens beroende data

Här är de anslutnings data som enheten samlar in från varje virtuell dator som är aktive rad för en agent utan agent analys. Dessa data skickas till Azure.

**Data** | **Kommando som används** 
--- | --- 
Lokal port | Netstat
Lokal IP-adress | Netstat
Fjärr-IP-adress | Netstat
Fjärr-IP-adress | Netstat
Status för TCP-anslutning | Netstat
Process-ID | Netstat
Antal aktiva anslutningar | Netstat


Här är process data som enheten samlar in från varje virtuell dator som är aktive rad för en agent utan agent analys. Dessa data skickas till Azure.

**Data** | **WMI-klass** | **Egenskap för WMI-klass**
--- | --- | ---
Processnamn | Win32_Process | ExecutablePath
Process argument | Win32_Process | Raden
Programnamn | Win32_Process | VersionInfo. ProductName-parameter för egenskapen ExecutablePath

#### <a name="linux-vm-app-dependencies-data"></a>Linux VM-appens beroende data

Här är anslutnings-och process data som installations programmet samlar in från varje Linux-VM som är aktive rad för en agent utan agent för beroende analys. Dessa data skickas till Azure.

**Data** | **Kommando som används** 
--- | ---
Lokal port | Netstat 
Lokal IP-adress | Netstat 
Fjärr-IP-adress | Netstat 
Fjärr-IP-adress | Netstat 
Status för TCP-anslutning | Netstat 
Antal aktiva anslutningar | Netstat
Process-ID  | Netstat 
Processnamn | PS
Process argument | PS
Programnamn | dpkg eller rpm



## <a name="collected-data---hyper-v"></a>Insamlade data – Hyper-V

Enheten samlar in metadata, prestanda data och beroende analys data (om agenten för beroende [analys](concepts-dependency-visualization.md) används).

### <a name="metadata"></a>Metadata
Metadata som upptäcks av Azure Migrate-enheten hjälper dig att ta reda på om datorer och appar är klara för migrering till Azure, datorer med rätt storlek och appar, planerar kostnader och analys av program beroenden. Microsoft använder inte dessa data i någon granskning av licens efterlevnad.

Här är en fullständig lista över Hyper-V VM-metadata som enheten samlar in och skickar till Azure.

**DATA** | **WMI-KLASS** | **EGENSKAP FÖR WMI-KLASS**
--- | --- | ---
**Dator information** | 
Serie nummer för BIOS _ Msvm_BIOSElement | BIOSSerialNumber
VM-typ (gen 1 eller 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Visnings namn för virtuell dator | Msvm_VirtualSystemSettingData | ElementName
VM-version | Msvm_ProcessorSettingData | VirtualQuantity
Minne (byte) | Msvm_MemorySettingData | VirtualQuantity
Maximalt minne som kan utnyttjas av VM | Msvm_MemorySettingData | Gräns
Dynamiskt minne aktiverat | Msvm_MemorySettingData | DynamicMemoryEnabled
Operativ systemets namn/version/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems namn data
Energi status för virtuell dator | Msvm_ComputerSystem | EnabledState
**Per disk information** | 
Disk-ID | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Typ av virtuell hård disk | Msvm_VirtualHardDiskSettingData | Typ
Storlek på virtuell hård disk | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Överordnad virtuell hård disk | Msvm_VirtualHardDiskSettingData | ParentPath
**Per NIC-information** | 
IP-adresser (syntetiska nätverkskort) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP aktiverat (syntetiska nätverkskort) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NÄTVERKSKORT-ID (syntetiska nätverkskort) | Msvm_SyntheticEthernetPortSettingData | InstanceID
MAC-adress för nätverkskort (syntetiska nätverkskort) | Msvm_SyntheticEthernetPortSettingData | Adress
NÄTVERKSKORT-ID (äldre nätverkskort) | MsvmEmulatedEthernetPortSetting-data | InstanceID
MAC-ID för nätverkskort (äldre nätverkskort) | MsvmEmulatedEthernetPortSetting-data | Adress

### <a name="performance-data"></a>Prestandadata

Här är de prestanda data för virtuella Hyper-datorer som enheten samlar in och skickar till Azure.

**Prestanda räknar klass** | **Räknare** | **Utvärderings påverkan**
--- | --- | ---
Virtuell Hyper-V hypervisor-processor | % Kör tid för gäst | Rekommenderad storlek/kostnad för virtuell dator
VIRTUELL Hyper-V-dynamiskt minne | Aktuellt tryck (%)<br/> Fysiskt synligt fysiskt minne (MB) | Rekommenderad storlek/kostnad för virtuell dator
Virtuell lagrings enhet för Hyper-V | Lästa byte per sekund | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Virtuell lagrings enhet för Hyper-V | Skrivna byte/sekund | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Hyper-V Virtual Network Adapter | Mottagna byte per sekund | Beräkning för VM-storlek
Hyper-V Virtual Network Adapter | Skickade byte per sekund | Beräkning för VM-storlek

- PROCESSOR användningen är summan av all användning, för alla virtuella processorer som är kopplade till en virtuell dator.
- Minnes användningen är (aktuellt tryck * fysiskt synligt minne i gäst)/100.
- Värdena för disk-och nätverks användning samlas in från de listade Hyper-V-prestandaräknare.


## <a name="collected-data---physical"></a>Insamlade data-fysiska

Enheten samlar in metadata, prestanda data och beroende analys data (om agenten för beroende [analys](concepts-dependency-visualization.md) används).

### <a name="windows-metadata"></a>Windows-metadata

Metadata som upptäcks av Azure Migrate-enheten hjälper dig att ta reda på om datorer och appar är klara för migrering till Azure, datorer med rätt storlek och appar, planerar kostnader och analys av program beroenden. Microsoft använder inte dessa data i någon granskning av licens efterlevnad.

Här är en fullständig lista över Windows Server-metadata som enheten samlar in och skickar till Azure.

**DATA** | **WMI-KLASS** | **EGENSKAP FÖR WMI-KLASS**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domän, namn, PartOfDomain
Antal processor kärnor | Win32_PRocessor | NumberOfCores
Allokerat minne | Win32_ComputerSystem | TotalPhysicalMemory
BIOS-serienummer | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS-GUID | Win32_ComputerSystemProduct | UUID
Start typ | Win32_DiskPartition | Sök efter partition med Type = **GPT: system** för EFI/BIOS
Operativsystemets namn | Win32_OperatingSystem | Caption
OS-version |Win32_OperatingSystem | Version
Operativsystemarkitektur | Win32_OperatingSystem | OSArchitecture
Antal diskar | Win32_DiskDrive | Modell, storlek, DeviceID, MediaType, namn
Diskstorlek | Win32_DiskDrive | Storlek
NIC-lista | Win32_NetworkAdapterConfiguration | Beskrivning, index
IP-adress för nätverkskort | Win32_NetworkAdapterConfiguration | IPAddress
MAC-adress för nätverkskort | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Linux-metadata

Här är en fullständig lista över Linux-serverns metadata som enheten samlar in och skickar till Azure.

**DATA** | **LINUX** 
--- | --- 
FQDN | katt/proc/sys/kernel/hostname, hostname-f
Antal processor kärnor |  /proc/cpuinfo \| awk '/^ processor/{print $3} ' \| WC-l
Allokerat minne | katt/proc/Meminfo \| grep MemTotal \| awk ' {printf "%. 0f", $2/1024}
BIOS-serienummer | lshw \| grep "seriell:" \| Head-N1 \| awk {Print $2} <br/> /usr/sbin/dmidecode-t 1 \| grep ' \| awk ' {$1 = ""; $2 = ""; Print}
BIOS-GUID | katt/sys/Class/DMI/ID/product_uuid
Start typ | [-d/sys/firmware/EFI]  && ECHO EFI \| \| ECHO BIOS
OS-namn/version | Vi kommer åt de här filerna för operativ systemets version och namn:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Operativsystemarkitektur | Uname-m
Antal diskar | fdisk-l \| egrep ' disk. * byte ' \| awk ' {Print $2} ' \| cut-F1-d ': '
Start disk | DF/Boot \| -sed-n 2p \| awk ' {Print $1} '
Diskstorlek | fdisk-l \| egrep ' disk. * byte ' \| egrep $disk: \| awk ' {Print $5} '
NIC-lista | IP-o-4 addr show \| awk ' {print $2} '
IP-adress för nätverkskort | IP-adress Visa $nic \| grep inet \| awk {Print $2} \| Klipp ut F1-d "/" 
MAC-adress för nätverkskort | IP-adress Visa $nic \| grep eter  \| awk {Print $2}

### <a name="windows-performance-data"></a>Prestanda data för Windows

Här är prestanda data för Windows Server som enheten samlar in och skickar till Azure.

**Data** | **WMI-klass** | **Egenskap för WMI-klass**
--- | --- | ---
CPU-användning | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Minnesanvändning | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Antal nätverkskort | Win32_PerfFormattedData_Tcpip_NetworkInterface | Hämta antalet nätverks enheter.
Mottagna data per nätverkskort | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Överförda data per nätverkskort | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Antal diskar | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Antal diskar
Disk information | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Prestanda data för Linux

Här är de prestanda data för Linux-servern som enheten samlar in och skickar till Azure.

**Data** | **Linux** 
--- | --- 
CPU-användning | katt/proc/stat/| grep ' CPU '-/proc/stat
Minnesanvändning | gratis \| grep MEM- \| awk {Print $3/$ 2 * 100,0}
Antal nätverkskort | lshw-klass nätverk \| grep ETH [0-60] \| WC-l
Mottagna data per nätverkskort | katt/sys/Class/net/ETH $ NIC/statistik/rx_bytes
Överförda data per nätverkskort | katt/sys/Class/net/ETH $ NIC/statistik/tx_bytes
Antal diskar | fdisk-l \| egrep ' disk. * byte ' \| awk ' {Print $2} ' \| cut-F1-d ': '
Disk information | katt/proc/diskstats


## <a name="appliance-upgrades"></a>Installations program

Installationen uppgraderas eftersom Azure Migrate agenter som körs på enheten uppdateras. Detta sker automatiskt, eftersom automatisk uppdatering aktive ras på-enheten som standard. Du kan ändra den här standardinställningen för att uppdatera installations tjänsterna manuellt.

### <a name="turn-off-auto-update"></a>Inaktivera automatisk uppdatering

1. Öppna Registereditorn på den dator som kör-enheten.
2. Navigera till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Om du vill inaktivera automatisk uppdatering skapar du en register nyckel för automatisk **uppdatering** med DWORD-värdet 0.

    ![Ange register nyckel](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Aktivera automatisk uppdatering

Du kan aktivera automatisk uppdatering med någon av följande metoder:

- Genom att ta bort register nyckeln AutoUpdate från HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Klicka på **Visa apparat-tjänster** från de senaste uppdaterings kontrollerna på panelen **Konfigurera krav** för att aktivera automatisk uppdatering.

Ta bort register nyckeln:

1. Öppna Registereditorn på den dator som kör-enheten.
2. Navigera till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Ta bort den automatiska **uppdateringen** av register nyckeln som tidigare har skapats för att inaktivera automatisk uppdatering.

Om du vill aktivera från installations Configuration Manager efter att identifieringen har slutförts:

1. I Konfigurations hanteraren för installation går du till **Konfigurera krav** panelen
2. I den senaste uppdaterings kontrollen klickar du på **Visa apparat tjänster** och klickar på länken för att aktivera automatisk uppdatering.

    ![Aktivera automatiska uppdateringar](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Kontrol lera version för version

Du kan kontrol lera versionen av versions tjänsterna med någon av följande metoder:

- I Konfigurations hanteraren för utrustning går du till **Konfigurera krav** panelen.
- I   >  **program och funktioner** på kontroll panelen på datorn.

För att kontrol lera installations hanteraren för installationen:

1. I Konfigurations hanteraren för installation går du till **Konfigurera krav** panelen
2. Klicka på **Visa apparat tjänster** i den senaste uppdaterings kontrollen.

    ![Kontrol lera version](./media/migrate-appliance/versions.png)

Så här kontrollerar du i kontroll panelen:

1. Klicka på **Starta**  >    >  **program och funktioner** på kontroll panelen på enheten
2. Kontrol lera versions service versionerna i listan.

    ![Kontrol lera version på kontroll panelen](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Uppdatera en äldre version manuellt

Om du kör en äldre version för någon av komponenterna måste du avinstallera tjänsten och manuellt uppdatera till den senaste versionen.

1. Om du vill söka efter de senaste versionerna av service-tjänsterna [laddar du ned](https://aka.ms/latestapplianceservices) LatestComponents.jspå filen.
2.    När du har laddat ned öppnar du LatestComponents.jsfilen i anteckningar.
3. Hitta den senaste tjänst versionen i filen och nedladdnings länken för den. Exempel:

    "Namn": "ASRMigrationWebApp", "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.    Hämta den senaste versionen av en föråldrad tjänst med hjälp av länken Hämta i filen.
5. När du har laddat ned kör du följande kommando i ett administratörs kommando fönster för att kontrol lera integriteten för den hämtade MSI-filen.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Exempel: C: \> certutil-HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Kontrol lera att kommandots utdata matchar hash-värdens post för tjänsten i filen (till exempel MD5 hash-värdet ovan).
6. Kör nu MSI för att installera tjänsten. Det är en tyst installation och installations fönstret stängs när det är färdigt.
7. När installationen är klar kontrollerar du tjänstens version i **kontroll panelens**  >  **program och funktioner**. Tjänste versionen bör nu uppgraderas till den senaste som visas i JSON-filen.



## <a name="next-steps"></a>Nästa steg

- [Lär dig hur](how-to-set-up-appliance-vmware.md) du konfigurerar-installationen för VMware.
- [Lär dig hur](how-to-set-up-appliance-hyper-v.md) du konfigurerar-enheten för Hyper-V.
- [Lär dig hur](how-to-set-up-appliance-physical.md) du konfigurerar installationen av fysiska servrar.

