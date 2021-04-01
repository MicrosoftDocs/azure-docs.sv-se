---
title: Hantera Server-Azure Portal-Azure Database for MySQL flexibel Server
description: Lär dig hur du hanterar en Azure Database for MySQL flexibel Server från Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7a01863b3a0c29e94550be67ca957655cff32660
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90937332"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Hantera en Azure Database for MySQL-flexibel Server (för hands version) med Azure Portal


> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Den här artikeln visar hur du hanterar Azure Database for MySQL flexibla servrar (för hands version). Hanterings uppgifter omfattar skalning av beräkning och lagring, rest Server administratörs lösen ord och ta bort servern.

## <a name="sign-in"></a>Logga in
Logga in på [Azure-portalen](https://portal.azure.com). Gå till din flexibla Server resurs i Azure Portal.

## <a name="scale-compute-and-storage"></a>Skala beräkning och lagring

När servern har skapats kan du skala mellan olika [pris nivåer](https://azure.microsoft.com/pricing/details/mysql/) när dina behov förändras. Du kan även skala upp eller ned beräkning och minne genom att öka eller minska virtuella kärnor.

1. Välj din server i Azure Portal. Välj **Compute + Storage**, som finns i avsnittet **Inställningar** .

2. Du kan ändra **beräknings nivån**, **vCore**, **lagringen** för att skala upp servern med högre beräknings nivå eller skala upp på samma nivå genom att öka lagrings-eller virtuella kärnor till ett värde som du vill ha.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="skala flexibel lagrings Server":::

   > [!Important]
   > - Det går inte att skala upp lagrings utrymmet.
   > - Vid skalning av virtuella kärnor sker en omstart av servern.

3. Välj **OK** för att spara ändringarna.

## <a name="reset-admin-password"></a>Återställ administratörs lösen ord

Du kan ändra administratörs rollens lösen ord med hjälp av Azure Portal.

1. Välj din server i Azure Portal. Välj **Återställ lösen ord** i fönstret **Översikt** .

2. Ange ett nytt lösen ord och bekräfta lösen ordet. I text rutan visas ett varnings krav för lösen ords komplexitet.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="Återställ ditt lösen ord för flexibel Server":::

3. Välj **Spara** för att spara det nya lösen ordet.

## <a name="delete-a-server"></a>Ta bort en server

Du kan ta bort servern om du inte längre behöver den.

1. Välj din server i Azure Portal. I **översikts** fönstret väljer du **ta bort**.

2. Skriv namnet på servern i indatatypen för att bekräfta att du vill ta bort servern.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="ta bort den flexibla servern":::

   > [!NOTE]
   > Det går inte att ångra borttagningen av en server.

3. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg
- [Lär dig hur du startar eller stoppar en server](how-to-stop-start-server-portal.md)
- [Lär dig hur du återställer en server](how-to-restore-server-portal.md)
- [Felsöka anslutningsproblem](how-to-troubleshoot-common-connection-issues.md)

