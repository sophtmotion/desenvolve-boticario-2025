# Olá, pessoal!
Nossa missão é deixar de ser apenas estudantes de SQL para nos tornarmos analistas de dados em uma missão real. O cliente é a Livraria DevSaber, uma nova loja online que fez suas primeiras vendas e, até agora, anotou tudo em uma planilha. Isso é um começo, mas para crescer, eles precisam de insights.
Nosso desafio é transformar essa planilha em um mini data warehouse inteligente no Google BigQuery. Vamos construir todo o pipeline de dados: desde criar a estrutura, carregar os dados até extrair as respostas que ajudarão a livraria a entender seus negócios.
No final, todas as perguntas deverão ser documentadas e respondidas neste README.md.

# 1. Introdução: A Missão da Livraria DevSaber
## Por que uma planilha não é ideal para uma empresa que quer analisar vendas a fundo?
Uma planilha é ótima para começar, mas tem sérias limitações para uma empresa que deseja crescer:

Limite de Dados:
E se a livraria fizer 1 milhão de vendas? A maioria das planilhas não consegue lidar com esse volume. Elas ficam lentas, travam ou nem abrem. Já um banco de dados como o BigQuery foi feito para processar volumes massivos de informação.

Integridade e Padronização:
Em uma planilha, é muito fácil cometer erros de digitação. Um dia você escreve "e-book", no outro "ebook". Isso gera inconsistência. Um banco de dados impõe regras e padronização, garantindo dados confiáveis.

Dificuldade em Análises Complexas:
Cruzar informações de vendas, clientes e estoque em uma planilha é extremamente complicado. Já no SQL, comandos simples como JOIN resolvem esse problema rapidamente.

Automação e Escalabilidade:
Planilhas dependem de atualização manual. Em um pipeline de dados, coleta, carga e transformação podem ser automatizadas, permitindo que a empresa cresça sem perder qualidade de análise.

## Que perguntas o dono da Livraria DevSaber gostaria de responder?
Com os dados de vendas organizados em um data warehouse, o dono pode ir além do simples "quanto vendemos" e explorar questões estratégicas:
Qual é o valor médio por pedido?
Em quais cidades ou estados estão os melhores clientes?
Quem são os clientes que mais compram ou gastam mais?
Quais são os livros mais vendidos e os que quase não vendem?
Houve aumento de vendas após uma campanha de marketing ou em datas especiais (ex.: Dia dos Namorados)?

# 2.Estruturando o Armazenamento: CREATE TABLE no BigQuery
## Com base nos dados brutos, quais outras duas tabelas precisamos criar? Que colunas e tipos de dados elas teriam?
Tabela Produto com as colunas: ID_Produto, Nome_Produto, Categoria_Produto, Preco_Produto 
Tabela Venda com as colunas: ID_Venda, ID_Cliente, ID_Produto, Data_Venda, Quantidade 

## Se o BigQuery não tem chaves estrangeiras, como garantimos que um ID_Cliente na tabela de vendas realmente existe na tabela de clientes?
Resposta: A responsabilidade é nossa, na hora de construir a consulta com o JOIN. Embora o BigQuery não aplique automaticamente a integridade referencial por falta de chaves estrangeiras, pode-se verificar a existência do ID_Cliente usando uma consulta JOIN e, por exemplo, um LEFT JOIN para identificar registos na tabela de vendas que não têm correspondência na tabela de clientes. 

# 3. Ingerindo os Dados: INSERT INTO
## Por que é uma boa prática inserir os clientes e produtos em suas próprias tabelas antes de inserir os dados de vendas?
Para garantir a normalização do banco de dados, evitar a redundância de informações, facilitar a manutenção e a consulta dos dados, e obter uma visão consolidada para análise e tomada de decisão. Ter tabelas separadas permite que as informações sobre clientes (como dados pessoais e histórico) e produtos (como descrições e preços) sejam armazenadas de forma organizada e independente, o que simplifica o processo de adicionar novas vendas e melhora a eficiência do sistema. 
As tabelas de clientes e produtos contêm informações específicas sobre um único "tipo" de dado, o que reduz a repetição e os erros. Já a tabela de Vendas, relaciona informações de clientes e produtos (Um cliente comprou X produto -> uma venda foi feita -> um registro é gerado na tabela de vendas).

## Em um cenário com milhões de vendas por dia, o INSERT INTO seria a melhor abordagem? 
Não, o comando INSERT INTO sozinho provavelmente não seria a melhor abordagem para milhões de vendas por dia. Processos em Batch ou Streaming, usando ETLs e Pub/Sub podem ser mais recomendados nesse caso.

# 4. Análise de Dados: Fazendo Perguntas com SELECT e JOIN

  ## Pergunta 1: Qual o nome dos clientes que moram no estado de 'SP'?
  R: Ana Silva e Carla Dias
  
  <img width="332" height="176" alt="image" src="https://github.com/user-attachments/assets/a793ed21-8bcd-4e70-bfeb-388b215a94c9" />


  ## Pergunta 2: Quais produtos pertencem à categoria 'Ficção Científica'?
  R: O Guia do Mochileiro e Duna
  
  <img width="329" height="176" alt="image" src="https://github.com/user-attachments/assets/ed1b6437-854a-4d28-9f18-618158d8830d" />


  ## Pergunta 3: Listar todas as vendas, mostrando o nome do cliente, o nome do produto e a data da venda. Ordene pela data.
  R:
  Linha	Nome_Cliente	Nome_Produto	Data_Venda
    1	Ana Silva	Fundamentos de SQL	2024-01-15
    2	Bruno Costa	Duna	2024-01-18
    3	Carla Dias	Python para Dados	2024-02-02
    4	Ana Silva	Duna	2024-02-10
    5	Daniel Souza	Fundamentos de SQL	2024-02-20
    6	Bruno Costa	O Guia do Mochileiro	2024-03-05

  <img width="542" height="293" alt="image" src="https://github.com/user-attachments/assets/3d314bdc-c5c2-45e0-bdc0-270d391a82b0" />
  
  ## Pergunta 4: Qual o valor total de cada venda? (quantidade * preço)
  R: Linha	ID_Venda	Valor_Total
      1	      1	      60
      2	      2	      80.5
      3	      3	      150
      4	      4	      80.5
      5	      5	      60
      6	      6	      42
      
  <img width="525" height="248" alt="image" src="https://github.com/user-attachments/assets/1c66a0e5-e36a-4e76-90b3-1ae46726bc6b" />

  ## Pergunta 5: Qual o produto mais vendido em termos de quantidade?
  R: Python para Dados com 2 produtos
  
  <img width="551" height="185" alt="image" src="https://github.com/user-attachments/assets/8001ad13-6cd9-4293-876f-26ebad6ae900" />

Ao responder a essas perguntas com dados, o dono da livraria pode tomar decisões mais inteligentes, como criar promoções direcionadas, otimizar o estoque e entender o comportamento de seus clientes.

# 5. Automação e Reuso: Criando uma VIEW
## Qual é a principal vantagem de usar uma VIEW em vez de simplesmente salvar o código em um arquivo de texto?
A principal vantagem de usar uma VIEW é a capacidade de simplificar consultas complexas e fornecer uma camada de abstração e segurança sobre os dados, permitindo que os desenvolvedores reutilizem o mesmo código, organizem o acesso aos dados e mascarem a complexidade do banco de dados subjacente sem precisar reescrever a consulta completa sempre que necessário. 

## Se o preço de um produto mudar na tabela Produtos, o Valor_Total na VIEW será atualizado automaticamente na próxima vez que a consultarmos?
Sim, o Valor_Total numa vista será atualizado automaticamente na próxima vez que a consultar, pois as views no BigQuery são "consultas armazenadas" que executam a consulta subjacente a cada vez que são acedidas.

# Pipeline de Dados — Livraria DevSaber

Projeto em grupo desenvolvido no Bootcamp de Engenharia de Dados do Grupo Boticário. Objetivo: construir um pipeline de dados fim a fim — desde a criação da estrutura e carregamento dos dados até a criação de views e consultas analíticas que ajudem a livraria a entender seu negócio. Todas as consultas foram geradas e executadas no BigQuery (Standard SQL).

## Sumário
- Contexto e objetivos
- Contribuição
- Equipe

## Contexto e objetivos
O pipeline atende perguntas de negócio típicas de uma livraria, como:
- Evolução de vendas por período
- Ticket médio por cliente e por categoria
- Categorias mais vendidas
- Recorrência de clientes

Resultados são materializados em views e/ou materialized views no BigQuery, prontas para consumo analítico.

## Contribuição
- Fluxo com branches por feature e Pull Requests.
- Commits pequenos e descritivos.
- Revisão obrigatória por pelo menos uma pessoa do time.
- Documentar mudanças relevantes em `docs/` e atualizar o README quando necessário.

## Equipe
Projeto em grupo — Bootcamp de Engenharia de Dados do Grupo Boticário.
- Luana Silva - https://github.com/LuanaTechVet
- Sophia de Jong - https://github.com/sophtmotion
- [Nome 3]
- [Nome 4]


--- 

Este repositório foi pensado para começar enxuto e permitir evolução gradual (ex.: migrações automatizadas, materialized views, e camadas adicionais de qualidade) sem refatorações extensas.
