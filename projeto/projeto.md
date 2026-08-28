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

## 6. O que o agente não deve fazer

O agente deverá respeitar limites de autonomia e segurança durante todo o processo.

### 6.1 Não executar ações que exigem aprovação humana sem autorização

O agente não deverá executar automaticamente tarefas que tenham sido definidas como dependentes de aprovação humana.

Em especial, não deverá:

* emitir uma fatura sem aprovação prévia;
* prosseguir com a emissão caso seja identificada alguma divergência;
* registrar no fluxo de caixa um lançamento cuja classificação seja incerta;
* tomar decisões por conta própria em situações não previstas pelas regras estabelecidas.

### 6.2 Não presumir informações

Quando não houver informação suficiente para determinar com segurança a categoria, unidade, regra de rateio ou qualquer outro dado necessário, o agente não deverá inventar, presumir ou escolher arbitrariamente uma resposta.

Nesses casos, deverá:

1. registrar a pendência;
2. informar qual informação não pôde ser determinada;
3. solicitar orientação humana quando necessária.

### 6.3 Não interromper todo o processo por causa de uma exceção

Uma falha ou dúvida em uma tarefa não deverá interromper automaticamente todas as demais atividades do processo mensal.

O agente deverá:

1. interromper apenas a tarefa ou lançamento afetado;
2. registrar a ocorrência como pendência;
3. continuar executando as demais tarefas independentes que possam ser realizadas com segurança;
4. incluir a pendência no status mensal.

### 6.4 Não ocultar ações ou problemas

O agente não deverá concluir silenciosamente ações relevantes ou ocultar erros encontrados durante o processo.

Deverá notificar o usuário sobre:

* tarefas que dependem de aprovação;
* divergências encontradas;
* lançamentos que não puderam ser classificados;
* falhas na execução;
* documentos esperados que não foram encontrados;
* tarefas automáticas relevantes concluídas, quando estiver prevista notificação.

### 6.5 Não alterar regras ou cálculos por conta própria

O agente não deverá modificar:

* fórmulas existentes nas planilhas;
* regras de rateio;
* categorias financeiras;
* critérios de classificação;
* limites de aprovação;

sem que a alteração tenha sido previamente definida e validada pelo usuário.

### 6.6 Não expor informações confidenciais

O agente não deverá registrar em logs públicos, repositórios públicos ou outros ambientes inadequados:

* credenciais e senhas;
* dados bancários;
* documentos fiscais reais;
* dados de clientes;
* identificadores empresariais sensíveis;
* documentos financeiros ou contábeis confidenciais.

Dados utilizados para documentação pública e demonstração do projeto deverão ser fictícios ou anonimizados.

## 7. Ferramentas e integrações necessárias

A implementação do agente dependerá da integração entre diferentes ferramentas já utilizadas no processo e uma plataforma responsável pela automação e orquestração das tarefas.

### 7.1 Armazenamento em nuvem

As contas mensais de energia são armazenadas em ambiente de armazenamento em nuvem.

O agente deverá ser capaz de:

* acessar as pastas correspondentes às unidades geradoras;
* identificar novos documentos;
* identificar a competência;
* recuperar os arquivos necessários para processamento.

### 7.2 Google Sheets

O Google Sheets será utilizado em dois processos principais:

**Planilha de faturamento**

O agente deverá:

* identificar a aba correspondente à unidade;
* inserir os dados extraídos da conta de energia;
* preservar as fórmulas existentes;
* recuperar os resultados dos cálculos.

**Planilha de fluxo de caixa**

O agente deverá:

* consultar registros existentes;
* verificar possíveis duplicidades;
* adicionar novos lançamentos;
* registrar as classificações e rateios definidos.

### 7.3 Plataforma de faturamento

O agente deverá ser capaz de preparar os dados necessários para emissão da fatura.

A possibilidade de realizar a emissão diretamente pela plataforma será investigada durante o desenvolvimento.

A emissão deverá ocorrer somente após aprovação humana.

### 7.4 Conta corrente empresarial

As movimentações da conta corrente empresarial serão utilizadas como principal fonte para identificação de receitas e gastos.

Será necessário investigar uma forma segura de obter informações como:

* data;
* valor;
* descrição da movimentação;
* recebedor ou pagador.

A forma de integração ainda não foi definida.

### 7.5 Plataforma de investimentos

O agente deverá obter mensalmente o extrato da conta de investimentos empresarial.

Será necessário investigar se existe uma forma segura de automatizar:

* acesso ao extrato;
* identificação da competência;
* download do documento.

### 7.6 E-mail

O e-mail será utilizado para envio de documentos à contabilidade.

Quando o envio puder ser realizado com segurança e todas as validações forem satisfeitas, o agente poderá executar essa tarefa automaticamente e posteriormente notificar o usuário.

### 7.7 Canal de interação com o usuário

O canal desejado inicialmente para:

* solicitar aprovações;
* comunicar pendências;
* apresentar divergências;
* enviar notificações de tarefas concluídas;

é o WhatsApp.

A viabilidade técnica dessa integração ainda será investigada.

Caso o WhatsApp apresente limitações para o primeiro protótipo, poderá ser utilizado temporariamente outro canal de interação.

### 7.8 Inteligência artificial

Um modelo de IA será necessário principalmente para tarefas que envolvam interpretação ou classificação de informações não totalmente padronizadas, como:

* interpretar descrições de movimentações financeiras;
* sugerir categorias;
* identificar situações ambíguas;
* reconhecer quando não possui informação suficiente para tomar uma decisão.

Cálculos matemáticos, regras fixas de rateio e outras operações determinísticas não deverão depender do modelo de IA.

### 7.9 Plataforma de automação e orquestração

Será necessária uma ferramenta responsável por coordenar o fluxo entre os diferentes sistemas.

Essa ferramenta deverá permitir, entre outras funções:

* executar tarefas automaticamente;
* acessar serviços externos;
* utilizar modelos de IA;
* aplicar regras;
* consultar e atualizar planilhas;
* manter o estado do processo mensal;
* interromper etapas específicas quando necessária aprovação humana;
* continuar tarefas independentes mesmo quando existirem pendências.

A plataforma de automação ainda não foi escolhida.

Entre as alternativas a serem avaliadas estão ferramentas low-code/no-code e soluções desenvolvidas com código.

## 8. Decisão inicial de arquitetura

### 8.1 Plataforma escolhida para a primeira versão

Para a primeira versão do agente, foi escolhido o **n8n** como plataforma principal de automação e orquestração.

A escolha foi feita considerando os seguintes critérios:

* possibilidade de execução local;
* facilidade de visualização dos fluxos;
* integração com diferentes ferramentas e APIs;
* possibilidade de utilização de modelos de IA;
* suporte a fluxos que exigem aprovação humana;
* menor curva de aprendizado para a construção do primeiro protótipo;
* possibilidade de evolução futura sem necessidade de reconstruir todo o projeto.

### 8.2 Estratégia de execução inicial

A primeira versão será desenvolvida preferencialmente com o n8n executado localmente.

Essa abordagem permitirá estudar e testar o funcionamento do agente em ambiente controlado antes de avaliar uma implantação permanente em servidor ou serviço em nuvem.

A execução local também permitirá compreender melhor aspectos relacionados a:

* armazenamento de credenciais;
* autenticação;
* comunicação com serviços externos;
* segurança dos dados;
* execução e monitoramento dos fluxos;
* backup e recuperação do projeto.

A execução local não será considerada, por si só, uma garantia de segurança. Credenciais, tokens e informações confidenciais deverão ser protegidos independentemente do ambiente utilizado.

### 8.3 Papel do n8n

O n8n será utilizado principalmente como **orquestrador do agente**.

Sua função será coordenar as diferentes etapas do processo, incluindo:

* recebimento de gatilhos;
* acesso às fontes de dados;
* execução das regras de negócio;
* comunicação com modelos de IA;
* atualização das planilhas;
* controle das etapas do processo;
* solicitação de aprovação humana;
* tratamento de exceções;
* envio de notificações;
* integração com outras ferramentas.

### 8.4 Evolução futura com Python

Python não será utilizado como plataforma principal na primeira versão.

Sua incorporação será avaliada progressivamente quando alguma tarefa apresentar necessidade de:

* processamento de dados mais complexo;
* tratamento avançado de documentos;
* regras difíceis de manter visualmente no n8n;
* grande quantidade de condições repetitivas;
* maior controle sobre cálculos ou validações;
* criação de funções específicas;
* testes automatizados;
* melhor desempenho ou manutenção por meio de código.

Nesses casos, o n8n poderá continuar responsável pela orquestração enquanto módulos desenvolvidos em Python executam tarefas especializadas.

A adoção de Python, portanto, será baseada em uma necessidade técnica identificada durante o desenvolvimento, e não em uma etapa obrigatória ou prazo previamente definido.

### 8.5 Arquitetura inicial prevista

A arquitetura inicial do projeto será baseada na seguinte divisão de responsabilidades:

* **n8n:** automação e orquestração do fluxo;
* **modelo de IA:** interpretação e classificação de informações quando houver ambiguidade;
* **regras determinísticas:** cálculos, validações e rateios previamente definidos;
* **serviços externos:** armazenamento de documentos, planilhas, e-mail, sistemas financeiros e demais ferramentas necessárias;
* **interação humana:** aprovação de ações previamente definidas como sensíveis e resolução de exceções.

Essa arquitetura poderá ser modificada durante o desenvolvimento conforme limitações, riscos ou novas necessidades sejam identificados.

### 8.6 Critério de evolução da arquitetura

## 9. Decisões da IA, regras automáticas e aprovação humana

Para reduzir erros e manter o controle sobre ações sensíveis, o agente será desenvolvido com três níveis de decisão:

* regras automáticas;
* decisões apoiadas por inteligência artificial;
* decisões que exigem aprovação humana.

O princípio adotado será:

> **A IA será utilizada para interpretar situações ambíguas, enquanto cálculos e regras conhecidas serão executados de forma determinística. Ações sensíveis permanecerão sob controle humano.**

### 9.1 Regras automáticas

Sempre que uma tarefa puder ser determinada por critérios objetivos e previamente conhecidos, deverá ser utilizada uma regra automática.

Entre essas tarefas estão:

* identificar a unidade geradora a partir da origem ou identificação do documento;
* identificar a competência dos documentos;
* inserir dados nas abas correspondentes das planilhas;
* preservar e utilizar as fórmulas existentes;
* verificar duplicidade de lançamentos;
* aplicar regras previamente definidas de rateio;
* associar despesas a uma unidade quando existir uma regra explícita;
* verificar se documentos correspondem à competência esperada;
* atualizar o status das tarefas executadas;
* realizar cálculos matemáticos e validações determinísticas.

Regras conhecidas não deverão ser substituídas por decisões probabilísticas de um modelo de IA.

### 9.2 Decisões apoiadas por inteligência artificial

A IA será utilizada quando houver necessidade de interpretar informações que não estejam completamente padronizadas.

Entre as possíveis aplicações estão:

* interpretar descrições de movimentações financeiras;
* sugerir a categoria de um lançamento;
* interpretar informações textuais presentes em documentos;
* identificar situações que não correspondam claramente às regras existentes;
* detectar possíveis ambiguidades;
* indicar quando não possui informação suficiente para uma classificação segura.

A IA poderá sugerir uma classificação, mas não deverá inventar informações ou executar automaticamente uma ação quando a confiança for insuficiente.

### 9.3 Situações que exigem aprovação humana

Algumas ações deverão obrigatoriamente depender de validação humana.

#### Emissão de faturas

O agente deverá preparar os dados e realizar as conferências necessárias, mas a emissão da fatura somente poderá ocorrer após aprovação explícita do usuário.

#### Lançamentos financeiros ambíguos

Quando não for possível determinar com segurança:

* a categoria;
* a unidade correspondente;
* a regra de rateio;
* ou qualquer informação necessária para o lançamento;

o agente não deverá registrar a transação na planilha.

Deverá apresentar a situação ao usuário e aguardar orientação.

#### Exceções não previstas

Situações que possam alterar valores, classificações, regras ou decisões já estabelecidas deverão ser encaminhadas para validação humana.

### 9.4 Ações que poderão ocorrer sem aprovação prévia

Determinadas tarefas poderão ser executadas automaticamente quando todas as validações forem concluídas com sucesso.

Entre elas:

* atualização de dados nas planilhas conforme regras conhecidas;
* aplicação de cálculos e rateios previamente definidos;
* atualização do status mensal;
* envio do extrato mensal de investimentos à contabilidade.

Após o envio automático do extrato, o agente deverá notificar o usuário de que a tarefa foi concluída.

### 9.5 Tratamento de baixa confiança

Quando uma decisão baseada em IA apresentar baixa confiança ou informações contraditórias, o agente deverá:

1. não executar a ação dependente daquela decisão;
2. registrar a situação como pendência;
3. apresentar as informações disponíveis;
4. solicitar validação humana;
5. continuar executando tarefas independentes que não sejam afetadas pela pendência.

O agente não deverá transformar uma estimativa ou interpretação incerta em uma decisão definitiva sem validação.

Uma tarefa permanecerá implementada diretamente no n8n enquanto puder ser representada de maneira clara, segura e de fácil manutenção.

Quando uma etapa se tornar excessivamente complexa, repetitiva ou difícil de testar e manter no fluxo visual, será avaliada sua implementação em Python ou em outra solução mais adequada.

As alterações de arquitetura e os motivos que levaram a cada decisão serão documentados ao longo do projeto.
