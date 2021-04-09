---
title: 'Azure FarmBeats-API: er'
description: 'Lär dig om Azure FarmBeats-API: er som tillhandahåller jordbruks företag med ett standardiserat RESTful-gränssnitt med JSON-baserade svar.'
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 292374bcce90da042dc6bd672feaad3750759aa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674518"
---
# <a name="azure-farmbeats-apis"></a>Azure FarmBeats-API: er

I den här artikeln beskrivs Azure FarmBeats-API: erna. Azure FarmBeats-API: er tillhandahåller jordbruks företag med ett standardiserat RESTful-gränssnitt med JSON-baserade svar som hjälper dig att dra nytta av Azure FarmBeats-funktioner, till exempel:

- API: er för att få sensor, kamera, drönare, väder, satellit och granskade grunddata.
- Normalisering och contextualization av data över gemensamma data leverantörer.
- Schematiserade åtkomst och fråge funktioner på alla inmatade data.
- Automatisk generering av metadata som kan frågas, baserat på agronom-funktioner.
- Automatiskt genererade tidssekvensiska agg regeringar för skapande av snabb modell.
- Integrerad Azure Data Factory motor för att enkelt bygga anpassade pipeliner för data bearbetning.

## <a name="application-development"></a>Programutveckling

FarmBeats-API: erna innehåller teknisk dokumentation för Swagger. Information om alla API: er och deras motsvarande förfrågningar och svar finns i [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

I följande tabell sammanfattas alla objekt och resurser i FarmBeats Datahub:

| Objekt och resurser | Beskrivning
--- | ---|
Anläggningen | Server gruppen motsvarar en fysisk placering av intresse i FarmBeats-systemet. Varje server grupp har ett Server grupps namn och ett unikt server grupps-ID. |
Enhet  | Enheten motsvarar en fysisk enhet som finns i Server gruppen. Varje enhet har ett unikt enhets-ID. En enhet tillhandahålls vanligt vis till en Server grupp med ett Server grupp-ID.
DeviceModel  | DeviceModel motsvarar enhetens metadata, till exempel tillverkare och typ av enhet, som antingen är gateway eller nod.
Mäta  | Sensorn motsvarar en fysisk sensor som registrerar värden. En sensor är vanligt vis ansluten till en enhet med ett enhets-ID.
SensorModel  | SensorModel motsvarar sensorns metadata, till exempel tillverkaren, typen av sensor, som är antingen analog eller digital och sensor måttet, till exempel omgivande temperatur och tryck.
Telemetri  | Telemetri ger möjlighet att läsa telemetri meddelanden för ett visst sensor-och tidsintervall.
Jobb  | Jobb motsvarar ett arbets flöde med aktiviteter som körs i FarmBeats-systemet för att få önskade utdata. Varje jobb är associerat med ett jobb-ID och jobb typ.
JobType  | JobType motsvarar olika jobb typer som stöds av systemet. Systemdefinierade och användardefinierade jobb typer ingår.
ExtendedType  | ExtendedType motsvarar listan över system-och användardefinierade typer i systemet. ExtendedType hjälper dig att skapa en ny sensor, scen eller scen fil typ i FarmBeats-systemet.
Partner  | Partner motsvarar sensor-och bilder integration partner för FarmBeats.
Tegel  | Scenen motsvarar alla genererade utdata i kontexten för en Server grupp. Varje scen har ett scen-ID, en scen källa, en scen typ och ett Server grupp-ID som är kopplat till den. Varje scen-ID kan ha flera associerade scen-filer.
SceneFile |SceneFile motsvarar alla filer som genereras för en enda scen. Ett enda scen-ID kan ha flera associerade SceneFile-ID: n.
Regel  |Regel motsvarar ett villkor för grupprelaterade data som utlöser en avisering. Varje regel är i kontexten för en server grupps data.
Varning  | Aviseringen motsvarar ett meddelande som skapas när ett regel villkor uppfylls. Varje avisering är en regels kontext.
Roll definitions  | Roll definitions definierar tillåtna och otillåtna åtgärder för en roll.
RoleAssignment  |RoleAssignment motsvarar tilldelningen av en roll till en användare eller ett huvud namn för tjänsten.

### <a name="data-format"></a>Dataformat

JSON är ett gemensamt språk oberoende data format som ger en enkel text representation av godtyckliga data strukturer. Mer information finns på JSON- [webbplatsen](https://www.json.org/).

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering

HTTP-begäranden till REST API skyddas med Azure Active Directory (Azure AD).
Om du vill göra en autentiserad begäran till REST-API: erna kräver klient koden autentisering med giltiga autentiseringsuppgifter innan du kan anropa API: et. Autentiseringen samordnas mellan de olika aktörerna av Azure AD. Den ger klienten en åtkomsttoken som bevis på autentiseringen. Token skickas sedan i HTTP-Authorization-huvudet för REST API begär Anden. Mer information om Azure AD-autentisering finns i [Azure Active Directory](https://portal.azure.com) för utvecklare.

Åtkomsttoken måste skickas i efterföljande API-begäranden i avsnittet rubrik, som:

```http
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>Rubriker för HTTP-begäran

Här är de vanligaste begärandehuvuden som du måste ange när du gör ett API-anrop till Azure FarmBeats Datahub.


**Huvud** | **Beskrivning och exempel**
--- | ---
Content-Type  | Formatet för begäran (Content-Type: Application/ <format> ). För Azure FarmBeats Datahub-API: er är formatet JSON. Innehålls typ: Application/JSON
Auktorisering  | Anger den åtkomsttoken som krävs för att göra ett API-anrop. Auktorisering: Bearer <Access-Token>
Acceptera | Svars formatet. För Azure FarmBeats Datahub-API: er är formatet JSON. Acceptera: Application/JSON

### <a name="api-requests"></a>API-begäranden

Om du vill göra en REST API-begäran kombinerar du HTTP-metoden (GET, POST, tag eller DELETE), URL: en till API-tjänsten, URI: n till en resurs att fråga, skicka data till, uppdatera eller ta bort och lägger sedan till en eller flera HTTP-begärandehuvuden.

URL: en till API-tjänsten är din Datahub-URL, till exempel https:// \<yourdatahub-website-name> . azurewebsites.net.

Alternativt kan du inkludera frågeparametrar på GET-anrop för att filtrera, begränsa storleken på och sortera data i svaren.

Följande exempel förfrågan används för att hämta listan över enheter:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

De flesta GET-, POST-och parkera-anrop kräver en text för JSON-begäran.

Följande exempel förfrågan skapar en enhet. Den här begäran har indataports-JSON med begär ande texten.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Frågeparametrar

För REST GET-anrop kan du filtrera, begränsa storleken på och sortera data i ett API-svar genom att inkludera en eller flera frågeparametrar i begärande-URI: n. För frågeparametrar, se API-dokumentationen och enskilda GET-anrop.
När du till exempel frågar listan över enheter (GET Call på/Device) kan du ange följande frågeparametrar:

![Lista över enheter](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Felhantering

Azure FarmBeats Datahub-API: er returnerar standard-HTTP-fel. De vanligaste fel koderna är följande:

 |Felkod             | Beskrivning |
 |---                    | --- |
 |200                    | Klart |
 |201                    | Create (post) lyckades |
 |400                    | Felaktig begäran. Det finns ett fel i begäran. |
 |401                    | Tillstånd. Anroparen för API: et har inte behörighet att komma åt resursen. |
 |404                    | Resursen hittades inte |
 |5XX                    | Internt Server fel. Fel koderna som börjar med 5XX innebär att det finns ett fel på servern. Se Server loggar och följande avsnitt för mer information. |


Förutom vanliga HTTP-fel returnerar Azure FarmBeats Datahub-API: er också interna fel i följande format:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

I det här exemplet har det obligatoriska fältet "namn" inte angetts i nytto lasten när en grupp skapades. Det resulterande fel meddelandet skulle vara:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Lägg till användare eller program registreringar till Azure Active Directory

Azure FarmBeats-API: er kan nås av en användare eller en app-registrering i Azure Active Directory. Följ dessa steg om du vill skapa en app-registrering i Azure Active Directory:

1. Gå till [Azure Portal](https://portal.azure.com)och välj **Azure Active Directory**  >  **Appregistreringar**  >  **ny registrering**. Du kan också använda ett befintligt konto.
2. För ett nytt konto gör du följande:

    - Ange ett namn.
    - Välj **konton endast i den här organisations katalogen (en enda klient)**.
    - Använd standardvärdena i resten av fälten.
    - Välj **Register** (Registrera).

3. Gör följande i **översikts** fönstret ny och befintlig app Registration:

    - Avbilda **klient-ID och klient** **-ID**.
    - Gå till **certifikat och hemligheter** för att generera en ny klient hemlighet och avbilda **klient hemligheten**.
    - Gå tillbaka till **Översikt** och välj länken bredvid **hantera program i den lokala katalogen**.
    - Gå till **Egenskaper** för att avbilda **objekt-ID: t**.

4. Gå till din Datahub-Swagger (https:// <yourdatahub> . azurewebsites.net/swagger/index.html) och gör följande:
    - Gå till **RoleAssignment-API: et**.
    - Utför ett inlägg för att skapa ett **RoleAssignment** -objekt för det **objekt-ID** som du nyss skapade.
 
```json
{
  "roleDefinitionId": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "objectId": "objectId from step 3 above",
  "objectIdType": "ServicePrincipalId",
  "tenantId": "tenant id of your Azure subscription"
}
```

  > [!NOTE]
  > Mer information om hur du lägger till användare och Active Directory registrering finns i [Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md).

När du har slutfört de föregående stegen kan registreringen av appen (klienten) anropa Azure FarmBeats-API: erna genom att använda en åtkomsttoken via Bearer-autentisering.

Använd åtkomsttoken för att skicka den i efterföljande API-begäranden i rubrik avsnittet som:

```http
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```