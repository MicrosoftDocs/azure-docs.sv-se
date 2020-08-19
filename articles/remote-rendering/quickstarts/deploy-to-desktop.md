---
title: Distribuera Unity-exempel till skrivbordet
description: Snabb start som visar hur du hämtar Unity-exemplet på en stationär dator
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 6e1a6cb583c0d310cc7ce73995224c42bb84784d
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566173"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Snabb start: Distribuera Unity-exempel till Skriv bordet

Den här snabb starten beskriver hur du distribuerar och kör appen snabb starts exempel för enhet till en stationär dator.

I den här snabb starten får du lära dig att:

> [!div class="checklist"]
>
>* Bygg appen snabb starts exempel för Desktop
>* Distribuera exemplet till en dator
>* Kör exemplet på en dator

## <a name="prerequisites"></a>Förutsättningar

I den här snabb starten ska vi distribuera exempelprojektet från [snabb start: återge en modell med Unity](render-model.md).

Se till att dina autentiseringsuppgifter sparas korrekt med scenen och att du kan ansluta till en session inifrån Unit-redigeraren.

## <a name="disable-virtual-reality-support"></a>Inaktivera stöd för virtuell verklighet

Endast flata skrivbordsappar stöds för närvarande på Desktop, så VR-stödet måste inaktive ras.

1. Öppna *redigera > projekt inställningar...*
1. Välj **spelare** till vänster.
1. Välj fliken **universell Windows-plattform inställningar** .
1. Expandera **XR-inställningarna**.
1. Det går inte att inaktivera **Virtual verklighet**. \
    ![inställningar för spelare](./media/unity-disable-xr.png)
1. I *XR inställningar*expanderar du **publicerings inställningar**.
1. I **enhets familjer som stöds**kontrollerar du att **Skriv bordet** är markerat.

## <a name="build-the-sample-project"></a>Bygg exempelprojektet

1. Öppna *fil > versions inställningar*.
1. Ändra *plattform* till **universell Windows-plattform** (**fristående dator** stöds också, men används inte här, se [plattforms begränsningar](../reference/limits.md#platform-limitations)).
1. Ställ in *mål enheten* på **datorn**.
1. Ange *arkitektur* till **x86**.
1. Ange *build-typ* till **D3D-projekt**. \
  ![Build-inställningar](./media/unity-build-settings-pc.png)
1. Välj **Växla till plattform**.
1. När du trycker på **skapa** (eller "skapa och kör") kommer du att uppmanas att välja en mapp där lösningen ska lagras.
1. Öppna den genererade **snabb starten. SLN** med Visual Studio.
1. Ändra konfigurationen till **version** och **x86**.
1. Växla fel söknings läge till **lokal dator**. \
  ![Lösnings konfiguration](./media/unity-deploy-config-pc.png)
1. Bygg lösningen (F7).

## <a name="launch-the-sample-project"></a>Starta exempelprojektet

Starta fel söknings programmet i Visual Studio (F5). Appen installeras automatiskt på datorn.

Exempel appen bör starta och sedan starta en ny session. Efter en stund är sessionen klar och den fjärranslutna modellen visas framför dig.
Om du vill starta exemplet en gång senare kan du även hitta det från Start-menyn.

## <a name="next-steps"></a>Nästa steg

I nästa snabb start tar vi en titt på att konvertera en anpassad modell.

> [!div class="nextstepaction"]
> [Snabb start: konvertera en modell för åter givning](convert-model.md)
