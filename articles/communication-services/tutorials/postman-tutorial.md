---
title: Självstudie – signera och göra förfrågningar till ACS SMS API med Postman
titleSuffix: An Azure Communication Services tutorial
description: Lär dig hur du signerar och gör förfrågningar för ACS med Postman för att skicka ett SMS-meddelande.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5805734a9253962d672a4236a5650e9de8b37f0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044302"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>Självstudie: signera och göra förfrågningar med Postman
I den här självstudien kommer vi att konfigurera och använda Postman för att skicka en begäran till ACS-tjänster (Azure Communication Services) med HTTP. I slutet av den här självstudien har du skickat ett SMS-meddelande med ACS och Postman och kan använda Postman för att utforska andra API: er i ACS.

I den här självstudien kommer vi att:
> [!div class="checklist"]
> * Hämtar Postman
> * Konfigurera PostMan för att signera HTTP-begäranden
> * Gör en begäran mot ACS SMS API för att skicka ett meddelande.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Med det kostnads fria kontot får du $200 i Azure-krediter för att testa valfri kombination av tjänster.
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Lär dig hur du skapar en kommunikations tjänst resurs](../quickstarts/create-communication-resource.md).
- Ett ACS-telefonnummer som kan skicka SMS-meddelanden finns i vår [Hämta ett telefonnummer](../quickstarts/telephony-sms/get-phone-number.md) för att få ett sådant.

## <a name="downloading-and-installing-postman"></a>Hämta och installera Postman

Postman är ett Skriv bords program som kan skapa API-begäranden mot alla HTTP-API: er. Den används ofta för att testa och utforska API: er. Vi kommer att ladda ned den senaste [versionen av Skriv bordet från Postman-webbplatsen](https://www.postman.com/downloads/). Postman har versioner för Windows, Mac och Linux så ladda ned den version som är lämplig för ditt operativ system. När du har laddat ned öppnar du programmet. Du kommer att visas på Start skärmen, där du uppmanas att logga in eller skapa ett Postman-konto. Att skapa ett konto är valfritt och kan ignoreras genom att klicka på länken hoppa över och gå till app. Om du skapar ett konto sparas inställningarna för API-begäranden till Postman, vilket gör att du kan hämta dina begär Anden på andra datorer.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="Postman Start skärm som visar möjligheten att skapa ett konto eller hoppa över och gå till appen.":::

När du har skapat ett konto eller hoppat över att skapa ett konto bör du nu se Postman huvud fönster.

## <a name="creating-and-configuring-a-postman-collection"></a>Skapa och konfigurera en Postman-samling

Postman kan organisera förfrågningar på många sätt. I den här självstudien. Vi kommer att skapa en Postman-samling. Det gör du genom att välja knappen samlingar till vänster i programmet:

:::image type="content" source="media/postman/collections-tab.png" alt-text="Postman huvud skärm med fliken samlingar markerat.":::

När du har valt klickar du på Skapa ny samling för att starta processen för att skapa samlingen. En ny flik öppnas i mitten av Postman. Ge samlingen ett namn, oavsett vad du vill. Här är samlingen med namnet "ACS":

:::image type="content" source="media/postman/acs-collection.png" alt-text="Postman med en ACS-samling öppen och namnet på samlingen är markerat.":::

När samlingen har skapats och namngetts, är du redo att konfigurera den.

### <a name="adding-collection-variables"></a>Lägger till Collection-variabler

För att hantera autentisering och för att göra förfrågningar enklare, anger vi två Collection-variabler i den nyligen skapade ACS-samlingen. Dessa variabler är tillgängliga för alla begär anden i din ACS-samling. Om du vill komma igång med att skapa variabler går du till samlingens Variable's-flik.

:::image type="content" source="media/postman/variable-stab.png" alt-text="Postman med fliken variabler i ACS-samlingen.":::

Skapa två variabler en gång på fliken samling:
- nyckel – den här variabeln ska vara en av dina nycklar från nyckel sidan för Azure Communication Services i Azure Portal. Till exempel `oW...A==`.
- slut punkt – den här variabeln ska vara din Azure Communication Services-slutpunkt från nyckel sidan. **Se till att du tar bort avslutande snedstreck**. Till exempel `https://contoso.communication.azure.com`.

Ange värdena i kolumnen "inledande värde" på variabel skärmen. När du har angett trycker du på knappen "Behåll alla" precis ovanför tabellen till höger. När din Postman-skärm är korrekt konfigurerad bör det se ut ungefär så här:

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="Postman med en ACS-samlings variabler har ställts in korrekt.":::

Du kan lära dig mer om variabler genom att läsa [Postman dokumentation om dem](https://learning.postman.com/docs/sending-requests/variables).

### <a name="creating-a-pre-request-script"></a>Skapa ett skript för för begäran

Nästa steg är att skapa ett skript för för begäran i Postman. Ett skript för för begäran, är ett skript som körs före varje begäran i Postman och kan ändra eller ändra parametrarna för begäran för din räkning. Vi använder detta för att signera våra HTTP-begäranden så att de kan auktoriseras av ACS-tjänster. För ytterligare information om signerings kraven kan du [läsa vår guide om autentisering](/rest/api/communication/authentication).

Vi skapar det här skriptet i samlingen så att det körs på alla begär anden i samlingen. Det gör du genom att klicka på underfliken "pre-Request script" i fliken samling.

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="Postman med en ACS-samlings skript för förbegäran Sub-Tab valts.":::

På den här underordnade fliken kan du skapa ett skript för för begäran genom att ange det i text rutan nedan. Det kan vara lättare att skriva detta, i en fullständig kod redigerare som [Visual Studio Code](https://code.visualstudio.com/) innan du klistrar in den när den är klar. Vi kommer att gå igenom varje del av skriptet i den här självstudien. Du kan hoppa över till slutet om du vill kopiera den till Postman och komma igång. Vi börjar skriva skriptet.

### <a name="writing-the-pre-request-script"></a>Skriva skriptet för för begäran

Det första vi gör är att skapa en UTC-sträng (Coordinated Universal Time) och lägga till den i HTTP-huvudet "date". Vi lagrar även den här strängen i en variabel för att använda den senare vid signering:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

Härnäst ska vi Hasha begär ande texten med SHA 256 och placera den i `x-ms-content-sha256` sidhuvudet. Postman innehåller några [standard bibliotek](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries) för hashing och signering globalt, så vi behöver inte installera dem eller kräva dem:

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

Nu ska vi använda vår tidigare angivna slut punkts variabel för att fram värdet för HTTP-värd huvudet. Vi behöver göra detta eftersom värd rubriken inte anges förrän skriptet har bearbetats:

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

När den här informationen har skapats kan vi nu skapa strängen, som vi kommer att signera för HTTP-begäran, detta består av flera tidigare skapade värden:

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

Slutligen måste vi signera den här strängen med vår ACS-nyckel och sedan lägga till den i vår begäran i `Authorization` huvudet:

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>Det slutliga skriptet för för begäran

Det slutliga skriptet för för begäran bör se ut ungefär så här:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

Ange eller klistra in det slutliga skriptet i text rutan på fliken skript i för begäran:

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="Postman med en ACS-samlings skript för förbegäran angiven.":::

Tryck på CTRL + S eller tryck på knappen Spara för att spara skriptet till samlingen. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Postman Spara skript knapp för för begäran.":::

## <a name="creating-a-request-in-postman"></a>Skapa en begäran i Postman

Nu när allt har kon figurer ATS är vi redo att skapa en ACS-begäran i Postman. Kom igång genom att klicka på plus ikonen (+) bredvid samlingen ACS:

:::image type="content" source="media/postman/create-request.png" alt-text="Postman plus-knappen.":::

Då skapas en ny flik för vår begäran i Postman. När du har skapat det måste vi konfigurera det. Vi kommer att göra en begäran mot SMS-sändnings-API: et så glöm inte att läsa [dokumentationen för det här API: et för hjälp](/rest/api/communication/sms/send). Vi konfigurerar Postman-begäran.

Börja med att ställa in typ av begäran till `POST` och ange i `{{endpoint}}/sms?api-version=2021-03-07` fältet fråge-URL. Den här URL: en använder vår tidigare skapade `endpoint` variabel för att automatiskt skicka den till ACS-resursen.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="En Postman-begäran, med typen inställt på POST och URL: en har angetts korrekt.":::

Välj fliken brödtext i begäran och ändra sedan alternativ knappen under till "RAW". Till höger finns en listruta med texten "text", ändra den till JSON:

:::image type="content" source="media/postman/postman-json.png" alt-text="Ställa in begär ande texten som RAW och JSON":::

Detta konfigurerar begäran om att skicka och ta emot information i JSON-format.

I text området nedan måste du ange en begär ande text, den ska ha följande format:

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

För värdet "från" måste du [Hämta ett telefonnummer](../quickstarts/telephony-sms/get-phone-number.md) i ACS-portalen som tidigare nämnts. Ange det utan blank steg och med prefixet för din landskod. Exempel: `+15555551234`. Ditt "meddelande" kan vara det som du vill skicka, men det `Hello from ACS` är ett användbart exempel. Värdet "till" måste vara en telefon som du har åtkomst till som kan ta emot SMS-meddelanden. Att använda din egen mobil är en bra idé.

När vi har angetts måste vi spara denna begäran i ACS-samlingen som vi skapade tidigare. Detta säkerställer att den hämtar variabler och skript för förbegäran som vi skapade tidigare. Om du vill göra det klickar du på knappen "Spara" längst upp till höger i fråge fältet.

:::image type="content" source="media/postman/postman-save.png" alt-text="Knappen Spara för en Postman-begäran.":::

Då visas ett dialog fönster där du uppmanas att göra det, vad du vill kalla begäran och var du vill spara den. Du kan ge det något som du vill, men se till att du väljer ACS-samlingen i den nedre delen av dialog rutan:

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="Dialog rutan Postman Spara förfrågan med ACS-samlingen vald.":::

## <a name="sending-a-request"></a>Skicka en begäran

Nu när allt är konfigurerat bör du kunna skicka begäran och få ett SMS-meddelande på din telefon. Det gör du genom att se till att din begäran har skapats är markerad och sedan trycka på "Skicka"-knappen till höger:

:::image type="content" source="media/postman/postman-send.png" alt-text="En Postman-begäran med knappen Skicka markerad.":::

Om allt går bra bör du nu se svaret från ACS, vilket ska vara 202 status kod:

:::image type="content" source="media/postman/postman-202.png" alt-text="En Postman-begäran har skickats med en 202-status kod.":::

Mobil telefonen, som äger det nummer du angav i värdet "till", bör även ha fått ett SMS-meddelande. Nu har du en fungerande Postman-konfiguration som kan kommunicera med ACS-tjänster och skicka SMS-meddelanden.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska ACS-API: er](/rest/api/communication/) 
>  [Läs mer om autentisering](/rest/api/communication/authentication) 
>  [Läs mer om Postman](https://learning.postman.com/)

Du kanske också vill:

- [Lägga till chatt i din app](../quickstarts/chat/get-started.md)
- [Skapa åtkomsttoken för användare](../quickstarts/access-tokens.md)
- [Lär dig mer om klient-och server arkitektur](../concepts/client-and-server-architecture.md)
- [Läs mer om autentisering](../concepts/authentication.md)