---
title: WAF (Azure Web Application Firewall) uppfyller anpassade regler
description: Den här artikeln är en översikt över de anpassade reglerna för brand vägg för webbaserade program (WAF) som är kompatibla med Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/31/2020
ms.author: victorh
ms.openlocfilehash: bdb115cf6d0f0aa2c55f6143015a323df6faa114
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83744293"
---
# <a name="geomatch-custom-rules-preview"></a>Ommatchnings anpassade regler (förhands granskning)

Med anpassade regler kan du skapa skräddarsydda regler som passar de exakta behoven för dina program och säkerhets principer. Nu kan du begränsa åtkomsten till dina webb program efter land/region. Precis som med alla anpassade regler kan den här logiken sammanställas med andra regler som passar ditt programs behov.

Om du vill skapa en anpassad regel för geo-filtrering väljer du bara *Geo-Location* som matchnings typ och väljer sedan det land/den region eller de länder/regioner som du vill tillåta/blockera från ditt program. Mer information finns i [så här skapar du anpassade regler i PowerShell](configure-waf-custom-rules.md) och fler anpassade regel exempel (Create-Custom-waf-rules.MD).

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="countryregion-codes"></a>Lands-/regions koder

Om du använder en kors matchnings operator kan väljare vara någon av följande tvåsiffriga lands-eller regions koder. 

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

När du har lärt dig om anpassade regler [skapar du dina egna anpassade regler](create-custom-waf-rules.md).
