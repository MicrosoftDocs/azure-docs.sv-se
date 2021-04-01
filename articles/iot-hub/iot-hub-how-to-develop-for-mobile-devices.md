---
title: 'Utveckla för mobila enheter med Azure IoT SDK: er | Microsoft Docs'
description: 'Guide för utvecklare – lär dig hur du utvecklar för mobila enheter med hjälp av Azure IoT Hub SDK: er.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 63fade17d10189cbe8e775b2a859902f1c2bb004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151645"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Utveckla för mobila enheter med Azure IoT SDK: er

Saker i Sakernas Internet kan referera till en mängd olika enheter med varierande kapacitet: sensorer, mikrokontrollanter, smarta enheter, industriella gatewayer och till och med mobila enheter.  En mobil enhet kan vara en IoT-enhet där den skickar enhets-till-moln-telemetri och hanteras av molnet.  Det kan också vara enheten som kör ett Server dels tjänst program som hanterar andra IoT-enheter.  I båda fallen kan [Azure IoT Hub SDK](./iot-hub-devguide-sdks.md) : er användas för att utveckla program som fungerar för mobila enheter.  

## <a name="develop-for-native-ios-platform"></a>Utveckla för ursprunglig iOS-plattform

Azure IoT Hub SDK: er tillhandahåller inbyggd iOS-plattforms stöd via Azure IoT Hub C SDK.  Du kan se den som en iOS SDK som du kan använda i ditt Swift-eller mål C XCode-projekt.  Det finns två sätt att använda C SDK på iOS:

* Använd CocoaPod-biblioteken i XCode-projektet direkt.  
* Ladda ned käll koden för C SDK och bygg för iOS-plattformen enligt [build-instruktionen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) för MacOS.  

Azure IoT Hub C SDK skrivs i C99 för maximal portabilitet till olika plattformar.  Den här processen omfattar att skriva ett tunt införande-lager för de plattformsspecifika komponenter som du hittar här för [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  Funktionerna i C SDK kan utnyttjas på iOS-plattformen, inklusive Azure IoT Hub-primitiver som stöds och SDK-/regionsspecifika funktioner som till exempel principer för att försöka använda för nätverks tillförlitlighet.  Gränssnittet för iOS SDK liknar också gränssnittet för Azure IoT Hub C SDK.  

Dessa dokument går igenom hur du utvecklar ett enhets program eller ett tjänst program på en iOS-enhet:

* [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-ios.md)  
* [Skicka meddelanden från molnet till din enhet med IoT Hub](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Utveckla med Azure IoT Hub CocoaPod-bibliotek

Azure IoT Hub SDK: er släpper en uppsättning mål-C CocoaPod-bibliotek för iOS-utveckling.  För att se den senaste listan över CocoaPod-bibliotek, se [CocoaPods för Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  När de relevanta biblioteken har integrerats i XCode-projektet finns det två sätt att skriva IoT Hub relaterad kod:

* Mål C-funktion: om projektet är skrivet i mål-C kan du anropa API: er från Azure IoT Hub C SDK direkt.  Om ditt projekt är skrivet i SWIFT kan du anropa `@objc func` innan du skapar din funktion och fortsätta att skriva alla logikar som är relaterade till Azure IoT Hub med hjälp av c-eller mål-c-koden.  En uppsättning exempel som demonstrerar båda finns i [exempel lagrings platsen](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* Inkludera C-exempel: om du har skrivit ett C-enhets program kan du referera till det direkt i XCode-projektet:
    * Lägg till filen sample. c i XCode-projektet från XCode.  
    * Lägg till rubrik filen i beroendet.  En rubrik fil ingår i [exempel lagrings platsen](https://github.com/Azure-Samples/azure-iot-samples-ios) som ett exempel. Mer information finns på Apples dokumentations sida för [mål-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Utveckla för Android-plattform
Azure IoT Hub Java SDK stöder Android-plattformen.  Om du har testat den aktuella API-versionen går du till vår [plattforms support sida](iot-hub-device-sdk-platform-support.md) för den senaste uppdateringen.

Dessa dokument går igenom hur du utvecklar ett enhets program eller tjänst program på en Android-enhet med hjälp av Gradle och Android Studio:

* [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-android.md)  
* [Snabb start: styra en enhet](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Nästa steg

* [IoT Hub REST API referens](/rest/api/iothub/)
* [Käll kod för Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)