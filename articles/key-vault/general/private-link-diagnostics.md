---
title: Diagnostisera konfigurationsproblem för privata länkar i Azure Key Vault
description: Lösa vanliga problem med privat länkar med Key Vault och djupgående att gå in i konfigurationen
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 03abe4e4e098d46060e33ba114872905e54a443f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96317071"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>Diagnostisera konfigurationsproblem för privata länkar i Azure Key Vault

## <a name="introduction"></a>Introduktion

Den här artikeln hjälper användare att diagnostisera och åtgärda problem som rör Key Vault och funktionen privata länkar. Den här guiden hjälper dig med konfigurations aspekter, till exempel att hämta privata länkar som fungerar för första gången, eller för att åtgärda en situation där privata länkar slutade fungera på grund av en ändring.

Om du inte har använt den här funktionen kan du läsa [integrera Key Vault med en privat Azure-länk](private-link-service.md).

### <a name="problems-covered-by-this-article"></a>Problem som omfattas av den här artikeln

- Dina DNS-frågor returnerar fortfarande en offentlig IP-adress för nyckel valvet i stället för en privat IP-adress som du förväntar dig att använda funktionen privata länkar.
- Alla begär Anden som görs av en klient som använder en privat länk fungerar inte med tids gränser eller nätverks fel, och problemet är inte tillfälligt.
- Nyckel valvet har en privat IP-adress, men förfrågningar får fortfarande `403` svar med den `ForbiddenByFirewall` interna felkoden.
- Du använder privata länkar, men ditt nyckel valv accepterar fortfarande förfrågningar från det offentliga Internet.
- Ditt nyckel valv har två privata slut punkter. Förfrågningar som använder sig av fungerar bra, men förfrågningar som använder den andra fungerar inte.
- Du har en annan prenumeration, ett nyckel valv eller ett virtuellt nätverk som använder privata länkar. Du vill skapa en ny liknande distribution, men du kan inte få privata länkar som fungerar där.

### <a name="problems-not-covered-by-this-article"></a>Problem som inte omfattas av den här artikeln

- Det finns ett tillfälligt anslutnings problem. I en viss klient visas vissa begär Anden som fungerar och vissa fungerar inte. *Tillfälliga problem orsakas vanligt vis inte av ett problem i konfigurationen av privata länkar. de är ett tecken på nätverks-eller klient överbelastning.*
- Du använder en Azure-produkt som stöder BYOK (Bring Your Own Key), CMK (kundens hanterade nycklar) eller åtkomst till hemligheter som lagras i Key Vault. När du aktiverar brand väggen i Key Vault-inställningarna kan den produkten inte komma åt ditt nyckel valv. *Titta på produktspecifik dokumentation. Kontrol lera att det uttryckligen tillstånd har stöd för nyckel valv med brand väggen aktive rad. Kontakta supporten för den aktuella produkten om det behövs.*

### <a name="how-to-read-this-article"></a>Läsa den här artikeln

Om du är nybörjare på privata länkar eller om du utvärderar en komplex distribution rekommenderar vi att du läser hela artikeln. Annars kan du välja det avsnitt som är mer begripligt för problemet som du är välkommen till.

Nu sätter vi igång!

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. bekräfta att du äger klient anslutningen

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>Bekräfta att klienten körs i det virtuella nätverket

Den här guiden är avsedd att hjälpa dig att åtgärda anslutningar till nyckel valv som kommer från program koden. Exempel är program och skript som körs i Azure Virtual Machines, Azure Service Fabric-kluster, Azure App Service, Azure Kubernetes service (AKS) och liknande andra. Den här guiden gäller även för åtkomst som utförs i Azure Portal web-Base-användargränssnittet, där webbläsaren har åtkomst till nyckel valvet direkt.

Genom att definiera privata länkar måste programmet, skriptet eller portalen köras på datorn, klustret eller miljön som är anslutna till den Virtual Network där den [privata slut punkts resursen](../../private-link/private-endpoint-overview.md) distribuerades.

Om programmet, skriptet eller portalen körs på ett godtyckligt Internet-anslutet nätverk, är den här guiden inte tillämplig och det går troligen inte att använda privata länkar. Den här begränsningen gäller också för kommandon som körs i Azure Cloud Shell eftersom de körs på en fjärran sluten Azure-dator som finns på begäran i stället för i användarens webbläsare.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>Om du använder en hanterad lösning kan du läsa mer i en speciell dokumentation

Den här guiden gäller inte lösningar som hanteras av Microsoft, där nyckel valvet används av en Azure-produkt som finns oberoende av kundens Virtual Network. Exempel på sådana scenarier är Azure Storage eller Azure SQL som har kon figurer ATS för kryptering i vila, Azure Event Hub som krypterar data med kundtillhandahållna nycklar, Azure Data Factory åtkomst till tjänstens autentiseringsuppgifter som lagras i Key Vault, Azure-pipeline hämtar hemligheter från Key Vault och andra liknande scenarier. I dessa fall *måste du kontrol lera om produkten stöder nyckel valv med brand väggen aktive rad*. Det här stödet utförs vanligt vis med funktionen [betrodda tjänster](overview-vnet-service-endpoints.md#trusted-services) i Key Vault-brandväggen. Många produkter ingår dock inte i listan över betrodda tjänster av olika orsaker. I så fall når du den produktspecifika supporten.

Ett litet antal Azure-produkter stöder begreppet VNet- *inmatning*. Med enkla villkor lägger produkten till en nätverks enhet till kunden Virtual Network, så att den kan skicka begär Anden som om distribuerades till Virtual Network. Ett viktigt exempel är [Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Produkter som detta kan göra förfrågningar till nyckel valvet med hjälp av privata länkar och den här fel söknings guiden kan hjälpa dig.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. kontrol lera att anslutningen har godkänts och lyckats

Följande steg kontrollerar att den privata slut punkts anslutningen godkänns och genomförts:

1. Öppna Azure Portal och öppna Key Vault-resursen.
2. På den vänstra menyn väljer du **nätverk**.
3. Klicka på fliken **anslutningar för privata slut punkter** . Alla anslutningar för privata slut punkter och deras respektive tillstånd visas. Om det inte finns några anslutningar, eller om anslutningen till Virtual Network saknas, måste du skapa en ny privat slut punkt. Detta kommer att täckas senare.
4. I **anslutningar för privata slut punkter** hittar du det du diagnostiserar och bekräftar att "anslutnings status" har **godkänts** och att etablerings status har **slutförts**.
    - Om anslutningen är i vänte läge kan du kanske bara godkänna den.
    - Om anslutningen "Avvisad", "Misslyckad", "fel", "frånkopplad" eller något annat tillstånd, är den inte effektiv alls, måste du skapa en ny privat slut punkts resurs.

Det är en bra idé att ta bort inaktiva anslutningar för att hålla rent saker.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. kontrol lera att nyckel valvs brand väggen är korrekt konfigurerad

>[!IMPORTANT]
> Om du ändrar brand Väggs inställningarna kan du ta bort åtkomst från legitima klienter som fortfarande inte använder privata länkar. Se till att du är medveten om konsekvenserna av varje ändring i brand Väggs konfigurationen.

Ett viktigt begrepp är att funktionen privata länkar bara *ger* åtkomst till ditt nyckel valv i en Virtual Network som är stängd för att förhindra data exfiltrering. Ingen befintlig åtkomst *tas bort* . För att effektivt blockera åtkomst från det offentliga Internet måste du aktivera nyckel valvs brand väggen explicit:

1. Öppna Azure Portal och öppna Key Vault-resursen.
2. På den vänstra menyn väljer du **nätverk**.
3. Se till att fliken **brand väggar och virtuella nätverk** är markerad överst.
4. Kontrol lera att alternativet **privat slut punkt och valda nätverk** är markerat. Om du hittar **alla nätverk** väljer du vilket förklarar varför externa klienter fortfarande kan komma åt nyckel valvet.

Följande instruktioner gäller även för brand Väggs inställningar:

- Funktionen privata länkar kräver inte att något "virtuellt nätverk" anges i brand Väggs inställningarna för nyckel valvet. Alla begär Anden som använder den privata IP-adressen för nyckel valvet (se nästa avsnitt) måste fungera, även om inget virtuellt nätverk har angetts i nyckel valvets brand Väggs inställningar.
- Funktionen privata länkar kräver inte att någon IP-adress anges i brand Väggs inställningarna för nyckel valvet. Alla begär Anden som använder den privata IP-adressen för nyckel valvet måste fungera, även om ingen IP-adress har angetts i brand Väggs inställningarna.

Om du använder privata länkar är de enda motiveringarna för att ange ett virtuellt nätverk eller en IP-adress i Key Vault-brand väggen:

- Du har ett hybrid system där vissa klienter använder privata länkar, vissa använder tjänst slut punkter, vissa använder offentliga IP-adresser.
- Du övergår till privata länkar. I det här fallet bör du ta bort virtuella nätverk och IP-adresser från nyckel valvets brand Väggs inställningar när du har bekräftat att alla klienter använder privata länkar.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. kontrol lera att nyckel valvet har en privat IP-adress

### <a name="difference-between-private-and-public-ip-addresses"></a>Skillnad mellan privata och offentliga IP-adresser

En privat IP-adress har alltid ett av följande format:

- 10. x. x: exempel: `10.1.2.3` , `10.56.34.12` .
- 172.16. x. x till 172.32 x. x: exempel: `172.20.1.1` , `172.31.67.89` .
- 192.168. x. x: exempel: `192.168.0.1` , `192.168.100.7`

Vissa IP-adresser och intervall är reserverade:

- 224. x. x: multicast
- 255.255.255.255: sändning
- 127. x. x: loopback
- 169.254. x. x: länk-lokal
- 168.63.129.16: intern DNS

Alla andra IP-adresser är offentliga.

När du bläddrar i portalen eller kör ett kommando som visar IP-adressen kontrollerar du att du kan identifiera om den IP-adressen är privat, offentlig eller reserverad. För att privata länkar ska fungera måste Key Vault-värdnamnet matcha till en privat IP-adress som tillhör Virtual Network-adressutrymmet.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>Hitta den privata IP-adressen för nyckel valvet i det virtuella nätverket

Du måste diagnostisera matchning av värdnamn och för att du måste känna till den exakta privata IP-adressen för nyckel valvet med privata länkar aktiverade. Följ den här proceduren för att hitta adressen:

1. Öppna Azure Portal och öppna Key Vault-resursen.
2. På den vänstra menyn väljer du **nätverk**.
3. Klicka på fliken **anslutningar för privata slut punkter** . Alla anslutningar för privata slut punkter och deras respektive tillstånd visas.
4. Hitta den som du diagnostiserar och bekräfta att "anslutnings status" har **godkänts** och att etablerings statusen har **slutförts**. Om du inte ser detta går du tillbaka till föregående avsnitt i det här dokumentet.
5. När du hittar rätt objekt klickar du på länken i kolumnen **privat slut punkt** . Den privata slut punkts resursen öppnas.
6. Översikts sidan kan visa ett avsnitt som kallas **anpassade DNS-inställningar**. Bekräfta att det bara finns en post som matchar Key Vault-värdnamnet. Posten visar nyckel valvets privata IP-adress.
7. Du kan också klicka på länken i **nätverks gränssnittet** och kontrol lera att den privata IP-adressen är samma som visas i föregående steg. Nätverks gränssnittet är en virtuell enhet som representerar nyckel valvet.

IP-adressen är den som virtuella datorer och andra enheter som *Kör i samma Virtual Network* använder för att ansluta till nyckel valvet. Anteckna IP-adressen eller se till att fliken webbläsare är öppen och rör den inte när du gör ytterligare undersökningar.

>[!NOTE]
> Om nyckel valvet har flera privata slut punkter har det flera privata IP-adresser. Detta är bara användbart om du har flera virtuella nätverk som har åtkomst till samma nyckel valv, var och en via sin egen privata slut punkt (den privata slut punkten tillhör en enda Virtual Network). Kontrol lera att du diagnostiserar problemet för rätt Virtual Network och välj rätt privat slut punkts anslutning i proceduren ovan. Skapa **inte** heller flera privata slut punkter för samma Key Vault i samma Virtual Network. Detta behövs inte och är en källa för förvirring.

## <a name="5-validate-the-dns-resolution"></a>5. kontrol lera DNS-matchningen

DNS-matchning är processen att översätta Key Vault-värdnamnet (exempel: `fabrikam.vault.azure.net` ) till en IP-adress (exempel: `10.1.2.3` ). Följande underavsnitt visar förväntade resultat av DNS-matchning i varje scenario.

### <a name="key-vaults-without-private-link"></a>Nyckel valv utan privat länk

Det här avsnittet är avsett för inlärnings ändamål. När nyckel valvet inte har någon privat slut punkts anslutning i godkänt tillstånd, ger matchning av värdnamn ett resultat som liknar detta:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

Du kan se att namnet matchar en offentlig IP-adress och att det inte finns något `privatelink` alias. Aliaset förklaras senare, oroa dig inte om det nu.

Ovanstående resultat förväntas oavsett om datorn är ansluten till Virtual Network eller är en godtycklig dator med en Internet anslutning. Detta beror på att nyckel valvet inte har någon privat slut punkts anslutning i godkänt tillstånd, och därför finns det inget behov av nyckel valvet för att stödja privata länkar.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>Nyckel valv med privat länk matchning från valfri Internet dator

När nyckel valvet har en eller flera privata slut punkts anslutningar i godkänt tillstånd och du matchar värd namnet från en valfri dator som är ansluten till Internet (en dator som *inte är* ansluten till den Virtual Network där den privata slut punkten finns), ska du hitta följande:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

Den viktiga skillnaden från föregående scenario är att det finns ett nytt alias med värdet `{vaultname}.privatelink.vaultcore.azure.net` . Det innebär att nyckel valvets data plan är redo att ta emot begär Anden från privata länkar.

Det innebär inte att begär Anden som utförs från datorer *utanför* den Virtual Network (precis som den som du använder) använder privata länkar – de kommer inte. Du kan se att det från det faktum att värd namnet fortfarande matchar en offentlig IP-adress. Endast datorer *som är anslutna till Virtual Network* kan använda privata länkar. Mer information om detta kommer att följa.

Om du inte ser `privatelink` aliaset innebär det att nyckel valvet har noll privata slut punkts anslutningar i `Approved` läget. Gå tillbaka till [det här avsnittet](#2-confirm-that-the-connection-is-approved-and-succeeded) innan du försöker igen.

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>Nyckel valv med privat länk matchning från Virtual Network

När nyckel valvet har en eller flera privata slut punkts anslutningar i godkänt tillstånd och du matchar värd namnet från en dator som är ansluten till den Virtual Network där den privata slut punkten skapades, är detta det förväntade svaret:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  10.1.2.3
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3
```

Det finns två viktiga skillnader. Först matchas namnet till en privat IP-adress. Det måste vara IP-adressen som vi hittade i [motsvarande avsnitt](#find-the-key-vault-private-ip-address-in-the-virtual-network) i den här artikeln. Sedan finns det inga andra alias efter det `privatelink` . Detta beror på att Virtual Network DNS-servrarna *fångar* upp kedjan med alias och returnerar den privata IP-adressen direkt från namnet `fabrikam.privatelink.vaultcore.azure.net` . Posten är i själva verket en `A` post i en privat DNS zon. Mer information om detta kommer att följa.

>[!NOTE]
> Resultatet ovan sker bara på en virtuell dator som är ansluten till den Virtual Network där den privata slut punkten skapades. Om du inte har en virtuell dator som har distribuerats i Virtual Network som innehåller den privata slut punkten, distribuerar du en anslutning till den och ansluter den till den och kör sedan `nslookup` kommandot (Windows) eller `host` kommandot (Linux) ovan.

Om du kör de här kommandona på en virtuell dator som är ansluten till den Virtual Network där den privata slut punkten skapades, och de **inte** visar nyckel valvets privata IP-adress, kan nästa avsnitt hjälpa dig att åtgärda problemet.

## <a name="6-validate-the-private-dns-zone"></a>6. Verifiera Privat DNS zonen

Om DNS-matchningen inte fungerar enligt beskrivningen i föregående avsnitt, kan det finnas ett problem med din Privat DNS zon och det här avsnittet kan hjälpa dig. Om DNS-matchningen visar rätt privata IP-adress för Key Vault är din Privat DNS zon förmodligen korrekt. Du kan hoppa över hela det här avsnittet.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Bekräfta att den nödvändiga resursen för Privat DNSs zonen finns

Din Azure-prenumeration måste ha en [privat DNS zon](../../dns/private-dns-privatednszone.md) resurs med det här exakta namnet:

`privatelink.vaultcore.azure.net`

Du kan söka efter den här resursens förekomst genom att gå till prenumerations sidan i portalen och välja resurser på den vänstra menyn. Resurs namnet måste vara `privatelink.vaultcore.azure.net` och resurs typen måste vara **privat DNS zon**.

Normalt skapas den här resursen automatiskt när du skapar en privat slut punkt med en vanlig procedur. Men det finns fall där den här resursen inte skapas automatiskt och du måste göra det manuellt. Den här resursen kan också ha tagits bort av misstag.

Om du inte har den här resursen skapar du en ny Privat DNS zon resurs i din prenumeration. Kom ihåg att namnet måste vara exakt `privatelink.vaultcore.azure.net` , utan blank steg eller ytterligare punkter. Om du anger fel namn kommer namn matchningen som beskrivs i den här artikeln inte att fungera. Mer information om hur du skapar den här resursen finns i [skapa en privat Azure-DNS-zon med hjälp av Azure Portal](../../dns/private-dns-getstarted-portal.md). Om du följer den sidan kan du hoppa över Virtual Network skapa eftersom du redan borde ha en. Du kan även hoppa över validerings procedurer med Virtual Machines.

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Bekräfta att Privat DNS zon är länkad till Virtual Network

Det finns inte tillräckligt med Privat DNS zon. Den måste också vara länkad till Virtual Network som innehåller den privata slut punkten. Om Privat DNS zon inte är länkad till rätt Virtual Network, kommer alla DNS-matchningar från den Virtual Network att ignorera Privat DNS zon.

Öppna Privat DNS zon resurs och klicka på alternativet **virtuella nätverks länkar** på den vänstra menyn. En lista med länkar visas, var och en med namnet på en Virtual Network i din prenumeration. Den Virtual Network som innehåller den privata slut punkts resursen måste visas här. Om det inte finns där lägger du till det. Detaljerade anvisningar finns i [länka det virtuella nätverket](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network). Du kan lämna alternativet "Aktivera automatisk registrering" omarkerat – funktionen är inte relaterad till privata länkar.

När Privat DNS zon är länkad till Virtual Network kommer DNS-begäranden som kommer från Virtual Network att söka efter namn i Privat DNS zon. Detta krävs för korrekt adress matchning på Virtual Machines ansluten till den Virtual Network där den privata slut punkten skapades.

>[!NOTE]
> Om du precis har sparat länken kan det ta lite tid innan det här börjar gälla, även efter att portalen säger att åtgärden har slutförts. Du kan också behöva starta om den virtuella datorn som du använder för att testa DNS-matchning.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>Bekräfta att Privat DNS zonen innehåller rätt A-post

Öppna zonen Privat DNS med namnet i portalen `privatelink.vaultcore.azure.net` . På sidan Översikt visas alla poster. Som standard skapas en post med namn `@` och typ `SOA` , vilket innebär auktoritets start. Vidrör inte.

För att namn matchningen för nyckel valvet ska fungera måste det finnas en `A` post med namnet på det enkla valvet utan suffix eller punkter. Om värd namnet till exempel är `fabrikam.vault.azure.net` , måste det finnas en `A` post med namnet `fabrikam` , utan suffix eller punkter.

Dessutom måste värdet för `A` posten (IP-adressen) vara [den privata IP-adressen för Key Vault](#find-the-key-vault-private-ip-address-in-the-virtual-network). Om du hittar `A` posten men den innehåller fel IP-adress måste du ta bort fel IP-adress och lägga till en ny. Vi rekommenderar att du tar bort hela `A` posten och lägger till en ny.

>[!NOTE]
> När du tar bort eller ändrar en `A` post kan datorn fortfarande matcha den gamla IP-adressen eftersom TTL-värdet (Time to Live) kanske inte har gått ut än. Vi rekommenderar att du alltid anger ett TTL-värde som är mindre än 60 sekunder (en minut) och inte större än 600 sekunder (10 minuter). Om du anger ett värde som är för stort kan det ta för lång tid för dina klienter att återställa efter avbrott.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>DNS-matchning för mer än en Virtual Network

Om det finns flera virtuella nätverk och var och en har sin egen privata slut punkts resurs som refererar till samma nyckel valv, måste nyckel valvets värdnamn matcha till en annan privat IP-adress beroende på nätverket. Det innebär att flera Privat DNS zoner också behövs, var och en länkad till en annan Virtual Network och använder en annan IP-adress i `A` posten.

I mer avancerade scenarier kan de virtuella nätverken ha peering aktive rad. I det här fallet behöver bara en Virtual Network en privat slut punkts resurs, men båda kan behöva länkas till Privat DNS zon resursen. Detta är ett scenario som inte omfattas direkt av det här dokumentet.

### <a name="understand-that-you-have-control-over-dns-resolution"></a>Förstå att du har kontroll över DNS-matchning

Som förklaras i [föregående avsnitt](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine)har ett nyckel valv med privata länkar alias `{vaultname}.privatelink.vaultcore.azure.net` i sin *offentliga* registrering. Den DNS-server som används av Virtual Network använder den offentliga registreringen, men den kontrollerar varje alias för en *privat* registrering, och om en sådan finns, stoppas följande alias som definieras vid offentlig registrering.

Den här logiken innebär att om Virtual Network är länkad till en Privat DNS zon med namn `privatelink.vaultcore.azure.net` och den offentliga DNS-registreringen för nyckel valvet har ett alias `fabrikam.privatelink.vaultcore.azure.net` (Observera att hostname-suffixet för nyckel valvet stämmer överens med namnet på den privat DNS zonen exakt), kommer DNS-frågan att söka efter en `A` post med namnet `fabrikam` *i privat DNS zon*. Om `A` posten hittas returneras IP-adressen i DNS-frågan och ingen ytterligare sökning utförs vid offentlig DNS-registrering.

Som du kan se är namn matchningen under din kontroll. Rationella för den här designen är:

- Du kan ha ett komplext scenario som omfattar anpassade DNS-servrar och integrering med lokala nätverk. I så fall måste du kontrol lera hur namn översätts till IP-adresser.
- Du kan behöva åtkomst till ett nyckel valv utan privata länkar. I så fall måste matcha värd namnet från Virtual Network returnera den offentliga IP-adressen, och detta inträffar eftersom nyckel valv utan privata länkar inte har `privatelink` aliaset i namn registreringen.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. kontrol lera att förfrågningar till nyckel valv använder privat länk

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>Fråga `/healthstatus` slut punkten för nyckel valvet

Ditt nyckel valv ger `/healthstatus` slut punkten som kan användas för diagnostik. Svars rubrikerna innehåller den ursprungliga IP-adressen som visas i Key Vault-tjänsten. Du kan anropa slut punkten med följande kommando (**kom ihåg att använda nyckel valvets värdnamn**):

Windows (PowerShell):

```powershell
PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers
```

```output
Key                           Value
---                           -----
Pragma                        no-cache
x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
x-ms-keyvault-service-version 1.2.27.0
x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security     max-age=31536000;includeSubDomains
Content-Length                4
Cache-Control                 no-cache
Content-Type                  application/json; charset=utf-8
```

Linux eller en ny version av Windows 10 som innehåller `curl` :

```console
joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
```

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: application/json; charset=utf-8
x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
x-ms-keyvault-service-version: 1.2.27.0
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security: max-age=31536000;includeSubDomains
Content-Length: 4
```

Om du inte får utdata som liknar den, eller om du får ett nätverks fel, betyder det att ditt nyckel valv inte är tillgängligt via det värdnamn du angav ( `fabrikam.vault.azure.net` i exemplet). Antingen matchar värd namnet inte rätt IP-adress, eller så har du ett anslutnings problem på transport lagret. Det kan bero på problem med routningen, paket droppar och andra orsaker. Du måste undersöka ytterligare.

Svaret måste innehålla sidhuvud `x-ms-keyvault-network-info` :

```console
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
```

`addr`Fältet i `x-ms-keyvault-network-info` rubriken visar IP-adressen för begärans ursprung. Den här IP-adressen kan vara något av följande:

- Den privata IP-adressen för den dator som utför begäran. Detta anger att begäran använder privata länkar eller tjänst slut punkter. Detta är det förväntade resultatet för privata länkar.
- En annan privat IP-adress, *inte* från den dator som utför begäran. Detta anger att en viss anpassad routning är effektiv. Det anger fortfarande att begäran använder privata länkar eller tjänst slut punkter.
- En offentlig IP-adress. Detta anger att begäran dirigeras till Internet via en gateway-enhet (NAT). Detta anger att begäran inte använder privata länkar och att vissa problem måste åtgärdas. Vanliga orsaker till detta är 1) den privata slut punkten är inte godkänd och lyckad. och 2) värd namnet matchar inte nyckel valvets privata IP-adress. Den här artikeln innehåller fel söknings åtgärder för båda fallen.

>[!NOTE]
> Om begäran om att `/healthstatus` fungera, men `x-ms-keyvault-network-info` rubriken saknas, hanteras slut punkten förmodligen inte av nyckel valvet. Eftersom kommandona ovan även validerar HTTPS-certifikat kan den saknade rubriken vara ett tecken på manipulering.

### <a name="query-the-key-vault-ip-address-directly"></a>Fråga nyckel valvets IP-adress direkt

>[!IMPORTANT]
> Åtkomst till nyckel valvet utan HTTPS-certifikat validering är farligt och kan bara användas i inlärnings syfte. Produktions koden får aldrig komma åt nyckel valvet utan den här verifieringen på klient sidan. Även om du bara diagnostiserar problem kan du bli underkastad manipulering av försök som inte kommer att visas om du ofta inaktiverar HTTPS-certifikat verifiering i dina begär anden till Key Vault.

Om du har installerat en ny version av PowerShell kan du använda `-SkipCertificateCheck` för att hoppa över https-certifikats kontroller, och sedan kan du rikta [nyckel valvets IP-adress](#find-the-key-vault-private-ip-address-in-the-virtual-network) direkt:

```powershell
PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers
```

Om du använder `curl` kan du göra det med följande `-k` argument:

```console
joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus
```

Svaren måste vara samma som i föregående avsnitt, vilket innebär att det måste innehålla `x-ms-keyvault-network-info` rubriken med samma värde. `/healthstatus`Slut punkten är inte försiktig om du använder Key Vault-värdnamnet eller IP-adressen.

Om du ser `x-ms-keyvault-network-info` returnera ett värde för begäran med Key Vault-värdnamnet och ett annat värde för begäran som använder IP-adressen, är varje begäran en annan slut punkt. Se förklaring av `addr` fältet från `x-ms-keyvault-network-info` i föregående avsnitt för att avgöra vilket fall som är fel och måste åtgärdas.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. andra ändringar och anpassningar som orsakar påverkan

Följande objekt är icke-omfattande undersöknings åtgärder. De meddelar dig var du ska leta efter ytterligare problem, men du måste ha avancerade nätverks kunskaper för att åtgärda problem i dessa scenarier.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Diagnostisera anpassade DNS-servrar på Virtual Network

I portalen öppnar du Virtual Network resursen. Öppna **DNS-servrar** i den vänstra menyn. Om du använder "anpassad" kanske DNS-matchningen inte är så som beskrivs i det här dokumentet. Du måste diagnostisera hur DNS-servrarna matchar Key Vault-värdnamnet.

Om du använder standard Azure-tillhandahållna DNS-servrar är hela dokumentet tillämpligt.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>Diagnostisera värdar för att åsidosätta eller anpassa DNS-servrar på den virtuella datorn

I många operativ system kan du ange en explicit fast IP-adress per värdnamn, vanligt vis genom att ändra `hosts` filen. De kan också tillåta åsidosättning av DNS-servrar. Om du använder något av dessa scenarier kan du fortsätta med systemdefinierade diagnos alternativ.

### <a name="promiscuous-proxies-fiddler-etc"></a>Filtrerad (Fiddler)-proxyservrar

Med undantag för vad som uttryckligen anges fungerar bara diagnostikinställningar i den här artikeln om det inte finns någon icke-filtrerad proxy i miljön. Även om dessa proxyservrar ofta installeras exklusivt på den dator som diagnostiseras (Fiddler är det vanligaste exemplet) kan erfarna administratörer skriva över rot certifikat utfärdare och installera en icke-filtrerad proxy i gateway-enheter som hanterar flera datorer i nätverket. Dessa proxyservrar kan påverka både säkerhet och pålitlighet i huvudsak. Microsoft stöder inte konfigurationer som använder sådana produkter.

### <a name="other-things-that-may-affect-connectivity"></a>Andra saker som kan påverka anslutningen

Det här är en icke uttömmande lista över objekt som kan hittas i avancerade eller komplexa scenarier:

- Brand Väggs inställningar, antingen Azure-brandväggen som är ansluten till Virtual Network eller en anpassad brand Väggs lösning som distribueras i Virtual Network eller på datorn.
- Nätverks-peering, som kan påverka vilka DNS-servrar som används och hur trafik dirigeras.
- Lösningar för anpassad Gateway (NAT), vilket kan påverka hur trafiken dirigeras, inklusive trafik från DNS-frågor.