---
title: Copiar dados do Google Cloud Storage utilizando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como copiar dados do Google Cloud Storage para armazenamentos de dados de coletor com suporte por meio do Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9c9a4b41dbb9b9acc2982ae2af1f3a611f9d3beb
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228282"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Copiar dados do Google Cloud Storage utilizando o Azure Data Factory

Este artigo descreve como copiar dados do Google Cloud Storage. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do Google Cloud Storage tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [suporte para a matriz de origem/coletor](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Especificamente, este conector do Google Cloud Storage dá suporte à cópia de arquivos no estado em que se encontram ou à análise de arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>A cópia de dados do Google Cloud Storage aproveita o [conector do Amazon S3](connector-amazon-simple-storage-service.md) com o ponto de extremidade S3 personalizado correspondente, pois o Google Cloud Storage fornece interoperabilidade compatível com S3.

## <a name="required-permissions"></a>Permissões necessárias

Para copiar dados do Google Cloud Storage, verifique se você recebeu as permissões a seguir:

- **Para a execução de atividade de cópia:**: `s3:GetObject` e `s3:GetObjectVersion` para operações de objeto.
- **Para a criação de GUI do Data Factory**: `s3:ListAllMyBuckets` e `s3:ListBucket`/`s3:GetBucketLocation` para permissões de operações de Bucket são necessárias para operações como testar a conexão e procurar/navegar em caminhos de arquivo. Se você não quiser conceder essa permissão, ignore a conexão de teste na página de criação de serviços vinculados e especifique o caminho diretamente nas configurações do conjunto de dados.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Google Cloud Storage.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Google Cloud Storage:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AmazonS3**. | Sim |
| accessKeyId | ID da chave de acesso secreta. Para localizar a chave de acesso e o segredo, acesse **Google Cloud Storage** > **Configurações** > **Interoperabilidade**. |Sim |
| secretAccessKey | A chave de acesso do secreta em si. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| serviceUrl | Especifique o ponto de extremidade S3 personalizado como **`https://storage.googleapis.com`**. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

Veja um exemplo:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

- Para **Parquet e formato de texto delimitado**, consulte [conjunto de dados de formato de Parquet e texto delimitado](#parquet-and-delimited-text-format-dataset) seção.
- Para outros formatos, como **formato ORC/Avro/JSON/binário**, consulte [outro conjunto de dados do formato](#other-format-dataset) seção.

### <a name="parquet-and-delimited-text-format-dataset"></a>Conjunto de dados de formato de parquet e texto delimitado

Para copiar dados do Google Cloud Storage no **Parquet ou o formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo no conjunto de dados com base no formato e configurações com suporte. As propriedades a seguir têm suporte para o armazenamento de nuvem do Google em `location` configurações no conjunto de dados com base no formato:

| Propriedade   | DESCRIÇÃO                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade type sob `location` no conjunto de dados deve ser definida como **AmazonS3Location**. | Sim      |
| bucketName | O nome do bucket S3.                                          | Sim      |
| folderPath | O caminho para a pasta em determinada partição. Se você quiser usar o caractere curinga para a pasta de filtro, ignore essa configuração e especificar nas configurações de fonte da atividade. | Não        |
| fileName   | O nome do arquivo sob o determinado bucket + folderPath. Se você quiser usar o caractere curinga para filtrar arquivos, ignore essa configuração e especificar nas configurações de fonte da atividade. | Não       |

> [!NOTE]
> **AmazonS3Object** ainda há suporte para o tipo de conjunto de dados com formato de texto/Parquet mencionado na próxima seção como-é para a atividade de pesquisa/cópia/GetMetadata para compatibilidade com versões anteriores. São sugeridas para usar esse novo modelo no futuro, e o ADF criação da interface do usuário foi alternada para gerar esses novos tipos.

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Outro conjunto de dados de formato

Para copiar dados do Google Cloud Storage no **formato ORC/Avro/JSON/binário**, as propriedades a seguir têm suporte:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **AmazonS3Object** |Sim |
| bucketName | O nome do bucket S3. O filtro curinga não é suportado. |Sim para atividade de cópia/pesquisa, não para a atividade GetMetadata |
| key | O **filtro de nome ou curinga** da chave do objeto S3 sob o bucket especificado. Aplica-se apenas quando a propriedade "prefix" não é especificada. <br/><br/>Há suporte ao filtro curinga para a parte da pasta e para a parte do nome do arquivo. Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"key": "rootfolder/subfolder/*.csv"`<br/>– Exemplo 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). Use `^` como escape se o nome real da pasta/arquivo tiver curingas ou esse caractere de escape. |Não  |
| prefix | Prefixo da chave do objeto S3. Objetos cujas chaves começam com esse prefixo serão selecionados. Aplica-se apenas quando a propriedade "chave" não é especificada. |Não  |
| version | A versão do objeto S3 se o controle de versão do S3 está habilitado. |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não  |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não  |
| format | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quer analisar ou gerar arquivos com um formato específico, os seguintes tipos de formato de arquivo são compatíveis: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato Json](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compression | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Níveis compatíveis são: **Ideal** e **Mais Rápido**. |Não  |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **bucketName** para bucket e **prefixo** para a parte da pasta.<br>Para copiar um único arquivo com um determinado nome, especifique **bucketName** para bucket e **chave** para o nome da parte mais o arquivo de pasta.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **bucketName** para bucket e **chave** para o nome da parte mais o arquivo de pasta.

**Exemplo: usando o prefixo**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção apresenta uma lista das propriedades com suporte da fonte do Google Cloud Storage.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage como fonte

- Para copiar de **Parquet e formato de texto delimitado**, consulte [Parquet e a origem do formato de texto delimitado](#parquet-and-delimited-text-format-source) seção.
- Para cópia de outros formatos, como **formato ORC/Avro/JSON/binário**, consulte [outra fonte de formato](#other-format-source) seção.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet e a origem do formato de texto delimitado

Para copiar dados do Google Cloud Storage no **Parquet ou o formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo sobre a atividade de cópia baseada em formato código-fonte e configurações com suporte. As propriedades a seguir têm suporte para o armazenamento de nuvem do Google em `storeSettings` as configurações na fonte de cópia com base no formato:

| Propriedade                 | DESCRIÇÃO                                                  | Obrigatório                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | A propriedade type sob `storeSettings` deve ser definida como **AmazonS3ReadSetting**. | Sim                                                         |
| recursive                | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando recursiva é definida como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não                                                           |
| prefix                   | Prefixo para a chave do objeto S3 em determinado bucket configurado no conjunto de dados para filtrar objetos de origem. Objetos cujas chaves começam com esse prefixo serão selecionados. Aplica-se somente quando `wildcardFolderPath` e `wildcardFileName` propriedades não forem especificadas. |                                                             |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga em um determinado bucket configurado no conjunto de dados para pastas de código-fonte do filtro. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                                           |
| wildcardFileName         | O nome de arquivo com caracteres curinga na determinado bucket + folderPath/wildcardFolderPath para filtrar arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape.  Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Se Sim `fileName` no conjunto de dados e `prefix` não forem especificadas |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora. | Não                                                           |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                                           |
| maxConcurrentConnections | O número das conexões para se conectar ao repositório de armazenamento simultaneamente. Especifique somente quando você quiser limitar a conexão simultâneo ao armazenamento de dados. | Não                                                          |

> [!NOTE]
> Para o formato de texto delimitado por Parquet /, **FileSystemSource** ainda tem suporte como origem de atividade de cópia de tipo mencionada na próxima seção-é para compatibilidade com versões anteriores. São sugeridas para usar esse novo modelo no futuro, e o ADF criação da interface do usuário foi alternada para gerar esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Outra fonte de formato

Para copiar dados do Google Cloud Storage no **formato ORC/Avro/JSON/binário**, as propriedades a seguir têm suporte na atividade de cópia **origem** seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **FileSystemSource** |Sim |
| recursive | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observe que quando o recursivo estiver definido como verdadeiro e o coletor for um armazenamento baseado em arquivo, subpasta/pasta vazia não será copiada/criada no coletor.<br/>Os valores permitidos são: **true** (padrão), **false** | Não  |
| maxConcurrentConnections | O número das conexões para se conectar ao repositório de armazenamento simultaneamente. Especifique somente quando você quiser limitar a conexão simultâneo ao armazenamento de dados. | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e do nome de arquivo com filtros curinga.

| bucket | key | recursive | Estrutura da pasta de origem e resultado do filtro (os arquivos em negrito são recuperados)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | falso | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| bucket | `Folder*/*` | verdadeiro | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| bucket | `Folder*/*.csv` | falso | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| bucket | `Folder*/*.csv` | verdadeiro | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados que têm suporte como origens e coletores na atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
