---
title: 'Självstudie: Konfigurera peering för ExpressRoute-krets – Azure Portal'
description: I den här självstudien lär du dig att skapa och etablera ExpressRoute privata och Microsoft-peering med hjälp av Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 11a4798c0cb3bc010bbdbae1fcb709951c67781a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721903"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>Självstudie: skapa och ändra peering för en ExpressRoute-krets med hjälp av Azure Portal

Den här självstudien visar hur du skapar och hanterar konfiguration av routning för en Azure Resource Manager ExpressRoute-krets med hjälp av Azure Portal. Du kan också kontrol lera status, uppdatera eller ta bort och avetablera peer-kopplingar för en ExpressRoute-krets. Om du vill använda en annan metod för att arbeta med din krets väljer du en artikel i följande lista:

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Offentlig peering](about-public-peering.md)
> * [Video-privat peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 

Du kan konfigurera privat peering och Microsoft-peering för en ExpressRoute-krets (offentlig Azure-peering är inaktuell för nya kretsar). Peering kan konfigureras i vilken ordning du väljer. Dock måste du se till att du slutför konfigurationen av en peering i taget. Mer information om routningsdomäner och peering-domäner finns i [ExpressRoute](expressroute-circuit-peerings.md). Information om offentlig peering finns i [ExpressRoute offentlig peering](about-public-peering.md).

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Konfigurera, uppdatera och ta bort Microsoft-peering för en krets
> - Konfigurera, uppdatera och ta bort Azures privata peering för en krets

## <a name="prerequisites"></a>Förutsättningar

* Kontrol lera att du har granskat följande sidor innan du påbörjar konfigurationen:
    * [Förutsättningar](expressroute-prerequisites.md) 
    * [Routningskrav](expressroute-routing.md)
    * [Arbetsflöden](expressroute-workflows.md)
* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och se till att kretsen aktive ras av anslutnings leverantören innan du fortsätter. Om du vill konfigurera peering (-ar) måste ExpressRoute-kretsen vara i ett tillstånd med etablerad och aktiverat. 
* Om du planerar att använda en delad nyckel/MD5-hash, måste du använda nyckeln på båda sidor av tunneln. Gränsen är högst 25 alfanumeriska tecken. Specialtecken stöds inte. 

Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en tjänst leverantör som erbjuder hanterade Layer 3-tjänster (vanligt vis en IPVPN, t. ex. MPLS), konfigurerar din anslutnings leverantör och hanterar operationsföljden åt dig. 

> [!IMPORTANT]
> Vi gör för närvarande inte reklam för peerings som konfigurerats av tjänstleverantörer via tjänsthanteringsportalen. Vi arbetar på att kunna aktivera den här funktionen snart. Kontakta tjänst leverantören innan du konfigurerar BGP-peering.
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-peering

Det här avsnittet hjälper dig att skapa, Hämta, uppdatera och ta bort Microsofts peering-konfiguration för en ExpressRoute-krets.

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som har kon figurer ATS före den 1 augusti 2017 kommer att ha alla tjänst prefix som annonseras via Microsoft-peering, även om det inte finns några väg filter definierade. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inga prefix som annonseras förrän ett flödes filter är kopplat till kretsen. Mer information finns i [Konfigurera ett flödes filter för Microsoft-peering](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering

1. Konfigurera ExpressRoute-kretsen. Kontrol lera **providerns status** för att säkerställa att kretsen är helt etablerad av anslutnings leverantören innan du fortsätter.

   Om din anslutnings leverantör erbjuder hanterade Layer 3-tjänster kan du be din anslutnings leverantör att aktivera Microsoft-peering åt dig. Du behöver inte följa instruktionerna som anges i nästa avsnitt. Men om din anslutnings leverantör inte hanterar routning åt dig, kan du fortsätta med de här stegen när du har skapat kretsen.

   **Krets-Provider-status: inte etablerad**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned.png" alt-text="Skärm bild som visar översikts sidan för ExpressRoute-demo kretsen med en röd ruta som markerar providerns status som inte etablerad":::

   **Krets-Provider-status: etablerad**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned.png" alt-text="Skärm bild som visar översikts sidan för ExpressRoute-demo kretsen med en röd ruta som markerar providerns status som etablerad":::

2. Konfigurera Microsoft-peering för kretsen. Kontrol lera att du har följande information innan du fortsätter.

   * Ett par med undernät som ägs av dig och registrerats i en RIR/IR. Ett undernät kommer att användas för den primära länken, medan det andra används för den sekundära länken. Från vart och ett av dessa undernät tilldelar du den första användbara IP-adressen till routern som Microsoft använder den andra användbara IP-adressen för sin router. Det finns tre alternativ för det här paret undernät:
       * IPv4: två/30 undernät. Dessa måste vara giltiga offentliga IPv4-prefix.
       * IPv6: två/126-undernät. Dessa måste vara giltiga offentliga IPv6-prefix.
       * Både: två/30 undernät och två/126-undernät.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Annonserade prefix: du anger en lista över alla prefix som du planerar att annonsera över BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.
   * **Valfritt-** Kund-ASN: om du annonserar prefix som inte har registrerats på peering AS Number kan du ange det AS-nummer som de är registrerade på.
   * Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.
   * **Valfritt-** En MD5-hash om du väljer att använda en.
1. Du kan välja den peering som du vill konfigurera, som du ser i följande exempel. Välj Microsofts peering-rad.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-microsoft-peering.png" alt-text="Välj Microsoft-peering-raden":::

4. Konfigurera Microsoft-peering. **Spara** konfigurationen när du har angett alla parametrar. Följande bild visar en exempel konfiguration:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m-validation-needed.png" alt-text="Konfigurera Microsoft peering-verifiering krävs":::

> [!IMPORTANT]
> Microsoft kontrollerar om de angivna annonserade offentliga prefixen och peer-ASN (eller kund-ASN) är tilldelade till dig i Internet-routningstabellen. Om du hämtar de offentliga prefixen från en annan entitet och om tilldelningen inte är registrerad i routningstabellen, slutförs inte den automatiska verifieringen och kräver manuell verifiering. Om den automatiska valideringen Miss lyckas visas meddelandet "validering krävs". 
>
> Om du ser meddelandet "validering krävs" samlar du in dokumenten som visar att de offentliga prefixen har tilldelats till din organisation av den entitet som anges som ägare till prefixen i routningstabellen och skickar dessa dokument för manuell verifiering genom att öppna ett support ärende. 
>

   Om din krets har statusen "validering krävs" måste du öppna ett support ärende för att Visa bevis för ägarskapet av prefixen till vårt support team. Du kan öppna ett support ärende direkt från portalen, som du ser i följande exempel:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png" alt-text="Validering krävs – support ärende":::

5. När konfigurationen har godkänts ser du något som liknar följande bild:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/microsoft-peering-validation-configured.png" alt-text="Peering-status: konfigurerad":::

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Så här visar du Microsofts peering-information

Du kan visa egenskaperna för Microsoft-peering genom att välja raden för peer koppling.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Visa egenskaper för Microsoft-peering":::

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Så här uppdaterar du Microsofts peering-konfiguration

Du kan välja raden för peering som du vill ändra och sedan ändra peering-egenskaperna och spara dina ändringar.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png" alt-text="Välj peering-rad":::

## <a name="azure-private-peering"></a><a name="private"></a>Privat peering i Azure

Det här avsnittet hjälper dig att skapa, Hämta, uppdatera och ta bort Azures konfiguration för privata peering för en ExpressRoute-krets.

> [!IMPORTANT]
> IPv6-stöd för privat peering är för närvarande en **offentlig för hands version**. Om du vill ansluta ditt virtuella nätverk till en ExpressRoute-krets med IPv6-baserad privat peering konfigurerad kontrollerar du att ditt virtuella nätverk är dubbel stack och följer rikt linjerna som beskrivs [här](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).
> 
> 

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter. 

   Om din anslutnings leverantör erbjuder hanterade Layer 3-tjänster kan du be din anslutnings leverantör att aktivera Azures privata peering åt dig. Du behöver inte följa instruktionerna som anges i nästa avsnitt. Men om din anslutnings leverantör inte hanterar routning åt dig, kan du fortsätta med nästa steg när du har skapat kretsen.

   **Krets-Provider-status: inte etablerad**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-private.png" alt-text="Skärm bild som visar översikts sidan för ExpressRoute-demo kretsen med en röd ruta som markerar leverantörs statusen som är inställd på inte etablerad":::

   **Krets-Provider-status: etablerad**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned-private-peering.png" alt-text="Skärm bild som visar översikts sidan för ExpressRoute-demo kretsen med en röd ruta som markerar leverantörens status som är inställd på etablerad":::

2. Konfigurera Azures privata peering för kretsen. Kontrol lera att du har följande objekt innan du fortsätter med nästa steg:

   * Ett par undernät som inte ingår i något adress utrymme som är reserverat för virtuella nätverk. Ett undernät kommer att användas för den primära länken, medan det andra används för den sekundära länken. Från vart och ett av dessa undernät tilldelar du den första användbara IP-adressen till routern som Microsoft använder den andra användbara IP-adressen för sin router. Det finns tre alternativ för det här paret undernät:
       * IPv4: två/30 undernät.
       * IPv6: två/126-undernät.
       * Både: två/30 undernät och två/126-undernät.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-nummer för denna peering förutom numret från 65515 till 65520.
   * Du måste annonsera vägarna från din lokala Edge-router till Azure via BGP när du konfigurerar privat peering.
   * **Valfritt-** En MD5-hash om du väljer att använda en.
3. Välj den Azure-privata peering-raden som visas i följande exempel:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-private-peering.png" alt-text="Välj den privata peering-raden":::

4. Konfigurera privat peering. **Spara** konfigurationen när du har angett alla parametrar.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/private-peering-configuration.png" alt-text="Konfigurera privat peering":::

5. När konfigurationen har godkänts ser du något som liknar följande exempel:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/save-private-peering.png" alt-text="Sparad privat peering":::

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Så här visar du Azures privata peering-information

Du kan visa egenskaperna för Azures privata peering genom att välja den peeringen.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Visa egenskaper för privata peering":::

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Uppdatera konfigurationen av Azures privata peering

Du kan välja raden för peering och ändra peering-egenskaperna. Spara ändringarna efter uppdateringen.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/update-private-peering.png" alt-text="Uppdatera privat peering":::

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Så här tar du bort Microsoft-peering

Du kan ta bort konfigurationen för Microsoft-peering genom att högerklicka på peering och välja **ta bort** som visas i följande bild:

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-microsoft-peering.png" alt-text="Ta bort Microsoft-peering":::

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Så här tar du bort Azures privata peering

Du kan ta bort din privata peering-konfiguration genom att högerklicka på peering och välja **ta bort** som visas i följande bild:

> [!WARNING]
> Du måste se till att alla virtuella nätverk och ExpressRoute Global Reach-anslutningar tas bort innan du kör den här åtgärden. 
> 

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-private-peering.png" alt-text="Ta bort privat peering":::

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Azures privata peering kan du skapa en ExpressRoute-Gateway för att länka ett virtuellt nätverk till kretsen. 

> [!div class="nextstepaction"]
> [Konfigurera en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
