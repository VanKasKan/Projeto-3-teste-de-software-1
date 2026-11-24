# Critérios de teste — aluguel

### Análise caixa-preta do grupo

#### Classes de equivalência

| Parâmetro ou regra | Classe de equivalência | Domínio ou condição |
|---|---|---|
| `categoria` | Categoria econômica (válida) | `ECONOMICO`, diária bruta de R$ 120. |
| `categoria` | Categoria intermediária (válida) | `INTERMEDIARIO`, diária bruta de R$ 180. |
| `categoria` | Categoria SUV (válida) | `SUV`, diária bruta de R$ 280. |
| `categoria` | Categoria ausente (inválida) | `null`. Gera `NullPointerException`. |
| `nivelCliente` | Cliente comum (válida) | `COMUM`. Nunca recebe desconto de fidelidade. |
| `nivelCliente` | Cliente prata (válida) | `PRATA`. Fidelidade de 5% a partir de 7 diárias, se não houver atraso anterior. |
| `nivelCliente` | Cliente ouro (válida) | `OURO`. Fidelidade de 10% a partir de 5 diárias, se não houver atraso anterior. |
| `nivelCliente` | Nível ausente (inválida) | `null`. Gera `NullPointerException`. |
| `seguro` | Sem seguro (válida) | `SEM_SEGURO`, R$ 0 por diária. |
| `seguro` | Seguro básico (válida) | `BASICO`, R$ 25 por diária. |
| `seguro` | Seguro completo (válida) | `COMPLETO`, R$ 45 por diária. |
| `seguro` | Seguro ausente (inválida) | `null`. Gera `NullPointerException`. |
| `numeroDiarias` | Menos de uma diária (inválida) | Inteiros menores ou iguais a 0. Gera `IllegalArgumentException`. |
| `numeroDiarias` | Faixa de 1 a 2 diárias (válida) | Desconto por duração de 0%. Nenhum nível recebe fidelidade. |
| `numeroDiarias` | Faixa de 3 a 4 diárias (válida) | Desconto por duração de 5%. Nenhum nível recebe fidelidade (`PRATA` exige 7 e `OURO` exige 5). |
| `numeroDiarias` | Faixa de 5 a 6 diárias (válida) | Desconto por duração de 5%. `OURO` é elegível à fidelidade e `PRATA` não. |
| `numeroDiarias` | Faixa de 7 a 14 diárias (válida) | Desconto por duração de 10%. `PRATA` e `OURO` são elegíveis à fidelidade. |
| `numeroDiarias` | Faixa de 15 ou mais diárias (válida) | Desconto por duração de 15%. `PRATA` e `OURO` são elegíveis. Não há limite superior. |
| `quilometrosRodados` | Quilometragem negativa (inválida) | Inteiros menores que 0. Gera `IllegalArgumentException`. |
| `quilometrosRodados` | Dentro da franquia (válida) | De 0 até 100 × `numeroDiarias`. Excedente nulo e custo de quilometragem zero. |
| `quilometrosRodados` | Excedente na primeira faixa (válida) | Excedente de 1 a 100 km acima da franquia. Todo o excedente a R$ 0,80/km. |
| `quilometrosRodados` | Excedente na segunda faixa (válida) | Excedente de 101 a 300 km acima da franquia. Todo o excedente a R$ 1,00/km. |
| `quilometrosRodados` | Excedente na terceira faixa (válida) | Excedente acima de 300 km. Todo o excedente a R$ 1,50/km. |
| `horasAtraso` | Atraso negativo (inválida) | Inteiros menores que 0. Gera `IllegalArgumentException`. |
| `horasAtraso` | Atraso isento (válida) | 0 a 1 hora. Custo de atraso zero. |
| `horasAtraso` | Atraso proporcional (válida) | 2 a 3 horas. 20% da diária bruta da categoria por hora. |
| `horasAtraso` | Atraso com diária integral (válida) | 4 horas ou mais. Uma diária bruta, sem acréscimo por hora adicional. |
| `possuiAtrasoAnterior` | Com registro de atraso anterior | `true`. Anula a fidelidade de qualquer nível. Não altera as demais componentes. |
| `possuiAtrasoAnterior` | Sem registro de atraso anterior | `false`. A fidelidade pode ser aplicada se nível e número de diárias qualificarem. |
| Retorno | Resultado válido | `BigDecimal` com duas casas decimais e arredondamento `HALF_UP`. |

#### Valores-limite

| Parâmetro ou regra | Valor-limite | Classes separadas e inclusão do limite |
|---|---|---|
| `numeroDiarias` | 0 e 1 | Até 0 é inválido. A partir de 1 é válido. O valor 1 pertence à classe válida (primeira faixa, desconto de 0%). |
| `numeroDiarias` | 2 e 3 | Até 2 não tem desconto por duração. A partir de 3 o desconto é de 5%. O valor 3 pertence à faixa de 5%. |
| `numeroDiarias` | 4 e 5 | Até 4 o cliente `OURO` não recebe fidelidade. A partir de 5 recebe 10%. O valor 5 pertence à classe com fidelidade. O desconto por duração é de 5% nos dois lados. |
| `numeroDiarias` | 6 e 7 | Até 6 o desconto por duração é de 5% e `PRATA` não recebe fidelidade. A partir de 7 o desconto é de 10% e `PRATA` recebe 5%. O valor 7 pertence às duas classes superiores. |
| `numeroDiarias` | 14 e 15 | Até 14 o desconto por duração é de 10%. A partir de 15 é de 15%. O valor 15 pertence à faixa de 15%. |
| `quilometrosRodados` | -1 e 0 | Menor que 0 é inválido. A partir de 0 é válido. O valor 0 pertence à classe válida (dentro da franquia). |
| `quilometrosRodados` | 100 × `numeroDiarias` (franquia) | Até a franquia, inclusive, não há cobrança. Um km acima da franquia gera excedente de 1 km cobrado a R$ 0,80. O valor da franquia pertence à classe sem cobrança. A franquia varia com o número de diárias. |
| `quilometrosRodados` | Franquia + 100 e franquia + 101 (excedente de 100 e de 101 km) | Excedente até 100 km usa R$ 0,80/km. Excedente de 101 km usa R$ 1,00/km sobre todo o excedente. O excedente de 100 km pertence à primeira faixa. |
| `quilometrosRodados` | Franquia + 300 e franquia + 301 (excedente de 300 e de 301 km) | Excedente até 300 km usa R$ 1,00/km. Excedente de 301 km usa R$ 1,50/km sobre todo o excedente. O excedente de 300 km pertence à segunda faixa. |
| `horasAtraso` | -1 e 0 | Menor que 0 é inválido. A partir de 0 é válido. O valor 0 pertence à classe válida (isenta). |
| `horasAtraso` | 1 e 2 | Até 1 hora é isento. A partir de 2 horas cobra-se 20% da diária por hora. O valor 2 pertence à faixa proporcional. |
| `horasAtraso` | 3 e 4 | Até 3 horas a cobrança é proporcional (3 horas equivalem a 60% da diária). A partir de 4 horas cobra-se uma diária inteira. O valor 4 pertence à faixa de diária integral. |

#### Tabela de decisão

| Regra | Combinação de condições | Ação prevista na especificação |
|---|---|---|
| VAL-01 | Categoria, nível ou seguro nulo. Demais parâmetros indiferentes. | Lançar `NullPointerException`. A verificação de nulos tem precedência sobre as validações numéricas. |
| VAL-02 | Categoria, nível e seguro presentes e número de diárias menor que 1. Quilometragem e atraso indiferentes. | Lançar `IllegalArgumentException`. |
| VAL-03 | Categoria, nível e seguro presentes e quilometragem negativa. Diárias e atraso indiferentes. | Lançar `IllegalArgumentException`. |
| VAL-04 | Categoria, nível e seguro presentes e horas de atraso negativas. Diárias e quilometragem indiferentes. | Lançar `IllegalArgumentException`. |
| VAL-05 | Todas as referências presentes, diárias maiores ou iguais a 1, quilometragem e atraso não negativos. | Calcular e retornar o custo total. |
| DUR-01 | Diárias de 1 a 2. | Desconto por duração de 0% sobre as diárias brutas. |
| DUR-02 | Diárias de 3 a 6. | Desconto por duração de 5% sobre as diárias brutas. |
| DUR-03 | Diárias de 7 a 14. | Desconto por duração de 10% sobre as diárias brutas. |
| DUR-04 | Diárias de 15 ou mais. | Desconto por duração de 15% sobre as diárias brutas. |
| FID-01 | Nível `COMUM`. Diárias e atraso anterior indiferentes. | Fidelidade de 0%. |
| FID-02 | Nível `PRATA`, diárias maiores ou iguais a 7 e sem atraso anterior. | Fidelidade de 5% sobre as diárias já com desconto por duração. |
| FID-03 | Nível `PRATA` e diárias menores que 7. Atraso anterior indiferente. | Fidelidade de 0%. |
| FID-04 | Nível `PRATA`, diárias maiores ou iguais a 7 e com atraso anterior. | Fidelidade de 0%. |
| FID-05 | Nível `OURO`, diárias maiores ou iguais a 5 e sem atraso anterior. | Fidelidade de 10% sobre as diárias já com desconto por duração. |
| FID-06 | Nível `OURO` e diárias menores que 5. Atraso anterior indiferente. | Fidelidade de 0%. |
| FID-07 | Nível `OURO`, diárias maiores ou iguais a 5 e com atraso anterior. | Fidelidade de 0%. |
| FID-08 | Desconto por duração e fidelidade ambos aplicáveis. | Os descontos são compostos. A fidelidade incide sobre o valor já reduzido pela duração, e os percentuais não são somados. |
| KM-01 | Quilometragem menor ou igual a 100 × diárias (sem excedente). | Custo de quilometragem zero. |
| KM-02 | Excedente de 1 a 100 km. | Todo o excedente a R$ 0,80/km. |
| KM-03 | Excedente de 101 a 300 km. | Todo o excedente a R$ 1,00/km (tarifa única, sem escalonamento por faixa). |
| KM-04 | Excedente acima de 300 km. | Todo o excedente a R$ 1,50/km (tarifa única, sem escalonamento por faixa). |
| SEG-01 | Seguro `SEM_SEGURO`. | Custo de seguro zero. |
| SEG-02 | Seguro `BASICO`. | R$ 25 por diária, multiplicado pelo número de diárias. |
| SEG-03 | Seguro `COMPLETO`. | R$ 45 por diária, multiplicado pelo número de diárias. |
| ATR-01 | Atraso de 0 a 1 hora. | Isento, custo de atraso zero. |
| ATR-02 | Atraso de 2 a 3 horas. | 20% da diária bruta da categoria, sem desconto, multiplicado pelas horas de atraso. |
| ATR-03 | Atraso de 4 horas ou mais. | Uma diária bruta da categoria, sem desconto. O valor não cresce com horas adicionais. |
| TOT-01 | Entradas válidas. | Total igual a diárias após descontos, mais quilometragem excedente, mais seguro, mais atraso. Retorno com duas casas decimais e `HALF_UP`. |

### Análise caixa-branca do grupo

| Método ou trecho | Decisão, condição ou caminho | Obrigação de cobertura |
|---|---|---|
| `calcularCustoLocacao`, fluxo geral | O método é sequencial e sem laços. Há quatro saídas antecipadas por exceção (uma `NullPointerException` e três `IllegalArgumentException`) e uma saída normal pelo `return`. | Exercitar cada uma das quatro saídas antecipadas e ao menos um caminho completo até o `return`. |
| `calcularCustoLocacao`, verificação de argumentos nulos | Uma única decisão com três condições ligadas por OU: categoria nula, nível nulo e seguro nulo. Há curto-circuito: o nível só é avaliado se a categoria não for nula, e o seguro só é avaliado se categoria e nível não forem nulos. | Exercitar os resultados verdadeiro e falso da decisão. Exercitar cada condição como a única verdadeira (categoria nula, nível nulo com categoria presente, seguro nulo com os dois anteriores presentes) e o caso com as três falsas. |
| `calcularCustoLocacao`, checagem do número de diárias | A decisão verifica se o número de diárias é menor ou igual a zero. Só é avaliada se não houver referência nula. | Exercitar os resultados verdadeiro e falso. Usar os valores 0 e 1 para detectar troca de operador relacional. |
| `calcularCustoLocacao`, checagem da quilometragem negativa | A decisão verifica se a quilometragem é menor que zero. Só é avaliada com referências presentes e diárias válidas. | Exercitar os resultados verdadeiro e falso. Usar os valores -1 e 0 para detectar troca de operador relacional. |
| `calcularCustoLocacao`, checagem das horas de atraso negativas | A decisão verifica se as horas de atraso são menores que zero. Só é avaliada com as validações anteriores aprovadas. | Exercitar os resultados verdadeiro e falso. Usar os valores -1 e 0 para detectar troca de operador relacional. |
| `calcularCustoLocacao`, seleção do valor da diária | Cadeia de decisões: categoria econômica, senão categoria intermediária, senão SUV. O ramo final atende SUV porque os nulos já foram descartados. | Exercitar os três ramos: `ECONOMICO`, `INTERMEDIARIO` e `SUV`. |
| `calcularCustoLocacao`, taxa de desconto por duração | Cadeia de decisões com limites em 2, 6 e 14 diárias. Cada decisão só é avaliada se a anterior for falsa. | Exercitar os quatro ramos (0%, 5%, 10% e 15%). Usar os pares 2 e 3, 6 e 7, 14 e 15 para detectar troca de operador relacional. |
| `calcularCustoLocacao`, fidelidade, primeira decisão (`PRATA`) | A decisão combina três condições ligadas por E: nível `PRATA`, diárias maiores ou iguais a 7 e ausência de atraso anterior. Há curto-circuito: as diárias só são avaliadas se o nível for `PRATA`, e o atraso anterior só é avaliado se o nível for `PRATA` e as diárias forem pelo menos 7. | Exercitar os resultados verdadeiro e falso. Mostrar o efeito independente de cada condição, a partir do caso verdadeiro (`PRATA`, 7 ou mais diárias, sem atraso anterior): alterar só o nível, alterar só as diárias (com o par 6 e 7) e alterar só o atraso anterior. |
| `calcularCustoLocacao`, fidelidade, segunda decisão (`OURO`) | A decisão combina três condições ligadas por E: nível `OURO`, diárias maiores ou iguais a 5 e ausência de atraso anterior. Só é avaliada se a primeira decisão for falsa. Há curto-circuito: as diárias só são avaliadas se o nível for `OURO`, e o atraso anterior só é avaliado se o nível for `OURO` e as diárias forem pelo menos 5. | Exercitar os resultados verdadeiro e falso. Mostrar o efeito independente de cada condição, a partir do caso verdadeiro (`OURO`, 5 ou mais diárias, sem atraso anterior): alterar só o nível, alterar só as diárias (com o par 4 e 5) e alterar só o atraso anterior. Incluir `PRATA` com 5 ou 6 diárias e `PRATA` com 7 ou mais diárias e atraso anterior, que chegam a esta decisão e não recebem benefício. |
| `calcularCustoLocacao`, resultado da cadeia de fidelidade | A cadeia produz três caminhos: 5%, 10% ou 0% (ramo final, para `COMUM` e para clientes não qualificados). | Exercitar os três caminhos. |
| `calcularCustoLocacao`, composição de duração e fidelidade | A fidelidade é calculada sobre o valor já reduzido pelo desconto por duração. A combinação duração de 0% com fidelidade positiva é infactível, pois a fidelidade exige ao menos 5 diárias e então a duração já é de 5%. | Exercitar ao menos um caminho com desconto por duração e fidelidade aplicados juntos. Não incluir a combinação infactível. |
| `calcularCustoLocacao`, excedente de quilometragem | A decisão verifica se o excedente (quilometragem menos 100 × diárias) é menor ou igual a zero. | Exercitar os resultados verdadeiro e falso. Usar excedente 0 e excedente 1 para detectar troca de operador relacional. |
| `calcularCustoLocacao`, franquia calculada com `long` | A franquia é calculada com tipo `long` para evitar estouro de `int` quando o número de diárias é muito grande (acima de cerca de 21 milhões). | Exercitar um número de diárias grande o bastante para estourar `int` em 100 × diárias, com quilometragem baixa. Não deve surgir cobrança espúria de quilometragem nem exceção. |
| `calcularCustoLocacao`, tarifa quilométrica | Cadeia de decisões com limites de excedente em 100 e 300 km. Só é avaliada se houver excedente positivo. | Exercitar os três ramos (R$ 0,80, R$ 1,00 e R$ 1,50). Usar os pares 100 e 101, 300 e 301 para detectar troca de operador relacional. |
| `calcularCustoLocacao`, valor do seguro por diária | Cadeia de decisões: sem seguro, senão seguro básico, senão seguro completo (ramo final). | Exercitar os três ramos: `SEM_SEGURO`, `BASICO` e `COMPLETO`. |
| `calcularCustoLocacao`, custo de atraso | Cadeia de decisões com limites em 1 e 3 horas. No ramo de 2 a 3 horas o custo usa a diária bruta da categoria multiplicada pelas horas. | Exercitar os três ramos (isento, proporcional e diária integral). Usar os pares 1 e 2, 3 e 4 para detectar troca de operador relacional. No ramo proporcional, exercitar 2 e 3 horas e um caso com desconto nas diárias, para distinguir a diária bruta da diária com desconto. |
| `calcularCustoLocacao`, arredondamento final (`setScale(2, HALF_UP)`) | Caminho único, sem decisão. Todas as componentes (diárias, quilometragem, seguro e atraso) têm no máximo duas casas decimais, então nenhum resultado alcançável precisa de arredondamento. | Verificar que o retorno tem escala 2 em todos os caminhos exercitados. O modo `HALF_UP` não é distinguível pela saída. |