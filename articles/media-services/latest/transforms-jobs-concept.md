---
title: Transformações e Trabalhos nos Serviços de Mídia do Azure | Microsoft Docs
description: Ao usar os Serviços de Mídia, é necessário criar um recurso de Transformação para descrever as regras ou especificações para processar seus vídeos. Este artigo fornece uma visão geral do recurso de Transformação e como usá-lo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/08/2019
ms.author: juliako
ms.openlocfilehash: 01b386c820a09af0e616698aabc58a886c30bb09
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550940"
---
# <a name="transforms-and-jobs"></a>Transformações e Trabalhos

Este tópico fornece detalhes sobre [transforma](https://docs.microsoft.com/rest/api/media/transforms) e [trabalhos](https://docs.microsoft.com/rest/api/media/jobs) e explica a relação entre essas entidades. 

## <a name="overview"></a>Visão geral 

### <a name="transformsjobs-workflow"></a>Fluxo de trabalho de transformações/trabalhos

O diagrama a seguir mostra o fluxo de trabalho/transformações.

![Transformações](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Fluxo de trabalho típico

1. Criar uma Transformação 
2. Enviar Trabalhos sob essa Transformação 
3. Listar Transformações 
4. Exclua uma transformação, se você não planeja usá-la no futuro. 

#### <a name="example"></a>Exemplo

Suponha que você deseja extrair o primeiro quadro de todos os seus vídeos como uma imagem em miniatura. Estas são as etapas necessárias: 

1. Defina a receita ou a regra para processar seus vídeos – "usar o primeiro quadro do vídeo como a miniatura". 
2. Para cada vídeo informar ao serviço: 
    1. Onde encontrar esse vídeo,  
    2. Onde gravar a saída a imagem em miniatura de saída. 

Uma **Transformação** ajuda você a criar uma vez a receita (Etapa 1) e enviar trabalhos usando essa receita (Etapa 2).

> [!NOTE]
> Propriedades de **Transformação** e **Trabalho** que são do tipo Datetime estão sempre em formato UTC.

## <a name="transforms"></a>Transformações

Use **Transformações** para configurar tarefas comuns para codificar ou analisar vídeos. Cada **Transformação** descreve uma receita ou um fluxo de trabalho de tarefas para processar os arquivos de áudio ou vídeos. Uma única transformação pode aplicar mais de uma regra. Por exemplo, uma transformação poderia especificar que cada vídeo seja codificado em um arquivo MP4 em uma determinada taxa de bits, e que uma imagem em miniatura gerada desde o primeiro quadro do vídeo. Você precisaria adicionar uma entrada de TransformOutput para cada regra que você deseja incluir em sua Transformação. Você pode usar as predefinições para informar a transformação como os arquivos de mídia de entrada devem ser processados.

### <a name="viewing-schema"></a>Esquema de exibição

Em serviços de mídia v3 predefinições são entidades com rigidez de tipos na própria API. Você pode encontrar a definição de "esquema" para esses objetos no [especificação de API aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Você também pode exibir as definições predefinidas (como **StandardEncoderPreset**) na [API REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (ou outra documentação de referência SDK do Media Services v3).

### <a name="creating-transforms"></a>Criando transformações

Você pode criar transformações usando REST, CLI, ou usar qualquer um dos SDKs publicados. A API é controlada pelo Azure Resource Manager v3, portanto, você também pode usar modelos do Azure Resource Manager para criar Transformações na sua conta dos Serviços de Mídia do Microsoft Azure. O controle de acesso baseado em função pode ser usado para bloquear o acesso a transformações.

### <a name="updating-transforms"></a>Atualização de transformações

Se você precisar atualizar seu [transformar](https://docs.microsoft.com/rest/api/media/transforms), use o **atualizar** operação. Ele destina-se para fazer alterações a descrição ou as prioridades do TransformOutputs subjacente. É recomendável que essas atualizações sejam realizadas depois que todos os trabalhos em andamento forem concluídos. Se você pretende reescrever a receita, você precisa criar uma nova transformação.

### <a name="transform-object-diagram"></a>Transformar o diagrama de objetos

O diagrama a seguir mostra a **transformar** objeto e os objetos que ela faz referência a incluindo as relações de derivação. As setas cinzas mostram um tipo que as referências de trabalho e as setas verdes mostram as relações de derivação de classe.<br/>Clique na imagem para exibi-la em tamanho normal.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

## <a name="jobs"></a>Trabalhos

Um **Trabalho** é a solicitação real para os serviços de mídia do Azure para aplicar a **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. Quando a Transformação for criada, você poderá enviar trabalhos usando as APIs dos Serviços de Mídia ou um dos SDKs publicados. O **Trabalho** especifica informações como o local do vídeo de entrada e o local para a saída. É possível especificar a localização do vídeo de entrada usando: URLs HTTPS, URLs SAS ou [Ativos](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Entrada de trabalho de HTTPS

Use [entrada de HTTPS do trabalho](job-input-from-http-how-to.md) se seu conteúdo já está acessível por meio de uma URL e você não precisa armazenar o arquivo de origem no Azure (por exemplo, a importação do S3). Esse método também é adequado se você tiver o conteúdo no armazenamento de BLOBs do Azure, mas não há necessidade do arquivo esteja em um ativo. Atualmente, este método só aceita um único arquivo de entrada.

### <a name="asset-as-job-input"></a>Ativo como entrada de trabalho

Use [ativo como entrada de trabalho](job-input-from-local-file-how-to.md) se o conteúdo da entrada já está em um ativo ou o conteúdo é armazenado no arquivo local. Também é uma boa opção se você planeja publicar o ativo de entrada para streaming ou baixar (digamos que você deseja publicar o mp4 para download, mas também deseja fazer a conversão de fala em texto ou face detecção). Esse método dá suporte a vários arquivos ativos (por exemplo, MBR streaming conjuntos que foram codificados localmente).

### <a name="checking-job-progress"></a>Verificando o andamento do trabalho

O progresso e o estado de trabalhos podem ser obtidos pelo monitoramento de eventos com a Grade de Eventos do Azure. Para obter mais informações, consulte [Monitorar eventos usando EventGrid](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Atualizar trabalhos

A operação de atualização na entidade [Trabalho](https://docs.microsoft.com/rest/api/media/jobs) pode ser usada para modificar as propriedades *description* e *priority* depois que o trabalho é enviado. Uma alteração na propriedade *priority* só será eficaz se o trabalho ainda estiver na fila. Se o trabalho tiver iniciado o processamento ou tiver sido concluído, a alteração da prioridade não terá qualquer efeito.

### <a name="job-object-diagram"></a>Diagrama de objeto de trabalho

O diagrama a seguir mostra a **trabalho** objeto e os objetos que ela faz referência a incluindo as relações de derivação.<br/>Clique na imagem para exibi-la em tamanho normal.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

## <a name="configure-media-reserved-units"></a>Configurar unidades reservadas de mídia

Para os trabalhos de análise de áudio e análise de vídeo acionados pelo Serviços de Mídia do Microsoft Azure v3 ou pelo Video Indexer, é altamente recomendável provisionar sua conta com 10 MRUs (Unidades reservadas de mídia) do S3. Se você precisar de mais de 10 MRUs do S3, abra um ticket de suporte usando o [Portal do Microsoft Azure](https://portal.azure.com/).

Para obter detalhes, confira [Dimensionar o processamento de mídia com a CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Consulte também

* [Códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtragem, ordenação, paginação de entidades de serviços de mídia](entities-overview.md)

## <a name="next-steps"></a>Próximas etapas

- Antes de começar a desenvolver, examine [desenvolver com APIs dos serviços de mídia v3](media-services-apis-overview.md) (inclui informações sobre como acessar as APIs, as convenções de nomenclatura, etc.)
- Confira estes tutoriais:

    - [Tutorial: Carregar, codificar e transmitir vídeos usando .NET](stream-files-tutorial-with-api.md)
    - [Tutorial: Analisar vídeos com os Serviços de Mídia v3 usando .NET](analyze-videos-tutorial-with-api.md)
