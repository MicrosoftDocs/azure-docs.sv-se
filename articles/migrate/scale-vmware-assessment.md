---
title: Utvärdera ett stort antal virtuella VMware-datorer för migrering till Azure med Azure Migrate
description: Beskriver hur du bedömer ett stort antal virtuella VMware-datorer för migrering till Azure med hjälp av tjänsten Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 206df399736dbd3b5d1d52531a249bbd37646514
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753679"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Utvärdera ett stort antal virtuella VMware-datorer för migrering till Azure


Den här artikeln beskriver hur du bedömer stora tal (1000-35000) för lokala virtuella VMware-datorer för migrering till Azure med hjälp av verktyget för att utvärdera Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV). 

I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Planera för bedömning i stor skala.
> * Konfigurera Azure-behörigheter och Förbered VMware för utvärdering.
> * Skapa ett Azure Migrate projekt och skapa en utvärdering.
> * Granska utvärderingen när du planerar för migrering.


> [!NOTE]
> Om du vill testa ett koncept koncept för att utvärdera ett antal virtuella datorer innan du bedömer i skala följer du våra [själv studie kurser](./tutorial-discover-vmware.md)

## <a name="plan-for-assessment"></a>Planera för utvärdering

När du planerar för utvärdering av ett stort antal virtuella VMware-datorer finns det några saker att tänka på:

- **Planera Azure Migrate projekt**: ta reda på hur du distribuerar Azure Migrate-projekt. Om dina data Center till exempel finns i olika geografiska områden, eller om du behöver lagra identifierings-, utvärderings-eller migrerings-relaterade metadata i en annan geografi, kan du behöva flera projekt. 
- **Plan utrustning**: Azure Migrate använder en lokal Azure Migrate-installation som distribueras som en virtuell VMware-dator för att kontinuerligt identifiera virtuella datorer. Enheten övervakar miljö ändringar, till exempel att lägga till virtuella datorer, diskar eller nätverkskort. Den skickar även metadata-och prestanda information om dem till Azure. Du måste ta reda på hur många apparater du behöver distribuera.
- **Planera konton för identifiering**: Azure Migrates enheten använder ett konto med åtkomst till vCenter Server för att identifiera virtuella datorer för utvärdering och migrering. Om du upptäcker fler än 10 000 virtuella datorer måste du konfigurera flera konton eftersom det krävs att det inte finns några överlappande mellan de virtuella datorerna som identifierats från två enheter i ett projekt. 

> [!NOTE]
> Om du konfigurerar flera anordningar ser du till att det inte finns några överlappande mellan de virtuella datorerna på de vCenter-konton som angetts. En identifiering med sådan överlappning är ett scenario som inte stöds. Om en virtuell dator identifieras av fler än en installation resulterar detta i dubbletter i identifieringen och i problem med att aktivera replikering för den virtuella datorn med hjälp av Azure Portal i Server-migreringen.

## <a name="planning-limits"></a>Planerings gränser
 
Använd de gränser som sammanfattas i den här tabellen för planering.

**Planering** | **Gränser**
--- | --- 
**Azure Migrate projekt** | Utvärdera upp till 35 000 virtuella datorer i ett projekt.
**Azure Migrate-installation** | En apparat kan identifiera upp till 10 000 virtuella datorer på en vCenter Server.<br/> En installation kan bara ansluta till en enda vCenter Server.<br/> En installation kan bara associeras med ett enda Azure Migrate-projekt.<br/>  Ett valfritt antal enheter kan associeras med ett enda Azure Migrate-projekt. <br/><br/> 
**Grupper** | Du kan lägga till upp till 35 000 virtuella datorer i en enda grupp.
**Azure Migrate utvärdering** | Du kan utvärdera upp till 35 000 virtuella datorer i en enda utvärdering.

Här är några exempel på distributioner med dessa begränsningar:


**vCenter-Server** | **Virtuella datorer på servern** | **Rekommendation** | **Åtgärd**
---|---|---|---
En | < 10 000 | Ett Azure Migrate projekt.<br/> En-apparat.<br/> Ett vCenter-konto för identifiering. | Konfigurera installationen, Anslut till vCenter Server med ett konto.
En | > 10 000 | Ett Azure Migrate projekt.<br/> Flera enheter.<br/> Flera vCenter-konton. | Konfigurera installations programmet för varje 10 000-VM.<br/><br/> Konfigurera vCenter-konton och dividera lagret för att begränsa åtkomsten för ett konto till färre än 10 000 virtuella datorer.<br/> Anslut varje installation till vCenter-servern med ett konto.<br/> Du kan analysera beroenden mellan datorer som har identifierats med olika enheter. <br/> <br/> Se till att det inte överlappar de virtuella datorerna på de vCenter-konton som angetts. En identifiering med sådan överlappning är ett scenario som inte stöds. Om en virtuell dator identifieras av fler än en installation resulterar detta i en dubblett i identifieringen och i problem med att aktivera replikering för den virtuella datorn med hjälp av Azure Portal i Server-migreringen.
Flera | < 10 000 |  Ett Azure Migrate projekt.<br/> Flera enheter.<br/> Ett vCenter-konto för identifiering. | Konfigurera enheter, Anslut till vCenter Server med ett konto.<br/> Du kan analysera beroenden mellan datorer som har identifierats med olika enheter.
Flera | > 10 000 | Ett Azure Migrate projekt.<br/> Flera enheter.<br/> Flera vCenter-konton. | Om vCenter Server identifiering < 10 000 virtuella datorer måste du konfigurera en installation för varje vCenter Server.<br/><br/> Om vCenter Server identifiering > 10 000 virtuella datorer måste du konfigurera en installation för varje 10 000-dator.<br/> Konfigurera vCenter-konton och dividera lagret för att begränsa åtkomsten för ett konto till färre än 10 000 virtuella datorer.<br/> Anslut varje installation till vCenter-servern med ett konto.<br/> Du kan analysera beroenden mellan datorer som har identifierats med olika enheter. <br/><br/> Se till att det inte överlappar de virtuella datorerna på de vCenter-konton som angetts. En identifiering med sådan överlappning är ett scenario som inte stöds. Om en virtuell dator identifieras av fler än en installation resulterar detta i en dubblett i identifieringen och i problem med att aktivera replikering för den virtuella datorn med hjälp av Azure Portal i Server-migreringen.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Planera identifiering i en miljö med flera klienter

Om du planerar för en miljö med flera klienter kan du begränsa identifieringen på vCenter Server.

- Du kan ställa in identifierings omfånget för enheten på ett vCenter Server Data Center, kluster eller en mapp med kluster, värdar eller en mapp med värdar eller enskilda virtuella datorer.
- Om din miljö delas mellan klienter och du vill identifiera varje klient separat, kan du begränsa åtkomsten till det vCenter-konto som installeras av enheten. 
    - Du kanske vill omfånget med VM-mappar om klienterna delar värdar. Azure Migrate kan inte identifiera virtuella datorer om vCenter-kontot har åtkomst beviljad på den virtuella vCenter-mappens nivå. Om du vill begränsa identifieringen av VM-mappar kan du göra det genom att se till att vCenter-kontot har skrivskyddad åtkomst som tilldelats på en VM-nivå. [Läs mer](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Förbered för utvärdering

Förbered Azure och VMware för Server utvärdering. 

1. Kontrol lera [krav och begränsningar för VMware-support](migrate-support-matrix-vmware.md).
2. Konfigurera behörigheter för ditt Azure-konto för att interagera med Azure Migrate.
3. Förbered VMware för utvärdering.

Följ anvisningarna i [den här självstudien](./tutorial-discover-vmware.md) för att konfigurera de här inställningarna.


## <a name="create-a-project"></a>Skapa ett projekt

I enlighet med dina planerings krav gör du följande:

1. Skapa ett Azure Migrate projekt.
2. Lägg till verktyget Azure Migrate Server Assessment i projekten.

[Läs mer](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Skapa och granska en utvärdering

1. Skapa utvärderingar för virtuella VMware-datorer.
1. Granska utvärderingarna i förberedelser inför planering av migrering.


Följ anvisningarna i [den här självstudien](./tutorial-assess-vmware-azure-vm.md) för att konfigurera de här inställningarna.
    

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller följande avsnitt:
 
> [!div class="checklist"] 
> * Planerad för att skala Azure Migrate bedömningar för virtuella VMware-datorer
> * Förbereda Azure och VMware för utvärdering
> * Skapat ett Azure Migrate-projekt och körde utvärderingar
> * Granskade utvärderingar inför migrering.

Nu kan du [lära dig hur](concepts-assessment-calculation.md) utvärderingar beräknas och hur du [ändrar utvärderingen](how-to-modify-assessment.md).