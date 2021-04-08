---
title: Använd privata slutpunkter med Azure Batch-konton
description: Lär dig hur du ansluter privat till ett Azure Batch-konto med hjälp av privata slut punkter.
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: d2e9d36e9e964f2e9f9a5a986fbf55d19b3069d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920011"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Använd privata slutpunkter med Azure Batch-konton

[Azure Batch-konton](accounts.md) har som standard en offentlig slut punkt och är allmänt tillgängliga. Batch-tjänsten ger möjlighet att skapa privata batch-konton och inaktivera offentlig nätverks åtkomst.

Med hjälp av en [privat Azure-länk](../private-link/private-link-overview.md)kan du ansluta till ett Azure Batch konto via en [privat slut punkt](../private-link/private-endpoint-overview.md). Den privata slut punkten är en uppsättning privata IP-adresser i ett undernät i det virtuella nätverket. Du kan sedan begränsa åtkomsten till ett Azure Batch konto över privata IP-adresser.

Med privat länk kan användare komma åt ett Azure Batch konto inifrån det virtuella nätverket eller från ett peer-kopplat virtuellt nätverk. Resurser som är mappade till privat länk är också tillgängliga lokalt via privat peering via VPN eller [Azure ExpressRoute](../expressroute/expressroute-introduction.md). Du kan ansluta till ett Azure Batch-konto som kon figurer ATS med privat länk genom att använda [metoden automatisk eller manuell godkännande](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow).

> [!IMPORTANT]
> Stöd för privat anslutning i Azure Batch är för närvarande tillgängligt för alla regioner förutom Tyskland Central, Tyskland nordöstra, Kina, östra, Kina, östra 2, Kina, norra och Kina, norra 2.

I den här artikeln beskrivs stegen för att skapa ett privat batch-konto och komma åt det med en privat slut punkt.

## <a name="azure-portal"></a>Azure Portal

Använd följande steg för att skapa ett privat batch-konto med hjälp av Azure Portal:

1. Välj **Batch-tjänst** i fönstret **skapa en resurs** och välj sedan **skapa**.
2. Ange prenumeration, resurs grupp, region och namn på batch-konto på fliken **grundläggande** och välj sedan **Nästa: Avancerat**.
3. På fliken **Avancerat** ställer du in **offentligt nätverks åtkomst** på **inaktive rad**.
4. I **Inställningar** väljer du **privata slut punkts anslutningar** och väljer sedan **+ privat slut punkt**.
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="Anslutningar för privata slut punkter":::
5. I fönstret **grundläggande** anger eller väljer du prenumeration, resurs grupp, resurs namn för privat slut punkt och region information. Välj sedan **Nästa: resurs**.
6. I fönstret **resurs** anger du **resurs typen** till **Microsoft.BatCH/batchAccounts**. Välj det privata batch-konto som du vill få åtkomst till och välj sedan **Nästa: konfiguration**.
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="Skapa en privat slut punkt – resurs fönster":::
7. I rutan **konfiguration** anger eller väljer du den här informationen:
   - **Virtuellt nätverk**: Välj det virtuella nätverket.
   - **Undernät**: Välj ditt undernät.
   - **Integrera med privat DNS-zon**: Välj **Ja**. För att kunna ansluta privat med din privata slut punkt behöver du en DNS-post. Vi rekommenderar att du integrerar din privata slut punkt med en privat DNS-zon. Du kan också använda dina egna DNS-servrar eller skapa DNS-poster med hjälp av värd filerna på dina virtuella datorer.
   - **Privat DNS zon**: Välj privatelink. \<region\> . batch.azure.com. Den privata DNS-zonen fastställs automatiskt. Du kan inte ändra den med hjälp av Azure Portal.
8. Välj **Granska + skapa** och vänta sedan på att Azure ska verifiera konfigurationen.
9. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

När den privata slut punkten har allokerats kan du få åtkomst till batch-kontot från virtuella datorer i samma virtuella nätverk med hjälp av den privata slut punkten.

> [!IMPORTANT]
> Att utföra åtgärder utanför det virtuella nätverk där den privata slut punkten är etablerad leder till ett "AuthorizationFailure"-meddelande i Azure-portalen.

Så här visar du IP-adressen från Azure Portal:

1. Välj **Alla resurser**.
2. Sök efter den privata slut punkt som du skapade tidigare.
3. Välj fliken **Översikt** för att se DNS-inställningar och IP-adresser.

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="DNS-inställningar och IP-adresser för privat slut punkt":::

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

När du [skapar ett batch-konto med hjälp av Azure Resource Manager mall](quick-create-template.md)ändrar du mallen för att ange **PublicNetworkAccess** till **inaktive rad** enligt nedan.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>Konfigurera DNS-zoner

Använd en [privat DNS-zon](../dns/private-dns-privatednszone.md) i det undernät där du har skapat den privata slut punkten. Konfigurera slut punkterna så att varje privat IP-adress mappas till en DNS-post.

När du skapar den privata slut punkten kan du integrera den med en [privat DNS-zon](../dns/private-dns-privatednszone.md) i Azure. Om du väljer att använda en [anpassad domän](../dns/dns-custom-domain.md)i stället måste du konfigurera den för att lägga till DNS-poster för alla privata IP-adresser som reserver ATS för den privata slut punkten.

## <a name="pricing"></a>Priser

Mer information om kostnader som rör privata slut punkter finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link/).

## <a name="current-limitations-and-best-practices"></a>Aktuella begränsningar och bästa praxis

Tänk på följande när du skapar ett privat batch-konto:

- Privata slut punkts resurser måste skapas i samma prenumeration som batch-kontot.
- Om du vill ta bort den privata anslutningen måste du ta bort den privata slut punkts resursen.
- När ett batch-konto har skapats med offentlig nätverks åtkomst kan du inte ändra det till enbart privat åtkomst.
- DNS-poster i den privata DNS-zonen tas inte bort automatiskt när du tar bort en privat slut punkt eller när du tar bort en region från batch-kontot. Du måste ta bort DNS-posterna manuellt innan du lägger till en ny privat slutpunkt som är länkad till den här privata DNS-zonen. Om du inte rensar DNS-posterna kan oväntade problem med data planet inträffa, till exempel data avbrott i regioner som lagts till efter borttagning av privat slut punkt eller region borttagning.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar batch-pooler i virtuella nätverk](batch-virtual-network.md).
- Lär dig hur du [skapar batch-pooler utan offentliga IP-adresser](batch-pool-no-public-ip-address.md)
- Lär dig hur du [skapar batch-pooler med angivna offentliga IP-adresser](create-pool-public-ip.md).
- Lär dig mer om [Azures privata länk](../private-link/private-link-overview.md).
