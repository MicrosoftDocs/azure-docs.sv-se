---
title: Läs om de senaste Azure-gäst OS-versionerna | Microsoft Docs
description: Den senaste versionen nyheter och SDK-kompatibilitet för Azure Cloud Services gäst operativ system.
services: cloud-services
documentationcenter: na
author: yohaddad
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 8/17/2020
ms.author: yohaddad
ms.openlocfilehash: 7ac7fd67032c49ca07a2a050b72dce199cfebf54
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548505"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure gäst operativ system versioner och SDK-kompatibilitet mat ris
Innehåller uppdaterad information om de senaste Azure Guest OS-versionerna för Cloud Services. Den här informationen hjälper dig att planera uppgraderings vägen innan ett gäst operativ system är inaktiverat. Om du konfigurerar dina roller så att de använder *automatiska* uppdateringar av gäst operativ system enligt beskrivningen i [Azures uppdaterings inställningar för gäst operativ system][Azure Guest OS Update Settings]är det inte viktigt att du läser den här sidan.

> [!IMPORTANT]
> Den här sidan gäller för Cloud Services webb-och arbets roller som körs ovanpå ett gäst operativ system. Den **gäller inte** för IaaS Virtual Machines.
>
>


> [!TIP]
>  Prenumerera på [gäst operativ system uppdateringens RSS-flöde] för att ta emot de senaste aviseringarna om alla gäst operativ system ändringar.
>
>

> [!IMPORTANT]
> Endast de senaste 2 versionerna av gäst operativ systemet kommer att stödjas och vara tillgängliga i Azure Portal.
>
>

Är du osäker på hur du uppdaterar ditt gäst operativ system? Kolla [här][cloud updates] .

## <a name="news-updates"></a>Diskussions grupps uppdateringar

###### <a name="august-17-2020"></a>**17 augusti 2020**
Juli gäst operativ system har släppts. 

###### <a name="august-10-2020"></a>**10 augusti 2020**
Gäst operativ systemet i juni har släppts. 

###### <a name="june-2-2020"></a>**2 juni 2020**
Gäst operativ systemet har släppts. 

###### <a name="may-4-2020"></a>**4 maj 2020**
Gäst operativ systemet i april har släppts. 

###### <a name="april-2-2020"></a>**2 april 2020**
Gäst operativ systemet i mars har släppts. 

###### <a name="march-5-2020"></a>**5 mars 2020**
Gäst operativ systemet i februari har släppts. 

###### <a name="january-24-2020"></a>**24 januari 2020**
Gäst operativ systemet i januari har släppts. 

###### <a name="january-8-2020"></a>**8 januari 2020**
December gäst operativ system har släppts.

###### <a name="december-5-2019"></a>**5 december 2019**
Gäst operativ systemet i november har släppts.

###### <a name="november-1-2019"></a>**Den 1 november 2019**
Gäst operativ systemet i oktober har släppts.

###### <a name="october-7-2019"></a>**7 oktober 2019**
Gäst operativ systemet i september har släppts.

###### <a name="september-4-2019"></a>**4 september 2019**
Gäst operativ systemet i augusti har släppts.

###### <a name="july-26-2019"></a>**26 juli 2019**
Juli gäst operativ system har släppts.

###### <a name="july-8-2019"></a>**8 juli 2019**
Gäst operativ systemet i juni har släppts.

###### <a name="june-6-2019"></a>**6 juni 2019**
Gäst operativ systemet har släppts.

###### <a name="may-7-2019"></a>**7 maj 2019**
Gäst operativ systemet i april har släppts.

###### <a name="march-26-2019"></a>**26 mars 2019**
Gäst operativ systemet i mars har släppts.

###### <a name="march-12-2019"></a>**12 mars 2019**
Gäst operativ systemet i februari har släppts.

###### <a name="february-5-2019"></a>**5 februari 2019**
Gäst operativ systemet i januari har släppts.

###### <a name="january-24-2019"></a>**24 januari 2019**
Family 6 gäst operativ system (Windows Server 2019) har släppts.

###### <a name="january-7-2019"></a>**7 januari 2019**
December gäst operativ system har släppts.

###### <a name="december-14-2018"></a>**14 december 2018**
Gäst operativ systemet i november har släppts.

###### <a name="november-8-2018"></a>**8 november 2018**
Gäst operativ systemet i oktober har släppts.

###### <a name="october-12-2018"></a>**12 oktober 2018**
Gäst operativ systemet i september har släppts.

## <a name="releases"></a>Versioner

## <a name="family-6-releases"></a>Family 6-versioner
**Windows Server 2019**

.NET Framework installerat: 3,5, 4.7.2

> [!NOTE]
> Windows Azure SDK för .NET – 3,0 kan hämtas [här][Windows Azure SDK].
>
>Installations steg:
>1. Avinstallera alla äldre versioner av MicrosoftAzureAuthoringTools*. msi
>2. Installera [Azure SDK för .net – 3,0][Windows Azure SDK]
>3. Starta om datorn
>4. Skapa ett nytt moln tjänst projekt och Lägg till en enskild arbets roll
>5. Ändra OS-familjen till 6 och bygg ett paket
>6. Distribuera paketet till Azure med hjälp av Azure Portal eller Visual Studio
>
>Gäst operativ system familj 6 version 6 aktiverar TLS 1,2 genom att uttryckligen inaktivera TLS 1,0 och 1,1 och definiera en specifik uppsättning chiffersviter. Läs [mer].


| Konfigurations sträng | Utgivningsdatum | Inaktive rings datum |
| --- | --- | --- |
|  WA – GÄST-OS-6.21 _202007-01  |  17 augusti 2020  |  Publicera 6,23  |  
|  WA – GÄST-OS-6.20 _202006-02  |  10 augusti 2020  |  Publicera 6,22  |  
|~~WA – GÄST-OS-6.19 _202005-02~~|  2 juni 2020  |  17 augusti 2020  |  
|~~WA – GÄST-OS-6.18 _202004-01~~|  4 maj 2020  |  10 augusti 2020  |  
|~~WA – GÄST-OS-6.17 _202003-01~~|  2 april 2020  |  2 juni 2020  |  
|~~WA – GÄST-OS-6.16 _202002-01~~|  5 mars 2020  |  4 maj 2020  |  
|~~WA – GÄST-OS-6.15 _202001-01~~|  24 januari 2020  |  2 april 2020  |  
|~~WA – GÄST-OS-6.14 _201912-01~~| 8 januari 2020 | 5 mars 2020 |  
|~~WA – GÄST-OS-6.13 _201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA – GÄST-OS-6,12 _201910-01~~| Den 1 november 2019 | 8 januari 2020 |  
|~~WA – GÄST-OS-6.11 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA – GÄST-OS-6.10-_201908 – 01~~| 4 augusti 2019 | Den 1 november 2019  |  
|~~WA – GÄST-OS-6,9-_201907 – 0~~|26 juli 2019 | 7 oktober 2019 |
|~~WA – GÄST-OS-6,8 _201906 – 01~~|8 juli 2019 |4 augusti 2019 |
|~~WA – GÄST-OS-6.7 _201905 – 01~~ |6 juni 2019 |26 juli 2019 |
|~~WA – GÄST-OS-6.6 _201904 – 01~~ |7 maj 2019 |8 juli 2019 |
|~~WA – GÄST-OS-6.5 _201903 – 01~~ |26 mars 2019 |6 juni 2019 |
|~~WA – GÄST-OS – 6.4 _201902 – 01~~ |12 mars 2019 |7 maj 2019 |
|~~WA – GÄST-OS-6.3 _201901 – 01~~ |5 februari 2019 |26 mars 2019 |
|~~WA – GÄST-OS-6.2 _201812 – 01~~ |24 januari 2019 |12 mars 2019 |
|~~WA – GÄST-OS-6.1 _201811 – 01~~ |24 januari 2019 |5 februari 2019 |

## <a name="family-5-releases"></a>Family 5-versioner
**Windows Server 2016**

.NET Framework installerat: 3,5, 4.6.2

> [!NOTE]
> RDP-lösenordet för OS-familjen 5 måste bestå av minst 10 tecken.
>


| Konfigurations sträng | Utgivningsdatum | Inaktive rings datum |
| --- | --- | --- |
|  WA – GÄST-OS-5.45 _202007-01  |  17 augusti 2020  |  Publicera 5,47  |  
|  WA – GÄST-OS-5.44 _202006-02  |  10 augusti 2020  |  Publicera 5,46  |  
|~~WA – GÄST-OS-5.43 _202005-02~~|  2 juni 2020  |  17 augusti 2020  |  
|~~WA – GÄST-OS-5.42 _202004-01~~|  4 maj 2020  |  10 augusti 2020  |  
|~~WA – GÄST-OS-5.41 _202003-01~~|  2 april 2020  |  2 juni 2020  |  
|~~WA – GÄST-OS-5.40 _202002-01~~|  5 mars 2020  |  4 maj 2020  |  
|~~WA – GÄST-OS-5.39 _202001-01~~|  24 januari 2020  |  2 april 2020  |  
|~~WA – GÄST-OS-5.38 _201912-01~~| 8 januari 2020 | 5 mars 2020 |  
|~~WA – GÄST-OS-5.37 _201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA – GÄST-OS-5.36 _201910-01~~| Den 1 november 2019 | 8 januari 2020 |  
|~~WA – GÄST-OS-5.35 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA – GÄST-OS-5.34 _201908-01~~|  4 augusti 2019  | Den 1 november 2019 |  
|~~WA – GÄST-OS-5.33 _201907-01~~| 26 juli 2019 | 7 oktober 2019 |  
|~~WA – GÄST-OS-5.32 _201906-01~~|8 juli 2019 |4 augusti 2019 |
|~~WA – GÄST-OS-5.31 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA – GÄST-OS-5.30 _201904-01~~ |7 maj 2019 |8 juli 2019 |
|~~WA – GÄST-OS-5.29 _201903-01~~ |26 mars 2019 |6 juni 2019 |
|~~WA – GÄST-OS-5.28 _201902-01~~ |12 mars 2019 |7 maj 2019 |
|~~WA – GÄST-OS-5.27 _201901-01~~ |5 februari 2019 |26 mars 2019 |
|~~WA – GÄST-OS-5.26 _201812-01~~ |7 januari 2019 |12 mars 2019 |
|~~WA – GÄST-OS-5,25-_201811 – 01~~ |14 december 2018 |5 februari 2019 |
|~~WA – GÄST-OS-5.24 _201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA – GÄST-OS-5.23 _201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="family-4-releases"></a>Family 4-versioner
**Windows Server 2012 R2**

.NET Framework installerat: 3,5, 4.5.1, 4.5.2

| Konfigurations sträng | Utgivningsdatum | Inaktive rings datum |
| --- | --- | --- |
|  WA – GÄST-OS-4.80 _202007-01  |  17 augusti 2020  |  Publicera 4,82  | 
|  WA – GÄST-OS-4.79 _202006-02  |  10 augusti 2020  |  Publicera 4,81  | 
|~~WA – GÄST-OS-4.78 _202005-02~~|  2 juni 2020  |  17 augusti 2020  |  
|~~WA – GÄST-OS-4.77 _202004-01~~|  4 maj 2020  |  10 augusti 2020  |  
|~~WA – GÄST-OS-4.76 _202003-01~~|  2 april 2020  |  2 juni 2020  |  
|~~WA – GÄST-OS-4.75 _202002-01~~|  5 mars 2020  |  4 maj 2020  |  
|~~WA – GÄST-OS-4.74 _202001-01~~|  24 januari 2020  |  2 april 2020  |  
|~~WA – GÄST-OS-4.73 _201912-01~~| 8 januari 2020 | 5 mars 2020 |  
|~~WA – GÄST-OS-4.72 _201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA – GÄST-OS-4.71 _201910-01~~| Den 1 november 2019 | 8 januari 2020 |  
|~~WA – GÄST-OS-4.70 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA – GÄST-OS-4.69 _201908-01~~| 4 augusti 2019 | Den 1 november 2019 |  
|~~WA – GÄST-OS-4.68 _201907-01~~| 26 juli 2019  | 7 oktober 2019 |
|~~WA – GÄST-OS-4.67 _201906-01~~| 8 juli 2019 |4 augusti 2019 |
|~~WA – GÄST-OS-4.66 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA – GÄST-OS-4.65 _201904-01~~ |7 maj 2019 |8 juli 2019 |
|~~WA – GÄST-OS-4.64 _201903-01~~ |26 mars 2019 |6 juni 2019 |
|~~WA – GÄST-OS-4.63 _201902-01~~ |12 mars 2019 |7 maj 2019 |
|~~WA – GÄST-OS-4.62 _201901-01~~ |5 februari 2019 |26 mars 2019 |
|~~WA – GÄST-OS-4.61 _201812-01~~ |7 januari 2019 |12 mars 2019 |
|~~WA – GÄST-OS-4.60 _201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA – GÄST-OS-4.59 _201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA – GÄST-OS-4.58 _201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="family-3-releases"></a>Family 3-versioner
**Windows Server 2012**

.NET Framework installerat: 3,5, 4,5

| Konfigurations sträng | Utgivningsdatum | Inaktive rings datum |
| --- | --- | --- |
|  WA – GÄST-OS-3.87 _202007-01  |  17 augusti 2020  |  Publicera 3,89  |  
|  WA – GÄST-OS-3.86 _202006-02  |  10 augusti 2020  |  Publicera 3,88  |  
|~~WA – GÄST-OS-3.85 _202005-02~~|  2 juni 2020  |  17 augusti 2020  |  
|~~WA – GÄST-OS-3.84 _202004-01~~|  4 maj 2020  |  10 augusti 2020  |  
|~~WA – GÄST-OS-3.83 _202003-01~~|  2 april 2020  |  2 juni 2020  |  
|~~WA – GÄST-OS-3.82 _202002-01~~|  5 mars 2020  |  4 maj 2020  |  
|~~WA – GÄST-OS-3.81 _202001-01~~|  24 januari 2020  |  2 april 2020  |  
|~~WA – GÄST-OS-3.80 _201912-01~~| 8 januari 2020 | 5 mars 2020 |  
|~~WA – GÄST-OS-3.79 _201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA – GÄST-OS-3.78 _201910-01~~| Den 1 november 2019 | 8 januari 2020 |  
|~~WA – GÄST-OS-3.77 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA – GÄST-OS-3.76 _201908-01~~|  4 augusti 2019  |  Den 1 november 2019  |  
|~~WA – GÄST-OS-3,75 _201907-01~~| 26 juli 2019 | 7 oktober 2019 |
|~~WA – GÄST-OS-3.74 _201906-01~~| 8 juli 2019 |4 augusti 2019 |
|~~WA – GÄST-OS-3.73 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA – GÄST-OS-3.72 _201904-01~~ |7 maj 2019 |8 juli 2019 |
|~~WA – GÄST-OS-3.71 _201903-01~~ |26 mars 2019 |6 juni 2019 |
|~~WA – GÄST-OS-3.70 _201902-01~~ |12 mars 2019 |7 maj 2019 |
|~~WA – GÄST-OS-3.69 _201901-01~~ |5 februari 2019 |26 mars 2019 |
|~~WA – GÄST-OS-3.68 _201812-01~~ |7 januari 2019 |12 mars 2019 |
|~~WA – GÄST-OS-3.67 _201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA – GÄST-OS-3.66 _201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA – GÄST-OS-3.65 _201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="family-2-releases"></a>Family 2-versioner
**Windows Server 2008 R2 SP1**

.NET Framework installerat: 3,5 (omfattar 2,0 och 3,0), 4,5

| Konfigurations sträng | Utgivningsdatum | Inaktive rings datum |
| --- | --- | --- |
|  WA – GÄST-OS-2.100 _202007-01  |  17 augusti 2020  |  Publicera 2,102  |  
|  WA – GÄST-OS-2.99 _202006-02  |  10 augusti 2020  |  Publicera 2,101  |  
|~~WA – GÄST-OS-2.98 _202005-02~~|  2 juni 2020  |  17 augusti 2020  |  
|~~WA – GÄST-OS-2.97 _202004-01~~|  4 maj 2020  |  10 augusti 2020  |  
|~~WA – GÄST-OS-2.96 _202003-01~~|  2 april 2020  |  2 juni 2020  |  
|~~WA – GÄST-OS-2.95 _202002-01~~|  5 mars 2020  |  4 maj 2020  |  
|~~WA – GÄST-OS-2.94 _202001-01~~|  24 januari 2020  |  2 april 2020  |  
|~~WA – GÄST-OS-2.93 _201912-01~~| 8 januari 2020 | 5 mars 2020 |  
|~~WA – GÄST-OS-2.92 _201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA – GÄST-OS-2.91 _201910-01~~| Den 1 november 2019 | 8 januari 2020 |  
|~~WA – GÄST-OS-2.90 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA – GÄST-OS-2.89 _201908-01~~| 4 augusti 2019 | Den 1 november 2019 |  
|~~WA – GÄST-OS-2,88 _201907-01~~| 26 juli 2019 | 7 oktober 2019 |
|~~WA – GÄST-OS-2.87 _201906-01~~|8 juli 2019 | 4 augusti 2019 |
|~~WA – GÄST-OS-2.86 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA – GÄST-OS-2.85 _201904-01~~ |7 maj 2019 |8 juli 2019 |
|~~WA – GÄST-OS-2.84 _201903-01~~ |26 mars 2019 |6 juni 2019 |
|~~WA – GÄST-OS-2.83 _201902-01~~ |12 mars 2019 |7 maj 2019 |
|~~WA – GÄST-OS-2.82 _201901-01~~ |5 februari 2019 |26 mars 2019 |
|~~WA – GÄST-OS-2.81 _201812-01~~ |7 januari 2019 |12 mars 2019 |
|~~WA – GÄST-OS-2,80 _201811 – 01~~ |14 december 2018 |5 februari 2019 |
|~~WA – GÄST-OS-2.79 _201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA – GÄST-OS-2.78 _201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="msrc-patch-updates"></a>Uppdateringar av MSRC-uppdatering
Listan över korrigeringar som ingår i varje månads gäst operativ system version finns [här][patches].

## <a name="sdk-support"></a>SDK-support
Även om [indragnings principen för Azure SDK][retire policy sdk] visar att endast versioner som är äldre än 2,2 stöds, kan du använda tidigare versioner av vissa gäst operativ system familjer. Du bör alltid använda den senaste SDK som stöds.

| Gäst operativ system familj | Kompatibla SDK-versioner |
| --- | --- |
| 6 |Version 2.9.6 + |
| 5 |Version 2.9.5.1 + |
| 4 |Version 2.1 + |
| 3 |Version 1.8 + |
| 2 |Version 1.3 + |
| 1 |Version 1.0 + |

## <a name="guest-os-release-information"></a>Information om gäst operativ system version
Det finns tre datum som är viktiga för gäst OS-versioner: **utgivnings** datum, **inaktiverat** datum och **utgångs** datum. Ett gäst operativ system anses tillgängligt när det finns i portalen och kan väljas som mål gäst operativ system. När ett gäst operativ system når det **inaktiverade** datumet tas det bort från Azure. Alla moln tjänster som är riktade till gäst operativ systemet kommer dock fortfarande att fungera som vanligt.

Fönstret mellan **inaktiverat** datum och **förfallo** datum ger dig en buffert som enkelt kan övergå från ett gäst operativ system till en nyare. Om du använder *Automatisk* som gäst operativ system är du alltid på den senaste versionen och du behöver inte bekymra dig om att den upphör att gälla.

När **förfallo** datumet har passerat, stoppas alla moln tjänster som fortfarande använder gäst operativ systemet, tas bort eller framtvingas uppgraderas. Du kan läsa mer om policyn för pensionering [här][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Gäst operativ system familj – versions förklaring
Gäst operativ system familjer baseras på utgivna versioner av Microsoft Windows Server. Gäst operativ systemet är det underliggande operativ systemet som Azure Cloud Services körs på. Varje gäst operativ system har en serie, version och versions nummer.

* **Gäst operativ system familj**  
  En Windows Server-operativsystem version som gäst operativ systemet baseras på. *Family 3* är till exempel baserad på Windows Server 2012.
* **Gäst operativ system version**  
  Som är speciell för en gäst operativ system familje avbildning plus relevanta [Microsoft Security Response Center (MSRC)][msrc] -korrigeringsfiler som är tillgängliga när den nya gäst operativ system versionen skapas. Alla korrigeringsfiler kan inte tas med.

    Talen börjar med 0 och ökar med 1 varje gången en ny uppsättning uppdateringar läggs till. Efterföljande nollor visas bara om det är viktigt. Det vill säga version 2,10 är en annan, mycket senare version än version 2,1.
* **Gäst operativ system version**  
  En ny utgåva av en gäst operativ system version. En nya utgåva sker om Microsoft hittar problem under testningen. kräver ändringar. Den senaste versionen ersätter alltid tidigare versioner, offentliga eller ej. Azure Portal tillåter endast att användare väljer den senaste versionen för en specifik version. Distributioner som körs på en tidigare version är vanligt vis inte uppgraderade beroende på felets allvarlighets grad.

I exemplet nedan är 2 familjen, 12 är versionen och "REL2" är versionen.

**Gäst operativ system version** – 2,12 REL2

**Konfigurations sträng för den här utgåvan** – WA-Guest-OS-2.12 _201208-02

Konfigurations strängen för ett gäst operativ system har samma information inbäddad i den, tillsammans med ett datum som visar vilka MSRC-korrigeringsfiler som övervägdes för den versionen. I det här exemplet ansågs MSRC-korrigeringsfiler som skapats för Windows Server 2008 R2 upp till och inklusive augusti 2012 att inkluderas. Endast korrigeringar som specifikt gäller för den versionen av Windows Server ingår. Om en MSRC-korrigering till exempel gäller Microsoft Office tas den inte med eftersom produkten inte är en del av Windows Server Base-avbildningen.

## <a name="guest-os-system-update-process"></a>Uppdaterings process för gäst operativ system
Den här sidan innehåller information om kommande gäst operativ system versioner. Kunder har angett att de vill veta när en version sker eftersom deras moln tjänst roller kommer att startas om, om de är inställda på automatisk uppdatering. Gäst operativ system versioner uppstår vanligt vis 2-3 veckor efter den MSRC-uppdaterings version som inträffar den andra tisdagen varje månad. Nya versioner innehåller alla relevanta MSRC-korrigeringsfiler för varje gäst operativ system familj.

Microsoft Azure publicerar ständigt uppdateringar. Gäst operativ systemet är bara en sådan uppdatering i pipelinen. En version kan påverkas av många faktorer för att lista här. Dessutom körs Azure på bokstavligen hundratals tusentals datorer. Det innebär att det är omöjligt att ange ett exakt datum och tidpunkt när rollerna ska starta om. Vi arbetar på en plan för att begränsa eller tid för omstarter.

När en ny version av gäst operativ systemet publiceras kan det ta lång tid att sprida hela Azure. När tjänsterna har uppdaterats till det nya gäst operativ systemet startas de om enligt uppdaterings domäner. Tjänster som har angetts för att använda automatiska uppdateringar kommer att få en version först. Efter uppdateringen ser du den nya gäst operativ system versionen som anges för din tjänst i Azure Portal. Frigörelser kan inträffa under denna period. Vissa versioner kan distribueras under längre perioder och automatiska omstarter av omstarter kanske inte inträffar i flera veckor efter det officiella lanserings datumet. När ett gäst operativ system är tillgängligt kan du välja att uttryckligen välja den versionen från portalen eller i konfigurations filen.

En mycket värdefull information om omstarter och pekare till mer teknisk information om gäst-och värd operativ system uppdateringar finns i MSDN-bloggen [starta om på grund av OS-uppgraderingar][restarts].

Om du uppdaterar gäst operativ systemet manuellt kan du läsa mer i återställnings [policyn för gäst systemet][retirepolicy] .

## <a name="guest-os-supportability-and-retirement-policy"></a>Principer för support och dragande av gäst operativ system
Principen för support och dragande av gäst operativ system beskrivs [här][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[RSS-flöde för gäst operativ system uppdatering]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[kraftfull]: https://docs.microsoft.com/azure/cloud-services/applications-dont-support-tls-1-2  
