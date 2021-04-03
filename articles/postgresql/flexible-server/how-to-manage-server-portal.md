---
title: Hantera Server-Azure Portal-Azure Database for PostgreSQL-flexibel Server
description: Lär dig hur du hanterar en Azure Database for PostgreSQL-flexibel Server från Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc
ms.openlocfilehash: 1ac418d855696138341115412dc7e2601d4cf3a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91961416"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Hantera en Azure Database for PostgreSQL-flexibel server med hjälp av Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Den här artikeln visar hur du hanterar din Azure Database for PostgreSQL-flexibla Server. Hanterings uppgifter omfattar skalning av beräknings-och lagrings utrymme, återställning av administratörs lösen ord och visning av Server information.

## <a name="sign-in"></a>Logga in

Logga in på [Azure-portalen](https://portal.azure.com). Gå till din flexibla Server resurs i Azure Portal.

## <a name="scale-compute-and-storage"></a>Skala beräkning och lagring

När servern har skapats kan du skala mellan olika [pris nivåer](https://azure.microsoft.com/pricing/details/postgresql/) när dina behov förändras. Du kan även skala upp eller ned beräkning och minne genom att öka eller minska virtuella kärnor.

> [!NOTE]
> Det går inte att skala upp lagrings utrymmet till lägre värde.

1. Välj din server i Azure Portal. Välj **Compute + Storage**, som finns i avsnittet **Inställningar** .
2. Du kan ändra **beräknings nivån** , **vCore**, **lagringen** för att skala upp servern med högre beräknings nivå eller skala upp på samma nivå genom att öka lagrings-eller virtuella kärnor till önskat värde.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="skala flexibel lagrings Server":::

> [!Important]
> - Det går inte att skala upp lagrings utrymmet.
> - Vid skalning av virtuella kärnor sker en omstart av servern.

3. Välj **OK** för att spara ändringarna.

## <a name="reset-admin-password"></a>Återställ administratörs lösen ord

Du kan ändra administratörs rollens lösen ord med hjälp av Azure Portal.

1. Välj din server i Azure Portal. Välj **Återställ lösen ord** i **översikts** fönstret.
2. Ange ett nytt lösen ord och bekräfta lösen ordet. I text rutan visas ett varnings krav för lösen ords komplexitet.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="Återställ ditt lösen ord för flexibel Server":::

3. Välj **Spara** för att spara det nya lösen ordet.

## <a name="delete-a-server"></a>Ta bort en server

Du kan ta bort servern om du inte längre behöver den.

1. Välj din server i Azure Portal. I **översikts** fönstret väljer du **ta bort**.
2. Skriv namnet på servern i indatatypen för att bekräfta att du vill ta bort servern.

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="ta bort den flexibla servern":::

   > [!IMPORTANT]
   > Det går inte att ångra borttagningen av en server.

  > [!div class="mx-imgBorder"]
  > ![ta bort den flexibla servern](./media/howto-manage-server-portal/delete-server.png)  

3. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- [Förstå säkerhets kopierings-och återställnings begrepp](concepts-backup-restore.md)
- [Justera och övervaka servern](concepts-monitoring.md)
