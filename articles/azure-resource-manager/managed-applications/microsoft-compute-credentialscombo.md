---
title: CredentialsCombo-GRÄNSSNITTs element
description: Beskriver användar gränssnitts elementet Microsoft. Compute. CredentialsCombo för Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: 47c88e08e5d2eac09fbcd5b60a8ccd73b46c9616
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063805"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>GRÄNSSNITTs element för Microsoft. Compute. CredentialsCombo

En grupp med kontroller med inbyggd verifiering för Windows-och Linux-lösenord och offentliga SSH-nycklar.

## <a name="ui-sample"></a>UI-exempel

För Windows ser användarna:

![Microsoft. Compute. CredentialsCombo-fönster](./media/managed-application-elements/microsoft-compute-credentialscombo-windows.png)

För Linux med lösen ord valt ser användarna:

![Microsoft. Compute. CredentialsCombo Linux-lösenord](./media/managed-application-elements/microsoft-compute-credentialscombo-linux-password.png)

För Linux med offentlig SSH-nyckel vald ser användarna:

![Microsoft. Compute. CredentialsCombo Linux-nyckel](./media/managed-application-elements/microsoft-compute-credentialscombo-linux-key.png)

## <a name="schema"></a>Schema

För Windows använder du följande schema:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

För **Linux** använder du följande schema:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="sample-output"></a>Exempelutdata

Om `osPlatform` är **Windows**, eller `osPlatform` är **Linux** och användaren har angett ett lösen ord i stället för en offentlig SSH-nyckel, returnerar kontrollen följande utdata:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Om `osPlatform` är **Linux** och användaren TILLHANDAHÖLL en offentlig SSH-nyckel, returnerar kontrollen följande utdata:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Kommentarer

- `osPlatform` måste anges och kan vara antingen **Windows** eller **Linux**.
- Om `constraints.required` är inställt på **Sant** måste text rutorna lösen ord eller SSH-offentlig nyckel ha värden för att kunna verifiera. Standardvärdet är **True**.
- Om `options.hideConfirmation` är inställt på **Sant** döljs den andra text rutan för att bekräfta användarens lösen ord. Standardvärdet är **falskt**.
- Om `options.hidePassword` är inställt på **Sant** är alternativet för att använda lösenordsautentisering dolt. Den kan endast användas när `osPlatform` är **Linux**. Standardvärdet är **falskt**.
- Ytterligare begränsningar för tillåtna lösen ord kan implementeras med hjälp av `customPasswordRegex` egenskapen. Strängen i `customValidationMessage` visas när ett lösen ord Miss lyckas med anpassad verifiering. Standardvärdet för båda egenskaperna är **Null**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
