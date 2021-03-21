---
title: Microsoft Azure Maps-täckning för väder tjänster (för hands version)
description: Läs mer om Microsoft Azure Maps-täckning för väder tjänster (för hands version)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: references_regions
manager: philmea
ms.openlocfilehash: 6c4e9eb765a72b7a0b495f81a954b484ef6aa2b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905493"
---
# <a name="azure-maps-weather-services-preview-coverage"></a>Täckning för Azure Maps väder tjänster (för hands version)

> [!IMPORTANT]
> Azure Maps väder tjänster finns för närvarande i offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Den här artikeln innehåller information om disponering för Azure Maps [väder tjänster](/rest/api/maps/weather). Azure Maps väder data tjänster returnerar information som radar paneler, aktuella väder förhållanden, väder prognoser och väder längs en väg.

Azure Maps har inte samma nivå av information och exakthet för alla länder och regioner.

Följande tabell innehåller information om vilken typ av väder information som du kan begära från varje land/region.

| Symbol | Innebörd |
|--------|---------|
|*       |Omfattar aktuella villkor, Tim prognos, kvartals vis prognos, daglig prognos, väder utmed väg och dagliga index. |


## <a name="americas"></a>Nord- och Sydamerika

| Land/region              |  Satellit paneler | Minut prognos, radar paneler | Svåra väder varningar | Andra |
|-----------------------------|:----------------:|:-----------------:|:-----------------:|:--------:|  
| Anguilla                                 | ✓ |   | |  ✓| 
| Antarktis                               | ✓ |   | |✓|
| Antigua och Barbuda                      | ✓ |   | |✓| 
| Argentina                                | ✓ |   | |✓| 
| Aruba                                    | ✓ |   | |✓| 
| Bahamas                                  | ✓ |   | |✓| 
| Barbados                                 | ✓ |   | |✓| 
| Belize                                   | ✓ |   | |✓| 
| Bermuda                                  | ✓ |   | |✓| 
| Bolivia                                  | ✓ |   | |✓| 
| Bonaire                                  | ✓ |   | |✓| 
| Brasilien                                   | ✓ |   | ✓ |✓| 
| Brittiska Jungfruöarna                   | ✓ |   | |✓| 
| Kanada                                   | ✓ | ✓ | ✓ | ✓| 
| Caymanöarna                           | ✓ |   | |✓| 
| Chile                                    | ✓ |   | |✓| 
| Colombia                                 | ✓ |   | |✓| 
| Costa Rica                               | ✓ |   | |✓| 
| Kuba                                     | ✓ |   | |✓| 
| Curaçao                                  | ✓ |   | |✓| 
| Dominica                                 | ✓ |   | |✓| 
| Dominikanska republiken                       | ✓ |   | |✓| 
| Ecuador                                  | ✓ |   | |✓| 
| El Salvador                              | ✓ |   | |✓| 
| Falklandsöarna                         | ✓ |   | |✓| 
| Franska Guyana                            | ✓ |   | |✓| 
| Grönland                                | ✓ |   | |✓| 
| Grenada                                  | ✓ |   | |✓| 
| Guadeloupe                               | ✓ |   | |✓| 
| Guatemala                                | ✓ |   | |✓| 
| Guyana                                   | ✓ |   | |✓| 
| Haiti                                    | ✓ |   | |✓| 
| Honduras                                 | ✓ |   | |✓| 
| Jamaica                                  | ✓ |   | |✓| 
| Martinique                               | ✓ |   | |✓| 
| Mexico                                   | ✓ |   | |✓| 
| Montserrat                               | ✓ |   | |✓| 
| Nicaragua                                | ✓ |   | |✓| 
| Panama                                   | ✓ |   | |✓| 
| Paraguay                                 | ✓ |   | |✓| 
| Peru                                     | ✓ |   | |✓| 
| Puerto Rico                              | ✓ |   | ✓ |✓| 
| Saint Barthélemy                         | ✓ |   | |✓| 
| Saint Kitts och Nevis                    | ✓ |   | |✓| 
| Saint Lucia                              | ✓ |   | |✓| 
| Saint Martin                             | ✓ |   | |✓| 
| Saint-Pierre och Miquelon                | ✓ |   | |✓| 
| Saint Vincent och Grenadinerna         | ✓ |   | |✓| 
| Sint Eustatius                           | ✓ |   | |✓|  
| Sint Maarten                             | ✓ |   | |✓| 
| Sydgeorgien och Sydsandwichöarna | ✓ |   | |✓| 
| Surinam                                 | ✓ |   | |✓| 
| Trinidad och Tobago                      | ✓ |   | |✓| 
| Turks- och Caicosöarna                 | ✓ |   | |✓| 
| Amerikanska öar                    | ✓ |   | |✓| 
| Amerikanska jungfru öarna                      | ✓ |   | ✓|✓| 
| USA                            | ✓ | ✓ | ✓| ✓| 
| Uruguay                                  | ✓ |   | |✓| 
| Venezuela                                | ✓ |   | |✓| 


## <a name="middle-east-and-africa"></a>Mellanöstern och Afrika

| Land/region              |  Satellit paneler | Minut prognos, radar paneler | Svåra väder varningar | Andra | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:| 
| Algeriet                     | ✓               |                              |     |  ✓| 
| Angola                      | ✓               |                              |     |  ✓| 
| Bahrain                     | ✓               |                              |     |  ✓| 
| Benin                       | ✓               |                              |     |  ✓| 
| Botswana                    | ✓               |                              |     |  ✓| 
| Bouvetön               | ✓               |                              |     |  ✓| 
| Burkina Faso                | ✓               |                              |     |  ✓| 
| Burundi                     | ✓               |                              |     |  ✓| 
| Kamerun                    | ✓               |                              |     |  ✓| 
| Cabo Verde                  | ✓               |                              |     |  ✓| 
| Centralafrikanska Republiken    | ✓               |                              |     |  ✓| 
| Tchad                        | ✓               |                              |     |  ✓| 
| Komorerna                     | ✓               |                              |     |  ✓| 
| Kongo (DR)                 | ✓               |                              |     |  ✓|
| Côte d'Ivoire               | ✓               |                              |     |  ✓| 
| Djibouti                    | ✓               |                              |     |  ✓| 
| Egypten                       | ✓               |                              |     |  ✓| 
| Ekvatorialguinea           | ✓               |                              |     |  ✓| 
| Eritrea                     | ✓               |                              |     |  ✓| 
| eSwatini                    | ✓               |                              |     |  ✓| 
| Etiopien                    | ✓               |                              |     |  ✓| 
| Franska sydterritorierna | ✓               |                              |     |  ✓| 
| Gabon                       | ✓               |                              |     |  ✓| 
| Gambia                      | ✓               |                              |     |  ✓| 
| Ghana                       | ✓               |                              |     |  ✓| 
| Guinea                      | ✓               |                              |     |  ✓| 
| Guinea-Bissau               | ✓               |                              |     |  ✓| 
| Iran                        | ✓               |                              |     |  ✓| 
| Irak                        | ✓               |                              |     |  ✓| 
| Israel                      | ✓               |                              |   ✓   |  ✓| 
| Jordanien                      | ✓               |                              |     |  ✓| 
| Kenya                       | ✓               |                              |     |  ✓| 
| Kuwait                      | ✓               |                              |     |  ✓| 
| Libanon                     | ✓               |                              |     |  ✓| 
| Lesotho                     | ✓               |                              |     |  ✓| 
| Liberia                     | ✓               |                              |     |  ✓| 
| Libyen                       | ✓               |                              |     |  ✓| 
| Madagaskar                  | ✓               |                              |     |  ✓| 
| Malawi                      | ✓               |                              |     |  ✓| 
| Mali                        | ✓               |                              |     |  ✓| 
| Mauretanien                  | ✓               |                              |     |  ✓| 
| Mauritius                   | ✓               |                              |     |  ✓| 
| Mayotte                     | ✓               |                              |     |  ✓| 
| Marocko                     | ✓               |                              |     |  ✓| 
| Moçambique                  | ✓               |                              |     |  ✓| 
| Namibia                     | ✓               |                              |     |  ✓| 
| Niger                       | ✓               |                              |     |  ✓| 
| Nigeria                     | ✓               |                              |     |  ✓| 
| Oman                        | ✓               |                              |     |  ✓| 
| Palestinska myndigheten       | ✓               |                              |     |  ✓| 
| Qatar                       | ✓               |                              |     |  ✓| 
| Réunion                     | ✓               |                              |     |  ✓| 
| Rwanda                      | ✓               |                              |     |  ✓| 
| Saint Helena, Ascension, Tristan da Cunha        | ✓               |            |     |  ✓| 
| São Tomé och Príncipe       | ✓               |                              |     |  ✓| 
| Saudiarabien                | ✓               |                              |     |  ✓| 
| Senegal                     | ✓               |                              |     |  ✓| 
| Seychellerna                  | ✓               |                              |     |  ✓| 
| Sierra Leone                | ✓               |                              |     |  ✓| 
| Somalia                     | ✓               |                              |     |  ✓| 
| Sydafrika                | ✓               |                              |     |  ✓| 
| Sydsudan                 | ✓               |                              |     |  ✓| 
| Sudan                       | ✓               |                              |     |  ✓| 
| Syrien                       | ✓               |                              |     |  ✓| 
| Tanzania                    | ✓               |                              |     |  ✓| 
| Togo                        | ✓               |                              |     |  ✓| 
| Tunisien                     | ✓               |                              |     |  ✓| 
| Uganda                      | ✓               |                              |     |  ✓| 
| Förenade Arabemiraten        | ✓               |                              |     |  ✓| 
| Jemen                       | ✓               |                              |     |  ✓| 
| Zambia                      | ✓               |                              |     |  ✓| 
| Zimbabwe                    | ✓               |                              |     |  ✓| 


## <a name="asia-pacific"></a>Asien och stillahavsområdet

| Land/region              |  Satellit paneler | Minut prognos, radar paneler | Svåra väder varningar | Andra |
|-----------------------------|:----------------:|:-----------------:|:--------:|  :--------:|
| Afghanistan                       | ✓ |   | | ✓| 
| Amerikanska Samoa                    | ✓ |   |  ✓| ✓| 
| Australien                         | ✓ | ✓ |  ✓ | ✓| 
| Bangladesh                        | ✓ |   | | ✓| 
| Bhutan                            | ✓ |   | | ✓| 
| Brittiska territoriet i Indiska Oceanen    | ✓ |   | | ✓| 
| Brunei                            | ✓ |   | | ✓| 
| Kambodja                          | ✓ |   | | ✓| 
| Kina                             | ✓ | ✓ |  ✓ | ✓| 
| Julön                  | ✓ |   | | ✓| 
| Kokosöarna           | ✓ |   | | ✓| 
| Cooköarna                      | ✓ |   | | ✓| 
| Fiji                              | ✓ |   | | ✓| 
| Franska Polynesien                  | ✓ |   | | ✓| 
| Guam                              | ✓ |   |  ✓| ✓| 
| Heard- och McDonaldöarna | ✓ |   | | ✓| 
| Folkrepubliken Kinas särskilda administrativa region Hongkong                     | ✓ |   | | ✓| 
| Indien                             | ✓ |   | | ✓| 
| Indonesien                         | ✓ |   | | ✓| 
| Japan                             | ✓ | ✓ |  ✓| ✓| 
| Kazakstan                        | ✓ |   | | ✓| 
| Kiribati                          | ✓ |   | | ✓| 
| Korea                             | ✓ | ✓ | ✓ |  ✓| 
| Kirgizistan                        | ✓ |   | | ✓| 
| Laos                              | ✓ |   | | ✓| 
| Folkrepubliken Kinas särskilda administrativa region Macao                         | ✓ |   | | ✓| 
| Malaysia                          | ✓ |   | | ✓| 
| Maldiverna                          | ✓ |   | | ✓| 
| Marshallöarna                  | ✓ |   | ✓ | ✓| 
| Mikronesien                        | ✓ |   | ✓ | ✓| 
| Mongoliet                          | ✓ |   | | ✓| 
| Myanmar                           | ✓ |   | | ✓| 
| Nauru                             | ✓ |   | | ✓| 
| Nepal                             | ✓ |   | | ✓| 
| Nya Kaledonien                     | ✓ |   | | ✓| 
| Nya Zeeland                       | ✓ |   | ✓ | ✓| 
| Niue                              | ✓ |   | | ✓| 
| Norfolkön                    | ✓ |   | | ✓| 
| Nordkorea                       | ✓ |   | | ✓| 
| Nordmarianerna          | ✓ |   | ✓ | ✓| 
| Pakistan                          | ✓ |   | | ✓| 
| Palau                             | ✓ |   | ✓ | ✓| 
| Papua Nya Guinea                  | ✓ |   | | ✓| 
| Filippinerna                       | ✓ |   | ✓ | ✓| 
| Pitcairn                  | ✓ |   | | ✓| 
| Samoa                             | ✓ |   | | ✓| 
| Singapore                         | ✓ |   | | ✓| 
| Solomonöarna                   | ✓ |   | | ✓| 
| Sri Lanka                         | ✓ |   | | ✓| 
| Taiwan                            | ✓ |   | | ✓| 
| Tadzjikistan                        | ✓ |   | | ✓| 
| Thailand                          | ✓ |   | | ✓| 
| Timor-Leste                       | ✓ |   | | ✓| 
| Tokelau                           | ✓ |   | | ✓| 
| Tonga                             | ✓ |   | | ✓| 
| Turkmenistan                      | ✓ |   | | ✓| 
| Tuvalu                            | ✓ |   | | ✓| 
| Uzbekistan                        | ✓ |   | | ✓| 
| Vanuatu                           | ✓ |   | | ✓| 
| Vietnam                           | ✓ |   | | ✓| 
| Wallis och Futuna                 | ✓ |   | | ✓| 


## <a name="europe"></a>Europa

| Land/region              |  Satellit paneler | Minut prognos, radar paneler | Svåra väder varningar | Andra | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:|
| Albanien                | ✓ |   | | ✓| 
| Andorra                | ✓ |   | ✓ | ✓| 
| Armenien                | ✓ |   | | ✓| 
| Österrike                | ✓ | ✓ | ✓ | ✓|
| Azerbajdzjan             | ✓ |   | | ✓| 
| Vitryssland                | ✓ |   | | ✓| 
| Belgien                | ✓ | ✓ |  ✓| ✓| 
| Bosnien och Hercegovina | ✓ | ✓ | ✓ | ✓| 
| Bulgarien               | ✓ |   |  ✓| ✓| 
| Kroatien                | ✓ | ✓ |  ✓| ✓| 
| Cypern                 | ✓ |   | ✓ | ✓| 
| Czechia                | ✓ | ✓ | ✓ | ✓| 
| Danmark                | ✓ | ✓ | ✓ | ✓| 
| Estland                | ✓ | ✓ |  ✓| ✓| 
| Färöarna          | ✓ |   | | ✓| 
| Finland                | ✓ | ✓ | ✓ | ✓| 
| Frankrike                 | ✓ | ✓ | ✓ | ✓| 
| Georgia                | ✓ |   | | ✓| 
| Tyskland                | ✓ | ✓ | ✓ | ✓| 
| Gibraltar              | ✓ | ✓ | | ✓| 
| Grekland                 | ✓ |   |  ✓| ✓| 
| Guernsey               | ✓ |   | | ✓| 
| Ungern                | ✓ | ✓ |  ✓| ✓| 
| Island                | ✓ |   | ✓ | ✓| 
| Irland                | ✓ | ✓ |  ✓| ✓| 
| Italien                  | ✓ |   |  ✓| ✓|
| Isle of Man            | ✓ |   | | ✓| 
| Jan Mayen              | ✓ |   | | ✓| 
| Jersey                 | ✓ |   | | ✓| 
| Kosovo                 | ✓ |   |  ✓| ✓| 
| Lettland                 | ✓ |   | ✓ | ✓| 
| Liechtenstein          | ✓ | ✓ |  ✓| ✓| 
| Litauen              | ✓ |   | ✓ | ✓| 
| Luxemburg             | ✓ | ✓ |  ✓| ✓| 
| Nordmakedonien        | ✓ |   | ✓ | ✓| 
| Malta                  | ✓ |   | ✓ | ✓| 
| Moldavien                | ✓ | ✓ | ✓ | ✓| 
| Monaco                 | ✓ | ✓ |  ✓| ✓| 
| Montenegro             | ✓ | ✓ |  ✓| ✓| 
| Nederländerna            | ✓ | ✓ |  ✓| ✓| 
| Norge                 | ✓ | ✓ |  ✓| ✓| 
| Polen                 | ✓ | ✓ |  ✓| ✓| 
| Portugal               | ✓ | ✓ |  ✓| ✓| 
| Rumänien                | ✓ | ✓ |  ✓| ✓| 
| Ryssland                 | ✓ |   |  ✓| ✓| 
| San Marino             | ✓ |   |  ✓| ✓| 
| Serbien                 | ✓ | ✓ |  ✓| ✓| 
| Slovakien               | ✓ | ✓ |  ✓| ✓| 
| Slovenien               | ✓ | ✓ |  ✓| ✓| 
| Spanien                  | ✓ | ✓ |  ✓| ✓| 
| Svalbard               | ✓ |   | | ✓|
| Sverige                 | ✓ | ✓ |  ✓| ✓| 
| Schweiz            | ✓ | ✓ | ✓| ✓| 
| Turkiet                 | ✓ |   | | ✓| 
| Ukraina                | ✓ |   | | ✓| 
| Storbritannien         | ✓ | ✓ | ✓| ✓| 
| Vatikanstaten           | ✓ |   |✓ | ✓|