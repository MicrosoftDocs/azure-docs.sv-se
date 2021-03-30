---
title: FileUpload-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. Common. FileUpload UI för Azure Portal. Gör det möjligt för användarna att behöva ladda upp filer när de distribuerar ett hanterat program.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: c4eb3972be85ac7e291380f64127c20424b0d0f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87004296"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Elementet Microsoft. Common. FileUpload UI

En kontroll som låter en användare ange en eller flera filer som ska överföras.

## <a name="ui-sample"></a>UI-exempel

![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft-common-fileupload.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exempelutdata

Om alternativen. Multiple är false och Options. uploadMode är File, innehåller utdata innehållet i filen som en JSON-sträng:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Om alternativen. Multiple är true and'options. uploadMode är File, kommer utdata att ha innehållet i filerna som en JSON-matris:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Om alternativen. Multiple är false och Options. uploadMode är URL, har utdata en URL som en JSON-sträng:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Om alternativen. Multiple är true och Options. uploadMode är URL, innehåller utdata en lista med URL: er som en JSON-matris:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

När du testar en CreateUiDefinition trunkerar vissa webbläsare (som Google Chrome) webb adresser som genererats av elementet Microsoft. Common. FileUpload i webb läsar konsolen. Du kan behöva Högerklicka på enskilda Länkar för att kopiera de fullständiga URL: erna.

## <a name="remarks"></a>Kommentarer

- `constraints.accept` anger vilka typer av filer som visas i webbläsarens fil dialog ruta. Se [HTML5-specifikationen](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) för tillåtna värden. Standardvärdet är **Null**.
- Om `options.multiple` är inställt på **Sant** tillåts användaren att välja fler än en fil i webbläsarens fil dialog ruta. Standardvärdet är **falskt**.
- Det här elementet stöder överföring av filer i två lägen baserat på värdet för `options.uploadMode` . Om **filen** har angetts innehåller utdata innehållet i filen som en blob. Om **URL** anges överförs filen till en tillfällig plats och utdata har URL: en för blobben. Tillfälliga blobbar rensas efter 24 timmar. Standardvärdet är **File**.
- En uppladdad fil är skyddad. URL: en för utdata innehåller en [SAS-token](../../storage/common/storage-sas-overview.md?toc=/azure/storage/blobs/toc.json) för att komma åt filen under distributionen.
- Värdet för `options.openMode` avgör hur filen läses. Om filen förväntas vara oformaterad text anger du **text**. Annars anger du **Binary**. Standardvärdet är **text**.
- Om `options.uploadMode` är inställt på **File** och `options.openMode` är inställt på **binär**, är utdata Base64-kodad.
- `options.encoding` anger den kodning som ska användas vid läsning av filen. Standardvärdet är **UTF-8** och används bara när `options.openMode` har angetts till **text**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
