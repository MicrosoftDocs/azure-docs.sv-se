---
title: Anropa REST API åtgärder med autentisering med delad nyckel
titleSuffix: Discover how to call Azure Storage REST API operations with Shared Key authorization. Get detailed information about each step of the sample operation.
description: Använd Azure Storage REST API för att göra en begäran till Blob Storage med hjälp av autentisering med delad nyckel.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2019
ms.author: tamram
ms.reviewer: ozge
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f569fdac19c4f765828d24f4d6615fdd7bafef8a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89010910"
---
# <a name="call-rest-api-operations-with-shared-key-authorization"></a>Anropa REST API åtgärder med autentisering med delad nyckel

Den här artikeln visar hur du anropar Azure Storage REST-API: er, inklusive hur du skapar ett Authorization-huvud. Den är skriven från den tidpunkt då en utvecklare vet ingenting om REST och ingen idé att göra ett REST-samtal. När du har lärt dig hur du kallar en REST-åtgärd kan du utnyttja den här kunskapen för att använda andra Azure Storage REST-åtgärder.

## <a name="prerequisites"></a>Förutsättningar

Exempel programmet visar en lista över BLOB-behållare för ett lagrings konto. Om du vill testa koden i den här artikeln behöver du följande objekt:

- Installera [Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) med arbets belastningen **Azure Development** .

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Ett allmänt lagrings konto. Om du inte har ett lagrings konto än kan du läsa [skapa ett lagrings konto](storage-account-create.md).

- Exemplet i den här artikeln visar hur du listar behållarna i ett lagrings konto. Om du vill se utdata lägger du till några behållare i Blob Storage i lagrings kontot innan du börjar.

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Exempel programmet är ett konsol program skrivet i C#.

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Det här kommandot klonar lagret till den lokala git-mappen. Öppna Visual Studio-lösningen genom att leta reda på mappen Storage-dotNet-REST-API-with-auth, öppna den och dubbelklicka på StorageRestApiAuth. SLN.

## <a name="about-rest"></a>Om REST

REST står för *ompresentations tillstånds överföring*. Om du vill ha en bestämd definition kan du kolla på [Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer).

REST är en arkitektur som gör att du kan interagera med en tjänst via ett Internet protokoll, till exempel HTTP/HTTPS. REST är oberoende av program varan som körs på servern eller klienten. REST API kan anropas från vilken plattform som helst som stöder HTTP/HTTPS. Du kan skriva ett program som körs på en Mac, Windows, Linux, en Android-telefon eller surfplatta, iPhone, iPod eller webbplats och använda samma REST API för alla dessa plattformar.

Ett anrop till REST API består av en begäran som görs av klienten och ett svar som returneras av tjänsten. I begäran skickar du en URL med information om vilken åtgärd du vill anropa, vilken resurs som ska agera på, eventuella frågeparametrar och huvuden, och beroende på vilken åtgärd som anropades, en data nytto Last. Svaret från tjänsten innehåller en status kod, en uppsättning svarshuvuden och beroende på vilken åtgärd som anropades, en data nytto Last.

## <a name="about-the-sample-application"></a>Om exempel programmet

Exempel programmet visar en lista över behållare i ett lagrings konto. När du förstår hur informationen i REST API-dokumentationen motsvarar din faktiska kod är andra REST-anrop enklare att räkna ut.

Om du tittar på [BLOB service-REST API](/rest/api/storageservices/Blob-Service-REST-API)visas alla åtgärder som du kan utföra på Blob Storage. Lagrings klient biblioteken är omslutningar i REST-API: erna – de gör det enkelt för dig att komma åt lagring utan att använda REST-API: er direkt. Men som nämnts ovan vill du ibland använda REST API i stället för ett lagrings klient bibliotek.

## <a name="list-containers-operation"></a>Lista behållare åtgärd

Granska referensen för [ListContainers](/rest/api/storageservices/List-Containers2) -åtgärden. Den här informationen hjälper dig att förstå var några av fälten kommer från i förfrågan och svaret.

**Metod för begäran**: Hämta. Det här verbet är den HTTP-metod som du anger som egenskap för objektet Request. Andra värden för det här verbet omfattar HEAD, placering och DELETE, beroende på vilket API som du anropar.

**URI för begäran**: `https://myaccount.blob.core.windows.net/?comp=list` .  Begärd URI skapas från slut punkten för Blob Storage-kontot `https://myaccount.blob.core.windows.net` och resurs strängen `/?comp=list` .

[URI-parametrar](/rest/api/storageservices/List-Containers2#uri-parameters): det finns ytterligare frågeparametrar som du kan använda när du anropar ListContainers. Ett par av dessa parametrar är *timeout* för anropet (i sekunder) och *prefix*, som används för filtrering.

En annan användbar parameter är *maxresults:* om fler behållare är tillgängliga än det här värdet kommer svars texten att innehålla ett *NextMarker* -element som anger nästa behållare som ska returneras för nästa begäran. Om du vill använda den här funktionen anger du *NextMarker* -värdet som *markörens* parameter i URI: n när du gör nästa förfrågan. När du använder den här funktionen är det detsamma som att växla genom resultaten.

Om du vill använda ytterligare parametrar lägger du till dem i resurs strängen med värdet, t. ex. det här exemplet:

```
/?comp=list&timeout=60&maxresults=100
```

[Begärandehuvuden](/rest/api/storageservices/List-Containers2#request-headers)**:** det här avsnittet listar de obligatoriska och valfria begärandehuvuden. Tre huvuden krävs: ett *Authorization* -huvud, *x-MS-date* (innehåller UTC-tiden för begäran) och *x-MS-version* (anger vilken version av REST API som ska användas). Inklusive *x-MS-client-Request-ID* i rubrikerna är valfritt – du kan ange värdet för det här fältet till något. den skrivs till Storage Analytics-loggarna när loggning är aktiverat.

[Brödtext i begäran](/rest/api/storageservices/List-Containers2#request-body)**:** det finns ingen begär ande text för ListContainers. Brödtext för begäran används på alla åtgärder för att ladda upp blobbar, samt SetContainerAccessPolicy, vilket gör att du kan skicka i en XML-lista över lagrade åtkomst principer som ska tillämpas. Lagrade åtkomst principer beskrivs i artikeln [med signaturer för delad åtkomst (SAS)](storage-sas-overview.md).

[Svars status kod](/rest/api/storageservices/List-Containers2#status-code)**:** anger vilka status koder du behöver känna till. I det här exemplet är HTTP-statuskoden 200 OK. Om du vill ha en fullständig lista över HTTP-statuskod, kan du läsa mer i [status kod definitioner](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Om du vill se felkoder som är unika för Storage REST-API: erna, se [vanliga REST API felkoder](/rest/api/storageservices/common-rest-api-error-codes)

[Svars rubriker](/rest/api/storageservices/List-Containers2#response-headers)**:** dessa inkluderar *innehålls typ*; *x-MS-Request-ID*, som är det ID för begäran som du godkände. *x-MS-version*, som anger vilken version av BLOB service som används. och *datumet*, som är i UTC och anger vilken tid begäran gjordes.

[Svars text](/rest/api/storageservices/List-Containers2#response-body): det här fältet är en XML-struktur som tillhandahåller de data som begärs. I det här exemplet är svaret en lista över behållare och deras egenskaper.

## <a name="creating-the-rest-request"></a>Skapar REST-begäran

För säkerhet vid körning i produktion ska du alltid använda HTTPS i stället för HTTP. I den här övningen ska du använda HTTP så att du kan visa begär ande-och svars data. Om du vill visa information om begäran och svar i de faktiska REST-anropen kan du ladda ned [Fiddler](https://www.telerik.com/fiddler) eller ett liknande program. I Visual Studio-lösningen är lagrings kontots namn och nyckel hårdkodad i klassen. Metoden ListContainersAsyncREST skickar lagrings kontots namn och lagrings konto nyckeln till de metoder som används för att skapa de olika komponenterna i REST-begäran. I ett verkligt världs program skulle lagrings kontots namn och nyckel finnas i en konfigurations fil, miljövariabler eller hämtas från en Azure Key Vault.

I vårt exempel projekt finns koden för att skapa ett Authorization-huvud i en separat klass. Idén är att du kan ta hela klassen och lägga till den i din egen lösning och använda den "i befintligt skick". Huvud koden för auktorisering fungerar för de flesta REST API anrop till Azure Storage.

Om du vill bygga begäran, som är ett HttpRequestMessage-objekt, går du till ListContainersAsyncREST i program. cs. Stegen för att skapa begäran är:

- Skapa den URI som ska användas för att anropa tjänsten.
- Skapa HttpRequestMessage-objektet och ange nytto lasten. Nytto lasten är null för ListContainersAsyncREST eftersom vi inte skickar något i.
- Lägg till begärandehuvuden för x-MS-date och x-MS-version.
- Hämta Authorization-huvudet och Lägg till det.

Grundläggande information som du behöver:

- För ListContainers är **metoden** `GET` . Det här värdet anges när en instans av begäran skapas.
- **Resursen** är den fråge del av URI: n som anger vilket API som anropas, så värdet är `/?comp=list` . Som tidigare nämnts finns resursen på referens dokument sidan som visar information om ListContainers-API: [et](/rest/api/storageservices/List-Containers2).
- URI: n konstrueras genom att Blob Service slut punkten för det lagrings kontot skapas och resursen sammanfogas. Värdet för **begärande-URI: n** slutar `http://contosorest.blob.core.windows.net/?comp=list` .
- För ListContainers är **requestBody** null och det finns inga extra **huvuden**.

Olika API: er kan ha andra parametrar för att skickas till som *ifMatch*. Ett exempel på var du kan använda ifMatch är när du anropar PutBlob. I så fall ställer du in ifMatch till en eTag och uppdaterar bara blobben om den eTag som du tillhandahåller matchar den aktuella eTag: en i blobben. Om någon annan har uppdaterat blobben sedan den hämtade eTag-filen, kommer deras ändring inte att åsidosättas.

Börja med att ange `uri` och `payload` .

```csharp
// Construct the URI. It will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Provide the appropriate payload, in this case null.
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Sedan instansierar du begäran och ställer in metoden till `GET` och tillhandahåller URI: n.

```csharp
// Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Lägg till begärandehuvuden för `x-ms-date` och `x-ms-version` . Den här platsen i koden är också där du lägger till eventuella ytterligare begärandehuvuden som krävs för anropet. I det här exemplet finns det inga ytterligare huvuden. Ett exempel på ett API som skickar extra huvuden är den angivna ACL-åtgärden för behållare. Detta API-anrop lägger till ett sidhuvud med namnet "x-MS-BLOB-Public-Access" och värdet för åtkomst nivån.

```csharp
// Add the request headers for x-ms-date and x-ms-version.
DateTime now = DateTime.UtcNow;
httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
// If you need any additional headers, add them here before creating
//   the authorization header.
```

Anropa metoden som skapar Authorization-huvudet och Lägg till den i begärandehuvuden. Du får se hur du skapar Authorization-huvudet senare i artikeln. Metod namnet är GetAuthorizationHeader, som du kan se i det här kodfragmentet:

```csharp
// Get the authorization header and add it.
httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
    storageAccountName, storageAccountKey, now, httpRequestMessage);
```

I det här läget `httpRequestMessage` innehåller rest-begäran slutförd med begärandehuvuden.

## <a name="send-the-request"></a>Skicka begäran

Nu när du har skapat begäran kan du anropa SendAsync-metoden för att skicka den till Azure Storage. Kontrol lera att värdet för svars status koden är 200, vilket innebär att åtgärden har slutförts. Parsa sedan svaret. I det här fallet får du en XML-lista över behållare. Nu ska vi titta på koden för att anropa GetRESTRequest-metoden för att skapa begäran, köra begäran och sedan undersöka svaret på listan över behållare.

```csharp
    // Send the request.
    using (HttpResponseMessage httpResponseMessage =
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200),
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Om du kör en nätverks avlyssning, till exempel [Fiddler](https://www.telerik.com/fiddler) när du ringer till SendAsync, kan du se information om begäran och svar. Vi tar oss en titt. Namnet på lagrings kontot är *contosorest*.

**Anmoda**

```
GET /?comp=list HTTP/1.1
```

**Begärandehuvuden:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Status kod och svars rubriker returnerades efter körning:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Svars text (XML):** I list behållarens åtgärd visas listan över behållare och deras egenskaper.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Nu när du förstår hur du skapar begäran, anropar tjänsten och tolkar resultaten, så kan vi se hur du skapar ett Authorization-huvud. Att skapa sidhuvudet är komplicerat, men den goda nyheten är att när du har koden igång fungerar den för alla REST-API: er för lagrings tjänsten.

## <a name="creating-the-authorization-header"></a>Skapa ett Authorization-huvud

> [!TIP]
> Azure Storage stöder nu Azure Active Directory-integration (Azure AD) för blobbar och köer. Azure AD erbjuder en mycket enklare upplevelse för att auktorisera en begäran till Azure Storage. Mer information om hur du använder Azure AD för att auktorisera REST-åtgärder finns i [bevilja med Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory). En översikt över Azure AD-integrering med Azure Storage finns i [autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory](storage-auth-aad.md).

Det finns en artikel som förklarar konceptuellt (ingen kod) hur du [auktoriserar begär anden till Azure Storage](/rest/api/storageservices/authorize-requests-to-azure-storage).

Nu ska vi ta bort den artikeln nedåt och Visa koden.

Börja med att använda autentisering med delad nyckel. Formatet för auktoriserings huvud ser ut så här:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Fältet signatur är en hash-baserad Message Authentication Code (HMAC) som skapats från begäran och beräknas med SHA256-algoritmen och sedan kodas med base64-kodning. Har du fått det? (Låser dig där, du har inte till och med hört att ordet är *kanoniskt* ännu.)

Det här kodfragmentet visar formatet för den delade nyckelns signatur sträng:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

De flesta av dessa fält används sällan. För Blob Storage anger du VERB, MD5, innehålls längd, kanoniska huvuden och kanoniskt resurs. Du kan lämna det andra tomt (men låta det `\n` vara tomt).

Vad är CanonicalizedHeaders och CanonicalizedResource? Bra fråga! Vad faktiskt är, vad är det kanoniska medelvärdet? Microsoft Word känner inte ens igen som ett ord. Det här är vad [Wikipedia säger om kanoniska](https://en.wikipedia.org/wiki/Canonicalization): *i dator vetenskap är kanoniska (ibland standardisering eller normalisering) en process för att konvertera data som har fler än en möjlig representation till en "standard", "normal" eller kanonisk form.* I normal-Speak innebär detta att ta med listan över objekt (till exempel rubriker när det gäller kanoniska rubriker) och standardisera dem i ett format som krävs. Microsoft beslutade i princip ett format och du måste matcha det.

Vi börjar med de två kanoniska fälten, eftersom de krävs för att skapa ett Authorization-huvud.

### <a name="canonicalized-headers"></a>Kanoniska rubriker

Om du vill skapa det här värdet hämtar du de huvuden som börjar med "x-MS-" och sorterar dem och formaterar dem sedan i en sträng med `[key:value\n]` instanser, sammanfogade till en sträng. I det här exemplet ser de kanoniska rubrikerna ut så här:

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Här är koden som används för att skapa utdata:

```csharp
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
        where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
        orderby kvp.Key
        select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder headersBuilder = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        headersBuilder.Append(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValue in kvp.Value)
        {
            string trimmedValue = headerValue.TrimStart().Replace("\r\n", string.Empty);
            headersBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used
            // if there are multiple values for one of the headers.
            separator = ',';
        }

        headersBuilder.Append("\n");
    }

    return headersBuilder.ToString();
}
```

### <a name="canonicalized-resource"></a>Kanonisk resurs

Den här delen av Signature-strängen representerar det lagrings konto som är målet för begäran. Kom ihåg att begärd URI är `<http://contosorest.blob.core.windows.net/?comp=list>` , med det faktiska konto namnet ( `contosorest` i det här fallet). I det här exemplet returneras detta:

```
/contosorest/\ncomp:list
```

Om du har frågeparametrar innehåller det här exemplet även dessa parametrar. Här är koden, som även hanterar ytterligare frågeparametrar och frågeparametrar med flera värden. Kom ihåg att du skapar den här koden så att den fungerar för alla REST-API: er. Du vill inkludera alla möjligheter, även om ListContainers-metoden inte behöver alla.

```csharp
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item.ToLower()).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Nu när de kanoniska strängarna har angetts ska vi titta på hur du skapar själva Authorization-huvudet. Du börjar med att skapa en sträng med meddelandet signatur i formatet StringToSign som tidigare visades i den här artikeln. Det här konceptet är enklare att förklara med kommentarer i koden, så här är den sista metoden som returnerar Authorization-huvudet:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + signature);
    return authHV;
}
```

När du kör den här koden ser den resulterande MessageSignature ut som i det här exemplet:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Här är det sista värdet för AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader är det sista sidhuvudet som placerats i begärandehuvuden innan svaret har publicerats.

Som täcker allt du behöver veta för att kunna lägga ihop en klass med vilken du kan skapa en begäran om att anropa REST-API: er för lagrings tjänster.

## <a name="example-list-blobs"></a>Exempel: lista blobbar

Nu ska vi titta på hur du ändrar koden för att anropa List BLOB-åtgärden för container *container-1*. Den här koden är nästan identisk med koden för att lista behållare, den enda skillnaden är URI: n och hur du tolkar svaret.

Om du tittar på referens dokumentationen för [ListBlobs](/rest/api/storageservices/List-Blobs), ser du att metoden är *Get* och att RequestURI är:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

I ListContainersAsyncREST ändrar du koden som anger URI: n till API: t för ListBlobs. Container namnet är **container-1**.

```csharp
String uri =
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Sedan kan du hantera svaret genom att ändra koden så att den söker efter blobbar i stället för behållare.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

När du kör det här exemplet får du resultat som följande:

**Kanoniska huvuden:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Kanonisk resurs:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**Meddelandets signatur:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**Authorization-huvud:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Följande värden är från [Fiddler](https://www.telerik.com/fiddler):

**Anmoda**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Begärandehuvuden:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Status kod och svars rubriker returnerades efter körning:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Svars text (XML):** Detta XML-svar visar listan över blobbar och deras egenskaper.

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig hur du gör en begäran till Blob Storage REST API. Med begäran kan du hämta en lista över behållare eller en lista över blobbar i en behållare. Du har lärt dig hur du skapar signaturen för REST API anropet och hur du använder det i REST-begäran. Slutligen har du lärt dig hur du undersöker svaret.

## <a name="next-steps"></a>Nästa steg

- [Blob-tjänstens REST-API](/rest/api/storageservices/blob-service-rest-api)
- [Fil-tjänstens REST-API](/rest/api/storageservices/file-service-rest-api)
- [Kö-tjänstens REST-API](/rest/api/storageservices/queue-service-rest-api)
- [Tabelltjänstens REST-API](/rest/api/storageservices/table-service-rest-api)
