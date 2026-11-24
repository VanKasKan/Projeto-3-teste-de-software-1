# Critérios de teste — poquer

> Orientação: As linhas preenchidas usam um pedido fictício e servem para testar a montagem dos prompts. Substituam todas essas linhas pela análise da funcionalidade deste arquivo antes de preparar o plano oficial. Acrescentem uma linha por classe, fronteira, regra ou obrigação de cobertura. Se um critério não se aplicar, substituam sua tabela por uma justificativa breve.

> Orientação: Registrem somente os critérios e seus resultados de análise. A LLM escolhe os valores de entrada para cada caso, determina o resultado esperado e escreve os testes JUnit.

### Análise caixa-preta do grupo

> Orientação: Usem a especificação como fonte. Considerem a composição da mão, os naipes, as repetições e as posições permitidas do ás.

#### Classes de equivalência

> Orientação: Informem o parâmetro ou regra, deem um nome à classe e descrevam a faixa ou propriedade que inclui seus membros. Registrem classes válidas e inválidas quando previstas.
definição: sequência refere-se 

| Parâmetro ou regra | Classe de equivalência | Domínio ou condição |
|---|---|---|
| Lista com valores das cartas | Formato válido | Lista com exatamente 5 elementos do tipo ValorCarta|
| Lista com valores das cartas | Formato inválido gera IllegalArgumentException | Lista de tamanho diferente de 5 |
| Lista com valores das cartas | Formato inválido gera NullPointerException | Lista Nula ou com elemento Nulo |
| Lista com naipes das cartas | Formato válido | Lista com exatamente 5 elementos do tipo Naipe|
| Lista com naipes das cartas | Formato inválido gera IllegalArgumentException | Lista de tamanho diferente de 5 |
| Lista com naipes das cartas | Formato inválido gera NullPointerException | Lista Nula ou com elemento Nulo |
| Pares (valor, naipe) | Pares válidos | Cada par é único |
| Pares (valor, naipe) | Pares inválidos geram IllegalArgumentException | Existe ao menos 2 pares iguais |
| Sequência | É sequência | 5 cartas de valores consecutivos conforme a ordem `AS`, `DOIS`, `TRES`, `QUATRO`, `CINCO`, `SEIS`, `SETE`, `OITO`, `NOVE`, `DEZ`, `VALETE`, `DAMA` e `REI`|
| Sequência | É sequência | `DEZ`, `VALETE`, `DAMA`, `REI` e `AS` |
| Sequência | Não é sequência | Demais combinações de `AS`, `DOIS`, `TRES`, `QUATRO`, `CINCO`, `SEIS`, `SETE`, `OITO`, `NOVE`, `DEZ`, `VALETE`, `DAMA` e `REI` não inclusas em `É sequência` |
| Sequência de cartas |`SEQUENCIA_REAL_DO_MESMO_NAIPE` | mão possuí 10–J–Q–K–A do mesmo naipe |
| Sequência de cartas |`SEQUENCIA_DO_MESMO_NAIPE` | mão possuí sequência do mesmo naipe E NÃO É `SEQUENCIA_REAL_DO_MESMO_NAIPE` |
| Sequência de cartas |`QUADRA` |  mão possui quatro cartas do mesmo valor E NÃO É `SEQUENCIA_DO_MESMO_NAIPE` | 
| Sequência de cartas |`TRINCA_E_PAR` |  mão possui três cartas de um valor e duas de outro valor E NÃO É `QUADRA` |
| Sequência de cartas |`CINCO_DO_MESMO_NAIPE` |  mão possui cinco cartas do mesmo naipe sem sequência E NÃO É `TRINCA_E_PAR` |
| Sequência de cartas |`SEQUENCIA` | mão possui sequência com mais de um naipe E NÃO É`CINCO_DO_MESMO_NAIPE` |
| Sequência de cartas |`TRINCA` |  mão possui três cartas do mesmo valor E NÃO É`SEQUENCIA` |
| Sequência de cartas |`DOIS_PARES` |  mão possui dois pares de valores distintos E NÃO É`TRINCA` | 
| Sequência de cartas |`UM_PAR` | mão possui duas cartas do mesmo valor E NÃO É`DOIS_PARES` | 
| Sequência de cartas |`CARTA_ALTA` | se não for 'UM_PAR' |



#### Valores-limite

> Orientação: Indiquem o valor onde muda a validade ou o comportamento e descrevam as classes que ele separa. Esclareçam a qual classe pertence o próprio limite.

| Parâmetro ou regra | Valor-limite | Classes separadas e inclusão do limite |
|---|---|---|
| Lista com valores das cartas | 5 | exatamente 5 é válido, diferente de 5 é inválido|
| Lista com naipes das cartas | 5 | exatamente 5 é válido, diferente de 5 é inválido|
| Pares (valor, naipe) distintos | 5 | exatamente 5 pares únicos é válido, menos de 5 pares únicos é inválido |
| Sequência | 5 | 5 cartas de valores consecutivos é sequência, menos de 5 não é sequência|
| Sequência de cartas :`SEQUENCIA_REAL_DO_MESMO_NAIPE` | 10–J–Q–K–A | mão com 10–J–Q–K–A é `SEQUENCIA_REAL_DO_MESMO_NAIPE`, mão sem 10–J–Q–K–A não é |
| Sequência de cartas :`SEQUENCIA_DO_MESMO_NAIPE` | sequência de 5 cartas de mesmo naipe, 10–J–Q–K–A | sequência de menos de 5 cartas do mesmo naipe não é `SEQUENCIA_DO_MESMO_NAIPE`, sequência de exatamente 5 cartas do mesmo naipe é `SEQUENCIA_DO_MESMO_NAIPE`, 10–J–Q–K–A de mesmo naipe não é `SEQUENCIA_DO_MESMO_NAIPE`|
| Sequência de cartas :`QUADRA` | 4 cartas do mesmo valor / sequencia | mão possuí menos de 4 cartas do mesmo valor não é `QUADRA`, mão possui 4 caras do mesmo valor é `QUADRA`| 
| Sequência de cartas :`TRINCA_E_PAR` | A-A-B-B-B | Seja A, B valores de cartas distintos, A-A-B-B-B é `TRINCA_E_PAR`, A-B-B-B-B, A-C-B-B-B, A-A-C-B-B não é `TRINCA_E_PAR`|
| Sequência de cartas :`CINCO_DO_MESMO_NAIPE` | 5 cartas do mesmo naipe | 5 cartas do mesmo naipe sem sequência e que não é `TRINCA_E_PAR` é `CINCO_DO_MESMO_NAIPE`, menos de 5 cartas do mesmo naipe não é `CINCO_DO_MESMO_NAIPE`  |
| Sequência de cartas :`SEQUENCIA` | 5 cartas consecutivas, 2 naipes | menos de 5 cartas consecutivas com 2 ou mais naipes distintos não é `SEQUENCIA`, exatamente 5 cartas com 2 ou mais naipes distintos é `SEQUENCIA`, exatamente 5 cartas com 1 único naipe não é `SEQUENCIA`|
| Sequência de cartas :`TRINCA` | A-B-C-C-C | Seja A,B,C valores distintos, A-A-C-C-C não é `TRINCA`, A-B-C-C-C é `TRINCA`, A-B-B-C-C não é `TRINCA` |
| Sequência de cartas :`DOIS_PARES` | A-A-B-B-C | Seja A, B, C, D valores distintos A-A-B-B-B não é `DOIS_PARES`, A-A-B-B-C é `DOIS_PARES`, A-A-B-C-D não é `DOIS_PARES`| 
| Sequência de cartas :`UM_PAR` | A-A-B-C-D | Seja A,B,C,D,E valores distintos A-B-C-D-E não é `UM_PAR`, A-A-B-C-D é `UM_PAR`, A-A-B-B-C não é `UM_PAR` | 

#### Tabela de decisão

> Orientação: Uma linha descreve uma combinação geral de condições e a ação que a especificação prevê. Usem estados ou categorias, como condição satisfeita, condição insatisfeita ou indiferente, quando couber.

| Regra | Combinação de condições | Ação prevista na especificação |
|---|---|---|
| DEMO-01 | Quantidade válida e cadastro ativo | Aceitar o pedido |
| EXC-01 | Lista nula ou elemento nulo | retorna NullPointerException |
| EXC-02 | Lista de valores ou naipes tem tamanho diferente de 5 | retorna IllegalArgumentException |
| EXC-03 | Entrada tem 01 par valor,naipe repetido | gera exceção |
| REG-01 | Entrada é 10–J–Q–K–A | classifica como `SEQUENCIA_REAL_DO_MESMO_NAIPE`|
| REG-02 |  mão possuí sequência do mesmo naipe E NÃO É `SEQUENCIA_REAL_DO_MESMO_NAIPE`| classifica como `SEQUENCIA_DO_MESMO_NAIPE` |
| REG-03 | mão possui quatro cartas do mesmo valor E NÃO É `SEQUENCIA_DO_MESMO_NAIPE` | classifica como `QUADRA` |
| REG-04 | mão possui três cartas de um valor e duas de outro valor E NÃO É `QUADRA` | classifica como `TRINCA_E_PAR` |
| REG-05 | mão possui cinco cartas do mesmo naipe sem sequência E NÃO É `TRINCA_E_PAR` | classifica como `CINCO_DO_MESMO_NAIPE` |
| REG-06 | mão possui sequência com mais de um naipe E NÃO É`CINCO_DO_MESMO_NAIPE` | classifica como `SEQUENCIA` |
| REG-07 | mão possui três cartas do mesmo valor E NÃO É`SEQUENCIA`| classifica como `TRINCA` |
| REG-08 | mão possui dois pares de valores distintos E NÃO É`TRINCA` | classifica como `DOIS_PARES` |
| REG-09 | mão possui duas cartas do mesmo valor E NÃO É`DOIS_PARES` | classifica como `UM_PAR`|
| REG-10 | mão não é 'UM_PAR' | classifica como `CARTA_ALTA`| 

### Análise caixa-branca do grupo

> Orientação: Usem o código de produção. Na primeira coluna, indiquem o método e o trecho ou a decisão. Na segunda, descrevam em linguagem natural como a decisão, condição ou caminho funciona. Na terceira, indiquem quais resultados de decisão, ramos ou caminhos a suíte deve exercitar. Se houver curto-circuito, indiquem quando uma condição posterior chega a ser avaliada.

| Método ou trecho | Decisão, condição ou caminho | Obrigação de cobertura |
|---|---|---|
| `validarPedido`, checagem do limite máximo | A decisão verifica se a quantidade excede o limite. | Exercitar os resultados verdadeiro e falso da decisão. |
