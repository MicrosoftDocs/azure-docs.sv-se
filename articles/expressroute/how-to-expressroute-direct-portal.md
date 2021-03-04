---
title: 'Azure-ExpressRoute: Konfigurera ExpressRoute Direct: Portal'
description: På den här sidan kan du konfigurera ExpressRoute direkt med hjälp av portalen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: b133f1cce4af07d8d5e50e04670741fcf7c936a4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097082"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>Skapa ExpressRoute Direct med hjälp av Azure Portal

Den här artikeln visar hur du skapar ExpressRoute Direct med hjälp av Azure Portal.
Med ExpressRoute Direct kan du ansluta direkt till Microsofts globala nätverk vid peering-platser som är strategiskt fördelade över hela världen. Mer information finns i [Om ExpressRoute Direct](expressroute-erdirect-about.md).

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Innan du använder ExpressRoute Direct måste du först registrera din prenumeration. Registrera dig genom att göra följande via Azure PowerShell:
1.  Logga in på Azure och välj den prenumeration som du vill registrera.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Registrera din prenumeration för offentlig för hands version med följande kommando:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

När du har registrerat dig kontrollerar du att **Microsoft. Network** Resource-providern är registrerad i din prenumeration. När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören.

1. Få åtkomst till dina prenumerations inställningar enligt beskrivningen i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md).
1. I prenumerationen för **Resource providers** kontrollerar du att **Microsoft. Network** -providern visar en **registrerad** status. Om Microsoft. Network Resource-providern inte finns i listan över registrerade providers, lägger du till den.

## <a name="create-expressroute-direct"></a><a name="create-erdir"></a>Skapa ExpressRoute Direct

1. På [Azure Portal](https://portal.azure.com) -menyn eller på **Start** sidan väljer du **skapa en resurs**.

1. På sidan **nytt** går du till fältet **_Sök på Marketplace_*_ och skriver _* ExpressRoute Direct**. Välj sedan **RETUR** för att komma till Sök resultaten.

1. Välj **ExpressRoute Direct** i resultatet.

1. På sidan **ExpressRoute Direct** väljer du **skapa** för att öppna sidan **skapa ExpressRoute Direct** .

1. Börja med att fylla i fälten på sidan **grundläggande** .

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="Sidan Grundinställningar":::

    * **Prenumeration**: den Azure-prenumeration som du vill använda för att skapa en ny ExpressRoute Direct. ExpressRoute Direct Resource och ExpressRoute-kretsar måste vara i samma prenumeration.
    * **Resurs grupp**: Azure-resurs gruppen där den nya ExpressRoute Direct-resursen ska skapas i. Om du inte har en befintlig resurs grupp kan du skapa en ny.
    * **Region**: den offentliga Azure-region som resursen ska skapas i.
    * **Namn**: namnet på den nya ExpressRoute Direct-resursen.

1. Fyll sedan i fälten på sidan **konfiguration** .

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="Skärm bild som visar sidan &quot;skapa ExpressRoute Direct&quot; med fliken &quot;konfiguration&quot; vald.":::

    * **Peering-plats**: peering-platsen där du ska ansluta till ExpressRoute Direct-resursen. Om du vill ha mer information om peering-platser granskar du [ExpressRoute-platser](expressroute-locations-providers.md).
   * **Bandbredd**: den port par bandbredd som du vill reservera. ExpressRoute Direct stöder bandbredds alternativen 10 GB och 100 GB. Om den önskade bandbredden inte är tillgänglig på den angivna peering-platsen [öppnar du en supportbegäran i Azure Portal](https://aka.ms/azsupt).
   * **Inkapsling**: ExpressRoute Direct stöder både QinQ-och Dot1Q-inkapsling.
     * Om QinQ har valts tilldelas varje ExpressRoute-krets dynamiskt en S-tagg och kommer att vara unik i hela ExpressRoute Direct-resursen.
     *  Varje C-tagg på kretsen måste vara unik på kretsen, men inte över ExpressRoute Direct.
     * Om du väljer Dot1Q-inkapsling måste du hantera unikt C-tag (VLAN) över hela ExpressRoute Direct-resursen.
     >[!IMPORTANT]
     >ExpressRoute Direct får bara vara en inkapslings typ. Det går inte att ändra inkapsling när ExpressRoute Direct skapas.
     >

1. Ange eventuella resurs Taggar och välj sedan **Granska + skapa** för att validera ExpressRoute direkta resurs inställningar.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="Skärm bild som visar sidan &quot;skapa ExpressRoute&quot; med fliken &quot;granska + skapa&quot; vald.":::

1. Välj **Skapa**. Ett meddelande visas där du vet att distributionen pågår. Statusen visas på den här sidan när resurserna skapas. 

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>Generera bokstaven för auktorisering (LOA)

Det går inte att skapa en auktoriserad auktoriserad enhet från portalen just nu. Använd **[Azure PowerShell](expressroute-howto-erdirect.md#authorization)** för att få en behörighets beteckning.

## <a name="change-admin-state-of-links"></a><a name="state"></a>Ändra administratörs tillstånd för länkar

Den här processen ska användas för att genomföra ett lager 1-test, vilket säkerställer att varje kors anslutning korrigeras korrekt i varje router för primär och sekundär.

1. På sidan ExpressRoute Direct Resource **Overview** i avsnittet **länkar** väljer du **link1**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="Länk 1" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. Växla inställningen **admin-tillstånd** till **aktive rad** och välj sedan **Spara**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="Admin-tillstånd":::

    >[!IMPORTANT]
    >Faktureringen påbörjas när admin-tillstånd är aktiverat på någon av länkarna.
    >

1. Upprepa samma process för **Link2**.

## <a name="create-a-circuit"></a><a name="circuit"></a>Skapa en krets

Som standard kan du skapa 10 kretsar i prenumerationen där ExpressRoute Direct-resursen är. Det här antalet kan ökas med stöd. Du ansvarar för att spåra både allokerad och Använd bandbredd. Etablerad bandbredd är summan av alla kretsars bandbredd på ExpressRoute Direct-resursen. Använd bandbredd är den fysiska användningen av de underliggande fysiska gränssnitten.

* Det finns ytterligare krets bandbredder som kan användas på ExpressRoute Direct endast för att stödja de scenarier som beskrivs ovan. Detta är: 40 Gbit/s och 100 Gbit/s.

* SkuTier kan vara Local, standard eller Premium.

* SkuFamily måste vara MeteredData. Obegränsad stöds inte på ExpressRoute Direct.

Följande steg hjälper dig att skapa en ExpressRoute-krets från ExpressRoute Direct-arbetsflödet. Om du hellre vill kan du också skapa en krets med hjälp av arbets flödet för vanliga kretsar, även om det inte är någon fördel med att använda arbets flödes stegen för vanliga kretsar för den här konfigurationen. Se [skapa och ändra en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md).

1. I avsnittet ExpressRoute Direct- **Inställningar** väljer du **kretsar** och väljer sedan **+ Lägg till**. 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="Skärm bild som visar ExpressRoute-inställningarna med valda kretsar och Lägg till markerade." lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. Konfigurera inställningarna på **konfigurations** sidan.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="Konfigurations sida – ExpressRoute Direct":::

1. Ange eventuella resurs taggar, Välj **Granska + skapa** för att verifiera värdena innan du skapar resursen.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="Granska och skapa – ExpressRoute Direct":::

1. Välj **Skapa**. Ett meddelande visas där du vet att distributionen pågår. Statusen visas på den här sidan när resurserna skapas. 

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute Direct finns i [översikten](expressroute-erdirect-about.md).
