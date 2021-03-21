---
title: Geo-filtrering på en domän för Azures front dörr | Microsoft Docs
description: I den här artikeln får du lära dig om geo-filtrering för Azures front dörr
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2020
ms.author: duau
ms.reviewer: tyao
ms.openlocfilehash: 33ad17e3024011368c909a89e9164ca06d044bad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612090"
---
# <a name="geo-filtering-on-a-domain-for-azure-front-door"></a>Geo-filtrering på en domän för Azures front dörr

Som standard svarar Azures front dörr på alla användar förfrågningar oavsett var den plats där begäran kommer från finns. I vissa fall kanske du vill begränsa åtkomsten till ditt webb program i länder/regioner. Med tjänsten brand vägg för webbaserade program (WAF) i front dörren kan du definiera en princip med hjälp av anpassade åtkomst regler för en viss sökväg på slut punkten, antingen för att tillåta eller blockera åtkomst från angivna länder/regioner. 

En WAF-princip innehåller en uppsättning anpassade regler. Regeln består av matchnings villkor, en åtgärd och en prioritet. I ett matchnings villkor definierar du ett matchande variabel-, operator-och match-värde. För en geo filtrerings regel är en match-variabel REMOTE_ADDR, operatorn är en geografisk matchning och värdet är ett land/region med två bokstäver av intresse. "ZZ"-lands kod eller "okänt"-land fångar upp IP-adresser som ännu inte har mappats till ett land i vår data uppsättning. Du kan lägga till ZZ i matchnings villkoret för att undvika falska positiva identifieringar. Du kan kombinera ett villkor för en geografisk matchning och en REQUEST_URI sträng matchnings villkor för att skapa en Sök vägs baserad geo-filtrerings regel. 


Du kan konfigurera en princip för geo-filtrering för din front dörr genom att använda [Azure PowerShell](front-door-tutorial-geo-filtering.md) eller genom att använda en [snabb starts mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering).

## <a name="countryregion-code-reference"></a>Kod referens för land/region

|Lands-/regionkod | Land/region namn |
| ----- | ----- |
| AD | Andorra |
| AE | Förenade Arabemiraten|
| AF | Afghanistan|
| AG | Antigua och Barbuda|
| AL | Albanien|
| AM | Armenien|
| AO | Angola|
| AR | Argentina|
| AS | Amerikanska Samoa|
| AT | Österrike|
| AU | Australien|
| AZ | Azerbajdzjan|
| BA | Bosnien och Hercegovina|
| BB | Barbados|
| BD | Bangladesh|
| BE | Belgien|
| BF | Burkina Faso|
| BG | Bulgarien|
| BH | Bahrain|
| BI | Burundi|
| BJ | Benin|
| BL | Saint Barthélemy|
| BN | Brunei Darussalam|
| BO | Bolivia|
| BR | Brasilien|
| BS | Bahamas|
| BT | Bhutan|
| BW | Botswana|
| BY | Vitryssland|
| BZ | Belize|
| CA | Kanada|
| CD | Demokratiska republiken Kongo|
| CF | Centralafrikanska Republiken|
| CH | Schweiz|
| CI | Cote d'Ivoire|
| CL | Chile|
| CM | Kamerun|
| CN | Kina|
| CO | Colombia|
| CR | Costa Rica|
| CU | Kuba|
| CV | Cabo Verde|
| CY | Cypern|
| CZ | Tjeckien|
| DE | Tyskland|
| DK | Danmark|
| DO | Dominikanska republiken|
| DZ | Algeriet|
| EC | Ecuador|
| EE | Estland|
| EG | Egypten|
| ES | Spanien|
| ET | Etiopien|
| FI | Finland|
| FJ | Fiji|
| FM | Mikronesien|
| FR | Frankrike|
| GB | Storbritannien|
| GE | Georgia|
| GF | Franska Guyana|
| GH | Ghana|
| GN | Guinea|
| GP | Guadeloupe|
| GR | Grekland|
| GT | Guatemala|
| GY | Guyana|
| HK | Folkrepubliken Kinas särskilda administrativa region Hongkong|
| HN | Honduras|
| HR | Kroatien|
| HT | Haiti|
| HU | Ungern|
| ID | Indonesien|
| IE | Irland|
| IL | Israel|
| IN | Indien|
| IQ | Irak|
| IR | Islamiska republiken Iran|
| IS | Island|
| IT | Italien|
| JM | Jamaica|
| JO | Jordanien|
| JP | Japan|
| KE | Kenya|
| KG | Kirgizistan|
| KH | Kambodja|
| KI | Kiribati|
| KN | Saint Kitts och Nevis|
| KP | Nordkorea|
| KR | Sydkorea|
| KW | Kuwait|
| KY | Caymanöarna|
| KZ | Kazakstan|
| LA | Demokratiska folkrepubliken Laos|
| LB | Libanon|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Litauen|
| LU | Luxemburg|
| LV | Lettland|
| LY | Libyen |
| MA | Marocko|
| MD | Moldavien|
| MG | Madagaskar|
| MK | Nordmakedonien|
| ML | Mali|
| MM | Myanmar|
| MN | Mongoliet|
| MO | Folkrepubliken Kinas särskilda administrativa region Macao|
| MQ | Martinique|
| MR | Mauretanien|
| MT | Malta|
| MV | Maldiverna|
| MW | Malawi|
| MX | Mexico|
| MY | Malaysia|
| MZ | Moçambique|
| NA | Namibia|
| NE | Niger|
| NG | Nigeria|
| NI | Nicaragua|
| NL | Nederländerna|
| NO | Norge|
| NP | Nepal|
| NR | Nauru|
| NZ | Nya Zeeland|
| OM | Oman|
| PA | Panama|
| PE | Peru|
| PH | Filippinerna|
| PK | Pakistan|
| PL | Polen|
| PR | Puerto Rico|
| PT | Portugal|
| PW | Palau|
| PY | Paraguay|
| QA | Qatar|
| RE | Réunion|
| RO | Rumänien|
| RS | Serbien|
| RU | Ryska federationen|
| RW | Rwanda|
| SA | Saudiarabien|
| Standardavvikelse | Sudan|
| SE | Sverige|
| SG | Singapore|
| SI | Slovenien|
| SK | Slovakien|
| SN | Senegal|
| SO | Somalia|
| SR | Surinam|
| SS | Sydsudan|
| SV | El Salvador|
| SY | Arabrepubliken Syrien|
| SZ | Swaziland|
| TC | Turks- och Caicosöarna|
| TG | Togo|
| TH | Thailand|
| TN | Tunisien|
| TR | Turkiet|
| TT | Trinidad och Tobago|
| TW | Taiwan|
| TZ | Tanzania|
| UA | Ukraina|
| UG | Uganda|
| USA | USA|
| UY | Uruguay|
| UZ | Uzbekistan|
| VC | Saint Vincent och Grenadinerna|
| VE | Venezuela|
| VG | Jungfruöarna, Storbritannien|
| VI | Jungfruöarna, USA|
| VN | Vietnam|
| ZA | Sydafrika|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Lär dig hur du [konfigurerar en princip för geo-filtrering WAF](front-door-tutorial-geo-filtering.md).
