---
title: 'Självstudie: kom igång med Azure Synapse Analytics – visualisera arbets ytans data med Power BI'
description: I den här självstudien får du lära dig hur du skapar en Power BI arbets yta, länkar din Azure Synapse-arbetsyta och skapar en Power BI data uppsättning som använder data i Azure Synapse-arbetsytan.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 952d69cccff86d1a0119391c400a40908c62ed69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98208972"
---
# <a name="visualize-data-with-power-bi"></a>Visualisera data med Power BI

I den här självstudien får du lära dig hur du skapar en Power BI arbets yta, länkar din Azure Synapse-arbetsyta och skapar en Power BI data uppsättning som använder data i din Azure Synapse-arbetsyta. 

> [!NOTE]
> [Installera Power BI Desktop](https://aka.ms/pbidesktopstore)för att slutföra den här självstudien.

## <a name="overview"></a>Översikt

Från NYC taxi-data skapade vi sammanställda data uppsättningar i två tabeller:
- **nyctaxi.passengercountstats**
- **SQLDB1. dbo. PassengerCountStats**

Du kan länka en Power BI arbets yta till din Azure Synapse-arbetsyta. Med den här funktionen kan du enkelt hämta data till din Power BI-arbetsyta. Du kan redigera dina Power BI rapporter direkt i din Azure Synapse-arbetsyta. 

### <a name="create-a-power-bi-workspace"></a>Skapa en Power BI arbets yta

1. Logga in på [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
1. Klicka på **arbets ytor** och välj sedan **skapa en arbets yta**. Skapa en ny Power BI arbets yta med namnet **NYCTaxiWorkspace1** eller liknande, eftersom namnet måste vara unikt.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Länka din Azure Synapse-arbetsyta till din nya Power BI-arbetsyta

1. I Synapse Studio går du till **Hantera**  >  **länkade tjänster**.
1. Välj **ny**  >  **Anslut till Power BI**.
1. Ange **namnet** till **NYCTaxiWorkspace1**.
1. Ange **arbets ytans namn** till Power BI arbets ytan som du skapade ovan, ungefär som **NYCTaxiWorkspace1**.
1. Välj **Skapa**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Skapa en Power BI-datauppsättning som använder data i din Azure Synapse-arbetsyta

1. I Synapse Studio går du till **utveckla**  >  **Power BI**.
1. Gå till **NYCTaxiWorkspace1**  >  **Power BI data uppsättningar** och välj **ny Power BI data uppsättning**. Klicka på **Starta**.
1. Välj data källan **SQLPOOL1** , klicka på **Fortsätt**.
1. Klicka på **Hämta** för att ladda ned. pbids-filen för filen **NYCTaxiWorkspace1SQLPOOL1. pbids** . Klicka på **Fortsätt**.
1. Öppna den nedladdade **. pbids** -filen. Power BI Desktop öppnar och ansluter automatiskt till **SQLDB1** i din Azure Synapse-arbetsyta.
1. Om en dialog ruta visas som heter **SQL Server Database**:
    1. Välj **Microsoft-konto**.
    1. Välj **Logga** in och logga in på ditt konto.
    1. Välj **Anslut**.
1. När dialog rutan **navigatör** öppnas kontrollerar du tabellen **PassengerCountStats** och väljer **load**.
1. När dialog rutan **anslutnings inställningar** visas väljer du **DirectQuery**  >  **OK**.
1. Välj **rapport** knappen på vänster sida.
1. Under **visualiseringar** klickar du på ikonen linje diagram för att lägga till ett **linje diagram** i rapporten.
    1. Dra kolumnen **PassengerCount** till **visualiserings** axeln under **fält**  >  .
    1. Dra kolumnerna **SumTripDistance** och **AvgTripDistance** till **visualiserings**  >  **värden**.
1. På fliken **Start** väljer du **publicera**.
1. Välj **Spara** för att spara ändringarna.
1. Välj fil namnet **PassengerAnalysis. pbix** och välj sedan **Spara**.
1. I fönstret **Publicera till Power BI** , under **Välj ett mål**, väljer du din **NYCTaxiWorkspace1** och klickar sedan på **Välj**.
1. Vänta tills publiceringen har slutförts. 

### <a name="configure-authentication-for-your-dataset"></a>Konfigurera autentisering för din data uppsättning

1. Öppna [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) och **Logga** in.
1. På den vänstra sidan, under **arbets ytor**, väljer du arbets ytan **NYCTaxiWorkspace1** .
1. På arbets ytan letar du reda på en data uppsättning som kallas **passagerar analys** och en rapport som kallas **passagerar analys**.
1. Hovra över **PassengerAnalysis** -datauppsättningen, Välj knappen med tre punkter (...) och välj sedan **Inställningar**.
1. I **autentiseringsuppgifter för data källa** klickar du på **Redigera**, anger **autentiseringsmetoden** till **OAuth2** och väljer sedan **Logga** in.

### <a name="edit-a-report-in-synapse-studio"></a>Redigera en rapport i Synapse Studio

1. Gå tillbaka till Synapse Studio och välj **Stäng och uppdatera**.
1. Gå till **utveckla** hubben.
1. Till höger om knappen **Power BI** lager, tre punkter (...) och klicka på **Uppdatera** för att uppdatera noden **Power BI rapporter** .
1. Under **Power BI** bör du se:
    * I **NYCTaxiWorkspace1**  >  **Power BI data uppsättningar**, en ny data uppsättning med namnet **PassengerAnalysis**.
    * Under **NYCTaxiWorkspace1**  >  **Power BI rapporter** kallas en ny rapport som heter **PassengerAnalysis**.
1. Välj **PassengerAnalysis** -rapporten. Rapporten öppnas och du kan redigera den direkt i Synapse Studio.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka](get-started-monitor.md)
                                 

