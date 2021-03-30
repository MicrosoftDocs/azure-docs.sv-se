---
title: Hantera förfallo datum för webb innehåll i Azure CDN | Microsoft Docs
description: Lär dig hur du hanterar förfallo datum för Azure Web Apps/Cloud Services, ASP.NET eller IIS-innehåll i Azure CDN.
services: cdn
documentationcenter: .NET
author: asudbring
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: how-to
ms.date: 02/15/2018
ms.author: allensu
ms.openlocfilehash: d8eb450d2010bf2a525a26f1c5ff48f59732ce43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240978"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Hantera utgång av webbinnehåll i Azure CDN
> [!div class="op_single_selector"]
> * [Azure-webbinnehåll](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Filer från offentligt tillgängliga ursprungs webb servrar kan cachelagras i Azure Content Delivery Network (CDN) tills deras TTL-värde (Time to Live) förflutit. TTL-värdet bestäms av `Cache-Control` huvudet i HTTP-svaret från ursprungs servern. I den här artikeln beskrivs hur du ställer in `Cache-Control` rubriker för Web Apps funktionen i Microsoft Azure App Service, Azure Cloud Services, ASP.NET-program och Internet Information Services (IIS)-platser som alla har kon figurer ATS på samma sätt. Du kan ange `Cache-Control` sidhuvudet antingen med hjälp av konfigurationsfiler eller program mässigt. 

Du kan också styra cacheinställningar från Azure Portal genom att ange [regler för CDN-cachelagring](cdn-caching-rules.md). Om du skapar en eller flera regler för cachelagring och ställer in deras beteende för att **åsidosätta** eller **kringgå cache**, ignoreras de cacheinställningar som anges i den här artikeln. Information om allmänna cachelagring av koncept finns i [så här fungerar cachelagring](cdn-how-caching-works.md).

> [!TIP]
> Du kan välja att ange inget TTL-värde för en fil. I det här fallet tillämpar Azure CDN automatiskt en standard-TTL på sju dagar, om du inte har konfigurerat reglerna för cachelagring i Azure Portal. Detta standard-TTL gäller endast för allmänna webb leverans optimeringar. För stora fil optimeringar är standard-TTL en dag och för optimering av medie direkt uppspelning är standard-TTL ett år.
> 
> Mer information om hur Azure CDN arbetar för att påskynda åtkomst till filer och andra resurser finns i [Översikt över Azure-Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ange Cache-Control rubriker med hjälp av regler för CDN-cachelagring
Den bästa metoden för att ställa in en webb servers `Cache-Control` huvud är att använda regler för cachelagring i Azure Portal. Mer information om regler för CDN-cachelagring finns i [styra Azure CDN cachelagring med regler för cachelagring](cdn-caching-rules.md).

> [!NOTE] 
> Reglerna för cachelagring är bara tillgängliga för **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** -profiler. För **Azure CDN Premium från Verizon** -profiler måste du använda [Azure CDN-regel motorn](./cdn-verizon-premium-rules-engine.md) i **hanterings** portalen för liknande funktioner.

**Så här navigerar du till sidan regler för CDN-cachelagring**:

1. I Azure Portal väljer du en CDN-profil och väljer sedan slut punkten för webb servern.

1. I det vänstra fönstret under inställningar, väljer du **Cachelagringsregler**.

   ![Knappen CDN caching rules](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Sidan **Cachelagringsregler** visas.

   ![Sidan CDN-cachelagring](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Ange en webb servers Cache-Control rubriker med globala regler för cachelagring:**

1. Under **globala regler för cachelagring**, **ställer du in** **beteende för cachelagring av frågesträngar** för att **Ignorera frågesträngar** och ställa in **beteende för cachelagring**
      
1. Vid **förfallo tid för cache** anger du 3600 i rutan **sekunder** eller 1 i rutan **timmar** . 

   ![Exempel på globala regler för CDN-cachelagring](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Den här globala regeln för cachelagring anger en cache-varaktighet på en timme och påverkar alla begär anden till slut punkten. Den åsidosätter alla `Cache-Control` eller `Expires` http-huvuden som skickas av ursprungs servern som anges av slut punkten.   

1. Välj **Spara**.

**Så här ställer du in en webb servers fils Cache-Control rubriker med anpassade regler för cachelagring:**

1. Skapa två matchnings villkor under **anpassade regler för cachelagring**:

     a. För det första matchnings villkoret anger du **matcha villkor** till **sökväg** och anger `/webfolder1/*` för **matchnings värde**. Ange **beteende för cachelagring** för att **åsidosätta** och ange 4 i rutan **dagar** .

     b. För det andra matchnings villkoret anger du **matchnings villkor** till **sökväg** och anger `/webfolder1/file1.txt` för **matchnings värde**. Ange **beteende för cachelagring** för att **åsidosätta** och ange 2 i rutan **dagar** .

    ![Exempel på anpassade caching-regler för CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Den första anpassade regeln för cachelagring anger en cache-varaktighet på fyra timmar för alla filer i `/webfolder1` mappen på ursprungs servern som anges av slut punkten. Den andra regeln åsidosätter bara den första regeln för `file1.txt` filen och anger varaktigheten två timmar för cachen.

1. Välj **Spara**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Ange Cache-Control rubriker med hjälp av konfigurationsfiler
För statiskt innehåll, till exempel bilder och formatmallar, kan du styra uppdaterings frekvensen genom att ändra **applicationHost.config** eller **Web.config** konfigurationsfilerna för ditt webb program. Om du vill ange `Cache-Control` rubriken för ditt innehåll använder du `<system.webServer>/<staticContent>/<clientCache>` elementet i någon av filerna.

### <a name="using-applicationhostconfig-files"></a>Använda ApplicationHost.config-filer
**ApplicationHost.config** -filen är rot filen i IIS-konfigurations systemet. Konfigurations inställningarna i en **ApplicationHost.config** -fil påverkar alla program på platsen, men åsidosätts av inställningarna för eventuella **Web.config** -filer som finns för ett webb program.

### <a name="using-webconfig-files"></a>Använda Web.config-filer
Med en **Web.config** -fil kan du anpassa hur hela webb programmet eller en specifik katalog i ditt webb program fungerar. Normalt har du minst en **Web.config** -fil i rotmappen för ditt webb program. För varje **Web.config** -fil i en angiven mapp, påverkar konfigurations inställningarna allting i mappen och dess undermappar, om de inte åsidosätts på undermappnivå av en annan **Web.config** fil. 

Du kan till exempel ange ett `<clientCache>` element i en **Web.config** -fil i webbappens rotmapp för att cachelagra allt statiskt innehåll i ditt webb program i tre dagar. Du kan också lägga till en **Web.config** -fil i en undermapp med mer varierande innehåll (till exempel `\frequent` ) och ange dess `<clientCache>` element för att cachelagra undermappens innehåll i sex timmar. Netto resultatet är att innehållet på hela webbplatsen cachelagras i tre dagar, förutom för innehåll i `\frequent` katalogen, som cachelagras i minst sex timmar.  

Följande XML-konfigurationsfil visar hur du ställer in `<clientCache>` elementet för att ange en maximal ålder på tre dagar:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Om du vill använda attributet **cacheControlMaxAge** måste du ange värdet för attributet **cacheControlMode** till `UseMaxAge` . Den här inställningen orsakade att HTTP-huvudet och-direktivet har lagts till i `Cache-Control: max-age=<nnn>` svaret. Formatet på TimeSpan-värdet för **cacheControlMaxAge** -attributet är `<days>.<hours>:<min>:<sec>` . Värdet konverteras till sekunder och används som värde för `Cache-Control` `max-age` direktivet. Mer information om `<clientCache>` -elementet finns i [client \<clientCache> cache ](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Ange Cache-Control rubriker program mässigt
För ASP.NET-program styr du funktionerna för CDN-cachelagring genom att ange egenskapen **HttpResponse. cache** i .NET-API: et. Information om egenskapen **HttpResponse. cache** finns i [HttpResponse. cache Property](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) och [HttpCachePolicy Class](/dotnet/api/system.web.httpcachepolicy).  

Följ dessa steg om du vill cachelagra program innehåll program mässigt i ASP.NET:
   1. Kontrol lera att innehållet har marker ATS som cacheable genom `HttpCacheability` att ställa in på `Public` . 
   1. Ange en cache-verifierare genom att anropa någon av följande `HttpCachePolicy` metoder:
      - Anrop `SetLastModified` för att ange ett tids stämplings värde för `Last-Modified` huvudet.
      - Anrop `SetETag` för att ange ett värde för `ETag` rubriken.
   1. Du kan också ange en förfallo tid för cachen genom `SetExpires` att ringa till ange ett värde för `Expires` rubriken. Annars gäller standardheuristiken för cachelagring som beskrivits tidigare i det här dokumentet.

Lägg till exempel till följande C#-kod om du vill cachelagra innehåll i en timme:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testa Cache-Control huvud
Du kan enkelt verifiera TTL-inställningarna för ditt webb innehåll. Testa att ditt webb innehåll innehåller svars huvudet med webbläsarens [utvecklingsverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) `Cache-Control` . Du kan också använda ett verktyg som **wget**, [Postman](https://www.getpostman.com/)eller [Fiddler](https://www.telerik.com/fiddler) för att undersöka svarshuvuden.

## <a name="next-steps"></a>Nästa steg
* [Läs mer om **clientCache** -elementet](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Läs dokumentationen för egenskapen **HttpResponse. cache**](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Läs dokumentationen för HttpCachePolicy- **klassen**](/dotnet/api/system.web.httpcachepolicy)  
* [Lär dig mer om cachelagring av koncept](cdn-how-caching-works.md)
