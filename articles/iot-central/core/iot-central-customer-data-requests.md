---
title: Funktioner för begäran om kund data i Azure IoT Central | Microsoft Docs
description: I den här artikeln beskrivs hur du identifierar, tar bort och exporterar kund information i Azure IoT Central-programmet.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: dabcadea96f4ced5bdf73a35ef533e6d290595c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87001884"
---
# <a name="azure-iot-central-customer-data-request-features"></a>Azure IoT Central funktioner för kund data förfrågan

Azure IoT Central är en fullständigt hanterad Sakernas Internet-som-tjänst-lösning (IoT) som gör det enkelt att ansluta, övervaka och hantera dina IoT-tillgångar i skala, skapa djupgående insikter från dina IoT-data och vidta välgrundade åtgärder.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifiera kund information

Azure Active Directory Object-IDs används för att identifiera användare och tilldela roller. Azure IoT Central-portalen visar användar-e-postadresser för roll tilldelningar, men endast Azure Active Directory objekt-ID: t lagras, skickas e-postadressen dynamiskt från Azure Active Directory. Azure IoT Central-administratörer kan visa, exportera och ta bort program användare i avsnittet användar administration i ett Azure-IoT Central program.

I programmet kan e-postadresser konfigureras för att ta emot aviseringar. I det här fallet lagras e-postadresser i IoT Central och måste hanteras från konto administrations sidan i appen.

För enheter har Microsoft ingen information och har ingen åtkomst till data som gör det möjligt för enheter att använda användar korrelation. Många av de enheter som hanteras i Azure IoT Central är inte personliga enheter, till exempel en Vending dator eller kaffe tillverkare. Kunder kan dock överväga att vissa enheter ska vara personligt identifierbara och att de kan behålla sina egna till gångar eller inventerings spårnings system som knyter enheter till individer. Azure IoT Central hanterar och lagrar alla data som är kopplade till enheter som om de vore personliga data.

När du använder Microsoft Enterprise Services genererar Microsoft viss information, som kallas system genererade loggar. Dessa loggar utgör faktiska åtgärder som utförs i tjänsten och diagnostikdata som är relaterade till enskilda enheter och som inte är relaterade till användar aktiviteter. Azure IoT Central systemgenererade loggar kan inte nås eller exporteras av program administratörer.

## <a name="deleting-customer-data"></a>Tar bort kund information

Möjligheten att ta bort användar data tillhandahålls bara via sidan IoT Central administration. Program administratörer kan välja vilken användare som ska tas bort och välja **ta bort** i det övre högra hörnet i programmet för att ta bort posten. Program administratörer kan också ta bort enskilda konton som inte längre är associerade med programmet i fråga.

När en användare har tagits bort skickas inga ytterligare aviseringar till dem. Deras e-postadress måste dock tas bort individuellt från varje konfigurerad avisering.

## <a name="exporting-customer-data"></a>Exportera kund information

Möjligheten att exportera data tillhandahålls bara via sidan IoT Central administration. Kund information, inklusive tilldelade roller, kan väljas, kopieras och klistras in av en program administratör.

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

Mer information om konto administration, inklusive roll definitioner, finns i [så här administrerar du ditt program](howto-administer.md).
