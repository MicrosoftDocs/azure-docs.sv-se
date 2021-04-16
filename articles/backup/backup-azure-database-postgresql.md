---
title: Säkerhetskopiera Azure Database for PostgreSQL
description: Lär dig Azure Database for PostgreSQL säkerhetskopiering med långsiktig kvarhållning (förhandsversion)
ms.topic: conceptual
ms.date: 04/12/2021
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 8fd69e016c7f0b175ef49b98add5692743858f62
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480077"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Azure Database for PostgreSQL säkerhetskopiering med långsiktig kvarhållning (förhandsversion)

Azure Backup och Azure Database Services har gått samman för att skapa en säkerhetskopieringslösning i företagsklass för Azure Database for PostgreSQL-servrar som behåller säkerhetskopior i upp till 10 år.

Förutom långsiktig kvarhållning har lösningen även många andra funktioner som anges nedan:

- Rollbaserad åtkomstkontroll i Azure (Azure RBAC) till databasen med hjälp Azure Active Directory autentisering med hanterad tjänstidentitet (MSI).
- Kundkontrollerad schemalagd säkerhetskopiering och säkerhetskopiering på begäran på enskild databasnivå.
- På databasnivå återställs till valfri Postgres-server eller direkt till bloblagring.
- Långsiktig kvarhållning.
- Central övervakning av alla åtgärder och jobb.
- Säkerhetskopior lagras i separata säkerhets- och feldomäner. Så även om källservern komprometteras eller till och med skulle tas bort, förblir säkerhetskopiorna säkra i [Säkerhetskopieringsvalvet.](backup-vault-overview.md)
- Användning av **pg_dump** ger större flexibilitet vid återställningar så att du kan återställa över databasversioner eller till och med återställa endast en del av säkerhetskopian.

Du kan använda den här lösningen separat eller utöver den inbyggda säkerhetskopieringslösning som erbjuds av Azure PostgreSQL och som erbjuder kvarhållning i upp till 35 dagar. Den interna lösningen lämpar sig för driftsmässiga återställningar, till exempel när du vill återställa från de senaste säkerhetskopiorna. Lösningen Azure Backup hjälper dig med dina efterlevnadsbehov och mer detaljerad och flexibel säkerhetskopiering och återställning.

## <a name="support-matrix"></a>Stödmatris

|Support  |Information  |
|---------|---------|
|Distributioner som stöds   |  [Azure Database for PostgreSQL – enskild server](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Azure-regioner som stöds |  USA, östra, USA, östra 2, USA, centrala, USA, södra centrala, USA, västra, USA, västra centrala, Brasilien, södra, Kanada, centrala, Europa, norra, Europa, västra, Storbritannien, södra, Storbritannien, västra, Tyskland, västra centrala, Schweiz, norra, Schweiz, västra, Asien, östra, Asien, sydöstra, Japan, östra, Japan, västra, Sydkorea, centrala, Sydkorea, södra, Indien, centrala, Australien, östra, Australien, centrala, Australien, centrala 2, FÖRENADE ARABEMIRATEN, norra  |
|Azure PostgreSQL-versioner som stöds    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Överväganden och begränsningar för funktioner

- Alla åtgärder stöds endast från Azure Portal enheter.
- Rekommenderad gräns för maximal databasstorlek är 400 GB.
- Säkerhetskopiering mellan regioner stöds inte. Det innebär att du inte kan servera en Azure PostgreSQL-server till ett valv i en annan region. På samma sätt kan du bara återställa en säkerhetskopia till en server inom samma region som valvet.
- Endast data återställs vid tidpunkten för återställningen. "Roller" återställs inte.
- I förhandsversionen rekommenderar vi att du bara kör lösningen i testmiljön.

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

1. Den här lösningen **pg_dump** att göra säkerhetskopior av dina Azure PostgreSQL-databaser.

2. När du har angett de Azure PostgreSQL-databaser som du vill säkerhetskopiera verifierar tjänsten om den har rätt uppsättning behörigheter och åtkomst för att utföra säkerhetskopieringen på servern och databasen.

3. Azure Backup en arbetsroll med ett tillägg för säkerhetskopiering installerat. Det här tillägget kommunicerar med Postgres-servern.

4. Det här tillägget består av en koordinator och ett Azure Postgres-plugin-program. Koordinatorn ansvarar för att utlösa arbetsflöden för olika åtgärder som att konfigurera säkerhetskopiering, säkerhetskopiering och återställning, men plugin-programmet ansvarar för det faktiska dataflödet.
  
5. När du utlöser konfigurationsskydd på de valda databaserna konfigurerar säkerhetskopieringstjänsten koordinatorn med säkerhetskopieringsscheman och annan principinformation.

6. Vid den schemalagda tiden kommunicerar koordinatorn med plugin-programmet och börjar strömma säkerhetskopierade data från Postgres-servern med **hjälp av pg_dump**.

7. Plugin-programmet skickar data direkt till Backup-valvet, vilket eliminerar behovet av en mellanlagringsplats. Data krypteras med Microsoft-hanterade nycklar och lagras av Azure Backup tjänsten i lagringskonton.

8. När dataöverföringen är klar bekräftar koordinatorn genomförande med säkerhetskopieringstjänsten.

    ![Säkerhetskopieringsprocessen](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Konfigurera säkerhetskopiering på Azure PostgreSQL-databaser

Du kan konfigurera säkerhetskopiering på flera databaser över flera Azure PostgreSQL-servrar. Se till att [de nödvändiga behörigheter](#prerequisite-permissions-for-configure-backup-and-restore) som krävs av tjänsten för att konfigurerar Postgres-servrarna redan har konfigurerats.

Följande anvisningar är en stegvis guide för att konfigurera säkerhetskopiering på Azure PostgreSQL-databaser med hjälp av Azure Backup:

1. Det finns två sätt att påbörja processen:

    1. Gå till [Säkerhetskopieringscenter](backup-center-overview.md)  ->  **– översiktssäkerhetskopia.**  ->  

        ![Gå till Backup Center](./media/backup-azure-database-postgresql/backup-center.png)

        Under **Initiera: Konfigurera säkerhetskopiering** väljer du **typen Datakälla** som **Azure Database for PostgreSQL**.

        ![I Initiate: Configure Backup (Initiera: Konfigurera säkerhetskopiering) väljer du Datasource Type (Typ av datakälla)](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. Du kan också gå direkt till [Säkerhetskopieringsvalv](backup-vault-overview.md)  ->  **Säkerhetskopiera**.

        ![Gå till Säkerhetskopieringsvalv](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Välj Säkerhetskopiering i Backup-valv](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. Under **Konfigurera säkerhetskopiering** väljer du **det säkerhetskopieringsvalv** som du vill säkerhetskopiera Postgres-databaserna till. Den här informationen är ifylld i förväg om du redan är i valvkontexten.

    ![Välj Säkerhetskopieringsvalv i Konfigurera säkerhetskopiering](./media/backup-azure-database-postgresql/configure-backup.png)

1. Välj eller skapa en **säkerhetskopieringspolicy.**

    ![Välj Säkerhetskopieringspolicy](./media/backup-azure-database-postgresql/backup-policy.png)

1. Välj resurser eller Postgres-databaser som ska backa upp.

    ![Välj resurser som ska backa upp](./media/backup-azure-database-postgresql/select-resources.png)

1. Du kan välja från listan över alla Azure PostgreSQL-servrar över prenumerationer om de finns i samma region som valvet. Expandera pilen för att se listan över databaser på en server.

    ![Välj servrar](./media/backup-azure-database-postgresql/choose-servers.png)

1. Tjänsten kör dessa kontroller på de valda databaserna för att verifiera huruvida valvet har behörighet att servera de valda Postgres-servrarna och databaserna.
    1. **Beredskap för säkerhetskopiering** för alla databaser bör **läsa Klart** för att fortsätta.
    1. Om det uppstår ett fel kan du **antingen** åtgärda felet **och göra en** ny uppdatering eller ta bort databasen från valen.

    ![Verifieringsfel för att åtgärda](./media/backup-azure-database-postgresql/validation-errors.png)

1. Bekräfta all information under Granska **och konfigurera och välj** Konfigurera **säkerhetskopiering** för att skicka åtgärden.

    ![Bekräfta informationen i Granska och konfigurera](./media/backup-azure-database-postgresql/review-and-configure.png)

1. När den har utlösts **skapar åtgärden Konfigurera** säkerhetskopiering en säkerhetskopieringsinstans. Du kan spåra status för åtgärden i fönstret [Säkerhetskopieringsinstanser](backup-center-monitor-operate.md#backup-instances) i vyn Backup Center eller valvet.

    ![Säkerhetskopieringsinstanser](./media/backup-azure-database-postgresql/backup-instances.png)

1. Säkerhetskopieringarna utlöses enligt det säkerhetskopieringsschema som definierats i principen. Jobben kan spåras under [Säkerhetskopieringsjobb.](backup-center-monitor-operate.md#backup-jobs) För närvarande kan du visa jobb för de senaste sju dagarna.

    ![Säkerhetskopieringsjobb](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Skapa säkerhetskopieringspolicy

1. Gå till **Säkerhetskopieringsprinciper i Backup Center**  ->  **Lägg**  ->  **till**. Du kan också gå till **Säkerhetskopieringsvalv**  ->  **säkerhetskopieringspolicyn**  ->  **Lägg till**.

    ![Lägg till säkerhetskopieringspolicy](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Ange ett **namn** för den nya principen.

    ![Ange principnamn](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Definiera schemat för säkerhetskopiering. Vi stöder för närvarande **veckovis säkerhetskopiering.** Du kan schemalägga säkerhetskopieringarna en eller flera dagar i veckan.

    ![Definiera schemat för säkerhetskopiering](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. Definiera **kvarhållningsinställningar.** Du kan lägga till en eller flera kvarhållningsregler. Varje kvarhållningsregel förutsätter indata för specifika säkerhetskopieringar och datalager och kvarhållningstid för dessa säkerhetskopior.

1. Du kan välja att lagra dina säkerhetskopior i ett av  de två datalager (eller nivåer): Säkerhetskopieringsdatalager (standardnivå) eller **Arkivdatalager** (i förhandsversion).

   Du kan välja **Vid förfallodatum för att flytta** säkerhetskopian för att arkivera datalagret när den upphör att gälla i det säkerhetskopierade datalagret.

1. Standardregeln **för kvarhållning** tillämpas utan någon annan kvarhållningsregel och har standardvärdet tre månader.

    - Kvarhållningen sträcker sig från sju dagar till 10 år i **datalagret för säkerhetskopiering.**
    - Kvarhållningen sträcker sig från sex månader till 10 år i **arkivdatalagret**.

    ![Redigera kvarhållningstid](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>Kvarhållningsreglerna utvärderas i en förbestämd prioritetsordning. Prioriteten är högst för den **årliga regeln,** följt av månadsregeln **och** sedan **veckoregeln.** Standardinställningarna för kvarhållning tillämpas när inga andra regler är kvalificerade. Till exempel kan samma återställningspunkt vara den första lyckade säkerhetskopieringen som tas varje vecka samt den första lyckade säkerhetskopieringen som tas varje månad. Men eftersom den månatliga regelprioritet är högre än veckoregeln gäller kvarhållningen för den första lyckade säkerhetskopieringen som tas varje månad.

## <a name="restore"></a>Återställ

Du kan återställa en databas till valfri Azure PostgreSQL-server inom samma prenumeration, om tjänsten har rätt uppsättning behörigheter på målservern. Se till [att de nödvändiga](#prerequisite-permissions-for-configure-backup-and-restore) behörigheter som krävs av tjänsten för att konfigurerar Postgres-servrarna redan är konfigurerade.

Följ den här stegvisa guiden för att utlösa en återställning:

1. Det finns två sätt att starta återställningsprocessen:
    1. Gå till [Översikt över Backup Center](backup-center-overview.md)  ->  **–**  ->  **Återställa.**

    ![Välj Återställ i Backup Center](./media/backup-azure-database-postgresql/backup-center-restore.png)

    Under **Initiera: Återställ** väljer du **typen Datakälla** som **Azure Database for PostgreSQL**. Välj **säkerhetskopieringsinstansen**.

    ![Välj Typ av datakälla i Initiate:Restore (Initiera:Återställ)](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. Du kan också gå direkt till **Backup-valvsäkerhetskopieringsinstanser.**  ->   Välj **Säkerhetskopieringsinstans** som motsvarar den databas som du vill återställa.

    ![Säkerhetskopieringsinstanser för återställning](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Lista över säkerhetskopieringsinstanser](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Välj Återställ](./media/backup-azure-database-postgresql/select-restore.png)

1. **Välj återställningspunkt** i listan över alla fullständiga säkerhetskopior som är tillgängliga för den valda säkerhetskopieringsinstansen. Som standard är den senaste återställningspunkten markerad.

    ![Välj återställningspunkt](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Lista över återställningspunkter](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. Parametrar för **återställning av indata**. Nu kan du välja mellan två typer av återställningar: **Återställ som databas och** Återställ som **filer.**

1. **Återställ som databas:** Återställ säkerhetskopierade data för att skapa en ny databas på PostgreSQL-målservern.

    - Målservern kan vara samma som källservern. Det finns dock inte stöd för att skriva över den ursprungliga databasen.
    - Du kan välja från servern för alla prenumerationer, men i samma region som valvet.
    - Välj **Granska + återställ.** Detta utlöser validering för att kontrollera om tjänsten har rätt återställningsbehörighet på målservern.

    ![Återställa som databas](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Återställ som filer:** Dumpa säkerhetskopieringsfilerna till mållagringskontot (blobar).

    - Du kan välja mellan lagringskonton för alla prenumerationer, men i samma region som valvet.
    - Välj målcontainern från containerlistan som filtrerats för det valda lagringskontot.
    - Välj **Granska + återställ.** Detta utlöser validering för att kontrollera om tjänsten har rätt återställningsbehörighet på målservern.

    ![Återställa som filer](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Om återställningspunkten finns på arkivnivån måste du återskapa återställningspunkten innan du återställer.
   
   ![Inställningar för återuppdrering](./media/backup-azure-database-postgresql/rehydration-settings.png)
   
   Ange följande ytterligare parametrar som krävs för återuppfuktning:
   - **Återfuktningsprioritet:** Standardvärdet är **Standard.**
   - **Återuppdningsvaraktighet:** Den maximala rehydreringstiden är 30 dagar och den minsta varaktigheten för återuppfuktning är 10 dagar. Standardvärdet är **15**.
   
   Återställningspunkten lagras i **säkerhetskopieringsdatalagret under** den angivna återställningstiden.


1. Granska informationen och välj **Återställ.** Detta utlöser ett motsvarande återställningsjobb som kan spåras under **Säkerhetskopieringsjobb**.

>[!NOTE]
>Arkivstöd för Azure Database for PostgreSQL är i begränsad offentlig förhandsversion.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Nödvändiga behörigheter för att konfigurera säkerhetskopiering och återställning

Azure Backup följer strikta säkerhetsriktlinjer. Även om det ™ en intern Azure-tjänst antas inte behörigheter för resursen och måste anges uttryckligen av användaren.  På samma sätt lagras inte autentiseringsuppgifter för att ansluta till databasen. Detta är viktigt för att skydda dina data. I stället använder vi Azure Active Directory autentisering.

[Ladda ned det här](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) dokumentet för att få ett automatiserat skript och relaterade instruktioner. Den beviljar en lämplig uppsättning behörigheter till en Azure PostgreSQL-server för säkerhetskopiering och återställning.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>Hantera säkerhetskopierade Azure PostgreSQL-databaser

Det här är de hanteringsåtgärder som du kan utföra på **Säkerhetskopieringsinstanser:**

### <a name="on-demand-backup"></a>Säkerhetskopiering på begäran

Om du vill utlösa en säkerhetskopiering som inte är i det schema som anges i principen går du till **Säkerhetskopieringsinstanser**  ->  **Säkerhetskopiera nu.**
Välj i listan över kvarhållningsregler som har definierats i den associerade säkerhetskopieringsprincipen.

![Utlösningssäkerhetskopiering nu](./media/backup-azure-database-postgresql/backup-now.png)

![Välj från listan över kvarhållningsregler](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Sluta skydda

Du kan stoppa skyddet för ett säkerhetskopieringsobjekt. Detta tar även bort de associerade återställningspunkterna för säkerhetskopieringsobjektet. Om återställningspunkter inte finns på arkivnivån under minst sex månader medför borttagningen av dessa återställningspunkter kostnader för tidig borttagning. Vi erbjuder ännu inte alternativet att stoppa skyddet samtidigt som vi behåller de befintliga återställningspunkterna.

![Sluta skydda](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Ändra princip

Du kan ändra den associerade principen med en säkerhetskopieringsinstans.

1. Välj **ändringsprincipen för**  ->  **Säkerhetskopieringsinstans.**

    ![Ändra princip](./media/backup-azure-database-postgresql/change-policy.png)

1. Välj den nya princip som du vill tillämpa på databasen.

    ![Tilldela om princip](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Felsökning

Det här avsnittet innehåller felsökningsinformation för att backa upp Azure PostgreSQL-databaser med Azure Backup.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Ge Backup Vault MSI **läsbehörighet** på den PG-server som du vill säkerhetskopiera eller återställa.

För att upprätta säker anslutning till PostgreSQL-Azure Backup använder autentiseringsmodellen [för hanterad tjänstidentitet (MSI).](../active-directory/managed-identities-azure-resources/overview.md) Det innebär att säkerhetskopieringsvalvet endast har åtkomst till de resurser som uttryckligen har beviljats behörighet av användaren.

En system-MSI tilldelas automatiskt till valvet när det skapas. Du måste ge det här valvet MSI åtkomst till de PostgreSQL-servrar som du planerar att backa upp databaser från.

Steg:

1. I Postgres-servern går du **till fönstret Access Control (IAM).**

    ![Access Control fönster](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Välj **Lägg till rolltilldelningar.**

    ![Lägg till rolltilldelning](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. I det högra kontextfönstret som öppnas anger du följande:<br>

   - **Roll:** Välj **rollen** Läsare i listrutan.<br>
   - **Tilldela åtkomst till:** Välj alternativet **Användare, grupp eller tjänstens** huvudnamn i listrutan.<br>
   - **Välj:** Ange namnet på säkerhetskopieringsvalvet som du vill säkerhetskopiera den här servern och dess databaser till.<br>

    ![Välj roll](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Skapa en användare för säkerhetskopiering av databasen som kan autentisera med Azure Active Directory:

Det här felet kan uppstå om det inte finns någon Azure Active Directory-administratör för PostgreSQL-servern eller om det inte finns någon säkerhetskopieringsanvändare som kan autentisera med hjälp av Azure Active Directory.

Steg:

Lägg till en Active Directory-administratör på OSS-servern:

Det här steget krävs för att ansluta till databasen via en användare som kan autentisera med Azure Active Directory i stället för ett lösenord. Azure AD-administratörsanvändaren i Azure Database for PostgreSQL har rollen **azure_ad_admin**. Endast en **azure_ad_admin** roll kan skapa nya databasanvändare som kan autentisera med Azure AD.

1. Gå till fliken Active Directory-administratör i det vänstra navigeringsfönstret i servervyn och lägg till dig själv (eller någon annan) som Active Directory-administratör.

    ![Ange Active Directory-administratör](./media/backup-azure-database-postgresql/set-admin.png)

1. Se till att **spara användarinställningen** för AD-administratör.

    ![Spara inställningen för Active Directory-administratörsanvändare](./media/backup-azure-database-postgresql/save-admin-setting.png)

I det [här dokumentet](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) finns en lista över de steg som du behöver utföra för att slutföra stegen för behörighetsbeviljande.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Upprätta nätverkslinjen genom att aktivera flaggan **Tillåt åtkomst till Azure-tjänster** i servervyn. I servervyn går du till fönstret **Anslutningssäkerhet** och ställer in **flaggan Tillåt åtkomst till Azure-tjänster** på **Ja.**

![Tillåt åtkomst till Azure-tjänster](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Behörighet att återställa till en lagringskontocontainer när du återställer som filer

1. Ge MSI-valvet för Backup behörighet att komma åt lagringskontocontainrarna med hjälp av Azure Portal.
    1. Gå till **Lagringskonto**  ->  **och Access Control** Lägg till  ->  **rolltilldelning**.
    1. Tilldela **rollen Storage Blob Data-deltagare** till MSI för Säkerhetskopieringsvalvet.

    ![Tilldela rollen Storage Blob Data-deltagare](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Du kan också ge detaljerade behörigheter till den specifika container som du återställer till med hjälp av kommandot Azure CLI [az role assignment create.](/cli/azure/role/assignment)

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Ersätt parametern assignee med **program-ID** för valvets MSI och omfångsparametern för att referera till din specifika container.
    1. Om du vill hämta **program-ID** för MSI-valvet väljer **du Alla program** under **Programtyp:**

        ![Välj Alla program](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Sök efter valvnamnet och kopiera program-ID:t:

        ![Sök efter valvnamn](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Nästa steg

- [Översikt över säkerhetskopieringsvalv](backup-vault-overview.md)
