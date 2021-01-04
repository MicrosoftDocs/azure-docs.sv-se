---
title: Felsöka miljö avbildningar
titleSuffix: Azure Machine Learning
description: Lär dig hur du felsöker problem med miljö avbildnings versioner och paket installationer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733398"
---
# <a name="troubleshoot-environment-image-builds"></a>Felsöka versioner av miljö avbildningar
Lär dig hur du felsöker problem med Docker-miljö avbildnings avbildningar och paket installationer.

## <a name="prerequisites"></a>Förutsättningar

* En **Azure-prenumeration**. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
* För att felsöka lokalt måste du ha en fungerande Docker-installation på det lokala systemet.

## <a name="docker-image-build-failures"></a>Versioner av Docker-avbildnings version
 
För majoriteten av avbildnings versions fel kan du hitta rotor saken i avbildningens versions logg.
Du kan hitta avbildnings versions loggen från Azure Machine Learning portal (20 \_ avbildnings \_ version \_log.txt) eller från dina ACR-aktiviteter kör loggar
 
I de flesta fall är det enklare att återskapa fel lokalt. Kontrol lera typen av fel och prova något av följande `setuptools` :

- Installera Conda-beroende lokalt `conda install suspicious-dependency==X.Y.Z`
- Installera pip-beroende lokalt `pip install suspicious-dependency==X.Y.Z`
- Försök att materialisera hela miljön `conda create -f conda-specification.yml`

> [!IMPORTANT]
> Kontrol lera att plattformen och tolkaren i din lokala beräkning matchar de som finns på fjärrdatorn. 

### <a name="timeout"></a>Tidsgräns 
 
Timeout-problem kan uppstå för olika nätverks problem:
- Låg Internet bandbredd
- Server problem
- Stort beroende som inte kan laddas ned med angivna inställningar för Conda eller pip
 
Meddelanden som liknar nedan visar problemet:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Möjliga lösningar:
 
- Försök med en annan källa för beroendet om det är tillgängligt, till exempel speglingar, Blob Storage eller andra python-flöden.
- Uppdatera Conda eller pip. Uppdatera tids gräns inställningarna om en anpassad Docker-fil används.
- Vissa pip-versioner har kända problem. Överväg att lägga till en speciell version av pip i miljö beroenden.

### <a name="package-not-found"></a>Paketet hittades inte

Detta är det vanligaste fallet för avbildnings byggen.

- Det gick inte att hitta Conda-paketet
        ```
        ResolvePackageNotFound: 
          - not-existing-conda-package
        ```

- Det gick inte att hitta det angivna pip-paketet eller versionen
        ```
        ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
        ERROR: No matching distribution found for invalid-pip-package
        ```

- Felaktigt kapslat pip-beroende
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

Kontrol lera att paketet finns på de angivna källorna. Använd [pip-sökning](https://pip.pypa.io/en/stable/reference/pip_search/) för att verifiera pip-beroenden.

`pip search azureml-core`

Använd [Conda search](https://docs.conda.io/projects/conda/en/latest/commands/search.html)för Conda-beroenden.

`conda search conda-forge::numpy`

För fler alternativ:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Installations information

Kontrol lera att den distribution som krävs finns för den angivna plattformen och python-tolkens version.

För pip-beroenden går du till `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` för att se om den version som krävs är tillgänglig. Till exempel https://pypi.org/project/azureml-core/1.11.0/#files

Kontrol lera paket i kanal lagrings platsen för Conda-beroenden.
För kanaler som underhålls av Anaconda, Inc., se [här](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Uppdatering av pip-paket

Under installationen eller uppdateringen av ett pip-paket kan matcharen behöva uppdatera ett redan installerat paket för att uppfylla de nya kraven.
Avinstallationen kan inte utföras av olika orsaker relaterade till pip-versionen eller hur beroendet installerades.
Det vanligaste scenariot är att ett beroende som har installerats av Conda inte kunde avinstalleras av pip.
I det här scenariot bör du överväga att avinstallera beroendet med `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Installations problem

Vissa installations versioner har problem med paket matchare som kan leda till ett skapande fel.

Om en anpassad bas avbildning eller Dockerfile används, rekommenderar vi att du använder Conda version 4.5.4 eller senare.

PIP-paketet krävs för att installera pip-beroenden och om en version inte anges i miljön kommer den senaste versionen att användas.
Vi rekommenderar att du använder en känd version av pip för att undvika tillfälliga problem eller bryta ändringar som kan orsakas av den senaste versionen av verktyget.

Överväg att fästa pip-versionen i din miljö om du ser något av meddelandena nedan:

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

Dessutom kan pip-installationen fastna i en oändlig loop om det inte går att matcha konflikter i beroenden. Om du arbetar lokalt kan du nedgradera pip-versionen till < 20,3. I en Conda-miljö som skapats från en YAML-fil visas det här problemet endast om Conda-falska är en kanal med högsta prioritet. Du kan åtgärda problemet genom att uttryckligen ange pip < 20,3 (! = 20,3 eller = 20.2.4 PIN-kod till annan version) som ett Conda-beroende i Conda Specification-filen.

## <a name="service-side-failures"></a>Fel på tjänst Sidan

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>Det gick inte att hämta bilden från MCR/adressen kunde inte matchas för Container Registry.
Möjliga problem:
- Sökvägen till behållar registret kanske inte kan lösas på rätt sätt. Kontrol lera att avbildnings namnen använder dubbla snedstreck och riktningen för snedstreck på Linux vs Windows-värdar är korrekt.
- Om ACR bakom ett virtuellt nätverk använder en privat slut punkt i [en region som inte stöds](https://docs.microsoft.com/azure/private-link/private-link-overview#availability), konfigurerar du ACR bakom ett VNet med hjälp av tjänstens slut punkt (offentlig åtkomst) från portalen och försöker igen.
- När du har placerat ACR bakom ett VNet ser du till att [arm-mallen](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) körs. Detta gör att arbets ytan kan kommunicera med ACR-instansen.

### <a name="401-error-from-workspace-acr"></a>401 fel från arbets ytans ACR
Synkronisera om lagrings nycklar med [ws.sync_keys ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--)

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>Miljön fortsätter att kasta "väntar på att andra Conda-åtgärder ska slutföras..." Fels
När en avbildning skapas kontinuerligt låses Conda av SDK-klienten. Om processen kraschade eller avbröts på ett felaktigt sätt av Conda stannar den i låst tillstånd. Du löser detta genom att manuellt ta bort Lås filen. 

### <a name="custom-docker-image-not-in-registry"></a>Anpassad Docker-avbildning saknas i registret
Kontrol lera om [rätt tagg](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) används och `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` inaktiverar Conda och använder användarens installerade paket.

### <a name="common-vnet-issues"></a>Vanliga VNet-problem

1. Kontrol lera att lagrings kontot, beräknings klustret och Azure Container Registry finns i samma undernät i det virtuella nätverket.
2. När ACR ligger bakom ett VNet kan det inte användas direkt för att bygga avbildningar. Beräknings klustret måste användas för att bygga avbildningar.
3. Lagringen kan behöva placeras bakom ett VNet när:
    - Använda inferencing eller privata hjul
    - Se 403-tjänst fel (inte auktoriserad)
    - Det går inte att hämta bild information från ACR/MCR

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>Image-genereringen Miss lyckas vid försök att komma åt skyddad lagring i nätverket
- ACR-aktiviteter fungerar inte bakom VNet. Om användaren har ACR bakom det virtuella nätverket måste de använda beräknings klustret för att bygga en avbildning.
- Lagringen ska ligga bakom VNet för att kunna ta emot beroenden från den. 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>Det går inte att köra experiment när nätverks säkerhet har Aktiver ATS för lagring
När du använder standard Docker-avbildningar och aktiverar användar hanterade beroenden måste du använda MicrosoftContainerRegistry-och AzureFrontDoor. FirstParty [-tjänstetaggar](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) för att tillåten MCR och dess beroenden.

 Mer information finns i [Aktivera VNet](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) .

### <a name="creating-an-icm"></a>Skapa en ICM

När du skapar/tilldelar en ICM till Metaarkiv ska du ta med CSS-Supportens biljett så att vi kan få bättre förståelse för problemet.

## <a name="next-steps"></a>Nästa steg

- [Träna en maskin inlärnings modell för att kategorisera blommor](how-to-train-scikit-learn.md)
- [Träna en maskin inlärnings modell med en anpassad Docker-avbildning](how-to-train-with-custom-image.md)