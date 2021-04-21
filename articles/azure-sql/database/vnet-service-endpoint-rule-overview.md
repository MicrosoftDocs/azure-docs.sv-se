---
title: Slutpunkter och regler för virtuella nätverk för databaser i Azure SQL Database
description: Markera ett undernät som en tjänstslutpunkt för virtuellt nätverk. Lägg sedan till slutpunkten som en regel för virtuellt nätverk i ACL för databasen. Din databas accepterar sedan kommunikation från alla virtuella datorer och andra noder i undernätet.
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
ms.openlocfilehash: 67e807e948caf1fec014457814c1b7f105630f9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784433"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Använda tjänstslutpunkter för virtuellt nätverk och regler för servrar i Azure SQL Database

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*Regler för virtuella* nätverk är en brandväggssäkerhetsfunktion som styr om servern för dina databaser och elastiska pooler i [Azure SQL Database](sql-database-paas-overview.md) eller för dina dedikerade SQL-pooldatabaser (tidigare SQL DW) i [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) accepterar kommunikation som skickas från specifika undernät i virtuella nätverk. Den här artikeln förklarar varför regler för virtuella nätverk ibland är det bästa alternativet för säker kommunikation till din databas i SQL Database och Azure Synapse Analytics.

> [!NOTE]
> Den här artikeln gäller både SQL Database och Azure Synapse Analytics. För enkelhetens skull refererar *termen* databas till båda databaserna i SQL Database och Azure Synapse Analytics. På samma sätt refererar alla *referenser* till servern till den [logiska SQL-server](logical-servers.md) som är värd för SQL Database och Azure Synapse Analytics.

Om du vill skapa en regel för virtuellt nätverk måste det först finnas en [tjänstslutpunkt][vm-virtual-network-service-endpoints-overview-649d] för virtuellt nätverk som regeln ska referera till.

## <a name="create-a-virtual-network-rule"></a>Skapa en regel för virtuellt nätverk

Om du bara vill skapa en regel för virtuellt nätverk kan du gå vidare till stegen och förklaringen [senare i den här artikeln.](#anchor-how-to-by-using-firewall-portal-59j)

## <a name="details-about-virtual-network-rules"></a>Information om regler för virtuellt nätverk

I det här avsnittet beskrivs flera detaljer om regler för virtuella nätverk.

### <a name="only-one-geographic-region"></a>Endast ett geografiskt område

Varje tjänstslutpunkt för virtuellt nätverk gäller endast för en Azure-region. Slutpunkten gör inte att andra regioner kan acceptera kommunikation från undernätet.

Alla regler för virtuella nätverk är begränsade till den region som dess underliggande slutpunkt gäller för.

### <a name="server-level-not-database-level"></a>Servernivå, inte databasnivå

Varje regel för virtuellt nätverk gäller för hela servern, inte bara för en viss databas på servern. Med andra ord gäller regler för virtuella nätverk på servernivå, inte på databasnivå.

IP-regler kan däremot tillämpas på båda nivåer.

### <a name="security-administration-roles"></a>Säkerhetsadministrationsroller

Det finns en uppdelning av säkerhetsroller i administrationen av tjänstslutpunkter för virtuella nätverk. Åtgärd krävs från var och en av följande roller:

- **Nätverksadministratör [(rollen Nätverksdeltagare):](../../role-based-access-control/built-in-roles.md#network-contributor)** &nbsp; Aktivera slutpunkten.
- **Databasadministratör ([rollen SQL Server deltagare):](../../role-based-access-control/built-in-roles.md#sql-server-contributor)** &nbsp; Uppdatera åtkomstkontrollista (ACL) för att lägga till det angivna undernätet på servern.

#### <a name="azure-rbac-alternative"></a>Alternativ för Azure RBAC

Rollerna nätverksadministratör och databasadministratör har fler funktioner än vad som behövs för att hantera regler för virtuella nätverk. Endast en delmängd av deras funktioner behövs.

Du har möjlighet att använda rollbaserad åtkomstkontroll [(RBAC)][rbac-what-is-813s] i Azure för att skapa en enda anpassad roll som bara har den nödvändiga delmängden funktioner. Den anpassade rollen kan användas i stället för att involvera antingen nätverksadministratören eller databasadministratören. Ytan för din säkerhetsexponering är lägre om du lägger till en användare i en anpassad roll jämfört med om du lägger till användaren i de andra två större administratörsrollerna.

> [!NOTE]
> I vissa fall finns databasen i SQL Database och det virtuella nätverkets undernät i olika prenumerationer. I dessa fall måste du se till att följande konfigurationer:
>
> - Båda prenumerationerna måste finnas i samma Azure Active Directory (Azure AD) klientorganisation.
> - Användaren har de behörigheter som krävs för att initiera åtgärder, till exempel att aktivera tjänstslutpunkter och lägga till ett undernät för virtuellt nätverk på den angivna servern.
> - Båda prenumerationerna måste ha Microsoft.Sql-providern registrerad.

## <a name="limitations"></a>Begränsningar

För SQL Database har funktionen regler för virtuella nätverk följande begränsningar:

- I brandväggen för din databas i SQL Database refererar varje regel för virtuellt nätverk till ett undernät. Alla dessa refererade undernät måste finnas i samma geografiska region som är värd för databasen.
- Varje server kan ha upp till 128 ACL-poster för alla virtuella nätverk.
- Regler för virtuellt nätverk gäller endast Azure Resource Manager virtuella nätverk och inte på [klassiska distributionsmodellnätverk.][arm-deployment-model-568f]
- Om du aktiverar tjänstslutpunkter för virtuellt SQL Database aktiveras även slutpunkterna för Azure Database for MySQL och Azure Database for PostgreSQL. När slutpunkterna är **inställda på PÅ** kan försök att ansluta från slutpunkterna till Azure Database for MySQL eller Azure Database for PostgreSQL instanser misslyckas.
  - Den underliggande orsaken är att Azure Database for MySQL och Azure Database for PostgreSQL förmodligen inte har någon konfigurerad regel för virtuellt nätverk. Du måste konfigurera en regel för virtuellt Azure Database for MySQL och Azure Database for PostgreSQL så att anslutningen lyckas.
  - Om du vill definiera brandväggsregler för virtuella nätverk på en logisk SQL-server som redan har konfigurerats med privata slutpunkter anger du Neka offentlig **nätverksåtkomst** till **Nej.**
- IP-adressintervall gäller för följande nätverksobjekt i brandväggen, men regler för virtuella nätverk gäller inte:
  - [Plats-till-plats (S2S) virtuellt privat nätverk (VPN)][vpn-gateway-indexmd-608y]
  - Lokalt via [Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Att tänka på när du använder tjänstslutpunkter

När du använder tjänstslutpunkter för SQL Database bör du tänka på följande:

- **Utgående till Azure SQL Database offentliga IP-adresser krävs.** Nätverkssäkerhetsgrupper (NSG: er) måste öppnas för att SQL Database IP-adresser för att tillåta anslutning. Du kan göra detta med hjälp av [NSG-tjänsttaggar](../../virtual-network/network-security-groups-overview.md#service-tags) för SQL Database.

### <a name="expressroute"></a>ExpressRoute

Om du använder [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) från din lokala plats för offentlig peering eller Microsoft-peering måste du identifiera de NAT IP-adresser som används. För offentlig peering, använder varje ExpressRoute-krets som standard två NAT IP-adresser, som används för Azure-tjänsttrafik när trafiken kommer till Microsoft Azure-stamnätverket. För Microsoft-peering tillhandahålls DE NAT IP-adresser som används av antingen kunden eller tjänstleverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Mer information om NAT för expressroute offentlig peering och Microsoft-peering finns i [NAT-krav för offentlig Azure-peering.](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

Om du vill tillåta kommunikation från kretsen till SQL Database måste du skapa IP-nätverksregler för de offentliga IP-adresserna för din NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Effekten av att använda tjänstslutpunkter för virtuellt nätverk med Azure Storage

Azure Storage har implementerat samma funktion som gör att du kan begränsa anslutningen till ditt Azure Storage-konto. Om du väljer att använda den här funktionen med Azure Storage konto som SQL Database använder kan du få problem. Nästa är en lista och diskussion om SQL Database och Azure Synapse Analytics funktioner som påverkas av detta.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase och COPY-instruktion

PolyBase och COPY-instruktionen används ofta för att läsa in data till Azure Synapse Analytics från Azure Storage-konton för datainmatning med högt dataflöde. Om det Azure Storage-konto som du läser in data från begränsar endast åtkomst till en uppsättning virtuella nätverksundernät, bryts anslutningen när du använder PolyBase och COPY-instruktionen till lagringskontot. Följ stegen i det här avsnittet för att aktivera import- och exportscenarier med copy och PolyBase med Azure Synapse Analytics som ansluter till Azure Storage som är skyddade i ett virtuellt nätverk.

#### <a name="prerequisites"></a>Förutsättningar

- Installera Azure PowerShell med hjälp av den [här guiden](/powershell/azure/install-az-ps).
- Om du har ett v1- eller [Azure Blob Storage-konto](../../storage/common/storage-account-upgrade.md)för generell användning måste du först uppgradera till generell användning v2 genom att följa stegen i Uppgradera till ett v2-lagringskonto för generell användning.
- Du måste ha **Tillåt betrodda Microsoft-tjänster** åtkomst till det här lagringskontot aktiverat under **inställningsmenyn** Azure Storage brandväggar och virtuella nätverk. Om du aktiverar den här konfigurationen kan PolyBase och COPY-instruktionen ansluta till lagringskontot med hjälp av stark autentisering där nätverkstrafiken finns kvar i Azure-stamnätverket. Mer information finns i den [här guiden.](../../storage/common/storage-network-security.md#exceptions)

> [!IMPORTANT]
> PowerShell Azure Resource Manager modulen stöds fortfarande av SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot felkorrigeringar fram till åtminstone december 2020. Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är betydligt identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Steg

1. Om du har en fristående dedikerad SQL-pool registrerar du din SQL-server med Azure AD med hjälp av PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Det här steget krävs inte för dedikerade SQL-pooler i en Azure Synapse Analytics arbetsyta.

1. Om du har en Azure Synapse Analytics kan du registrera arbetsytans system hanterade identitet:

   1. Gå till Azure Synapse Analytics arbetsyta i Azure Portal.
   2. Gå till **fönstret Hanterade identiteter.**
   3. Kontrollera att **alternativet Tillåt pipelines** är aktiverat.
   
1. Skapa ett **v2-lagringskonto för generell användning** genom att följa stegen i [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Om du har ett konto för generell användning v1 eller Blob Storage måste du först uppgradera till *v2* genom att följa stegen i Uppgradera till ett [v2-lagringskonto](../../storage/common/storage-account-upgrade.md)för generell användning.
   > - Kända problem med Azure Data Lake Storage Gen2 finns i [Kända problem med Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md).

1. Under ditt lagringskonto går du **till Access Control (IAM)** och väljer Lägg **till rolltilldelning**. Tilldela **Azure-rollen Storage Blob Data-deltagare** till den server eller arbetsyta som är värd för din dedikerade SQL-pool, som du har registrerat med Azure AD.

   > [!NOTE]
   > Endast medlemmar med ägarbehörighet på lagringskontot kan utföra det här steget. Olika inbyggda Roller i Azure finns i [Inbyggda roller i Azure.](../../role-based-access-control/built-in-roles.md)
  
1. Så här aktiverar du PolyBase-anslutning till Azure Storage konto:

   1. Skapa en [huvudnyckel för](/sql/t-sql/statements/create-master-key-transact-sql) databasen om du inte har skapat en tidigare.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Skapa en databasomfattning med autentiseringsuppgifter med **IDENTITY = "Hanterad tjänstidentitet".**

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Du behöver inte ange SECRET med en Azure Storage åtkomstnyckel eftersom den här mekanismen använder [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) under omslaget.
       > - Identitetsnamnet ska vara **"Hanterad tjänstidentitet"** för att PolyBase-anslutningen ska fungera med ett Azure Storage-konto som är skyddat i ett virtuellt nätverk.

   1. Skapa en extern datakälla med `abfss://` schemat för att ansluta till ditt allmänna v2-lagringskonto med PolyBase.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Om du redan har externa tabeller som är associerade med ett konto för generell användning v1 eller Blob Storage bör du först ta bort de externa tabellerna. Ta sedan bort motsvarande externa datakälla. Skapa sedan en extern datakälla med schemat som ansluter till ett `abfss://` v2-lagringskonto för generell användning, som tidigare visats. Skapa sedan om alla externa tabeller med hjälp av den här nya externa datakällan. Du kan enkelt [generera create-scripts](/sql/ssms/scripting/generate-and-publish-scripts-wizard) för alla externa tabeller med hjälp av guiden Generera och publicera skript.
       > - Mer information om schemat `abfss://` finns i Använda Azure Data Lake Storage Gen2 [URI](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Mer information om ATT SKAPA EXTERN DATAKÄLLA finns i den [här guiden.](/sql/t-sql/statements/create-external-data-source-transact-sql)

   1. Fråga som vanligt med hjälp av [externa tabeller](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="sql-database-blob-auditing"></a>SQL Database blobgranskning

Blobgranskning push-pushar granskningsloggar till ditt eget lagringskonto. Om det här lagringskontot använder funktionen tjänstslutpunkter för virtuellt nätverk bryts anslutningen från SQL Database till lagringskontot.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Lägga till en brandväggsregel för virtuellt nätverk på servern

För länge sedan, innan den här funktionen förbättrades, behövde du aktivera tjänstslutpunkter för virtuellt nätverk innan du kunde implementera en regel för virtuellt nätverk i brandväggen. Slutpunkterna relaterar ett visst virtuellt nätverksundernät till en databas i SQL Database. Från och med januari 2018 kan du kringgå det här kravet genom att ange **flaggan IgnoreMissingVNetServiceEndpoint.** Nu kan du lägga till en brandväggsregel för virtuellt nätverk på servern utan att aktivera tjänstslutpunkter för virtuellt nätverk.

Att bara ange en brandväggsregel hjälper inte att skydda servern. Du måste också aktivera tjänstslutpunkter för virtuellt nätverk för att säkerheten ska gälla. När du aktiverar tjänstslutpunkter upplever undernätet för det virtuella nätverket avbrott tills övergången från avstängd till på är klar. Den här stilleståndstiden gäller särskilt för stora virtuella nätverk. Du kan använda flaggan **IgnoreMissingVNetServiceEndpoint** för att minska eller eliminera avbrottstiden under övergången.

Du kan ange **flaggan IgnoreMissingVNetServiceEndpoint** med hjälp av PowerShell. Mer information finns i [PowerShell för att skapa en tjänstslutpunkt och regel][sql-db-vnet-service-endpoint-rule-powershell-md-52d]för virtuellt nätverk för SQL Database .

## <a name="errors-40914-and-40615"></a>Felen 40914 och 40615

Anslutningsfel 40914 relaterar till regler för virtuellt nätverk *som* anges i **brandväggsfönstret** i Azure Portal. Fel 40615 är liknande, förutom att det gäller *IP-adressregler* i brandväggen.

### <a name="error-40914"></a>Fel 40914

**Meddelandetext:** Det går inte att öppna *servern [servernamn]* som begärdes vid inloggningen. Klienten har inte åtkomst till servern."

**Felbeskrivning:** Klienten finns i ett undernät som har serverslutpunkter för virtuellt nätverk. Servern har dock ingen regel för virtuellt nätverk som ger undernätet behörighet att kommunicera med databasen.

**Fellösning:** I fönstret **Brandvägg** i fönstret Azure Portal du kontrollen för regler för virtuellt nätverk för att [lägga till en regel för virtuellt](#anchor-how-to-by-using-firewall-portal-59j) nätverk för undernätet.

### <a name="error-40615"></a>Fel 40615

**Meddelandetext:** Det går inte att öppna servern {0} som begärdes vid inloggningen. Klienten med IP-adressen {1} " " får inte åtkomst till servern."

**Felbeskrivning:** Klienten försöker ansluta från en IP-adress som inte har behörighet att ansluta till servern. Serverbrandväggen har ingen IP-adressregel som gör att en klient kan kommunicera från den angivna IP-adressen till databasen.

**Fellösning:** Ange klientens IP-adress som en IP-regel. Använd **brandväggsfönstret** i fönstret Azure Portal att göra det här steget.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Använda portalen för att skapa en regel för virtuellt nätverk

Det här avsnittet illustrerar hur du kan använda [Azure Portal][http-azure-portal-link-ref-477t] för att *skapa* en regel för virtuellt nätverk i databasen i SQL Database. Regeln talar om för databasen att den ska acceptera kommunikation från ett visst undernät som har taggats som en *tjänstslutpunkt för virtuellt nätverk.*

> [!NOTE]
> Om du tänker lägga till en tjänstslutpunkt i brandväggsreglerna för det virtuella nätverket för servern måste du först se till att tjänstslutpunkter är aktiverat för undernätet.
>
> Om tjänstslutpunkter inte är aktiverat för undernätet ber portalen dig att aktivera dem. Välj knappen **Aktivera** i samma fönster där du lägger till regeln.

## <a name="powershell-alternative"></a>PowerShell-alternativ

Ett skript kan också skapa regler för virtuellt nätverk med hjälp av **PowerShell-cmdleten New-AzSqlServerVirtualNetworkRule** [eller az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Om du är intresserad kan du gå till [PowerShell för att skapa en tjänstslutpunkt för virtuellt][sql-db-vnet-service-endpoint-rule-powershell-md-52d]nätverk och en regel för SQL Database .

## <a name="rest-api-alternative"></a>REST API alternativ

Internt anropar PowerShell-cmdletarna för åtgärder i virtuella SQL-nätverk REST API:er. Du kan anropa REST-API:erna direkt.

- [Regler för virtuellt nätverk: Åtgärder][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Förutsättningar

Du måste redan ha ett undernät som är taggat med det specifika tjänstslutpunktsnamnet för det virtuella nätverket *som* är relevant för SQL Database.

- Det relevanta namnet på slutpunktstypen **är Microsoft.Sql**.
- Om undernätet kanske inte är taggat med typnamnet kan du se [Kontrollera att undernätet är en slutpunkt.][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal steg

1. Logga in på [Azure-portalen][http-azure-portal-link-ref-477t].

1. Sök efter och välj **SQL-servrar** och välj sedan din server. Under **Säkerhet** väljer du **Brandväggar och virtuella nätverk**.

1. Ställ **in Tillåt åtkomst till Azure-tjänster** på **AV**.

    > [!IMPORTANT]
    > Om du lämnar kontrollen inställd på **PÅ** accepterar servern kommunikation från alla undernät inom Azure-gränsen. Det är kommunikation som kommer från en av de IP-adresser som identifieras som de inom intervall som definierats för Azure-datacenter. Att lämna kontrollen på **PÅ** kan vara för hög åtkomst ur säkerhetssynpunkt. Funktionen Microsoft Azure Virtual Network tjänstslutpunkt tillsammans med funktionen för regler för virtuellt nätverk i SQL Database tillsammans kan minska din säkerhetsyta.

1. Välj **+ Lägg till befintlig** i avsnittet **Virtuella** nätverk.

    ![Skärmbild som visar hur du väljer + Lägg till befintlig (undernätsslutpunkt som en SQL-regel).][image-portal-firewall-vnet-add-existing-10-png]

1. I det nya **fönstret Skapa/uppdatera** fyller du i rutorna med namnen på dina Azure-resurser.

    > [!TIP]
    > Du måste inkludera rätt adressprefix för ditt undernät. Du hittar värdet **för Adressprefix** i portalen. Gå till **Alla resurser** &gt; **Alla typer** av virtuella &gt; **nätverk**. Filtret visar dina virtuella nätverk. Välj ditt virtuella nätverk och välj **sedan Undernät.** Kolumnen **ADRESSINTERVALL** har det adressprefix som du behöver.

    ![Skärmbild som visar hur du fyller i rutor för den nya regeln.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Välj **knappen OK** längst ned i fönstret.

1. Se den resulterande regeln för virtuellt nätverk i **fönstret** Brandvägg.

    ![Skärmbild som visar den nya regeln i fönstret Brandvägg.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Följande statusar eller tillstånd gäller för reglerna:
>
> - **Klar:** Anger att åtgärden som du initierade har lyckats.
> - **Misslyckades:** Anger att åtgärden som du initierade har misslyckats.
> - **Borttagna:** Gäller endast för borttagningsåtgärden och anger att regeln har tagits bort och inte längre gäller.
> - **InProgress**: Anger att åtgärden pågår. Den gamla regeln gäller när åtgärden är i det här tillståndet.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Relaterade artiklar

- [Tjänstslutpunkter för virtuellt nätverk i Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Brandväggsregler på servernivå och databasnivå][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Nästa steg

- [Använd PowerShell för att skapa en tjänstslutpunkt för virtuellt nätverk och sedan en regel för virtuellt nätverk för SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regler för virtuellt nätverk: Åtgärder med][rest-api-virtual-network-rules-operations-862r] REST API:er

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
