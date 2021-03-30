---
title: Rendera täckning | Microsoft Azure Maps
description: Lär dig om Azure Maps återger olika regioner med detaljerade eller förenklade data. Se vilken nivå den använder för raster-och vektor panels kartor i dessa regioner.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 69b122196cbeb7508cddf3a890768f83fdcd4727
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88030936"
---
# <a name="azure-maps-render-coverage"></a>Azure Maps återge täckning

Azure Maps använder både raster paneler och vektor paneler för att skapa kartor. Med den lägsta lösningen passar hela världen i en enda panel. Med den högsta upplösningen representerar en enda panel 38 kvadratmeter. Du kan se mer information om kontinenter, regioner, städer och enskilda gator när du zoomar in kartan. Mer information om paneler finns i [zoomnings nivåer och panel rutnät](zoom-levels-and-tile-grid.md).

Maps har dock inte samma nivå av information och exakthet för alla regioner. Följande tabeller beskriver den informations nivå som du kan återge för varje region.

## <a name="legend"></a>Förklaring

| Symbol | Innebörd |
|--------|---------|
| ✓ | Regionen visas med detaljerade data.   |
| Ø | Regionen representeras med förenklade data. |


## <a name="africa"></a>Afrika 


| Land/region | Raster paneler Unified | Vektor paneler Unified |
| ------ | :------------------: | :------------------: |
| Algeriet                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botswana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       | ✓ | ✓ |
| Kamerun                         | ✓ | ✓ |
| Centralafrikanska Republiken         | ✓ | Ø |
| Tchad                             | ✓ | Ø |
| Komorerna                          | ✓ | Ø |
| Demokratiska republiken Kongo | ✓ | ✓ |
| Côte d'Ivoire                    | ✓ | Ø |
| Djibouti                         | ✓ | Ø |
| Egypten                            | ✓ | ✓ |
| Ekvatorialguinea                | ✓ | Ø |
| Eritrea                          | ✓ | Ø |
| Etiopien                         | ✓ | Ø |
| Gabon                            | ✓ | ✓ |
| Gambia                           | ✓ | Ø |
| Ghana                            | ✓ | ✓ |
| Guinea                           | ✓ | Ø |
| Guinea-Bissau                    | ✓ | Ø |
| Kenya                            | ✓ | ✓ |
| Lesotho                          | ✓ | ✓ |
| Liberia                          | ✓ | Ø |
| Libyen                            | ✓ | Ø |
| Madagaskar                       | ✓ | Ø |
| Malawi                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauretanien                       | ✓ | ✓ |
| Mauritius                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Marocko                          | ✓ | ✓ |
| Moçambique                       | ✓ | ✓ |
| Namibia                          | ✓ | ✓ |
| Niger                            | ✓ | ✓ |
| Nigeria                          | ✓ | ✓ |
| Réunion                          | ✓ | ✓ |
| Rwanda                           | ✓ | ✓ |
| Saint Helena, Ascension och Tristan da Cunha | ✓ | Ø |
| São Tomé och Príncipe            | ✓ | Ø |
| Senegal                          | ✓ | ✓ |
| Sierra Leone                     | ✓ | ✓ |
| Somalia                          | ✓ | ✓ |
| Sydafrika                     | ✓ | ✓ |
| Sydsudan                      | ✓ | ✓ |
| Sudan                            | ✓ | ✓ |
| Swaziland                        | ✓ | ✓ |
| Förenade republiken Tanzania      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunisien                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zambia                           | ✓ | ✓ |
| Zimbabwe                         | ✓ | ✓ |

## <a name="americas"></a>Nord- och Sydamerika

| Land/region | Raster paneler Unified | Vektor paneler Unified |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antigua och Barbuda       | ✓ | ✓ |
| Argentina                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamas                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermuda                   | ✓ | ✓ |
| Mångnationella staten staten Bolivia | ✓ | ✓ |
| Bonaire, Sint Eustatius och Saba | ✓ | ✓ |
| Brasilien                    | ✓ | ✓ |
| Kanada                    | ✓ | ✓ |
| Caymanöarna            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| Colombia                  | ✓ | ✓ |
| Costa Rica                | ✓ | ✓ |
| Kuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominica                  | ✓ | ✓ |
| Dominikanska republiken        | ✓ | ✓ |
| Ecuador                   | ✓ | ✓ |
| Falk lands öarna (Malvinas) | ✓ | ✓ |
| Franska Guyana             | ✓ | ✓ |
| Grönland                 | ✓ | Ø |
| Grenada                   | ✓ | ✓ |
| Guadeloupe                | ✓ | ✓ |
| Guatemala                 | ✓ | ✓ |
| Guyana                    | ✓ | ✓ |
| Haiti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamaica                   | ✓ | ✓ |
| Martinique                | ✓ | ✓ |
| Mexico                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nicaragua                 | ✓ | ✓ |
| Nordmarianerna  | ✓ | ✓ |
| Panama                    | ✓ | ✓ | 
| Paraguay                  | ✓ | ✓ |
| Peru                      | ✓ | ✓ |
| Puerto Rico               | ✓ | ✓ |
| Quebec (Kanada)           | ✓ | ✓ |
| Saint Barthélemy          | ✓ | ✓ |
| Saint Kitts och Nevis     | ✓ | ✓ |
| Saint Lucia               | ✓ | ✓ |
| Saint Martin (franska)     | ✓ | ✓ |
| Saint-Pierre och Miquelon | ✓ | ✓ |
| Saint Vincent och Grenadinerna | ✓ | ✓ |
| Sint Maarten (nederländska)      | ✓ | ✓ |
| Sydgeorgien och Sydsandwichöarna | ✓ | ✓ |
| Surinam                  | ✓ | ✓ |
| Trinidad och Tobago       | ✓ | ✓ |
| Turks- och Caicosöarna  | ✓ | ✓ |
| USA             | ✓ | ✓ |
| Uruguay                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Jungfruöarna, Storbritannien   | ✓ | ✓ |
| Jungfruöarna, USA      | ✓ | ✓ |

## <a name="asia"></a>Asien 

| Land/region | Raster paneler Unified | Vektor paneler Unified |
| ------ | :------------------: | :------------------: |
| Afghanistan               |   | Ø |
| Bahrain                   | ✓ | ✓ |
| Bangladesh                |   | Ø |
| Bhutan                    |   | Ø |
| Brittiska territoriet i Indiska Oceanen |   | Ø |
| Brunei                    | ✓ | ✓ |
| Kambodja                  |   | Ø |
| Kina                     |   | Ø |
| Kokosöarna   |   | Ø |
| Demokratiska folkrepubliken Korea |   | Ø |
| Folkrepubliken Kinas särskilda administrativa region Hongkong             | ✓ | ✓ |
| Indien                     | Ø | ✓ | 
| Indonesien                 | ✓ | ✓ |
| Iran                      |   | Ø |
| Irak                      | ✓ | ✓ |
| Israel                    |   | ✓ |
| Japan                     |   | Ø |
| Jordanien                    | ✓ | ✓ |
| Kazakstan                |   | ✓ |
| Kuwait                    | ✓ | ✓ |
| Kirgizistan                |   | Ø |
| Demokratiska folkrepubliken Laos |   | Ø |
| Libanon                   | ✓ | ✓ |
| Folkrepubliken Kinas särskilda administrativa region Macao                 | ✓ | ✓ |
| Malaysia                  | ✓ | ✓ |
| Maldiverna                  |   | Ø |
| Mongoliet                  |   | Ø |
| Myanmar                   |   | Ø |
| Nepal                     |   | Ø |
| Oman                      | ✓ | ✓ |
| Pakistan                  |   | Ø |
| Filippinerna               | ✓ | ✓ |
| Qatar                     | ✓ | ✓ |
| Sydkorea         | ✓ | Ø |
| Saudiarabien              | ✓ | ✓ |
| Senkaku öarna           |   | ✓ |
| Singapore                 | ✓ | ✓|
| Sri Lanka                 |   | Ø |
| Arabrepubliken Syrien      |   | Ø |
| Taiwan                    | ✓ | ✓ |
| Tadzjikistan                |   | Ø |
| Thailand                  | ✓ | ✓ |
| Timor-Leste               |   | Ø |
| Turkmenistan              |   | Ø |
| Förenade Arabemiraten      | ✓ | ✓ |
| USA mindre öar |   | Ø |
| Uzbekistan                |   | Ø |
| Vietnam                   | ✓ | ✓ |
| Jemen                     | ✓ | ✓ |

## <a name="oceania"></a>Oceanien

| Land/region | Raster paneler Unified | Vektor paneler Unified |
| ------ | :------------------: | :------------------: |
| Amerikanska Samoa            |   | ✓ |
| Australien                 | ✓ | ✓ |
| Cooköarna              |   | Ø |
| Fiji                      |   | Ø |
| Franska Polynesien          |   | Ø |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | Ø |
| Marshallöarna          |   | Ø |
| Mikronesien                |   | Ø |
| Nauru                     |   | Ø |
| Nya Kaledonien             |   | Ø |
| Nya Zeeland               | ✓ | ✓ |
| Niue                      |   | Ø |
| Norfolkön            |   | Ø |
| Palau                     |   | Ø |
| Papua Nya Guinea          |   | Ø |
| Pitcairn                  |   | Ø |
| Samoa                     |   | Ø |
| Solomonöarna           |   | Ø|
| Tokelau                   |   | Ø |
| Tonga                     |   | Ø |
| Tuvalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis och Futuna         |   | Ø |


## <a name="europe"></a>Europa

| Land/region | Raster paneler Unified | Vektor paneler Unified |
| ------ | :------------------: | :------------------: |
| Albanien                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Armenien                   | ✓ | Ø |
| Österrike                   | ✓ | ✓ |
| Azerbajdzjan                | ✓ | Ø |
| Vitryssland                   | Ø | ✓ |
| Belgien                   | ✓ | ✓ |
| Bosnia-Herzegovina        | ✓ | ✓ |
| Bulgarien                  | ✓ | ✓ |
| Kroatien                   | ✓ | ✓ |
| Cypern                    | ✓ | ✓ |
| Tjeckien            | ✓ | ✓ |
| Danmark                   | ✓ | ✓ |
| Estland                   | ✓ | ✓ |
| Färöarna             | ✓ | Ø |
| Finland                   | ✓ | ✓ |
| Frankrike                    | ✓ | ✓ |
| Georgia                   | ✓ | Ø |
| Tyskland                   | ✓ | ✓ |
| Gibraltar                 | ✓ | ✓ |
| Grekland                    | ✓ | ✓ |
| Guernsey                  | ✓ | ✓ |
| Ungern                   | ✓ | ✓ |
| Island                   | ✓ | ✓ |
| Irland                   | ✓ | ✓ |
| Isle of Man               | ✓ | ✓ |
| Italien                     | ✓ | ✓ |
| Jan Mayen                 | ✓ | ✓ |
| Jersey                    | ✓ | ✓ |
| Lettland                    | ✓ | ✓ |
| Liechtenstein             | ✓ | ✓ |
| Litauen                 | ✓ | ✓ |
| Luxemburg                | ✓ | ✓ |
| Nordmakedonien           | ✓ | ✓ |
| Malta                     | ✓ | ✓ |
| Moldavien                   | ✓ | ✓ |
| Monaco                    | ✓ | ✓ |
| Montenegro                | ✓ | ✓ |
| Nederländerna               | ✓ | ✓ |
| Norge                    | ✓ | ✓ |
| Polen                    | ✓ | ✓ |
| Portugal                  | ✓ | ✓ |
| Rumänien                   | ✓ | ✓ |
| Ryska federationen        | ✓ | ✓ |
| San Marino                | ✓ | ✓ |
| Serbien                    | ✓ | ✓ |
| Slovakien                  | ✓ | ✓ |
| Slovenien                  | ✓ | ✓ |
| Södra Kurils           | ✓ | ✓ |
| Spanien                     | ✓ | ✓ |
| Svalbard                  | ✓ | ✓ |
| Sverige                    | ✓ | ✓ |
| Schweiz               | ✓ | ✓ |
| Turkiet                    | ✓ | ✓ |
| Ukraina                   | ✓ | ✓ |
| Storbritannien            | ✓ | ✓ |
| Vatikanstaten              | ✓ | ✓ |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Maps rendering finns i [zoomnings nivåer och panel rutnät](zoom-levels-and-tile-grid.md).

Lär dig mer om [täcknings områdena för Maps-routningstjänsten](routing-coverage.md). 
