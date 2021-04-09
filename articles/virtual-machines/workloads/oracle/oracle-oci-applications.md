---
title: Arkitekturer för att distribuera Oracle-appar på Azure Virtual Machines | Microsoft Docs
description: Program arkitekturer för att distribuera Oracle-appar, inklusive E-Business Suite, JD Edwards EnterpriseOne och östra på Microsoft Azure virtuella datorer med databaser i Azure eller i Oracle Cloud Infrastructure (OCI).
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 07/18/2019
ms.author: kegorman
ms.openlocfilehash: 862bb886c7ec5dfd40c7acdbae2f70f6698a711b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669930"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Arkitekturer för att distribuera Oracle-program på Azure

Microsoft och Oracle har arbetat tillsammans för att kunderna ska kunna distribuera Oracle-program som Oracle E-Business Suite, JD Edwards EnterpriseOne och i molnet. I och med introduktionen av förhands granskning av [privata nätverks anslutningar](configure-azure-oci-networking.md) mellan Microsoft Azure och OCI (Oracle Cloud Infrastructure) kan Oracle-program nu distribueras på Azure med sina backend-databaser i Azure eller OCI. Oracle-program kan också integreras med Azure Active Directory, så att du kan konfigurera enkel inloggning så att användarna kan logga in på Oracle-appen med sina Azure Active Directory (Azure AD)-autentiseringsuppgifter.

OCI erbjuder flera Oracle Database-alternativ för Oracle-program, inklusive DBaaS, Exadata Cloud service, Oracle RAC och Infrastructure-as-a-Service (IaaS). För närvarande är den autonoma databasen inte en backend-slutpunkt för Oracle-program.

Det finns [flera alternativ](oracle-overview.md) för att distribuera Oracle-program i Azure, till exempel på hög tillgänglighet och på ett säkert sätt. Azure erbjuder också [VM-avbildningar för Oracle-databasen](oracle-vm-solutions.md) som du kan distribuera om du väljer att köra dina Oracle-program helt på Azure.

I följande avsnitt beskrivs arkitektur rekommendationer från både Microsoft och Oracle för att distribuera Oracle E-Business Suite, JD Edwards EnterpriseOne och östra i en konfiguration mellan molnet eller helt i Azure. Microsoft och Oracle har testat dessa program och bekräftat att prestanda uppfyller de standarder som anges av Oracle för dessa program.

## <a name="architecture-considerations"></a>Arkitektur överväganden

Oracle-program består av flera tjänster som kan finnas på samma eller flera virtuella datorer i Azure och alternativt i OCI. 

Program instanser kan konfigureras med privata eller offentliga slut punkter. Microsoft och Oracle rekommenderar att du konfigurerar en *virtuell skydds-värd* med en offentlig IP-adress i ett separat undernät för hantering av programmet. Tilldela sedan endast privata IP-adresser till de andra datorerna, inklusive databas nivån. 

När du konfigurerar ett program i en plattforms oberoende arkitektur måste du planera för att se till att IP-adressutrymmet i det virtuella Azure-nätverket inte överlappar det privata IP-adressutrymmet i det virtuella OCI-moln nätverket.

För ytterligare säkerhet ställer du in nätverks säkerhets grupper på en under näts nivå så att endast trafik på vissa portar och IP-adresser tillåts. Datorer på mellan nivå ska till exempel endast ta emot trafik från det virtuella nätverket. Ingen extern trafik bör uppnå datorerna på mellan nivå direkt.

För hög tillgänglighet kan du ställa in redundanta instanser av de olika servrarna i samma tillgänglighets uppsättning eller olika tillgänglighets zoner. Med tillgänglighets zoner får du ett service avtal på 99,99% drift tid, medan tillgänglighets uppsättningar låter dig uppnå ett service avtal på 99,95% drift tid i regionen. Exempel arkitekturer som visas i den här artikeln distribueras över två tillgänglighets zoner.

När du distribuerar ett program med hjälp av sammanlänkning mellan moln kan du fortsätta att använda en befintlig ExpressRoute-krets för att ansluta din Azure-miljö till ditt lokala nätverk. Men du behöver en separat ExpressRoute-krets för sammanlänkning till OCI än den som ansluter till ditt lokala nätverk.

## <a name="e-business-suite"></a>E-Business Suite

Oracle E-Business Suite (EBS) är en uppsättning program, inklusive hantering av leverans kedjor (SCM) och kund Relations hantering (CRM). För att kunna dra nytta av OCI: s hanterade databas portfölj kan EBS distribueras med hjälp av Cross-Cloud-sammanlänkning mellan Microsoft Azure och OCI. I den här konfigurationen körs presentations-och program nivå i Azure och databas nivån i OCI, som illustreras i följande arkitektur diagram (bild 1).

![Arkitektur för E-Business Suite över moln](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Bild 1: E-Business Suite över moln arkitektur* 

I den här arkitekturen är det virtuella nätverket i Azure anslutet till ett virtuellt moln nätverk i OCI med hjälp av sammanlänkningen mellan moln. Program nivån har kon figurer ATS i Azure, medan databasen har kon figurer ATS i OCI. Vi rekommenderar att du distribuerar varje komponent till sitt eget undernät med nätverks säkerhets grupper för att endast tillåta trafik från specifika undernät på specifika portar.

Arkitekturen kan också anpassas för distribution helt och hållet i Azure med hög tillgängliga Oracle-databaser som kon figurer ATS med Oracle data Guard i två tillgänglighets zoner i en region. Följande diagram (bild 2) är ett exempel på detta arkitektur mönster:

![E-Business Suite-arkitektur med enbart Azure](media/oracle-oci-applications/ebs-arch-azure.png)

*Bild 2: E-Business Suite-arkitektur med enbart Azure*

I följande avsnitt beskrivs de olika komponenterna på hög nivå.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Program nivå (mellan nivåer)

Program nivån är isolerad i sitt eget undernät. Det finns flera virtuella datorer som har kon figurer ATS för fel tolerans och enkel korrigerings hantering. De här virtuella datorerna kan backas upp av delad lagring, som erbjuds av Azure NetApp Files och Ultra SSD. Den här konfigurationen gör det enklare att distribuera korrigeringsfiler utan drift avbrott. Datorerna på program nivån bör tillföras av en offentlig belastningsutjämnare så att förfrågningar till EBS program nivå bearbetas även om en dator på nivån är offline på grund av ett fel.

### <a name="load-balancer"></a>Lastbalanserare

Med en Azure Load Balancer kan du distribuera trafik över flera instanser av arbets belastningen för att säkerställa hög tillgänglighet. I det här fallet konfigureras en offentlig belastningsutjämnare, eftersom användarna får åtkomst till EBS-programmet via webben. Belastningsutjämnaren distribuerar belastningen till båda datorerna på mellan nivå. För ytterligare säkerhet kan du tillåta trafik endast från användare som har åtkomst till systemet från företags nätverket med hjälp av en plats-till-plats-VPN-eller ExpressRoute-och nätverks säkerhets grupper.

### <a name="database-tier"></a>Databas nivå

Den här nivån är värd för Oracle-databasen och är indelad i sitt eget undernät. Vi rekommenderar att du lägger till nätverks säkerhets grupper som endast tillåter trafik från program nivån till databas nivån på den Oracle-specifika databas porten 1521.

Microsoft och Oracle rekommenderar en installation med hög tillgänglighet. Hög tillgänglighet i Azure kan uppnås genom att konfigurera två Oracle-databaser i två tillgänglighets zoner med Oracle data Guard, eller genom att använda Oracle Database Exadata Cloud service i OCI. När du använder Oracle Database Exadata Cloud Service distribueras databasen i två undernät. Du kan också konfigurera Oracle Database i virtuella datorer i OCI i två tillgänglighets domäner med Oracle data Guard.


### <a name="identity-tier"></a>Identitets nivå

Identitets nivån innehåller den EBS-kontrollerade virtuella datorn. Med EBS-kontrollerare kan du synkronisera identiteter från Oracle Identity Cloud Service (IDCS) och Azure AD. EBS-kontrollerare krävs eftersom EBS inte stöder enkla inloggnings protokoll som SAML 2,0 eller OpenID Connect. EBS-kontrollen använder OpenID Connect-token (som genereras av IDCS), validerar den och skapar sedan en session för användaren i EBS. 

Även om den här arkitekturen visar IDCS-integrering, kan Azure AD Unified Access och enkel inloggning också aktive ras med Oracle Access Manager med Oracle Internet Directory eller Oracle Unified Directory. Mer information finns i fakta bladet om att [distribuera Oracle-EBS med IDCS-integrering](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) eller [distribuera Oracle-EBS med OAM-integrering](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

För hög tillgänglighet rekommenderar vi att du distribuerar redundanta servrar i EBS-kontrollen över flera tillgänglighets zoner med en belastningsutjämnare framför den.

När din infrastruktur har kon figurer ATS kan du installera E-Business Suite genom att följa installations guiden från Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards-EnterpriseOne

Oracle: s JD Edwards-EnterpriseOne är en integrerad program Svit med omfattande företags program för resurs planering. Det är ett program med flera nivåer som kan konfigureras med antingen en Oracle-eller SQL Server databas server del. I det här avsnittet beskrivs information om hur du distribuerar JD Edwards-EnterpriseOne med en Oracle Database backend-server antingen i OCI eller i Azure.

I följande rekommenderade arkitektur (bild 3) distribueras administrations-, presentations-och mellan nivåer till det virtuella nätverket i Azure. Databasen distribueras i ett virtuellt moln nätverk i OCI.

Precis som med E-Business Suite kan du konfigurera en valfri skydds-nivå för säkra administrativa funktioner. Använd skydds VM-värden som en hopp Server för att komma åt programmet och databas instanserna.

![JD Edwards EnterpriseOne över moln arkitektur](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Bild 3: JD Edwards EnterpriseOne Cross-Cloud Architecture*

I den här arkitekturen är det virtuella nätverket i Azure anslutet till det virtuella moln nätverket i OCI med hjälp av sammanlänkningen mellan moln. Program nivån har kon figurer ATS i Azure, medan databasen har kon figurer ATS i OCI. Vi rekommenderar att du distribuerar varje komponent till sitt eget undernät med nätverks säkerhets grupper för att endast tillåta trafik från specifika undernät på specifika portar.

Arkitekturen kan också anpassas för distribution helt och hållet i Azure med hög tillgängliga Oracle-databaser som kon figurer ATS med Oracle data Guard i två tillgänglighets zoner i en region. Följande diagram (bild 4) är ett exempel på detta arkitektur mönster:

![JD Edwards EnterpriseOne Azure-arkitektur](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Bild 4: JD Edwards EnterpriseOne Azure-arkitektur*

I följande avsnitt beskrivs de olika komponenterna på hög nivå.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Administrativ nivå

Som namnet antyder används den här nivån för administrativa uppgifter. Du kan dela ut ett separat undernät för den administrativa nivån. Tjänsterna och servrarna på den här nivån används främst för installation och administration av programmet. Därför räcker det att enskilda instanser av dessa servrar. Det krävs inga redundanta instanser för att programmet ska bli hög tillgängligt.

Komponenterna i den här nivån är följande:
    
 - **Etablerings Server** – den här servern används för distribution från slut punkt till slut punkt för de olika komponenterna i programmet. Den kommunicerar med instanserna på de andra nivåerna, inklusive instanserna på databas nivån, över Port 22. Den är värd för Serverhanteraren-konsolen för JD Edwards EnterpriseOne.
 - **Distributions Server** – den här servern krävs främst för att installera JD Edwards EnterpriseOne. Under installations processen fungerar den här servern som den centrala lagrings platsen för nödvändiga filer och installations paket. Program varan distribueras eller distribueras till andra servrar och klienter från den här servern.
 - **Utvecklings klient** – den här servern innehåller komponenter som körs i en webbläsare samt inbyggda program.

### <a name="presentation-tier"></a>Presentationsnivå

Den här nivån innehåller olika komponenter som Application Interface Services (AIS), Application Development Framework (ADF) och Java-programservrar (JAS). Servrarna i den här nivån kommunicerar med servrarna på mellan nivå. De är uppkopplade av en belastningsutjämnare som dirigerar trafik till den nödvändiga servern baserat på port numret och URL: en som trafiken tas emot på. Vi rekommenderar att du distribuerar flera instanser av varje server typ för hög tillgänglighet.

Följande är komponenterna på den här nivån:
    
- **Application Interface Services (AIS)** – AIS-servern tillhandahåller kommunikations gränssnittet mellan JD Edwards EnterpriseOne Mobile Enterprise Applications och JD Edwards EnterpriseOne.
- **Java-Programserver (JAS)** – Jas tar emot begär Anden från belastningsutjämnaren och skickar dem till mellan nivå för att köra komplicerade uppgifter. JAS har möjlighet att köra en enkel affärs logik.
- **Bi Publisher-Server (BIP)** – den här servern visar rapporter baserade på data som samlats in av JD-Edwards EnterpriseOne-programmet. Du kan utforma och styra hur rapporten visar data baserat på olika mallar.
- **Business Services-server (BSS)** – BSS möjliggör informations utbyte och samverkan med andra Oracle-program.
- **Real tids händelse Server (RTE)** – med hjälp av RTE-servern kan du konfigurera meddelanden till externa system om transaktioner som inträffar i JDE EnterpriseOne-systemet. Den använder en prenumerations modell och gör det möjligt för tredje parts system att prenumerera på händelser. För att belastningsutjämna begär anden till båda RTE-servrarna, se till att servrarna finns i ett kluster.
- **ADF-Server (Application Development Framework)** – ADF-servern används för att köra JD Edwards EnterpriseOne-program som har utvecklats med Oracle ADF. Detta distribueras på en Oracle WebLogic-Server med ADF-körning.

### <a name="middle-tier"></a>Mellan nivå

Mellan nivån innehåller logik servern och batch-servern. I det här fallet är båda servrarna installerade på samma virtuella dator. För produktions scenarier rekommenderar vi dock att du distribuerar Logic Server och batch server på separata servrar. Flera servrar distribueras på mellan nivå över två tillgänglighets zoner för högre tillgänglighet. En Azure-belastningsutjämnare bör skapas och dessa servrar bör placeras i sin backend-pool för att säkerställa att båda servrarna är aktiva och bearbetar begär Anden.

Servrarna på mellan nivå får förfrågningar från servrarna i presentations nivån och den offentliga belastningsutjämnaren. Regler för nätverks säkerhets grupper måste konfigureras för att neka trafik från någon annan adress än presentations nivå under nätet och belastningsutjämnaren. En NSG-regel kan också konfigureras för att tillåta trafik på port 22 från skydds-värden i hanterings syfte. Du kanske kan använda den offentliga belastningsutjämnaren för att belastningsutjämna begär Anden mellan de virtuella datorerna på mellan nivå.

Följande två komponenter finns på mellan nivån:

- **Logic Server** – innehåller affärs logik eller affärs funktioner.
- **Batch Server** – används för batchbearbetning

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Oracle: s Server-programsvit innehåller program vara för personal och ekonomisk hantering. Programsviten är flera nivåer och program inkluderar HRMS (personal Management Systems), hantering av kund relationer (CRM), finanser och hantering av leverans kedjor (FSCM) och Enterprise Performance Management (EPM).

Vi rekommenderar att varje nivå i program varu paketet distribueras i sitt eget undernät. En Oracle-databas eller Microsoft SQL Server krävs som backend-databas för programmet. Det här avsnittet innehåller information om hur du distribuerar en libmed en Oracle Database-Server del.

Följande är en kanonisk arkitektur för att distribuera The libapplication Suite i en plattforms oberoende arkitektur (bild 5).

![Arkitektur över hela molnet](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Figur 5: arkitektur över hela molnet*

I den här exempel arkitekturen är det virtuella nätverket i Azure anslutet till det virtuella moln nätverket i OCI med hjälp av sammanlänkningen mellan moln. Program nivån har kon figurer ATS i Azure, medan databasen har kon figurer ATS i OCI. Vi rekommenderar att du distribuerar varje komponent till sitt eget undernät med nätverks säkerhets grupper för att endast tillåta trafik från specifika undernät på specifika portar.

Arkitekturen kan också anpassas för distribution helt och hållet i Azure med hög tillgängliga Oracle-databaser som kon figurer ATS med Oracle data Guard i två tillgänglighets zoner i en region. Följande diagram (bild 6) är ett exempel på detta arkitektur mönster:

![Enpresterande Azure-arkitektur](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Bild 6: enpresterande Azure-arkitektur*

I följande avsnitt beskrivs de olika komponenterna på hög nivå.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Programnivå

Program nivån innehåller instanser av de fjärrinstallationsservrar-program servrar, webb servrar för den elastiska sökningen och den andra processen. En Azure Load Balancer har kon figurer ATS för att acceptera begär Anden från användare som dirigeras till rätt server på program nivå.

För hög tillgänglighet bör du överväga att konfigurera redundanta instanser av varje server på program nivå i olika tillgänglighets zoner. Azure Load Balancer kan konfigureras med flera Server dels pooler för att dirigera varje begäran till rätt server.

### <a name="peopletools-client"></a>PeopleTools-klient

PeopleTools-klienten används för att utföra administrations aktiviteter, till exempel utveckling, migrering och uppgradering. Eftersom PeopleTools-klienten inte krävs för att uppnå hög tillgänglighet för ditt program behövs inte redundanta servrar av PeopleTools-klienten.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Nästa steg

Använd [terraform-skript](https://github.com/microsoft/azure-oracle) för att konfigurera Oracle-appar i Azure och upprätta anslutningar mellan moln med OCI.

Mer information och dokumentation om OCI finns i dokumentationen till [Oracle-molnet](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
