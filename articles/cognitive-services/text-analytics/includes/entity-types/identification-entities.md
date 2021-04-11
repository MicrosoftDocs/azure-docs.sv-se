---
title: Identifiering av entiteter
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: a74c0cad971389168d643c9504f5bb809438a1ea
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106098061"
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

        ABA-flöde (American Banks Association). Returneras också med `domain=phi` .

        Lägg till i-parametern för att hämta entitets kategorin `ABARoutingNumber` `pii-categories` . `ABARoutingNumber` kommer också att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`
      
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

        SWIFT-koder för information om betalnings instruktioner. Returneras också med `domain=phi` .

        Lägg till i-parametern för att hämta entitets kategorin `SWIFTCode` `pii-categories` . `SWIFTCode` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
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

        Kreditkorts nummer. Returneras också med `domain=phi` .

        Lägg till i-parametern för att hämta entitets kategorin `CreditCardNumber` `pii-categories` . `CreditCardNumber` kommer att returneras i API-svaret om det upptäcks.

    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
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

        IBAN-koder för information om betalnings instruktioner. Returneras också med `domain=phi` .

        Lägg till i-parametern för att hämta entitets kategorin `InternationlBankingAccountNumber` `pii-categories` . `InternationlBankingAccountNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
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
    :::column span="2":::
        **Information** Returneras också med `domain=phi` .
        
        Lägg till i-parametern för att hämta entitets kategorin `ARNationalIdentityNumber` `pii-categories` . `ARNationalIdentityNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>Österrike

:::row:::
    :::column span="":::
        **Entitet**

        ID-kort för Österrike

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `ATIdentityCard` `pii-categories` . `ATIdentityCard` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Skatte identifierings nummer för Österrike

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `ATTaxIdentificationNumber` `pii-categories` . `ATTaxIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Österrike Value Added Tax (moms) nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `ATValueAddedTaxNumber` `pii-categories` . `ATValueAddedTaxNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>Australien

:::row:::
    :::column span="":::
        **Entitet**

        Bank konto nummer för Australien

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `AUDriversLicenseNumber` `pii-categories` . `AUDriversLicenseNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australiensiskt företags nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `AUBusinessNumber` `pii-categories` . `AUBusinessNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australien, företags nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `AUCompanyNumber` `pii-categories` . `AUCompanyNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Enhets licens för Australien  

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `AUDriversLicense` `pii-categories` . `AUDriversLicense` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australien, medicin konto nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `AUMedicalAccountNumber` `pii-categories` . `AUMedicalAccountNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australien Passport-nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `ATPassportNumber` `pii-categories` . `ATPassportNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australien, skatte fil nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `ATTaxIdentificationNumber` `pii-categories` . `ATTaxIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>Belgien

:::row:::
    :::column span="":::
        **Entitet**

        Belgien National Number

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `BENationalNumber` `pii-categories` . `BENationalNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Belgiskt mervärdes skatt (moms) nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `BEValueAddedTaxNumber` `pii-categories` . `BEValueAddedTaxNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>Brasilien 

:::row:::
    :::column span="":::
        **Entitet**

        Brasilien juridisk enhets nummer (CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `BRLegalEntityNumber` `pii-categories` . `BRLegalEntityNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brasilien CPF-nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `BRCPFNumber` `pii-categories` . `BRCPFNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brasilien National ID-kort (RG)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `BRNationalIDRG` `pii-categories` . `BRNationalIDRG` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>Kanada

:::row:::
    :::column span="":::
        **Entitet**

        Bank konto nummer för Kanada

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `CABankAccountNumber` `pii-categories` . `CABankAccountNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Licens nummer för Kanadas driv rutin

    :::column-end:::

    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `CADriversLicenseNumber` `pii-categories` . `CADriversLicenseNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Hälso tjänst nummer för Kanada

        
    :::column-end:::

    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `CAHealthServiceNumber` `pii-categories` . `CAHealthServiceNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Canada Passport-nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `CAPassportNumber` `pii-categories` . `CAPassportNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Canada personal Health Identification Number (PHIN)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `CAPersonalHealthIdentification` `pii-categories` . `CAPersonalHealthIdentification` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanadas sociala försäkrings nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `CASocialInsuranceNumber` `pii-categories` . `CASocialInsuranceNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Entitet**

        Nummer på Chile-identitets kort

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `CLIdentityCardNumber` `pii-categories` . `CLIdentityCardNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>Kina

:::row:::
    :::column span="":::
        **Entitet**

        PRC-nummer (Folkrepubliken Kina)

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `CNResidentIdentityCardNumber` `pii-categories` . `CNResidentIdentityCardNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Europeiska unionen (EU)

:::row:::
    :::column span="":::
        **Entitet**

        EU betalkort nummer

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `EUDebitCardNumber` `pii-categories` . `EUDebitCardNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Licens nummer för EU-drivrutin

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `EUDriversLicenseNumber` `pii-categories` . `EUDriversLicenseNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU GPU-koordinater

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `EUGPSCoordinates` `pii-categories` . `EUGPSCoordinates` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU: s nationella identifierings nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `EUNationalIdentificationNumber` `pii-categories` . `EUNationalIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU Passport-nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `EUPassportNumber` `pii-categories` . `EUPassportNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU: s socialförsäkrings nummer (SSN) eller motsvarande ID

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `EUSocialSecurityNumber` `pii-categories` . `EUSocialSecurityNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU: s skatte identifierings nummer (TIN)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `EUTaxIdentificationNumber` `pii-categories` . `EUTaxIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>Frankrike

:::row:::
    :::column span="":::
        **Entitet**

        Fransk driv rutins licens nummer

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `FRDriversLicenseNumber` `pii-categories` . `FRDriversLicenseNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franskt hälso försäkrings nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `FRHealthInsuranceNumber` `pii-categories` . `FRHealthInsuranceNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franskt nationellt ID-kort (CNI)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `FRNationalID` `pii-categories` . `FRNationalID` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franskt Passport-nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `FRPassportNumber` `pii-categories` . `FRPassportNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Frankrike, socialförsäkrings nummer (INSEE)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `FRSocialSecurityNumber` `pii-categories` . `FRSocialSecurityNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Frankrikes skatte identifierings nummer (Numéro SPI)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `FRTaxIdentificationNumber` `pii-categories` . `FRTaxIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franskt mervärdes skatt (moms) nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `FRValueAddedTaxNumber` `pii-categories` . `FRValueAddedTaxNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>Tyskland

:::row:::
    :::column span="":::
        **Entitet**

        Tysk kör korts nummer

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `DEDriversLicenseNumber` `pii-categories` . `DEDriversLicenseNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kort nummer för Tyskland-identitet

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `DEIdentityCardNumber` `pii-categories` . `DEIdentityCardNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Tysklands Passport-nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `DEPassportNumber` `pii-categories` . `DEPassportNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Skatte identifikations nummer för Tyskland

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `DETaxIdentificationNumber` `pii-categories` . `DETaxIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Mervärdes skatte nummer för Tyskland

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `DEValueAddedNumber` `pii-categories` . `DEValueAddedNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hongkong

:::row:::
    :::column span="":::
        **Entitet**

        HKID-nummer (Hong Kong Identity Card)

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `HKIdentityCardNumber` `pii-categories` . `HKIdentityCardNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Ungern

:::row:::
    :::column span="":::
        **Entitet**

        Ungern personal Identification Number

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `HUPersonalIdentificationNumber` `pii-categories` . `HUPersonalIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ungern skatte identifierings nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `HUTaxIdentificationNumber` `pii-categories` . `HUTaxIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ungern mervärdes skatte nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `HUValueAddedNumber` `pii-categories` . `HUValueAddedNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>Indien

:::row:::
    :::column span="":::
        **Entitet**

        Permanent konto nummer för Indien (PAN)

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `INPermanentAccount` `pii-categories` . `INPermanentAccount` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Aadhaar-nummer (Indien Unique Identification)

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `INUniqueIdentificationNumber` `pii-categories` . `INUniqueIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonesien

:::row:::
    :::column span="":::
        **Entitet**

        KTP-nummer (Indonesien Identity Card)

    :::column-end:::
    :::column span="2":::

        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `IDIdentityCardNumber` `pii-categories` . `IDIdentityCardNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irland

:::row:::
    :::column span="":::
        **Entitet**

        Irland personal public service (PPS)-nummer

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `IEPersonalPublicServiceNumber` `pii-categories` . `IEPersonalPublicServiceNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        Irland personal public service (PPS) Number v2

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `IEPersonalPublicServiceNumberV2` `pii-categories` . `IEPersonalPublicServiceNumberV2` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israel

:::row:::
    :::column span="":::
        **Entitet**

        Israel National ID

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `ILNationalID` `pii-categories` . `ILNationalID` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Israel bank konto nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `ILBankAccountNumber` `pii-categories` . `ILBankAccountNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>Italien

:::row:::
    :::column span="":::
        **Entitet**

        Italien-driv Rutinens licens-ID

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `ITDriversLicenseNumber` `pii-categories` . `ITDriversLicenseNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Italiensk räkenskaps kod

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `ITFiscalCode` `pii-categories` . `ITFiscalCode` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Italienskt mervärdes skatte nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `ITValueAddedTaxNumber` `pii-categories` . `ITValueAddedTaxNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japan

:::row:::
    :::column span="":::
        **Entitet**

        Japanskt bank konto nummer

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `JPBankAccountNumber` `pii-categories` . `JPBankAccountNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japansk driv Rutins licens nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `JPDriversLicenseNumber` `pii-categories` . `JPDriversLicenseNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan "mina siffror" (personliga)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `JPMyNumberPersonal` `pii-categories` . `JPMyNumberPersonal` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan "mitt nummer" (företag)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `JPMyNumberCorporate` `pii-categories` . `JPMyNumberCorporate` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan, inhemskt registrerings nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `ITValueAddedTaxNumber` `pii-categories` . `ITValueAddedTaxNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japanskt kort nummer för Japan

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `JPResidenceCardNumber` `pii-categories` . `JPResidenceCardNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan, sociala försäkrings nummer (SIN)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `JPSocialInsuranceNumber` `pii-categories` . `JPSocialInsuranceNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japanskt Passport-nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `JPPassportNumber` `pii-categories` . `JPPassportNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxemburg

:::row:::
    :::column span="":::
        **Entitet**

        Luxemburgsk nationella identifierings nummer (fysiska personer)

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `LUNationalIdentificationNumberNatural` `pii-categories` . `LUNationalIdentificationNumberNatural` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Luxemburgsk nationella identifierings nummer (icke-fysiska personer)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `LUNationalIdentificationNumberNonNatural` `pii-categories` . `LUNationalIdentificationNumberNonNatural` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entitet**

        Nummer för Malta-identitets kort

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `MTIdentityCardNumber` `pii-categories` . `MTIdentityCardNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nummer på Malta skatte identifikation

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `MTTaxIDNumber` `pii-categories` . `MTTaxIDNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nya Zeeland

:::row:::
    :::column span="":::
        **Entitet**

        Nya Zeeland bank konto nummer

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `NZBankAccountNumber` `pii-categories` . `NZBankAccountNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ny Zeelands driv Rutins licens nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `NZDriversLicenseNumber` `pii-categories` . `NZDriversLicenseNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nya Zeeland, inre intäkts nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `NZInlandRevenueNumber` `pii-categories` . `NZInlandRevenueNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        New Zeelands ministeriet för hälso tillstånd

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `NZMinistryOfHealthNumber` `pii-categories` . `NZMinistryOfHealthNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Nya Zeeland, sociala välfärd-nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `NZSocialWelfareNumber` `pii-categories` . `NZSocialWelfareNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filippinerna

:::row:::
    :::column span="":::
        **Entitet**

        Filippinerna-enhetligt ID-nummer för flera syften

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `PHUnifiedMultiPurposeIDNumber` `pii-categories` . `PHUnifiedMultiPurposeIDNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugal 

:::row:::
    :::column span="":::
        **Entitet**

        Kort nummer för Portugal, unionsmedborgare

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `PTCitizenCardNumber` `pii-categories` . `PTCitizenCardNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Skatte identifikations nummer för Portugal

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `PTTaxIdentificationNumber` `pii-categories` . `PTTaxIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapore

:::row:::
    :::column span="":::
        **Entitet**

        NRIC-nummer (National registration ID Card) i Singapore

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `PTTaxIdentificationNumber` `pii-categories` . `PTTaxIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Sydafrika

:::row:::
    :::column span="":::
        **Entitet**

        FN-nummer för Sydafrika

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `ZAIdentificationNumber` `pii-categories` . `ZAIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Sydkorea

:::row:::
    :::column span="":::
        **Entitet**

        Sydkorea, inhemskt registrerings nummer

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `KRResidentRegistrationNumber` `pii-categories` . `KRResidentRegistrationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>Spanien

:::row:::
    :::column span="":::
        **Entitet**

        Spansk DNI

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `ESDNI` `pii-categories` . `ESDNI` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spaniens person nummer (SSN)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `ESSocialSecurityNumber` `pii-categories` . `ESSocialSecurityNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spaniens skatte identifierings nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `ESTaxIdentificationNumber` `pii-categories` . `ESTaxIdentificationNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Schweiz

:::row:::
    :::column span="":::
        **Entitet**

        Schweiziska socialförsäkrings nummer AHV

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `CHSocialSecurityNumber` `pii-categories` . `CHSocialSecurityNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taiwan 

:::row:::
    :::column span="":::
        **Entitet**

        Nationella ID för Taiwan

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `TWNationalID` `pii-categories` . `TWNationalID` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Inhemskt Taiwan-certifikat (ARC/TARC)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `TWResidentCertificate` `pii-categories` . `TWResidentCertificate` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Taiwanesiskt Passport-nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `TWPassportNumber` `pii-categories` . `TWPassportNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Storbritannien

:::row:::
    :::column span="":::
        **Entitet**

        engelska Kör korts nummer

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `UKDriversLicenseNumber` `pii-categories` . `UKDriversLicenseNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       engelska Nummer för val av rulle

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `UKNationalInsuranceNumber` `pii-categories` . `UKNationalInsuranceNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       engelska NHS-nummer (National Hälsotjänst)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `UKNationalHealthNumber` `pii-categories` . `UKNationalHealthNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       engelska Nationellt försäkrings nummer (NINO)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `UKNationalInsuranceNumber` `pii-categories` . `UKNationalInsuranceNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       engelska eller amerikanskt Passport-nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `USUKPassportNumber` `pii-categories` . `USUKPassportNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       engelska Unikt Taxpayer Reference Number

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `UKUniqueTaxpayerNumber` `pii-categories` . `UKUniqueTaxpayerNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>USA

:::row:::
    :::column span="":::
        **Entitet**

        Amerikanskt socialförsäkrings nummer (SSN)

    :::column-end:::
    :::column span="2":::
        **Information**

        Lägg till i-parametern för att hämta entitets kategorin `USSocialSecurityNumber` `pii-categories` . `USSocialSecurityNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Amerikansk kör korts nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `USDriversLicenseNumber` `pii-categories` . `USDriversLicenseNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       USA eller Storbritannien Passport-nummer

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `USUKPassportNumber` `pii-categories` . `USUKPassportNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Amerikanskt individuellt Taxpayer Identification Number (ITIN)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `USIndividualTaxpayerIdentification` `pii-categories` . `USIndividualTaxpayerIdentification` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Nummer för USA: s narkotika verk ställande byrå (DIETANOLAMIN)

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `DrugEnforcementAgencyNumber` `pii-categories` . `DrugEnforcementAgencyNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Konto nummer för amerikansk bank

    :::column-end:::
    :::column span="2":::

        Lägg till i-parametern för att hämta entitets kategorin `USBankAccountNumber` `pii-categories` . `USBankAccountNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
