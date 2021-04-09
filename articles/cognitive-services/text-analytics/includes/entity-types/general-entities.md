---
title: Allmänna namngivna entiteter
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: c1ff099dd6dffe06e9707ff23fffd57ae753ab64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "99500069"
---
NER-funktionen för Textanalys returnerar följande allmänna (ej identifierade) entitets kategorier. till exempel när du skickar begär anden till `/entities/recognition/general` slut punkten.


| Kategori | Beskrivning                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Person](#category-person)     | Namn på personer.  |
| [PersonType](#category-persontype) | Jobb typer eller roller som innehas av en person. |
| [Plats](#category-location)    | Naturliga och mänskliga landmärkeer, strukturer, geografiska egenskaper och geografiskt politiska enheter |
| [Organisation](#category-organization)  | Företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer.  |
| [Händelse](#category-event)  | Historiska, sociala och naturligt förekommande händelser. |
| [Produkt](#category-product) | Fysiska objekt av olika kategorier. |
| [Kvalifikation](#category-skill) | En funktion, kunskap eller expertis.  |
| [Adress](#category-address) | Fullständiga post adresser.  |
| [Telefonnummer](#category-phonenumber) | Telefonnummer. |
| [E-post](#category-email) | E-postadresser. |
| [URL](#category-url) | URL: er till webbplatser. |
| [IP-adress](#category-ip) | Nätverks-IP-adresser. |
| [DateTime](#category-datetime) | Datum och tidpunkter på dagen. |
| [Kvantitet](#category-quantity) | Numeriska mått och enheter. |


### <a name="category-person"></a>Kategori: person

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        Person

    :::column-end:::
    :::column span="2":::
        **Information**

        Namn på personer.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Kategori: PersonType

Den här kategorin innehåller följande entitet:


:::row:::
    :::column span="":::
        **Entitet**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Information**

        Jobb typer eller roller som innehas av en person
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>Kategori: plats

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        Location

    :::column-end:::
    :::column span="2":::
        **Information**

        Naturliga och mänskliga landmärkeer, strukturer, geografiska egenskaper och geografiskt politiska enheter.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Under Kategorier

Entiteten i den här kategorin kan ha följande under kategorier.

:::row:::
    :::column span="":::
        **Underkategori för entitet**

        GPE (politisk entitet)

    :::column-end:::
    :::column span="2":::
        **Information**

        Städer, länder/regioner, stater.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Rörande

    :::column-end:::
    :::column span="2":::

        Konstgjorda-strukturer. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Skyddade

    :::column-end:::
    :::column span="2":::

        Geografiska och naturliga funktioner som floder, hav och Deserts.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-organization"></a>Kategori: organisation

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        Organisation

    :::column-end:::
    :::column span="2":::
        **Information**

        Företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer. Nationella objekt och religions ingår inte i den här entitetstypen.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Under Kategorier

Entiteten i den här kategorin kan ha följande under kategorier.

:::row:::
    :::column span="":::
        **Underkategori för entitet**

        Sjukdom

    :::column-end:::
    :::column span="2":::
        **Information**

        Medicinska företag och grupper.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Börs kurs

    :::column-end:::
    :::column span="2":::

        Fond börs grupper. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Idrotts relaterade organisationer.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>Kategori: händelse

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        Händelse

    :::column-end:::
    :::column span="2":::
        **Information**

        Historiska, sociala och naturligt förekommande händelser.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`,,,,,,, `es` `fr` `de` `it` `zh-hans` `ja` `ko` `pt-pt` och `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Under Kategorier

Entiteten i den här kategorin kan ha följande under kategorier.

:::row:::
    :::column span="":::
        **Underkategori för entitet**

        Kulturella

    :::column-end:::
    :::column span="2":::
        **Information**

        Kulturella händelser och helgdagar.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Fysiska

    :::column-end:::
    :::column span="2":::

        Naturligt förekommande händelser.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Idrotts evenemang.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>Kategori: produkt

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        Produkt

    :::column-end:::
    :::column span="2":::
        **Information**

        Fysiska objekt av olika kategorier.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>Under Kategorier

Entiteten i den här kategorin kan ha följande under kategorier.

:::row:::
    :::column span="":::
        **Underkategori för entitet**

        Dator produkter
    :::column-end:::
    :::column span="2":::
        **Information**

        Dator produkter.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>Kategori: kompetens

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        Kvalifikation

    :::column-end:::
    :::column span="2":::
        **Information**

        En funktion, kunskap eller expertis.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`  
      
   :::column-end:::
:::row-end:::

### <a name="category-address"></a>Kategori: adress

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        Adress

    :::column-end:::
    :::column span="2":::
        **Information**

        Fullständig e-postadress.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Kategori: telefonnummer

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Information**

        Telefonnummer (endast USA och EU-telefonnummer).
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-email"></a>Kategori: e-post

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        E-post

    :::column-end:::
    :::column span="2":::
        **Information**

        E-postadresser.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-url"></a>Kategori: URL

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        URL

    :::column-end:::
    :::column span="2":::
        **Information**

        URL: er till webbplatser. 
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-ip"></a>Kategori: IP

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        IP-adress

    :::column-end:::
    :::column span="2":::
        **Information**

        nätverks-IP-adresser. 
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Kategori: DateTime

Den här kategorin innehåller följande entiteter:

:::row:::
    :::column span="":::
        **Entitet**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Information**

        Datum och tidpunkter på dagen. 
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

Entiteter i den här kategorin kan ha följande under Kategorier

#### <a name="subcategories"></a>Under Kategorier

Entiteten i den här kategorin kan ha följande under kategorier.

:::row:::
    :::column span="":::
        **Underkategori för entitet**

        Datum

    :::column-end:::
    :::column span="2":::
        **Information**

        Kalender datum.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Tid

    :::column-end:::
    :::column span="2":::

        Tider på dagen.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        DateRange

    :::column-end:::
    :::column span="2":::

        Datum intervall.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        TimeRange

    :::column-end:::
    :::column span="2":::

        Tidsintervall.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Varaktighet

    :::column-end:::
    :::column span="2":::

        Varaktigheter.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ange

    :::column-end:::
    :::column span="2":::

        Ange, upprepade gånger.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Kategori: kvantitet

Den här kategorin innehåller följande entiteter:

:::row:::
    :::column span="":::
        **Entitet**

        Kvantitet

    :::column-end:::
    :::column span="2":::
        **Information**

        Siffror och numeriska kvantiteter.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Under Kategorier

Entiteten i den här kategorin kan ha följande under kategorier.

:::row:::
    :::column span="":::
        **Underkategori för entitet**

        Antal

    :::column-end:::
    :::column span="2":::
        **Information**

        Nummer.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Procent

    :::column-end:::
    :::column span="2":::

        Procenttal
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Ordnings tal

    :::column-end:::
    :::column span="2":::

        Ordnings tal.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Ålder

    :::column-end:::
    :::column span="2":::

        Personer.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Valuta

    :::column-end:::
    :::column span="2":::

        Valutor
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Dimensioner

    :::column-end:::
    :::column span="2":::

        Dimensioner och mått.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Temperatur

    :::column-end:::
    :::column span="2":::

        Temperaturer.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
