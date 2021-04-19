---
title: Azure Migrate-installation
description: Innehåller en sammanfattning av stödet för Azure Migrate installationen.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: d7fc04e65e2b79d43c48acd5a8c621f28d5c0403
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714676"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-installation

Den här artikeln sammanfattar krav och supportkrav för Azure Migrate installation.

## <a name="deployment-scenarios"></a>Distributionsscenarier

Den Azure Migrate enheten används i följande scenarier.

**Scenario** | **Verktyg** | **Används för att**
--- | --- | ---
**Identifiering och utvärdering av servrar som körs i VMware-miljön** | Azure Migrate: Identifiering och utvärdering | Identifiera servrar som körs i din VMware-miljö<br/><br/> Utför identifiering av installerad programvaruinventering, agentlös beroendeanalys och identifiera SQL Server instanser och databaser.<br/><br/> Samla in serverkonfiguration och prestandametadata för utvärderingar.
**Agentlös migrering av servrar som körs i VMware-miljön** | Azure Migrate:Server Migration | Identifiera servrar som körs i din VMware-miljö. <br/><br/> Replikera servrar utan att installera några agenter på dem.
**Identifiering och utvärdering av servrar som körs i Hyper-V-miljö** | Azure Migrate: Identifiering och utvärdering | Identifiera servrar som körs i Hyper-V-miljön.<br/><br/> Samla in serverkonfiguration och prestandametadata för utvärderingar.
**Identifiering och utvärdering av fysiska eller virtualiserade servrar lokalt** |  Azure Migrate: Identifiering och utvärdering |  Identifiera fysiska eller virtualiserade servrar lokalt.<br/><br/> Samla in serverkonfiguration och prestandametadata för utvärderingar.

## <a name="deployment-methods"></a>Distributionsmetoder

Installationen kan distribueras på ett par olika sätt:

- Installationen kan distribueras med hjälp av en mall för servrar som körs i VMware eller Hyper-V-miljö[(OVA-mall för VMware](how-to-set-up-appliance-vmware.md) eller [VHD för Hyper-V).](how-to-set-up-appliance-hyper-v.md)
- Om du inte vill använda en mall kan du distribuera installationen för VMware- eller Hyper-V-miljön med hjälp av ett [PowerShell-installationsskript.](deploy-appliance-script.md)
- I Azure Government bör du distribuera installationen med hjälp av ett skript för Installationsprogram för PowerShell. Se distributionsstegen [här.](deploy-appliance-script-government.md)
- För fysiska eller virtualiserade servrar lokalt eller i andra moln distribuerar du alltid installationen med hjälp av ett PowerShell-installationsskript. Se distributionsstegen [här.](how-to-set-up-appliance-physical.md)
- Nedladdningslänkar finns i tabellerna nedan.

## <a name="appliance---vmware"></a>Installation – VMware

I följande tabell sammanfattas Azure Migrate installationskraven för VMware.

> [!Note]
> Identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är nu i förhandsversion. Om du vill testa funktionen använder du [**den här länken**](https://aka.ms/AzureMigrate/SQL) till att skapa ett projekt i regionen **Australien, östra**. Om du redan har ett projekt i Australien, östra och vill prova den här funktionen måste du se till att du har slutfört dessa [**krav**](how-to-discover-sql-existing-project.md) på portalen.

**Krav** | **VMware**
--- | ---
**Behörigheter** | För att få åtkomst till installationens konfigurationshanterare lokalt eller via fjärrkonfiguration måste du ha ett lokalt användarkonto eller ett domänanvändarkonto med administratörsbehörighet på enhetens server.
**Installationstjänster** | Installationen har följande tjänster:<br/><br/> - **Konfigurationshanteraren** för installationen: Det här är en webbapp som kan konfigureras med källinformation för att starta identifieringen och utvärderingen av servrar.<br/> - **VMware-identifieringsagent:** Agenten samlar in metadata för serverkonfiguration som kan användas för att skapa som lokala utvärderingar.<br/>- **VMware-utvärderingsagent:** Agenten samlar in metadata om serverprestanda som kan användas för att skapa prestandabaserade utvärderingar.<br/>- **Tjänst för automatisk** uppdatering: Tjänsten håller alla agenter som körs på installationen uppdaterade. Den körs automatiskt en gång var 24:e timme.<br/>- **DRA-agent:** Samordnar serverreplikeringen och kommunikationen mellan replikerade servrar och Azure. Används endast vid replikering av servrar till Azure med hjälp av agentlös migrering.<br/>- **Gateway:** Skickar replikerade data till Azure. Används endast vid replikering av servrar till Azure med agentlös migrering.<br/>- **SQL-identifierings- och** utvärderingsagent: skickar konfigurations- och prestandametadata för SQL Server instanser och databaser till Azure.
**Projektgränser** |  En apparat kan bara registreras med ett enda projekt.<br/> Ett enda projekt kan ha flera registrerade installationer.
**Identifieringsgränser** | En enhet kan identifiera upp till 10 000 servrar som körs på en vCenter Server.<br/> En enhet kan ansluta till en enda vCenter Server.
**Distribution som stöds** | Distribuera som ny server som körs på vCenter Server med hjälp av en OVA-mall.<br/><br/> Distribuera på en befintlig server som kör Windows Server 2016 med installationsskriptet för PowerShell.
**OVA-mall** | Ladda ned från projektet eller [härifrån](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> Nedladdningsstorleken är 11,9 GB.<br/><br/> Den nedladdade programmallen levereras med en Windows Server 2016-utvärderingslicens som är giltig i 180 dagar.<br/>Om utvärderingsperioden är nära att gå ut rekommenderar vi att du laddar ned och distribuerar en ny installation med hjälp av OVA-mallen , eller att du aktiverar operativsystemlicensen för installationservern.
**OVA-verifiering** | [Kontrollera](tutorial-discover-vmware.md#verify-security) OVA-mallen som laddats ned från projektet genom att kontrollera hash-värdena.
**PowerShell-skript** | Läs den här artikeln [om](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) hur du distribuerar en installation med hjälp av installationsskriptet för PowerShell.<br/><br/> 
**Maskinvaru- och nätverkskrav** |  Installationen ska köras på en server med Windows Server 2016, 32 GB RAM, 8 virtuella processorer, cirka 80 GB disklagring och en extern virtuell växel.<br/> Installationen kräver Internetåtkomst, antingen direkt eller via en proxyserver.<br/><br/> Om du distribuerar installationen med hjälp av en OVA-mall behöver du tillräckligt med resurser på vCenter Server för att skapa en server som uppfyller maskinvarukraven.<br/><br/> Om du kör installationen på en befintlig server kontrollerar du att den kör Windows Server 2016 och uppfyller maskinvarukraven.<br/>_(För närvarande stöds distributionen av installationen endast på Windows Server 2016.)_
**VMware-krav** | Om du distribuerar installationen som en server på vCenter Server måste den distribueras på en vCenter Server som kör 5.5, 6.0, 6.5 eller 6.7 och en ESXi-värd som kör version 5.5 eller senare.<br/><br/> 
**VDDK (agentlös migrering)** | Om du vill utnyttja installationen för agentlös migrering av servrar måste VMware vSphere VDDK installeras på enhetens server.

## <a name="appliance---hyper-v"></a>Installation – Hyper-V

**Krav** | **Hyper-V**
--- | ---
**Behörigheter** | För att få åtkomst till installationens konfigurationshanterare lokalt eller via fjärrkonfiguration måste du ha ett lokalt användarkonto eller ett domänanvändarkonto med administratörsbehörighet på enhetens server.
**Installationstjänster** | Installationen har följande tjänster:<br/><br/> - **Konfigurationshanteraren** för installationen: Det här är en webbapp som kan konfigureras med källinformation för att starta identifieringen och utvärderingen av servrar.<br/> - **Identifieringsagent:** Agenten samlar in metadata för serverkonfiguration som kan användas för att skapa som lokala utvärderingar.<br/>- **Utvärderingsagent:** Agenten samlar in metadata om serverprestanda som kan användas för att skapa prestandabaserade utvärderingar.<br/>- **Tjänst för automatisk** uppdatering: Tjänsten håller alla agenter som körs på installationen uppdaterade. Den körs automatiskt en gång var 24:e timme.
**Projektbegränsningar** |  En apparat kan bara registreras med ett enda projekt.<br/> Ett enda projekt kan ha flera registrerade installationer.
**Identifieringsgränser** | En apparat kan identifiera upp till 5 000 servrar som körs i Hyper-V-miljön.<br/> En installation kan ansluta till upp till 300 Hyper-V-värdar.
**Distribution som stöds** | Distribuera som server som körs på en Hyper-V-värd med hjälp av en VHD-mall.<br/><br/> Distribuera på en befintlig server som kör Windows Server 2016 med hjälp av PowerShell-installationsskriptet.
**VHD-mall** | Zip-fil som innehåller en virtuell hårddisk. Ladda ned från projektet eller [härifrån](https://go.microsoft.com/fwlink/?linkid=2140422).<br/><br/> Nedladdningsstorleken är 8,91 GB.<br/><br/> Den nedladdade programmallen levereras med en Windows Server 2016-utvärderingslicens som är giltig i 180 dagar.<br/> Om utvärderingsperioden är nära att gå ut rekommenderar vi att du laddar ned och distribuerar en ny installation eller att du aktiverar operativsystemslicensen för installationsservern.
**VHD-verifiering** | [Kontrollera](tutorial-discover-hyper-v.md#verify-security) vhd-mallen som laddats ned från projektet genom att kontrollera hash-värdena.
**PowerShell-skript** | Läs den här artikeln [om](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) hur du distribuerar en installation med hjälp av PowerShell-installationsskriptet.<br/>
**Maskinvaru- och nätverkskrav**  |  Installationen ska köras på en server med Windows Server 2016, 16 GB RAM-minne, 8 virtuella processorer, cirka 80 GB disklagring och en extern virtuell växel.<br/> Installationen behöver en statisk eller dynamisk IP-adress och kräver Internetåtkomst, antingen direkt eller via en proxyserver.<br/><br/> Om du kör installationen som en server som körs på en Hyper-V-värd behöver du tillräckligt med resurser på värden för att skapa en server som uppfyller maskinvarukraven.<br/><br/> Om du kör installationen på en befintlig server kontrollerar du att den kör Windows Server 2016 och uppfyller maskinvarukraven.<br/>_(För närvarande stöds distributionen av installationen endast på Windows Server 2016.)_
**Hyper-V-krav** | Om du distribuerar installationen med VHD-mallen är den enhet som tillhandahålls av Azure Migrate virtuell Hyper-V-dator version 5.0.<br/><br/> Hyper-V-värden måste köra Windows Server 2012 R2 eller senare.

## <a name="appliance---physical"></a>Installation – fysisk

**Krav** | **Fysisk**
--- | ---
**Behörigheter** | För att få åtkomst till installationens konfigurationshanterare lokalt eller via fjärrkonfiguration måste du ha ett lokalt användarkonto eller ett domänanvändarkonto med administratörsbehörighet på enhetens server.
**Installationstjänster** | Installationen har följande tjänster:<br/><br/> - **Konfigurationshanteraren** för installationen: Det här är en webbapp som kan konfigureras med källinformation för att starta identifieringen och utvärderingen av servrar.<br/> - **Identifieringsagent:** Agenten samlar in metadata för serverkonfiguration som kan användas för att skapa som lokala utvärderingar.<br/>- **Utvärderingsagent:** Agenten samlar in metadata om serverprestanda som kan användas för att skapa prestandabaserade utvärderingar.<br/>- **Tjänst för automatisk** uppdatering: Tjänsten håller alla agenter som körs på installationen uppdaterade. Den körs automatiskt en gång var 24:e timme.
**Projektgränser** |  En apparat kan bara registreras med ett enda projekt.<br/> Ett enda projekt kan ha flera registrerade installationer.<br/>
**Identifieringsgränser** | En enhet kan identifiera upp till 1 000 fysiska servrar.
**Distribution som stöds** | Distribuera på en befintlig server som kör Windows Server 2016 med installationsskriptet för PowerShell.
**PowerShell-skript** | Ladda ned skriptet (AzureMigrateInstaller.ps1) i en zip-fil från projektet eller [härifrån](https://go.microsoft.com/fwlink/?linkid=2140334). [Läs mer](tutorial-discover-physical.md).<br/><br/> Nedladdningsstorleken är 85,8 MB.
**Skriptverifiering** | [Kontrollera](tutorial-discover-physical.md#verify-security) powershell-installationsskriptet som laddats ned från projektet genom att kontrollera hash-värdena.
**Maskinvaru- och nätverkskrav** |  Installationen ska köras på servern med Windows Server 2016, 16 GB RAM-minne, 8 virtuella processorer, cirka 80 GB disklagring.<br/> Installationen behöver en statisk eller dynamisk IP-adress och kräver Internetåtkomst, antingen direkt eller via en proxy.<br/><br/> Om du kör installationen på en befintlig server kontrollerar du att den kör Windows Server 2016 och uppfyller maskinvarukraven.<br/>_(Distributionen av installationen stöds för närvarande endast på Windows Server 2016.)_

## <a name="url-access"></a>URL-åtkomst

Enheten Azure Migrate måste vara ansluten till Internet.

- När du distribuerar installationen Azure Migrate en anslutningskontroll till de URL:er som krävs.
- Du måste tillåta åtkomst till alla URL:er i listan. Om du bara gör en utvärdering kan du hoppa över url:erna som har markerats som obligatoriska för migrering utan VMware-agent.
- Om du använder en URL-baserad proxy för att ansluta till Internet kontrollerar du att proxyn matchar alla CNAME-poster som tas emot när URL:erna letas upp.

### <a name="public-cloud-urls"></a>URL:er för offentligt moln

**URL** | **Information**  
--- | --- |
*.portal.azure.com  | Gå till Azure-portalen.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Logga in på din Azure-prenumeration.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Skapa Azure Active Directory (AD)-appar som enheten ska kunna kommunicera med Azure Migrate.
management.azure.com | Skapa Azure AD-appar för att enheten ska kunna kommunicera med Azure Migrate.
*.services.visualstudio.com | Ladda upp enhetens loggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault.<br/> Obs! Se till att servrar som ska replikeras har åtkomst till detta.
aka.ms/* | Tillåt åtkomst till aka länkar; används för att ladda ned och installera de senaste uppdateringarna för installationstjänster.
download.microsoft.com/download | Tillåt nedladdningar från Microsoft Download Center.
*.servicebus.windows.net | Kommunikation mellan apparaten och Azure Migrate tjänsten.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Anslut till Azure Migrate-URL:er.
*.hypervrecoverymanager.windowsazure.com | **Används för migrering utan VMware-agent**<br/><br/> Anslut till Azure Migrate-URL:er.
*.blob.core.windows.net |  **Används för migrering utan VMware-agent**<br/><br/>Ladda upp data till lagring för migrering.

### <a name="government-cloud-urls"></a>URL:er för myndighetsmoln

**URL** | **Information**  
--- | --- |
*.portal.azure.us  | Gå till Azure-portalen.
graph.windows.net | Logga in på din Azure-prenumeration.
login.microsoftonline.us  | Skapa Azure Active Directory (AD)-appar som enheten ska kunna kommunicera med Azure Migrate.
management.usgovcloudapi.net | Skapa Azure AD-appar för installationen för att kommunicera Azure Migrate tjänsten.
*.services.visualstudio.com | Ladda upp apparatloggar som används för intern övervakning.
*.vault.usgovcloudapi.net | Hantera hemligheter i Azure Key Vault.
aka.ms/* | Tillåt åtkomst till aka länkar; används för att ladda ned och installera de senaste uppdateringarna för installationstjänster.
download.microsoft.com/download | Tillåt nedladdningar från Microsoft Download Center.
*.servicebus.usgovcloudapi.net  | Kommunikation mellan apparaten och Azure Migrate tjänsten.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Anslut till Azure Migrate-URL:er.
*.hypervrecoverymanager.windowsazure.us | **Används för migrering utan VMware-agent**<br/><br/> Anslut till Azure Migrate-URL:er.
*.blob.core.usgovcloudapi.net  |  **Används för migrering utan VMware-agent**<br/><br/>Ladda upp data till lagring för migrering.
*.applicationinsights.us | Ladda upp enhetens loggar som används för intern övervakning.  

### <a name="public-cloud-urls-for-private-link-connectivity"></a>URL:er för offentliga moln för privat länkanslutning

Installationen behöver åtkomst till följande URL:er (direkt eller via proxy) utöver privat länkåtkomst. 

**URL** | **Information**  
--- | --- | 
*.portal.azure.com  | Gå till Azure-portalen.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Logga in på din Azure-prenumeration.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Skapa Azure Active Directory (AD)-appar som enheten ska kunna kommunicera med Azure Migrate.
management.azure.com | Skapa Azure AD-appar för att enheten ska kunna kommunicera med Azure Migrate.
*.services.visualstudio.com (valfritt) | Ladda upp enhetens loggar som används för intern övervakning.
aka.ms/* (valfritt) | Tillåt åtkomst till aka länkar; används för att ladda ned och installera de senaste uppdateringarna för installationstjänster.
download.microsoft.com/download | Tillåt nedladdningar från Microsoft Download Center.
*.servicebus.windows.net | **Används för migrering utan VMware-agent**<br/><br/> Kommunikation mellan apparaten och Azure Migrate tjänsten.
*.vault.azure.net | **Används för migrering utan VMware-agent**<br/><br/>  Se till att servrar som ska replikeras har åtkomst till detta.
*.hypervrecoverymanager.windowsazure.com | **Används för migrering utan VMware-agent**<br/><br/> Anslut till Azure Migrate url:er.
*.blob.core.windows.net |  **Används för migrering utan VMware-agent**<br/><br/>Ladda upp data till lagring för migrering.

### <a name="government-cloud-urls-for-private-link-connectivity"></a>URL:er för myndighetsmoln för privat länkanslutning   

Installationen behöver åtkomst till följande URL:er (direkt eller via proxy) utöver privat länkåtkomst. 

**URL** | **Information**  
--- | --- |
*.portal.azure.us  | Gå till Azure-portalen.
graph.windows.net | Logga in på din Azure-prenumeration.
login.microsoftonline.us  | Skapa Azure Active Directory (AD)-appar som enheten ska kunna kommunicera med Azure Migrate.
management.usgovcloudapi.net | Skapa Azure AD-appar för installationen för att kommunicera med Azure Migrate tjänsten.
*.services.visualstudio.com (valfritt) | Ladda upp apparatloggar som används för intern övervakning.
aka.ms/* (valfritt) | Tillåt åtkomst till aka länkar; används för att ladda ned och installera de senaste uppdateringarna för installationstjänster.
download.microsoft.com/download | Tillåt nedladdningar från Microsoft Download Center.
*.servicebus.usgovcloudapi.net  | **Används för migrering utan VMware-agent**<br/><br/> Kommunikation mellan apparaten och Azure Migrate tjänsten. 
*.vault.usgovcloudapi.net | **Används för migrering utan VMware-agent**<br/><br/> Hantera hemligheter i Azure Key Vault.
*.hypervrecoverymanager.windowsazure.us | **Används för migrering utan VMware-agent**<br/><br/> Anslut till Azure Migrate-URL:er.
*.blob.core.usgovcloudapi.net  |  **Används för migrering utan VMware-agent**<br/><br/>Ladda upp data till lagring för migrering.
*.applicationinsights.us (valfritt) | Ladda upp enhetens loggar som används för intern övervakning.  

## <a name="collected-data---vmware"></a>Insamlade data – VMware

Enheten samlar in konfigurationsmetadata, prestandametadata och serverberoendedata (om agentlös [beroendeanalys](concepts-dependency-visualization.md) används).

### <a name="metadata"></a>Metadata

Metadata som identifieras av Azure Migrate-installationen hjälper dig att ta reda på om servrarna är redo för migrering till Azure, rätt storlek på servrar, abonnemangskostnader och analysera programberoenden. Microsoft använder inte dessa data i någon granskning av licensefterlevnad.

Här är en fullständig lista över servermetadata som enheten samlar in och skickar till Azure.

**Data** | **Counter**
--- | ---
**Serverinformation** |
Server-ID | vm.Config. InstanceUuid
Servernamn | vm.Config. Namn
vCenter Server-ID | VMwareClient.Instance.Uuid
Serverbeskrivning | vm.Summary.Config. Anteckning
Licensproduktnamn | vm.Client.ServiceContent.About.LicenseProductName
Typ av operativsystem | vm.SummaryConfig.GuestFullName
Starttyp | vm.Config. Firmware
Antal kärnor | vm.Config. Hardware.NumCPU
Minne (MB) | vm.Config. Hardware.MemoryMB
Antal diskar | vm.Config. Hardware.Device.ToList(). FindAll(x => är VirtualDisk).count
Diskstorlekslista | vm.Config. Hardware.Device.ToList(). FindAll(x => är VirtualDisk)
Lista över nätverkskort | vm.Config. Hardware.Device.ToList(). FindAll(x => är VirtualEthernet).count
CPU-användning | cpu.usage.average
Minnesanvändning |mem.usage.average
**Information per disk** |
Disknyckelvärde | Disk. Nyckel
Dikunit-nummer | Disk. UnitNumber
Nyckelvärde för diskstyrenhet | Disk. ControllerKey.Value
Etablerade gigabyte | virtualDisk.DeviceInfo.Summary
Disknamn | Värde som genereras med disk. UnitNumber, disk. Nyckel, disk. ControllerKey.VAlue
Läsåtgärder per sekund | virtualDisk.numberReadAveraged.average
Skrivåtgärder per sekund | virtualDisk.numberWriteAveraged.average
Läs dataflöde (MB per sekund) | virtualDisk.read.average
Skriva dataflöde (MB per sekund) | virtualDisk.write.average
**Information per nätverkskort** |
Nätverkskortets namn | Nic. Nyckel
MAC-adress | ((VirtualEthernetCard)nic). MacAddress
IPv4-adresser | vm.Guest.Net
IPv6-adresser | vm.Guest.Net
Läs dataflöde (MB per sekund) | net.received.average
Skriva dataflöde (MB per sekund) | net.transmitted.average
**Information om inventeringssökväg** |
Name | Behållare. GetType(). Namn
Typ av underobjekt | Behållare. ChildType
Referensinformation | Behållare. MoRef
Överordnad information | Container.Parent
Mappinformation per server | ((Mapp)container). ChildEntity.Type
Datacenterinformation per server | ((Datacenter)container). VmFolder
Datacenterinformation per värdmapp | ((Datacenter)container). HostFolder
Klusterinformation per värd | ((ClusterComputeResource)container). Värd
Värdinformation per server | ((HostSystem)container). Virtuell dator

### <a name="performance-data"></a>Prestandadata

Här är de prestandadata som en enhet samlar in för en server som körs på VMware och skickar till Azure.

**Data** | **Räknare** | **Utvärderingspåverkan**
--- | --- | ---
CPU-användning | cpu.usage.average | Rekommenderad serverstorlek/-kostnad
Minnesanvändning | mem.usage.average | Rekommenderad serverstorlek/-kostnad
Diskläsningsgenomflöde (MB per sekund) | virtualDisk.read.average | Beräkning för diskstorlek, lagringskostnad, serverstorlek
Disk skriver dataflöde (MB per sekund) | virtualDisk.write.average | Beräkning för diskstorlek, lagringskostnad, serverstorlek
Diskläsningsåtgärder per sekund | virtualDisk.numberReadAveraged.average | Beräkning för diskstorlek, lagringskostnad, serverstorlek
Disk-skrivåtgärder per sekund | virtualDisk.numberWriteAveraged.average  | Beräkning för diskstorlek, lagringskostnad, serverstorlek
NIC-läsflöde (MB per sekund) | net.received.average | Beräkning för serverstorlek
Nätverkskortet skriver dataflöde (MB per sekund) | net.transmitted.average  |Beräkning för serverstorlek

### <a name="installed-software-inventory"></a>Installerad programvaruinventering

Installationen samlar in data om installerad programvaruinventering på servrar.

#### <a name="windows-server-software-inventory-data"></a>Windows Server-programvaruinventeringsdata

Här är de programinventeringsdata som enheten samlar in från varje Windows-server som identifieras i din VMware-miljö.

**Data** | **Registerplats** | **Nyckel**
--- | --- | ---
Programnamn  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Version  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion
Leverantör  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>Data om Windows Server-funktioner

Här är de funktionsdata som enheten samlar in från varje Windows-server som identifieras i din VMware-miljö.

**Data**  | **PowerShell-cmdlet** | **Egenskap**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Funktionstyp | Get-WindowsFeature  | FeatureType
Överordnad  | Get-WindowsFeature  | Överordnad

#### <a name="sql-server-metadata"></a>SQL Server metadata

Här är de SQL Server som enheten samlar in från varje Windows-server som identifieras i din VMware-miljö.

**Data**  | **Registerplats**  | **Nyckel**
--- | --- | ---
Name  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Utgåva  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Utgåva
Service Pack  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Sp
Version  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Version

#### <a name="windows-server-operating-system-data"></a>Windows Server-operativsystemdata

Här är de operativsystemdata som enheten samlar in från varje Windows-server som identifieras i din VMware-miljö.

**Data**  | **WMI-klass**  | **WMI-klassegenskap**
--- | --- | ---
Name  | Win32_operatingsystem  | Caption
Version  | Win32_operatingsystem  | Version
Arkitektur  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Inventeringsdata för Linux-serverprogramvara

Här är de programinventeringsdata som enheten samlar in från varje Linux-server som identifieras i din VMware-miljö. Ett eller flera kommandon körs baserat på serverns operativsystem.

**Data**  | **Kommandon**
--- | ---
Name | rpm, dpkg-query, snap
Version | rpm, dpkg-query, snap
Leverantör | rpm, dpkg-query, snap

#### <a name="linux-server-operating-system-data"></a>Operativsystemdata för Linux-server

Här är de operativsystemdata som enheten samlar in från varje Linux-server som identifieras i din VMware-miljö.

**Data**  | **Kommandon**
--- | ---
Name <br/> version | Samlas in från en eller flera av följande filer:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version
Arkitektur | Uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server data om instanser och databaser

Enheten samlar in data SQL Server instanser och databaser.

> [!Note]
> Identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är nu i förhandsversion. Om du vill testa funktionen använder du [**den här länken**](https://aka.ms/AzureMigrate/SQL) till att skapa ett projekt i regionen **Australien, östra**. Om du redan har ett projekt i Australien, östra och vill prova den här funktionen måste du se till att du har slutfört dessa [**krav**](how-to-discover-sql-existing-project.md) på portalen.

#### <a name="sql-database-metadata"></a>SQL-databasmetadata

**Databasmetadata** | **Egenskaper för vyer/SQL Server-objekt**
--- | ---
Unik identifierare för databasen | sys.databases
Serverdefinierat databas-ID | sys.databases
Namnet på databasen | sys.databases
Kompatibilitetsnivå för databas | sys.databases
Sorteringsnamn för databas | sys.databases
Databasens tillstånd | sys.databases
Databasens storlek (i MB) | sys.master_files
Enhetsbeteckning för plats som innehåller datafiler | SERVERPROPERTY och Software\Microsoft\MSSQLServer\MSSQLServer
Lista över databasfiler | sys.databases, sys.master_files
Service Broker är aktiverat eller inte | sys.databases
Databasen är aktiverad för insamling av ändringsdata eller inte | sys.databases

#### <a name="sql-server-metadata"></a>SQL Server metadata

**Servermetadata** | **Vyer/SQL-serveregenskaper**
--- | ---
Servernamn |SERVERPROPERTY
FQDN | Anslutningssträng som härletts från identifiering av installerade program
Installations-ID | sys.dm_server_registry
Serverversion | SERVERPROPERTY
Serverutgåva | SERVERPROPERTY
Servervärdplattform (Windows/Linux) | SERVEREGENSKAPER
Serverns produktnivå (RTM SP CTP) | SERVEREGENSKAPER
Standardsökväg för säkerhetskopiering | SERVEREGENSKAPER
Standardsökvägen för datafilerna | SERVERPROPERTY och Software\Microsoft\MSSQLServer\MSSQLServer
Standardsökvägen för loggfilerna | SERVERPROPERTY och Software\Microsoft\MSSQLServer\MSSQLServer
Nej. av kärnor på servern | sys.dm_os_schedulers, sys.dm_os_sys_info
Namn på serverar sorterad | SERVEREGENSKAPER
Nej. av kärnor på servern med STATUS SYNLIG ONLINE | sys.dm_os_schedulers
Unikt server-ID | sys.dm_server_registry
Ha aktiverad eller inte | SERVEREGENSKAPER
Buffertpooltillägget är aktiverat eller inte | sys.dm_os_buffer_pool_extension_configuration
Redundanskluster konfigurerade eller inte | SERVERPROPERTY
Server som endast använder Windows-autentiseringsläge | SERVERPROPERTY
Servern installerar PolyBase | SERVERPROPERTY
Nej. av logiska processorer i systemet | sys.dm_server_registry, sys.dm_os_sys_info
Förhållandet mellan antalet logiska eller fysiska kärnor som exponeras av ett fysiskt processorpaket | sys.dm_os_schedulers, sys.dm_os_sys_info
Inga fysiska processorer i systemet | sys.dm_os_schedulers, sys.dm_os_sys_info
Datum och tid då servern senast startade | sys.dm_server_registry
Maximal minnesanvändning för server (i MB) | sys.dm_os_process_memory
Totalt nej. av användare i alla databaser | sys.databases, sys.logins
Total storlek för alla användardatabaser | sys.databases
Storlek på temporär databas | sys.master_files, sys.config, sys.dm_os_sys_info
Nej. av inloggningar | sys.logins
Lista över länkade servrar | sys.servers
Lista över agentjobb | [msdb]. [dbo]. [sysjobs], [sys]. [syslogins], [msdb]. [dbo]. [syscategories]

### <a name="performance-metadata"></a>Prestandametadata

**Prestanda** | **Vyer/SQL-serveregenskaper** | **Utvärderingspåverkan**
--- | --- | ---
SQL Server processoranvändning| sys.dm_os_ring_buffers| Rekommenderad SKU-storlek (CPU-dimension)
Antal logiska SQL-processorer| sys.dm_os_sys_info| Rekommenderad SKU-storlek (CPU-dimension)
Fysiskt SQL-minne som används| sys.dm_os_process_memory| Oanvända
Procentandel sql-minnesanvändning| sys.dm_os_process_memory | Oanvända
Databas-CPU-användning| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Rekommenderad SKU-storlek (CPU-dimension)
Databasminne som används (buffertpool)| sys.dm_os_buffer_descriptors| Rekommenderad SKU-storlek (minnesdimension)
Filläsnings-/skriv-I/O| sys.dm_io_virtual_file_stats, sys.master_files| Rekommenderad SKU-storlek (IO-dimension)
Filens antal läsningar/skrivningar| sys.dm_io_virtual_file_stats, sys.master_files| Rekommenderad SKU-storlek (dataflödesdimension)
Fil-I/O har stoppat läsning/skrivning (ms)| sys.dm_io_virtual_file_stats, sys.master_files| Rekommenderad SKU-storlek (I/O-svarstidsdimension)
Filstorlek| sys.master_files| Rekommenderad SKU-storlek (lagringsdimension)


### <a name="application-dependency-data"></a>Programberoende data

Agentlös beroendeanalys samlar in anslutnings- och processdata.

#### <a name="windows-server-dependencies-data"></a>Data om Windows Server-beroenden

Här är anslutningsdata som enheten samlar in från varje Windows-server, aktiverad för agentlös beroendeanalys.

**Data** | **Kommandon**
--- | ---
Lokal port | Netstat
Lokal IP-adress | Netstat
Fjärr-IP-adress | Netstat
Fjärr-IP-adress | Netstat
TCP-anslutningstillstånd | Netstat
Process-ID | Netstat
Antal aktiva anslutningar | Netstat

Här är anslutningsdata som enheten samlar in från varje Windows-server, aktiverad för agentlös beroendeanalys.

**Data** | **WMI-klass** | **WMI-klassegenskap**
--- | --- | ---
Processnamn | Win32_Process | ExecutablePath
Processargument | Win32_Process | Kommandorad
Programnamn | Win32_Process | Parametern VersionInfo.ProductName för egenskapen ExecutablePath

#### <a name="linux-server-dependencies-data"></a>Data om Linux-serverberoenden

Här är anslutningsdata som enheten samlar in från varje Linux-server, aktiverade för agentlös beroendeanalys.

**Data** | **Kommandon**
--- | ---
Lokal port | Netstat
Lokal IP-adress | Netstat
Fjärr-IP-adress | Netstat
Fjärr-IP-adress | Netstat
TCP-anslutningstillstånd | Netstat
Antal aktiva anslutningar | Netstat
Process-ID  | Netstat
Processnamn | Ps
Processargument | Ps
Programnamn | dpkg eller rpm

## <a name="collected-data---hyper-v"></a>Insamlade data – Hyper-V

Enheten samlar in konfigurations- och prestandametadata från servrar som körs i Hyper-V-miljön.

### <a name="metadata"></a>Metadata
Metadata som identifieras av Azure Migrate-installationen hjälper dig att ta reda på om servrarna är redo för migrering till Azure, rätt storlek på servrar och abonnemangskostnader. Microsoft använder inte dessa data i någon granskning av licensefterlevnad.

Här är en fullständig lista över servermetadata som enheten samlar in och skickar till Azure.

**Data** | **WMI-klass** | **WMI-klassegenskap**
--- | --- | ---
**Serverinformation** | 
Serienummer för BIOS | Msvm_BIOSElement | BIOSSerialNumber
Servertyp (Gen 1 eller 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Serverns visningsnamn | Msvm_VirtualSystemSettingData | ElementName
Serverversion | Msvm_ProcessorSettingData | VirtualQuantity
Minne (byte) | Msvm_MemorySettingData | VirtualQuantity
Maximalt minne som kan användas av servern | Msvm_MemorySettingData | Gräns
Dynamiskt minne aktiverat | Msvm_MemorySettingData | DynamicMemoryEnabled
Operativsystemnamn/version/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems-namndata
Serverns energistatus | Msvm_ComputerSystem | EnabledState
**Information per disk** |
Diskidentifierare | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Typ av virtuell hårddisk | Msvm_VirtualHardDiskSettingData | Typ
Storlek på virtuell hårddisk | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Överordnad virtuell hårddisk | Msvm_VirtualHardDiskSettingData | ParentPath
**Information per nätverkskort** |
IP-adresser (syntetiska nätverkskort) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP-aktiverat (syntetiska nätverkskort) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-ID (syntetiska nätverkskort) | Msvm_SyntheticEthernetPortSettingData | InstanceID
MAC-adress för nätverkskort (syntetiska nätverkskort) | Msvm_SyntheticEthernetPortSettingData | Adress
NIC-ID (äldre nätverkskort) | MsvmEmulatedEthernetPortSetting Data | InstanceID
MAC-ID för nätverkskort (äldre nätverkskort) | MsvmEmulatedEthernetPortSetting Data | Adress

### <a name="performance-data"></a>Prestandadata

Här är de serverprestandadata som enheten samlar in och skickar till Azure.

**Prestandaräknarklass** | **Räknare** | **Utvärderingspåverkan**
--- | --- | ---
Hyper-V Hypervisor Virtual Processor | % gästkörningstid | Rekommenderad serverstorlek/-kostnad
Hyper-V dynamiskt minne Server | Aktuellt tryck (%)<br/> Synligt fysiskt minne för gäst (MB) | Rekommenderad serverstorlek/-kostnad
Virtuell Lagringsenhet för Hyper-V | Lästa byte/sekund | Beräkning för diskstorlek, lagringskostnad, serverstorlek
Virtuell Lagringsenhet för Hyper-V | Skrivningsbyte/sekund | Beräkning för diskstorlek, lagringskostnad, serverstorlek
Hyper-V-Virtual Network adapter | Mottagna byte/sekund | Beräkning för serverstorlek
Hyper-V-Virtual Network adapter | Skickade byte/sekund | Beräkning för serverstorlek

- Cpu-användning är summan av all användning för alla virtuella processorer som är anslutna till en server.
- Minnesanvändningen är (aktuellt tryck * synligt fysiskt gästminne) /100.
- Värden för disk- och nätverksanvändning samlas in från de listade Hyper-V-prestandaräknarna.

## <a name="collected-data---physical"></a>Insamlade data – fysisk

Installationen samlar in konfigurations- och prestandametadata från fysiska eller virtuella servrar som körs lokalt.

### <a name="metadata"></a>Metadata

Metadata som identifieras av Azure Migrate-installationen hjälper dig att ta reda på om servrarna är redo för migrering till Azure, rätt storlek på servrar och abonnemangskostnader. Microsoft använder inte dessa data i någon granskning av licensefterlevnad.

### <a name="windows-server-metadata"></a>Windows Server-metadata

Här är en fullständig lista över Windows Server-metadata som enheten samlar in och skickar till Azure.

**Data** | **WMI-klass** | **WMI-klassegenskap**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domän, Namn, PartOfDomain
Antal processorkärnor | Win32_PRocessor | NumberOfCores
Allokerat minne | Win32_ComputerSystem | TotalPhysicalMemory
BIOS-serienummer | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS GUID | Win32_ComputerSystemProduct | Uuid
Starttyp | Win32_DiskPartition | Sök efter partition med typen = **GPT:System** för EFI/BIOS
Operativsystemets namn | Win32_OperatingSystem | Caption
OS-version |Win32_OperatingSystem | Version
Operativsystemarkitektur | Win32_OperatingSystem | OSArchitecture
Antal diskar | Win32_DiskDrive | Model, Size, DeviceID, MediaType, Name
Diskstorlek | Win32_DiskDrive | Storlek
NIC-lista | Win32_NetworkAdapterConfiguration | Beskrivning, Index
NÄTVERKSKORTs-IP-adress | Win32_NetworkAdapterConfiguration | IPAddress
MAC-adress för nätverkskort | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-server-metadata"></a>Linux-servermetadata

Här är en fullständig lista över Linux-servermetadata som installationen samlar in och skickar till Azure.

**Data** | **Kommandon**
--- | ---
FQDN | cat /proc/sys/kernel/hostname, hostname -f
Antal processorkärnor |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
Allokerat minne | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
BIOS-serienummer | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
BIOS GUID | cat /sys/class/dmi/id/product_uuid
Starttyp | [ -d /sys/firmware/efi ] && eFI echo \| \| BIOS
Os-namn/version | Vi kommer åt dessa filer för OS-versionen och namnet:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Operativsystemarkitektur | Uname -m
Antal diskar | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Startdisk | df /boot \| sed -n 2p \| awk '{print $1}'
Diskstorlek | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
NIC-lista | ip -o -4 addr show \| awk '{print $2}'
NÄTVERKSKORTs-IP-adress | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
MAC-adress för nätverkskort | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Windows-prestandadata

Här är windows-serverns prestandadata som enheten samlar in och skickar till Azure.

**Data** | **WMI-klass** | **WMI-klassegenskap**
--- | --- | ---
CPU-användning | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Minnesanvändning | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Antal nätverkskort | Win32_PerfFormattedData_Tcpip_NetworkInterface | Hämta antalet nätverksenhet.
Mottagna data per nätverkskort | Win32_PerfFormattedData_Tcpip_NetworkInterface  | ByteReceivedPerSec
Data som överförs per nätverkskort | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Antal diskar | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Antal diskar
Diskinformation | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Linux-prestandadata

Här är linux-serverns prestandadata som enheten samlar in och skickar till Azure.

| **Data** | **Kommandon** |
| --- | --- |
| CPU-användning | cat /proc/stat/ \| grep 'cpu' /proc/stat |
| Minnesanvändning | free \| grep Mem \| awk '{print $3/$2 * 100.0}' |
| Antal nätverkskort | lshw -class network \| grep eth[0-60] \| wc -l |
| Mottagna data per nätverkskort | cat /sys/class/net/eth$nic/statistics/rx_bytes |
| Data som överförs per nätverkskort | cat /sys/class/net/eth$nic/statistics/tx_bytes |
| Antal diskar | fdisk -l \| egrep 'Disk. \* bytes' \| awk '{print $2}' \| cut -f1 -d ':' |
| Diskinformation | cat /proc/diskstats |

## <a name="appliance-upgrades"></a>Uppgraderingar av installation

Installationen uppgraderas när de Azure Migrate som körs på installationen uppdateras. Detta sker automatiskt eftersom automatisk uppdatering är aktiverat i installationen som standard. Du kan ändra den här standardinställningen om du vill uppdatera installationstjänster manuellt.

### <a name="turn-off-auto-update"></a>Inaktivera automatisk uppdatering

1. Öppna Registereditorn på servern som kör installationen.
2. Gå till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Om du vill inaktivera automatisk uppdatering skapar du en nyckel för **automatisk uppdatering av registernyckeln** med DWORD-värdet 0.

    ![Ange registernyckel](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Aktivera automatisk uppdatering

Du kan aktivera automatisk uppdatering med någon av följande metoder:

- Genom att ta bort registernyckeln AutoUpdate från HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Klicka på **Visa installationstjänster** från de senaste uppdateringskontrollerna **på panelen Konfigurera krav** för att aktivera automatisk uppdatering.

Så här tar du bort registernyckeln:

1. Öppna Registereditorn på servern som kör installationen.
2. Gå till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Ta bort registernyckeln **AutoUpdate**, som skapades tidigare för att stänga av automatisk uppdatering.

Om du vill aktivera från Konfigurationshanteraren när identifieringen är klar:

1. I konfigurationshanteraren för installationen går **du till panelen Konfigurera** förutsättningar
2. I den senaste uppdateringskontrollen klickar du på **Visa installationstjänster** och klickar på länken för att aktivera automatisk uppdatering.

    ![Aktivera automatiska uppdateringar](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Kontrollera versionen för installationstjänster

Du kan kontrollera versionen för installationstjänster med någon av följande metoder:

- I Hanteraren för enhetskonfiguration går **du till panelen Konfigurera** förutsättningar.
- På enheten går du till **Kontrollpanelen**  >  **program och funktioner**.

Så här checkar du in konfigurationshanteraren för installationen:

1. I konfigurationshanteraren för installationen går **du till panelen Konfigurera** förutsättningar
2. I den senaste uppdateringskontrollen klickar du på **Visa installationstjänster.**

    ![Kontrollera version](./media/migrate-appliance/versions.png)

Så här checkar du in Kontrollpanelen:

1. I installationen klickar du på **Starta**  >  **Kontrollpanelen**  >  **program och funktioner**
2. Kontrollera versionerna av installationstjänster i listan.

    ![Kontrollera versionen i Kontrollpanelen](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Uppdatera en äldre version manuellt

Om du kör en äldre version för någon av tjänsterna måste du avinstallera tjänsten och uppdatera manuellt till den senaste versionen.

1. Om du vill söka efter de senaste versionerna av [installationstjänsten laddar](https://aka.ms/latestapplianceservices) du LatestComponents.jspå filen.
2. När du har laddat ned öppnar LatestComponents.jspå filen i Anteckningar.
3. Hitta den senaste tjänstversionen i filen och nedladdningslänken för den. Exempel:

    "Name": "ASRMigrationWebApp", "DownloadLink": https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi " ", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4. Ladda ned den senaste versionen av en inaktuell tjänst med hjälp av nedladdningslänken i filen.
5. När du har hämtat kör du följande kommando i ett administratörskommandofönster för att verifiera integriteten för den nedladdade MSI:n.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Exempel: C: \> CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Kontrollera att kommandoutdata matchar hash-värdeposten för tjänsten i filen (till exempel MD5-hashvärdet ovan).
6. Kör nu MSI för att installera tjänsten. Det är en tyst installation och installationsfönstret stängs när det är klart.
7. När installationen är klar kontrollerar du versionen av tjänsten i **Kontrollpanelen Program**  >  **och funktioner**. Tjänstversionen bör nu uppgraderas till den senaste som visas i json-filen.

## <a name="next-steps"></a>Nästa steg

- [Lär dig](how-to-set-up-appliance-vmware.md) hur du ställer in installationen för VMware.
- [Lär dig](how-to-set-up-appliance-hyper-v.md) hur du ställer in installationen för Hyper-V.
- [Lär dig](how-to-set-up-appliance-physical.md) hur du ställer in installationen för fysiska servrar.
