---
title: Vanliga frågor och svar om Azure Synapse
description: Vanliga frågor och svar om Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 9a0fb8ed8ac54fa866b6db7d8f808c011c0c6758
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695829"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Synapse Analytics

I den här guiden hittar du de vanligaste frågorna om Azure Synapse Analytics.

## <a name="general"></a>Allmänt

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>F: Hur kan jag använda RBAC-roller för att skydda min arbets yta?

A: Azure Synapse introducerar ett antal roller och omfattningar för att tilldela dem för att förenkla säkerheten för din arbets yta.

Synapse RBAC-roller:
* Synapse-administratör
* Synapse SQL-administratör
* Synapse Spark-administratör
* Synapse-deltagare (för hands version)
* Synapse artefakt utgivare (för hands version)
* Synapse artefakt användare (förhands granskning)
* Synapse Compute-operator (förhands granskning)
* Synapse Credential-användare (förhands granskning)

För att skydda din Synapse-arbetsyta tilldelar du RBAC-rollerna till följande RBAC-scope:
* Arbetsytor
* Spark-pooler
* Integrerings körningar
* Länkade tjänster
* Autentiseringsuppgifter

Med dedikerade SQL-pooler har du dessutom alla samma säkerhetsfunktioner som du känner till och älskar.

### <a name="q-how-do-i-control-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>F: Hur gör jag för att kontrol lera dedikerade SQL-pooler, Server lös SQL-pooler och Server lös Spark-pooler?

A: som start punkt fungerar Azure Synapse med de inbyggda kostnads analys-och kostnads aviseringarna som finns på Azures prenumerations nivå.

- Dedikerade SQL-pooler – du har direkt insyn i kostnaderna och kontroll över kostnaderna, eftersom du skapar och anger storleken på dedikerade SQL-pooler. Du kan styra vilka användare som ska kunna skapa eller skala dedikerade SQL-pooler med Azure RBAC-roller.

- SQL-pooler utan server – du har övervaknings-och kostnads hanterings kontroller som gör att du kan lägga till en daglig, veckovis och månatlig nivå. Mer information [finns i Cost Management för Server lös SQL-pool](./sql/data-processed.md) . 

- Server lös Spark-pooler – du kan begränsa vem som kan skapa Spark-pooler med Synapse RBAC-roller.  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>F: kommer att Synapse-arbetsytans organisation av objekt och granularitet i GA?

A: Synapse-arbetsytor stöder användardefinierade mappar.

### <a name="q-can-i-link-more-than-one-power-bi-workspace-to-a-single-azure-synapse-workspace"></a>F: kan jag länka fler än en Power BI arbets yta till en enda Azure Synapse-arbetsyta?
    
A: för närvarande kan du bara länka en enskild Power BI arbets yta till en Azure Synapse-arbetsyta. 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>F: är Synapse länk till Cosmos DB GA?

A: Synapse-länk för Apache Spark är GA. Synapse-länk för Server lös SQL-pool finns i offentlig för hands version.

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>F: stöder Azure Synapse-arbetsytan CI/CD? 

A: Ja! Alla pipeline-artefakter, antecknings böcker, SQL-skript och Spark-jobb finns i git. Alla definitioner av mellanpooler kommer att lagras i git som ARM-mallar. Dedikerade SQL-pooler (scheman, tabeller, vyer osv.) hanteras med databas projekt med stöd för CI/CD.

## <a name="pipelines"></a>Pipelines

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>F: Hur gör jag för att se till att jag vet vilka autentiseringsuppgifter som används för att köra en pipeline? 

A: varje aktivitet i en Synapse-pipeline körs med den angivna autentiseringsuppgiften i den länkade tjänsten.

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>F: är SSIS IRs som stöds i Synapse integrera?

A: inte just nu. 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>F: Hur gör jag för att migrera befintliga pipelines från Azure Data Factory till en Azure dataSynapses-arbetsyta?

A: vid det här tillfället måste du manuellt återskapa Azure Data Factory pipelines och relaterade artefakter genom att exportera JSON från den ursprungliga pipelinen och importera den till din Synapse-arbetsyta.

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>F: Vad är skillnaden mellan Apache Spark för Synapse och Apache Spark?

A: Apache Spark för Synapse är Apache Spark med stöd för integreringar med andra tjänster (AAD, AzureML osv.) och ytterligare bibliotek (mssparktuils, Hummingbird) och i förväg justerade prestanda konfigurationer.

Alla arbets belastningar som för närvarande körs på Apache Spark körs på Apache Spark för Azure-Synapse utan ändring. 

### <a name="q-what-versions-of-spark-are-available"></a>F: vilka versioner av Spark är tillgängliga?

A: Azure Synapse Apache Spark fullständigt stöd för Spark 2,4. En fullständig lista över kärn komponenter och version som stöds för närvarande finns i [Apache Spark versions stöd](./spark/apache-spark-version-support.md).

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>F: finns det en motsvarighet till DButils i Azure Synapse Spark?

A: Ja, Azure Synapse Apache Spark innehåller **mssparkutils** -biblioteket. Fullständig dokumentation av verktyget finns i [Introduktion till Microsoft Spark-verktyg](./spark/microsoft-spark-utilities.md).

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>F: Hur gör jag för att ange sessionsnycklar i Apache Spark?

A: om du vill ange sessionsnycklar använder du%% konfigurera Magic available. En omstart av sessionen krävs för att parametrarna ska börja gälla. 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>F: Hur gör jag för att ange kluster nivå parametrar i en server lös Spark-pool?

A: om du vill ange kluster nivå parametrar kan du ange en spark. conf-fil för Spark-poolen. Den här poolen kommer sedan att respektera parametrarna som infaller i konfigurations filen. 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>F: kan jag köra ett Spark-kluster med flera användare i Azure Synapse Analytics?
 
A: Azure Synapse tillhandahåller syftes inbyggda motorer för vissa användnings fall. Apache Spark för Synapse är utformad som en jobb tjänst och inte en kluster modell. Det finns två scenarier där personer ber om en kluster modell med flera användare.

**Scenario #1: många användare som har åtkomst till ett kluster för att betjäna data i BI-syfte.**

Det enklaste sättet att utföra den här uppgiften är att laga data med Spark och sedan dra nytta av funktionerna i Synapse SQL för att de ska kunna ansluta Power BI till dessa data uppsättningar.

**Scenario #2: ha flera utvecklare på ett enda kluster för att spara pengar.**
 
För att uppfylla det här scenariot bör du ge varje utvecklare en server lös Spark-pool som är inställd på att använda ett litet antal Spark-resurser. Eftersom Server lös Spark-pooler inte kostar något, tills de aktivt används minimerar kostnaden när det finns flera utvecklare. Poolerna delar metadata (Spark-tabeller) så att de enkelt kan arbeta med varandra.

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>F: Hur gör jag för att inkludera, hantera och installera bibliotek?

A: du kan installera externa paket via en requirements.txt-fil när du skapar Spark-poolen, från arbets ytan Synapse eller från Azure Portal. Se [Hantera bibliotek för Apache Spark i Azure Synapse Analytics](./spark/apache-spark-azure-portal-add-libraries.md).

## <a name="dedicated-sql-pools"></a>Dedikerade SQL-pooler

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>F: vilka är de funktionella skillnaderna mellan dedikerade SQL-pooler och pooler utan Server?

S: du hittar en fullständig lista över skillnader i [skillnaderna i T-SQL-funktionen i SYNAPSE SQL](./sql/overview-features.md).

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>F: nu är Azure Synapse GA, Hur flyttar jag mina dedikerade SQL-pooler som tidigare var fristående till Azure Synapse? 

A: det finns ingen "flytta" eller "migrering". Du kan välja att aktivera nya funktioner för arbets ytor på dina befintliga pooler. Om du gör det sker inga ändringar, men du kommer att kunna använda nya funktioner som Synapse Studio, Spark och Server lös SQL-pooler.

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>F: Vad är standard distributionen av dedikerade SQL-pooler nu? 

A: som standard kommer alla nya dedikerade SQL-pooler att distribueras till en arbets yta. men om du behöver kan du fortfarande skapa en dedikerad SQL-pool (tidigare SQL DW) i en fristående form faktor. 

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Azure Synapse Analytics](get-started.md)
* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använda en serverlös SQL-pool](quickstart-sql-on-demand.md)
