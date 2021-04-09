---
title: Aktivera replikering för lokala datorer med privata slut punkter
description: Den här artikeln beskriver hur du konfigurerar replikering för lokala datorer med hjälp av privata slut punkter i Site Recovery.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 7f10654e1c96f1756e5864d20fa2a6817385e994
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98629801"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>Replikera lokala datorer med hjälp av privata slut punkter

Med Azure Site Recovery kan du använda privata [Azure Private Link](../private-link/private-endpoint-overview.md) -slutpunkter för att replikera dina lokala datorer till ett virtuellt nätverk i Azure. Åtkomst till privata slut punkter till ett återställnings valv stöds i alla Azures regioner med kommersiell & myndigheter.

I den här artikeln beskrivs hur du utför följande steg:

- Skapa ett Azure Backup Recovery Services valv för att skydda dina datorer.
- Aktivera en hanterad identitet för valvet. Bevilja de behörigheter som krävs för att komma åt lagrings kontona för att aktivera replikering av trafik från lokala platser till Azures mål platser. Hanterad identitets åtkomst för lagring krävs för åtkomst till valvet för privat länk.

- Gör DNS-ändringar som krävs för privata slut punkter.
- Skapa och godkänn privata slut punkter för ett valv i ett virtuellt nätverk.
- Skapa privata slut punkter för lagrings kontona. Du kan fortsätta att tillåta offentlig eller brand Väggs åtkomst för lagring vid behov. Att skapa en privat slut punkt för åtkomst till lagring krävs inte för Azure Site Recovery.
  
Följande diagram visar arbets flödet för replikering för Hybrid haveri beredskap med privata slut punkter. Du kan inte skapa privata slut punkter i ditt lokala nätverk. Om du vill använda privata länkar måste du skapa ett virtuellt Azure-nätverk (kallat ett *kringgående-nätverk* i den här artikeln), upprätta en privat anslutning mellan lokalt och det kringgåde nätverket och sedan skapa privata slut punkter i det kringgåde nätverket. Du kan välja vilken typ av privat anslutning som helst.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Diagram som visar arkitekturen för Azure Site Recovery och privata slut punkter.":::

## <a name="prerequisites-and-caveats"></a>Krav och varningar

- Privata länkar stöds i Site Recovery 9,35 och senare.
- Du kan bara skapa privata slut punkter för nya Recovery Services-valv som inte har några registrerade objekt. Därför måste du skapa privata slut punkter innan några objekt läggs till i valvet. Information om priser finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link/) .
- När du skapar en privat slut punkt för ett valv är valvet låst. Det går endast att komma åt från nätverk som har privata slut punkter.
- Azure Active Directory stöder för närvarande inte privata slut punkter. Så du behöver tillåta utgående åtkomst från det skyddade virtuella Azure-nätverket till IP-adresser och fullständigt kvalificerade domän namn som krävs för att Azure Active Directory ska fungera i en region.
  I förekommande fall kan du också använda nätverks säkerhets grupp tag gen "Azure Active Directory" och Azure Firewall-taggar för att tillåta åtkomst till Azure Active Directory.
- Fem IP-adresser krävs i det kringgåde nätverket där du skapar din privata slut punkt. När du skapar en privat slut punkt för valvet skapar Site Recovery fem privata Länkar för åtkomst till dess mikrotjänster.
- En ytterligare IP-adress krävs i kringgå nätverk för anslutning till ett lagrings konto för privata slut punkter. Du kan använda alla anslutnings metoder mellan lokalt och din slut punkt för lagrings kontot. Du kan till exempel använda Internet-eller Azure- [ExpressRoute](../expressroute/index.yml). Att upprätta en privat länk är valfritt. Du kan bara skapa privata slut punkter för lagring på Generell användning v2-konton. Se [priser för Azure Page blobbar](https://azure.microsoft.com/pricing/details/storage/page-blobs/) för information om priser för data överföring på generell användning v2-konton.

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>Skapa och Använd privata slut punkter för Site Recovery

 I följande avsnitt beskrivs de steg som du måste vidta för att skapa och använda privata slut punkter för Site Recovery i dina virtuella nätverk.

> [!NOTE]
> Vi rekommenderar att du följer de här stegen i den ordning som visas. Om du inte gör det kanske du inte kan använda privata slut punkter i valvet, och du kan behöva starta om processen med ett nytt valv.

### <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services-valv innehåller datorernas replikeringsinformation. Den används för att utlösa Site Recovery åtgärder. Information om hur du skapar ett Recovery Services valv i den Azure-region där du vill redundansväxla om det finns en katastrof, finns i [skapa ett Recovery Services-valv](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

### <a name="enable-the-managed-identity-for-the-vault"></a>Aktivera hanterad identitet för valvet

Med en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) kan valvet komma åt dina lagrings konton. Site Recovery kan behöva komma åt lagrings kontona för mål lagring och cache/logg, beroende på dina behov. Hanterad identitets åtkomst krävs när du använder den privata länk tjänsten för valvet.

1. Gå till Recovery Services-valvet. Välj **identitet** under **Inställningar**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Skärm bild som visar sidan med identitets inställningar.":::

1. Ändra **statusen** till **på** och välj **Spara**.

   Ett objekt-ID genereras. Valvet har nu registrerats med Azure Active Directory.

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Skapa privata slut punkter för Recovery Services valvet

För att skydda datorerna i det lokala käll nätverket behöver du en privat slut punkt för valvet i det kringgåde nätverket. Skapa den privata slut punkten genom att använda det privata länk centret i Azure Portal eller genom att använda [Azure PowerShell](../private-link/create-private-endpoint-powershell.md).

1. Sök efter "privat länk" i sökrutan Azure Portal Sök. Välj **privat länk** för att gå till Private Link Center:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Skärm bild som visar hur du söker Azure Portal för privat länk Center.":::

1. I det vänstra fönstret väljer du **privata slut punkter**. På sidan **privata slut punkter** väljer du **Lägg till** för att börja skapa en privat slut punkt för valvet:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Skärm bild som visar hur du skapar en privat slut punkt i det privata länk centret.":::

1. På sidan **skapa en privat slut punkt** anger du informationen för att skapa din privata slut punkts anslutning.

   1. **Grunderna**. Ange grundläggande information om dina privata slut punkter. Använd den region som du använde för det kringgåde nätverket:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Skärm bild som visar fliken grundläggande för att skapa en privat slut punkt.":::

   1. **Resurs**. På den här fliken måste du ange den plattform-som-tjänst-resurs som du vill skapa anslutningen för. Välj **Microsoft. RecoveryServices/valv** under **resurs typ** för den valda prenumerationen. Välj namnet på Recovery Services valvet under **resurs**. Välj **Azure Site Recovery** som **mål under resurs**.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Skärm bild som visar fliken resurs för länkning till en privat slut punkt.":::

   1. **Konfiguration**. På den här fliken anger du det kringgå nätverk och undernät där du vill att den privata slut punkten ska skapas. 

      Aktivera integrering med en privat DNS-zon genom att välja **Ja**.
      Välj en befintlig DNS-zon eller skapa en ny. Om du väljer **Ja** länkas zonen automatiskt till Bypass-nätverket. Den här åtgärden lägger också till de DNS-poster som krävs för DNS-matchning av nya IP-adresser och fullständigt kvalificerade domän namn som skapats för den privata slut punkten.

      Se till att du väljer att skapa en ny DNS-zon för varje ny privat slut punkt som ansluter till samma valv. Om du väljer en befintlig privat DNS-zon skrivs de tidigare CNAME-posterna över. Se [vägledning för privat slut punkt](../private-link/private-endpoint-overview.md#private-endpoint-properties) innan du fortsätter.

      Om din miljö har en nav-och eker-modell behöver du bara en privat slut punkt och bara en privat DNS-zon för hela installationen. Detta beror på att alla virtuella nätverk redan har peering aktiverat mellan dem. Mer information finns i [DNS-integrering för privata slut punkter](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Om du vill skapa en privat DNS-zon manuellt följer du stegen i [skapa privata DNS-zoner och lägger till DNS-poster manuellt](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Skärm bild som visar fliken konfiguration för konfiguration av en privat slut punkt.":::

   1. **Taggar**. Du kan också lägga till taggar för din privata slut punkt.

   1. **Granska \+ skapa**. När verifieringen är klar väljer du **skapa** för att skapa den privata slut punkten.

När den privata slut punkten skapas läggs fem fullständigt kvalificerade domän namn (FQDN) till i den privata slut punkten. De här länkarna gör att datorerna i det lokala nätverket kan komma åt, via det kringgåde nätverket, alla nödvändiga Site Recovery mikrotjänster i sammanhanget för valvet. Du kan använda samma privata slut punkt för att skydda alla Azure-datorer i det kringgåde nätverket och alla peer-peer-nätverk.

De fem domän namnen är formaterade i det här mönstret:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>Godkänn privata slut punkter för Site Recovery

Om du skapar den privata slut punkten och du också är ägare till Recovery Services valvet, godkänns den privata slut punkten som du skapade tidigare automatiskt inom några minuter. Annars måste ägaren av valvet godkänna den privata slut punkten innan du kan använda den. Om du vill godkänna eller avvisa en begärd privat slut punkts anslutning går du till **anslutningar för privata slut punkter** under **Inställningar** på sidan återställnings valv.

Du kan gå till den privata slut punkts resursen för att granska status för anslutningen innan du fortsätter:

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Skärm bild som visar sidan anslutningar för privata slut punkter i valvet och listan över anslutningar.":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Valfritt Skapa privata slut punkter för lagrings kontot för cache

Du kan använda en privat slut punkt för att Azure Storage. Att skapa privata slut punkter för lagrings åtkomst är valfritt för Azure Site Recovery replikering. Om du skapar en privat slut punkt för lagring behöver du en privat slut punkt för cache-/logg lagrings kontot i ditt kringgå virtuella nätverk.

> [!NOTE]
> Om privata slut punkter inte är aktiverade på lagrings kontot kommer skyddet fortfarande att lyckas. Replikeringstrafik kommer dock att transiteras via Internet till att Azure Site Recovery offentliga slut punkter. För att säkerställa replikeringstrafik via privata länkar måste lagrings kontot vara aktiverat med privata slut punkter.

> [!NOTE]
> Privata slut punkter för lagring kan bara skapas på Generell användning v2-lagrings konton. Information om priser finns i [priser för Azure Page blobbar](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Följ anvisningarna [för att skapa privat lagring](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) för att skapa ett lagrings konto med en privat slut punkt. Se till att välja **Ja** under **integrera med privat DNS-zon**. Välj en befintlig DNS-zon eller skapa en ny.

### <a name="grant-required-permissions-to-the-vault"></a>Bevilja de behörigheter som krävs för valvet

Beroende på din konfiguration kan du behöva ett eller flera lagrings konton i Azures mål Azure-region. Ge sedan hanterade identitets behörigheter för alla cache-/logg lagrings konton som krävs av Site Recovery. I så fall måste du skapa de lagrings konton som krävs i förväg.

Innan du aktiverar replikering av virtuella datorer måste den hanterade identiteten för valvet ha följande roll behörigheter, beroende på typ av lagrings konto.

- Resource Manager-baserade lagrings konton (standard typ):
  - [Deltagare](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage Blob Data-deltagare](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager-baserade lagrings konton (Premium typ):
  - [Deltagare](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage Blob Data-ägare](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klassiska lagrings konton:
  - [Klassisk lagrings konto deltagare](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Klassisk lagrings kontots nyckel operatörs tjänst roll](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

De här stegen beskriver hur du lägger till en roll tilldelning till ditt lagrings konto:

1. Gå till lagringskontot. Välj **åtkomst kontroll (IAM)** i det vänstra fönstret.

1. I avsnittet **Lägg till en roll tilldelning** väljer du **Lägg till**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Skärm bild som visar sidan åtkomst kontroll (IAM) för ett lagrings konto.":::

1. På sidan **Lägg till en roll tilldelning** väljer du rollen i listan i början av det här avsnittet i listan **roll** . Ange namnet på valvet och välj sedan **Spara**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Skärm bild som visar sidan Lägg till roll tilldelning.":::

När du har lagt till dessa behörigheter måste du tillåta åtkomst till Microsoft-betrodda tjänster. Gå till **brand väggar och virtuella nätverk** och välj **Tillåt att betrodda Microsoft-tjänster får åtkomst till det här lagrings kontot** i **undantag**.

### <a name="protect-your-virtual-machines"></a>Skydda dina virtuella datorer

När du har slutfört föregående aktiviteter fortsätter du med installationen av din lokala infrastruktur. Fortsätt genom att utföra någon av följande uppgifter: 

- [Distribuera en konfigurations Server för VMware och fysiska datorer](./vmware-azure-deploy-configuration-server.md)
- [Konfigurera Hyper-V-miljön för replikering](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

När installationen är klar aktiverar du replikering för dina käll datorer. Konfigurera inte infrastrukturen förrän de privata slut punkterna för valvet har skapats i kringgående-nätverket.

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Skapa privata DNS-zoner och Lägg till DNS-poster manuellt

Om du inte har valt alternativet att integrera med en privat DNS-zon när du skapade den privata slut punkten för valvet följer du stegen i det här avsnittet.

Skapa en privat DNS-zon som tillåter Site Recovery-providern (för Hyper-V-datorer) eller processervern (för VMware/fysiska datorer) för att matcha privata FQDN till privata IP-adresser.

1. Skapa en privat DNS-zon.

   1. Sök efter "privat DNS-zon" i Sök fältet **alla tjänster** och välj sedan **privat DNS zon** i resultatet:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Skärm bild som visar sökning efter privat DNS-zon på sidan nya resurser i Azure Portal.":::

   1. På sidan **privat DNS zoner** väljer du knappen **Lägg till** för att börja skapa en ny zon.

   1. Ange den information som krävs på sidan **skapa privat DNS-zon** . Ange **privatelink.siterecovery.windowsazure.com** som namn på den privata DNS-zonen. Du kan välja vilken resurs grupp och vilken prenumeration som helst.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Skärm bild som visar fliken grundläggande på sidan Skapa Privat DNS zon.":::

   1. Fortsätt till fliken **Granska \+ skapande** för att granska och skapa DNS-zonen.

1. Länka den privata DNS-zonen till det virtuella nätverket.

   Du måste nu länka den privata DNS-zon som du skapade till Bypass.

   1. Gå till den privata DNS-zon som du skapade i föregående steg och gå sedan till **virtuella nätverks länkar** i det vänstra fönstret. Välj **Lägg till**.

   1. Ange den information som krävs. I listan **prenumeration** och **virtuella nätverk** väljer du information som motsvarar det kringgåde nätverket. Lämna standardvärdena i övriga fält.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Skärm bild som visar sidan Lägg till virtuell nätverks länk.":::

1. Lägg till DNS-poster.

   Nu när du har skapat den nödvändiga privata DNS-zonen och den privata slut punkten måste du lägga till DNS-poster i din DNS-zon.

   > [!NOTE]
   > Om du använder en anpassad privat DNS-zon måste du se till att du har liknande poster, enligt beskrivningen i följande steg.

   I det här steget måste du skapa poster för varje FQDN i din privata slut punkt i din privata DNS-zon.

   1. Gå till din privata DNS-zon och gå sedan till **översikts** avsnittet i det vänstra fönstret. Välj **post uppsättning** för att börja lägga till poster.

   1. På sidan **Lägg till uppsättning av poster** lägger du till en post för varje fullständigt kvalificerat domän namn och privat IP-adress som **en** typ post. Du kan hämta en lista över de fullständigt kvalificerade domän namnen och IP-adresserna på den **privata slut punkts** sidan i **översikten**. Som du kan se i följande skärm bild läggs det första fullständigt kvalificerade domän namnet från den privata slut punkten till i post uppsättningen i den privata DNS-zonen.

      Dessa fullständigt kvalificerade domän namn matchar det här mönstret: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Skärm bild som visar sidan Lägg till uppsättning av poster.":::

## <a name="next-steps"></a>Nästa steg

Nu när du har aktiverat privata slut punkter för replikeringen av virtuella datorer kan du läsa följande artiklar för ytterligare och relaterad information:

- [Distribuera en lokal konfigurations Server](./vmware-azure-deploy-configuration-server.md)
- [Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer till Azure](./hyper-v-azure-tutorial.md)