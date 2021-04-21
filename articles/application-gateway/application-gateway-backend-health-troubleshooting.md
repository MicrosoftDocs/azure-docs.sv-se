---
title: Felsöka problem med hälsotillstånd för Azure Application Gateway
description: Beskriver hur du felsöker problem med hälsotillstånd för Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 8664f9327af37345c7104c65b2521212669ae806
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786334"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Felsöka problem med hälsotillstånd i serverdelen i Application Gateway
==================================================

<a name="overview"></a>Översikt
--------

Som standard Azure Application Gateway serverservrar för att kontrollera deras hälsostatus och för att kontrollera om de är redo att betjäna begäranden. Användare kan också skapa anpassade avsökningar för att nämna värdnamnet, sökvägen som ska avsökas och statuskoder som ska godkännas som Felfria. Om backend-servern inte svarar korrekt markerar Application Gateway servern som Inte feltillstånd och slutar vidarebefordra begäranden till servern. När servern har börjat svara Application Gateway vidarebefordrar begärandena igen.

### <a name="how-to-check-backend-health"></a>Så här kontrollerar du hälsotillståndet för backend

Om du vill kontrollera hälsotillståndet för din backend-pool kan du använda **sidan Backend Health** Azure Portal. Du kan också använda [Azure PowerShell,](/powershell/module/az.network/get-azapplicationgatewaybackendhealth) [CLI](/cli/azure/network/application-gateway#az_network_application_gateway_show_backend_health)eller [REST API](/rest/api/application-gateway/applicationgateways/backendhealth).

Statusen som hämtas av någon av dessa metoder kan vara något av följande:

- Felfri

- Ohälsosamt

- Okänt

Om serverserverns hälsostatus är Felfri innebär det att Application Gateway vidarebefordrar begärandena till den servern. Men om serverhälsan för alla servrar i en serverpool är Inte felbar eller okänd kan det uppstå problem när du försöker komma åt program. I den här artikeln beskrivs symptom, orsak och lösning för vart och ett av de fel som visas.

<a name="backend-health-status-unhealthy"></a>Hälsostatus för backend: Inte feltillstånd
-------------------------------

Om hälsostatusen för backend är Inte felskyddad liknar portalvyn följande skärmbild:

![Application Gateway backend-hälsa – Inte feltillstånd](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Eller om du använder en Azure PowerShell-, CLI- eller Azure REST API-fråga får du ett svar som liknar följande:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
När du får statusen Ej felande server för alla servrar i en serverpool vidarebefordras inte begäranden till servrarna och Application Gateway returnerar felet "502 Felaktig gateway" till den begärande klienten. Om du vill felsöka det här problemet **kontrollerar du** kolumnen Information på **fliken Backend Health.**

Meddelandet som visas i **kolumnen Information** ger mer detaljerad information om problemet och utifrån det kan du börja felsöka problemet.

> [!NOTE]
> Standardavsökningsbegäran skickas i formatet \<protocol\> ://127.0.0.1: \<port\> /. Till exempel för http://127.0.0.1:80 en HTTP-avsökning på port 80. Endast HTTP-statuskoder mellan 200 och 399 anses vara felfria. Protokollet och målporten ärvs från HTTP-inställningarna. Om du vill Application Gateway avsökning på ett annat protokoll, värdnamn eller sökväg och identifiera en annan statuskod som Felfri konfigurerar du en anpassad avsökning och associerar den med HTTP-inställningarna.

<a name="error-messages"></a>Felmeddelanden
------------------------
#### <a name="backend-server-timeout"></a>Tidsgräns för serverdel

**Meddelande:** Den tid det tar för backend att svara på Application Gateways hälsoavsökning är mer än \' tidsgränströskeln i avsökningsinställningen.

**Orsak:** När Application Gateway skickar en HTTP(S)-avsökningsbegäran till backend-servern väntar den på ett svar från backend-servern under en konfigurerad period. Om serverdelsservern inte svarar inom den konfigurerade perioden (tidsgränsvärdet) markeras den som Inte felig tills den börjar svara inom den konfigurerade tidsgränsperioden igen.

**Lösning:** Kontrollera varför serverdelsservern eller programmet inte svarar inom den konfigurerade tidsgränsen och kontrollera även programberoendena. Kontrollera till exempel om databasen har några problem som kan utlösa en fördröjning i svaret. Om du är medveten om programmets beteende och det bara bör svara efter tidsgränsvärdet ökar du tidsgränsvärdet från de anpassade avsökningsinställningarna. Du måste ha en anpassad avsökning för att ändra tidsgränsvärdet. Information om hur du konfigurerar en anpassad avsökning [finns på dokumentationssidan](./application-gateway-create-probe-portal.md).

Följ dessa steg om du vill öka timeout-värdet:

1.  Öppna serversidan direkt och kontrollera hur lång tid det tar för servern att svara på den sidan. Du kan använda val annat verktyg för att få åtkomst till serverservern, inklusive en webbläsare med hjälp av utvecklarverktyg.

1.  När du har räknat ut hur lång tid det tar för programmet att svara väljer du fliken **Hälsoavsökningar** och väljer sedan den avsökning som är associerad med HTTP-inställningarna.

1.  Ange ett timeout-värde som är större än programmets svarstid i sekunder.

1.  Spara de anpassade avsökningsinställningarna och kontrollera om hälsotillståndet för backend visas som Felfritt nu.

#### <a name="dns-resolution-error"></a>DNS-lösningsfel

**Meddelande:** Application Gateway kunde inte skapa en avsökning för den här backend- Detta inträffar vanligtvis när det fullständiga domännamnet (FQDN) för serverdelen inte har angetts på korrekt sätt. 

**Orsak:** Om serverpoolen är av typen IP-adress/FQDN eller App Service matchar Application Gateway IP-adressen för det FQDN som anges via Domain Name System (DNS) (anpassad eller Azure-standard) och försöker ansluta till servern på TCP-porten som anges i HTTP-inställningarna. Men om det här meddelandet visas föreslår det att Application Gateway inte kunde matcha IP-adressen för det angivna FQDN:et.

**Lösning:**

1.  Kontrollera att det FQDN som angetts i backend-poolen är korrekt och att det är en offentlig domän och försök sedan att matcha det från din lokala dator.

1.  Om du kan lösa IP-adressen kan det vara något fel med DNS-konfigurationen i det virtuella nätverket.

1.  Kontrollera om det virtuella nätverket är konfigurerat med en anpassad DNS-server. Om den är det kontrollerar du varför DNS-servern inte kan matchas mot IP-adressen för det angivna FQDN-namnet.

1.  Om du använder Azures standard-DNS kontrollerar du med domännamnsregistratorn om korrekt A-post- eller CNAME-postmappning har slutförts.

1.  Om domänen är privat eller intern kan du försöka matcha den från en virtuell dator i samma virtuella nätverk. Om du kan lösa det startar du Application Gateway och kontrollerar igen. Om du Application Gateway måste du stoppa [och starta med](/powershell/module/azurerm.network/stop-azurermapplicationgateway) hjälp [av](/powershell/module/azurerm.network/start-azurermapplicationgateway) PowerShell-kommandona som beskrivs i dessa länkade resurser.

#### <a name="tcp-connect-error"></a>TCP-anslutningsfel

**Meddelande:** Application Gateway kunde inte ansluta till backend-datorn.
Kontrollera att backend svarar på den port som används för avsökningen.
Kontrollera också om någon NSG/UDR/brandvägg blockerar åtkomst till IP-adressen och porten för den här backend-datorn

**Orsak:** Efter DNS-lösningsfasen Application Gateway att ansluta till backend-servern på TCP-porten som har konfigurerats i HTTP-inställningarna. Om Application Gateway inte kan upprätta en TCP-session på den angivna porten markeras avsökningen som Inte felskyddad med det här meddelandet.

**Lösning:** Följ dessa steg om du får det här felet:

1.  Kontrollera om du kan ansluta till backend-servern på den port som anges i HTTP-inställningarna med hjälp av en webbläsare eller PowerShell. Kör till exempel följande kommando: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Om den port som anges inte är den önskade porten anger du rätt portnummer för Application Gateway ansluta till backend-servern

1.  Om du inte kan ansluta på porten från den lokala datorn också gör du följande:

    a.  Kontrollera inställningarna för nätverkssäkerhetsgruppen (NSG) för backend-serverns nätverkskort och undernät och om inkommande anslutningar till den konfigurerade porten tillåts. Om de inte är det skapar du en ny regel för att tillåta anslutningarna. Information om hur du skapar NSG-regler [finns på dokumentationssidan](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules).

    b.  Kontrollera om NSG-inställningarna för Application Gateway undernät tillåter utgående offentlig och privat trafik, så att en anslutning kan upprättas. Mer information om hur du skapar NSG-regler finns på dokumentsidan i steg 3a.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Kontrollera inställningarna för användardefinierade vägar (UDR) för Application Gateway och backend-serverns undernät för eventuella routningsavvikelser. Kontrollera att UDR inte dirigerar trafiken från backend-undernätet. Du kan till exempel söka efter vägar till virtuella nätverksutrustning eller standardvägar som annonseras till Application Gateway-undernätet via Azure ExpressRoute och/eller VPN.

    d.  Om du vill kontrollera gällande vägar och regler för ett nätverkskort kan du använda följande PowerShell-kommandon:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Om du inte hittar några problem med NSG eller UDR kontrollerar du om det finns programrelaterade problem på serverservern som hindrar klienterna från att upprätta en TCP-session på de konfigurerade portarna. Några saker att kontrollera:

    a.  Öppna en kommandotolk (Win+R - \> cmd), ange `netstat` och välj Retur.

    b.  Kontrollera om servern lyssnar på den port som är konfigurerad. Exempel:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Om den inte lyssnar på den konfigurerade porten kontrollerar du inställningarna för webbservern. Exempel: webbplatsbindningar i IIS, serverblock i NGINX och virtuell värd i Apache.

    d.  Kontrollera inställningarna för operativsystemets brandvägg för att se till att inkommande trafik till porten tillåts.

#### <a name="http-status-code-mismatch"></a>Matchningsfel för HTTP-statuskod

**Meddelande:** Statuskoden för http-svaret \' för backend matchar inte avsökningsinställningen. Expected:{HTTPStatusCode0} Received:{HTTPStatusCode1}.

**Orsak:** När TCP-anslutningen har upprättats och en TLS-handskakning är klar (om TLS är aktiverat) skickar Application Gateway avsökningen som en HTTP GET-begäran till serverservern. Som tidigare beskrivits kommer standardavsökningen att vara \<protocol\> till ://127.0.0.1: /, och den betraktar svarsstatuskoder i \<port\> 200 till 399 som Felfri. Om servern returnerar någon annan statuskod markeras den som Inte feltillstånd med det här meddelandet.

**Lösning:** Beroende på serverserverns svarskod kan du göra följande. Några av de vanliga statuskoderna visas här:

| **Fel** | **Åtgärder** |
| --- | --- |
| Matchningsfel för avsökningsstatuskod: Mottagen 401 | Kontrollera om backend-servern kräver autentisering. Application Gateway avsökningar kan inte skicka autentiseringsuppgifter för autentisering. Antingen tillåter du HTTP 401 i en matchning av avsökningsstatuskod eller \" \" avsöker till en sökväg där servern inte kräver autentisering. |
| Matchningsfel för avsökningsstatuskod: Mottagen 403 | Åtkomst är förbjuden. Kontrollera om åtkomst till sökvägen tillåts på backend-servern. |
| Matchningsfel för avsökningsstatuskod: Mottagen 404 | Sidan hittades inte. Kontrollera om värdnamnets sökväg är tillgänglig på backend-servern. Ändra värdnamnet eller sökvägsparametern till ett tillgängligt värde. |
| Matchningsfel för avsökningsstatuskod: Tog emot 405 | Avsökningsbegäranden för Application Gateway använder HTTP GET-metoden. Kontrollera om servern tillåter den här metoden. |
| Matchningsfel för avsökningsstatuskod: 500 har tagits emot | Internt serverfel. Kontrollera serverdelens hälsotillstånd och om tjänsterna körs. |
| Matchningsfel för avsökningsstatuskod: Tog emot 503 | Tjänsten är inte tillgänglig. Kontrollera serverdelens hälsotillstånd och om tjänsterna körs. |

Eller om du tror att svaret är giltigt och du vill Application Gateway godkänna andra statuskoder som Felfri kan du skapa en anpassad avsökning. Den här metoden är användbar i situationer där serversidans webbplats behöver autentisering. Eftersom avsökningsbegäranden inte innehåller några användarautentiseringsuppgifter misslyckas de och en HTTP 401-statuskod returneras av backend-servern.

Följ dessa steg om du vill skapa en [anpassad avsökning.](./application-gateway-create-probe-portal.md)

#### <a name="http-response-body-mismatch"></a>Matchningsfel för HTTP-svarstext

**Meddelande:** Brödtexten i \' http-svaret för backend matchar inte avsökningsinställningen. Mottagen svarstext innehåller inte {string}.

**Orsak:** När du skapar en anpassad avsökning kan du markera en serverdelsserver som Felfri genom att matcha en sträng från svarstexten. Du kan till exempel konfigurera Application Gateway godkänna "obehörig" som en sträng som ska matchas. Om serversvaret för avsökningsbegäran innehåller strängen **obehörig** markeras den som Felfri. Annars markeras den som Inte feltillstånd med det här meddelandet.

**Lösning:** Följ dessa steg för att lösa problemet:

1.  Öppna serverdelen lokalt eller från en klientdator på avsökningssökvägen och kontrollera svarstexten.

1.  Kontrollera att svarstexten i Application Gateway anpassade avsökningskonfigurationen matchar det som har konfigurerats.

1.  Om de inte matchar ändrar du avsökningskonfigurationen så att har rätt strängvärde att acceptera.

Läs mer om [hur Application Gateway avsökningsmatchning.](./application-gateway-probe-overview.md#probe-matching)

>[!NOTE]
> Om du vill veta mer om SNI-beteende och skillnader mellan V1- och v2-SKU:n för alla TLS-relaterade felmeddelanden kan du gå till [översiktssidan för TLS.](ssl-overview.md)


#### <a name="backend-server-certificate-invalid-ca"></a>Ogiltig certifikatutfärdare för backend-server

**Meddelande:** Servercertifikatet som används av serverservern är inte signerat av en välkänd certifikatutfärdare (CA). Tillåt serverservern på Application Gateway genom att ladda upp rotcertifikatet för servercertifikatet som används av serverservern.

**Orsak:** Ssl från end-to-end Application Gateway v2 kräver att serverserverns certifikat verifieras för att servern ska anses vara felfri.
För att ett TLS-/SSL-certifikat ska vara betrott måste certifikatet på backend-servern utfärdas av en certifikatutfärdare som ingår i det betrodda arkivet Application Gateway. Om certifikatet inte har utfärdats av en betrodd certifikatutfärdare (till exempel om ett själv signerat certifikat har använts) bör användarna ladda upp utfärdarens certifikat till Application Gateway.

**Lösning:** Följ dessa steg för att exportera och ladda upp det betrodda rotcertifikatet till Application Gateway. (De här stegen är för Windows-klienter.)

1.  Logga in på datorn där programmet finns.

1.  Välj Win+R eller högerklicka på **knappen Start** och välj sedan **Kör.**

1.  Ange `certmgr.msc` och välj Retur. Du kan också söka efter Certifikathanteraren på **Start-menyn.**

1.  Leta upp certifikatet, vanligtvis `\Certificates - Current User\\Personal\\Certificates\` i , och öppna det.

1.  Välj rotcertifikatet och välj sedan **Visa certifikat.**

1.  I Certifikategenskaper väljer du **fliken** Information.

1.  På fliken **Information** väljer du **alternativet** Kopiera till fil och sparar filen i base-64-kodad X.509 (. CER)-format.

1.  Öppna sidan Application Gateway **HTTP-inställningar** i Azure Portal.

1. Öppna HTTP-inställningarna, välj **Lägg till** certifikat och leta upp den certifikatfil som du nyss sparade.

1. Välj **Spara** för att spara HTTP-inställningarna.

Du kan också exportera rotcertifikatet från en klientdator genom att direkt komma åt servern (kringgå Application Gateway) via webbläsaren och exportera rotcertifikatet från webbläsaren.

Mer information om hur du extraherar och laddar upp betrodda rotcertifikat i Application Gateway finns i [Exportera betrott rotcertifikat (för v2 SKU).](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)

#### <a name="trusted-root-certificate-mismatch"></a>Matchningsfel för betrott rotcertifikat

**Meddelande:** Rotcertifikatet för servercertifikatet som används av serverservern matchar inte det betrodda rotcertifikatet som lagts till i programgatewayen. Se till att du lägger till rätt rotcertifikat för att tillåta att server slutet listas.

**Orsak:** Ssl från Application Gateway v2 kräver att serverserverns certifikat verifieras för att servern ska anses vara felfri.
För att ett TLS-/SSL-certifikat ska vara betrott måste servercertifikatet utfärdas av en certifikatutfärdare som ingår i det betrodda arkivet för Application Gateway. Om certifikatet inte har utfärdats av en betrodd certifikatutfärdare (till exempel om ett själv signerat certifikat har använts) bör användarna ladda upp utfärdarens certifikat till Application Gateway.

Certifikatet som har laddats upp till Application Gateway HTTP-inställningarna måste matcha rotcertifikatet för servercertifikatet.

**Lösning:** Om du får det här felmeddelandet finns det ett matchningsfel mellan certifikatet som har laddats upp till Application Gateway och det som laddades upp till backend-servern.

Följ steg 1–11 i föregående metod för att ladda upp rätt betrott rotcertifikat till Application Gateway.

Mer information om hur du extraherar och laddar upp betrodda rotcertifikat i Application Gateway finns i Exportera betrott rotcertifikat [(för v2 SKU).](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)
> [!NOTE]
> Det här felet kan också inträffa om backend-servern inte utbyter hela certifikatkedjan, inklusive Root > Intermediate (om tillämpligt) > Leaf under TLS-handskakningen. Du kan kontrollera detta genom att använda OpenSSL-kommandon från valfri klient och ansluta till serversidan med hjälp av de konfigurerade inställningarna i den Application Gateway avsökningen.

Exempel:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Om utdata inte visar hela kedjan för det certifikat som returneras exporterar du certifikatet igen med hela kedjan, inklusive rotcertifikatet. Konfigurera certifikatet på serverservern. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Ogiltigt eget namn (CN) för servercertifikat

**Meddelande:** Servercertifikatet (CN) eget namn matchar inte avsökningens värdhuvud.

**Orsak:** Application Gateway kontrollerar om värdnamnet som anges i HTTP-inställningarna för serverplatsen matchar CN som presenteras av backend-serverns TLS/SSL-certifikat. Detta är Standard_v2 och WAF_v2 SKU (V2). Standard- och WAF SKU:erna (v1) Servernamnindikator (SNI) anges som FQDN i adressen för backend-poolen. Mer information om SNI-beteende och skillnader mellan v1 och v2 SKU finns i Översikt över [TLS-avslutning](ssl-overview.md)och TLS från Application Gateway .

Om det finns en standardavsökning i v2-SKU:n (ingen anpassad avsökning har konfigurerats och associerats) anges SNI från det värdnamn som anges i HTTP-inställningarna. Eller om "Välj värdnamn från backend-adress" anges i HTTP-inställningarna, där backend-adresspoolen innehåller ett giltigt FQDN, tillämpas den här inställningen.

Om det finns en anpassad avsökning som är associerad med HTTP-inställningarna anges SNI från det värdnamn som anges i den anpassade avsökningskonfigurationen. Om Välj värdnamn från HTTP-inställningar för **backend** har valts i den anpassade avsökningen, anges SNI från det värdnamn som anges i HTTP-inställningarna.

Om **Välj värdnamn från backend-adress** har angetts i HTTP-inställningarna måste backend-adresspoolen innehålla ett giltigt FQDN.

Om du får det här felmeddelandet matchar inte CN för servercertifikatet det värdnamn som konfigurerats i den anpassade avsökningen eller HTTP-inställningarna (om Välj värdnamn från **HTTP-inställningarna** för serverdatorn har valts). Om du använder en standardavsökning anges värdnamnet som **127.0.0.1**. Om det inte är ett önskat värde bör du skapa en anpassad avsökning och associera den med HTTP-inställningarna.

**Lösning:**

Följ dessa anvisningar för att lösa problemet.

För Windows:

1.  Logga in på datorn där programmet finns.

1.  Välj Win+R eller högerklicka på **knappen Start** och välj **Kör.**

1.  Ange **certmgr.msc** och välj Retur. Du kan också söka efter Certifikathanteraren på **Start-menyn.**

1.  Leta upp certifikatet (vanligtvis `\Certificates - Current User\\Personal\\Certificates` i ) och öppna certifikatet.

1.  På fliken **Information** kontrollerar du certifikatets **ämne.**

1.  Kontrollera certifikatets CN från informationen och ange samma i värdnamnsfältet för den anpassade avsökningen eller i HTTP-inställningarna (om Välj värdnamn från **HTTP-inställningar** för serverområdet har valts). Om det inte är det önskade värdnamnet för din webbplats måste du skaffa ett certifikat för domänen eller ange rätt värdnamn i konfigurationen för den anpassade avsökningen eller HTTP-inställningen.

För Linux med OpenSSL:

1.  Kör det här kommandot i OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Från egenskaperna som visas hittar du certifikatets CN och anger samma i värdnamnsfältet i http-inställningarna. Om det inte är det önskade värdnamnet för din webbplats måste du skaffa ett certifikat för domänen eller ange rätt värdnamn i konfigurationen för den anpassade avsökningen eller HTTP-inställningen.

#### <a name="backend-certificate-is-invalid"></a>Servercertifikatet är ogiltigt

**Meddelande:** Servercertifikatet är ogiltigt. Det aktuella datumet ligger inte inom \" giltigt \" \" från- och \" giltigt datumintervall på certifikatet.

**Orsak:** Varje certifikat levereras med ett giltighetsintervall och HTTPS-anslutningen är inte säker om inte serverns TLS/SSL-certifikat är giltigt. Aktuella data måste vara inom giltiga **från och** giltiga **till** intervallet. Om det inte är det betraktas certifikatet som ogiltigt och det skapar ett säkerhetsproblem där Application Gateway markerar serverservern som Inte felskyddad.

**Lösning:** Om ditt TLS/SSL-certifikat har upphört att gälla förnyar du certifikatet med leverantören och uppdaterar serverinställningarna med det nya certifikatet. Om det är ett själv signerat certifikat måste du generera ett giltigt certifikat och ladda upp rotcertifikatet till Application Gateway HTTP-inställningarna. Det gör du genom att följa dessa steg:

1.  Öppna ditt Application Gateway HTTP-inställningar i portalen.

1.  Välj den inställning där certifikatet har upphört att gälla, välj **Lägg till** certifikat och öppna den nya certifikatfilen.

1.  Ta bort det gamla certifikatet med **hjälp av ikonen** Ta bort bredvid certifikatet och välj sedan **Spara**.

#### <a name="certificate-verification-failed"></a>Certifikatverifieringen misslyckades

**Meddelande:** Det gick inte att verifiera servercertifikatet. Om du vill ta reda på orsaken kontrollerar du OpenSSL-diagnostiken för det meddelande som är associerat med felkoden {errorCode}

**Orsak:** Det här felet uppstår Application Gateway du inte kan verifiera certifikatets giltighet.

**Lösning:** Lös problemet genom att kontrollera att certifikatet på servern har skapats korrekt. Du kan till exempel använda [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) för att verifiera certifikatet och dess egenskaper och sedan försöka ladda upp certifikatet på nytt till Application Gateway HTTP-inställningarna.

<a name="backend-health-status-unknown"></a>Hälsostatus för backend: okänd
-------------------------------
Om hälsotillståndet för backend visas som Okänt liknar portalvyn följande skärmbild:

![Application Gateway backend-hälsa – Okänd](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Det här beteendet kan inträffa av en eller flera av följande orsaker:

1.  NSG:n i Application Gateway-undernätet blockerar inkommande åtkomst till portarna 65503-65534 (v1 SKU) eller 65200-65535 (v2 SKU) från "Internet".
1.  UDR på Application Gateway-undernätet är inställd på standardvägen (0.0.0.0/0) och nästa hopp har inte angetts som "Internet".
1.  Standardvägen annonseras av en ExpressRoute-/VPN-anslutning till ett virtuellt nätverk via BGP.
1.  Den anpassade DNS-servern är konfigurerad i ett virtuellt nätverk som inte kan matcha offentliga domännamn.
1.  Application Gateway är i ett feltillstånd.

**Lösning:**

1.  Kontrollera om din NSG blockerar åtkomst till portarna 65503-65534 (v1 SKU) eller 65200-65535 (v2 SKU) från **Internet:**

    a.  På Application Gateway **fliken** Översikt väljer du **länken Virtual Network/undernät.**

    b.  På fliken **Undernät i** det virtuella nätverket väljer du det undernät där Application Gateway har distribuerats.

    c.  Kontrollera om någon NSG har konfigurerats.

    d.  Om en NSG har konfigurerats söker du efter den NSG-resursen på **fliken Sök** eller under **Alla resurser**.

    e.  I avsnittet Regler för **inkommande** trafik lägger du till en regel för inkommande trafik som tillåter målportintervallet 65503-65534 för  v1-SKU eller 65200-65535 v2-SKU med källuppsättningen Any eller **Internet**. 

    f.  Välj **Spara** och kontrollera att du kan visa backend som Felfri. Du kan också göra det via [PowerShell/CLI](../virtual-network/manage-network-security-group.md).

1.  Kontrollera om din UDR har en standardväg (0.0.0.0/0) med nästa hopp inte inställt som **Internet:**
    
    a.  Följ steg 1a och 1b för att fastställa undernätet.

    b.  Kontrollera om det finns någon konfigurerad UDR. Om det finns det söker du efter resursen i sökfältet eller under **Alla resurser.**

    c.  Kontrollera om det finns några standardvägar (0.0.0.0/0) med nästa hopp inte inställt på **Internet**. Om inställningen antingen är virtuell installation **eller Virtual Network Gateway** måste du se till att den virtuella installationen eller den lokala enheten kan dirigera paketet korrekt tillbaka till Internetmålet utan att ändra paketet. 

    d.  Annars ändrar du nästa hopp till **Internet,** väljer **Spara** och verifierar hälsotillståndet för backend.

1.  Standardväg som annonseras av ExpressRoute/VPN-anslutningen till det virtuella nätverket via BGP:

    a.  Om du har en ExpressRoute-/VPN-anslutning till det virtuella nätverket via BGP, och om du annonserar en standardväg, måste du se till att paketet dirigeras tillbaka till Internetmålet utan att ändra det. Du kan kontrollera detta med hjälp **av alternativet Felsök** anslutning i Application Gateway portalen.

    b.  Välj målet manuellt som valfri Internet-dirigerbar IP-adress som 1.1.1.1. Ange målporten som vad som helst och verifiera anslutningen.

    c.  Om nästa hopp är virtuell nätverksgateway kan det finnas en standardväg som annonseras via ExpressRoute eller VPN.

1.  Om det finns en anpassad DNS-server konfigurerad i det virtuella nätverket kontrollerar du att servern (eller servrarna) kan matcha offentliga domäner. Offentlig domännamnsmatchning kan krävas i scenarier där Application Gateway måste kontakta externa domäner som OCSP-servrar eller för att kontrollera certifikatets återkallningsstatus.

1.  Om du Application Gateway felfri och körs går du **till Resource Health** i portalen och kontrollerar att tillståndet är **Felfritt.** Kontakta supporten **om du ser tillståndet** Inte feltillstånd eller [Degraderat.](https://azure.microsoft.com/support/options/) 

<a name="next-steps"></a>Nästa steg
----------

Läs mer om [Application Gateway diagnostik och loggning.](./application-gateway-diagnostics.md)
