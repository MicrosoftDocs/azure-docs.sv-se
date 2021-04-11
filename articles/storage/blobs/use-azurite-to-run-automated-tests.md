---
title: Köra automatiserade tester med Azurite
titleSuffix: Azure Storage
description: Lär dig hur du skriver automatiserade tester mot privata slut punkter för Azure Blob Storage med hjälp av Azurite.
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: c4e8a11e0c46cb9a138a1a66060d9fdcc72c192e
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112045"
---
# <a name="run-automated-tests-by-using-azurite"></a>Köra automatiserade tester med Azurite

Lär dig hur du skriver automatiserade tester mot privata slut punkter för Azure Blob Storage med hjälp av Azurite Storage-emulatorn.

## <a name="run-tests-on-your-local-machine"></a>Köra tester på den lokala datorn

1. Installera den senaste versionen av [python](https://www.python.org/)

1. Installera [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

1. Installera och kör [Azurite](../common/storage-use-azurite.md):

   **Alternativ 1:** Använd NPM för att installera och kör sedan Azurite lokalt

   ```bash
   # Install Azurite
   npm install -g azurite
   
   # Create an Azurite directory
   mkdir c:/azurite
   
   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **Alternativ 2:** Använd Docker för att köra Azurite

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. I Azure Storage Explorer väljer **du Anslut till en lokal emulator**

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Skärm bild av Azure Storage Explorer ansluter till Azure Storage källa.":::

1. Ange ett **visnings namn** och **blobbar port** nummer för att ansluta Azurite och Använd Azure Storage Explorer för att hantera lokal blob-lagring.

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="Skärm bild av Azure Storage Explorer att ansluta till en lokal emulator.":::

1. Skapa en virtuell Python-miljö

   ```bash
   python -m venv .venv
   ```

1. Skapa en behållare och initiera miljövariabler. Använd en [PyTest](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/2.1.0/plugins.html) -fil för att generera tester. Här är ett exempel på en conftest.py-fil:

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > Värdet som visas för `AZURE_STORAGE_CONNECTION_STRING` är standardvärdet för Azurite, det är inte en privat nyckel.

1. Installera beroenden som anges i en [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt) -fil

   ```bash
   pip install -r requirements.txt
   ```

1. Köra tester:

   ```bash
   python -m pytest ./tests
   ```

När du har kört testerna kan du se filerna i Azurite Blob Storage med hjälp av Azure Storage Explorer.

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="Skärm bild av Azure Storage Explorer visar filer som har genererats av testerna.":::

## <a name="run-tests-on-azure-pipelines"></a>Köra tester på Azure-pipeliner

När du har kört tester lokalt ser du till att testerna skickas på [Azure-pipeliner](/azure/devops/pipelines). Använd en Docker Azurite-avbildning som värdbaserad agent på Azure eller Använd NPM för att installera Azurite. I följande Azure-pipeline-exempel används NPM för att installera Azurite.
  
```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'
  
- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

När du har kört testerna för Azure-pipelinen bör du se utdata som liknar detta:

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Skärm bild av Azures pipelines test resultat.":::

## <a name="next-steps"></a>Nästa steg

- [Använd Azurite-emulatorn för lokal Azure Storage utveckling](../common/storage-use-azurite.md)
- [Exempel: använda Azurite för att köra Blob Storage-tester i Azure DevOps-pipeline](https://github.com/Azure-Samples/automated-testing-with-azurite)
