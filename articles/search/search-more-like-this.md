---
title: moreLikeThis no Azure Search (versão prévia) – Azure Search
description: Documentação preliminar para o recurso moreLikeThis (versão prévia), exposto na API REST do Azure Search.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 5723f1ab7258a9e0d672b5c0fd9fd0b9c4dc8721
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522916"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis no Azure Search

> [!Note]
> moreLikeThis está em versão prévia e não destina-se para uso em produção. O [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há nenhum suporte de SDK do .NET no momento.

`moreLikeThis=[key]` é um parâmetro de consulta na [API de pesquisa de documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) que localiza os documentos semelhantes ao documento especificado pela chave do documento. Quando uma solicitação de pesquisa é feita com `moreLikeThis`, uma consulta é gerada com termos de pesquisa extraídos do documento fornecido que descrevem melhor o documento. A consulta gerada é usada para fazer a solicitação de pesquisa. Por padrão, o conteúdo de todos os campos pesquisáveis é considerado, menos todos os campos restritos que você especificou usando o `searchFields` parâmetro. O parâmetro `moreLikeThis` não pode ser usado com o parâmetro de pesquisa, `search=[string]`.

Por padrão, o conteúdo de todos os campos pesquisáveis de nível superior é considerado. Se você quiser especificar campos específicos em vez disso, você pode usar o `searchFields` parâmetro. 

Não é possível usar moreLikeThis em campos pesquisáveis de subpropriedades de uma [tipo complexo](search-howto-complex-data-types.md).

## <a name="examples"></a>Exemplos 

Veja abaixo um exemplo de uma consulta moreLikeThis. A consulta localiza os documentos cujos campos de descrição são mais parecidos com o campo do documento de origem, conforme especificado pelo parâmetro `moreLikeThis`.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>Próximas etapas

Você pode usar qualquer ferramenta de teste de web para fazer experiências com esse recurso.  É recomendável usar o Postman para este exercício.

> [!div class="nextstepaction"]
> [Explorar as APIs de REST do Azure Search usando o Postman](search-fiddler.md)