---
title: Använda Azure Migrate med privata slutpunkter
description: Använd Azure Migrate private link för att identifiera, utvärdera och migrera med private link.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/07/2020
ms.openlocfilehash: e4feaa8f1b30bfe31f4e645943f766b5736150b3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818376"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>Använda Azure Migrate med privata slutpunkter  

Den här artikeln beskriver hur du använder Azure Migrate för att identifiera, utvärdera och migrera servrar över ett privat nätverk med hjälp av [Azure Private Link](https://docs.microsoft.com/azure/private-link/private-endpoint-overview). 

Du kan använda [verktygen Azure Migrate: Identifiering](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-discovery-and-assessment-tool) och utvärdering [och Azure Migrate: Servermigrering](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) för att ansluta privat och säkert till Azure Migrate-tjänsten via en privat ExpressRoute-peering eller en PLATS-till-plats-VPN-anslutning med hjälp av en privat Azure-länk. 

Anslutningsmetoden för privata slutpunkter rekommenderas när det finns ett organisationskrav för att få åtkomst Azure Migrate tjänsten och andra Azure-resurser utan att gå över offentliga nätverk. Du kan också använda stödet för privata länkar för att använda dina befintliga privata ExpressRoute-peeringkretsar för bättre bandbredds- eller svarstidskrav. 

## <a name="support-requirements"></a>Supportkrav 

### <a name="required-permissions"></a>Behörigheter som krävs

**Behörighet som deltagare + administratör för** **användaråtkomst eller** Ägare för prenumerationen. 

### <a name="supported-scenarios-and-tools"></a>Scenarier och verktyg som stöds

**Distribution** | **Information** | **Verktyg** 
--- | --- | ---
**Identifiering och utvärdering** | Utför en agentlös identifiering och utvärdering av dina servrar i stor skala som körs på valfri plattform – hypervisor-plattformar som [VMware vSphere](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware) eller [Microsoft Hyper-V,](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v)offentliga moln som [AWS](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws) eller [GCP](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp)eller till och med bare [metal-servrar.](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical) | Azure Migrate: Identifiering och utvärdering  <br/> 
**Programvaruinventering** | Identifiera appar, roller och funktioner som körs på virtuella VMware-datorer. | Azure Migrate: Identifiering och utvärdering  
**Visualisering av beroenden** | Använd funktionen beroendeanalys för att identifiera och förstå beroenden mellan servrar. <br/> [Agentlös beroendevisualisering](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies-agentless) stöds inbyggt med Azure Migrate private link-stöd. <br/>[Agentbaserad beroendevisualisering](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) kräver Internetanslutning. [lär dig hur](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) du använder privata slutpunkter för agentbaserad beroendevisualisering. | Azure Migrate: Identifiering och utvärdering |
**Migrering** | Utför [agentlösa Hyper-V-migreringar](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) eller använd den agentbaserade metoden för att migrera dina virtuella [VMware-datorer,](./tutorial-migrate-vmware-agent.md)virtuella [Hyper-V-datorer,](./tutorial-migrate-physical-virtual-machines.md)fysiska servrar, virtuella datorer som körs på [AWS,](./tutorial-migrate-aws-virtual-machines.md)virtuella datorer som körs på [GCP](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)eller virtuella datorer som körs på en annan virtualiseringsprovider. [](./tutorial-migrate-physical-virtual-machines.md) | Azure Migrate: Servermigrering
 
>[!Note]
>
> [Agentlösa VMware-migreringar](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) kräver Internetåtkomst eller anslutning via ExperessRoute Microsoft-peering. <br/> [Lär dig](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute) hur du använder privata slutpunkter för att utföra replikeringar via privat ExpressRoute-peering eller en VPN-anslutning för plats-till-plats (S2S).  <br/><br/> 
   
#### <a name="other-integrated-tools"></a>Andra integrerade verktyg

Vissa migreringsverktyg kanske inte kan ladda upp användningsdata till Azure Migrate om offentlig nätverksåtkomst är inaktiverad. Projektet Azure Migrate konfigureras för att tillåta trafik från alla nätverk att ta emot data från andra Microsoft- eller [externa isv-erbjudanden (oberoende programvaruleverantörer).](https://docs.microsoft.com/azure/migrate/migrate-services-overview#isv-integration) 


Om du vill aktivera offentlig nätverksåtkomst för Azure Migrate-projektet går du Azure Migrate **på** sidan med Azure Portal, väljer **Nej** och **väljer Spara.**

![Diagram som visar hur du ändrar åtkomstläget för nätverket.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>Ytterligare överväganden   

**Överväganden** | **Information**
--- | --- 
**Prissättning** | Prisinformation finns i Priser [för Azure-blobar](https://azure.microsoft.com/pricing/details/storage/page-blobs/) och [Prissättning för privata Azure-länkar.](https://azure.microsoft.com/pricing/details/private-link/)  
**Krav för virtuellt nätverk** | Slutpunkten för ExpressRoute/VPN-gatewayen ska finnas i det valda virtuella nätverket eller i ett virtuellt nätverk som är anslutet till det. Du kan behöva ~15 IP-adresser i det virtuella nätverket.  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Skapa ett projekt med privat slutpunktsanslutning

Använd den [här artikeln](https://docs.microsoft.com/azure/migrate/create-manage-projects#create-a-project-for-the-first-time) för att konfigurera ett nytt Azure Migrate projekt. 

> [!Note]
> Du kan inte ändra anslutningsmetoden till privat slutpunktsanslutning för befintliga Azure Migrate projekt.

I avsnittet **Avancerad** konfiguration anger du informationen nedan för att skapa en privat slutpunkt för ditt Azure Migrate projekt.
- I **Anslutningsmetod** väljer du **Privat slutpunkt.** 
- I **Inaktivera offentlig slutpunktsåtkomst** behåller du standardinställningen **Nej.** Vissa migreringsverktyg kanske inte kan ladda upp användningsdata till Azure Migrate om offentlig nätverksåtkomst är inaktiverad. [Läs mer.](#other-integrated-tools)
- I **Virtuell nätverksprenumeration** väljer du prenumerationen för det privata virtuella slutpunktsnätverket. 
- I **Virtuellt nätverk** väljer du det virtuella nätverket för den privata slutpunkten. Enheten Azure Migrate och andra programvarukomponenter som behöver ansluta till Azure Migrate-projektet måste finnas i det här nätverket eller i ett anslutet virtuellt nätverk.
- I **Undernät** väljer du undernätet för den privata slutpunkten. 

Välj **Skapa**. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Stäng inte den här sidan när projektet skapas.

![Skapa projekt](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

    
Detta skapar ett migreringsprojekt och kopplar en privat slutpunkt till det. 

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>Identifiera och utvärdera servrar för migrering med hjälp av azure private link 

### <a name="set-up-the-azure-migrate-appliance"></a>Konfigurera Azure Migrate enhet 

1. I **Identifiera datorer** Är dina datorer  >  **virtualiserade?** väljer du servertypen.
2. I **Generera Azure Migrate projektnyckel** anger du ett namn för Azure Migrate installationen. 
3. Välj **Generera nyckel för** att skapa de Azure-resurser som krävs. 

    > [!Important]
    > Stäng inte sidan Identifiera datorer när du skapar resurser.  
    - I det här steget Azure Migrate ett nyckelvalv, lagringskonto, Recovery Services-valv (endast för agentlösa VMware-migreringar) och några interna resurser och kopplar en privat slutpunkt till varje resurs. De privata slutpunkterna skapas i det virtuella nätverk som valdes när projektet skapades.  
    - När de privata slutpunkterna har skapats uppdateras DNS CNAME-resursposterna för Azure Migrate-resurserna till ett alias i en underdomän med prefixet "privatelink". Som standard Azure Migrate också en privat DNS-zon som motsvarar underdomänen "privatelink" för varje resurstyp och infogar DNS A-poster för de associerade privata slutpunkterna. Detta gör att Azure Migrate och andra programvarukomponenter som finns i källnätverket kan nå Azure Migrate-resursslutpunkter på privata IP-adresser.  
    - Azure Migrate också en hanterad [](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) identitet för migreringsprojektet och ger behörigheter till den hanterade identiteten för säker åtkomst till lagringskontot.  

4. När nyckeln har genererats kopierar du nyckelinformationen för att konfigurera och registrera installationen.   

#### <a name="download-the-appliance-installer-file"></a>Ladda ned installationsfilen  

> [!Note]
> Om du har problem med att ladda ned installationsfilen skapar du ett supportärenden.

Azure Migrate: Identifiering och utvärdering använder en enkel Azure Migrate enhet. Installationen utför serveridentifiering och skickar metadata för serverkonfiguration och prestanda till Azure Migrate.

Om du vill konfigurera installationen laddar du ned den komprimerade filen som innehåller installationsskriptet från portalen. Kopiera den komprimerade filen på den server som ska vara värd för installationen. 

Kontrollera att servern [](https://docs.microsoft.com/azure/migrate/migrate-appliance) uppfyller maskinvarukraven för det valda scenariot (VMware/Hyper-V/fysisk eller annan) och kan ansluta till nödvändiga Azure-URL:er – [offentliga](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) moln och myndighetsmoln. [](./migrate-appliance.md#government-cloud-urls-for-private-link-connectivity)

När du har hämtat den komprimerade filen kör du installationsskriptet för att distribuera installationen.

#### <a name="run-the-script"></a>Kör skriptet

1. Extrahera den komprimerade filen till en mapp på den server som ska vara värd för installationen. 
2. Starta PowerShell på datorn med administratörsbehörighet (förhöjd behörighet).
3. Ändra PowerShell-katalogen till mappen som innehåller det innehåll som extraheras från den nedladdade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**, enligt följande:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1```
   
5. När skriptet har slutförts startas installationens konfigurationshanterare så att du kan konfigurera installationen. Om det uppstår problem granskar du skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>Konfigurera installationen och starta kontinuerlig identifiering

Öppna en webbläsare på valfri dator som kan ansluta till enhetens server och öppna URL:en för enhetens konfigurationshanterare: `https://appliance name or IP address: 44368` . Alternativt kan du öppna konfigurationshanteraren från installationens serverdator genom att välja genvägen för Konfigurationshanteraren.

#### <a name="set-up-prerequisites"></a>Konfigurera förutsättningar

1. Läs informationen från tredje part och godkänn **licensvillkoren**.    
 
2. I konfigurationshanteraren > **konfigurera krav** gör du följande:
   - **Anslutning:** Enheten söker efter åtkomst till de URL:er som krävs. Om servern använder en proxyserver:
     - Välj **Konfigurera proxy för** att ange proxyadressen eller och `http://ProxyIPAddress` `http://ProxyFQDN` lyssningsporten.
     - Ange autentiseringsuppgifter om proxyn kräver autentisering. Endast HTTP-proxy stöds.
     - Om du vill kan du lägga till en lista över URL:er/IP-adresser som ska kringgå proxyservern. Om du använder privat ExpressRoute-peering måste du kringgå dessa [URL:er.](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute#configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations)
     - Du måste välja Spara **för att** registrera konfigurationen om du har uppdaterat proxyserverinformationen eller lagt till URL:er/IP-adresser för att kringgå proxyn.
     
        > [!Note]
        > Om du får ett fel med aka.ms/*-länken under anslutningskontrollen och du inte vill att enheten ska få åtkomst till den här URL:en via Internet måste du inaktivera tjänsten för automatisk uppdatering i installationen genom att följa stegen [**här**](https://docs.microsoft.com/azure/migrate/migrate-appliance#turn-off-auto-update). När den automatiska uppdateringen har inaktiverats hoppas aka.ms/ URL-anslutningskontrollen över. 

   - **Tidssynkronisering:** Tiden på installationen ska vara synkroniserad med Internettiden för att identifieringen ska fungera korrekt.
   - **Installera uppdateringar:** Installationen ser till att de senaste uppdateringarna installeras. När kontrollen är klar kan du välja Visa installationstjänster **för** att se status och versioner av de tjänster som körs på installationsservern.
        > [!Note]
        > Om du har valt att inaktivera tjänsten för automatisk uppdatering i installationen kan du uppdatera installationstjänster manuellt för att hämta de senaste versionerna av tjänsterna genom att följa stegen [**här**](https://docs.microsoft.com/azure/migrate/migrate-appliance#manually-update-an-older-version).
   - **Installera VDDK**: ( Krävs endast för _VMware-installation)_ Enheten kontrollerar att VMware vSphere Virtual Disk Development Kit (VDDK) är installerat. Om det inte är installerat laddar du ned VDDK 6.7 från VMware och extraherar det nedladdade zip-innehållet till den angivna platsen på installationen, enligt **installationsanvisningarna.**

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Registrera installationen och starta kontinuerlig identifiering

När kontrollen av förhandskrav har slutförts följer du dessa steg för att registrera installationen och starta kontinuerlig identifiering för respektive scenarier: virtuella [VMware-datorer,](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#register-the-appliance-with-azure-migrate)virtuella [Hyper-V-datorer,](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v#register-the-appliance-with-azure-migrate)fysiska [servrar,](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical#register-the-appliance-with-azure-migrate) [virtuella AWS-datorer](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws#register-the-appliance-with-azure-migrate), [virtuella GCP-datorer](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp#register-the-appliance-with-azure-migrate).


>[!Note]
> Om du får DNS-lösningsproblem under installationens registrering eller vid tidpunkten för att  starta identifieringen ser du till att Azure Migrate-resurser som skapades under steget Generera nyckel i portalen kan nås från den lokala server som är värd för Azure Migrate-installationen. [Läs mer om hur du verifierar nätverksanslutningen](#troubleshoot-network-connectivity).

### <a name="assess-your-servers-for-migration-to-azure"></a>Utvärdera dina servrar för migrering till Azure
När identifieringen är klar utvärderar du dina servrar (virtuella[VMware-datorer,](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm)virtuella [Hyper-V-datorer,](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v) [](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm)fysiska servrar, virtuella [AWS-datorer,](https://docs.microsoft.com/azure/migrate/tutorial-assess-aws)virtuella [GCP-datorer)](https://docs.microsoft.com/azure/migrate/tutorial-assess-gcp)för migrering till virtuella Azure-datorer eller Azure VMware Solution (AVS) med hjälp av verktyget Azure Migrate: Identifiering och utvärdering. 

Du kan också [utvärdera dina](https://docs.microsoft.com/azure/migrate/tutorial-discover-import#prepare-the-csv) lokala datorer med verktyget Azure Migrate: Identifiering och utvärdering med hjälp av en importerad fil med kommaavgränsade värden (CSV).   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>Migrera servrar till Azure med azure private link

I följande avsnitt beskrivs de steg som krävs för att Azure Migrate med privata [slutpunkter](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) för migreringar med hjälp av privat ExpressRoute-peering eller VPN-anslutningar.  

Den här artikeln visar en proof-of-concept-distributionssökväg för agentbaserade replikeringar för att [](./tutorial-migrate-physical-virtual-machines.md)migrera dina virtuella [VMware-datorer,](./tutorial-migrate-vmware-agent.md)virtuella [Hyper-V-datorer,](./tutorial-migrate-physical-virtual-machines.md)fysiska servrar, virtuella datorer som körs på [AWS](./tutorial-migrate-aws-virtual-machines.md), virtuella datorer som körs på [GCP](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)eller virtuella datorer som körs på en annan virtualiseringsprovider med hjälp av privata Azure-slutpunkter. Du kan använda en liknande metod för att utföra [agentlösa Hyper-V-migreringar](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) med hjälp av private link.

>[!Note]
>[Agentlösa VMware-migreringar](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical) kräver Internetåtkomst eller anslutning via ExperessRoute Microsoft-peering. 

### <a name="set-up-a-replication-appliance-for-migration"></a>Konfigurera en replikeringsinstallation för migrering 

Följande diagram illustrerar det agentbaserade replikeringsarbetsflödet med privata slutpunkter med hjälp Azure Migrate: Server Migration.  

![Replikeringsarkitektur](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

Verktyget använder en replikeringsinstallation för att replikera dina servrar till Azure. Använd den här artikeln [för att förbereda och konfigurera en dator för replikeringsinstallationen. ](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#prepare-a-machine-for-the-replication-appliance)

När du har ställt in replikeringsinstallationen använder du följande instruktioner för att skapa de nödvändiga resurserna för migrering. 

1. I **Identifiera datorer** Är dina datorer  >  **virtualiserade?** väljer du Inte **virtualiserad/Övrigt.**
2. I **Målregion** väljer du och bekräftar den Azure-region som du vill migrera datorerna till.
3. Välj **Skapa resurser för** att skapa de Azure-resurser som krävs. Stäng inte sidan när du skapar resurser.   
    - Detta skapar ett Recovery Services-valv i bakgrunden och aktiverar en hanterad identitet för valvet. Ett Recovery Services-valv är en entitet som innehåller replikeringsinformation för servrar och som används för att utlösa replikeringsåtgärder.  
    - Om Azure Migrate har en privat slutpunktsanslutning skapas en privat slutpunkt för Recovery Services-valvet. Detta lägger till fem fullständigt kvalificerade privata namn (FQDN) till den privata slutpunkten, ett för varje mikrotjänst som är länkad till Recovery Services-valvet.   
    - De fem domännamnen är formaterade i det här mönstret: <br/> _{Vault-ID}-asr-pod01-{type}-. {target-geo-code}_. privatelink.siterecovery.windowsazure.com  
    - Som standard skapar Azure Migrate en privat DNS-zon och lägger till DNS A-poster för Recovery Services-valvets mikrotjänster. Den privata DNS-zonen länkas sedan till det privata slutpunktens virtuella nätverk. Detta gör att den lokala replikeringsinstallationen kan matcha de fullständigt kvalificerade domännamnen med sina privata IP-adresser.

4. Innan du registrerar replikeringsinstallationen måste du se till att valvets FQDN för privata länkar kan nås från den dator som är värd för replikeringsinstallationen. [Läs mer om hur du verifierar nätverksanslutningen.](#troubleshoot-network-connectivity) 

5. När du har verifierat anslutningen laddar du ned installationens konfiguration och nyckelfil, kör installationsprocessen och registrerar installationen på Azure Migrate. Granska de [detaljerade stegen här.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#set-up-the-replication-appliance) När du har ställt in replikeringsinstallationen följer du dessa anvisningar [för att installera mobilitetstjänsten](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#install-the-mobility-service) på de datorer som du vill migrera. 

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>Replikera servrar till Azure med hjälp av en privat Azure-länk 

Följ nu dessa [steg för](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) att välja servrar för replikering.  

I **Inställningar för**  >  **replikeringsmål**  >  **cache-/replikeringslagringskonto** använder du listrutan för att välja ett lagringskonto som ska replikeras via en privat länk.  

Om ditt Azure Migrate-projekt har en privat slutpunktsanslutning måste du bevilja behörigheter till den hanterade identiteten för  [Recovery Services-valvet](#grant-access-permissions-to-the-recovery-services-vault) för att få åtkomst till det lagringskonto som Azure Migrate.   

Om du vill aktivera replikeringar via en privat länk skapar du [dessutom en privat slutpunkt för lagringskontot.](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Bevilja åtkomstbehörigheter till Recovery Services-valvet

Den hanterade identiteten för Recovery Services-valvet kräver behörigheter för autentiserad åtkomst till cache-/replikeringslagringskontot. 

Använd vägledningen nedan för att identifiera Recovery Services-valvet som skapats Azure Migrate och bevilja de behörigheter som krävs. 

**_Identifiera Recovery Services-valvet och objekt-ID:t för den hanterade identiteten_**

Information om Recovery Services-valvet finns på sidan Azure Migrate: Server **Migration.** 

1. Gå till **Azure Migrate och** välj **Översikt på** panelen Azure Migrate: Server Migration.

    ![Översiktssidan på Azure Migrate hubben](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. Välj Egenskaper i den vänstra **rutan.** Anteckna Recovery Services-valvets namn och ID för hanterad identitet. Valvet har _privat slutpunkt som_ **anslutningstyp och** _Övrigt_ som **replikeringstyp.** Du behöver den här informationen samtidigt som du ger åtkomst till valvet.
      
    ![Azure Migrate: Egenskapssidan servermigrering](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**_Bevilja de behörigheter som krävs för att få åtkomst till lagringskontot_**

 Den hanterade identiteten för valvet måste beviljas följande rollbehörigheter för det lagringskonto som krävs för replikering.  I så fall måste du skapa lagringskontot i förväg.

>[!Note]
> För att migrera virtuella Hyper-V-datorer till Azure med privat länk måste du bevilja åtkomst till både lagringskontot för replikering och cachelagringskontot. 

Rollbehörigheterna varierar beroende på typen av lagringskonto.

- Resource Manager lagringskonton (standardtyp):
  - [Deltagare](../role-based-access-control/built-in-roles.md#contributor) _och_
  - [Storage Blob Data-deltagare](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager lagringskonton (Premium-typ):
  - [Deltagare](../role-based-access-control/built-in-roles.md#contributor) _och_
  - [Storage Blob Data-ägare](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. Gå till det replikerings-/cachelagringskonto som valts för replikering. Välj **Åtkomstkontroll (IAM)** i den vänstra rutan. 

1. I avsnittet **Lägg till en rolltilldelning** väljer du Lägg **till**:

   ![Lägg till en rolltilldelning](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)


1. På sidan **Lägg till rolltilldelning** går du till **fältet** Roll och väljer lämplig roll från listan över behörigheter som nämns ovan. Ange namnet på valvet som antecknades tidigare och välj **Spara**.

    ![Tillhandahålla rollbaserad åtkomst](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

4. Förutom dessa behörigheter måste du även tillåta åtkomst till Microsofts betrodda tjänster. Om nätverksåtkomsten är begränsad till valda nätverk väljer du Tillåt **betrodda Microsoft-tjänster** att komma åt det här lagringskontot i avsnittet Undantag på **fliken** Nätverk.  

![Tillåt betrodda Microsoft-tjänster för lagringskonto](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)


### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>Skapa en privat slutpunkt för lagringskontot (valfritt)

Om du vill replikera med ExpressRoute med privat peering [skapar](https://docs.microsoft.com/azure/private-link/tutorial-private-endpoint-storage-portal#create-storage-account-with-a-private-endpoint) du en privat slutpunkt för cache-/replikeringslagringskontona (målunderkälla: **_blob)._** 

>[!Note]
>
> - Du kan bara skapa privata slutpunkter på ett Generell användning v2-lagringskonto (GPv2). Prisinformation finns i Prissättning [för Azure-sidblobar](https://azure.microsoft.com/pricing/details/storage/page-blobs/) och [Prissättning för privata Länkar i Azure](https://azure.microsoft.com/pricing/details/private-link/)

Den privata slutpunkten för lagringskontot ska skapas i samma virtuella nätverk som Azure Migrate projekt privat slutpunkt eller ett annat virtuellt nätverk som är anslutet till det här nätverket. 

Välj **Ja** och integrera med en privat DNS-zon. Den privata DNS-zonen hjälper till att dirigera anslutningarna från det virtuella nätverket till lagringskontot via en privat länk. Om **du väljer** Ja länkar automatiskt DNS-zonen till det virtuella nätverket och lägger till DNS-posterna för att lösa nya IP-adresser och fullständigt kvalificerade domännamn som skapats. Läs mer om [privata DNS-zoner.](https://docs.microsoft.com/azure/dns/private-dns-overview)

Om användaren som skapar den privata slutpunkten också är ägare till lagringskontot godkänns den privata slutpunkten automatiskt. Annars måste ägaren av lagringskontot godkänna den privata slutpunkten för användning. Om du vill godkänna eller avvisa en begärd privat slutpunktsanslutning går du till **Privata slutpunktsanslutningar** under **Nätverk** på lagringskontosidan.

Granska statusen för anslutningen till den privata slutpunkten innan du fortsätter. 

![Godkännandestatus för privat slutpunkt](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

När du har skapat den privata slutpunkten använder du listrutan i Inställningarna för replikera mål Cachelagringskonto för att välja lagringskontot för replikering  >    >   via en privat länk.  

Kontrollera att den lokala replikeringsinstallationen har nätverksanslutning till lagringskontot på den privata slutpunkten. [Läs mer om hur du verifierar nätverksanslutningen.](#troubleshoot-network-connectivity)

>[!Note]
>
> - Om replikeringslagringskontot är av premiumtyp måste  du välja ett annat lagringskonto av _standardtyp_ för cachelagringskontot för migrering av virtuella Hyper-V-datorer till Azure. I det här fallet måste du skapa privata slutpunkter för både replikerings- och cachelagringskontot.  

Följ sedan de här instruktionerna för [att granska och starta replikeringen](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) och utföra [migreringar.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#run-a-test-migration)  

## <a name="troubleshoot-network-connectivity"></a>Felsöka nätverksanslutning 

### <a name="validate-private-endpoints-configuration"></a>Verifiera konfigurationen av privata slutpunkter 

Kontrollera att den privata slutpunkten är ett godkänt tillstånd.  

1. Gå till Azure Migrate: egenskaper för identifiering och utvärdering och servermigrering.
2. Egenskapssidan innehåller en lista över privata slutpunkter och FQDN för privata länkar som skapades automatiskt av Azure Migrate.  

3. Välj den privata slutpunkt som du vill diagnostisera.  
    1. Kontrollera att anslutningstillståndet är Godkänd.
    2. Om anslutningen är i ett väntande tillstånd måste du få den godkänd.
    3. Du kan också navigera till den privata slutpunktsresursen och granska om det virtuella nätverket matchar det virtuella nätverket Migrate project private endpoint . 

    ![Visa privat slutpunktsanslutning](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)

### <a name="verify-dns-resolution"></a>Verifiera DNS-upplösning 

Den lokala installationen (eller replikeringsprovidern) kommer åt Azure Migrate med sina fullständigt kvalificerade domännamn för privata länkar (FQDN). Du kan behöva ytterligare DNS-inställningar för att matcha den privata IP-adressen för de privata slutpunkterna från källmiljön. [Använd den här artikeln](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) för att förstå DE DNS-konfigurationsscenarier som kan hjälpa dig att felsöka eventuella problem med nätverksanslutningen.  

Verifiera den privata länkanslutningen genom att utföra en DNS-lösning av Azure Migrate-resursslutpunkter (FQDN för privat länkresurs) från den lokala server som är värd för Migrate-installationen och se till att den matchar en privat IP-adress. Information om privata slutpunkter och FQDN för privat länkresurs finns på egenskapssidorna Identifiering och utvärdering och Servermigrering. Välj **Ladda ned DNS-inställningar** för att visa listan.   

 ![Azure Migrate: Egenskaper för identifiering och utvärdering](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 ![Azure Migrate: Egenskaper för servermigrering](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties.png)

Ett illustrerande exempel på DNS-upplösning för det privata länk-FQDN för lagringskontot.  

- Ange _nslookup<storage-account-name>_.blob.core.windows.net.  Ersätt <storage-account-name> med namnet på det lagringskonto som används för Azure Migrate.  

    Du får ett meddelande som liknar följande:  

   ![DNS-lösningsexempel](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- En privat IP-adress på 10.1.0.5 returneras för lagringskontot. Den här adressen tillhör det privata virtuella nätverkets slutpunktsundernät.   

Du kan verifiera DNS-upplösningen för andra Azure Migrate artefakter med en liknande metod.   

Om DNS-upplösningen är felaktig följer du dessa steg:  

- Om du använder en anpassad DNS granskar du dina anpassade DNS-inställningar och kontrollerar att DNS-konfigurationen är korrekt. Vägledning finns i Översikt [över privat slutpunkt: DNS-konfiguration.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)
- Om du använder Azure-tillhandahållna DNS-servrar kan du läsa mer om felsökning i avsnittet nedan.  

> [!Tip]
> Du kan uppdatera DNS-posterna i källmiljön manuellt genom att redigera DNS-värdfilen i din lokala installation med FQDN för den privata länkresursen och deras associerade privata IP-adresser. Det här alternativet rekommenderas endast för testning. <br/>  


### <a name="validate-the-private-dns-zone"></a>Verifiera Privat DNS zonen   
Om DNS-upplösningen inte fungerar enligt beskrivningen i föregående avsnitt kan det finnas ett problem med din Privat DNS zon.  

#### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Bekräfta att den Privat DNS zonen finns  
Som standard Azure Migrate också en privat DNS-zon som motsvarar underdomänen "privatelink" för varje resurstyp. Den privata DNS-zonen skapas i samma Azure-resursgrupp som resursgruppen för den privata slutpunkten. Azure-resursgruppen ska innehålla privata DNS-zonresurser i följande format:
- privatelink.vaultcore.azure.net för nyckelvalvet 
- privatelink.blob.core.windows.net för lagringskontot
- privatelink.siterecovery.windowsazure.com för Recovery Services-valvet (för Hyper-V- och agentbaserade replikeringar)
- privatelink.prod.migration.windowsazure.com – migrera projekt, utvärderingsprojekt och identifieringsplats.   

Den privata DNS-zonen skapas automatiskt av en Azure Migrate (förutom det cache-/replikeringslagringskonto som användaren har valt). Du kan hitta den länkade privata DNS-zonen genom att gå till sidan för den privata slutpunkten och välja DNS-konfigurationer. Du bör se den privata DNS-zonen under avsnittet för privat DNS-integrering. 

![Skärmbild av DNS-konfiguration](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration.png)  

Om DNS-zonen inte finns (se nedan) skapar [du en ny Privat DNS Zonresurs.](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal)  

![Skapa en Privat DNS zon](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone.png) 

#### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Bekräfta att den Privat DNS zonen är länkad till det virtuella nätverket  
Den privata DNS-zonen ska vara länkad till det virtuella nätverk som innehåller den privata slutpunkten för DNS-frågan för att matcha den privata IP-adressen för resursslutpunkten. Om den privata DNS-zonen inte är länkad till rätt Virtual Network dns-upplösning från det virtuella nätverket ignorerar den privata DNS-zonen.   

Gå till den privata DNS-zonresursen i Azure Portal och välj länkarna för virtuella nätverk på den vänstra menyn. Du bör se de länkade virtuella nätverken.

![Visa länkar till virtuella nätverk](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links.png) 

Då visas en lista med länkar, var och en med namnet på ett virtuellt nätverk i din prenumeration. Det virtuella nätverk som innehåller resursen Privat slutpunkt måste anges här. Annars följer [du den här artikeln för](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) att länka den privata DNS-zonen till ett virtuellt nätverk.    

När den privata DNS-zonen är länkad till det virtuella nätverket söker DNS-begäranden från det virtuella nätverket efter DNS-poster i den privata DNS-zonen. Detta krävs för korrekt adressmatchning till det virtuella nätverket där den privata slutpunkten skapades.   

#### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Bekräfta att den privata DNS-zonen innehåller rätt A-poster 

Gå till den privata DNS-zon som du vill felsöka. På sidan Översikt visas alla DNS-poster för den privata DNS-zonen. Kontrollera att det finns en DNS A-post för resursen. Värdet för A-posten (IP-adressen) måste vara resursernas privata IP-adress. Om du hittar A-posten med fel IP-adress måste du ta bort fel IP-adress och lägga till en ny. Vi rekommenderar att du tar bort hela A-posten och lägger till en ny och rensar DNS på den lokala källinstallationen.   

Ett illustrerande exempel för lagringskontots DNS A-post i den privata DNS-zonen:

![DNS-poster](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Ett illustrerande exempel för Recovery Services-valvets mikrotjänst-DNS A-poster i den privata DNS-zonen: 

![DNS-poster för Recovery Services-valv](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records.png)   

>[!Note]
> När du tar bort eller ändrar en A-post kan datorn fortfarande matchas mot den gamla IP-adressen eftersom TTL-värdet (Time To Live) kanske inte har gått ut ännu.  

#### <a name="other-things-that-may-affect-private-link-connectivity"></a>Andra saker som kan påverka privat länkanslutning  

Det här är en icke-fullständig lista över objekt som finns i avancerade eller komplexa scenarier: 

- Brandväggsinställningar, antingen Azure Firewall till det virtuella nätverket eller en anpassad brandväggslösning som distribueras på enhetens dator.  
- Nätverks-peering, vilket kan påverka vilka DNS-servrar som används och hur trafiken dirigeras.  
- Anpassade gatewaylösningar (NAT) kan påverka hur trafiken dirigeras, inklusive trafik från DNS-frågor.

Mer information finns i [felsökningsguiden för anslutningsproblem med privata slutpunkter.](https://docs.microsoft.com/azure/private-link/troubleshoot-private-endpoint-connectivity)  

## <a name="next-steps"></a>Nästa steg 
- [Slutför migreringsprocessen och](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#complete-the-migration) granska [metodtipsen efter migreringen.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#post-migration-best-practices)