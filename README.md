# Pipeline de dados do Telegram

Este projeto tem como objetivo criar um pipeline de dados do Telegram. A ideia é criar uma série de etapas que vão desde a ingestão dos dados, passando pelo processamento, e chegando ao destino final em que os dados processados ficarão disponíveis para análise.

O pipeline possui as seguintes etapas:

*   Ingestão
*   Wrangling
*   Apresentação

No caso, a ideia é extrair mensagens de pessoas que participam de um grupo no Telegram através da utilização de um **chatbot**, que é um tipo de software que interage com usuários através de conversas automatizadas em plataformas de mensagens, como é o caso do Telegram. Um possível uso desse tipo de aplicação é no atendimento ao cliente, em que o **bot** ajuda a resolver alguns problemas ou esclarecer dúvidas recorrentes antes que um atendimento humano seja acionado.

A primeira etapa do projeto, então, é a criação do **bot** e adcioná-lo ao grupo criado no Telegram. O **bot** vai captar todas as mensagens enviadas no grupo, que podem ser acessadas através da API de bots do Telegram (veja a documentação neste ![link](https://core.telegram.org/bots/api)).

![Arquitetura](https://github.com/cmpbj/data-pipeline-telegram/blob/main/doc/arch.png?raw=true)

* O Telegram representa a fonte de dados transacionais. As mensagens enviadas por usuários em um grupo serão capturadas pelo bot e redirecionadas via webhook para o AWS API GATEWAY. 

* Ingestão: As requisições HTTP contendo o conteúdo de cada mensagem vai ser recebida pela API GATEWAY e redirecionada para o AWS LAMBDA. Nessa parte, a função criada no LAMBDA vai salvar o conteúdo de cada mensagem em seu formato original (JSON) e armazenar esses arquivos no AWS S3 (*raw*), particionado por dia.

* Wrangling: Uma vez por dia, o gatilho criado no AWS EVENT BRIDGE vai acionar uma outra função do LAMBDA que vai processar as mensagens do dia anterior, denormalizando os dados e salvando o conteúdo no formado Apache Parquet em outro bucket do AWS S3 (*enriched*), também particionado por dia

* Apresentação: Por fim, após o processamento dos dados, estes ficarão disponíveis no S3 e prontos para serem apresentados através de uma tabela criada no AWS ATHENA. Neste ponto, é possível criar consultas analíticas na tabela através do SQL. 
