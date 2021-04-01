---
title: Prissättning för Virtual Machine-erbjudanden – Azure Marketplace
description: Förklarar de tre metoderna för att ange prissättningen för erbjudanden för virtuella datorer.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: fe8024c60e204a4ec72002c878f7753ae35aa00d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89393827"
---
# <a name="pricing-for-virtual-machine-offers"></a>Prissättning för erbjudanden på virtuella datorer

> [!NOTE]
> Cloud Partner Portal API: er är integrerade i och fortsätter att fungera i Partner Center. Över gången introducerar små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter över gången till Partner Center. CPP-API: er bör endast användas för befintliga produkter som redan har integrerats före över gången till Partner Center. nya produkter bör använda API: er för överföring av Partner Center.

Det finns tre sätt att ange prissättning för virtuella datorer: anpassade grund priser, priser per kärna och priser för kalkyl blad.

## <a name="customized-core-pricing"></a>Anpassat kärn pris

Priset är särskilt för varje region och kärn kombination. Varje region i försäljnings listan måste anges i **virtualMachinePricing** / **regionPrices** -avsnittet i definitionen.  Använd rätt valuta koder för varje [region](#regions) i din begäran.  Följande exempel visar dessa krav:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```

## <a name="per-core-pricing"></a>Priser per kärna

I detta fall anger utgivaren ett pris i USD för deras SKU och alla andra priser genereras automatiskt. Priset per kärna anges i den **enskilda** parametern i begäran.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```

## <a name="spreadsheet-pricing"></a>Priser för kalkyl blad

Utgivaren kan också ladda upp pris kalkyl bladet till en tillfällig lagrings plats. ta sedan med URI: n i begäran som andra fil artefakter. Kalkyl bladet laddas sedan upp, översätts för att utvärdera det angivna pris schemat och uppdaterar slutligen erbjudandet med pris informationen. Efterföljande GET-begäranden för erbjudandet returnerar kalkyl bladets URI och de utvärderade priserna för regionen.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

## <a name="new-core-sizes-added-on-722019"></a>Nya kärn storlekar som lagts till på 7/2/2019

VM-utgivaren meddelades den 2 juli 2019 om tillägg av nya priser för nya storlekar för virtuella Azure-datorer (baserat på antalet kärnor).  De nya priserna är för kärn storlekarna 10, 44, 48, 60, 120, 208 och 416.  För befintliga virtuella datorer har nya priser för dessa kärnor automatiskt beräknats baserat på aktuella priser. Utgivare har fram till den 1 augusti 2019 att granska ytterligare priser och göra önskade ändringar.  Efter det här datumet börjar de automatiskt beräknade priserna för de nya kärn storlekarna att gälla, om de inte redan publiceras om av utgivaren.

## <a name="regions"></a>Regioner

I följande tabell visas olika regioner som du kan ange för anpassade kärn priser och deras motsvarande valuta koder.

| **Region** | **Namn**             | **Valutakod** |
|------------|----------------------|-------------------|
| DZ         | Algeriet              | DZD               |
| AR         | Argentina            | ARS               |
| AU         | Australien            | AUD               |
| AT         | Österrike              | EUR               |
| BH         | Bahrain              | BHD               |
| BY         | Vitryssland              | RUB               |
| BE         | Belgien              | EUR               |
| BR         | Brasilien               | USD               |
| BG         | Bulgarien             | BGN               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Colombia             | KOPIOR               |
| CR         | Costa Rica           | CRC               |
| HR         | Kroatien              | HRK               |
| CY         | Cypern               | EUR               |
| CZ         | Czechia       | CZK               |
| DK         | Danmark              | DKK               |
| DO         | Dominikanska republiken   | USD               |
| EC         | Ecuador              | USD               |
| EG         | Egypten                | EGP               |
| SV         | El Salvador          | USD               |
| EE         | Estland              | EUR               |
| FI         | Finland              | EUR               |
| FR         | Frankrike               | EUR               |
| DE         | Tyskland              | EUR               |
| GR         | Grekland               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | Folkrepubliken Kinas särskilda administrativa region Hongkong        | HKD               |
| HU         | Ungern              | HUF               |
| IS         | Island              | ISKALLOKERING               |
| IN         | Indien                | INR               |
| ID         | Indonesien            | IDR               |
| IE         | Irland              | EUR               |
| IL         | Israel               | ILS               |
| IT         | Italien                | EUR               |
| JP         | Japan                | JPY               |
| JO         | Jordanien               | JOD               |
| KZ         | Kazakstan           | KZT               |
| KE         | Kenya                | KES               |
| KR         | Korea                | KRW               |
| KW         | Kuwait               | KWD               |
| LV         | Lettland               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litauen            | EUR               |
| LU         | Luxemburg           | EUR               |
| MY         | Malaysia             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Mexico               | MXN               |
| ME         | Montenegro           | EUR               |
| MA         | Marocko              | MAD               |
| NL         | Nederländerna          | EUR               |
| NZ         | Nya Zeeland          | NZD               |
| NG         | Nigeria              | NGN               |
| MK         | Nordmakedonien      | MKD               |
| NO         | Norge               | NOK               |
| OM         | Oman                 | OMR               |
| PK         | Pakistan             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Peru                 | PENNOR               |
| PH         | Filippinerna          | PHP               |
| PL         | Polen               | PLN               |
| PT         | Portugal             | EUR               |
| PR         | Puerto Rico          | USD               |
| QA         | Qatar                | QAR               |
| RO         | Rumänien              | RON               |
| RU         | Ryssland               | RUB               |
| SA         | Saudiarabien         | SAR               |
| RS         | Serbien               | RSD               |
| SG         | Singapore            | SGD               |
| SK         | Slovakien             | EUR               |
| SI         | Slovenien             | EUR               |
| ZA         | Sydafrika         | ZAR               |
| ES         | Spanien                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Sverige               | SEK               |
| CH         | Schweiz          | CHF               |
| TW         | Taiwan               | TWD               |
| TH         | Thailand             | THB               |
| TT         | Trinidad och Tobago  | TDD               |
| TN         | Tunisien              | TND               |
| TR         | Turkiet               | TRY               |
| UA         | Ukraina              | UAH               |
| AE         | Förenade Arabemiraten | EUR               |
| GB         | Storbritannien       | GBP               |
| USA         | USA        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
