---
title: Automatisera byggen, tester och distributioner av ett Azure Stream Analytics jobb med CI/CD-verktyg
description: Den här artikeln beskriver hur du använder Azure Stream Analytics CI/CD-verktyg för att automatiskt bygga, testa och distribuera ett Azure Stream Analytics-projekt.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 9e79e0a2c030e2ebfcd5ddfd49e7c05afdb0dc3c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019558"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>Automatisera byggen, tester och distributioner av ett Azure Stream Analytics jobb med CI/CD-verktyg

Du kan använda Azure Stream Analytics CI/CD NPM-paketet för att automatiskt bygga, testa och distribuera din Azure Stream Analytics Visual Studio-kod eller Visual Studio-projekt. Projekten kan skapas med utvecklingsverktyg eller så kan de exporteras från befintliga Stream Analytics-jobb. Den här artikeln beskriver hur du använder NPM-paketet med valfritt CI/CD-system. För distribution med Azure-pipelines, se [Använd Azure-DevOps för att skapa en CI/CD-pipeline för ett Stream Analytics jobb](set-up-cicd-pipeline.md).

## <a name="installation"></a>Installation

Du kan [Ladda ned paketet](https://www.npmjs.com/package/azure-streamanalytics-cicd) direkt eller installera det [globalt](https://docs.npmjs.com/downloading-and-installing-packages-globally) med `npm install -g azure-streamanalytics-cicd` kommandot. Vi rekommenderar att du använder kommandot, som också kan användas i en PowerShell-eller Azure CLI-skript-uppgift för en build-pipeline i **Azure-pipelines**.

## <a name="build-the-project"></a>Bygga projektet

NPM **-paketet ASA-streamanalytics-cicd** innehåller verktyg för att skapa Azure Resource Manager mallar för Stream Analytics [Visual Studio Code-projekt](./quick-create-visual-studio-code.md) eller [Visual Studio-projekt](stream-analytics-quick-create-vs.md). Du kan också använda NPM-paketet på Windows, macOS och Linux utan att installera Visual Studio Code eller Visual Studio.

När du har installerat paketet använder du följande kommando för att bygga dina Stream Analytics-projekt.

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

Kommandot *build* kontrollerar nyckelords-syntaxen och genererar Azure Resource Manager-mallen.

| Parameter | Beskrivning |
|---|---|
| `-project` | Den absoluta sökvägen till **asaproj.jsi** filen för ditt Visual Studio Code-projekt eller **[ditt projekt namn]. Asaproj** för Visual Studio-projekt. |
| `-outputPath` | Sökvägen till mappen utdata för Azure Resource Manager mallar. Om den inte anges placeras mallarna i den aktuella katalogen. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

När ett Stream Analytics projekt skapas, genererar följande två filer under mappen utdata:

* Azure Resource Manager mallfil

   `[ProjectName].JobTemplate.json`

* Azure Resource Manager parameter fil

   `[ProjectName].JobTemplate.parameters.json`

Standard parametrarna i parameters.jsi filen är från inställningarna i Visual Studio Code-eller Visual Studio-projektet. Om du vill distribuera till en annan miljö ersätter du parametrarna på motsvarande sätt.

Standardvärdena för alla autentiseringsuppgifter är **Null**. Du måste ange värdena innan du distribuerar till Azure.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

Om du vill använda hanterad identitet för Azure Data Lake Store gen1 som utgående mottagare måste du ge åtkomst till tjänstens huvud namn med hjälp av PowerShell innan du distribuerar till Azure. Lär dig mer om hur du [distribuerar ADLS gen1 med hanterad identitet med Resource Manager-mall](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).

## <a name="local-run"></a>Lokal körning

Om ditt projekt har angivna lokala indatafiler kan du köra ett Stream Analytics skript lokalt genom att använda `localrun` kommandot.

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parameter | Beskrivning |
|---|---|
| `-project` | Sökvägen till **asaproj.jspå** filen för ditt Visual Studio Code-projekt eller **[ditt projekt namn]. Asaproj** för Visual Studio-projekt. |
| `-outputPath` | Sökvägen till mappen utdata. Om den inte anges placeras resultatet av utdata i den aktuella katalogen. |
| `-customCodeZipFilePath` | Sökvägen till zip-filen för den anpassade C#-koden, till exempel UDF eller deserialiserare, om de används. Paketera DLL-filerna i en zip-fil och ange den här sökvägen. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> JavaScript UDF fungerar bara på Windows.

## <a name="automated-test"></a>Automatiserat test

Du kan använda CI/CD NPM-paketet för att konfigurera och köra automatiserade tester för ditt Stream Analytics-skript.

### <a name="add-a-test-case"></a>Lägg till ett test fall

Test fallen beskrivs i en test konfigurations fil. Kom igång genom att använda `addtestcase` kommandot för att lägga till en mall för test ärenden i test konfigurations filen. Om test konfigurations filen inte finns skapas en som standard.

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| Parameter | Beskrivning |
|---|---|
| `-project` | Sökvägen till **asaproj.jspå** filen för ditt Visual Studio Code-projekt eller **[ditt projekt namn]. Asaproj** för Visual Studio-projekt. |
| `-testConfigPath` | Sökvägen till test konfigurations filen. Om den inte anges kommer filen att sökas i **\test** under den aktuella katalogen i **asaproj.js** filen, med standard fil namnet **testConfig.jspå**. En ny fil kommer att skapas om den inte existerade. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

Om test konfigurations filen är tom skrivs följande innehåll till filen. Annars läggs ett testfall till i matrisen med **TestCases**. Nödvändiga konfigurationer fylls i automatiskt enligt konfigurationsfilerna för indatafilerna, om de finns. Annars konfigureras standardvärdena. **Sökvägen** till varje indata och förväntade utdata måste anges innan testet körs. Du kan ändra konfigurationen manuellt.

Om du vill att test valideringen ska ignorera en viss utmatning anger du det **obligatoriska** fältet för den förväntade utmatningen till **falskt**.

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": "Required",
          "Required": true
        }
      ]
    }
  ]
}
```

### <a name="run-a-unit-test"></a>Kör ett enhets test

Du kan använda följande kommando för att köra flera test fall för ditt projekt. En sammanfattning av test resultaten genereras i mappen utdata. Processen avslutas med kod **0** för alla tester som har överförts. **-1** för undantags fel. **-2** för testerna misslyckades.

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parameter | Beskrivning |
|---|---|
| `-project` | Sökvägen till **asaproj.jspå** filen för ditt Visual Studio Code-projekt eller **[ditt projekt namn]. Asaproj** för Visual Studio-projekt. |
| `-testConfigPath` | Sökvägen till test konfigurations filen. Om den inte anges kommer filen att sökas i **\test** under den aktuella katalogen i **asaproj.js** filen, med standard fil namnet **testConfig.jspå**.
| `-outputPath` | Sökvägen till mappen test resultat utdata. Om den inte anges placeras resultatet av utdata i den aktuella katalogen. |
| `-customCodeZipFilePath` | Sökvägen till zip-filen för anpassad kod, till exempel UDF eller deserialiserare, om de används. |

När alla tester är klara genereras en sammanfattning av test resultaten i JSON-format i mappen utdata. Sammanfattnings filen heter **testResultSummary.jspå**.

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>Distribuera till Azure

Du kan använda Azure Resource Manager mall och parameter-filer som genererats från build för att [distribuera jobbet till Azure](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template).

## <a name="next-steps"></a>Nästa steg

* [Kontinuerlig integrering och kontinuerlig distribution för Azure Stream Analytics](cicd-overview.md)
* [Konfigurera CI/CD-pipeline för Stream Analytics jobb med hjälp av Azure-pipeline](set-up-cicd-pipeline.md)