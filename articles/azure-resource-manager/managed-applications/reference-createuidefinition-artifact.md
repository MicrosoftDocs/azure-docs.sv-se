---
title: CreateUiDefinition-artefakt
description: Visar hur du skapar createUiDefinition-artefakten för ett Azure-hanterat program. Filen heter createUiDefinition.jspå.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 9be1cb02c419314a33b9487bf183ef3659b2b549
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75651493"
---
# <a name="reference-user-interface-elements-artifact"></a>Referens: artefakt för användar gränssnitts element

Den här artikeln är en referens för en *createUiDefinition.jspå* artefakten i Azure Managed Applications. Mer information om hur du redigerar användar gränssnitts element finns i [skapa användar gränssnitts element](create-uidefinition-elements.md).

## <a name="user-interface-elements"></a>Element för användargränssnitt

Följande JSON visar ett exempel på *createUiDefinition.jspå* filen för Azure Managed Applications:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skapa ett hanterat program med anpassade åtgärder och resurser](tutorial-create-managed-app-with-custom-provider.md)
- [Referens: artefakt för distributions mal len](reference-main-template-artifact.md)
- [Referens: Visa definitions artefakt](reference-view-definition-artifact.md)