---
title: Slut punkter och regler för virtuella nätverk för databaser i Azure SQL Database
description: Markera ett undernät som en tjänst slut punkt för virtuellt nätverk. Lägg sedan till slut punkten som en virtuell nätverks regel i åtkomst kontrol listan för din databas. Databasen godkänner sedan kommunikationen från alla virtuella datorer och andra noder i under nätet.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 0dcffe6731c177d1d45c569361fcb200f23af86c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99095366"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Använda tjänstslutpunkter för virtuellt nätverk och regler för servrar i Azure SQL Database

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*Virtuella nätverks regler* är en brand Väggs säkerhetsfunktion som styr om servern för dina databaser och elastiska pooler i [Azure SQL Database](sql-database-paas-overview.md) eller för din dedikerade SQL-pool (tidigare SQL DW)-databaser i [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) godkänner kommunikation som skickas från vissa undernät i virtuella nätverk. I den här artikeln förklaras varför virtuella nätverks regler ibland det bästa alternativet för att på ett säkert sätt tillåta kommunikation till databasen i SQL Database och Azure Synapse Analytics.

> [!NOTE]
> Den här artikeln gäller för både SQL Database-och Azure Synapse-analys. För enkelhetens skull refererar termen *databas* till båda databaserna i SQL Database och Azure Synapse Analytics. På samma sätt refererar alla referenser till *servern* till den [logiska SQL-Server](logical-servers.md) som är värd för SQL Database och Azure Synapse Analytics.

Om du vill skapa en regel för virtuellt nätverk måste du först vara en [tjänst slut punkt för virtuella nätverk][vm-virtual-network-service-endpoints-overview-649d] för att regeln ska referera till.

## <a name="create-a-virtual-network-rule"></a>Skapa en regel för virtuellt nätverk

Om du bara vill skapa en regel för virtuella nätverk kan du gå vidare till stegen och förklaringen [längre fram i den här artikeln](#anchor-how-to-by-using-firewall-portal-59j).

## <a name="details-about-virtual-network-rules"></a>Information om regler för virtuella nätverk

I det här avsnittet beskrivs flera Detaljer om regler för virtuella nätverk.

### <a name="only-one-geographic-region"></a>Endast en geografisk region

Varje slut punkt för virtuellt nätverk gäller endast en Azure-region. Slut punkten tillåter inte att andra regioner accepterar kommunikation från under nätet.

Alla virtuella nätverks regler är begränsade till den region som den underliggande slut punkten gäller för.

### <a name="server-level-not-database-level"></a>Server nivå, inte databas nivå

Varje virtuell nätverks regel gäller hela servern, inte bara till en viss databas på servern. Med andra ord tillämpas virtuella nätverks regler på server nivå, inte på databas nivå.

Dessutom kan IP-regler tillämpas på någon av nivåerna.

### <a name="security-administration-roles"></a>Säkerhets administrations roller

Det finns en separation av säkerhets roller i administration av tjänst slut punkter för virtuella nätverk. Åtgärd krävs från var och en av följande roller:

- **Nätverks administratör (rollen [nätverks deltagare](../../role-based-access-control/built-in-roles.md#network-contributor) ):** &nbsp; Aktivera slut punkten.
- **Databas administratör (rollen [SQL Server deltagare](../../role-based-access-control/built-in-roles.md#sql-server-contributor) ):** &nbsp; Uppdatera åtkomst kontrol listan (ACL) för att lägga till angivet undernät till servern.

#### <a name="azure-rbac-alternative"></a>Alternativ för Azure RBAC

Rollerna för nätverks administratör och databas administratörer har fler funktioner än vad som krävs för att hantera virtuella nätverks regler. Endast en delmängd av deras funktioner krävs.

Du kan välja att använda [rollbaserad åtkomst kontroll (RBAC)][rbac-what-is-813s] i Azure för att skapa en enskild anpassad roll som bara har de funktioner som krävs. Den anpassade rollen kan användas i stället för att involvera antingen nätverks administratören eller databas administratören. Arean av din säkerhets exponering är lägre om du lägger till en användare till en anpassad roll och lägger till användaren till de andra två större administratörs rollerna.

> [!NOTE]
> I vissa fall finns databasen i SQL Database och det virtuella nätverkets undernät i olika prenumerationer. I dessa fall måste du se till att följande konfigurationer:
>
> - Båda prenumerationerna måste finnas i samma Azure Active Directory-klient (Azure AD).
> - Användaren har de behörigheter som krävs för att initiera åtgärder, till exempel att aktivera tjänstens slut punkter och lägga till ett virtuellt nätverks under nät till den aktuella servern.
> - Båda prenumerationerna måste ha Microsoft. SQL-providern registrerad.

## <a name="limitations"></a>Begränsningar

För SQL Database har funktionen regler för virtuellt nätverk följande begränsningar:

- I brand väggen för din databas i SQL Database refererar varje virtuell nätverks regel till ett undernät. Alla dessa refererade undernät måste ligga inom samma geografiska region som är värd för databasen.
- Varje server kan ha upp till 128 ACL-poster för alla virtuella nätverk.
- Regler för virtuella nätverk gäller endast för Azure Resource Manager virtuella nätverk och inte till klassiska nätverk för [distributions modeller][arm-deployment-model-568f] .
- Aktivering av tjänst slut punkter för virtuella nätverk till SQL Database också aktivera slut punkter för Azure Database for MySQL och Azure Database for PostgreSQL. När slut punkter har angetts till **på**, försöker ansluta från slut punkterna till Azure Database for MySQL eller Azure Database for PostgreSQL instanser kan Miss lyckas.
  - Den bakomliggande orsaken är att Azure Database for MySQL och Azure Database for PostgreSQL troligen inte har någon konfigurerad regel för virtuella nätverk. Du måste konfigurera en regel för virtuella nätverk för Azure Database for MySQL och Azure Database for PostgreSQL, och anslutningen kommer att lyckas.
  - Ange **neka offentlig nätverks åtkomst** till **Nej** om du vill definiera brand Väggs regler för virtuella nätverk på en logisk SQL-Server som redan har kon figurer ATS med privata slut punkter.
- I brand väggen gäller IP-adressintervall för följande nätverks objekt, men inga regler för virtuella nätverk:
  - [Virtuellt privat nätverk (VPN) för plats-till-plats (S2S)][vpn-gateway-indexmd-608y]
  - Lokalt via [Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Att tänka på när du använder tjänstens slut punkter

När du använder tjänstens slut punkter för SQL Database bör du gå igenom följande överväganden:

- **Utgående till Azure SQL Database offentliga IP-adresser krävs.** Nätverks säkerhets grupper (NSG: er) måste öppnas för att SQL Database IP-adresser för att tillåta anslutning. Du kan göra detta med hjälp av NSG [service-Taggar](../../virtual-network/network-security-groups-overview.md#service-tags) för SQL Database.

### <a name="expressroute"></a>ExpressRoute

Om du använder [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) från dina lokaler, för offentlig peering eller Microsoft-peering, måste du identifiera de NAT-IP-adresser som används. För offentlig peering, använder varje ExpressRoute-krets som standard två NAT IP-adresser, som används för Azure-tjänsttrafik när trafiken kommer till Microsoft Azure-stamnätverket. För Microsoft-peering tillhandahålls de NAT-IP-adresser som används av antingen kunden eller tjänst leverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Mer information om NAT för ExpressRoute-offentlig och Microsoft-peering finns i [NAT-krav för offentlig Azure-peering](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

Om du vill tillåta kommunikation från din krets till SQL Database måste du skapa IP-nätverksnummer för de offentliga IP-adresserna för din NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Effekt av att använda tjänst slut punkter för virtuella nätverk med Azure Storage

Azure Storage har implementerat samma funktion som gör att du kan begränsa anslutningen till ditt Azure Storage-konto. Om du väljer att använda den här funktionen med ett Azure Storage konto som SQL Database använder, kan du stöta på problem. Härnäst är en lista och diskussion om SQL Database-och Azure Synapse Analytics-funktioner som påverkas av detta.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase och COPY-instruktion

PolyBase och COPY-instruktionen används ofta för att läsa in data i Azure Synapse Analytics från Azure Storage konton för data inmatning med hög data flöde. Om Azure Storage-kontot som du läser in data från begränsar åtkomsten till en uppsättning virtuella nätverk under nät, kommer anslutningen när du använder PolyBase och KOPIERINGs instruktionen till lagrings kontot att brytas. Om du vill aktivera import-och export scenarier med hjälp av kopiera och PolyBase med Azure Synapse Analytics ansluter du till Azure Storage som är skyddade till ett virtuellt nätverk, följer du stegen i det här avsnittet.

#### <a name="prerequisites"></a>Förutsättningar

- Installera Azure PowerShell med hjälp av [den här guiden](/powershell/azure/install-az-ps).
- Om du har ett allmänt v1-eller Azure Blob Storage-konto måste du först uppgradera till General-Purpose v2 genom att följa stegen i [Uppgradera till ett allmänt-syfte v2-lagrings konto](../../storage/common/storage-account-upgrade.md).
- Du måste ha **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot** på menyn Azure Storage konto **brand väggar och inställningar för virtuella nätverk** . Om du aktiverar den här konfigurationen tillåts PolyBase-och COPY-instruktionen att ansluta till lagrings kontot med hjälp av stark autentisering där nätverks trafiken finns kvar i Azure-stamnätet. Mer information finns i [den här guiden](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. AzureRM-modulen kommer att fortsätta att ta emot fel korrigeringar fram till minst december 2020. Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Steg

1. Om du har en fristående SQL-pool registrerar du SQL Server med Azure AD med hjälp av PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Det här steget krävs inte för dedikerade SQL-pooler i en Azure Synapse Analytics-arbetsyta.

1. Om du har en Azure Synapse Analytics-arbetsyta registrerar du arbets ytans systemhanterade identitet:

   1. Gå till din Azure Synapse Analytics-arbetsyta i Azure Portal.
   2. Gå till fönstret **hanterade identiteter** .
   3. Kontrol lera att alternativet **Tillåt pipeliner** är aktiverat.
   
1. Skapa ett **Allmänt-syfte v2-lagrings konto** genom att följa stegen i [skapa ett lagrings konto](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Om du har ett allmänt v1-eller Blob Storage-konto måste du *först uppgradera till v2* genom att följa stegen i [Uppgradera till ett allmänt-syfte v2-lagrings konto](../../storage/common/storage-account-upgrade.md).
   > - Kända problem med Azure Data Lake Storage Gen2 finns i [kända problem med Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md).

1. Under ditt lagrings konto går du till **Access Control (IAM)** och väljer **Lägg till roll tilldelning**. Tilldela Azure-rollen **Storage BLOB data Contributor** till den server eller arbets yta som är värd för din dedikerade SQL-pool, som du har registrerat med Azure AD.

   > [!NOTE]
   > Endast medlemmar med ägar behörighet för lagrings kontot kan utföra det här steget. För olika inbyggda Azure-roller, se [inbyggda Azure-roller](../../role-based-access-control/built-in-roles.md).
  
1. Så här aktiverar du PolyBase-anslutning till Azure Storage-kontot:

   1. Skapa en [huvud nyckel](/sql/t-sql/statements/create-master-key-transact-sql) för databasen om du inte har skapat en tidigare.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Skapa en databas – begränsade autentiseringsuppgifter med **Identity = hanterad tjänstidentitet**.

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Du behöver inte ange hemlighet med en Azure Storage åtkomst nyckel eftersom den här mekanismen använder [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) under försättsblad.
       > - IDENTITETS namnet ska vara **hanterad tjänstidentitet** för PolyBase-anslutningar för att fungera med ett Azure Storage-konto som skyddas av ett virtuellt nätverk.

   1. Skapa en extern data källa med `abfss://` schemat för att ansluta till ditt General-Purpose v2-lagrings konto med PolyBase.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Om du redan har externa tabeller som är kopplade till ett allmänt v1-eller Blob Storage-konto bör du först släppa dessa externa tabeller. Ta sedan bort motsvarande externa data källa. Därefter skapar du en extern data källa med det `abfss://` schema som ansluter till ett allmänt lagrings konto för generell användning, som du ser ovan. Återskapa sedan alla externa tabeller genom att använda den här nya externa data källan. Du kan använda [guiden skapa och publicera skript](/sql/ssms/scripting/generate-and-publish-scripts-wizard) för att skapa Create-scripts för att enkelt skapa skript för alla externa tabeller.
       > - Mer information om `abfss://` schemat finns i [Använd Azure Data Lake Storage Gen2-URI](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Mer information om att skapa en extern DATA källa finns i [den här guiden](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Fråga som normal med hjälp av [externa tabeller](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="sql-database-blob-auditing"></a>SQL Database-BLOB-granskning

Med BLOB-granskning skickas gransknings loggar till ditt eget lagrings konto. Om det här lagrings kontot använder funktionen för tjänst slut punkter för virtuella nätverk kommer anslutningen från SQL Database till lagrings kontot att brytas.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Lägg till en brand Väggs regel för virtuellt nätverk på servern

Innan den här funktionen har förbättrats tidigare var du tvungen att aktivera tjänst slut punkter för virtuella nätverk innan du kunde implementera en aktiv virtuell nätverks regel i brand väggen. Slut punkterna relaterade till ett angivet virtuellt nätverks under nät till en databas i SQL Database. Från och med januari 2018 kan du kringgå det här kravet genom att ange flaggan **IgnoreMissingVNetServiceEndpoint** . Nu kan du lägga till en brand Väggs regel för virtuella nätverk på servern utan att aktivera tjänst slut punkter för virtuella nätverk.

Att bara ange en brand Väggs regel skyddar inte servern. Du måste också aktivera tjänst slut punkter för virtuella nätverk för att säkerheten ska börja gälla. När du aktiverar tjänstens slut punkter upplever det virtuella nätverkets under nät drift stopp tills den slutför över gången från inaktive rad till på. Den här stillestånds tiden är särskilt sann i kontexten för stora virtuella nätverk. Du kan använda flaggan **IgnoreMissingVNetServiceEndpoint** för att minska eller eliminera stillestånds tiden under över gången.

Du kan ställa in flaggan **IgnoreMissingVNetServiceEndpoint** med hjälp av PowerShell. Mer information finns i [PowerShell för att skapa en tjänst slut punkt för virtuellt nätverk och regel för SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Fel 40914 och 40615

Anslutnings fel 40914 relaterar till *virtuella nätverks regler*, enligt vad som anges i **brand Väggs** fönstret i Azure Portal. Fel 40615 liknar varandra, förutom att det relaterar till *IP-adressens regler* i brand väggen.

### <a name="error-40914"></a>Fel 40914

**Meddelande text:** "Det går inte att öppna servern *[Server namn]* som begärdes av inloggningen. Klienten har inte behörighet att komma åt servern. "

**Fel Beskrivning:** Klienten finns i ett undernät som har Server slut punkter för virtuella nätverk. Men servern har ingen regel för virtuella nätverk som ger under nätet rätt att kommunicera med databasen.

**Fel matchning:** I fönstret **brand vägg** i Azure Portal använder du kontrollen regler för virtuellt nätverk för att [lägga till en virtuell nätverks regel](#anchor-how-to-by-using-firewall-portal-59j) för under nätet.

### <a name="error-40615"></a>Fel 40615

**Meddelande text:** "Det går inte att öppna servern" {0} som begärdes av inloggningen. Klienten med IP-adressen {1} är inte tillåten för att komma åt servern.

**Fel Beskrivning:** Klienten försöker ansluta från en IP-adress som inte har behörighet att ansluta till servern. Server brand väggen har ingen IP-serveradress som gör att en klient kan kommunicera från den tilldelade IP-adressen till-databasen.

**Fel matchning:** Ange klientens IP-adress som en IP-regel. Använd **brand Väggs** fönstret i Azure Portal för att utföra det här steget.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Använd portalen för att skapa en regel för virtuellt nätverk

I det här avsnittet beskrivs hur du kan använda [Azure Portal][http-azure-portal-link-ref-477t] för att skapa en *regel för virtuella nätverk* i din databas i SQL Database. Regeln instruerar din databas att acceptera kommunikation från ett visst undernät som har taggats som en *tjänst slut punkt för virtuellt nätverk*.

> [!NOTE]
> Om du vill lägga till en tjänst slut punkt i brand Väggs reglerna för virtuella nätverk för-servern ska du först se till att tjänstens slut punkter är aktiverade för under nätet.
>
> Om tjänst slut punkter inte är aktiverade för under nätet ber portalen dig att aktivera dem. Välj knappen **Aktivera** i samma fönster där du lägger till regeln.

## <a name="powershell-alternative"></a>PowerShell-alternativ

Ett skript kan också skapa regler för virtuella nätverk genom att använda PowerShell **-cmdleten New-AzSqlServerVirtualNetworkRule** eller [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create). Om du är intresse rad, se [PowerShell för att skapa en tjänst slut punkt för virtuella nätverk och regel för SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API alternativ

Internt anropar PowerShell-cmdletar för SQL Virtual Network-åtgärder REST-API: er. Du kan anropa REST-API: erna direkt.

- [Regler för virtuella nätverk: åtgärder][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Förutsättningar

Du måste redan ha ett undernät som är taggat med det aktuella namnet på den virtuella nätverks tjänstens slut punkts *typ* som är relevant för SQL Database.

- Det relevanta namnet på slut punkts typen är **Microsoft. SQL**.
- Om ditt undernät kanske inte är taggat med typ namnet, se [Verifiera att ditt undernät är en slut punkt][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal steg

1. Logga in på [Azure-portalen][http-azure-portal-link-ref-477t].

1. Sök efter och välj **SQL-servrar** och välj sedan servern. Under **säkerhet** väljer du **brand väggar och virtuella nätverk**.

1. Ange **Tillåt åtkomst till Azure** -tjänster **.**

    > [!IMPORTANT]
    > Om du lämnar kontrollen inställd på **på**, godkänner servern kommunikation från ett undernät i Azure-gränser. Det är en kommunikation som härstammar från en av de IP-adresser som identifieras som de inom intervall som definierats för Azure-datacenter. Om du lämnar kontrollen till **på aktive** rad kan det vara för hög åtkomst från en säkerhets punkt. Den Microsoft Azure Virtual Network tjänstens slut punkts funktion i samordning med funktionen regler för virtuella nätverk i SQL Database tillsammans kan minska området för säkerhets ytan.

1. Välj **+ Lägg till befintlig** i avsnittet **virtuella nätverk** .

    ![Skärm bild som visar Välj + Lägg till befintlig (under näts slut punkt som en SQL-regel).][image-portal-firewall-vnet-add-existing-10-png]

1. I rutan nytt **Skapa/uppdatera** fyller du i rutorna med namnen på dina Azure-resurser.

    > [!TIP]
    > Du måste inkludera rätt adressprefix för ditt undernät. Du hittar värdet för **adressprefixet** i portalen. Gå till **alla resurser** &gt; **alla typer** av &gt; **virtuella nätverk**. Filtret visar dina virtuella nätverk. Välj ditt virtuella nätverk och välj sedan **undernät**. I kolumnen **adress intervall** finns det adressprefix du behöver.

    ![Skärm bild som visar fyllning i rutor för den nya regeln.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Välj **OK** -knappen långt ned i fönstret.

1. Se den resulterande virtuella nätverks regeln i **brand Väggs** fönstret.

    ![Skärm bild som visar den nya regeln i brand Väggs fönstret.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Följande status eller tillstånd gäller för reglerna:
>
> - **Klar**: indikerar att åtgärden du initierade har slutförts.
> - **Misslyckades**: indikerar att åtgärden du initierade misslyckades.
> - **Borttagen**: gäller endast borttagnings åtgärden och anger att regeln har tagits bort och inte längre gäller.
> - **Pågår**: anger att åtgärden pågår. Den gamla regeln gäller när åtgärden är i det här läget.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Relaterade artiklar

- [Tjänstslutpunkter för virtuellt nätverk i Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Brand Väggs regler på server nivå och databas nivå][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Nästa steg

- [Använd PowerShell för att skapa en tjänst slut punkt för virtuellt nätverk och sedan en regel för virtuella nätverk för SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regler för virtuella nätverk: åtgärder][rest-api-virtual-network-rules-operations-862r] med REST API: er

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
