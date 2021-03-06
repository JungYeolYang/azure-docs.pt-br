---
title: Desenvolver com APIs v3 – Azure | Microsoft Docs
description: Este artigo discute as regras que se aplicam às entidades e APIs ao desenvolver com os serviços de mídia v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3ce20b56fc2cbebbed4b525eeccc2c12d14cccc3
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65556240"
---
# <a name="developing-with-media-services-v3-apis"></a>Desenvolver com os serviços de mídia APIs v3

Como desenvolvedor, você pode usar a [API REST](https://aka.ms/ams-v3-rest-ref) dos Serviços de Mídia ou bibliotecas de clientes que permitem interagir com a API REST para criar, gerenciar e manter fluxos de trabalho de mídia personalizados com facilidade. A API dos [Serviços de Mídia v3](https://aka.ms/ams-v3-rest-sdk) se baseia na especificação do OpenAPI (anteriormente conhecida como um Swagger).

Este artigo discute as regras que se aplicam às entidades e APIs ao desenvolver com os serviços de mídia v3.

## <a name="accessing-the-azure-media-services-api"></a>Acessando a API de serviços de mídia do Azure

Para estar autorizado a acessar os recursos e a API dos Serviços de Mídia, primeiro você deve ser autenticado. Dá suporte a serviços de mídia [Azure Active Directory (AD do Azure)-com base em](../../active-directory/fundamentals/active-directory-whatis.md) autenticação. Duas opções de autenticação comuns são:
 
* **Autenticação de entidade de serviço** - usado para autenticar um serviço (por exemplo: aplicativos web, aplicativos de funções, aplicativos lógicos, API e microsserviços). Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços daemon, serviços de camada intermediária ou trabalhos agendados. Por exemplo, para Web há aplicativos sempre devem ser uma camada intermediária que se conecta aos serviços de mídia com uma entidade de serviço.
* **Autenticação de usuário** - usado para autenticar uma pessoa que está usando o aplicativo para interagir com os recursos de serviços de mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais do usuário. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo.

A API de serviços de mídia requer que o usuário ou aplicativo que faz a API REST solicitações têm acesso ao recurso da conta de serviços de mídia e usar um **Colaborador** ou **proprietário** função. A API pode ser acessada com o **Reader** função, mas apenas **obter** ou **lista**   operações estarão disponíveis. Para obter mais informações, consulte [controle de acesso baseado em função para contas de serviços de mídia](rbac-overview.md).

Em vez de criar uma entidade de serviço, considere o uso de identidades gerenciadas para recursos do Azure para acessar a API de serviços de mídia por meio do Azure Resource Manager. Para saber mais sobre identidades gerenciadas para recursos do Azure, consulte [What ' s identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Entidade de serviço do Azure AD 

Se você estiver criando um aplicativo do Azure AD e o serviço principal, o aplicativo deve ser no seu próprio locatário. Depois de criar o aplicativo, dê o aplicativo **Colaborador** ou **proprietário** acesso da função para a conta de serviços de mídia. 

Se você não tiver certeza se você tem permissões para criar um aplicativo do Azure AD, consulte [permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na figura a seguir, os números representam o fluxo das solicitações em ordem cronológica:

![Aplicativos de camada intermediária](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Um aplicativo de camada intermediária solicita um token de acesso do AD do Azure que tem os seguintes parâmetros:  

   * Ponto de extremidade do locatário do Azure AD.
   * URI de recurso dos Serviços de Mídia.
   * URI de recurso dos Serviços de Mídia REST.
   * Valores do aplicativo do Azure AD: a ID do cliente e o segredo do cliente.
   
   Para obter todos os valores necessários, consulte [acesso API de serviços de mídia do Azure com a CLI do Azure](access-api-cli-how-to.md)

2. O token de acesso do Azure AD é enviado à camada intermediária.
4. A camada intermediária envia a solicitação à API REST da Mídia do Azure com o token do Azure AD.
5. A camada intermediária obtém novamente os dados dos Serviços de Mídia.

### <a name="samples"></a>Amostras

Consulte os seguintes exemplos que mostram como se conectar com a entidade de serviço do Azure AD:

* [Conecte-se com REST](media-rest-apis-with-postman.md)  
* [Conectar-se com Java](configure-connect-java-howto.md)
* [Conectar-se com .NET](configure-connect-dotnet-howto.md)
* [Conectar-se com Node.js](configure-connect-nodejs-howto.md)
* [Conectar-se com Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Convenções de nomenclatura

Nomes de recursos do Serviços de Mídia do Azure v3 (por exemplo, ativos, trabalhos, transformações) estão sujeitos a restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes dos recursos são sempre exclusivos. Desse modo, é possível usar qualquer cadeia de caracteres de identificador exclusivo (por exemplo, GUIDs) para os nomes dos recursos. 

Nomes de recurso dos Serviços de Mídia não podem incluir: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', o caractere de aspas simples ou quaisquer caracteres de controle. Todos os outros caracteres são permitidos. O comprimento máximo de um nome de recurso é de 260 caracteres. 

Para obter mais informações sobre a atribuição de nome do Azure Resource Manager, confira: [Requisitos de nomenclatura](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenções de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Operações de longa execução

As operações marcadas com `x-ms-long-running-operation` nos serviços de mídia do Azure [swagger arquivos](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) são longas operações de execução. 

Para obter detalhes sobre como controlar operações assíncronas no Azure, consulte [operações assíncronas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Os serviços de mídia tem as seguintes operações de longa execução:

* Criar LiveEvent
* Atualizar LiveEvent
* Delete LiveEvent
* Iniciar LiveEvent
* Parar LiveEvent
* Reset LiveEvent
* Criar LiveOutput
* Delete LiveOutput
* Criar StreamingEndpoint
* Atualizar StreamingEndpoint
* Excluir StreamingEndpoint
* Iniciar StreamingEndpoint
* Parar um StreamingEndpoint
* Escala StreamingEndpoint


## <a name="sdks"></a>SDKs

> [!NOTE]
> Os SDKs dos Serviços de Mídia do Azure v3 não têm garantia de serem thread-safe. Ao desenvolver um aplicativo com multi-thread, você deverá adicionar sua própria lógica de sincronização de thread para proteger o cliente ou usar um novo objeto AzureMediaServicesClient por thread. Você também deve tomar cuidado com problemas de multi-threading introduzidos por objetos opcionais fornecidos pelo código ao cliente (como uma instância do HttpClient no .NET).

|SDK|Referência|
|---|---|
|[SDK .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Referência do .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Referência de Java](https://aka.ms/ams-v3-java-ref)|
|[SDK do Python](https://aka.ms/ams-v3-python-sdk)|[Referência do Python](https://aka.ms/ams-v3-python-ref)|
|[SDK do Node.js](https://aka.ms/ams-v3-nodejs-sdk) |[Referência do Node.js](https://aka.ms/ams-v3-nodejs-ref)| 
|[SDK do Go](https://aka.ms/ams-v3-go-sdk) |[Referência do Go](https://aka.ms/ams-v3-go-ref)|
|[SDK do Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Consulte também

- [SDK do .NET EventGrid que inclui eventos de serviço de mídia](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições de eventos de Serviços de Mídia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Gerenciador dos Serviços de Mídia do Azure

O [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) (Gerenciador dos Serviços de Mídia do Azure) é uma ferramenta disponível para clientes do Windows que desejam saber mais sobre os Serviços de Mídia. O AMSE é um aplicativo do WinForms/C# que carrega, baixa, codifica e transmite VoD e conteúdo ao vivo com os Serviços de Mídia. A ferramenta AMSE destina-se aos clientes que desejam testar os Serviços de Mídia sem escrever nenhum código. O código AMSE é fornecido como um recurso para clientes que desejam desenvolver com os Serviços de Mídia.

O AMSE é um projeto de software livre, cujo suporte é fornecido pela comunidade (os problemas podem ser relatados a https://github.com/Azure/Azure-Media-Services-Explorer/issues). Este projeto adotou o [Código de Conduta Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Para obter mais informações, confira as [Perguntas frequentes sobre o Código de Conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou contate opencode@microsoft.com para enviar outras perguntas ou comentários.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, classificação, paginação de entidades dos Serviços de Mídia

Consulte [filtragem, ordenação, paginação de entidades de serviços de mídia do Azure](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se aos serviços de mídia com Java](configure-connect-java-howto.md)
* [Conectar-se aos serviços de mídia com .NET](configure-connect-dotnet-howto.md)
* [Conectar-se aos serviços de mídia com Node. js](configure-connect-nodejs-howto.md)
* [Conectar-se aos serviços de mídia com o Python](configure-connect-python-howto.md)
