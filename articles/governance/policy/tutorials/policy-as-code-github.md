---
title: 'Självstudie: implementera Azure Policy som kod med GitHub'
description: I den här självstudien implementerar du ett Azure Policy som kod arbets flöde med export, GitHub-åtgärder och GitHub-arbetsflöden
ms.date: 10/20/2020
ms.topic: tutorial
ms.openlocfilehash: 76a46adc3fc8efab4f7a2d6e656e83c2537dd037
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92326174"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>Självstudie: implementera Azure Policy som kod med GitHub

Ett Azure Policy som kod arbets flöde gör det möjligt att hantera princip definitioner och tilldelningar som kod, styra livs cykeln för uppdatering av dessa definitioner och automatisera valideringen av resultat resultaten. I den här självstudien lär du dig att använda Azure Policy funktioner med GitHub för att bygga en process för livs cykeln. Aktiviteterna är bland annat följande:

> [!div class="checklist"]
> - Exportera princip definitioner och tilldelningar till GitHub
> - Push-princip objekt som uppdaterats i GitHub till Azure
> - Utlös en kompatibilitetskontroll från åtgärden GitHub

Om du vill tilldela en princip för att identifiera det aktuella kompatibilitetstillstånd för dina befintliga resurser förklarar du hur du gör det i snabb starts artiklarna.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Granska [design ett Azure policy som kod arbets flöde](../concepts/policy-as-code.md) för att förstå de design mönster som används i den här självstudien.

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>Exportera Azure Policy objekt från Azure Portal

Följ dessa steg om du vill exportera en princip definition från Azure Portal:

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

1. Välj **definitioner** till vänster på sidan Azure policy.

1. Använd knappen **Exportera definitioner** eller Välj ellipsen på raden i en princip definition och välj sedan **Exportera definition**.

1. Välj knappen **Logga in med GitHub** . Om du ännu inte har autentiserat med GitHub för att auktorisera Azure Policy att exportera resursen, granskar du åtkomsten till [GitHub-åtgärden](https://github.com/features/actions) i det nya fönstret som öppnas och väljer **Godkänn AzureGitHubActions** för att fortsätta med export processen. När det är klart stängs det nya fönstret.

1. På fliken **grundläggande** inställningar anger du följande alternativ och väljer sedan fliken **principer** eller **Nästa: principer** -knappen längst ned på sidan.

   - **Lagrings filter**: Ställ in på _mina databaser_ om du bara vill se de databaser som du äger eller _alla_ lagrings platser för att se alla du har beviljat åtgärden GitHub.
   - **Databas**: Ange till den lagrings plats som du vill exportera Azure policy resurserna till.
   - **Gren**: Ange grenen i lagrings platsen. Att använda en annan gren än standardinställningen är ett bra sätt att verifiera dina uppdateringar innan du sammanfogar dem till käll koden.
   - **Katalog**: _rotmappen_ som Azure policy resurserna ska exporteras till. Undermappar under den här katalogen skapas baserat på vilka resurser som exporteras.

1. På fliken **principer** anger du Sök omfång genom att välja ellipsen och välja en kombination av hanterings grupper, prenumerationer eller resurs grupper.
   
1. Använd knappen **Lägg till princip definition (n)** för att söka efter de objekt som ska exporteras. I fönstret som öppnas väljer du varje objekt som ska exporteras. Filtrera markeringen efter sökrutan eller typ. När du har valt alla objekt som ska exporteras använder du knappen **Lägg till** längst ned på sidan.

1. För varje valt objekt väljer du önskade export alternativ, till exempel _definition_ eller _definition och tilldelning (er)_ för en princip definition. Välj sedan fliken **Granska + exportera** eller **Nästa: granska + exportera** -knappen längst ned på sidan.

   > [!NOTE]
   > Om du väljer alternativ _definition och tilldelning (er)_ , exporteras endast princip tilldelningar inom det område som anges av filtret när princip definitionen läggs till.

1. På fliken **Granska och exportera** kontrollerar du informationen och använder sedan knappen **Exportera** längst ned på sidan.

1. Kontrol lera mappen GitHub lagrings platsen, Branch och _root Level_ för att se att de valda resurserna nu har exporter ATS till din käll kontroll.

Azure Policy resurserna exporteras till följande struktur i den valda GitHub-lagringsplatsen och i _mappen rot nivå_:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>Push-princip objekt som uppdaterats i GitHub till Azure

1. När princip objekt exporteras skapas även en [GitHub-arbetsflödes](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) fil med namnet `.github/workflows/manage-azure-policy-<randomLetters>.yml` för att komma igång.

   > [!NOTE]
   > Arbets flödes filen GitHub skapas varje gången en export används. Varje instans av filen är speciell för alternativen under export åtgärden.

1. Den här arbets flödes filen använder åtgärden [hantera Azure policy](https://github.com/marketplace/actions/manage-azure-policy) för att skicka ändringar som gjorts i de exporterade princip objekten i GitHub-lagringsplatsen tillbaka till Azure policy. Som standard betraktar åtgärden och synkroniserar bara de filer som skiljer sig från de som finns i Azure. Du kan också använda `assignments` parametern i åtgärden för att bara synkronisera ändringar som gjorts till vissa uppdragsfiler. Den här parametern kan endast användas för att tillämpa princip tilldelningar för en speciell miljö. Mer information finns i Readme- [filen för att hantera Azure policy-lagringsplats](https://github.com/Azure/manage-azure-policy).

1. Som standard måste arbets flödet utlösas manuellt. Det gör du med hjälp av **åtgärderna** i GitHub, väljer `manage-azure-policy-<randomLetters>` arbets flödet, väljer **Kör arbets flöde** och **kör sedan arbets flödet** igen.

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="Skärm bild av knapparna åtgärd, arbets flöde och kör arbets flöde i GitHub-webbgränssnittet.":::

   > [!NOTE]
   > Arbets flödes filen måste finnas i standard grenen för att kunna identifieras och köras manuellt.

1. Arbets flödet synkroniserar ändringarna som görs i princip objekt med Azure och ger dig status i loggarna.

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="Skärm bild av arbets flödet i åtgärds-och loggad information i loggarna.":::

1. Arbets flödet lägger också till information i Azure Policy objekt `properties.metadata` som du kan spåra.

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="Skärm bild av Azure Policys definitionen i Azure Portal uppdaterad med metadata som är särskilt för GitHub-åtgärden.":::

### <a name="trigger-compliance-scans-using-github-action"></a>Utlös genomsökning av kompatibilitet med GitHub-åtgärd

Med hjälp av [åtgärden Azure policy kompatibilitetskontroll](https://github.com/marketplace/actions/azure-policy-compliance-scan) kan du utlösa en genomsökning på begäran från ditt [GitHub-arbetsflöde](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) på en eller flera resurser, resurs grupper eller prenumerationer och ändra arbets flödets sökväg baserat på dessa resursers kompatibilitetstillstånd. Du kan också konfigurera arbets flödet så att det körs vid en schemalagd tidpunkt för att få den senaste kompatibilitetsstatus vid en lämplig tidpunkt. Den här GitHub-åtgärden kan också generera en rapport om kompatibilitetstillstånd för genomsökta resurser för vidare analys eller för arkivering.

I följande exempel körs en kompatibilitetskontroll för en prenumeration. 

```yaml

on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

```

## <a name="review"></a>Genomgång

I den här självstudien har du genomfört följande uppgifter:

> [!div class="checklist"]
> - Exporterade princip definitioner och tilldelningar till GitHub
> - Publicerade princip objekt som uppdaterats i GitHub till Azure
> - Utlöst en kompatibilitetskontroll från åtgärden GitHub

## <a name="next-steps"></a>Nästa steg

Mer information om principdefinitionernas strukturer finns i den här artikeln:

> [!div class="nextstepaction"]
> [Azure Policy-definitionsstruktur](../concepts/definition-structure.md)