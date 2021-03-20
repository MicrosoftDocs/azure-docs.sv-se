---
title: Versionshistorik
titleSuffix: Microsoft Genomics
description: Versions historiken för uppdateringar av Microsoft Genomics python-klienten för korrigeringar och nya funktioner.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "76991090"
---
# <a name="version-release-history"></a>Versionshistorik
Microsoft Genomicss teamet uppdaterar regelbundet Microsoft Genomics python-klienten för korrigeringar och nya funktioner. 

## <a name="latest-release"></a>Senaste versionen
Den aktuella python-klienten är version 0.9.0. Den släpptes februari 6 2019 och stöder arbets flöden som körs med användas 3,5 och GATK4. Det stöder gVCF-utdata och kan acceptera ett valfritt argument för komprimering av utdata.


## <a name="release-history"></a>Versions historik 
Nya versioner av Microsoft Genomics python-klienten lanseras en gång per år. När nya versioner av Microsoft Genomics python-klienten släpps uppdateras en lista över korrigeringar och funktioner här. När nya versioner släpps bör tidigare versioner fortsätta att stödjas i minst 90 dagar. När tidigare versioner inte längre stöds visas den på den här sidan. 

### <a name="version-090"></a>Version 0.9.0
Version 0.9.0 innehåller stöd för komprimering av utdata. Detta motsvarar att köra `-bgzip` följt av `-tabix` i VCF-eller gvcf-utdata. Mer information finns i [vanliga frågor och svar](frequently-asked-questions-genomics.md). 

### <a name="version-081"></a>Version 0.8.1
Version 0.8.1 innehåller mindre fel korrigeringar.  

### <a name="version-080"></a>Version 0.8.0
Version 0.8.0 innehåller stöd för GATK4 och utmatning av gVCFs.  

### <a name="version-074"></a>Version 0.7.4
Version 0.7.4 innehåller stöd för att acceptera SAS-token i stället för konto nycklar i `config.txt` indatamängden. Mer information finns i [snabb start för indata SAS-token](quickstart-input-sas.md). 

### <a name="version-073"></a>Version 0.7.3
Version 0.7.3 innehåller mindre fel korrigeringar.

### <a name="version-072"></a>Version 0.7.2
Version 0.7.2 är den första versionen. Den släpptes november 1 2017.
