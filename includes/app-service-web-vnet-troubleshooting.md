---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: cec44bbabdb7d528c30a8d3396b819f2eb3c5386
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999471"
---
Funktionen är enkel att konfigurera, men det innebär inte att du kommer att få problem kostnads fritt. Om du stöter på problem med att komma åt den önskade slut punkten finns det vissa verktyg som du kan använda för att testa anslutningen från App-konsolen. Det finns två konsoler som du kan använda. Det ena är kudu-konsolen och den andra konsolen i Azure Portal. Om du vill komma åt kudu-konsolen från din app går du till **verktyg**  >  **kudu**. Du kan också komma åt Kudo-konsolen på [webbplats namn]. scm. azurewebsites. net. När webbplatsen har lästs in går du till fliken **fel söknings konsol** . Gå till **verktyg**-konsolen för att komma till den Azure Portal-värdbaserade konsolen från din app  >  .

#### <a name="tools"></a>Verktyg
I inbyggda Windows-appar fungerar inte **ping**, **nslookup** och **tracert** genom-konsolen på grund av säkerhets begränsningar (de fungerar i [anpassade Windows-behållare](../articles/app-service/quickstart-custom-container.md)). Två separata verktyg läggs till för att fylla i Void. För att testa DNS-funktionen har vi lagt till ett verktyg med namnet **nameresolver.exe**. Syntax:

```console
nameresolver.exe hostname [optional: DNS Server]
```

Du kan använda nameresolver för att kontrol lera de värdnamn som appen är beroende av. På så sätt kan du testa om du har något som är felkonfigurerat med DNS eller kanske inte har åtkomst till din DNS-server. Du kan se den DNS-server som appen använder i-konsolen genom att titta på miljövariabler WEBSITE_DNS_SERVER och WEBSITE_DNS_ALT_SERVER.

> [!NOTE]
> nameresolver.exe fungerar för närvarande inte i anpassade Windows-behållare.
>

Du kan använda nästa verktyg för att testa TCP-anslutning till en kombination av värd och port. Det här verktyget kallas **tcpping** och syntaxen är:

```console
tcpping.exe hostname [optional: port]
```

**Tcpping** -verktyget visar om du kan komma åt en speciell värd och port. Det kan visa att det bara går att visa om det finns ett program som lyssnar på värd-och port kombinationen och det finns nätverks åtkomst från din app till den angivna värden och porten.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Felsöka åtkomst till resurser som är värdar för virtuella nätverk
Ett antal saker kan förhindra att appen når en speciell värd och port. I de flesta fall är det någon av följande saker:

* **En brand vägg är på väg.** Om du har en brand vägg i så fall kan du trycka på TCP-timeout. TCP-tidsgräns är 21 sekunder i det här fallet. Använd verktyget **tcpping** för att testa anslutningen. TCP-timeout kan orsakas av många saker utöver brand väggar, men starta där.
* **DNS är inte tillgängligt.** DNS-timeoutvärdet är 3 sekunder per DNS-server. Om du har två DNS-servrar är tids gränsen 6 sekunder. Använd nameresolver för att se om DNS fungerar. Du kan inte använda nslookup eftersom det inte använder den DNS som ditt virtuella nätverk har kon figurer ATS med. Om det inte går att komma åt kan du ha en brand vägg eller NSG som blockerar åtkomsten till DNS, eller så kan den vara avstängd.

Om dessa objekt inte besvarar dina problem, se först till exempel:

**Regional VNet-integrering**
* Är målet en icke-RFC1918 adress och du har inte WEBSITE_VNET_ROUTE_ALL angetts till 1?
* Finns det en NSG som blockerar utgående från ditt integrations-undernät?
* Om du använder Azure ExpressRoute eller en VPN-anslutning är din lokala gateway konfigurerad för att dirigera trafik tillbaka till Azure? Om du kan komma åt slut punkter i ditt virtuella nätverk men inte lokalt, kontrollerar du dina vägar.
* Har du tillräckligt med behörighet för att ställa in delegering i integrations under nätet? Under konfigurationen av regional VNet-integrering delegeras ditt integrations under nät till Microsoft. Web/Server grupper. GRÄNSSNITTET för VNet-integrering delegerar under nätet till Microsoft. Web/Server grupper automatiskt. Om ditt konto inte har tillräckliga nätverks behörigheter för att konfigurera delegering, behöver du en person som kan ange attribut i ditt integrations-undernät för att delegera under nätet. Om du vill delegera integrations under nätet manuellt går du till Azure Virtual Network Subnet UI och anger delegeringen för Microsoft. Web/Server grupper.

**Gateway – nödvändig VNet-integrering**
* Är adress intervallet för punkt-till-plats i RFC 1918-intervallen (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Visas gatewayen som i portalen? Om din Gateway inte är igång kan du ta den tillbaka.
* Visas certifikaten som synkroniserade eller så misstänker du att nätverks konfigurationen har ändrats?  Om dina certifikat inte är synkroniserade eller om du misstänker att en ändring har gjorts i konfigurationen av ditt virtuella nätverk som inte har synkroniserats med ASP, väljer du **Synkronisera nätverk**.
* Är den lokala gatewayen konfigurerad för att dirigera trafik tillbaka till Azure om du är i ett virtuellt nätverk? Om du kan komma åt slut punkter i ditt virtuella nätverk men inte lokalt, kontrollerar du dina vägar.
* Försöker du använda en gateway för samexistens som stöder både punkt-till-plats-och ExpressRoute? Samexistens-gatewayer stöds inte med VNet-integrering.

Fel sökning av nätverks problem är en utmaning eftersom det inte går att se vad som blockerar åtkomsten till en speciell värd: port kombination. Några orsaker är:

* Du har en brand vägg på din värd som förhindrar åtkomst till program porten från ditt punkt-till-plats-IP-intervall. Korsande undernät kräver ofta offentlig åtkomst.
* Mål värden är avstängd.
* Ditt program är nere.
* Du har fel IP-adress eller värddator namn.
* Ditt program lyssnar på en annan port än det du förväntade dig. Du kan matcha ditt process-ID med lyssnings porten med hjälp av "netstat-Aon" på slut punkts värden.
* Dina nätverks säkerhets grupper konfigureras på ett sådant sätt att de förhindrar åtkomst till din program värd och port från ditt punkt-till-plats-IP-intervall.

Du vet inte vilken adress appen faktiskt använder. Det kan vara vilken adress som helst i integrations under nätet eller från punkt-till-plats-adressintervallet, så du måste tillåta åtkomst från hela adress intervallet.

Ytterligare fel söknings steg är:

* Anslut till en virtuell dator i det virtuella nätverket och försök att ansluta till resurs värden: port därifrån. Om du vill testa TCP-åtkomst använder du PowerShell **-kommandot test-NetConnection**. Syntax:

```powershell
test-netconnection hostname [optional: -Port]
```

* Hämta ett program på en virtuell dator och testa åtkomsten till denna värd och port från-konsolen från din app med hjälp av **tcpping**.

#### <a name="on-premises-resources"></a>Lokala resurser ####

Om din app inte kan komma åt en resurs lokalt, kontrollerar du om du kan komma åt resursen från det virtuella nätverket. Använd PowerShell **-kommandot test-NetConnection** för att kontrol lera TCP-åtkomst. Om din virtuella dator inte kan komma åt din lokala resurs kanske VPN-eller ExpressRoute-anslutningen inte är korrekt konfigurerad.

Om den virtuella datorn som är värd för virtuella datorer kan komma åt ditt lokala system, men din app inte kan, beror orsaken förmodligen på något av följande orsaker:

* Dina vägar har inte kon figurer ATS med undernät eller punkt-till-plats-adressintervall i din lokala gateway.
* Dina nätverks säkerhets grupper blockerar åtkomsten för ditt punkt-till-plats-IP-intervall.
* Dina lokala brand väggar blockerar trafik från ditt punkt-till-plats-IP-intervall.
* Du försöker komma åt en icke-RFC 1918-adress med hjälp av funktionen regional VNet-integrering.
