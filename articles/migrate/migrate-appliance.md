---
title: Azure Migrate-installation
description: Innehåller en sammanfattning av stödet för den Azure Migrate-enheten.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: dadca1fadef9d2967f20cae13e40d01de73d39e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778346"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-installation

I den här artikeln sammanfattas förutsättningarna och support kraven för Azure Migrate-enheten.

## <a name="deployment-scenarios"></a>Distributionsscenarier

Azure Migrates apparaten används i följande scenarier.

**Scenario** | **Verktyg** | **Används för att**
--- | --- | ---
**Identifiering och utvärdering av servrar som körs i VMware-miljön** | Azure Migrate: identifiering och utvärdering | Identifiera servrar som körs i VMware-miljön<br/><br/> Utför identifiering av installerad program varu inventering, agent beroende analys och identifiering SQL Server instanser och databaser.<br/><br/> Samla in Server konfiguration och prestanda-metadata för utvärderingar.
**Migrering utan agent för servrar som körs i VMware-miljön** | Azure Migrate: Server-migrering | Identifiera servrar som körs i VMware-miljön. <br/><br/> Replikera servrar utan att installera några agenter på dem.
**Identifiering och utvärdering av servrar som körs i Hyper-V-miljön** | Azure Migrate: identifiering och utvärdering | Identifiera servrar som körs i din Hyper-V-miljö.<br/><br/> Samla in Server konfiguration och prestanda-metadata för utvärderingar.
**Identifiering och utvärdering av fysiska eller virtualiserade servrar lokalt** |  Azure Migrate: identifiering och utvärdering |  Identifiera fysiska eller virtualiserade servrar lokalt.<br/><br/> Samla in Server konfiguration och prestanda-metadata för utvärderingar.

## <a name="deployment-methods"></a>Distributions metoder

Installationen kan distribueras med ett par olika metoder:

- Installationen kan distribueras med hjälp av en mall för servrar som kör i VMware-eller Hyper-V-miljön ([ägg-mall för VMware](how-to-set-up-appliance-vmware.md) eller [VHD för Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Om du inte vill använda en mall kan du distribuera-installationen för VMware-eller Hyper-V-miljön med hjälp av ett [PowerShell-skript](deploy-appliance-script.md).
- I Azure Government bör du distribuera installationen med hjälp av ett PowerShell-skript. Se distributions stegen [här](deploy-appliance-script-government.md).
- För fysiska eller virtualiserade servrar lokalt eller i andra moln distribuerar du alltid installationen med hjälp av ett PowerShell-skript. Se distributions stegen [här](how-to-set-up-appliance-physical.md).
- Nedladdnings länkarna är tillgängliga i tabellerna nedan.

## <a name="appliance---vmware"></a>Utrustning – VMware

I följande tabell sammanfattas kraven för Azure Migrate-installationen för VMware.

> [!Note]
> Identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är nu i för hands version. Om du vill testa funktionen använder du [**den här länken**](https://aka.ms/AzureMigrate/SQL) till att skapa ett projekt i regionen **Australien, östra**. Om du redan har ett projekt i Australien, östra och vill prova den här funktionen måste du se till att du har slutfört dessa [**krav**](how-to-discover-sql-existing-project.md) på portalen.

**Krav** | **VMware**
--- | ---
**Behörigheter** | Om du vill få åtkomst till Configuration Manager lokalt eller via fjärr anslutning måste du ha ett lokalt eller domän användar konto med administratörs behörighet på installations servern.
**Apparat tjänster** | Enheten har följande tjänster:<br/><br/> - Installation av **Configuration Manager**: det här är ett webb program som kan konfigureras med käll information för att starta identifieringen och utvärderingen av servrar.<br/> - **VMware Discovery agent**: agenten samlar in metadata för Server konfigurationen som kan användas för att skapa som lokala utvärderingar.<br/>- **VMware Assessment agent**: agenten samlar in metadata för Server prestanda som kan användas för att skapa prestandabaserade utvärderingar.<br/>- **Tjänsten automatisk uppdatering**: tjänsten håller alla agenter som körs på enheten uppdaterade. Den körs automatiskt en gång var 24: e timme.<br/>- **Dra-agent**: dirigerar Server replikering och koordinerar kommunikationen mellan replikerade servrar och Azure. Används endast när du replikerar servrar till Azure med hjälp av en agent lös migrering.<br/>- **Gateway**: skickar replikerade data till Azure. Används endast när du replikerar servrar till Azure med hjälp av en agent lös migrering.<br/>- **SQL-identifiering och utvärderings agent**: skickar metadata för konfiguration och prestanda för SQL Server instanser och databaser till Azure.
**Projekt gränser** |  En installation kan bara registreras med ett enda projekt.<br/> Ett enda projekt kan ha flera registrerade apparater.
**Identifierings gränser** | En apparat kan identifiera upp till 10 000 servrar som körs på en vCenter Server.<br/> En apparat kan ansluta till en enda vCenter Server.
**Distribution som stöds** | Distribuera som ny server som körs på vCenter Server med hjälp av en kontraktmall-mall.<br/><br/> Distribuera på en befintlig server som kör Windows Server 2016 med PowerShell Installer-skript.
**Mall för ägg** | Hämta från Project [eller härifrån](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> Hämtnings storleken är 11,9 GB.<br/><br/> Mallen för hämtade installationer innehåller en utvärderings licens för Windows Server 2016, som är giltig i 180 dagar.<br/>Om utvärderings perioden ligger nära förfallo datum, rekommenderar vi att du laddar ned och distribuerar en ny enhet med hjälp av en områdesmall, eller att du aktiverar operativ Systems licensen för installations servern.
**Enhets verifiering** | [Verifiera](tutorial-discover-vmware.md#verify-security) den ägg-mall som hämtats från projektet genom att kontrol lera hash-värdena.
**PowerShell-skript** | Läs den här [artikeln](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) om hur du distribuerar en installation med hjälp av PowerShell Installer-skriptet.<br/><br/> 
**Maskin-och nätverks krav** |  Installationen ska köras på server med Windows Server 2016, 32 GB RAM, 8 virtuella processorer, runt 80 GB disk lagring och en extern virtuell växel.<br/> Enheten kräver Internet åtkomst, antingen direkt eller via en proxyserver.<br/><br/> Om du distribuerar enheten med hjälp av en tjänstmall-mall behöver du tillräckligt med resurser på vCenter Server för att skapa en server som uppfyller maskin varu kraven.<br/><br/> Om du kör-installationen på en befintlig server kontrollerar du att den kör Windows Server 2016 och uppfyller maskin varu kraven.<br/>_(För närvarande stöds endast distributionen av installations programmet på Windows Server 2016.)_
**Krav för VMware** | Om du distribuerar installationen som en server på vCenter Server måste den distribueras på en vCenter Server som kör 5,5, 6,0, 6,5 eller 6,7 och en ESXi-värd som kör version 5,5 eller senare.<br/><br/> 
**VDDK (utan agent migrering)** | För att kunna utnyttja installationen av servrar utan agent måste VMware vSphere VDDK installeras på installations servern.

## <a name="appliance---hyper-v"></a>Apparat-Hyper-V

**Krav** | **Hyper-V**
--- | ---
**Behörigheter** | Om du vill få åtkomst till Configuration Manager lokalt eller via fjärr anslutning måste du ha ett lokalt eller domän användar konto med administratörs behörighet på installations servern.
**Apparat tjänster** | Enheten har följande tjänster:<br/><br/> - Installation av **Configuration Manager**: det här är ett webb program som kan konfigureras med käll information för att starta identifieringen och utvärderingen av servrar.<br/> - **Identifierings agent**: agenten samlar in metadata för Server konfigurationen som kan användas för att skapa som lokala utvärderingar.<br/>- **Bedömnings agent**: agenten samlar in metadata för Server prestanda som kan användas för att skapa prestandabaserade utvärderingar.<br/>- **Tjänsten automatisk uppdatering**: tjänsten håller alla agenter som körs på enheten uppdaterade. Den körs automatiskt en gång var 24: e timme.
**Projekt gränser** |  En installation kan bara registreras med ett enda projekt.<br/> Ett enda projekt kan ha flera registrerade apparater.
**Identifierings gränser** | En installation kan identifiera upp till 5000 servrar som körs i Hyper-V-miljön.<br/> En apparat kan ansluta till upp till 300 Hyper-V-värdar.
**Distribution som stöds** | Distribuera som en server som körs på en Hyper-V-värd med en VHD-mall.<br/><br/> Distribuera på en befintlig server som kör Windows Server 2016 med PowerShell Installer-skript.
**VHD-mall** | Zip-fil som innehåller en virtuell hård disk. Hämta från Project eller härifrån [.](https://go.microsoft.com/fwlink/?linkid=2140422)<br/><br/> Hämtnings storleken är 8,91 GB.<br/><br/> Mallen för hämtade installationer innehåller en utvärderings licens för Windows Server 2016, som är giltig i 180 dagar.<br/> Om utvärderings perioden ligger nära förfallo datum, rekommenderar vi att du laddar ned och distribuerar en ny installation, eller att du aktiverar operativ Systems licensen för installations servern.
**VHD-verifiering** | [Verifiera](tutorial-discover-hyper-v.md#verify-security) den VHD-mall som hämtats från projektet genom att kontrol lera hash-värdena.
**PowerShell-skript** | Läs den här [artikeln](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) om hur du distribuerar en installation med hjälp av PowerShell Installer-skriptet.<br/>
**Maskin-och nätverks krav**  |  Installationen ska köras på server med Windows Server 2016, 16 GB RAM, 8 virtuella processorer, cirka 80 GB disk lagring och en extern virtuell växel.<br/> Installationen behöver en statisk eller dynamisk IP-adress och kräver Internet åtkomst, antingen direkt eller via en proxyserver.<br/><br/> Om du kör-installationen som en server som körs på en Hyper-V-värd behöver du tillräckligt med resurser på värden för att skapa en server som uppfyller maskin varu kraven.<br/><br/> Om du kör-installationen på en befintlig server kontrollerar du att den kör Windows Server 2016 och uppfyller maskin varu kraven.<br/>_(För närvarande stöds endast distributionen av installations programmet på Windows Server 2016.)_
**Krav för Hyper-V** | Om du distribuerar-installationen med VHD-mallen, är installationen av Hyper-V VM version 5,0 som tillhandahålls av Azure Migrate.<br/><br/> Hyper-V-värden måste köra Windows Server 2012 R2 eller senare.

## <a name="appliance---physical"></a>Apparat-fysisk

**Krav** | **Fysisk**
--- | ---
**Behörigheter** | Om du vill få åtkomst till Configuration Manager lokalt eller via fjärr anslutning måste du ha ett lokalt eller domän användar konto med administratörs behörighet på installations servern.
**Apparat tjänster** | Enheten har följande tjänster:<br/><br/> - Installation av **Configuration Manager**: det här är ett webb program som kan konfigureras med käll information för att starta identifieringen och utvärderingen av servrar.<br/> - **Identifierings agent**: agenten samlar in metadata för Server konfigurationen som kan användas för att skapa som lokala utvärderingar.<br/>- **Bedömnings agent**: agenten samlar in metadata för Server prestanda som kan användas för att skapa prestandabaserade utvärderingar.<br/>- **Tjänsten automatisk uppdatering**: tjänsten håller alla agenter som körs på enheten uppdaterade. Den körs automatiskt en gång var 24: e timme.
**Projekt gränser** |  En installation kan bara registreras med ett enda projekt.<br/> Ett enda projekt kan ha flera registrerade apparater.<br/>
**Identifierings gränser** | En apparat kan identifiera upp till 1000 fysiska servrar.
**Distribution som stöds** | Distribuera på en befintlig server som kör Windows Server 2016 med PowerShell Installer-skript.
**PowerShell-skript** | Hämta skriptet (AzureMigrateInstaller.ps1) i en zip- [fil från projektet eller härifrån.](https://go.microsoft.com/fwlink/?linkid=2140334) [Läs mer](tutorial-discover-physical.md).<br/><br/> Hämtnings storleken är 85,8 MB.
**Skript verifiering** | [Kontrol lera](tutorial-discover-physical.md#verify-security) att PowerShell-installationsprogrammet som hämtats från projektet genom att kontrol lera hash-värdena.
**Maskin-och nätverks krav** |  Installationen ska köras på server med Windows Server 2016, 16 GB RAM, 8 virtuella processorer, cirka 80 GB disk lagring.<br/> Installationen behöver en statisk eller dynamisk IP-adress och kräver Internet åtkomst, antingen direkt eller via en proxyserver.<br/><br/> Om du kör-installationen på en befintlig server kontrollerar du att den kör Windows Server 2016 och uppfyller maskin varu kraven.<br/>_(För närvarande stöds endast distributionen av installations programmet på Windows Server 2016.)_

## <a name="url-access"></a>URL-åtkomst

Azure Migrate-utrustningen behöver anslutning till Internet.

- När du distribuerar installationen kontrollerar Azure Migrate en anslutning till de begärda URL: erna.
- Du måste tillåta åtkomst till alla webb adresser i listan. Om du bara utför utvärderingen kan du hoppa över de URL: er som marker ATS som obligatoriska för migrering av VMware-agenten.
- Om du använder en URL-baserad proxy för att ansluta till Internet, måste du kontrol lera att proxyn matchar eventuella CNAME-poster som tas emot vid sökning av URL: erna.

### <a name="public-cloud-urls"></a>Offentliga moln-URL: er

**URL** | **Information**  
--- | --- |
*.portal.azure.com  | Gå till Azure-portalen.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *. live.com <br/> *. office.com | Logga in på din Azure-prenumeration.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Skapa Azure Active Directory (AD) appar för att kunna kommunicera med Azure Migrate.
management.azure.com | Skapa Azure AD-appar för att enheten ska kunna kommunicera med Azure Migrate.
*.services.visualstudio.com | Ladda upp installations loggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault.<br/> Obs! se till att servrar som ska replikeras har åtkomst till detta.
aka.ms/* | Tillåt åtkomst till aka-länkar; används för att hämta och installera de senaste uppdateringarna för installations tjänster.
download.microsoft.com/download | Tillåt hämtning från Microsoft Download Center.
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
*.services.visualstudio.com | Ladda upp installations loggar som används för intern övervakning.
*. vault.usgovcloudapi.net | Hantera hemligheter i Azure Key Vault.
aka.ms/* | Tillåt åtkomst till aka-länkar; används för att hämta och installera de senaste uppdateringarna för installations tjänster.
download.microsoft.com/download | Tillåt hämtning från Microsoft Download Center.
*. servicebus.usgovcloudapi.net  | Kommunikation mellan installations programmet och tjänsten Azure Migrate.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Anslut till Azure Migrate tjänst-URL: er.
*. hypervrecoverymanager.windowsazure.us | **Används för migrering av VMware-agent**<br/><br/> Anslut till Azure Migrate tjänst-URL: er.
*. blob.core.usgovcloudapi.net  |  **Används för migrering av VMware-agent**<br/><br/>Överför data till lagring för migrering.
*. applicationinsights.us | Ladda upp installations loggar som används för intern övervakning.

## <a name="collected-data---vmware"></a>Insamlade data – VMware

Installations programmet samlar in konfigurations metadata, prestanda-metadata och Server beroende data (om agenten beroende [analys](concepts-dependency-visualization.md) används).

### <a name="metadata"></a>Metadata

Metadata som identifieras av Azure Migrate-enheten hjälper dig att ta reda på om servrarna är klara för migrering till Azure, servrar med rätt storlek, planerar kostnader och analys av program beroenden. Microsoft använder inte dessa data i någon granskning av licens efterlevnad.

Här är en fullständig lista över de Server-metadata som enheten samlar in och skickar till Azure.

**DATA** | **MEDELVÄRDE**
--- | ---
**Serverinformation** |
Server-ID | vm.Config. InstanceUuid
Servernamn | vm.Config. Namn
vCenter Server-ID | VMwareClient. instance. uuid
Server Beskrivning | vm.Summary.Config. Antecknings
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
Information om mappar per server | ((Mapp)-behållare). ChildEntity. Type
Data Center information per server | ((Data Center)-behållare). VmFolder
Data Center information per värd-mapp | ((Data Center)-behållare). HostFolder
Kluster information per värd | ((ClusterComputeResource) container). Värd
Värd information per server | ((HostSystem) container). DATORN

### <a name="performance-data"></a>Prestandadata

Här är de prestanda data som en apparat samlar in för en server som körs på VMware och skickar till Azure.

**Data** | **Räknare** | **Utvärderings påverkan**
--- | --- | ---
CPU-användning | processor. Usage. genomsnitt | Rekommenderad Server storlek/kostnad
Minnes användning | minnes användning. genomsnitt | Rekommenderad Server storlek/kostnad
Disk läsnings data flöde (MB per sekund) | virtualDisk. Read. Average | Beräkning för disk storlek, lagrings kostnad, Server storlek
Disk skrivnings data flöde (MB per sekund) | virtualDisk. Write. Average | Beräkning för disk storlek, lagrings kostnad, Server storlek
Disk läsnings åtgärder per sekund | virtualDisk. numberReadAveraged. Average | Beräkning för disk storlek, lagrings kostnad, Server storlek
Disk skrivnings åtgärder per sekund | virtualDisk. numberWriteAveraged. Average  | Beräkning för disk storlek, lagrings kostnad, Server storlek
NIC-läst data flöde (MB per sekund) | net. Received. Average | Beräkning för Server storlek
NÄTVERKSKORT skriver data flöde (MB per sekund) | net. överföring. genomsnitt  |Beräkning för Server storlek

### <a name="installed-software-inventory"></a>Installerad program varu inventering

Enheten samlar in data om installerad program varu inventering på servrar.

#### <a name="windows-server-software-inventory-data"></a>Inventerings data för Windows Server-programvara

Här är program varu inventerings data som installationen samlar in från varje Windows Server som identifieras i VMware-miljön.

**Data** | **Registerplats** | **Nyckel**
--- | --- | ---
Programnamn  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Version  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion
Leverantör  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>Windows Server-funktioner-data

Här är de funktions data som installationen samlar in från varje Windows Server som identifieras i VMware-miljön.

**Data**  | **PowerShell-cmdlet** | **Egenskap**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Funktions typ | Get-WindowsFeature  | FeatureType
Överordnad  | Get-WindowsFeature  | Överordnad

#### <a name="sql-server-metadata"></a>SQL Server metadata

Här är de SQL Server data som enheten samlar in från varje Windows Server som identifieras i VMware-miljön.

**Data**  | **Registerplats**  | **Nyckel**
--- | --- | ---
Name  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Utgåva  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Utgåva
Service Pack  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | SP
Version  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Version

#### <a name="windows-server-operating-system-data"></a>Operativ system data för Windows Server

Här är de operativ Systems data som installations programmet samlar in från varje Windows Server som identifieras i VMware-miljön.

**Data**  | **WMI-klass**  | **Egenskap för WMI-klass**
--- | --- | ---
Name  | Win32_operatingsystem  | Caption
Version  | Win32_operatingsystem  | Version
Arkitektur  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Inventerings data för Linux server-programvara

Här är de program varu inventerings data som enheten samlar in från varje Linux-server som identifieras i VMware-miljön. Ett eller flera av kommandona körs baserat på serverns operativ system.

**Data**  | **Kommandon**
--- | ---
Name | RPM, dpkg-fråga, fäst
Version | RPM, dpkg-fråga, fäst
Leverantör | RPM, dpkg-fråga, fäst

#### <a name="linux-server-operating-system-data"></a>Operativ system data för Linux-Server

Här är de operativ Systems data som enheten samlar in från varje Linux-server som identifieras i VMware-miljön.

**Data**  | **Kommandon**
--- | ---
Name <br/> version | Samlas in från en eller flera av följande filer:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version
Arkitektur | uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server instanser och databas data

Enheten samlar in data på SQL Server instanser och databaser.

> [!Note]
> Identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är nu i för hands version. Om du vill testa funktionen använder du [**den här länken**](https://aka.ms/AzureMigrate/SQL) till att skapa ett projekt i regionen **Australien, östra**. Om du redan har ett projekt i Australien, östra och vill prova den här funktionen måste du se till att du har slutfört dessa [**krav**](how-to-discover-sql-existing-project.md) på portalen.

#### <a name="sql-database-metadata"></a>Metadata för SQL-databas

**Metadata för databas** | **Vyer/SQL Server egenskaper**
--- | ---
Unikt ID för databasen | sys.databases
Server-definierat databas-ID | sys.databases
Namn på databasen | sys.databases
Kompatibilitetsnivå för databas | sys.databases
Sorterings namn för databas | sys.databases
Tillstånd för databasen | sys.databases
Databasens storlek (i MB) | sys.master_files
Enhets beteckning för platsen som innehåller datafiler | SERVERPROPERTY och Software\Microsoft\MSSQLServer\MSSQLServer
Lista över databasfiler | sys. Databass, sys.master_files
Service Broker är aktiverat eller inte | sys.databases
Databasen har Aktiver ATS för registrering av ändrings data | sys.databases

#### <a name="sql-server-metadata"></a>SQL Server metadata

**Serverns metadata** | **Vyer/SQL Server-egenskaper**
--- | ---
Servernamn |SERVERPROPERTY
FQDN | Anslutnings sträng härledd från identifiering av installerade program
Installations-ID | sys.dm_server_registry
Serverversion | SERVERPROPERTY
Server Edition | SERVERPROPERTY
Server värd plattform (Windows/Linux) | SERVERPROPERTY
Serverns produkt nivå (RTM SP CTP) | SERVERPROPERTY
Standard Sök väg för säkerhets kopiering | SERVERPROPERTY
Standard Sök väg för datafilerna | SERVERPROPERTY och Software\Microsoft\MSSQLServer\MSSQLServer
Standard Sök väg för loggfilerna | SERVERPROPERTY och Software\Microsoft\MSSQLServer\MSSQLServer
Nej. av kärnor på servern | sys.dm_os_schedulers sys.dm_os_sys_info
Server sorterings namn | SERVERPROPERTY
Nej. av kärnor på servern med status synlig ONLINE | sys.dm_os_schedulers
Unikt server-ID | sys.dm_server_registry
HA aktiverat eller inte | SERVERPROPERTY
Buffertpooltillägget är aktiverat eller inte | sys.dm_os_buffer_pool_extension_configuration
Redundanskluster har kon figurer ATS eller inte | SERVERPROPERTY
Server som endast använder Windows-autentiseringsläge | SERVERPROPERTY
Server installerar PolyBase | SERVERPROPERTY
Nej. av logiska processorer i systemet | sys.dm_server_registry sys.dm_os_sys_info
Förhållandet mellan antalet logiska eller fysiska kärnor som exponeras av ett fysiskt processor paket | sys.dm_os_schedulers sys.dm_os_sys_info
Inga fysiska processorer i systemet | sys.dm_os_schedulers sys.dm_os_sys_info
Datum och tid då servern senast startades | sys.dm_server_registry
Maximal användning av server minne (i MB) | sys.dm_os_process_memory
Totalt antal. användare i alla databaser | sys. databases, sys. logins
Total storlek på alla användar databaser | sys.databases
Storlek på tillfällig databas | sys.master_files, sys.configurations, sys.dm_os_sys_info
Nej. av inloggningar | sys. logins
Lista över länkade servrar | sys. servers
Lista över Agent jobb | [msdb]. [dbo]. [sysjobs], [sys]. [syslogins], [msdb]. [dbo]. [syscategories]

### <a name="performance-metadata"></a>Metadata för prestanda

**Prestanda** | **Vyer/SQL Server-egenskaper** | **Utvärderings påverkan**
--- | --- | ---
SQL Server processor användning| sys.dm_os_ring_buffers| Rekommenderad SKU-storlek (CPU-dimension)
Antal logiska SQL-processorer| sys.dm_os_sys_info| Rekommenderad SKU-storlek (CPU-dimension)
SQL fysiskt minne som används| sys.dm_os_process_memory| Outnyttja
Procent andel minnes användning i SQL| sys.dm_os_process_memory | Outnyttja
PROCESSOR användning i databas| sys.dm_exec_query_stats sys.dm_exec_plan_attributes| Rekommenderad SKU-storlek (CPU-dimension)
Databas minne som används (buffert)| sys.dm_os_buffer_descriptors| Rekommenderad SKU-storlek (minnes dimension)
Läs/skriv-IO för fil| sys.dm_io_virtual_file_stats sys.master_files| Rekommenderad SKU-storlek (IO-dimension)
Fil antal läsningar/skrivningar| sys.dm_io_virtual_file_stats sys.master_files| Rekommenderad SKU-storlek (data flödes dimension)
Läs/skriv-/Skriv läsning av fil IO (MS)| sys.dm_io_virtual_file_stats sys.master_files| Rekommenderad SKU-storlek (i/o-latens dimension)
Filstorlek| sys.master_files| Rekommenderad SKU-storlek (lagrings dimension)


### <a name="application-dependency-data"></a>Program beroende data

Beroende analys av agent samlar in anslutningen och bearbeta data.

#### <a name="windows-server-dependencies-data"></a>Windows Server-beroende data

Här är de anslutnings data som enheten samlar in från varje Windows Server, aktive rad för en agent lös beroende analys.

**Data** | **Kommandon**
--- | ---
Lokal port | Netstat
Lokal IP-adress | Netstat
Fjärr-IP-adress | Netstat
Fjärr-IP-adress | Netstat
Status för TCP-anslutning | Netstat
Process-ID | Netstat
Antal aktiva anslutningar | Netstat

Här är de anslutnings data som enheten samlar in från varje Windows Server, aktive rad för en agent lös beroende analys.

**Data** | **WMI-klass** | **Egenskap för WMI-klass**
--- | --- | ---
Processnamn | Win32_Process | ExecutablePath
Process argument | Win32_Process | Raden
Programnamn | Win32_Process | VersionInfo. ProductName-parameter för egenskapen ExecutablePath

#### <a name="linux-server-dependencies-data"></a>Data för Linux server-beroenden

Här är de anslutnings data som enheten samlar in från varje Linux-server, aktive rad för en agent lös beroende analys.

**Data** | **Kommandon**
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

Installations programmet samlar in konfigurations-och prestanda-metadata från servrar som körs i Hyper-V-miljön.

### <a name="metadata"></a>Metadata
Metadata som upptäckts av Azure Migrate-enheten hjälper dig att ta reda på om servrarna är klara för migrering till Azure, servrar med rätt storlek och kostnader. Microsoft använder inte dessa data i någon granskning av licens efterlevnad.

Här är en fullständig lista över de Server-metadata som enheten samlar in och skickar till Azure.

**Data** | **WMI-klass** | **Egenskap för WMI-klass**
--- | --- | ---
**Serverinformation** | 
Serie nummer för BIOS | Msvm_BIOSElement | BIOSSerialNumber
Server typ (gen 1 eller 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Serverns visnings namn | Msvm_VirtualSystemSettingData | ElementName
Serverversion | Msvm_ProcessorSettingData | VirtualQuantity
Minne (byte) | Msvm_MemorySettingData | VirtualQuantity
Högsta mängd minne som kan utnyttjas av servern | Msvm_MemorySettingData | Gräns
Dynamiskt minne aktiverat | Msvm_MemorySettingData | DynamicMemoryEnabled
Operativ systemets namn/version/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems namn data
Serverns energi status | Msvm_ComputerSystem | EnabledState
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

Här är Server prestanda data som enheten samlar in och skickar till Azure.

**Prestanda räknar klass** | **Räknare** | **Utvärderings påverkan**
--- | --- | ---
Virtuell Hyper-V hypervisor-processor | % Kör tid för gäst | Rekommenderad Server storlek/kostnad
Dynamiskt minne Server för Hyper-V | Aktuellt tryck (%)<br/> Fysiskt synligt fysiskt minne (MB) | Rekommenderad Server storlek/kostnad
Virtuell lagrings enhet för Hyper-V | Lästa byte per sekund | Beräkning för disk storlek, lagrings kostnad, Server storlek
Virtuell lagrings enhet för Hyper-V | Skrivna byte/sekund | Beräkning för disk storlek, lagrings kostnad, Server storlek
Hyper-V Virtual Network Adapter | Mottagna byte per sekund | Beräkning för Server storlek
Hyper-V Virtual Network Adapter | Skickade byte per sekund | Beräkning för Server storlek

- PROCESSOR användningen är summan av all användning, för alla virtuella processorer som är anslutna till en server.
- Minnes användningen är (aktuellt tryck * fysiskt synligt minne i gäst)/100.
- Värdena för disk-och nätverks användning samlas in från de listade Hyper-V-prestandaräknare.

## <a name="collected-data---physical"></a>Insamlade data-fysiska

Enheten samlar in konfigurations-och prestanda-metadata från fysiska eller virtuella servrar som körs lokalt.

### <a name="metadata"></a>Metadata

Metadata som upptäckts av Azure Migrate-enheten hjälper dig att ta reda på om servrarna är klara för migrering till Azure, servrar med rätt storlek och kostnader. Microsoft använder inte dessa data i någon granskning av licens efterlevnad.

### <a name="windows-server-metadata"></a>Windows Server-metadata

Här är en fullständig lista över Windows Server-metadata som enheten samlar in och skickar till Azure.

**Data** | **WMI-klass** | **Egenskap för WMI-klass**
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

### <a name="linux-server-metadata"></a>Linux-serverns metadata

Här är en fullständig lista över Linux-serverns metadata som enheten samlar in och skickar till Azure.

**Data** | **Kommandon**
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

**Data** | **Kommandon**
--- | ---
CPU-användning | katt/proc/stat/| grep ' CPU '-/proc/stat
Minnesanvändning | gratis \| grep MEM- \| awk {Print $3/$ 2 * 100,0}
Antal nätverkskort | lshw-klass nätverk \| grep ETH [0-60] \| WC-l
Mottagna data per nätverkskort | katt/sys/Class/net/ETH $ NIC/statistik/rx_bytes
Överförda data per nätverkskort | katt/sys/Class/net/ETH $ NIC/statistik/tx_bytes
Antal diskar | fdisk-l \| egrep ' disk. * byte ' \| awk ' {Print $2} ' \| cut-F1-d ': '
Disk information | katt/proc/diskstats


## <a name="appliance-upgrades"></a>Installations program

Installationen uppgraderas eftersom Azure Migrate tjänsterna som körs på installationen uppdateras. Detta sker automatiskt, eftersom automatisk uppdatering aktive ras på-enheten som standard. Du kan ändra den här standardinställningen för att uppdatera installations tjänsterna manuellt.

### <a name="turn-off-auto-update"></a>Inaktivera automatisk uppdatering

1. Öppna Registereditorn på den server som kör-enheten.
2. Navigera till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Om du vill inaktivera automatisk uppdatering skapar du en register nyckel för automatisk **uppdatering** med DWORD-värdet 0.

    ![Ange register nyckel](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Aktivera automatisk uppdatering

Du kan aktivera automatisk uppdatering med någon av följande metoder:

- Genom att ta bort register nyckeln AutoUpdate från HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Klicka på **Visa apparat-tjänster** från de senaste uppdaterings kontrollerna på panelen **Konfigurera krav** för att aktivera automatisk uppdatering.

Ta bort register nyckeln:

1. Öppna Registereditorn på den server som kör-enheten.
2. Navigera till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Ta bort den automatiska **uppdateringen** av register nyckeln som tidigare har skapats för att inaktivera automatisk uppdatering.

Om du vill aktivera från installations Configuration Manager efter att identifieringen har slutförts:

1. I Konfigurations hanteraren för installation går du till **Konfigurera krav** panelen
2. I den senaste uppdaterings kontrollen klickar du på **Visa apparat tjänster** och klickar på länken för att aktivera automatisk uppdatering.

    ![Aktivera automatiska uppdateringar](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Kontrol lera version för version

Du kan kontrol lera versionen av versions tjänsterna med någon av följande metoder:

- I Konfigurations hanteraren för utrustning går du till **Konfigurera krav** panelen.
- I   >  **program och funktioner** på kontroll panelen på enheten.

För att kontrol lera installations hanteraren för installationen:

1. I Konfigurations hanteraren för installation går du till **Konfigurera krav** panelen
2. Klicka på **Visa apparat tjänster** i den senaste uppdaterings kontrollen.

    ![Kontrol lera version](./media/migrate-appliance/versions.png)

Så här kontrollerar du i kontroll panelen:

1. Klicka på **Starta**  >    >  **program och funktioner** på kontroll panelen på enheten
2. Kontrol lera versions service versionerna i listan.

    ![Kontrol lera version på kontroll panelen](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Uppdatera en äldre version manuellt

Om du kör en äldre version för någon av tjänsterna, måste du avinstallera tjänsten och manuellt uppdatera till den senaste versionen.

1. Om du vill söka efter de senaste versionerna av service-tjänsterna [laddar du ned](https://aka.ms/latestapplianceservices) LatestComponents.jspå filen.
2. När du har laddat ned öppnar du LatestComponents.jsfilen i anteckningar.
3. Hitta den senaste tjänst versionen i filen och nedladdnings länken för den. Exempel:

    "Namn": "ASRMigrationWebApp", "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4. Hämta den senaste versionen av en föråldrad tjänst med hjälp av länken Hämta i filen.
5. När du har laddat ned kör du följande kommando i ett administratörs kommando fönster för att kontrol lera integriteten för den hämtade MSI-filen.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Exempel: C: \> certutil-HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Kontrol lera att kommandots utdata matchar hash-värdens post för tjänsten i filen (till exempel MD5 hash-värdet ovan).
6. Kör nu MSI för att installera tjänsten. Det är en tyst installation och installations fönstret stängs när det är färdigt.
7. När installationen är klar kontrollerar du tjänstens version i **kontroll panelens**  >  **program och funktioner**. Tjänste versionen bör nu uppgraderas till den senaste som visas i JSON-filen.

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur](how-to-set-up-appliance-vmware.md) du konfigurerar-installationen för VMware.
- [Lär dig hur](how-to-set-up-appliance-hyper-v.md) du konfigurerar-enheten för Hyper-V.
- [Lär dig hur](how-to-set-up-appliance-physical.md) du konfigurerar installationen av fysiska servrar.