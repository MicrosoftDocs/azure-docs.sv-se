---
title: Begär real tids data och prognostiserade väder data med hjälp av Azure Maps väder tjänster (för hands version)
description: Lär dig hur du begär real tids (aktuell) och prognostiserat (minut, varje timme, dagligen) väder data med Microsoft Azure Maps väder tjänster (för hands version)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: fe1b337fe3e1dcf499f9a7428f66543108d0c050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680419"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services-preview"></a>Begär real tids data och prognostiserade väder data med hjälp av Azure Maps väder tjänster (för hands version) 

> [!IMPORTANT]
> Azure Maps väder tjänster finns för närvarande i offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Maps [väder tjänster](/rest/api/maps/weather) är en uppsättning RESTful-API: er som gör att utvecklare kan integrera mycket dynamiska historiska, real tids data och prognostiserade väder data och visualiseringar i sina lösningar. I den här artikeln visar vi hur du begär både real tids data och prognostiserade väder data.

I den här artikeln får du lära dig hur du:

* Begär real tids data (aktuella) väder data med hjälp av [API: t hämta aktuella villkor](/rest/api/maps/weather/getcurrentconditionspreview).
* Begär allvarliga väder aviseringar med hjälp av API: t för att [få allvarliga väder varningar](/rest/api/maps/weather/getsevereweatheralertspreview).
* Begär dagliga prognoser med hjälp av [API: t Hämta dagliga prognoser](/rest/api/maps/weather/getdailyforecastpreview).
* Begär Tim prognoser med hjälp av API för att [få Tim prognos](/rest/api/maps/weather/gethourlyforecastpreview).
* Minut prognoser per minut med hjälp av API: et för att [få minuter](/rest/api/maps/weather/getminuteforecastpreview).

Den här videon innehåller exempel på hur du kan göra REST-anrop till Azure Maps väder tjänster.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player?format=ny]

## <a name="prerequisites"></a>Förutsättningar

1. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

    >[!IMPORTANT]
    >[API: et för att få en minuts prognos](/rest/api/maps/weather/getminuteforecastpreview) kräver en pris nivås nyckel på S1. Alla andra API: er kräver en S0 pris nivå nyckel.

I den här självstudien används [Postman](https://www.postman.com/) -programmet, men du kan välja en annan API utvecklings miljö.

## <a name="request-real-time-weather-data"></a>Begär väder data i real tid

[API: erna hämta aktuella villkor](/rest/api/maps/weather/getcurrentconditionspreview) returnerar detaljerade väder förhållanden som nederbörd, temperatur och vridning för en specifik koordinat plats. Dessutom går det att hämta observationer från de senaste 6 eller 24 timmarna för en viss plats. Svaret innehåller information som observations datum och tid, en kort beskrivning av väder förhållandena, väder ikonen, utfällning indikator flaggor och temperatur. RealFeel™ temperatur-och UV-index returneras också.

I det här exemplet ska du använda [API: t hämta aktuella villkor](/rest/api/maps/weather/getcurrentconditionspreview) för att hämta aktuella väder förhållanden vid koordinater i Seattle, WA.

1. Öppna Postman-appen. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **samling**.  Namnge samlingen och välj knappen **skapa** . Du kommer att använda den här samlingen för resten av exemplen i det här dokumentet.

2. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående steg och välj sedan **Spara**.

3. Välj metoden **Hämta** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Klicka på knappen blå **sändning** . Svars texten innehåller aktuell väder information.

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>Begär allvarliga väder varningar

[Azure Maps få allvarliga väderleks aviserings-API: er](/rest/api/maps/weather/getsevereweatheralertspreview) returneras allvarliga väder aviseringar som är tillgängliga i hela världen från de offentliga och ledande, globala och regionala väderleks leverantörer. Tjänsten kan returnera information som aviserings typ, kategori, nivå och detaljerade beskrivningar av aktiva allvarliga aviseringar för den begärda platsen, till exempel orkaner, thunderstorms, blixt, värme vågor eller skogs bränder. Som exempel kan logistik chefer visualisera svåra väder förhållanden på en karta, tillsammans med företags platser och planerade vägar, och koordinera ytterligare med driv rutiner och lokala arbetare.

I det här exemplet ska du använda [API: et för att få svåra väder varningar](/rest/api/maps/weather/getsevereweatheralertspreview) för att hämta aktuella väder förhållanden vid koordinaterna i Cheyenne, WY.

>[!NOTE]
>I det här exemplet hämtas svåra väder varningar vid tidpunkten för den här skrivningen. Det är sannolikt att det inte längre finns några allvarliga väder varningar på den begärda platsen. Om du vill hämta faktiska allvarliga aviserings data när du kör det här exemplet måste du hämta data på en annan koordinat plats.

1. Öppna Postman-appen, klicka på **ny** och välj **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående avsnitt eller skapa en ny och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Klicka på knappen blå **sändning** . Om det inte finns några allvarliga väder varningar innehåller svars texten en tom `results[]` matris. Om det finns allvarliga väder varningar innehåller svars texten något som följande JSON-svar:

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>Begär dagliga väder prognos data

[API: et för att hämta dagliga prognoser](/rest/api/maps/weather/getdailyforecastpreview) returnerar den detaljerade dagliga väder prognosen, till exempel temperatur och vridning. Begäran kan ange hur många dagar som ska returneras: 1, 5, 10, 15, 25 eller 45 dagar för en specifik koordinat plats. Svaret innehåller information som temperatur, vind, nederbörd, luft kvalitet och UV-index.  I det här exemplet begär vi fem dagar efter att du har angett `duration=5` .

>[!IMPORTANT]
>I pris nivån S0 kan du begära en daglig prognos för nästa 1, 5, 10 och 15 dagar. I pris nivån S1 kan du även begära dagliga prognoser för de kommande 25 dagarna och 45 dagar.

I det här exemplet ska du använda [API: et Hämta dagliga prognoser](/rest/api/maps/weather/getdailyforecastpreview) för att hämta väder prognosen på fem dagar för koordinater i Seattle, WA.

1. Öppna Postman-appen, klicka på **ny** och välj **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående avsnitt eller skapa en ny och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Klicka på knappen blå **sändning** . Svars texten innehåller fem dagars väder prognos data. För det kortfattat, visar JSON-svaret nedan prognosen för den första dagen.
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>Begär data för varje timmes väder prognos

[API: et för erhållna Tim prognoser](/rest/api/maps/weather/gethourlyforecastpreview) returnerar detaljerad väder prognos per timme för nästa 1, 12, 24 (1 dag), 72 (3 dagar), 120 (5 dagar) och 240 timmar (10 dagar) för den aktuella koordinat platsen. API: t returnerar information som temperatur, fuktighet, vind, nederbörd och UV-index.

>[!IMPORTANT]
>I pris nivån S0 kan du begära en prognos per timme för nästa 1, 12, 24 timmar (1 dag) och 72 timmar (3 dagar). I pris nivån S1 kan du även begära Tim prognos för nästföljande 120 (5 dagar) och 240 timmar (10 dagar).

I det här exemplet ska du använda [API: et](/rest/api/maps/weather/gethourlyforecastpreview) för att få Tim prognos för att hämta Tim väder prognosen för de kommande 12 timmarna vid koordinater i Seattle, WA.

1. Öppna Postman-appen, klicka på **ny** och välj **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående avsnitt eller skapa en ny och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Klicka på knappen blå **sändning** . Svars texten innehåller väder prognos data under de kommande 12 timmarna. För det kortfattat, visar JSON-svaret nedan prognosen för den första timmen.

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>Data för begäran om minut-till-minut – väder prognoser

 [API: et för Get Minute-prognos](/rest/api/maps/weather/getminuteforecastpreview) returnerar minut-till-minut-prognoser för en specifik plats under de kommande 120 minuterna. Användare kan begära väder prognoser i intervall om 1, 5 och 15 minuter. Svaret innehåller information som typ av fällning (inklusive regn, snö eller en blandning av både), start tid och fällnings värde (dBZ).

I det här exemplet ska du använda [API: et för att hämta Minute](/rest/api/maps/weather/getminuteforecastpreview) för att hämta väder prognoser per minut vid koordinater i Seattle, WA. Väder prognosen anges för kommande 120 minuter. Frågan begär att prognosen ska ges med 15 minuters intervall, men du kan ändra parametern till antingen 1 eller 5 minuter.

1. Öppna Postman-appen, klicka på **ny** och välj **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående avsnitt eller skapa en ny och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Klicka på knappen blå **sändning** . Svars texten innehåller väder prognos data under de närmaste 120 minuterna, i 15-minuters intervall.

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Koncept för Azure Maps väder tjänster (för hands version)](./weather-services-concepts.md)

> [!div class="nextstepaction"]
> [Azure Maps väder tjänster (för hands version) REST API](/rest/api/maps/weather)