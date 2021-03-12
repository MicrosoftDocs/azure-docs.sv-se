---
title: Uppgradera Azure Service Fabric Cluster-versionen
description: Lär dig mer om kluster versioner i Azure Service Fabric, inklusive en länk till de senaste versionerna från Service Fabric teamets blogg.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: 3e859a04ffb0b885aab0f31e83afad8380cbcc95
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010209"
---
# <a name="upgrade-your-azure-service-fabric-cluster-version"></a>Uppgradera Azure Service Fabric Cluster-versionen

Kontrol lera att klustret alltid kör en version av Azure Service Fabric som stöds. I minst 60 dagar efter att vi tillkännaget lanseringen av en ny version av Service Fabric, avslutas stöd för den tidigare versionen. Du hittar meddelanden om nya versioner i [Service Fabric teamets blogg](https://azure.microsoft.com/updates/?product=service-fabric).

För varje version av Service Fabric runtime kan du använda de angivna eller äldre versionerna av SDK/NuGet-paketen. Nyare versioner av paketen kanske inte kan rikta in sig på äldre kluster. Äldre kluster kan ha funktions-eller protokoll ändringar som inte stöds i de nyare paket miljöerna.

I följande artiklar finns mer information om hur du håller klustret med en Service Fabric-version som stöds:

- [Uppgradera ett Azure Service Fabric-kluster](service-fabric-cluster-upgrade.md)
- [Uppgradera den Service Fabric version som körs på det fristående Windows Server-klustret](service-fabric-cluster-upgrade-windows-server.md)

## <a name="unsupported-versions"></a>Versioner som inte stöds

### <a name="upgrade-alert-for-versions-between-57-and-6363"></a>Uppgraderings avisering för versioner mellan 5,7 och 6.3.63. *

Azure-infrastrukturen har gjort en ändring som kan påverka Service Fabric kunder för att förbättra säkerheten och tillgängligheten. Den här ändringen påverkar alla Service Fabric kluster som kör version 5,7 till 6,3.

En uppdatering av Service Fabric runtime är tillgänglig för alla Service Fabric-versioner som stöds i alla regioner. Uppgradera till en av de senaste versionerna som stöds den 19 januari 2021 för att undvika avbrott i tjänsten.

Om du har en Support plan och behöver teknisk hjälp kan du kontakta support kanalerna för Azure. Öppna en support förfrågan för Azure Service Fabric och omnämnande den här kontexten i support ärendet.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Om du inte uppgraderar till en version som stöds

Azure Service Fabric-kluster som körs på versioner från 5,7 till 6.3.63. * blir otillgängliga om du inte har uppgraderat den 19 januari 2021.

#### <a name="required-action"></a>Nödvändig åtgärd

Uppgradera till en Service Fabric-version som stöds för att förhindra drift avbrott eller förlust av funktioner som är relaterade till den här ändringen. Se till att klustren kör minst följande versioner för att förhindra problem i miljön.

> [!Note]
> **Alla utgivna versioner av 7,2 innehåller nödvändiga ändringar**.
  
  | Operativsystem | Nuvarande Service Fabric körning i klustret | Version för CU/korrigering |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Ubuntu 16 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16,04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16,04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18,04 | 7,2. * | 7,2. * |

### <a name="upgrade-alert-for-versions-later-than-63"></a>Uppgradera aviseringen för versioner senare än 6,3

Azure-infrastrukturen har gjort en ändring som kan påverka Service Fabric kunder för att förbättra säkerheten och tillgängligheten. Den här ändringen påverkar alla Service Fabric kluster som använder [öppet nätverks läge för behållare](./service-fabric-networking-modes.md#set-up-open-networking-mode) och kör versioner 6,3 till 7,0 eller inkompatibla versioner som stöds senare än 7,0. En uppdatering av Service Fabric runtime är tillgänglig för alla Service Fabric-versioner som stöds i alla regioner.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Om du inte uppgraderar till en version som stöds

Azure Service Fabric-kluster som körs på oförändrade versioner senare än 6,3 kommer att uppleva avbrott i funktioner eller tjänst avbrott om de inte uppgraderas till en version som stöds senast 19 januari 2021.
  
  - **För kluster som kör en version av Service Fabric som är större än 6,3 och som inte använder funktionen öppna nätverk**, är klustret kvar.

 - **För kluster som kör en version av Service Fabric som är större än 6,3 och använder [funktionen öppna nätverksfunktioner för behållare](./service-fabric-networking-modes.md#set-up-open-networking-mode)** , kan klustret bli otillgängligt och upphör att fungera, vilket kan orsaka avbrott i tjänsten för dina arbets belastningar.
 
 -   **För kluster som kör [Windows-versioner mellan 7.0.457 och 7.0.466 (båda versionerna som ingår)](#supported-version-names) och Windows OS har funktionen Windows-behållare aktive rad. Obs! Linux-versionerna 7.0.457, 7.0.464 och 7.0.465 påverkas inte**.
    - **Påverkan**: klustret upphör att fungera, vilket kan orsaka avbrott i tjänsten för dina arbets belastningar.
    
#### <a name="required-action"></a>Nödvändig åtgärd

Säkerställ att klustren kör någon av följande versioner för att förhindra drift avbrott eller förlust av funktioner.

Versioner av Service Fabric i tabellen innehåller nödvändiga ändringar för att förhindra att funktioner går förlorade. Kontrol lera att du använder någon av dessa versioner.  

> [!Note]
> **Azure Service Fabric kluster som körs på version 6,5, måste utföra flera uppgraderingar samtidigt innan infrastucuture ändrar för att undvika förlust av funktioner i klustret**. 
>   -   1. Uppgradera till 7.0.466. **Kluster som kör Windows-operativsystemet som har funktionen Windows-behållare aktiverat får inte finnas i den här mellanliggande versionen. De måste utföra nästa steg (II) nedan.  Uppgradera för att vara på ett säkrare och kompatibelt version för att undvika avbrott i tjänsten**
>   -   2. Uppgradera till senaste inklagomåls versioner i 7,0 * Release (7.0.478) eller någon av de högre versionerna i listan nedan.


> [!Note]
> **Alla versions versioner av 7,2 innehåller nödvändiga ändringar**.

 | Operativsystem | Nuvarande Service Fabric körning i klustret | Version för CU/korrigering |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Linux Ubuntu 16,04 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16,04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16,04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18,04 | 7,2. * | 7,2. * |

## <a name="supported-versions"></a>Versioner som stöds

I följande tabell visas de versioner av Service Fabric och deras support slutdatum.

| Service Fabric körning i klustret | Kan uppgradera direkt från kluster version |Kompatibel SDK-eller NuGet-paket version | Slut på support |
| --- | --- |--- | --- |
| Alla kluster versioner före 5.3.121 | 5.1.158.* |Mindre än eller lika med version 2,3 |20 januari 2017 |
| 5,3. * | 5.1.158.* |Mindre än eller lika med version 2,3 |24 februari 2017 |
| 5,4. * | 5.1.158.* |Mindre än eller lika med version 2,4 |10 maj 2017       |
| 5,5. * | 5.4.164.* |Mindre än eller lika med version 2,5 |10 augusti 2017    |
| 5,6. * | 5.4.164.* |Mindre än eller lika med version 2,6 |13 oktober 2017   |
| 5,7. * | 5.4.164.* |Mindre än eller lika med version 2,7 |15 december 2017  |
| 6,0. * | 5.6.205.* |Mindre än eller lika med version 2,8 |30 mars 2018     |
| 6,1. * | 5.7.221.* |Mindre än eller lika med version 3,0 |15 juli 2018      |
| 6,2. * | 6.0.232.* |Mindre än eller lika med version 3,1 |26 oktober 2018   |
| 6,3. * | 6.1.480.* |Mindre än eller lika med version 3,2 |31 mars 2019  |
| 6,4. * | 6.2.301.* |Mindre än eller lika med version 3,3 |15 september 2019 |
| 6,5. * | 6.4.617.* |Mindre än eller lika med version 3,4 |Den 1 augusti 2020 |
| 7.0.466.* | 6.4.664.* |Mindre än eller lika med version 4,0|31 januari 2021  |
| 7.0.466.* | 6,5. * |Mindre än eller lika med version 4,0|31 januari 2021 |
| 7.0.470.* | 7.0.466.* |Mindre än eller lika med version 4,0 |31 januari 2021  |
| 7.0.472.* | 7.0.466.* |Mindre än eller lika med version 4,0 |31 januari 2021  |
| 7.0.478.* | 7.0.466.* |Mindre än eller lika med version 4,0 |31 januari 2021  |
| 7.1.409.* | 7.0.466.* |Mindre än eller lika med version 4,1 |31 juli 2021 |
| 7.1.417.* | 7.0.466.* |Mindre än eller lika med version 4,1 |31 juli 2021 |
| 7.1.428.* | 7.0.466.* |Mindre än eller lika med version 4,1 |31 juli 2021 |
| 7.1.456.* | 7.0.466.* |Mindre än eller lika med version 4,1 |31 juli 2021 |
| 7.1.458.* | 7.0.466.* |Mindre än eller lika med version 4,1 |31 juli 2021 |
| 7.1.459.* | 7.0.466.* |Mindre än eller lika med version 4,1 |31 juli 2021 |
| 7.1.503.* | 7.0.466.* |Mindre än eller lika med version 4,1 |31 juli 2021 |
| 7.1.510.* | 7.0.466.* |Mindre än eller lika med version 4,1 |31 juli 2021 |
| 7.2.413.* | 7.0.470.* |Mindre än eller lika med version 4,2 |Aktuell version, så inget slutdatum |
| 7.2.432.* | 7.0.470.* |Mindre än eller lika med version 4,2 |Aktuell version, så inget slutdatum |
| 7.2.433.* | 7.0.470.* |Mindre än eller lika med version 4,2 |Aktuell version, så inget slutdatum |
| 7.2.445.* | 7.0.470.* |Mindre än eller lika med version 4,2 |Aktuell version, så inget slutdatum |
| 7.2.452.* | 7.0.470.* |Mindre än eller lika med version 4,2 |Aktuell version, så inget slutdatum |
| 7.2.457.* | 7.0.470.* |Mindre än eller lika med version 4,2 |Aktuell version, så inget slutdatum |
| 7.2.477.* | 7.0.478.* |Mindre än eller lika med version 4,2 |Aktuell version, så inget slutdatum |

## <a name="supported-operating-systems"></a>Operativsystem som stöds

I följande tabell visas de operativ system som stöds för de Service Fabric-versioner som stöds.

| Operativsystem | Tidigaste Service Fabric version som stöds |
| --- | --- |
| Windows Server 2012 R2 | Alla versioner |
| Windows Server 2016 | Alla versioner |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16,04 | 6.0 |
| Linux Ubuntu 18,04 | 7.1 |

## <a name="supported-version-names"></a>Versions namn som stöds

I följande tabell visas versions namnen för Service Fabric och deras motsvarande versions nummer.

| Versions namn | Windows-versions nummer | Linux-versions nummer |
| --- | --- | --- |
| 5,3 RTO | 5.3.121.9494 | Inte tillämpligt|
| 5,3 CU1 | 5.3.204.9494 | Inte tillämpligt|
| 5,3 CU2 | 5.3.301.9590 | Inte tillämpligt|
| 5,3 CU3 | 5.3.311.9590 | Inte tillämpligt|
| 5,4 CU2 | 5.4.164.9494 | Inte tillämpligt|
| 5,5 CU1 | 5.5.216.0    | Inte tillämpligt|
| 5,5 CU2 | 5.5.219.0 | Inte tillämpligt|
| 5,5 CU3 | 5.5.227.0 | Inte tillämpligt|
| 5,5 CU4 | 5.5.232.0 | Inte tillämpligt|
| 5,6 RTO | 5.6.204.9494 | Inte tillämpligt|
| 5,6 CU2 | 5.6.210.9494 | Inte tillämpligt|
| 5,6 CU3 | 5.6.220.9494 | Inte tillämpligt|
| 5,7 RTO | 5.7.198.9494 | Inte tillämpligt|
| 5,7 CU4 | 5.7.221.9494 | Inte tillämpligt|
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | Inte tillämpligt|
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 |
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | Inte tillämpligt|
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | Inte tillämpligt|
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | Inte tillämpligt|
| 6,5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7,0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7,0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7,0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7,0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7,0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7,1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7,1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7,1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7,1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7,1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7,1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7,1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7,1 CU10 | 7.1.510.9590 | NA |
| 7,2 RTO | 7.2.413.9590 | NA |
| 7,2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7,2 CU3 | 7.2.433.9590 | NA |
| 7,2 CU4 | 7.2.445.9590 | 7.2.447.1 |
| 7,2 CU5 | 7.2.452.9590 | 7.2.454.1 |
| 7,2 CU6 | 7.2.457.9590 | 7.2.456.1 |
| 7,2 CU7 | 7.2.477.9590 | 7.2.476.1 |