---
title: Förstå enhets uppdatering för Azure IoT Hub-kompatibilitet | Microsoft Docs
description: Förstå hur enhets uppdatering för Azure IoT Hub mäter enhets uppdateringens efterlevnad.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101664003"
---
# <a name="device-update-compliance"></a>Enhets Uppdateringsefterlevnad

I enhets uppdatering för IoT Hub är efterlevnaden av hur många enheter som har installerat den senaste versions kompatibla uppdateringen. En enhet är kompatibel om den har installerat den senaste tillgängliga uppdateringen som är kompatibel för den. 

Överväg till exempel en instans av enhets uppdateringen med följande uppdateringar:

|Uppdaterings namn|Uppdatera version|Kompatibel enhets modell|
|-----------|--------------|-----------------------|
|Update1    |1.0    |Model1|
|Update2    |1.0    |Model2|
|Update3    |2.0    |Model1|

Anta att följande distributioner har skapats:

|Distributions namn    |Uppdaterings namn    |Mål grupp|
|-----------|--------------|-------------------|
|Deployment1    |Update1    |Group1|
|Deployment2    |Update2    |Group2|
|Deployment3    |Update3    |Group3|

Tänk nu på följande enheter med grupp medlemskap och installerade versioner:

|DeviceId   |Enhets modell   |Installerad uppdaterings version|Group |Efterlevnad|
|-----------|--------------|-----------------------|-----|---------|
|Device1    |Model1 |1.0    |Group1 |Nya uppdateringar är tillgängliga</span>|
|Device2    |Model1 |2.0    |Group3 |Vid senaste uppdateringen|
|Device3    |Model2 |1.0    |Group2 |Vid senaste uppdateringen|
|Device4    |Model1 |1.0    |Group3 |Uppdatering pågår|

Device1 och Device4 är inte kompatibla eftersom de har version 1,0 installerat även om det finns en senare versions uppdatering, Update3, kompatibel för deras modell i enhets uppdaterings instansen. Device2 och Device3 är båda kompatibla eftersom de har de senaste versions uppdateringar som är kompatibla med de installerade modellerna.

Efterlevnaden tar inte hänsyn till om en uppdatering distribueras till en enhets grupp eller inte. det letar efter uppdateringar som publicerats till enhets uppdateringen. I exemplet ovan, även om Device1 har installerat uppdateringen distribuerad till den, betraktas den som icke-kompatibel. Device1 kommer att fortsätta anses vara inkompatibel till den installerar Update3. Status för efterlevnad kan hjälpa dig att identifiera om nya distributioner behövs. 

Som det visas ovan finns tre efterlevnadsprinciper i enhets uppdatering för IoT Hub:

*   Den **senaste uppdateringen** – enheten har installerat den senaste versionen som är kompatibel med uppdateringen publicerad till enhets uppdateringen.
*   **Uppdatering** pågår – en aktiv distribution håller på att leverera den högsta versions kompatibla uppdateringen till enheten.
*   **Nya uppdateringar är tillgängliga** – en enhet har ännu inte installerat den senaste versionen som är kompatibel och inte finns i en aktiv distribution för den uppdateringen.
