---
title: Azure Policy tillägg för Visual Studio Code
description: Lär dig hur du använder Azure Policy-tillägget för Visual Studio Code för att leta upp Azure Resource Manager alias.
ms.date: 01/11/2021
ms.topic: how-to
ms.openlocfilehash: 4c4ba0eeb0506179ff92ead0ee86f048600d157e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98107947"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Använd Azure Policy-tillägg för Visual Studio Code

> Gäller Azure Policy Extension version **0.1.1** och senare

Lär dig hur du använder Azure Policy tillägget för Visual Studio Code för att söka efter [alias](../concepts/definition-structure.md#aliases), granska resurser och principer, exportera objekt och utvärdera princip definitioner. Först beskriver vi hur du installerar tillägget Azure Policy i Visual Studio Code. Sedan kommer vi att gå igenom hur du söker efter alias.

Azure Policy-tillägget för Visual Studio Code kan installeras i Windows.

## <a name="prerequisites"></a>Förutsättningar

Följande objekt krävs för att slutföra stegen i den här artikeln:

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-and-configure-the-azure-policy-extension"></a>Installera och konfigurera Azure Policy-tillägget

När du uppfyller kraven kan du installera Azure Policy tillägget för Visual Studio Code genom att följa dessa steg:

1. Öppna Visual Studio Code.
1. Gå till **Visa**  >  **tillägg** i meny raden.
1. I rutan Sök anger du **Azure policy**.
1. Välj **Azure policy** från Sök resultaten och välj sedan **Installera**.
1. Välj **Läs in igen** när det behövs.

För en nationell moln användare följer du de här stegen för att ställa in Azure-miljön först:

1. Välj **File\Preferences\Settings**.
1. Sök efter följande sträng: _Azure: Cloud_
1. Välj nation-molnet i listan:

   :::image type="content" source="../media/extension-for-vscode/set-default-azure-cloud-sign-in.png" alt-text="Skärm bild av valet av netazure Cloud-inloggning för Visual Studio Code." border="false":::

## <a name="using-the-policy-extension"></a>Använda princip tillägget

> [!NOTE]
> Ändringar som görs lokalt i principer som visas i Azure Policy-tillägget för Visual Studio Code synkroniseras inte med Azure.

### <a name="connect-to-an-azure-account"></a>Ansluta till ett Azure-konto

Om du vill utvärdera resurser och uppslags-alias måste du ansluta till ditt Azure-konto. Följ dessa steg för att ansluta till Azure från Visual Studio Code:

1. Logga in på Azure från Azure Policy-tillägget eller paletten Command.

   - Azure Policy tillägg

     Från Azure Policy-tillägget väljer **du logga in på Azure**.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png" alt-text="Skärm bild av Visual Studio Code och ikonen för Azure Policy-tillägget." border="false":::

   - Kommando palett

     I meny raden går du till **Visa**  >  **kommando-palett** och anger **Azure: Logga** in.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png" alt-text="Skärm bild av Azure Clouds inloggnings alternativ för Visual Studio Code från paletten Command." border="false":::

1. Följ anvisningarna för inloggning för att logga in på Azure. När du är ansluten visas namnet på ditt Azure-konto i statusfältet längst ned i Visual Studio Code-fönstret.

### <a name="select-subscriptions"></a>Välj prenumerationer

Första gången du loggar in, läses endast standard prenumerations resurser och principer in av Azure Policy-tillägget. Följ dessa steg om du vill lägga till eller ta bort prenumerationer från att visa resurser och principer:

1. Starta kommandot prenumeration från kommando tolken eller fönster foten.

   - Kommando palett:

     I meny raden går du till **Visa** > **kommando-palett** och anger **Azure: Välj prenumerationer**.

   - Fönster fot

     I fönster foten längst ned på skärmen väljer du det segment som matchar **Azure: \<your account\>**.

1. Använd filter rutan för att snabbt hitta prenumerationer efter namn. Markera eller ta bort kontrollen från varje prenumeration för att ange de prenumerationer som visas i Azure Policy tillägget. När du har lagt till eller tagit bort prenumerationer som ska visas väljer du **OK**.

### <a name="search-for-and-view-resources"></a>Sök efter och visa resurser

I Azure Policy-tillägget visas resurser i de valda prenumerationerna per resurs leverantör och resurs grupp i fönstret **resurser** . Trädvyn innehåller följande grupperingar av resurser i den valda prenumerationen eller på prenumerations nivån:

- **Resursprovidrar**
  - Varje registrerad resurs leverantör med resurser och relaterade underordnade resurser som har princip-alias
- **Resurs grupper**
  - Alla resurser av resurs gruppen som de befinner sig i

Som standard filtrerar tillägget "Resource Provider"-delen av befintliga resurser och resurser som har princip-alias. Ändra det här beteendet i **Inställningar**  >  **tillägg**  >  **Azure policy** om du vill se alla resurs leverantörer utan att filtrera.

Kunder med hundratals eller tusentals resurser i en enda prenumeration kan föredra ett sökbart sätt att hitta sina resurser. Med Azure Policy-tillägget kan du söka efter en speciell resurs med följande steg:

1. Starta Sök gränssnittet från Azure Policy-tillägget eller paletten Command.

   - Azure Policy tillägg

     Från Azure Policy tillägget, Hovra över panelen **resurser** och välj ellipsen och välj sedan **Sök resurser**.

   - Kommando palett:

     I meny raden går du till **Visa** > **kommando-palett** och anger **resurser: Sök efter resurser**.

1. Om fler än en prenumeration har valts för visning använder du filtret för att välja vilken prenumeration som ska genomsökas.

1. Använd filtret för att välja vilken resurs grupp som ska genomsökas som är en del av den tidigare valda prenumerationen.

1. Använd filtret för att välja vilken resurs som ska visas. Filtret fungerar både för resurs namnet och resurs typen.

### <a name="discover-aliases-for-resource-properties"></a>Identifiera alias för resurs egenskaper

När en resurs väljs, oavsett om Sök gränssnittet eller genom att markera det i trädvyn, öppnar Azure Policy-tillägget JSON-filen som representerar resursen och alla dess Azure Resource Manager egenskaps värden.

När en resurs är öppen visas Azure Policy alias om ett sådant finns under hovring över resurs hanterarens egenskaps namn eller värde. I det här exemplet är resursen en `Microsoft.Compute/virtualMachines` resurs typ och egenskapen **Properties. StorageProfile. imageReference. erbjudande** hovras över. Vid hovring visas matchande alias.

:::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Skärm bild av Azure Policy-tillägget för Visual Studio-kod som hovrar på en egenskap för att visa namnen på alias." border="false":::

> [!NOTE]
> VS Code-tillägget stöder bara utvärdering av resurs hanterarens läges egenskaper. Mer information om lägena finns i [läges definitionerna](../concepts/definition-structure.md#mode).

### <a name="search-for-and-view-policies-and-assignments"></a>Sök efter och Visa principer och tilldelningar

I Azure Policy-tillägget visas princip typer och princip tilldelningar som en TreeView för de prenumerationer som valts för att visas i fönstret **principer** . Kunder med hundratals eller tusentals principer eller tilldelningar i en enda prenumeration kan föredra ett sökbart sätt att hitta sina principer eller uppgifter. Med Azure Policy-tillägget kan du söka efter en speciell princip eller tilldelning med följande steg:

1. Starta Sök gränssnittet från Azure Policy-tillägget eller paletten Command.

   - Azure Policy tillägg

     Från Azure Policy tillägget, Hovra över panelen **principer** och välj ellipsen och välj sedan **Sök principer**.

   - Kommando palett:

     I meny raden går du till **Visa** > **kommando-palett** och anger **principer: Sök principer**.

1. Om fler än en prenumeration har valts för visning använder du filtret för att välja vilken prenumeration som ska genomsökas.

1. Använd filtret för att välja vilken princip typ eller tilldelning som ska genomsökas som är en del av den tidigare valda prenumerationen.

1. Använd filtret för att välja vilken princip eller Visa. Filtret fungerar för _DisplayName_ för princip definitionen eller princip tilldelningen.

När du väljer en princip eller tilldelning, oavsett om du väljer att använda Sök gränssnittet eller genom att markera det i trädvyn, öppnar Azure Policy-tillägget den JSON som representerar principen eller tilldelningen och alla dess egenskaps värden för Resource Manager. Tillägget kan verifiera det öppnade Azure Policy JSON-schemat.

### <a name="export-objects"></a>Exportera objekt

Objekt från dina prenumerationer kan exporteras till en lokal JSON-fil. Hovra över eller Välj ett objekt som kan exporteras i fönstret **resurser** eller **principer** . Välj ikonen Spara i slutet av den markerade raden och välj en mapp där du vill spara resurs-JSON.

Följande objekt kan exporteras lokalt:

- Fönstret resurser
  - Resursgrupper
  - Enskilda resurser (antingen i en resurs grupp eller under en resurs leverantör)
- Rutan principer
  - Principtilldelningar
  - Inbyggda princip definitioner
  - Anpassade princip definitioner
  - Initiativ

### <a name="on-demand-evaluation-scan"></a>Utvärderingsgenomsökning på begäran

En utvärderings genomsökning kan startas med Azure Policy-tillägget för Visual Studio Code. Starta en utvärdering genom att välja och fästa följande objekt: en resurs, en princip definition och en princip tilldelning.

1. Om du vill fästa varje objekt kan du hitta det i antingen rutan **resurser** eller i fönstret **principer** och välja ikonen fäst på Redigera flik. Genom att fästa ett objekt läggs det till i **utvärderings** fönstret för tillägget.
1. I **utvärderings** fönstret väljer du ett av varje objekt och använder ikonen Välj för utvärdering för att markera det som ingår i utvärderingen.
1. Välj ikonen kör utvärdering längst upp i **utvärderings** fönstret. Ett nytt fönster i Visual Studio Code öppnas med den resulterande utvärderings informationen i JSON-format.

> [!NOTE]
> För [AuditIfNotExists](../concepts/effects.md#auditifnotexists) -eller [DeployIfNotExists](../concepts/effects.md#deployifnotexists) -princip definitioner använder du plus ikonen i **utvärderings** fönstret för att välja en _relaterad_ resurs för den här kontrollen.

Utvärderings resultaten innehåller information om princip definitionen och princip tilldelningen tillsammans med egenskapen **policyEvaluations. evaluationResult** . Utdata ser ut ungefär som i följande exempel:

```json
{
    "policyEvaluations": [
        {
            "policyInfo": {
                ...
            },
            "evaluationResult": "Compliant",
            "effectDetails": {
                "policyEffect": "Audit",
                "existenceScope": "None"
            }
        }
    ]
}
```

> [!NOTE]
> VS Code-tillägget stöder bara utvärdering av resurs hanterarens läges egenskaper. Mer information om lägena finns i [läges definitionerna](../concepts/definition-structure.md#mode).
>
> Utvärderings funktionen fungerar inte på macOS-och Linux-installationer av tillägget.

### <a name="sign-out"></a>Logga ut

I meny raden går du till **Visa**  >  **kommando-palett** och anger sedan **Azure: Logga ut**.

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig att [program mässigt skapa principer](programmatically-create.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
