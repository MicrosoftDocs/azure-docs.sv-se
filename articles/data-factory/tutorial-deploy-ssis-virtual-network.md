---
title: Självstudie för att konfigurera en Azure-SSIS integration runtime för att ansluta till ett virtuellt nätverk
description: Lär dig hur du ansluter en Azure-SSIS integration runtime för att ansluta till ett virtuellt Azure-nätverk.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 3dbbce4adc44696fdd12f6ce948b48b34caaed75
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391243"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Konfigurera en Azure-SQL Server Integration Services (SSIS) integration Runtime (IR) för att ansluta till ett virtuellt nätverk

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här självstudien innehåller grundläggande anvisningar för hur du använder Azure Portal för att konfigurera en Azure-SQL Server Integration Services (SSIS) integration Runtime (IR) för att ansluta till ett virtuellt nätverk.

Stegen är:

- Konfigurera ett virtuellt nätverk.
- Anslut Azure-SSIS IR till ett virtuellt nätverk från Azure Data Factory Portal.

## <a name="prerequisites"></a>Förutsättningar

- **Azure-SSIS integration runtime**. Om du inte har en Azure-SSIS integration runtime, [etablera en Azure-SSIS integration runtime i Azure Data Factory](tutorial-deploy-ssis-packages-azure.md) innan du börjar.

- **Användar behörighet**. Den användare som skapar Azure-SSIS IR måste ha [roll tilldelningen](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) minst på Azure Data Factory resurs med något av alternativen nedan:

    - Använd den inbyggda rollen nätverks deltagare. Den här rollen ingår i _Microsoft. Network/ \*_ permission, som har en mycket större omfattning än vad som behövs.
    - Skapa en anpassad roll som endast innehåller nödvändig _Microsoft. Network/virtualNetworks/ \* /Join/Action-_ behörighet. Om du även vill ta med dina egna offentliga IP-adresser för Azure-SSIS IR när du ansluter till den till ett Azure Resource Manager virtuellt nätverk, kan du även ta med _Microsoft. Network/publicIPAddresses/*/Join/Action-_ behörighet i rollen.

- **Virtuellt nätverk**.

    - Om du inte har ett virtuellt nätverk skapar du [ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

    - Kontrol lera att det virtuella nätverkets resurs grupp kan skapa och ta bort vissa Azure-nätverks resurser.
    
        Azure-SSIS IR måste skapa vissa nätverks resurser i samma resurs grupp som det virtuella nätverket. Dessa resurser omfattar:
        - En Azure Load Balancer med namnet *\<Guid> -azurebatch-cloudserviceloadbalancer*
        - En nätverks säkerhets grupp med namnet * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - En offentlig Azure-IP-adress med namnet-azurebatch-cloudservicepublicip
    
        De här resurserna kommer att skapas när din Azure-SSIS IR startas. De kommer att tas bort när Azure-SSIS IR stoppar. För att undvika att blockera Azure-SSIS IR från att stoppa kan du inte återanvända de här nätverks resurserna i dina andra resurser.

    - Kontrol lera att du inte har [resurs låset](../azure-resource-manager/management/lock-resources.md) för resurs gruppen/prenumerationen som det virtuella nätverket tillhör. Om du konfigurerar ett skrivskyddat/borttagnings lås kommer det inte att gå att starta och stoppa Azure-SSIS IR, eller så slutar det svara.

    - Se till att du inte har en Azure Policy tilldelning som förhindrar att följande resurser skapas under resurs gruppen/prenumerationen som det virtuella nätverket tillhör:
        - Microsoft. Network/belastningsutjämnare
        - Microsoft. Network/NetworkSecurityGroups

- Under **nätverks konfigurations** scenarier beskrivs inte i den här självstudien:
    - Om du hämtar dina egna offentliga IP-adresser för Azure-SSIS IR.
    - Om du använder en egen Domain Name System-Server (DNS).
    - Om du använder en nätverks säkerhets grupp (NSG) i under nätet.
    - Om du använder Azure-ExpressRoute eller en användardefinierad väg (UDR).
    - Om du använder anpassade Azure-SSIS IR.
    
    Mer information hittar du i [konfiguration av virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Konfigurera ett virtuellt nätverk

Använd Azure Portal för att konfigurera ett virtuellt nätverk innan du försöker ansluta till ett Azure-SSIS IR.

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöder endast dessa webbläsare Data Factory användar gränssnitt.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **fler tjänster**. Filtrera och välj **virtuella nätverk**.

1. Filtrera efter och välj ditt virtuella nätverk i listan.

1. På sidan **virtuellt nätverk** väljer du **Egenskaper**.

1. Välj kopierings knappen för **resurs-ID** för att kopiera resurs-ID för det virtuella nätverket till Urklipp. Spara ID: t från Urklipp i OneNote eller en fil.

1. På den vänstra menyn väljer du **undernät**.

    - Kontrol lera att det undernät du väljer har tillräckligt med ledigt adress utrymme för att Azure-SSIS IR ska kunna använda. Lämna tillgängliga IP-adresser för minst två gånger IR-nodnummer. Azure reserverar vissa IP-adresser i varje undernät. De här adresserna kan inte användas. De första och sista IP-adresserna i under näten är reserverade för protokoll överensstämmelse och tre fler adresser används för Azure-tjänster. Mer information finns i finns [det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Välj inte GatewaySubnet för att distribuera en Azure-SSIS IR. Den är dedikerad för virtuella nätverks-gatewayer.
    - Använd inte ett undernät som uteslutande används av andra Azure-tjänster (till exempel SQL Database SQL-hanterad instans, App Service och så vidare).

1. Verifiera att Azure Batch-providern är registrerad i Azure-prenumerationen som har det virtuella nätverket. Eller registrera Azure Batch-providern. Om du redan har ett Azure Batch konto i din prenumeration registreras din prenumeration för Azure Batch. (Om du skapar Azure-SSIS IR i Data Factory Portal registreras Azure Batch-providern automatiskt åt dig.)

   1. I Azure Portal väljer du **prenumerationer** på den vänstra menyn.

   1. Välj din prenumeration.

   1. Välj **resurs leverantörer** till vänster och bekräfta att **Microsoft.BatCH** är en registrerad Provider.

   ![Bekräftelse av status registrerad](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Om du inte ser **Microsoft.BatCH** i listan kan du registrera det genom att [skapa ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Ansluta Azure-SSIS IR till ett virtuellt nätverk

När du har konfigurerat ditt Azure Resource Manager virtuella nätverk eller ett klassiskt virtuellt nätverk kan du ansluta Azure-SSIS IR till det virtuella nätverket:

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöder endast dessa webbläsare Data Factory användar gränssnitt.

1. I [Azure Portal](https://portal.azure.com)väljer du **data fabriker** på den vänstra menyn. Om du inte ser **data fabriker** på menyn väljer du **fler tjänster** och i avsnittet **information + analys** väljer du **data fabriker**.

   ![Lista över data fabriker](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Välj din data fabrik med Azure-SSIS IR i listan. Du ser start sidan för din data fabrik. Välj panelen **författare & Monitor** . Du ser Data Factory användar gränssnitt på en separat flik.

   ![Datafabrikens startsida](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. I Data Factory användar gränssnitt växlar du till fliken **Redigera** , väljer **anslutningar** och växlar till fliken **integrerings körningar** .

   ![Fliken "integrerings körningar"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Om din Azure-SSIS IR körs går du till listan **åtgärder** i kolumnen för **integration runtime** och väljer **stopp** knappen för din Azure-SSIS IR. Du kan inte redigera din Azure-SSIS IR förrän du stoppar den.

   ![Stoppa IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. I listan med **integrerings körningar** i kolumnen **åtgärder** väljer du knappen **Redigera** för Azure-SSIS IR.

   ![Redigera integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Gå igenom avsnitten **allmänna inställningar** och **SQL-inställningar** på sidan för integration runtime-installationen genom att välja knappen **Nästa** .

1. I avsnittet **Avancerade inställningar** :
   1. Markera kryss rutan **Välj ett VNet för Azure-SSIS integration runtime att ansluta, Tillåt ADF att skapa vissa nätverks resurser och om du vill ta med egna statiska offentliga IP-adresser** .

   1. För **prenumeration** väljer du den Azure-prenumeration som har ditt virtuella nätverk.

   1. För **plats** är samma plats för integrerings körningen markerad.

   1. I **typ** väljer du typ av virtuellt nätverk: klassisk eller Azure Resource Manager. Vi rekommenderar att du väljer ett Azure Resource Manager virtuellt nätverk, eftersom klassiska virtuella nätverk snart kommer att bli föråldrade.

   1. För **VNet-namn** väljer du namnet på ditt virtuella nätverk. Det bör vara samma som används för SQL Database med tjänst slut punkter för virtuella nätverk eller SQL-hanterad instans med privat slut punkt som värd för SSISDB. Eller bör vara samma som den som är ansluten till ditt lokala nätverk. Annars kan det vara ett virtuellt nätverk för att ta med egna statiska offentliga IP-adresser för Azure-SSIS IR.

   1. För **under näts namn** väljer du namnet på under nätet för ditt virtuella nätverk. Det bör vara samma som används för SQL-datbase med tjänst slut punkter för virtuella nätverk som värd för SSISDB. Eller bör vara ett annat undernät än det som används för din SQL-hanterade instans med privat slut punkt som värd för SSISDB. Annars kan det vara vilket undernät som helst för att ta med egna statiska IP-adresser för Azure-SSIS IR.

   1. Välj **VNet-validering**. Om verifieringen lyckas väljer du **Fortsätt**.

   ![Avancerade inställningar med ett virtuellt nätverk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. I avsnittet **Sammanfattning** granskar du alla inställningar för din Azure-SSIS IR. Välj sedan **Uppdatera**.

1. Starta din Azure-SSIS IR genom att välja **Start** -knappen i kolumnen **åtgärder** för din Azure-SSIS IR. Det tar cirka 20 till 30 minuter att starta Azure-SSIS IR som ansluter till ett virtuellt nätverk.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att ansluta Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).