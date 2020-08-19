---
title: Konfigurera en data uppsättnings mappning i Azure Data Share
description: Lär dig hur du konfigurerar en data uppsättnings mappning för en mottagen resurs med hjälp av Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257837"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Så här konfigurerar du en data uppsättnings mappning för en mottagen resurs i Azure Data Share

Den här artikeln beskriver hur du konfigurerar en data uppsättnings mappning för en mottagen resurs med hjälp av Azure Data Share. Du kanske vill konfigurera en data uppsättnings mappning för att ange ett mål data lager där du vill ta emot data, eller om du behöver ändra mål data lagret.

## <a name="navigate-to-a-received-data-share"></a>Navigera till en mottagen data resurs

I Azure Data Share-tjänsten navigerar du till din mottagna resurs och väljer fliken **data uppsättningar** . 

![Mappning av data uppsättning](./media/dataset-mapping.png "Mappning av data uppsättning") 

Markera kryss rutan bredvid den data uppsättning som du vill tilldela ett mål. Välj **+ Mappa till mål** för att välja ett nytt mål lager. Välj först **mappning** först om data uppsättningen redan är mappad och du vill ändra mål data lagret.

![Mappa till mål](./media/dataset-map-target.png "Mappa till mål") 

## <a name="select-a-target-store"></a>Välj ett mål lager

Välj en mål data lager typ som du vill att data ska hamna i. För ögonblicks bilds-baserad delning flyttas alla data som redan finns i tidigare mappade lagrings konton automatiskt till det nya mål arkivet. För delning på plats väljer du ett data lager på den angivna platsen. Platsen är Azure Data Center där dataproviderns käll data lager finns.

![Mål lagrings konto](./media/dataset-map-target-sql.png "Mål lagring") 

## <a name="select-a-file-format-sql-sources-only"></a>Välj ett fil format (endast SQL-källor)

Om käll informationen kommer från en SQL-baserad källa och du vill ta emot den som en fil, kan du välja vilket format den tas emot i. 

![Välj format](./media/sql-file-formats.png "SQL-filformat")

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .



