---
title: Funktioner i Azure Communication Services UI Framework
titleSuffix: An Azure Communication Services conceptual document
description: Lär dig mer om funktioner i UI Framework
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5b1aab8b38614249d6b502044b5c4c8170f46b3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492225"
---
# <a name="ui-framework-capabilities"></a>Funktioner i UI Framework

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Med hjälp av Azure Communication Services UI Framework kan du skapa kommunikations upplevelser med en uppsättning återanvändbara komponenter. Dessa komponenter finns i två varianter: **bas** komponenter är de mest grundläggande Bygg stenarna i användar gränssnittet, medan kombinationer av dessa bas komponenter kallas **sammansatta** komponenter.

## <a name="ui-framework-composite-components"></a>Sammansatta GRÄNSSNITTs Ramverks komponenter

| Sammansatt               | Beskrivning                                               | Webb   | Android | iOS   |
|-------------------------|-----------------------------------------------------------|-------|---------|-------|
| Grupp som anropar sammansatt | Ingångs samtal med låg vikt för röst och video för Azure Communication Services som anropar med hjälp av användar gränssnittet i Fluent UI design. Stöder grupp som anropar med grupp-ID för Azure Communication Services. Den sammansatta anslutningen gör att en-till-en-anrop kan användas genom att referera till en Azure Communication Services-identitet eller ett telefonnummer för PSTN med hjälp av ett telefonnummer som har införskaffats via Azure.                                    | React |  |  |
| Gruppera chatt sammansatt    | Lätt att chatta med Azure Communication Services med hjälp av design resurser för Fluent UI. Den här upplevelsen fokuserar på att leverera en enkel Chat-klient som kan ansluta till Azure Communication Services-trådar. Det gör att användare kan skicka meddelanden och se mottagna meddelanden med Skriv indikatorer och Läs kvitton. Den skalar från 1:1 för att gruppera chatt scenarier. Har stöd för en enda chatt-tråd.                         | React |  |  |

## <a name="ui-framework-base-components"></a>Bas komponenter för UI Framework

| Komponent             | Beskrivning                                                                                                                                                                                                                                                                        | Webb   | Android | iOS |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|---------|-----|
| Anrops leverantör    | Core initierar komponent för anrop. Nödvändig komponent för att initiera andra komponenter ovanpå den. Hanterar kärn logik för att initiera anrops klienten med hjälp av Azure Communication Services-åtkomsttoken. Stöder grupp koppling. | React | Saknas     | Saknas |
| Medie kontroller   | Tillåter användare att hantera det aktuella anropet genom att växla av, aktivera/inaktivera video och avsluta samtalet.                                                                                                                                                              | React | Saknas     | Saknas |
| Medie Galleri   | Presentera alla samtals deltagare i ett enda galleri. Galleriet stöder både video-och statiska deltagare. För video aktiverade deltagare återges video.                                                                                                                | React | Saknas     | Saknas |
| Mikrofon inställningar | Välj mikrofonen som ska användas för uppringning. Den här kontrollen kan användas före och under ett anrop för att välja mikrofon enheten.                                                                                                                                               | React | Saknas     | Saknas |
| Kamera inställningar     | Välj den kamera som ska användas för video samtal. Den här kontrollen kan användas före och under ett anrop för att välja video enheten.                                                                                                                                             | React | Saknas     | Saknas |
| Enhets inställningar     | Kombinerar mikrofon-och kamera inställningar till en enda komponent                                                                                                 | React | Saknas     | Saknas |
| Chat-Provider       | Core initierar komponent för chatt. Nödvändig komponent för att initiera andra komponenter ovanpå den. Hanterar kärn logik för att initiera chatt-klienten med en Azure Communication Services-åtkomsttoken och den tråd som den kommer att ansluta till.                                     | React | Saknas     | Saknas |
| Skicka ruta          | Ingående komponent som gör att användarna kan skicka meddelanden till chatt-tråden. Indatamängden stöder text, hyperlänkar, emojis och andra Unicode-tecken, inklusive andra alfabet.                                                                                                                         | React | Saknas     | Saknas |
| Chat-tråd           | Tråd komponent som visar användaren både mottagna och skickade meddelanden med sina avsändar uppgifter. Tråden stöder Skriv indikatorer och Läs kvitton. Du kan bläddra bland dessa trådar för att granska chatt historiken.
| Deltagar lista      | Visa alla deltagare i anropet eller chatt-tråden som en lista.  | React | Saknas     | Saknas |

## <a name="ui-framework-capabilities"></a>Funktioner i UI Framework

| Funktion                                                             | Grupp som anropar sammansatt | Gruppera chatt sammansatt | Bas komponenter |
|---------------------------------------------------------------------|-------------------------|----------------------|-----------------|
| Delta i team möte                                                  |                         |                      |           
| Delta i team live-evenemang                                               |                         |                      | 
| Starta VoIP-anrop till Team användare                                       |                         |                      | 
| Gå med i en team Mötes chatt                                           |                         |                      |            
| Delta i Azure Communication Services-anrop med grupp-ID                | ✔                      |                      | ✔
| Starta ett VoIP-anrop till en eller flera Azure Communication Services-användare |                         |                      |           
| Delta i en Azure Communication Services-chatt                    |                         | ✔                   | ✔
| Ring upp/Stäng av samtal                                                    | ✔                       |                      | ✔
| Video om/på-anrop                                                | ✔                       |                      | ✔
| Skärm delning                                                      | ✔                       |                      | ✔
| Deltagar Galleri                                                 | ✔                       |                      | ✔
| Mikrofon hantering                                               | ✔                       |                      | ✔
| Kamera hantering                                                   | ✔                       |                      | ✔
| Anropa lobbyn                                                          |                         |                      | ✔
| Skicka chatt meddelande                                                   |                         | ✔                   |            
| Ta emot chatt meddelande                                                |                         | ✔                   | ✔
| Skriv indikatorer                                                   |                         | ✔                   | ✔
| Läs kvitto                                                        |                         | ✔                   | ✔
| Deltagar lista                                                    |                         |                      | ✔


## <a name="customization-support"></a>Anpassnings stöd

| Komponent typ            | Teman     | Layout                                                              | Data modeller |
|---------------------------|------------|---------------------------------------------------------------------|-------------|
| Sammansatt komponent       |     Saknas    | Saknas                                                                 |     Saknas     |
| Bas komponent            |     Ej tillämpligt    | Layout för komponenter kan ändras med extern formatering         |     Ej tillämpligt     |


## <a name="platform-support"></a>Plattformssupport

| SDK    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
|--------|--------------------|----------------------|----------|----------|----------|------------|
| UI SDK | Chrome \* , ny kant | Chrome \* , Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*Observera att den senaste versionen av Chrome stöds förutom de tidigare två versionerna.

\*\*Observera att Safari-versionerna 13.1 + stöds. Utgående video för Safari macOS stöds inte ännu, men stöds i iOS. Delning av utgående skärm stöds endast på Desktop iOS.
