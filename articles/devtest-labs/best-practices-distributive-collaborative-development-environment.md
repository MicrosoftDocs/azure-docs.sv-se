---
title: Distribuerad samarbets utveckling för Azure DevTest Labs resurser
description: Innehåller metod tips för att skapa en distribuerad och samarbets utvecklings miljö för att utveckla DevTest Labs-resurser.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1ef6d7aa7d3cfd4fcc64eaa45259684dfcb9ccee
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592372"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Metod tips för distribuerad och samverkande utveckling av Azure DevTest Labs resurser
Distribuerad samarbets utveckling gör att olika team eller personer kan utveckla och underhålla en kodbas. För att lyckas, är utvecklings processen beroende av möjligheten att skapa, dela och integrera information. Den här nyckel utvecklings principen kan användas i Azure DevTest Labs. Det finns flera typer av resurser i ett labb som ofta distribueras mellan olika labb i ett företag. De olika typerna av resurser fokuserar på två områden:

- Resurser som lagras internt i labbet (labbbaserade)
- Resurser som lagras i [externa databaser som är anslutna till labbet](devtest-lab-add-artifact-repo.md) (kod databas-baserad). 

I det här dokumentet beskrivs några metod tips som gör det möjligt att samar beta och distribuera över flera team samtidigt som du säkerställer anpassning och kvalitet på alla nivåer.

## <a name="lab-based-resources"></a>Labbbaserade resurser

### <a name="custom-virtual-machine-images"></a>Anpassade avbildningar av virtuella datorer
Du kan ha en gemensam källa med anpassade avbildningar som distribueras till labbet på en natt basis. Detaljerad information finns i [bild fabrik](image-factory-create.md).    

### <a name="formulas"></a>Formler
[Formler](devtest-lab-manage-formulas.md) är labbbaserade och har inte en distributions mekanism. Labb medlemmarna gör all utveckling av formler. 

## <a name="code-repository-based-resources"></a>Kod lagrings resurser
Det finns två olika funktioner som baseras på kod databaser, artefakter och miljöer. Den här artikeln går igenom funktionerna och hur du effektivt konfigurerar databaser och arbets flöden så att du kan anpassa tillgängliga artefakter och miljöer på organisations nivå eller grupp nivå.  Det här arbets flödet baseras på standard [strategin för käll kods kontroll grenar](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Viktiga begrepp
Käll informationen för artefakter innehåller metadata, skript. Käll informationen för miljöer omfattar metadata-och Resource Manager-mallar med stödfiler som PowerShell-skript, DSC-skript, zip-filer och så vidare.  

### <a name="repository-structure"></a>Databas struktur  
Den vanligaste konfigurationen för käll kods kontroll (SCC) är att skapa en struktur med flera nivåer för lagring av kod filer (Resource Manager-mallar, metadata och skript) som används i labbet. Mer specifikt kan du skapa olika lagrings platser för resurser som hanteras av de olika nivåerna i företaget:   

- Resurser i hela företaget.
- Affär senhet/avdelning – breda resurser
- Team-/regionsspecifika resurser.

Var och en av dessa nivåer länkar till en annan lagrings plats där huvud grenen måste vara av produktions kvaliteten. [Grenarna](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) i varje lagrings plats kan vara för utveckling av dessa resurser (artefakter eller mallar). Den här strukturen justeras bra med DevTest Labs eftersom du enkelt kan ansluta flera databaser och flera grenar samtidigt till organisationens labb. Databas namnet ingår i användar gränssnittet (UI) för att undvika förvirring när det finns identiska namn, beskrivning och utgivare.
     
Följande diagram visar två databaser: en företags lagrings plats som underhålls av IT-avdelningen och en avdelnings lagrings plats som underhålls av R&D-delningen.

![En exempel distributions miljö och samarbets miljö](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Med den här lager strukturen kan du utveckla och upprätthålla en högre nivå av kvalitet i huvud grenen samtidigt som flera databaser är anslutna till ett labb och ger större flexibilitet.

## <a name="next-steps"></a>Nästa steg    
Se följande artiklar:

- Lägg till en lagrings plats i ett labb med antingen [Azure Portal](devtest-lab-add-artifact-repo.md) eller via [Azure Resource Management-mallen](add-artifact-repository.md)
- [DevTest Labs-artefakter](devtest-lab-artifact-author.md)
- [DevTest Labs miljöer](devtest-lab-create-environment-from-arm.md).
