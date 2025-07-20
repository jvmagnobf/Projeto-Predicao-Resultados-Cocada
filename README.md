# Relatório do Projeto: Algoritmo de Previsão de Resultados do Campeonato Brasileiro

**Aluno:** João Victor Magno
**DRE:** 123711443

---

### **1. Introdução**

Este relatório detalha o projeto de desenvolvimento de um algoritmo para prever os resultados de partidas de futebol do Campeonato Brasileiro. O objetivo central foi utilizar dados estatísticos históricos das partidas para construir um modelo matemático capaz de estimar o saldo de gols do mandante em um confronto, indicando assim o provável vencedor e por quantos gols ele ganhou. A metodologia empregada foi o método de Mínimos Quadrados, uma técnica de análise de regressão para encontrar a linha de melhor ajuste para um conjunto de dados.

### **2. Coleta e Preparação dos Dados**

O projeto iniciou-se com a utilização de dois arquivos de dados distintos:
* Um contendo informações gerais das partidas desde 2003, como data, times (mandante e visitante) e o placar final.
* Outro contendo estatísticas detalhadas de cada time por partida, como chutes, posse de bola, número de passes, faltas, cartões, entre outros.

Primeiramente eu carreguei os dois datasets em data frames para uma melhor vizualização e manipulação dos dados. O segundo passo consistiu em unificar essas duas fontes de dados, pois as estatísticas dos times estavam em datasets diferentes das informações da partida. Assim, após manipularmos os dataframes, foi criado um único conjunto de dados onde cada linha representava uma partida e continha tanto as informações gerais quanto as estatísticas de desempenho do time mandante e do time visitante, devidamente identificadas. Isso foi realizado de maneira que primeiro criei um data frame somente com as estatísticas dos mandantes por partida e alterei o nome das colunas desse data frame de maneira que, se antes era `chutes`, agora se torna `m_chutes`. Também criei um data frame para os visitantes da mesma maneira que fiz para os mandantes, no qual de `chutes` se torna `v_chutes`. Posteriormente juntei esses dois data frames com o data frame que continha as informações das partidas.

Posteriormente, foi realizada uma etapa crucial de limpeza e filtragem. Como muitas partidas antigas no banco de dados não possuíam estatísticas completas, foi necessário remover os registros com dados ausentes para garantir a consistência do modelo, assim reduziu bastante a quantidade dos dados mas garante a qualidade deles. Foi retirado as colunas que não agregavam valor ao método (ex: Nome do Técnico, Nome do Estádio), para se manter somente as colunas relevantes para a melhor aplicação do método. As colunas de posse de bola e precisão de passes, que estavam em formato de texto (ex: "78%"), foram convertidas para valores numéricos para possibilitar o cálculo matemático. Percebi que mesmo após essas mudanças existiam várias partidas que não continham a precisão do passe, então criei dois banco de partidas, um com precisão de passe e outro sem. Ao final deste processo, obtive dois banco de dados prontos para a modelagem.  Porém, para o projeto eu utilizei somente o que não contém a precisão de passe pois ele tem mais partidas a serem analisadas.

### **3. Metodologia: O Método de Mínimos Quadrados**

O núcleo do projeto foi a aplicação do método de Mínimos Quadrados para criar a "máquina de predição". A abordagem foi a seguinte:

1.  **Definição do Vetor de Resultados (vetor b):** O resultado a ser previsto foi definido como o **saldo de gols do time mandante** (gols do mandante - gols do visitante). Para cada partida no banco de dados, esse valor foi calculado, formando um vetor de resultados.

2.  **Definição da Matriz de Estatísticas (Matriz A):** As estatísticas de todas as partidas (chutes do mandante, posse de bola do visitante, faltas do mandante, etc.) foram organizadas em uma grande matriz. Cada linha dessa matriz correspondia a uma partida e cada coluna a uma estatística específica. Para essa matriz foram retiradas as colunas de gols do mandante e do visitante, pois é isso que buscamos.

3.  **Criação do Modelo:** O método de Mínimos Quadrados foi utilizado para resolver o sistema $Ax = b$. O objetivo é encontrar o vetor `x` (aqui chamado de `m_predicao`), que representa os **pesos** ou a **influência** de cada estatística no saldo de gols final. Por exemplo, o modelo calcula o quão mais importante é um "chute no alvo" em comparação com a "posse de bola" para determinar a vitória de um time da casa.

Foram criados dois modelos distintos para análise comparativa:
* **Modelo Específico:** Treinado apenas com dados de confrontos entre time1 e time2, ou confrontos de um ano especifico, ou somente confrontos que um determinado time participe (definidos de acordo com o que o usuário quer analisar), criando uma máquina de predição especializada para este filtro.
* **Modelo Geral:** Treinado com todas as partidas disponíveis no banco de dados tratado.

### **4. Análise e Resultados**

Para testar a eficácia dos modelos, foi selecionada uma partida real do banco de dados para a qual já se conhecia o resultado. As estatísticas desta partida foram usadas como entrada para as máquinas de predição.

* **Predição com o Modelo Específico:** Ao usar o modelo treinado apenas com jogos entre Vasco e Botafogo-RJ, a previsão para o saldo de gols do mandante foi de aproximadamente **1.0**. O saldo de gols real daquela partida era **1**. Isso demonstra uma alta precisão do modelo especializado.

* **Predição com o Modelo Geral:** Usando o mesmo jogo, o modelo geral previu um saldo de gols de **-0.06**, um resultado que não correspondeu à realidade do confronto específico.

Adicionalmente, o projeto calculou as estatísticas de uma "partida média", tanto para o filtro específico quanto para o campeonato em geral. Ao aplicar os modelos a essas partidas médias, observou-se que o saldo de gols previsto para o time da casa era positivo em ambos os cenários (0.75 para o modelo específico e 0.45 para o modelo geral), confirmando matematicamente a conhecida vantagem do fator casa no futebol.

### **5. Conclusão**

O projeto demonstrou com sucesso a viabilidade de se utilizar o método de Mínimos Quadrados para criar um modelo preditivo para resultados de futebol, baseado em estatísticas de jogo. A análise revelou que, embora um modelo geral possa capturar tendências amplas do campeonato, um modelo treinado com dados específicos de um confronto direto é significativamente mais preciso para prever o resultado desse mesmo confronto. O trabalho estabelece uma base sólida para análises estatísticas esportivas, mostrando como a álgebra linear pode ser aplicada para extrair insights valiosos de dados esportivos.