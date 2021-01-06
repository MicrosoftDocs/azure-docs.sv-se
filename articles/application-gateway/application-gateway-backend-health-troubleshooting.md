---
title: Felsök problem med Server delens hälsa i Azure Application Gateway
description: Beskriver hur du felsöker Server dels hälso problem för Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 05df2144b892aed764f9606fb19bd6a3242b97f3
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934908"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Felsök problem med Server delens hälsa i Application Gateway
==================================================

<a name="overview"></a>Översikt
--------

Azure Application Gateway avsöker som standard server dels servrar för att kontrol lera sin hälso status och kontrol lera om de är redo att betjäna begär Anden. Användare kan också skapa anpassade avsökningar för att nämna värd namnet, sökvägen som ska avsökas och status koderna ska godkännas som felfria. I varje fall, om backend-servern inte svarar korrekt, Application Gateway markerar servern som ohälsosam och slutar vidarebefordra begär anden till servern. När servern har börjat svara korrekt fortsätter Application Gateway att vidarebefordra förfrågningarna.

> [!NOTE]
> Den här artikeln innehåller referenser till termen *vitlista*, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

### <a name="how-to-check-backend-health"></a>Så här kontrollerar du Server dels hälsa

Om du vill kontrol lera hälso tillståndet för din backend-pool kan du använda sidan för **hälso tillstånds** sidan på Azure Portal. Du kan också använda [Azure PowerShell](/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), [CLI](/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)eller [REST API](/rest/api/application-gateway/applicationgateways/backendhealth).

Statusen som hämtas av någon av dessa metoder kan vara något av följande:

- Felfri

- Ohälsosamt

- Okänt

Om Server delens hälso status för en server är felfri, innebär det att Application Gateway vidarebefordrar begär anden till den servern. Men om Server delens hälsa för alla servrar i en backend-pool inte är felfri eller okänd kan du stöta på problem när du försöker få åtkomst till program. I den här artikeln beskrivs symptom, orsak och lösningar för varje fel som visas.

<a name="backend-health-status-unhealthy"></a>Server dels hälso status: ej felfri
-------------------------------

Om Server delens hälso tillstånd inte är felfritt ser Portal vyn ut ungefär som på följande skärm bild:

![Application Gateway server dels hälsa – ej felfri](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Om du använder en Azure PowerShell-, CLI-eller Azure REST API-fråga får du ett svar som liknar följande:
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
När du har fått en felaktig backend-server status för alla servrar i en backend-pool, vidarebefordrar inte begär anden till servrarna och Application Gateway returnerar fel meddelandet "502 Felaktig gateway" till den begär ande klienten. Du kan felsöka det här problemet genom att kontrol lera kolumnen **information** på fliken **Server dels hälsa** .

Det meddelande som visas i kolumnen **information** innehåller mer detaljerade insikter om problemet och utifrån dessa kan du börja felsöka problemet.

> [!NOTE]
> Standard avsöknings förfrågan skickas i formatet \<protocol\> ://127.0.0.1: \<port\> /. Till exempel http://127.0.0.1:80 för en http-avsökning på port 80. Endast HTTP-statuskod på 200 till 399 betraktas som felfria. Protokoll-och mål porten ärvs från HTTP-inställningarna. Om du vill att Application Gateway ska avsöka på ett annat protokoll, värd namn eller sökväg och identifiera en annan status kod som felfri, konfigurerar du en anpassad avsökning och kopplar den till HTTP-inställningarna.

<a name="error-messages"></a>Felmeddelanden
------------------------
#### <a name="backend-server-timeout"></a>Timeout för backend-server

**Meddelande:** Tiden det tar för Server delen att svara på Application Gateway \' s hälso avsökning är över tids gränsen för avsöknings inställningen.

**Orsak:** När Application Gateway skickar en HTTP (S)-avsöknings förfrågan till backend-servern, väntar den på ett svar från backend-servern för en konfigurerad period. Om backend-servern inte svarar inom den konfigurerade perioden (timeout-värdet), markeras den som ohälsosam tills den börjar svara inom den angivna tids perioden igen.

**Lösning:** Kontrol lera varför backend-servern eller programmet inte svarar inom den angivna tids gränsen och kontrol lera även program beroenden. Kontrol lera till exempel om databasen har problem som kan utlösa en fördröjning i svaret. Om du är medveten om programmets beteende och det bara ska svara efter timeout-värdet, ökar du timeout-värdet från inställningarna för anpassad avsökning. Du måste ha en anpassad avsökning för att ändra timeout-värdet. Information om hur du konfigurerar en anpassad avsökning [finns på dokumentations sidan](./application-gateway-create-probe-portal.md).

Följ dessa steg om du vill öka timeout-värdet:

1.  Få åtkomst till backend-servern direkt och kontrol lera hur lång tid det tar för servern att svara på den sidan. Du kan använda valfritt verktyg för att få åtkomst till backend-servern, inklusive en webbläsare som använder utvecklarverktyg.

1.  När du har tagit reda på den tid det tar för programmet att svara väljer du fliken **hälso avsökningar** och väljer sedan den avsökning som är kopplad till dina http-inställningar.

1.  Ange ett tids gräns värde som är större än programmets svars tid, i sekunder.

1.  Spara inställningarna för anpassad avsökning och kontrol lera om Server dels hälso tillståndet visas som felfri nu.

#### <a name="dns-resolution-error"></a>DNS-matchnings fel

**Meddelande:** Application Gateway gick inte att skapa en avsökning för den här server delen. Detta inträffar vanligtvis när det fullständiga domännamnet (FQDN) för serverdelen inte har angetts på korrekt sätt. 

**Orsak:** Om backend-poolen är av typen IP-adress/FQDN eller App Service, matchar Application Gateway IP-adressen för FQDN som anges via Domain Name System (DNS) (anpassat eller Azure standard) och försöker ansluta till servern på TCP-porten som anges i HTTP-inställningarna. Men om det här meddelandet visas föreslår det att Application Gateway inte kunde matcha IP-adressen för det FQDN som angetts.

**Lösning:**

1.  Kontrol lera att det fullständiga domän namnet som angavs i backend-poolen är korrekt och att det är en offentlig domän och försök sedan att lösa den från den lokala datorn.

1.  Om du kan matcha IP-adressen kan det vara något fel med DNS-konfigurationen i det virtuella nätverket.

1.  Kontrol lera om det virtuella nätverket är konfigurerat med en anpassad DNS-server. Om det är det kontrollerar du DNS-servern om varför den inte kan matcha IP-adressen för det angivna fullständiga domän namnet.

1.  Om du använder Azure standard DNS, kan du kontrol lera med domän namnet registrator om rätt A-post eller CNAME-Postmappning har slutförts.

1.  Om domänen är privat eller intern försöker du lösa den från en virtuell dator i samma virtuella nätverk. Om du kan lösa det startar du om Application Gateway och kontrollerar igen. Om du vill starta om Application Gateway måste du [stoppa](/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) och [Starta](/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) med hjälp av PowerShell-kommandona som beskrivs i dessa länkade resurser.

#### <a name="tcp-connect-error"></a>TCP Connect-fel

**Meddelande:** Application Gateway gick inte att ansluta till Server delen.
Kontrol lera att Server delen svarar på den port som används för avsökningen.
Kontrol lera också om någon NSG/UDR/brand vägg blockerar åtkomsten till IP-adressen och porten för den här server delen

**Orsak:** Efter DNS-matchningen försöker Application Gateway ansluta till backend-servern på TCP-porten som har kon figurer ATS i HTTP-inställningarna. Om Application Gateway inte kan upprätta en TCP-session på den angivna porten markeras avsökningen som ohälsosam med det här meddelandet.

**Lösning:** Följ dessa steg om du får det här felet:

1.  Kontrol lera om du kan ansluta till backend-servern på den port som anges i HTTP-inställningarna med hjälp av en webbläsare eller PowerShell. Kör till exempel följande kommando: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Om den port som anges inte är den önskade porten anger du rätt port nummer för Application Gateway för att ansluta till backend-servern

1.  Om du inte kan ansluta till porten från den lokala datorn kan du också:

    a.  Kontrol lera inställningarna för nätverks säkerhets gruppen (NSG) för backend-serverns nätverkskort och undernät och om inkommande anslutningar till den konfigurerade porten är tillåtna. Om de inte gör det skapar du en ny regel som tillåter anslutningarna. Information om hur du skapar NSG-regler [finns i dokumentations sidan](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules).

    b.  Kontrol lera om NSG-inställningarna för Application Gateway under nätet tillåter utgående offentlig och privat trafik, så att en anslutning kan göras. Kontrol lera dokument sidan som anges i Steg 3a för att lära dig mer om hur du skapar NSG-regler.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Kontrol lera inställningarna för användardefinierade vägar (UDR) för Application Gateway och backend-serverns undernät för eventuella väg avvikelser. Kontrol lera att UDR inte dirigerar trafiken från backend-undernätet. Sök till exempel efter vägar till virtuella nätverks enheter eller standard vägar som annonseras till Application Gateway under nätet via Azure ExpressRoute och/eller VPN.

    d.  Om du vill kontrol lera de effektiva vägarna och reglerna för ett nätverkskort kan du använda följande PowerShell-kommandon:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Om du inte hittar några problem med NSG eller UDR kontrollerar du Server dels servern för programrelaterade problem som hindrar klienter från att upprätta en TCP-session på de konfigurerade portarna. Några saker att kontrol lera:

    a.  Öppna en kommando tolk (Win + R- \> cmd), ange `netstat` och välj RETUR.

    b.  Kontrol lera om servern lyssnar på den port som har kon figurer ATS. Ett exempel:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Om den inte lyssnar på den konfigurerade porten kontrollerar du inställningarna för webb servern. Exempel: webbplats bindningar i IIS, Server block i NGINX och virtuell värd i Apache.

    d.  Kontrol lera inställningarna för operativ system brand väggen för att se till att inkommande trafik till porten är tillåten.

#### <a name="http-status-code-mismatch"></a>Felaktig HTTP-statuskod

**Meddelande:** Status koden för Server delens \' HTTP-svar matchade inte avsöknings inställningen. Förväntades: {HTTPStatusCode0} togs emot: {HTTPStatusCode1}.

**Orsak:** När TCP-anslutningen har upprättats och en TLS-handskakning görs (om TLS är aktiverat) skickar Application Gateway avsökningen som en HTTP GET-begäran till backend-servern. Som tidigare beskrivits är standard avsökningen till \<protocol\> ://127.0.0.1: \<port\> /, och den tar hänsyn till svars status koder i Rage 200 till 399 som felfri. Om servern returnerar någon annan status kod så markeras den som ohälsosam med det här meddelandet.

**Lösning:** Beroende på backend-serverns svarskod kan du utföra följande steg. Några av de vanliga status koderna visas här:

| **Fel** | **Åtgärder** |
| --- | --- |
| Fel kod för avsöknings status kod: mottaget 401 | Kontrol lera om backend-servern kräver autentisering. Application Gateway avsökningar kan inte skicka autentiseringsuppgifter för autentisering. Antingen tillåter \" HTTP 401 \" i en avsöknings status kod matchning eller avsökning till en sökväg där servern inte kräver autentisering. | |
| Fel kod för avsöknings status kod: mottaget 403 | Åtkomst förbjuden. Kontrol lera om åtkomst till sökvägen tillåts på backend-servern. | |
| Fel kod för avsöknings status kod: mottaget 404 | Sidan hittades inte. Kontrol lera om värd namns Sök vägen är tillgänglig på backend-servern. Ändra värd namnet eller Sök vägs parametern till ett tillgängligt värde. | |
| Fel kod för avsöknings status kod: mottaget 405 | Avsöknings begär Anden för Application Gateway använder HTTP GET-metoden. Kontrol lera om servern tillåter den här metoden. | |
| Fel kod för avsöknings status kod: mottaget 500 | Internt serverfel. Kontrollera serverdelens hälsotillstånd och om tjänsterna körs. | |
| Fel kod för avsöknings status kod: mottaget 503 | Tjänsten är inte tillgänglig. Kontrollera serverdelens hälsotillstånd och om tjänsterna körs. | |

Eller också kan du skapa en anpassad avsökning om du tror att svaret är giltigt och du vill Application Gateway acceptera andra status koder som felfria. Den här metoden är användbar i situationer där Server dels webbplatsen behöver autentisering. Eftersom avsöknings begär Anden inte har några användarautentiseringsuppgifter, kommer de inte att fungera, och en HTTP 401-status kod returneras av backend-servern.

Följ [dessa steg](./application-gateway-create-probe-portal.md)om du vill skapa en anpassad avsökning.

#### <a name="http-response-body-mismatch"></a>Felaktigt innehåll i HTTP-svar

**Meddelande:** Innehållet i Server delens \' HTTP-svar matchade inte avsöknings inställningen. Mottagen svars texten innehåller inte {String}.

**Orsak:** När du skapar en anpassad avsökning har du ett alternativ för att markera en backend-server som felfri genom att matcha en sträng från svars texten. Du kan till exempel konfigurera Application Gateway att godkänna "obehörig" som en sträng som ska matchas. Om Server delens svar för avsöknings förfrågan innehåller strängen **obehörig**, så markeras den som felfri. Annars kommer den att markeras som ohälsosam med det här meddelandet.

**Lösning:** Följ dessa steg för att lösa problemet:

1.  Få åtkomst till backend-servern lokalt eller från en klient dator på avsöknings Sök vägen och kontrol lera svars texten.

1.  Kontrol lera att svars texten i Application Gateway anpassad avsöknings konfiguration matchar vad som är konfigurerat.

1.  Om de inte matchar ändrar du avsöknings konfigurationen så att har rätt sträng värde att acceptera.

Läs mer om [matchning av Application Gateway avsökning](./application-gateway-probe-overview.md#probe-matching).

>[!NOTE]
> Information om SNI beteende och skillnader mellan v1-och v2-SKU finns på sidan [TLS-översikt](ssl-overview.md) för alla TLS-relaterade fel meddelanden.


#### <a name="backend-server-certificate-invalid-ca"></a>Backend-servercertifikat ogiltigt CA

**Meddelande:** Server certifikatet som används av Server delen är inte signerat av en välkänd certifikat utfärdare (CA). Tillåt Server delen på Application Gateway genom att ladda upp rot certifikatet för Server certifikatet som används av Server delen.

**Orsak:** End-to-end-SSL med Application Gateway v2 kräver att backend-serverns certifikat verifieras för att anse att servern är felfri.
För att ett TLS/SSL-certifikat ska vara betrott måste certifikatet på backend-servern utfärdas av en certifikat utfärdare som ingår i det betrodda lagrings lagret för Application Gateway. Om certifikatet inte har utfärdats av en betrodd certifikat utfärdare (till exempel om ett självsignerat certifikat användes) bör användare ladda upp utfärdarens certifikat till Application Gateway.

**Lösning:** Följ dessa steg om du vill exportera och ladda upp det betrodda rot certifikatet till Application Gateway. (De här stegen gäller för Windows-klienter.)

1.  Logga in på den dator där ditt program finns.

1.  Välj Win + R eller högerklicka på knappen **Start** och välj sedan **Kör**.

1.  Ange `certmgr.msc` och välj RETUR. Du kan också söka efter Certificate Manager på **Start** -menyn.

1.  Leta upp certifikatet, vanligt vis i `\Certificates - Current User\\Personal\\Certificates\` , och öppna det.

1.  Välj rot certifikat och välj sedan **Visa certifikat**.

1.  I certifikat egenskaperna väljer du fliken **information** .

1.  På fliken **information** väljer du alternativet **Kopiera till fil** och sparar filen i Base-64-kodad X. 509 (. CER-format.

1.  Öppna sidan Application Gateway HTTP- **Inställningar** i Azure Portal.

1. Öppna HTTP-inställningarna, Välj **Lägg till certifikat** och leta upp den certifikat fil som du nyss sparade.

1. Välj **Spara** för att spara http-inställningarna.

Alternativt kan du exportera rot certifikatet från en klient dator genom att direkt komma åt servern (kringgå Application Gateway) via webbläsare och exportera rot certifikatet från webbläsaren.

Mer information om hur du extraherar och laddar upp betrodda rot certifikat i Application Gateway finns [Exportera ett betrott rot certifikat (för v2-SKU)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Matchnings fel för betrodda rot certifikat

**Meddelande:** Rot certifikatet för Server certifikatet som används av Server delen stämmer inte överens med det betrodda rot certifikat som har lagts till i Application Gateway. Se till att du lägger till rätt rot certifikat för att vitlista Server delen

**Orsak:** End-to-end-SSL med Application Gateway v2 kräver att backend-serverns certifikat verifieras för att anse att servern är felfri.
Om ett TLS/SSL-certifikat ska vara betrott måste Server del certifikatet utfärdas av en certifikat utfärdare som ingår i det betrodda lagrings lagret för Application Gateway. Om certifikatet inte utfärdats av en betrodd certifikat utfärdare (till exempel ett självsignerat certifikat användes), ska användarna ladda upp utfärdarens certifikat för att Application Gateway.

Certifikatet som har laddats upp till Application Gateway HTTP-inställningarna måste matcha Server certifikatets rot certifikat.

**Lösning:** Om du får det här fel meddelandet uppstår ett matchnings fel mellan det certifikat som har laddats upp till Application Gateway och det som överfördes till backend-servern.

Följ steg 1-11 i föregående metod för att ladda upp rätt betrott rot certifikat till Application Gateway.

Mer information om hur du extraherar och laddar upp betrodda rot certifikat i Application Gateway finns [Exportera ett betrott rot certifikat (för v2-SKU)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Det här felet kan också inträffa om backend-servern inte utbyter hela kedjan av certifikatet, inklusive roten > mellanliggande (om tillämpligt) > löv under TLS-handskakningen. För att verifiera kan du använda OpenSSL-kommandon från vilken klient som helst och ansluta till backend-servern med hjälp av de konfigurerade inställningarna i Application Gateway avsökningen.

Ett exempel:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Om utdata inte visar den fullständiga kedjan av det certifikat som returneras, exportera certifikatet igen med den fullständiga kedjan, inklusive rot certifikatet. Konfigurera certifikatet på backend-servern. 

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

#### <a name="backend-certificate-invalid-common-name-cn"></a>Backend-certifikatets ogiltiga nätverks namn (CN)

**Meddelande:** Server dels certifikatets eget namn (CN) matchar inte avsökningens värd huvud.

**Orsak:** Application Gateway kontrollerar om värd namnet som anges i Server delens HTTP-inställningar matchar det CN som presenteras av backend-serverns TLS/SSL-certifikat. Detta är Standard_v2 och WAF_v2 SKU-beteende (v2). Standard-och WAF SKU: n (v1) Servernamnindikator (SNI) anges som FQDN i Server delens adresspool. Mer information om SNI-beteende och skillnader mellan v1-och v2-SKU finns i [Översikt över TLS-terminering och slut punkt till slut punkt för TLS med Application Gateway](ssl-overview.md).

I v2-SKU: n, om det finns en standard avsökning (ingen anpassad avsökning har kon figurer ATS och associerats), kommer SNI att ställas in från det värdnamn som anges i HTTP-inställningarna. Eller, om "Välj värdnamn från Server dels adress" anges i HTTP-inställningarna, där backend-adresspoolen innehåller ett giltigt fullständigt domän namn, tillämpas den här inställningen.

Om det finns en anpassad avsökning kopplad till HTTP-inställningarna kommer SNI att ställas in från det värdnamn som anges i den anpassade avsöknings konfigurationen. Eller, om **Välj värd namn från Server delens HTTP-inställningar** har valts i den anpassade avsökningen, kommer SNI att ställas in från det värdnamn som anges i http-inställningarna.

Om **Välj värd namn från Server dels adress** har angetts i http-inställningarna måste backend-adresspoolen innehålla ett giltigt fullständigt domän namn.

Om du får det här fel meddelandet matchar inte CN för Server dels certifikatet det värdnamn som kon figurer ATS i den anpassade avsökningen eller HTTP-inställningarna (om **Välj värd namn från Server dels HTTP-inställningar** är markerat). Om du använder en standard avsökning får värd namnet värdet **127.0.0.1**. Om detta inte är ett önskat värde bör du skapa en anpassad avsökning och koppla den till HTTP-inställningarna.

**Lösning:**

Följ dessa anvisningar för att lösa problemet.

För Windows:

1.  Logga in på den dator där ditt program finns.

1.  Välj Win + R eller högerklicka på knappen **Start** och välj **Kör**.

1.  Ange **certmgr. msc** och välj RETUR. Du kan också söka efter Certificate Manager på **Start** -menyn.

1.  Leta upp certifikatet (vanligt vis i `\Certificates - Current User\\Personal\\Certificates` ) och öppna certifikatet.

1.  På fliken **information** kontrollerar du certifikatets **ämne**.

1.  Verifiera certifikatets CN-namn från informationen och ange samma i fältet värdnamn för den anpassade avsökningen eller i HTTP-inställningarna (om **Välj värdnamn för HTTP-inställningar för Server** del är markerat). Om det inte är det önskade värd namnet för din webbplats måste du skaffa ett certifikat för domänen eller ange rätt värdnamn i konfigurationen för anpassad avsökning eller HTTP-inställning.

För Linux med OpenSSL:

1.  Kör det här kommandot i OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Från de egenskaper som visas söker du efter CN för certifikatet och anger samma namn i fältet värdnamn i http-inställningarna. Om det inte är det önskade värd namnet för din webbplats måste du skaffa ett certifikat för domänen eller ange rätt värdnamn i konfigurationen för anpassad avsökning eller HTTP-inställning.

#### <a name="backend-certificate-is-invalid"></a>Server dels certifikatet är ogiltigt

**Meddelande:** Server dels certifikatet är ogiltigt. Det aktuella datumet ligger inte inom det \" giltiga från \" och det \" giltiga \" datum intervallet för certifikatet.

**Orsak:** Varje certifikat kommer med ett giltighets intervall och HTTPS-anslutningen blir inte säker om serverns TLS/SSL-certifikat är giltigt. Aktuella data måste vara inom **giltigt från** och **giltigt till** -intervallet. Om det inte är det anses certifikatet vara ogiltigt och det kommer att skapa ett säkerhets problem där Application Gateway markerar backend-servern som ohälsosam.

**Lösning:** Om TLS/SSL-certifikatet har upphört att gälla förnyar du certifikatet hos leverantören och uppdaterar Server inställningarna med det nya certifikatet. Om det är ett självsignerat certifikat måste du generera ett giltigt certifikat och överföra rot certifikatet till Application Gateway HTTP-inställningar. Det gör du genom att följa dessa steg:

1.  Öppna inställningarna för Application Gateway HTTP i portalen.

1.  Välj den inställning som har det utgångna certifikatet, Välj **Lägg till certifikat** och öppna den nya certifikat filen.

1.  Ta bort det gamla certifikatet med hjälp av ikonen **ta bort** bredvid certifikatet och välj sedan **Spara**.

#### <a name="certificate-verification-failed"></a>Certifikat verifieringen misslyckades

**Meddelande:** Det gick inte att verifiera Server dels certifikatets giltighet. Ta reda på orsaken genom att kontrol lera OpenSSL-diagnostik för meddelandet som är associerat med felkoden {errorCode}

**Orsak:** Felet uppstår när Application Gateway inte kan verifiera certifikatets giltighet.

**Lösning:** Lös problemet genom att kontrol lera att certifikatet på servern har skapats på rätt sätt. Du kan till exempel använda [openssl](https://www.openssl.org/docs/man1.0.2/man1/verify.html) för att verifiera certifikatet och dess egenskaper och sedan försöka att ladda upp certifikatet till Application Gateway http-inställningar.

<a name="backend-health-status-unknown"></a>Server dels hälso status: okänd
-------------------------------
Om Server delens hälso tillstånd visas som okänd ser Portal vyn ut ungefär som på följande skärm bild:

![Application Gateway server dels hälsa – okänd](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Detta kan bero på en eller flera av följande orsaker:

1.  NSG på Application Gateway-undernätet blockerar inkommande åtkomst till portarna 65503-65534 (v1 SKU) eller 65200-65535 (v2 SKU) från "Internet".
1.  UDR på Application Gateway under nätet har angetts som standard väg (0.0.0.0/0) och nästa hopp inte har angetts som "Internet".
1.  Standard vägen annonseras av en ExpressRoute/VPN-anslutning till ett virtuellt nätverk via BGP.
1.  Den anpassade DNS-servern har kon figurer ATS på ett virtuellt nätverk som inte kan matcha offentliga domän namn.
1.  Application Gateway är i ett ohälsosamt tillstånd.

**Lösning:**

1.  Kontrol lera om din NSG blockerar åtkomsten till portarna 65503-65534 (v1 SKU) eller 65200-65535 (v2 SKU) från **Internet**:

    a.  På fliken Application Gateway **Översikt** väljer du länken **Virtual Network/undernät** .

    b.  På fliken **undernät** i det virtuella nätverket väljer du det undernät där Application Gateway har distribuerats.

    c.  Kontrol lera om någon NSG har kon figurer ATS.

    d.  Om en NSG har kon figurer ATS kan du söka efter NSG-resursen på fliken **Sök** eller under **alla resurser**.

    e.  I avsnittet **regler för inkommande** trafik lägger du till en regel för inkommande trafik som tillåter mål port intervallet 65503-65534 för v1 sku eller 65200-65535 v2 SKU med **käll** uppsättningen som **valfri** eller **Internet**.

    f.  Välj **Spara** och verifiera att du kan visa Server delen som felfri. Alternativt kan du göra det via [PowerShell/CLI](../virtual-network/manage-network-security-group.md).

1.  Kontrol lera om din UDR har en standard väg (0.0.0.0/0) med nästa hopp som inte angetts som **Internet**:
    
    a.  Följ steg 1a och 1b för att fastställa ditt undernät.

    b.  Kontrol lera om några UDR har kon figurer ATS. I så fall kan du söka efter resursen i Sök fältet eller under **alla resurser**.

    c.  Kontrol lera om det finns några standard vägar (0.0.0.0/0) med nästa hopp som inte angetts som **Internet**. Om inställningen är **virtuell** installation eller **Virtual Network Gateway**, måste du se till att den virtuella enheten eller den lokala enheten kan dirigera paketet tillbaka till Internet-målet utan att ändra paketet.

    d.  Annars ändrar du nästa hopp till **Internet**, väljer **Spara** och kontrollerar Server dels hälsan.

1.  Standard väg annonseras av ExpressRoute/VPN-anslutningen till det virtuella nätverket via BGP:

    a.  Om du har en ExpressRoute/VPN-anslutning till det virtuella nätverket via BGP, och om du annonserar en standard väg, måste du se till att paketet dirigeras tillbaka till Internet-målet utan att ändra det. Du kan kontrol lera genom att använda **anslutnings fel söknings** alternativet i Application Gateway portalen.

    b.  Välj målet manuellt som valfri IP-adress för Internet-routning som 1.1.1.1. Ange mål porten som vad som helst och kontrol lera anslutningen.

    c.  Om nästa hopp är en virtuell nätverksgateway kan det finnas en standard väg som annonseras via ExpressRoute eller VPN.

1.  Om en anpassad DNS-server har kon figurer ATS på det virtuella nätverket kontrollerar du att servern (eller servrarna) kan matcha offentliga domäner. Offentlig domän namn matchning kan krävas i scenarier där Application Gateway måste kontakta externa domäner som OCSP-servrar eller för att kontrol lera certifikatets återkallnings status.

1.  Kontrol lera att Application Gateway är felfri och körs genom att gå till alternativet **Resource Health** i portalen och kontrol lera att tillståndet är **felfritt**. [Kontakta supporten](https://azure.microsoft.com/support/options/)om du ser en **felaktig** eller **försämrad** status.

<a name="next-steps"></a>Nästa steg
----------

Läs mer om [Application Gateway diagnostik och loggning](./application-gateway-diagnostics.md).