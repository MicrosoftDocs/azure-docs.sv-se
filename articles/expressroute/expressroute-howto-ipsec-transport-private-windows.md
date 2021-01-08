---
title: 'Azure ExpressRoute Private peering: konfigurera IPsec-transportläge – Windows-värdar'
description: Så här aktiverar du IPsec-transportläge mellan virtuella Azure Windows-datorer och lokala Windows-värdar via ExpressRoute privata peering med hjälp av grup princip objekt och organisationsenheter.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: cfcc515787cee2bc90a2100e84337a3c96219d8a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017280"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Konfigurera IPsec-transportläge för privat ExpressRoute-peering

Den här artikeln hjälper dig att skapa IPsec-tunnlar i transport läge över ExpressRoute privata peering. Tunneln skapas mellan virtuella Azure-datorer som kör Windows och lokala Windows-värdar. Stegen i den här artikeln för den här konfigurationen använder grup princip objekt. Det går att skapa den här konfigurationen utan att använda organisationsenheter (OU) och grup princip objekt (GPO). Kombinationen av organisationsenheter och grup princip objekt bidrar till att förenkla kontrollen av dina säkerhets principer och gör det möjligt att snabbt skala upp. Anvisningarna i den här artikeln förutsätter att du redan har en Active Directory konfiguration och att du är van att använda organisationsenheter och grup princip objekt.

## <a name="about-this-configuration"></a>Om den här konfigurationen

Konfigurationen i följande steg använder ett enda virtuellt Azure-nätverk (VNet) med ExpressRoute privat peering. Den här konfigurationen kan dock sträcka sig över andra Azure-virtuella nätverk och lokala nätverk. Den här artikeln hjälper dig att definiera en IPsec-krypterings princip som du kan tillämpa på en grupp med virtuella Azure-datorer eller lokala värdar. Dessa virtuella Azure-datorer eller lokala värdar tillhör samma ORGANISATIONSENHET. Du konfigurerar kryptering mellan virtuella Azure-datorer (VM1 och VM2) och den lokala host1 endast för HTTP-trafik med mål porten 8080. Olika typer av IPsec-principer kan skapas utifrån dina krav.

### <a name="working-with-ous"></a>Arbeta med organisationsenheter 

Säkerhets principen som är associerad med en ORGANISATIONSENHET överförs till datorerna via GRUPPRINCIPOBJEKTet. Några fördelar med att använda organisationsenheter, i stället för att använda principer på en enda värd, är:

* Att associera en princip med en ORGANISATIONSENHET garanterar att datorer som tillhör samma ORGANISATIONSENHET får samma principer.
* Om du ändrar säkerhets principen som är kopplad till OU så tillämpas ändringarna på alla värdar i ORGANISATIONSENHETen.

### <a name="diagrams"></a>Förutsägelse

Följande diagram visar samtrafik och tilldelat IP-adressutrymme. De virtuella Azure-datorerna och den lokala värden kör Windows 2016. De virtuella Azure-datorerna och de lokala host1 ingår i samma domän. De virtuella Azure-datorerna och de lokala värdarna kan matcha namn korrekt med DNS.

[![1]][1]

Det här diagrammet visar IPsec-tunnlar i överföring i ExpressRoute privat peering.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Arbeta med IPsec-princip

I Windows är kryptering kopplad till IPsec-princip. IPsec-principen avgör vilken IP-trafik som skyddas och säkerhetsmekanismen som tillämpas på IP-paketen.
**IPSec-principer** består av följande objekt: **filter listor**, **filter åtgärder** och **säkerhets regler**.

När du konfigurerar IPsec-principen är det viktigt att förstå följande terminologi för IPsec-principer:

* **IPSec-princip:** En regel uppsättning. Endast en princip kan vara aktiv (tilldelad) vid en viss tidpunkt. Varje princip kan ha en eller flera regler som alla kan vara aktiva samtidigt. En dator kan bara tilldelas en aktiv IPsec-princip vid en specifik tidpunkt. I IPsec-principen kan du dock definiera flera åtgärder som kan vidtas i olika situationer. Varje uppsättning IPsec-regler är associerad med en filter lista som påverkar den typ av nätverks trafik som regeln gäller.

* **Filter listor:** Filter listor är paket med ett eller flera filter. En lista kan innehålla flera filter. Ett filter definierar om kommunikationen blockeras, tillåts eller skyddas baserat på följande kriterier: IP-adressintervall, protokoll eller till och med vissa portar. Varje filter matchar en viss uppsättning villkor. till exempel paket som skickas från ett visst undernät till en viss dator på en specifik målport. När nätverks förhållandena matchar ett eller flera av dessa filter, aktive ras filter listan. Varje filter definieras i en viss filter lista. Filter kan inte delas mellan filter listor. En bestämd filter lista kan dock integreras i flera IPsec-principer. 

* **Filter åtgärder:** En säkerhets metod definierar en uppsättning säkerhetsalgoritmer, protokoll och nycklar som en dator erbjuder vid IKE-förhandlingar. Filter åtgärder är listor över säkerhets metoder, rankade i prioritetsordning.  När en dator förhandlar om en IPsec-session, accepterar eller skickar du förslag baserat på säkerhets inställningen som lagras i filter åtgärds listan.

* **Säkerhets regler:** Regler styr hur och när en IPsec-princip skyddar kommunikation. Den använder **filter lista** och **filter åtgärder** för att skapa en IPSec-regel för att skapa IPSec-anslutningen. Varje princip kan ha en eller flera regler som alla kan vara aktiva samtidigt. Varje regel innehåller en lista över IP-filter och en samling säkerhets åtgärder som utförs vid en matchning med den filter listan:
  * Åtgärder för IP-filter
  * Autentiseringsmetoder
  * Inställningar för IP-tunnel
  * Anslutningstyper

[![5]][5]

## <a name="before-you-begin"></a>Innan du börjar

Se till att du uppfyller följande krav:

* Du måste ha en fungerande Active Directory konfiguration som du kan använda för att implementera grupprincip inställningar. Mer information om grup princip objekt finns i [Grupprincip objekt](/previous-versions/windows/desktop/Policy/group-policy-objects).

* Du måste ha en aktiv ExpressRoute-krets.
  * Information om hur du skapar en ExpressRoute-krets finns i [skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md). 
  * Kontrol lera att kretsen har Aktiver ATS av anslutnings leverantören. 
  * Kontrol lera att Azures privata peering har kon figurer ATS för din krets. Mer information finns i artikeln [om konfigurering av routning](expressroute-howto-routing-arm.md) . 
  * Kontrol lera att du har ett VNet och en virtuell nätverksgateway som skapats och är helt etablerad. Följ anvisningarna för att [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway för ExpressRoute använder GatewayType "ExpressRoute", inte VPN.

* ExpressRoute virtuella nätverksgateway måste vara ansluten till ExpressRoute-kretsen. Mer information finns i [ansluta ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

* Verifiera att virtuella Azure Windows-datorer distribueras till VNet.

* Kontrol lera att det finns en anslutning mellan de lokala värdarna och de virtuella Azure-datorerna.

* Kontrol lera att virtuella Azure Windows-datorer och lokala värdar kan använda DNS för att matcha namn korrekt.

### <a name="workflow"></a>Arbetsflöde

1. Skapa ett grup princip objekt och koppla det till ORGANISATIONSENHETen.
2. Definiera en IPsec- **filter åtgärd**.
3. Definiera en IPsec- **filterlista**.
4. Skapa en IPsec-princip med **säkerhets regler**.
5. Tilldela IPsec-GRUPPRINCIPOBJEKTet till ORGANISATIONSENHETen.

### <a name="example-values"></a>Exempelvärden

* **Domän namn:** ipsectest.com

* **OU:** IPSecOU

* **Lokal Windows-dator:** host1

* **Virtuella Azure Windows-datorer:** VM1, VM2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. skapa ett grup princip objekt

1. Skapa ett nytt grup princip objekt som är länkat till en ORGANISATIONSENHET genom att öppna snapin-modulen grupprincip hantering. Leta sedan reda på den ORGANISATIONSENHET som GRUPPRINCIPOBJEKTet ska länkas till. I exemplet heter ORGANISATIONSENHETen **IPSecOU**. 

   [![9]][9]
2. I snapin-modulen för grupprincip hantering väljer du ORGANISATIONSENHETen och högerklickar på. I list rutan väljer du "**skapa ett grup princip objekt i den här domänen och länka det här...**".

   [![10]][10]
3. Ge GRUPPRINCIPOBJEKTet ett intuitivt namn så att du enkelt kan hitta det senare. Välj **OK** för att skapa och länka grupprincipobjektet.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. Aktivera GPO-länken

Om du vill tillämpa GRUPPRINCIPOBJEKTet på ORGANISATIONSENHETen får GRUPPRINCIPOBJEKTet inte bara kopplas till ORGANISATIONSENHETen, men länken måste också vara aktive rad.

1. Leta upp det grup princip objekt som du har skapat, högerklicka och välj **Redigera** i list rutan.
2. Om du vill tillämpa GRUPPRINCIPOBJEKTet på ORGANISATIONSENHETen väljer du **länk aktiverat**.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. definiera åtgärden för IP-filter

1. I list rutan högerklickar du på **IP-säkerhetsprincip på Active Directory** och väljer sedan **Hantera IP-filterlistor och filter åtgärder.**...

   [![15]][15]
2. På fliken "**Hantera filter åtgärder**" väljer du **Lägg till**.

   [![16]][16]

3. I **guiden filter åtgärd för IP-säkerhet** väljer du **Nästa**.

   [![17]][17]
4. Ge filter åtgärden ett intuitivt namn så att du kan hitta den senare. I det här exemplet heter filter åtgärden **kryptering**. Du kan också lägga till en beskrivning. Välj **Nästa**.

   [![18]][18]
5. Med **förhandlings säkerhet** kan du definiera beteendet om IPSec inte kan upprättas med en annan dator. Välj **förhandla om säkerhet** och välj sedan **Nästa**.

   [![19]][19]
6. På sidan **kommunicera med datorer som inte stöder IPSec** väljer **du Tillåt inte oskyddad kommunikation** och väljer sedan **Nästa**.

   [![20]][20]
7. På sidan **IP-trafik och säkerhet** väljer du **anpassad** och väljer sedan **Inställningar...**.

   [![21]][21]
8. På sidan **anpassade säkerhets metod inställningar** väljer du **data integritet och kryptering (ESP): SHA1, 3DES**. Välj sedan **OK**.

   [![22]][22]
9. På sidan **Hantera filter åtgärder** kan du se att filtret för **kryptering** har lagts till. Välj **Stäng**.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. definiera en lista med IP-filter

Skapa en filter lista som anger krypterad HTTP-trafik med mål porten 8080.

1. Använd en **IP-filterlista** för att kvalificera vilka typer av trafik som måste krypteras. I fliken **Hantera IP-filterlistor** väljer du **Lägg** till för att lägga till en ny IP-filterlista.

   [![24]][24]
2. I fältet **Namn:** anger du ett namn för IP-filterlistan. Till exempel **Azure-onpremises-HTTP8080**. Välj sedan **Lägg till**.

   [![25]][25]
3. På sidan **Beskrivning av IP-filter och speglad egenskap** väljer du **speglad**. Den speglade inställningen matchar paket som går i båda riktningarna, vilket möjliggör tvåvägs kommunikation. Välj sedan **Nästa**.

   [![26]][26]
4. På sidan källa för **IP-trafikkälla** väljer du **en speciell IP-adress eller ett undernät** i list rutan **käll adress:** . 

   [![27]][27]
5. Ange käll adressens **IP-adress eller undernät:** IP-trafik och välj sedan **Nästa**.

   [![28]][28]
6. Ange **mål adress:** IP-adress eller undernät. Välj **Nästa**.

   [![29]][29]
7. På sidan **IP-protokoll typ** väljer du **TCP**. Välj **Nästa**.

   [![30]][30]
8. På sidan **IP-protokoll port** väljer du **från valfri port** och **port:**. Skriv **8080** i text rutan. De här inställningarna anger att endast HTTP-trafiken på mål porten 8080 ska krypteras. Välj **Nästa**.

   [![31]][31]
9. Visa listan över IP-filter.  Konfigurationen av IP **-filterlistan Azure-onpremises-HTTP8080** utlöser kryptering för all trafik som matchar följande kriterier:

   * Alla käll adresser i 10.0.1.0/24 (Azure Subnet2)
   * Alla mål adresser i 10.2.27.0/25 (lokalt undernät)
   * TCP-protokoll
   * Målport 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. Redigera listan över IP-filter

Om du vill kryptera samma typ av trafik från den lokala värden till den virtuella Azure-datorn behöver du ett andra IP-filter. Följ samma steg som du använde för att konfigurera det första IP-filtret och skapa ett nytt IP-filter. De enda skillnaderna är käll under nätet och mål under nätet.

1. Om du vill lägga till ett nytt IP-filter i listan IP-filter väljer du **Redigera**.

   [![33]][33]
2. På sidan **IP-filterlista** väljer du **Lägg till**.

   [![34]][34]
3. Skapa ett andra IP-filter med inställningarna i följande exempel:

   [![35]][35]
4. När du har skapat det andra IP-filtret kommer IP-filterlistan att se ut så här:

   [![36]][36]

Om kryptering krävs mellan en lokal plats och ett Azure-undernät för att skydda ett program. I stället för att ändra den befintliga listan över IP-filter kan du lägga till en ny IP-filterlista. Om du kopplar två eller fler IP-filterlistor till samma IPsec-princip får du mer flexibilitet. Du kan ändra eller ta bort en IP-filterlista utan att påverka de andra IP-filterlistorna.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. skapa en IPsec-säkerhetsprincip 

Skapa en IPsec-princip med säkerhets regler.

1. Välj de **IPSecurity-principer i Active Directory** som är kopplade till organisationsenheten. Högerklicka och välj **skapa IP-säkerhetsprincip**.

   [![37]][37]
2. Namnge säkerhets principen. Till exempel **princip – Azure-onpremises**. Välj **Nästa**.

   [![38]][38]
3. Välj **Nästa** utan att markera kryss rutan.

   [![39]][39]
4. Kontrol lera att kryss rutan **Redigera egenskaper** är markerad och välj sedan **Slutför**.

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. redigera IPsec-säkerhetsprincipen

Lägg till IPsec-principen i **IP-filterlistan** och **filter åtgärden** som du har konfigurerat tidigare.

1. Välj **Lägg till** **på fliken Egenskaper** för http-princip.

   [![41]][41]
2. På sidan Välkommen klickar du på **Nästa**.

   [![42]][42]
3. En regel ger möjlighet att definiera IPsec-läge: tunnel läge eller transport läge.

   * I tunnel läge kapslas det ursprungliga paketet av en uppsättning IP-huvuden. Tunnel läge skyddar den interna routningsinformation genom att kryptera det ursprungliga paketets IP-huvud. Tunnel läge implementeras i stor utsträckning mellan gateways i scenarier med plats-till-plats-VPN. Tunnel läge är i de flesta fall som används för kryptering från slut punkt till slut punkt mellan värdar.

   * Transport läge krypterar bara nytto lasten och ESP-trailern. det ursprungliga paketets IP-huvud är inte krypterat. I transport läge är paketets IP-källa och IP-mål oförändrade.

   Välj **den här regeln anger ingen tunnel** och väljer sedan **Nästa**.

   [![43]][43]
4. **Nätverks typ** definierar vilken nätverks anslutning som associeras med säkerhets principen. Välj **alla nätverks anslutningar** och välj sedan **Nästa**.

   [![44]][44]
5. Välj den IP-filterlista som du skapade tidigare,  **Azure-onpremises-HTTP8080** och välj sedan **Nästa**.

   [![45]][45]
6. Välj den befintliga filter åtgärdens **kryptering** som du skapade tidigare.

   [![46]][46]
7. Windows stöder fyra distinkta typer av autentiseringar: Kerberos, certifikat, NTLMv2 och i förväg delad nyckel. Eftersom vi arbetar med domänanslutna värdar väljer **Active Directory standard (Kerberos V5-protokoll)** och väljer sedan **Nästa**.

   [![47]][47]
8. Den nya principen skapar säkerhets regeln: **Azure-onpremises-HTTP8080**. Välj **OK**.

   [![48]][48]

IPsec-principen kräver alla HTTP-anslutningar på mål porten 8080 för att använda IPsec-transportläge. Eftersom HTTP är ett tydligt text protokoll, där säkerhets principen är aktive rad, så krypteras data när de överförs via ExpressRoute privata peering. IPsec-principen för Active Directory är mer komplex att konfigurera än Windows-brandväggen med avancerad säkerhet. Den tillåter dock mer anpassning av IPsec-anslutningen.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. tilldela IPsec-GRUPPRINCIPOBJEKTet till ORGANISATIONSENHETen

1. Visa principen. Säkerhets grup principen har definierats, men ännu inte tilldelats.

   [![49]][49]
2. Om du vill tilldela säkerhets grup principen till OU- **IPSecOU** högerklickar du på säkerhets principen och väljer **tilldela**.
   Varje dator tht tillhör ORGANISATIONSENHETen kommer att ha tilldelats säkerhets grup principen.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Kontrol lera trafik kryptering

Om du vill ta reda på vilket krypterings-GPO som tillämpas på ORGANISATIONSENHETen, installerar du IIS på alla virtuella Azure-datorer och i host1. Varje IIS anpassas för att svara på HTTP-begäranden på Port 8080.
För att verifiera kryptering kan du installera en nätverks avlyssning (t. ex. wireshark) på alla datorer i ORGANISATIONSENHETen.
Ett PowerShell-skript fungerar som en HTTP-klient för att generera HTTP-förfrågningar på Port 8080:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
Följande nätverks hämtning visar resultatet för lokala host1 med Visa filter-ESP för att matcha enbart den krypterade trafiken:

[![51]][51]

Om du kör PowerShell-skriptet lokalt (HTTP-klient) visar nätverks avbildningen i den virtuella Azure-datorn ett liknande spår.

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "nätverks diagram IPSec-transportläge via ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "intressant IPSec-trafik"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "IPSec-princip för Windows"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "organisations enhet i Grupprincip"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "skapa ett grup princip objekt som är kopplat till organisationsenheten"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "tilldela ett namn till grupprincipobjektet som är ASSOCIERAt med organisationsenheten"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "Redigera grupprincipobjektet"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "Hantera IP-filterlistor och filter åtgärder"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "Lägg till filter åtgärd"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Åtgärds guide"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "filter åtgärds namn"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "filter åtgärd"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "ange beteendet är en osäker anslutning har upprättats"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png- "säkerhetsmekanism"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png- "anpassad säkerhets metod"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "filter åtgärds lista"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Lägg till en ny IP-filterlista"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "Lägg till http-trafik i IP-filtret"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "matchnings paket i båda riktningarna"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "Val av käll under nät"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "käll nätverk"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "mål nätverk"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "protokoll"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png- "källport och målport"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "filter lista"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "IP-filterlista med HTTP-trafik"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "lägga till ett andra IP-filter"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP-filterlista – sekund post"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP-filterlista – sekund post"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "skapa säkerhets princip för IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "namn på IPSec-principen"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPSec-princip, guide"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "redigering av IPSec-princip"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "Lägg till ny säkerhets regel i IPSec-principen"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "skapa en ny säkerhets regel"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "transport läge"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "nätverks typ"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "Val av befintlig IP-filterlista"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "Val av befintlig filter åtgärd"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "Val av autentiseringsmetod"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "slutet av skapandet av säkerhets principen"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "IPSec-princip LÄNKad till grupprincipobjektet men inte tilldelad"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "IPSec-princip som tilldelats grupprincipobjektet"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "avbildning av IPSec-krypterad trafik"