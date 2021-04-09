---
title: Använda Azure CDN med SAS | Microsoft Docs
description: Azure CDN stöder användning av signaturen för delad åtkomst (SAS) för att ge begränsad åtkomst till privata lagrings behållare.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: ccf55e0e3986de8afe23cb646d4df743b576900c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725330"
---
# <a name="using-azure-cdn-with-sas"></a>Använda Azure CDN med SAS

När du konfigurerar ett lagrings konto för Azure Content Delivery Network (CDN) som ska användas för cachelagring av innehåll, kan alla som känner till URL: er för dina lagrings behållare komma åt filerna som du har laddat upp. För att skydda filerna i ditt lagrings konto kan du ange åtkomsten till dina lagrings behållare från offentlig till privat. Men om du gör det kommer ingen att kunna komma åt dina filer. 

Om du vill ge begränsad åtkomst till privata lagringscontainrar kan du använda funktionen SAS (signatur för delad åtkomst) för Azure-lagringskontot. En SAS är en URI som ger begränsad åtkomst till dina Azure-lagringsresurser utan att du exponerar din kontonyckel. Du kan tillhandahålla en SAS till klienter som du inte litar på med din lagrings konto nyckel men som du vill delegera åtkomst till till vissa lagrings konto resurser. Genom att distribuera en URI för signaturer för delad åtkomst till dessa klienter ger du dem åtkomst till en resurs under en angiven tids period.
 
Med en SAS kan du definiera olika parametrar för åtkomst till en BLOB, till exempel start-och förfallo tider, behörigheter (Läs/skriv) och IP-intervall. I den här artikeln beskrivs hur du använder SAS tillsammans med Azure CDN. Mer information om SAS, inklusive hur du skapar den och dess parameter alternativ finns i [använda signaturer för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Konfigurera Azure CDN för att arbeta med lagrings-SAS
Följande tre alternativ rekommenderas för att använda SAS med Azure CDN. Alla alternativ förutsätter att du redan har skapat en fungerande SAS (se krav). 
 
### <a name="prerequisites"></a>Förutsättningar
Starta genom att skapa ett lagrings konto och sedan skapa en SAS för din till gång. Du kan generera två typer av lagrade Access-signaturer: en tjänst-SAS eller en konto säkerhets Association. Mer information finns i [typer av signaturer för delad åtkomst](../storage/common/storage-sas-overview.md#types-of-shared-access-signatures).

När du har skapat en SAS-token kan du komma åt Blob Storage-filen genom att lägga till `?sv=<SAS token>` i URL: en. URL: en har följande format: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Exempel:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Mer information om hur du ställer in parametrar finns i avsnittet [överväganden för SAS-parametrar](#sas-parameter-considerations) och [signaturer för delad åtkomst](../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works).

![SA-inställningar för CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Alternativ 1: använda SAS med direkt lagring till Blob Storage från Azure CDN

Det här alternativet är det enklaste och använder en enkel SAS-token, som skickas från Azure CDN till ursprungs servern.
 
1. Välj en slut punkt, Välj **regler för cachelagring** och välj sedan **cache varje unik URL** från listan **frågesträng caching** .

    ![CDN-cachelagringsregler](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. När du har konfigurerat SAS på ditt lagrings konto måste du använda SAS-token med URL: en för CDN-slutpunkten och ursprungs servern för att komma åt filen. 
   
   Den resulterande CDN-slutpunktens URL har följande format: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Exempel:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Finjustera cachens varaktighet genom att använda regler för cachelagring eller genom att lägga till `Cache-Control` sidhuvud på ursprungs servern. Eftersom Azure CDN behandlar SAS-token som en enkel frågesträng rekommenderar vi att du ställer in en varaktighet för cachelagring som upphör att gälla vid eller före förfallo tiden för SAS. Annars, om en fil cachelagras under en längre tid än vad SAS är aktiv, kan filen vara tillgänglig från Azure CDN ursprungs servern efter att förfallo tiden för SAS har förflutit. Om den här situationen inträffar och du vill göra den cachelagrade filen otillgänglig, måste du utföra en rensnings åtgärd på filen för att ta bort den från cachen. Information om hur du ställer in cachens varaktighet på Azure CDN finns i [kontroll Azure CDN cachelagring med regler för cachelagring](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Alternativ 2: dold CDN SAS-token med en Rewrite-regel
 
Det här alternativet är endast tillgängligt för **Azure CDN Premium från Verizon** -profiler. Med det här alternativet kan du skydda blob-lagringen på ursprungs servern. Du kanske vill använda det här alternativet om du inte behöver vissa åtkomst begränsningar för filen, men vill hindra användare från att komma åt lagrings ursprunget direkt för att förbättra Azure CDN avläsnings tider. SAS-token, som är okänd för användaren, krävs för alla som har åtkomst till filer i den angivna behållaren för ursprungs servern. Men på grund av den här regeln för URL-omskrivning krävs ingen SAS-token på CDN-slutpunkten.
 
1. Använd [regel motorn](./cdn-verizon-premium-rules-engine.md) för att skapa en Rewrite-regel för URL. Det tar upp till 4 timmar att sprida nya regler.

   ![Knappen Hantera CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Motor knapp för CDN-regler](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Följande exempel på URL-omskrivning använder ett mönster för reguljära uttryck med en samlad grupp och en slut punkt med namnet *sasstoragedemo*:
   
   Källa:   
   `(container1/.*)`


   Mål:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL för att skriva om regel-Left ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
    ![ CDN URL, Skriv om regel-höger](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. När den nya regeln blir aktiv, kan vem som helst komma åt filer i den angivna behållaren på CDN-slutpunkten oavsett om de använder en SAS-token i URL: en. Här är formatet: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Exempel:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Finjustera cachens varaktighet genom att använda regler för cachelagring eller genom att lägga till `Cache-Control` sidhuvud på ursprungs servern. Eftersom Azure CDN behandlar SAS-token som en enkel frågesträng rekommenderar vi att du ställer in en varaktighet för cachelagring som upphör att gälla vid eller före förfallo tiden för SAS. Annars, om en fil cachelagras under en längre tid än vad SAS är aktiv, kan filen vara tillgänglig från Azure CDN-slutpunkten efter att förfallo tiden för sa har förflutit.. Om den här situationen inträffar och du vill göra den cachelagrade filen otillgänglig, måste du utföra en rensnings åtgärd på filen för att ta bort den från cachen. Information om hur du ställer in cachens varaktighet på Azure CDN finns i [kontroll Azure CDN cachelagring med regler för cachelagring](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Alternativ 3: använda autentisering med CDN-säkerhetstoken med en omskrivnings regel

Om du vill använda autentisering med Azure CDN säkerhetstoken måste du ha ett **Azure CDN Premium från Verizon** -profilen. Det här alternativet är det säkraste och anpassningsbara. Klient åtkomst baseras på de säkerhets parametrar som du anger i säkerhetstoken. När du har skapat och konfigurerat säkerhetstoken krävs det i alla URL: er för CDN-slutpunkter. Men på grund av den här regeln för URL-omskrivning krävs ingen SAS-token på CDN-slutpunkten. Om SAS-token senare blir ogiltig kommer Azure CDN inte längre att kunna verifiera innehållet från ursprungs servern.

1. [Skapa en Azure CDN säkerhetstoken](./cdn-token-auth.md#setting-up-token-authentication) och aktivera den med hjälp av regel motorn för CDN-slutpunkten och sökvägen där användarna kan komma åt filen.

   En URL för säkerhetstoken har följande format:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Exempel:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Parameter alternativen för autentisering av säkerhetstoken skiljer sig från parameter alternativen för en SAS-token. Om du väljer att använda en förfallo tid när du skapar en säkerhetstoken bör du ange samma värde som förfallo tid för SAS-token. Detta säkerställer att förfallo tiden är förutsägbar. 
 
2. Använd [regel motorn](./cdn-verizon-premium-rules-engine.md) för att skapa en regel för att skriva över en URL för att ge SAS-åtkomsttoken åtkomst till alla blobbar i behållaren. Det tar upp till 4 timmar att sprida nya regler.

   Följande exempel på URL-omskrivning använder ett mönster för reguljära uttryck med en samlad grupp och en slut punkt med namnet *sasstoragedemo*:
   
   Källa:   
   `(container1/.*)`
   
   Mål:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL för att skriva om regel-Left ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
    ![ CDN URL, Skriv om regel-höger](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Om du förnyar SAS, se till att du uppdaterar regeln för att skapa en ny URL med den nya SAS-token. 

## <a name="sas-parameter-considerations"></a>Överväganden för SAS-parametrar

Eftersom SAS-parametrar inte är synliga för Azure CDN kan Azure CDN inte ändra leverans beteendet baserat på dem. De definierade parameter begränsningarna gäller endast för begär Anden som Azure CDN gör till ursprungs servern, inte för förfrågningar från klienten till Azure CDN. Den här skillnaden är viktig att tänka på när du anger SAS-parametrar. Om dessa avancerade funktioner krävs och du använder [Alternativ 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), anger du lämpliga begränsningar för Azure CDN säkerhetstoken.

| SAS-parameter namn | Beskrivning |
| --- | --- |
| Start | Den tid som Azure CDN kan börja komma åt BLOB-filen. På grund av klock skillnaden (när en klock signal anländer vid olika tidpunkter för olika komponenter), väljer du en tid 15 minuter innan du vill att till gången ska vara tillgänglig direkt. |
| Slut | Tiden efter vilken Azure CDN inte längre kan komma åt BLOB-filen. Tidigare cachelagrade filer på Azure CDN är fortfarande tillgängliga. Om du vill kontrol lera förfallo tiden för filen anger du antingen lämplig förfallo tid för Azure CDN säkerhetstoken eller rensar till gången. |
| Tillåtna IP-adresser | Valfritt. Om du använder **Azure CDN från Verizon** kan du ange den här parametern till de intervall som definieras i [Azure CDN från IP-intervall för Verizon Edge Server](./cdn-pop-list-api.md). Om du använder **Azure CDN från Akamai** kan du inte ange parametern IP-intervall eftersom IP-adresserna inte är statiska.|
| Tillåtna protokoll | De protokoll som tillåts för en begäran som görs med kontots SAS. HTTPS-inställningen rekommenderas.|

## <a name="next-steps"></a>Nästa steg

Mer information om SAS finns i följande artiklar:
- [Använda signaturer för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md)
- [Signaturer för delad åtkomst, del 2: skapa och använda en SAS med Blob Storage](../storage/common/storage-sas-overview.md)

Mer information om hur du ställer in token-autentisering finns i [skydda Azure Content Delivery Network-tillgångar med token-autentisering](./cdn-token-auth.md).