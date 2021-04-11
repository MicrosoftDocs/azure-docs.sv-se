---
title: Tal fonetiska uppsättningar – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder det fonetiska alfabetet i Speech service till det internationella fonetiska alfabetet (IPA) och när du ska använda den som angetts.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 8e5480b7412291c427c5eaea94a63343f12c62cf
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077524"
---
# <a name="speech-service-phonetic-sets"></a>Fonetiska uppsättningar för tal tjänst

Tal tjänsten definierar fonetiska alfabet ("telefon uppsättningar" för kort), bestående av sju språk. `en-US`,,,,, `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` och `zh-TW` . Tal tjänstens telefonnummer anges vanligt vis till det <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">internationella fonetiska alfabetet (IPA) </a>. Telefon uppsättningar för tal tjänster används tillsammans med [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md)som en del av det text till tal-erbjudandet. I den här artikeln får du lära dig hur dessa telefon uppsättningar mappas och när du ska använda vilken telefon uppsättning.

# <a name="en-us"></a>[en-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>Engelskt suprasegmentals

| Exempel 1 (start för konsonant, ord initial för vokal) | Exempel 2 (Intervocalic för konsonant, Word medial Nucleus för vokal) | Exempel 3 (CODA för konsonant, Word final för vokal) | Kommentarer |
|--|--|--|--|
| Burger/b er **1** r-g AX r/ | falafel/f AX-l AA **1** -f AX l/ | gitarr/g IH-t AA **1** r/ | Röst tjänst telefon ange stress efter vokalen av den betonade stavelseen |
| inopportune/IH **2** -n AA-p AX r-t Gu 1 n/ | delikhet/d IH-s ih **2**-m AX-l händelsehubbnamnområde 1-r AX-t iy/ | personal/w er er 1 r k-f Ao **2** r s/ | Röst tjänst telefon ange stress efter vokalen under understrykad stavelse |

### <a name="english-vowels"></a>Engelska vokaler

| `sapi` | `ipa` | Exempel 1     | Exempel 2 | Exempel 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy     | `i`   | **EA** t       | f-**ee** l  | Vall **ey**                  |
| ih     | `ɪ`   | **i** f        | f **i** lla  |                             |
| ey     | `eɪ`  | **en** te       | g **a** te  | d-**AY**                     |
| händelsehubbnamnområde     | `ɛ`   | **e** mycket     | p **e** t   | m-**händelsehubbnamnområde** (sällsynt ord slutligen) |
| AE     | `æ`   | **en** ctive    | c **a** t   | n **Ah** (sällsynt ord slutligen) |
| aa     | `ɑ`   | **o**-bstinate | p **o** PPY | r **Ah** (sällsynt ord slutligen) |
| Ao     | `ɔ`   | **o**-intervall    | c **au** se | Ut **Ah**                    |
| ojsan     | `ʊ`   | b **säkerhets** k      |           |                             |
| ISA     | `oʊ`  | **o** LD       | cl **o** Ne | g **o**                      |
| Gu     | `u`   | **U** ber      | b **säkerhets** St | t-**säkerhets**                     |
| Ah     | `ʌ`   | **u** ncle     | c **u** t   |                             |
| ay     | `aɪ`  | **i**-CE       | b-**i** te  | fl-**y**                     |
| AW     | `aʊ`  | **OU** t       | s **OU** to | c **-** av                     |
| oy     | `ɔɪ`  | **OI** l       | j **OI** n  | t-**Oy**                     |
| y-Gu   | `ju`  | **Yu** ma      | h **man** | f **y**                     |
| AX     | `ə`   | **en** go       | wom **a** n | är **en**                    |

### <a name="english-r-colored-vowels"></a>Engelska R-färgade vokaler

| `sapi` | `ipa` | Exempel 1    | Exempel 2      | Exempel 3  |
|--------|-------|--------------|----------------|------------|
| IH r   | `ɪɹ`  | **öron** s     | t **IR**-amisu   | n **öron**   |
| händelsehubbnamnområde r   | `ɛɹ`  | **luft** plan | app **ar**-ently | SC **ar** e  |
| ojsan r   | `ʊɹ`  |              |                | c **ditt** e   |
| Ay r   | `aɪɹ` | **Irland**, mark  | f **IR**-eplace  | CH **oir**  |
| AW r   | `aʊɹ` | **timme** s    | p **ägre** full   | s **vår**   |
| Ao r   | `ɔɹ`  | **eller** ange   | m **eller** Al      | s **Oar**   |
| AA r   | `ɑɹ`  | **ar**-tist   | s:t **ar** t      | c **ar**    |
| er r   | `ɝ`   | **öron**    | b **IR** d       | f **Your**    |
| AX r   | `ɚ`   |              | alla **er**-Gy    | d **er** |

### <a name="english-semivowels"></a>Engelskt Semivowels

| `sapi` | `ipa` | Exempel 1           | Exempel 2  | Exempel 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w** i:te, s **UE** | Al **w** AYS |           |
| y      | `j`   | **y**-ARD, f **e** w   | på **i**  |           |

### <a name="english-aspirated-oral-stops"></a>Engelska inpiratkopierade muntliga stopp

| `sapi` | `ipa` | Exempel 1 | Exempel 2   | Exempel 3  |
|--------|-------|-----------|-------------|------------|
| P      | `p`   | **p** ut   | ha s **PP** en  | FLA **p**   |
| b      | `b`   | **b**-ig   | NUM **b** er  | CRA **b**   |
| t      | `t`   | **t**-ALK  | capi **t** Al | sough **t** |
| d      | `d`   | **d**-ig   | kördes **d** om  | ro **d**    |
| k      | `k`   | **c** ut   | SLA **CK** er | IRA-**q**   |
| g      | `g`   | **g** o    | a **g** o     | dra **g**   |

### <a name="english-nasal-stops"></a>Nasal för engelska stoppas

| `sapi` | `ipa` | Exempel 1        | Exempel 2  | Exempel 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m** at, sammantaget   | ca **m**-era | Roo **m**    |
| n      | `n`   | **n** o, s **n** | te **n** t   | kyckling **n** |
| naturgas     | `ŋ`   |                  | Li **n** k   | s **ing**    |

### <a name="english-fricatives"></a>Engelskt fricatives

| `sapi` | `ipa` | Exempel 1   | Exempel 2        | Exempel 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f** ork    | Le **f** t         | HAL **f**   |
| v      | `v`   | **v** ärde   | e **v**-version        | Lo **v** e   |
| i:te     | `θ`   | **th** i    | **Empat** y      | mån  |
| DH     | `ð`   | **en**    | Mo **er**       | smoo |
| s      | `s`   | **s**     | RI **s** k         | fakta **s**  |
| z      | `z`   | **ö**-AP     | Bu **s** y         | barn **s**   |
| f     | `ʃ`   | **sh** e    | abbrevia **ti** | ru-**sh**   |
| zh     | `ʒ`   | **J** acques | lä **s** urera     | Gara **g** e |
| h      | `h`   | **h**-ELP    | en **h**-ning      | a –**h** a!  |

### <a name="english-affricates"></a>Engelskt affricates

| `sapi` | `ipa` | Exempel 1 | Exempel 2    | Exempel 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **CH** in  | Fu **t** urera   | Atta **CH** |
| jh     | `dʒ`  | **j** Oy   | Ori **g** Inal | Oran **g** e |

### <a name="english-approximants"></a>Engelskt approximants

| `sapi` | `ipa` | Exempel 1          | Exempel 2  | Exempel 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**-ID, g **l** AD  | PA **l** ACE | Chi **lla** |
| r      | `ɹ`   | **r** Ed, b **r** ing | bo-**RR** | ta **r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Franska suprasegmentals

Tal tjänstens telefon uppsättning placerar stress efter vokalen för den betonade stavelseen, men `fr-FR` tal tjänst telefonnumret har inte stöd för IPA-understressen "ˌ". Om IPA-understressen behövs bör du använda IPA direkt.

### <a name="french-vowels"></a>Franska vokaler

| `sapi` | `ipa` | Exempel 1     | Exempel 2       | Exempel 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **en** rbre     | p **a** TTE       | IR **a**   |
| aa     | `ɑ`   |               | p **â** te        | p **a** s   |
| AA ~   | `ɑ̃`  | **en** Fant    | ENF **en** t      | t **EM** PS |
| AX     | `ə`   |               | p **e** Tite      | l **e**    |
| händelsehubbnamnområde     | `ɛ`   | **e**-lle      | p **e** RDU       | ét **AI** t |
| gemenskaps     | `ø`   | **œu** FS      | CR i **EU**     | Qu **EU**  |
| ey     | `e`   | ému           | crétin          | ôté       |
| händelsehubbnamnområde ~   | `ɛ̃`  | **im**-port | p **Ein** tt    | mat **i** |
| iy     | `i`   | **jag** dée      | PET **i** te      | är **jag**   |
| ø     | `œ`   | **œu** f       | p **EU** r        |           |
| o     | `ɔ`   | **o**-bstacle  | c **o**-RPS       |           |
| till ~   | `ɔ̃`  | **på** storlek      | r **på** deur     | b **på**   |
| ISA     | `o`   | **au**-diteur  | b **Eau** koppling    | p **ô**    |
| OE ~   | `œ̃ ` | **registrering**        | l **un** di       | br **o**  |
| Gu     | `u`   | **OU**-trage   | intr **OU** vable | **ou**    |
| uy     | `y`   | **u** Ne       | p **u** NIR       | él **u**   |

### <a name="french-consonants"></a>Franska konsonanter

| `sapi` | `ipa` | Exempel 1   | Exempel 2     | Exempel 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**-ête    | ha **b**-Ille   | ro **b** e                         |
| d      | `d`   | **d** Irland    | Ron **d** EUR   | Chau **d** e                       |
| f      | `f`   | **f**-Emme   | Su **FF**-IXE   | bo **f**                          |
| g      | `g`   | **g** auche  | é **g** ALE     | BA-**Gu** e                        |
| naturgas     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)Park |
| hy     | `ɥ`   | h **u** i Il   | n **u** Irland     |                                  |
| k      | `k`   | **c**-Arte   | é **c** aille   | vara **c**                          |
| l      | `l`   | **l**-Ong    | é **l** Irland     | BA **l**                          |
| m      | `m`   | **m**-Adam  | AI **m** er     | Po **mm** e                        |
| n      | `n`   | **n** ou    | te **n** IR     | bo **NN** e                        |
| nj     | `ɲ`   |             |               | PEI **GN** e                       |
| P      | `p`   | **p** Atte   | Re-**s** som     | ca **p**                          |
| r      | `ʁ`   | **r** på     | Cha **r** IoT   | skickati **r**                       |
| s      | `s`   | **s** Ourir  | en **SS** EZ     | PA **SS** e                        |
| f     | `ʃ`   | **CH** för handsare | ma **CH** AD   | Po **,** e                        |
| t      | `t`   | **t**-ête    | ô **t** er      | Ne **t**                          |
| v      | `v`   | **v**-överordnad    | i **v**-retur  | rê **v** e                         |
| w      | `w`   | **OU** i     | f **OU** AD    |                                  |
| y      | `j`   | **j** OD     | p **i** étiner  | Mars-**Ille**                    |
| z      | `z`   | * * z * * éro   | Rai **s** onner | ro **s** e                         |
| zh     | `ʒ`   | **j** Ardin  | man **g** er    | Piè **g** e                        |
|        | `n‿`  |             |               | u **n** Arbre                     |
|        | `t‿`  |             |               | Quan **d**                        |
|        | `z‿`  |             |               | di **x**                          |

<a id="fr-1"></a>
**1** *endast för några främmande ord.*

> [!TIP]
> `fr-FR`Tal tjänst telefonnumret har inte stöd för följande franska liasions, `n‿` , `t‿` , och `z‿` . Om de behövs bör du överväga att använda IPA direkt.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Tyska suprasegmentals

| Exempel 1 (start för konsonant, ord initial för vokal) | Exempel 2 (Intervocalic för konsonant, Word medial Nucleus för vokal) | Exempel 3 (CODA för konsonant, Word final för vokal) | Kommentarer |
|--|--|--|--|
| Anders/a **1** n-d AX r s/ | Multiplikationszeichen/m ojsan l-t iy-p l iy-k a-TS y: **1** n s – TS AY-c n/ | Biologie/b iy-on-l-g iy **1**/ | Röst tjänst telefon ange stress efter vokalen av den betonade stavelseen |
| Allgemeinwissen/a **2** l-g AX-m AY 1 n-v IH-s n/ | Abfallentsorgungsfirma/a 1 p-f a l-^ händelsehubbnamnområde n t-z och **2** AX r-g ojsan ng s-f IH AX r-m a/ | Computertomographie/k-m-p y Gu 1-t AX r-t-d-d-DD-g r a-f iy **2**/ | Röst tjänst telefon ange stress efter vokalen under understrykad stavelse |

### <a name="german-vowels"></a>Tyska vokaler

| `sapi` | `ipa`     | Exempel 1                             | Exempel 2     | Exempel 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| en     | `aː`      | **Ett** ber                              | Maßst **a** b   | Schem **a**                         |
| a      | `a`       | **En** bfall                            | B **a** CH      | Agath **a**                         |
| o     | `ɔ`       | **O**-sten                             | PF **o** sten   |                                    |
| händelsehubbnamnområde    | `ɛː`      | **Ä** hnlichkeit                       | B **ä** r       | [<sup>1</sup>](#de-v-1) Fasci **AE** |
| händelsehubbnamnområde     | `ɛ`       | **ä** ndern                            | ProZ **e** NT   | Amygdal **AE**                      |
| AX     | `ə`       | [<sup>2</sup>](#de-v-2)v **e** rstauen | Aach **e** n    | Frag **e**                          |
| iy     | `iː`      | **Jag** körde                              | ABB **IE** gt   | Relativitätstheor **IE**            |
| ih     | `ɪ`       | **Jag** nnung                            | s **i** ngen    | Trä **y**                          |
| gemenskaps     | `øː`      | **Ö** Ski                              | ABL **ö**-sten  | Malm **ö**                          |
| ISA     | `o`, `oː` | **o**-hnE                              | Balk **o** n    | Trept                        |
| ø     | `œ`       | **Ö** ffnung                           | BEF **ö**-rdern |                                    |
| ey     | `e`, `eː` | **E**-Berhard                          | ABF **e** gt    | b                                  |
| Gu     | `uː`      | **U**                               | H **u** t       | AKK **u**                           |
| ojsan     | `ʊ`       | **U** nterschiedes                     | b **u** NT      |                                    |
| UE     | `yː`      | **Ü** bermut                           | PFL **ü** gt    | Män **ü**                           |
| uy     | `ʏ`       | **ü** PPIG                             | S **y**-stam    |                                    |

<a id="de-v-1"></a>
**1** *endast i ord av främmande ursprung, t. ex.: Fasci **AE**.*<br>
<a id="de-v-2"></a>
**2** *Word-intially endast i ord av främmande ursprung, till exempel **en** ppointment. Stavelse – inlednings vis i: ' v **e** rstauen.*

### <a name="german-diphthong"></a>Tyska Diphthong

| `sapi` | `ipa`       | Exempel 1    | Exempel 2          | Exempel 3 |
|--------|-------------|--------------|--------------------|-----------|
| ay     | `ai`        | **EI** nsam   | Unabhängigk **EI** t | ABT **EI** |
| AW     | `au`        | **au**-ßen    | ABB **au** St        | St **au**  |
| oy     | `ɔy`, `ɔʏ̯` | **EU**-phorie | TR **äu** MT         | SCH **EU** |

### <a name="german-semivowels"></a>Tyska semivowels

| `sapi` | `ipa` | Exempel 1 | Exempel 2    | Exempel 3  |
|--------|-------|-----------|--------------|------------|
| AX r   | `ɐ`   |           | abänd **er** n | Lås **er** |

### <a name="german-consonants"></a>Tyska konsonanter

| `sapi` | `ipa` | Exempel 1 | Exempel 2 | Exempel 3 |
|--|--|--|--|--|
| b | `b` | **B**-ank | | [<sup>1</sup>](#de-c-1) PU **b** | 
| c | `ç` | **CH** EMIE | mögli **CH** St | [<sup>2</sup>](#de-c-2)i **CH** |
| d | `d` | **d**-anken | [<sup>3</sup>](#de-c-3) Längd **d** l | [<sup>4</sup>](#de-c-4) Clau **d** e | 
| jh | `ʤ` | **J** EFF | gemana **g** t | [<sup>5</sup>](#de-c-5) Kanal **g** e |
| f | `f` | **F**-ahrtdauer | Angri **AA** slustig | abbruchrei **f** |  
| g | `g` | **g** ut |  [<sup>6</sup>](#de-c-6) GRE **g** |  |
| h | `h` | **H**-ausanbau |  |  | 
| y | `j` | **J** OD | Reakt **i** | HU **i** | 
| k | `k` | **K** OMA | Aspe **k** t | FLEC **k** | 
| l | `l` | **l** au | ähne **l** n | zuvie **l** | 
| m | `m` | **M** ut | A **m** t | Leh **m** | 
| n | `n` | **n** un | u **n** d | Okej **n** | 
| naturgas | `ŋ` | [<sup>7</sup>](#de-c-7)**ng** Uyen | Schwa **tomma** | R **ing** | 
| P | `p` | **P** Artner | abru **p** t | Ti **p** | 
| växling | `pf` | **PF**-ERD | damm-**PF** t | Till **PF** |
| r | `ʀ`, `r`, `ʁ` | **R**-Eise | KNU-**resurspost** t | Haa **r** | 
| s | `s` | [<sup>8</sup>](#de-c-8)**S** taccato | BI **s** t | Mie **s** | 
| f | `ʃ` | **SCH** egel | mi **SCH** t | Lappi **SCH** | 
| t | `t` | **T**-Raum | S **t**-raße | MK **t** | 
| ts | `ts` | **Z** μg | Ar **z** t | Wit **z** | 
| ch | `tʃ` | **Tsch** echien | aufgepu **tsch** t | bundesdeu **tsch** | 
| v | `v` | **v**-Inken | Q **u** Alle | [<sup>9</sup>](#de-c-9) Gr **säkerhets** ve | 
| x | [<sup>10</sup>](#de-c-10) `x` ,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) BA **CH** erach | Ma **-** t mögli **CH** St | Schma **CH**  |
| z | `z` | **s** uper |  |  | 
| zh | `ʒ` | **G** enre | B **re**-ezinski | Edvi **g** e |

<a id="de-c-1"></a>
**1** *endast i ord av främmande ursprung, t. ex.: PU **b**.*<br>
<a id="de-c-2"></a>
**2** *mjuk "CH" efter "e" och "i"*<br>
<a id="de-c-3"></a>
**3** *endast i ord av främmande ursprung, t. ex.: längd **d** l.*<br>
<a id="de-c-4"></a>
**4** *endast i ord av främmande ursprung, till exempel: Clau **d** e.*<br>
<a id="de-c-5"></a>
**5** *endast i ord av främmande ursprung, till exempel: n **g** e.*<br>
<a id="de-c-6"></a>
**6** *ord – endast Terminal i ord som är av främmande ursprung, till exempel GRE **g**.*<br>
<a id="de-c-7"></a>
**7** *endast i ord av främmande ursprung, till exempel: **ng** Uyen.*<br>
<a id="de-c-8"></a>
**8** *endast i ord av främmande ursprung, t. ex.: **S** taccato.*<br>
<a id="de-c-9"></a>
**9** *endast i ord av främmande ursprung, t. ex.: gr **säkerhets** ve.*<br>
<a id="de-c-10"></a>
**10** *IPA `x` är en svår "CH" efter alla icke-främre vokaler (a, AA, Oh, ojsan, Gu och Diphthong AW).*<br>
<a id="de-c-11"></a>
**11** *IPA `ç` är en mjuk "CH" efter front vokaler (IH, IY, händelsehubbnamnområde, AE, uy, UE, OE, EU även i diphthongs Ay, Oy) och konsonanter*<br>
<a id="de-c-12"></a>
**12** *ord – inlednings vis endast i ord av främmande ursprung, t. ex.: **J** Uan. Stavelse-från början även i ord som: ba **CH** erach.*<br>

### <a name="german-oral-consonants"></a>Tyska orala konsonanter

| `sapi` | `ipa` | Exempel 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich/b AX-^ a 1 x t-l IH c/ |

> [!NOTE]
> Vi måste lägga till en [GS \] -telefon mellan två distinkta vokaler, förutom att de två vokalerna är en äkta Diphthong. Den här orala konsonanten är en glottal-stopp, mer information finns i <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank"> glottal Stop <span class="docon docon-navigate-external x-hidden-focus"> </a> </a> .

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Spanska vokaler

| `sapi` | `ipa` | Exempel 1    | Exempel 2     | Exempel 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **en** LTO     | c **a** ntar    | CAS **a**     |
| i      | `i`   | **jag** bérica  | av **i** Spa    | skatt **i**     |
| e      | `e`   | **e**-LeFante | vid **e** nKlicka    | elefant **e** |
| o      | `o`   | **o**-Caso    | ENC **o**-ntrar | ocasenc **o** |
| ä      | `u`   | **u** sted    | p **u** nta     | Juanl **u**   |

### <a name="spanish-consonants"></a>Spanska konsonanter

| `sapi` | `ipa`      | Exempel 1  | Exempel 2      | Exempel 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**-aobab |                | **s b**        |
|        | `β`        |            | Bao **b** AB     | baoba **b**     |
| ch     | `tʃ`       | **CH** Eque | Co **CH** e      | Marraque **CH** |
| d      | `d`        | **d**-Edo   |                | portlan **d**   |
|        | `ð`        |            | de **d** o       | Verda **d**     |
| f      | `f`        | **f**-ácil  | ele **f** för hands   | PU **f**        |
| g      | `g`        | **g** Anga  |                | dópin **g**     |
|        | `ɣ`        |            | a **g** UA       | tuare **g**     |
| j      | `j`        | **jag** Odo   | Cal **i** listan   | Re-**y**        |
| JJ     | `j.j` `jj` |            | Vi **lla** a      |                |
| k      | `k`        | **c**-oche  | bo,**c** a       | titáni **c**    |
| l      | `l`        | **l**-ápiz  | a **l** a        | sladdar **l**     |
| lla     | `ʎ`        | **ll**-Ave  | desarro **lla** o |                |
| m      | `m`        | **m**-ordning | a **m** p.a.       | álbu **m**      |
| n      | `n`        | **n** Ada   | CE **n** a       | rató **n**      |
| nj     | `ɲ`        | **ñ**-Aña   | Ara **ñ**-azo    |                |
| P      | `p`        | **p** OCA   | till **p** o       | sluta **p**       |
| r      | `ɾ`        |            | ca **r** a       | Abri **r**      |
| post     | `r`        | **r**-adio  | Co **RR** e      | PU-**resurspost**       |
| s      | `s`        | **s** ACO   | va **s** o       | Pelo **s**      |
| t      | `t`        | **t**-oldo  | a **t** ar       | Disque **t**    |
| i:te     | `θ`        | **z**-EBRA  | en **z** UL       | lápi **z**      |
| w      | `w`        | h **u** ESO  | AG **u** a       | Gua **u**       |
| x      | `x`        | **j** OTA   | a **j** o        | relo **j**      |

> [!TIP]
> `es-ES`Tal tjänstens telefon uppsättning stöder inte följande spanska IPA, `β` , `ð` , och `ɣ` . Om de behövs bör du överväga att använda IPA direkt.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

Tal tjänst telefonnumret för `zh-CN` är baserat på den inbyggda uppsättningen för <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank"></a> telefonnummer.

### <a name="tone"></a>Lämplig ton

| Pinyin-ton | `sapi` | Character-exempel |
|-------------|--------|-------------------|
| mā          | MA 1  | 妈                 |
| má          | ma 2  | 麻                 |
| mǎ          | ma 3  | 马                 |
| mà          | ma 4  | 骂                 |
| &          | ma 5  | 嘛                 |

#### <a name="example"></a>Exempel

| Tecken | Tjänst för taligenkänning                |
|-----------|-------------------------------|
| 组织关系      | zu 3-Zhi 1-Guan 1 – XI 5 |
| 累进        | Lei-3-Jin 4                 |
| 西宅巷       | XI 1-Zhai 2-Xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

Tal tjänst telefonnumret för `zh-TW` är baserat på den inbyggda telefonens <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">Bopomofo </a> -uppsättning.

### <a name="tone"></a>Lämplig ton

| Tal tjänst tonen | Bopomofo-ton | Exempel (Word) | Röst tjänst telefoner | Bopomofo | Pinyin (拼音) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | tomt         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚˇ                   | ㄉㄚˇ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥˇ ㄗ˙               | 一ㄥˇ ㄗ˙  | Yǐng Zi    |

#### <a name="example"></a>Exempel

| Tecken | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡˇ      |
| 然后        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢˇㄉㄧㄠˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

Tal tjänstens telefon uppsättning för `ja-JP` baseras på den inbyggda telefon- <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">kana </a> -uppsättningen.

### <a name="stress"></a>Stryka

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` mainstress |
| `+`    | `ˌ` under stress  |

#### <a name="example"></a>Exempel

| Tecken | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | Go-ˈ wɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化       | サィテキカ +  | sajitecikaˌ |

***
