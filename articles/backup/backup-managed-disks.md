---
title: Säkerhetskopiera Azure Managed Disks
description: Lär dig hur du säkerhetskopierar Azure-Managed Disks från Azure Portal.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: ca86550c4dec4b51c60d9ecdef124e38783a3764
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98738160"
---
# <a name="back-up-azure-managed-disks-in-preview"></a>Säkerhetskopiera Azure Managed Disks (för hands version)

>[!IMPORTANT]
>Azure disk Backup är i för hands version utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). För region tillgänglighet, se [support mat ris](disk-backup-support-matrix.md).
>
>[Fyll i det här formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) om du vill registrera dig för för hands versionen.

Den här artikeln förklarar hur du säkerhetskopierar den [hanterade Azure-disken](../virtual-machines/managed-disks-overview.md) från Azure Portal.

I den här artikeln får du lära dig att:

- Skapa ett säkerhets kopierings valv

- Skapa en säkerhetskopieringspolicy

- Konfigurera en säkerhets kopia av en Azure-disk

- Köra ett säkerhets kopierings jobb på begäran

Information om tillgänglighet för Azure disk backup, stödda scenarier och begränsningar finns i [support mat ris](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Skapa ett säkerhets kopierings valv

Ett säkerhets kopierings valv är en lagrings enhet i Azure som innehåller säkerhets kopierings data för olika nyare arbets belastningar som Azure Backup stöder, till exempel Azure Database for PostgreSQL-servrar och Azure-diskar. Säkerhets kopierings valv gör det enkelt att organisera dina säkerhets kopierings data samtidigt som du minimerar hanterings kostnaderna. Säkerhets kopierings valv baseras på Azure Resource Manager modell för Azure, som tillhandahåller förbättrade funktioner för att skydda säkerhetskopierade data.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).
1. Skriv **Backup Center** i sökrutan.
1. Under **tjänster** väljer du **Backup Center**.
1. På sidan **säkerhets kopierings Center** väljer du **valv**.

   ![Välj valv i Backup Center](./media/backup-managed-disks/backup-center.png)

1. På skärmen **initiera: skapa valv** väljer du **säkerhets kopierings valv** och **fortsätter**.

   ![Starta: skapa valv](./media/backup-managed-disks/initiate-create-vault.png)

1. På fliken **grundläggande** anger du prenumeration, resurs grupp, namn på säkerhets kopierings valv, region och redundans för lagring av säkerhets kopior. Fortsätt genom att välja **Granska + skapa**. Lär dig mer om att [skapa ett säkerhets kopierings valv](./backup-vault-overview.md#create-a-backup-vault).

   ![Granska och skapa valv](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>Skapa säkerhets kopierings princip

1. I *DemoVault* **Backup-valvet** som du skapade i föregående steg, går du till **säkerhets kopierings principer** och väljer **Lägg till**.

   ![Lägg till säkerhets kopierings princip](./media/backup-managed-disks/backup-policies.png)

1. På fliken **grundläggande** anger du princip namn och väljer **DataSource-typ** som **Azure-disk**. Valvet är redan förifyllt och de valda valv egenskaperna visas.

   >[!NOTE]
   > Även om det valda valvet kan ha inställningen för global redundans, stöder Azure disk backup endast Snapshot-datalager. Alla säkerhets kopior lagras i en resurs grupp i din prenumeration och kopieras inte till lagringen för säkerhets kopierings valv.

   ![Välj typ av data Källa](./media/backup-managed-disks/datasource-type.png)

1. På fliken **säkerhets kopierings princip** väljer du frekvens för säkerhets kopierings schema.

   ![Välj frekvens för säkerhets kopierings schema](./media/backup-managed-disks/backup-schedule-frequency.png)

   Azure disk Backup erbjuder flera säkerhets kopior per dag. Om du behöver mer frekventa säkerhets kopieringar väljer du säkerhets kopierings frekvensen varje **timme** med möjlighet att göra säkerhets kopior med intervaller var fjärde, 6, 8 eller 12 timmar. Säkerhets kopiorna schemaläggs utifrån det **tidsintervall** som valts. Om du till exempel väljer **var fjärde timme** så tas säkerhets kopiorna ungefär i intervallet var fjärde timme, så att säkerhets kopiorna distribueras jämnt över dagen. Om det räcker med en sådan säkerhets kopiering kan du välja den **dagliga** säkerhets kopierings frekvensen. I frekvensen för daglig säkerhets kopiering kan du ange tiden på dagen då dina säkerhets kopieringar görs. Det är viktigt att notera att tiden på dagen anger start tiden för säkerhets kopieringen och inte tidpunkten då säkerhets kopieringen slutförts. Tiden som krävs för att slutföra säkerhets kopierings åtgärden beror på olika faktorer, inklusive storleken på disken och omsättnings takten mellan på varandra följande säkerhets kopieringar. Säkerhets kopiering av Azure-diskar är dock en agent utan agent som använder [stegvisa ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md), vilket inte påverkar produktions programmets prestanda.

1. På fliken **säkerhets kopierings policy** väljer du inställningar för kvarhållning som uppfyller kravet på återställnings punkt mål.

   Standard regeln för kvarhållning gäller om ingen annan bevarande regel anges. Standard bevarande regeln kan ändras för att ändra kvarhållningsperioden, men den kan inte tas bort. Du kan lägga till en ny bevarande regel genom att välja **Lägg till bevarande regel**.

   ![Lägg till en bevarande regel](./media/backup-managed-disks/add-retention-rule.png)

   Du kan välja den **första genomförda säkerhets kopieringen** varje dag eller varje vecka, och ange hur länge de enskilda säkerhets kopiorna ska behållas innan de tas bort. Det här alternativet är användbart om du vill behålla vissa säkerhets kopior av dagen eller veckan under en längre tids period. Alla andra frekventa säkerhets kopieringar kan behållas under kortare tid.

   ![Inställningar för kvarhållning](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >Azure Backup för Managed Disks använder stegvisa ögonblicks bilder som är begränsade till 200 ögonblicks bilder per disk. För att du ska kunna ta säkerhets kopior på begäran från schemalagda säkerhets kopieringar begränsar säkerhets kopierings principen den totala säkerhets kopian till 180. Läs mer om [stegvisa ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md#restrictions) för hanterad disk.

1. Slutför skapandet av säkerhets kopierings principen genom att välja **Granska + skapa**.

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering

Säkerhets kopierings valv använder hanterad identitet för att få åtkomst till andra Azure-resurser. För att kunna konfigurera säkerhets kopiering av hanterade diskar kräver säkerhets kopierings valvets hanterade identitet en uppsättning behörigheter på de käll diskar och resurs grupper där ögonblicks bilder skapas och hanteras.

En systemtilldelad hanterad identitet är begränsad till en per resurs och är knuten till den här resursens livs cykel. Du kan bevilja behörighet till den hanterade identiteten med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Hanterad identitet är ett tjänst objekt av en särskild typ som bara kan användas med Azure-resurser. Läs mer om [hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md).

Följande förutsättningar måste vara uppfyllda för att säkerhets kopiering av hanterade diskar ska kunna konfigureras:

1. Tilldela rollen **disk säkerhets kopierings läsare** till säkerhets kopierings valvets hanterade identitet på käll disken som ska säkerhets kopie ras.

   1. Gå till den disk som ska säkerhets kopie ras.

   1. Gå till **åtkomst kontroll (IAM)** och välj **Lägg till roll tilldelningar**

   1. I rutan till höger kontext väljer du **disk säkerhets kopierings läsare** i list rutan **roll** . Välj den hanterade identiteten för säkerhets kopierings valvet och **Spara**.

   >[!TIP]
   >Ange namnet på säkerhets kopierings valvet för att välja valvets hanterade identitet.

   ![Lägg till rollen disk säkerhets kopierings läsare](./media/backup-managed-disks/disk-backup-reader-role.png)

1. Tilldela rollen för **disk ögonblicks bild deltagare** till säkerhets kopierings valvets hanterade identitet i resurs gruppen där säkerhets kopior skapas och hanteras av Azure backups tjänsten. Disk ögonblicks bilderna lagras i en resurs grupp i din prenumeration. Om du vill tillåta Azure Backup tjänst att skapa, lagra och hantera ögonblicks bilder måste du ange behörigheter till säkerhets kopierings valvet.

   **Välja resurs grupp för att lagra och hantera ögonblicks bilder:**

   - Välj inte samma resurs grupp som käll disken.

   - Som en rikt linje rekommenderar vi att du skapar en dedikerad resurs grupp som ett ögonblicks bild data lager som ska användas av den Azure Backup tjänsten. Genom att ha en dedikerad resurs grupp kan du begränsa åtkomst behörigheterna för resurs gruppen, vilket ger säkerhet och enkel hantering av säkerhets kopierings data.

   - Du kan använda den här resurs gruppen för att lagra ögonblicks bilder på flera diskar som ska säkerhets kopie ras (eller planerade).  

   - Du kan inte skapa en stegvis ögonblicks bild för en viss disk utanför diskens prenumeration. Välj därför resurs gruppen i samma prenumeration som den disk som ska säkerhets kopie ras. Läs mer om [stegvis ögonblicks bild](../virtual-machines/disks-incremental-snapshots.md#restrictions) för hanterade diskar.

   Följ dessa steg om du vill tilldela rollen:

   1. Gå till resurs gruppen. Till exempel är resurs gruppen *SnapshotRG*, som är i samma prenumeration som den disk som ska säkerhets kopie ras.

   1. Gå till **åtkomst kontroll (IAM)** och välj **Lägg till roll tilldelningar**.

   1. I rutan till höger kontext väljer du **disk ögonblicks bild deltagare** i list rutan **roll** . Välj den hanterade identiteten för säkerhets kopierings valvet och **Spara**.

   >[!TIP]
   >Ange namnet på säkerhets kopierings valvet för att välja valvets hanterade identitet.

   ![Lägg till rollen disk ögonblicks bild deltagare](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. Kontrol lera att den hanterade identiteten för säkerhets kopierings valvet har rätt uppsättning roll tilldelningar på den käll disk och resurs grupp som fungerar som ögonblicks bild data lager.

   1. Gå till **säkerhets kopierings valv – > identitet** och välj **Azure Role-tilldelningar**.

      ![Välj Azure Role-tilldelningar](./media/backup-managed-disks/azure-role-assignments.png)

   1. Kontrol lera att rollen, resurs namnet och resurs typen är korrekt reflekterande.

      ![Verifiera rollen, resurs namnet och resurs typen](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >När roll tilldelningarna visas korrekt på portalen kan det ta ungefär 15 minuter innan behörigheten tillämpas på säkerhets kopierings valvets hanterade identitet.

1. När förutsättningarna är uppfyllda går du till **säkerhets kopierings valv – > översikt** och väljer **säkerhets kopiering** för att börja konfigurera säkerhets kopiering av diskarna.

   ![Välj säkerhets kopia](./media/backup-managed-disks/select-backup.png)

1. På fliken **grundläggande** väljer du **Azure-disk** som typ av data källa.

   ![Välj Azure-disk](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >Azure Backup använder [stegvisa ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md#restrictions) av hanterade diskar, som endast lagrar förändrings ändringarna på disken sedan den senaste ögonblicks bilden på standard HDD lagring, oavsett lagrings typ för den överordnade disken. För ytterligare tillförlitlighet lagras stegvisa ögonblicks bilder i zoner redundant lagring (ZRS) som standard i regioner som stöder ZRS. För närvarande stöder Azure disk backup drift säkerhets kopiering av hanterade diskar som inte kopierar säkerhets kopior till lagring med säkerhets kopierings valv. Inställningen för redundans för säkerhets kopiering för säkerhets kopierings valv gäller inte för återställnings punkterna.

1. På fliken **säkerhets kopierings princip** väljer du en säkerhets kopierings princip.

   ![Välj säkerhets kopierings princip](./media/backup-managed-disks/choose-backup-policy.png)

1. På fliken **resurser** väljer du **Välj Azure-disk**. I rutan till höger kontext väljer du de diskar som ska säkerhets kopie ras.

   ![Välj diskar som ska säkerhets kopie ras](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >När portalen låter dig välja flera diskar och konfigurera säkerhets kopiering, är varje disk en enskild säkerhets kopierings instans. För närvarande stöder Azure disk backup endast säkerhets kopiering av enskilda diskar. Det finns inte stöd för säkerhets kopiering av flera diskar som är anslutna till en virtuell disk vid en viss tidpunkt.
   >
   >När du använder portalen är du begränsad till att välja diskar i samma prenumeration. Om du har flera diskar som ska säkerhets kopie ras eller om diskarna är spridda över olika prenumerationer kan du använda skript för att automatisera.
   >
   >Mer information om tillgänglighet för Azure disk backup, stödda scenarier och begränsningar finns i [support mat ris](disk-backup-support-matrix.md).

1. Välj en **resurs grupp för ögonblicks bilder** och välj **Verifiera**. Det här är resurs gruppen där Azure Backups tjänsten skapade och hanterar de stegvisa ögonblicks bilderna för säkerhets kopierings valvet. Hanterad identitet tilldelas till de nödvändiga roll behörigheterna som en del av förutsättningarna.

   ![Välj resurs grupp för ögonblicks bild](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >Det kan ta några minuter innan verifieringen är klar innan du kan slutföra konfigurationen av arbets flöde för säkerhets kopiering. Verifieringen kan Miss lyckas om:
   >
   > - en disk stöds inte. Se [support mat ris](disk-backup-support-matrix.md) för scenarier som inte stöds.
   > - den hanterade identiteten för säkerhets kopierings valvet har inte några giltiga roll tilldelningar på **disken** som ska säkerhets kopie ras eller på den **resurs grupp för ögonblicks bilder** där stegvisa ögonblicks bilder lagras.

1. När verifieringen är klar väljer du **Granska och konfigurera** för att konfigurera säkerhets kopieringen av de valda diskarna.

## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

1. I *DemoVault* **Backup-valvet** som du skapade i föregående steg, går du till **säkerhets kopierings instanser** och väljer en säkerhets kopierings instans.

   ![Välj säkerhets kopierings instans](./media/backup-managed-disks/select-backup-instance.png)

1. På skärmen **säkerhets kopierings instanser** hittar du:

   - **viktig** information, inklusive käll disk namn, resurs grupp för ögonblicks bilder där stegvisa ögonblicks bilder lagras, säkerhets kopierings valv och säkerhets kopierings principer.
   - **Jobb status** visar en sammanfattning av säkerhets kopierings-och återställnings åtgärderna och deras status under de senaste sju dagarna.
   - En lista över **återställnings punkter** för den valda tids perioden.

1. Välj **säkerhets kopiering** för att initiera en säkerhets kopiering på begäran.

   ![Välj Säkerhetskopiera nu](./media/backup-managed-disks/backup-now.png)

1. Välj en av de bevarande regler som är associerade med säkerhets kopierings principen. Den här bevarande regeln avgör Retentions tiden för säkerhets kopiering på begäran. Klicka på **Säkerhetskopiera nu** för att starta säkerhets kopieringen.

   ![Initiera säkerhets kopiering](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>Spåra en säkerhets kopierings åtgärd

Azure Backups tjänsten skapar ett jobb för schemalagda säkerhets kopieringar eller om du utlöser säkerhets kopiering på begäran för spårning. Så här visar du status för säkerhets kopierings jobb:

1. Gå till sidan **säkerhets kopierings instans** . Den visar instrument panelen jobb med drift och status för de senaste sju dagarna.

   ![Instrument panel för jobb](./media/backup-managed-disks/jobs-dashboard.png)

1. Om du vill visa status för säkerhets kopieringen väljer du **Visa alla** för att visa pågående och tidigare jobb för den här säkerhets kopierings instansen.

   ![Välj Visa alla](./media/backup-managed-disks/view-all.png)

1. Granska listan över säkerhets kopierings-och återställnings jobb och deras status. Välj ett jobb i listan med jobb för att visa jobb information.

   ![Välj jobb för att visa information](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>Nästa steg

- [Återställa Azure-Managed Disks](restore-managed-disks.md)