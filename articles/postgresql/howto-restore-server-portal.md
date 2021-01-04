---
title: Säkerhets kopiering och återställning – Azure Portal-Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du återställer en server i Azure Database for PostgreSQL-enskild server med hjälp av Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 6/30/2020
ms.openlocfilehash: 82cec4cc448f0ec30aecf6f8a69f399e0abbdde0
ms.sourcegitcommit: 0830e02635d2f240aae2667b947487db01f5fdef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2020
ms.locfileid: "97706957"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Säkerhetskopiera och återställa en server i Azure Database for PostgreSQL-enskild server med hjälp av Azure Portal

## <a name="backup-happens-automatically"></a>Säkerhets kopiering sker automatiskt
Azure Database for PostgreSQL servrar säkerhets kopie ras regelbundet för att aktivera återställnings funktioner. Med den här funktionen kan du återställa servern och alla dess databaser till en tidigare tidpunkt på en ny server.

## <a name="set-backup-configuration"></a>Ange konfiguration för säkerhets kopiering

Du kan välja mellan att konfigurera servern för antingen lokalt redundanta säkerhets kopieringar eller geografiskt redundanta säkerhets kopieringar när servern skapas, i fönstret **pris nivå** .

> [!NOTE]
> När en server har skapats har den typ av redundans som den har, geografiskt redundant vs lokalt redundant, inte växlats.
>

När du skapar en server via Azure Portal, är **pris nivå** fönstret där du väljer antingen **lokalt redundant** eller **geografiskt redundant** säkerhets kopiering för servern. Det här fönstret är också där du väljer **kvarhållningsperiod för säkerhets kopior** – hur lång tid (i antal dagar) som du vill att serverns säkerhets kopior ska lagras.

   :::image type="content" source="./media/howto-restore-server-portal/pricing-tier.png" alt-text="Pris nivå – Välj redundans för säkerhets kopiering":::

Mer information om hur du anger dessa värden under skapa finns i [snabb starten för Azure Database for postgresql server](quickstart-create-server-database-portal.md).

Du kan ändra kvarhållningsperioden för säkerhets kopior för en server genom att följa stegen nedan:
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj din Azure Database for PostgreSQL-server. Den här åtgärden öppnar **översikts** sidan.
3. Välj **pris nivå** på menyn under **Inställningar**. Med skjutreglaget kan du ändra **kvarhållningsperioden för säkerhets kopior** till dina preferenser mellan 7 och 35 dagar.
I skärm bilden nedan har den ökats till 34 dagar.
:::image type="content" source="./media/howto-restore-server-portal/3-increase-backup-days.png" alt-text="Kvarhållning av säkerhets kopior har ökat":::

4. Bekräfta ändringen genom att klicka på **OK** .

Kvarhållningsperioden för säkerhets kopior styr hur långt tillbaka i tiden en tidpunkts återställning kan hämtas, eftersom den baseras på tillgängliga säkerhets kopior. Återställning av tidpunkt för tidpunkt beskrivs ytterligare i följande avsnitt. 

## <a name="point-in-time-restore"></a>Återställning från tidpunkt
Med Azure Database for PostgreSQL kan du återställa servern till en tidpunkt och till en ny kopia av-servern. Du kan använda den här nya servern för att återställa dina data eller låta klient programmen peka på den nya servern.

Om en tabell till exempel råkat släppas efter middag idag, kan du återställa till tiden precis före 12.00 och hämta den saknade tabellen och data från den nya kopian av servern. Återställning av tidpunkt på Server är på server nivå, inte på databas nivå.

Följande steg återställer exempel servern till en tidpunkt:
1. I Azure Portal väljer du Azure Database for PostgreSQL-servern. 

2. I verktygsfältet på sidan **Översikt** för servern väljer du **Återställ**.

   :::image type="content" source="./media/howto-restore-server-portal/2-server.png" alt-text="Azure Database for PostgreSQL-översikt – knappen Återställ":::

3. Fyll i formuläret Återställ med den information som behövs:

   :::image type="content" source="./media/howto-restore-server-portal/3-restore.png" alt-text="Azure Database for PostgreSQL-återställnings information":::
   - **Återställnings punkt**: Välj den tidpunkt som du vill återställa till.
   - **Mål server**: Ange ett namn för den nya servern.
   - **Plats**: du kan inte välja region. Som standard är det samma som käll servern.
   - **Pris nivå**: du kan inte ändra de här parametrarna när du gör en tidpunkts återställning. Det är samma som källservern. 

4. Klicka på **OK** för att återställa servern för att återställa till en tidpunkt. 

5. När återställningen är klar letar du upp den nya server som har skapats för att verifiera att data har återställts som förväntat.

Den nya servern som skapats av återställning vid olika tidpunkter har samma inloggnings namn och lösen ord för Server administratören som var giltiga för den befintliga servern vid den tidpunkt som väljs. Du kan ändra lösen ordet från den nya serverns **översikts** sida.

Den nya servern som skapas under en återställning saknar de brandväggsregler eller VNet-tjänstslutpunkter som fanns på den ursprungliga servern. Dessa regler måste konfigureras separat för den nya servern.

Om din käll PostgreSQL-Server är krypterad med Kundhanterade nycklar kan du läsa [dokumentationen](concepts-data-encryption-postgresql.md) för ytterligare överväganden.

## <a name="geo-restore"></a>Geo-återställning

Om du har konfigurerat servern för geografiskt redundanta säkerhets kopieringar kan en ny server skapas från säkerhets kopian av den befintliga servern. Den nya servern kan skapas i vilken region som helst som Azure Database for PostgreSQL tillgänglig.  

1. Välj knappen **skapa en resurs** (+) i det övre vänstra hörnet i portalen. Välj **databaser**  >  **Azure Database for PostgreSQL**.

   :::image type="content" source="./media/howto-restore-server-portal/1-navigate-to-postgres.png" alt-text="Navigera till Azure Database for PostgreSQL.":::

2. Välj distributions alternativet **enskild server** .

   :::image type="content" source="./media/howto-restore-server-portal/2-select-deployment-option.png" alt-text="Välj Azure Database for PostgreSQL distributions alternativ för enskild server.":::
 
3. Ange prenumeration, resurs grupp och namn på den nya servern. 

4. Välj **säkerhets kopia** som **data källa**. Den här åtgärden läser in en listruta som innehåller en lista över servrar som har geo-redundanta säkerhets kopieringar aktiverade.
   
   :::image type="content" source="./media/howto-restore-server-portal/4-geo-restore.png" alt-text="Välj data källa.":::
    
   > [!NOTE]
   > När en server först skapas kanske den inte är omedelbart tillgänglig för geo Restore. Det kan ta några timmar för nödvändiga metadata att fyllas i.
   >

5. Välj List rutan **säkerhets kopiering** .
   
   :::image type="content" source="./media/howto-restore-server-portal/5-geo-restore-backup.png" alt-text="Välj listruta för säkerhets kopiering.":::

6. Välj käll servern som du vill återställa från.
   
   :::image type="content" source="./media/howto-restore-server-portal/6-select-backup.png" alt-text="Välj säkerhets kopiering.":::

7. Servern kommer att standardvärdet för antal **virtuella kärnor**, **kvarhållning av säkerhets kopior**, **redundans alternativ** för säkerhets kopior, **motor version** och **autentiseringsuppgifter för administratörer**. Välj **Fortsätt**. 
   
   :::image type="content" source="./media/howto-restore-server-portal/7-accept-backup.png" alt-text="Fortsätt med säkerhets kopiering.":::

8. Fyll i resten av formuläret med dina inställningar. Du kan välja valfri **plats**.

    När du har valt platsen kan du välja **Konfigurera Server** för att uppdatera **beräknings generationen** (om det är tillgängligt i den region som du har valt), antal **virtuella kärnor**, **kvarhållning av säkerhets kopior** och **alternativet för redundans**. Att ändra **pris nivå** (Basic, generell användning eller minnesoptimerade) eller **lagrings** storlek under återställningen stöds inte.

   :::image type="content" source="./media/howto-restore-server-portal/8-create.png" alt-text="Fyll i formulär."::: 

9. Välj **Granska + skapa** för att granska dina val. 

10. Välj **Skapa** för att etablera servern. Den här åtgärden kan ta några minuter.

Den nya servern som skapades med geo Restore har samma inloggnings namn och lösen ord för Server administratören som var giltiga för den befintliga servern vid den tidpunkt då återställningen initierades. Du kan ändra lösen ordet från den nya serverns **översikts** sida.

Den nya servern som skapas under en återställning saknar de brandväggsregler eller VNet-tjänstslutpunkter som fanns på den ursprungliga servern. Dessa regler måste konfigureras separat för den nya servern.

Om din käll PostgreSQL-Server är krypterad med Kundhanterade nycklar kan du läsa [dokumentationen](concepts-data-encryption-postgresql.md) för ytterligare överväganden.


## <a name="next-steps"></a>Nästa steg
- Läs mer om tjänstens [säkerhets kopieringar](concepts-backup.md).
- Lär dig mer om [affärs kontinuitets](concepts-business-continuity.md) alternativ.