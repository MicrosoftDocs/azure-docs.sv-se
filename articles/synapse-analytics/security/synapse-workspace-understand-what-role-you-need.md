---
title: Förstå de roller som krävs för att utföra vanliga uppgifter i Synapse
description: Den här artikeln beskriver vilka inbyggda Synapse RBAC-roller som krävs för att utföra vissa uppgifter
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ba00e6f4fe97b0614483fd04ddee9fc768558db5
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102112"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>Förstå de roller som krävs för att utföra vanliga uppgifter i Synapse

Den här artikeln hjälper dig att förstå vilken Synapse RBAC-baserad (rollbaserad åtkomst kontroll) eller Azure RBAC-roller du behöver för att få jobbet gjort i Synapse Studio.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Åtkomst kontroll för Synapse Studio och arbets flödes Sammanfattning 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>Komma åt Synapse Studio och visa dess innehåll

- Du kan öppna Synapse Studio och Visa information om arbets ytan och lista någon av dess Azure-resurser (SQL-pooler, Spark-pooler eller integrerings körningar) om du har tilldelats en Synapse RBAC-roll eller har rollen Azure-ägare, deltagare eller läsare på arbets ytan.

### <a name="resource-management"></a>Resurshantering

- Du kan skapa SQL-pooler, Apache Spark pooler och integrerings körningar om du är Azure-ägare eller deltagare på arbets ytan.
- Du kan pausa eller skala en dedikerad SQL-pool, konfigurera en spark-pool eller en integration Runtime om du är en Azure-ägare eller deltagare på arbets ytan eller resursen.

### <a name="viewing-and-editing-code-artifacts"></a>Visa och redigera kod artefakter

- Med åtkomst till Synapse Studio kan du skapa nya kod artefakter, till exempel SQL-skript, antecknings böcker, Spark-jobb, länkade tjänster, pipelines, data flöden, utlösare och autentiseringsuppgifter.  (Dessa artefakter kan publiceras eller sparas med ytterligare behörighet.)  
- Om du är en Synapse artefakt användare, Synapse artefakt utgivare, Synapse-deltagare eller Synapse-administratör kan du Visa, öppna och redigera redan publicerade kod artefakter.

### <a name="executing-your-code"></a>Köra din kod

- Du kan köra SQL-skript på SQL-pooler om du har de nödvändiga SQL-behörigheterna definierade i SQL-pooler.  
- Du kan köra antecknings böcker och Spark-jobb om du har behörighet för Synapse Compute-operator på arbets ytan eller i vissa Apache Spark pooler.  
- Med beräknings operatorns behörigheter på arbets ytan eller vissa integrerings körningar och lämpliga autentiseringsuppgifter kan du köra pipeliner.

### <a name="monitoring-and-managing-execution"></a>Övervakning och hantering av körning
- Du kan granska statusen för att köra antecknings böcker och jobb i Apache Spark pooler om du är Synapse-användare.
- Du kan granska loggar och avbryta jobb och pipeliner om du är Synapse Compute-operator på arbets ytan eller för en speciell Spark-pool eller pipeline.  

### <a name="publishing-and-saving-your-code"></a>Publicera och spara koden

- Du kan publicera nya eller uppdaterade kod artefakter till tjänsten om du är Synapse artefakt utgivare, Synapse Contributor eller Synapse-administratör. 
- Du kan allokera kod artefakter till en arbets gren i en git-lagringsplats om arbets ytan är git-aktiverad och du har git-behörigheter. Med git aktive rad tillåts publicering endast från samarbets grenen.
- Om du stänger Synapse Studio utan att publicera eller verkställa ändringar i kod artefakter går ändringarna förlorade.


## <a name="tasks-and-required-roles"></a>Uppgifter och nödvändiga roller

I tabellen nedan visas vanliga aktiviteter och för varje aktivitet, Synapse RBAC eller Azure RBAC-roller som krävs.  

>[!Note]
>- Synapse-administratören visas inte för varje aktivitet om det inte är den enda rollen som ger nödvändig behörighet.  En Synapse-administratör kan utföra alla uppgifter som aktive ras av andra Synapse RBAC-roller.</br>
>- Den minsta Synapse RBAC-rollen som krävs visas.
>- Alla Synapse RBAC-roller vid valfri omfattning ger dig Synapse användar behörighet på arbets ytan
>- Alla Synapse RBAC-behörigheter/åtgärder som visas i tabellen är prefixen Microsoft/Synapse/arbetsyte/... </br>


Uppgift (jag vill...) |Roll (jag måste vara...)|Synapse RBAC-behörighet/åtgärd
--|--|--
|Öppna Synapse Studio på en arbets yta|Synapse-användare eller|läsa
| |Azure-ägare, deltagare eller läsare på arbets ytan|inget
|Lista SQL-pooler, Apache Spark pooler, integrations körningar och åtkomst till konfigurations information|Synapse-användare eller|läsa|
||Azure-ägare, deltagare eller läsare på arbets ytan|inget
|Lista länkade tjänster, autentiseringsuppgifter, hanterade privata slut punkter|Synapse-användare|läsa
SQL-POOLER|
Skapa en dedikerad SQL-pool eller en server lös SQL-pool|Azure-ägare eller deltagare på arbets ytan|inget
Hantera (pausa, skala eller ta bort) en dedikerad SQL-pool|Azure-ägare eller deltagare i SQL-poolen eller arbets ytan|inget
Skapa ett SQL-skript</br>|Synapse-användare eller </br>Azure-ägare eller deltagare på arbets ytan, </br>*Ytterligare SQL-behörigheter krävs för att köra ett SQL-skript, publicera eller genomför ändringar*.|
Visa och öppna eventuella publicerade SQL-skript| Synapse artefakt användare, artefakt utgivare, Synapse-deltagare|artefakter/läsningar
Köra ett SQL-skript på en server lös SQL-pool|SQL-behörigheter för poolen (beviljas automatiskt till en Synapse-administratör)|inget
Köra ett SQL-skript på en dedikerad SQL-pool|SQL-behörigheter för poolen|inget
Publicera ett nytt, uppdaterat eller Borttaget SQL-skript|Synapse artefakt utgivare, Synapse-deltagare|sqlScripts/skriva, ta bort
Spara ändringar i ett SQL-skript till git-lagrings platsen|Kräver git-behörigheter på lagrings platsen|
Tilldela Active Directory administratör på arbets ytan (via arbets ytans egenskaper i Azure Portal)|Azure-ägare eller deltagare på arbets ytan |
APACHE SPARK-POOLER|
Skapa en Apache Spark pool|Azure-ägare eller deltagare på arbets ytan|
Övervaka Apache Spark program| Synapse-användare|läsa
Visa loggfilerna för Notebook och jobb körning |Synapse Compute-operator|
Avbryt alla Notebook-eller Spark-jobb som körs på en Apache Spark pool|Synapse Compute-operator i Apache Spark-poolen.|bigDataPools/useCompute
Skapa en antecknings bok eller en jobb definition|Synapse-användare eller </br>Azure-ägare, deltagare eller läsare på arbets ytan</br> *Ytterligare behörigheter krävs för att köra, publicera eller bekräfta ändringar*|läsa</br></br></br></br></br> 
Visa och öppna en publicerad antecknings bok eller jobb definition, inklusive Granska sparade utdata|Synapse artefakt användare, Synapse artefakt utgivare, Synapse Contributor på arbets ytan|artefakter/läsningar
Kör en bärbar dator och granska dess utdata|Synapse Apache Spark-administratör, Synapse Compute-operator på den valda Apache Spark poolen|bigDataPools/useCompute 
Publicera eller ta bort en antecknings bok eller jobb definition (inklusive utdata) till tjänsten|Artefakt utgivare på arbets ytan, Synapse Apache Spark administratör|antecknings böcker/skriva, ta bort
Genomför ändringar i en bärbar dator eller jobb definition på git-lagrings platsen|Git-behörigheter|inget
PIPELINES, INTEGRERINGS KÖRNINGAR, DATA FLÖDEN, DATA UPPSÄTTNINGAR & UTLÖSARE|
Skapa, uppdatera eller ta bort en integrerings körning|Azure-ägare eller deltagare på arbets ytan|
Övervaka status för integration runtime|Synapse-användare|läsa, pipeliner/viewOutputs
Granska pipeline-körningar|Synapse artefakt utgivare/Synapse-deltagare|läsa, pipeliner/viewOutputs 
Skapa en pipeline |Synapse-användare</br>*Ytterligare Synapse-behörigheter krävs för att felsöka, lägga till utlösare, publicera eller Spara ändringar*|läsa
Skapa ett data flöde eller en data uppsättning |Synapse-användare</br>*Ytterligare Synapse-behörigheter krävs för att publicera eller Spara ändringar*|läsa
Visa och öppna en publicerad pipeline |Synapse artefakt användare | artefakter/läsningar
Förhandsgranska data uppsättnings data|Synapse användare + Synapse Credential User på WorkspaceSystemIdentity| 
Felsöka en pipeline med standard integrerings körningen|Synapse användare + Synapse Credential User på WorkspaceSystemIdentity-autentiseringsuppgiften|Läs </br>autentiseringsuppgifter/useSecret
Skapa en utlösare, inklusive utlösare nu (kräver behörighet att köra pipelinen)|Synapse användare + Synapse Credential User på WorkspaceSystemIdentity|läsa, autentiseringsuppgifter/useSecret/åtgärd
Kör/kör en pipeline|Synapse användare + Synapse Credential User på WorkspaceSystemIdentity|läsa, autentiseringsuppgifter/useSecret/åtgärd
Kopiera data med hjälp av Kopiera data-verktyget|Synapse användare + Synapse Credential User på arbets ytans system identitet|läsa, autentiseringsuppgifter/useSecret/åtgärd
Mata in data (med ett schema)|Synapse Author + Synapse Credential User på arbets ytans system identitet|läsa, autentiseringsuppgifter/useSecret/åtgärd
Publicera en ny, uppdaterad eller borttagen pipeline, data flöde eller utlösare för tjänsten|Synapse artefakt utgivare på arbets ytan|pipeline/Skriv, ta bort</br>data flöden/skriva, ta bort</br>utlösare/skriva, ta bort
Genomför ändringar av pipelines, data flöden, data uppsättningar eller utlösare för git-lagrings platsen |Git-behörigheter|inget 
LÄNKADE TJÄNSTER|
Skapa en länkad tjänst (inklusive tilldela autentiseringsuppgifter)|Synapse-användare</br>*Ytterligare behörigheter krävs för att använda en länkad tjänst med autentiseringsuppgifter, eller för att publicera eller Spara ändringar*|läsa
Visa och öppna en publicerad länkad tjänst|Synapse artefakt användare|linkedServices/skriva, ta bort  
Testa anslutningen till en länkad tjänst som skyddas av en autentiseringsuppgift|Synapse användare + Synapse Credential User|autentiseringsuppgifter/useSecret/åtgärd|
Publicera en länkad tjänst|Synapse artefakt utgivare, Synapse länkade Data Manager|linkedServices/skriva, ta bort
Genomför länkade tjänst definitioner till git-lagrings platsen|Git-behörigheter|inget
ÅTKOMST HANTERING|
Granska Synapse RBAC-roll tilldelningar i valfri omfattning|Synapse-användare|läsa
Tilldela och ta bort Synapse RBAC-roll tilldelningar för användare, grupper och tjänstens huvud namn| Synapse-administratör på arbets ytan eller på en bestämd arbets ytans objekt omfång|roleAssignments/skriva, ta bort 

>[!Note]
>Gäst användare från en annan klient organisation kan inte granska, lägga till eller ändra roll tilldelningar oavsett vilken roll de har tilldelats. 

## <a name="next-steps"></a>Nästa steg

Lär dig [hur du granskar SYNAPSE RBAC-roll tilldelningar](./how-to-review-synapse-rbac-role-assignments.md)

Lär dig [hur du hanterar SYNAPSE RBAC-roll tilldelningar](./how-to-manage-synapse-rbac-role-assignments.md). 