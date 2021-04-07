---
title: Flytta Azure-regioner – Azure Portal – Azure Database for MySQL
description: Flytta en Azure Database for MySQL-server från en Azure-region till en annan med hjälp av en Läs replik och Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.openlocfilehash: 279526d31adf25d1f4cd5a6d9b15519679bd88b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540237"
---
# <a name="move-an-azure-database-for-mysql-server-to-another-region-by-using-the-azure-portal"></a>Flytta en Azure Database for MySQL-server till en annan region med hjälp av Azure Portal

Det finns olika scenarier för att flytta en befintlig Azure Database for MySQL-server från en region till en annan. Du kanske till exempel vill flytta en produktions server till en annan region som en del av din planering för haveri beredskap.

Du kan använda en Azure Database for MySQL [över flera regioner](concepts-read-replicas.md#cross-region-replication) för att slutföra flytten till en annan region. Det gör du genom att först skapa en Läs replik i mål regionen. Stoppa sedan replikering till Läs replik servern för att göra den till en fristående server som accepterar både Läs-och Skriv trafik. 

> [!NOTE]
> Den här artikeln fokuserar på att flytta servern till en annan region. Om du vill flytta servern till en annan resurs grupp eller prenumeration kan du läsa artikeln [Flytta](../azure-resource-manager/management/move-resource-group-and-subscription.md) . 

## <a name="prerequisites"></a>Förutsättningar

- Funktionen Läs replik är bara tillgänglig för Azure Database for MySQL servrar i Generell användning eller Minnesoptimerade pris nivåer. Se till att käll servern är i någon av dessa pris nivåer.

- Kontrol lera att din Azure Database for MySQL käll Server finns i den Azure-region som du vill flytta från.

## <a name="prepare-to-move"></a>Förbered för att flytta

Gör så här om du vill skapa en skrivskyddad replik Server mellan regioner i mål regionen med hjälp av Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj den befintliga Azure Database for MySQL-server som du vill använda som käll Server. Den här åtgärden öppnar **översikts** sidan.
1. Välj **replikering** på menyn under **Inställningar**.
1. Välj **Lägg till replik**.
1. Ange ett namn på replik servern.
1. Välj plats för replik servern. Standard platsen är samma som käll servern. Kontrol lera att du har valt den mål plats där du vill att repliken ska distribueras.
1. Bekräfta skapandet av repliken genom att klicka på **OK** . När repliken skapas kopieras data från käll servern till repliken. Tid för att skapa kan vara flera minuter eller mer, i proportion till storleken på käll servern.

>[!NOTE]
> När du skapar en replik ärver den inte den virtuella nätverks slut punkten för käll servern. Dessa regler måste konfigureras separat för repliken.

## <a name="move"></a>Flytta

> [!IMPORTANT]
> Den fristående servern kan inte göras till en replik igen.
> Innan du stoppar replikeringen på en Läs replik måste du se till att repliken har alla data som du behöver.

Om du stoppar replikeringen till replik servern blir det en fristående server. Använd följande steg för att stoppa replikeringen till repliken från Azure Portal:

1. När repliken har skapats letar du reda på och väljer din Azure Database for MySQL käll Server. 
1. Välj **replikering** på menyn under **Inställningar**.
1. Välj replik servern.
1. Välj **stoppa replikering**.
1. Bekräfta att du vill stoppa replikeringen genom att klicka på **OK**.

## <a name="clean-up-source-server"></a>Rensa käll Server

Du kanske vill ta bort käll Azure Database for MySQLs servern. Gör det med hjälp av följande steg:

1. När repliken har skapats letar du reda på och väljer din Azure Database for MySQL käll Server.
1. I fönstret **Översikt** väljer du **ta bort**.
1. Skriv in namnet på käll servern för att bekräfta att du vill ta bort.
1. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en Azure Database for MySQL-server från en region till en annan med hjälp av Azure Portal och rensade de onödiga käll resurserna. 

- Läs mer om [Läs repliker](concepts-read-replicas.md)
- Lär dig mer om [att hantera Läs repliker i Azure Portal](howto-read-replicas-portal.md)
- Läs mer om alternativ för [affärs kontinuitet](concepts-business-continuity.md)