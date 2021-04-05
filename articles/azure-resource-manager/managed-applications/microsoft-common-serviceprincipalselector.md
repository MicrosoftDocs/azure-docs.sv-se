---
title: ServicePrincipalSelector-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. Common. ServicePrincipalSelector UI för Azure Portal. Ger en kontroll för att välja ett program och en text ruta för att ange ett lösen ord eller tumavtryck för certifikatet.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184458"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Elementet Microsoft. Common. ServicePrincipalSelector UI

En kontroll som låter användarna välja ett befintligt [huvud namn för tjänsten](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) eller registrera ett nytt program. När du väljer **Skapa ny** följer du stegen för att registrera ett nytt program. När du väljer ett befintligt program tillhandahåller kontrollen en text ruta för att ange ett lösen ord eller tumavtryck för certifikatet.

## <a name="ui-samples"></a>UI-exempel

Du kan använda ett standard program, skapa ett nytt program eller använda ett befintligt program.

### <a name="use-default-application-or-create-new"></a>Använd standard programmet eller skapa ny

Standardvyn bestäms av värdena i `defaultValue` egenskapen och **tjänstens huvud namns typ** är inställd på **Skapa ny**. Om `principalId` egenskapen innehåller en giltig globalt unik identifierare (GUID) söker kontrollen efter programmets `objectId` . Standardvärdet gäller om användaren inte gör något val från kontrollen.

Om du vill registrera ett nytt program väljer du **ändrings val** och dialog rutan **Registrera ett program** visas. Ange **namn**, **typ av konto som stöds** och välj knappen **Registrera** .

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Initial vy för Microsoft. Common. ServicePrincipalSelector.":::

När du har registrerat ett nytt program använder du **autentiseringstypen** för att ange ett lösen ord eller tumavtryck för certifikatet.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Microsoft. Common. ServicePrincipalSelector-autentisering.":::

### <a name="use-existing-application"></a>Använd befintligt program

Om du vill använda ett befintligt program väljer du **Välj befintligt** och väljer sedan **gör markering**. Använd dialog rutan **Välj ett program** för att söka efter programmets namn. Från resultaten väljer du programmet och sedan knappen **Välj** . När du har valt ett program visar kontrollen **autentiseringstypen** för att ange ett lösen ord eller tumavtryck för certifikatet.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Microsoft. Common. ServicePrincipalSelector Välj befintligt program.":::

## <a name="schema"></a>Schema

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer

- De obligatoriska egenskaperna är följande:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Anger standard `principalId` och `name` .

- De valfria egenskaperna är följande:
  - `toolTip`: Bifogar en knapp Beskrivning `infoBalloon` till varje etikett.
  - `visible`: Dölj eller Visa kontrollen.
  - `options`: Anger om alternativet tumavtryck för certifikatet ska vara tillgängligt eller inte.
  - `constraints`: Regex-begränsningar för lösen ords validering.

## <a name="example"></a>Exempel

Följande är ett exempel på `Microsoft.Common.ServicePrincipalSelector` kontrollen. `defaultValue`Egenskapen anges `principalId` `<default guid>` som en plats hållare för ett standard-ID för program-ID: t.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Exempel på utdata

`appId`Är ID: t för den program registrering som du har valt eller skapat. `objectId`Är en matris med objekt-ID: n för tjänstens huvud namn som har kon figurer ATS för den valda program registreringen.

När inget val görs från kontrollen `newOrExisting` är egenskap svärdet **nytt**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

När **Skapa nytt** eller ett befintligt program väljs från kontrollen `newOrExisting` , är egenskap svärdet **befintligt**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Nästa steg

- En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
- En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).