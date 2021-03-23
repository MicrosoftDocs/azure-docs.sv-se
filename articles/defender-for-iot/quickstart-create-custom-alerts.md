---
title: Skapa anpassade aviseringar
description: Förstå, skapa och tilldela anpassade enhets aviseringar för tjänsten Azure Defender for IoT.
ms.topic: quickstart
ms.date: 09/04/2020
ms.openlocfilehash: 836619fa200983bc91d26db4d063e8545b11a1b5
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781049"
---
# <a name="create-custom-alerts"></a>Skapa anpassade aviseringar

Genom att använda anpassade säkerhets grupper och aviseringar kan du utnyttja all fullständig säkerhets information och kategoriska för att säkerställa bättre säkerhet i din IoT-lösning.

## <a name="why-use-custom-alerts"></a>Varför ska jag använda anpassade aviseringar?

Du vet att dina IoT-enheter är bäst.

För kunder som fullständigt förstår sitt förväntade enhets beteende kan du med hjälp av Defender för IoT översätta denna förståelse till en princip för enhets beteenden och en avisering om eventuella avvikelser från förväntat, normalt beteende.

## <a name="security-groups"></a>Säkerhetsgrupper

Med säkerhets grupper kan du definiera logiska grupper av enheter och hantera deras säkerhets tillstånd på ett centraliserat sätt.

Dessa grupper kan representera enheter med specifik maskin vara, enheter som har distribuerats på en viss plats eller någon annan grupp som passar dina specifika behov.

Säkerhets grupper definieras av en enhets dubbla tag-egenskap med namnet **SecurityGroup**. Som standard har varje IoT-lösning på IoT Hub en säkerhets grupp med namnet **default**. Ändra värdet för egenskapen **SecurityGroup** om du vill ändra säkerhets gruppen för en enhet.

Exempel:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  },
```

Använd säkerhets grupper för att gruppera dina enheter i logiska kategorier. När du har skapat grupperna tilldelar du dem till de anpassade aviseringarna som du önskar, för den mest effektiva IoT-säkerhetslösningen från slut punkt till slut punkt.

## <a name="customize-an-alert"></a>Anpassa en avisering

1. Öppna din IoT Hub och välj **Inställningar** på menyn **säkerhet** .

1. Välj på **anpassade aviseringar**.

1. Välj en säkerhets grupp som du vill tillämpa anpassningen på.

1. Välj **Lägg till en anpassad avisering**.

1. Välj en anpassad avisering i list rutan.

1. Redigera de obligatoriska egenskaperna och välj **OK**.

1. Se till att välja **Spara**. Om du inte sparar den nya aviseringen tas aviseringen bort nästa gången du stänger IoT Hub.

## <a name="alerts-available-for-customization"></a>Aviseringar tillgängliga för anpassning

Defender for IoT erbjuder ett stort antal aviseringar, som kan anpassas efter dina behov. Granska den [anpassningsbara aviserings tabellen](concept-customizable-security-alerts.md) för aviserings allvarlighets grad, data källa, beskrivning och våra föreslagna reparations steg om och när varje avisering tas emot.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du distribuerar en säkerhets agent...

> [!div class="nextstepaction"]
> [Distribuera en säkerhets agent](how-to-deploy-agent.md)
