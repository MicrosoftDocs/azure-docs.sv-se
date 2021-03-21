---
title: 'Snabb start: dirigera till delade tjänster med en ARM-mall'
titleSuffix: Azure Virtual WAN
description: Den här snabb starten visar hur du konfigurerar vägar för att få åtkomst till en delad tjänst-VNet med en arbets belastning som du vill att varje VNet och gren ska få åtkomst till med hjälp av en Azure Resource Manager mall (ARM-mall).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: subject-armqs
ms.openlocfilehash: f325dd445e778bf03049d2c9e2e00fed7a427ccf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443971"
---
# <a name="quickstart-route-to-shared-services-vnets-using-an-arm-template"></a>Snabb start: dirigera till delade tjänster virtuella nätverk med en ARM-mall

I den här snabb starten beskrivs hur du använder en Azure Resource Manager mall (ARM-mall) för att konfigurera vägar för att få åtkomst till ett virtuellt nätverk för delade tjänster med arbets belastningar som du vill att varje VNet och gren (VPN/ER/P2S) ska ha åtkomst till Exempel på dessa delade arbets belastningar kan vara virtuella datorer med tjänster som domänkontrollanter eller fil resurser eller Azure-tjänster som exponeras internt via [privat Azure-slutpunkt](../private-link/private-endpoint-overview.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f301-virtual-wan-with-route-tables%2fazuredeploy.json)

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* Certifikat data för offentlig nyckel krävs för den här konfigurationen. Exempel data finns i artikeln. Exempel data tillhandahålls dock endast för att uppfylla mallens krav för att skapa en P2S-Gateway. När mallen har slutförts och resurserna har distribuerats måste du uppdatera det här fältet med dina egna certifikat data för att konfigurationen ska fungera. Se [VPN-certifikat för användare](certificates-point-to-site.md#cer).

## <a name="review-the-template"></a><a name="review"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/301-virtual-wan-with-route-tables). Mallen för den här artikeln är för lång för att kunna visas här. Om du vill visa mallen går du till [azuredeploy.jspå](https://github.com/Azure/azure-quickstart-templates/blob/master/301-virtual-wan-with-route-tables/azuredeploy.json).

I den här snabb starten skapar du en virtuell WAN-distribution med flera hubbar i Azure, inklusive alla gatewayer och VNet-anslutningar. Listan över indataparametrar har avsiktligt hållits. Du kan ändra IP-adresserings planen genom att ändra variablerna i mallen. Scenariot beskrivs ytterligare i artikeln [Scenario: VNet](scenario-shared-services-vnet.md) -artikel för delade tjänster.

:::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Distributions arkitektur" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

Den här mallen skapar en fullständigt fungerande virtuell WAN-miljö med följande resurser:

* 2 distinkta hubbar i olika regioner.
* 4 virtuella Azure-nätverk (VNet).
* 2 VNet-anslutningar för varje VWan-hubb.
* 1 punkt-till-plats (P2S) VPN-gateway i varje hubb.
* 1 plats-till-plats (S2S) VPN-gateway i varje hubb.
* 1 ExpressRoute-gateway i varje hubb.
* Anpassade routningstabeller RT_SHARED i varje hubb.
* En etikett LBL_RT_SHARED att gruppera RT_SHARED routningstabeller.

Flera Azure-resurser definieras i mallen:

* [**Microsoft. Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft. Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft. Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft. Network/hubroutetables**](/azure/templates/microsoft.network/virtualhubs/hubRouteTables)
* [**Microsoft. Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft. Network/vpngateways**](/azure/templates/microsoft.network/vpngateways)
* [**Microsoft. Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft. Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnServerConfigurations)

>[!NOTE]
> Den här ARM-mallen skapar inte resurser på kund sidan som krävs för Hybrid anslutning. När du har distribuerat mallen måste du fortfarande skapa och konfigurera P2S VPN-klienter, VPN-grenar (lokala platser) och ansluta ExpressRoute-kretsar.
>

Du hittar fler mallar i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a><a name="deploy"></a>Distribuera mallen

Om du vill distribuera den här mallen korrekt måste du använda knappen för att distribuera till Azure-knappen och Azure Portal, i stället för andra metoder, av följande anledningar:

* För att kunna skapa P2S-konfigurationen måste du ladda upp rot certifikat data. Data fältet accepterar inte certifikat data när du använder PowerShell eller CLI.
* Den här mallen fungerar inte korrekt med Cloud Shell på grund av överföring av certifikat data.
* Dessutom kan du enkelt ändra mallen och parametrarna i portalen för att hantera IP-adressintervall och andra värden.

1. Klicka på **Distribuera till Azure**.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f301-virtual-wan-with-route-tables%2fazuredeploy.json)
1. Klicka på **Redigera mall** om du vill visa mallen. På den här sidan kan du justera några av värdena, till exempel adress utrymme eller namnet på vissa resurser. **Spara** för att spara ändringarna eller **Ignorera**.
1. Ange värdena på sidan Mall. För den här mallen krävs P2S offentliga certifikat data. Om du använder den här artikeln som en övning kan du använda följande data från CER-filen som exempel data för båda hubbarna. När mallen har körts och distributionen är klar måste du ersätta den här informationen med [certifikat data](certificates-point-to-site.md#cer) från den offentliga nyckeln för din egen distribution för att kunna använda P2s-konfigurationen.

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. När du är klar med att ange värden väljer du **Granska + skapa**.
1. På sidan **Granska och skapa** efter verifierings steg väljer du **skapa**.
1. Det tar ungefär 75 minuter för distributionen att slutföras. Du kan se förloppet på sidan **Översikt över** mallen. Om du stänger portalen fortsätter distributionen.

   :::image type="content" source="./media/quickstart-route-shared-services-template/template.png" alt-text="Exempel på distribution har slutförts":::

## <a name="validate-the-deployment"></a><a name="validate"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **resurs grupper** i det vänstra fönstret.
1. Välj den resurs grupp som du skapade i föregående avsnitt. På sidan **Översikt** visas något som liknar det här exemplet: :::image type="content" source="./media/quickstart-route-shared-services-template/resources.png" alt-text="exempel på resurser" lightbox="./media/quickstart-route-shared-services-template/resources.png":::

1. Klicka på det virtuella WAN-nätverket för att Visa hubbarna. På den virtuella WAN-sidan klickar du på varje hubb för att Visa anslutningar och annan information om hubben.
   :::image type="content" source="./media/quickstart-route-shared-services-template/hub.png" alt-text="Exempel på hubbar" lightbox="./media/quickstart-route-shared-services-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>Slutför hybrid konfigurationen

Mallen konfigurerar inte alla inställningar som krävs för ett hybrid nätverk. Du måste utföra följande konfigurationer och inställningar, beroende på dina behov.

* [Konfigurera VPN-grenar – lokala platser](virtual-wan-site-to-site-portal.md#site)
* [Slutför VPN-konfigurationen för P2S](virtual-wan-point-to-site-portal.md)
* [Anslut ExpressRoute-kretsarna](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du har skapat tar du bort dem. Några av de virtuella WAN-resurserna måste tas bort i en viss ordning på grund av beroenden. Borttagningen kan ta ungefär 30 minuter att slutföra.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Slutför VPN-konfigurationen för P2S](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [Konfigurera VPN-grenar – lokala platser](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [Anslut ExpressRoute-kretsarna](virtual-wan-expressroute-portal.md)
