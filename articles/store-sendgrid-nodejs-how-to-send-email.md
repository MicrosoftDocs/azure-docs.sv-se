---
title: Så här använder du e-posttjänsten SendGrid (Node.js) | Microsoft Docs
description: 'Lär dig hur du skickar e-post med e-posttjänsten SendGrid i Azure. Kod exempel som skrivits med Node.js-API: et.'
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015440"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Skicka e-post med hjälp av SendGrid från Node.js

Den här guiden visar hur du utför vanliga programmerings åtgärder med e-posttjänsten SendGrid i Azure. Exemplen skrivs med hjälp av Node.js-API: et. Scenarierna som beskrivs är att **Skapa e-post**, **skicka e-post**, **lägga till bilagor**, **använda filter** och **Uppdatera egenskaper**. Mer information om SendGrid och hur du skickar e-post finns i avsnittet [Nästa steg](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Vad är e-posttjänsten för SendGrid?

SendGrid är en [molnbaserad e-posttjänst] som tillhandahåller tillförlitlig [e-postleverans], skalbarhet och real tids analys tillsammans med flexibla API: er som underlättar anpassad integrering. Vanliga scenarier för SendGrid-användning är:

* Skicka kvitton automatiskt till kunder
* Administrera distributions listor för att skicka kunders månatliga e-Fliers och Special erbjudanden
* Samla in real tids mått för saker som blockerad e-post och kundens svars tid
* Generera rapporter för att hjälpa till att identifiera trender
* Vidarebefordra kund förfrågningar
* E-postmeddelanden från ditt program

Mer information finns i [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Referera till modulen SendGrid Node.js

SendGrid-modulen för Node.js kan installeras via Node Package Manager (NPM) med hjälp av följande kommando:

```bash
npm install sendgrid
```

Efter installationen kan du kräva modulen i ditt program med hjälp av följande kod:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

SendGrid-modulen exporterar **SendGrid** -och **e-** postfunktioner.
**SendGrid** ansvarar för att skicka e-post via webb-API, medan **e-post** kapslar in ett e-postmeddelande.

## <a name="how-to-create-an-email"></a>Gör så här: skapa ett e-postmeddelande

Att skapa ett e-postmeddelande med SendGrid-modulen innebär att först skapa ett e-postmeddelande med e-postfunktionen och sedan skicka det med hjälp av funktionen SendGrid. Följande är ett exempel på hur du skapar ett nytt meddelande med e-postfunktionen:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Du kan också ange ett HTML-meddelande för klienter som har stöd för det genom att ange HTML-egenskapen. Exempel:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Att ange både text-och HTML-egenskaperna ger korrekt återgång till text innehåll för klienter som inte har stöd för HTML-meddelanden.

Mer information om alla egenskaper som stöds av e-postfunktionen finns i [SendGrid-NodeJS][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Gör så här: Skicka ett e-postmeddelande

När du har skapat ett e-postmeddelande med hjälp av e-postfunktionen kan du skicka det med webb-API: et som tillhandahålls av SendGrid. 

### <a name="web-api"></a>Webb-API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Exemplet ovan visar att skicka i ett e-postobjekt och en callback-funktion, kan du också direkt anropa funktionen Send genom att ange e-postegenskaper direkt. Exempel:  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Gör så här: Lägg till en bilaga
Bifogade filer kan läggas till i ett meddelande genom att ange fil namn och sökväg (er) i egenskapen **Files** . I följande exempel visas hur du skickar en bilaga:

```javascript
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.',
    files: [
        {
            filename:     '',           // required only if file.content is used.
            contentType:  '',           // optional
            cid:          '',           // optional, used to specify cid for inline content
            path:         '',           //
            url:          '',           // == One of these three options is required
            content:      ('' | Buffer) //
        }
    ],
});
```

> [!NOTE]
> När du använder egenskapen **Files** måste filen vara tillgänglig via [FS. readfile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Om filen som du vill bifoga finns i Azure Storage, till exempel i en BLOB-behållare, måste du först kopiera filen till en lokal lagrings plats eller till en Azure-enhet innan den kan skickas som en bifogad fil med egenskapen **Files** .
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Gör så här: använda filter för att aktivera sid fötter och spårning

SendGrid tillhandahåller ytterligare e-postfunktioner genom att använda filter. Detta är inställningar som kan läggas till i ett e-postmeddelande för att aktivera vissa funktioner som att aktivera Klicka på spårning, Google Analytics, prenumerations spårning och så vidare. En fullständig lista över filter finns i [filter inställningar][Filter Settings].

Filter kan tillämpas på ett meddelande med hjälp av **filter** egenskapen.
Varje filter anges av en hash som innehåller filter specifika inställningar.
Följande exempel visar sidfoten och klickar på spårnings filter:

### <a name="footer"></a>Sidfot

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'footer': {
        'settings': {
            'enable': 1,
            'text/plain': 'This is a text footer.'
        }
    }
});

sendgrid.send(email);
```

### <a name="click-tracking"></a>Klicka på spåra

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'clicktrack': {
        'settings': {
            'enable': 1
        }
    }
});

sendgrid.send(email);
```

## <a name="how-to-update-email-properties"></a>Så här gör du: uppdatera e-postegenskaper

Vissa e-postegenskaper kan skrivas över med **setProperty** eller läggas till med hjälp av **addProperty**. Du kan till exempel lägga till fler mottagare genom att använda

```javascript
email.addTo('jeff@contoso.com');
```

eller ange ett filter med hjälp av

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Mer information finns i [SendGrid-NodeJS][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Så här gör du: Använd ytterligare SendGrid-tjänster

SendGrid erbjuder webbaserade API: er som du kan använda för att utnyttja ytterligare SendGrid-funktioner från ditt Azure-program. Fullständig information finns i [SENDGRID API-dokumentationen][SendGrid API documentation].

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i SendGrid-e-posttjänsten kan du följa dessa länkar för att lära dig mer.

* SendGrid Node.js modul-lagringsplats: [SendGrid-NodeJS][sendgrid-nodejs]
* SendGrid API-dokumentation: <https://sendgrid.com/docs>
* SendGrid Special erbjudande för Azure-kunder: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[molnbaserad e-posttjänst]: https://sendgrid.com/email-solutions
[transaktionell e-postleverans]: https://sendgrid.com/transactional-email
