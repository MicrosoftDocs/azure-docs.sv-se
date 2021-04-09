---
title: Överordnade inställningar i Azure SignalR service
description: Få en introduktion till överordnade inställningar och protokoll för överordnade meddelanden.
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 6752a9564dc0d9351d1c21f5be14eb626186ac0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724069"
---
# <a name="upstream-settings"></a>Överordnade inställningar

Överordnad är en förhands gransknings funktion som gör att Azure SignalR service kan skicka meddelanden och anslutnings händelser till en uppsättning slut punkter i Server fritt läge. Du kan använda uppströms för att anropa en nav-metod från klienter i Server lös läge och låta slut punkter bli aviserade när klient anslutningar är anslutna eller frånkopplade.

> [!NOTE]
> Endast Server fritt läge kan konfigurera överordnade inställningar.

## <a name="details-of-upstream-settings"></a>Information om överordnade inställningar

Överordnade inställningar består av en lista över ordnings känsliga objekt. Varje objekt består av:

* En URL-mall som anger var meddelanden skickas till.
* En uppsättning regler.
* Konfigurationer för autentisering. 

När den angivna händelsen inträffar, kontrol leras ett objekts regler en i ordning. Meddelanden skickas till det första matchande objektets överordnade URL.

### <a name="url-template-settings"></a>Inställningar för URL-mall

Du kan Parameterisera webb adressen för att stödja olika mönster. Det finns tre fördefinierade parametrar:

|Fördefinierad parameter|Beskrivning|
|---------|---------|
|)| En hubb är ett koncept för Azure SignalR-tjänsten. En hubb är en isolerad enhet. Omfattningen av användare och meddelande leverans är begränsad till en hubb.|
|kategori| En kategori kan vara något av följande värden: <ul><li>**anslutningar**: anslutnings livs längds händelser. Den utlöses när en klient anslutning ansluts eller kopplas från. Den innehåller anslutna och frånkopplade händelser.</li><li>**meddelanden**: utlöses när klienter anropar en nav-metod. Den innehåller alla andra händelser, förutom de i kategorin **anslutningar** .</li></ul>|
|händelse| I kategorin **meddelanden** är en händelse målet i ett [anrops meddelande](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) som klienter skickar. För kategorin **anslutningar** används endast *anslutna* och *frånkopplade* .|

Dessa fördefinierade parametrar kan användas i URL-mönstret. Parametrarna ersätts med ett angivet värde när du utvärderar den överordnade URL: en. Exempel: 
```
http://host.com/{hub}/api/{category}/{event}
```
När en klient anslutning i hubben "chatt" är ansluten, skickas ett meddelande till denna URL:
```
http://host.com/chat/api/connections/connected
```
När en klient i "chatt"-hubben anropar Hub `broadcast` -metoden skickas ett meddelande till denna URL:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="key-vault-secret-reference-in-url-template-settings"></a>Key Vault hemlig referens i inställningar för URL-mall

URL: en för överordnad är inte kryptering i vilo läge. Om du har känslig information rekommenderar vi att du använder Key Vault för att spara dem där åtkomst kontroll har bättre försäkring. I princip kan du aktivera den hanterade identiteten för Azure SignalR-tjänsten och sedan bevilja Läs behörighet på en Key Vault-instans och använda Key Vault referens i stället för oformaterad text i URL-mönster för uppladdning.

1. Lägg till en tilldelad identitet eller användardefinierad identitet. Se [hur du lägger till hanterad identitet i Azure Portal](./howto-use-managed-identity.md#add-a-system-assigned-identity)

2. Bevilja hemlig Läs behörighet för den hanterade identiteten i åtkomst principerna i Key Vault. Se [tilldela en princip för Key Vault åtkomst med hjälp av Azure Portal](../key-vault/general/assign-access-policy-portal.md)

3. Ersätt din känsliga text med syntaxen `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}` i uppströms URL-mönstret.

> [!NOTE]
> Hemligt innehåll läses endast om när du ändrar de överordnade inställningarna eller ändrar den hanterade identiteten. Se till att du har beviljat Läs behörighet till den hanterade identiteten innan du använder Key Vault hemliga referensen.

### <a name="rule-settings"></a>Regel inställningar

Du kan ange regler för *nav regler*, *kategori regler* och *händelse regler* separat. Matchnings regeln stöder tre format. Ta händelse regler som exempel:
- Använd en asterisk (*) för att matcha alla händelser.
- Använd ett kommatecken (,) för att koppla flera händelser. Matchar till exempel `connected, disconnected` de anslutna och frånkopplade händelserna.
- Använd det fullständiga händelse namnet för att matcha händelsen. Matchar till exempel `connected` den anslutna händelsen.

> [!NOTE]
> Om du använder Azure Functions-och [signal utlösare](../azure-functions/functions-bindings-signalr-service-trigger.md)exponerar signal utlösaren en enda slut punkt i följande format: `<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>` .
> Du kan bara konfigurera **Inställningar för URL-mal len** till denna URL och behålla **regel inställningar** som standard. Se [SignalR service integration](../azure-functions/functions-bindings-signalr-service-trigger.md#signalr-service-integration) för information om hur du hittar `<Function_App_URL>` och `<API_KEY>` .

### <a name="authentication-settings"></a>Autentiseringsinställningar

Du kan konfigurera autentisering för varje överordnat inställnings objekt separat. När du konfigurerar autentisering anges en token i `Authentication` rubriken för det överordnade meddelandet. För närvarande stöder Azure Signaling-tjänsten följande autentiseringstyper:
- `None`
- `ManagedIdentity`

När du väljer `ManagedIdentity` måste du aktivera en hanterad identitet i Azure SignalR-tjänsten i förväg och även ange en resurs. Mer information finns i [Managed identiteter för Azure SignalR service](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-the-azure-portal"></a>Skapa överordnade inställningar via Azure Portal

1. Gå till Azure SignalR-tjänsten.
2. Välj **Inställningar** och växla **tjänst läge** till **Server** lös. De överordnade inställningarna visas:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Överordnade inställningar":::

3. Lägg till URL: er under **mönster för överordnad URL**. Sedan visas standardvärdet i inställningar som **Hub-regler** .
4. Om du vill ange inställningar för **Hubbs regler**, **händelse regler**, **kategori regler** och **överordnad autentisering**, väljer du värdet för **nav-regler**. En sida där du kan redigera inställningar visas:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Information om överordnade inställningar":::

5. Om du vill ange en **överordnad autentisering**, se till att du har aktiverat en hanterad identitet först. Välj sedan **Använd hanterad identitet**. Beroende på dina behov kan du välja valfria alternativ under **resurs-ID för autentisering**. Mer information finns i [Managed identiteter för Azure SignalR service](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-resource-manager-template"></a>Skapa överordnade inställningar via Resource Manager-mall

Ange egenskapen i egenskapen för att skapa överordnade inställningar med hjälp av en [Azure Resource Manager mall](../azure-resource-manager/templates/overview.md) `upstream` `properties` . Följande fragment visar hur du ställer in `upstream` egenskapen för att skapa och uppdatera överordnade inställningar.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="serverless-protocols"></a>Server lös protokoll

Azure SignalR service skickar meddelanden till slut punkter som följer följande protokoll. Du kan använda [SignalR tjänstens Utlös ande bindning](../azure-functions/functions-bindings-signalr-service-trigger.md) med Funktionsapp, som hanterar dessa protokoll åt dig.

### <a name="method"></a>Metod

POST

### <a name="request-header"></a>Begärandehuvud

|Name |Beskrivning|
|---------|---------|
|X-ASRS-Connection-ID |Anslutnings-ID för klient anslutningen.|
|X-ASRS – hubb |Hubben som klient anslutningen tillhör.|
|X-ASRS – kategori |Den kategori som meddelandet tillhör.|
|X-ASRS – händelse |Händelsen som meddelandet tillhör.|
|X-ASRS – signatur |En hash-baserad meddelande verifierings kod (HMAC) som används för verifiering. Se [signatur](#signature) för information.|
|X-ASRS-User-Claims |En grupp med anspråk för klient anslutningen.|
|X-ASRS-User-ID |Användar identiteten för den klient som skickar meddelandet.|
|X-ASRS – klient-fråga |Frågan om begäran när klienterna ansluter till tjänsten.|
|Autentisering |En valfri token när du använder `ManagedIdentity` . |

### <a name="request-body"></a>Begärandetext

#### <a name="connected"></a>Ansluten

Innehålls typ: Application/JSON

#### <a name="disconnected"></a>Frånkopplad

Innehålls typ: `application/json`

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|Fel |sträng |Fel meddelandet för en stängd anslutning. Tom när anslutningar stängs utan fel.|

#### <a name="invocation-message"></a>Anrops meddelande

Innehålls typ: `application/json` eller `application/x-msgpack`

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|InvocationId |sträng | En valfri sträng som representerar ett anrops meddelande. Hitta information om [anrop](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Mål |sträng | Samma som händelsen och samma som målet i ett [anrops meddelande](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding). |
|Argument |Objekt mat ris |En matris som innehåller argument som ska tillämpas på den metod som anges i `Target` . |

### <a name="signature"></a>Signatur

Tjänsten beräknar SHA256-koden för `X-ASRS-Connection-Id` värdet genom att använda både den primära åtkomst nyckeln och den sekundära åtkomst nyckeln som `HMAC` nyckel. Tjänsten ställer in den i `X-ASRS-Signature` rubriken när den gör HTTP-förfrågningar till uppladdning:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Nästa steg

- [Hanterade identiteter för Azure SignalR service](howto-use-managed-identity.md)
- [Azure Functions-utveckling och -konfiguration med Azure SignalR Service](signalr-concept-serverless-development-config.md)
- [Hantera meddelanden från SignalR-tjänsten (Utlös bindning)](../azure-functions/functions-bindings-signalr-service-trigger.md)
- [Exempel på Utlös ande bindning för SignalR tjänst](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)