---
title: Azure Stream Analytics för hands versions funktioner
description: Den här artikeln innehåller Azure Stream Analytics funktioner som för närvarande finns i för hands version.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 8/07/2020
ms.openlocfilehash: d07a2364cbd2b63ef9e38387b6e915f7e63676ae
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012163"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics för hands versions funktioner

I den här artikeln sammanfattas alla funktioner som för hands version för Azure Stream Analytics. Att använda för hands versions funktionerna i en produktions miljö rekommenderas inte.

## <a name="public-previews"></a>Allmänt tillgängliga förhandsversioner

Följande funktioner finns i offentlig för hands version. Du kan dra nytta av dessa funktioner idag, men Använd dem inte i produktions miljön.

### <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>Autentisera för att SQL Database utdata med hanterade identiteter (för hands version)

Azure Stream Analytics stöder [autentisering med hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för Azure SQL Database utgående mottagare. Hanterade identiteter eliminerar begränsningar för användarbaserade autentiseringsmetoder, t. ex. behovet av att autentisera på grund av lösen ords ändringar. 

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Resultat av hög prestanda i real tid med anpassade ML-modeller som hanteras av Azure Machine Learning

Azure Stream Analytics stöder real tids resultat i real tid genom att använda anpassade Machine Learning modeller som hanteras av Azure Machine Learning och som finns i Azure Kubernetes service (AKS) eller Azure Container Instances (ACI), med hjälp av ett arbets flöde som inte kräver att du skriver kod. [Registrera dig](https://aka.ms/asapreview1) för för hands version

### <a name="c-custom-de-serializers"></a>De anpassade C#-deserialiserarna
Utvecklare kan dra nytta av kraften i Azure Stream Analytics att bearbeta data i protobuf, XML eller något anpassat format. Du kan implementera [anpassade deserialiserare](custom-deserializer-examples.md) i C#, som sedan kan användas för deserialisering av händelser som tagits emot av Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Utökning med C# anpassad kod

Utvecklare som skapar Stream Analytics moduler i molnet eller på IoT Edge kan skriva eller återanvända anpassade C#-funktioner och anropa dem direkt i frågan via [användardefinierade funktioner](stream-analytics-edge-csharp-udf-methods.md).

### <a name="debug-query-steps-in-visual-studio"></a>Felsöka fråge steg i Visual Studio

Du kan enkelt förhandsgranska den mellanliggande rad uppsättningen i ett data diagram när du gör en lokal testning i Azure Stream Analytics verktyg för Visual Studio. 


### <a name="live-data-testing-in-visual-studio"></a>Live data testning i Visual Studio

Visual Studio-verktyg för Azure Stream Analytics förbättra den lokala testnings funktionen som gör att du kan testa dina frågor mot Live händelse strömmar från moln källor som Händelsehubben eller IoT Hub. Lär dig hur du [testar Live data lokalt med hjälp av Azure Stream Analytics verktyg för Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code för Azure Stream Analytics

Azure Stream Analytics-jobb kan skapas i Visual Studio Code. Se kursen för att [komma igång med vs Code](./quick-create-visual-studio-code.md).

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Lokal testning med real tids data i Visual Studio Code

Du kan testa dina frågor mot Real tids data på den lokala datorn innan du skickar jobbet till Azure. Varje test upprepning tar mindre än två till tre sekunder i genomsnitt, vilket resulterar i en mycket effektiv utvecklings process.

## <a name="other-previews"></a>Andra för hands versionerna

Följande funktioner är också tillgängliga i för hands version på begäran.

### <a name="support-for-azure-stack"></a>Stöd för Azure Stack
Den här funktionen är aktive rad på Azure IoT Edge Runtime och utnyttjar anpassade Azure Stack funktioner, till exempel inbyggt stöd för lokala indata och utdata som körs på Azure Stack (till exempel Event Hubs, IoT Hub, Blob Storage). Med den här nya integrationen kan du skapa hybrid arkitekturer som kan analysera dina data nära var de skapas, minska svars tiden och maximera insikterna.
Med den här funktionen kan du skapa hybrid arkitekturer som kan analysera dina data nära var de skapas, minska svars tiden och maximera insikterna. Du måste [Registrera](https://aka.ms/asapreview1) dig för den här för hands versionen.