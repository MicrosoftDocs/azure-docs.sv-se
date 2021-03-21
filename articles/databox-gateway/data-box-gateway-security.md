---
title: Azure Data Box Gateway säkerhet | Microsoft Docs
description: Beskriver funktioner för säkerhet och sekretess som skyddar din Azure Data Box Gateway virtuella enhet, tjänst och data lokalt och i molnet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 13d3809611714992f24a66a96c22074e69fba9bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786664"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box Gateway säkerhet och data skydd

Säkerhet är ett viktigt problem när du använder en ny teknik, särskilt om tekniken används med konfidentiell eller patentskyddad information. Azure Data Box Gateway hjälper dig att se till att endast auktoriserade entiteter kan visa, ändra eller ta bort dina data.

I den här artikeln beskrivs Azure Data Box Gateway säkerhetsfunktioner som skyddar varje lösnings komponent och de data som lagras i dem.

Data Box Gateway lösningen består av fyra huvud komponenter som interagerar med varandra:

- **Data Box Gateway tjänst som finns i Azure**. Hanterings resursen som du använder för att skapa enhets ordningen, konfigurera enheten och sedan spåra beställningen.
- **Data Box gateway enhet**. Den virtuella enhet som du etablerar i hypervisor-systemet som du anger. Den här virtuella enheten används för att importera dina lokala data till Azure.
- **Klienter/värdar som är anslutna till enheten**. Klienterna i din infrastruktur som ansluter till Data Box Gateway-enheten och innehåller data som behöver skyddas.
- **Moln lagring**. Platsen i Azure Cloud Platform där data lagras. Den här platsen är vanligt vis det lagrings konto som är kopplat till Data Box Gateway resurs som du skapar.

## <a name="data-box-gateway-service-protection"></a>Data Box Gateway tjänst skydd

Tjänsten Data Box Gateway är en hanterings tjänst som finns i Azure. Tjänsten används för att konfigurera och hantera enheten.

[!INCLUDE [data-box-gateway-service-protection](../../includes/data-box-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box Gateway enhets skydd

Den Data Box Gateway enheten är en virtuell enhet som är etablerad i hypervisorn för ett lokalt system som du anger. Enheten hjälper till att skicka data till Azure. Din enhet:

- Behöver en aktiverings nyckel för att få åtkomst till Azure Stack Edge Pro/Data Box Gateway-tjänsten.
- Skyddas hela tiden av ett enhets lösen ord.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Skydda enheten via aktiverings nyckeln

Endast en auktoriserad Data Box Gateway enhet får ansluta till den Data Box Gateways tjänst som du skapar i din Azure-prenumeration. Om du vill auktorisera en enhet måste du använda en aktiverings nyckel för att aktivera enheten med Data Box Gateway tjänsten.

[!INCLUDE [data-box-gateway-activation-key](../../includes/data-box-gateway-activation-key.md)]

Mer information finns i [Hämta en aktiverings nyckel](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Skydda enheten via lösen ord

Lösen ord se till att endast behöriga användare kan komma åt dina data. Data Box Gateway enheter startar i låst tillstånd.

Du kan:

- Anslut till det lokala webb gränssnittet på enheten via en webbläsare och ange ett lösen ord för att logga in på enheten.
- Fjärrans luta till enhetens PowerShell-gränssnitt över HTTP. Fjärrhantering är aktiverat som standard. Du kan sedan ange enhetens lösen ord för att logga in på enheten. Mer information finns i fjärrans [luta till din data Box gateway-enhet](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-gateway-password-best-practices](../../includes/data-box-gateway-password-best-practices.md)]
- Använd det lokala webb gränssnittet för att [ändra lösen ordet](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Om du ändrar lösen ordet ska du se till att meddela alla fjärråtkomst-användare så att de inte har problem med att logga in.

## <a name="protect-your-data"></a>Skydda dina data

I det här avsnittet beskrivs Data Box Gateway säkerhetsfunktioner som skyddar data som överförs och lagras.

### <a name="protect-data-at-rest"></a>Skydda data i vila

[!INCLUDE [data-box-gateway-data-rest](../../includes/data-box-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Skydda data i flygningen

[!INCLUDE [data-box-gateway-data-flight](../../includes/data-box-gateway-data-flight.md)]

### <a name="protect-data-using-storage-accounts"></a>Skydda data med hjälp av lagrings konton

[!INCLUDE [data-box-gateway-data-storage-accounts](../../includes/data-box-gateway-protect-data-storage-accounts.md)]

- Rotera och [Synkronisera dina lagrings konto nycklar](data-box-gateway-manage-shares.md#sync-storage-keys) regelbundet för att hjälpa till att skydda ditt lagrings konto från obehöriga användare.

### <a name="protect-the-device-data-using-bitlocker"></a>Skydda enhets data med BitLocker

För att skydda de virtuella diskarna på din Data Box Gateway virtuella dator, rekommenderar vi att du aktiverar BitLocker. BitLocker är inte aktiverat som standard. Mer information finns i:

- [Inställningar för krypterings stöd i Hyper-V Manager](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
- [Stöd för BitLocker i en virtuell dator](https://kb.vmware.com/s/article/2036142)

## <a name="manage-personal-information"></a>Hantera personlig information

Tjänsten Data Box Gateway samlar in personlig information i följande scenarier:

[!INCLUDE [data-box-gateway-manage-personal-data](../../includes/data-box-gateway-manage-personal-data.md)]

Om du vill visa en lista över användare som har åtkomst till eller tar bort en resurs följer du stegen i [Hantera resurser på data Box Gateway](data-box-gateway-manage-shares.md).

Mer information hittar du i sekretess policyn för Microsoft på [säkerhets Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

[Distribuera din Data Box Gateway-enhet](data-box-gateway-deploy-prep.md)
