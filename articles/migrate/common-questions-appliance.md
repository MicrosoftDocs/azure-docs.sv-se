---
title: Vanliga frågor och svar om Azure Migrate
description: Få svar på vanliga frågor om Azure Migrate-enheten.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 5a050d9aab9e8665c6048391488e57c9b4af10a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043073"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate utrustning: vanliga frågor

I den här artikeln besvaras vanliga frågor om Azure Migrate-enheten. Om du har andra frågor kontrollerar du följande resurser:

- [Allmänna frågor](resources-faq.md) om Azure Migrate
- Frågor om [identifiering, utvärdering och beroende visualisering](common-questions-discovery-assessment.md)
- Frågor om [Server migrering](common-questions-server-migration.md)
- Få svar på frågor i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Vad är Azure Migrates apparaten?

Azure Migrate-installationen är en förenklad enhet som verktyget Azure Migrate: Server bedömning använder för att identifiera och utvärdera fysiska eller virtuella servrar från lokalt eller i ett moln. Verktyget Azure Migrate: Migreringsverktyg för Server använder också installationen av en agent lös migrering av lokala virtuella VMware-datorer.

Här är mer information om Azure Migrate-enheten:

- Enheten distribueras lokalt som en virtuell dator eller fysisk dator.
- Enheten identifierar lokala datorer och skickar kontinuerligt metadata och prestanda data till Azure Migrate.
- Identifiering av enheter är agenten. Inget har installerats på identifierade datorer.

[Läs mer](migrate-appliance.md) om enheten.

## <a name="how-can-i-deploy-the-appliance"></a>Hur kan jag distribuera installationen?

Installationen kan distribueras med ett par olika metoder:

- Installationen kan distribueras med hjälp av en mall för servrar som kör i VMware-eller Hyper-V-miljön ([ägg-mall för VMware](how-to-set-up-appliance-vmware.md) eller [VHD för Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Om du inte vill använda en mall kan du distribuera-installationen för VMware-eller Hyper-V-miljön med hjälp av ett [PowerShell-skript](deploy-appliance-script.md).
- I Azure Government bör du distribuera installationen med hjälp av ett PowerShell-skript. Se distributions stegen [här](deploy-appliance-script-government.md).
- För fysiska eller virtualiserade servrar lokalt eller i andra moln distribuerar du alltid installationen med hjälp av ett PowerShell-skript. Se distributions stegen [här](how-to-set-up-appliance-physical.md).

## <a name="how-does-the-appliance-connect-to-azure"></a>Hur ansluter-enheten till Azure?

Enheten kan ansluta via Internet eller med hjälp av Azure-ExpressRoute. 

- Kontrol lera att enheten kan ansluta till dessa [Azure-URL: er](./migrate-appliance.md#url-access). 
- Du kan använda ExpressRoute med Microsoft-peering. Offentlig peering är inaktuell och är inte tillgänglig för nya ExpressRoute-kretsar.
- Privat peering stöds bara.


## <a name="does-appliance-analysis-affect-performance"></a>Påverkar enhets analysen prestanda?

Azure Migrate utrustnings profiler lokalt för att mäta prestanda data kontinuerligt. Den här profileringen har nästan ingen prestanda påverkan på profilerade datorer.

## <a name="can-i-harden-the-appliance-vm"></a>Kan jag härdning av den virtuella datorn?

När du använder den nedladdade mallen för att skapa den virtuella datorns dator kan du lägga till komponenter (till exempel) till mallen om du lämnar de kommunikations-och brand Väggs regler som krävs för Azure Migrates enheten.

## <a name="what-network-connectivity-is-required"></a>Vilken nätverks anslutning krävs?

Enheten behöver åtkomst till Azure-URL: er. [Granska](migrate-appliance.md#url-access) URL-listan.

## <a name="what-data-does-the-appliance-collect"></a>Vilka data samlas in av enheten?

I följande artiklar finns information om data som Azure Migrates apparaten samlar in på virtuella datorer:

- **Virtuell VMware-dator**: [Granska](migrate-appliance.md#collected-data---vmware) insamlade data.
- **Virtuell Hyper-V-dator**: [Granska](migrate-appliance.md#collected-data---hyper-v) insamlade data.
- **Fysiska eller virtuella servrar**:[Granska](migrate-appliance.md#collected-data---physical) insamlade data.

## <a name="how-is-data-stored"></a>Hur lagras data?

Data som samlas in av Azure Migrates enheten lagras på den Azure-plats där du skapade Azure Migrate-projektet.

Här är mer information om hur data lagras:

- Insamlade data lagras säkert i CosmosDB i en Microsoft-prenumeration. Data tas bort när du tar bort Azure Migrate-projektet. Lagringen hanteras av Azure Migrate. Du kan inte specifikt välja ett lagrings konto för insamlade data.
- Om du använder [beroende visualisering](concepts-dependency-visualization.md)lagras de data som samlas in i en Azure Log Analytics-arbetsyta som skapats i din Azure-prenumeration. Data tas bort när du tar bort Log Analytics arbets ytan i din prenumeration. 

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Hur mycket data överförs under kontinuerlig profilering?

Mängden data som skickas till Azure Migrate beror på flera parametrar. Ett Azure Migrate projekt som har 10 datorer (var och en med en disk och ett nätverkskort) skickar till exempel cirka 50 MB data per dag. Värdet är ungefärlig; det faktiska värdet varierar beroende på antalet data punkter för diskarna och nätverkskorten. Om antalet datorer, diskar eller nätverkskort ökar, är ökningen av data som skickas inte linjära.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Är data krypterade i vila och under överföring?

Ja, för båda:

- Metadata skickas säkert till Azure Migrate tjänsten via Internet via HTTPS.
- Metadata lagras i en [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) -databas och i [Azure Blob Storage](../storage/common/storage-service-encryption.md) i en Microsoft-prenumeration. Metadata krypteras i vila för lagring.
- Data för beroende analys krypteras också vid överföring (med säker HTTPS). Den lagras i en Log Analytics arbets yta i din prenumeration. Data krypteras i vila för beroende analys.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Hur ansluter-enheten till vCenter Server?

De här stegen beskriver hur installationen ansluter till VMware vCenter Server:

1. Enheten ansluter till vCenter Server (port 443) genom att använda de autentiseringsuppgifter du angav när du konfigurerade installationen.
2. Enheten använder VMware PowerCLI för att fråga vCenter Server för att hämta metadata om de virtuella datorer som hanteras av vCenter Server.
3. Installations programmet samlar in konfigurations data om virtuella datorer (kärnor, minne, diskar, nätverkskort) och prestanda historiken för varje virtuell dator under den senaste månaden.
4. Insamlade metadata skickas till Azure Migrate: Server bedömnings verktyget (via Internet via HTTPS) för utvärdering.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Kan Azure Migrate-enheten ansluta till flera vCenter-servrar?

Nej. Det finns en en-till-en-mappning mellan en [Azure Migrate-apparat](migrate-appliance.md) och vCenter Server. För att identifiera virtuella datorer på flera vCenter Server instanser måste du distribuera flera enheter. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Kan ett Azure Migrate projekt ha flera anordningar?

Ett projekt kan ha flera registrerade enheter. En apparat kan dock bara registreras med ett projekt.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Kan Azure Migrate utrustning/-replikering ansluta till samma vCenter?

Ja. Du kan lägga till både Azure Migrate-installationen (som används för utvärdering och agent lös VMware-migrering) och replikeringstjänsten (som används för agentbaserade migrering av virtuella VMware-datorer) till samma vCenter-Server. Men se till att du inte konfigurerar båda enheterna på samma virtuella dator och inte stöds för närvarande.

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Hur många virtuella datorer eller servrar kan jag identifiera med en apparat?

Du kan identifiera upp till 10 000 virtuella VMware-datorer, upp till 5 000 virtuella Hyper-V-datorer och upp till 1000 fysiska servrar med en enda apparat. Om du har fler datorer i din lokala miljö läser du om skalning av [en Hyper-V-utvärdering](scale-hyper-v-assessment.md), [skalning av en VMware-utvärdering](scale-vmware-assessment.md)och [skalning av en fysisk server-utvärdering](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Kan jag ta bort en installation?

Det finns för närvarande inte stöd för att ta bort en apparat från projektet.

Det enda sättet att ta bort installationen är att ta bort resurs gruppen som innehåller det Azure Migrate projekt som är associerat med enheten.

Men om du tar bort resurs gruppen raderas även andra registrerade enheter, den identifierade inventeringen, utvärderingen och alla andra Azure-komponenter i resurs gruppen som är associerade med projektet.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Kan jag använda enheten med en annan prenumeration eller ett annat projekt?

Om du vill använda installationen med en annan prenumeration eller ett annat projekt måste du konfigurera om den befintliga installationen genom att köra PowerShell-installationsprogrammet för det speciella scenariot (VMware/Hyper-V/fysisk) på installations datorn. Skriptet kommer att rensa befintliga komponenter och inställningar för installationen för att distribuera en ny installation. Se till att rensa webbläsarens cacheminne innan du börjar använda Konfigurations hanteraren för nyligen distribuerade installationer.

Du kan inte heller återanvända en befintlig Azure Migrate projekt nyckel på en omkonfigurerad installation. Se till att generera en ny nyckel från den önskade prenumerationen/projektet för att slutföra registreringen av produkten.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Kan jag konfigurera enheten på en virtuell Azure-dator?

Nej. Det här alternativet stöds för närvarande inte.

## <a name="can-i-discover-on-an-esxi-host"></a>Kan jag identifiera på en ESXi-värd?

Nej. Du måste ha vCenter Server för att kunna identifiera virtuella VMware-datorer.

## <a name="how-do-i-update-the-appliance"></a>Hur gör jag för att du uppdatera installationen?

Som standard uppdateras enheten och dess installerade agenter automatiskt. Enheten söker efter uppdateringar var 24: e timme. Uppdateringar som inte har gjorts om. 

Endast apparaten och installations agenterna uppdateras av dessa automatiska uppdateringar. Operativ systemet uppdateras inte genom att Azure Migrate automatiska uppdateringar. Använd Windows-uppdateringar för att hålla operativ systemet uppdaterat.

## <a name="can-i-check-agent-health"></a>Kan jag kontrol lera agent hälsan?

Ja. På portalen går du till sidan **agent hälsa** för Azure Migrate: Server utvärdering eller Azure Migrate Migreringsverktyg för Server. Där kan du kontrol lera anslutnings statusen mellan Azure och identifierings-och utvärderings agenterna på enheten.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>Kan jag lägga till flera autentiseringsuppgifter för servern på VMware-enheten?

Ja, vi har nu stöd för flera autentiseringsuppgifter för servern för att utföra program varu inventering (identifiering av installerade program), en agent lös beroende analys och identifiering av SQL Server instanser och databaser. [Läs mer](tutorial-discover-vmware.md#provide-server-credentials) om hur du anger autentiseringsuppgifter för installationen av Configuration Manager.

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>Vilken typ av autentiseringsuppgifter för servern kan jag lägga till på VMware-enheten?
Du kan ange domän/Windows (icke-domän)/Linux (icke-domän)/SQL Server autentiseringsuppgifter för Konfigurations hanteraren. [Lär dig mer](add-server-credentials.md) om hur du anger autentiseringsuppgifter och hur vi hanterar dem.

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>Vilken typ av SQL Server anslutnings egenskaper stöds av Azure Migrate för SQL-identifiering?
Azure Migrate krypterar kommunikationen mellan Azure Migrates utrustning och källa SQL Server instanser (med egenskapen krypterad anslutning inställd på TRUE). Dessa anslutningar är krypterade med [TrustServerCertificate](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (anges till sant). transport lagret använder SSL för att kryptera kanalen och kringgå certifikat kedjan för att verifiera förtroende. Installations servern måste vara konfigurerad för att [lita på certifikatets rot utfärdare](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Om inget certifikat har etablerats på servern när det startas, genererar SQL Server ett självsignerat certifikat som används för att kryptera inloggnings paket. [Läs mer](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).


## <a name="next-steps"></a>Nästa steg

Läs [Azure Migrate översikt](migrate-services-overview.md).