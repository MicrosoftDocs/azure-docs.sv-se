---
title: Kom igång med Azure Service Fabric CLI
description: Lär dig hur du använder Azure Service Fabric CLI. Lär dig hur du ansluter till ett kluster och hanterar program.
author: jeffj6123
ms.topic: conceptual
ms.date: 5/19/2020
ms.author: jejarry
ms.openlocfilehash: ff322cb3633616e9004bc8fa25b048b6f1263008
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046342"
---
# <a name="azure-service-fabric-cli"></a>Azure Service Fabric CLI

Azure Service Fabric-kommandoradsgränssnittet (CLI) är ett kommandoradsverktyg för att interagera med och hantera Service Fabric-enheter. Service Fabric CLI kan användas med antingen Windows- eller Linux-kluster. Service Fabric CLI kan köras på valfri plattform där Python stöds.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att din miljö har både Python och pip installerade före installationen. Mer information finns i [snabbstartdokumentationen för pip](https://pip.pypa.io/en/latest/quickstart/) och den officiella [dokumentationen för installation av Python](https://wiki.python.org/moin/BeginnersGuide/Download).

CLI stöder python-versionerna 2,7 och 3.6 +, med python 3. x rekommenderas.

### <a name="service-fabric-target-runtime"></a>Service Fabric-mål-runtime

Service Fabric CLI är tänkt att stödja den senaste runtime-versionen av Service Fabric SDK. Använd följande tabell för att fastställa vilken version av CLI som ska installeras:

| CLI-version   | runtime-version som stöds |
|---------------|---------------------------|
| Senaste (~ = 10) | Senaste (~ = 7,1)            |
| 9.0.0         | 7.1                       |
| 8.0.0         | 6.5                       |
| 7.1.0         | 6.4                       |
| 6.0.0         | 6.3                       |
| 5.0.0         | 6,2                       |
| 4.0.0         | 6.1                       |
| 3.0.0         | 6.0                       |
| 1.1.0         | 5.6, 5.7                  |

Alternativt kan du ange en målversion av CLI för installation genom att ange suffixet `==<version>` för kommandot `pip install`. Till exempel skulle syntaxen för version 1.1.0 vara:

```shell
pip install -I sfctl==1.1.0
```

Ersätt följande `pip install`-kommando med det tidigare nämnda kommandot vid behov.

Mer information om Service Fabric CLI-versioner finns i [GitHub-dokumentationen](https://github.com/Azure/service-fabric-cli/releases).

## <a name="install-pip-python-and-the-service-fabric-cli"></a>Installera pip, Python och Service Fabric CLI

Du kan installera pip och Python på din plattform på många olika sätt. Följande är anvisningar för att snabbt komma igång med Python 3 och pip på populära operativsystem.

### <a name="windows"></a>Windows

I Windows 10, Windows Server 2016 och Windows Server 2012 R2 använder du de officiella anvisningarna för standardinstallation. Python-installationsprogrammet installerar även pip som standard.

1. Gå till den officiella [sidan för python-nedladdningar](https://www.python.org/downloads/)och ladda ned den senaste versionen av python 3. x.

2. Starta installationsprogrammet.

3. Längst ned i prompten väljer du **Lägg till python 3. x i sökvägen**.

4. Välj **Installera nu** och slutför installationen.

Nu kan du öppna ett nytt kommandofönster och hämta versionen för Python och pip.

```shell
python --version
pip --version
```

Kör sedan följande kommando för att installera Azure Service Fabric CLI (sfctl) och Visa hjälp sidan för CLI:

```shell
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>Ubuntu- och Windows-undersystem for Linux

Kör sedan följande kommandon för att installera Service Fabric CLI:

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

Sedan kan du testa installationen med:

```bash
sfctl -h
```

Om du får ett felmeddelande om att ett kommando inte hittas, t.ex:

`sfctl: command not found`

Kontrollera att `~/.local/bin` är åtkomligt från `$PATH`:

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .shellrc
```

Om installationen på Windows-undersystemet för Linux misslyckas med felaktiga mappbehörigheter kan du behöva försöka igen med förhöjda behörigheter:

```bash
sudo pip3 install sfctl
```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (stöd för förhandsversion av Service Fabric)

Kör följande kommandon för att installera Service Fabric CLI i Red Hat:

```bash
sudo yum install -y python38
sudo yum install python38-setuptools
sudo easy_install-3.4 pip
sudo pip3 install sfctl
```

För att testa installationen kan du läsa de steg som beskrivs i avsnittet **Ubuntu och Windows-undersystem för Linux**

<a name = "cli-mac"></a>
### <a name="macos"></a>MacOS

För MacOS rekommenderar vi att du använder [HomeBrew-pakethanteraren](https://brew.sh). Installera HomeBrew om det inte är installerat genom att köra följande kommando:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Installera sedan den senaste python 3. x, pip och Service Fabric CLI från terminalen genom att köra följande kommandon:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>CLI-syntax

Kommandon inleds alltid med prefixet `sfctl`. För allmän information om alla kommandon du kan använda använder du `sfctl -h`. För hjälp med ett enda kommando, använd `sfctl <command> -h`.

Kommandon följer en upprepningsbar struktur, där kommandots mål står före verbet eller åtgärden.

```shell
sfctl <object> <action>
```

I det här exemplet är `<object>` målet för `<action>`.

## <a name="select-a-cluster"></a>Välj ett kluster

Innan du kan utföra några åtgärder måste du välja ett kluster att ansluta till. Kör till exempel följande kommando för att välja och ansluta till klustret med namnet `testcluster.com`:

> [!WARNING]
> Använd inte oskyddade Service Fabric-kluster i produktionsmiljöer.

```shell
sfctl cluster select --endpoint http://testcluster.com:19080
```

Klusterslutpunkten måste föregås av `http` eller `https`. Det måste innehålla porten för HTTP-gateway. Den här porten och adressen är samma som webbadressen för Service Fabric Explorer.

För kluster som skyddas med ett certifikat kan du ange ett PEM-kodat certifikat. Certifikatet kan anges som en enda fil eller ett par med certifikat och nyckel. Om det är ett självsignerat certifikat som inte är signerat av en certifikatutfärdare kan du använda alternativet `--no-verify` för att kringgå verifiering av certifikatutfärdare.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Mer information finns i [Ansluta till ett Azure Service Fabric-kluster](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Grundläggande åtgärder

Klustrets anslutningsinformation bevaras mellan olika Service Fabric CLI-sessioner. När du har valt ett Service Fabric-kluster kan du köra alla Service Fabric-kommandon på klustret.

Om du till exempel vill se hälsotillståndet för Service Fabric-klustret kör du följande kommando:

```shell
sfctl cluster health
```

Kommandot ger följande utdata:

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>Tips och felsökning

Här följer några förslag och tips för lösning av vanliga problem.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Konvertera ett certifikat från PFX till PEM

Service Fabric CLI har stöd för certifikat på klientsidan i form av PEM-filer (.pem-filtillägg). Om du använder PFX-filer från Windows måste du konvertera dessa certifikat till PEM-format. Om du vill konvertera en PFX-fil till en PEM-fil använder du följande kommando:

```shell
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Om du ska konvertera från en PEM-fil till en PFX-fil kan du på samma sätt använda följande kommando (inga lösenord anges här):

```shell
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

Mer information finns i [OpenSSL-dokumentationen](https://www.openssl.org/docs/).

### <a name="connection-problems"></a>Anslutningsproblem

Vissa åtgärder kan generera följande meddelande:

`Failed to establish a new connection`

Kontrollera att den angivna kluster-slutpunkten är tillgänglig och lyssnar. Kontrollera också att gränssnittet för Service Fabric Explorer kan nås på denna värd och port. Använd `sfctl cluster select` till att uppdatera slutpunkten.

### <a name="detailed-logs"></a>Detaljerade loggar

Detaljerade loggar är ofta användbara när du felsöker eller rapporterar problem. `--debug`Flaggan ökar utförligheten för utdata.

### <a name="command-help-and-syntax"></a>Hjälp och syntax för kommandon

Om du vill ha hjälp med ett visst kommando eller en grupp med kommandon, använder du flaggan `-h`.

```shell
sfctl application -h
```

Här är ett annat exempel:

```shell
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>Uppdatera Service Fabric CLI 

Om du vill uppdatera Service Fabric CLI kör du följande kommandon (ersätt `pip` med `pip3` beroende på vad du valde under den ursprungliga installationen):

```shell
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera ett program med Azure Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
* [Kom igång med Service Fabric på Linux](service-fabric-get-started-linux.md)
