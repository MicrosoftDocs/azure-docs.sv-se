---
title: Konfigurera kontinuerlig säkerhets kopiering och tidpunkts återställning med Azure Portal i Azure Cosmos DB.
description: Lär dig hur du identifierar återställnings punkten och konfigurerar kontinuerlig säkerhets kopiering med Azure Portal. Det visar hur du återställer ett Live-och borttaget konto.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ee6eedbc078e1b9c07ed00922ce1c37b38410128
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381876"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>Konfigurera och hantera kontinuerlig säkerhets kopiering och tidpunkts återställning (för hands version) – med Azure Portal
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funktionen för återställning av tidpunkt (kontinuerlig säkerhets kopiering) för Azure Cosmos DB är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB funktionen för återställning av tidpunkt (för hands version) hjälper dig att återställa från en oavsiktlig ändring i en behållare, för att återställa ett borttaget konto, en databas eller en behållare eller för att återställa till en region (där säkerhets kopior fanns). Med läget för kontinuerlig säkerhets kopiering kan du återställa till en viss tidpunkt inom de senaste 30 dagarna.

I den här artikeln beskrivs hur du identifierar återställnings punkten och konfigurerar kontinuerlig säkerhets kopiering med Azure Portal.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Etablera ett konto med kontinuerlig säkerhets kopiering

När du skapar ett nytt Azure Cosmos DB konto väljer du **kontinuerligt** läge för alternativet **säkerhets kopierings policy** för att aktivera funktionen för återställning av tidpunkt för det nya kontot. När den här funktionen har Aktiver ATS för kontot är alla databaser och behållare tillgängliga för kontinuerlig säkerhets kopiering. Med den dagliga återställningen återställs alltid data till ett nytt konto, för närvarande går det inte att återställa till ett befintligt konto.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="Etablera ett Azure Cosmos DB-konto med kontinuerlig säkerhets kopierings konfiguration." border="true":::

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Återställa ett Live-konto från oavsiktlig ändring

Du kan använda Azure Portal för att återställa ett Live-konto eller valda databaser och behållare under det. Använd följande steg för att återställa dina data:

1. Logga in på [Azure Portal](https://portal.azure.com/)
1. Gå till ditt Azure Cosmos DB-konto och öppna fönstret **tidpunkt för återställning** .

   > [!NOTE]
   > Rutan Återställ i Azure Portal är bara ifylld om du har `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` behörigheten. Mer information om hur du anger den här behörigheten finns i artikeln [säkerhetskopiera och återställa behörigheter](continuous-backup-restore-permissions.md) .

1. Fyll i följande information för att återställa:

   * **Återställnings punkt (UTC)** – en tidstämpel under de senaste 30 dagarna. Kontot ska finnas i tidsstämpeln. Du kan ange återställnings punkten i UTC. Det kan vara så nära det andra när du vill återställa det. Välj länken **Klicka här** om du vill ha hjälp med att [identifiera återställnings punkten](#event-feed).

   * **Plats** – den mål region där kontot återställs. Kontot bör finnas i den här regionen vid den aktuella tidsstämpeln (t. ex. USA, västra eller USA, östra). Ett konto kan bara återställas till de regioner där käll kontot fanns.

   * **Återställ resurs** – du kan antingen välja **hela kontot** eller en **vald databas eller behållare** att återställa. Databaserna och behållarna bör finnas på den aktuella tidsstämpeln. Beroende på vilken återställnings punkt och plats som valts, fylls återställnings resurserna i, vilket gör att användaren kan välja vissa databaser eller behållare som behöver återställas.

   * **Resurs grupp** – resurs grupp under vilken mål kontot ska skapas och återställas. Resurs gruppen måste redan finnas.

   * **Återställ mål konto** – mål kontots namn. Mål kontots namn måste följa samma rikt linjer som när du skapar ett nytt konto. Det här kontot kommer att skapas av återställnings processen i samma region där ditt käll konto finns.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Återställa ett Live-konto från ändrings Azure Portal av misstag." border="true":::

1. När du har valt parametrarna ovan väljer du knappen **Skicka** för att starta en återställning. Återställnings kostnaden är en tids avgift som baseras på mängden data och avgifter för lagring i den aktuella regionen. Mer information finns i avsnittet om [prissättning](continuous-backup-restore-introduction.md#continuous-backup-pricing) .

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Använd händelse flöden för att identifiera återställnings tiden

När du fyller i återställnings punkt tiden i Azure Portal, om du behöver hjälp med att identifiera återställnings punkt, väljer du länken **Klicka här** och tar dig till bladet för händelse inmatning. Händelse flödet innehåller en fullständig åter givning lista över skapa, ersätta och ta bort händelser för databaser och behållare för käll kontot. 

Om du till exempel vill återställa till punkten innan en viss behållare har tagits bort eller uppdaterats, kontrollerar du denna händelse-feed. Händelser visas i kronologisk fallande ordning när de inträffade, med de senaste händelserna överst. Du kan bläddra igenom resultaten och välja tiden före eller efter händelsen för att ytterligare begränsa tiden.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="Använd händelse flöden för att identifiera återställnings punkt tiden." border="true":::

> [!NOTE]
> Händelse flödet visar inte ändringarna i objekt resurserna. Du kan alltid manuellt ange eventuella tidsstämplar under de senaste 30 dagarna (så länge kontot finns vid den tiden) för återställning.

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Återställa ett borttaget konto

Du kan använda Azure Portal för att helt återställa ett borttaget konto inom 30 dagar från borttagningen. Använd följande steg för att återställa ett borttaget konto:

1. Logga in på [Azure Portal](https://portal.azure.com/)
1. Sök efter *Azure Cosmos DB* resurser i det globala Sök fältet. Den visar alla dina befintliga konton.
1. Välj sedan knappen **Återställ** . I rutan Återställ visas en lista över borttagna konton som kan återställas inom kvarhållningsperioden, som är 30 dagar från borttagnings tiden.
1. Välj det konto som du vill återställa.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Återställ ett borttaget konto från Azure Portal." border="true":::

   > [!NOTE]
   > Obs! fönstret Restore i Azure Portal är bara ifyllt om du har `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` behörigheten. Mer information om hur du anger den här behörigheten finns i artikeln [säkerhetskopiera och återställa behörigheter](continuous-backup-restore-permissions.md) .

1. Välj ett konto som du vill återställa och ange följande information för att återställa ett borttaget konto:

   * **Återställnings punkt (UTC)** – en tidstämpel under de senaste 30 dagarna. Kontot måste ha funnits vid den tidsstämpeln. Ange återställnings punkten i UTC. Det kan vara så nära det andra när du vill återställa det.

   * **Plats** – den mål region där kontot måste återställas. Käll kontot bör finnas i den här regionen vid den aktuella tidsstämpeln. Exempel: USA, västra eller USA, östra.  

   * **Resurs grupp** – resurs grupp under vilken mål kontot ska skapas och återställas. Resurs gruppen måste redan finnas.

   * **Återställ mål konto** – mål kontots namn måste följa samma rikt linjer som när du skapar ett nytt konto. Det här kontot kommer att skapas av återställnings processen i samma region där ditt käll konto finns.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>Spåra status för återställnings åtgärden

När du har initierat en återställnings åtgärd väljer du klock ikonen för **aviseringar** i det övre högra hörnet av portalen. Den ger en länk som visar status för det konto som återställs. När återställningen pågår kommer kontots status att *skapas*, när återställningen har slutförts, ändras kontots status till *online*.

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="Statusen för återställda konto ändringar från att skapas till online när åtgärden har slutförts." border="true":::

## <a name="next-steps"></a>Nästa steg

* Konfigurera och hantera kontinuerlig säkerhets kopiering med [Azure CLI](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-powershell.md)eller [Azure Resource Manager](continuous-backup-restore-template.md).
* [Resurs modell för kontinuerligt säkerhets kopierings läge](continuous-backup-restore-resource-model.md)
* [Hantera behörigheter](continuous-backup-restore-permissions.md) som krävs för att återställa data med kontinuerligt säkerhets kopierings läge.
