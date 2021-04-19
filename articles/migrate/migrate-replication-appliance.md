---
title: Azure Migrate-replikeringsinstallation
description: Lär dig mer Azure Migrate replikeringsinstallationen för agentbaserad VMware-migrering.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 5f63b033c3995932662fc9b68c1397bf57b0326e
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714982"
---
# <a name="replication-appliance"></a>Replikeringsinstallation

I den här artikeln beskrivs replikeringsinstallationen som används av [verktyget Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) när du migrerar virtuella VMware-datorer, fysiska datorer och virtuella datorer i privata/offentliga moln till Azure med agentbaserad migrering. 


## <a name="overview"></a>Översikt

Replikeringsinstallationen distribueras när du ställer in agentbaserad migrering av virtuella VMware-datorer eller fysiska servrar. Den distribueras som en enskild lokal dator, antingen som en virtuell VMware-dator eller en fysisk server. Den körs:

- **Replikeringsinstallation:** Replikeringsinstallationen samordnar kommunikation och hanterar datareplikering för lokala virtuella VMware-datorer och fysiska servrar som replikeras till Azure.
- **Processerserver:** Processerservern, som installeras som standard på replikeringsinstallationen, och gör följande:
    - **Replikeringsgateway:** Den fungerar som en replikeringsgateway. Den tar emot replikeringsdata från datorer som är aktiverade för replikering. Den optimerar replikeringsdata med cachelagring, komprimering och kryptering och skickar dem till Azure.
    - **Installationsprogram** för agent: Utför en push-installation av mobilitetstjänsten. Den här tjänsten måste installeras och köras på varje lokal dator som du vill replikera för migrering.

## <a name="appliance-deployment"></a>Distribution av installation

**Används för** | **Information**
--- |  ---
**Agentbaserad migrering av virtuella VMware-datorer** | Du laddar ned OVA-mallen från Azure Migrate-hubben och importerar till vCenter Server för att skapa den virtuella datorn.
**Agentbaserad migrering på fysisk dator** | Om du inte har någon VMware-infrastruktur, eller om du inte kan skapa en virtuell VMware-dator med hjälp av en OVA-mall, laddar du ned ett installationsprogram från Azure Migrate-hubben och kör det för att konfigurera enhetens dator.

> [!NOTE]
> Om du distribuerar i Azure Government du installationsfilen för att distribuera replikeringsinstallationen.

## <a name="appliance-requirements"></a>Installationskrav

När du ställer in replikeringsinstallationen med hjälp av OVA-mallen som finns i Azure Migrate Hub kör installationen Windows Server 2016 och uppfyller supportkraven. Om du ställer in replikeringsinstallationen manuellt på en fysisk server kontrollerar du att den uppfyller kraven.

**Komponent** | **Krav**
--- | ---
 | **VMware VM-installation**
PowerCLI | [PowerCLI version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) ska installeras om replikeringsinstallationen körs på en virtuell VMware-dator.
NIC-typ | VMXNET3 (om installationen är en virtuell VMware-dator)
 | **Maskinvaruinställningar**
Processorkärnor | 8
RAM | 16 GB
Antal diskar | Tre: OS-disken, processervcachedisken och kvarhållningsdisken.
Ledigt diskutrymme (cache) | 600 GB
Ledigt diskutrymme (kvarhållningsdisk) | 600 GB
**Programvaruinställningar** |
Operativsystem | Windows Server 2016 eller Windows Server 2012 R2
Licens | Installationen levereras med en utvärderingslicens för Windows Server 2016, som är giltig i 180 dagar.<br/><br/> Om utvärderingsperioden är nära att gå ut rekommenderar vi att du laddar ned och distribuerar en ny installation eller att du aktiverar operativsystemlicensen för den virtuella datorn.
Nationella inställningar för operativsystem | Engelska (en-us)
TLS | TLS 1.2 ska vara aktiverat.
.NET Framework | .NET Framework 4.6 eller senare ska installeras på datorn (med stark kryptografi aktiverad.
MySQL | MySQL ska vara installerat i installationen.<br/> MySQL ska installeras. Du kan installera manuellt eller Site Recovery installera det under installationens distribution.
Andra appar | Kör inte andra appar i replikeringsinstallationen.
Windows Server-roller | Aktivera inte dessa roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Grupprinciper | Aktivera inte dessa grupprinciper: <br> – Förhindra åtkomst till kommandotolken. <br> – Förhindra åtkomst till redigeringsverktyg för registret. <br> – Förtroendelogik för bifogade filer. <br> – Aktivera skriptkörning. <br> [Läs mer](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | – Ingen befintlig standardwebbplats <br> – Ingen befintlig webbplats/program som lyssnar på port 443 <br>– Aktivera  [anonym autentisering](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> – Aktivera [FastCGI-inställning](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))
**Nätverksinställningar** |
IP-adresstyp | Statisk
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring)
NIC-typ | VMXNET3

## <a name="mysql-installation"></a>MySQL-installation 

MySQL måste vara installerat på replikeringsinstallationsdatorn. Den kan installeras med någon av dessa metoder.

**Metod** | **Information**
--- | ---
Ladda ned och installera manuellt | Ladda ned MySQL-& placera det i mappen C:\Temp\ASRSetup och installera sedan manuellt.<br/> När du ställer in installationen visas MySQL som redan installerat.
Utan nedladdning online | Placera MySQL-installationsprogrammet i mappen C:\Temp\ASRSetup. När du installerar installationen och väljer att ladda ned och installera MySQL använder installationsprogrammet som du lade till.
Ladda ned och installera i Azure Migrate | När du installerar installationen och uppmanas att ange MySQL väljer du **Ladda ned och installera**.

## <a name="url-access"></a>URL-åtkomst

Replikeringsinstallationen behöver åtkomst till dessa URL:er i det offentliga Azure-molnet.

**URL** | **Information**
--- | ---
\*.backup.windowsazure.com | Används för replikerad dataöverföring och samordning
\*.store.core.windows.net | Används för replikerad dataöverföring och samordning
\*.blob.core.windows.net | Används för att få åtkomst till lagringskonto som lagrar replikerade data
\*.hypervrecoverymanager.windowsazure.com | Används för replikeringshanteringsåtgärder och samordning
https:\//management.azure.com | Används för replikeringshanteringsåtgärder och samordning
*.services.visualstudio.com | Används i loggningssyfte (det är valfritt)
time.windows.com | Används för att kontrollera tidssynkronisering mellan system och global tid.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.live.com <br/> https: \/ /graph.windows.net <br/> https:\//login.windows.net <br/> https: \/ /www.live.com <br/> https: \/ /www.microsoft.com  | Installationen behöver åtkomst till dessa URL:er. De används för åtkomstkontroll och identitetshantering av Azure Active Directory
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | För att slutföra nedladdningen av MySQL. I några regioner kan nedladdningen omdirigeras till CDN-URL:en. Kontrollera att CDN-URL:en också tillåts om det behövs.


## <a name="azure-government-url-access"></a>Azure Government URL-åtkomst

Replikeringsinstallationen behöver åtkomst till dessa URL:er i Azure Government.

**URL** | **Information**
--- | ---
\*.backup.windowsazure.us | Används för replikerad dataöverföring och samordning
\*.store.core.windows.net | Används för replikerad dataöverföring och samordning
\*.blob.core.windows.net | Används för åtkomst till lagringskonto som lagrar replikerade data
\*.hypervrecoverymanager.windowsazure.us | Används för replikeringshanteringsåtgärder och samordning
https:\//management.usgovcloudapi.net | Används för replikeringshanteringsåtgärder och samordning
*.services.visualstudio.com | Används i loggningssyfte (det är valfritt)
time.nist.gov | Används för att kontrollera tidssynkronisering mellan system och global tid.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.live.com <br/> https: \/ /graph.windows.net <br/> https:\//login.windows.net <br/> https: \/ /www.live.com <br/> https: \/ /www.microsoft.com  | Installationen med OVA behöver åtkomst till dessa URL:er. De används för åtkomstkontroll och identitetshantering av Azure Active Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Slutför mySQL-nedladdningen. I några regioner kan nedladdningen omdirigeras till CDN-URL:en. Kontrollera att CDN-URL:en också tillåts om det behövs.  

>[!Note]
>
> Om du migrerar projekt har en privat slutpunktsanslutning behöver du åtkomst till följande URL:er utöver private link-åtkomst:   
> - *.blob.core.windows.com – För att få åtkomst till lagringskonto som lagrar replikerade data. Detta är valfritt och krävs inte om lagringskontot har en privat slutpunkt kopplad. 
> - https: \/ /management.azure.com för replikeringshanteringsåtgärder och samordning. 
>- https:\//login.microsoftonline.com <br/>https:\//login.windows.net <br/> https: \/ /www.live.com _och_ <br/> https: \/ /www.microsoft.com för åtkomstkontroll och identitetshantering av Azure Active Directory

## <a name="port-access"></a>Portåtkomst

**Enhet** | **Anslutning**
--- | ---
Virtuella datorer | Den tjänsten Mobility som körs på virtuella datorer kommunicerar med den lokala replikeringsinstallationen (konfigurationsservern) på port HTTPS 443 inkommande för replikeringshantering.<br/><br/> Virtuella datorer skickar replikeringsdata till processervservern (körs på konfigurationsserverdatorn) på port HTTPS 9443 inkommande. Den här porten kan ändras.
Replikeringsinstallation | Replikeringsinstallationen dirigerar replikering med Azure via port HTTPS 443 utgående.
Processerserver | Processerservern tar emot replikeringsdata, optimerar och krypterar dem och skickar dem till Azure Storage via port 443 utgående.<br/> Processerservern körs som standard i replikeringsinstallationen.


## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator börjar den inledande replikeringen till Azure Storage med hjälp av den angivna replikeringsprincipen. 
2. Trafiken replikeras till offentliga slutpunkter för Azure Storage via Internet. Replikering av trafik via ett virtuellt privat nätverk för plats-till-plats (VPN) från en lokal plats till Azure stöds inte.
3. När den inledande replikeringen är klar påbörjas deltareplikeringen. Spårade ändringar för en dator loggas.
4. Kommunikationen sker på följande sätt:
    - Virtuella datorer kommunicerar med replikeringsinstallationen på port HTTPS 443 inkommande för replikeringshantering.
    - Replikeringsinstallationen dirigerar replikering med Azure via port HTTPS 443 utgående.
    - Virtuella datorer skickar replikeringsdata till processervservern (körs på replikeringsinstallationen) på port HTTPS 9443 inkommande. Den här porten kan ändras.
    - Processerservern tar emot replikeringsdata, optimerar och krypterar dem och skickar dem till Azure Storage via port 443 utgående.
5. Replikeringsdataloggarna hamnar först i ett cachelagringskonto i Azure. Loggarna bearbetas och data lagras på en azure-hanterad disk.

![Diagram som visar arkitekturen för replikeringsprocessen.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Uppgraderingar av installation

Installationen uppgraderas manuellt från den Azure Migrate hubben. Vi rekommenderar att du alltid kör den senaste versionen.

1. I Azure Migrate > Servers > Azure Migrate: Server Assessment (Serverutvärdering) och Infrastructure servers (Infrastrukturservrar) väljer **du Configuration servers (Konfigurationsservrar).**
2. I **Konfigurationsservrar** visas en länk i **Agentversion** när en ny version av replikeringsinstallationen är tillgänglig. 
3. Ladda ned installationsprogrammet till replikeringsinstallationsdatorn och installera uppgraderingen. Installationsprogrammet identifierar den version som körs på installationen.
 
## <a name="next-steps"></a>Nästa steg

- [Lär dig hur](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) du ställer in replikeringsinstallationen för agentbaserad migrering av virtuella VMware-datorer.
- [Lär dig](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) hur du ställer in replikeringsinstallationen för fysiska servrar.
