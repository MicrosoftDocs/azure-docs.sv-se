---
title: Git-integrering för Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig Azure Machine Learning integreras med en lokal Git-lagringsplats för att spåra lagringsplats, gren och aktuell genomförandeinformation som en del av en träningskörning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 04/08/2021
ms.openlocfilehash: 60dca43f95b190791c8fb593042ed612340a3af5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874557"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integrering för Azure Machine Learning

[Git](https://git-scm.com/) är ett populärt system för versionskontroll som gör att du kan dela och samarbeta i dina projekt. 

Azure Machine Learning har fullständigt stöd för Git-lagringsplatsen för spårning av arbete – du kan klona lagringsplatsen direkt till filsystemet för den delade arbetsytan, använda Git på din lokala arbetsstation eller använda Git från en CI/CD-pipeline.

När du skickar ett jobb Azure Machine Learning om källfiler lagras på en lokal Git-lagringsplats spåras information om lagringsplatsen som en del av träningsprocessen.

Eftersom Azure Machine Learning spårar information från en lokal git-lagringsplats är den inte kopplad till någon specifik central lagringsplats. Din lagringsplats kan klonas från GitHub, GitLab, Bitbucket, Azure DevOps eller någon annan git-kompatibel tjänst.

> [!TIP]
> Använd Visual Studio Code för att interagera med Git via ett grafiskt användargränssnitt. Information om hur du ansluter Azure Machine Learning en fjärrbearbetningsinstans med Visual Studio Code finns i Ansluta till en [Azure Machine Learning-beräkningsinstans i Visual Studio Code (förhandsversion)](how-to-set-up-vs-code-remote.md)
>
> Mer information om hur Visual Studio versionskontrollfunktioner i Code finns i [Använda versionskontroll](https://code.visualstudio.com/docs/editor/versioncontrol) i VS Code och [Arbeta med GitHub i VS Code.](https://code.visualstudio.com/docs/editor/github)

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Klona Git-databaser till arbetsytans filsystem
Azure Machine Learning ett delat filsystem för alla användare på arbetsytan.
Om du vill klona en Git-lagringsplats till den här filresursen rekommenderar vi att du skapar en beräkningsinstans & [öppna en terminal](how-to-access-terminal.md).
När terminalen har öppnats har du åtkomst till en fullständig Git-klient och kan klona och arbeta med Git via Git CLI-upplevelsen.

Vi rekommenderar att du klonar lagringsplatsen till användarkatalogen så att andra inte gör kollisioner direkt på din arbetsgren.

Du kan klona valfri Git-lagringsplats som du kan autentisera till (GitHub, Azure Repos, BitBucket osv.)

Mer information om kloning finns i guiden om [hur du använder Git CLI.](https://guides.github.com/introduction/git-handbook/)

## <a name="authenticate-your-git-account-with-ssh"></a>Autentisera ditt Git-konto med SSH
### <a name="generate-a-new-ssh-key"></a>Generera en ny SSH-nyckel
1) [Öppna terminalfönstret på](./how-to-access-terminal.md) fliken Azure Machine Learning Notebook.

2) Klistra in texten nedan och ersätt med din e-postadress.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Detta skapar en ny ssh-nyckel med hjälp av det angivna e-postmeddelandet som en etikett.

```
> Generating public/private rsa key pair.
```

3) När du uppmanas att "Ange en fil där du vill spara tangenten" trycker du på Retur. Detta godkänner standardfilens plats.

4) Kontrollera att standardplatsen är "/home/azureuser/.ssh" och tryck på Retur. Annars anger du platsen "/home/azureuser/.ssh".

> [!TIP]
> Kontrollera att SSH-nyckeln har sparats i "/home/azureuser/.ssh". Den här filen sparas på beräkningsinstansen och kan bara nås av ägaren till beräkningsinstansen

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) Skriv en säker lösenfras i prompten. Vi rekommenderar att du lägger till en lösenfras i SSH-nyckeln för ökad säkerhet

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Lägga till den offentliga nyckeln i Git-kontot
1) Kopiera innehållet i filen med den offentliga nyckeln i terminalfönstret. Om du har bytt namn på nyckeln ersätter du id_rsa.pub med filnamnet för den offentliga nyckeln.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Kopiera och klistra in i terminalen**
> * Windows: `Ctrl-Insert` för att kopiera och använda eller för att klistra `Ctrl-Shift-v` `Shift-Insert` in.
> * Mac OS: `Cmd-c` för att kopiera och klistra `Cmd-v` in.
> * FireFox/IE kanske inte stöder behörigheter för Urklipp korrekt.

2) Markera och kopiera nyckelutdata i Urklipp.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs)  Börja i **steg 2.**

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Börja i **steg 4.**

### <a name="clone-the-git-repository-with-ssh"></a>Klona Git-lagringsplatsen med SSH

1) Kopiera URL:en för SSH Git-klonen från Git-lagringsplatsen.

2) Klistra in URL:en i kommandot `git clone` nedan för att använda SSH Git-lagringsplatsens URL. Det ser ut ungefär så här:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

Du ser ett svar som:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH kan visa serverns SSH-fingeravtryck och be dig att verifiera det. Du bör kontrollera att det visade fingeravtrycket matchar ett av fingeravtrycken på sidan för offentliga SSH-nycklar.

SSH visar det här fingeravtrycket när det ansluter till en okänd värd för att skydda dig mot [man-in-the-middle-attacker](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10)). När du har accepterat värdens fingeravtryck kommer SSH inte att fråga dig igen om inte fingeravtrycket ändras.

3) När du tillfrågas om du vill fortsätta ansluta skriver du `yes` . Git kommer att klona lagringsplatsen och konfigurera den ursprungliga fjärrplatsen för att ansluta till SSH för framtida Git-kommandon.

## <a name="track-code-that-comes-from-git-repositories"></a>Spåra kod som kommer från Git-lagringsplatsen

När du skickar en träningskörning från Python SDK eller Machine Learning CLI laddas de filer som behövs för att träna modellen upp till din arbetsyta. Om kommandot är tillgängligt i din utvecklingsmiljö använder uppladdningsprocessen det för att kontrollera om filerna lagras på `git` en git-lagringsplats. I så fall laddas även information från git-lagringsplatsen upp som en del av träningskörningen. Den här informationen lagras i följande egenskaper för träningskörningen:

| Egenskap | Git-kommando som används för att hämta värdet | Description |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Den URI som lagringsplatsen klonade från. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Den URI som lagringsplatsen klonade från. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Den aktiva grenen när körningen skickades. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Den aktiva grenen när körningen skickades. |
| `azureml.git.commit` | `git rev-parse HEAD` | Commit-hashen för koden som skickades för körningen. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Commit-hashen för koden som skickades för körningen. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, om grenen/commit är dirty; annars `false` . |

Den här informationen skickas för körningar som använder en beräknings-, maskininlärningspipeline eller skriptkörning.

Om dina träningsfiler inte finns på en git-lagringsplats i utvecklingsmiljön, eller om kommandot inte är `git` tillgängligt, spåras ingen git-relaterad information.

> [!TIP]
> Om du vill kontrollera om git-kommandot är tillgängligt i utvecklingsmiljön öppnar du en shell-session, kommandotolk, PowerShell eller något annat kommandoradsgränssnitt och skriver följande kommando:
>
> ```
> git --version
> ```
>
> Om du har installerat och i sökvägen får du ett svar som liknar `git version 2.4.1` . Mer information om hur du installerar git i utvecklingsmiljön finns på [Git-webbplatsen](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Visa den loggade informationen

Git-informationen lagras i egenskaperna för en träningskörning. Du kan visa den här informationen med hjälp Azure Portal, Python SDK och CLI. 

### <a name="azure-portal"></a>Azure Portal

1. Välj din [arbetsyta](https://ml.azure.com)i Studio-portalen.
1. Välj __Experiment__ och välj sedan ett av dina experiment.
1. Välj en av körningarna i __kolumnen RUN NUMBER (KÖRNINGSNUMMER).__
1. Välj __Utdata + loggar__ och expandera sedan __loggarna__ och __azureml-posterna.__ Välj länken som börjar med __### \_ azure__.

Den loggade informationen innehåller text som liknar följande JSON:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

När du har skickat en träningskörning [returneras](/python/api/azureml-core/azureml.core.run%28class%29) ett Run-objekt. Attributet `properties` för det här objektet innehåller den loggade git-informationen. Följande kod hämtar till exempel commit-hashen:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

`az ml run`CLI-kommandot kan användas för att hämta egenskaperna från en körning. Följande kommando returnerar till exempel egenskaperna för den senaste körningen i experimentet med namnet `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Mer information finns i [referensdokumentationen för az ml](/cli/azure/ml/run) run.

## <a name="next-steps"></a>Nästa steg

* [Använda beräkningsmål för modellträning](how-to-set-up-training-targets.md)