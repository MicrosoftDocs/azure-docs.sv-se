---
title: Anslut privat till en Azure-webbapp med privat slut punkt
description: Anslut privat till en webbapp med hjälp av privat Azure-slutpunkt
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/16/2021
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 4de405e07a9ae9d1efb33f2cee3630a1eefdef33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655911"
---
# <a name="using-private-endpoints-for-azure-web-app"></a>Använda privata slutpunkter för Azure Web Apps

> [!IMPORTANT]
> Privat slut punkt är tillgänglig för Windows-och Linux-webbappar, som är behållare eller inte, som finns på dessa App Service planer: **isolerade**, **PremiumV2**, **PremiumV3**, **Functions Premium** (kallas ibland elastisk Premium-plan). 

Du kan använda privat slut punkt för din Azure-webbapp för att tillåta klienter som finns i ditt privata nätverk att säkert komma åt appen via privat länk. Den privata slut punkten använder en IP-adress från ditt Azure VNet-adressutrymme. Nätverks trafik mellan en klient i ditt privata nätverk och webbappen går igenom VNet och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet.

Med hjälp av privat slut punkt för din webbapp kan du:

- Skydda din webbapp genom att konfigurera den privata slut punkten och eliminera offentlig exponering.
- Anslut säkert till en webbapp från lokala nätverk som ansluter till VNet med en VPN-eller ExpressRoute-peering.
- Undvik eventuella data exfiltrering från ditt VNet. 

Om du bara behöver en säker anslutning mellan ditt VNet och din webbapp är en tjänst slut punkt den enklaste lösningen. Om du också behöver komma åt webbappen från lokala platser via en Azure-Gateway, ett regionalt, interaktivt VNet eller ett globalt peer-baserat VNet är den privata slut punkten lösningen.  

Mer information finns i [tjänst slut punkter][serviceendpoint].

## <a name="conceptual-overview"></a>Begreppsmässig översikt

En privat slut punkt är ett särskilt nätverks gränssnitt (NIC) för din Azure-webbapp i ett undernät i din Virtual Network (VNet).
När du skapar en privat slut punkt för din webbapp ger den säker anslutning mellan klienter i ditt privata nätverk och din webbapp. Den privata slut punkten tilldelas en IP-adress från det virtuella nätverkets IP-adressintervall.
Anslutningen mellan den privata slut punkten och webb programmet använder en säker [privat länk][privatelink]. Privat slut punkt används endast för inkommande flöden till din webbapp. Utgående flöden använder inte den här privata slut punkten, men du kan mata in utgående flöden till nätverket i ett annat undernät med [funktionen för VNet-integrering][vnetintegrationfeature].

Under nätet där du ansluter den privata slut punkten kan ha andra resurser, du behöver inte ett dedikerat tomt undernät.
Du kan också distribuera den privata slut punkten i en annan region än webbappen. 

> [!Note]
>Funktionen för VNet-integrering kan inte använda samma undernät som privat slut punkt, detta är en begränsning för funktionen för VNet-integrering.

Från ett säkerhets perspektiv:

- När du aktiverar privata slut punkter till din webbapp inaktiverar du all offentlig åtkomst.
- Du kan aktivera flera privata slut punkter i andra virtuella nätverk och undernät, inklusive virtuella nätverk i andra regioner.
- IP-adressen för det privata slut punkts NÄTVERKSKORTet måste vara dynamisk, men den är densamma tills du tar bort den privata slut punkten.
- NÄTVERKSKORTet för den privata slut punkten kan inte ha en associerad NSG.
- Under nätet som är värd för den privata slut punkten kan ha en NSG associerad, men du måste inaktivera principerna för nätverks principer för den privata slut punkten: se [inaktivera nätverks principer för privata slut punkter][disablesecuritype]. Därför kan du inte filtrera efter NSG åtkomst till din privata slut punkt.
- När du aktiverar privat slut punkt till din webbapp utvärderas inte konfigurationen av [åtkomst begränsningar][accessrestrictions] för webbappen.
- Du kan eliminera risken för data exfiltrering från VNet genom att ta bort alla NSG-regler där målet är tagga Internet eller Azure-tjänster. När du distribuerar en privat slut punkt för en webbapp kan du bara komma åt just den här webbappen via den privata slut punkten. Om du har en annan webbapp måste du distribuera en annan dedikerad privat slut punkt för den här andra webbappen.

I webb-HTTP-loggfilerna för din webbapp hittar du klientens käll-IP. Den här funktionen implementeras med TCP-proxy-protokollet och vidarebefordrar klientens IP-egenskap till webbappen. Mer information finns i [Hämta anslutnings information med hjälp av TCP proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Global översikt över webb programs privata slut punkt](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

När du använder en privat slut punkt för webbappen måste den begärda URL: en matcha namnet på din webbapp. Som standard mywebappname.azurewebsites.net.

Som standard, utan privat slut punkt, är det offentliga namnet på din webbapp ett kanoniskt namn för klustret.
Namn matchningen är till exempel:

|Namn |Typ |Värde |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


När du distribuerar en privat slut punkt uppdaterar vi DNS-posten så att den pekar på det kanoniska namnet mywebapp.privatelink.azurewebsites.net.
Namn matchningen är till exempel:

|Namn |Typ |Värde |Markera om |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|< – den offentliga IP-adressen är inte din privata slut punkt, du får ett 403-fel|

Du måste konfigurera en privat DNS-server eller en Azure DNS privat zon för att testa att du kan ändra värd posten för test datorn.
Den DNS-zon som du behöver skapa är: **privatelink.azurewebsites.net**. Registrera posten för din webbapp med en A-post och den privata slut punktens IP-adress.
Namn matchningen är till exempel:

|Namn |Typ |Värde |Markera om |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|< – Azure skapar den här posten i Azures offentliga DNS för att peka app service till privatelink och det hanteras av oss|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|< – du hanterar den här posten i ditt DNS-system för att peka på din privata slut punkts IP-adress|

Efter den här DNS-konfigurationen kan du komma åt din webbapp privat med standard namnet mywebappname.azurewebsites.net. Du måste använda det här namnet eftersom standard certifikatet utfärdas för *. azurewebsites.net.


Om du behöver använda ett anpassat DNS-namn måste du lägga till det anpassade namnet i din webbapp. Det anpassade namnet måste verifieras som valfritt anpassat namn med hjälp av offentlig DNS-matchning. Mer information finns i [anpassad DNS-validering][dnsvalidation].

För kudu-konsolen, eller kudu REST API (distribution med Azure DevOpss lokala agenter till exempel), måste du skapa två poster i din Azure DNS privata zon eller på din anpassade DNS-server. 

| Namn | Typ | Värde |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | A | PrivateEndpointIP | 



## <a name="pricing"></a>Priser

Pris information finns i [priser för privata Azure-länkar][pricing].

## <a name="limitations"></a>Begränsningar

När du använder Azure Function i elastisk Premium-plan med privat slut punkt, för att köra eller köra funktionen i Azure-webbportalen, måste du ha direkt åtkomst till nätverket eller så får du ett HTTP 403-fel. Med andra ord måste webbläsaren kunna komma åt den privata slut punkten för att köra funktionen från Azure-webbportalen. 

Du kan ansluta upp till 100 privata slut punkter till en viss webbapp.

Fjärr fel söknings funktionen är inte tillgänglig när privat slut punkt har Aktiver ATS för webb programmet. Rekommendationen är att distribuera koden till en plats och fjärran sluten fel sökning där.

FTP-åtkomst tillhandahålls via den inkommande offentliga IP-adressen. Den privata slut punkten har inte stöd för FTP-åtkomst till webbappen.

Vi förbättrar den privata länk funktionen och den privata slut punkten regelbundet. Läs [den här artikeln][pllimitations] för uppdaterad information om begränsningar.

## <a name="next-steps"></a>Nästa steg

- Information om hur du distribuerar privat slut punkt för din webbapp via portalen finns i [så här ansluter du privat till en webbapp med portalen][howtoguide1]
- Om du vill distribuera en privat slut punkt för din webbapp med Azure CLI kan du läsa [så här ansluter du privat till en webbapp med Azure CLI][howtoguide2]
- Om du vill distribuera en privat slut punkt för din webbapp med hjälp av PowerShell, se [så här ansluter du privat till en webbapp med PowerShell][howtoguide3]
- Information om hur du distribuerar privat slut punkt för din webbapp med Azure-mall finns i [så här ansluter du privat till en webbapp med Azure-mall][howtoguide4]
- Slutpunkt-till-slutpunkt-exempel, hur du ansluter en frontend-webbapp till en säker server för webb program med VNet-injektering och privat slut punkt med ARM-mall finns i den här [snabb][howtoguide5] starten
- Slutpunkt-till-slutpunkt-exempel, hur du ansluter en frontend-webbapp till en skyddad Server dels webbapp med VNet-injektering och privat slut punkt med terraform finns i det här [exemplet][howtoguide6]


<!--Links-->
[serviceendpoint]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[privatelink]: ../../private-link/private-link-overview.md
[vnetintegrationfeature]: ../web-sites-integrate-with-vnet.md
[disablesecuritype]: ../../private-link/disable-private-endpoint-network-policy.md
[accessrestrictions]: ../app-service-ip-restrictions.md
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: ../app-service-web-tutorial-custom-domain.md
[pllimitations]: ../../private-link/private-endpoint-overview.md#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: ../../private-link/tutorial-private-endpoint-webapp-portal.md
[howtoguide2]: ../scripts/cli-deploy-privateendpoint.md
[howtoguide3]: ../scripts/powershell-deploy-private-endpoint.md
[howtoguide4]: ../scripts/template-deploy-private-endpoint.md
[howtoguide5]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection
[howtoguide6]: ../scripts/terraform-secure-backend-frontend.md
