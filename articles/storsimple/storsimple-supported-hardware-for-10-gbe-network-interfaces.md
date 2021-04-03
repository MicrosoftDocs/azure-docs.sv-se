---
title: Maskin vara för StorSimple 10 GbE-gränssnitt | Microsoft Docs
description: Beskriver de anslutnings bara, kablar och växlar för de 10 GbE-nätverks gränssnitten som stöds på din StorSimple-enhet.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: fd30c7ec15eadd3d7945349a8aae4ac824bfdc7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954094"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Maskin vara som stöds för 10 GbE Network-gränssnitt på din StorSimple-enhet

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt
Den här artikeln innehåller information om extra maskin vara som fungerar med din Microsoft Azure StorSimple-enhet.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista över enheter som har testats av Microsoft
Microsoft har testat följande små (SFP) Sänd tagare, kablar och växlar för att säkerställa att de fungerar optimalt med enheter. (Följande tabeller kommer att uppdateras när ny maskin vara testas.)

### <a name="sfp-transceivers"></a>SFP +-Sänd tagare
| Modell | Modell |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kablar
| S. Nej. | Modell | Modell |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB – CU1M |
| 2. |Cisco |SFP-H10GB – CU2M |
| 3. |Cisco |SFP-H10GB – CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Switchar
| S. Nej. | Modell | Modell |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lista över enheter som testats i fältet
Det här avsnittet innehåller en lista över enheter som har distribuerats i fältet av StorSimple-kunder. Dessa har inte testats av Microsoft men fungerar sannolikt med din StorSimple-enhet.

| Parameter | Värde |
| --- | --- |
| Byt märke |Juniper |
| Växel modell |ex4550-32F |
| Växla operativ systemets version |JunOS 12,3 R 9.4 |
| Blad modell |Portar onboarding (PIC 0) |
| Sänd tagare gör |Juniper |
| Sänd tagare modell |Del nummer 740-021308 <br></br> Del nummer 740-030658 |
| Version för inbyggd Sänd program vara |Rev 01 version 0,0 (rapporterad) |
| Kabel modell |Duplex-bygel-LC/LC 50/125μ, OM3, LSZH |
| StorSimple-modell |8600 |
| StorSimple program varu version |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista över enheter som har testats av OEM-providern (Mellanox)
Mellanox har testat följande små (SFP) Sänd tagare, kablar och växlar för att säkerställa att de fungerar optimalt med Mellanox-nätverks gränssnitt som de 10 GbE-nätverks gränssnitten på din StorSimple-enhet.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kablar och moduler som stöds av Mellanox
I följande tabell visas de kablar och moduler som stöds av Mellanox. Dessa har inte testats av Microsoft men fungerar sannolikt med din StorSimple-enhet.

| S. Nej. | Hastighet | Modell | Beskrivning | Modell |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |passiv koppar kabel SFP + 10 GB/s 1m |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2 M |passiv koppar kabel SFP + 10 GB/s 2 m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |passiv koppar kabel SFP + 10 GB/s 3m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5 M |passiv koppar kabel SFP + 10 GB/s 5 m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cisco SFP + kabel |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cisco SFP + kabel |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cisco SFP + kabel |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP + till SFP + 1-tums direkt koppling koppar kabel |HP |
| 9. |10 GbE |455883 – B21 HP BLc |10 GB SR SFP + opt |HP |
| 10. |10 GbE |455886 – B21 HP BLc |10 GB LR SFP + opt |HP |
| 11. |10 GbE |487649 – B21 HP BLc |SFP + 0,5 m 10 GbE koppar kabel |HP |
| 12. |10 GbE |487652 – B21 HP BLc |SFP + 1 GbE koppar kabel |HP |
| 13.4. |10 GbE |487655 – B21 HP BLc |SFP + 3mad GbE koppar kabel |HP |
| längre. |10 GbE |487658 – B21 HP BLc |SFP + 7 Mad GbE koppar kabel |HP |
| 15.4. |10 GbE |537963 – B21 HP BLc |SFP + 5 Mad GbE koppar kabel |HP |
| 16. |10 GbE |AP784A HP |3m C-seriens passiva koppar med SFP + kabel |HP |
| 43. |10 GbE |AP785A HP |5 m C-seriens passiva koppar med SFP + kabel |HP |
| arton. |10 GbE |AP818A HP |1 miljon-seriens aktiva koppar SFP + kabel |HP |
| 19.3. |10 GbE |AP819A HP |3m B-seriens aktiva koppar SFP + kabel |HP |
| tjugo. |10 GbE |J9150A HP |X132 10G SFP + LC SR-sändtagaren |HP |
| 30. |10 GbE |J9151A HP |X132 10G SFP + LC LR Sänd tagare |HP |
| 22.2. |10 GbE |J9283B HP |X242 10G SFP + SFP + 3m DAC-kabel |HP |
| 23. |10 GbE |J9285B HP |X242 10G SFP + SFP + 7 m DAC-kabel |HP |
| 24.1. |10 GbE |JD095B HP |X240 10G SFP + SFP + 0.65 m DAC-kabel |HP |
| 25.1. |10 GbE |JD096B HP |X240 10G SFP + SFP + 1.2 m DAC-kabel |HP |
| 26. |10 GbE |JD097B HP |X240 10G SFP + SFP + 3m pappa-kabel |HP |
| 27. |10 GbE |MAM1Q00A – uppfyller kraven enligt Mellanox |QSFP till SFP + adapter |Mellanox-tekniker |
| senast. |10 GbE |MC2309124 – 006 MT |Passiv koppar kabel 1x SFP + till QSFP 10 GB/s 24AWG 7 m |Mellanox-tekniker |
| 29.4. |10 GbE |MC2309124 – 007 MT |Passiv koppar kabel 1x SFP + till QSFP 10 GB/s 24AWG 7 m |Mellanox-tekniker |
| dagar. |10 GbE |MC2309130 – 003 MT |Passiv koppar kabel 1x SFP + till QSFP 10 GB/s 30awg 3m |Mellanox-tekniker |
| fram. |10 GbE |MC2309130 – 00A MT |Passiv koppar kabel 1x SFP + till QSFP 10 GB/s 30awg 0,5 m |Mellanox-tekniker |
| 32. |10 GbE |MC3309124 – 005 MT |Passiv koppar kabel 1x SFP + 10 GB/s 24AWG 5 m |Mellanox-tekniker |
| 33. |10 GbE |MC3309124 – 007 MT |Passiv koppar kabel 1x SFP + 10 GB/s 24AWG 7 m |Mellanox-tekniker |
| 34. |10 GbE |MC3309130 – 003 MT |Passiv koppar kabel 1x SFP + 10 GB/s 30awg 3m |Mellanox-tekniker |
| 35. |10 GbE |MC3309130 – 00A MT |Passiv koppar kabel 1x SFP + 10 GB/s 30awg 0,5 m |Mellanox-tekniker |

### <a name="switches-supported-by-mellanox"></a>Växlar som stöds av Mellanox
I följande tabell visas de växlar som stöds av Mellanox. Dessa har inte testats av Microsoft men fungerar sannolikt med din StorSimple-enhet.

| S. Nej. | Hastighet | Modell | Beskrivning | Modell |
| --- | --- | --- | --- | --- |
| 1. |10GbE |516733 – B21 |HP ProCurve 6120XG 10 GbE Ethernet-bladswitch |HP |
| 2. |10GbE |538113 – B21 |HP-Pass-Through modul (PTM) |HP |
| 3. |10GbE |EN4093 |IBM PureFlex system Fabric EN4093 10 Gigabit skalbar switch-modul |IBM |
| 4. |1GbE |3020 |Cisco Catalyst 3020 1GbE switch-bladet |Cisco |
| 5. |1GbE |3020X |Cisco Catalyst 3020X 1GbE switch-bladet |Cisco |
| 6. |1GbE |438030 – B21 |HP 1GbE switch-modul – GbE2c Layer 2/3 Ethernet-bladswitch |HP |
| 7. |1GbE |6120G |HP ProCurve-6120G/XG 1GbE switch-bladet |HP |

## <a name="next-steps"></a>Nästa steg
[Läs mer om StorSimple maskin varu komponenter och status](./storsimple-8000-monitor-hardware-status.md).