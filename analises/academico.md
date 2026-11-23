# Critérios de teste — academico

> Orientação: As linhas preenchidas usam um pedido fictício e servem para testar a montagem dos prompts. Substituam todas essas linhas pela análise da funcionalidade deste arquivo antes de preparar o plano oficial. Acrescentem uma linha por classe, fronteira, regra ou obrigação de cobertura. Se um critério não se aplicar, substituam sua tabela por uma justificativa breve.

> Orientação: Registrem somente os critérios e seus resultados de análise. A LLM escolhe os valores de entrada para cada caso, determina o resultado esperado e escreve os testes JUnit.

### Análise caixa-preta do grupo

> Orientação: Usem a especificação como fonte. Considerem notas, frequência, arredondamento e as regras de classificação da situação acadêmica.

#### Classes de equivalência

> Orientação: Informem o parâmetro ou regra, deem um nome à classe e descrevam a faixa ou propriedade que inclui seus membros. Registrem classes válidas e inválidas quando previstas.

| Parâmetro ou regra | Classe de equivalência | Domínio ou condição |
|---|---|---|
| Frequência| faixa válida | Inteiros de 0 a 100|
| Frequencia| faixa inválida| Inteiros menores que 0 e maiores que 100|
| Notas| Faixa Válida | Float entre 0 e 10 |
| Notas| Faixa Inválida| Float menores que 0 e maiores que 10|
| Notas| Faixa Inválido| Números não finitos|

#### Valores-limite

> Orientação: Indiquem o valor onde muda a validade ou o comportamento e descrevam as classes que ele separa. Esclareçam a qual classe pertence o próprio limite.

| Parâmetro ou regra | Valor-limite | Classes separadas e inclusão do limite |
|---|---|---|
| Frequência| 0 | Inteiros até 0 é válido. Abaixo de 0 é inválido|
| Frequência| 100 | Inteiros até 100 é válido. Acima de 100 é inválido|
| Nota| 0 | Float até 0 é válido. Menor que 0 é inválido|
| Nota| 10 | Float até 10.0 é válido. Maior que 10.0 é inválido|

#### Tabela de decisão

> Orientação: Uma linha descreve uma combinação geral de condições e a ação que a especificação prevê. Usem estados ou categorias, como condição satisfeita, condição insatisfeita ou indiferente, quando couber.

| Regra | Combinação de condições | Ação prevista na especificação |
|---|---|---|
| APR-01|Frequencia >= 75% e Média >= 7 |APR - Aprovado por Média|
| APR-02|Frequencia >= 75% e Média >= 5 e < 7 e todas as notas >= 3 |APMN - Aprovado por Nota|
| REP-01|Frequencia >= 75% e Média <= 3|REP - Reprovado por Média|
| REP-02|Frequencia < 75% e Média >= 3|REPF - Reprovado por Faltas|
| REP-03|Frequencia < 75% e Média < 3|REMF - Reprovado por Média e Faltas|
| REC|Frequencia >= 75% e Média >= 3 e < 5 |REC - Em recuperação|

### Análise caixa-branca do grupo

> Orientação: Usem o código de produção. Na primeira coluna, indiquem o método e o trecho ou a decisão. Na segunda, descrevam em linguagem natural como a decisão, condição ou caminho funciona. Na terceira, indiquem quais resultados de decisão, ramos ou caminhos a suíte deve exercitar. Se houver curto-circuito, indiquem quando uma condição posterior chega a ser avaliada.

| Método ou trecho | Decisão, condição ou caminho | Obrigação de cobertura |
|---|---|---|
| consolidarParcialmente, linha 24| Verifica se o valor da frequÊncia é inválido| Se for inválido, ThrowIllegalArgument. Se não, ignora. |
| consolidarParcialmente, linha 34| Verifica se a média for menor que 3, e se for, verifica se é reprovado por média ou por média e falta| Retorna REMF se por média e falta, REP se por média Se não for menor que 3, ignora.|
| consolidarParcialmente, linha 37| Verifica se a frequencia é menor que 75| Se for menor, retorna REPF, se for maior, ignora|
| consolidarParcialmente, linha 40| Verifica se a média é maior ou igual a 7| Se verdadeiro, retorna APR, se não, ignora|
| consolidarParcialmente, linha 44| Verifica se a média está > 5 e a menor nota é > 3| Se verdadeiro, retorna APRN, se falso, ignora. |
| consolidarParcialmente | Todas as condicionais foram ignoradas | Retorna REC.
| arredondarNota | Verifica se a nota ou média possuem mais que duas casas decimais após a vírgula | Arredonda para cima  para a primeira casa decimal após a vírgula|
