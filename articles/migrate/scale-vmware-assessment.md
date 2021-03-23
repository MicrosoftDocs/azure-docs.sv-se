---
title: Utvärdera ett stort antal servrar i VMware-miljön för migrering till Azure med Azure Migrate
description: Beskriver hur du bedömer ett stort antal servrar i VMware-miljön för migrering till Azure med hjälp av tjänsten Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10b8aaeaa25e49140dbf6f31c064c7f823d23e31
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778261"
---
# <a name="assess-large-numbers-of-servers-in-vmware-environment-for-migration-to-azure"></a>Utvärdera ett stort antal servrar i VMware-miljön för migrering till Azure


Den här artikeln beskriver hur du bedömer stora tal (1000-35000) för lokala servrar i VMware-miljön för migrering till Azure med hjälp av verktyget för identifiering och utvärdering av Azure Migrate.

[Azure Migrate](migrate-services-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV). 

I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Planera för bedömning i stor skala.
> * Konfigurera Azure-behörigheter och Förbered VMware för utvärdering.
> * Skapa ett Azure Migrate projekt och skapa en utvärdering.
> * Granska utvärderingen när du planerar för migrering.


> [!NOTE]
> Om du vill testa ett koncept koncept för att utvärdera ett par servrar innan du bedömer i skala följer du våra [själv studie kurser](./tutorial-discover-vmware.md)

## <a name="plan-for-assessment"></a>Planera för utvärdering

När du planerar för utvärdering av ett stort antal servrar i VMware-miljön finns det några saker att tänka på:

- **Planera Azure Migrate projekt**: ta reda på hur du distribuerar Azure Migrate-projekt. Om dina data Center till exempel finns i olika geografiska områden, eller om du behöver lagra identifierings-, utvärderings-eller migrerings-relaterade metadata i en annan geografi, kan du behöva flera projekt. 
- **Plan utrustning**: Azure Migrate använder en lokal Azure Migrate-enhet som distribueras som en virtuell VMware-dator för att kontinuerligt identifiera servrar. Enheten övervakar miljö ändringar, till exempel att lägga till servrar, diskar eller nätverkskort. Den skickar även metadata-och prestanda information om dem till Azure. Du måste ta reda på hur många apparater du behöver distribuera.
- **Planera konton för identifiering**: Azure Migrates enheten använder ett konto med åtkomst till vCenter Server för att identifiera servrar för utvärdering och migrering. Om du upptäcker fler än 10 000 servrar måste du konfigurera flera konton eftersom det krävs att det inte sker några överlappande mellan servrar som identifieras från två enheter i ett projekt. 

> [!NOTE]
> Om du konfigurerar flera anordningar kontrollerar du att det inte finns några överlappande mellan servrarna på de vCenter-konton som angetts. En identifiering med sådan överlappning är ett scenario som inte stöds. Om en server identifieras av fler än en installation resulterar detta i dubbletter i identifiering och i problem med att aktivera replikering för servern med hjälp av Azure Portal i Server-migrering.

## <a name="planning-limits"></a>Planerings gränser
 
Använd de gränser som sammanfattas i den här tabellen för planering.

**Planering** | **Gränser**
--- | --- 
**Azure Migrate projekt** | Utvärdera upp till 35 000 servrar i ett projekt.
**Azure Migrate-installation** | En apparat kan identifiera upp till 10 000 servrar på en vCenter Server.<br/> En installation kan bara ansluta till en enda vCenter Server.<br/> En installation kan bara associeras med ett enda Azure Migrate-projekt.<br/>  Ett valfritt antal enheter kan associeras med ett enda Azure Migrate-projekt. <br/><br/> 
**Grupper** | Du kan lägga till upp till 35 000-servrar i en enda grupp.
**Azure Migrate utvärdering** | Du kan utvärdera upp till 35 000-servrar i en enda utvärdering.

Här är några exempel på distributioner med dessa begränsningar:


**vCenter-Server** | **Servrar på servern** | **Rekommendation** | **Åtgärd**
---|---|---|---
En | < 10 000 | Ett Azure Migrate projekt.<br/> En-apparat.<br/> Ett vCenter-konto för identifiering. | Konfigurera installationen, Anslut till vCenter Server med ett konto.
En | > 10 000 | Ett Azure Migrate projekt.<br/> Flera enheter.<br/> Flera vCenter-konton. | Konfigurera en installation för varje 10 000-Server.<br/><br/> Konfigurera vCenter-konton och dividera lagret för att begränsa åtkomsten för ett konto till färre än 10 000 servrar.<br/> Anslut varje installation till vCenter-servern med ett konto.<br/> Du kan analysera beroenden mellan servrar som identifieras med olika enheter. <br/> <br/> Se till att det inte finns några överlappande mellan servrarna på de vCenter-konton som angetts. En identifiering med sådan överlappning är ett scenario som inte stöds. Om en server identifieras av fler än en installation resulterar detta i en dubblett i identifieringen och i problem med att aktivera replikering för servern med hjälp av Azure Portal i Server-migrering.
Flera | < 10 000 |  Ett Azure Migrate projekt.<br/> Flera enheter.<br/> Ett vCenter-konto för identifiering. | Konfigurera enheter, Anslut till vCenter Server med ett konto.<br/> Du kan analysera beroenden mellan servrar som identifieras med olika enheter.
Flera | > 10 000 | Ett Azure Migrate projekt.<br/> Flera enheter.<br/> Flera vCenter-konton. | Om vCenter Server identifiering < 10 000-servrar konfigurerar du en installation för varje vCenter Server.<br/><br/> Om vCenter Server identifiering > 10 000-servrar konfigurerar du en installation för varje 10 000-Server.<br/> Konfigurera vCenter-konton och dividera lagret för att begränsa åtkomsten för ett konto till färre än 10 000 servrar.<br/> Anslut varje installation till vCenter-servern med ett konto.<br/> Du kan analysera beroenden mellan servrar som identifieras med olika enheter. <br/><br/> Se till att det inte finns några överlappande mellan servrarna på de vCenter-konton som angetts. En identifiering med sådan överlappning är ett scenario som inte stöds. Om en server identifieras av fler än en installation resulterar detta i en dubblett i identifieringen och i problem med att aktivera replikering för servern med hjälp av Azure Portal i Server-migrering.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Planera identifiering i en miljö med flera klienter

Om du planerar för en miljö med flera klienter kan du begränsa identifieringen på vCenter Server.

- Du kan ställa in identifierings omfånget för enheten på ett vCenter Server Data Center, kluster eller en mapp med kluster, värdar eller en mapp med värdar eller enskilda servrar.
- Om din miljö delas mellan klienter och du vill identifiera varje klient separat, kan du begränsa åtkomsten till det vCenter-konto som installeras av enheten. 
    - Du kanske vill omfånget med VM-mappar om klienterna delar värdar. Azure Migrate kan inte identifiera servrar om vCenter-kontot har åtkomst som beviljats på katalog nivå för vCenter VM. Om du vill begränsa identifieringen av VM-mappar kan du göra det genom att se till att vCenter-kontot har skrivskyddad åtkomst som tilldelas på en server nivå. [Läs mer](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Förbered för utvärdering

Förbered Azure och VMware för identifierings-och utvärderings verktyg:

1. Kontrol lera [krav och begränsningar för VMware-support](migrate-support-matrix-vmware.md).
2. Konfigurera behörigheter för ditt Azure-konto för att interagera med Azure Migrate.
3. Förbered VMware för utvärdering.

Följ anvisningarna i [den här självstudien](./tutorial-discover-vmware.md) för att konfigurera de här inställningarna.


## <a name="create-a-project"></a>Skapa ett projekt

I enlighet med dina planerings krav gör du följande:

1. Skapa ett Azure Migrate projekt.
2. Lägg till verktyget för identifiering och utvärdering av Azure Migrate i projekten.

[Läs mer](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Skapa och granska en utvärdering

1. Skapa utvärderingar för servrar i VMware-miljön.
1. Granska utvärderingarna i förberedelser inför planering av migrering.


Följ anvisningarna i [den här självstudien](./tutorial-assess-vmware-azure-vm.md) för att konfigurera de här inställningarna.
    

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller följande avsnitt:
 
> [!div class="checklist"] 
> * Planerad för att skala Azure Migrate bedömningar för servrar i VMware-miljön
> * Förbereda Azure och VMware för utvärdering
> * Skapat ett Azure Migrate-projekt och körde utvärderingar
> * Granskade utvärderingar inför migrering.

Nu kan du [lära dig hur](concepts-assessment-calculation.md) utvärderingar beräknas och hur du [ändrar utvärderingen](how-to-modify-assessment.md).