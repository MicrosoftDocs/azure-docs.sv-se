---
title: Starta om Azure Portal-Azure Database for PostgreSQL – flexibel Server
description: I den här artikeln beskrivs hur du utför omstarts åtgärder i Azure Database for PostgreSQL via Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90936936"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>Starta om Azure Database for PostgreSQL – flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Den här artikeln innehåller steg för steg-anvisningar för att starta om den flexibla servern. Den här åtgärden är användbar för att tillämpa eventuella statiska parameter ändringar som kräver omstart av databas servern. Proceduren är densamma för servrar som kon figurer ATS med redundanta zoner med hög tillgänglighet. 

> [!IMPORTANT]
> När den kon figurer ATS med hög tillgänglighet startas både den primära servern och standby-servrarna på samma gång.

## <a name="pre-requisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

-   Du måste ha en flexibel Server.

## <a name="restart-your-flexible-server"></a>Starta om den flexibla servern

Följ dessa steg för att starta om den flexibla servern.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill starta om.

2.  Klicka på **Översikt** i den vänstra panelen och klicka på **starta om**.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="Starta om valet":::

3.  Ett meddelande visas om att ett popup-meddelande visas.

4.  Klicka på **Ja** om du vill fortsätta.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="Bekräfta omstart":::
 
6.  Ett meddelande visas om att omstart har påbörjats.

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om [verksamhets kontinuitet](./concepts-business-continuity.md)
-   Lär dig mer om [Zone-redundant hög tillgänglighet](./concepts-high-availability.md)
