---
title: Om nätverk i haveri beredskap för Azure VM med Azure Site Recovery
description: Innehåller en översikt över nätverk för replikering av virtuella Azure-datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: harshacs
ms.openlocfilehash: b9fdaf8a0791570ecee402442c5faefe2f70a22b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370448"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Om nätverk i haveri beredskap för virtuella Azure-datorer



Den här artikeln ger nätverks vägledning när du replikerar och återställer virtuella Azure-datorer från en region till en annan med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Innan du börjar

Lär dig hur Site Recovery ger haveri beredskap för [det här scenariot](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typisk nätverks infrastruktur

Följande diagram illustrerar en typisk Azure-miljö för program som körs på virtuella Azure-datorer:

![Diagram som illustrerar en typisk Azure-miljö för program som körs på virtuella Azure-datorer.](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Om du använder Azure-ExpressRoute eller en VPN-anslutning från ditt lokala nätverk till Azure är miljön följande:

![kund-miljö](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalt skyddas nätverk med brand väggar och nätverks säkerhets grupper (NSG: er). Service märken ska användas för att kontrol lera nätverks anslutningen. NSG: er bör tillåta flera service märken att kontrol lera utgående anslutningar.

>[!IMPORTANT]
> Att använda en autentiserad proxy för att kontrol lera nätverks anslutningen stöds inte av Site Recovery, och det går inte att aktivera replikering.

>[!NOTE]
>- Filtrering av IP-adresser ska inte utföras för att kontrol lera utgående anslutningar.
>- Det går inte att lägga till Azure Site Recovery IP-adresser i Azure vägvals tabell för att kontrol lera utgående anslutningar.

## <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för webbadresser

Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutningar kan du tillåta följande Site Recovery webb adresser:

**URL** | **Information**
--- | ---
*.blob.core.windows.net | Krävs så att data kan skrivas till cache-lagrings kontot i käll regionen från den virtuella datorn. Om du känner till alla cache-lagrings konton för dina virtuella datorer kan du tillåta åtkomst till de angivna URL: erna för lagrings konton (t. ex.: cache1.blob.core.windows.net och cache2.blob.core.windows.net) i stället för *. blob.core.windows.net
login.microsoftonline.com | Krävs för auktorisering och autentisering till Site Recovery tjänst-URL: er.
*.hypervrecoverymanager.windowsazure.com | Krävs så att kommunikationen mellan Site Recoverys tjänsten kan ske från den virtuella datorn.
*.servicebus.windows.net | Krävs så att Site Recovery övervakning och diagnostikdata kan skrivas från den virtuella datorn.
*.vault.azure.net | Tillåter åtkomst att aktivera replikering för ADE-aktiverade virtuella datorer via portalen
*. automation.ext.azure.com | Gör det möjligt att aktivera automatisk uppgradering av mobilitets agenten för ett replikerat objekt via portalen

## <a name="outbound-connectivity-using-service-tags"></a>Utgående anslutning med service märken

När du använder NSG för att kontrol lera utgående anslutningar måste dessa service märken vara tillåtna.

- För lagrings kontona i käll regionen:
    - Skapa en NSG-regel för [lagrings tjänst](../virtual-network/network-security-groups-overview.md#service-tags) som är baserad på käll regionen.
    - Tillåt dessa adresser så att data kan skrivas till cache-lagrings kontot från den virtuella datorn.
- Skapa Azure Active Directory en NSG-baserad [(AAD) service tag](../virtual-network/network-security-groups-overview.md#service-tags) -regel för att tillåta åtkomst till alla IP-adresser som motsvarar AAD
- Skapa en EventsHub service tag-baserad NSG-regel för mål regionen, vilket ger åtkomst till Site Recovery övervakning.
- Skapa en AzureSiteRecovery service tag-baserad NSG-regel för att tillåta åtkomst till Site Recovery tjänst i vilken region som helst.
- Skapa en AzureKeyVault-baserad NSG-regel. Detta krävs endast för att aktivera replikering av ADE-aktiverade virtuella datorer via portalen.
- Skapa en GuestAndHybridManagement-baserad NSG-regel. Detta krävs endast för att aktivera automatisk uppgradering av mobilitets agenten för ett replikerat objekt via portalen.
- Vi rekommenderar att du skapar de nödvändiga NSG-reglerna på en test-NSG och kontrollerar att det inte finns några problem innan du skapar reglerna på en produktions NSG.

## <a name="example-nsg-configuration"></a>Exempel på NSG-konfiguration

Det här exemplet visar hur du konfigurerar NSG-regler för en virtuell dator att replikera.

- Om du använder NSG-regler för att kontrol lera utgående anslutningar använder du reglerna "Tillåt HTTPS utgående" till port: 443 för alla nödvändiga IP-adressintervall.
- Exemplet förutsätter att käll platsen för den virtuella datorn är "USA, östra" och att mål platsen är "Central US".

### <a name="nsg-rules---east-us"></a>NSG-regler – USA, östra

1. Skapa en utgående HTTPS-säkerhetsregel (443) för "Storage. öst" på NSG som visas på skärm bilden nedan.

      ![Skärm bild som visar Lägg till utgående säkerhets regel för en nätverks säkerhets grupp för lagrings punkt öst U S.](./media/azure-to-azure-about-networking/storage-tag.png)

2. Skapa en utgående HTTPS (443) säkerhets regel för "AzureActiveDirectory" på NSG som visas på skärm bilden nedan.

      ![Skärm bild som visar Lägg till utgående säkerhets regel för en nätverks säkerhets grupp för Azure A D.](./media/azure-to-azure-about-networking/aad-tag.png)

3. På samma sätt som säkerhets regler, skapar du en utgående HTTPS (443) säkerhets regel för "EventHub. Central" på den NSG som motsvarar mål platsen. Detta ger åtkomst till Site Recovery övervakning.

4. Skapa en utgående HTTPS (443) säkerhets regel för "AzureSiteRecovery" på NSG. Detta ger till gång till Site Recovery tjänst i vilken region som helst.

### <a name="nsg-rules---central-us"></a>NSG-regler – centrala USA

De här reglerna krävs för att replikeringen ska kunna aktive ras från mål regionen till käll regionen efter redundans:

1. Skapa en utgående HTTPS-säkerhets regel (443) för "Storage. Central" på NSG.

2. Skapa en utgående HTTPS (443) säkerhets regel för "AzureActiveDirectory" på NSG.

3. På samma sätt som säkerhets regler, skapar du en utgående HTTPS (443) säkerhets regel för "EventHub. East" på den NSG som motsvarar käll platsen. Detta ger åtkomst till Site Recovery övervakning.

4. Skapa en utgående HTTPS (443) säkerhets regel för "AzureSiteRecovery" på NSG. Detta ger till gång till Site Recovery tjänst i vilken region som helst.

## <a name="network-virtual-appliance-configuration"></a>Konfiguration av virtuell nätverks installation

Om du använder virtuella nätverks installationer (NVA) för att styra utgående nätverks trafik från virtuella datorer kan installationen få en begränsning om all replikeringstrafik passerar genom NVA. Vi rekommenderar att du skapar en nätverks tjänst slut punkt i ditt virtuella nätverk för "lagring" så att replikeringstrafiken inte går till NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Skapa nätverks tjänstens slut punkt för lagring
Du kan skapa en nätverks tjänst slut punkt i ditt virtuella nätverk för "lagring" så att replikeringstrafiken inte lämnar Azure-gränser.

- Välj ditt virtuella Azure-nätverk och klicka på tjänstens slut punkter

    ![lagring – slut punkt](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Klicka på fliken Lägg till och Lägg till tjänst slut punkter öppnas
- Välj Microsoft. Storage under "tjänst" och de undernät som krävs under fältet "undernät" och klicka på "Lägg till"

>[!NOTE]
>Begränsa inte åtkomst till virtuella nätverk till dina lagrings konton som används för ASR. Du bör tillåta åtkomst från alla nätverk

### <a name="forced-tunneling"></a>Tvingad tunneltrafik

Du kan åsidosätta Azures standard system väg för adressprefixet 0.0.0.0/0 med en [anpassad väg och dirigera](../virtual-network/virtual-networks-udr-overview.md#custom-routes) VM-trafik till en lokal virtuell nätverks installation (NVA), men den här konfigurationen rekommenderas inte för Site Recovery replikering. Om du använder anpassade vägar bör du [skapa en tjänst slut punkt för virtuellt nätverk](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i ditt virtuella nätverk för "lagring", så att replikeringstrafiken inte lämnar Azure-gränser.

## <a name="next-steps"></a>Nästa steg
- Börja skydda dina arbets belastningar genom att [Replikera virtuella Azure-datorer](./azure-to-azure-quickstart.md).
- Läs mer om [IP-kvarhållning](site-recovery-retain-ip-azure-vm-failover.md) av redundans för virtuella Azure-datorer.
- Läs mer om haveri beredskap för [virtuella Azure-datorer med ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).