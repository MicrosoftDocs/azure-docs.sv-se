---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c3f913aa3de1b723cd4eae70ff8e578a8abbec12
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95560019"
---
#### <a name="to-create-a-new-service"></a>Skapa en ny tjänst
1. Använd dina Microsoft-konto autentiseringsuppgifter för att logga in på [Microsoft Azure Government Portal](https://portal.azure.us/).
2. I myndighets portalen klickar du på **+** och sedan på **Visa alla** på Marketplace. Sök efter _StorSimple Physical_. Markera och klicka på **StorSimple Physical Device Series** och sedan på **Skapa**. Alternativt, i myndighets portalen, klicka på **+** och under **lagring** klickar du på **StorSimple fysisk enhets serie**.
3. Utför följande steg på bladet för **StorSimple Device Manager**:
   
   1. Ange ett unikt **resursnamn** för tjänsten. Det här är ett eget namn som kan användas för att identifiera tjänsten. Namnet kan innehålla mellan 2 och 50 tecken som kan vara bokstäver, siffror och bindestreck. Namnet måste börja och sluta med en bokstav eller en siffra.
   2. Välj en **prenumeration** från listrutan. Prenumerationen är kopplad till ditt faktureringskonto. Det här fältet syns inte om du bara har en prenumeration.
   3. Välj **Använd befintlig grupp** eller **Skapa ny grupp** för **Resursgrupp**. Mer information finns i avsnittet om [Azure-resursgrupper](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).
   4. Ange en **plats** för din tjänst. Plats syftar på den geografiska region där du vill distribuera enheten. Välj **USGov Iowa** eller **USGov Virginia**.
   5. Välj **Skapa ett nytt lagringskonto**, för att automatiskt skapa ett lagringskonto med tjänsten. Ange ett namn för lagringskontot. Avmarkera kryssrutan om du behöver ha din data på en annan plats.
   6. Markera **Fäst på instrumentpanelen** om du vill skapa en snabblänk till tjänsten på instrumentpanelen.
   7. Skapa StorSimple Device Manager genom att klicka på **Skapa**. Det tar några minuter att skapa tjänsten. När tjänsten har skapats visas ett meddelande och bladet för den nya tjänsten öppnas.