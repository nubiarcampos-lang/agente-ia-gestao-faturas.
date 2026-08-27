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

* ## 4. Entradas do agente

### 4.1 Contas mensais de energia

Cada unidade geradora possui uma pasta própria em ambiente de armazenamento em nuvem.

As contas são disponibilizadas mensalmente em formato PDF.

Os arquivos possuem identificadores que permitem determinar a qual unidade geradora pertencem e também identificar sua competência.

Para preservar a privacidade dos dados reais, neste projeto público as unidades serão representadas como:

* Unidade A
* Unidade B

Exemplos fictícios de nomes de arquivos:

* `UNIDADE_A_2026_07_conta.pdf`
* `UNIDADE_B_2026_07_conta.pdf`

Do PDF deverão ser extraídas informações como:

* data de referência;
* data de vencimento;
* valor a pagar;
* consumo em kWh;
* energia injetada.

Esses dados deverão ser encaminhados para a aba correspondente da planilha de faturamento.

### 4.2 Planilha de faturamento

A planilha de faturamento será utilizada como uma das entradas e também como ferramenta de cálculo.

Ela possui abas separadas para cada unidade geradora e fórmulas já existentes para calcular o valor mensal de faturamento.

O agente não deverá inicialmente substituir essas fórmulas.

O fluxo esperado será:

1. identificar a unidade geradora;
2. extrair os dados da conta de energia;
3. inserir os dados na aba correspondente;
4. permitir que as fórmulas existentes façam os cálculos;
5. recuperar o valor calculado;
6. realizar as conferências necessárias antes do faturamento.

### 4.3 Movimentações da conta empresarial

As movimentações da conta empresarial serão a principal fonte de informações para o fluxo de caixa.

As informações disponíveis nas transações incluem, entre outras:

* data;
* valor;
* descrição;
* ente recebedor ou pagador.

Essas informações deverão ser utilizadas para tentar identificar:

* se o lançamento é uma receita ou um gasto;
* a categoria correspondente;
* a qual unidade geradora o lançamento pertence;
* se a despesa deve ser compartilhada entre as duas unidades.

Quando a descrição ou o recebedor não forem suficientes para uma classificação segura, o lançamento deverá ser encaminhado para validação humana.

### 4.4 Documentos complementares

Algumas despesas também podem possuir documentos recebidos por outros meios, como contas e boletos enviados por e-mail.

Esses documentos poderão ser utilizados como fonte complementar de informação, mas não serão considerados inicialmente como fonte obrigatória para o funcionamento do agente.

### 4.5 Extrato mensal de investimentos

O extrato mensal da conta de investimentos empresarial será outra entrada do processo.

Nesse caso, o documento não precisa necessariamente ser interpretado pela IA.

Inicialmente, o objetivo é automatizar o processo de:

1. obtenção do extrato mensal;
2. geração ou download do arquivo;
3. preparação para envio à contabilidade.

A necessidade de interpretação do conteúdo do extrato será avaliada em etapas futuras do projeto.

* Quais ações exigirão aprovação humana?
* Como proteger credenciais e dados empresariais durante a execução do agente?
* Como o agente será testado e seus resultados validados?

## 5. O que o agente precisa fazer

### 5.1 Processar as contas mensais de energia

Para cada unidade geradora, o agente deverá:

1. identificar a chegada ou disponibilidade de uma nova conta mensal;
2. identificar a competência do documento;
3. identificar a qual unidade geradora a conta pertence;
4. ler o arquivo em formato PDF;
5. extrair os dados necessários para o faturamento;
6. inserir os dados na aba correta da planilha de faturamento;
7. permitir que as fórmulas existentes na planilha realizem os cálculos;
8. recuperar o valor calculado para faturamento;
9. realizar as conferências definidas para o processo;
10. preparar os dados necessários para emissão da fatura;
11. solicitar aprovação humana antes da emissão;
12. emitir a fatura somente após a aprovação.

Caso seja encontrada alguma divergência durante a conferência, a emissão deverá ser interrompida até que o problema seja analisado.

### 5.2 Atualizar o fluxo de caixa

O agente deverá:

1. obter as movimentações da conta empresarial;
2. identificar individualmente cada transação;
3. extrair informações como:

   * data;
   * valor;
   * descrição;
   * recebedor ou pagador;
4. identificar se o lançamento é uma receita ou um gasto;
5. identificar a categoria correspondente;
6. identificar a qual unidade geradora o lançamento pertence;
7. identificar despesas compartilhadas;
8. aplicar as regras de rateio previamente definidas;
9. verificar se o lançamento já foi registrado anteriormente;
10. registrar os lançamentos classificados com segurança na planilha de fluxo de caixa.

Quando não houver informação suficiente para classificar um lançamento com segurança, o agente **não deverá realizar o lançamento**.

Nesse caso, deverá solicitar validação humana e aguardar a definição correta antes de atualizar a planilha.

### 5.3 Enviar o extrato mensal de investimentos

O agente deverá:

1. verificar a disponibilidade do extrato mensal;
2. obter o documento correspondente à competência correta;
3. verificar se o arquivo é o documento esperado;
4. preparar o envio para a contabilidade;
5. realizar o envio automaticamente dentro do prazo estabelecido;
6. notificar o usuário após a conclusão do envio.

Essa atividade não exige aprovação humana prévia quando todas as verificações forem concluídas com sucesso.

Caso exista alguma inconsistência no documento ou na competência, o envio deverá ser interrompido e o usuário deverá ser avisado.

### 5.4 Acompanhar o status do processo mensal

O agente deverá manter um acompanhamento do processo de cada competência, permitindo identificar o que já foi concluído, o que está pendente e o que depende de intervenção humana.

O acompanhamento deverá incluir, no mínimo:

#### Faturamento por unidade

* conta mensal localizada;
* dados extraídos;
* planilha de faturamento atualizada;
* cálculo realizado;
* conferência concluída;
* aprovação solicitada;
* fatura emitida.

#### Fluxo de caixa

* movimentações obtidas;
* lançamentos identificados;
* lançamentos classificados;
* lançamentos registrados;
* lançamentos aguardando validação humana.

#### Documentação contábil

* extrato mensal localizado;
* documento verificado;
* documento enviado;
* envio confirmado.

### 5.5 Tratamento de exceções

Sempre que o agente encontrar uma situação que não esteja prevista nas regras existentes ou não possuir informação suficiente para executar uma tarefa com segurança, deverá:

1. interromper somente a etapa afetada;
2. registrar a pendência;
3. informar qual foi o problema encontrado;
4. solicitar intervenção humana quando necessário;
5. continuar executando as demais tarefas que não dependam daquela pendência.
