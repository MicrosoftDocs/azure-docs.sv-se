---
title: Skapa anpassade artefakter för den virtuella DevTest Labs-datorn | Microsoft Docs
description: Lär dig hur du skapar artefakter som ska användas med Azure DevTest Labs för att distribuera och konfigurera program efter att du har etablerat en virtuell dator.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85acfcc3811e671e58fadab08a23951778e1323d
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270690"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Skapa anpassade artefakter för den virtuella DevTest Labs-datorn

Titta på följande video för en översikt över stegen som beskrivs i den här artikeln:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Översikt
Du kan använda *artefakter* för att distribuera och konfigurera programmet när du har etablerat en virtuell dator. En artefakt består av en artefakt definitions fil och andra skriptfiler som lagras i en mapp på en git-lagringsplats. Artefakt definitions filer består av JSON och uttryck som du kan använda för att ange vad du vill installera på en virtuell dator. Du kan till exempel definiera namnet på en artefakt, ett kommando som ska köras och parametrar som är tillgängliga när kommandot körs. Du kan referera till andra skriptfiler i artefakt definitions filen efter namn.

## <a name="artifact-definition-file-format"></a>Fil format för artefakt definition
I följande exempel visas de avsnitt som utgör bas strukturen för en definitions fil:

```json
  {
    "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
    "title": "",
    "description": "",
    "iconUri": "",
    "targetOsType": "",
    "parameters": {
      "<parameterName>": {
        "type": "",
        "displayName": "",
        "description": ""
      }
    },
    "runCommand": {
      "commandToExecute": ""
    }
  }
```

| Elementnamn | Obligatoriskt? | Beskrivning |
| --- | --- | --- |
| $schema |Nej |Plats för JSON-schemafilen. JSON-schemafilen kan hjälpa dig att testa giltighets tiden för definitions filen. |
| rubrik |Ja |Namnet på den artefakt som visas i labbet. |
| beskrivning |Ja |Beskrivning av den artefakt som visas i labbet. |
| iconUri |Nej |URI för den ikon som visas i labbet. |
| targetOsType |Ja |Operativ system för den virtuella dator där artefakten är installerad. Alternativ som stöds är Windows och Linux. |
| parametrar |Nej |Värden som anges när kommandot artefakt installation körs på en dator. Detta hjälper dig att anpassa din artefakt. |
| Kör |Ja |Artefakt installations kommando som körs på en virtuell dator. |

### <a name="artifact-parameters"></a>Artefakt parametrar
I avsnittet parametrar i definitions filen anger du vilka värden som en användare kan ange när de installerar en artefakt. Du kan referera till dessa värden i kommandot artefakt installation.

Använd följande struktur för att definiera parametrar:

```json
  "parameters": {
    "<parameterName>": {
      "type": "<type-of-parameter-value>",
      "displayName": "<display-name-of-parameter>",
      "description": "<description-of-parameter>"
    }
  }
```

| Elementnamn | Obligatoriskt? | Beskrivning |
| --- | --- | --- |
| typ |Ja |Typ av parameter värde. Se följande lista för de tillåtna typerna. |
| displayName |Ja |Namnet på den parameter som visas för en användare i labbet. |
| beskrivning |Ja |Beskrivning av den parameter som visas i labbet. |

Tillåtna typer är:

* sträng (valfri giltig JSON-sträng)
* int (alla giltiga JSON-heltal)
* bool (alla giltiga JSON-booleska)
* matris (alla giltiga JSON-matriser)

## <a name="secrets-as-secure-strings"></a>Hemligheter som säkra strängar
Deklarera hemligheter som säkra strängar. Här är syntaxen för att deklarera en säker sträng-parameter i `parameters` avsnittet i **artifactfile.jspå** filen:

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

För kommandot artefakt installation kör du PowerShell-skriptet som tar den säkra sträng som skapats med hjälp av kommandot ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Det fullständiga exemplet artifactfile.jspå och artifact.ps1 (PowerShell-skript) finns i [det här exemplet på GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

En annan viktig punkt att observera är att inte logga hemligheter till-konsolen eftersom utdata har fångats för användar fel sökning. 

## <a name="artifact-expressions-and-functions"></a>Artefakt uttryck och funktioner
Du kan använda uttryck och funktioner för att skapa ett artefakt installations kommando.
Uttryck omges av hakparenteser ([och]) och utvärderas när artefakten installeras. Uttryck kan finnas var som helst i ett JSON-sträng värde. Uttryck returnerar alltid ett annat JSON-värde. Om du behöver använda en tecken sträng som börjar med en hak paren tes ([) måste du använda två hakparenteser ([[).
Normalt använder du uttryck med funktioner för att skapa ett värde. Precis som i Java Script formateras funktions anrop som **functionname (arg1, ARG2, Arg3)**.

I följande lista visas vanliga funktioner:

* **parametrar (parameterName)**: returnerar ett parameter värde som anges när artefakt kommandot körs.
* **concat (arg1, ARG2, arg3,.....)**: kombinerar flera sträng värden. Den här funktionen kan ta en rad olika argument.

I följande exempel visas hur du använder uttryck och funktioner för att konstruera ett värde:

```json
  runCommand": {
      "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
  , ' -RawPackagesList ', parameters('packages')
  , ' -Username ', parameters('installUsername')
  , ' -Password ', parameters('installPassword'))]"
  }
```

## <a name="create-a-custom-artifact"></a>Skapa en anpassad artefakt

1. Installera en JSON-redigerare. Du behöver en JSON-redigerare för att arbeta med artefakt definitions filer. Vi rekommenderar att du använder [Visual Studio Code](https://code.visualstudio.com/), som är tillgänglig för Windows, Linux och OS X.
2. Hämta ett exempel på artifactfile.jsi definitions filen. Kolla in artefakterna som skapats av DevTest Labs-teamet i vår [GitHub-lagringsplats](https://github.com/Azure/azure-devtestlab). Vi har skapat ett omfattande bibliotek med artefakter som kan hjälpa dig att skapa dina egna artefakter. Hämta en artefakt definitions fil och gör ändringar i den för att skapa dina egna artefakter.
3. Använd IntelliSense. Använd IntelliSense för att visa giltiga element som du kan använda för att skapa en artefakt definitions fil. Du kan också se de olika alternativen för värden för ett element. Om du till exempel redigerar **targetOsType** -elementet visar IntelliSense två alternativ för Windows eller Linux.
4. Lagra artefakten i den [offentliga git-lagringsplatsen för DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) eller [din egen git-lagringsplats](devtest-lab-add-artifact-repo.md). I det offentliga lagret kan du Visa artefakter som delas av andra som du kan använda direkt eller anpassa dem efter dina behov.
   
   1. Skapa en separat katalog för varje artefakt. Katalog namnet ska vara detsamma som artefakt namnet.
   2. Lagra artefakt definitions filen (artifactfile.jspå) i den katalog som du har skapat.
   3. Lagra skript som refereras från artefakt installations kommandot.
      
      Här är ett exempel på hur en artefakt mapp kan se ut:
      
      ![Exempel på artefakt mappar](./media/devtest-lab-artifact-author/git-repo.png)
5. Om du använder din egen lagrings plats för att lagra artefakter lägger du till lagrings platsen i labbet genom att följa anvisningarna i artikeln: [Lägg till en git-lagringsplats för artefakter och mallar](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Relaterade artiklar
* [Så här diagnostiserar du artefakt fel i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Anslut en virtuell dator till en befintlig Active Directory domän med hjälp av en Resource Manager-mall i DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägger till en git-artefakt i ett labb](devtest-lab-add-artifact-repo.md).
