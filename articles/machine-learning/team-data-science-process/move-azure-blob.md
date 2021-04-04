---
title: Flytta data till och från Azure Blob Storage – team data science process
description: Flytta data till och från Azure Blob Storage med hjälp av Azure Storage Explorer, AzCopy, python och SSIS.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5148084fa22266b1352046c7d8737b9804c5f4d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93311850"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Flytta data till och från Azure Blob Storage

Team data science-processen kräver att data matas in eller läses in i en mängd olika lagrings miljöer som ska bearbetas eller analyseras på det sätt som är lämpligast i varje steg i processen.

## <a name="different-technologies-for-moving-data"></a>Olika tekniker för att flytta data

I följande artiklar beskrivs hur du flyttar data till och från Azure Blob Storage med hjälp av olika tekniker.

* [Azure Storage-Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](../../storage/common/storage-use-azcopy-v10.md)
* [Python](../../storage/blobs/storage-quickstart-blobs-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Vilken metod är bäst för dig beror på ditt scenario. [Scenarierna för avancerad analys i Azure Machine Learning](plan-sample-scenarios.md) artikel hjälper dig att avgöra vilka resurser du behöver för en mängd olika data vetenskaps arbets flöden som används i avancerad analys process.

> [!NOTE]
> En fullständig introduktion till Azure Blob Storage finns i grunderna för [Azure Blob](../../storage/blobs/storage-quickstart-blobs-dotnet.md) och [Azure Blob service](/rest/api/storageservices/Blob-Service-Concepts).
> 
> 

## <a name="using-azure-data-factory"></a>Använda Azure Data Factory

Alternativt kan du använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) för att: 

* Skapa och Schemalägg en pipeline som laddar ned data från Azure Blob Storage, 
* skicka den till en publicerad Azure Machine Learning-webbtjänst, 
* ta emot förutsägelse analys resultat och 
* överför resultaten till Storage. 

Mer information finns i [skapa förutsägande pipelines med hjälp av Azure Data Factory och Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har en Azure-prenumeration, ett lagrings konto och motsvarande lagrings nyckel för det kontot. Innan du laddar upp/laddar ned data måste du känna till Azure Storage kontots namn och konto nyckel.

* Information om hur du konfigurerar en Azure-prenumeration finns i [den kostnads fria utvärderings perioden på en månad](https://azure.microsoft.com/pricing/free-trial/).
* Anvisningar om hur du skapar ett lagrings konto och hämtar information om konton och nycklar finns i [om Azure Storage-konton](../../storage/common/storage-account-create.md).