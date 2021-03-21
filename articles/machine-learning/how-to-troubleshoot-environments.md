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
ms.openlocfilehash: ec0c7d64f2145cdaf594cb903c072984f4d376a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519137"
---
# <a name="troubleshoot-environment-image-builds"></a>Felsöka versioner av miljö avbildningar

Lär dig hur du felsöker problem med Docker-miljö avbildnings avbildningar och paket installationer.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install).
* [Azure CLI](/cli/azure/install-azure-cli).
* [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
* För att felsöka lokalt måste du ha en fungerande Docker-installation på det lokala systemet.

## <a name="docker-image-build-failures"></a>Versioner av Docker-avbildnings version
 
För de flesta bildbuild-fel hittar du rotor saken i avbildnings versions loggen.
Hitta avbildnings build-loggen från Azure Machine Learning portal (20 \_ avbildnings \_ version \_log.txt) eller från dina Azure Container Registry uppgifts körnings loggar.
 
Det är vanligt vis enklare att återskapa fel lokalt. Kontrol lera typen av fel och prova något av följande `setuptools` :

- Installera ett Conda-beroende lokalt: `conda install suspicious-dependency==X.Y.Z` .
- Installera ett pip-beroende lokalt: `pip install suspicious-dependency==X.Y.Z` .
- Försök att materialisera hela miljön: `conda create -f conda-specification.yml` .

> [!IMPORTANT]
> Kontrol lera att plattformen och tolken på ditt lokala beräknings kluster matchar dem i det fjärranslutna beräknings klustret. 

### <a name="timeout"></a>Tidsgräns 
 
Följande nätverks problem kan orsaka timeout-fel:

- Låg Internet bandbredd
- Server problem
- Stora beroenden som inte går att ladda ned med angivna inställningar för Conda eller pip
 
Meddelanden som liknar följande exempel visar problemet:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Om du får ett fel meddelande kan du prova någon av följande möjliga lösningar:
 
- Försök med en annan källa, till exempel speglingar, Azure Blob Storage eller andra python-flöden för beroendet.
- Uppdatera Conda eller pip. Om du använder en anpassad Docker-fil uppdaterar du inställningarna för timeout.
- Vissa pip-versioner har kända problem. Överväg att lägga till en speciell version av pip i miljö beroenden.

### <a name="package-not-found"></a>Paketet hittades inte

Följande fel är de vanligaste för avbildnings Bygg fel:

- Det gick inte att hitta Conda-paketet:

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- Det gick inte att hitta det angivna pip-paketet eller versionen:

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- Felaktigt kapslat pip-beroende:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

Kontrol lera att paketet finns på de angivna källorna. Använd [pip-sökning](https://pip.pypa.io/en/stable/reference/pip_search/) för att verifiera pip-beroenden:

- `pip search azureml-core`

Använd [Conda search](https://docs.conda.io/projects/conda/en/latest/commands/search.html)för Conda-beroenden:

- `conda search conda-forge::numpy`

Om du vill ha fler alternativ kan du försöka med följande:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Installations information

Kontrol lera att den distribution som krävs finns för den angivna plattformen och python-tolkens version.

För pip-beroenden går du till `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` för att se om den version som krävs är tillgänglig. Gå till https://pypi.org/project/azureml-core/1.11.0/#files för att se ett exempel.

Kontrol lera paketet i kanal lagrings platsen för Conda-beroenden.
För kanaler som underhålls av Anaconda, Inc., se [sidan Anaconda-paket](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Uppdatering av pip-paket

Under en installation eller uppdatering av ett pip-paket kan matcharen behöva uppdatera ett redan installerat paket för att uppfylla de nya kraven.
Avinstallationen kan inte utföras av olika orsaker som är relaterade till pip-versionen eller hur beroendet installerades.
Det vanligaste scenariot är att ett beroende som har installerats av Conda inte kunde avinstalleras av pip.
I det här scenariot avinstallerar du beroendet med hjälp av `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Installations problem

Vissa installations versioner har problem med paket matchare som kan leda till ett skapande fel.

Om du använder en anpassad bas avbildning eller Dockerfile rekommenderar vi att du använder Conda version 4.5.4 eller senare.

Ett pip-paket krävs för att installera pip-beroenden. Om en version inte anges i miljön används den senaste versionen.
Vi rekommenderar att du använder en känd version av pip för att undvika tillfälliga problem eller bryta ändringar som den senaste versionen av verktyget kan orsaka.

Överväg att fästa pip-versionen i din miljö om du ser följande meddelande:

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

PIP-under process fel:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

PIP-installationen kan fastna i en oändlig loop om det inte går att matcha konflikter i beroenden. Om du arbetar lokalt kan du nedgradera pip-versionen till < 20,3. I en Conda-miljö som skapats från en YAML-fil visas det här problemet endast om Conda-falska är den högsta prioritets kanalen. Du kan åtgärda problemet genom att uttryckligen ange pip < 20,3 (! = 20,3 eller = 20.2.4 PIN-kod till annan version) som ett Conda-beroende i Conda Specification-filen.

## <a name="service-side-failures"></a>Fel på Server Sidan

Se följande scenarier för att felsöka möjliga fel på Server sidan.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>Det går inte att hämta en avbildning från ett behållar register, eller så gick det inte att matcha adressen för ett behållar register

Möjliga problem:
- Sökvägen till behållar registret kanske inte kan lösas på rätt sätt. Kontrol lera att avbildnings namnen använder dubbla snedstreck och riktningen för snedstreck i Linux-och Windows-värdar är korrekt.
- Om ett behållar register bakom ett virtuellt nätverk använder en privat slut punkt i [en region som inte stöds](../private-link/private-link-overview.md#availability), konfigurerar du behållar registret med hjälp av tjänstens slut punkt (offentlig åtkomst) från portalen och försöker igen.
- När du har beställt behållar registret bakom ett virtuellt nätverk kör du [Azure Resource Manager-mallen](./how-to-network-security-overview.md) så att arbets ytan kan kommunicera med behållar register instansen.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>Du får ett 401-fel från arbets ytans behållares register

Omsynkronisera lagrings nycklar med hjälp av [ws.sync_keys ()](/python/api/azureml-core/azureml.core.workspace.workspace#sync-keys--).

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>Miljön behåller en "väntar på att andra Conda-åtgärder ska slutföras..." Fels

När en avbildning skapas kontinuerligt låses Conda av SDK-klienten. Om processen kraschade eller avbröts felaktigt av användaren, stannar Conda i låst tillstånd. Lös problemet genom att manuellt ta bort Lås filen. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>Den anpassade Docker-avbildningen finns inte i registret

Kontrol lera om [rätt tagg](./how-to-use-environments.md#create-an-environment) används och `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` inaktiverar Conda och använder användarens installerade paket.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>Du får ett av följande vanliga problem med virtuella nätverk

- Kontrol lera att lagrings kontot, beräknings klustret och behållar registret finns i samma undernät i det virtuella nätverket.
- När ditt behållar register ligger bakom ett virtuellt nätverk kan det inte användas direkt för att bygga avbildningar. Du måste använda beräknings klustret för att bygga avbildningar.
- Lagringen kan behöva placeras bakom ett virtuellt nätverk om du:
    - Använd inferencing eller privat hjul.
    - Se 403 (inte auktoriserad) tjänst fel.
    - Det går inte att hämta bild information från Azure Container Registry.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>Det går inte att bygga avbildningen när du försöker komma åt nätverket skyddad lagring

- Azure Container Registry aktiviteter fungerar inte bakom ett virtuellt nätverk. Om användaren har sitt behållar register bakom ett virtuellt nätverk måste de använda beräknings klustret för att bygga en avbildning.
- Lagringen bör ligga bakom ett virtuellt nätverk för att hämta beroenden från det.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>Du kan inte köra experiment när nätverks säkerheten är aktive rad i lagring

Om du använder standard Docker-avbildningar och aktiverar användar hanterade beroenden använder du MicrosoftContainerRegistry-och AzureFrontDoor. FirstParty [-tjänstetaggarna](./how-to-network-security-overview.md) för att tillåten Azure Container Registry och dess beroenden.

 Mer information finns i [Aktivera virtuella nätverk](./how-to-network-security-overview.md).

### <a name="you-need-to-create-an-icm"></a>Du måste skapa en ICM

När du skapar/tilldelar en ICM till Metaarkiv ska du ta med CSS-Supportens biljett så att vi kan få bättre förståelse för problemet.

## <a name="next-steps"></a>Nästa steg

- [Träna en maskin inlärnings modell för att kategorisera blommor](how-to-train-scikit-learn.md)
- [Träna en maskin inlärnings modell med hjälp av en anpassad Docker-avbildning](how-to-train-with-custom-image.md)
