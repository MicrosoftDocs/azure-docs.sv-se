---
title: Utvärdera ett stort antal servrar i Hyper-V-miljön för migrering till Azure med Azure Migrate | Microsoft Docs
description: Beskriver hur du bedömer ett stort antal servrar i Hyper-V-miljön för migrering till Azure med hjälp av tjänsten Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 495e1bf415146471fcccad34e2879398e12e1769
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780301"
---
# <a name="assess-large-numbers-of-servers-in-hyper-v-environment-for-migration-to-azure"></a>Utvärdera ett stort antal servrar i Hyper-V-miljön för migrering till Azure

Den här artikeln beskriver hur du bedömer ett stort antal lokala servrar i Hyper-V-miljön för migrering till Azure med hjälp av verktyget Azure Migrate identifiering och bedömning.

[Azure Migrate](migrate-services-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV). 


I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Planera för bedömning i stor skala.
> * Konfigurera Azure-behörigheter och Förbered Hyper-V för utvärdering.
> * Skapa ett Azure Migrate projekt och skapa en utvärdering.
> * Granska utvärderingen när du planerar för migrering.


> [!NOTE]
> Om du vill testa ett koncept koncept för att utvärdera ett par servrar innan du bedömer i skala följer du våra [själv studie kurser](./tutorial-discover-hyper-v.md)

## <a name="plan-for-assessment"></a>Planera för utvärdering

När du planerar för utvärdering av ett stort antal servrar i Hyper-V-miljön finns det några saker att tänka på:

- **Planera Azure Migrate projekt**: ta reda på hur du distribuerar Azure Migrate-projekt. Om dina data Center till exempel finns i olika geografiska områden, eller om du behöver lagra identifierings-, utvärderings-eller migrerings-relaterade metadata i en annan geografi, kan du behöva flera projekt.
- **Plan utrustning**: Azure Migrate använder en lokal Azure Migrate-enhet som distribueras som en virtuell Hyper-V-dator för att kontinuerligt identifiera servrar för utvärdering och migrering. Enheten övervakar miljö ändringar, till exempel att lägga till servrar, diskar eller nätverkskort. Den skickar även metadata-och prestanda information om dem till Azure. Du måste ta reda på hur många enheter som ska distribueras.


## <a name="planning-limits"></a>Planerings gränser
 
Använd de gränser som sammanfattas i den här tabellen för planering.

**Planering** | **Gränser**
--- | --- 
**Azure Migrate projekt** | Utvärdera upp till 35 000 servrar i ett projekt.
**Azure Migrate-installation** | En apparat kan identifiera upp till 5000 servrar.<br/> En apparat kan ansluta till upp till 300 Hyper-V-värdar.<br/> En installation kan bara associeras med ett enda Azure Migrate-projekt.<br/> Ett valfritt antal enheter kan associeras med ett enda Azure Migrate-projekt. <br/><br/> 
**Grupper** | Du kan lägga till upp till 35 000-servrar i en enda grupp.
**Azure Migrate utvärdering** | Du kan utvärdera upp till 35 000-servrar i en enda utvärdering.



## <a name="other-planning-considerations"></a>Andra planerings överväganden

- Om du vill starta identifiering från installationen måste du välja varje Hyper-V-värd. 
- Om du kör en miljö med flera klienter kan du för närvarande inte identifiera servrar som tillhör en speciell klient. 

## <a name="prepare-for-assessment"></a>Förbered för utvärdering

Förbereda Azure och Hyper-V för identifierings-och utvärderings verktyg: 

1. Kontrol lera [kraven och begränsningarna för Hyper-V-support](migrate-support-matrix-hyper-v.md).
2. Konfigurera behörigheter för ditt Azure-konto för att interagera med Azure Migrate
3. Förbereda Hyper-V-värdar och-servrar

Följ anvisningarna i [den här självstudien](./tutorial-discover-hyper-v.md) för att konfigurera de här inställningarna.

## <a name="create-a-project"></a>Skapa ett projekt

I enlighet med dina planerings krav gör du följande:

1. Skapa ett Azure Migrate projekt.
2. Lägg till verktyget för identifiering och utvärdering av Azure Migrate i projekten.

[Läs mer](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Skapa och granska en utvärdering

1. Skapa utvärderingar för servrar i Hyper-V-miljön.
1. Granska utvärderingarna i förberedelser inför planering av migrering.

[Lär dig mer](tutorial-assess-hyper-v.md) om att skapa och granska utvärderingar.
    

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller följande avsnitt:
 
> [!div class="checklist"] 
> * Planerad för att skala Azure Migrate bedömningar för servrar i Hyper-V-miljön
> * Förbereda Azure och Hyper-V för utvärdering
> * Skapat ett Azure Migrate-projekt och körde utvärderingar
> * Granskade utvärderingar inför migrering.

Nu kan du [lära dig hur](concepts-assessment-calculation.md) utvärderingar beräknas och hur du [ändrar utvärderingar](how-to-modify-assessment.md)