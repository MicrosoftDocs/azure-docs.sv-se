---
title: Data konvertering för Azure API för FHIR
description: Använd mallarna $convert-data slut punkt och anpassa konverterare för att konvertera data i Azure API för FHIR.
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 01/19/2021
ms.author: ranku
ms.openlocfilehash: 2a34cfee57ecc1870c420c4c0f3c9261aa02f192
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490933"
---
# <a name="how-to-convert-data-to-fhir-preview"></a>Så här konverterar du data till FHIR (för hands version)

> [!IMPORTANT]
> Den här funktionen är tillgänglig som en offentlig förhandsversion utan servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den anpassade slut punkten $convert-data i Azure API för FHIR är avsedd för data konvertering från olika format till FHIR. Den använder motorn för flytande mallar och mallarna från [FHIR-konverteraren](https://github.com/microsoft/FHIR-Converter) som standardmallar. Du kan anpassa de här omvandlings mallarna efter behov. För närvarande stöder den HL7v2 till FHIR-konvertering.

## <a name="use-the-convert-data-endpoint"></a>Använd slut punkten $convert-data

`https://<<FHIR service base URL>>/$convert-data`

$convert-data tar en [parameter](http://hl7.org/fhir/parameters.html) resurs i begär ande texten enligt beskrivningen nedan:

**Parameter resurs:**

| Parameternamn      | Beskrivning | Godkända värden |
| ----------- | ----------- | ----------- |
| inputData      | Data som ska konverteras. | Ett giltigt värde för JSON-sträng data typ|
| inputDataType   | Datatyp för indata. | ```HL7v2``` |
| templateCollectionReference | Referens till en mall samling. Det kan vara en referens till **standardmallarna** eller en anpassad mall som är registrerad med Azure API för FHIR. Se nedan om du vill veta mer om hur du anpassar mallarna, hanterar dem på ACR och registrerar dig för Azure API för FHIR.  | ```microsofthealth/fhirconverter:default```, \<RegistryServer\>/\<imageName\>@\<imageDigest\> |
| rootTemplate | Den rotmapp som ska användas vid omvandling av data. | ```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04``` |  

> [!WARNING]
> Standardmallar hjälper dig att komma igång snabbt. Dessa kan dock uppdateras när vi uppgraderar Azure API för FHIR. Om du vill ha konsekvent data konvertering i olika versioner av Azure API för FHIR, måste du vara värd för din egna kopia av mallarna på en Azure Container Registry, registrera dem på Azure-API: et för FHIR och använda i API-anropen enligt beskrivningen senare.

**Exempel förfrågan:**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**Exempel svar:**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>Anpassa mallar

Du kan använda [FHIR Converter-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) för Visual Studio Code för att anpassa mallarna efter dina behov. Tillägget ger en interaktiv redigerings upplevelse och gör det enkelt att ladda ned Microsoft-publicerade mallar och exempel data. Mer information finns i dokumentationen i tillägget.

## <a name="host-and-use-templates"></a>Värd-och användnings mallar

Vi rekommenderar starkt att du är värd för din egen kopia av mallar på ACR. Det finns fyra steg som du kan använda för att vara värd för en egen kopia av mallar och använda dem i $convert-data-åtgärden:

1. Skicka mallarna till din Azure Container Registry.
1. Aktivera hanterad identitet på din Azure API för FHIR-instans.
1. Ge åtkomst till ACR till Azure API för FHIR-hanterad identitet.
1. Registrera ACR-servrarna i Azure-API: et för FHIR.

### <a name="push-templates-to-azure-container-registry"></a>Push-mallar till Azure Container Registry

När du har skapat en ACR-instans kan du använda _FHIR-konverteraren: kommandot push-mallar_ i [tillägget FHIR Converter](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) för att skicka de anpassade mallarna till ACR. Du kan också använda verktyget för [hantering av mallar](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md) för detta ändamål.

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>Aktivera hanterad identitet på Azure API för FHIR

Bläddra till din instans av Azure API för FHIR-tjänsten i Azure Portal och välj bladet **identitet** .
Ändra statusen till **på** för att aktivera hanterad identitet i Azure API för FHIR.

![Aktivera hanterad identitet](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>Ge åtkomst till ACR till Azure API för FHIR

Gå till bladet Access Control (IAM) i din ACR-instans och välj _Lägg till roll tilldelningar_.

![ACR roll tilldelning](media/convert-data/fhir-acr-role-assignment.png)

Bevilja AcrPull-rollen till din Azure API för FHIR-tjänstinstans.

![Lägg till roll](media/convert-data/fhir-acr-role-add.png)

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>Registrera ACR-servrarna i Azure API för FHIR

Du kan registrera ACR-servern med hjälp av Azure Portal eller med CLI.

#### <a name="registering-the-acr-server-using-azure-portal"></a>Registrera ACR-servern med hjälp av Azure Portal
Navigera till _artefakt_ bladet under _data omvandling_ i din Azure API för FHIR-instans. Listan över aktuella registrerade ACR-servrar visas. Klicka på _Lägg till_ och välj din register server i list rutan. Du måste klicka på _Spara_ för att registreringen ska börja gälla. Det kan ta några minuter att tillämpa ändringen och starta om instansen.

#### <a name="registering-the-acr-server-using-cli"></a>Registrera ACR-servern med CLI
Du kan registrera upp till 20 ACR-servrar i Azure-API: et för FHIR.

Installera healthcareapis CLI från Azure PowerShell vid behov:

```powershell
az extension add -n healthcareapis
```

Registrera ACR-servrarna i Azure API för FHIR enligt exemplen nedan:

##### <a name="register-a-single-acr-server"></a>Registrera en enskild ACR-Server

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

##### <a name="register-multiple-acr-servers"></a>Registrera flera ACR-servrar

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

### <a name="verify"></a>Verifiera

Anropa $convert-data-API: et som anger din mal lin reference i templateCollectionReference-parametern.

`<RegistryServer>/<imageName>@<imageDigest>`
