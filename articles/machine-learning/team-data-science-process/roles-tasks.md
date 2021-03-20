---
title: Process roller och uppgifter för team data vetenskap
description: En översikt över viktiga komponenter, personal roller och associerade uppgifter för en data vetenskaps grupp.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4bcbbd9378b6ea861c926eed16d2ceed8131b913
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000971"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Process roller och uppgifter för team data vetenskap

TDSP (Team data science process) är ett ramverk som utvecklats av Microsoft och som ger en strukturerad metod för att effektivt bygga förutsägelse analys lösningar och intelligenta program. Den här artikeln beskriver viktiga personal roller och associerade uppgifter för ett data vetenskaps team som är standardiserat för den här processen.

Den här inledande artikeln länkar till självstudier om hur du konfigurerar TDSP-miljön. Självstudierna innehåller detaljerad information om hur du använder Azure DevOps Projects, Azure databaser-databaser och Azure-kort.  Det motivbaserade målet flyttas från konceptet genom modellering och till distribution.

Självstudierna använder Azure-DevOps eftersom det är så här implementerar du TDSP på Microsoft. Azure-DevOps fören klar samarbetet genom att integrera rollbaserad säkerhet, hantering av arbets objekt, spårning och kod värdskap, delning och käll kontroll. Självstudierna använder också en Azure [data science Virtual Machine](https://aka.ms/dsvm) (DSVM) som Analytics Desktop, som har flera populära data vetenskaps verktyg förkonfigurerade och integrerade med Microsoft-programvara och Azure-tjänster. 

Du kan använda självstudierna för att implementera TDSP med andra kod värdar, smidiga planerings verktyg och utvecklingsverktyg, men vissa funktioner kanske inte är tillgängliga.

## <a name="structure-of-data-science-groups-and-teams"></a>Struktur för data vetenskaps grupper och team

Data vetenskaps funktioner i företag organiseras ofta i följande hierarki:

- Data vetenskaps grupp
  - Data vetenskaps team/s inom gruppen

I en sådan struktur finns grupp ledare och grupp ledare. Ett data vetenskaps projekt utförs vanligt vis av ett data vetenskaps team. Data vetenskaps team har projekt som leder till projekt hanterings-och styrnings aktiviteter och enskilda data forskare och tekniker för att utföra data vetenskaps-och data tekniks delar i projektet. Den första projekt konfigurationen och styrningen utförs av gruppen, teamet eller projekt leads.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definition och uppgifter för de fyra TDSP-rollerna
Med antagandet att data vetenskaps enheten består av team i en grupp finns det fyra olika roller för TDSP-personal:

1. **Grupp hanterare**: hanterar hela data vetenskaps enheten i ett företag. En data vetenskaps enhet kan ha flera team, där var och en arbetar med flera data vetenskaps projekt i olika affärs lodräta. En grupp hanterare kan delegera sina uppgifter till ett surrogat, men de uppgifter som är associerade med rollen ändras inte.
   
2. **Grupp ledare**: hanterar ett team i en företags data vetenskaps enhet. Ett team består av flera data forskare. För en liten data vetenskaps enhet kan grupp chefen och teamets lead vara samma person.
   
3. **Projekt ledare**: hanterar dagliga aktiviteter för enskilda data forskare i ett visst data vetenskaps projekt.
   
4. **Projicera enskilda bidrags givare**: data experter, affärsanalytiker, data tekniker, arkitekter och andra som kör ett data vetenskaps projekt.

> [!NOTE]
> Beroende på företagets struktur och storlek kan en enskild person spela mer än en roll, eller så kan mer än en person fylla i en roll.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Aktiviteter som ska utföras av de fyra rollerna

I följande diagram visas toppnivå aktiviteter för varje team data vetenskaps process roll. Det här schemat och följande, mer detaljerad översikt över aktiviteter för varje TDSP-roll kan hjälpa dig att välja den själv studie kurs du behöver utifrån dina ansvars områden.

![Översikt över roller och aktiviteter](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Grupp hanterings uppgifter

Grupp hanteraren eller en utsedd TDSP system administratör utför följande uppgifter för att anta TDSP:

- Skapar en Azure DevOps- **organisation** och ett grupp projekt i organisationen. 
- Skapar en **lagrings plats för projekt mal len** i Azure DevOps Group-projektet och dirigerar den från lagrings platsen för projekt mal len som har utvecklats av Microsoft TDSP-teamet. Microsoft TDSP Project Template-lagringsplatsen innehåller:
  - En **standardiserad katalog struktur**, inklusive kataloger för data, kod och dokument.
  - En uppsättning **standardiserade dokumentmallar** som hjälper dig att skapa en effektiv data vetenskaps process.
- Skapar en **verktygs lagrings plats** och dirigerar den från verktygets lagrings plats som utvecklats av Microsoft TDSP-teamet. TDSP Utility-lagringsplatsen från Microsoft tillhandahåller en uppsättning användbara verktyg för att göra arbetet i en data expert mer effektivt. Microsoft-verktygets lagrings plats innehåller verktyg för interaktiv data utforskning, analys, rapportering och bas linje modellering och rapportering.
- Konfigurerar **säkerhets kontroll principen** för organisations kontot.

Detaljerade anvisningar finns i [grupp chefs aktiviteter för ett data vetenskaps team](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Grupp ledar uppgifter

Team ledare eller en utsedd projekt administratör utför följande uppgifter för att anta TDSP:

- Skapar ett team **projekt** i gruppens Azure DevOps-organisation.
- Skapar **lagrings platsen för projekt mal len** i projektet och dirigerar den från den grupp projekt mal len lagrings plats som ställts in av grupp hanteraren eller ombudet.
- Skapar **team Utility-lagringsplatsen**, dirigerar den från grupp verktygs lagrings platsen och lägger till Team-/regionsspecifika verktyg i lagrings platsen.
- Du kan också skapa [Azure File Storage](https://azure.microsoft.com/services/storage/files/) för att lagra användbara data till gångar för teamet. Andra grupp medlemmar kan montera det här delade moln fil arkivet på sina Analytics-datorer.
- Du kan också montera Azure File Storage på teamets **DSVM** och lägga till Team data till gångar.
- Ställer in **säkerhets kontroll** genom att lägga till team medlemmar och konfigurera deras behörigheter.

Detaljerade anvisningar finns i [grupp ledar aktiviteter för ett data vetenskaps team](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Projekt ledar uppgifter

Projektets lead Slutför följande uppgifter för att anta TDSP:

- Skapar en **projekt databas** i team projektet och dirigerar den från lagrings platsen för projekt mal len.
- Du kan också skapa **Azure File Storage** för att lagra projektets data till gångar.
- Du kan också montera Azure File Storage i **DSVM** och lägga till projekt data till gångar.
- Ställer in **säkerhets kontroll** genom att lägga till projekt medlemmar och konfigurera deras behörigheter.

Detaljerade anvisningar finns i [projekt ledar aktiviteter för ett data vetenskaps team](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Projicera enskilda bidrags uppgifter

Projektets enskilda deltagare, vanligt vis en data expert, utför följande uppgifter med hjälp av TDSP:

- Klonar **projekt databasen** som har skapats av projektets lead.
- Du kan också montera det delade teamet och Project **Azure File Storage** på sina **data science Virtual Machine** (DSVM).
- Kör projektet.

Detaljerade anvisningar om hur du registrerar dig på ett projekt finns i [projekt enskilda deltagar uppgifter för ett data vetenskaps team](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Arbets flöde för data vetenskaps projekt körning

Genom att följa relevanta självstudier, data tekniker, projekt ledare och grupp ledare kan du skapa arbets objekt för att spåra alla aktiviteter och faser för projektet från början till slut. Med Azure databaser kan du främja samarbetet mellan data experter och se till att artefakterna som genereras vid projekt körningen är versioner som styrs och delas av alla projekt medlemmar. Med Azure DevOps kan du länka dina Azure-kort till dina Azure databaser-lagringsplatser och enkelt spåra vad som har gjorts för ett arbets objekt.

Följande bild beskriver TDSP-arbetsflödet för projekt körning:

![Arbets flöde för vanliga data vetenskaps projekt](./media/roles-tasks/overview-project-execute.png)

Arbets flödes stegen kan grupperas i tre aktiviteter:

- Projekt ledare utför Sprint planering
- Data forskare utvecklar artefakter på `git` grenar för att ta itu med arbets objekt
- Projekt ledare eller andra grupp medlemmar gör kod granskningar och sammanfogar arbets grenar till den primära grenen

Detaljerade anvisningar om arbets flödet för projekt körning finns i [smidig utveckling av data vetenskaps projekt](agile-development.md).

## <a name="tdsp-project-template-repository"></a>TDSP-projektmall

Använd Microsoft TDSP-teamets [mall för projekt mal len](https://github.com/Azure/Azure-TDSP-ProjectTemplate) för att stödja effektiv projekt körning och samarbete. Lagrings platsen innehåller en standardiserad katalog struktur och dokumentmallar som du kan använda för dina egna TDSP-projekt.

## <a name="next-steps"></a>Nästa steg

Utforska mer detaljerade beskrivningar av de roller och uppgifter som definierats av team data science-processen:

- [Grupp chefs aktiviteter för ett data vetenskaps team](group-manager-tasks.md)
- [Grupp ledar uppgifter för ett data vetenskaps team](team-lead-tasks.md)
- [Projekt ledar uppgifter för ett data vetenskaps team](project-lead-tasks.md)
- [Projicera enskilda deltagar uppgifter för ett data vetenskaps team](project-ic-tasks.md)
