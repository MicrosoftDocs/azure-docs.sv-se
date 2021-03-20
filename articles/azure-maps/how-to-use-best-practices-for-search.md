---
title: Metod tips för Azure Maps Search Service | Microsoft Azure Maps
description: Lär dig hur du använder de bästa metoderna när du använder Search Service från Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 11c1938c3c1ccba533f52336fad81ebeaae53b24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895485"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Metod tips för Azure Maps Search Service

Azure Maps [search service](/rest/api/maps/search) innehåller API: er som erbjuder olika funktioner som hjälper utvecklare att söka efter adresser, platser, företags listor efter namn eller kategori och annan geografisk information. Exempel: med[fuzzy Search API](/rest/api/maps/search/getsearchfuzzy) kan användare söka efter en adress eller ett intresse punkt (POI).

Den här artikeln förklarar hur du använder ljud metoder när du anropar data från Azure Maps Search Service. Du lär dig följande:
> [!div class="checklist"]
> * Bygg frågor för att returnera relevanta matchningar
> * Begränsa Sök Resultat
> * Lär dig skillnaderna mellan resultat typer
> * Läs adress Sök-svars strukturen

## <a name="prerequisites"></a>Förutsättningar

1. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.

I den här artikeln används [Postman-appen](https://www.postman.com/downloads/) för att bygga rest-anrop, men du kan välja vilken API utvecklings miljö som helst.

## <a name="best-practices-to-geocode-addresses"></a>Metod tips för adresser för att koda

När du söker efter en fullständig eller partiell adress med hjälp av Azure Maps Search Service läser API: et nyckelord från din Sök fråga. Sedan returnerar den longitud-och latitud-koordinaterna för adressen. Den här processen kallas för *kodning*.

Möjligheten att koda i ett land/en region beror på tillgängligheten för väg data och att det är en bra kodning av tjänsten. Mer information om Azure Maps funktioner för att koda efter land eller region finns i lands [kodnings täckning](./geocoding-coverage.md).

### <a name="limit-search-results"></a>Begränsa Sök Resultat

 Azure Maps Sök-API: n kan hjälpa dig att begränsa Sök resultaten på lämpligt sätt. Du kan begränsa resultaten så att du kan visa relevanta data för dina användare.

> [!NOTE]
> Sök-API: erna stöder fler parametrar än de som beskrivs i den här artikeln.

#### <a name="geobiased-search-results"></a>Resultat av neutrala Sök Resultat

Om du vill göra ett neutralt resultat till det relevanta område för din användare ska du alltid lägga till så många plats detaljer som möjligt. Du kanske vill begränsa Sök resultaten genom att ange några typer av indata:

* Ange `countrySet` parametern. Du kan till exempel ställa in den till `US,FR` . Som standard söker API: t igenom hela världen, så att den kan returnera onödiga resultat. Om frågan saknar parameter kan `countrySet` sökningen returnera felaktiga resultat. En sökning efter en stad med namnet *Bellevue* returnerar till exempel resultat från USA och Frankrike eftersom båda länderna/regionerna innehåller en stad med namnet *Bellevue*.

* Du kan använda `btmRight` parametrarna och `topleft` för att ange avgränsnings rutan. Dessa parametrar begränsar sökningen till ett angivet område på kartan.

* Om du vill påverka resultatet av relevansen definierar du `lat` parametrarna och för `lon` koordinaten. Använd `radius` parametern för att ställa in radien för Sök fältet.


#### <a name="fuzzy-search-parameters"></a>Parametrar för fuzzy-sökning

Vi rekommenderar att du använder Azure Maps [Sök i fuzzy API](/rest/api/maps/search/getsearchfuzzy) när du inte känner till dina användar indata för en Sök fråga. Till exempel kan indata från användaren vara en adress eller typ av intresse (POI), som *shopping-mall*. API: n kombinerar POI-sökning och-kodning i en kanonisk *enkel sökning*: 

* `minFuzzyLevel`Parametrarna och `maxFuzzyLevel` hjälper till att returnera relevanta matchningar även när frågeparametrar inte exakt matchar den information som användaren vill. För att maximera prestandan och minska ovanliga resultat ställer du in Sök frågor till standardvärdena `minFuzzyLevel=1` och `maxFuzzyLevel=2` . 

    Om till exempel `maxFuzzyLevel` parametern är inställd på 2 matchas Sök termen *restrant* till *restaurang*. Du kan åsidosätta standard nivåer för fuzzy när du behöver. 

* Använd `idxSet` parametern för att prioritera den exakta uppsättningen av resultat typer. Om du vill prioritera en exakt uppsättning resultat kan du skicka en kommaavgränsad lista över index. Objekt ordningen spelar ingen roll i listan. Azure Maps stöder följande index:

* `Addr` - **Adress intervall**: adress punkter som interpoleras från början och slutet av gatan. Dessa punkter visas som adress intervall.
* `Geo` - **Geografiska** områden: administrativa indelningar av mark. En geografi kan till exempel vara land/region, delstat eller stad.
* `PAD` - **Adress adresser**: adresser som innehåller gatu namn och nummer. Punkt adresser kan hittas i ett index. Ett exempel är *Soquel Dr 2501*. En punkt adress ger den högsta möjliga noggrannhets nivån för adresser.  
* `POI` - **Punkter av intresse**: punkter på en karta som anses vara värda eller som kan vara intressanta. [Sök adressens API](/rest/api/maps/search/getsearchaddress) returnerar inte POI: er.  
* `Str` - **Streets**: gator på kartan.
* `XStr` - **Kors gator eller korsningar**: Knut punkter eller platser där två gator korsar varandra.


#### <a name="usage-examples"></a>Användningsexempel

* `idxSet=POI` – Sök endast i POI: er. 

* `idxSet=PAD,Addr` – Sök endast adresser. `PAD` anger punkt adressen och `Addr` anger adress intervallet.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Omvänd-landskod och filter för en geografisk enhets typ

När du gör en omvänd kod sökning i [omvänt API för Sök adressen](/rest/api/maps/search/getsearchaddressreverse)kan tjänsten returnera polygoner för administrativa områden. Du kanske exempelvis vill hämta områdes polygonen för en stad. Du kan begränsa sökningen till specifika geografi-entitetstyper genom att inkludera `entityType` parametern i dina begär Anden. 

Det resulterande svaret innehåller geografi-ID: t och entitetstypen som matchades. Om du anger mer än en entitet returnerar slut punkten den *minsta tillgängliga entiteten*. Du kan använda det returnerade geometri-ID: t för att hämta geografins geometri genom [tjänsten search polygon](/rest/api/maps/search/getsearchpolygon).

#### <a name="sample-request"></a>Exempel förfrågan

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Svarsåtgärder

```JSON
{
    "summary": {
        "queryTime": 14,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="set-the-results-language"></a>Ange ett resultat språk

Använd `language` parametern för att ange språket för de returnerade Sök resultaten. Om begäran inte anger språket, används som standard Search Service det vanligaste språket i landet eller regionen. När inga data är tillgängliga på det angivna språket används standard språket. 

Mer information finns i [Azure Maps språk som stöds](./supported-languages.md).


### <a name="use-predictive-mode-automatic-suggestions"></a>Använd förutsägande läge (automatiska förslag)

Om du vill hitta fler matchningar för partiella frågor anger du `typeahead` parametern till `true` . Den här frågan tolkas som en del Indatatyp och sökningen går in i förutsägande läge. Om du inte anger `typeahead` parametern till `true` förutsätter tjänsten att all relevant information har skickats in.

I följande exempel fråga frågar Sök adress tjänsten efter *mi*. Här har `typeahead` parametern angetts till `true` . Svaret visar att Sök tjänsten tolkade frågan som partiell fråga. Svaret innehåller resultat för en automatiskt föreslagen fråga.

#### <a name="sample-query"></a>Exempelfråga

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Svarsåtgärder

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>Koda en URI för att hantera specialtecken 

Om du vill hitta kors adresser måste du koda URI: n för att hantera specialtecken i adressen. Tänk på följande adress exempel: *1st minimering & union gata, Seattle*. Här kodar du et-tecknet ( `&` ) innan du skickar begäran. 

Vi rekommenderar att du kodar tecken data i en URI. I en URI kodar du alla tecken genom att använda ett procent tecken ( `%` ) och ett hexadecimalt värde på två tecken som motsvarar tecknens UTF-8-kod.

#### <a name="usage-examples"></a>Användningsexempel

Börja med följande adress:

```
query=1st Avenue & E 111th St, New York
```

Koda adressen:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Du kan använda följande metoder.

Java Script eller TypeScript:
```javascript
encodeURIComponent(query)
```

C# eller Visual Basic:
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP
```PHP
urlencode(query)
```

Ruby
```Ruby
CGI::escape(query) 
```

Införliva
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Offline
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Metod tips för POI-sökning

I en POI-sökning kan du begära POI resultat efter namn. Du kan till exempel söka efter ett företag efter namn. 

Vi rekommenderar starkt att du använder `countrySet` parametern för att ange länder/regioner där ditt program behöver täckning. Standard beteendet är att söka hela världen. Den här breda sökningen kan returnera onödiga resultat och sökningen kan ta lång tid.

### <a name="brand-search"></a>Varumärkes sökning

För att förbättra relevansen för resultaten och informationen i svaret innehåller ett POI söksvar varumärkes information. Du kan använda den här informationen för att ytterligare parsa svaret.

I en begäran kan du skicka en kommaavgränsad lista med varumärkes namn. Använd listan för att begränsa resultaten till vissa varumärken genom att ange `brandSet` parametern. Objekt ordningen spelar ingen roll i listan. När du tillhandahåller flera märkes listor måste resultaten som returneras tillhöra minst en av dina listor.

För att utforska varumärkes sökning, ska vi göra en [POI kategori Sök](/rest/api/maps/search/getsearchpoicategory) förfrågan. I följande exempel söker vi efter gas stationer nära Microsofts campus i Redmond, Washington. Svaret visar varumärkes information för varje POI som returnerades.

#### <a name="sample-query"></a>Exempelfråga

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Svarsåtgärder

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Sök efter flyg plats

Med hjälp av Sök POI-API: et kan du söka efter flyg platser med hjälp av den officiella koden. Du kan till exempel använda *Sea* för att hitta Seattle-Tacoma internationella flyg platsen: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Närliggande sökning

Om du vill hämta POI-resultat kring en bestämd plats kan du prova att använda [Sök i närheten API](/rest/api/maps/search/getsearchnearby). Slut punkten returnerar endast POI resultat. Det går inte att använda en Sök fråga-parameter. 

För att begränsa resultatet rekommenderar vi att du ställer in RADIUS.

## <a name="understanding-the-responses"></a>Förstå Svaren

Låt oss hitta en adress i Seattle genom att göra en adress – Sök-begäran till Azure Maps Search Service. I följande URL för begäran anger vi att `countrySet` parametern ska `US` söka efter adressen i USA.

### <a name="sample-query"></a>Exempelfråga

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Typer av resultat som stöds

* **Punkt adress**: punkter på en karta som har en viss adress med gatu namn och nummer. Punkt adress ger den högsta noggrannhets nivån för adresser. 

* **Adress intervall**: det intervall av adress punkter som interpoleras från början och slutet av gatan.  

* **Geografi**: områden på en karta som representerar administrativa indelningar av en mark, till exempel land/region, delstat eller stad. 

* **POI**: punkter på en karta som är värda och som kan vara intressanta.

* **Gata**: gator på kartan. Adresser matchas mot de latitud-och longitud koordinater för gatan som innehåller adressen. Hus numret kanske inte bearbetas. 

* **Kors gatan**: skärnings punkter. Kors gator representerar Knut punkter där två gator korsar varandra.

### <a name="response"></a>Svarsåtgärder

Nu ska vi titta på svars strukturen. I svaret som följer är typerna av resultat objekt olika. Om du ser noggrant ser du tre typer av resultat objekt:

* Adress för punkt
* Gata
* Kors gatan

Observera att adresss ökningen inte returnerar POI: er.  

`Score`Parametern för varje Response-objekt anger hur det matchande resultatet relaterar till poängen för andra objekt i samma svar. Mer information om parametrar för svars objekt finns i [Hämta Sök adress](/rest/api/maps/search/getsearchaddress).

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometri

Svars typen *Geometry* kan innehålla det geometri-ID som returneras i `dataSources` objektet under `geometry` och `id` . Du kan till exempel använda [tjänsten search polygon](/rest/api/maps/search/getsearchpolygon) för att begära geometri data i ett interjson-format. Med hjälp av det här formatet kan du få en stad eller en flyg plats för en uppsättning entiteter. Du kan sedan använda dessa gräns data för att [ställa in en inhägnad](./tutorial-geofence.md) eller [söka POI: er i geometrin](/rest/api/maps/search/postsearchinsidegeometry).


Svar för [Sök adress](/rest/api/maps/search/getsearchaddress) -API eller [Sök i fuzzy](/rest/api/maps/search/getsearchfuzzy) -API: t kan innehålla det geometri-ID som returneras i `dataSources` objektet under `geometry` och `id` :


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

> [!div class="nextstepaction"]
> [Så här skapar du Azure Maps Search Service-begäranden](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Search Service API-dokumentation](/rest/api/maps/search)