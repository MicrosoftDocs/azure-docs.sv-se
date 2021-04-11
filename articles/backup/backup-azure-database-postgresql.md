---
title: Säkerhetskopiera Azure Database for PostgreSQL
description: Läs mer om Azure Database for PostgreSQL säkerhets kopiering med långsiktig kvarhållning (för hands version)
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: 5eba9d78dda45197c0d1e92195980f3d731734a8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011724"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Azure Database for PostgreSQL säkerhets kopiering med långsiktig kvarhållning (för hands version)

Azure Backup och Azure Database-tjänster har tillsammans skapat en lösning för säkerhets kopiering i företags klass för Azure Database for PostgreSQL servrar som behåller säkerhets kopior i upp till 10 år.

Förutom långsiktig kvarhållning har lösningen även många andra funktioner som anges nedan:

- Rollbaserad åtkomst kontroll i Azure (Azure RBAC) till databasen med hjälp av Azure Active Directory-och Hanterad tjänstidentitet-autentisering (MSI).
- Kund kontrollerad schemalagd och säkerhets kopiering på begäran på den enskilda databas nivån.
- Databas nivån återställs till en postgres-Server eller direkt till Blob Storage.
- Långsiktig kvarhållning.
- Central övervakning av alla åtgärder och jobb.
- Säkerhets kopior lagras i separata säkerhets-och fel domäner. Så även om käll servern skulle komprometteras eller till och med stoppas, skulle säkerhets kopieringarna vara skyddade i [säkerhets kopierings valvet](backup-vault-overview.md).
- Användningen av **pg_dump** ger större flexibilitet i återställning, så att du kan återställa över databas versioner eller till och med bara återställa en del av säkerhets kopian.

Du kan använda den här lösningen fristående eller utöver den inbyggda säkerhets kopierings lösningen som erbjuds av Azure-PostgreSQL som erbjuder kvarhållning upp till 35 dagar. Den interna lösningen passar för drifts återställningar, till exempel när du vill återställa från de senaste säkerhets kopiorna. Azure Backup-lösningen hjälper dig med dina behov av efterlevnad och mer detaljerad och flexibel säkerhets kopiering och återställning.

## <a name="support-matrix"></a>Stödmatris

|Support  |Information  |
|---------|---------|
|Distributioner som stöds   |  [Azure Database for PostgreSQL – enskild server](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Azure-regioner som stöds |  USA, östra USA, östra USA 2, centrala USA, södra centrala USA, västra USA, västra USA 2, västra centrala USA, södra Brasilien, centrala Kanada, Nord Europa, Västeuropa, Storbritannien, södra, Storbritannien, västra, Tyskland, västra centrala, Schweiz, norra, Schweiz, västra, Asien, östra, USA, östra, västra Japan, centrala USA, södra centrala USA, östra Australien, centrala Australien 2, östra  |
|Azure PostgreSQL-versioner som stöds    |   9,5, 9,6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Funktions överväganden och begränsningar

- Alla åtgärder stöds endast från Azure Portal.
- Rekommenderad gräns för maximal databas storlek är 400 GB.
- Säkerhets kopiering mellan regioner stöds inte. Det innebär att du inte kan säkerhetskopiera en Azure PostgreSQL-server till ett valv i en annan region. På samma sätt kan du bara återställa en säkerhets kopia till en server inom samma region som valvet.
- Endast data återställs vid tidpunkten för återställningen. "Roller" har inte återställts.
- I för hands versionen rekommenderar vi att du bara kör lösningen i din test miljö.

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

1. Den här lösningen använder **pg_dump** för att ta säkerhets kopior av dina Azure postgresql-databaser.

2. När du har angett de Azure PostgreSQL-databaser som du vill säkerhetskopiera, verifierar tjänsten om den har rätt uppsättning behörigheter och åtkomst för att utföra säkerhets kopieringen på servern och databasen.

3. Azure Backup skapar en arbets roll med ett säkerhets kopierings tillägg installerat. Det här tillägget kommunicerar med postgres-servern.

4. Tillägget består av en koordinator och ett Azure postgres-pluginprogram. Även om koordinatorn ansvarar för att utlösa arbets flöden för olika åtgärder som att konfigurera säkerhets kopiering, säkerhets kopiering och återställning, är plugin-programmet ansvarigt för det faktiska data flödet.
  
5. När du har aktiverat konfigurera skydd för de valda databaserna konfigurerar säkerhets kopierings tjänsten koordinatorn med säkerhets kopierings scheman och annan princip information.

6. Vid den schemalagda tiden kommunicerar koordinatorn med plugin-programmet och börjar strömma säkerhetskopierade data från postgres-servern med hjälp av **pg_dump**.

7. Plugin-programmet skickar data direkt till säkerhets kopierings valvet, vilket eliminerar behovet av en mellanlagringsplats. Data krypteras med hjälp av Microsoft-hanterade nycklar och lagras av Azure Backup-tjänsten i lagrings konton.

8. När data överföringen är slutförd bekräftar koordinatorn incheckningen med säkerhets kopierings tjänsten.

    ![Säkerhetskopieringsprocessen](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Konfigurera säkerhets kopiering på Azure PostgreSQL-databaser

Du kan konfigurera säkerhets kopiering på flera databaser över flera Azure PostgreSQL-servrar. Se till att de [nödvändiga behörigheter](#prerequisite-permissions-for-configure-backup-and-restore) som krävs av tjänsten för att säkerhetskopiera postgres-servrarna redan har kon figurer ATS.

Följande instruktioner är en steg-för-steg-guide för att konfigurera säkerhets kopiering på Azure PostgreSQL-databaser med hjälp av Azure Backup:

1. Det finns två sätt att starta processen:

    1. Gå till [säkerhets kopierings Center](backup-center-overview.md)  ->  **– Översikt**  ->  **säkerhets kopiering**.

        ![Gå till säkerhets kopierings Center](./media/backup-azure-database-postgresql/backup-center.png)

        Under **initiera: Konfigurera säkerhets kopiering** väljer du **data källans typ** som **Azure Database for PostgreSQL**.

        ![I starta: Konfigurera säkerhets kopiering väljer du typ av data Källa](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. Du kan också gå direkt till säkerhets kopierings [valv](backup-vault-overview.md)  ->  .

        ![Gå till säkerhets kopierings valv](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Välj säkerhets kopiering i säkerhets kopierings valv](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. Under **Konfigurera säkerhets kopiering** väljer du det **säkerhets kopierings valv** som du vill säkerhetskopiera postgres-databaser till. Den här informationen fylls i i förväg om du redan befinner dig i valv kontexten.

    ![Välj säkerhets kopierings valv i Konfigurera säkerhets kopiering](./media/backup-azure-database-postgresql/configure-backup.png)

1. Välj eller skapa en **säkerhets kopierings princip**.

    ![Välj säkerhets kopierings princip](./media/backup-azure-database-postgresql/backup-policy.png)

1. Välj resurser eller postgres-databaser som ska säkerhets kopie ras.

    ![Välj vilka resurser som ska säkerhets kopie ras](./media/backup-azure-database-postgresql/select-resources.png)

1. Du kan välja i listan över alla Azure PostgreSQL-servrar över prenumerationer om de befinner sig i samma region som valvet. Expandera pilen om du vill se en lista över databaser i en server.

    ![Välj servrar](./media/backup-azure-database-postgresql/choose-servers.png)

1. Tjänsten kör dessa kontroller på de valda databaserna för att kontrol lera om valvet har behörighet att säkerhetskopiera valda postgres-servrar och databaser.
    1. **Beredskap för säkerhets kopiering** för alla databaser bör läsas **klart** för att fortsätta.
    1. Om det uppstår ett fel kan du antingen **åtgärda** felet och **Verifiera** eller ta bort databasen från valen.

    ![Verifierings fel som ska åtgärdas](./media/backup-azure-database-postgresql/validation-errors.png)

1. Bekräfta all information under **Granska och konfigurera** och välj **Konfigurera säkerhets kopiering** för att skicka åtgärden.

    ![Bekräfta information i granska och konfigurera](./media/backup-azure-database-postgresql/review-and-configure.png)

1. När den här åtgärden har utlösts skapas en **säkerhets kopierings** instans. Du kan spåra status för åtgärden under fönstret [säkerhets kopierings instanser](backup-center-monitor-operate.md#backup-instances) i säkerhets kopierings Center-eller valv-vyn.

    ![Säkerhets kopierings instanser](./media/backup-azure-database-postgresql/backup-instances.png)

1. Säkerhets kopiorna utlöses enligt det schema för säkerhets kopiering som definierats i principen. Jobben kan spåras under [säkerhets kopierings jobb](backup-center-monitor-operate.md#backup-jobs). För närvarande kan du Visa jobb under de senaste sju dagarna.

    ![Säkerhets kopierings jobb](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Skapa säkerhets kopierings princip

1. Gå till **säkerhets kopierings Center**  ->  **säkerhets kopierings principer**  ->  **Lägg till**. Du kan också **gå till säkerhets** kopierings  ->  **policyn**  ->  **Lägg till** säkerhets kopiering.

    ![Lägg till säkerhets kopierings princip](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Ange ett **namn** för den nya principen.

    ![Ange princip namn](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Definiera schemat för säkerhets kopiering. Vi stöder för närvarande **veckovis** säkerhets kopiering. Du kan schemalägga säkerhets kopieringar på en eller flera dagar i veckan.

    ![Definiera schema för säkerhets kopiering](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. Definiera inställningar för **kvarhållning** . Du kan lägga till en eller flera regler för kvarhållning. Varje kvarhållning regel förutsätter indata för vissa säkerhets kopieringar och data lager och Retentions tid för dessa säkerhets kopior.

1. Du kan välja att lagra säkerhets kopior i ett av de två data lager (eller-nivåerna): **säkerhets kopierings data lager** (standard nivå) eller **Arkiv data lager** (i för hands version).

   Du kan välja **förfallo datum om** du vill flytta säkerhets kopian för att arkivera data lagret när det upphör att gälla i säkerhets kopierings data lagret.

1. **Standard regeln för kvarhållning** tillämpas om ingen annan bevarande regel har angetts och har ett standardvärde på tre månader.

    - Varaktighets intervallet för kvarhållning från sju dagar till 10 år i **säkerhets kopierings data lagret**.
    - Varaktighets intervallet för kvarhållning från sex månader till 10 år i **Arkiv data lagret**.

    ![Redigera varaktighet för kvarhållning](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>Reglerna för kvarhållning utvärderas i en fördefinierad prioritetsordning. Prioriteten är högsta för **års** regeln, följt av **varje månad** och sedan **vecko** regeln. Standardinställningar för kvarhållning tillämpas när inga andra regler är kvalificerade. Samma återställnings punkt kan till exempel vara den första genomförda säkerhets kopieringen varje vecka och den första genomförda säkerhets kopieringen varje månad. Men eftersom den månatliga regel prioriteten är högre än för vecko regeln, gäller den kvarhållning som motsvarar den första genomförda säkerhets kopieringen som görs varje månad.

## <a name="restore"></a>Återställ

Du kan återställa en databas till alla Azure PostgreSQL-servrar i samma prenumeration, om tjänsten har rätt behörighet på mål servern. Se till att de nödvändiga [behörigheter](#prerequisite-permissions-for-configure-backup-and-restore) som krävs av tjänsten för att säkerhetskopiera postgres-servrarna redan har kon figurer ATS.

Följ den här steg-för-steg-guiden för att utlösa en återställning:

1. Det finns två sätt att starta återställnings processen:
    1. Gå till [Backup Center](backup-center-overview.md)-  ->  **översikten**  ->  **återställning**.

    ![Välj Återställ i säkerhets kopierings Center](./media/backup-azure-database-postgresql/backup-center-restore.png)

    Under **initiera: Återställ** väljer du **data källans typ** som **Azure Database for PostgreSQL**. Välj **säkerhets kopierings instans**.

    ![Välj DataSource-typ i initiera: Återställ](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. Du kan också gå direkt till säkerhets kopierings **valvets**  ->  **säkerhets kopierings instanser**. Välj den **säkerhets kopierings instans** som motsvarar den databas som du vill återställa.

    ![Säkerhets kopierings instanser för återställning](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Lista över säkerhets kopierings instanser](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Välj Återställ](./media/backup-azure-database-postgresql/select-restore.png)

1. **Välj återställnings punkt** i listan över alla fullständiga säkerhets kopior som är tillgängliga för den valda säkerhets kopierings instansen. Som standard väljs den senaste återställnings punkten.

    ![Välj återställnings punkt](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Lista över återställnings punkter](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. Parametrar för att **återställa** information. I det här läget kan du välja mellan två typer av återställningar: **Återställ som databas** och **Återställ som filer**.

1. **Återställ som databas**: Återställ säkerhets kopierings data för att skapa en ny databas på mål postgresql-servern.

    - Mål servern kan vara samma som käll servern. Det finns dock inte stöd för att skriva över den ursprungliga databasen.
    - Du kan välja från servern för alla prenumerationer, men i samma region som valvet.
    - Välj **Granska + Återställ**. Detta utlöser verifieringen för att kontrol lera om tjänsten har rätt återställnings behörighet på mål servern.

    ![Återställ som databas](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Återställ som filer**: dumpa säkerhetskopieringsfilerna till mål lagrings kontot (blobbar).

    - Du kan välja mellan lagrings kontona för alla prenumerationer, men i samma region som valvet.
    - Välj mål containern från den filtrerade behållar listan för det valda lagrings kontot.
    - Välj **Granska + Återställ**. Detta utlöser verifieringen för att kontrol lera om tjänsten har rätt återställnings behörighet på mål servern.

    ![Återställ som filer](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Om återställnings punkten finns på Arkiv nivå måste du utföra återställnings punkten innan du återställer.
   
   ![ÅTERUPPVÄCKNING-inställningar](./media/backup-azure-database-postgresql/rehydration-settings.png)
   
   Ange följande ytterligare parametrar som krävs för ÅTERUPPVÄCKNING:
   - **ÅTERUPPVÄCKNING prioritet:** Standardvärdet **är standard**.
   - **ÅTERUPPVÄCKNING varaktighet:** Den maximala ÅTERUPPVÄCKNING varaktigheten är 30 dagar och den minsta ÅTERUPPVÄCKNING-varaktigheten är 10 dagar. Standardvärdet är **15**.
   
   Återställnings punkten lagras i **säkerhets kopierings data lagret** under den angivna ÅTERUPPVÄCKNING varaktigheten.


1. Granska informationen och välj **Återställ**. Detta utlöser ett motsvarande återställnings jobb som kan spåras under **säkerhets kopierings jobb**.

>[!NOTE]
>Arkiv stöd för Azure Database for PostgreSQL är i begränsad offentlig för hands version.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Nödvändiga behörigheter för att konfigurera säkerhets kopiering och återställning

Azure Backup följer strikta säkerhets rikt linjer. Även om det är en inbyggd Azure-tjänst, förmodas inte behörigheter på resursen och måste uttryckligen anges av användaren.  På samma sätt lagras inte autentiseringsuppgifter för att ansluta till databasen. Detta är viktigt för att skydda dina data. Vi använder i stället Azure Active Directory autentisering.

[Hämta det här dokumentet](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) för att få ett automatiserat skript och relaterade instruktioner. Den ger en lämplig uppsättning behörigheter till en Azure PostgreSQL-Server för säkerhets kopiering och återställning.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>Hantera de säkerhetskopierade Azure PostgreSQL-databaserna

Detta är de hanterings åtgärder som du kan utföra på **säkerhets kopierings instanserna**:

### <a name="on-demand-backup"></a>Säkerhets kopiering på begäran

Om du vill utlösa en säkerhets kopia som inte finns i det schema som anges i principen går du till säkerhets **kopierings instanser**  ->  **nu**.
Välj i listan över bevarande regler som definierades i den associerade säkerhets kopierings principen.

![Utlös säkerhets kopiering nu](./media/backup-azure-database-postgresql/backup-now.png)

![Välj från listan över bevarande regler](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Sluta skydda

Du kan stoppa skyddet av ett säkerhets kopierings objekt. Detta tar även bort tillhör ande återställnings punkter för det säkerhetskopierade objektet. Om återställnings punkterna inte finns på Arkiv nivån i minst sex månader, kommer borttagning av dessa återställnings punkter att medföra kostnad för tidig borttagning. Vi har ännu inte möjlighet att stoppa skyddet samtidigt som de befintliga återställnings punkterna behålls.

![Sluta skydda](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Ändra princip

Du kan ändra den associerade principen med en säkerhets kopierings instans.

1. Välj ändrings princip för **säkerhets kopierings instans**  ->  .

    ![Ändra princip](./media/backup-azure-database-postgresql/change-policy.png)

1. Välj den nya princip som du vill tillämpa på databasen.

    ![Omtilldela princip](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Felsökning

Det här avsnittet innehåller felsöknings information för att säkerhetskopiera Azure PostgreSQL-databaser med Azure Backup.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Ge säkerhets kopierings valvet MSI **Läs** åtkomst på den PG-server som du vill säkerhetskopiera eller återställa:

För att upprätta en säker anslutning till PostgreSQL-databasen använder Azure Backup autentiserings modellen [hanterad tjänstidentitet (MSI)](../active-directory/managed-identities-azure-resources/overview.md) . Det innebär att säkerhets kopierings valvet endast har åtkomst till de resurser som uttryckligen har beviljats behörighet av användaren.

En system-MSI tilldelas automatiskt till valvet vid tidpunkten för skapandet. Du måste ge valvet MSI åtkomst till de PostgreSQL-servrar som du vill säkerhetskopiera databaser från.

Steg:

1. I postgres-servern går du till fönstret **Access Control (IAM)** .

    ![Access Controls fönster](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Välj **Lägg till en roll tilldelning**.

    ![Lägg till rolltilldelning](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. I det högra kontext fönstret som öppnas anger du följande:<br>

    **Roll:** Acrobat<br>
    **Tilldela åtkomst till:** Välj **säkerhets kopierings valv**<br>
    Om du inte hittar alternativet **säkerhets kopierings valv** i list rutan väljer du **alternativet Azure AD-användare, grupp eller tjänstens huvud namn**<br>

    ![Välj roll](./media/backup-azure-database-postgresql/select-role.png)

    **Välj:** Ange namnet på det säkerhets kopierings valv som du vill säkerhetskopiera servern och dess databaser till.<br>

    ![Ange namn på säkerhets kopierings valv](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Skapa en säkerhets kopierings användare för databasen som kan autentiseras med Azure Active Directory:

Det här felet kan komma från en frånvaro av en Azure Active Directory administratör för PostgreSQL-servern eller i frånvaro av en säkerhets kopierings användare som kan autentisera med hjälp av Azure Active Directory.

Steg:

Lägg till en Active Directory-administratör till OSS-servern:

Det här steget krävs för att ansluta till databasen via en användare som kan autentisera med Azure Active Directory i stället för ett lösen ord. Azure AD admin-användaren i Azure Database for PostgreSQL kommer att ha rollen **azure_ad_admin**. Endast en **azure_ad_admin** roll kan skapa nya databas användare som kan autentiseras med Azure AD.

1. Gå till fliken Active Directory administratör i det vänstra navigerings fönstret i vyn Server och Lägg till dig själv (eller någon annan) som Active Directory administratören.

    ![Ange Active Directory administratör](./media/backup-azure-database-postgresql/set-admin.png)

1. Se till att **Spara** användar inställningen för AD-administratör.

    ![Spara Active Directory administratörs användar inställning](./media/backup-azure-database-postgresql/save-admin-setting.png)

I [det här dokumentet](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) hittar du en lista över de steg som du måste utföra för att slutföra de steg som krävs för godkännande.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Etablera nätverks linjen genom att aktivera flaggan **Tillåt åtkomst till Azure-tjänster** i vyn Server. I vyn Server, under fönstret **anslutnings säkerhet** , ställer du in flaggan **Tillåt åtkomst till Azure-tjänster** på **Ja**.

![Tillåt åtkomst till Azure-tjänster](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Behörighet att återställa till en lagrings konto behållare vid återställning som filer

1. Ge säkerhets kopierings valvet MSI behörighet att komma åt lagrings konto behållare med hjälp av Azure Portal.
    1. Gå till **lagrings konto**  ->  **Access Control**  ->  **Lägg till roll tilldelning**.
    1. Tilldela rollen **Storage BLOB data Contributor** till säkerhets kopierings valvets MSI.

    ![Tilldela rollen Storage BLOB data Contributor](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Du kan också ge detaljerad behörighet till den angivna behållaren som du återställer till genom att använda kommandot [skapa roll tilldelning](/cli/azure/role/assignment) i Azure CLI-AZ.

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Ersätt tilldelnings parametern med **program-ID: t** för valvets MSI och omfattnings parametern för att referera till din specifika behållare.
    1. Om du vill hämta **program-ID: t** för valvet MSI väljer du **alla program** under **program typ**:

        ![Välj alla program](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Sök efter valv namnet och kopiera program-ID: t:

        ![Sök efter valv namn](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Nästa steg

- [Översikt över säkerhets kopierings valv](backup-vault-overview.md)