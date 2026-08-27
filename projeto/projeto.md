# Construção do Primeiro Agente de IA

## 1. Objetivo do projeto

Este projeto tem como objetivo estudar e documentar, de forma prática e reproduzível, o processo de construção de um agente de IA.

O primeiro caso de uso será a automação de tarefas administrativas e financeiras relacionadas à gestão de duas unidades geradoras de energia fotovoltaica pertencentes à mesma empresa, com foco inicial em faturamento, fluxo de caixa e organização mensal de documentos contábeis.

A proposta é construir o agente de forma incremental, registrando as decisões, testes, dificuldades e soluções encontradas durante o desenvolvimento.

> **Privacidade:** todos os exemplos e dados utilizados na documentação pública deste projeto serão fictícios ou anonimizados. Documentos empresariais, fiscais, bancários e dados de clientes não serão disponibilizados neste repositório.

---

## 2. Levantamento inicial do processo

### 2.1 Objetivo do processo atual

Mensalmente é necessário:

* obter os dados de geração e consumo das unidades;
* alimentar uma planilha de faturamento;
* obter o valor calculado para faturamento;
* emitir os documentos correspondentes;
* registrar receitas e despesas no fluxo de caixa;
* classificar os lançamentos;
* identificar a qual unidade cada lançamento pertence;
* realizar os rateios de despesas compartilhadas;
* obter o extrato mensal da conta de investimentos empresarial;
* encaminhar a documentação necessária para a contabilidade.

### 2.2 Processo atual de faturamento

A conta de energia é disponibilizada mensalmente em formato PDF em um ambiente de armazenamento em nuvem.

Atualmente, são extraídas manualmente do documento as seguintes informações:

* data de referência;
* data de vencimento;
* valor a pagar;
* consumo em kWh;
* energia injetada.

Essas informações são inseridas em uma planilha de faturamento.

A planilha possui abas separadas para cada unidade geradora e utiliza fórmulas para realizar os cálculos necessários.

Após o cálculo, o valor obtido é utilizado para realizar o faturamento mensal.

Antes da emissão, devem ser conferidos pelo menos:

* período/data correspondente;
* valor calculado;
* valor efetivamente recebido.

### 2.3 Processo atual de fluxo de caixa

Existe uma única planilha de fluxo de caixa para as duas unidades geradoras.

A estrutura atual possui os seguintes campos:

* Data
* Ano
* Mês
* Usina
* Tipo
* Categoria
* Valor
* Observação

O campo **Usina** identifica uma unidade específica ou indica que determinado valor pertence às duas unidades.

O campo **Tipo** diferencia receitas e gastos.

Entre as categorias utilizadas estão:

* Receita
* Pró-labore
* Despesa administrativa
* Despesa operacional
* Imposto
* Retirada de sócio

Atualmente, a classificação dos lançamentos é realizada manualmente.

Também existem despesas compartilhadas que precisam ser rateadas entre as unidades.

### 2.4 Regras de divisão das despesas

Algumas regras de negócio já identificadas são:

| Despesa                 | Regra                                                                                    |
| ----------------------- | ---------------------------------------------------------------------------------------- |
| Internet                | Dividida entre as duas unidades                                                          |
| Condomínio              | Pertence à respectiva unidade                                                            |
| Energia elétrica        | Pertence à respectiva unidade                                                            |
| Água compartilhada      | Dividida entre as duas unidades                                                          |
| Reparos da rede de água | Divididos entre as duas unidades, mesmo quando cobrados inicialmente de apenas uma delas |

Existem situações excepcionais que poderão exigir validação humana antes do lançamento.

### 2.5 Processo relacionado aos investimentos

Mensalmente é necessário:

1. acessar a instituição financeira;
2. obter o extrato mensal da conta de investimentos empresarial;
3. gerar o documento em formato apropriado;
4. encaminhá-lo à contabilidade.

Esta atividade possui prazo mensal e atualmente é realizada manualmente.

---

## 3. Questões ainda não definidas

* Quais documentos serão utilizados como fonte para alimentar o fluxo de caixa?
* Como receitas e despesas serão identificadas automaticamente?
* Quais lançamentos podem ser classificados automaticamente?
* Quais regras de rateio devem ser implementadas de forma determinística?
* Como tratar despesas que não se enquadram nas regras conhecidas?
* Quais informações devem ser interpretadas por IA e quais devem ser tratadas por regras tradicionais?
* Quais ferramentas serão utilizadas na implementação?
* Quais tarefas poderão ser executadas automaticamente?
* Quais ações exigirão aprovação humana?
* Como proteger credenciais e dados empresariais durante a execução do agente?
* Como o agente será testado e seus resultados validados?
