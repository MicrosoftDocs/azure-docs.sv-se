---
title: Utvärdera ett stort antal fysiska servrar för migrering till Azure med Azure Migrate | Microsoft Docs
description: Beskriver hur du bedömer ett stort antal fysiska servrar för migrering till Azure med hjälp av tjänsten Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 01/19/2020
ms.openlocfilehash: 232475c50ab56fe6fb7a39a3497a8de3947fe851
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780318"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Utvärdera ett stort antal fysiska servrar för migrering till Azure

Den här artikeln beskriver hur du bedömer ett stort antal lokala fysiska servrar för migrering till Azure med hjälp av verktyget Azure Migrate identifiering och bedömning.

[Azure Migrate](migrate-services-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV). 


I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Planera för bedömning i stor skala.
> * Konfigurera Azure-behörigheter och Förbered fysiska servrar för utvärdering.
> * Skapa ett Azure Migrate projekt och skapa en utvärdering.
> * Granska utvärderingen när du planerar för migrering.


> [!NOTE]
> Om du vill testa ett koncept koncept för att utvärdera ett par servrar innan du bedömer i skala följer du våra [själv studie kurser](./tutorial-discover-physical.md).

## <a name="plan-for-assessment"></a>Planera för utvärdering

När du planerar för utvärdering av ett stort antal fysiska servrar finns det några saker att tänka på:

- **Planera Azure Migrate projekt**: ta reda på hur du distribuerar Azure Migrate-projekt. Om dina data Center till exempel finns i olika geografiska områden, eller om du behöver lagra identifierings-, utvärderings-eller migrerings-relaterade metadata i en annan geografi, kan du behöva flera projekt.
- **Plan utrustning**: Azure Migrate använder en lokal Azure Migrate-enhet som distribueras på en Windows-Server för att kontinuerligt identifiera servrar för utvärdering och migrering. Enheten övervakar miljö ändringar, till exempel att lägga till servrar, diskar eller nätverkskort. Den skickar även metadata-och prestanda information om dem till Azure. Du måste ta reda på hur många enheter som ska distribueras.


## <a name="planning-limits"></a>Planerings gränser
 
Använd de gränser som sammanfattas i den här tabellen för planering.

**Planering** | **Gränser**
--- | --- 
**Azure Migrate projekt** | Utvärdera upp till 35 000 servrar i ett projekt.
**Azure Migrate-installation** | En apparat kan identifiera upp till 1000 servrar.<br/> En installation kan bara associeras med ett enda Azure Migrate-projekt.<br/> Ett valfritt antal enheter kan associeras med ett enda Azure Migrate-projekt. <br/><br/> 
**Grupper** | Du kan lägga till upp till 35 000-servrar i en enda grupp.
**Azure Migrate utvärdering** | Du kan utvärdera upp till 35 000-servrar i en enda utvärdering.


## <a name="other-planning-considerations"></a>Andra planerings överväganden

- Om du vill starta identifiering från installationen måste du välja varje fysisk server. 

## <a name="prepare-for-assessment"></a>Förbered för utvärdering

Förbereda Azure och fysiska servrar för identifierings-och utvärderings verktyg:  

1. Kontrol lera [krav och begränsningar för den fysiska serverns support](migrate-support-matrix-physical.md).
2. Konfigurera behörigheter för ditt Azure-konto för att interagera med Azure Migrate.
3. Förbered de fysiska servrarna.

Följ anvisningarna i [den här självstudien](./tutorial-discover-physical.md) för att konfigurera de här inställningarna.

## <a name="create-a-project"></a>Skapa ett projekt

I enlighet med dina planerings krav gör du följande:

1. Skapa ett Azure Migrate-projekt.
2. Lägg till verktyget för identifiering och utvärdering av Azure Migrate i projekten.

[Läs mer](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Skapa och granska en utvärdering

1. Skapa utvärderingar för fysiska servrar.
1. Granska utvärderingarna i förberedelser inför planering av migrering.

[Lär dig mer](tutorial-assess-physical.md) om att skapa och granska utvärderingar.
    

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller följande avsnitt:
 
> [!div class="checklist"] 
> * Planerad för att skala Azure Migrate bedömningar för fysiska servrar.
> * För beredde Azure och fysiska servrar för utvärdering.
> * Ett Azure Migrate-projekt har skapats och utvärderingen har körts.
> * Granskade utvärderingar inför migrering.

Nu kan du [lära dig hur](concepts-assessment-calculation.md) utvärderingar beräknas och hur du [ändrar utvärderingen](how-to-modify-assessment.md).