---
title: 'Anvisningar: utveckla anpassade kommandon program – tal service'
titleSuffix: Azure Cognitive Services
description: I den här instruktionen får du lära dig hur du utvecklar och anpassar program för anpassade kommandon. Med anpassade kommandon är det enkelt att bygga omfattande röst kommandos program som är optimerade för röst-och första interaktions upplevelser och som passar bäst för slut för ande av aktiviteter eller kommando-och-kontroll-scenarier, särskilt väl matchade för Sakernas Internet (IoT), omgivande enheter.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 98c0459e0b67102458169147b1d39e98e2b3e2b1
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632961"
---
# <a name="develop-custom-commands-applications"></a>Utveckla anpassade kommandon program

I den här instruktionen får du lära dig hur du utvecklar och konfigurerar anpassade kommando program. Med anpassade kommandon är det enkelt att bygga omfattande röst kommandos program som är optimerade för röst-och första interaktions upplevelser och som passar bäst för slut för ande av aktiviteter eller kommando-och-kontroll-scenarier, särskilt väl matchade för Sakernas Internet (IoT), omgivande enheter.

I den här artikeln skapar du ett program som kan aktivera och inaktivera en TV, ange temperatur och ange ett larm. När du har skapat de här grundläggande kommandona omfattas följande alternativ för att anpassa kommandon:

* Lägga till parametrar till kommandon
* Lägga till konfigurationer till kommando parametrar
* Skapa interaktions regler
* Skapa mallar för språk skapande för tal svar
* Använda anpassad röst 

## <a name="create-application-with-simple-commands"></a>Skapa program med enkla kommandon

Börja med att skapa ett tomt program för anpassade kommandon. Mer information finns i [snabb](quickstart-custom-commands-application.md)starten. Den här gången skapar du ett tomt projekt i stället för att importera ett projekt.

1. I rutan **namn** anger du projekt namnet som `Smart-Room-Lite` (eller något annat alternativ).
1. I listan **språk** väljer du **engelska (USA)**.
1. Välj eller skapa en valfri LUIS-resurs.

   > [!div class="mx-imgBorder"]
   > ![Skapa ett projekt](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Uppdatera LUIS-resurser (valfritt)

Du kan uppdatera den redigerings resurs som du har valt i fönstret **nytt projekt** och ange en förutsägelse resurs. Förutsägelse resurs används för igenkänning när programmet för anpassade kommandon publiceras. Du behöver ingen förutsägelse resurs under utvecklings-och test faserna.

### <a name="add-turnon-command"></a>Lägg till TurnOn-kommando

I det tomma programmet för **Smart-Room-lite** anpassade kommandon som du nyss skapade, lägger du till ett enkelt kommando som bearbetar en uttryck, `turn on the tv` och svarar på meddelandet `Ok, turning the tv on` .

1. Skapa ett nytt kommando genom att välja **nytt kommando** längst upp i det vänstra fönstret. Det **nya kommando** fönstret öppnas.
1. Ange ett värde för fältet **namn** som **TurnON**.
1. Välj **Skapa**.

I fönstret i mitten visas olika egenskaper för kommandot. Du konfigurerar följande egenskaper för kommandot. Förklaringar av alla konfigurations egenskaper för ett kommando finns i [referenser](./custom-commands-references.md).

| Konfiguration            | Beskrivning                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Exempel på meningar** | Exempel yttranden användaren kan säga att utlösa det här kommandot                                                                 |
| **Parametrar**       | Information som krävs för att slutföra kommandot                                                                                |
| **Regler för slut för ande** | De åtgärder som ska vidtas för att utföra kommandot. Till exempel för att svara på användaren eller kommunicera med en annan webb tjänst. |
| **Interaktions regler**   | Ytterligare regler för att hantera mer detaljerade eller komplexa situationer                                                              |


> [!div class="mx-imgBorder"]
> ![Skapa ett kommando](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Lägg till exempel meningar

Vi börjar med **exempel menings** avsnittet och ger ett exempel på vad användaren kan säga.

1. Markera avsnittet **exempel meningar** i mitten av fönstret.
1. I det högra fönstret lägger du till exempel:

    ```
    turn on the tv
    ```

1.  Välj **Spara** längst upp i fönstret.

För tillfället har vi inga parametrar, så vi kan gå vidare till avsnittet **regler för slut för ande** .

#### <a name="add-a-completion-rule"></a>Lägg till en regel för slut för ande

Sedan måste kommandot ha en regel för slut för ande. Den här regeln anger användaren att en uppfyllelse åtgärd utförs. Om du vill läsa mer om regler och regler för slut för ande går du till [referenser](./custom-commands-references.md).

1. Välj standard regel för slut för **ande slutförd** och redigera den på följande sätt:

    
    | Inställning    | Föreslaget värde                          | Beskrivning                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Namn**       | ConfirmationResponse                  | Ett namn som beskriver syftet med regeln          |
    | **Villkor** | Inget                                     | Villkor som avgör när regeln kan köras    |
    | **Åtgärder**    | Skicka tal svar > enkel redigerare > första variationen > `Ok, turning the tv on` | Den åtgärd som ska vidtas när regel villkoret är sant |

   > [!div class="mx-imgBorder"]
   > ![Skapa ett tal svar](media/custom-commands/create-speech-response-action.png)

1. Spara åtgärden genom att välja **Spara** .
1. Gå tillbaka till avsnittet **regler för slut för ande** och välj **Spara** för att spara alla ändringar. 

    > [!NOTE]
    > Du behöver inte använda standard regeln för slut för ande som medföljer kommandot. Om det behövs kan du ta bort den befintliga regeln för standard slut för ande och lägga till en egen regel.

### <a name="add-settemperature-command"></a>Lägg till SetTemperature-kommando

Lägg nu till ytterligare en kommando **SetTemperature** som tar en enda uttryck, `set the temperature to 40 degrees` och svara på meddelandet `Ok, setting temperature to 40 degrees` .

Följ stegen som visas för kommandot **TurnON** för att skapa ett nytt kommando med hjälp av exempel meningen "**Ställ in temperatur på 40 grader**".

Redigera sedan **de befintliga reglerna för färdig komplettering** enligt följande:

| Inställning    | Föreslaget värde                          |
| ---------- | ---------------------------------------- |
| Namn  | ConfirmationResponse                  |
| Villkor | Inget                                     |
| Åtgärder    | Skicka tal svar > enkel redigerare > första variationen > `Ok, setting temperature to 40 degrees` |

Välj **Spara** för att spara alla ändringar i kommandot.

### <a name="add-setalarm-command"></a>Lägg till SetAlarm-kommando

Skapa ett nytt kommando **SetAlarm** med hjälp av exempel meningen "**Ange ett larm för 9 am imorgon**". Redigera sedan **de befintliga reglerna för färdig komplettering** enligt följande:

| Inställning    | Föreslaget värde                          |
| ---------- | ---------------------------------------- |
| Regelnamn  | ConfirmationResponse                  |
| Villkor | Inget                                     |
| Åtgärder    | Skicka tal svar > enkel redigerare > första variationen >`Ok, setting an alarm for 9 am tomorrow` |

Välj **Spara** för att spara alla ändringar i kommandot.

### <a name="try-it-out"></a>Prova

Testa beteendet med hjälp av panelen test chatt. Välj **träna** ikon överst i den högra rutan. När inlärningen är klar väljer du **test**. Prova följande uttryck-exempel via röst eller text:

- Du skriver: Ange temperatur till 40 grader
- Förväntat svar: OK, ställer in temperatur på 40 grader
- Du skriver: Aktivera TV: n
- Förväntat svar: OK och aktivera TV på
- Du skriver: Ange ett larm för 9 am imorgon
- Förväntat svar: OK, ange ett larm för 9 am imorgon

> [!div class="mx-imgBorder"]
> ![Testa med Web Chat](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> I test panelen kan du välja **Aktivera** information för information om hur den här rösten/text indatan bearbetades.

## <a name="add-parameters-to-commands"></a>Lägga till parametrar till kommandon

I det här avsnittet får du lära dig hur du lägger till parametrar till dina kommandon. Parametrar är information som krävs av kommandona för att slutföra en uppgift. I komplexa scenarier kan parametrar också användas för att definiera villkor som utlöser anpassade åtgärder.

### <a name="configure-parameters-for-turnon-command"></a>Konfigurera parametrar för kommandot TurnOn

Börja med att redigera det befintliga **TurnON** -kommandot för att aktivera och inaktivera flera enheter.

1. Nu när kommandot kommer att hantera både på-och-av-scenarier, byter du namn på kommandot till **TurnOnOff**.
   1. I det vänstra fönstret väljer du kommandot **TurnON** och väljer sedan knappen med tre punkter (...) bredvid **nytt kommando** överst i fönstret.
   
   1. Välj **Byt namn**. Ändra **namnet** till **TurnOnOff** i **namn på kommando** fönster.

1. Sedan lägger du till en ny parameter i det här kommandot som anger om användaren vill aktivera eller inaktivera enheten.
   1. Välj  **Lägg till** finns överst i den mellersta rutan. Välj **parameter** i list rutan.
   1. I den högra rutan, i avsnittet **parametrar** , lägger du till värdet i rutan **namn** som **mikrofonen**.
   1. Välj **obligatoriskt**. I fönstret **Lägg till svar för en obligatorisk parameter** väljer du **enkel redigerare**. I den **första varianten** lägger du till
        ```
        On or Off?
        ```
   1. Välj **Uppdatera**.

       > [!div class="mx-imgBorder"]
       > ![Skapa nödvändigt parameter svar](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Nu konfigurerar vi parameter egenskaperna. Förklaringar av alla konfigurations egenskaper för ett kommando finns i [referenser](./custom-commands-references.md). Konfigurera egenskaperna för parametern enligt följande:
      

       | Konfiguration      | Föreslaget värde     | Beskrivning                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Namn               | `OnOff`           | Ett beskrivande namn för parametern                                                                           |
       | Är global          | avmarkerat       | Kryss ruta som anger om ett värde för den här parametern används globalt för alla kommandon i programmet|
       | Obligatorisk           | analysera         | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs |
       | Svar för obligatorisk parameter      |Enkel redigerare > `On or Off?`      | En uppmaning om att fråga efter värdet för den här parametern när den inte är känd |
       | Typ               | Sträng          | Typ av parameter, till exempel Number, String, datum tid eller geografi   |
       | Konfiguration      | Acceptera fördefinierade indatavärden från intern katalog | För strängar begränsar detta indata till en uppsättning möjliga värden |
       | Fördefinierade indatavärden     | `on`, `off`           | Uppsättning möjliga värden och deras alias         |
       
        
   1. Om du vill lägga till fördefinierade indatavärden väljer du **Lägg till ett fördefinierat indata** och i fönstret **nytt objekt**  , skriver in **namnet** enligt vad som anges i tabellen ovan. I det här fallet använder vi inte alias, så du kan lämna det tomt.
   
      > [!div class="mx-imgBorder"]
      > ![Skapa parameter](media/custom-commands/create-on-off-parameter.png)

   1. Välj **Spara** för att spara alla konfigurationer för parametern.
 
#### <a name="add-subjectdevice-parameter"></a>Lägg till SubjectDevice-parameter 

1. Välj sedan **Lägg till** igen för att lägga till en andra parameter för att representera namnet på de enheter som kan styras med det här kommandot. Använd följande konfiguration.


    | Inställning            | Föreslaget värde       |
    | ------------------ | --------------------- |
    | Namn               | `SubjectDevice`         |
    | Är global          | avmarkerat             |
    | Obligatorisk           | analysera               |
    | Svar för obligatorisk parameter     | Enkel redigerare > `Which device do you want to control?`    | 
    | Typ               | Sträng                |          |
    | Konfiguration      | Acceptera fördefinierade indatavärden från intern katalog | 
    | Fördefinierade indatavärden | `tv`, `fan`               |
    | Alias ( `tv` )      | `television`, `telly`     |

1. Välj **Spara**

#### <a name="modify-example-sentences"></a>Ändra exempel meningar

För kommandon med parametrar är det bra att lägga till exempel meningar som beskriver alla möjliga kombinationer. Exempel:

* Fullständig parameter information – `turn {OnOff} the {SubjectDevice}`
* Partiell parameter information – `turn it {OnOff}`
* Ingen parameter information – `turn something`

Exempel på meningar med olika typer av information gör att de anpassade kommandona kan matcha både ensidiga lösningar och flera lösningar med delvis information.

Med det i åtanke redigerar du exempel meningarna så att de använder parametrarna enligt rekommendationerna nedan:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Välj **Spara**.

> [!TIP]
> I exempel menings redigeraren använder du klammerparenteser för att referera till dina parametrar. - `turn {OnOff} the {SubjectDevice}` Använd fliken för automatisk komplettering som backas upp av parametrarna som skapats tidigare.

#### <a name="modify-completion-rules-to-include-parameters"></a>Ändra regler för slut för ande för att inkludera parametrar

Ändra den befintliga **ConfirmationResponse** för slut för ande regeln.

1. I avsnittet **villkor** väljer du **Lägg till ett villkor**.
1. I fönstret **nytt villkor** väljer du **nödvändiga parametrar** i listan **typ** . I check listan nedan kontrollerar du både **mikrofonen** och **SubjectDevice**.
1. Låt **IsGlobal** vara avmarkerat.
1. Välj **Skapa**.
1. I avsnittet **åtgärder** redigerar du den befintliga **svars åtgärden skicka tal** genom att hovra över åtgärden och välja knappen Redigera. Den här gången ska du använda de nyligen skapade **mikrofonen** -och **SubjectDevice** -parametrarna

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Välj **Spara**.

Prova ändringarna genom att välja **träna** -ikonen överst i den högra rutan. När inlärningen är klar väljer du **test**. Ett **test av** programfönstret visas. Prova följande interaktioner.

- Inmatade: Stäng av TV
- Utdata: OK, stänga av TV
- Inmatade: Stäng av TV: n
- Utdata: OK, stänga av TV
- Inmatade: Stäng av
- Utdata: vilken enhet vill du styra?
- Inmatade: TV
- Utdata: OK, stänga av TV

### <a name="configure-parameters-for-settemperature-command"></a>Konfigurera parametrar för kommandot SetTemperature

Ändra kommandot **SetTemperature** så att det kan ställa in temperaturen enligt användaren.

Lägg till ny parameter **temperatur** med följande konfiguration

| Konfiguration      | Föreslaget värde     |
| ------------------ | ----------------|
| Namn               | `Temperature`           |
| Obligatorisk           | analysera         |
| Svar för obligatorisk parameter      | Enkel redigerare > `What temperature would you like?`
| Typ               | Antal          |


Redigera exemplet yttranden till följande värden.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Redigera befintliga regler för slut för Ande enligt följande konfiguration.

| Konfiguration      | Föreslaget värde     |
| ------------------ | ----------------|
| Villkor         | Obligatorisk parameter > temperatur           |
| Åtgärder           | Skicka tal svar > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-to-the-setalarm-command"></a>Konfigurera parametrar för SetAlarm-kommandot

Lägg till en parameter med namnet **datetime** med följande konfiguration.

   | Inställning                           | Föreslaget värde                     | 
   | --------------------------------- | ----------------------------------------|
   | Namn                              | `DateTime`                               |
   | Obligatorisk                          | analysera                                 |
   | Svar för obligatorisk parameter   | Enkel redigerare > `For what time?`            | 
   | Typ                              | DateTime                                |
   | Standardinställningar för datum                     | Om datumet saknas används idag            |
   | Standardvärden                     | Om tiden saknas i början av dagen     |


> [!NOTE]
> I den här artikeln har vi främst använt parameter typerna String, Number och DateTime. För en lista över alla parameter typer som stöds och deras egenskaper, gå till [referenser](./custom-commands-references.md).


Redigera exempel-yttranden till följande värden.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Redigera befintliga regler för slut för Ande enligt följande konfiguration.

   | Inställning    | Föreslaget värde                               |
   | ---------- | ------------------------------------------------------- |
   | Åtgärder    | Skicka tal svar – `Ok, alarm set for {DateTime}`  |

Testa alla tre kommandona tillsammans med yttranden som är relaterade till olika kommandon. Observera att du kan växla mellan olika kommandon.

- Inmatade: Ställ in ett larm
- Utdata: för vilken tid?
- Inmatade: slå på TV
- Utdata: OK, aktivera TV på
- Inmatade: Ställ in ett larm
- Utdata: för vilken tid?
- Inmatade: 17
- Utdata: OK, alarm set för 2020-05-01 17:00:00

## <a name="add-configurations-to-commands-parameters"></a>Lägga till konfigurationer i parametrar för kommandon

I det här avsnittet får du lära dig mer om konfiguration av avancerad parameter, inklusive:

 - Hur parameter värden kan tillhöra en uppsättning som definierats externt till anpassade kommando program
 - Lägga till verifierings satser i värdet för parametrarna

### <a name="configure-parameter-as-external-catalog-entity"></a>Konfigurera parametern som extern katalog-entitet

Med anpassade kommandon kan du konfigurera sträng typ parametrar för att referera till externa kataloger som är värdbaserade över en webb slut punkt. På så sätt kan du uppdatera den externa katalogen oberoende utan att göra ändringar i programmet för anpassade kommandon. Den här metoden är användbar i fall där katalog posterna kan vara stora i tal.

Återanvänd parametern **SubjectDevice** från kommandot **TurnOnOff** . Den aktuella konfigurationen för den här parametern **accepterar fördefinierade indata från den interna katalogen**. Detta avser en statisk lista över enheter som definieras i parameter konfigurationen. Vi vill flytta ut det här innehållet till en extern data källa som kan uppdateras oberoende av varandra.

Börja med att lägga till en ny webb slut punkt. Gå till avsnittet **webb slut punkter** i det vänstra fönstret och Lägg till en ny webb slut punkt med följande konfiguration.

| Inställning | Föreslaget värde |
|----|----|
| Namn | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Metod | GET |


Om det föreslagna värdet för URL inte fungerar för dig måste du konfigurera och vara värd för en enkel webb slut punkt som returnerar en JSON-enhet som består av en lista över de enheter som kan kontrol leras. Webb slut punkten ska returnera en JSON-formaterad på följande sätt:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Gå sedan till sidan parameter inställningar för **SubjectDevice** och ändra egenskaperna till följande.

| Inställning | Föreslaget värde |
| ----| ---- |
| Konfiguration | Acceptera fördefinierade indata från extern katalog |                               
| Katalog slut punkt | getDevices |
| Metod | GET |

Välj sedan **Spara**.

> [!IMPORTANT]
> Du kan inte se ett alternativ för att konfigurera en parameter att acceptera indata från en extern katalog om du inte har angett webb slut punkten i avsnittet **webb slut punkt** i det vänstra fönstret.

Prova genom att välja **träna** och vänta på slut för ande av utbildning. När inlärningen är klar väljer du **testa** och prova några interaktioner.

* Inmatade: Aktivera
* Utdata: vilken enhet vill du styra?
* Inmatade: lampor
* Utdata: OK, aktivera indikeringar

> [!NOTE]
> Observera hur du kan styra alla enheter som finns på webb slut punkten nu. Du måste fortfarande träna programmet för att testa de nya ändringarna och publicera programmet på nytt.

### <a name="add-validation-to-parameters"></a>Lägg till validering i parametrar

**Valideringar** är tillämpliga för vissa parameter typer som gör att du kan konfigurera begränsningar för parameterns värde och fråga efter korrigering om värden inte faller inom begränsningarna. En fullständig lista över parameter typer som utökar validerings konstruktionen finns i [referenser](./custom-commands-references.md)

Testa verifieringar med kommandot **SetTemperature** . Använd följande steg för att lägga till en validering för **temperatur** parametern.

1. Välj kommandot **SetTemperature** i det vänstra fönstret.
1. Välj  **temperatur** i fönstret i mitten.
1. Välj **Lägg till en verifiering** i den högra rutan.
1. I fönstret **ny validering** konfigurerar du verifiering enligt följande och väljer **skapa**.


    | Parameter konfiguration | Föreslaget värde | Beskrivning |
    | ---- | ---- | ---- |
    | Minvärde | `60` | För Number-parametrar kan det minsta värdet som denna parameter anta |
    | Maxvärde | `80` | För Number-parametrar kan det högsta värdet som den här parametern anta |
    | Haveri svar |  Enkel redigerare > första varianten > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Fråga om du vill ange ett nytt värde om valideringen Miss lyckas |

    > [!div class="mx-imgBorder"]
    > ![Lägg till en intervall validering](media/custom-commands/add-validations-temperature.png)

Prova genom att välja ikonen **tåg** som finns överst i den högra rutan. När inlärningen är klar väljer du **test** och prova några interaktioner:

- Inmatade: Ange temperatur till 72 grader
- Utdata: OK, ställa in temperatur på 72 grader
- Inmatade: Ange temperatur till 45 grader
- Utdata: det går bara att ange temperatur mellan 60 och 80 grader
- Inmatade: gör det till 72 grader i stället
- Utdata: OK, ställa in temperatur på 72 grader

## <a name="add-interaction-rules"></a>Lägga till interaktionsregler

Interaktions regler är *ytterligare regler* för att hantera vissa eller komplexa situationer. Även om du är kostnads fri att skapa egna anpassade interaktions regler, i det här exemplet använder du interaktions regler för följande mål scenarier:

* Bekräfta kommandon
* Lägga till en enstaka stegs korrigering i kommandon

Om du vill veta mer om interaktions regler går du till avsnittet [referenser](./custom-commands-references.md) .

### <a name="add-confirmations-to-a-command"></a>Lägg till bekräftelser i ett kommando

Om du vill lägga till en bekräftelse använder du kommandot **SetTemperature** . För att få en bekräftelse skapar du interaktions regler med hjälp av följande steg.

1. Välj kommandot **SetTemperature** i det vänstra fönstret.
1. Lägg till interaktions regler genom att välja **Lägg till** i fönstret i mitten. Välj sedan **interaktions regler**  >  **Bekräfta kommando**.

    Den här åtgärden lägger till tre interaktions regler som ber användaren att bekräfta datum och tid för larmet och förväntar sig en bekräftelse (Ja/Nej) för nästa turn.

    1. Ändra den **bekräftade kommando** interaktions regeln enligt följande konfiguration:
        1. Byt namn på **namn** för att **Bekräfta temperatur**.
        1. Lägg till ett nytt villkor som temperatur för **obligatoriska parametrar**  >  .
        1. Lägg till en ny åtgärd som **typ**  >  **Skicka tal svar**  >  **är du säker på att du vill ställa in temperaturen som {temperatur} grader?**
        1. Lämna standardvärdet för att **Bekräfta bekräftelse från användaren** i **förväntningar** -avsnittet.
      
         > [!div class="mx-imgBorder"]
         > ![Skapa nödvändigt parameter svar](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Ändra **bekräftelse regeln lyckades** för att hantera en lyckad bekräftelse (användaren säger ja).
      
          1. Ändring av **namn** till **bekräftelse temperatur lyckades**.
          1. Lämna den redan befintliga **bekräftelsen lyckades** .
          1. Lägg till ett nytt villkor som **typ**  >  **obligatoriska parametrar**  >  **temperatur**.
          1. Lämna standardvärdet för **efter körnings tillstånd** som **regler för att köra slut för ande**.

    1. Ändra bekräftelse regeln för **nekad bekräftelse** för att hantera scenarier när en bekräftelse nekas (användaren har nej).

          1. Ändra **namn** till **bekräftelse temperatur nekad**.
          1. Lämna den redan befintliga **bekräftelsen nekades** .
          1. Lägg till ett nytt villkor som **typ**  >  **obligatoriska parametrar**  >  **temperatur**.
          1. Lägg till en ny åtgärd som **typ**  >  **Skicka tal svar**  >  **inget problem. Vilken temperatur sedan?**
          1. Lämna standardvärdet för **post-Execution-tillstånd** som **väntar på användarens indata**.

> [!IMPORTANT]
> I den här artikeln har du använt den inbyggda bekräftelse funktionen. Du kan också lägga till interaktions regler manuellt en i taget.
   
Prova ändringarna genom att välja **träna**, vänta tills utbildningen har slutförts och välj **test**.

- **Inmatade**: Ange temperatur till 80 grader
- **Utdata**: är du säker på att du vill ställa in temperaturen som 80 grader?
- **Inmatade**: Nej
- **Utdata**: inga problem. Vilken temperatur sedan?
- **Inmatade**: 72 grader
- **Utdata**: är du säker på att du vill ställa in temperaturen som 72 grader?
- **Inmatade**: Ja
- **Utdata**: OK, ställa in temperatur på 83 grader

### <a name="implement-corrections-in-a-command"></a>Implementera korrigeringar i ett kommando

I det här avsnittet konfigurerar du en steg korrigering som används när åtgärden redan har körts. Du ser också ett exempel på hur en korrigering aktive ras som standard om kommandot inte är uppfyllt ännu. Lägg till en korrigering när kommandot inte är slutfört genom att lägga till den nya parametern **AlarmTone**.

Välj kommandot **SetAlarm** i det vänstra fönstret och Lägg till den nya parametern **AlarmTone**.
        
- **Namn**  >  **AlarmTone**
- **Typ**  >  **Sträng**
- **Standardvärde**  >  **CHIMES**
- **Konfiguration**  >  **Acceptera fördefinierade indatavärden från den interna katalogen**
- **Fördefinierade indatavärden**  >  **CHIMES**, **Jingle** och **ECHO** som enskilda fördefinierade indata


Uppdatera sedan svaret för **datetime** -parametern till **redo att ställa in alarm med tonen som {AlarmTone}. För vilken tid?**. Ändra sedan regeln för slut för Ande enligt följande:

1. Välj den befintliga regeln för slut för ande **ConfirmationResponse**.
1. Hovra över den befintliga åtgärden i den högra rutan och välj **Redigera**.
1. Uppdatera tal svaret till **OK, alarm inställt för {DateTime}. Alarm tonen är {AlarmTone}.**

> [!IMPORTANT]
> Alarm tonen kan ändras utan någon explicit konfiguration i ett pågående kommando, till exempel när kommandot inte har slutförts än. *En korrigering är aktive rad som standard för alla kommando parametrar, oavsett vilket antal som finns om kommandot ännu är uppfyllt.*

#### <a name="correction-when-command-is-completed"></a>Korrigering när kommandot har slutförts

Plattformen för anpassade kommandon ger också möjlighet att utföra en enstaka stegs korrigering även när kommandot har slutförts. Den här funktionen är inte aktive rad som standard. Det måste konfigureras explicit. Använd följande steg för att konfigurera en korrigering i ett steg.

1. I kommandot **SetAlarm** lägger du till en interaktions regel av typen **Uppdatera föregående-kommando** för att uppdatera det tidigare angivna larmet. Byt namn på standard **namnet** för interaktions regeln för att **uppdatera tidigare larm**.
1. Lämna standard villkoret **föregående-kommandot måste uppdateras** i befintligt skick.
1. Lägg till ett nytt villkor **som**  >  **obligatorisk parameter för parametern**  >  **datetime**.
1. Lägg till en ny åtgärd som **typ**  >  **Skicka Speech Response**  >  **Simple Editor**  >  **uppdaterar föregående alarm tid till {DateTime}.**
1. Lämna standardvärdet för **post-Execution-tillstånd** när **kommandot har slutförts**.

Prova ändringarna genom att välja **träna**, vänta tills utbildningen har slutförts och välj **test**.

- **Inmatade**: Ange ett larm.
- **Utdata**: redo att ställa in alarm med tonen som CHIMES. För vilken tid?
- **Inmatade**: Ställ in ett larm med tonen som Jingle för 9 am imorgon.
- **Output**: OK, alarm anges för 2020-05-21 09:00:00. Alarm tonen är Jingle.
- **Inmatade**: Nej, 8.
- **Utdata**: uppdaterar föregående larm tid till 2020-05-29 08:00.

> [!NOTE]
> I ett verkligt program, i avsnittet **åtgärder** i den här korrigerings regeln, måste du också skicka tillbaka en aktivitet till klienten eller anropa en http-slutpunkt för att uppdatera larm tiden i systemet. Den här åtgärden bör enbart vara ansvarig för att uppdatera alarm tiden och inte andra attribut för kommandot. I det här fallet är det en larm signal.

## <a name="add-language-generation-templates-for-speech-responses"></a>Lägga till mallar för att skapa språk för talsvar

Med språkgenerationens mallar kan du anpassa svaren som skickas till klienten och introducera varians i svaren. Anpassningen av språket kan uppnås genom att:

* Användning av mallar för språk skapande
* Användning av anpassningsbara uttryck

Mallar för anpassade kommandon baseras på BotFramework: s [LG-mallar](/azure/bot-service/file-format/bot-builder-lg-file-format#templates). Eftersom anpassade kommandon skapar en ny LG-mall vid behov (det vill säga för tal svar i parametrar eller åtgärder) behöver du inte ange namnet på LG-mallen. Så i stället för att definiera mallen som:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Du behöver bara definiera bröd texten i mallen utan namnet, enligt följande.

> [!div class="mx-imgBorder"]
> ![exempel på template Editor](./media/custom-commands/template-editor-example.png)


Den här ändringen introducerar variationer i de tal svar som skickas till klienten. För samma uttryck skulle det motsvarande tal svaret slumpmässigt plockas ut från de tillhandahållna alternativen.

Genom att dra nytta av LG-mallar kan du också definiera komplexa tal svar för kommandon med hjälp av anpassade uttryck. Du kan läsa mer om [LG templates-formatet](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) . Anpassade kommandon som standard har stöd för alla funktioner med följande mindre skillnader:

* I entiteterna för LG-mallar visas som $ {entityName}. I anpassade kommandon använder vi inte entiteter, men parametrar kan användas som variabler med någon av dessa representationer $ {parameterName} eller {parameterName}
* Mallens sammansättning och expansion stöds inte i anpassade kommandon. Detta beror på att du aldrig redigerar `.lg` filen direkt, utan bara svaren på automatiskt skapade mallar.
* Anpassade funktioner som matas in av LG stöds inte i anpassade kommandon. Fördefinierade funktioner stöds fortfarande.
* Alternativ (Strict, replaceNull & lineBreakStyle) stöds inte i anpassade kommandon.

### <a name="add-template-responses-to-turnonoff-command"></a>Lägg till mallar svar till kommandot TurnOnOff

Ändra kommandot **TurnOnOff** för att lägga till en ny parameter med följande konfiguration:

| Inställning            | Föreslaget värde       | 
| ------------------ | --------------------- | 
| Namn               | `SubjectContext`         | 
| Är global          | avmarkerat             | 
| Obligatorisk           | avmarkerat               | 
| Typ               | Sträng                |
| Standardvärde      | `all` |
| Konfiguration      | Acceptera fördefinierade indatavärden från intern katalog | 
| Fördefinierade indatavärden | `room`, `bathroom`, `all`|

#### <a name="modify-completion-rule"></a>Ändra regel för slut för ande

Redigera avsnittet **åtgärder** i den befintliga regeln för slut för ande av **ConfirmationResponse**. I popup-fönstret **Redigera åtgärd** växlar du till redigeraren för **mallar** och ersätter texten med följande exempel.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

**Träna** och **testa** ditt program på följande sätt. Observera variationen av svar på grund av användning av flera alternativ för mallnamnet, och även användning av anpassningsbara uttryck.

* Inmatade: slå på TV
* Utdata: OK, aktivera TV på
* Inmatade: slå på TV
* Utdata: färdig, aktive rad på TV
* Inmatade: Stäng av lamporna
* Utdata: OK, vilket stänger av alla lampor
* Inmatade: inaktivera rums lampor
* Utdata: OK, inaktivera rums belysningen

## <a name="use-custom-voice"></a>Använda anpassad röst

Ett annat sätt att anpassa anpassade kommando svar är att välja en anpassad röst för utdata. Använd följande steg för att växla standard rösten till en anpassad röst.

1. I programmet anpassade kommandon väljer du **Inställningar** i det vänstra fönstret.
1. Välj **anpassad röst** från fönstret i mitten.
1. Välj önskad anpassad eller offentlig röst i tabellen.
1. Välj **Spara**.

> [!div class="mx-imgBorder"]
> ![Exempel på meningar med parametrar](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - För **offentliga röster** är **neurala-typer** bara tillgängliga för vissa regioner. För att kontrol lera tillgängligheten, se [standard-och neurala röster efter region/slut punkt](./regions.md#standard-and-neural-voices).
> - För **anpassade röster** kan de skapas från sidan anpassat röst projekt. Se [Kom igång med anpassad röst](./how-to-custom-voice.md).

Programmet kommer nu att svara i den valda rösten i stället för standard rösten.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [integrerar dina anpassade kommandon](how-to-custom-commands-setup-speech-sdk.md) med en klient-app med hjälp av tal-SDK.
* [Konfigurera kontinuerlig distribution](how-to-custom-commands-deploy-cicd.md) för dina anpassade kommandon-program med Azure DevOps. 
                      