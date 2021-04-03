---
title: Nätverkssäkerhet i Azure Data Lake Storage Gen1 | Microsoft Docs
description: Förstå hur integreringen av virtuella nätverk fungerar i Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 716e3766fdd7c1999efa12456346862a9902d7a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108719"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Integrering av virtuella nätverk för Azure Data Lake Storage Gen1

Den här artikeln introducerar integrering av virtuella nätverk för Azure Data Lake Storage Gen1. Med integrering av virtuella nätverk kan du konfigurera dina konton för att endast acceptera trafik från specifika virtuella nätverk och undernät. 

Den här funktionen hjälper till att skydda ditt Data Lake Storage-konto från externa hot.

Integrering av virtuella nätverk för Data Lake Storage Gen1 utnyttjar säkerheten i tjänstslutpunkter för virtuella nätverk mellan ditt virtuella nätverk och Azure Active Directory-tjänsten (Azure AD) för att generera ytterligare säkerhetsanspråk i åtkomsttoken. Dessa anspråk används sedan för att autentisera ditt virtuella nätverk till ditt Data Lake Storage Gen1-konto och tillåta åtkomst.

> [!NOTE]
> Det finns ingen extra kostnad med att använda dessa funktioner. Kontot faktureras enligt standardavgifterna för Data Lake Storage Gen1. Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable). Information om alla andra Azure-tjänster som du använder finns i [prissättning](https://azure.microsoft.com/pricing/#product-picker).

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Scenarier för integrering av virtuella nätverk för Data Lake Storage Gen1

Med integrering av virtuella nätverk för Data Lake Storage Gen1 kan du begränsa åtkomsten till ditt Data Lake Storage Gen1-konto från specifika virtuella nätverk och undernät. När ditt konto är låst till de angivna virtuella nätverkets undernät tillåts inte åtkomst för andra virtuella nätverk/virtuella datorer i Azure. Funktionellt möjliggör integrering av virtuella nätverk med Data Lake Storage Gen1 samma scenario som [tjänstslutpunkter för virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md). Det finns några viktiga skillnader som beskrivs i följande avsnitt. 

![Scenariodiagram för integrering av virtuella nätverk med Data Lake Storage Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> De befintliga IP-brandväggsreglerna kan användas utöver regler för virtuellt nätverk för att även tillåta åtkomst från lokala nätverk. 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Optimal routning med integrering av virtuella nätverk med Data Lake Storage Gen1

En viktig fördel med tjänstslutpunkter för virtuellt nätverk är [optimal routning](../virtual-network/virtual-network-service-endpoints-overview.md#key-benefits) från ditt virtuella nätverk. Du kan utföra samma vägoptimering till Data Lake Storage Gen1-konton. Använd följande [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md#user-defined) från ditt virtuella nätverk till ditt Data Lake Storage Gen1-konto.

**Offentlig IP-adress för Data Lake Storage** – Använd den offentliga IP-adressen för dina Data Lake Storage Gen1-målkonton. För att identifiera IP-adresserna för ditt Data Lake Storage Gen1-konto [löser du DNS-namnen](./data-lake-store-connectivity-from-vnets.md#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) för dina konton. Skapa en separat post för varje adress.

```azurecli
# Create a route table for your resource group.
az network route-table create --resource-group $RgName --name $RouteTableName

# Create route table rules for Data Lake Storage public IP addresses.
# There's one rule per Data Lake Storage public IP address. 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the virtual network, and apply the newly created route table to it.
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>Dataexfiltrering från kundens virtuella nätverk

Utöver att skydda Data Lake Storage-konton för åtkomst från det virtuella nätverket är du kanske även intresserad av att se till att det inte finns någon exfiltrering till ett obehörigt konto.

Använd en brandväggslösning i det virtuella nätverket för att filtrera utgående trafik baserat på målkontots URL. Tillåt åtkomst endast till godkända Data Lake Storage Gen1-konton.

Några tillgängliga alternativ är:
- [Azure Firewall](../firewall/overview.md): [Distribuera och konfigurera en Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md) för det virtuella nätverket. Skydda den utgående Data Lake Storage-trafiken och låsa den till den kända och godkända konto-URL:en.
- [Brandvägg för virtuell nätverksinstallation](https://azure.microsoft.com/solutions/network-appliances/): Administratören kan tillåta att endast vissa kommersiella brandväggsleverantörer används. Använd en brandväggslösning för virtuell nätverksinstallation som är tillgänglig på Azure Marketplace för att utföra samma funktion.

> [!NOTE]
> Användning av brandväggar i datasökvägen introducerar ytterligare ett hopp i datasökvägen. Det kan påverka nätverkets prestanda för slutpunkt till slutpunkt-datautbyte. Dataflödestillgänglighet och anslutningens svarstid kan påverkas. 

## <a name="limitations"></a>Begränsningar

- HDInsight-kluster som skapades innan det fanns stöd för integrering av virtuella nätverk med Data Lake Storage Gen1 måste återskapas för att ge stöd för den här nya funktionen.
 
- När du skapar ett nytt HDInsight-kluster och väljer ett Data Lake Storage Gen1-konto med integrering av virtuella nätverk aktiverat misslyckas processen. Inaktivera först regeln för virtuellt nätverk. Eller så kan du på bladet **Brandvägg och virtuella nätverk** i Data Lake Storage-kontot välja **Tillåt åtkomst från alla nätverk och tjänster**. Skapa därefter HDInsight-klustret innan du slutligen återaktiverar regeln för virtuellt nätverk eller avmarkerar **Allow access from all networks and services (Tillåt åtkomst från alla nätverk och tjänster)**. Mer information finns i avsnittet [Undantag](#exceptions).

- Integrering av virtuella nätverk med Data Lake Storage Gen1 fungerar inte med [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).
  
- Fil- och mappdata i ditt Data Lake Storage Gen1-konto med virtuellt nätverk aktiverat är inte tillgängliga från portalen. Den här begränsningen omfattar åtkomst från en virtuell dator som finns i det virtuella nätverket och aktiviteter som att använda Datautforskaren. Kontohanteringsaktiviteter fortsätter att fungera. Fil- och mappdata i ditt Data Lake Storage-konto med virtuellt nätverk aktiverat är tillgängliga via alla icke-portalresurser. Dessa resurser inkluderar SDK-åtkomst, PowerShell-skript och andra Azure-tjänster när de inte kommer från portalen. 

## <a name="configuration"></a>Konfiguration

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>Steg 1: Konfigurera ditt virtuella nätverk till att använda en Azure AD-tjänstslutpunkt

1.  Gå till Azure-portalen och logga in på ditt konto.
 
2.  [Skapa ett nytt virtuellt nätverk](../virtual-network/quick-create-portal.md) i din prenumeration. Eller så går du till ett befintligt virtuellt nätverk. Det virtuella nätverket måste finnas i samma region som Data Lake Storage Gen1-kontot.
 
3.  På bladet **Virtuellt nätverk** väljer du **Tjänstslutpunkter**.
 
4.  Välj **Lägg till** för att lägga till en ny tjänstslutpunkt.

    ![Lägga till en tjänstslutpunkt för virtuellt nätverk](media/data-lake-store-network-security/config-vnet-1.png)

5.  Välj **Microsoft.AzureActiveDirectory** som tjänst för slutpunkten.

     ![Välja tjänstslutpunkten Microsoft.AzureActiveDirectory](media/data-lake-store-network-security/config-vnet-2.png)

6.  Välj de undernät som du vill tillåta anslutningsmöjligheter för. Välj **Lägg till**.

    ![Välja undernätet](media/data-lake-store-network-security/config-vnet-3.png)

7.  Det kan ta upp till 15 minuter för tjänstslutpunkten att läggas till. När den har lagts till visas den i listan. Kontrollera att den visas och att all information är enligt konfigurationen.
 
    ![Lyckat tillägg av tjänstslutpunkten](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>Steg 2: Konfigurera det tillåtna virtuella nätverket eller undernätet för ditt Data Lake Storage Gen1-konto

1.  När du har konfigurerat det virtuella nätverket [skapar du ett nytt Azure Data Lake Storage Gen1-konto](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) i din prenumeration. Eller så går du till ett befintligt Data Lake Storage Gen1-konto. Data Lake Storage Gen1-kontot måste finnas i samma region som det virtuella nätverket.
 
2.  Välj **Brandvägg och virtuella nätverk**.

    > [!NOTE]
    > Om du inte ser **Brandvägg och virtuella nätverk** i inställningarna loggar du ut från portalen. Stäng webbläsaren och rensa webbläsarens cacheminne. Starta om datorn och försök igen.

       ![Lägga till en regel för virtuellt nätverk till ditt Data Lake Storage-konto](media/data-lake-store-network-security/config-adls-1.png)

3.  Välj **Valda nätverk**.
 
4.  Välj **Lägg till befintligt virtuellt nätverk**.

    ![Lägga till befintligt virtuellt nätverk](media/data-lake-store-network-security/config-adls-2.png)

5.  Välj de virtuella nätverk och undernät som du vill tillåtna för anslutningar. Välj **Lägg till**.

    ![Välja virtuellt nätverk och undernät](media/data-lake-store-network-security/config-adls-3.png)

6.  Se till att de virtuella nätverken och undernäten visas korrekt i listan. Välj **Spara**.

    ![Spara den nya regeln](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > Det kan ta upp till 5 minuter innan inställningarna börjar gälla när du har sparat.

7.  [Valfritt] På sidan **Brandvägg och virtuella nätverk** kan du i avsnittet **Brandvägg** tillåta anslutningar från specifika IP-adresser. 

## <a name="exceptions"></a>Undantag
Du kan aktivera anslutningar från Azure-tjänster och virtuella datorer utanför dina valda virtuella nätverk. På bladet **Brandvägg och virtuella nätverk** väljer du bland två val i området **Undantag**:
 
- **Tillåt att alla Azure-tjänster får åtkomst till det här Data Lake Storage Gen1-kontot**. Det här alternativet tillåter att Azure-tjänster som Azure Data Factory, Azure Event Hubs och alla virtuella Azure-datorer kommunicerar med ditt Data Lake Storage-konto.

- **Ge Azure Data Lake Analytics åtkomst till detta Data Lake Storage Gen1-konto**. Det här alternativet tillåter Data Lake Analytics-anslutning till det här Data Lake Storage-kontot. 

  ![Undantag för brandvägg och virtuella nätverk](media/data-lake-store-network-security/firewall-exceptions.png)

Vi rekommenderar att du behåller de här undantagen avstängda. Aktivera dem bara om du behöver anslutning mellan dessa andra tjänster utifrån ditt virtuella nätverk.