---
title: Regler för dynamiskt ifyllda grupp medlemskap – Azure AD | Microsoft Docs
description: Så här skapar du medlemskaps regler för automatisk ifyllning av grupper och en regel referens.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: overview
ms.date: 02/18/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c25504e3313234ac6b6f80a6e00c77fce28b1400
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174537"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Regler för dynamiskt medlemskap för grupper i Azure Active Directory

I Azure Active Directory (Azure AD) kan du skapa komplexa attribut-baserade regler för att aktivera dynamiska medlemskap för grupper. Medlemskap i dynamiska grupper minskar det administrativa arbetet med att lägga till och ta bort användare. I den här artikeln beskrivs egenskaper och syntax för att skapa dynamiska medlemskaps regler för användare eller enheter. Du kan skapa en regel för dynamiskt medlemskap i säkerhets grupper eller Microsoft 365 grupper.

När alla attribut för en användare eller enhet ändras, utvärderar systemet alla dynamiska grupp regler i en katalog för att se om ändringen skulle utlösa någon grupp lägger till eller tar bort. Om en användare eller enhet uppfyller en regel i en grupp läggs de till som medlem i gruppen. Om de inte längre uppfyller regeln tas de bort. Du kan inte lägga till eller ta bort en medlem i en dynamisk grupp manuellt.

- Du kan skapa en dynamisk grupp för enheter eller för användare, men du kan inte skapa en regel som innehåller både användare och enheter.
- Du kan inte skapa en enhets grupp baserat på enhetens ägares attribut. Enhets medlemskaps regler kan bara referera till enhetens attribut.

> [!NOTE]
> Den här funktionen kräver en Azure AD Premium P1-licens för varje unik användare som är medlem i en eller flera dynamiska grupper. Du behöver inte tilldela licenser till användare för att de ska vara medlemmar i dynamiska grupper, men du måste ha det minsta antalet licenser i Azure AD-organisationen för att omfatta alla sådana användare. Om du till exempel har totalt 1 000 unika användare i alla dynamiska grupper i din organisation, behöver du minst 1 000 licenser för Azure AD Premium P1 för att uppfylla licens kravet.
> Ingen licens krävs för enheter som är medlemmar i en dynamisk enhets grupp.

## <a name="rule-builder-in-the-azure-portal"></a>Regel verktyg i Azure Portal

Azure AD tillhandahåller ett regel verktyg för att skapa och uppdatera viktiga regler snabbare. Regel verktyget stöder konstruktion av upp till fem uttryck. Regel verktyget gör det lättare att skapa en regel med några enkla uttryck, men den kan inte användas för att återskapa varje regel. Om regel verktyget inte stöder den regel som du vill skapa kan du använda text rutan.

Här följer några exempel på avancerade regler eller syntax som vi rekommenderar att du skapar med hjälp av text rutan:

- Regel med fler än fem uttryck
- Regeln för direkt rapporter
- Ställer in [operator prioritet](#operator-precedence)
- [Regler med komplexa uttryck](#rules-with-complex-expressions); till exempel `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Regel verktyget kanske inte kan visa vissa regler som skapats i text rutan. Ett meddelande kan visas om regel verktyget inte kan visa regeln. Regel verktyget ändrar inte den syntax, validering eller bearbetning av dynamiska grupp regler som stöds på något sätt.

Mer detaljerade instruktioner finns i [skapa eller uppdatera en dynamisk grupp](groups-create-rule.md).

![Lägg till medlemskaps regel för en dynamisk grupp](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Regel-syntax för ett enskilt uttryck

Ett enda uttryck är den enklaste formen av en medlemskaps regel och har bara tre delar som anges ovan. En regel med ett enda uttryck ser ut ungefär så här: `Property Operator Value` , där syntaxen för egenskapen är namnet på objektet objekt. Property.

Följande är ett exempel på en korrekt konstruerad medlemskaps regel med ett enda uttryck:

```
user.department -eq "Sales"
```

Parenteser är valfria för ett enda uttryck. Den totala längden för bröd texten i medlemskaps regeln får inte överstiga 2048 tecken.

## <a name="constructing-the-body-of-a-membership-rule"></a>Skapa bröd texten i en medlemskaps regel

En medlemskaps regel som automatiskt fyller i en grupp med användare eller enheter är ett binärt uttryck som resulterar i ett sant eller falskt resultat. De tre delarna i en enkel regel är:

- Egenskap
- Operator
- Värde

Ordningen på delarna i ett uttryck är viktiga för att undvika syntaxfel.

## <a name="supported-properties"></a>Egenskaper som stöds

Det finns tre typer av egenskaper som kan användas för att skapa en medlemskaps regel.

- Boolesk
- Sträng
- Sträng samling

Följande är de användar egenskaper som du kan använda för att skapa ett enda uttryck.

### <a name="properties-of-type-boolean"></a>Egenskaper av boolesk typ

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| accountEnabled |Sant falskt |User. accountEnabled-EQ sant |
| dirSyncEnabled |Sant falskt |User. dirSyncEnabled-EQ sant |

### <a name="properties-of-type-string"></a>Egenskaper av typen sträng

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| city |Valfritt sträng värde eller *Null* |(User. City-EQ "värde") |
| land |Valfritt sträng värde eller *Null* |(User. Country-EQ "värde") |
| companyName | Valfritt sträng värde eller *Null* | (User. företags namn – EQ "värde") |
| avdelning |Valfritt sträng värde eller *Null* |(User. Department-EQ "värde") |
| displayName |Valfritt sträng värde |(User. displayName-EQ "value") |
| Anställnings |Valfritt sträng värde |(User. Anställningsnr-EQ "value")<br>(User. Anställningsnr-Ne *Null*) |
| facsimileTelephoneNumber |Valfritt sträng värde eller *Null* |(User. facsimileTelephoneNumber-EQ "value") |
| förnamn |Valfritt sträng värde eller *Null* |(User. givenName-EQ "value") |
| jobTitle |Valfritt sträng värde eller *Null* |(User. befattning-EQ "value") |
| e-post |Valfritt sträng värde eller *Null* (SMTP-adress för användaren) |(User. mail-EQ "värde") |
| smeknamn för e-post |Valfritt sträng värde (e-postalias för användaren) |(User. smek namn-EQ "värde") |
| mobil |Valfritt sträng värde eller *Null* |(User. Mobile-EQ "value") |
| objectId |Användar objektets GUID |(User. objectId-EQ "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Lokal säkerhets identifierare (SID) för användare som synkroniserats från lokalt till molnet. |(User. onPremisesSecurityIdentifier-EQ "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Ingen DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(User. passwordPolicies-EQ "DisableStrongPassword") |
| physicalDeliveryOfficeName |Valfritt sträng värde eller *Null* |(User. physicalDeliveryOfficeName-EQ "value") |
| postalCode |Valfritt sträng värde eller *Null* |(User. Postnr-EQ "värde") |
| preferredLanguage |ISO 639-1-kod |(User. preferredLanguage-EQ "en-US") |
| sipProxyAddress |Valfritt sträng värde eller *Null* |(User. sipProxyAddress-EQ "value") |
| state |Valfritt sträng värde eller *Null* |(User. State-EQ "värde") |
| streetAddress |Valfritt sträng värde eller *Null* |(User. streetAddress-EQ "value") |
| surname |Valfritt sträng värde eller *Null* |(User. efter namn – EQ "värde") |
| telephoneNumber |Valfritt sträng värde eller *Null* |(User. telephoneNumber-EQ "value") |
| usageLocation |Två bokstäver för lands-och regions kod |(User. usageLocation-EQ "US") |
| userPrincipalName |Valfritt sträng värde |(User. userPrincipalName-EQ " alias@domain ") |
| userType |medlems gäster *Null* |(User. userType-EQ "medlem") |

### <a name="properties-of-type-string-collection"></a>Egenskaper av typen sträng samling

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| otherMails |Valfritt sträng värde |(User. otherMails-contains " alias@domain ") |
| proxyAddresses |SMTP: alias@domain SMTP: alias@domain |(User. proxyAddresses-contains "SMTP: alias@domain ") |

Information om egenskaper som används för enhets regler finns i [regler för enheter](#rules-for-devices).

## <a name="supported-expression-operators"></a>Operatorer som stöds

I följande tabell visas alla operatorer som stöds och deras syntax för ett enda uttryck. Operatorer kan användas med eller utan bindestreck (-).

| Operator | Syntax |
| --- | --- |
| Inte lika med |-Ne |
| Lika med |-EQ |
| Börjar inte med |-notStartsWith |
| Börjar med |– startsWith |
| Innehåller inte |-notContains |
| Innehåller |– innehåller |
| Matchar inte |-notMatch |
| Matchning |-matcha |
| I | – in |
| Inte i | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Använda operatorerna-in och-notIn

Om du vill jämföra värdet för ett användarattribut mot ett antal olika värden kan du använda operatorerna-in eller-notIn. Använd hakparenteserna "[" och "]" för att börja och avsluta listan med värden.

 I följande exempel utvärderar uttrycket till sant om värdet för User. Department är lika med något av värdena i listan:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Använda operatorn-match 
Operatorn **-match** används för att matcha alla reguljära uttryck. Exempel:

```
user.displayName -match "Da.*"   
```
Da,/DAV, David utvärderar till sant, aDa utvärderas till false.

```
user.displayName -match ".*vid"
```
David utvärderar till true, da utvärderas till false.

## <a name="supported-values"></a>Värden som stöds

Värdena som används i ett uttryck kan bestå av flera typer, inklusive:

* Strängar
* Boolesk – sant, falskt
* Tal
* Matriser – nummer mat ris, sträng mat ris

När du anger ett värde i ett uttryck är det viktigt att du använder rätt syntax för att undvika fel. Några tips för syntax är:

* Dubbla citat tecken är valfria om värdet är en sträng.
* Sträng-och regex-åtgärder är inte Skift läges känsliga.
* Om ett sträng värde innehåller dubbla citat tecken ska båda citat tecknen undantas med hjälp av \` tecken, till exempel User. Department-EQ \` "Sales \` " är rätt syntax när "Sales" är värdet.
* Du kan också utföra null-kontroller, med null som ett värde, till exempel `user.department -eq null` .

### <a name="use-of-null-values"></a>Användning av null-värden

Om du vill ange ett null-värde i en regel kan du använda värdet *Null* . 

* Använd EQ eller-Ne när du jämför *Null* -värdet i ett uttryck.
* Använd citat tecken runt ordet *Null* endast om du vill att det ska tolkas som ett litteralt sträng värde.
* Operatorn-not kan inte användas som en jämför ande operator för null. Om du använder det får du ett fel meddelande om du använder null eller $null.

Det korrekta sättet att referera till null-värdet är följande:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regler med flera uttryck

En grupp medlemskaps regel kan bestå av mer än ett enskilt uttryck som är anslutet av logiska operatorerna-och,-eller, och. Logiska operatorer kan också användas i en kombination. 

Följande är exempel på korrekt utformade medlemskaps regler med flera uttryck:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Prioritet för operator

Alla operatorer visas nedan i prioritetsordning från högsta till lägsta. Operatorerna på samma rad har samma prioritet:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Följande är ett exempel på en operator prioritet där två uttryck utvärderas för användaren:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Parenteser behövs bara när prioriteten inte uppfyller dina krav. Om du till exempel vill att avdelningen ska utvärderas först, visar följande hur parenteser kan användas för att fastställa ordningen:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regler med komplexa uttryck

En medlemskaps regel kan bestå av komplexa uttryck där egenskaper, operatorer och värden tar i mer komplexa formulär. Uttryck betraktas som komplexa när något av följande stämmer:

* Egenskapen består av en samling värden. specifika egenskaper för flera värden
* Uttrycken använder operatorerna-any och-all
* Värdet för uttrycket kan vara ett eller flera uttryck

## <a name="multi-value-properties"></a>Egenskaper för flera värden

Egenskaper för flera värden är samlingar med objekt av samma typ. De kan användas för att skapa medlemskaps regler med hjälp av-alla logiska operatorer.

| Egenskaper | Värden | Användning |
| --- | --- | --- |
| assignedPlans | Varje objekt i samlingen visar följande sträng egenskaper: capabilityStatus, service, servicePlanId |User. assignedPlans – any (assignedPlan. servicePlanId-EQ "efb87545-963c-4e0d-99df-69c6916d9eb0"-och assignedPlan. capabilityStatus-EQ "Enabled") |
| proxyAddresses| SMTP: alias@domain SMTP: alias@domain | (User. proxyAddresses – any ( \_ -innehåller "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Använda operatorerna-any och-all

Du kan använda-alla-operatorer för att tillämpa ett villkor för en eller alla objekt i samlingen.

* – alla (uppfylls när minst ett objekt i samlingen matchar villkoret)
* – alla (uppfylls när alla objekt i samlingen matchar villkoret)

#### <a name="example-1"></a>Exempel 1

assignedPlans är en egenskap med flera värden som visar alla tjänst planer som tilldelats användaren. Följande uttryck väljer användare som har tjänst prenumerationen Exchange Online (plan 2) (som ett GUID-värde) som också är i aktiverat läge:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

En regel som den här kan användas för att gruppera alla användare som har en Microsoft 365 (eller annan Microsoft Online Service)-kapacitet aktive rad. Du kan sedan använda med en uppsättning principer för gruppen.

#### <a name="example-2"></a>Exempel 2

Följande uttryck väljer alla användare som har en tjänst plan som är associerad med Intune-tjänsten (identifieras av tjänst namnet "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

#### <a name="example-3"></a>Exempel 3

Följande uttryck väljer alla användare som inte har någon asigned-tjänst plan:

```
user.assignedPlans -all (assignedPlan.servicePlanId -eq "")
```

### <a name="using-the-underscore-_-syntax"></a>Använda syntaxen under streck ( \_ )

Under streck ( \_ )-syntaxen matchar förekomster av ett angivet värde i en av de egenskaper för mängd med flera värden som du använder för att lägga till användare eller enheter i en dynamisk grupp. Den används med operatorerna-any eller-all.

Här är ett exempel på hur du använder under streck ( \_ ) i en regel för att lägga till medlemmar baserat på User. proxyAddress (det fungerar på samma gång för User. otherMails). Den här regeln lägger till alla användare med proxyadress som innehåller "contoso"-gruppen.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Andra egenskaper och gemensamma regler

### <a name="create-a-direct-reports-rule"></a>Skapa en "direkt rapporter"-regel

Du kan skapa en grupp som innehåller alla direkt rapporter för en chef. När chefens direkt rapporter ändras i framtiden, justeras gruppens medlemskap automatiskt.

Regeln för direkt rapporter konstrueras med hjälp av följande syntax:

```
Direct Reports for "{objectID_of_manager}"
```

Här är ett exempel på en giltig regel där "62e19b97-8b3d-4d4a-A106-4ce66896a863" är objectID för chefen:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Följande tips kan hjälpa dig att använda regeln korrekt.

- **Chefs-ID** : t är objekt-ID: t för chefen. Du hittar den i chefens **profil**.
- För att regeln ska fungera måste du se till att egenskapen **chef** är korrekt inställd för användare i din organisation. Du kan kontrol lera det aktuella värdet i användarens **profil**.
- Den här regeln stöder endast chefens direkt rapporter. Med andra ord kan du inte skapa en grupp med chefens direkt rapporter *och* deras rapporter.
- Den här regeln kan inte kombineras med andra medlemskaps regler.

### <a name="create-an-all-users-rule"></a>Skapa en regel för alla användare

Du kan skapa en grupp som innehåller alla användare i en organisation som använder en medlemskaps regel. När användare läggs till eller tas bort från organisationen i framtiden justeras gruppens medlemskap automatiskt.

Regeln "alla användare" är konstruerad med ett enda uttryck med operatorn-Ne och värdet null. Den här regeln lägger till B2B-gäst användare samt medlems användare i gruppen.

```
user.objectId -ne null
```
Om du vill att din grupp ska undanta gäst användare och bara inkludera medlemmar i din organisation kan du använda följande syntax:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>Skapa en regel för alla enheter

Du kan skapa en grupp som innehåller alla enheter i en organisation som använder en medlemskaps regel. När enheter läggs till eller tas bort från organisationen i framtiden, justeras gruppens medlemskap automatiskt.

Regeln "alla enheter" är konstruerad med ett enda uttryck med operatorn-Ne och värdet null:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Egenskaper för tillägg och anpassade tilläggs egenskaper

Tilläggets attribut och anpassade tilläggs egenskaper stöds som sträng egenskaper i dynamiska medlemskaps regler. [Attributen för tillägg](/graph/api/resources/onpremisesextensionattributes) synkroniseras från den lokala serverns AD och formatet "ExtensionAttributeX", där X är lika med 1-15. Här är ett exempel på en regel som använder attributet Extension som en egenskap:

```
(user.extensionAttribute15 -eq "Marketing")
```

[Anpassade tilläggs egenskaper](../hybrid/how-to-connect-sync-feature-directory-extensions.md) synkroniseras från lokala Windows Server AD eller från ett anslutet SaaS-program och har formatet `user.extension_[GUID]_[Attribute]` , där:

* [GUID] är den unika identifieraren i Azure AD för programmet som skapade egenskapen i Azure AD
* [Attribute] är namnet på egenskapen som det skapades

Ett exempel på en regel som använder en anpassad tilläggs egenskap är:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

Du hittar namnet på den anpassade egenskapen i katalogen genom att fråga en användares egenskap med hjälp av Graph Explorer och söka efter egenskaps namnet. Dessutom kan du nu välja länken **Hämta anpassade tilläggs egenskaper** i regel verktyget för dynamisk användar grupp för att ange ett unikt app-ID och ta emot den fullständiga listan med anpassade tilläggs egenskaper som ska användas när du skapar en regel för dynamiskt medlemskap. Den här listan kan också uppdateras för att få nya anpassade tilläggs egenskaper för appen.

## <a name="rules-for-devices"></a>Regler för enheter

Du kan också skapa en regel som väljer enhets objekt för medlemskap i en grupp. Du kan inte ha både användare och enheter som grupp medlemmar. 

> [!NOTE]
> Attributet **organizationalUnit** visas inte längre och ska inte användas. Den här strängen anges av Intune i vissa fall, men känns inte igen av Azure AD, så inga enheter läggs till i grupper baserat på det här attributet.

> [!NOTE]
> systemlabels är ett skrivskyddat attribut som inte kan anges med Intune.
>
> För Windows 10 är rätt format för deviceOSVersion-attributet följande: (Device. deviceOSVersion-EQ "10.0.17763"). Formateringen kan verifieras med PowerShell-cmdleten Get-MsolDevice.

Följande enhets egenskaper kan användas.

 Enhets attribut  | Värden | Exempel
 ----- | ----- | ----------------
 accountEnabled | Sant falskt | (Device. accountEnabled-EQ true)
 displayName | valfritt sträng värde |(Device. displayName-EQ "Anders iPhone")
 deviceOSType | valfritt sträng värde | (Device. deviceOSType-EQ "iPad")-eller (Device. deviceOSType-EQ "iPhone")<br>(Device. deviceOSType-innehåller "AndroidEnterprise")<br>(Device. deviceOSType-EQ "AndroidForWork")<br>(Device. deviceOSType-EQ "Windows")
 deviceOSVersion | valfritt sträng värde | (Device. deviceOSVersion-EQ "9,1")<br>(Device. deviceOSVersion-EQ "10.0.17763.0")
 deviceCategory | ett giltigt namn på enhets kategori | (Device. deviceCategory-EQ "BYOD")
 deviceManufacturer | valfritt sträng värde | (Device. deviceManufacturer-EQ "Samsung")
 deviceModel | valfritt sträng värde | (Device. deviceModel-EQ "iPad Air")
 deviceOwnership | Personlig, företag, okänd | (Device. deviceOwnership-EQ "Company")
 enrollmentProfileName | Registrerings profil namn för Apple-enhet, Android Enterprise-företagsägda dedikerad enhets registrerings profil namn eller Windows autopilot profil namn | (Device. enrollmentProfileName-EQ "DEP iPhone")
 isRooted | Sant falskt | (Device. isRooted-EQ true)
 managementType | MDM (för mobila enheter)<br>PC (för datorer som hanteras av Intune PC-agenten) | (Device. managementType-EQ "MDM")
 deviceId | ett giltigt ID för Azure AD-enhet | (Device. deviceId-EQ "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | ett giltigt objekt-ID för Azure AD |  (Device. objectId-EQ "76ad43c9-32c5-45e8-A272-7b58b58f596d")
 devicePhysicalIds | ett sträng värde som används av autopilot, till exempel alla autopilot-enheter, Ordernr eller PurchaseOrderID  | (Device. devicePhysicalIDs-any _-contains "[ZTDId]") (Device. devicePhysicalIds-any _-EQ "[Ordernr]: 179887111881") (Device. devicePhysicalIds-any _-EQ "[PurchaseOrderId]: 76222342342")
 systemLabels | valfri sträng som matchar enhets egenskapen i Intune för att tagga moderna arbets plats enheter | (device.systemLabels-innehåller "M365Managed")

> [!Note]  
> För deviceOwnership när du skapar dynamiska grupper för enheter måste du ange värdet till "företag". I Intune representeras enhetens ägarskap i stället för företag. Mer information finns på [OwnerTypes](/intune/reports-ref-devices#ownertypes) . 

## <a name="next-steps"></a>Nästa steg

De här artiklarna innehåller ytterligare information om grupper i Azure Active Directory.

- [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Skapa en ny grupp och lägga till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Hantera dynamiska regler för användare i en grupp](groups-create-rule.md)
