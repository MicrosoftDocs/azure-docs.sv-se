---
title: Data inmatning & Automation
titleSuffix: Azure Machine Learning
description: Lär dig mer om-och nack delar med de tillgängliga data inmatnings alternativen för att träna dina Machine Learning-modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: a096375e32e3d8a6760da88fe5ec86a70d364aff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872103"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Alternativ för data inmatning för Azure Machine Learning arbets flöden

I den här artikeln får du lära dig om de tillgängliga alternativen för data inmatning i Azure Machine Learning. 

Välj mellan:
+ [Azure Data Factory](#azure-data-factory) pipelines, specifikt utformade för att extrahera, läsa in och transformera data

+ [Azure Machine Learning python SDK](#azure-machine-learning-python-sdk)och tillhandahålla en anpassad kod för data inmatnings uppgifter.

+ en kombination av båda

Data inmatning är den process i vilken ostrukturerade data extraheras från en eller flera källor och sedan förbereds för utbildning av maskin inlärnings modeller. Det är också tids krävande, särskilt om du gör det manuellt, och om du har stora mängder data från flera källor. Att automatisera den här ansträngningen frigör resurser och ser till att dina modeller använder de senaste och aktuella data.

## <a name="azure-data-factory"></a>Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) erbjuder inbyggt stöd för övervakning av data källor och utlösare för data inmatnings pipeliner.  

I följande tabell sammanfattas de tekniker och nack delar som används för att använda Azure Data Factory för arbets flöden för data inmatning.

|Fördelar|Nackdelar
---|---
Konstruerad för att extrahera, läsa in och transformera data.|Erbjuder för närvarande en begränsad uppsättning Azure Data Factory pipeline-uppgifter 
Gör att du kan skapa data drivna arbets flöden för att dirigera data förflyttning och omvandlingar i skala.|Dyrt att konstruera och underhålla. Mer information finns på [sidan med pris](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) information för Azure Data Factory.
Integrerat med olika Azure-verktyg som [Azure Databricks](../data-factory/transform-data-using-databricks-notebook.md) och [Azure Functions](../data-factory/control-flow-azure-function-activity.md) | Skript körs inte internt i stället beroende på separat beräkning för skript körningar 
Har inbyggt stöd för data inmatning som utlöses av data Källa| 
Processerna för data förberedelse och modell inlärning är separata.|
Härkomst-kapacitet för inbäddad data för Azure Data Factory data flöden|
Innehåller ett [användar gränssnitt](../data-factory/quickstart-create-data-factory-portal.md) med låg kod upplevelse för metoder som inte är skriptbaserade |

De här stegen och följande diagram illustrerar Azure Data Factory data inmatnings flödet.

1. Hämta data från sina källor
1. Transformera och spara data i en BLOB-behållare för utgående data som fungerar som data lagring för Azure Machine Learning
1. Med för beredda data lagrade, anropar Azure Data Factory pipelinen en utbildnings Machine Learning pipeline som tar emot de data som för bereddes för modell träning


    ![ADF-datainmatning](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Lär dig hur du skapar en pipeline för data inmatning för Machine Learning med [Azure Data Factory](how-to-data-ingest-adf.md).

## <a name="azure-machine-learning-python-sdk"></a>Azure Machine Learning python SDK 

Med [python SDK](/python/api/overview/azure/ml)kan du inkludera data inmatnings uppgifter i ett [Azure Machine Learning pipeline](./how-to-create-machine-learning-pipelines.md) -steg.

I följande tabell sammanfattas de tekniker och con för att använda SDK: n och ett ML-pipeline-steg för data inmatnings uppgifter.

Fördelar| Nackdelar
---|---
Konfigurera egna Python-skript | Har inte inbyggt stöd för att aktivera ändringar av data källan. Kräver Logic app-eller Azure Function-implementeringar
Förberedelse av data som en del av alla modell utbildnings körningar|Kräver utvecklings kunskaper för att skapa ett skript för data inmatning
Stöder skript för förberedelse av data på olika beräknings mål, inklusive [Azure Machine Learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed) |Tillhandahåller inget användar gränssnitt för att skapa inmatnings mekanismen

I följande diagram består Azure Machine Learning pipelinen av två steg: data inmatning och modell träning. Steget för data inmatning omfattar uppgifter som kan utföras med python-bibliotek och python SDK, till exempel för att extrahera data från lokala/webb-källor och data transformationer, som t. ex. värde som saknas Imputation. I övnings steget används för beredda data som indata till ditt utbildnings skript för att träna din Machine Learning-modell. 

![Azure pipeline + SDK-datainmatning](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Nästa steg

Följ de här instruktions artiklarna:
* [Bygg en pipeline för data inmatning med Azure Data Factory](how-to-data-ingest-adf.md)

* [Automatisera och hantera pipeliner för data inmatning med Azure-pipeliner](how-to-cicd-data-ingestion.md).