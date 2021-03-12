---
title: Exportera avidentifierade data (för hands version) för Azure API för FHIR
description: Den här artikeln beskriver hur du konfigurerar och använder avidentifierad export
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: matjazl
ms.openlocfilehash: 60a2a41a8005e8bd0fbc313c9a177d54df6dac5e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020362"
---
# <a name="exporting-de-identified-data-preview"></a>Exportera avidentifierade data (för hands version)

> [!Note] 
> Resultat när den avidentifierade exporten används varierar beroende på faktorer som till exempel data som inte har angetts och funktioner som valts av kunden. Microsoft kan inte utvärdera de avidentifierade export utmatningarna eller fastställa om det är möjligt att godkänna kundernas användnings fall och krav. Den avidentifierade exporten är inte garanterad för att uppfylla särskilda juridiska, regler eller krav på efterlevnad.

Kommandot $export kan också användas för att exportera de data som identifieras från FHIR-servern. Den använder anonymisering-motorn från [FHIR-verktyg för anonymisering](https://github.com/microsoft/FHIR-Tools-for-Anonymization)och tar anonymisering konfigurations information i frågeparametrar. Du kan skapa en egen anonymisering-konfigurationsfil eller använda [exempel konfigurations filen](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) för HIPAA Safe Harbor-metoden som en start punkt. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> Just nu stöder Azure API för FHIR endast avidentifierad export på system nivå ($export).

|Frågeparameter            | Exempel |Valfrihet| Beskrivning|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.jspå|Krävs för avidentifierad export |Namn på konfigurations filen. Se konfigurations fil formatet [här](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format). Den här filen bör behållas i en behållare med namnet **anonymisering** inom samma Azure Storage-konto som har kon figurer ATS som export plats. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Valfritt för avidentifierad export|Detta är etag i konfigurations filen. Du kan hämta etag med Azure Storage Explorer från BLOB-egenskapen|

> [!IMPORTANT]
> Både rå export och de avidentifierade export skrivningar till samma Azure Storage-konto som anges som en del av export konfigurationen. Vi rekommenderar att du använder olika behållare som motsvarar olika avidentifierade konfigurations-och hanterings användar åtkomst på behållar nivån.