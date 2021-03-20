---
title: Felsöka Azure CDN slut punkter – 404 status kod
description: Lär dig hur du felsöker problem med Azure Content Delivery Network-slutpunkter som returnerar status koderna för 404 HTTP-svar.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d6ad0b8b37bd4f04c22ed52d4ac6717202f22889
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192490"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Felsöka Azure CDN slut punkter som returnerar en 404-status kod
I den här artikeln kan du Felsöka problem med en Azure Content Delivery Network-slutpunkt (CDN) som returnerar 404 HTTP-svars status koder.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Alternativt kan du också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.

## <a name="symptom"></a>Symptom
Du har skapat en CDN-profil och en slut punkt, men innehållet verkar inte vara tillgängligt i CDN. Användare som försöker komma åt ditt innehåll via CDN-URL: en får en HTTP 404-status kod. 

## <a name="cause"></a>Orsak
Det finns flera möjliga orsaker, inklusive:

* Filens ursprung är inte synligt för CDN.
* Slut punkten är felkonfigurerad, vilket gör att CDN ser fel ställe.
* Värden avvisar värd rubriken från CDN.
* Slut punkten har inte haft tid att sprida i CDN.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
> [!IMPORTANT]
> När du har skapat en CDN-slutpunkt är den inte direkt tillgänglig för användning eftersom det tar tid för registreringen att spridas via CDN:
> - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom tio minuter. 
> - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
> - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 90 minuter. 
> 
> Om du har slutfört stegen i det här dokumentet och du fortfarande får 404 svar, kan du vänta några timmar innan du öppnar ett support ärende.
> 
> 

### <a name="check-the-origin-file"></a>Kontrol lera ursprungs filen
Kontrol lera först att filen som ska cachelagras är tillgänglig på ursprungs servern och att den är offentligt tillgänglig på Internet. Det snabbaste sättet att göra det är att öppna en webbläsare i en privat eller Incognito-session och bläddra direkt till filen. Skriv eller klistra in webb adressen i rutan adress och kontrol lera att den resulterar i filen som du förväntar dig. Anta till exempel att du har en fil i ett Azure Storage konto, som är tillgänglig på https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Om du kan läsa in filens innehåll kommer testet att lyckas.

![Lyckades!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Även om det här är det snabbaste och enklaste sättet att verifiera att filen är offentligt tillgänglig, kan vissa nätverkskonfigurationer i organisationen se till att en fil är offentligt tillgänglig när den är i själva verket endast synlig för användare av nätverket (även om den finns i Azure). För att säkerställa att detta inte är fallet testar du filen med en extern webbläsare, till exempel en mobil enhet som inte är ansluten till din organisations nätverk eller en virtuell dator i Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Kontrol lera ursprungs inställningarna
När du har kontrollerat att filen är offentligt tillgänglig på Internet kontrollerar du dina ursprungs inställningar. I [Azure Portal](https://portal.azure.com)bläddrar du till din CDN-profil och väljer den slut punkt du felsöker. På den resulterande **slut punkts** sidan väljer du ursprung.  

![Slut punkts sida med ursprung markerat](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Sidan **ursprung** visas. 

![Ursprungs sida](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Ursprungs typ och värdnamn
Kontrol lera att värdena för **ursprungs typen** och **ursprungs värd namnet** är korrekta. I det här exemplet är https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, hostname-delen av URL: en är *cdndocdemo.blob.Core.Windows.net*, vilket är korrekt. Eftersom Azure Storage, webbappar och moln tjänst ursprung använder ett List Rute värde för fältet **ursprungs namn** , är fel stavningar inte ett problem. Men om du använder ett anpassat ursprung måste du kontrol lera att värd namnet har stavats korrekt.

#### <a name="http-and-https-ports"></a>HTTP-och HTTPS-portar
Kontrol lera dina **http-** och **https-portar**. I de flesta fall är 80 och 443 korrekta och du behöver inga ändringar.  Men om ursprungs servern lyssnar på en annan port, måste den visas här. Om du inte är säker kan du Visa URL: en för din ursprungs fil. HTTP-och HTTPS-specifikationerna använder portarna 80 och 443 som standardvärden. I exempel-URL: en, https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, anges ingen port, så standardvärdet 443 antas och inställningarna är korrekta.  

Anta dock att URL: en för ursprungs filen som du testade tidigare är http: \/ /www.contoso.com:8080/file.txt. Observera *: 8080* -delen i slutet av hostname-segmentet. Det här numret instruerar webbläsaren att använda port 8080 för att ansluta till webb servern på www- \. contoso.com, och därför måste du ange *8080* i fältet **http-port** . Det är viktigt att Observera att dessa port inställningar bara påverkar vilken port som slut punkten använder för att hämta information från ursprunget.

> [!NOTE]
> **Azure CDN Standard från Akamai** -slutpunkter tillåter inte det fullständiga INTERVALLET för TCP-port för ursprung.  En lista över ursprungsportar som inte tillåts finns i [Azure CDN från Akamai-tillåtna ursprungsportar](/previous-versions/azure/mt757337(v=azure.100)).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Kontrol lera slut punkts inställningarna
På sidan **slut punkt** väljer du knappen **Konfigurera** .

![Slut punkts sida med knappen Konfigurera markerad](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Sidan **konfiguration** av CDN-slutpunkt visas.

![Konfigurera sida](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoll
För **protokoll** kontrollerar du att protokollet som används av klienterna är markerat. Eftersom samma protokoll som används av klienten är den som används för att komma åt ursprunget, är det viktigt att du har de ursprungliga portarna konfigurerade korrekt i föregående avsnitt. CDN-slutpunkten lyssnar bara på HTTP-och HTTPS-portarna (80 och 443), oavsett ursprungs portar.

Vi går tillbaka till vårt hypotetiska exempel med http: \/ /www.contoso.com:8080/file.txt.  Som du kommer ihåg contoso angav du *8080* som http-port, men vi antar också att de har angett *44300* som https-port.  Om de skapade en slut punkt med namnet *contoso* skulle deras CDN-slutpunkts-värdnamn vara *contoso.azureedge.net*.  En begäran om http: \/ /contoso.azureedge.net/file.txt är en HTTP-begäran, så slut punkten använder http på port 8080 för att hämta den från ursprunget.  En säker begäran över HTTPS, https: \/ /contoso.azureedge.net/file.txt, skulle leda till att slut punkten använder https på port 44300 vid hämtning av filen från ursprunget.

#### <a name="origin-host-header"></a>Ursprungsvärdadress
**Ursprungs värd huvudet** är värd huvud värde som skickas till ursprunget med varje begäran.  I de flesta fall bör det vara samma som det **ursprungliga värd namnet** som vi verifierade tidigare.  Ett felaktigt värde i det här fältet ger normalt sett 404 status, men det kan förmodligen orsaka andra 4xx status, beroende på vad ursprunget förväntar sig.

#### <a name="origin-path"></a>Sökväg till ursprung
Slutligen bör vi kontrol lera vår **ursprungs Sök väg**.  Som standard är detta tomt.  Du bör endast använda det här fältet om du vill begränsa omfattningen av de ursprungs resurser som du vill göra tillgängliga i CDN.  

I exempel slut punkten ville vi att alla resurser på lagrings kontot är tillgängliga, så **ursprungs Sök vägen** lämnades tomt.  Det innebär att en begäran till https: \/ /cdndocdemo.azureedge.net/publicblob/lorem.txt resulterar i en anslutning från slut punkten till cdndocdemo.Core.Windows.net som begär */publicblob/lorem.txt*.  På samma sätt kan en begäran om https: \/ /cdndocdemo.azureedge.net/donotcache/status.png resultera i slut punkten som begär */donotcache/status.png* från ursprunget.

Men vad händer om du inte vill använda CDN för varje sökväg i ditt ursprung?  Anta att du bara ville exponera *publicblob* -sökvägen.  Om vi anger */publicblob* i fältet **ursprungs Sök väg** så kommer slut punkten att infoga */publicblob* innan varje begäran görs till ursprunget.  Det innebär att begäran för https: \/ /cdndocdemo.azureedge.net/publicblob/-lorem.txt nu faktiskt tar den begär ande delen av URL: en, */publicblob/lorem.txt* och lägger till */publicblob* i början. Detta resulterar i en begäran om */publicblob/publicblob/lorem.txt* från ursprunget.  Om sökvägen inte matchar en faktisk fil returnerar ursprunget status 404.  Rätt URL för att hämta lorem.txt i det här exemplet skulle faktiskt vara https: \/ /cdndocdemo.azureedge.net/lorem.txt.  Observera att vi inte inkluderar */publicblob* -sökvägen alls, eftersom begär ande delen av URL: en är */lorem.txt* och slut punkten lägger till */publicblob*, vilket resulterar i att */publicblob/lorem.txt* att begäran skickas till ursprunget.

