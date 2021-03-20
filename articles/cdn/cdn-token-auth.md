---
title: Skydda Azure CDN-tillgångar med token-autentisering | Microsoft Docs
description: Lär dig hur du använder token-autentisering för att skydda åtkomsten till dina Azure CDN-tillgångar.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mazha
ms.openlocfilehash: 21ef06f37e6840df08b1477f9c0ff24f6e15d1a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95978054"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Skydda Azure CDN-tillgångar med token-autentisering

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt

Token-autentisering är en mekanism som gör att du kan förhindra att Azure-Content Delivery Network (CDN) kan betjäna till gångar till obehöriga klienter. Token-autentisering görs vanligt vis för att förhindra *hotlinking* av innehåll, där en annan webbplats, till exempel ett meddelande kort, använder dina till gångar utan behörighet. Hotlinking kan påverka dina kostnader för innehålls leverans. Genom att aktivera token-autentisering i CDN autentiseras begär anden av CDN Edge-servern innan CDN levererar innehållet. 

## <a name="how-it-works"></a>Så här fungerar det

Token-autentiseringen verifierar att begär Anden genereras av en betrodd plats genom att kräva att begär Anden innehåller ett token-värde som innehåller kodad information om beställaren. Innehållet hanteras endast till en beställare om den kodade informationen uppfyller kraven. annars nekas förfrågningar. Du kan ställa in kraven genom att använda en eller flera av följande parametrar:

- Land/region: Tillåt eller Neka förfrågningar som kommer från de länder/regioner som anges av [lands-/regions koden](/previous-versions/azure/mt761717(v=azure.100)).
- URL: Tillåt endast begär Anden som matchar den angivna till gången eller sökvägen.
- Host: Tillåt eller neka begär Anden som använder de angivna värdarna i begär ande huvudet.
- Referent: Tillåt eller neka begäran från angiven referent.
- IP-adress: Tillåt endast begär Anden som kommer från en speciell IP-adress eller IP-undernät.
- Protokoll: Tillåt eller neka begär Anden baserat på det protokoll som används för att begära innehållet.
- Förfallo tid: tilldela en datum-och tids period för att säkerställa att en länk endast är giltig under en begränsad tids period.

Mer information finns i de detaljerade konfigurations exemplen för varje parameter i [ställa in token-autentisering](#setting-up-token-authentication).

>[!IMPORTANT] 
> Om token-auktorisering har Aktiver ATS för en sökväg i det här kontot är standard-cache läget det enda läget som kan användas för cachelagring av frågesträngar. Mer information finns i [Kontrollera cachelagringsbeteendet med frågesträngar](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Referensarkitektur

Följande arbets flödes diagram beskriver hur CDN använder token-autentisering för att arbeta med din webbapp.

![Arbets flöde för CDN-token-autentisering](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Validerings logik för token på CDN-slutpunkten
    
Följande flödes schema beskriver hur Azure CDN verifierar en klientbegäran när token-autentisering konfigureras på CDN-slutpunkten.

![Validerings logik för CDN-token](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Konfigurera token-autentisering

1. Från [Azure Portal](https://portal.azure.com)bläddrar du till din CDN-profil och väljer sedan **Hantera** för att starta tilläggs portalen.

    ![Knappen Hantera CDN-profil](./media/cdn-token-auth/cdn-manage-btn.png)

2. Hovra över **http stor** och välj sedan **token auth** i utfällandet. Du kan sedan konfigurera krypterings nyckeln och krypterings parametrarna på följande sätt:

   1. Skapa en eller flera krypterings nycklar. En krypterings nyckel är Skift läges känslig och kan innehålla en kombination av alfanumeriska tecken. Andra typer av tecken, inklusive blank steg, är inte tillåtna. Den maximala längden är 250 tecken. För att säkerställa att dina krypterings nycklar är slumpmässiga rekommenderar vi att du skapar dem med hjälp av [openssl-verktyget](https://www.openssl.org/). 

      Verktyget OpenSSL har följande syntax:

      ```rand -hex <key length>```

      Exempel:

      ```OpenSSL> rand -hex 32``` 

      Skapa både en primär och en säkerhets kopierings nyckel för att undvika drift stopp. En säkerhets kopierings nyckel ger oavbruten åtkomst till ditt innehåll när den primära nyckeln uppdateras.
    
   2. Ange en unik krypterings nyckel i rutan **primär nyckel** och ange en säkerhets kopierings nyckel i rutan **säkerhets kopierings nyckel** om du vill.

   3. Välj den minsta krypterings versionen för varje nyckel från listan **lägsta krypterings version** och välj sedan **Uppdatera**:
      - **V2**: anger att nyckeln kan användas för att generera version 2,0 och 3,0-token. Använd bara det här alternativet om du övergår från en äldre version 2,0-krypterings nyckel till en version 3,0-nyckel.
      - **V3**: (rekommenderas) anger att nyckeln endast kan användas för att generera version 3,0-token.

      ![Konfigurations nyckel för CDN token auth](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Använd krypterings verktyget för att konfigurera krypterings parametrar och generera en token. Med krypterings verktyget kan du tillåta eller Neka förfrågningar baserat på förfallo tid, land/region, referent, protokoll och klient-IP (i valfri kombination). Även om det inte finns någon gräns för antalet och kombinationen av parametrar som kan kombineras för att bilda en token, är den totala längden på en token begränsad till 512 tecken. 

      ![Verktyget CDN-kryptering](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Ange värden för en eller flera av följande krypterings parametrar i avsnittet **kryptera verktyg** : 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Parameternamn</th> 
      >   <th>Beskrivning</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Tilldelar en förfallo tid till en token, efter vilken token upphör att gälla. Begär Anden som skickats efter att förfallo tiden har nekats. Den här parametern använder en Unix-tidsstämpel, som baseras på antalet sekunder sedan standard-UNIX-epoken `1/1/1970 00:00:00 GMT` . (Du kan konvertera mellan standard tid och UNIX-tid med hjälp av online-verktyg.)> 
      >    Om du till exempel vill att token ska upphöra att gälla `12/31/2016 12:00:00 GMT` anger du värdet för Unix-tidsstämpel `1483185600` . 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Gör att du kan skräddarsy tokens till en viss till gång eller sökväg. Den begränsar åtkomsten till begär Anden vars URL börjar med en viss relativ sökväg. URL: er är Skift läges känsliga. Mata in flera sökvägar genom att avgränsa varje sökväg med kommatecken. Lägg inte till blank steg. Beroende på dina behov kan du ange olika värden för att ge olika åtkomst nivåer.> 
      >    För webb adressen är till exempel `http://www.mydomain.com/pictures/city/strasbourg.png` dessa begär Anden tillåtna för följande indatavärden: 
      >    <ul>
      >       <li>Indatavärde `/` : alla begär Anden tillåts.</li>
      >       <li>Inmatat värde `/pictures` , följande begär Anden tillåts: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Indatavärde `/pictures/` : endast begär Anden som innehåller `/pictures/` sökvägen tillåts. Till exempel `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Indatavärde `/pictures/city/strasbourg.png` : endast begär Anden för denna angivna sökväg och till gång är tillåtna.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Tillåter endast förfrågningar som härstammar från ett eller flera specificerade länder/regioner. Begär Anden som kommer från alla andra länder/regioner nekas. Använd en [ISO 3166-lands-/regionkod](/previous-versions/azure/mt761717(v=azure.100)) i två bokstäver för varje land/region och avgränsa var och en med kommatecken. Lägg inte till ett blank steg. Om du till exempel bara vill tillåta åtkomst från USA och Frankrike anger du `US,FR` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Nekar förfrågningar som kommer från ett eller flera specificerade länder/regioner. Begär Anden som kommer från alla andra länder/regioner är tillåtna. Implementeringen är samma som <b>ec_country_allow</b> -parametern. Om lands-/regionkoden finns i både <b>ec_country_allow</b> -och <b>ec_country_deny</b> -parametrarna har <b>ec_country_allow</b> -parametern företräde.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Tillåter endast förfrågningar från angiven referent. En referent identifierar URL: en för den webb sida som är länkad till den resurs som begärs. Ta inte med protokollet i parametervärdet.> 
      >    Följande typer av indatatyper tillåts:
      >    <ul>
      >       <li>Ett värdnamn eller ett värdnamn och en sökväg.</li>
      >       <li>Flera referenter. Om du vill lägga till flera referenter avgränsar du varje referent med kommatecken. Lägg inte till ett blank steg. Om du anger ett referent värde, men referent-informationen inte skickas i begäran på grund av webb läsar konfigurationen, nekas begäran som standard.</li> 
      >       <li>Begär Anden med saknad eller tom referent information. Som standard blockerar den <b>ec_ref_allow</b> parametern dessa typer av begär Anden. Om du vill tillåta dessa förfrågningar anger du antingen texten, "saknas" eller anger ett tomt värde (genom att använda ett avslutande kommatecken).</li> 
      >       <li>Under domäner. Ange en asterisk () om du vill tillåta under domäner \* . Om du till exempel vill tillåta alla under domäner av `contoso.com` anger du `*.contoso.com` .</li>
      >    </ul> 
      >    Om du till exempel vill tillåta åtkomst för förfrågningar från `www.contoso.com` , alla under domäner under `contoso2.com` och begär Anden med tomma eller saknade referenter, anger du `www.contoso.com,*.contoso.com,missing` .</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Nekar förfrågningar från angiven referent. Implementeringen är samma som <b>ec_ref_allow</b> -parametern. Om det finns en referent i både <b>ec_ref_allow</b> -och <b>ec_ref_deny</b> -parametrarna har <b>ec_ref_allow</b> -parametern företräde.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Tillåter endast begär Anden från det angivna protokollet. Giltiga värden är `http` , `https` , eller `http,https` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Nekar förfrågningar från det angivna protokollet. Implementeringen är samma som <b>ec_proto_allow</b> -parametern. Om det finns ett protokoll i både <b>ec_proto_allow</b> -och <b>ec_proto_deny</b> -parametrarna har <b>ec_proto_allow</b> -parametern företräde.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Begränsar åtkomsten till den angivna förfrågans IP-adress. Både IPV4 och IPV6 stöds. Du kan antingen ange en enskild IP-adress för begäran eller IP-adresser som är associerade med ett visst undernät. Tillåter till exempel `11.22.33.0/22` förfrågningar från IP-adresser 11.22.32.1 till 11.22.35.254.</td>
      > </tr>
      > </table>

   5. När du är klar med att ange värden för krypterings parameter väljer du en nyckel som ska krypteras (om du har skapat både en primär nyckel och en säkerhets kopierings nyckel) från **nyckeln för att kryptera** listan.
    
   6. Välj en krypterings version från listan **krypterings version** : **v2** för version 2 eller **v3** för version 3 (rekommenderas). 

   7. Välj **kryptera** för att generera token.

      När token har skapats visas den i rutan **genererad token** . Om du vill använda token lägger du till den som en frågesträng i slutet av filen i URL-sökvägen. Till exempel `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Du kan också testa din token med dekrypterings verktyget så att du kan visa din tokens parametrar. Klistra in token-värdet i rutan **token för att dekryptera** . Välj den krypterings nyckel som du vill använda från **nyckeln för att dekryptera** listan och välj sedan **dekryptera**.

      När token har dekrypterats visas parametrarna i rutan **ursprungliga parametrar** .

   9. Du kan också anpassa den typ av svarskod som returneras när en begäran nekas. Välj **aktive rad** och sedan svars koden från listan **svars kod** . **Huvud namnet** anges automatiskt till **location**. Välj **Spara** för att implementera den nya svars koden. För vissa svars koder måste du också ange webb adressen till fel sidan i rutan **rubrik värde** . **403** -svars koden (förbjuden) är markerad som standard. 

3. Välj **regel motor** under **http Large**. Du använder regel motorn för att definiera sökvägar för att tillämpa funktionen, aktivera funktionen token Authentication och aktivera ytterligare funktioner för token-autentisering. Mer information finns i [regel motor referens](./cdn-verizon-premium-rules-engine-reference.md).

   1. Välj en befintlig regel eller skapa en ny regel för att definiera den till gång eller sökväg för vilken du vill använda token-autentisering. 
   2. Om du vill aktivera token-autentisering för en regel väljer du **[token auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm)** i listan **funktioner** och väljer sedan **aktive rad**. Välj **Uppdatera** om du vill uppdatera en regel eller **lägga till** om du skapar en regel.
        
      ![CDN-regler motor för token-autentisering aktivera exempel](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. I regel motorn kan du också aktivera ytterligare funktioner för token-autentisering. Om du vill aktivera någon av följande funktioner väljer du den i listan **funktioner** och väljer sedan **aktive rad**.
    
   - **[Denial Code för token-autentisering](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm)**: bestämmer vilken typ av svar som returneras till en användare när en begäran nekas. Regel uppsättningen här åsidosätter svars koden som anges i avsnittet **anpassad Denial-hantering** på sidan för tokenbaserad autentisering.

   - **[Token auth IGNORE URL Case](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm)**: anger om den URL som används för att validera token är Skift läges känslig.

   - **[Parameter för autentiseringstoken](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm)**: byter namn på frågesträngparametern för token som visas i den begärda URL: en. 
        
     ![Exempel på Inställningar för CDN-autentiseringsinställningar för motor](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Du kan anpassa din token genom att öppna käll koden i [GitHub](https://github.com/VerizonDigital/ectoken).
   Tillgängliga språk är:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN funktioner och priser för Provider

Information om funktioner finns i [Azure CDN produkt funktioner](cdn-features.md). Information om priser finns i [Content Delivery Network priser](https://azure.microsoft.com/pricing/details/cdn/).