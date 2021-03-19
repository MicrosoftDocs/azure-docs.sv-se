---
title: Innehålls uppföljnings lederna i Azure Kinect
description: Förstå bröd text ramen, lederna, gemensamma koordinater och gemensam hierarki i Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, portning, brödtext, spårning, gemensam, hierarki, ben, anslutning
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277693"
---
# <a name="azure-kinect-body-tracking-joints"></a>Innehålls uppföljnings lederna i Azure Kinect

Azure Kinect Body Tracking kan spåra flera mänskliga organ samtidigt. Varje brödtext innehåller ett ID för temporal korrelation mellan ramar och den IK-Skeleton. Antalet organisationer som identifieras i varje ram kan förvärvas med hjälp av `k4abt_frame_get_num_bodies()` .

## <a name="joints"></a>Lederna

Gemensam position och orientering är uppskattningar i förhållande till referensens globala djup sensor ram. Positionen anges i millimeter. Orienteringen uttrycks som en normaliserad quaternion.

## <a name="joint-coordinates"></a>Gemensamma koordinater

Placering och orientering för varje gemensamt formulärs eget gemensamma koordinatsystem. Alla gemensamma koordinatsystem är absoluta koordinatsystem i förhållande till 3D-koordinatsystemet för djupet.

> [!NOTE]
> Gemensamma koordinater är i Axel orientering. Axel orienteringen används ofta med kommersiella Avatars, spel motorer och åter givnings program. Genom att använda axel orientering blir det enklare att spegla rörelser, t. ex. höja båda armarna med 20 grader.

![Gemensamma koordinater](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>Gemensam hierarki

En Skeleton omfattar 32 fogar med den gemensamma hierarkin som flödar från mitten av bröd texten till de yttersta. Varje anslutning (ben) länkar den överordnade skarven med en underordnad skarv. Bilden illustrerar de gemensamma platserna och anslutningarna i förhållande till människo kroppen.

![Gemensam hierarki](./media/concepts/joint-hierarchy.png)

I följande tabell räknas de gemensamma standard anslutningarna upp.

|Index |Gemensamt namn     | Överordnad skarv   |
|------|---------------|----------------|
| 0    |PELVIS         | -              |
| 1    |SPINE_NAVAL    | PELVIS         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |STYCKE           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | PELVIS         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | PELVIS         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | STYCKE           |
| 27   |NÄSA           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>Nästa steg

[Indexkarta för kroppsspårning](body-index-map.md)
