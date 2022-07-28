# Pipeline de dados do Telegram

Este projeto tem como objetivo criar um pipeline de dados do Telegram. A ideia é criar uma série de etapas que vão desde a ingestão dos dados, passando pelo processamento, e chegando ao destino final em que os dados processados ficarão disponíveis para análise.

O pipeline possui as seguintes etapas:

*   Ingestão
*   Wrangling
*   Apresentação

![Arquitetura](https://github.com/cmpbj/data-pipeline-telegram/blob/main/doc/arch.png?raw=true)

* O Telegram representa a fonte de dados transacionais. As mensagens enviadas por usuários em um grupo serão capturadas pelo bot e redirecionadas via webhook para o AWS API GATEWAY. 

* Ingestão: As requisições HTTP contendo o conteúdo de cada mensagem vai ser recebida pela API GATEWAY e redirecionada para o AWS LAMBDA. Nessa parte, a função criada no LAMBDA vai salvar o conteúdo de cada mensagem no formato original e armazenar esses arquivos no AWS S3 particionado por dia.

* Wrangling: Uma vez por dia, o gatilho criado no AWS EVENT BRIDGE vai acionar uma outra função do LAMBDA que vai processar as mensagens do dia anterior, denormalizando os dados e salvando o conteúdo no formado Apache Parquet no AWS S3 particionado por dia

* Apresentação: Por fim, após o processamento dos dados, estes ficarão disponíveis no S3 e prontos para serem apresentados através de uma tabela criada no AWS ATHENA. Neste ponto, é possível criar consultas analíticas na tabela através do SQL. 