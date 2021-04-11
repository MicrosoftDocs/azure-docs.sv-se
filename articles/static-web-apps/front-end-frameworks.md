---
title: Konfigurera klient dels ramverk med för hands versionen av Azure statisk Web Apps
description: Inställningar för populära front-end-ramverk som krävs för Azures statiska Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 9f02c9ad10e2b03bddc7c3ca2cfb54932464b69a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731759"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Konfigurera klient dels ramverk och bibliotek med Azures statiska Web Apps för hands version

Den statiska Azure-Web Apps kräver att du har rätt konfigurations värden i [build-konfigurationsfilen](github-actions-workflow.md) för ditt klient ramverk eller bibliotek.

## <a name="configuration"></a>Konfiguration

I följande tabell visas inställningarna för en serie ramverk och bibliotek<sup>1</sup>.

Avsikten med tabell kolumnerna förklaras av följande objekt:

- **Utmatnings plats**: visar värdet för `output_location` , som är [mappen för inbyggda versioner av programfiler](github-actions-workflow.md#build-and-deploy).

- **Anpassat build-kommando**: när ramverket kräver ett annat kommando än `npm run build` eller `npm run azure:build` , kan du definiera ett [anpassat build-kommando](github-actions-workflow.md#custom-build-commands).

| Ramverk | Plats för appartefakt | Anpassat build-kommando |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | ej tillämpligt <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Vinkel universell](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | saknas |
| [Backbone.js](https://backbonejs.org/) | `/` | saknas |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | saknas |
| [Ember](https://emberjs.com/) | `dist` | saknas |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | saknas |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | saknas |
| [Hyperapp](https://hyperapp.dev/) | `/` | saknas |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | saknas |
| [jQuery](https://jquery.com/) | `/` | saknas |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | saknas |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | saknas |
| [Marko](https://markojs.com/) | `public` | saknas |
| [Meteor](https://www.meteor.com/) | `bundle` | saknas |
| [Mithril](https://mithril.js.org/) | `dist` | saknas |
| [Mera](https://www.polymer-project.org/) | `build/default` | saknas |
| [Preact](https://preactjs.com/) | `build` | saknas |
| [React](https://reactjs.org/) | `build` | saknas |
| [RedwoodJS](https://redwoodjs.com/) | `web/dist` | `yarn rw build` |
| [>](https://stenciljs.com/) | `www` | saknas |
| [Svelte](https://svelte.dev/) | `public` | saknas |
| [Three.js](https://threejs.org/) | `/` | saknas |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | saknas |
| [Vue.js](https://vuejs.org/) | `dist` | saknas |

<sup>1</sup> tabellen ovan är inte avsedd att vara en fullständig lista över ramverk och bibliotek som fungerar med Azures statiska Web Apps.

<sup>2</sup> inte tillämpligt

## <a name="next-steps"></a>Nästa steg

- [Versions- och arbetsflödeskonfiguration](github-actions-workflow.md)
