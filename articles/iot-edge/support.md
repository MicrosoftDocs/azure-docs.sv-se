---
title: Operativsystem som stöds, containermotorer – Azure IoT Edge
description: Lär dig vilka operativsystem som kan köra Azure IoT Edge daemon och runtime samt containermotorer som stöds för dina produktionsenheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67532fce2cac0ec9d05b4caa069e63014b813bd8
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576354"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge system som stöds

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Den här artikeln innehåller information om vilka system och komponenter som stöds av IoT Edge, oavsett om de är officiellt eller i förhandsversion.

## <a name="get-support"></a>Få support

Om du får problem när du Azure IoT Edge tjänsten finns det flera sätt att söka support. Prova någon av följande kanaler för support:

**Rapportera buggar** – merparten av utvecklingen som går till Azure IoT Edge produkt sker i IoT Edge med öppen källkod. Buggar kan rapporteras på [problemsidan](https://github.com/azure/iotedge/issues) i projektet. Buggar som rör Azure IoT Edge för Linux på Windows kan rapporteras på [sidan med problem med iotedge-eflow.](https://github.com/azure/iotedge-eflow/issues) Korrigeringar tar sig snabbt från projekt till produktuppdateringar.

**Microsofts kundsupport –** Användare som har en [supportplan](https://azure.microsoft.com/support/plans/) kan kontakta Microsofts kundsupport genom att skapa en supportbiljett direkt från [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funktionsbegäranden** – Azure IoT Edge spårar funktionsförfrågningar via produktens [User Voice-sida](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Containermotorer

Azure IoT Edge-moduler implementeras som containrar, så IoT Edge behöver en containermotor för att starta dem. Microsoft tillhandahåller en containermotor, moby-engine, som uppfyller det här kravet. Den här containermotorn baseras på Moby-projektet med öppen källkod. Docker CE och Docker EE är andra populära containermotorer. De baseras också på Moby-projektet med öppen källkod och är kompatibla med Azure IoT Edge. Microsoft tillhandahåller bästa möjliga stöd för system som använder dessa containermotorer; Microsoft kan dock inte leverera korrigeringar för problem i dem. Därför rekommenderar Microsoft att du använder moby-engine i produktionssystem.

<br>
<center>

![Moby-motorn som containerkörning](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operativsystem

Azure IoT Edge körs på de flesta operativsystem som kan köra containrar. Alla dessa system stöds dock inte lika mycket. Operativsystem är grupperade i nivåer som representerar den supportnivå som användarna kan förvänta sig.

* Nivå 1-system stöds. För nivå 1-system gör Microsoft följande:
  * har det här operativsystemet i automatiserade tester
  * tillhandahåller installationspaket för dem
* Nivå 2-system är kompatibla Azure IoT Edge och kan användas relativt enkelt. För system på nivå 2:
  * Microsoft har gjort informell testning på plattformarna eller känner till en partner som Azure IoT Edge på plattformen
  * Installationspaket för andra plattformar kan fungera på dessa plattformar

Värdoperativsystemets familj måste alltid matcha familjen för gästoperativsystemet som används i en moduls container.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Med andra ord kan du bara använda Linux-containrar på Linux- och Windows-containrar i Windows. När du använder Windows-containrar stöds endast process isolerade containrar, inte Hyper-V-isolerade containrar.  

IoT Edge för Linux i Windows använder IoT Edge på en virtuell Linux-dator som körs på en Windows-värd. På så sätt kan du köra Linux-moduler på en Windows-enhet.
:::moniker-end
<!-- end 1.1 -->

### <a name="tier-1"></a>Nivå 1

De system som anges i följande tabeller stöds av Microsoft, antingen allmänt tillgängliga eller i allmänt tillgänglig förhandsversion, och testas med varje ny version.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Azure IoT Edge stöder moduler som skapats som antingen Linux- eller Windows-containrar. Linux-containrar kan distribueras till Linux-enheter eller distribueras till Windows-enheter med IoT Edge för Linux på Windows. Windows-containrar kan bara distribueras till Windows-enheter.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Azure IoT Edge version 1.2 stöder endast moduler som skapats som Linux-containrar.

Det finns för närvarande inget sätt att köra IoT Edge version 1.2 på Windows-enheter. [IoT Edge för Linux på Windows](iot-edge-for-linux-on-windows.md) är det rekommenderade sättet att IoT Edge på Windows-enheter, men för närvarande körs bara IoT Edge 1.1. Mer information finns i artikeln IoT Edge [1.1.](?view=iotedge-2018-06&preserve-view=true)

:::moniker-end
<!-- end 1.2 -->

#### <a name="linux-containers"></a>Linux-containrar

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Moduler som skapats som Linux-containrar kan distribueras till Linux- eller Windows-enheter. För Linux-IoT Edge installeras körningskörningen direkt på värdenheten. För Windows-enheter körs en virtuell Linux-dator som är förbyggd IoT Edge körningen på värdenheten.

[IoT Edge för Linux på Windows är](iot-edge-for-linux-on-windows.md) för närvarande i offentlig förhandsversion, men är det rekommenderade sättet att IoT Edge på Windows-enheter.

| Operativsystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Offentlig förhandsversion |
| Windows 10 Pro | Offentlig förhandsversion |  |  |
| Windows 10 Enterprise | Offentlig förhandsversion |  |  |
| Windows 10 IoT Enterprise | Offentlig förhandsversion |  |  |
| Windows Server 2019 | Offentlig förhandsversion |  |  |

Alla Windows-operativsystem måste vara version 1809 (version 17763) eller senare.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

| Operativsystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Offentlig förhandsversion |

:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>Stödet för Ubuntu Server 16.04 upphörde med lanseringen IoT Edge version 1.1.

#### <a name="windows-containers"></a>Windows-containrar

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
>[!IMPORTANT]
>IoT Edge 1.1 LTS är den senaste versionens kanal som stöder Windows-containrar. Från och med version 1.2 stöds inte Windows-containrar. Överväg att använda eller flytta [IoT Edge för Linux i Windows för](iot-edge-for-linux-on-windows.md) att IoT Edge på Windows-enheter.

Moduler som skapats som Windows-containrar kan endast distribueras till Windows-enheter.

| Operativsystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/tutorial-c-module/green-check.png) |  |  |

Alla Windows-operativsystem måste vara version 1809 (version 17763). Den specifika versionen av Windows krävs för IoT Edge på Windows eftersom versionen av Windows-containrarna måste exakt matcha windows-värdenhetens version. Windows-containrar använder för närvarande endast version 17763.

>[!NOTE]
>Windows 10 IoT Core upphörde med lanseringen av IoT Edge version 1.1.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge 1.1 LTS är den senaste versionen av kanalen som stöder Windows-containrar. Från och med version 1.2 stöds inte Windows-containrar.

Information om operativsystem som stöds för Windows-containrar finns i [IoT Edge 1.1-versionen](?view=iotedge-2018-06&preserve-view=true) av den här artikeln.

:::moniker-end
<!-- end 1.2 -->

### <a name="tier-2"></a>Nivå 2

De system som anges i följande tabell anses vara kompatibla med Azure IoT Edge, men testas eller underhålls inte aktivt av Microsoft.

| Operativsystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS- och Raspberry Pi-operativsystemet |  | ![Raspberry Pi OSOperativsystem + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OSOperativsystem + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Installationsstegen för Ubuntu Server 18.04 i Installera eller avinstallera [Azure IoT Edge för Linux](how-to-install-iot-edge.md) bör fungera utan ändringar i Ubuntu 20.04.

## <a name="releases"></a>Versioner

IoT Edge och information finns på sidan [med azure-iotedge-versioner.](https://github.com/Azure/azure-iotedge/releases) Det här avsnittet innehåller information från viktig information som hjälper dig att visualisera komponenterna i varje version enklare.

I följande tabell visas de komponenter som ingår i varje version från och med 1.2.0. Komponenterna i den här tabellen kan installeras eller uppdateras individuellt och är bakåtkompatibla med äldre versioner.

| Frisläpp | aziot-edge | edgeHub<br>edgeAgent | aziot-identity-service |
| ------- | ---------- | -------------------- | ---------------------- |
| **1.2** | 1.2.0      | 1.2.0                | 1.2.0                  |

I följande tabell visas de komponenter som ingår i varje version upp till 1.1 LTS-versionen. Komponenterna i den här tabellen kan installeras eller uppdateras individuellt och är bakåtkompatibla med äldre versioner.

| Frisläpp | iotedge | edgeHub<br>edgeAgent | libiothsm | Moby |
|--|--|--|--|--|
| **1.1 LTS**<sup>1</sup> | 1.1.0<br>1.1.1<br><br> | 1.1.0<br>1.1.1<br>1.1.2 | 1.1.0<br>1.1.1<br><br> |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4 (ARMv7hl, CentOS)<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> IoT Edge 1.1 är den första kanalen för långsiktig support (LTS). Den här versionen introducerade inga nya funktioner, men kommer att ta emot säkerhetsuppdateringar och korrigeringar av regressioner. IoT Edge 1.1 LTS använder .NET Core 3.1 och kommer att stödjas fram till den 3 december 2022 för att matcha [.NET Core- och .NET 5-versionens](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)livscykel.

>[!IMPORTANT]
>Med lanseringen av en långsiktig supportkanal rekommenderar vi att alla nuvarande kunder som kör 1.0.x uppgraderar sina enheter till 1.1.x för att få löpande support.

IoT Edge använder MICROSOFT.Azure.Devices.Client SDK. Mer information finns i [GitHub-lagringsplatsen Azure IoT C# SDK](https://github.com/Azure/azure-iot-sdk-csharp) eller Referensinnehåll för [Azure SDK för .NET.](/dotnet/api/overview/azure/iot/client) I följande lista visas den version av klient-SDK som varje version testas mot:

| IoT Edge-version | Microsoft.Azure.Devices.Client SDK-version |
|------------------|--------------------------------------------|
| 1.2.0            | 1.33.4-NestedEdge
| 1.1 (LTS)        | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>Virtual Machines

Azure IoT Edge kan köras på virtuella datorer. Att använda en virtuell dator IoT Edge en enhet är vanligt när kunder vill utöka befintlig infrastruktur med Edge Intelligence. Familjen för den virtuella värddatorns operativsystem måste matcha familjen för gästoperativsystemet som används i en moduls container. Det här kravet är detsamma som när Azure IoT Edge körs direkt på en enhet. Azure IoT Edge är oberoende av den underliggande virtualiseringstekniken och fungerar i virtuella datorer som drivs av plattformar som Hyper-V och vSphere.

<br>

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

<center>

![Azure IoT Edge på en virtuell dator](./media/support/edge-on-vm-with-windows.png)

</center>

::: moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

<center>

![Azure IoT Edge på en virtuell dator](./media/support/edge-on-vm.png)

</center>

:::moniker-end

## <a name="minimum-system-requirements"></a>Minsta systemkrav

Azure IoT Edge fungerar bra på enheter som är så små som Raspberry Pi3-maskinvara i serverklass. Hur du väljer rätt maskinvara för ditt scenario beror på vilka arbetsbelastningar du vill köra. Det kan vara komplicerat att fatta det slutliga enhetsbeslutet. Du kan dock enkelt börja skapa prototyper för en lösning på traditionella bärbara eller stationära datorer.

Erfarenhet av prototyper hjälper dig att vägleda ditt slutliga enhetsval. Frågor som du bör överväga är:

* Hur många moduler finns det i din arbetsbelastning?
* Hur många lager delar dina modulers containrar?
* På vilket språk skrivs dina moduler?
* Hur mycket data kommer modulerna att bearbeta?
* Behöver dina moduler någon specialiserad maskinvara för att påskynda sina arbetsbelastningar?
* Vilka är de önskade prestandaegenskaperna för din lösning?
* Vad är din maskinvarubudget?
