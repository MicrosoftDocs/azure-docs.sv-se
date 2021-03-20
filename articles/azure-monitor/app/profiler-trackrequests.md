---
title: Skriv kod för att spåra begär Anden med Azure Application Insights | Microsoft Docs
description: Skriv kod för att spåra begär Anden med Application Insights så att du kan hämta profiler för dina begär Anden.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.custom: devx-track-csharp
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: aaa1d6df9faa20b1a561bfccdfea682af7645c18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88930255"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Skriv kod för att spåra begär Anden med Application Insights

Om du vill visa profiler för ditt program på sidan prestanda måste du Azure Application Insights för att spåra begär Anden för ditt program. Application Insights kan automatiskt spåra begär Anden för program som bygger på redan instrumenterade ramverk. Två exempel är ASP.NET och ASP.NET Core. 

För andra program, till exempel Azure Cloud Services Worker-roller och Service Fabric tillstånds lösa API: er, måste du skriva kod för att tala om för Application Insights där begär Anden börjar och slutar. När du har skrivit koden skickas begär ande telemetri till Application Insights. Du kan visa telemetri på sidan prestanda och profilerna samlas in för dessa begär Anden. 

Gör så här om du vill spåra begär Anden manuellt:

  1. I början av programmets livs längd lägger du till följande kod:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Mer information om den här nyckel konfigurationen för global Instrumentation finns i [använda Service Fabric med Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Lägg till en using-instruktion runt den kod som du vill använda, `StartOperation<RequestTelemetry>`  som du ser i följande exempel:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Det `StartOperation<RequestTelemetry>` finns inte stöd för att anropa i ett annat `StartOperation<RequestTelemetry>` omfång. Du kan använda `StartOperation<DependencyTelemetry>` i det kapslade omfånget i stället. Exempel:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
