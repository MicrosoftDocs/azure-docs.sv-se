---
title: Felsök sessions tillhörighets problem
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller information om hur du felsöker problem med sessions tillhörighet i Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 548bda36ed2b167c159d32a575b63ecbf10b16dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397577"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Felsöka fel vid tillhörighet mellan Azure Application Gateway-session

Lär dig hur du diagnostiserar och löser problem med sessionsgränsen i Azure Application Gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

Den cookie-baserade sessionstillhörighetsfunktionen är användbar när du vill behålla en användarsession på samma server. Genom att använda gatewayhanterade cookies kan Application Gateway dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Det här är viktigt i de fall där sessionstillstånd har sparats lokalt på servern för en användarsession.

## <a name="possible-problem-causes"></a>Möjliga problem orsakar

Problemet med att underhålla cookie-baserad session tillhörighet kan inträffa på grund av följande huvudsakliga orsaker:

- Inställningen "cookie-baserad tillhörighet" är inte aktive rad
- Ditt program kan inte hantera cookie-baserad tillhörighet
- Programmet använder cookie-baserad tillhörighet men begär Anden fortfarande bouncing mellan server dels servrar

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Kontrol lera om inställningen "cookie-baserad tillhörighet" är aktive rad

Ibland kan problem med sessionsgränsen uppstå när du glömmer att aktivera inställningen "cookie-baserad tillhörighet". Följ anvisningarna för att avgöra om du har aktiverat inställningen "cookie-baserad tillhörighet" på fliken HTTP-inställningar i Azure Portal:

1. Logga in på [Azure portal](https://portal.azure.com/).

2. Klicka på **alla resurser** i det **vänstra navigerings** fönstret. Klicka på namnet på programmets gateway på bladet alla resurser. Om den prenumeration du valde redan har flera resurser kan du ange namnet på programmets gateway i **filtret efter namn...** för att enkelt få åtkomst till Application Gateway.

3. Fliken Välj **http-inställningar** under **Inställningar**.

   ![Skärm bilden visar inställningar med inställningarna H T T P valt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Klicka på **appGatewayBackendHttpSettings** på höger sida för att kontrol lera om du har valt **aktive rad** för cookie-baserad tillhörighet.

   ![Skärm bild som visar Gateway-inställningarna för en app-Gateway, inlcuidng om cookie-baserad tillhörighet har valts.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Du kan också kontrol lera *värdet för alternativet*"**CookieBasedAffinity**" är inställt på "**backendHttpSettingsCollection**" med någon av följande metoder:

- Kör [Get-AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) i PowerShell
- Titta igenom JSON-filen med hjälp av Azure Resource Manager mall

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Programmet kan inte hantera cookie-baserad tillhörighet

#### <a name="cause"></a>Orsak

Programgatewayen kan bara utföra sessionsbaserade tillhörighet med hjälp av en cookie.

#### <a name="workaround"></a>Lösning

Om programmet inte kan hantera cookie-baserad tillhörighet måste du använda en extern eller intern Azure-belastningsutjämnare eller en annan lösning från tredje part.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Programmet använder cookie-baserad tillhörighet men begär Anden fortfarande bouncing mellan server dels servrar

#### <a name="symptom"></a>Symptom

Du har aktiverat inställningen cookie-baserad tillhörighet när du kommer åt Application Gateway med hjälp av en kort namn-URL i Internet Explorer, till exempel: `http://website` , Bouncing är fortfarande mellan backend-servrar.

Följ instruktionerna för att identifiera problemet:

1. Ta en webb fel söknings spårning på "klienten" som ansluter till programmet bakom Application Gateway (vi använder Fiddler i det här exemplet).
    **Tips** Om du inte vet hur du använder Fiddler markerar du alternativet "**Jag vill samla in nätverks trafik och analysera den med hjälp av webb fel sökning**" längst ned.

2. Kontrol lera och analysera sessions loggarna för att avgöra om de cookies som tillhandahålls av klienten har ARRAffinity-information. Om du inte hittar ARRAffinity-information, till exempel "**ARRAffinity =** *ARRAffinityValue*" inom cookie-uppsättningen, innebär det att klienten inte svarar med Arra cookie, som tillhandahålls av Application Gateway.
    Exempel:

    ![Skärm bild som visar en loggfil med en enda post markerad.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![Skärm bild som visar begärandehuvuden för H T T P, inklusive cookie-information.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

Programmet fortsätter att försöka ställa in cookien på varje begäran tills den får svar.

#### <a name="cause"></a>Orsak

Det här problemet uppstår eftersom Internet Explorer och andra webbläsare inte kan lagra eller använda cookien med en kort namn-URL.

#### <a name="resolution"></a>Lösning

Åtgärda problemet genom att ansluta till Application Gateway med hjälp av ett fullständigt domännamn. Använd till exempel [http://website.com](https://website.com/) eller [http://appgw.website.com](http://website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Ytterligare loggar för fel sökning

Du kan samla in fler loggar och analysera dem för att felsöka problem som rör mappning mellan cookie-baserade sessioner

### <a name="analyze-application-gateway-logs"></a>Analysera Application Gateway loggar

Följ anvisningarna för att samla in Application Gateway loggar:

aktivera loggning via Azure Portal

1. Leta upp resursen i [Azure Portal](https://portal.azure.com/)och klicka sedan på **diagnostikloggar**.

   För Application Gateway är tre loggar tillgängliga: åtkomst logg, prestanda logg, brand Väggs logg

2. Klicka på **Aktivera diagnostik** för att börja samla in data.

   ![Skärm bild som visar en Programgateway med valda diagnostikloggar.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Bladet **diagnostikinställningar** innehåller inställningarna för diagnostikloggar. I det här exemplet lagrar Log Analytics loggarna. Klicka på **Konfigurera** under **Log Analytics** för att ange din arbets yta. Du kan också använda händelsehubbar och ett lagringskonto till att spara dina diagnostiska loggar.

   ![Skärm bild som visar fönstret diagnostikinställningar med Log Analytics konfigurera valt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Bekräfta inställningarna och klicka sedan på **Spara**.

   ![Skärm bild som visar fönstret diagnostikinställningar med Spara valt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Visa och analysera Application Gateway åtkomst loggar

1. I Azure Portal under vyn Application Gateway **resurs väljer du diagnostikloggar i** avsnittet **övervakning** .

   ![Skärm bild som visar övervakning med diagnostikloggar markerade.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Välj "**ApplicationGatewayAccessLog**" i list rutan under **logg kategorier** på höger sida.  

   ![Skärm bild som visar List rutan för logg kategorier med ApplicationGatewayAccessLog valt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. I listan Application Gateway åtkomst logg klickar du på den logg som du vill analysera och exportera och exporterar sedan JSON-filen.

4. Konvertera JSON-filen som du exporterade i steg 3 till CSV-fil och visa dem i Excel, Power BI eller andra verktyg för data visualisering.

5. Kontrol lera följande data:

- **ClientIP**– det här är KLIENTens IP-adress från den anslutande klienten.
- **ClientPort** – detta är käll porten från den anslutande klienten för begäran.
- **RequestQuery** – Detta anger mål servern som begäran tas emot.
- **Server-routad**: backend-instans som begäran tas emot.
- **X-AzureApplicationGateway-log-ID**: KORRELATIONS-ID som används för begäran. Den kan användas för att felsöka trafik problem på backend-servrarna. Exempel: X-AzureApplicationGateway-CACHE-träff = 0&SERVER-ROUTad = 10.0.2.4.

  - **Server status**: http-svarskod som Application Gateway emot från Server delen.

  ![Skärm bild som visar Server status som oformaterad text, som huvudsakligen döljs, med clientPort och DIRIGERAd SERVER.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Om du ser två objekt kommer från samma ClientIP och klient port, och de skickas till samma backend-server, vilket innebär att den Application Gateway har kon figurer ATS korrekt.

Om du ser två objekt som kommer från samma ClientIP och klient port och de skickas till de olika backend-servrarna, innebär det att begäran är bouncing mellan backend-servrar. Välj "**programmet använder cookie-baserad tillhörighet men begär Anden som fortfarande bouncing mellan backend-servrar**" längst ned för att felsöka den.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Använd webb fel sökning för att avbilda och analysera HTTP-eller HTTPS-trafik

Webb fel söknings verktyg som Fiddler, kan hjälpa dig att felsöka webb program genom att samla in nätverks trafik mellan Internet-och test datorer. Med dessa verktyg kan du kontrol lera inkommande och utgående data när webbläsaren tar emot/skickar dem. I det här exemplet har Fiddler alternativet HTTP-omuppspelning som kan hjälpa dig att felsöka problem på klient sidan med webb program, särskilt för autentisering av problem.

Använd den webb fel sökare som du väljer. I det här exemplet ska vi använda Fiddler för att avbilda och analysera http-eller HTTPS-trafik genom att följa anvisningarna:

1. Hämta Fiddler-verktyget på <https://www.telerik.com/download/fiddler> .

    > [!NOTE]
    > Välj Fiddler4 om du har installerat .NET 4 på den insamlade datorn. Annars väljer du Fiddler2.

2. Högerklicka på installations programmets körbara fil och kör som administratör för att installera.

    ![Skärm bild som visar installations programmet för Fiddler-verktyget med en snabb meny med alternativet Kör som administratör valt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. När du öppnar Fiddler bör den automatiskt starta insamlingen av trafik (Lägg märke till att insamlingen visas i nedre vänstra hörnet). Tryck på F12 för att starta eller stoppa trafik avbildningen.

    ![Skärm bild som visar Fiddler webb fel sökare med insamlings indikatorn markerad.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Förmodligen är du intresse rad av dekrypterad HTTPS-trafik och du kan aktivera https-dekryptering genom att välja **verktyg**  >  **Fiddler alternativ** och markera kryss rutan " **dekryptera HTTPS-trafik**".

    ![Skärm bild som visar alternativen i Fiddler med H T T P markerat och dekryptera HTTPS-trafik har valts.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Du kan ta bort tidigare orelaterade sessioner innan du återskapar problemet genom att klicka på  **X** (ikon) > **ta bort alla** som följande skärm bild: 

    ![Skärm bild som visar den X-ikon som är vald, som visar alternativet ta bort alla.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. När du har återskapat problemet sparar du filen för granskning genom att välja **Arkiv**  >  **Spara**  >  **alla sessioner..**. 

    ![Skärm bild som visar alternativet Spara alla sessioner valt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Kontrol lera och analysera sessionens loggar för att ta reda på vad problemet är.

    Exempel:

- **Exempel:** Du hittar en loggfil som begäran skickas från klienten och den går till den offentliga IP-adressen för Application Gateway. Klicka på loggen för att visa information.  På den högra sidan är data i den nedre rutan det som Application Gateway återgår till klienten. Välj fliken "RAW" och ta reda på om klienten får en "**set-cookie: ARRAffinity =** *ARRAffinityValue*". Om det inte finns någon cookie är tillhörigheten för sessionen inte angiven, eller så använder Application Gateway inte cookie tillbaka till klienten.

   > [!NOTE]
   > Detta ARRAffinity-värde är cookie-ID: t, som Application Gateways uppsättningar för klienten som ska skickas till en viss backend-server.

   ![Skärm bild som visar ett exempel på information om en loggpost med värdet Set-Cookie markerat.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Exempel B:** Nästa sessionsinformation följt av föregående är klienten som svarar tillbaka till Application Gateway, som har angett ARRAAFFINITY. Om ARRAffinity cookie-ID matchar, ska paketet skickas till samma backend-server som användes tidigare. Kontrol lera de efterföljande raderna i http-kommunikationen för att se om klientens ARRAffinity-cookie ändras.

   ![Skärm bild som visar ett exempel på information om en loggpost med en cookie markerad.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> För samma kommunikations-session ska cookien inte ändras. Markera den översta rutan på höger sida, Välj fliken cookies för att se om klienten använder cookien och skicka tillbaka den till Application Gateway. Annars behåller inte klientens webbläsare och använder cookien för konversationer. Ibland kan klienten ligga.

 

## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet öppnar du ett [support ärende](https://azure.microsoft.com/support/options/).