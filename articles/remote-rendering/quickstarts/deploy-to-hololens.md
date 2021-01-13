---
title: Distribuera Unity-exempel till HoloLens
description: Snabbstart som visar hur du hämtar Unity-exemplet till HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: a5a44b9d27901fcd00f8813096de6bbae5f2310d
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179953"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Snabbstart: Distribuera Unity-exempel till HoloLens

Den här snabbstarten beskriver hur du distribuerar och kör quickstart-exempelappen för Unity till en HoloLens 2.

I den här snabbstarten lär du dig att:

> [!div class="checklist"]
>
>* Skapa quickstart-exempelappen för HoloLens
>* Distribuera exemplet till enheten
>* Köra exemplet på enheten

## <a name="prerequisites"></a>Förutsättningar

I den här snabbstarten ska vi distribuera exempelprojektet från [Snabbstart: Rendera en modell med Unity](render-model.md).

Se till att dina autentiseringsuppgifter sparas korrekt med scenen och att du kan ansluta till en session inifrån Unity-redigeraren.

## <a name="build-the-sample-project"></a>Skapa exempelprojektet

1. Öppna *File > Build Settings* (Arkiv > Bygginställningar).
1. Ändra *Platform* (Plattform) till **Universal Windows Platform** (UWP)
1. Ange **HoloLens** som *målenhet*
1. Ange **ARM64** som *arkitektur*
1. Ange **D3D Project**\ som *byggtyp*
    ![Bygginställningar](./media/unity-build-settings.png)
1. Välj **Switch to Platform** (Växla till plattform)
1. När du trycker på **Build** (Bygg), eller ”Build And Run” (Bygg och kör), uppmanas du att välja en mapp där du vill lagra lösningen
1. Öppna den genererade **Quickstart.sln**-filen med Visual Studio
1. Ändra konfigurationen till **Release** (Version) och **ARM64**
1. Växla felsökningsläge till **Remote Machine**\ (Fjärrdator)
    ![Lösningskonfiguration](media/unity-deploy-config.png)
1. Skapa lösningen
1. För projektet ”Quickstart” (Snabbstart) går du till *Properties > Debugging* (Egenskaper > Felsökning)
    1. Kontrollera att konfigurationen *Release* (Version) är aktiv
    1. Ställ in *Debugger to Launch* (Felsökningsprogram som ska starta) till **Remote Machine** (Fjärrdator)
    1. Ändra *dator namn* till **IP-adressen för din HoloLens**

## <a name="launch-the-sample-project"></a>Starta exempelprojektet

1. Anslut HoloLens med en USB-kabel till din dator.
1. Starta felsökningsprogrammet i Visual Studio (F5). Appen distribueras automatiskt till enheten.

Exempelappen bör starta och sedan starta en ny session. Efter en stund är sessionen klar och du har den fjärrenderade modellen framför dig.
Om du vill starta exemplet igen senare hittar du det även på HoloLens-startmenyn.

## <a name="next-steps"></a>Nästa steg

I nästa snabbstart ska vi titta på hur du kan konvertera en anpassad modell.

> [!div class="nextstepaction"]
> [Snabbstart: Konvertera en modell för rendering](convert-model.md)
