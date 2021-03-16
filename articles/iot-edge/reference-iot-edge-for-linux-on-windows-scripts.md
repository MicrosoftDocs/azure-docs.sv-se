---
title: PowerShell-skript för Azure IoT Edge för Linux i Windows | Microsoft Docs
description: Referens information för Azure IoT Edge för Linux på Windows PowerShell-skript för att distribuera, etablera och status IoT Edge för Linux på virtuella Windows-datorer.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: fe04615e234eaf5b006a80e6f584905626c637ba
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489493"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>PowerShell-skript för IoT Edge för Linux i Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Förstå de PowerShell-skript som distribuerar, etablerar och hämtar status IoT Edge för Linux på den virtuella Windows-datorn.

Kommandona som beskrivs i den här artikeln är från `AzureEFLOW.psm1` filen som finns på ditt system i din `WindowsPowerShell` katalog under `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` .

## <a name="deploy-eflow"></a>Deploy-Eflow

Kommandot **Deploy-Eflow** är den huvudsakliga distributions metoden. Distributions kommandot skapar den virtuella datorn, etablerar filer och distribuerar modulen IoT Edge agent. Även om ingen av parametrarna krävs kan de användas för att etablera din IoT Edge-enhet under distributionen och ändra inställningarna för den virtuella datorn när den skapas. Använd-kommandot för en fullständig lista `Get-Help Deploy-Eflow -full` .  

>[!NOTE]
>För etablerings typen refererar **X509** för närvarande endast till X509-etablering med hjälp av en [Azure-IoT Hub Device Provisioning service](../iot-dps/about-iot-dps.md). Den manuella X509-etablerings metoden stöds inte för närvarande.

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| eflowVhdxDir | Sökväg till katalog | Katalog där-distributionen lagrar VHDX-filen för den virtuella datorn. |
| provisioningType | **manuell**, **TPM**, **X509** eller **symmetrisk** |  Definierar vilken typ av etablering som du vill använda för din IoT Edge-enhet. |
| devConnString | Enhets anslutnings strängen för en befintlig IoT Edge enhet | Enhets anslutnings sträng för manuell etablering av en IoT Edge enhet (**manuell**). |
| symmKey | Den primära nyckeln för en befintlig DPS-registrering eller primär nyckel för en befintlig IoT Edge enhet som registrerats med symmetriska nycklar | Symmetrisk nyckel för etablering av en IoT Edge enhet (**X509** eller **symmetrisk**). |
| scopeId | Omfångs-ID för en befintlig DPS-instans. | Scope-ID för etablering av en IoT Edge enhet (**X509** eller **symmetrisk**). |
| registrationId | Registrerings-ID för en befintlig IoT Edge enhet | Registrerings-ID för etablering av en IoT Edge enhet (**X509** eller **symmetrisk**). |
| identityCertLocVm | Katalog Sök väg; måste finnas i en mapp som kan ägas av `iotedge` tjänsten | Absolut mål Sök väg för identitets certifikatet på den virtuella datorn för etablering av en IoT Edge enhet (**X509** eller **symmetrisk**). |
| identityCertLocWin | Sökväg till katalog | Absolut käll Sök väg för identitets certifikatet i Windows för etablering av en IoT Edge enhet (**X509** eller **symmetrisk**). |
| identityPkLocVm |  | Katalog Sök väg; måste finnas i en mapp som kan ägas av `iotedge` tjänsten | Absolut mål Sök väg för den privata identiteten på den virtuella datorn för etablering av en IoT Edge enhet (**X509** eller **symmetrisk**). |
| identityPkLocWin | Sökväg till katalog | Absolut käll Sök väg för den privata identiteten i Windows för etablering av en IoT Edge enhet (**X509** eller **symmetrisk**). |
| vmSizeDefintion | Högst 30 tecken | Definition av antalet kärnor och tillgängligt RAM-minne för den virtuella datorn. **Standardvärde**: Standard_K8S_v1. |
| vmDiskSize | Mellan 8 GB och 256 GB | Maximal disk storlek för den dynamiskt expanderande virtuella hård disken. **Standardvärde**: 16 GB. |
| vmUser | Högst 30 tecken | Användar namn för att logga in på den virtuella datorn. |
| vnetType | **Transparent** eller **ICS** | Typ av virtuell växel. **Standardvärde**: transparent. |
| vnetName | Inte längre än 64 tecken | Namnet på den virtuella växeln. **Standardvärde**: external. |
| enableVtpm | Inget | **Växlings parameter**. Skapa den virtuella datorn med TPM aktiverat eller inaktiverat. |
| mobyPackageVersion | Högst 30 tecken |  Versionen av Moby-paketet som ska verifieras eller installeras på den virtuella datorn.  **Standardvärde:** 19.03.11. |
| iotedgePackageVersion | Högst 30 tecken | Versionen av IoT Edge-paketet som ska verifieras eller installeras på den virtuella datorn. **Standardvärde:** 1.1.0. |
| installPackages | Inget | **Växlings parameter**. När det är växlat försöker skriptet att installera Moby och IoT Edge-paket i stället för att bara verifiera att paketen finns. |

## <a name="verify-eflowvm"></a>Verify-EflowVm

Kommandot **verify-EflowVm** är en exponerad funktion för att kontrol lera att IoT Edge för Linux på den virtuella Windows-datorn har skapats. Det tar bara vanliga parametrar och returnerar **True** om den virtuella datorn har skapats och **falskt** om den inte är det. Om du vill ha mer information använder du kommandot `Get-Help Verify-EflowVm -full` .

## <a name="provision-eflowvm"></a>Provision-EflowVm

Kommandot **provision-EflowVm** lägger till etablerings informationen för din IoT Edge-enhet till den virtuella datorns IoT Edge- `config.yaml` fil. Etableringen kan också utföras under distributions fasen genom att ange parametrar i kommandot **Deploy-Eflow** . Om du vill ha mer information använder du kommandot `Get-Help Provision-EflowVm -full` .

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| vmUser | Högst 30 tecken | Användar namn för att logga in på den virtuella datorn. |
| provisioningType | **manuell**, **TPM**, **X509** eller **symmetrisk** |  Definierar vilken typ av etablering som du vill använda för din IoT Edge-enhet. |
| devConnString | Enhets anslutnings strängen för en befintlig IoT Edge enhet | Enhets anslutnings sträng för manuell etablering av en IoT Edge enhet (**manuell**). |
| symmKey | Den primära nyckeln för en befintlig DPS-registrering eller primär nyckel för en befintlig IoT Edge enhet som registrerats med symmetriska nycklar | Symmetrisk nyckel för etablering av en IoT Edge enhet (**DPS**, **symmetrisk**). |
| scopeId | Omfångs-ID för en befintlig DPS-instans. | Scope-ID för etablering av en IoT Edge enhet (**DPS**). |
| registrationId | Registrerings-ID för en befintlig IoT Edge enhet | Registrerings-ID för etablering av en IoT Edge enhet (**DPS**). |
| identityCertLocVm | Katalog Sök väg; måste finnas i en mapp som kan ägas av `iotedge` tjänsten | Absolut mål Sök väg för identitets certifikatet på den virtuella datorn för etablering av en IoT Edge enhet (**DPS**, **X509**). |
| identityCertLocWin | Sökväg till katalog | Absolut käll Sök väg för identitets certifikatet i Windows för etablering av en IoT Edge enhet (**DPS**, **X509**). |
| identityPkLocVm |  | Katalog Sök väg; måste finnas i en mapp som kan ägas av `iotedge` tjänsten | Absolut mål Sök väg för den privata identiteten på den virtuella datorn för etablering av en IoT Edge enhet (**DPS**, **X509**). |
| identityPkLocWin | Sökväg till katalog | Absolut käll Sök väg för den privata identiteten i Windows för etablering av en IoT Edge enhet (**DPS**, **X509**). |

## <a name="get-eflowvmname"></a>Get-EflowVmName

Kommandot **Get-EflowVmName** används för att skicka frågor till den virtuella datorns aktuella värdnamn. Det här kommandot finns för att det faktum att Windows-värdnamn kan ändras med tiden. Det tar bara vanliga parametrar och returnerar den virtuella datorns aktuella värdnamn. Om du vill ha mer information använder du kommandot `Get-Help Get-EflowVmName -full` .

## <a name="get-eflowlogs"></a>Get-EflowLogs

Kommandot **Get-EflowLogs** används för att samla in och bunta loggar från IoT Edge för Linux på Windows-distribution. Den visar de buntade loggarna i form av en `.zip` mapp. Om du vill ha mer information använder du kommandot `Get-Help Get-EflowLogs -full` .

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| vmUser | Högst 30 tecken | Användar namn för att logga in på den virtuella datorn. |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

Kommandot **Get-EflowVmTpmProvisioningInfo** används för att samla in och visa den virtuella datorns vTPM etablerings information. Om den virtuella datorn skapades utan vTPM kommer kommandot att returnera att det inte gick att hitta information om TPM-etablering. Om du vill ha mer information använder du kommandot `Get-Help Get-EflowVmTpmProvisioningInfo -full` .

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| vmUser | Högst 30 tecken | Användar namn för att logga in på den virtuella datorn. |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

Kommandot **Get-EflowVmAddr** används för att hitta och visa den virtuella datorns IP-och MAC-adresser. Det tar bara vanliga parametrar. Om du vill ha mer information använder du kommandot `Get-Help Get-EflowVmAddr -full` .

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

Kommandot **Get-EflowVmSystemInformation** används för att samla in och Visa system information från den virtuella datorn, till exempel minnes-och lagrings användning. Om du vill ha mer information använder du kommandot `Get-Help Get-EflowVmSystemInformation -full` .

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| vmUser | Högst 30 tecken | Användar namn för att logga in på den virtuella datorn. |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

Kommandot **Get-EflowVmEdgeInformation** används för att samla in och Visa IoT Edge information från den virtuella datorn, till exempel vilken version av IoT Edge den virtuella datorn körs på. Om du vill ha mer information använder du kommandot `Get-Help Get-EflowVmEdgeInformation -full` .

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| vmUser | Högst 30 tecken | Användar namn för att logga in på den virtuella datorn. |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

Kommandot **Get-EflowVmEdgeModuleList** används för att fråga och Visa listan över IoT Edge moduler som körs på den virtuella datorn. Om du vill ha mer information använder du kommandot `Get-Help Get-EflowVmEdgeModuleList -full` .

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| vmUser | Högst 30 tecken | Användar namn för att logga in på den virtuella datorn. |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

Kommandot **Get-EflowVmEdgeStatus** används för att fråga och Visa status för IoT Edge runtime på den virtuella datorn. Om du vill ha mer information använder du kommandot `Get-Help Get-EflowVmEdgeStatus -full` .

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| vmUser | Högst 30 tecken | Användar namn för att logga in på den virtuella datorn. |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

Kommandot **Get-EflowVmUserName** används för att fråga efter och visa den virtuella datorns användar namn som användes för att skapa den virtuella datorn från registret. Det tar bara vanliga parametrar. Om du vill ha mer information använder du kommandot `Get-Help Get-EflowVmUserName -full` .

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

Kommandot **Get-EflowVmSshKey** används för att fråga och Visa SSH-nyckeln som används av den virtuella datorn. Det tar bara vanliga parametrar. Om du vill ha mer information använder du kommandot `Get-Help Get-EflowVmSshKey -full` .

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

Kommandot **SSH-EflowVm** används för ssh i den virtuella datorn. Om du vill ha mer information använder du kommandot `Get-Help Ssh-EflowVm -full` .

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| vmUser | Högst 30 tecken | Användar namn för att logga in på den virtuella datorn. |

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder dessa kommandon i följande artikel:

* [Installera Azure IoT Edge för Linux på Windows](how-to-install-iot-edge-windows.md)

* Se [referensen IoT Edge för Linux på Windows PowerShell-skript](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) för alla kommandon som är tillgängliga via PowerShell.
