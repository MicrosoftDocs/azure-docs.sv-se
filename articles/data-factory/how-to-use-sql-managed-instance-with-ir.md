---
title: Använd Azure SQL Managed instance med Azure-SQL Server Integration Services (SSIS) i Azure Data Factory
description: Lär dig hur du använder Azure SQL-hanterad instans med SQL Server Integration Services (SSIS) i Azure Data Factory.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: 190def0e6e2f77d330d2307753dc9e9d53c55dd7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564161"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Använd Azure SQL-hanterad instans med SQL Server Integration Services (SSIS) i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Du kan nu flytta dina SQL Server Integration Services-projekt (SSIS), paket och arbets belastningar till Azure-molnet. Distribuera, köra och hantera SSIS-projekt och-paket på Azure SQL Database eller SQL-hanterad instans med välbekanta verktyg som SQL Server Management Studio (SSMS). I den här artikeln beskrivs följande olika områden när du använder Azure SQL-hanterad instans med Azure-SSIS integration Runtime (IR):

- [Etablera en Azure-SSIS IR med SSIS-katalogen (SSISDB) som hanteras av Azure SQL-hanterad instans](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [Köra SSIS-paket av Azure SQL-hanterad instans Agent jobb](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Rensa SSISDB-loggar av Azure SQL-hanterad instans Agent jobb](#clean-up-ssisdb-logs)
- [Azure-SSIS IR redundans med Azure SQL-hanterad instans](configure-bcdr-azure-ssis-integration-runtime.md)
- [Migrera lokala SSIS-arbetsbelastningar till SSIS i ADF med Azure SQL Managed instance som databas arbets belastnings mål](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Etablera Azure-SSIS IR med SSISDB som hanteras av en Azure SQL-hanterad instans

### <a name="prerequisites"></a>Förutsättningar

1. [Aktivera Azure Active Directory (Azure AD) på Azure SQL-hanterad instans](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)när du väljer Azure Active Directory autentisering.

1. Välj hur du vill ansluta SQL-hanterad instans, över privat slut punkt eller över offentlig slut punkt:

    - Över privat slut punkt (prioriterad)

        1. Välj det virtuella nätverk som Azure-SSIS IR ska anslutas till:
            - I samma virtuella nätverk som den hanterade instansen, med ett **annat undernät**.
            - I ett annat virtuellt nätverk än den hanterade instansen via virtuell nätverks-peering (som är begränsat till samma region på grund av globala VNet-peering-begränsningar) eller en anslutning från det virtuella nätverket till ett virtuellt nätverk.

            Mer information om anslutningar för SQL-hanterad instans finns i [ansluta ditt program till Azure SQL Managed instance](../azure-sql/managed-instance/connect-application-instance.md).

        1. [Konfigurera virtuellt nätverk](#configure-virtual-network).

    - Över offentlig slut punkt

        Azure SQL Managed instances kan ge anslutning via [offentliga slut punkter](../azure-sql/managed-instance/public-endpoint-configure.md). Inkommande och utgående krav måste uppfyllas för att tillåta trafik mellan SQL-hanterad instans och Azure-SSIS IR:

        - När Azure-SSIS IR inte i ett virtuellt nätverk (önskad)

            **Inkommande krav för SQL-hanterad instans** för att tillåta inkommande trafik från Azure-SSIS IR.

            | Transport protokoll | Källa | Källportintervall | Mål | Målportintervall |
            |---|---|---|---|---|
            |TCP|Azure Cloud Service-tagg|*|VirtualNetwork|3342|

            Mer information finns i [Tillåt trafik för offentliga slut punkter i nätverks säkerhets gruppen](../azure-sql/managed-instance/public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).

        - När Azure-SSIS IR inne i ett virtuellt nätverk

            Det finns ett särskilt scenario när SQL-hanterad instans finns i en region som Azure-SSIS IR inte stöder Azure-SSIS IR finns i ett virtuellt nätverk utan VNet-peering på grund av en begränsning för global VNet-peering. I det här scenariot ansluter **Azure-SSIS IR i ett virtuellt nätverk** SQL-hanterad instans **över offentlig slut punkt**. Använd nedanstående regler för nätverks säkerhets grupper (NSG) för att tillåta trafik mellan SQL-hanterad instans och Azure-SSIS IR:

            1. **Inkommande krav för SQL-hanterad instans** för att tillåta inkommande trafik från Azure-SSIS IR.

                | Transport protokoll | Källa | Källportintervall | Mål |Målportintervall |
                |---|---|---|---|---|
                |TCP|Statisk IP-adress för Azure-SSIS IR <br> Mer information finns i [ta med din egen offentliga IP-adress för Azure-SSIS IR](join-azure-ssis-integration-runtime-virtual-network.md#publicIP).|*|VirtualNetwork|3342|

             1. **Utgående krav för Azure-SSIS IR** för att tillåta utgående trafik till SQL-hanterad instans.

                | Transport protokoll | Källa | Källportintervall | Mål |Målportintervall |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[SQL-hanterad instans offentlig slut punkt IP-adress](../azure-sql/managed-instance/management-endpoint-find-ip-address.md)|3342|

### <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

1. **Användar behörighet**. Den användare som skapar Azure-SSIS IR måste ha [roll tilldelningen](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) minst på Azure Data Factory resurs med något av alternativen nedan:

    - Använd den inbyggda rollen nätverks deltagare. Den här rollen ingår i _Microsoft. Network/ \*_ permission, som har en mycket större omfattning än vad som behövs.
    - Skapa en anpassad roll som endast innehåller nödvändig _Microsoft. Network/virtualNetworks/ \* /Join/Action-_ behörighet. Om du även vill ta med dina egna offentliga IP-adresser för Azure-SSIS IR när du ansluter till den till ett Azure Resource Manager virtuellt nätverk, kan du även använda _Microsoft. Network/publicIPAddresses/*/Join/Action-_ behörighet i rollen.

1. **Virtuellt nätverk**.

    1. Kontrol lera att det virtuella nätverkets resurs grupp kan skapa och ta bort vissa Azure-nätverks resurser.

        Azure-SSIS IR måste skapa vissa nätverks resurser i samma resurs grupp som det virtuella nätverket. Dessa resurser omfattar:
        - En Azure Load Balancer med namnet *\<Guid> -azurebatch-cloudserviceloadbalancer*
        - En nätverks säkerhets grupp med namnet * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - En offentlig Azure-IP-adress med namnet-azurebatch-cloudservicepublicip

        De här resurserna kommer att skapas när din Azure-SSIS IR startas. De kommer att tas bort när Azure-SSIS IR stoppar. För att undvika att blockera Azure-SSIS IR från att stoppa kan du inte återanvända de här nätverks resurserna i dina andra resurser.

    1. Kontrol lera att du inte har [resurs låset](../azure-resource-manager/management/lock-resources.md) för resurs gruppen/prenumerationen som det virtuella nätverket tillhör. Om du konfigurerar ett skrivskyddat/borttagnings lås kommer det inte att gå att starta och stoppa Azure-SSIS IR, eller så slutar det svara.

    1. Kontrol lera att du inte har någon Azure-princip som förhindrar att följande resurser skapas under resurs gruppen/prenumerationen som det virtuella nätverket tillhör:
        - Microsoft. Network/belastningsutjämnare
        - Microsoft. Network/NetworkSecurityGroups

    1. Tillåt trafik i regeln för nätverks säkerhets gruppen (NSG) för att tillåta trafik mellan SQL-hanterad instans och Azure-SSIS IR och trafik som krävs av Azure-SSIS IR.
        1. **Inkommande krav för SQL-hanterad instans** för att tillåta inkommande trafik från Azure-SSIS IR.

            | Transport protokoll | Källa | Källportintervall | Mål | Målportintervall | Kommentarer |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000-11999|Om din SQL Database Server anslutnings princip är inställd på **proxy** i stället för **omdirigering** krävs bara port 1433.|

        1. **Utgående krav för Azure-SSIS IR**, för att tillåta utgående trafik till SQL-hanterad instans och annan trafik som krävs av Azure-SSIS IR.

        | Transport protokoll | Källa | Källportintervall | Mål | Målportintervall | Kommentarer |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 |Tillåt utgående trafik till SQL-hanterad instans. Om anslutnings principen är inställd på **proxy** i stället för **omdirigering** krävs bara port 1433. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | Noderna i Azure-SSIS IR i det virtuella nätverket använder den här porten för att få åtkomst till Azure-tjänster, till exempel Azure Storage och Azure-Event Hubs. |
        | TCP | VirtualNetwork | * | Internet | 80 | Valfritt Noderna i Azure-SSIS IR i det virtuella nätverket Använd den här porten för att hämta en lista över återkallade certifikat från Internet. Om du blockerar den här trafiken kan du få nedgradering av prestanda när du startar IR och förlorar möjlighet att kontrol lera listan över återkallade certifikat för certifikat användning. Om du vill begränsa destinationen till vissa FQDN kan du läsa [Använd Azure-ExpressRoute eller användardefinierad väg (UDR)](./join-azure-ssis-integration-runtime-virtual-network.md#route).|
        | TCP | VirtualNetwork | * | Storage | 445 | Valfritt Den här regeln krävs bara när du vill köra SSIS-paketet som lagras i Azure Files. |
        |||||||

        1. **Inkommande krav för Azure-SSIS IR** för att tillåta trafik som krävs av Azure-SSIS IR.

        | Transport protokoll | Källa | Källportintervall | Mål | Målportintervall | Kommentarer |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (om du ansluter IR till ett virtuellt Resource Manager-nätverk) <br/><br/>10100, 20100, 30100 (om du ansluter IR till ett klassiskt virtuellt nätverk)| Den Data Factory tjänsten använder dessa portar för att kommunicera med noderna i ditt Azure-SSIS IR i det virtuella nätverket. <br/><br/> Oavsett om du skapar en NSG på under näts nivå konfigurerar Data Factory alltid en NSG på nivån för nätverkskorten (NIC) som är anslutna till de virtuella datorer som är värdar för Azure-SSIS IR. Det är bara inkommande trafik från Data Factory IP-adresser på de angivna portarna som tillåts av NSG på NÄTVERKSKORTs nivå. Även om du öppnar de här portarna till Internet trafik på under näts nivån blockeras trafik från IP-adresser som inte Data Factory IP-adresser på NÄTVERKSKORTs nivån. |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | Valfritt Den här regeln krävs bara när Microsofts support uppmanar kunden att öppna för avancerad fel sökning och kan stängas direkt efter fel sökningen. **CorpNetSaw** service tag tillåter endast säker åtkomst till arbets stationer på Microsofts företags nätverk för att använda fjärr skrivbord. Och den här tjänst tag gen kan inte väljas från portalen och är bara tillgänglig via Azure PowerShell eller Azure CLI. <br/><br/> På NÄTVERKSKORTs nivå NSG är port 3389 öppen som standard och vi låter dig kontrol lera port 3389 på under näts nivån NSG, medan Azure-SSIS IR har otillåten port 3389 utgående som standard i Windows-brandväggens regel på varje IR-nod för skydd. |
        |||||||

    1. Mer information finns i [konfiguration av virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration) :
        - Om du hämtar dina egna offentliga IP-adresser för Azure-SSIS IR
        - Om du använder en egen Domain Name System-Server (DNS)
        - Om du använder Azure-ExpressRoute eller en användardefinierad väg (UDR)
        - Om du använder anpassade Azure-SSIS IR

### <a name="provision-azure-ssis-integration-runtime"></a>Etablera Azure-SSIS Integration Runtime

1. Välj privat slut punkt för SQL-hanterad instans eller offentlig slut punkt.

    Vid [etablering Azure-SSIS IR](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) i Azure Portal/ADF-appen, på sidan SQL-inställningar, använder du en **privat slut punkt** för SQL-hanterad instans eller **offentlig slut punkt** när du skapar SSIS-katalogen (SSISDB).

    Värd namnet för den offentliga slut punkten anges i formatet <mi_name>. public. <dns_zone>. database.windows.net och att porten som används för anslutningen är 3342.  

    ![Skärm bild som visar integration runtime-installationen med skapa S-katalogen vald och slut punkten för katalog databas servern angavs.](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. Välj Azure AD-autentisering när det gäller.

    ![Katalog-offentlig-slutpunkt](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Mer information om hur du aktiverar Azure AD-autentisering finns i [Aktivera Azure AD på Azure SQL-hanterad instans](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Anslut Azure-SSIS IR till det virtuella nätverket när det gäller.

    På sidan Avancerad inställning väljer du den Virtual Network och undernät som ska kopplas.
    
    När du är i samma virtuella nätverk som SQL-hanterad instans väljer du ett **annat undernät** än SQL-hanterad instans. 

    Mer information om hur du ansluter Azure-SSIS IR till ett virtuellt nätverk finns i [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

    ![Skärm bild som visar avancerade inställningar för integration runtime-installationen, där du kan välja ett virtuellt nätverk som din körning ska ansluta till.](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

Mer information om hur du skapar en Azure-SSIS IR finns [i skapa en Azure-SSIS integration runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime).

## <a name="clean-up-ssisdb-logs"></a>Rensa SSISDB-loggar

Bevarande princip för SSISDB-loggar definieras av nedanstående egenskaper i [Catalog.catalog_properties](/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database):

- OPERATION_CLEANUP_ENABLED

    När värdet är TRUE tas åtgärds information och åtgärds meddelanden som är äldre än RETENTION_WINDOW (dagar) bort från katalogen. När värdet är FALSe lagras all åtgärds information och åtgärds meddelanden i katalogen. Obs: ett SQL Server jobb utför åtgärden rensa.

- RETENTION_WINDOW

    Antalet dagar som åtgärds information och åtgärds meddelanden lagras i katalogen. När värdet är-1 är kvarhållning fönstret oändligt. Obs: om ingen rensning önskas anger du OPERATION_CLEANUP_ENABLED till falskt.

Om du vill ta bort SSISDB-loggar som är utanför det bevarande fönster som angetts av administratören kan du utlösa den lagrade proceduren `[internal].[cleanup_server_retention_window_exclusive]` . Du kan också schemalägga körning av SQL-hanterad instans agent för att utlösa den lagrade proceduren.

## <a name="next-steps"></a>Nästa steg

- [Köra SSIS-paket av Azure SQL-hanterad instans Agent jobb](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Konfigurera verksamhets kontinuitet och haveri beredskap (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [Migrera lokala SSIS-arbetsbelastningar till SSIS i ADF](scenario-ssis-migration-overview.md)