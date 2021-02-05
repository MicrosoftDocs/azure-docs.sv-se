---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a1fb1c1be8a0203d9f36712fda8e30f0f9354091
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576123"
---
Du kan distribuera profiler för Azure VPN-klienter (Windows 10) med hjälp av Microsoft Intune. Den här artikeln hjälper dig att skapa en Intune-profil med anpassade inställningar.

## <a name="prerequisites"></a>Förutsättningar

* Enheter har redan registrerats med Intune MDM.
* Azure VPN-klienten för Windows 10 har redan distribuerats på klient datorn.
* Endast Windows-version 19H2 eller högre stöds.

## <a name="modify-xml"></a><a name="xml"></a>Ändra XML

I följande steg använder vi en exempel-XML för en anpassad OMA-URI-profil för Intune med följande inställningar:

* Anslut automatiskt
* Identifiering av betrodda nätverk har Aktiver ATS.

Andra alternativ som stöds finns i artikeln om [VPNV2 CSP](https://docs.microsoft.com/windows/client-management/mdm/vpnv2-csp) .

1. Hämta VPN-profilen från Azure Portal och extrahera *azurevpnconfig.xml* -filen från paketet.
1. Kopiera och klistra in texten nedan i en ny text redigerings fil.

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>
        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. Ändra posten mellan ```<ServerUrlList>``` och ```</ServerUrlList>``` med posten från din hämtade profil (azurevpnconfig.xml). Ändra FQDN för "TrustedNetworkDetection" så att den passar din miljö.
1. Öppna den hämtade Azure-profilen (azurevpnconfig.xml) och kopiera innehållet till Urklipp genom att markera texten och trycka på <ctrl> + C. Kopiera allt mellan följande AzVpnProfile rader, men Kopiera inte själva AzVpnProfile-raderna:

   ```
   <AzVpnProfile xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.datacontract.org/2004/07/">
     <any xmlns:d2p1="http://schemas.datacontract.org/2004/07/System.Xml"
       i:nil="true" />
   For example - copy the text in your xml that is located here.
   </AzVpnProfile>
   ```
1. Klistra in den kopierade texten från föregående steg i filen som du skapade i steg 2 mellan ```<CustomConfiguration>  </CustomConfiguration>``` taggarna. Spara filen med ett XML-tillägg.
1. Skriv ned värdet i ```<name>  </name>``` taggarna. Detta är namnet på profilen. Du behöver det här namnet när du skapar profilen i Intune. Stäng filen och kom ihåg var den finns sparad.

## <a name="create-intune-profile"></a>Skapa Intune-profil

I det här avsnittet skapar du en Microsoft Intune-profil med anpassade inställningar.

1. Logga in på Intune och navigera till **enheter – > konfigurations profiler**. Välj **+ Skapa profil**.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-profile.png" alt-text="Konfigurationsprofiler":::
1. För **Plattform** väljer du **Windows 10 och senare**. För **profil** väljer du **anpassad**. Välj sedan **Skapa**.
1. Ge profilen ett namn och en beskrivning och välj sedan **Nästa**.
1. På fliken **konfigurations inställningar** väljer du **Lägg till**.

    * **Namn:** Ange ett namn för konfigurationen.
    * **Beskrivning:** Valfri beskrivning.
    * **OMA-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (du hittar den här informationen i azurevpnconfig.xml-filen i <name></name> taggen).
    * **Datatyp:** Sträng (XML-fil).

   Välj mappikonen och välj filen som du sparade i steg 6 i [XML-](#xml) stegen. Välj **Lägg till**.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png" alt-text="Konfigurationsinställningar" lightbox="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png":::
1. Välj **Nästa**.
1. Under **tilldelningar** väljer du den grupp som du vill skicka konfigurationen till. Välj **Nästa**.
1. Tillämplighets regler är valfria. Definiera eventuella regler om det behövs och välj sedan **Nästa**.
1. På sidan **Granska och skapa** väljer du **skapa**.

    :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/create-profile.png" alt-text="Skapa profil":::
1. Din anpassade profil skapas nu. Microsoft Intune stegen för att distribuera den här profilen finns i [Tilldela användar-och enhets profiler](https://docs.microsoft.com/mem/intune/configuration/device-profile-assign).
