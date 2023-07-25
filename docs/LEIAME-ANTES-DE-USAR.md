<img src="https://guilhermeslacerda.github.io/drtools-site/images/logo_drtools.png" width="40%">


DR-Tools Code Health
====================

O **DR-Tools Code Health** é uma ferramenta pertencente ao [DR-Tools Suite](http://drtools.site), conjunto de ferramentas *open-source* para ajudar as pessoas desenvolvedoras em suas atividades diárias de análise e identificação de [problemas de design](#smells) no código.

O **DR-Tools Code Health**, através de um indicador agregado de diferentes critérios, visa identificar códigos que são difíceis de manter e correm mais riscos de defeitos e mudanças, além de detectar códigos com acúmulo de complexidade. O objetivo da ferramenta não é prever os problemas que sofrerão refactorings, mas apoiar os desenvolvedores na [priorização e filtragem dos problemas](#ranking-e-priorização) para tomada de decisão em conjunto, com base no código. Por isso, ele foi projetado para ser [usada no dia a dia do desenvolvimento](#como-analisar-código-com-o-dr-tools-code-health), durante atividades de programação, revisões de código e até mesmo apoiando o processo de build do sistema.

A seguir, nós apresentamos algumas informações básicas para sua utilização. 


Conteúdos
===============

* [Introdução](#introdução)
* [Conceitos e Termos Usados](#conceitos-e-termos-usados)
* [Como usar?](#como-usar)
	* [Instalação e Requisitos](#instalação-e-requisitos)
    * [Modo CLI](#modo-cli)
    * [Modo Interativo](#modo-interativo)
* [Módulos](#módulos)
    * [Métricas](#métricas)
    * [Smells](#smells)
    * [Ranking e Priorização](#ranking-e-priorização)
* [Como analisar código com o DR-Tools Code Health?](#como-analisar-código-com-o-drtools-code-health)
* [Customizações](#customizações)

# Introdução

O **DR-Tools Code Health** é uma ferramenta que facilita a análise de código, através de recursos como [*métricas*](#métricas), [*detecção de smells*](#smells) e [*priorização dos problemas*](#ranking-e-priorização) identificados, com base em critérios como severidade, representatividade dos smells, impacto dos smells em atributos da qualidade e nível de intervenção do time.

Antes de aprofundarmos a discussão sobre as funcionalidades do **DR-Tools Code Health**, é importante destacar alguns pontos levados em consideração no seu desenvolvimento.
São eles:
* permitir a [definição de heurísticas](#customizações) para [detecção de smells](#smells), de forma configurável e extensível;
* permitir o envolvimento dos desenvolvedores no processo de detecção de smells;
* considerar como os smells impactam a qualidade do software;
* trazer um [formato de priorização](#ranking-e-priorização), multicritério, considerando diferentes [níveis de granularidade](#termos-usados) e suas respectivas composições para identificar os elementos de código mais problemáticos.

> A *granularidade* se refere ao nível em que o smell é encontrado, podendo ser no nível de pacote, classe ou método.
> 
>Os *elementos de código* de um projeto de software são estruturas autocontidas - método(s) estão dentro de classe(s). As classe(s) estão dentro de pacote(s). O(s) pacote(s) estão dentro de um projeto.

De fato, apenas detectar a presença dos [smells](#smells) não é um indicativo de criticidade. É necessário, segundo os tomadores de decisão, avaliar quais impactos na qualidade esses diferentes problemas apresentam. 

Também, cada possível problema possui diferentes pontuações de severidade. Sem saber sua real severidade, as pessoas desenvolvedoras sentem-se relutantes em detectá-los e removê-los. Assim, a [priorização](#ranking-e-priorização) se torna cada vez mais relevante para apoiar os desenvolvedores neste processo. 

> *"A priorização é a atividade mais importante de gestão da dívida técnica, sendo uma área de pesquisa em aberto"* (PINA; GOLDMAN; TONIN, 2021). 
>
> *"Também, a priorização dos smells é um campo aberto de pesquisa"* (PECORELLI et al., 2020).

É evidentemente claro que a priorização pode ser realizada usando diferentes abordagens, com diferentes objetivos e critérios.
No entanto, ainda falta um conjunto sólido, validado e amplamente usado de ferramentas para priorização.
Especificamente, a [priorização dos smells](#ranking-e-priorização) é um campo aberto de pesquisa, que permite fazer alguns questionamentos: 
1. ao detectar um smell, o quanto ele é mais problemático do que seus pares? 
2. qual sua implicação nos atributos de qualidade e, mais especificamente, na manutenibilidade? 
3. quais elementos de código e qual granularidade são as mais prioritárias? 
4. o acúmulo dos smells em diferentes elementos de código podem ajudar na priorização? 
5. como envolver o time no processo de detecção e priorização, levando em consideração os objetivos de qualidade e de negócios?

Estes problemas de design precisam ser priorizados, filtrados e sintetizados, permitindo que os desenvolvedores possam avaliar sobre ações imediatas ou planejadas ao longo do tempo. O **DR-Tools Code Health** vem para apoiar as pessoas desenvolvedoras, trazendo recursos para avaliar estes questionamentos. 

# Conceitos e Termos Usados

## Conceitos

Para desenvolvimento do **DR-Tools Code Health**, consideramos alguns pontos importantes de análise de código:

* **Smells têm diferentes severidades:** Em um projeto de software, é possível encontrar diferentes tipos de [problemas de design](#smells), com diferentes níveis de severidades. A severidade está relacionada a criticidade de um elemento de código, influenciando negativamente a manutenção e evolução.
Os *elementos de código* são os principais componentes presentes em um projeto de software, autocontidos, como pacotes, classes e métodos.
Alguns elementos de código são mais problemáticos que outros, inclusive com smells de mesma instância;
* **Smells têm diferentes granularidades:** Os [problemas de design](#smells) também são localizados em diferentes níveis de granularidade. Os problemas podem estar presentes nos métodos, classes e pacotes/módulos que compõe o sistema. Ainda, os problemas em um determinado nível podem influenciar o outro nível. Inclusive, problemas de design no nível de método pode ser a causa raiz da inserção de problemas no nível de classes. Consequentemente, os problemas no nível de classe tem uma forte correlação positiva com problemas no nível de pacotes;
* **Smells são acumulativos:** Um projeto de software é formado por estruturas autocontidas (módulos/pacotes, que contém classes, que contém atributos e métodos). Por este motivo, um aspecto a ser considerado é a presença de mais de um [smell](#smells) em um elemento específico de código (*e.g.*, em um método), conhecido como co-ocorrência. 
Ainda, além da co-ocorrência, é importante considerar o acúmulo destes problemas nos diferentes níveis de granularidade, ou a composição das coocorrências;
* **Smells afetam diferentes atributos de qualidade:** Não só os atributos de qualidade afetam vários [problemas de design](#smells), mas também os problemas de design podem afetar mais de um atributo de qualidade. Um atributo de qualidade está relacionado a um problema se a estrutura de definição do problema contém o mapeamento entre o problema e os atributos de qualidade;
* **Dependendo da granularidade observada, muda a percepção de severidade:**
Modelos em diferentes níveis de granularidade devem ser o foco de novas investigações. Assim, ao observar a granularidade, é possível que a percepção de severidade mude. Por exemplo, ao olhar na granularidade de métodos, o método mais problemático e, portanto, prioritário para avaliação é um. Ao mudar a granularidade para classes e, assim, ter uma composição de co-ocorrências de todos os [smells](#smells) dos métodos e os smells presentes na classe, esta percepção tende a mudar;
* **Smells possuem diferentes níveis de intervenções:** Ao analisar os [smells](#smells), é importante ter uma noção daqueles que exigem uma intervenção imediata, intervenção planejada ou até mesmo que possam continuar no repositório, pois não afetam o projeto.
Visando contemplar todas estas considerações, o **DR-Tools Code Health** implementa um modelo de [priorização](#ranking-e-priorização) para [smells](#smells) para apoiar atividades de desenvolvimento e manutenção de software, ajudando os desenvolvedores na classificação e seleção dos elementos de código afetados pelos smells.

## Termos Usados

Internamente, o **DR-Tools Code Health** implementa um modelo que integra informações de definição, identificação, [priorização e filtro](#ranking-e-priorização) dos [smells](#smells). A forma de priorização combina critérios como severidade, representatividade, impacto na qualidade do código e intervenção do time. 
Além dos critérios, descrevemos em mais detalhes os [termos](#termos) adotados pelo **DR-Tools Code Health**. Para os elementos de código, adotamos o termo *namespace* para representar pacote, *type* para representar classe e *method* representando método.

A estrutura do modelo permite que atributos de qualidade sejam associados com os smells, para apoiar a priorização e filtragem. 

### Termos
| Termo                  | Descrição                       |
|:-----------------------|:--------------------------------|
|*Severidade* | define o quão grave é um smell e, consequentemente, um elemento de código afetado por vários smells|
|*Representatividade* | instâncias de um mesmo smell, recorrente em diferentes elementos de código, representando seu impacto na granularidade considerada. Com base na representatividade, é possível avaliar quais elementos de código são mais afetados por smells em uma dada granularidade|
|*Intervenção* | define o grau de envolvimento das pessoas desenvolvedoras para o smell que afetam os elementos de código, considerando o alvo (e.g., uma classe) e a ação necessária (imediata, planejada ou futura)|
|*Smell* | Termo adotado para representar problemas de design no código, independente da granularidade|
|*Heurística do Smell*| Definição da forma de detecção do smell, representando uma regra de detecção ou algo mais elaborado, como um algoritmo para sua detecção|
|*Elemento de Código*| Pode representar pacote(s), classe(s) e/ou método(s) de um sistema|
|*Granularidade*| Representa o nível do elemento de código, considerando todas as instâncias de smell do contexto (*Namespace, Type* ou *Method*)|
|*Diversidade*| Representa a quantidade de smells em um mesmo elemento de código|
|*Co-Occorência de Smells*| Estrutura que detecta a(s) mesma(s) ocorrência(s) de um conjunto de smells em diferentes elementos de código |
|*Threshold*| Limite definido para a métrica que pode compor a heurística de detecção do smell|
|*Code Disease Indicator (CDI)*| É um valor agregado que define a criticidade total de um elemento de código|
|*CDI Composition*| Representa o acúmulo de CDIs dos smells nos elementos de código, considerando o contexto atual e granularidades inferiores|

Além disso, o modelo implementado permite que [pesos sejam atribuídos](#customizações-via-arquivo-de-configuração) aos atributos de qualidade. Outros pesos também foram inseridos para refletir a percepção dos desenvolvedores em relação ao seu contexto de projeto. 
Os desenvolvedores podem ajustar e customizar esses parâmetros no [arquivo de configuração](#modo-cli) gerado no projeto (`drtools-config.properties`).  

Os [critérios](#critérios) possuem pesos que podem ser definidos pelos desenvolvedores e utilizados na computação da [priorização](#ranking-e-priorização).
Assim, o modelo combina aspectos quantitativos (*e.g.*, quantidade de smells em um elemento de código), com aspectos qualitativos (efeito de um smell na qualidade) e a percepção dos desenvolvedores, com a definição de pesos em outros dos critérios considerados.

### Critérios

| Critério                  | Descrição                       |
|:-----------------------|:--------------------------------|
|*Threshold* | definido para a métrica, segundo referências da literatura e que pode ser redefinido pelos desenvolvedores levando em consideração o contexto do projeto|
|*Importance* | Peso dado pelo desenvolvedor a um smell em relação a sua importância (*default=1*)|
|*Quality Impact* | Peso dado pelo desenvolvedor para o impacto em dado atributo de qualidade (*default=1*)|
|*Intervention* | Peso dado pelo desenvolvedor em relação a intervenção ao tratar um dado smell (*default=1*)|

# Como usar?

## Instalação e Requisitos

Para começar a usar o **DR-Tools Code Health**, basta descompactar o arquivo `drtools-code-healthX.X.zip` em um diretório (*e.g.*, `\drtools-code-health\`),  onde `X.X` representa a respectiva versão da ferramenta.

É recomendado que este diretório seja colocado no *System Path* do sistema.
O único pré-requisito é o *JRE* (Java Runtime Environment) versão 11 ou superior.

Para verificar se a ferramenta está pronta para uso, basta rodar o seguinte comando no prompt:
```
prompt> drtools-code-health <enter>
```

O resultado esperado deve ser este:

```
:: DRTools-Code-Health :: helping you to improve the health of your source code and reduce technical debt!

Usage: drtools-code-health <project-directory> [OPTIONS]
OPTIONS = <--init | --analyze [output directory]>

        CLI OPTIONS
Options                 Description
--init                  generate the start configuration about the project
--analyze               analyze project information about metrics, smells, ranking, and quality. If [output directory] is specified, the output results are saved there

        Usage examples:
        Example 1 : # drtools-code-health \Project\Java\src --init
        Example 2 : # drtools-code-health \Project\Java\src --analyze
        Example 3 : # drtools-code-health \Project\Java\src --analyze \temp\
```

O **DR-Tools Code Health** possui dois modos de execução: [CLI](#modo-cli) e [interativo](#modo-interativo). 

## Modo CLI

O modo CLI (*command line interface*) permite que as pessoas usuárias da ferramenta customizem os parâmetros para o projeto conforme a necessidade e também obtenham os resultados da análise do **DR-Tools Code Health**.

Ao utilizar as opções do modo CLI, o **DR-Tools Code Health** criará a seguinte estrutura de diretórios, por padrão.

```
.drtools
├───analysis
├───config
└───templates
```
A seguir, apresentamos estas opções em mais detalhes.

### Opção --init

A opção `--init` permite que as pessoas usuárias customizem os parâmetros utilizados para [detecção dos smells](#smells) e para a [priorização dos problemas](#ranking-e-priorização) no código. 

##### Uso do comando **--init**
```
prompt> drtools-code-health \temp\findbugs-3.0.1\src\ --init

:: DRTools-Code-Health :: helping you to improve the health of your source code and reduce technical debt!

starting project findbugs-3.0.1
validating \temp\findbugs-3.0.1\src\
loading configurations...
parsing source code...
loading code elements...
detecting code smells...
computing ranking of code smells...
prioritizing code smells...
Processing time: 31 seconds
creating directories if not exist...
creating config file...
creating template file (high threshold)...
creating template file (outlier)...
creating template file (defined by user)...
```
Ao utilizar esta opção, o **DR-Tools Code Health** criará um diretório `.drtools/` dentro da estrutura de diretórios do repositório analisado. Este diretório é usado pela ferramenta para armazenar os arquivos de configuração. São criados outros 2 sub-diretórios (`config` e `templates`, respectivamente).

Teremos uma uma estrutura de diretórios e arquivos como esta:

```
.drtools
├───config
│   ├───drtools-config.properties
└───templates
    ├───drtools-config-defined-by-user.properties
    ├───drtools-config-high-threshold.properties
    └───drtools-config-outlier.properties
```

O arquivo `drtools-config.properties` (diretório `config`) contém os parâmetros utilizados para analisar o código.
Ainda, são gerados outros arquivos no diretório `templates`, com outras configurações que também podem ser utilizadas, com variações nos *thresholds* das métricas. São eles:
* `drtools-config-defined-by-user.properties`: arquivo similar ao que está o diretório `config`, no qual as pessoas podem customizar conforme acharem conveniente
* `drtools-config-high-threshold.properties`: arquivo com *thresholds* baseados na soma da média com o desvio padrão da respectiva métrica
* `drtools-config-outlier.properties`: arquivo com *thresholds* baseados no outlier da métrica, considerando informações estatísticas do código

Estes arquivos são identificados na ferramenta a partir do conceito de *Profile*. O *Profile* ajuda a customizar e definir nomes para estas configurações, permitindo que as pessoas desenvolvedoras gerenciem melhor o uso destes parâmetros no **DR-Tools Code Health**.

Para utilizar as configurações que estão no diretório `templates`, basta mover o arquivo para o diretório `config` e renomear o arquivo para `drtools-config.properties`.


### Opção --analyze

A opção `--analyze` gera os resultados da análise de código, organizados por [*métricas*](#métricas), [*smells*](#smells)  e [*ranking*](#ranking-e-priorização). Os resultados estão disponíveis em arquivos no formato `CSV` e `JSON`.

Estes resultados são gerados no diretório `analysis`, identificados por data e hora de geração, conforme esta estrutura:
```
.drtools
├───analysis
│   ├───20230424_2134_findbugs-3.0.1
│   │   ├───metrics
│   │   ├───ranking
│   │   └───smells
│   └───20230424_2215_findbugs-3.0.1
│       ├───metrics
│       ├───ranking
│       └───smells
├───config
└───templates
```

Também, é possível gerar os resultados em um diretório específico, conforme o comando `drtools-code-health <diretório do projeto> --analyze \temp`. Assim, toda a estrutura de diretórios (`.drtools/analysis/`) estará dentro do diretório `\temp`. 

Este tipo de recurso é útil, pois as análises não ficariam dentro da estrutura de diretórios do repositório do projeto, podendo ser usada como uma *task* de um processo de *build pipeline* do projeto.


## Modo Interativo

Além do [modo CLI](#modo-cli), é possível executar o **DR-Tools Code Health** no modo interativo. Nesta opção, após a análise prévia do código do projeto, é fornecido um prompt com vários comandos de consulta a [métricas](#métricas), [smells](#smells) e [ranking](#ranking-e-priorização) de problemas no código.


##### Comandos básicos
```
prompt> drtools-code-health \temp\findbugs-3.0.1\src\

:: DRTools-Code-Health :: helping you to improve the health of your source code and reduce technical debt!

starting project findbugs-3.0.1
validating \temp\findbugs-3.0.1\src\
loading configurations...
parsing source code...
loading code elements...
detecting code smells...
computing ranking of code smells...
prioritizing code smells...
Processing time: 20 seconds


        You can use some commands:

Shortcut        Command                                         Description
dev           about                            show development information
cls           clear                            clear screen
exit          quit                             quit from interactive mode
fn            find-namespace                   find namespaces based on search string
ft            find-type                        find types based on search string
fm            find-method                      find methods based on search string
h             help                             show this help
mh            metric-help                      show metrics help
rh            ranking-help                     show commands about bad code ranking
rtn           reset-top-number                 reset the top parameter to filtering
sh            smell-help                       show commands about smells detection

        Usage: # <COMMAND> [--top <number>]

drtools-code-health#>
```

É possível usar tanto o `shortcut` quanto o `command` no prompt. 
Existem opções específicas de pesquisa, como o seguinte exemplo, que faz uma pesquisa de uma classe (`ft` ou `find-type`) chamada `buginstance`, encontrando duas ocorrências.

```
drtools-code-health#> ft buginstance

---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.BugInstance
        SLOC: 1673 NOM: 202 NPM: 192 WMC: 430 DEP: 67 I-DEP: 28 FAN-IN: 218 FAN-OUT: 48 NOA: 28 LCOM3: 0,93 DIT: 5.0 CHILD: 0.0 NPA: 0.0
List of smells detected                         Quality Attributes Affected
God Class                                    Understandability, Maintainability
Insufficient Modularization                  Modularity, Maintainability
Hub-like Modularization                      Maintainability, Coupling
Multifaceted Abstraction                     Cohesion
Deep Hierarchy                               Inheritance
Cyclically-dependent Modularization          Modularity
TOTAL OF SMELLS DETECTED: 6

---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.BugInstanceTest
        SLOC: 153 NOM: 18 NPM: 11 WMC: 23 DEP: 8 I-DEP: 2 FAN-IN: 0 FAN-OUT: 6 NOA: 1 LCOM3: 1,00 DIT: 2.0 CHILD: 0.0 NPA: 0.0
List of smells detected                         Quality Attributes Affected
Insufficient Modularization                  Modularity, Maintainability
TOTAL OF SMELLS DETECTED: 1

TOTAL OF TYPES WITH SMELLS: 2
```
Também, é possível usar a opção `--top <número>` nos comandos, filtrando o resultado.


# Módulos

## Métricas

No [menu de métricas](#menu-deopções-para-métricas), estão disponíveis várias opções para listagem de informações em diferentes níveis de granularidade e agrupadas, conforme contexto. Para uma melhor análise dos resultados, as métricas são ordenadas por complexidade e tamanho, buscando trazer os pontos mais complexos dos elementos de código.

##### Menu de opções para métricas
``` 
METRIC HELP
Shortcut        Command                                         Description
a             all-metrics                      show ALL metrics (namespaces/types/methods)
ac            all-metrics-coupling             show ALL metrics about COUPLING/DEPENDENCIES
s             metric-summary                   show SUMMARY of project
n             metric-namespace                 show information about NAMESPACES (packages)
t             metric-type                      show METRIC information about TYPES (classes)
m             metric-method                    show METRIC information about METHODS (functions)
d             metric-dependencies              show information about DEPENDENCIES of types
cd            metric-cyclic-dependencies       show information about CYCLIC DEPENDENCIES of types
id            metric-internal-dependencies     show information about INTERNAL DEPENDENCIES of types
nc            metric-namespace-coupling        show information about NAMESPACE COUPLING
tc            metric-type-coupling             show information about TYPE COUPLING
ml            metric-list                      show METRICS used
mt            metric-threshold                 show information about METRIC THRESHOLDS
sn            metric-stats-namespace           show the STATISTICS of NAMESPACE metrics
st            metric-stats-type                show the STATISTICS of TYPE metrics
sm            metric-stats-method              show the STATISTICS of METHOD metrics
san           metric-stats-and-namespace       show the STATISTICS AND NAMESPACES
sat           metric-stats-and-type            show the STATISTICS AND TYPES
sam           metric-stats-and-method          show the STATISTICS AND METHODS
```

O **DR-Tools Code Health** apresenta 48 métricas contextualizadas por *summary*, *namespaces*, *types*, *methods*, *dependencies* e *coupling*.
A seguir, apresentamos a lista por contexto: 

* **Summary (15):** *Total of namespaces, total of types, mean number of types/namespaces, total of lines of code (SLOC), average number of SLOC/types (with median and standard deviation), total of methods, average number of methods/types (com median e standard deviation), total of complexity (CYCLO), e average number of complexity/types (com median and standard deviation)*
* **Namespaces (2):** *Number of classes/types (NOC) e number of abstract classes (NAC)*
* **Types (14):** *Lines of code (SLOC), number of methods (NOM), number of public methods (NPM), class complexity (WMC), number of dependencies (DEP), number of internal dependencies (I-DEP), Number of other types that depend on a given type (FAN-IN), number of other types referenced by a type (FAN-OUT), number of fields/attributes (NOA), lack of cohesion in methods (LCOM3), deep in inheritance tree (DIT), number of children (CHILD), number of public attributes/fields (NPA) e number of cyclic dependencies (types) (CDEP)*
* **Methods (5):** *Lines of code (MLOC), cyclomatic complexity (CYCLO), number of invocations (CALLS), nested block depth (NBD) e number of parameters (PARAM)*
* **Namespace Coupling (5):** *Afferent coupling (CA), efferent coupling (CE), instability (I), abstractness degree (A) e normalized distance (D)*
* **Type Coupling (4):** *number of dependencies (DEP), number of internal dependencies (I-DEP), Number of other types that depend on a given type (FAN-IN) e number of other types referenced by a type (FAN-OUT)*
* **Dependencies (3):** *General dependencies (DEP), internal dependencies (I-DEP) e cyclic dependencies (CDEP)*

Também, o **DR-Tools Code Health** fornece uma análise estatística com quartis, média, mediana, valores mínimo e máximo, desvio padrão, amplitude e outlier para as métricas analisadas, além, é claro, do *threshold* dado pela literatura. Assim, é possível comparar os dados coletados do código com os dados estatísticos do projeto.  

##### Exemplo de consulta dos 3 *types* com maior tamanho e complexidade, suas respectivas métricas e análise estatística 
```
drtools-code-health#> sat --top 3
-----------------------------------------------------------------------------------------
METRIC  1stQ    3rdQ    Avg     Median  Min     Max     Max-Min StdDev  U-Fnc   Threshold
-----------------------------------------------------------------------------------------
SLOC    21,00   129,00  118,30  56,00   3,00    3001,00 2998,00 195,00  291,00  500,00
NOM     3,00    11,00   10,13   5,00    0,00    202,00  202,00  16,38   23,00   14,00
NPM     2,00    9,00    8,64    4,00    0,00    198,00  198,00  14,71   19,50   20,00
WMC     4,00    29,00   26,46   11,00   1,00    628,00  627,00  47,15   66,50   100,00
DEP     2,00    13,00   9,75    5,00    0,00    103,00  103,00  12,38   29,50   20,00
I-DEP   0,00    6,00    4,48    2,00    0,00    69,00   69,00   6,78    15,00   15,00
FAN-IN  0,00    4,00    5,62    1,00    0,00    218,00  218,00  16,51   10,00   10,00
FAN-OUT 2,00    10,00   7,85    5,00    0,00    75,00   75,00   8,85    22,00   15,00
NOA     0,00    6,00    4,68    2,00    0,00    76,00   76,00   7,41    15,00   8,00
LCOM3   0,00    0,92    0,56    0,75    0,00    2,00    2,00    0,44    2,30    0,80
DIT     1,00    2,00    1,66    1,00    1,00    10,00   9,00    1,02    3,50    4,00
CHILD   0,00    0,00    0,17    0,00    0,00    32,00   32,00   1,15    0,00    8,00
NPA     0,00    0,00    0,08    0,00    0,00    38,00   38,00   1,25    0,00    1,00
CDEP    0,00    0,00    0,06    0,00    0,00    9,00    9,00    0,42    0,00    1,00


Legend:
        1stQ=First Quartile | 3rdQ=Third Quartile | Avg=Average | Median=Median | Min=Min value | Max=Max value
        Max-Min=Amplitude | StdDev=Standard Deviation | U-Fnc=Upper Fence | Threshold=Metric Threshold


-------------------------------------------------------------------------------------------------------------------------------------------------------------------
TYPES                                                           SLOC    NOM     NPM     WMC     DEP     I-DEP   FAN-IN  FAN-OUT NOA     LCOM3   DIT     CHILD   NPA
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
                             edu.umd.cs.findbugs.OpcodeStack    3001    157     104     628     58      22      45      46      76      0,76    2       0       0
                             edu.umd.cs.findbugs.BugInstance    1673    202     192     430     67      28      218     48      28      0,93    5       0       0
                    edu.umd.cs.findbugs.detect.FindNullDeref    1375    40      20      247     103     69      0       75      17      0,79    1       0       0
``` 

## Smells

Os smells são sintomas de possíveis problemas de design no código, que podem afetar sua manutenção e evolução. 
Diversas técnicas de detecção foram propostas para a identificação destes smells a partir do código. Essas técnicas retornam os elementos de código identificados que, por sua vez, requerem esforço para sua remoção, usando refactorings ou outras técnicas apropriadas. 

No **DR-Tools Code Health**, definimos uma série de comandos que permitem analisar os smells em diferentes granularidades no projeto.

##### Menu de opções para consultas sobre os smells
```
 SMELLS HELP
Shortcut        Command                                         Description
ss            smell-summary                    show summary of smells detected
sco           smell-cooccurrences              show summary of cooccurrences of smells
scot          smell-cooccurrences-types        show summary of cooccurrences of smells (Type Category)
scom          smell-cooccurrences-methods      show summary of cooccurrences of smells (Method Category)
scd           criteria-default                 show criteria used (default values)
ls            list-smell                       show smells detected by the DR-Tools Code Health
sd            smell-details                    show smell details (granularity, importance, intervention, and impact on quality)
lsn           smell-namespaces                 show namespace smells
lst           smell-types                      show type smells
lsm           smell-methods                    show method smells
```

### Lista de Smells Detectados

Atualmente, o **DR-Tools Code Health** detecta 17 tipos de smells, nas granularidades de **namespace, type** e **method**. 
Os smells afetam a qualidade e, na maioria dos casos, o propósito da
reestruturação e refactorings tem como intenção e motivação melhorar a qualidade. Portanto, é importante associar os problemas com a qualidade e usá-los como um dos critérios de priorização, expondo as partes mais problemáticas para as pessoas desenvolvedoras do time.

A seguir, listamos as informações dos smells com respectivas informações adicionais como importância, impacto na qualidade e intervenção.  
Estas informações dos smells estão disponíveis através da opção `sd` do **DR-Tools Code Health**.

#### Granularidade: Namespace

* **Nome:** *Too Large Package*
	* **Descrição:** Packages/Subsystems with a high number of classes/packages indicate that they serve more than one specific responsibility
	* **Importância:** NORMAL
	* **Impacto na Qualidade:** Understandability, Modularity
	* **Ação de intervenção:** PLANNED
	* **Referência:** M. Lippert, S. Roock, “Refactoring in Large Software Projects: Performing Complex Restructurings Successfully”. John Wiley and Sons, 2006.

* **Nome:** *Cyclic Dependency*
	* **Descrição:** This smell occurs when two or more packages/subsystems depend on each other
	* **Importância:** HIGH
	* **Impacto na Qualidade:** Coupling, Modularity
	* **Ação de intervenção:** NORMAL
	* **Referência:** M. Lippert, S. Roock, “Refactoring in Large Software Projects: Performing Complex Restructurings Successfully”. John Wiley and Sons, 2006.

#### Granularidade: Type

* **Nome:** *God Class*
	* **Descrição:** This smell is huge, complex, and has an extremely high number of fields and methods
	* **Importância:** HIGH
	* **Impacto na Qualidade:** Understandability, Maintainability
	* **Ação de intervenção:** PLANNED
	* **Referência:** Bafandeh Mayvan B, Rasoolzadegan A, Javan Jafari A. Bad smell detection using quality metrics and refactoring opportunities. J Softw Evol Proc. 2020.
	
* **Nome:** *Broken Modularization*
	* **Descrição:** This smell arises when data and/or methods that ideally should have been localized into a single abstraction are separated and spread across multiple abstractions
	* **Importância:** LOW
	* **Impacto na Qualidade:** Modularity, Maintainability
	* **Ação de intervenção:** PLANNED
	* **Referência:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Nome:** *Cyclically Dependent Modularization*
	* **Descrição:** This smell arises when two or more abstractions depend on each other directly or indirectly
	* **Importância:** HIGH
	* **Impacto na Qualidade:** Modularity
	* **Ação de intervenção:** NORMAL
	* **Referência:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Nome:** *Insufficient Modularization*
	* **Descrição:** This smell arises when an abstraction exists that has not been completely decomposed, and a further decomposition could reduce its size, implementation complexity, or both
	* **Importância:** NORMAL
	* **Impacto na Qualidade:** Modularity, Maintainability
	* **Ação de intervenção:** PLANNED
	* **Referência:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Nome:** *Deep Hierarchy*
	* **Descrição:** This smell arises when an inheritance hierarchy is 'excessively' deep
	* **Importância:** NORMAL
	* **Impacto na Qualidade:** Inheritance
	* **Ação de intervenção:** PLANNED
	* **Referência:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Nome:** *Deficient Encapsulation*
	* **Descrição:** This smell occurs when the declared accessibility of one or more members of an abstraction is more permissive than actually required
	* **Importância:** NORMAL
	* **Impacto na Qualidade:** Encapsulation
	* **Ação de intervenção:** PLANNED
	* **Referência:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Nome:** *Hub-like Modularization*
	* **Descrição:** This smell arises when an abstraction has dependencies (both incoming and outgoing) with a large number of other abstractions
	* **Importância:** HIGH
	* **Impacto na Qualidade:** Maintainability, Coupling
	* **Ação de intervenção:** PLANNED
	* **Referência:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Nome:** *Multifaceted Abstraction*
	* **Descrição:** This smell arises when an abstraction has more than one responsibility assigned to it
	* **Importância:** NORMAL
	* **Impacto na Qualidade:** Cohesion
	* **Ação de intervenção:** PLANNED
	* **Referência:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Nome:** *Wide Hierarchy*
	* **Descrição:** This smell arises when an inheritance hierarchy is 'too' wide indicating that intermediate types may be missing
	* **Importância:** NORMAL
	* **Impacto na Qualidade:** Inheritance
	* **Ação de intervenção:** PLANNED
	* **Referência:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

#### Granularidade: Method

* **Nome:** *Long Method*
	* **Descrição:** This smell occurs when a method is too long to understand
	* **Importância:** HIGH
	* **Impacto na Qualidade:** Understandability, Maintainability
	* **Ação de intervenção:** PLANNED
	* **Referência:** M. Fowler, “Refactoring: Improving the Design of Existing Code”. Addison-Wesley, 1999.

* **Nome:** *Long Parameter List*
	* **Descrição:** This smell occurs when a method accepts a long list of parameters
	* **Importância:** LOW
	* **Impacto na Qualidade:** Maintainability
	* **Ação de intervenção:** LOW
	* **Referência:** M. Fowler, “Refactoring: Improving the Design of Existing Code”. Addison-Wesley, 1999.

* **Nome:** *Complex Method*
	* **Descrição:** This smell occurs when a method has high cyclomatic complexity
	* **Importância:** HIGH
	* **Impacto na Qualidade:** Understandability, Maintainability, Complexity
	* **Ação de intervenção:** NORMAL
	* **Referência:** Tushar Sharma, Marios Fragkoulis, and Diomidis Spinellis. * “House of Cards: Code Smells in Open-Source C# Repositories". * in ACM/IEEE International Symposium on Empirical Software Engineering and Measurement (ESEM), 2017.

* **Nome:** *Bumpy Road*
	* **Descrição:** This smell occurs when a method has high nested blocks
	* **Importância:** CRITICAL
	* **Impacto na Qualidade:** Understandability, Maintainability, Complexity
	* **Ação de intervenção:** IMMEDIATE
	* **Referência:** Tornhill, Adam. [The Bumpy Road Code Smell: Measuring Code Complexity by its Shape and Distribution](https://codescene.com/blog/bumpy-road-code-complexity-in-context/), 2023.  


Como exemplo, podemos listar os smells de *type*, filtrando as 3 primeiras classes. É possível observar os smells encontrados em cada classe e seus respectivos impactos na qualidade.
```
drtools-code-health#> lst --top 3

---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.ba.ch.Subtypes2
        SLOC: 806 NOM: 58 NPM: 38 WMC: 159 DEP: 28 I-DEP: 9 FAN-IN: 46 FAN-OUT: 15 NOA: 19 LCOM3: 0,84 DIT: 1.0 CHILD: 0.0 NPA: 0.0
List of smells detected                         Quality Attributes Affected
God Class                                    Understandability, Maintainability
Insufficient Modularization                  Modularity, Maintainability
Hub-like Modularization                      Maintainability, Coupling
Multifaceted Abstraction                     Cohesion
Cyclically-dependent Modularization          Modularity
TOTAL OF SMELLS DETECTED: 5

---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.BugInstance
        SLOC: 1673 NOM: 202 NPM: 192 WMC: 430 DEP: 67 I-DEP: 28 FAN-IN: 218 FAN-OUT: 48 NOA: 28 LCOM3: 0,93 DIT: 5.0 CHILD: 0.0 NPA: 0.0
List of smells detected                         Quality Attributes Affected
God Class                                    Understandability, Maintainability
Insufficient Modularization                  Modularity, Maintainability
Hub-like Modularization                      Maintainability, Coupling
Multifaceted Abstraction                     Cohesion
Deep Hierarchy                               Inheritance
Cyclically-dependent Modularization          Modularity
TOTAL OF SMELLS DETECTED: 6

---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.ba.AnalysisContext
        SLOC: 612 NOM: 84 NPM: 80 WMC: 146 DEP: 47 I-DEP: 28 FAN-IN: 137 FAN-OUT: 36 NOA: 30 LCOM3: 0,83 DIT: 1.0 CHILD: 0.0 NPA: 0.0
List of smells detected                         Quality Attributes Affected
God Class                                    Understandability, Maintainability
Insufficient Modularization                  Modularity, Maintainability
Hub-like Modularization                      Maintainability, Coupling
Multifaceted Abstraction                     Cohesion
Cyclically-dependent Modularization          Modularity
TOTAL OF SMELLS DETECTED: 5

TOTAL OF TYPES WITH SMELLS: 3
```


### Lista de Co-Ocorrências de Smells Detectadas

O **DR-Tools Code Health** também detecta vários tipos de co-ocorrências de smells, categorizados em *type* e *method*. As co-ocorrências podem indicar certos antipadrões de design que se repetem no projeto. 

#### Categoria: Type

* **Descrição:** *Big structures in types with modularization problems*
	* **Smells:** God Class, Insufficient Modularization, Hub-like Modularization, Cyclically Dependent Modularization, Multifaceted Abstraction
	* **Impacto na Qualidade:** Size, Cohesion, Complexity, Coupling

* **Descrição:** *Critical!! Structures in types with too many smells*
	* **Smells:** God Class, Insufficient Modularization, Hub-like Modularization, Cyclically Dependent Modularization, Multifaceted Abstraction, Deep Hierarchy
	* **Impacto na Qualidade:** Size, Cohesion, Complexity, Coupling

* **Descrição:** *Complex structures in types*
	* **Smells:** God Class, Insufficient Modularization, Multifaceted Abstraction
	* **Impacto na Qualidade:** Size, Complexity

* **Descrição:** *Deep hierarchy, big and low cohesion structures in types*
	* **Smells:** God Class, Insufficient Modularization, Deep Hierarchy, Multifaceted Abstraction
	* **Impacto na Qualidade:** Size, Complexity

* **Descrição:** *Complex and low cohesion structures in types*
	* **Smells:** Insufficient Modularization, Multifaceted Abstraction
	* **Impacto na Qualidade:** Cohesion, Complexity

* **Descrição:** *Low cohesion and cyclic dependency structures in types*
	* **Smells:** Insufficient Modularization, Cyclically Dependent Modularization
	* **Impacto na Qualidade:** Cohesion, Coupling

* **Descrição:** *Low cohesion and big structures in types*
	* **Smells:** Insufficient Modularization, God Class
	* **Impacto na Qualidade:** Cohesion, Size

* **Descrição:** *Low cohesion and high coupling structures in types*
	* **Smells:** Insufficient Modularization, Hub-like Modularization
	* **Impacto na Qualidade:** Cohesion, Coupling

* **Descrição:** *Low cohesion, high coupling, and big structures in types*
	* **Smells:** God Class, Insufficient Modularization, Hub-like Modularization
	* **Impacto na Qualidade:** Cohesion, Coupling, Size

#### Categoria: Method

* **Descrição:** *Big structures in methods*
	* **Smells:** Long Method, Long Parameter List
	* **Impacto na Qualidade:** Size

* **Descrição:** *Complex structures in methods*
	* **Smells:** Long Method, Bumpy Road, Complex Method
	* **Impacto na Qualidade:** Complexity

* **Descrição:** *Hard structures to understand in methods*
	* **Smells:** Long Method, Bumpy Road
	* **Impacto na Qualidade:** Complexity, Size

* **Descrição:** *Structures difficult to maintain in methods*
	* **Smells:** Complex Method, Long Method
	* **Impacto na Qualidade:** Complexity, Size

* **Descrição:** *High complexity structures in methods*
	* **Smells:** Complex Method, Bumpy Road
	* **Impacto na Qualidade:** Complexity

* **Descrição:** *Critical!! Structures in methods with too many smells*
	* **Smells:** Complex Method, Bumpy Road, Long Method, Long Parameter List
	* **Impacto na Qualidade:** Complexity, Size

* **Descrição:** *Several parameters and complex structures in methods*
	* **Smells:** Complex Method, Long Parameter List
	* **Impacto na Qualidade:** Complexity, Size

É possível identificar as co-ocorrências dos smells e suas respectivas quantidades detectadas a partir do comando `sco`.

```
drtools-code-health#> sco
-------------------------------------------
CODE ANALYSIS BASED ON SMELL CO-OCCURRENCES
-------------------------------------------

Category: TYPE
DESCRIPTION                                                     # of Instance(s)
Low cohesion and cyclic dependency structures in types                 16  (6,45%)
  (Insufficient Modularization, Cyclically-dependent Modularization)
   Impacts on Cohesion, Coupling
Complex and low cohesion structures in types                           16  (6,45%)
  (Insufficient Modularization, Multifaceted Abstraction)
   Impacts on Cohesion, Complexity
Low cohesion and big structures in types                               16  (6,45%)
  (Insufficient Modularization, God Class)
   Impacts on Cohesion, Size
Complex structures in types                                            6  (2,42%)
  (God Class, Insufficient Modularization, Multifaceted Abstraction)
   Impacts on Complexity, Size
Low cohesion and high coupling structures in types                     4  (1,61%)
  (Insufficient Modularization, Hub-Like Modularization)
   Impacts on Cohesion, Coupling
Big structures in types with modularization problems                   2  (0,81%)
  (God Class, Insufficient Modularization, Hub-Like Modularization, Cyclically-dependent Modularization, Multifaceted Abstraction)
   Impacts on Size, Complexity, Cohesion, Coupling
Low cohesion, high coupling, and big structures in types               2  (0,81%)
  (God Class, Insufficient Modularization, Hub-Like Modularization)
   Impacts on Cohesion, Coupling, Size
Critical!! Structures in types with too many smells                    1  (0,40%)
  (God Class, Insufficient Modularization, Cyclically-dependent Modularization, Hub-Like Modularization, Multifaceted Abstraction, Deep Hierarchy)
   Impacts on Complexity, Size, Cohesion, Coupling
Deep hierarchy, big and low cohesion structures in types               1  (0,40%)
  (God Class, Insufficient Modularization, Deep Hierarchy, Multifaceted Abstraction)
   Impacts on Complexity, Size

Category: METHOD
DESCRIPTION                                                     # of Instance(s)
Structures difficult to maintain in methods                            391  (20,50%)
  (Complex Method, Long Method)
   Impacts on Complexity, Size
Complex structures in methods                                          240  (12,59%)
  (Bumpy Road, Long Method, Complex Method)
   Impacts on Complexity
High complexity structures in methods                                  61  (3,20%)
  (Complex Method, Bumpy Road)
   Impacts on Complexity
Several parameters and complex structures in methods                   19  (1,00%)
  (Long Parameter List, Complex Method)
   Impacts on Complexity, Size
Critical!! Structures in methods with too many smells                  10  (0,52%)
  (Long Parameter List, Complex Method, Long Method, Bumpy Road)
   Impacts on Complexity, Size
Big structures in methods                                              6  (0,31%)
  (Long Method, Long Parameter List)
   Impacts on Size
```

## Ranking e Priorização

Mais importante do que detectar os possíveis problemas, representado pelos [smells](#smells), é importante fornecermos mais mecanismos de análise. 
Todos estes problemas não são igualmente importantes ou relevantes para os objetivos do software ou mesmo pensando em sua qualidade. As pessoas desenvolvedoras não resolvem os problemas com frequência, e uma das possíveis causas estejam ligadas a falta de um método de priorização e filtragem que leve em consideração aspectos de qualidade e a própria percepção dos desenvolvedores naquele momento do projeto.

O **DR-Tools Code Health** implementa um modelo de priorização que considera critérios como severidade, representatividade, impacto na qualidade e grau de intervenção, de forma individual e agregada para apresentar os elementos de código mais problemáticos. 

A **severidade** de um elemento de código é dada pelo soma das severidades dos smells presentes no elemento, considerando os pesos dado a sua importância, que pode ser definido pela pessoa desenvolvedora.

A **representatividade** define o quão representativos são os smells presentes em um elemento de código frente a outros smells na mesma granularidade. Com base na representatividade, é possível avaliar quais elementos de código são mais afetados por smells em uma dada granularidade.

O **impacto na qualidade** é dado pelo mapeamento dos atributos de qualidade vinculados aos smells com os pesos desses impactos, também definidos pela pessoa desenvolvedora.

A **intervenção** define o grau de importância em tratar um dado elemento de código, considerando a percepção das pessoas desenvolvedoras, que poderá customizar estes pesos.

> *"A qualidade é um dos aspectos mais importantes no desenvolvimento de software"* (SOMMERVILLE, 2016). 
>
> Conforme Yamashita (YAMASHITA, 2015), *"os atributos de
qualidade estão presentes em um problema se existe um mapeamento na definição do problema com estes atributos."* 

O **DR-Tools Code Health** permite analisar cada um desses critérios de forma individual e na sua respectiva granularidade. Estes valores são apresentados e normalizados em uma escala de 1 (menos crítico) a 10 (mais crítico). 
Além disso, é fornecido um indicador normalizado que agrega todos esses critérios, chamado *Code Disease Indicator (CDI)*, com valores entre 1 (menor crítico) e 100 (mais crítico).
Ainda, como os elementos de código são autocontidos, temos o *CCDI (Code Disease Indicator Composition)*, que agrega os CDIs de um elemento de código de menor granularidade (*e.g.*, métodos) com o CDI de uma granularidade maior (*e.g.*, classe), seguindo os mesmos intervalos definidos para o CDI.

A seguir, são apresentadas as opções disponíveis para ranking e priorização dos problemas de código apresentados pelo **DR-Tools Code Health**.

##### Menu de opções para ranking dos problemas
```
RANKING HELP
Shortcut        Command                                         Description
rs            ranking-summary                  show summary of smells ranking
sevn          severity-namespaces              show namespaces with the most severity
repn          representativity-namespaces      show namespaces with the most representativity (more smell instances, considering the same granularity)
quan          quality-namespaces               show namespaces with the most quality impacted
intn          intervention-namespaces          show namespaces with the most intervention needed
cdin          cdi-namespaces                   show the CDI (Code Disease Indicator) of namespaces
ccdin         comp-cdi-namespaces              show the composition CDI (Code Disease Indicator) of namespaces, including their types/methods
ccdit         comp-cdi-types                   show the composition CDI (Code Disease Indicator) of types, including their methods
sevt          severity-types                   show types with the most severity
rept          representativity-types           show types with the most representativity (more smell instances, considering the same granularity)
quat          quality-types                    show types with the most quality impacted
intt          intervention-types               show types with the most intervention needed
cdit          cdi-types                        show the CDI (Code Disease Indicator) of types
sevm          severity-methods                 show methods with the most severity
repm          representativity-methods         show methods with the most representativity (more smell instances, considering the same granularity)
quam          quality-methods                  show methods with the most quality impacted
intm          intervention-methods             show methods with the most intervention needed
cdim          cdi-methods                      show the CDI (Code Disease Indicator) of methods
```

# Como analisar código com o DR-Tools Code Health?

A detecção de problemas no código reduz o custo de manutenção se as falhas forem encontradas nos estágios iniciais de desenvolvimento de software. Ao desenvolver o **DR-Tools Code Health**, pensamos em como ele poderia ser utilizado no fluxo de desenvolvimento e como envolver as pessoas desenvolvedoras neste processo. Afinal, as pessoas desenvolvedoras são importantes no processo e sua participação na adaptação de técnicas semi-automatizadas tornam-as mais eficazes.
Além disso, é importante que as ferramentas sejam simples, com mecanismos de filtragem e estejam conectadas ao fluxo de desenvolvimento, permitindo ações pontuais e contínuas. 

## Definindo um Propósito de Análise

> *"Grande parte do dinheiro gasto com desenvolvimento de software é usado para entender códigos existentes."* 
> Kent Beck

A *análise de código* é uma disciplina que toda a pessoa desenvolvedora deve desenvolver, aprimorar para acelerar o seu entendimento e compreensão do código, visando facilitar o processo de manutenção e evolução do projeto de software.
Dentro dos pilares da análise, temos que acompanhar a evolução sob a ótica da coesão, acoplamento, tamanho e complexidade, gerenciando sua complexidade, independente de paradigma de programação. 

Para iniciar uma análise, é importante **definir o seu objetivo/propósito**.
Este objetivo pode ser: melhorar algum aspecto de qualidade, identificar possíveis problemas em uma granularidade específica, avaliar problemas que aparecem recorrentemente no código, melhorar o design, entre outros.

Depois, **atualize o seu repositório local do projeto**.
Você pode começar **analisando aspectos mais gerais do projeto**.
Comece **observando as métricas mais gerais**, verificando sua distribuição.

Observe este exemplo:

```
drtools-code-health#> a --top 5
------------------
SUMMARY OF METRICS
------------------
            Total of Namespaces: 57
                 Total of Types: 1102 - 19,33 (number of types/namespaces - median: 8,00 - std dev: 36,29)
                  Total of SLOC: 130364 - 118,30 (number of SLOC/types - median: 56,00 - std dev: 195,00)
               Total of Methods: 10734 - 9,74 (number of methods/types - median: 6,00 - std dev: 25,51)
                 Total of CYCLO: 29224 - 26,52 (number of CYCLO/types)



----------------------------------------------------------------------
NAMESPACES                                              NOC     NAC
----------------------------------------------------------------------
                        edu.umd.cs.findbugs.detect      196     6
                               edu.umd.cs.findbugs      154     42
                            edu.umd.cs.findbugs.ba      136     39
                          edu.umd.cs.findbugs.gui2      74      7
         edu.umd.cs.findbugs.classfile.engine.bcel      46      1


-------------------------------------------------------------------------------------------------------------------------------------------------------------------
TYPES                                                           SLOC    NOM     NPM     WMC     DEP     I-DEP   FAN-IN  FAN-OUT NOA     LCOM3   DIT     CHILD   NPA
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
                             edu.umd.cs.findbugs.OpcodeStack    3001    157     104     628     58      22      45      46      76      0,76    2       0       0
                             edu.umd.cs.findbugs.BugInstance    1673    202     192     430     67      28      218     48      28      0,93    5       0       0
                    edu.umd.cs.findbugs.detect.FindNullDeref    1375    40      20      247     103     69      0       75      17      0,79    1       0       0
                      edu.umd.cs.findbugs.detect.DumbMethods    1308    30      21      578     49      29      0       42      33      0,47    10      0       0
                            edu.umd.cs.findbugs.PluginLoader    1296    53      20      185     62      21      3       36      24      0,78    1       0       0


---------------------------------------------------------------------------------------------------------------------------------------------
METHODS                                                                                                 MLOC    CYCLO   CALLS   NBD     PARAM
---------------------------------------------------------------------------------------------------------------------------------------------
                                          edu.umd.cs.findbugs.detect.DumbMethods.sawOpcode(int seen)    769     339     526     5       1
                                         edu.umd.cs.findbugs.detect.FindPuzzlers.sawOpcode(int seen)    542     242     383     6       1
                                         edu.umd.cs.findbugs.detect.UnreadFields.sawOpcode(int seen)    382     171     235     8       1
du.umd.cs.findbugs.detect.FindUselessObjects.analyzeMethod(ClassContext classContext, Method method)    232     142     68      8       2
                  edu.umd.cs.findbugs.OpcodeStack.processMethodCall(DismantleBytecode dbc, int seen)    277     124     191     4       2
drtools-code-health#>
```

Com este simples comando (`a --top 5`), conseguimos **ter uma visão em diferentes níveis do projeto**. 
Primeiramente, um resumo geral, com número de *namespaces, types* e *methods*, valores médios, medianas e desvios padrões. 
Observe que temos um valor médio de 19 *types* por *namespace*. No entanto, ao observar dados específicos, no segundo nível, relacionado a *namespaces*, observamos que os 5 pacotes listados possuem valores maiores que a média. 

No nível de *types*, também podemos ver inúmeras informações importantes, com destaque para complexidade (métrica `WMC`) e tamanho (métrica `SLOC`) das classes, além de informações de dependências (`DEP, I-DEP`), tamanho da API (`NPM`) e acoplamentos (`FAN-IN, FAN-OUT`). 


Já no nível de *methods*, podemos observar a lista de métodos dos 5 considerados mais complexos. Observe que os 3 primeiros métodos listados possuem a mesma assinatura (`sawOpcode`) de classes distintas, o que pode ser um indicativo que um método definido em uma hierarquia. 


Aqui, podemos **avaliar diferentes pontos, como tamanho ou complexidade**. Porém, imagine que você queira considerar questões de entendimento do código. Uma métrica que ajuda a identificar tais problemas é o `NBD`. Temos um método que se destaca, com alto `NBD`. Este método não é o maior, mas certamente é o que se destaca com problemas de entendimento. Para saber mais detalhes sobre os *thresholds*, você pode listá-los com o comando `mt`.
 

 ```
 drtools-code-health#> mt
-----------------------------------
INFORMATION ABOUT METRIC THRESHOLDS
-----------------------------------

Profile: Default
Description: Contains the default information, using metric threshold values as reference

Small Project (SMALL)                                       small project with < 50 KLOC or 200 < classes
Medium Project (MEDIUM)                                     medium project with (50 KLOC <= project <= 250 KLOC) or (200 <= classes <= 1000)
Large Project (LARGE)                                       large project with > 250 KLOC or > 1000 classes
Number of Types/Classes (NOC)                               Good: <= 11,0; Regular: between 11,0 and 28,0; Bad: > 28,0
Number of Abstract Types/Classes (NAC)                      without references
Type Lines of Code (SLOC)                                   Bad: > 500,0
Number of Methods (NOM)                                     Good: <= 6,0; Regular: between 6,0 and 14,0; Bad: > 14,0
Weighted Methods per Class (WMC)                            Good: <= 20,0; Regular: between 20,0 and 100,0; Bad: > 100,0
Number of external types dependencies (DEP)                 Bad: > 20,0
Number of internal types dependencies (I-DEP)               Bad: > 15,0
Number of other types that depend on a given type (FAN-IN)  Bad: > 10,0
Number of other types referenced by a type (FAN-OUT)        Bad: > 15,0
Number of Public Methods (NPM)                              Good: <= 10,0; Regular: between 10,0 and 20,0; Bad: > 20,0
Number of Attributes/Fields (NOA)                           Good: <= 3,0; Regular: between 3,0 and 8,0; Bad: > 8,0
Lack of Cohesion in Methods (LCOM3)                         Good: = 0,0; Regular: between 0,0 and 0,8; Bad: > 0,8
Deep in Inheritance Tree (DIT)                              Good: <= 2,0; Regular: between 2,0 and 4,0; Bad: > 4,0
Number of Children (CHILD)                                  Bad: > 8,0
Number of Public Attributes/Fields (NPA)                    Bad: > 1,0
Number of cyclic dependencies (namespaces/types) (CDEP)     Bad: >= 1,0
Method Lines of Code (MLOC)                                 Good: <= 10,0; Regular: between 10,0 and 30,0; Bad: > 30,0
Cyclomatic Complexity (CYCLO)                               Good: <= 2,0; Regular: between 2,0 and 4,0; Bad: > 4,0
Number of Invocations (CALLS)                               Bad: > 5,0
Nested Block Depth (NBD)                                    Good: <= 1,0; Regular: between 1,0 and 3,0; Bad: > 3,0
Number of Parameters (PARAM)                                Good: <= 2,0; Regular: between 2,0 and 4,0; Bad: > 4,0
Afferent Coupling (CA)                                      Good: <= 7,0; Regular: between 7,0 and 39,0; Bad: > 39,0
Efferent Coupling (CE)                                      Good: <= 6,0; Regular: between 6,0 and 16,0; Bad: > 16,0
Package Instability (I)                                     range between 0=Maximally stability and 1=Maximally instability
Abstractness Degree (A)                                     range between 0=Minimally abstractness and 1=Maximally abstractness
Normalized Distance (D)                                     range between 0=exactly located in the main sequence and 1=far from the main sequence
```

Além desta primeira avaliação, podemos aprofundar a análise, olhando **como está a distribuição dos sintomas (smells)** no código.
Para isso, você pode usar o comando `ss`, que apresenta um resumo dos smells identificados.

```
drtools-code-health#> ss
-----------------
SUMMARY OF SMELLS
-----------------
      Total of Namespaces with smells: 9 of 57 (15,79%), with more than one smell: 4 (44,44% of detected or 7,02% of total)
           Total of Types with smells: 248 of 1102 (22,50%), with more than one smell: 99 (39,92% of detected or 8,98% of total)
         Total of Methods with smells: 1907 of 10734 (17,77%), with more than one smell: 759 (39,80% of detected or 7,07% of total)

                SMELLS DETECTED

SMELL NAMESPACES                                                # of Instance(s)
Too Large Package/Subsystem                                      8 (88,89%)
Cyclic Dependency                                                5 (55,56%)

SMELL TYPES                                                     # of Instance(s)
Insufficient Modularization                                      220 (88,71%)
Cyclically-dependent Modularization                              43 (17,34%)
Multifaceted Abstraction                                         41 (16,53%)
God Class                                                        40 (16,13%)
Deep Hierarchy                                                   27 (10,89%)
Hub-like Modularization                                          24 (9,68%)
Deficient Encapsulation                                          11 (4,44%)
Broken Modularization                                            2 (0,81%)
Wide Hierarchy                                                   1 (0,40%)

SMELL METHODS                                                   # of Instance(s)
Complex Method                                                   1708 (89,56%)
Long Method                                                      751 (39,38%)
Bumpy Road                                                       341 (17,88%)
Long Parameter List                                              157 (8,23%)
```

Com este resultado, é possível avaliar **como está a distribuição dos smells em diferentes níveis de granularidade** (*namespace, type, method*), quais smells mais aparecem e, principalmente, a **distribuição de mais de um smell em um único elemento de código**. Observando os dados apresentados no nível de métodos, mais de 17% dos métodos apresentam algum sintoma e, de todos detectados, mais de 39% apresentam mais de um sintoma. Isso é um forte indicativo para investigação, pois elementos de código que possuem mais de um smell são mais propensos a bugs e mudanças.  

Indo mais adiante na análise, podemos **avançar a análise para os critérios utilizados**. Por exemplo, vamos observar severidade dos métodos.


```
drtools-code-health#> sevm --top 5

                        METHOD SEVERITY
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.detect.DumbMethods.sawOpcode(int seen)              Line: 645
                Severity: 896,400 (Normalized value: 10,000)
List of smells detected                         Quality Attributes Affected
Long Method                                  Understandability, Maintainability
Complex Method                               Maintainability, Understandability, Complexity
Bumpy Road                                   Maintainability, Understandability, Complexity
TOTAL OF SMELLS DETECTED: 3
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.detect.FindPuzzlers.sawOpcode(int seen)             Line: 172
                Severity: 644,533 (Normalized value: 7,469)
List of smells detected                         Quality Attributes Affected
Long Method                                  Understandability, Maintainability
Complex Method                               Maintainability, Understandability, Complexity
Bumpy Road                                   Maintainability, Understandability, Complexity
TOTAL OF SMELLS DETECTED: 3
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.detect.UnreadFields.sawOpcode(int seen)             Line: 386
                Severity: 465,200 (Normalized value: 5,668)
List of smells detected                         Quality Attributes Affected
Long Method                                  Understandability, Maintainability
Complex Method                               Maintainability, Understandability, Complexity
Bumpy Road                                   Maintainability, Understandability, Complexity
TOTAL OF SMELLS DETECTED: 3
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.detect.FindDeadLocalStores.analyzeMethod(ClassContext classContext, Method method)          Line: 203
                Severity: 370,133 (Normalized value: 4,713)
List of smells detected                         Quality Attributes Affected
Long Method                                  Understandability, Maintainability
Complex Method                               Maintainability, Understandability, Complexity
Bumpy Road                                   Maintainability, Understandability, Complexity
TOTAL OF SMELLS DETECTED: 3
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.detect.FindUselessObjects.analyzeMethod(ClassContext classContext, Method method)           Line: 465
                Severity: 367,200 (Normalized value: 4,683)
List of smells detected                         Quality Attributes Affected
Long Method                                  Understandability, Maintainability
Complex Method                               Maintainability, Understandability, Complexity
Bumpy Road                                   Maintainability, Understandability, Complexity
TOTAL OF SMELLS DETECTED: 3

TOTAL OF METHODS WITH SMELLS: 5 of 1907

Legend:
Values between 1 (less critic) and 10 (more critic)
        RED criterion >= 7.0;   YELLOW criterion >= 4.5 and criterion < 7.0;    GREEN criterion < 4.5
```

Ao **observar a severidade**, conseguimos ver que a lista dos métodos sofreu alteração em relação a simples listagem das [métricas](#métricas). Isso se deve ao fato que a severidade considera não só o número de [smells](#smells) no mesmo elemento de código, mas os mais severos (*e.g.*, ao identificar dois métodos com o mesmo smell, como *Long Method*, qual é o mais severo deles?). O modelo implementado no **DR-Tools Code Health** computa o ranking e faz essa priorização considerando esse aspecto. Como é possível observar, os critérios são apresentados com valores normalizados entre 1 (menos crítico) e 10 (mais crítico).

Vamos **observar outro critério**, como a qualidade. Note que a lista dos métodos novamente foi alterada, dando prioridade para os smells que tem maior associação com vários atributos de qualidade.

```
drtools-code-health#> quam --top 5

                        METHOD QUALITY IMPACT
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.detect.FindRefComparison.checkEqualsComparison(Location location, JavaClass jclass, Method method, MethodGen methodGen, ConstantPoolGen cpg, TypeDataflow typeDataflow)             Line: 1084
                Quality Impact: 180,000 (Normalized value: 10,000)
List of smells detected                         Quality Attributes Affected
Long Method                                  Understandability, Maintainability
Long Parameter List                          Maintainability
Complex Method                               Maintainability, Understandability, Complexity
Bumpy Road                                   Maintainability, Understandability, Complexity
TOTAL OF SMELLS DETECTED: 4
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.ba.deref.UnconditionalValueDerefAnalysis.checkUnconditionalDerefDatabase(Location location, ValueNumberFrame vnaFrame, ConstantPoolGen constantPool, @CheckForNull IsNullValueFrame invFrame, TypeDataflow typeDataflow)            Line: 333
                Quality Impact: 180,000 (Normalized value: 10,000)
List of smells detected                         Quality Attributes Affected
Long Method                                  Understandability, Maintainability
Long Parameter List                          Maintainability
Complex Method                               Maintainability, Understandability, Complexity
Bumpy Road                                   Maintainability, Understandability, Complexity
TOTAL OF SMELLS DETECTED: 4
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.classfile.engine.bcel.FinallyDuplicatesInfoFactory$TryBlock.update(BitSet exceptionTargets, BitSet branchTargets, InstructionList il, Set<Integer> finallyTargets, BitSet usedTargets)              Line: 157
                Quality Impact: 180,000 (Normalized value: 10,000)
List of smells detected                         Quality Attributes Affected
Long Method                                  Understandability, Maintainability
Long Parameter List                          Maintainability
Complex Method                               Maintainability, Understandability, Complexity
Bumpy Road                                   Maintainability, Understandability, Complexity
TOTAL OF SMELLS DETECTED: 4
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.ba.npe.NullDerefAndRedundantComparisonFinder.checkForUnconditionallyDereferencedNullValues(Location thisLocation, Map<ValueNumber,SortedSet<Location>> knownNullAndDoomedAt, Map<ValueNumber,NullValueUnconditionalDeref> nullValueGuaranteedDerefMap, ValueNumberFrame vnaFrame, IsNullValueFrame invFrame, UnconditionalValueDerefSet derefSet, boolean isEdge)           Line: 471
                Quality Impact: 180,000 (Normalized value: 10,000)
List of smells detected                         Quality Attributes Affected
Long Method                                  Understandability, Maintainability
Long Parameter List                          Maintainability
Complex Method                               Maintainability, Understandability, Complexity
Bumpy Road                                   Maintainability, Understandability, Complexity
TOTAL OF SMELLS DETECTED: 4
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.detect.RepeatedConditionals.compareCode(int first, int endOfFirstSegment, int second, int endOfSecondSegment, boolean oppositeChecks)Line: 155
                Quality Impact: 180,000 (Normalized value: 10,000)
List of smells detected                         Quality Attributes Affected
Long Method                                  Understandability, Maintainability
Long Parameter List                          Maintainability
Complex Method                               Maintainability, Understandability, Complexity
Bumpy Road                                   Maintainability, Understandability, Complexity
TOTAL OF SMELLS DETECTED: 4

TOTAL OF METHODS WITH SMELLS: 5 of 1907

Legend:
Values between 1 (less critic) and 10 (more critic)
        RED criterion >= 7.0;   YELLOW criterion >= 4.5 and criterion < 7.0;    GREEN criterion < 4.5
```

Vamos **ver como fica o valor agregado destes critérios**, com a apresentação do *CDI*. O *CDI* considera todos os critérios para montar um ranking dos métodos sugeridos para uma avaliação da pessoa desenvolvedora.


```
drtools-code-health#> cdim --top 5

                        METHOD CDI (Code Disease Indicator)
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.detect.DumbMethods.sawOpcode(int seen)              Line: 645
Severity                 Representativity         Quality                  Intervention             CDI
Abs     Norm             Abs     Norm             Abs     Norm             Abs     Norm
896,400   10,000          0,001  7,000            144,000  8,180           21,000   7,254          74,780
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.detect.FindPuzzlers.sawOpcode(int seen)             Line: 172
Severity                 Representativity         Quality                  Intervention             CDI
Abs     Norm             Abs     Norm             Abs     Norm             Abs     Norm
644,533   7,469           0,001  7,000            144,000  8,180           21,000   7,254          55,857
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.detect.UnreadFields.sawOpcode(int seen)             Line: 386
Severity                 Representativity         Quality                  Intervention             CDI
Abs     Norm             Abs     Norm             Abs     Norm             Abs     Norm
465,200   5,668           0,001  7,000            144,000  8,180           21,000   7,254          42,383
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.detect.FindDeadLocalStores.analyzeMethod(ClassContext classContext, Method method)          Line: 203
Severity                 Representativity         Quality                  Intervention             CDI
Abs     Norm             Abs     Norm             Abs     Norm             Abs     Norm
370,133   4,713           0,001  7,000            144,000  8,180           21,000   7,254          35,240
---------------------------------------------------------------------------------------------------------------------------------------------
METHOD: edu.umd.cs.findbugs.detect.FindUselessObjects.analyzeMethod(ClassContext classContext, Method method)           Line: 465
Severity                 Representativity         Quality                  Intervention             CDI
Abs     Norm             Abs     Norm             Abs     Norm             Abs     Norm
367,200   4,683           0,001  7,000            144,000  8,180           21,000   7,254          35,020

TOTAL OF METHODS WITH SMELLS: 5 of 1907

Legend:
Values between 1 (less critic) and 10 (more critic)
        RED criterion >= 7.0;   YELLOW criterion >= 4.5 and criterion < 7.0;    GREEN criterion < 4.5
CDI (code disease indicator) values between 1 (less critic) and 100 (more critic)
        RED 3th Quartile (> 75%) of code elements more critic;  YELLOW 2nd Quartile/Median (>=50%) intermediary of code elements;       GREEN rest of code elements
```
Observe que o CDI faz uma agregação dos critérios, com valores normalizados que vão de 1 (menos crítico) a 100 (mais crítico). 

Agora, será que **ao mudar a granularidade, muda também nossa percepção de problemas**?
O **DR-Tools Code Health** permite esse tipo de investigação nas granularidades discutidas.


```
drtools-code-health#> sevt --top 5

                        TYPE SEVERITY
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.BugInstance           Severity: 361,177 (Normalized value: 10,000)
List of smells detected                         Quality Attributes Affected
God Class                                    Understandability, Maintainability
Insufficient Modularization                  Modularity, Maintainability
Hub-like Modularization                      Maintainability, Coupling
Multifaceted Abstraction                     Cohesion
Deep Hierarchy                               Inheritance
Cyclically-dependent Modularization          Modularity
TOTAL OF SMELLS DETECTED: 6
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.ba.AnalysisContext            Severity: 206,475 (Normalized value: 6,145)
List of smells detected                         Quality Attributes Affected
God Class                                    Understandability, Maintainability
Insufficient Modularization                  Modularity, Maintainability
Hub-like Modularization                      Maintainability, Coupling
Multifaceted Abstraction                     Cohesion
Cyclically-dependent Modularization          Modularity
TOTAL OF SMELLS DETECTED: 5
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.workflow.Filter               Severity: 162,730 (Normalized value: 5,055)
List of smells detected                         Quality Attributes Affected
God Class                                    Understandability, Maintainability
Insufficient Modularization                  Modularity, Maintainability
Deficient Encapsulation                      Encapsulation
TOTAL OF SMELLS DETECTED: 3
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.SortedBugCollection           Severity: 150,286 (Normalized value: 4,745)
List of smells detected                         Quality Attributes Affected
God Class                                    Understandability, Maintainability
Insufficient Modularization                  Modularity, Maintainability
Hub-like Modularization                      Maintainability, Coupling
Multifaceted Abstraction                     Cohesion
Deep Hierarchy                               Inheritance
TOTAL OF SMELLS DETECTED: 5
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.OpcodeStack           Severity: 136,424 (Normalized value: 4,399)
List of smells detected                         Quality Attributes Affected
God Class                                    Understandability, Maintainability
Insufficient Modularization                  Modularity, Maintainability
Hub-like Modularization                      Maintainability, Coupling
Cyclically-dependent Modularization          Modularity
TOTAL OF SMELLS DETECTED: 4

TOTAL OF TYPES WITH SMELLS: 5 of 248

Legend:
Values between 1 (less critic) and 10 (more critic)
        RED criterion >= 7.0;   YELLOW criterion >= 4.5 and criterion < 7.0;    GREEN criterion < 4.5
```

Também, da mesma forma, é possível avaliar os critérios na granularidade de *types* e avaliar os potenciais sintomas, bem como verificar o respectivo *CDI*.


```
drtools-code-health#> cdit --top 5

                        TYPE CDI (Code Disease Indicator)
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.BugInstance
Severity                 Representativity         Quality                  Intervention             CDI
Abs     Norm             Abs     Norm             Abs     Norm             Abs     Norm
361,177   10,000          0,015  10,000           135,000  10,000          66,000   10,000         100,000
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.ba.AnalysisContext
Severity                 Representativity         Quality                  Intervention             CDI
Abs     Norm             Abs     Norm             Abs     Norm             Abs     Norm
206,475   6,145           0,012  8,200            112,000  8,455           45,000   7,092          48,643
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.SortedBugCollection
Severity                 Representativity         Quality                  Intervention             CDI
Abs     Norm             Abs     Norm             Abs     Norm             Abs     Norm
150,286   4,745           0,012  8,200            112,000  8,455           50,000   7,785          38,655
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.OpcodeStack
Severity                 Representativity         Quality                  Intervention             CDI
Abs     Norm             Abs     Norm             Abs     Norm             Abs     Norm
136,424   4,399           0,010  6,400            84,000  6,575            28,000   4,738          25,976
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.ba.ch.Subtypes2
Severity                 Representativity         Quality                  Intervention             CDI
Abs     Norm             Abs     Norm             Abs     Norm             Abs     Norm
83,750   3,087            0,012  8,200            112,000  8,455           45,000   7,092          24,436

TOTAL OF TYPES WITH SMELLS: 5 of 248

Legend:
Values between 1 (less critic) and 10 (more critic)
        RED criterion >= 7.0;   YELLOW criterion >= 4.5 and criterion < 7.0;    GREEN criterion < 4.5
CDI (code disease indicator) values between 1 (less critic) and 100 (more critic)
        RED 3th Quartile (> 75%) of code elements more critic;  YELLOW 2nd Quartile/Median (>=50%) intermediary of code elements;       GREEN rest of code elements
```

Podemos também **avaliar a composição de potenciais problemas em uma granularidade menor com uma granularidade maior** com o *CCDI*. Ou seja, por exemplo, smells identificados nos métodos em conjunto com smells identificados na classe que contém estes métodos.   

``` 
drtools-code-health#> ccdit --top 5

                        TYPE CDI COMPOSITION (Types + Methods)
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.BugInstance   CDI: 51,131
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.ba.AnalysisContext    CDI: 25,465
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.SortedBugCollection   CDI: 20,918
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.OpcodeStack   CDI: 14,859
---------------------------------------------------------------------------------------------------------------------------------------------
TYPE: edu.umd.cs.findbugs.ba.ch.Subtypes2       CDI: 13,419

TOTAL OF TYPES WITH SMELLS: 5 of 248
CDI (code disease indicator) values between 1 (less critic) and 100 (more critic)
        RED 3th Quartile (> 75%) of code elements more critic;  YELLOW 2nd Quartile/Median (>=50%) intermediary of code elements;       GREEN rest of code elements
``` 

Por fim, ainda é possível avaliar o que o **DR-Tools Code Health** encontrou em relação a impacto na qualidade, importância dos problemas encontrados e intervenção nas respectivas granularidades comparando com os parâmetros customizados. 

```
drtools-code-health#> rs
----------------
SUMMARY: RANKING
----------------
Granularity: NAMESPACE
QUALITY ATTRIBUTES              # More Impacted         # Weight defined by Developer
Modularity                         50,00%                       33,33%
Understandability                  30,77%                       41,03%
Coupling                           19,23%                       25,64%
INTERVENTION                    # More Defined          # Weight defined by Developer
Planned                            61,54%                       76,19%
Normal                             38,46%                       23,81%
IMPORTANCE                      # More Defined          # Weight defined by Developer
Normal                             61,54%                       44,44%
High                               38,46%                       55,56%

Granularity: TYPE
QUALITY ATTRIBUTES              # More Impacted         # Weight defined by Developer
Maintainability                    41,15%                       52,67%
Modularity                         38,13%                       24,40%
Cohesion                           5,90%                        7,55%
Understandability                  5,76%                        7,37%
Inheritance                        4,03%                        2,58%
Coupling                           3,45%                        4,42%
Encapsulation                      1,58%                        1,01%
INTERVENTION                    # More Defined          # Weight defined by Developer
Planned                            89,49%                       94,45%
Normal                             10,51%                       5,55%
IMPORTANCE                      # More Defined          # Weight defined by Developer
Normal                             73,35%                       58,25%
High                               26,16%                       41,55%
Low                                0,49%                        0,19%

Granularity: METHOD
QUALITY ATTRIBUTES              # More Impacted         # Weight defined by Developer
Maintainability                    37,88%                       33,49%
Understandability                  35,87%                       31,71%
Complexity                         26,25%                       34,81%
INTERVENTION                    # More Defined          # Weight defined by Developer
Normal                             57,76%                       36,72%
Planned                            25,40%                       32,29%
Immediate                          11,53%                       29,32%
Low                                5,31%                        1,69%
IMPORTANCE                      # More Defined          # Weight defined by Developer
High                               83,16%                       77,33%
Critical                           11,53%                       21,45%
Low                                5,31%                        1,23%
drtools-code-health#>
```
Só para ilustrar uma dessas investigações, na granularidade de *method*, observando os atributos de qualidade, *maintainability* foi o mais impactado, porém considerando a atribuição de pesos definidos pelas pessoas desenvolvedoras (ou seja, considerando que os sintomas vinculados a estes atributos têm maior relevância para estas pessoas) foi *complexity*. 

Estas customizações são um recurso valioso, pois podemos customizar esses parâmetros nos diferentes critérios de acordo com o contexto do projeto.

Ainda, O **DR-Tools Code Health** pode ser adotado durante as [atividades programação](#atividades-de-programação), ou em sessões de [revisão de código](#revisões-de-código), com objetivo de apoiar a tomada de decisões.

O **DR-Tools Code Health** é uma ferramenta flexível e adaptável. Com ela, é possível analisar, cruzar, avaliar os dados do código de diferentes formas, além de poder customizar e parametrizar conforme contexto do projeto. O propósito da ferramenta não é julgar, mas apoiar e ajudar as pessoas desenvolvedoras na tomada de decisão. 


## Atividades de Programação
As atividades de programação consideradas vão desde implementações de novas funcionalidades até manutenções preventivas e perfectivas no código.

> Segundo Tufano et al. (TUFANO et al., 2017), *"a maior parte do código com smells (entre 60% e 66%) são introduzidos durante o aprimoramento do código*" 

Assim como os refactorings, que deveriam ser considerados como parte de sua atividade diária, um hábito, a análise de código também deveria fazer parte do cotidiano das pessoas desenvolvedoras. 

O uso do **DR-Tools Code Health** permite priorizar e filtrar os smells em diferentes granularidades, considerando aspectos de qualidade durante as atividades de programação ajudando na identificação de problemas. Antes mesmo de realizar um commit, o seu uso pode ajudar a minimizar e/ou evitar possíveis problemas que seriam identificados a posteriori e já presentes no repositório do
projeto.

## Revisões de Código
As atividades de revisão de código podem acontecer individualmente, em pares ou mesmo em reuniões do time. Estas revisões, quando realizadas com mais membros do time, podem trazer aprendizados, principalmente avaliando os aspectos negativos dos smells e que não devem ser seguidos. 

> Digkas et al. (DIGKAS et al., 2020) estudaram se há relação entre práticas de qualidade de código e a limpeza de um novo código. Para isso, avaliaram 57 projetos do ecossistema Apache, em um total de 66.661 classes e 56.890 commits. Os resultados sugerem que escrever um novo código limpo (ou pelo menos mais limpo) pode ser uma estratégia eficiente para reduzir a densidade de dívida técnica, evitando a degradação do software ao longo do tempo. Os resultados também sugerem que os projetos que adotam políticas explícitas de melhoria da qualidade (e.g., discussões sobre a qualidade nas reuniões do time) estão associados a uma maior frequência de novos commits de códigos mais limpos. 

Com o **DR-Tools Code Health**, é possível fazer um monitoramento contínuo da presença dos problemas para evitar um aumento crítico nas atividades de mudanças. A ferramenta pode ajudar o time a fazer estas investigações técnicas com base no código e planejar suas ações, sejam elas imediatas, planejadas ou até mesmo, ignorá-las.

A aplicação prática do **DR-Tools Code Health** pode auxiliar na avaliação e estudo dos problemas no código que são críticos e, consequentemente, os que não são, na sustentabilidade dos projetos. Ainda, a ferramenta pode ajudar os profissionais na monitoria e evolução dos problemas no código, avaliando de forma sistemática a necessidade de atuação.


# Customizações

É possível realizar vários tipos de customizações no **DR-Tools Code Health**.
Estas customizações são estão relacionadas a definições dos [smells](#smells), co-ocorrências e pesos utilizados na priorização. Algumas customizações podem envolver alteração de código e outras apenas alterando parâmetros no arquivo de configuração.  

## Customizações via Código

A maioria das pessoas desenvolvedoras se beneficia da abordagem baseada em heurísticas, porque permite a construção de detecções personalizadas, sendo eficiente na detecção do smell. A definição da customização é realizada em duas etapas: 1) formulação da heurística; 2) refinamento da heurística geral, incluindo métricas específicas, *thresholds* e operadores lógicos.
O sucesso da detecção depende diretamente da eficiência de um processo automatizado de personalização. 
Há, também, a necessidade de modelos de detecção generalizados que possam ser facilmente adaptados para novos smells e que permita a detecção de mais de um problema no mesmo elemento de código. 

Assim, o **DR-Tools Code Health** possui uma estrutura de alto nível que apoia a definição e identificação dos smells, do que puramente o uso de ASTs ou métricas.
Esta estrutura de suporte à detecção é uma extensão e adaptação da infraestrutura construída para o [DR-Tools Suite](http://drtools.site), que
já contempla componentes de código e métricas OO. O objetivo é permitir a instanciação do modelo e o método de priorização através de uma [API Fluente](https://martinfowler.com/bliki/FluentInterface.html). 

##### Exemplo de Definição de Smells no Code Health
```java
Smell godClass = new SmellBuilder()
		.type()
		.heuristic(new GodClass())
		.withName("God Class", SmellToken.GOD_CLASS, 
				"This smell is huge, complex, and has an extremely high number of fields and methods")
		.importance(Importance.HIGH)
		.withImpact(QualityAttribute.UNDERSTANDABILITY, QualityAttribute.MAINTAINABILITY)
		.withAction(Intervention.PLANNED)
		.createInstance();

Smell bumpyRoad = new SmellBuilder()
		.method()
		.heuristic(new BumpyRoad())
		.withName("Bumpy Road", SmellToken.BUMPY_ROAD, 
				"This smell occurs when a method has high nested blocks")
		.importance(Importance.CRITICAL)
		.withImpact(QualityAttribute.MAINTAINABILITY, QualityAttribute.UNDERSTANDABILITY,
							QualityAttribute.COMPLEXITY)
		.withAction(Intervention.IMMEDIATE)
		.createInstance();
```
É possível também definir quais smells aparecem constantemente juntos, nos elementos de código. O **DR-Tools Code Health** permite especificar co-ocorrências de vários smells, conforme necessidade do time.

##### Exemplo de Definição de Co-Ocorrências de Smells no Code Health
```java
SmellCoOccurrence complexStructuresType = new SmellCoOccurrenceBuilder()
		.withDescription("Complex structures in types")
		.addSmells(SmellToken.GOD_CLASS, SmellToken.INSUFFICIENT_MODULARIZATION, 
			SmellToken.MULTIFACETED_ABSTRACTION)
		.category(CoOccurrenceClassification.TYPE)
		.impactOn(QualityCoOccurrence.COMPLEXITY, QualityCoOccurrence.SIZE)
		.createInstance();

SmellCoOccurrence highStructuresMethod = new SmellCoOccurrenceBuilder()
		.withDescription("High complexity structures in methods")
		.addSmells(SmellToken.COMPLEX_METHOD, SmellToken.BUMPY_ROAD)
		.category(CoOccurrenceClassification.METHOD)
		.impactOn(QualityCoOccurrence.COMPLEXITY)
		.createInstance();
```

## Customizações via Arquivo de Configuração

Outras customizações, sem necessidade de alteração no código, podem ser feitas para ajustar parâmetros dos critérios usados na [priorização e ranking](#ranking-e-priorização) dos elementos de código mais problemáticos. Basta editar o arquivo e adaptar os parâmetros para o contexto do seu projeto.   

##### Trecho do arquivo de configuração `drtools-config.properties`
```properties
intervention.immediate=12.0
intervention.planned=6.0
intervention.normal=3.0
intervention.low=1.5

#
# -- Quality Attributes, linked to smells and describing its impact
#    Parameters: CRITICAL, HIGH, NORMAL, and LOW
#
quality.attribute.cohesion=HIGH
quality.attribute.complexity=CRITICAL
quality.attribute.coupling=HIGH
quality.attribute.encapsulation=NORMAL
quality.attribute.inheritance=NORMAL
```

------
