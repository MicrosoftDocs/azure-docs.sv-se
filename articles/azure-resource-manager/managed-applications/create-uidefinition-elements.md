---
title: Skapa GRÄNSSNITTs definitions element
description: Beskriver de element som ska användas när du konstruerar GRÄNSSNITTs definitioner för Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 3e8bdd85c112d90fd5e10f6e65e48ca6b3f71b6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410750"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition-element

I den här artikeln beskrivs schema och egenskaper för alla element som stöds i en CreateUiDefinition.

## <a name="schema"></a>Schema

Schemat för de flesta element är följande:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Egenskap | Krävs | Beskrivning |
| -------- | -------- | ----------- |
| name | Ja | En intern identifierare som refererar till en angiven instans av ett element. Den vanligaste användningen av element namnet är i `outputs` , där indatavärdena för de angivna elementen mappas till parametrarna i mallen. Du kan också använda den för att binda utmatning svärdet för ett element till ett `defaultValue` annat element. |
| typ | Ja | GRÄNSSNITTs kontroll som ska renderas för elementet. En lista över typer som stöds finns i [element](#elements). |
| etikett | Ja | Objektets visnings text. Vissa element typer innehåller flera etiketter, så värdet kan vara ett objekt som innehåller flera strängar. |
| Standar | Inga | Standardvärdet för elementet. Vissa element typer stöder komplexa standardvärden, vilket innebär att värdet kan vara ett objekt. |
| Angiven | Inga | Texten som ska visas i verktygs tipset för-elementet. På liknande sätt `label` stöder vissa element flera verktygs tips strängar. Infogade länkar kan bäddas in med markdown-syntax.
| begränsningar | Inga | En eller flera egenskaper som används för att anpassa elementets validerings beteende. De egenskaper som stöds för begränsningar varierar beroende på element typ. Vissa element typer stöder inte anpassning av verifierings beteendet och har därför ingen egenskap för begränsning. |
| alternativ | Inga | Ytterligare egenskaper som anpassar elementets beteende. På liknande sätt `constraints` varierar de egenskaper som stöds beroende på element typ. |
| tydligt | Inga | Anger om elementet visas. Om `true` , element och tillämpliga underordnade element visas. Standardvärdet är `true`. Använd [logiska funktioner](create-uidefinition-functions.md#logical-functions) för att dynamiskt styra egenskapens värde.

## <a name="elements"></a>Element

Dokumentationen för varje-element innehåller ett UI-exempel, schema, kommentarer om elementets beteende (vanligt vis vad gäller verifiering och anpassning som stöds) och exempel på utdata.

- [Kryss ruta för Microsoft. Common.](microsoft-common-checkbox.md)
- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft. Common. EditableGrid](microsoft-common-editablegrid.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft. Common. info](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft. Common. ServicePrincipalSelector](microsoft-common-serviceprincipalselector.md)
- [Microsoft. Common. Slider](microsoft-common-slider.md)
- [Microsoft. Common. TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft. Common. TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft. KeyVaultCertificateSelector](microsoft-keyvault-keyvaultcertificateselector.md)
- [Microsoft. ManagedIdentity. IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md)
- [Microsoft. Solutions. ResourceSelector](microsoft-solutions-resourceselector.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)
- [Microsoft. Storage. StorageBlobSelector](microsoft-storage-storageblobselector.md)

## <a name="next-steps"></a>Nästa steg

En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
