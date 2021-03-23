---
title: Webhook-aktivitet i Azure Data Factory
description: Webhook-aktiviteten fortsätter inte att köra pipelinen förrän den verifierar den bifogade data uppsättningen med vissa villkor som användaren anger.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4c3ff5d7139f4167769f78aa858c7d7a693539a3
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785945"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-aktivitet i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En webhook-aktivitet kan styra körningen av pipeliner via din anpassade kod. Med webhook-aktiviteten kan kundernas kod anropa en slut punkt och skicka den till en återanrops-URL. Pipeline-körningen väntar på motringningen innan den fortsätter till nästa aktivitet.

> [!IMPORTANT]
> Med webhook-aktiviteten kan du nu visa fel status för platsen och anpassade meddelanden tillbaka till aktivitet och pipeline. Ange _reportStatusOnCallBack_ till true och inkludera _StatusCode_ och _fel_ i nytto lasten för återanrop. Mer information finns i avsnittet [Ytterligare anmärkningar](#additional-notes) .

## <a name="syntax"></a>Syntax

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "reportStatusOnCallBack": false,
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | --------
**name** | Namnet på webhook-aktiviteten. | Sträng | Ja |
**bastyp** | Måste vara inställt på "webhook". | Sträng | Ja |
**metodsignatur** | REST API metod för mål slut punkten. | Sträng. Den typ som stöds är "POST". | Ja |
**adresser** | Mål slut punkten och sökvägen. | En sträng eller ett uttryck med **resultType** -värdet för en sträng. | Ja |
**sidhuvud** | Huvuden som skickas till begäran. Här är ett exempel som anger språk och typ på en begäran: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | En sträng eller ett uttryck med **resultType** -värdet för en sträng. | Ja. En `Content-Type` rubrik som `"headers":{ "Content-Type":"application/json"}` är obligatorisk. |
**brödtext** | Representerar den nytto last som skickas till slut punkten. | Giltigt JSON eller ett uttryck med **resultType** -värdet för JSON. Se [nytto Last schema för begäran](./control-flow-web-activity.md#request-payload-schema) om schemat för nytto lasten för begäran. | Ja |
**anspråksautentisering** | Autentiseringsmetoden som används för att anropa slut punkten. De typer som stöds är "grundläggande" och "ClientCertificate". Mer information finns i [Autentisering](./control-flow-web-activity.md#authentication). Om autentisering inte krävs utelämnar du den här egenskapen. | En sträng eller ett uttryck med **resultType** -värdet för en sträng. | Inga |
**timeout** | Hur länge aktiviteten väntar på att återanropet som anges av **callBackUri** ska anropas. Standardvärdet är 10 minuter ("00:10:00"). Värdena har formatet TimeSpan *d*. *HH*:*mm*:*SS*. | Sträng | Inga |
**Rapportera status vid motringning** | Låter en användare rapportera den misslyckade statusen för en webhook-aktivitet. | Boolesk | Inga |

## <a name="authentication"></a>Autentisering

En webhook-aktivitet stöder följande typer av autentisering.

### <a name="none"></a>Inget

Om autentisering inte krävs ska du inte inkludera egenskapen **Authentication** .

### <a name="basic"></a>Grundläggande

Ange det användar namn och lösen ord som ska användas med grundläggande autentisering.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Klient certifikat

Ange det Base64-kodade innehållet i en PFX-fil och ett lösen ord.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Hanterad identitet

Använd Data Factory: s hanterade identitet för att ange den resurs-URI som åtkomsttoken begärs för. Använd för att anropa Azure Resource Management-API: et `https://management.azure.com/` . Mer information om hur hanterade identiteter fungerar finns i [Översikt över hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Om din data fabrik har kon figurer ATS med en git-lagringsplats måste du lagra dina autentiseringsuppgifter i Azure Key Vault för att kunna använda Basic eller autentisering med klient certifikat. Azure Data Factory lagrar inte lösen ord i git.

## <a name="additional-notes"></a>Ytterligare information

Data Factory skickar ytterligare egenskapen **callBackUri** i texten som skickas till URL-slutpunkten. Data Factory förväntar sig att denna URI ska anropas före det angivna tids gräns värdet. Om URI: n inte anropas, Miss lyckas aktiviteten med statusen "stängningsåtgärd".

Webhook-aktiviteten Miss lyckas när anropet till den anpassade slut punkten Miss lyckas. Eventuella fel meddelanden kan läggas till i återanrops texten och användas i en senare aktivitet.

För varje REST API-anrop har klienten nått tids gränsen om slut punkten inte svarar inom en minut. Det här är en standard metod för HTTP-användning. För att åtgärda det här problemet implementerar du ett 202-mönster. I det aktuella fallet returnerar slut punkten 202 (accepterad) och klient avsökningarna.

Tids gränsen på en minut på begäran har inget att göra med aktivitetens tids gräns. Den senare används för att vänta på återanropet som anges av **callbackUri**.

Texten som skickas tillbaka till återanrops-URI: n måste vara giltig JSON. Ange `Content-Type` sidhuvudet till `application/json` .

När du använder egenskapen **rapportera status på motringning** måste du lägga till följande kod i bröd texten när du gör återanropet:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Nästa steg

Se följande kontroll flödes aktiviteter som stöds av Data Factory:

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Kör pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Tills-aktivitet](control-flow-until-activity.md)