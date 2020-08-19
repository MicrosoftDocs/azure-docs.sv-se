---
title: 'Självstudie: Azure Notebook – Personanpassare'
titleSuffix: Azure Cognitive Services
description: Den här självstudien simulerar en studie slinga _system i en Azure-anteckningsbok, som föreslår vilken typ av kaffe en kund ska beställa. Användarna och deras inställningar lagras i en användar data uppsättning. Information om kaffeet är också tillgänglig och lagrad i en kaffe data uppsättning.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-python
ms.openlocfilehash: 0cf2aa504fd7a36a55740b3a59eeb3759e67f469
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509917"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Självstudie: använda en Personanpassare i Azure Notebook

I den här självstudien körs en själv studie slinga i en Azure-anteckningsbok, som demonstrerar slut punkt till slut punkt för en själv studie slinga.

Slingan föreslår vilken typ av kaffe en kund ska beställa. Användarna och deras inställningar lagras i en användar data uppsättning. Information om kaffeet lagras i en kaffe data uppsättning.

## <a name="users-and-coffee"></a>Användare och kaffe

Den bärbara datorn, som simulerar användar interaktion med en webbplats, väljer en slumpmässig användare, tid på dag och typ av väder från data uppsättningen. En sammanfattning av användar informationen är:

|Kunder – kontext funktioner|Tider på dagen|Typer av väder|
|--|--|--|
|Anna<br>Bob<br>Cathy<br>Dave|Morgon<br>Totalt<br>Kvällen|Solig<br>RAINY<br>Tallar|

För att hjälpa personanpassa att lära sig, med tiden, vet _systemet_ även information om kaffe valet för varje person.

|Funktioner för kaffe åtgärder|Typer av temperatur|Ursprungs platser|Typer av rostade|Organisk|
|--|--|--|--|--|
|Cappacino|Frekvent|Kenya|Mörk|Organisk|
|Kall Brew|Kall|Brasilien|Ljus|Organisk|
|Iced mocha|Kall|Etiopien|Ljus|Inte ekologisk|
|Latte|Frekvent|Brasilien|Mörk|Inte ekologisk|

**Syftet** med den personliga slingan är att hitta den bästa matchningen mellan användarna och kaffe så mycket som möjligt.

Koden för den här själv studie kursen finns i [GitHub-lagringsplatsen för personanpassa exempel](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook).

## <a name="how-the-simulation-works"></a>Så här fungerar simuleringen

I början av det operativ system som körs, lyckas förslagen från Personanpassan bara mellan 20% och 30%. Detta lyckades anges av belöningen som skickas tillbaka till personens belönings-API, med en poäng på 1. Efter vissa ranknings-och belönings anrop förbättras systemet.

Efter den första begäran kör du en offline-utvärdering. Detta gör att en Personanpassare kan granska data och föreslå en bättre inlärnings princip. Tillämpa den nya inlärnings principen och kör antecknings boken igen med 20% av föregående antal förfrågningar. Slingan fungerar bättre med den nya inlärnings principen.

## <a name="rank-and-reward-calls"></a>Ranknings-och belönings samtal

För var och en av de tusen anropen till tjänsten personanpassa skickar Azure-anteckningsbok **ranknings** förfrågan till REST API:

* Ett unikt ID för händelsen rang/begäran
* Kontext funktioner – ett slumpmässigt val av användare, väder och tid på dagen – simulera en användare på en webbplats eller mobil enhet
* Åtgärder med funktioner – _alla_ kaffe data – från vilka personligare gör ett förslag

Systemet tar emot begäran och jämför sedan förutsägelsen med användarens kända val för samma tid på dag och väder. Om det kända valet är samma som det förväntade valet, skickas **belöningen** 1 tillbaka till personanpassaren. Annars är belöningen som skickas tillbaka 0.

> [!Note]
> Detta är en simulering så att algoritmen för belöningen är enkel. I ett verkligt scenario bör algoritmen använda affärs logik, möjligt vis med vikt för olika aspekter av kundens upplevelse, för att fastställa belönings poängen.


## <a name="prerequisites"></a>Krav

* Ett [Azure Notebook](https://notebooks.azure.com/) -konto.
* En [Azures personanpassa resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
    * Om du redan har använt personanpassa resursen, se till att [Rensa data](how-to-settings.md#clear-data-for-your-learning-loop) i Azure Portal för resursen.
* Ladda upp alla filer för [det här exemplet](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) i ett Azure Notebook-projekt.

Fil beskrivningar:

* [Personanpassar. ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) är den Jupyter Notebook för den här självstudien.
* [Användar data uppsättningen](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) lagras i ett JSON-objekt.
* En [kaffe data uppsättning](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) lagras i ett JSON-objekt.
* [Exempel-JSON](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) är det förväntade formatet för en post-begäran till ranknings-API: et.

## <a name="configure-personalizer-resource"></a>Konfigurera en personanpassa resurs

I Azure Portal konfigurerar du din [personanpassa resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) med **frekvensen Uppdatera modell** till 15 sekunder och en **belönings vänte tid** på 15 sekunder. Dessa värden finns på **[konfigurations](how-to-settings.md#configure-service-settings-in-the-azure-portal)** sidan.

|Inställning|Värde|
|--|--|
|uppdaterings modell frekvens|15 sekunder|
|vänte tid för belöning|15 sekunder|

Dessa värden har en mycket kort varaktighet för att kunna visa ändringar i den här självstudien. Dessa värden bör inte användas i ett produktions scenario utan att kontrol lera att de uppnår ditt mål med din personliga loop.

## <a name="set-up-the-azure-notebook"></a>Konfigurera Azure Notebook

1. Ändra kärnan till `Python 3.6` .
1. Öppna filen `Personalizer.ipynb`.

## <a name="run-notebook-cells"></a>Kör Notebook-celler

Kör varje körbar cell och vänta tills den returneras. Du vet att det är klart när hakparenteserna bredvid cellen visar ett tal i stället för en `*` . I följande avsnitt förklaras hur varje cell program mässigt och vad som är förväntat för utdata.

### <a name="include-the-python-modules"></a>Ta med python-moduler

Ta med de nödvändiga python-modulerna. Cellen har inga utdata.

```python
import json
import matplotlib.pyplot as plt
import random
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Ange namn på en Personanpassare resurs nyckel och namn

Från Azure Portal hittar du din nyckel och slut punkt på **Start** sidan för din personanpassa resurs. Ändra värdet för `<your-resource-name>` till namnet på din personanpassa resurs. Ändra värdet för `<your-resource-key>` till din personanpassa nyckel.

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Skriv ut aktuellt datum och aktuell tid
Använd den här funktionen för att notera start-och slut tiderna för den iterativa funktionen, iterationer.

Dessa celler har inga utdata. Funktionen skriver ut aktuellt datum och tidpunkt när det anropas.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Hämta den senaste uppdaterings tiden för modellen

När funktionen, `get_last_updated` , anropas, skriver funktionen ut det senast ändrade datum och tidpunkt då modellen uppdaterades.

Dessa celler har inga utdata. Funktionen utvärderar den senaste modell inlärnings datumet när det anropades.

Funktionen använder en GET-REST API för att [Hämta modell egenskaper](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties).

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):

    print('-----checking model')

    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])

    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>Hämta princip-och tjänst konfiguration

Verifiera tjänstens status med dessa två REST-anrop.

Dessa celler har inga utdata. Funktionen matar ut tjänst värden när den anropas.

```python
def get_service_settings():

    print('-----checking service settings')

    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)

    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>Skapa URL: er och läsa JSON-datafiler

Den här cellen

* skapar de URL: er som används i REST-anrop
* ställer in säkerhets huvud med hjälp av din personanpassa resurs nyckel
* anger det slumpmässiga Dirigerings-ID: t för rang händelse-ID
* läser i JSON-datafilerna
* anrops `get_last_updated` metod – inlärnings principen har tagits bort i exempel på utdata
* anrops `get_service_settings` metod

Cellen har utdata från anropet till `get_last_updated` och `get_service_settings` functions.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None
rankactionsjsonobj = None
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())

with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())

get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Kontrol lera att utdata `rewardWaitTime` och `modelExportFrequency` båda är inställda på 15 sekunder.

```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>Felsöka det första REST-anropet

Den här föregående cellen är den första cellen som anropar till Personanpassaren. Se till att REST-statuskoden i utdata är `<Response [200]>` . Om du får ett fel meddelande, till exempel 404, men du är säker på att resurs nyckeln och namnet är rätt, laddar du om antecknings boken.

Se till att antalet kaffe och användare är båda fyra. Om du får ett fel meddelande kontrollerar du att du har laddat upp alla tre JSON-filer.

### <a name="set-up-metric-chart-in-azure-portal"></a>Konfigurera mått diagram i Azure Portal

Senare i den här självstudien visas den tids krävande processen för 10 000-begäranden från webbläsaren med en uppdaterings text ruta. Det kan vara lättare att se i ett diagram eller som en total summa när den tids krävande processen slutar. Om du vill visa den här informationen använder du de mått som medföljer resursen. Du kan skapa diagrammet nu när du har slutfört en begäran till tjänsten och sedan uppdatera diagrammet regelbundet medan den tids krävande processen körs.

1. I Azure Portal väljer du din Personanpassare resurs.
1. I resurs navigeringen väljer du **mått** under övervakning.
1. Välj **Lägg till mått**i diagrammet.
1. Resurs-och mått namn området har redan angetts. Du behöver bara välja Mät värdet för **lyckade anrop** och agg regeringen av **Sum**.
1. Ändra tids filtret till de senaste 4 timmarna.

    ![Skapa mått diagram i Azure Portal och Lägg till mått för lyckade anrop under de senaste 4 timmarna.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    Du bör se tre lyckade anrop i diagrammet.

### <a name="generate-a-unique-event-id"></a>Generera ett unikt händelse-ID

Den här funktionen genererar ett unikt ID för varje rang anrop. ID: t används för att identifiera information om rankning och belönings samtal. Det här värdet kan komma från en affärs process, till exempel ett ID för webbvy eller transaktions-ID.

Cellen har inga utdata. Funktionen skriver utdata från det unika ID: t när det anropas.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Få slumpmässig användare, väder och tid på dagen

Den här funktionen väljer en unik användare, väder och tid på dagen och lägger sedan till dessa objekt i JSON-objektet som ska skickas till ranknings förfrågan.

Cellen har inga utdata. När funktionen anropas returnerar den den slumpmässiga användarens namn, slumpmässig väder och slumpmässig tid på dagen.

Listan med 4 användare och deras inställningar – endast vissa inställningar visas för det kortfattat:

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Lägg till alla kaffe data

Den här funktionen lägger till hela listan över kaffe till det JSON-objekt som ska skickas till ranknings förfrågan.

Cellen har inga utdata. Funktionen ändrar `rankjsonobj` när den anropas.


Exemplet på en enskild kaffe funktion är:

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"

    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Jämför förutsägelse med känd användar inställning

Den här funktionen anropas efter rang-API: et för varje iteration.

Den här funktionen Jämför användarens preferenser för kaffe, baserat på väder och tid på dygnet, med personligt anpassade förslag för användaren för dessa filter. Om förslaget matchar returneras resultatet 1, annars är poängen 0. Cellen har inga utdata. Funktionen matar ut poängen när den anropas.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1
    return 0
```

### <a name="loop-through-calls-to-rank-and-reward"></a>Loopa genom anrop till rankning och belöning

Nästa cell är det _huvudsakliga_ arbetet i antecknings boken, en slumpmässig användare, hämtning av kaffe listan, som skickar båda till ranknings-API: et. Jämför förutsägelsen med användarens kända inställningar och skicka sedan belöningen tillbaka till tjänsten för anpassning.

Loopen körs för `num_requests` gånger. En personanpassare behöver några tusen anrop för att rangordna och belöna att skapa en modell.

Ett exempel på JSON som skickas till ranknings-API: et följer. Listan över kaffe är inte fullständig, för det kortfattat. Du kan se hela JSON för kaffe i `coffee.json` .

JSON skickas till ranknings-API: et:

```json
{
   'contextFeatures':[
      {
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[
      {
         'id':'Cappucino',
         'features':[
            {
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

JSON-svar från rang-API: et:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ],
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2',
    'rewardActionId': 'Latte'
}
```

Slutligen visar varje slinga det slumpmässiga valet av användare, väder, tid på dagen och bestämd belöning. Belöningen 1 anger att den personliga resursen har valt rätt kaffe typ för den angivna användaren, väder och tid på dagen.

```console
1 Alice Rainy Morning Latte 1
```

Funktionen använder:

* Rang: ett INLÄGGs REST API för att [få rangordning](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank).
* Belöning: ett inlägg REST API att [rapportera belöning](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1

    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']


    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat)

        # show JSON to send to Rank
        print('To: ', jsonFormat)

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction
        print ('From: ',response.json())

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified)

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1

    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>Kör för 10 000 iterationer
Kör den personliga slingan för 10 000 iterationer. Detta är en tids krävande händelse. Stäng inte webbläsaren som kör antecknings boken. Uppdatera mått diagrammet i Azure Portal regelbundet för att se det totala antalet anrop till tjänsten. När du har cirka 20 000 samtal, ett rang-och belönings samtal för varje iteration av slingan, är iterationerna gjorda.

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Diagram resultat för att se förbättring

Skapa ett diagram från `count` och `rewards` .

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Kör diagram för 10 000 rang-begäranden

Kör `createChart` funktionen.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>Läser diagrammet

Det här diagrammet visar lyckad modell för den aktuella standard inlärnings principen.

![Det här diagrammet visar framgången för den aktuella inlärnings principen för testets varaktighet.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


Det idealiska målet som i slutet av testet gör att slingan är i genomsnitt en lyckad frekvens som ligger nära 100 procent minus utforskningen. Standardvärdet för utforskning är 20%.

`100-20=80`

Det här utforsknings värdet finns i Azure Portal för personanpassa resursen på sidan **konfiguration** .

För att hitta en bättre inlärnings policy, baserat på dina data till ranknings-API: et, kör du en [offline-utvärdering](how-to-offline-evaluation.md) i portalen för din personanpassa slinga.

## <a name="run-an-offline-evaluation"></a>Köra en offline-utvärdering

1. I Azure Portal öppnar du sidan **utvärdering** av personanpassa resurs.
1. Välj **Skapa utvärdering**.
1. Ange nödvändiga data för utvärderings namn och datum intervall för upprepnings utvärderingen. Datum intervallet får bara innehålla de dagar som du fokuserar på för utvärderingen.
    ![I Azure Portal öppnar du sidan utvärdering av personanpassa resurs. Välj Skapa utvärdering. Ange utvärderings namnet och datum intervallet.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Syftet med att köra denna offline-utvärdering är att avgöra om det finns en bättre inlärnings princip för de funktioner och åtgärder som används i den här slingan. Se till att **optimerings identifiering** är aktiverat för att hitta den bättre inlärnings principen.

1. Välj **OK** för att påbörja utvärderingen.
1. Sidan **utvärderings** version listar den nya utvärderingen och dess aktuella status. Den här utvärderingen kan ta lite tid beroende på hur mycket data du har. Du kan komma tillbaka till den här sidan efter några minuter för att se resultatet.
1. När utvärderingen är klar väljer du utvärderingen och väljer sedan **jämförelse av olika inlärnings principer**. Detta visar de tillgängliga utbildnings principerna och hur de fungerar med data.
1. Välj den översta inlärnings principen i tabellen och välj **Använd**. Detta använder den _bästa_ inlärnings principen för din modell och omtränar.

## <a name="change-update-model-frequency-to-5-minutes"></a>Ändra frekvens för uppdaterings modell till 5 minuter

1. Välj sidan **konfiguration** i Azure Portal, fortfarande på den personliga resursen.
1. Ändra **uppdaterings frekvensen för modellen** och **belönings vänte tiden** till 5 minuter och välj **Spara**.

Läs mer om [belönings vänte tid](concept-rewards.md#reward-wait-time) och [modell uppdaterings frekvens](how-to-settings.md#model-update-frequency).

```python
#Verify new learning policy and times
get_service_settings()
```

Kontrol lera att utmatningen `rewardWaitTime` och `modelExportFrequency` båda har värdet 5 minuter.
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Verifiera ny inlärnings princip

Gå tillbaka till Azure Notebooks-filen och Fortsätt genom att köra samma slinga, men endast för 2 000 iterationer. Uppdatera mått diagrammet i Azure Portal regelbundet för att se det totala antalet anrop till tjänsten. När du har cirka 4 000 samtal, ett rang-och belönings samtal för varje iteration av slingan, är iterationerna gjorda.

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Kör diagram för 2 000 rang-begäranden

Kör `createChart` funktionen.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>Granska det andra diagrammet

Det andra diagrammet bör visa en synlig ökning i rankning förutsägelser med användar inställningar.

![Det andra diagrammet bör visa en synlig ökning i rankning förutsägelser med användar inställningar.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte tänker fortsätta med själv studie serien kan du rensa följande resurser:

* Ta bort ditt Azure Notebook-projekt.
* Ta bort din personanpassa resurs.

## <a name="next-steps"></a>Nästa steg

[Jupyter Notebook och datafiler](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) som används i det här exemplet finns på GitHub-lagrings platsen för personanpassaren.

