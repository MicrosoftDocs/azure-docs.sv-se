---
title: Azure RabbitMQ-bindningar för Azure Functions
description: Lär dig att skicka Azure RabbitMQ-utlösare och bindningar i Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 2a480f25821f5022295b18ca24abfd2c0fb8a50c
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746532"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>RabbitMQ-bindningar för Azure Functions översikt

> [!NOTE]
> RabbitMQ-bindningarna stöds bara fullt ut av **Premium och dedikerade** planer. Förbrukning stöds inte.

Azure Functions integreras med [rabbitmq](https://www.rabbitmq.com/) via [utlösare och bindningar](./functions-triggers-bindings.md). Med tillägget Azure Functions RabbitMQ kan du skicka och ta emot meddelanden med hjälp av RabbitMQ-API: et med Functions.

| Åtgärd | Typ |
|---------|---------|
| Köra en funktion när ett RabbitMQ-meddelande kommer via kön | [Utlösare](./functions-bindings-rabbitmq-trigger.md) |
| Skicka RabbitMQ-meddelanden |[Utgående bindning](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Lägg till i functions-appen

För att komma igång med att utveckla med det här tillägget, se till att du först [konfigurerar en rabbitmq-slutpunkt](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint). Om du vill veta mer om RabbitMQ kan du titta på [sidan komma igång](https://www.rabbitmq.com/getstarted.html).

### <a name="functions-3x-and-higher"></a>Funktionerna 3. x och högre

Om du arbetar med utlösaren och bindningarna måste du referera till rätt paket. NuGet-paketet används för .NET-klass bibliotek medan tilläggs paketet används för alla andra program typer.

| Språk                                        | Lägg till efter...                                   | Kommentarer
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 4. x | |
| C#-skript, Java, Java Script, python, PowerShell | [Tilläggs paketet] registreras          | [Tillägget Azure-verktyg] rekommenderas för användning med Visual Studio Code. |
| C#-skript (endast online i Azure Portal)         | Lägga till en bindning                            | Om du vill uppdatera befintliga bindnings tillägg utan att behöva publicera om din Function-app, se [Uppdatera dina tillägg]. |

[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[paket för tillägg]: ./functions-bindings-register.md#extension-bundles
[Uppdatera dina tillägg]: ./functions-bindings-register.md
[Tillägg för Azure-verktyg]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>Functions 1. x och 2. x

RabbitMQ-bindnings tillägg stöds inte för funktionerna 1. x och 2. x. Använd funktionerna 3. x och högre.

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när ett RabbitMQ-meddelande skapas (utlösare)](./functions-bindings-rabbitmq-trigger.md)
- [Skicka RabbitMQ-meddelanden från Azure Functions (utgående bindning)](./functions-bindings-rabbitmq-output.md)