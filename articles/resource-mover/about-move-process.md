---
title: Om flyttnings processen i Azure Resource-arbetskraft
description: Lär dig mer om processen för att flytta resurser över flera regioner med Azure Resource överflyttning
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 02/01/2021
ms.author: raynew
ms.openlocfilehash: 6eba77e1af2cd1a038f2ebd61a85ccd03b9a0741
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074462"
---
# <a name="about-the-move-process"></a>Om flytt processen

Med [Azure Resource](overview.md) -arbetsstationen kan du flytta Azure-resurser i Azure-regioner. Den här artikeln sammanfattar de komponenter som används av resurs förflyttning och beskriver flytt processen. 


## <a name="components"></a>Komponenter

Dessa komponenter används vid flyttning av region.

**Komponent** | **Information**
--- | ---
**Resurs förflyttning** |  Resurs förflyttningen koordineras med [Azure-resurstjänster](../azure-resource-manager/management/resource-providers-and-types.md) för att dirigera flytten av resurser mellan regioner. Resurs förflyttningen analyserar resurs beroenden och underhåller och hanterar resursens tillstånd under flyttnings processen. 
**Flytta samling** |  En flyttnings samling är ett [Azure Resource Manager](../azure-resource-manager/management/overview.md) -objekt.<br/><br/> Flyttnings samlingen skapas under region flyttnings processen för varje parad kombination av käll-och mål regioner i en prenumeration. Samlingen innehåller metadata och konfigurations information om de resurser som du vill flytta.<br/><br/>Resurser som läggs till i en flyttnings samling måste finnas i samma prenumeration, men kan finnas i olika resurs grupper. 
**Flytta resurs** | När du lägger till en resurs i en flyttnings samling spåras den av resurs förflyttning som en flytt resurs.<br/><br/> Resurs förflyttningen innehåller information för alla flytta resurser i flytt samlingen och har en 1-till-en-relation mellan käll-och mål resursen. 
**Beroenden** | Resurs förflyttningen verifierar resurser som du lägger till i en samling och kontrollerar om resurser har några beroenden som inte finns i flyttnings samlingen.<br/><br/> När du har identifierat beroenden för en resurs kan du antingen lägga till dem i flyttnings samlingen och flytta dem också, eller så kan du välja alternativa befintliga resurser i mål regionen. Alla beroenden måste lösas innan du börjar flytta. 



## <a name="move-region-process"></a>Flytta regions process 

![Diagram över flytt stegen](./media/about-move-process/move-steps.png)

Varje flyttnings resurs går igenom de sammanfattade stegen.

| **Steg** | **Information** | **Tillstånd/problem** |
| --- | --- | --- |
| **Steg 1: Välj resurser** | Välj en resurs. Resursen läggs till i flyttnings samlingen. | Resurs tillstånd flyttas för att *förbereda väntande*.<br/><br/> Om resursen har beroenden anger *Verifiera* beroende att du måste lägga till beroende resurser i flytt samlingen. |
| **Steg 2: verifiera beroenden** | Starta validerings processen.<br/><br/> Om verifieringen visar att beroende resurser väntar, lägger du till dem i flyttnings samlingen. <br/><br/> Lägg till alla beroende resurser, även om du inte vill flytta dem. Du kan senare ange att de resurser som du flyttar ska använda olika resurser i mål regionen i stället.<br/><br/> Du validerar igen tills det inte finns några utestående beroenden. | När alla beroenden har lagts till och verifieringen lyckas, flyttas resurs status för att *förbereda väntande* utan problem. |
| **Steg 3: Förbered** | Starta förberedelse processen. Förberedelse stegen är beroende av de resurser som du flyttar:<br/><br/> - **Tillstånds lösa resurser**: endast konfigurations information har tillstånds lösa resurser. Dessa resurser behöver inte kontinuerlig replikering av data för att flytta dem. Exempel på detta är Azure Virtual Networks (virtuella nätverk), nätverkskort, belastningsutjämnare och nätverks säkerhets grupper. För den här typen av resurs genererar förberedelse processen en Azure Resource Manager-mall.<br/><br/> - **Tillstånds känsliga resurser**: tillstånds känsliga resurser har både konfigurations information och data som behöver flyttas. Exempel är virtuella Azure-datorer och Azure SQL-databaser. Förberedelse processen skiljer sig för varje resurs. Det kan omfatta replikering av käll resursen till mål regionen. | Inaktiverar flyttar resurs tillstånd till *förberedelser*.<br/><br/> När förberedelsen är klar flyttar resurs tillstånd till att *initiera flytten*, utan problem.<br/><br/> En misslyckad process flyttar status till *förberedelse misslyckades*. |
| **Steg 4: initiera flytta** | Starta flyttnings processen. Flyttnings metoden beror på resurs typen:<br/><br/> - **Tillstånds lös**: vanligt vis distribuerar flyttnings processen en importerad mall i mål regionen för tillstånds lösa resurser. Mallen baseras på käll resurs inställningarna och alla manuella ändringar som du gör i mål inställningarna.<br/><br/> - **Tillstånds känslig**: för tillstånds känsliga resurser kan flyttnings processen innebära att du skapar resursen, eller aktiverar en kopia i mål regionen.<br/><br/>  För endast tillstånds känsliga resurser kan det leda till avbrott i käll resurserna om du initierar en flytt. Till exempel virtuella datorer och SQL. | Om du slår på flytta flyttas statusen för att *initiera pågående flyttning*.<br/><br/> En lyckad initiering flyttar resurs tillstånd till *genomförd flytt*, utan problem. <br/><br/> En misslyckad flyttnings process flyttar status för att *initiera flytten misslyckades*. |
| **Steg 5 alternativ 1: ta bort flytt** | Efter den första flyttningen kan du välja om du vill gå vidare med fullständig flytt. Om du inte gör det kan du ta bort flytten och resurs förflyttningen tar bort resurserna som har skapats i målet. Replikeringen för tillstånds känsliga resurser fortsätter efter den ignorerande processen. Det här alternativet är användbart för testning. | Borttagning av resurser flyttar status till *Ignorera pågår*.<br/><br/> Status för borttagningen har slutförts för att *initiera väntande flyttning* utan problem.<br/><br/> Ett misslyckat flyttnings tillstånd för att *ta bort flyttning misslyckades*. |
| **Steg 5 alternativ 2: bekräfta flyttning** | När den första flytten är klar, om du vill gå vidare med en fullständig flytt, verifierar du resurserna i mål regionen och när du är klar kan du spara flyttningen.<br/><br/> För endast tillstånds känsliga resurser kan commit leda till att käll resurser som virtuella datorer eller SQL blir otillgängliga. | Om du genomför flyttningen flyttas resurs tillstånd till * genomför flytt pågår * *.<br/><br/> När åtgärden har genomförts visar resurs statusen *genomför flyttningen slutförd*, utan problem.<br/><br/> Ett misslyckat bekräftelse tillstånd att *Bekräfta flyttning misslyckades*. |
| **Steg 6: ta bort källa** | När du har allokerat flytten och verifierat resurser i mål regionen kan du ta bort käll resursen. | Efter commit flyttas ett resurs tillstånd till *borttagnings källan väntar*. Du kan sedan välja käll resursen och ta bort den.<br/><br/> -Det går inte att ta bort resurser som *väntar på borttagnings källa* . <br/><br/>Att ta bort en resurs grupp eller SQL Server i resurs förflyttnings portalen stöds inte. Dessa resurser kan bara tas bort från sidan resurs egenskaper. |


## <a name="move-region-states"></a>Flytta regions tillstånd

Flyttnings processen har ett antal tillstånd och problem som kan uppstå under varje tillstånd. Dessa sammanfattas i flödesschemat.

![Flödes schema för möjliga tillstånd och problem](./media/about-move-process/process.png)


### <a name="dependency-analysis"></a>Beroende analys

När du går igenom flytt processen kan du bli ombedd att verifiera beroenden om:
- En resurs använder beroende resurser som inte ingår i flytt samlingen.
- En beroende resurs i flyttnings samlingen har sina egna beroenden som inte finns i flyttnings samlingen.
- Du ändrade mål inställningarna för resursen och du måste verifiera om beroenden.


### <a name="remove-resources"></a>Ta bort resurser

Om du inte vill flytta en resurs kan du ta bort den från flyttnings samlingen. I allmänhet tas resursen bort från samlingen, tillsammans med eventuella associerade åtgärder eller objekt, till exempel replikering eller lagrade mallar. Exakt vad som händer när du tar bort en resurs beror på resurs typen och resursens tillstånd när du tar bort den. [Läs mer](remove-move-resources.md).

## <a name="move-impact"></a>Flytta effekt

I tabellen sammanfattas vad som påverkas när du flyttar mellan regioner.

**Beteende** | **Mellan regioner**
--- | --- | --- 
**Data** | Resurs data och metadata flyttas.<br/><br/> Metadata lagras tillfälligt för att spåra status för resurs beroenden och åtgärder.
**Resurs** | Käll resursen förblir orörd för att säkerställa att apparna fortsätter att fungera och kan tas bort efter flytten.<br/><br/> En resurs skapas i mål regionen.
**Flytta process** | Multi-Step-process kräver manuell ingrepp och övervakning.
**Test** | Att testa flytten är viktigt eftersom apparna bör fortsätta att fungera som förväntat i mål regionen efter flytten.
**Driftstopp** |  Ingen data förlust förväntades, men vissa avbrott för att flytta resurser.



## <a name="next-steps"></a>Nästa steg

[Flytta](tutorial-move-region-virtual-machines.md) Virtuella Azure-datorer till en annan region.
[Flytta](tutorial-move-region-sql.md) Azure SQL-resurser till en annan region.
