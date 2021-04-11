---
title: Git-integrering för Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig hur Azure Machine Learning integreras med en lokal git-lagringsplats för att spåra lagrings plats, gren och aktuell inchecknings information som en del av en utbildnings körning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 04/08/2021
ms.openlocfilehash: 2dc50702113f591075b790878347c4ca47beec4e
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107027813"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integrering för Azure Machine Learning

[Git](https://git-scm.com/) är ett populärt versions kontroll system som gör att du kan dela och samar beta med dina projekt. 

Azure Machine Learning har fullt stöd för git-lagringsplatser för att spåra arbete – du kan klona lagrings platser direkt till fil systemet på den delade arbets ytan, använda git på din lokala arbets Station eller använda Git från en CI/CD-pipeline.

Om källfiler lagras i en lokal git-lagringsplats när ett jobb skickas till Azure Machine Learning spåras information om lagrings platsen som en del av inlärnings processen.

Eftersom Azure Machine Learning spårar information från en lokal git-lagrings platsen är den inte kopplad till någon speciell Central lagrings plats. Din lagrings plats kan klonas från GitHub, GitLab, BitBucket, Azure DevOps eller någon annan git-kompatibel tjänst.

> [!TIP]
> Använd Visual Studio Code för att interagera med git via ett grafiskt användar gränssnitt. Om du vill ansluta till en Azure Machine Learning fjärran sluten beräknings instans med hjälp av Visual Studio Code, se [ansluta till en Azure Machine Learning beräknings instans i Visual Studio Code (för hands version)](how-to-set-up-vs-code-remote.md)
>
> Mer information om funktioner för versions kontroll i Visual Studio Code finns i [använda versions kontroll i vs Code](https://code.visualstudio.com/docs/editor/versioncontrol) och [arbeta med GitHub i vs Code](https://code.visualstudio.com/docs/editor/github).

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Klona Git-databaser till arbetsytans filsystem
Azure Machine Learning tillhandahåller ett delat fil system för alla användare på arbets ytan.
Om du vill klona en git-lagringsplats till den här fil resursen rekommenderar vi att du skapar en beräknings instans & [öppnar en Terminal](how-to-access-terminal.md).
När terminalen har öppnats har du åtkomst till en fullständig git-klient och kan klona och arbeta med git via git CLI-upplevelsen.

Vi rekommenderar att du klonar lagrings platsen till din användar katalog så att andra inte kommer att göra kollisioner direkt på din arbets gren.

Du kan klona en git-lagringsplats som du kan autentisera till (GitHub, Azure databaser, BitBucket osv.)

Mer information om kloning finns i guiden om [hur du använder git CLI](https://guides.github.com/introduction/git-handbook/).

## <a name="authenticate-your-git-account-with-ssh"></a>Autentisera ditt git-konto med SSH
### <a name="generate-a-new-ssh-key"></a>Generera en ny SSH-nyckel
1) [Öppna terminalfönstret](./how-to-access-terminal.md) på fliken Azure Machine Learning antecknings bok.

2) Klistra in texten nedan och ersätt den med din e-postadress.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Detta skapar en ny SSH-nyckel med hjälp av det tillhandahållna e-postmeddelandet som en etikett.

```
> Generating public/private rsa key pair.
```

3) När du uppmanas att ange en fil där du vill spara nyckeln trycker du på RETUR. Detta godkänner standard platsen för filen.

4) Kontrol lera att standard platsen är '/Home/azureuser/.ssh ' och tryck på RETUR. Ange annars platsen '/Home/azureuser/.ssh '.

> [!TIP]
> Se till att SSH-nyckeln sparas i "/Home/azureuser/.ssh". Filen som sparas i beräknings instansen är bara tillgänglig för instansens ägare

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) Skriv en säker lösen fras i prompten. Vi rekommenderar att du lägger till en lösen fras i SSH-nyckeln för ytterligare säkerhet

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Lägg till den offentliga nyckeln till git-kontot
1) Kopiera innehållet i den offentliga nyckel filen i terminalfönstret. Om du har bytt namn på nyckeln ersätter du id_rsa. pub med fil namnet för den offentliga nyckeln.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Kopiera och klistra in i Terminal**
> * Windows: `Ctrl-Insert` för att kopiera och använda `Ctrl-Shift-v` eller `Shift-Insert` Klistra in.
> * Mac OS: `Cmd-c` för att kopiera och `Cmd-v` Klistra in.
> * FireFox/IE kanske inte stöder urklipps behörigheter korrekt.

2) Markera och kopiera nyckel utdata i Urklipp.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure-DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs)  Starta i **steg 2**.

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Starta i **steg 4**.

### <a name="clone-the-git-repository-with-ssh"></a>Klona git-lagringsplatsen med SSH

1) Kopiera SSH git-klonings-URL: en från git-lagrings platsen.

2) Klistra in URL: en i `git clone` kommandot nedan för att använda din SSH git lagrings platsen-URL. Detta ser ut ungefär så här:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

Ett svar visas som:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH kan visa serverns SSH-finger avtryck och be dig verifiera den. Kontrol lera att finger avtrycket som visas matchar ett av finger avtryck på sidan offentliga SSH-nycklar.

SSH visar det här finger avtrycket när det ansluter till en okänd värd för att skydda dig från [man-in-the-middle-attacker](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10)). När du godkänner värdens finger avtryck kommer SSH inte att fråga dig igen om inte finger avtrycket ändras.

3) När du tillfrågas om du vill fortsätta ansluta skriver du `yes` . Git kommer att klona lagrings platsen och konfigurera den ursprungliga fjärrdatorn för att ansluta med SSH för framtida git-kommandon.

## <a name="track-code-that-comes-from-git-repositories"></a>Spåra kod som kommer från git-databaser

När du skickar en utbildning som körs från python SDK eller Machine Learning CLI överförs filerna som behövs för att träna modellen till din arbets yta. Om `git` kommandot är tillgängligt i utvecklings miljön använder överförings processen för att kontrol lera om filerna är lagrade i en git-lagringsplats. I så fall, överförs information från git-lagringsplatsen också som en del av övnings körningen. Den här informationen lagras i följande egenskaper för övnings körningen:

| Egenskap | Git-kommando som används för att hämta värdet | Beskrivning |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Den URI som din lagrings plats har kopierats från. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Den URI som din lagrings plats har kopierats från. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Den aktiva grenen när körningen skickades. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Den aktiva grenen när körningen skickades. |
| `azureml.git.commit` | `git rev-parse HEAD` | Bekräfta hashen för den kod som skickades för körningen. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Bekräfta hashen för den kod som skickades för körningen. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, om grenen/genomförandet är smutsig; Annars, `false` . |

Den här informationen skickas för körningar som använder en uppskattnings-, maskin inlärnings-pipeline eller skript körning.

Om dina utbildningsbaserade filer inte finns i en git-lagringsplats i utvecklings miljön, eller om `git` kommandot inte är tillgängligt, spåras ingen git-relaterad information.

> [!TIP]
> Om du vill kontrol lera om git-kommandot är tillgängligt i utvecklings miljön öppnar du en Shell-session, kommando tolk, PowerShell eller andra kommando rads gränssnitt och skriver följande kommando:
>
> ```
> git --version
> ```
>
> Om det är installerat och i sökvägen får du ett svar som liknar `git version 2.4.1` . Mer information om hur du installerar git i utvecklings miljön finns på [git-webbplatsen](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Visa den loggade informationen

Git-informationen lagras i egenskaperna för en utbildnings körning. Du kan visa den här informationen med hjälp av Azure Portal, python SDK och CLI. 

### <a name="azure-portal"></a>Azure Portal

1. Från [Studio-portalen](https://ml.azure.com)väljer du din arbets yta.
1. Välj __experiment__ och välj sedan ett av experimenten.
1. Välj en av körningarna från kolumnen __Kör nummer__ .
1. Välj __utdata + loggar__ och expandera sedan __loggarna__ och __azureml__ -posterna. Välj den länk som börjar med __### \_ Azure__.

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

När du har skickat in en utbildnings körning returneras ett [körnings](/python/api/azureml-core/azureml.core.run%28class%29) objekt. `properties`Attribut för det här objektet innehåller den loggade git-informationen. Följande kod hämtar exempelvis commit hash:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

`az ml run`CLI-kommandot kan användas för att hämta egenskaperna från en körning. Följande kommando returnerar till exempel egenskaperna för den senaste körningen i experimentet med namnet `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Mer information finns i referens dokumentationen för [AZ ml-körning](/cli/azure/ext/azure-cli-ml/ml/run) .

## <a name="next-steps"></a>Nästa steg

* [Använda beräknings mål för modell träning](how-to-set-up-training-targets.md)