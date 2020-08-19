---
title: 'Snabbstart: Skapa en miniatyrbild – REST, Python'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av API för visuellt innehåll och Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: c415e8e9c07ca991b32576ebf9daa109a500dbff
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505843"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Snabb start: skapa en miniatyr med hjälp av Visuellt innehåll REST API och python

I den här snabb starten ska du skapa en miniatyr bild från en avbildning med hjälp av Visuellt innehåll REST API. Med metoden [Hämta miniatyrbild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c) kan du ange önskad höjd och bredd. Därefter använder Visuellt innehåll smart beskärning för att identifiera det område som är intressant och generera koordinater för beskärning baserat på det området.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* [Skapa miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och slut punkts-URL: en, med namnet respektive `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .
- En kod redigerare som [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

Kopiera följande kod till kodredigeraren för att skapa och köra exemplet. 

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following lines.
# %matplotlib inline
# import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v3.0/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

# Construct URL
headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
# Call API
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

# Open the image from bytes
thumbnail = Image.open(BytesIO(response.content))

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))

# Save thumbnail to file
thumbnail.save('thumbnail.png')

# Display image
thumbnail.show()

# Optional. Display the thumbnail from Jupyter.
# plt.imshow(thumbnail)
# plt.axis("off")
```

Gör därefter något av följande:

1. Valfritt Ersätt värdet för `image_url` med URL: en för din egen bild.
1. Spara koden som en fil med tillägget `.py`. Till exempel `get-thumbnail.py`.
1. Öppna ett kommandotolksfönster.
1. I kommandotolken kör du exemplet med kommandot `python`. Till exempel `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar returneras i form av binärdata som representerar bilddata för miniatyrbilden. Exemplet ska visa den här bilden. Om begäran misslyckas visas svaret i kommandotolkens fönster och bör innehålla en felkod.

## <a name="run-in-jupyter-optional"></a>Köra i Jupyter (valfritt)

Du kan välja att köra den här snabbstarten steg för steg med hjälp av en Jupyter-anteckningsbok i [MyBinder](https://mybinder.org). Starta Binder med den här knappen:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Nästa steg

Nu ska du utforska ett python-program som använder Visuellt innehåll för att utföra optisk tecken igenkänning (OCR). Skapa Smart-beskurna miniatyrer; identifiera, kategorisera, tagga och beskriv visuella funktioner i bilder.

> [!div class="nextstepaction"]
> [Självstudie: API för visuellt innehåll med Python](../Tutorials/PythonTutorial.md)

* Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).
