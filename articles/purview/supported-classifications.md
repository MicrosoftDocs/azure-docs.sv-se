---
title: Lista över klassificeringar som stöds
description: Den här sidan visar de system klassificeringar som stöds i Azure avdelningens kontroll.
author: anmuk601
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 4/1/2021
ms.openlocfilehash: e1d3d495d958465e966701aa7ce91bc2706b48e0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219594"
---
# <a name="supported-classifications-in-azure-purview"></a>Klassificeringar som stöds i Azure avdelningens kontroll

Den här artikeln innehåller de system klassificeringar som stöds och definieras i Azure avdelningens kontroll (för hands version).


- **Distinkt matchnings tröskel**: det totala antalet distinkta data värden som måste hittas i en kolumn innan skannern kör data mönstret på den. Tröskelvärdet för distinkt matchning har inget att göra med mönster matchning, men det är ett krav för mönster matchning. Reglerna för system klassificering kräver att det finns minst 8 distinkta värden i varje kolumn för att de ska kunna klassificeras. Systemet måste ha det här värdet för att se till att kolumnen innehåller tillräckligt med data för skannern för att kunna klassificera den korrekt. En kolumn som innehåller flera rader som innehåller värdet 1 klassificeras till exempel inte. Kolumner som innehåller en rad med ett värde och resten av raderna har null-värden får inte heller klassificeras. Om du anger flera mönster gäller det här värdet för var och en av dem.

- **Minsta matchnings tröskel**: det är den minsta procent andelen data värdes matchningar i en kolumn som måste hittas av skannern för att klassificeringen ska tillämpas. System klassificering svärdet är inställt på 60%.


## <a name="defined-system-classifications"></a>Definierade system klassificeringar

Azure avdelningens kontroll klassificerar data efter [regex](https://wikipedia.org/wiki/Regular_expression) -och [blomma-filter](https://wikipedia.org/wiki/Bloom_filter). I följande listor beskrivs formatet, mönstret och nyckelorden för de Azure avdelningens kontroll-definierade system klassificeringarna.

Varje klassificerings namn har prefixet MICROSOFT.

## <a name="bloom-filter-classifications"></a>Filter klassificeringar för blomma

## <a name="city-country-and-place"></a>Stad, land och plats

Filter för stad, land och ort har förberetts med bästa tillgängliga data uppsättningar för att förbereda data.

## <a name="person"></a>Person

Filtret för person blomma har förberetts med de två två data uppsättningarna nedan.

- [2010 amerikanska inventerings data för efter namn (162-K-poster)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Populära barn namn (från SSN), med alla år 1880-2019 (98-K-poster)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>RegEx-klassificeringar

## <a name="aba-routing"></a>ABA-routning

### <a name="format"></a>Format

Nio siffror som kan vara i ett formaterat eller oformaterat mönster

### <a name="pattern"></a>Mönster

Formatera

- fyra siffror börjar med 0, 1, 2, 3, 6, 7 eller 8
- ett bindestreck
- fyra siffror
- ett bindestreck
- en siffra

Oformaterad: nio siffror i följd som börjar med 0, 1, 2, 3, 6, 7 eller 8

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>DNI-nummer (Argentina National Identity)

### <a name="format"></a>Format

Åtta siffror med eller utan punkter

### <a name="pattern"></a>Mönster

Åtta siffror:

- två siffror
- en valfri period
- tre siffror
- en valfri period
- tre siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>Bank konto nummer för Australien

### <a name="format"></a>Format

Sex till 10 siffror med eller utan ett filial nummer för bank tillstånd

### <a name="pattern"></a>Mönster

Konto numret är sex till 10 siffror.

Avdelnings nummer för Australiens bank status:

- tre siffror
- ett bindestreck
- tre siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>Enhets licens nummer för Australien

### <a name="format"></a>Format
Nio bokstäver och siffror

### <a name="pattern"></a>Mönster
nio bokstäver och siffror:

- två siffror eller bokstäver (inte Skift läges känsliga)
- två siffror
- fem siffror eller bokstäver (inte Skift läges känsliga)

ELLER

- en till två valfria bokstäver (inte Skift läges känsligt)
- fyra till nio siffror

ELLER

- nio siffror eller bokstäver (inte Skift läges känsliga)

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australian-medicare-number"></a>Australiensisk Medicare-nummer

### <a name="format"></a>Format

10-11 siffror

### <a name="pattern"></a>Mönster

10-11 siffror:

- den första siffran är inom intervallet 2-6
- nionde siffran är en kontroll siffra
- den tionde siffran är ärendets siffra
- elfte siffror (valfritt) är det enskilda talet

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_australia_medicare_number"></a>Keyword_Australia_Medicare_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>Australien Passport-nummer

### <a name="format"></a>Format

En bokstav följt av sju siffror

### <a name="pattern"></a>Mönster

En bokstav (inte Skift läges känsligt) följt av sju siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_passport"></a>Passport-nyckelord \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>Nyckelord för \_ Australien \_ Passport- \_ nummer

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>Australien, skatte fil nummer

### <a name="format"></a>Format

åtta till nio siffror

### <a name="pattern"></a>Mönster

åtta till nio siffror visas normalt med blank steg enligt följande:

- tre siffror
- ett valfritt utrymme
- tre siffror
- ett valfritt utrymme
- två till tre siffror där den sista siffran är en kontroll siffra

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_australia_tax_file_number"></a>Nyckelord, \_ Australien, \_ skatte \_ fil \_ nummer

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>Belgien National Number

### <a name="format"></a>Format

11 siffror plus valfria avgränsare

### <a name="pattern"></a>Mönster

11 siffror plus avgränsare:

- sex siffror och två valfria punkter i formatet åå. MM. DD för födelse datum
- En valfri avgränsare från punkt, bindestreck, blank steg
- tre sekventiella siffror (udda för hanar, även för hondjur)
- En valfri avgränsare från punkt, bindestreck, blank steg
- två kontroll siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_belgium_national_number"></a>Nyckelord \_ Belgien \_ National \_ Number

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>Brasilien CPF-nummer

### <a name="format"></a>Format

11 siffror som innehåller en kontroll siffra och som kan formateras eller formateras

### <a name="pattern"></a>Mönster

Formatera

- tre siffror
- en period
- tre siffror
- en period
- tre siffror
- ett bindestreck
- två siffror, som är kontroll siffror

Oformaterat:

- 11 siffror där de sista två siffrorna är kontroll siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_brazil_cpf"></a>\_CPF för Brasilien \_

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>Brasilien juridisk enhets nummer (CNPJ)

### <a name="format"></a>Format

14 siffror som innehåller ett registrerings nummer, avdelnings nummer och kontroll siffror, plus avgränsare

### <a name="pattern"></a>Mönster

14 siffror, plus avgränsare:

- två siffror
- en period
- tre siffror
- en period
- tre siffror (de första åtta siffrorna är registrerings numret)
- ett snedstreck
- fyrsiffrigt förgrenings nummer
- ett bindestreck
- två siffror, som är kontroll siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_brazil_cnpj"></a>\_CNPJ för Brasilien \_

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>Nationellt identifierings kort för Brasilien (RG)

### <a name="format"></a>Format

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Mönster

:::no-loc text="Registro Geral (old format):":::

- två siffror
- en period
- tre siffror
- en period
- tre siffror
- ett bindestreck
- en siffra, som är en kontroll siffra

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 siffror
- ett bindestreck
- en siffra, som är en kontroll siffra

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_brazil_rg"></a>\_RG för Brasilien \_

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>Bank konto nummer för Kanada

### <a name="format"></a>Format

7 eller 12 siffror

### <a name="pattern"></a>Mönster

Ett nummer för ett Canada bank konto är 7 eller 12 siffror.

Ett heltal för bank konto överföring är:

- fem siffror
- ett bindestreck
- tre siffror eller
- 0 noll &quot;&quot;
- åtta siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_canada_bank_account_number"></a>Nyckelord för \_ Kanada \_ Bank \_ konto \_ nummer

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>Licens nummer för Kanadas driv rutin

### <a name="format"></a>Format

Varierar per provins

### <a name="pattern"></a>Mönster

Olika mönster täcker Alberta, British Columbia, Manitoba, New Brunswick, Newfoundland/Labrador, Nova Scotia, Ontario, Prince Edward ön, Quebec och Saskatchewan

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_province_name_drivers_license_name"></a>Keyword \_ [provins \_ Name] \_ driv Rutinens \_ licens \_ namn

- Region förkortningen, till exempel AB
- Provins namnet, till exempel Alberta

#### <a name="keyword_canada_drivers_license"></a>\_Licens för Kanadas \_ driv rutin för nyckelord \_

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>Hälso tjänst nummer för Kanada

### <a name="format"></a>Format

10 siffror

### <a name="pattern"></a>Mönster

10 siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_canada_health_service_number"></a>Nyckelordet \_ Canada \_ Health \_ service- \_ nummer

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>Canada Passport-nummer

### <a name="format"></a>Format

två versala bokstäver följt av sex siffror

### <a name="pattern"></a>Mönster

två versala bokstäver följt av sex siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_canada_passport_number"></a>Nyckelordet \_ Canada \_ Passport \_ Number

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Passport-nyckelord \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>Canada personal Health Identification Number (PHIN)

### <a name="format"></a>Format

nio siffror

### <a name="pattern"></a>Mönster

nio siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_canada_phin"></a>Nyckelordet \_ Canada \_ Phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>Nyckelordet \_ Canada \_ provinss

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>Kanadas sociala försäkrings nummer

### <a name="format"></a>Format

nio siffror med valfria bindestreck eller blank steg

### <a name="pattern"></a>Mönster

Formatera

- tre siffror
- ett bindestreck eller blank steg
- tre siffror
- ett bindestreck eller blank steg
- tre siffror

Oformaterad: nio siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_sin"></a>Nyckelord \_ sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Nyckelordet \_ sin \_ samarbets

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>Nummer på Chile-identitets kort

### <a name="format"></a>Format

sju till åtta siffror plus avgränsare en kontroll siffra eller bokstav

### <a name="pattern"></a>Mönster

sju till åtta siffror plus avgränsare:

- en till två siffror
- en valfri period
- tre siffror
- en valfri period
- tre siffror
- ett bindestreck
- en siffra eller bokstav (inte Skift läges känsligt) som är en kontroll siffra

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_chile_id_card"></a>Nyckelords \_ Chile \_ ID- \_ kort

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>PRC-nummer (Kina-residentt ID Card)

### <a name="format"></a>Format

18 siffror

### <a name="pattern"></a>Mönster

18 siffror:

- sex siffror, som är en adress kod
- åtta siffror i formatet ÅÅÅÅMMDD, som är födelse datum
- tre siffror, som är en order kod
- en siffra, som är en kontroll siffra

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_china_resident_id"></a>Keywords \_ \_ inhemska \_ ID för Kina

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>Kreditkorts nummer

### <a name="format"></a>Format

14 till 16 siffror som kan formateras eller oformaterade (dddddddddddddddd) och som måste klara Luhn-testet.

### <a name="pattern"></a>Mönster

Komplext och robust mönster som identifierar kort från alla större varumärken, inklusive Visa, MasterCard, Discover-kort, JCB, American Express, presentkort och Diner-kort.

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_cc_verification"></a>\_CC- \_ autentisering för nyckelord

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>\_Namn på kopia av nyckelord \_

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>Kroatiens driv Rutinens licens nummer

Denna känsliga informations typ entitet är endast tillgänglig i EU: s driv Rutins typ av känslig information.

### <a name="format"></a>Format

åtta siffror utan blank steg och avgränsare

### <a name="pattern"></a>Mönster

åtta siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keywords_eu_drivers_license_number"></a>Nyckelord \_ EU- \_ drivrutinens \_ licens \_ nummer

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Nyckelord \_ Kroatien \_ \_ \_ licens nummer för EU-drivrutin \_

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>Nummer för Kroatien identitets kort

Den här känsliga informations typen entiteten ingår i EU: s nationella identifierings typ och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

nio siffror

### <a name="pattern"></a>Mönster

nio siffror i följd

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_croatia_id_card"></a>Nyckelord för \_ Kroatien \_ ID- \_ kort

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>OIB-nummer (Kroatien personal Identification)

### <a name="format"></a>Format

11 siffror

### <a name="pattern"></a>Mönster

11 siffror:

- 10 siffror
- sista siffran är en kontroll siffra

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_croatia_oib_number"></a>\_OIB- \_ \_ nummer för Republiken Keyword

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>Danmark personal identifikations nummer

### <a name="format"></a>Format

10 siffror som innehåller bindestreck

### <a name="pattern"></a>Mönster

10 siffror:

- sex siffror i formatet DDMMYY, som är födelse datum
- ett bindestreck
- fyra siffror där den sista siffran är en kontroll siffra

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_denmark_id"></a>\_Danmark \_ -ID för nyckelord

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>EU betalkort nummer

### <a name="format"></a>Format

16 siffror

### <a name="pattern"></a>Mönster

Komplext och robust mönster

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_eu_debit_card"></a>Nyckelord \_ EU- \_ betalkort \_

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Sökord för nyckelords \_ kort \_ \_

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>Nyckelords \_ kort \_ säkerhets \_ villkor \_ dict

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>Datum för villkor för nyckelords \_ kort \_ \_ \_

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>Licens nummer för EU-drivrutin

Dessa är entiteter i EU: s driv Rutins licens nummer känslig informations typ.

- Österrike
- Belgien
- Bulgarien
- Kroatien
- Cypern
- Tjeckiska
- Danmark
- Estland
- Finland
- Frankrike
- Tyskland
- Grekland
- Ungern
- Irland
- Italien
- Lettland
- Litauen
- Luxemburg
- Malta
- Nederländerna
- Polen
- Portugal
- Rumänien
- Slovakien
- Slovenien
- Spanien
- Sverige
- engelska

## <a name="eu-national-identification-number"></a>EU: s nationella identifierings nummer

Dessa är entiteter i EU: s nationella identifierings nummer, känslig informations typ.

- Österrike
- Belgien
- Bulgarien
- Kroatien
- Cypern
- Tjeckiska
- Danmark
- Estland
- Finland
- Frankrike
- Tyskland
- Grekland
- Ungern
- Irland
- Italien
- Lettland
- Litauen
- Luxemburg
- Malta
- Nederländerna
- Polen
- Portugal
- Rumänien
- Slovakien
- Slovenien
- Spanien
- engelska

## <a name="eu-passport-number"></a>EU Passport-nummer

Det här är entiteterna i EU: s Passport-nummer känslig information typeThese är entiteterna i numret för EU Passport-nummer.

- Österrike
- Belgien
- Bulgarien
- Kroatien
- Cypern
- Tjeckiska
- Danmark
- Estland
- Finland
- Frankrike
- Tyskland
- Grekland
- Ungern
- Irland
- Italien
- Lettland
- Litauen
- Luxemburg
- Malta
- Nederländerna
- Polen
- Portugal
- Rumänien
- Slovakien
- Slovenien
- Spanien
- Sverige
- engelska

## <a name="eu-social-security-number-or-equivalent-identification"></a>EU-social security number eller motsvarande identifiering

Detta är de entiteter som finns i EU: s socialförsäkrings nummer eller motsvarande typ av identifierings känslig information.

- Österrike
- Belgien
- Kroatien
- Tjeckiska
- Danmark
- Finland
- Frankrike
- Tyskland
- Grekland
- Ungern
- Portugal
- Spanien
- Sverige

## <a name="eu-tax-identification-number"></a>EU: s skatte identifierings nummer

Dessa entiteter finns i EU: s skatte identitets nummer känslig informations typ.

- Österrike
- Belgien
- Bulgarien
- Kroatien
- Cypern
- Tjeckiska
- Danmark
- Estland
- Finland
- Frankrike
- Tyskland
- Grekland
- Ungern
- Irland
- Italien
- Lettland
- Litauen
- Luxemburg
- Malta
- Nederländerna
- Polen
- Portugal
- Rumänien
- Slovakien
- Slovenien
- Spanien
- Sverige
- engelska



## <a name="finland-national-id"></a>Nationella ID för Finland

### <a name="format"></a>Format

sex siffror plus ett tecken som anger ett Century plus tre siffror plus en kontroll siffra

### <a name="pattern"></a>Mönster

Mönstret måste innehålla alla följande:

- sex siffror i formatet DDMMYY, som är ett födelse datum
- Century-markör (antingen "-", "+" eller "a")
- 3-siffrigt personligt identifikations nummer
- en siffra eller bokstav (Skift läges okänsligt) som är en kontroll siffra

### <a name="keywords"></a>Nyckelord

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>Nummer för Finland Passport

Den här känsliga informations typen entiteten är tillgänglig i den känsliga informations typen EU Passport Number och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

kombination av nio bokstäver och siffror

### <a name="pattern"></a>Mönster

kombination av nio bokstäver och siffror:

- två bokstäver (inte Skift läges känsliga)
- sju siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keywords_eu_passport_number_common"></a>Nyckelord \_ EU \_ Passport \_ Number \_ common

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>Nyckelord för \_ Finland \_ Passport- \_ nummer

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>Fransk driv rutins licens nummer

Den här känsliga informations typen entiteten är tillgänglig i EU: s driv rutin licens nummer känslig informations typ och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

12 siffror

### <a name="pattern"></a>Mönster

12 siffror med validering till rabatt liknande mönster som franska telefonnummer

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_french_drivers_license"></a>\_Franska \_ driv rutins \_ licens för nyckelord

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>Franskt nationellt ID-kort (CNI)

### <a name="format"></a>Format

12 siffror

### <a name="pattern"></a>Mönster

12 siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keywords_france_eu_national_id_card"></a>Keywords \_ Frankrike \_ EU \_ National \_ ID- \_ kort

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>Franskt Passport-nummer

Den här känsliga informations typen entiteten är tillgänglig i den känsliga informations typen EU Passport Number och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

nio siffror och bokstäver

### <a name="pattern"></a>Mönster

nio siffror och bokstäver:

- två siffror
- två bokstäver (inte Skift läges känsliga)
- fem siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_passport"></a>Passport-nyckelord \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>France social security number (INSEE) eller motsvarande identifiering

Denna känsliga informations typ entitet ingår i EU: s socialförsäkrings nummer och motsvarande ID-känslig informations typ och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

15 siffror

### <a name="pattern"></a>Mönster

Måste matcha ett av två mönster:

- 13 siffror följt av ett blank steg följt av två siffror eller
- 15 siffror i följd

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_fr_insee"></a>Nyckelord \_ fr \_ INSEE

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>Tysklands driv rutins licens nummer

Den här känsliga informations typen entiteten ingår i EU: s driv rutin licens nummer känslig informations typ och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

en kombination av 11 siffror och bokstäver

### <a name="pattern"></a>Mönster

11 siffror och bokstäver (inte Skift läges känsliga):

- en siffra eller bokstav
- två siffror
- sex siffror eller bokstäver
- en siffra
- en siffra eller bokstav

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_german_drivers_license_number"></a>Nyckelord \_ tyska \_ driv rutiner \_ licens \_ nummer

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>Kort nummer för Tyskland-identitet

### <a name="format"></a>Format

sedan 1 november 2010: nio bokstäver och siffror

från 1 april 1987 till och med 31 oktober 2010:10 siffror

### <a name="pattern"></a>Mönster

sedan 1 november 2010:

- en bokstav (inte Skift läges känsligt)
- åtta siffror

från 1 april 1987 till och med 31 oktober 2010:

- 10 siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_germany_id_card"></a>Nyckelord \_ Germany \_ ID- \_ kort

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>Tysklands Passport-nummer

Den här känsliga informations typen entiteten ingår i EU: s Passport-nummer känslig informations typ och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

10 siffror eller bokstäver

### <a name="pattern"></a>Mönster

Mönstret måste innehålla alla följande:

- det första tecknet är en siffra eller en bokstav från den här uppsättningen (C, F, G, H, J, K)
- tre siffror
- fem siffror eller bokstäver från den här uppsättningen (C,-H, J-N, P, R, T, V-Z)
- en siffra

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_german_passport"></a>Nyckelord \_ tyska \_ Passport

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>Nationellt ID-kort för Grekland

### <a name="format"></a>Format

Kombination av 7-8 bokstäver och siffror plus ett bindestreck

### <a name="pattern"></a>Mönster

Sju bokstäver och siffror (gammalt format):

- En bokstav (en bokstav i det grekiska alfabetet)
- Ett bindestreck
- Sex siffror

Åtta bokstäver och siffror (nytt format):

- Två bokstäver vars versal tecken förekommer i både grekiska och latinska alfabet (ABEZHIKMNOPTYX)
- Ett bindestreck
- Sex siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_greece_id_card"></a>\_ \_ ID- \_ kort för keywords Grekland

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>HKID-nummer (Hong Kong Identity Card)

### <a name="format"></a>Format

En kombination av 8-9 bokstäver och siffror plus valfria parenteser runt det sista tecknet

### <a name="pattern"></a>Mönster

Kombination av 8-9 bokstäver:

- 1-2 bokstäver (inte Skift läges känsliga)
- Sex siffror
- Det sista tecknet (valfri siffra eller bokstaven A), som är kontroll siffran och som kan omges av parenteser.

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_hong_kong_id_card"></a>Nyckelordet \_ Hong \_ Kong \_ ID- \_ kort

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>IP-adress

### <a name="format"></a>Format

#### <a name="ipv4"></a>IPv6

Komplext mönster som konton för formaterade (perioder) och icke-formaterade (inga perioder) versioner av IPv4-adresserna

#### <a name="ipv6"></a>IPv6

Komplext mönster som konton för formaterade IPv6-tal (som innehåller kolon)

### <a name="pattern"></a>Mönster

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_ipaddress"></a>Nyckelord \_ IPAddress

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>Permanent konto nummer för Indien (PAN)

### <a name="format"></a>Format

10 bokstäver eller siffror

### <a name="pattern"></a>Mönster

10 bokstäver eller siffror:

- Tre bokstäver (inte Skift läges känsliga)
- En bokstav i C, P, H, F, A, T, B, L, J, G (inte Skift läges känsligt)
- En bokstav
- Fyra siffror
- En bokstav (inte Skift läges känsligt)

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_india_permanent_account_number"></a>\_ \_ Permanent \_ konto \_ nummer för nyckelords Indien

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>Aadhaar-nummer (Indien Unique Identification)

### <a name="format"></a>Format

12 siffror som innehåller valfria blank steg eller bindestreck

### <a name="pattern"></a>Mönster

12 siffror:

- En siffra, som inte är 0 eller 1
- Tre siffror
- Ett valfritt mellanslag eller tank streck
- Fyra siffror
- Ett valfritt mellanslag eller tank streck
- Den sista siffran, som är kontroll siffran

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_india_aadhar"></a>Aadhar för keywords \_ Indien \_

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>KTP-nummer (Indonesien Identity Card)

### <a name="format"></a>Format

16 siffror som innehåller valfria perioder

### <a name="pattern"></a>Mönster

16 siffror:

- Tvåsiffrig provins kod
- En period (valfritt)
- Tvåsiffrig Regency-eller stads kod
- Deldistrikts kod med två siffror
- En period (valfritt)
- Sex siffror i formatet DDMMYY, som är födelse datum
- En period (valfritt)
- Fyra siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_indonesia_id_card"></a>Nyckelord för \_ Indonesien \_ -ID \_

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>Internationellt bankkonto nummer (IBAN)

### <a name="format"></a>Format

Landskod (två bokstäver) plus kontroll siffror (två siffror) plus :::no-loc text="bban"::: nummer (upp till 30 tecken)

### <a name="pattern"></a>Mönster

Mönstret måste innehålla alla följande:

- Lands kod med två bokstäver
- Två kontroll siffror (följt av ett valfritt blank steg)
- 1-7 grupper med fyra bokstäver eller siffror (kan avgränsas med blank steg)
- 1-3 bokstäver eller siffror

Formatet för varje land skiljer sig något åt. IBAN-känsliga informations typen omfattar dessa 60-länder:

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>Nyckelord

Inget

## <a name="ireland-personal-public-service-pps-number"></a>Irland personal public service (PPS)-nummer

### <a name="format"></a>Format

Gammalt format (till 31 dec 2012):

- sju siffror följt av 1-2 bokstäver

Nytt format (1 jan 2013 och senare):

- sju siffror följt av två bokstäver

### <a name="pattern"></a>Mönster

Gammalt format (till 31 dec 2012):

- sju siffror
- en till två bokstäver (inte Skift läges känsligt)

Nytt format (1 jan 2013 och senare):

- sju siffror
- en bokstav (inte Skift läges känsligt) som är en alfabetisk kontroll siffra
- En valfri bokstav i intervallet A-I eller &quot; W&quot;

### <a name="keywords"></a>Nyckelord

#### <a name="keywords_ireland_eu_national_id_card"></a>Keywords \_ Irland \_ EU \_ National \_ ID- \_ kort

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>Israel bank konto nummer

### <a name="format"></a>Format

13 siffror

### <a name="pattern"></a>Mönster

Formatera

- två siffror
- ett bindestreck
- tre siffror
- ett bindestreck
- åtta siffror

Oformaterat:

- 13 efterföljande siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_israel_bank_account_number"></a>Nyckelord \_ Israel \_ Bank \_ konto \_ nummer

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>Israel National Identification Number

### <a name="format"></a>Format

nio siffror

### <a name="pattern"></a>Mönster

nio siffror i följd

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_israel_national_id"></a>Nyckelord \_ Israel \_ National \_ ID

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>Italiensk driv rutins licens nummer

Den här känsliga informations typen entiteten ingår i EU: s driv rutin licens nummer känslig informations typ och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

en kombination av 10 bokstäver och siffror

### <a name="pattern"></a>Mönster

en kombination av 10 bokstäver och siffror:

- en bokstav (inte Skift läges känsligt)
- bokstaven &quot; A &quot; eller &quot; V &quot; (inte Skift läges känslig)
- sju siffror
- en bokstav (inte Skift läges känsligt)

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_italy_drivers_license_number"></a>Nyckelord \_ italienska \_ driv rutiner \_ licens \_ nummer

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>Japanskt bank konto nummer

### <a name="format"></a>Format

sju eller åtta siffror

### <a name="pattern"></a>Mönster

bank konto nummer:

- sju eller åtta siffror
- Bank kontots förgrenings kod:
- fyra siffror
- ett blank steg eller ett bindestreck (valfritt)
- tre siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_jp_bank_account"></a>Nyckelordet \_ JP \_ Bank \_ konto

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>Nyckelords \_ JP \_ Bank \_ förgrenings \_ kod

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>Japansk driv rutins licens nummer

### <a name="format"></a>Format

12 siffror

### <a name="pattern"></a>Mönster

12 siffror i följd

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_jp_drivers_license_number"></a>\_ \_ \_ Licens nummer för JP JP-drivrutiner \_

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>Japanskt Passport-nummer

### <a name="format"></a>Format

två bokstäver följt av sju siffror

### <a name="pattern"></a>Mönster

två bokstäver (inte Skift läges känsliga) följt av sju siffror

#### <a name="keyword_jp_passport"></a>Nyckelordet \_ JP \_ Passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>Japanskt kort nummer för Japan

### <a name="format"></a>Format

12 bokstäver och siffror

### <a name="pattern"></a>Mönster

12 bokstäver och siffror:

- två bokstäver (inte Skift läges känsliga)
- åtta siffror
- två bokstäver (inte Skift läges känsliga)

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_jp_residence_card_number"></a>Keyword \_ JP \_ - \_ kortets \_ nummer

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>Japan-inhemskt registrerings nummer

### <a name="format"></a>Format

11 siffror

### <a name="pattern"></a>Mönster

11 efterföljande siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_jp_resident_registration_number"></a>\_Inhemskt JP- \_ \_ registrerings \_ nummer

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>Japan, sociala försäkrings nummer (SIN)

### <a name="format"></a>Format

7-12 siffror

### <a name="pattern"></a>Mönster

7-12 siffror:

- fyra siffror
- ett bindestreck (valfritt)
- sex siffror eller
- 7-12 siffror i följd

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_jp_sin"></a>Keyword \_ JP \_ sin

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>Kort nummer för Malaysia-identifiering

### <a name="format"></a>Format

12 siffror som innehåller valfria bindestreck

### <a name="pattern"></a>Mönster

12 siffror:

- sex siffror i formatet YYMMDD, som är födelse datum
- ett bindestreck (valfritt)
- plats med två bokstäver och födelse kod
- ett bindestreck (valfritt)
- tre slumpmässiga siffror
- en-siffrig jämställdhets kod

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_malaysia_id_card_number"></a>Keyword \_ Malaysia \_ ID- \_ kort \_ nummer

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>Nederländskt BSN-nummer (Nederländernas unionsmedborgare)

### <a name="format"></a>Format

åtta-nio siffror som innehåller valfria blank steg

### <a name="pattern"></a>Mönster

åtta siffror:

- tre siffror
- ett blank steg (valfritt)
- tre siffror
- ett blank steg (valfritt)
- två-tre siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keywords_netherlands_eu_national_id_card"></a>Nyckelord \_ Nederländernas \_ \_ nationella \_ ID- \_ kort för EU

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>New Zeelands ministeriet för hälso tillstånd

### <a name="format"></a>Format

tre bokstäver, ett blank steg (valfritt) och fyra siffror

### <a name="pattern"></a>Mönster

- tre bokstäver (inte Skift läges känsliga) utom "I" och "O"
- ett blank steg (valfritt)
- fyra siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_nz_terms"></a>\_Villkor för NZ-nyckelord \_

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>ID-nummer för Norge

### <a name="format"></a>Format

11 siffror

### <a name="pattern"></a>Mönster

11 siffror:

- sex siffror i formatet DDMMYY, som är födelse datum
- tresiffrigt individuellt nummer
- två kontroll siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_norway_id_number"></a>Nyckelordet \_ norsk \_ ID- \_ nummer

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>Filippinerna enhetligt identifierings nummer för flera syften

### <a name="format"></a>Format

12 siffror avgränsade med bindestreck

### <a name="pattern"></a>Mönster

12 siffror:

- fyra siffror
- ett bindestreck
- sju siffror
- ett bindestreck
- en siffra

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_philippines_id"></a>\_Filippinerna \_ -ID för nyckelord

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>Kort för Polen-identitet

### <a name="format"></a>Format

tre bokstäver och sex siffror

### <a name="pattern"></a>Mönster

tre bokstäver (inte Skift läges känsliga) följt av sex siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_poland_national_id_passport_number"></a>Nyckelord \_ Polen \_ National \_ ID \_ Passport \_ Number

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>Polen National ID (PESEL)

### <a name="format"></a>Format

11 siffror

### <a name="pattern"></a>Mönster

- 6 siffror som representerar födelse datum i formatet YYMMDD
- 4 siffror
- 1 kontroll siffra

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_pesel_identification_number"></a>\_PESEL- \_ ID för nyckelord \_

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>Polen Passport-nummer

Den här känsliga informations typen entiteten ingår i EU: s Passport-nummer känslig informations typ och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

två bokstäver och sju siffror

### <a name="pattern"></a>Mönster

Två bokstäver (inte Skift läges känsliga) följt av sju siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_poland_national_id_passport_number"></a>Nyckelord \_ Polen \_ National \_ ID \_ Passport \_ Number

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>Kort nummer för Portugal, unionsmedborgare

### <a name="format"></a>Format

åtta siffror

### <a name="pattern"></a>Mönster

åtta siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_portugal_citizen_card"></a>Nyckelord för \_ Portugal, \_ medborgare \_

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>Driv rutins licens nummer för Portugal

Denna känsliga informations typ entitet är endast tillgänglig i EU: s driv Rutins typ av känslig information.

### <a name="format"></a>Format

två mönster – två bokstäver följt av 5-8 siffror med specialtecken

### <a name="pattern"></a>Mönster

Mönster 1: två bokstäver följt av 5/6 med specialtecken:

- Två bokstäver (inte Skift läges känsliga)
- Ett bindestreck
- Fem eller sex siffror
- Ett blank steg
- En siffra

Mönster 2: en bokstav följt av 6/8 siffror med specialtecken:

- En bokstav (inte Skift läges känsligt)
- Ett bindestreck
- Sex eller åtta siffror
- Ett blank steg
- En siffra

### <a name="keywords"></a>Nyckelord

#### <a name="keywords_eu_drivers_license_number"></a>Nyckelord \_ EU- \_ drivrutinens \_ licens \_ nummer

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Nyckelord \_ Portugal \_ EU- \_ drivrutinens \_ licens \_ nummer

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>Nationellt ID för Saudiarabien

### <a name="format"></a>Format

10 siffror

### <a name="pattern"></a>Mönster

10 efterföljande siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_saudi_arabia_national_id"></a>Nyckelord i \_ Saudiarabiens \_ \_ nationella \_ ID

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>NRIC-nummer (Singapore National Registration Identity Card)

### <a name="format"></a>Format

nio bokstäver och siffror

### <a name="pattern"></a>Mönster

- nio bokstäver och siffror:
- bokstaven &quot; F &quot; , &quot; G &quot; , &quot; S &quot; eller &quot; T &quot; (inte Skift läges känsligt)
- sju siffror
- en alfabetisk kontroll siffra

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_singapore_nric"></a>NRIC för keywords \_ Singapore \_

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>FN-nummer för Sydafrika

### <a name="format"></a>Format

13 siffror som kan innehålla blank steg

### <a name="pattern"></a>Mönster

13 siffror:

- sex siffror i formatet YYMMDD, som är födelse datum
- fyra siffror
- en ensiffriga medborgarskaps indikator
- siffran &quot; 8 &quot; eller &quot; 9&quot;
- en siffra, som är en kontroll Summa

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_south_africa_identification_number"></a>Nyckelord för \_ Sydafrika, \_ FN- \_ \_ nummer

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>Sydkorea, inhemskt registrerings nummer

### <a name="format"></a>Format

13 siffror som innehåller bindestreck

### <a name="pattern"></a>Mönster

13 siffror:

- sex siffror i formatet YYMMDD, som är födelse datum
- ett bindestreck
- en siffra som bestäms av Century och kön
- fyrsiffrig kod för region
- en siffra som används för att särskilja personer som de föregående talen är identiska med
- en kontroll siffra.

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_south_korea_resident_number"></a>Nyckelordet \_ södra \_ Korea, \_ inhemska \_ nummer

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>Spanien social security number (SSN)

Denna känsliga informations typ entitet ingår i EU: s socialförsäkrings nummer eller motsvarande ID-känslig informations typ och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

11-12 siffror

### <a name="pattern"></a>Mönster

11-12 siffror:

- två siffror
- ett snedstreck (valfritt)
- sju till åtta siffror
- ett snedstreck (valfritt)
- två siffror

### <a name="keywords"></a>Nyckelord

Inget

## <a name="sweden-national-id"></a>National Sverige-ID

### <a name="format"></a>Format

10 eller 12 siffror och en valfri avgränsare

### <a name="pattern"></a>Mönster

10 eller 12 siffror och en valfri avgränsare:

- två siffror (valfritt)
- Sex siffror i datum formatet YYMMDD
- avgränsare &quot; - &quot; eller &quot; + &quot; (valfritt)
- fyra siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keywords_swedish_national_identifier"></a>Nyckelord \_ svenska \_ National \_ Identifier

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>Sverige Passport-nummer

Den här känsliga informations typen entiteten ingår i EU: s Passport-nummer känslig informations typ och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

åtta siffror

### <a name="pattern"></a>Mönster

åtta siffror i följd

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_sweden_passport"></a>Nyckelord för \_ Sverige \_ Passport

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Passport-nyckelord \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>SWIFT-kod

### <a name="format"></a>Format

fyra bokstäver följt av 5-31 bokstäver eller siffror

### <a name="pattern"></a>Mönster

fyra bokstäver följt av 5-31 bokstäver eller siffror:

- Bank kod med fyra bokstäver (inte Skift läges känslig)
- ett valfritt utrymme
- 4-28 bokstäver eller siffror (Basic Bank Account Number (BBAN))
- ett valfritt utrymme
- en till tre bokstäver eller siffror (resten av BBAN)

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_swift"></a>Sökord \_ Swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>Nummer på Taiwans nationella identifiering

### <a name="format"></a>Format

en bokstav (på engelska) följt av nio siffror

### <a name="pattern"></a>Mönster

en bokstav (på engelska) följt av nio siffror:

- en bokstav (på engelska, inte Skift läges känsligt)
- siffran &quot; 1 &quot; eller &quot; 2&quot;
- åtta siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_taiwan_national_id"></a>Nyckelordet \_ Taiwan \_ National \_ ID

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>Taiwanesiskt Passport-nummer

### <a name="format"></a>Format

- Bio metriskt Passport-nummer: nio siffror
- Passport-nummer för icke-bio metrisk: nio siffror

### <a name="pattern"></a>Mönster

Bio metriskt Passport-nummer:

- tecknen &quot; 3&quot;
- åtta siffror

Passport-nummer för icke-bio metrisk:

- nio siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_taiwan_passport"></a>Nyckelordet \_ Taiwan \_ Passport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>Nummer för Taiwan-Resident certifikat (ARC/TARC)

### <a name="format"></a>Format

10 bokstäver och siffror

### <a name="pattern"></a>Mönster

10 bokstäver och siffror:

- två bokstäver (inte Skift läges känsliga)
- åtta siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_taiwan_resident_certificate"></a>Nyckelordet \_ taiwanesiska \_ inhemskt \_ certifikat

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>engelska Kör korts nummer

Den här känsliga informations typen entiteten ingår i EU: s driv rutin licens nummer känslig informations typ och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

En kombination av 18 bokstäver och siffror i det angivna formatet

### <a name="pattern"></a>Mönster

18 bokstäver och siffror:

- fem bokstäver (inte Skift läges känsliga) eller siffran &quot; 9 &quot; i stället för en bokstav
- en siffra
- fem siffror i datum formatet MMDDY för födelse datum (sjunde tecken ökas med 50 om driv rutinen är hona, det vill säga 51 till 62 i stället för 01 till 12)
- två bokstäver (inte Skift läges känsliga) eller siffran &quot; 9 &quot; i stället för en bokstav
- fem siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_uk_drivers_license"></a>Nyckelord \_ UK \_ driv rutiner \_ licens

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>engelska nummer för val av rulle

### <a name="format"></a>Format

två bokstäver följt av 1-4 siffror

### <a name="pattern"></a>Mönster

två bokstäver (inte Skift läges känsliga) följt av 1-4 siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_uk_electoral"></a>Val av nyckelord i \_ Storbritannien \_

- rådets nominerad
- nominerad form
- val register
- val rulle

## <a name="uk-national-health-service-number"></a>engelska nationellt hälso tjänst nummer

### <a name="format"></a>Format

10-17 siffror avgränsade med blank steg

### <a name="pattern"></a>Mönster

10-17 siffror:

- antingen tre eller 10 siffror
- ett blank steg
- tre siffror
- ett blank steg
- fyra siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_uk_nhs_number"></a>Nyckelord \_ UK \_ NHS- \_ nummer

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Nyckelord \_ UK \_ NHS \_ tal1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Keyword \_ UK \_ NHS \_ Number \_ DOB

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>engelska nationellt försäkrings nummer (NINO)

Den här känsliga informations typen entiteten ingår i EU: s nationella identifierings typ och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

sju tecken eller nio tecken avgränsade med blank steg eller streck

### <a name="pattern"></a>Mönster

två möjliga mönster:

- två bokstäver (giltiga NINOs använder bara vissa tecken i det här prefixet, vilket det här mönstret validerar, inte Skift läges känsligt)
- sex siffror
- antingen "A", "B", "C" eller "d" (som prefixet är endast vissa tecken tillåtna i suffixet, inte Skift läges känsligt)

ELLER

- två bokstäver
- ett blank steg eller ett streck
- två siffror
- ett blank steg eller ett streck
- två siffror
- ett blank steg eller ett streck
- två siffror
- ett blank steg eller ett streck
- antingen "A", "B", "C" eller "d"


### <a name="keywords"></a>Nyckelord

#### <a name="keyword_uk_nino"></a>Nyckelord \_ UK \_ Nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>engelska Unikt Taxpayer Reference Number

Den här typen av känslig information kan bara användas i:

- principer för data förlust skydd
- principer för efterlevnad för kommunikation
- informations styrning
- hantering av poster
- Microsoft Cloud App Security

### <a name="format"></a>Format

10 siffror utan blank steg och avgränsare

### <a name="pattern"></a>Mönster

10 siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keywords_uk_eu_tax_file_number"></a>Nyckelord \_ UK \_ EU \_ skatte \_ fil \_ nummer

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>Konto nummer för amerikansk bank

### <a name="format"></a>Format

6-17 siffror

### <a name="pattern"></a>Mönster

6-17 siffror i följd

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_usa_bank_account"></a>Nyckelord, \_ amerikanskt \_ Bank \_ konto

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>Amerikansk kör korts nummer

### <a name="format"></a>Format

Beror på status

### <a name="pattern"></a>Mönster

beror på tillstånd – till exempel New York:

- nio siffror formaterade som DDD kommer att matcha.
- nio siffror som ddddddddd kommer inte att matcha.

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_us_drivers_license_abbreviations"></a>Nyckelord \_ amerikanska \_ driv rutiner \_ licens \_ förkortningar

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>\_Licens för amerikanska \_ driv rutiner \_

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Nyckelord \_ [State \_ Name] \_ driv Rutinens \_ licens \_ namn

- tillstånds förkortning (till exempel &quot; ny &quot; )
- tillstånds namn (till exempel &quot; New York &quot; )

## <a name="us-individual-taxpayer-identification-number-itin"></a>Amerikanskt individuellt Taxpayer Identification Number (ITIN)

### <a name="format"></a>Format

nio siffror som börjar med &quot; 9 &quot; och innehåller &quot; 7 &quot; eller &quot; 8 &quot; som fjärde siffra, och som kan formateras med blank steg eller tank streck

### <a name="pattern"></a>Mönster

formatera

- siffran &quot; 9&quot;
- två siffror
- ett blank steg eller ett streck
- en &quot; 7 &quot; eller &quot; 8&quot;
- en siffra
- ett blank steg eller ett bindestreck
- fyra siffror

oformaterad

- siffran &quot; 9&quot;
- två siffror
- en &quot; 7 &quot; eller &quot; 8&quot;
- fem siffror

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_itin"></a>Nyckelord \_ ITIN

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>U.S. social security number (SSN)

### <a name="format"></a>Format

nio siffror som kan vara i ett formaterat eller oformaterat mönster

>[!Note]
> Om det utfärdats före mitten-2011 har en SSN stark formatering där vissa delar av talet måste ligga inom vissa intervall för att vara giltiga (men det finns ingen kontroll summa).
>

### <a name="pattern"></a>Mönster

fyra funktioner letar efter SSNs i fyra olika mönster:

- FUNC \_ SSN hittar SSNs 2011 med stark formatering som är formaterad med streck eller blank steg (DDD-DD-dddd eller DDD DD dddd)
- FUNC \_ unformatad \_ SSN hittar SSNS med stark formatering i 2011 förväg som är oformaterad med nio siffror i följd (ddddddddd)
- FUNC \_ -slumpmässigt \_ formaterad \_ ssn söker efter 2011-SSNS som är formaterade med bindestreck eller blank steg (DDD-DD-dddd eller DDD DD dddd)
- FUNC \_ -slumpmässigt \_ oformaterat \_ ssn söker efter 2011-SSNS som är oformaterade som nio efterföljande siffror (ddddddddd)

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_ssn"></a>Nyckelord \_ SSN

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>USA/STORBRITANNIEN Passport-nummer

Storbritannien entiteten Passport Number-känslig informations typ är tillgänglig i EU: s typ av typ av Passport-nummer och är tillgänglig som en fristående känslig informations typ entitet.

### <a name="format"></a>Format

nio siffror

### <a name="pattern"></a>Mönster

nio siffror i följd

### <a name="keywords"></a>Nyckelord

#### <a name="keyword_passport"></a>Passport-nyckelord \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
