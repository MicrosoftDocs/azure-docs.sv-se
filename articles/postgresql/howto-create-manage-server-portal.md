---
title: Hantera Azure Database for PostgreSQL – Azure Portal
description: Lär dig hur du hanterar en Azure Database for PostgreSQL-Server från Azure Portal.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 11/20/2019
ms.openlocfilehash: 4b581ac137a4172ab70e4fb3fb2a75e268115c06
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604132"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Hantera en Azure Database for PostgreSQL-server med hjälp av Azure Portal

Den här artikeln visar hur du hanterar dina Azure Database for PostgreSQL-servrar. Hanterings uppgifter omfattar skalning av beräknings-och lagrings utrymme, återställning av administratörs lösen ord och visning av Server information.

## <a name="sign-in"></a>Logga in

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-server"></a>Skapa en server

Besök [snabb](quickstart-create-server-database-portal.md) starten för att lära dig hur du skapar och kommer igång med en Azure Database for postgresql-server.

## <a name="scale-compute-and-storage"></a>Skala beräkning och lagring

När servern har skapats kan du skala mellan Generell användning-och Minnesoptimerade nivåer när dina behov förändras. Du kan också skala beräkning och minne genom att öka eller minska virtuella kärnor. Lagringen kan skalas upp (men du kan inte skala lagringen).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Skala mellan Generell användning-och Minnesoptimerade nivåer

Du kan skala från Generell användning till Minnesoptimerade och tvärtom. Det går inte att byta till och från Basic-nivån efter att servern har skapats.

1. Välj din server i Azure Portal. Välj **pris nivå**, som finns i avsnittet **Inställningar** .

2. Välj **generell användning** eller **minne som är optimerat**, beroende på vad du skalar till.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Skärm bild av Azure Portal för att välja Basic, Generell användning eller minnesoptimerade nivå i Azure Database for PostgreSQL":::

   > [!NOTE]
   > Om du ändrar nivåer startas servern om.

3. Välj **OK** för att spara ändringarna.

### <a name="scale-vcores-up-or-down"></a>Skala virtuella kärnor uppåt eller nedåt

1. Välj din server i Azure Portal. Välj **pris nivå**, som finns i avsnittet **Inställningar** .

2. Ändra **vCore** -inställningen genom att flytta skjutreglaget till önskat värde.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Skärm bild av Azure Portal att välja alternativet vCore i Azure Database for PostgreSQL":::

   > [!NOTE]
   > Vid skalning av virtuella kärnor sker en omstart av servern.

3. Välj **OK** för att spara ändringarna.

### <a name="scale-storage-up"></a>Skala upp lagring

1. Välj din server i Azure Portal. Välj **pris nivå**, som finns i avsnittet **Inställningar** .

2. Ändra **lagrings** inställningen genom att flytta skjutreglaget uppåt till önskat värde.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Skärm bild av Azure Portal för att välja lagrings skala i Azure Database for PostgreSQL":::

   > [!NOTE]
   > Det går inte att skala upp lagrings utrymmet.

3. Välj **OK** för att spara ändringarna.

## <a name="update-admin-password"></a>Uppdatera administratörs lösen ord

Du kan ändra administratörs rollens lösen ord med hjälp av Azure Portal.

1. Välj din server i Azure Portal. Välj **Återställ lösen ord** i **översikts** fönstret.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Skärm bild av Azure Portal för att återställa lösen ordet i Azure Database for PostgreSQL":::

2. Ange ett nytt lösen ord och bekräfta lösen ordet. I text rutan visas ett varnings krav för lösen ords komplexitet.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Skärm bild av Azure Portal för att återställa ditt lösen ord och spara i Azure Database for PostgreSQL":::

3. Välj **OK** för att spara det nya lösen ordet.

## <a name="delete-a-server"></a>Ta bort en server

Du kan ta bort servern om du inte längre behöver den. 

1. Välj din server i Azure Portal. I **översikts** fönstret väljer du **ta bort**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Skärm bild av Azure Portal att ta bort servern i Azure Database for PostgreSQL":::

2. Skriv namnet på servern i indatatypen för att bekräfta att det är den server som du vill ta bort.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Skärm bild av Azure Portal för att bekräfta att servern tas bort i Azure Database for PostgreSQL":::

   > [!NOTE]
   > Det går inte att ångra borttagningen av en server.

3. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [säkerhets kopiering och återställning av Server](howto-restore-server-portal.md)
- Lär dig mer om [justerings-och övervaknings alternativ i Azure Database for PostgreSQL](concepts-monitoring.md)
