# REFactor Race Python

## EQUIPE

- Integrante 1: Adriano Neto 
- Integrante 2: Davi Abras 
- Integrante 3: Gustavo Edson
- Integrante 4: Heitor Souza
- Integrante 5: João Vitor Jesus
- Integrante 6: Luiz Felipe Dasmaceno

## PENDÊNCIAS PARA A ENTREGA

### 1. Manter a ordem dos produtos duplicados

A função atual pode alterar a ordem dos produtos duplicados porque utiliza um `set`, que não garante a preservação da ordem original de entrada. É necessário ajustar a função para manter a ordem em que os produtos aparecem na lista original.

### 2. Adicionar os testes que faltam no GitHub

O repositório ainda contém apenas os quatro testes iniciais. É necessário atualizar o arquivo de testes com dois cenários adicionais: frete grátis e cliente funcionário. Dessa forma, a implementação ficará de acordo com o que foi documentado neste README.

### 3. Corrigir o erro de importação dos testes

O arquivo de testes tenta importar o código principal diretamente, mas o módulo está localizado na pasta `src/`. É necessário ajustar a importação no início de `test_checkout.py` para que o Python encontre o módulo corretamente, inclusive em um ambiente configurado do zero.

### 4. Renomear o arquivo de métricas

O arquivo está nomeado como `METRICS.MD`, mas o padrão definido para a entrega é `METRICS.md`. É necessário renomeá-lo e atualizar as métricas com os resultados reais da execução dos testes, incluindo a quantidade de testes aprovados e o percentual de cobertura obtido.

### 5. Criar os arquivos de configuração

É necessário criar os arquivos `.gitignore` e `requirements-dev.txt` na raiz do projeto, conforme a estrutura mínima indicada no enunciado.

O arquivo `requirements-dev.txt` deverá incluir as seguintes ferramentas:

- `pytest`
- `pytest-cov`
- `radon`
- `ruff`

---

## DESCRIÇÃO

Este projeto corresponde à entrega do desafio **REFactor Race Python**, proposto na unidade curricular Garantia e Qualidade de Software.

O objetivo é aplicar técnicas de refatoração em um sistema legado de processamento de pedidos, melhorando sua qualidade interna, legibilidade, testabilidade e manutenibilidade, sem alterar o comportamento esperado do programa.

Para avaliar as melhorias, são utilizadas ferramentas de testes automatizados, análise de complexidade, cobertura de testes e análise estática do código.

## DIAGNÓSTICO INICIAL

Antes de iniciar a refatoração, foram identificados problemas de qualidade no código original.

1. **Função com muitas responsabilidades:** a função `process_order` concentrava cálculos de subtotal, descontos, frete, impostos e pontos de fidelidade, além de realizar operações de saída e modificar estados.
2. **Duplicação de código:** o subtotal era calculado duas vezes por meio de laços `for` com a mesma finalidade.
3. **Condicionais aninhadas:** as regras de desconto para clientes VIP, funcionários e clientes regulares utilizavam estruturas condicionais que dificultavam a leitura.
4. **Números e textos fixos no código:** taxas de impostos, valores de frete e siglas de estados estavam distribuídos diretamente pelo código, sem centralização adequada.
5. **Busca ineficiente por produtos duplicados:** a identificação de produtos repetidos utilizava dois laços aninhados, resultando em complexidade quadrática, \(O(n^2)\).
6. **Uso de estado global:** a função modificava diretamente a lista global `ORDERS_PROCESSED`, criando efeitos colaterais e dificultando a realização de testes isolados.

## CODE SMELLS ENCONTRADOS

Os principais *code smells* identificados foram:

- **God Function:** uma única função concentrava diversas responsabilidades.
- **Código duplicado:** o cálculo do subtotal era repetido.
- **Condicionais complexas:** as regras de desconto apresentavam estruturas que poderiam ser simplificadas.
- **Valores mágicos:** taxas, multiplicadores e siglas estavam espalhados pelo código.
- **Algoritmo ineficiente:** a busca por produtos duplicados realizava comparações desnecessárias.
- **Estado global mutável:** a alteração de `ORDERS_PROCESSED` dificultava o isolamento dos testes.
- **Baixa testabilidade:** o acúmulo de responsabilidades tornava mais difícil verificar cada regra de negócio separadamente.

## REFATORAÇÕES REALIZADAS E DECISÕES TÉCNICAS

### 1. Extração de funções (*Extract Function*)

A lógica foi dividida em funções menores e especializadas, como:

- `calculate_subtotal`
- `calculate_discount`
- `calculate_shipping`
- `calculate_tax`
- `find_duplicate_products`

**Justificativa:** separar as responsabilidades facilita a leitura, a manutenção e a realização de testes específicos para cada regra de negócio.

### 2. Remoção de código duplicado (*Remove Duplicate Code*)

O cálculo do subtotal foi centralizado em uma única função, evitando a repetição da mesma lógica.

**Justificativa:** alterações futuras na regra de cálculo poderão ser realizadas em um único local, reduzindo o risco de inconsistências.

### 3. Substituição de condicionais por mapeamento (*Replace Conditional with Mapping*)

Foi utilizado o dicionário `TAX_RATES` para centralizar as taxas de impostos por estado.

**Justificativa:** o mapeamento facilita a leitura das regras e a manutenção das taxas, reduzindo a necessidade de múltiplas condições `if/elif`.

### 4. Substituição de valores mágicos por constantes

Foram criadas constantes, como `EXPRESS_SHIPPING_MULTIPLIER` e `SOUTHEAST_STATES`, para representar valores e conjuntos de domínio.

**Justificativa:** constantes com nomes descritivos deixam explícita a finalidade de cada valor e facilitam futuras alterações.

### 5. Otimização da busca por produtos duplicados

A busca por produtos repetidos foi reorganizada para utilizar um conjunto (`set`) na identificação dos itens já encontrados.

**Justificativa:** essa abordagem pode reduzir a complexidade média da busca de \(O(n^2)\) para \(O(n)\), considerando operações de conjunto com custo médio constante.

**Observação:** a implementação deve preservar a ordem original dos produtos duplicados, caso esse seja o comportamento do código legado. Para isso, o conjunto pode ser utilizado para consulta, enquanto uma lista mantém a ordem dos resultados.

### 6. Redução da complexidade da função principal

A extração das regras de negócio para funções especializadas reduziu a quantidade de decisões concentradas em `process_order`.

**Justificativa:** uma função principal mais simples facilita a compreensão do fluxo de execução e reduz o esforço necessário para realizar alterações futuras.

## TESTES ADICIONADOS

Além dos quatro testes originais, foram previstos dois cenários adicionais:

- `test_regular_customer_with_free_shipping`: verifica a aplicação da regra de frete grátis para pedidos com valor igual ou superior a R$ 500, conforme a regra existente no sistema.
- `test_employee_customer`: verifica o cálculo do desconto de 20% destinado a funcionários, conforme a regra existente no sistema.

Os testes devem confirmar os resultados esperados sem modificar as regras de negócio originais.

**Importante:** esses cenários só devem ser considerados entregues depois que os respectivos testes estiverem implementados no repositório e forem executados com sucesso.

## MÉTRICAS ANTES E DEPOIS

Os resultados completos da análise devem ser registrados no arquivo `METRICS.md`.

Os indicadores utilizados são:

- Quantidade de testes.
- Quantidade de testes aprovados.
- Cobertura dos testes automatizados.
- Complexidade ciclomática da função principal.
- Complexidade ciclomática média.
- Índice de manutenibilidade.
- Quantidade de problemas identificados pelo Ruff.

### Resumo das melhorias esperadas

- **Complexidade ciclomática:** redução da complexidade concentrada em `process_order` por meio da extração de funções.
- **Cobertura de testes:** ampliação da cobertura após a inclusão de cenários adicionais.
- **Análise estática:** redução dos problemas identificados pelo Ruff.
- **Manutenibilidade:** melhoria da organização e da separação das responsabilidades.
- **Testabilidade:** possibilidade de verificar as regras de negócio por meio de funções e testes mais específicos.

Os valores numéricos definitivos devem ser preenchidos somente após a execução das ferramentas. A comparação deve utilizar os resultados reais obtidos antes e depois da refatoração.

## DECISÕES TÉCNICAS

As principais decisões técnicas foram:

1. Dividir funções extensas em funções especializadas.
2. Centralizar as regras de impostos em um dicionário.
3. Substituir valores fixos por constantes descritivas.
4. Reduzir a duplicação de código.
5. Utilizar estruturas de dados mais adequadas para a busca por produtos duplicados.
6. Preservar as regras de negócio e o comportamento esperado do sistema.
7. Utilizar testes automatizados como proteção contra regressões.
8. Comparar métricas antes e depois para avaliar os resultados da refatoração.

## USO DE INTELIGÊNCIA ARTIFICIAL

- **Ferramenta utilizada:** Google Gemini.
- **Finalidade:** auxiliar na identificação de *code smells*, sugerir técnicas de refatoração, organizar as métricas e apoiar a elaboração da documentação.
- **Exemplo de sugestão recebida:** dividir a função principal em funções menores, centralizar taxas de impostos em um dicionário e utilizar um conjunto para auxiliar na identificação de produtos duplicados.
- **Validação das sugestões:** as sugestões devem ser revisadas pela equipe, verificadas quanto à preservação do comportamento e validadas por meio da execução dos testes automatizados e das ferramentas de análise de qualidade.

A utilização da ferramenta não substitui a análise crítica, a revisão do código ou a responsabilidade da equipe pelos resultados apresentados.

## MELHORIAS FUTURAS

Apesar das melhorias realizadas, ainda existem oportunidades de evolução:

- Reduzir ou eliminar a dependência do estado global `ORDERS_PROCESSED`.
- Adicionar *type hints* às funções para explicitar os tipos esperados de entrada e saída.
- Ampliar a suíte de testes para contemplar mais cenários e casos limite.
- Avaliar a separação das regras de negócio em módulos específicos, caso a complexidade do sistema aumente.
- Automatizar a execução de testes e análises estáticas por meio de integração contínua.
- Documentar as regras de negócio e os contratos das principais funções.
- Garantir que futuras otimizações preservem a ordem e os demais aspectos observáveis do comportamento original.

## CONCLUSÃO

A refatoração busca demonstrar que a qualidade de software não depende apenas da redução da quantidade de linhas de código, mas também da clareza, da organização, da testabilidade e da facilidade de manutenção.

A comparação entre os resultados iniciais e finais, apoiada por testes automatizados e métricas reproduzíveis, permite avaliar se as alterações realmente melhoraram a qualidade interna do sistema sem comprometer seu comportamento esperado.
