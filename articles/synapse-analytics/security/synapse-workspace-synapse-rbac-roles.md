---
title: Synapse RBAC-roller
description: I den här artikeln beskrivs de inbyggda RBAC-rollerna för Synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 35f66732fa9cb48b94f80bab203534c9d04b7a7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102129"
---
# <a name="synapse-rbac-roles"></a>Synapse RBAC-roller

I artikeln beskrivs de inbyggda RBAC-rollerna för Synapse, de behörigheter som de tilldelar och de omfång där de kan användas.  

## <a name="whats-changed-since-the-preview"></a>Vad har ändrats sedan för hands versionen?
För användare som är bekanta med de Synapse RBAC-roller som anges under för hands versionen gäller följande ändringar:
- Arbets ytans administratör har bytt namn till **Synapse-administratör**
- Apache Spark administratör har bytt namn till **Synapse Apache Spark-administratör** och har behörighet att visa alla publicerade kod artefakter, inklusive SQL-skript.  Den här rollen ger inte längre behörighet att använda arbets ytans MSI, som kräver användar rollen Synapse-autentiseringsuppgift.  Den här behörigheten krävs för att köra pipeliner. 
- SQL-administratören har bytt namn till **SQL-Synapse** och har behörighet att se alla publicerade kod artefakter, inklusive Spark-anteckningsböcker och jobb.  Den här rollen ger inte längre behörighet att använda arbets ytans MSI, som kräver användar rollen Synapse-autentiseringsuppgift. Den här behörigheten krävs för att köra pipeliner.
- **Nya, mer detaljerade SYNAPSE RBAC-roller** införs som fokuserar på stöd för utveckling och drift personer snarare än vissa analys körningar.  
- **Nya omfång på lägre nivåer** införs för flera roller.  Med dessa omfång kan roller begränsas till vissa resurser eller objekt.

>[!Note]
>De **nya SYNAPSE RBAC-rollerna och de lågnivå omfattningarna är för närvarande i för hands version**.  Du uppmanas att använda de här nya rollerna och omfattningarna, som stöds helt och ger feedback om användningen.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>Inbyggda RBAC-roller och omfång för Synapse

I följande tabell beskrivs de inbyggda rollerna och de omfång där de kan användas.

>[!Note]
> Användare med valfri Synapse RBAC-roll i valfri omfattning har automatiskt Synapse användar rollen vid arbets ytans omfång. 

|Roll |Behörigheter|Omfattningar|
|---|---|-----|
|Synapse-administratör  |Fullständig Synapse åtkomst till SQL-pooler utan server, Apache Spark pooler och integrerings körningar.  Inkluderar skapa, läsa, uppdatera och ta bort åtkomst till alla publicerade kod artefakter.  Innehåller användar behörigheter för beräknings Operator, länkade Data Manager och autentiseringsuppgifter på arbets ytans system identitetens autentiseringsuppgifter.  Inkluderar tilldelning av Synapse RBAC-roller. Förutom Synapse-administratören kan Azure-ägare också tilldela Synapse RBAC-roller. Azure-behörigheter krävs för att skapa, ta bort och hantera beräknings resurser. </br></br>_Kan läsa och skriva artefakter </br> kan utföra alla åtgärder på Spark-aktiviteter. </br> Kan visa Spark-poolens loggar </br> kan visa Sparad antecknings bok och pipeline-utdata </br> kan använda de hemligheter som lagras av länkade tjänster eller autentiseringsuppgifter </br> kan ansluta till SQL Server lös slut punkter med SQL `db_datareader` , `db_datawriter` , `connect` , och `grant` behörigheter </br> kan tilldela och återkalla Synapse RBAC-roller i aktuellt omfång_|Arbetsyta </br> Spark-pool<br/>Integration runtime </br>Länkad tjänst</br>Autentiseringsuppgift |
|Synapse Apache Spark administratör</br>|Fullständig Synapse åtkomst till Apache Spark pooler.  Skapa, läsa, uppdatera och ta bort åtkomst till publicerade Spark-jobb,-antecknings böcker och deras utdata och till bibliotek, länkade tjänster och autentiseringsuppgifter.  Innehåller Läs behörighet till alla andra publicerade kod artefakter. Innehåller inte behörighet att använda autentiseringsuppgifter och köra pipeliner. Inkluderar inte beviljad åtkomst. </br></br>_Kan utföra alla åtgärder på Spark-artefakter </br> kan utföra alla åtgärder på Spark-aktiviteter_|Arbetsyta</br>Spark-pool|
|Synapse SQL-administratör|Fullständig Synapse åtkomst till serverbaserade SQL-pooler.  Skapa, läsa, uppdatera och ta bort åtkomst till publicerade SQL-skript, autentiseringsuppgifter och länkade tjänster.  Innehåller Läs behörighet till alla andra publicerade kod artefakter.  Innehåller inte behörighet att använda autentiseringsuppgifter och köra pipeliner. Inkluderar inte beviljad åtkomst. </br></br>*Kan utföra alla åtgärder på SQL <br/> -skript kan ansluta till SQL Server-felslutna slut punkter med SQL `db_datareader` , `db_datawriter` , `connect` och- `grant` behörigheter*|Arbetsyta|
|Synapse-deltagare|Fullständig Synapse åtkomst till Apache Spark pooler och integrerings körningar. Inkluderar skapa, läsa, uppdatera och ta bort åtkomst till alla publicerade kod artefakter och deras utdata, inklusive autentiseringsuppgifter och länkade tjänster.  Innehåller behörigheter för beräknings operator. Innehåller inte behörighet att använda autentiseringsuppgifter och köra pipeliner. Inkluderar inte beviljad åtkomst. </br></br>_Kan läsa och skriva artefakter </br> kan visa sparade antecknings böcker och pipeline-utdata </br> kan göra att alla åtgärder på Spark-aktiviteter </br> kan visa Spark-poolens loggar_|Arbetsyta </br> Spark-pool<br/> Integration runtime|
|Synapse artefakt utgivare|Skapa, läsa, uppdatera och ta bort åtkomst till publicerade kod artefakter och deras utdata. Innehåller inte behörighet att köra kod eller pipeliner eller att bevilja åtkomst. </br></br>_Kan läsa publicerade artefakter och publicera artefakter </br> kan visa sparade antecknings böcker, Spark-jobb och pipeline-utdata_|Arbetsyta
|Synapse artefakt användare|Läs åtkomst till publicerade kod artefakter och deras utdata. Kan skapa nya artefakter men inte publicera ändringar eller köra kod utan ytterligare behörigheter.|Arbetsyta
|Synapse Compute-operator |Skicka Spark-jobb och-antecknings böcker och Visa loggar.  Tar bort Spark-jobb som skickats av en användare. Kräver ytterligare behörighet för användnings behörighet på arbets ytans system identitet för att köra pipeliner, Visa pipeline-körningar och utdata. </br></br>_Kan skicka och avbryta jobb, inklusive jobb som skickats av andra </br> kan visa Spark pool loggar_|Arbetsyta</br>Spark-pool</br>Integration runtime|
|Användare för Synapse-autentiseringsuppgift|Körnings-och konfigurations tids användningen av hemligheter inom autentiseringsuppgifter och länkade tjänster i aktiviteter som pipeliner körs. Om du vill köra pipeliner krävs den här rollen, som är begränsad till arbets ytans system identitet. </br></br>_Begränsad till autentiseringsuppgifter, tillåter åtkomst till data via en länkad tjänst som skyddas av autentiseringsuppgiften (kräver även behörighet för beräknings användning) </br> tillåter körning av pipelines som skyddas av arbets ytans system identitets autentiseringsuppgift (med ytterligare behörighet för beräknings användning)_|Arbetsyta </br>Länkad tjänst</br>Autentiseringsuppgift
|Synapse länkade Data Manager|Skapande och hantering av hanterade privata slut punkter, länkade tjänster och autentiseringsuppgifter. Kan skapa hanterade privata slut punkter som använder länkade tjänster som skyddas av autentiseringsuppgifter|Arbetsyta|
|Synapse-användare|Lista och Visa information om SQL-pooler, Apache Spark pooler, integrerings körningar och publicerade länkade tjänster och autentiseringsuppgifter. Inkluderar inte andra publicerade kod artefakter.  Kan skapa nya artefakter, men inte köra eller publicera utan ytterligare behörigheter.</br></br>_Kan visa och läsa Spark-pooler, integrerings körningar._|Arbets yta, Spark-pool</br>Länkad tjänst </br>Autentiseringsuppgift|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Synapse RBAC-roller och de åtgärder som de tillåter

>[!Note]
>- Alla åtgärder som anges i tabellerna nedan har prefixet "Microsoft. Synapse/..."</br>
>- Alla åtgärder för Läs-, skriv-och borttagnings åtgärder för artefakter gäller publicerade artefakter i Live-tjänsten.  Dessa behörigheter påverkar inte åtkomsten till artefakter i en ansluten git-lagrings platsen.  

I följande tabell visas de inbyggda rollerna och de åtgärder/behörigheter som var och en stöder.

Roll|Åtgärder
--|--
Synapse-administratör|arbets ytor/läsa</br>arbets ytor/roleAssignments/Write, Delete</br>arbets ytor/managedPrivateEndpoint/Write, Delete</br>arbets ytor/bigDataPools/useCompute/Action</br>arbets ytor/bigDataPools/viewLogs/Action</br>arbets ytor/integrationRuntimes/useCompute/Action</br>arbets ytor/artefakter/läsningar</br>arbets ytor/antecknings böcker/skriva, ta bort</br>arbets ytor/sparkJobDefinitions/Write, Delete</br>arbets ytor/sqlScripts/Write, Delete</br>arbets ytor/data flöden/Write, Delete</br>arbets ytor/pipeline/skriva, ta bort</br>arbets ytor/utlösare/skriva, ta bort</br>arbets ytor/data uppsättningar/Skriv, ta bort</br>arbets ytor/bibliotek/skriva, ta bort</br>arbets ytor/linkedServices/Write, Delete</br>arbets ytor/autentiseringsuppgifter/Skriv, ta bort</br>arbets ytor/antecknings böcker/viewOutputs/åtgärd</br>arbets ytor/pipeline/viewOutputs/åtgärd</br>arbets ytor/linkedServices/useSecret/Action</br>arbets ytor/autentiseringsuppgifter/useSecret/åtgärd|
|Synapse Apache Spark administratör|arbets ytor/läsa</br>arbets ytor/bigDataPools/useCompute/Action</br>arbets ytor/bigDataPools/viewLogs/Action</br>arbets ytor/antecknings böcker/viewOutputs/åtgärd</br>arbets ytor/artefakter/läsningar</br>arbets ytor/antecknings böcker/skriva, ta bort</br>arbets ytor/sparkJobDefinitions/Write, Delete</br>arbets ytor/bibliotek/skriva, ta bort</br>arbets ytor/linkedServices/Write, Delete</br>arbets ytor/autentiseringsuppgifter/Skriv, ta bort|
|Synapse SQL-administratör|arbets ytor/läsa</br>arbets ytor/artefakter/läsningar</br>arbets ytor/sqlScripts/Write, Delete</br>arbets ytor/linkedServices/Write, Delete</br>arbets ytor/autentiseringsuppgifter/Skriv, ta bort|
|Synapse-deltagare|arbets ytor/läsa</br>arbets ytor/bigDataPools/useCompute/Action</br>arbets ytor/bigDataPools/viewLogs/Action</br>arbets ytor/integrationRuntimes/useCompute/Action</br>arbets ytor/integrationRuntimes/viewLogs/Action</br>arbets ytor/artefakter/läsningar</br>arbets ytor/antecknings böcker/skriva, ta bort</br>arbets ytor/sparkJobDefinitions/Write, Delete</br>arbets ytor/sqlScripts/Write, Delete</br>arbets ytor/data flöden/Write, Delete</br>arbets ytor/pipeline/skriva, ta bort</br>arbets ytor/utlösare/skriva, ta bort</br>arbets ytor/data uppsättningar/Skriv, ta bort</br>arbets ytor/bibliotek/skriva, ta bort</br>arbets ytor/linkedServices/Write, Delete</br>arbets ytor/autentiseringsuppgifter/Skriv, ta bort</br>arbets ytor/antecknings böcker/viewOutputs/åtgärd</br>arbets ytor/pipeline/viewOutputs/åtgärd|
|Synapse artefakt utgivare|arbets ytor/läsa</br>arbets ytor/artefakter/läsningar</br>arbets ytor/antecknings böcker/skriva, ta bort</br>arbets ytor/sparkJobDefinitions/Write, Delete</br>arbets ytor/sqlScripts/Write, Delete</br>arbets ytor/data flöden/Write, Delete</br>arbets ytor/pipeline/skriva, ta bort</br>arbets ytor/utlösare/skriva, ta bort</br>arbets ytor/data uppsättningar/Skriv, ta bort</br>arbets ytor/bibliotek/skriva, ta bort</br>arbets ytor/linkedServices/Write, Delete</br>arbets ytor/autentiseringsuppgifter/Skriv, ta bort</br>arbets ytor/antecknings böcker/viewOutputs/åtgärd</br>arbets ytor/pipeline/viewOutputs/åtgärd|
|Synapse artefakt användare|arbets ytor/läsa</br>arbets ytor/artefakter/läsningar</br>arbets ytor/antecknings böcker/viewOutputs/åtgärd</br>arbets ytor/pipeline/viewOutputs/åtgärd|
|Synapse Compute-operator |arbets ytor/läsa</br>arbets ytor/bigDataPools/useCompute/Action</br>arbets ytor/bigDataPools/viewLogs/Action</br>arbets ytor/integrationRuntimes/useCompute/Action</br>arbets ytor/integrationRuntimes/viewLogs/Action|
|Användare för Synapse-autentiseringsuppgift|arbets ytor/läsa</br>arbets ytor/linkedServices/useSecret/Action</br>arbets ytor/autentiseringsuppgifter/useSecret/åtgärd|
|Synapse länkade Data Manager|arbets ytor/läsa</br>arbets ytor/managedPrivateEndpoint/Write, Delete</br>arbets ytor/linkedServices/Write, Delete</br>arbets ytor/autentiseringsuppgifter/Skriv, ta bort|
|Synapse-användare|arbets ytor/läsa|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Synapse RBAC-åtgärder och de roller som tillåter dem

I följande tabell visas Synapse-åtgärder och de inbyggda roller som tillåter följande åtgärder:

Action|Roll
--|--
arbets ytor/läsa|Synapse-administratör</br>Synapse Apache Spark administratör</br>Synapse SQL-administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare</br>Synapse artefakt användare</br>Synapse Compute-operator </br>Användare för Synapse-autentiseringsuppgift</br>Synapse länkade Data Manager</br>Synapse-användare 
arbets ytor/roleAssignments/Write, Delete|Synapse-administratör
arbets ytor/managedPrivateEndpoint/Write, Delete|Synapse-administratör</br>Synapse länkade Data Manager
arbets ytor/bigDataPools/useCompute/Action|Synapse-administratör</br>Synapse Apache Spark administratör</br>Synapse-deltagare</br>Synapse Compute-operator 
arbets ytor/bigDataPools/viewLogs/Action|Synapse-administratör</br>Synapse Apache Spark administratör</br>Synapse-deltagare</br>Synapse Compute-operator 
arbets ytor/integrationRuntimes/useCompute/Action|Synapse-administratör</br>Synapse-deltagare</br>Synapse Compute-operator 
arbets ytor/artefakter/läsningar|Synapse-administratör</br>Synapse Apache Spark administratör</br>Synapse SQL-administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare</br>Synapse artefakt användare
arbets ytor/antecknings böcker/skriva, ta bort|Synapse-administratör</br>Synapse Apache Spark administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare
arbets ytor/sparkJobDefinitions/Write, Delete|Synapse-administratör</br>Synapse Apache Spark administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare
arbets ytor/sqlScripts/Write, Delete|Synapse-administratör</br>Synapse SQL-administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare
arbets ytor/data flöden/Write, Delete|Synapse-administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare
arbets ytor/pipeline/skriva, ta bort|Synapse-administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare
arbets ytor/utlösare/skriva, ta bort|Synapse-administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare
arbets ytor/data uppsättningar/Skriv, ta bort|Synapse-administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare
arbets ytor/bibliotek/skriva, ta bort|Synapse-administratör</br>Synapse Apache Spark administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare
arbets ytor/linkedServices/Write, Delete|Synapse-administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare</br>Synapse länkade Data Manager
arbets ytor/autentiseringsuppgifter/Skriv, ta bort|Synapse-administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare</br>Synapse länkade Data Manager
arbets ytor/antecknings böcker/viewOutputs/åtgärd|Synapse-administratör</br>Synapse Apache Spark administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare</br>Synapse artefakt användare
arbets ytor/pipeline/viewOutputs/åtgärd|Synapse-administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare</br>Synapse artefakt användare
arbets ytor/linkedServices/useSecret/Action|Synapse-administratör</br>Användare för Synapse-autentiseringsuppgift
arbets ytor/autentiseringsuppgifter/useSecret/åtgärd|Synapse-administratör</br>Användare för Synapse-autentiseringsuppgift

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Synapse RBAC-omfång och deras roller som stöds

I tabellen nedan visas en lista över Synapse RBAC-omfattningar och de roller som kan tilldelas i varje omfång. 

>[!note]
>Om du vill skapa eller ta bort ett objekt måste du ha behörigheter på en omfattning på högre nivå.

Omfång|Roller
--|--
Arbetsyta |Synapse-administratör</br>Synapse Apache Spark administratör</br>Synapse SQL-administratör</br>Synapse-deltagare</br>Synapse artefakt utgivare</br>Synapse artefakt användare</br>Synapse Compute-operator </br>Användare för Synapse-autentiseringsuppgift</br>Synapse länkade Data Manager</br>Synapse-användare
Apache Spark pool | Synapse-administratör </br>Synapse-deltagare </br> Synapse Compute-operator
Integration runtime | Synapse-administratör </br>Synapse-deltagare </br> Synapse Compute-operator
Länkad tjänst |Synapse-administratör </br>Användare för Synapse-autentiseringsuppgift
Autentiseringsuppgift |Synapse-administratör </br>Användare för Synapse-autentiseringsuppgift
 
>[!note]
>Alla artefakt roller och åtgärder är begränsade på arbets ytans nivå. 

## <a name="next-steps"></a>Nästa steg

Lär dig [hur du granskar SYNAPSE RBAC-roll tilldelningar](./how-to-review-synapse-rbac-role-assignments.md) för en arbets yta.

Lär dig [hur du tilldelar SYNAPSE RBAC-roller](./how-to-manage-synapse-rbac-role-assignments.md)
