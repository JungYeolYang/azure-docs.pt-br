---
title: Entidade ordinal predefinida
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida de ordinal de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 752a26b21854ec9030fc1945024ae461445815a9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146711"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Entidade ordinal predefinida para um aplicativo LUIS
Número ordinal é uma representação numérica de um objeto dentro de um conjunto: `first`, `second`, `third`. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo ordinal às intenções do aplicativo. A entidade ordinal é compatível com [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Tipos de ordinal
A entidade ordinal é gerenciada por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Resolução para entidade ordinal predefinida

### <a name="api-version-2x"></a>Versão da API 2. x

O exemplo a seguir mostra a resolução da entidade **builtin.ordinal**.

```json
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Versão de API de visualização 3. x

O JSON a seguir é com o `verbose` parâmetro definido como `false`:

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ]
        }
    }
}
```

O JSON a seguir é com o `verbose` parâmetro definido como `true`:

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ],
            "$instance": {
                "ordinal": [
                  {
                    "type": "builtin.ordinal",
                    "text": "second",
                    "startIndex": 10,
                    "length": 6,
                    "modelTypeId": 2,
                    "modelType": "Prebuilt Entity Extractor",
                    "recognitionSources": [
                        "model"
                    ]
                  }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [percentual](luis-reference-prebuilt-percentage.md), [número de telefone](luis-reference-prebuilt-phonenumber.md), e [temperatura](luis-reference-prebuilt-temperature.md) entidades. 
