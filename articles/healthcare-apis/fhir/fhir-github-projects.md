---
title: Relaterade GitHub-projekt för Azure API för FHIR
description: Visa en lista över alla GitHub-databaser (Open Source) för Azure API för FHIR.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105097654"
---
# <a name="related-github-projects"></a>Relaterade GitHub-projekt

Vi har många projekt med öppen källkod på GitHub som innehåller käll koden och instruktioner för att distribuera tjänster för olika användnings områden. Du är alltid välkommen att besöka våra GitHub-databaser för att lära dig och experimentera med våra funktioner och produkter. 

## <a name="fhir-server"></a>FHIR-Server
* [Microsoft/FHIR-Server](https://github.com/microsoft/fhir-server/): FHIR-server med öppen källkod, som är grunden för Azure API för FHIR
* För att se de senaste versionerna, se [viktig information](https://github.com/microsoft/fhir-server/releases)
* [Microsoft/FHIR-Server-samples](https://github.com/microsoft/fhir-server-samples): en exempel miljö

## <a name="data-conversion--anonymization"></a>Data konvertering & anonymisering

#### <a name="fhir-converter"></a>FHIR-konverterare
* [Microsoft/FHIR – konverterare](https://github.com/microsoft/FHIR-Converter): ett konverterings verktyg som översätter äldre data format till FHIR
* Integrerat med Azure API för FHIR och FHIR Server för Azure i form av $convert-data åtgärd
* Pågående förbättringar i OSS och kontinuerlig integrering med FHIR-servrar
 
#### <a name="fhir-converter---vs-code-extension"></a>FHIR-konverterare – VS Code-tillägg
* [Microsoft/FHIR-tools-for-anonymisering](https://github.com/microsoft/FHIR-Tools-for-Anonymization): en uppsättning verktyg för att hjälpa till med data (i FHIR-format) anonymisering
* Integrerat med Azure API för FHIR samt FHIR-Server för Azure i form av "avidentifierad export"

#### <a name="fhir-tools-for-anonymization"></a>FHIR-verktyg för anonymisering
* [Microsoft/VSCode-azurehealthcareapis-tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools): ett vs Code-tillägg som innehåller en samling verktyg för att arbeta med Azure sjukvårds-API: er
* Lanserad till Visual Studio Marketplace
* Används för att redigera vätskor mallar som ska användas i FHIR-konverteraren

## <a name="iot-connector"></a>IoT-koppling

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integrering med IoT Hub och IoT Central
* [Microsoft/iomt-FHIR](https://github.com/microsoft/iomt-fhir): integration med IoT Hub eller IoT Central till FHIR med datanormalisering och FHIR konvertering av normaliserade data
* Normalisering: enhetens data information extraheras till ett gemensamt format för vidare bearbetning
* FHIR-konvertering: normaliserade och grupperade data mappas till FHIR. Observationer skapas eller uppdateras enligt konfigurerade mallar och länkas till enheten och patienten.
* [Verktyg som hjälper dig att bygga konversations kartan](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): visualisera mappnings konfigurationen för att normalisera enhetens indata och omvandla den till FHIR-resurserna. Utvecklare kan använda det här verktyget för att redigera och testa mappningar, enhets mappning och FHIR-mappning och exportera dem för att ladda upp till IoT-anslutningen i Azure Portal.

#### <a name="healthkit-and-fhir-integration"></a>Integrering av HealthKit och FHIR
* [Microsoft/HealthKit-on-FHIR](https://github.com/microsoft/healthkit-on-fhir): ett SWIFT-bibliotek som automatiserar exporten av Apple HealthKit-data till en FHIR-Server

 