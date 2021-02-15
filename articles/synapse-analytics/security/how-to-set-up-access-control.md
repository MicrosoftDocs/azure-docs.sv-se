---
title: Så här konfigurerar du åtkomst kontroll för din Synapse-arbetsyta
description: Den här artikeln lär dig hur du styr åtkomsten till en Synapse-arbetsyta med hjälp av Azure-roller, Synapse-roller, SQL-behörigheter och git-behörigheter.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 513d23de3d7f823ec934af32e5f3776e876f02d8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369007"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>Så här konfigurerar du åtkomst kontroll för din Synapse-arbetsyta 

Den här artikeln lär dig hur du styr åtkomsten till en Synapse-arbetsyta med hjälp av Azure-roller, Synapse-roller, SQL-behörigheter och git-behörigheter.   

I den här guiden skapar du en arbets yta och konfigurerar ett grundläggande åtkomst kontroll system som passar för många Synapse-projekt.  Den beskriver sedan mer avancerade alternativ för detaljerad kontroll om du behöver den.  

Synapse åtkomst kontroll kan förenklas med hjälp av säkerhets grupper som är justerade med rollerna och personer i din organisation.  Du behöver bara lägga till och ta bort användare från säkerhets grupper för att hantera åtkomst.

Innan du börjar den här genom gången kan du läsa [översikten över Synapse Access Control](./synapse-workspace-access-control-overview.md) för att bekanta dig med mekanismerna för åtkomst kontroll som används av Synapse.   

## <a name="access-control-mechanisms"></a>Funktioner för åtkomst kontroll

> [!NOTE]
> Den metod som tas i den här guiden är att skapa flera säkerhets grupper och sedan tilldela roller till dessa grupper. När grupperna har kon figurer ATS behöver du bara hantera medlemskap i säkerhets grupperna för att kontrol lera åtkomsten till arbets ytan.

För att skydda en Synapse-arbetsyta följer du ett mönster för att konfigurera följande objekt:

- **Säkerhets grupper** för att gruppera användare med liknande åtkomst krav.
- **Azure-roller**, för att styra vem som kan skapa och hantera SQL-pooler, Apache Spark pooler och integrerings körningar och åtkomst ADLS Gen2 lagring.
- **Synapse-roller**, för att styra åtkomsten till publicerade kod artefakter, användning av Apache Spark beräknings resurser och integrerings körningar 
- **SQL-behörigheter** för att kontrol lera åtkomsten till administrativa och data planet till SQL-pooler. 
- **Git-behörigheter**, för att styra vem som kan komma åt kod artefakter i käll kontroll om du konfigurerar git-stöd för arbets ytan 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>Steg för att skydda en Synapse-arbetsyta

I det här dokumentet används standard namn för att förenkla anvisningarna. Ersätt dem med namn som du väljer själv.

|Inställning | Standard namn | Beskrivning |
| :------ | :-------------- | :---------- |
| **Synapse-arbetsyta** | `workspace1` |  Namnet som Synapse-arbetsytan kommer att ha. |
| **ADLSGEN2-konto** | `storage1` | ADLS-kontot som ska användas med din arbets yta. |
| **Container** | `container1` | Den behållare i STG1 som arbets ytan ska använda som standard. |
| **Active Directory-klient** | `contoso` | Active Directory-klientens namn.|
||||

## <a name="step-1-set-up-security-groups"></a>STEG 1: Konfigurera säkerhets grupper

>[!Note] 
>Under förhands granskningen rekommenderades att skapa säkerhets grupper som har mappats till Synapse **SYNAPSE SQL-administratör** och **Synapse Apache Spark administratörs** roller.  Vi rekommenderar att du använder dessa nya funktioner för att kontrol lera åtkomsten till din arbets yta med introduktionen av nya, mer detaljerade Synapse RBAC-roller och-omfång.  De här nya rollerna och omfattningarna ger större flexibilitet i konfigurationen och känner av att utvecklarna ofta använder en blandning av SQL och spark i att skapa analys program och kan behöva beviljas åtkomst till vissa resurser i stället för hela arbets ytan. [Läs mer](./synapse-workspace-synapse-rbac.md) om Synapse RBAC.

Skapa följande säkerhets grupper för din arbets yta:

- **`workspace1_SynapseAdministrators`**, för användare som behöver fullständig kontroll över arbets ytan.  Lägg till dig själv i den här säkerhets gruppen, minst från början.
- **`workspace1_SynapseContributors`**, för utvecklare som behöver utveckla, felsöka och publicera kod till tjänsten.   
- **`workspace1_SynapseComputeOperators`**, för användare som behöver hantera och övervaka Apache Spark pooler och integrerings körningar.
- **`workspace1_SynapseCredentialUsers`**, för användare som behöver felsöka och köra Orchestration-pipeliner med hjälp av autentiseringsuppgifterna för arbets ytans MSI (hanterad tjänst identitet) och Avbryt pipeliner körs.   

Du tilldelar Synapse roller till dessa grupper i området för arbets ytan inom kort.  

Skapa även den här säkerhets gruppen: 
- **`workspace1_SQLAdmins`**, grupp för användare som behöver SQL Active Directory admin-auktoritet inom SQL-pooler på arbets ytan. 

`workspace1_SQLAdmins`Gruppen kommer att användas när du konfigurerar SQL-behörigheter i SQL-pooler när du skapar dem. 

För en grundläggande installation är dessa fem grupper tillräckliga. Senare kan du lägga till säkerhets grupper för att hantera användare som behöver mer specialiserad åtkomst eller ge användare endast åtkomst till specifika resurser.

> [!NOTE]
>- Lär dig hur du skapar en säkerhets grupp i [den här artikeln](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).
>- Lär dig hur du lägger till en säkerhets grupp från en annan säkerhets grupp i [den här artikeln](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md).

>[!Tip]
>Enskilda Synapse-användare kan använda Azure Active Directory i Azure Portal för att se deras grupp medlemskap för att avgöra vilka roller de har beviljats.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>STEG 2: Förbered ditt ADLS Gen2 lagrings konto

En Synapse-arbetsyta använder en standard lagrings behållare för:
  - Lagra säkerhetskopieringsfilerna för Spark-tabeller
  - Körnings loggar för Spark-jobb
  - Hantera bibliotek som du väljer att installera

Identifiera följande information om din lagring:

- Det ADLS Gen2 konto som ska användas för din arbets yta. Det här dokumentet anropar det `storage1` . `storage1` anses "primär" lagrings konto för din arbets yta.
- Den behållare i `workspace1` Synapse-arbetsytan som används som standard. Det här dokumentet anropar det `container1` . 

- Med hjälp av Azure Portal tilldelar du följande Azure-roller `container1` till säkerhets grupperna 

  - Tilldela rollen **Storage BLOB data Contributor** till `workspace1_SynapseAdmins` 
  - Tilldela rollen **Storage BLOB data Contributor** till `workspace1_SynapseContributors`
  - Tilldela rollen **Storage BLOB data Contributor** till `workspace1_SynapseComputeOperators`

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>STEG 3: skapa och konfigurera din Synapse-arbetsyta

I Azure Portal skapar du en arbets yta för Synapse:

- Välj din prenumeration
- Välj eller skapa en resurs grupp som du har rollen Azure- **ägare** för.
- Namnge arbets ytan `workspace1`
- Välj `storage1` för lagrings kontot
- Välj `container1` för den behållare som används som "filesystem".
- Öppna WS1 i Synapse Studio
- Navigera till **Hantera**  >  **Access Control** och tilldela Synapse-roller på *arbets ytans omfång* till säkerhets grupperna enligt följande:
  - Tilldela rollen **Synapse administratör** till `workspace1_SynapseAdministrators` 
  - Tilldela rollen **Synapse Contributor** till `workspace1_SynapseContributors` 
  - Tilldela rollen **Synapse Compute-operator** till `workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>STEG 4: bevilja MSI-åtkomst till arbets ytan till standard lagrings behållaren

Om du vill köra pipeliner och utföra system uppgifter kräver Synapse att MSI-filen (Managed Service Identity) för arbets ytan behöver åtkomst till `container1` i standard ADLS Gen2s kontot.

- Öppna Azure-portalen
- Leta upp lagrings kontot, `storage1` och sedan `container1`
- Använd **Access Control (IAM)**, kontrol lera att rollen **Storage BLOB data Contributor** är kopplad till arbets ytans MSI
  - Tilldela den om den inte är tilldelad.
  - MSI har samma namn som arbets ytan. I den här artikeln skulle det vara `workspace1` .

## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>STEG 5: bevilja Synapse-administratörer rollen Azure Contributor på arbets ytan 

För att kunna skapa SQL-pooler, Apache Spark pooler och integrerings körningar, måste användarna ha minst Azure Contributor-åtkomst till arbets ytan. Deltagar rollen tillåter också att dessa användare hanterar resurserna, inklusive pausa och skala.

- Öppna Azure-portalen
- Leta upp arbets ytan, `workspace1`
- Tilldela **Azure Contributor** -rollen `workspace1` till `workspace1_SynapseAdministrators` . 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>STEG 6: tilldela administratörs rollen för SQL Active Directory

Arbets ytans skapare konfigureras automatiskt som SQL Active Directory-administratör för arbets ytan.  Endast en enskild användare eller grupp kan tilldelas den här rollen. I det här steget tilldelar du SQL Active Directory-administratören på arbets ytan till `workspace1_SQLAdmins` säkerhets gruppen.  Genom att tilldela den här rollen får du den här gruppen hög privilegie rad administratörs åtkomst till alla SQL-pooler och databaser i arbets ytan.   

- Öppna Azure-portalen
- Navigera till `workspace1`
- Under **Inställningar** väljer du **SQL Active Directory admin**
- Välj **Ange administratör** och välj **`workspace1_SQLAdmins`**

>[!Note]
>Steg 6 är valfritt.  Du kan välja att ge `workspace1_SQLAdmins` gruppen en mindre privilegie rad roll. Om du vill tilldela `db_owner` eller andra SQL-roller måste du köra skript på varje SQL-databas. 

## <a name="step-7-grant-access-to-sql-pools"></a>STEG 7: bevilja åtkomst till SQL-pooler

Som standard tilldelas alla användare som tilldelats rollen Synapse administratör också SQL `db_owner` -rollen på SQL-poolen utan server, inbyggda och alla dess databaser.

Åtkomst till SQL-pooler för andra användare och för arbets ytans MSI kontrol leras med hjälp av SQL-behörigheter.  Att tilldela SQL-behörigheter kräver att SQL-skript körs på varje SQL-databas när den har skapats.  Det finns tre fall där du måste köra skripten:
1. Ge andra användare åtkomst till den serverbaserade SQL-poolen, "inbyggd" och dess databas
2. Bevilja alla användare åtkomst till databaser med dedikerade pooler
3. Beviljar MSI-åtkomst till en SQL-adresspool för att aktivera pipelines som kräver att SQL-adresspoolen har åtkomst.

Exempel på SQL-skript finns nedan.

Om du vill bevilja åtkomst till en dedikerad databas för SQL-pooler kan skripten köras av arbets ytans skapare eller någon annan medlem i `workspace1_SQLAdmins` gruppen.  

För att bevilja åtkomst till den serverbaserade SQL-poolen, "inbyggd", kan skripten köras av vilken medlem som helst i `workspace1_SQLAdmins` gruppen eller  `workspace1_SynapseAdministrators` gruppen. 

> [!TIP]
> Stegen nedan måste köras för **varje** SQL-pool för att ge användarna åtkomst till alla SQL-databaser, förutom i avsnittet [arbets ytan-begränsad behörighet](#workspace-scoped-permission) där du kan tilldela en användare en sysadmin-roll på arbets ytans nivå.

### <a name="step-71-serverless-sql-pool-built-in"></a>STEG 7,1: SQL-pool utan server, inbyggd

I det här avsnittet finns skript exempel som visar hur du ger en användare behörighet att komma åt en viss databas eller alla databaser i SQL-poolen utan server, inbyggd.

> [!NOTE]
> I skript exemplen ersätter du *alias* med alias för den användare eller grupp som beviljas åtkomst och *domän* med den företags domän som du använder.

#### <a name="database-scoped-permission"></a>Databas – begränsad behörighet

Följ stegen i det här exemplet om du vill bevilja åtkomst till en användare till en **enskild** server utan server SQL Database:

1. Skapa inloggning

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Skapa användare

    ```sql
    use yourdb -- Use your database name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Lägg till användare till medlemmar i den angivna rollen

    ```sql
    use yourdb -- Use your database name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>Arbets yta-begränsad behörighet

Om du vill ge fullständig åtkomst till **alla** SQL-pooler utan server på arbets ytan använder du skriptet i det här exemplet:

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>STEG 7,2: dedikerade SQL-pooler

Om du vill bevilja åtkomst till en **enda** dedikerad SQL-pool, följer du dessa steg i Synapse SQL-skript redigeraren:

1. Skapa användaren i databasen genom att köra följande kommando på mål databasen som väljs med hjälp av list rutan *Anslut till* :

    ```sql
    --Create user in the database
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Ge användaren en roll för att få åtkomst till databasen:

    ```sql
    --Grant role to the user in the database
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* och *db_datawriter* kan arbeta med Läs-/Skriv behörighet om du inte vill tilldela *db_owner* behörighet.
> För att en spark-användare ska kunna läsa och skriva direkt från Spark till eller från en SQL-pool krävs *db_owner* behörighet.

När du har skapat användarna kör du frågor för att kontrol lera att SQL-poolen utan server kan fråga lagrings kontot.

### <a name="step-73-sql-access-control-for-synapse-pipeline-runs"></a>STEG 7,3: SQL-åtkomst kontroll för Synapse-pipeline-körningar

### <a name="workspace-managed-identity"></a>Hanterad identitet för arbets yta

> [!IMPORTANT]
> Om du vill köra pipeliner som innehåller data uppsättningar eller aktiviteter som refererar till en SQL-pool måste arbets ytans identitet beviljas åtkomst till SQL-poolen.

Kör följande kommandon på varje SQL-pool för att tillåta att arbets ytans hanterade system identitet kör pipelines på SQL-poolens databas (er):  

>[!note]
>I skripten nedan är databasename detsamma som poolnamn för en dedikerad SQL-adresspool.  För en databas i den serverbaserade SQL-poolen "inbyggd", är databasename namnet på databasen.

```sql
--Create a SQL user for the workspace MSI in database
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<databasename> TO <workspacename>;
```

Du kan ta bort den här behörigheten genom att köra följande skript i samma SQL-pool:

```sql
--Revoke permission granted to the workspace MSI
REVOKE CONTROL ON DATABASE::<databasename> TO <workspacename>;

--Delete the workspace MSI user in the database
DROP USER [<workspacename>];
```

## <a name="step-8-add-users-to-security-groups"></a>STEG 8: lägga till användare i säkerhets grupper

Den inledande konfigurationen för ditt åtkomst kontroll system har slutförts.

Om du vill hantera åtkomst kan du lägga till och ta bort användare i de säkerhets grupper som du har konfigurerat.  Även om du kan tilldela användare till Synapse-roller manuellt, så konfigurerar den inte sina behörigheter konsekvent. Lägg i stället till eller ta bort användare till säkerhets grupperna.

## <a name="step-9-network-security"></a>STEG 9: nätverks säkerhet

Som ett sista steg för att skydda arbets ytan bör du skydda nätverks åtkomsten med:
- [Arbets ytans brand vägg](./synapse-workspace-ip-firewall.md)
- [Hanterat virtuellt nätverk](./synapse-workspace-managed-vnet.md) 
- [Privata slut punkter](./synapse-workspace-managed-private-endpoints.md)
- [Private Link](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>STEG 10: Slutför Ande

Din arbets yta är nu helt konfigurerad och säker.

## <a name="supporting-more-advanced-scenarios"></a>Stöd för mer avancerade scenarier

Den här guiden fokuserar på att konfigurera ett grundläggande åtkomst kontroll system. Du har stöd för mer avancerade scenarier genom att skapa ytterligare säkerhets grupper och tilldela dessa grupper mer detaljerade roller i mer exakta områden. Överväg följande fall:

**Aktivera git-stöd** för arbets ytan för mer avancerade utvecklings scenarier, inklusive CI/CD.  I git-läge avgör git-behörighet om en användare kan genomföra ändringar i sin arbets gren.  Publicering till tjänsten äger rum endast från samarbets grenen.  Överväg att skapa en säkerhets grupp för utvecklare som behöver utveckla och felsöka uppdateringar i en arbets gren, men behöver inte publicera ändringar i Live-tjänsten.

**Begränsa åtkomst till utvecklare** till vissa resurser.  Skapa ytterligare detaljerade säkerhets grupper för utvecklare som bara behöver åtkomst till vissa resurser.  Tilldela dessa grupper lämpliga Synapse-roller som är begränsade till vissa spark-pooler, integrations körningar eller autentiseringsuppgifter.

**Begränsa operatörer från åtkomst till kod artefakter**.  Skapa säkerhets grupper för operatörer som behöver övervaka drift status för Synapse-beräknings resurser och Visa loggar, men som inte behöver åtkomst till kod eller för att publicera uppdateringar till tjänsten. Tilldela de här grupperna beräknings operator rollen begränsad till vissa spark-pooler och integrerings körningar.  

## <a name="next-steps"></a>Nästa steg

Lär dig [hur du hanterar SYNAPSE RBAC-roll tilldelningar](./how-to-manage-synapse-rbac-role-assignments.md) skapa en [Synapse-arbetsyta](../quickstart-create-workspace.md)
