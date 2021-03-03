---
title: Identifiering av entiteter
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: a376b050d79709885e3542d330bb6b1eea48d046
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750584"
---
### <a name="financial-account-identification"></a>Identifiering av finansiella konton

Den här enhets kategorin innehåller finansiell information och officiella formulär för identifiering.

#### <a name="category-aba-routing-number"></a>Kategori: ABA-routing number

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        ABA-routing number

    :::column-end:::
    :::column span="2":::
        **Information**

        ABA-flöde (American Banks Association).
      
    :::column-end:::
:::row-end:::

#### <a name="category-swift-code"></a>Kategori: SWIFT-kod

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        SWIFT-kod

    :::column-end:::
    :::column span="2":::
        **Information**

        SWIFT-koder för information om betalnings instruktioner.
      
    :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Kategori: kredit kort

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        Kreditkort

    :::column-end:::
    :::column span="2":::
        **Information**

        Kreditkorts nummer. 
      
    :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Kategori: internationellt bank konto nummer (IBAN) 

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        Kreditkort

    :::column-end:::
    :::column span="2":::
        **Information**

        IBAN-koder för information om betalnings instruktioner.
      
    :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Identifiering av myndigheter och land/region

> [!NOTE]
> Följande finansiella och landsspecifika entiteter returneras inte med `domain=phi` parametern:
> * Passport-nummer
> * Skatte-ID

Följande entiteter är grupperade och listade efter land:

#### <a name="argentina"></a>Argentina

:::row:::
    :::column span="":::
        **Entitet**

        DNI-nummer (Argentina National Identity)

    :::column-end:::
:::row-end:::


#### <a name="austria"></a>Österrike

:::row:::
    :::column span="":::
        **Entitet**

        ID-kort för Österrike

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Skatte identifierings nummer för Österrike

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Österrike Value Added Tax (moms) nummer

    :::column-end:::
:::row-end:::



#### <a name="australia"></a>Australien

:::row:::
    :::column span="":::
        **Entitet**

        Bank konto nummer för Australien

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Australiensiskt företags nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Australien, företags nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Enhets licens för Australien  

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australien, medicin konto nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australien Passport-nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Australien Passport-nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Australien, skatte fil nummer

    :::column-end:::

:::row-end:::


#### <a name="belgium"></a>Belgien

:::row:::
    :::column span="":::
        **Entitet**

        Belgien National Number

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Belgiskt mervärdes skatt (moms) nummer

    :::column-end:::

:::row-end:::


#### <a name="brazil"></a>Brasilien 

:::row:::
    :::column span="":::
        **Entitet**

        Brasilien juridisk enhets nummer (CNPJ)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brasilien CPF-nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Brasilien National ID-kort (RG)

    :::column-end:::
:::row-end:::

#### <a name="canada"></a>Kanada

:::row:::
    :::column span="":::
        **Entitet**

        Bank konto nummer för Kanada

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Licens nummer för Kanadas driv rutin

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Hälso tjänst nummer för Kanada

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Canada Passport-nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Canada personal Health Identification Number (PHIN)

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Kanadas sociala försäkrings nummer

    :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Entitet**

        Nummer på Chile-identitets kort

    :::column-end:::
:::row-end:::

#### <a name="china"></a>Kina

:::row:::
    :::column span="":::
        **Entitet**

        PRC-nummer (Folkrepubliken Kina)

    :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Europeiska unionen (EU)

:::row:::
    :::column span="":::
        **Entitet**

        EU betalkort nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Licens nummer för EU-drivrutin

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU: s nationella identifierings nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU Passport-nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU: s socialförsäkrings nummer (SSN) eller motsvarande ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU: s skatte identifierings nummer (TIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU GPS-koordinater

    :::column-end:::
:::row-end:::

#### <a name="france"></a>Frankrike

:::row:::
    :::column span="":::
        **Entitet**

        Fransk driv rutins licens nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franskt hälso försäkrings nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franskt nationellt ID-kort (CNI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franskt Passport-nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Frankrike, socialförsäkrings nummer (INSEE)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Frankrikes skatte identifierings nummer (Numéro SPI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franskt mervärdes skatt (moms) nummer

    :::column-end:::
:::row-end:::

#### <a name="germany"></a>Tyskland

:::row:::
    :::column span="":::
        **Entitet**

        Tysk kör korts nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kort nummer för Tyskland-identitet

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Tysklands Passport-nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Skatte identifikations nummer för Tyskland

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Mervärdes skatte nummer för Tyskland

    :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hongkong

:::row:::
    :::column span="":::
        **Entitet**

        HKID-nummer (Hong Kong Identity Card)

    :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Ungern

:::row:::
    :::column span="":::
        **Entitet**

        Ungern personal Identification Number

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ungern skatte identifierings nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ungern mervärdes skatte nummer

    :::column-end:::
:::row-end:::

#### <a name="india"></a>Indien

:::row:::
    :::column span="":::
        **Entitet**

        Permanent konto nummer för Indien (PAN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Aadhaar-nummer (Indien Unique Identification)

    :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonesien

:::row:::
    :::column span="":::
        **Entitet**

        KTP-nummer (Indonesien Identity Card)

    :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irland

:::row:::
    :::column span="":::
        **Entitet**

        Irland personal public service (PPS)-nummer

    :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israel

:::row:::
    :::column span="":::
        **Entitet**

        Israel National ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Israel bank konto nummer

    :::column-end:::
:::row-end:::

#### <a name="italy"></a>Italien

:::row:::
    :::column span="":::
        **Entitet**

        Italien-driv Rutinens licens-ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Italiensk räkenskaps kod

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Italienskt mervärdes skatte nummer

    :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japan

:::row:::
    :::column span="":::
        **Entitet**

        Japanskt bank konto nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Japansk driv Rutins licens nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan "mina siffror" (personliga)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan "mitt nummer" (företag)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan, inhemskt registrerings nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japanskt kort nummer för Japan

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan, sociala försäkrings nummer (SIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japanskt Passport-nummer

    :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxemburg

:::row:::
    :::column span="":::
        **Entitet**

        Luxemburgsk nationella identifierings nummer (fysiska personer)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Luxemburgsk nationella identifierings nummer (icke-fysiska personer)

    :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entitet**

        Nummer för Malta-identitets kort

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nummer på Malta skatte identifikation

    :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nya Zeeland

:::row:::
    :::column span="":::
        **Entitet**

        Nya Zeeland bank konto nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ny Zeelands driv Rutins licens nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nya Zeeland, inre intäkts nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        New Zeelands ministeriet för hälso tillstånd

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Nya Zeeland, sociala välfärd-nummer

    :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filippinerna

:::row:::
    :::column span="":::
        **Entitet**

        Filippinerna-enhetligt ID-nummer för flera syften

    :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugal 

:::row:::
    :::column span="":::
        **Entitet**

        Kort nummer för Portugal, unionsmedborgare

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Skatte identifikations nummer för Portugal

    :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapore

:::row:::
    :::column span="":::
        **Entitet**

        NRIC-nummer (National registration ID Card) i Singapore

    :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Sydafrika

:::row:::
    :::column span="":::
        **Entitet**

        FN-nummer för Sydafrika

    :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Sydkorea

:::row:::
    :::column span="":::
        **Entitet**

        Sydkorea, inhemskt registrerings nummer

    :::column-end:::
:::row-end:::

#### <a name="spain"></a>Spanien

:::row:::
    :::column span="":::
        **Entitet**

        Spansk DNI

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spaniens person nummer (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spaniens skatte identifierings nummer

    :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Schweiz

:::row:::
    :::column span="":::
        **Entitet**

        Schweiziska socialförsäkrings nummer AHV

    :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taiwan 

:::row:::
    :::column span="":::
        **Entitet**

        Nationella ID för Taiwan

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Inhemskt Taiwan-certifikat (ARC/TARC)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Taiwanesiskt Passport-nummer

    :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Storbritannien

:::row:::
    :::column span="":::
        **Entitet**

        engelska Kör korts nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       engelska Nummer för val av rulle

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       engelska NHS-nummer (National Hälsotjänst)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       engelska Nationellt försäkrings nummer (NINO)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       engelska eller amerikanskt Passport-nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

       engelska Unikt Taxpayer Reference Number

    :::column-end:::

:::row-end:::


#### <a name="united-states"></a>USA

:::row:::
    :::column span="":::
        **Entitet**

        Amerikanskt socialförsäkrings nummer (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Amerikansk kör korts nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       USA eller Storbritannien Passport-nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Amerikanskt individuellt Taxpayer Identification Number (ITIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Nummer för USA: s narkotika verk ställande byrå (DIETANOLAMIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Konto nummer för amerikansk bank

    :::column-end:::
:::row-end:::
