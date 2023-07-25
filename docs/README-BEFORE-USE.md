<img src="https://guilhermeslacerda.github.io/drtools-site/images/logo_drtools.png" width="40%">

DR-Tools Code Health
====================

The **DR-Tools Code Health** is a tool belonging to the [DR-Tools Suite](http://drtools.site), an open-source set of tools designed to assist developers in their daily activities of analyzing and identifying code design issues or problems, commonly known as [code smells](#smells).

The **DR-Tools Code Health**, through an aggregated indicator of different criteria, aims to identify code that is difficult to maintain and carries a higher risk of defects and changes. It also detects code with a high level of complexity. The tool's main goal is not to predict which problems will undergo refactoring, but rather to support developers in [prioritizing and filtering the issues](#ranking-and-prioritization) for decision-making based on the code. 
Therefore, it is designed [to be used in day-to-day development activities](#how-can-we-use-dr-tools-code-health-to-code-analysis), such as programming, code reviews, and even supporting the system's build process.

Below, we provide some basic information for its use.


Contents
===============

* [Introduction](#introduction)
* [Concepts and Terminology](#concepts-and-terminology)
* [How to use?](#how-to-use)
	* [Installation and Requirements](#installation-and-requirements)
    * [CLI Mode](#cli-mode)
    * [Interactive Mode](#interactive-mode)
* [Modules](#modules)
    * [Metrics](#metrics)
    * [Smells](#smells)
    * [Ranking and Prioritization](#ranking-and-prioritization)
* [How to analyze code with DR-Tools Code Health?](#how-to-analyze-code-with-drtools-code-health)
* [Customizations](#customizations)

# Introduction

The **DR-Tools Code Health** is a tool that facilitates code analysis through features such as [metrics](#metrics), [smell detection](#smells), and [problem prioritization](#ranking-and-prioritization) based on criteria such as severity, smell representativity, impact on quality attributes, and team intervention level.

Before delving into the discussion of **DR-Tools Code Health** features, it is important to highlight some points considered in its development. 
They are as follows:

* Allow the [definition of heuristics](#customizations) for smell detection in a configurable and extensible manner.
* Involve developers in the smell detection process.
* Consider how smells impact software quality.
* Provide a [multicriteria prioritization format](#ranking-and-prioritization) that considers different [levels of granularity](#terminology) and their respective compositions to identify the most problematic code elements.

> *Granularity* refers to the level at which a smell is found, which can be at the package, class, or method level.
>
> The *code elements* of a software project are self-contained structures - method(s) are within class(es). Class(es) are within package(s). Package(s) are within a project.

Indeed, merely detecting the presence of [smells](#smells) is not an indication of their criticality. It is necessary, according to decision-makers, to assess the impacts on quality that these different problems present.

Moreover, each potential problem has different severity scores. Without knowing their actual severity, developers may feel hesitant to detect and remove them. Thus, [prioritization](#ranking-and-prioritization) becomes increasingly relevant to support developers in this process.

> "Prioritization is the most important activity in managing technical debt, and it is an area of ongoing research" (PINA; GOLDMAN; TONIN, 2021).
> 
> "Furthermore, the prioritization of smells is an open field of research" (PECORELLI et al., 2020).

It is evident that prioritization can be conducted using different approaches, with different objectives and criteria. However, there is still a lack of a solid, validated, and widely used set of tools for prioritization.
Specifically, the [prioritization of smells](#ranking-and-prioritization) is an open field of research that raises several questions:
1. When detecting a smell, how much more problematic is it compared to its counterparts?
2. What is its implication on quality attributes, particularly maintainability?
3. Which code elements and granularity are the most prioritized?
4. Can the accumulation of smells in different code elements assist in prioritization?
5. How can the team be involved in the process of detection and prioritization, considering both quality and business goals?

These design problems need to be prioritized, filtered, and synthesized, enabling developers to assess immediate or planned actions over time. The **DR-Tools Code Health** is here to support developers by providing resources to evaluate these questions.


# Concepts and Terminology

## Concepts

For the development of **DR-Tools Code Health**, we considered several important points regarding code analysis:

* **Smells have different severities:** In a software project, different types of [design problems](#smells) can be found, each with varying levels of severity. Severity is related to the criticality of a code element and negatively influences maintenance and evolution. 
The *code elements* are the main components present in a software project, self-contained entities such as packages, classes, and methods. Some code elements are more problematic than others, even with the same instance of smells.
* **Smells have different granularities:** [Design problems](#smells) are also located at different levels of granularity. Problems can be present in methods, classes, and packages/modules that compose the system. Moreover, problems at a certain level can influence other levels. For example, design problems at the method level can be the root cause of problems in the class level. Consequently, problems at the class level have a strong positive correlation with problems at the package level.
* **Smells are cumulative:** A software project consists of self-contained structures (modules/packages containing classes, which contain attributes and methods). Therefore, one aspect to consider is the presence of more than one [smell](#smells) in a specific code element, such as a method, known as co-occurrence. 
Furthermore, in addition to co-occurrence, it is important to consider the accumulation of these problems at different levels of granularity or the composition of co-occurrences.
* **Smells affect different quality attributes:** Not only do quality attributes affect various [design problems](#smells), but design problems can also impact multiple quality attributes. A quality attribute is related to a problem if the problem's definition structure contains a mapping to quality attributes.
* **Perception of severity changes depending on the observed granularity:** Models at different levels of granularity should be the focus of further investigations. Thus, when considering granularity, the perception of severity can change. For example, when examining the method granularity, the most problematic method and, therefore, the highest priority for evaluation is one. However, when changing the granularity to classes and considering the composition of co-occurrences of all [smells](#smells) in the methods and the smells present in the class, this perception tends to change.
* **Smells require different levels of intervention:** When analyzing [smells](#smells), it is important to have an understanding of those that require immediate intervention, planned intervention, or can even remain in the repository as they do not significantly impact the project.
To address all these considerations, **DR-Tools Code Health** implements a [prioritization model](#ranking-and-prioritization) for [smells](#smells) to support software development and maintenance activities, helping developers classify and select the code elements affected by smells.


## Terminology

Internally, **DR-Tools Code Health** implements a model that integrates information on the definition, identification, [prioritization, and filtering](#ranking-and-prioritization) of [smells](#smells). The prioritization process combines criteria such as severity, representativity, impact on code quality, and team intervention.

In addition to the criteria, we provide more detailed descriptions of the terms adopted by DR-Tools Code Health. For code elements, we use the term namespace to represent a package, type to represent a class, and method to represent a method.

The structure of the model allows quality attributes to be associated with smells to support prioritization and filtering.

### Terms

|Term           	     | Description                     |
|:-----------------------|:--------------------------------|
|*Severity* | Defines how severe a smell is and, consequently, an element of code affected by multiple smells |
|*Representativity* | Instances of the same smell recurring in different code elements, representing their impact at the considered granularity. Based on representativity, it is possible to assess which code elements are most affected by smells at a given granularity |
|*Intervention* | Defines the level of developer involvement required for addressing a smell affecting code elements, considering the target (*e.g.*, a class) and the necessary action (immediate, planned, or future) |
|*Smell* | Term used to represent design issues in the code, regardless of granularity |
|*Smell Heuristics* | Definition of how a smell is detected, representing a detection rule or something more elaborate, such as an algorithm for its detection |
|*Code Element* | Represents package(s), class(es), and/or method(s) of a system |
|*Granularity* | Represents the level of a code element, considering all instances of smells in the context (*Namespace, Type*, or *Method*) |
|*Diversity* | Represents the number of smells in the same code element |
|*Smell Co-Occurrence* | Structure that detects the same occurrence(s) of a set of smells in different code elements |
|*Threshold* | A limit defined for a metric that can compose the smell detection heuristic |
|*Code Disease Indicator (CDI)* | An aggregated value that defines the overall criticality of a code element |
|*CDI Composition* | Represents the accumulation of CDIs from smells in code elements, considering the current context and lower granularities |

Additionally, the implemented model allows for [assigning weights](#file-configuration-customizations) to quality attributes. Other weights have also been included to reflect developers' perception of their project context. 
Developers can adjust and customize these parameters in the project's generated [configuration file](#cli-mode) (`drtools-config.properties`).

The [criteria](#criteria) have weights that can be defined by developers and used in the computation of [prioritization](#ranking-and-prioritization). 
Thus, the model combines quantitative aspects (*e.g.*, the number of smells in a code element) with qualitative aspects (the impact of a smell on quality) and developers' perception, through the definition of weights in the other considered criteria.


### Criteria

| Criterion	| Description                            |
|:-----------------------|:--------------------------------|
|*Threshold* | Defined for the metric, based on literature references, and can be redefined by developers taking into account the project context |
|*Importance* |	Weight given by the developer to a smell in relation to its importance (default=1) |
|*Quality Impact* |	Weight given by the developer to the impact on a given quality attribute (default=1) |
|*Intervention* | Weight given by the developer regarding the intervention required to address a given smell (default=1) |

Note: Developers have the flexibility to [adjust the weights](#file-configuration-customizations) of these criteria to reflect their own priorities and considerations during the prioritization process.


# How use?

## Installation and Requirements

To start using **DR-Tools Code Health**, simply extract the `drtools-code-healthX.X.zip` file to a directory (*e.g.*, `\drtools-code-health\`), where `X.X` represents the respective version of the tool.

It is recommended to add this directory to the operating system's *System Path*.
The only prerequisite is the *Java Runtime Environment* (JRE) version 11 or higher.

To verify if the tool is ready to use, simply run the following command in the command prompt:
```
prompt> drtools-code-health <enter>
```

The expected result should be as follows:

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

The DR-Tools Code Health has two execution modes: [CLI](#cli-mode) and [interactive](#interactive-mode).

## CLI Mode

The CLI mode (*command line interface*) allows users of the tool to customize the parameters for their project as needed and obtain the results of the **DR-Tools Code Health** analysis.

When using the CLI mode options, the **DR-Tools Code Health** will create the following directory structure by default.

```
.drtools
├───analysis
├───config
└───templates
```
Here are the options in more detail.

### --init Option

The `--init` option allows users to customize the parameters used for [smell detection](#smells) and [problem prioritization](#ranking-and-prioritization) in the code.


##### Use of the **--init** command
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
When using this option, the **DR-Tools Code Health** will create a `.drtools/` directory within the directory structure of the analyzed repository. This directory is used by the tool to store configuration files. Two other subdirectories (`config` and `templates`, respectively) are created.

The directory structure and files will look like this:

```
.drtools
├───config
│   ├───drtools-config.properties
└───templates
    ├───drtools-config-defined-by-user.properties
    ├───drtools-config-high-threshold.properties
    └───drtools-config-outlier.properties
```

The `drtools-config.properties` (`config` directory) contains the parameters used to analyze the code. 
Additionally, other files are generated in the `templates` directory with alternative configurations that can also be used, varying the thresholds of the metrics. These files are:
* `drtools-config-defined-by-user.properties`: a file similar to the one in the `config` directory, where users can customize the parameters according to their preferences.
* `drtools-config-high-threshold.properties`: a file with *thresholds* based on the sum of the mean and standard deviation of the respective metric.
* `drtools-config-outlier.properties`: a file with *thresholds* based on the outlier of the metric, considering statistical information from the code.
These files are identified in the tool through the concept of a *Profile*. 
The *Profile* helps customize and define names for these configurations, allowing developers to manage the use of these parameters in the **DR-Tools Code Health** more effectively.

To use the configurations in the `templates` directory, simply move the desired file to the `config` directory and rename it to `drtools-config.properties`.


### --analyze Option

The `--analyze` option generates the results of the code analysis, organized by [metrics](#metrics), [smells](#smells), and [ranking](#ranking-and-prioritization). The results are available in files in `CSV` and `JSON` formats.

These results are generated in the `analysis` directory, identified by the date and time of generation, following this structure:

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
Also, it is possible to generate the results in a specific directory using the command `drtools-code-health <project directory> --analyze \temp`. This way, the entire directory structure (`.drtools/analysis/`) will be inside the `\temp` directory.

This feature is useful because the analysis results will not be within the project's directory structure. It can be used as a *task* in a project's *build pipeline* process.


## Interactive Mode

In addition to the [CLI mode](#cli-mode), it is possible to execute **DR-Tools Code Health** in interactive mode. In this option, after the initial analysis of the project's source code, a prompt is provided with various commands to query [metrics](#metrics), [smells](#smells), and [ranking](#ranking-and-prioritization) of code issues.


##### Basic Commands
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
You can use both the `shortcut` and the `command` in the prompt. There are specific search options, such as the following example, which performs a search for a class (`ft` or `find-type`) called `buginstance`, finding two occurrences.


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
Also, you can use the `--top <number>` option in the commands to filter the result.


# Modules

## Metrics

The [metrics menu](#metrics-options-menu) provides various options for listing information at different levels of granularity and grouped according to context. For a better analysis of the results, the metrics are ordered by complexity and size, aiming to highlight the most complex points in the code elements.


##### Metrics Options Menu
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

The **DR-Tools Code Health** presents 48 metrics contextualized by *summary*, *namespaces*, *types*, *methods*, *dependencies*, and *coupling*. 
Here is the list of metrics for each context:

* **Summary (15):** *Total of namespaces, total of types, mean number of types/namespaces, total of lines of code (SLOC), average number of SLOC/types (with median and standard deviation), total of methods, average number of methods/types (com median e standard deviation), total of complexity (CYCLO), and average number of complexity/types (with median and standard deviation)*
* **Namespaces (2):** *Number of classes/types (NOC) and number of abstract classes (NAC)*
* **Types (14):** *Lines of code (SLOC), number of methods (NOM), number of public methods (NPM), class complexity (WMC), number of dependencies (DEP), number of internal dependencies (I-DEP), Number of other types that depend on a given type (FAN-IN), number of other types referenced by a type (FAN-OUT), number of fields/attributes (NOA), lack of cohesion in methods (LCOM3), deep in inheritance tree (DIT), number of children (CHILD), number of public attributes/fields (NPA), and number of cyclic dependencies (types) (CDEP)*
* **Methods (5):** *Lines of code (MLOC), cyclomatic complexity (CYCLO), number of invocations (CALLS), nested block depth (NBD), and number of parameters (PARAM)*
* **Namespace Coupling (5):** *Afferent coupling (CA), efferent coupling (CE), instability (I), abstractness degree (A) e normalized distance (D)*
* **Type Coupling (4):** *number of dependencies (DEP), number of internal dependencies (I-DEP), Number of other types that depend on a given type (FAN-IN), and number of other types referenced by a type (FAN-OUT)*
* **Dependencies (3):** *General dependencies (DEP), internal dependencies (I-DEP), and cyclic dependencies (CDEP)*

Also, **DR-Tools Code Health** provides statistical analysis with quartiles, mean, median, minimum and maximum values, standard deviation, range, and outliers for the analyzed metrics, in addition to the *threshold* provided by the literature. This allows for comparing the data collected from the code with the statistical data of the project.


##### Example of querying the 3 types with the largest size and complexity, their respective metrics, and statistical analysis 
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

Smells are symptoms of potential design issues in the code that can affect its maintainability and evolution. Various detection techniques have been proposed to identify these smells from the code. These techniques return the identified code elements, which in turn require effort for their removal using refactorings or other appropriate techniques.

In **DR-Tools Code Health**, we have defined a series of commands that allow analyzing smells at different granularities in the project.


##### Smells Options Menu
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

### List of Detected Smells

Currently, **DR-Tools Code Health** detects 17 types of smells at the granularities of **namespace, type**, and **method**.
Smells impact quality and, in most cases, the purpose of restructuring and refactorings is to improve quality. Therefore, it is important to associate problems with quality and use them as one of the prioritization criteria, highlighting the most problematic parts to the developers in the team.

Below, we provide information about the smells, including additional details such as importance, impact on quality, and intervention.
This information about smells is available through the `sd` option in **DR-Tools Code Health**.


#### Granularity: Namespace

* **Name:** *Too Large Package*
	* **Description:** Packages/Subsystems with a high number of classes/packages indicate that they serve more than one specific responsibility
	* **Importance:** NORMAL
	* **Quality Impact:** Understandability, Modularity
	* **Intervention Action:** PLANNED
	* **Reference:** M. Lippert, S. Roock, “Refactoring in Large Software Projects: Performing Complex Restructurings Successfully”. John Wiley and Sons, 2006.

* **Name:** *Cyclic Dependency*
	* **Description:** This smell occurs when two or more packages/subsystems depend on each other
	* **Importance:** HIGH
	* **Quality Impact:** Coupling, Modularity
	* **Intervention Action:** NORMAL
	* **Reference:** M. Lippert, S. Roock, “Refactoring in Large Software Projects: Performing Complex Restructurings Successfully”. John Wiley and Sons, 2006.

#### Granularity: Type

* **Name:** *God Class*
	* **Description:** This smell is huge, complex, and has an extremely high number of fields and methods
	* **Importance:** HIGH
	* **Quality Impact:** Understandability, Maintainability
	* **Intervention Action:** PLANNED
	* **Reference:** Bafandeh Mayvan B, Rasoolzadegan A, Javan Jafari A. Bad smell detection using quality metrics and refactoring opportunities. J Softw Evol Proc. 2020.
	
* **Name:** *Broken Modularization*
	* **Description:** This smell arises when data and/or methods that ideally should have been localized into a single abstraction are separated and spread across multiple abstractions
	* **Importance:** LOW
	* **Quality Impact:** Modularity, Maintainability
	* **Intervention Action:** PLANNED
	* **Reference:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Name:** *Cyclically Dependent Modularization*
	* **Description:** This smell arises when two or more abstractions depend on each other directly or indirectly
	* **Importance:** HIGH
	* **Quality Impact:** Modularity
	* **Intervention Action:** NORMAL
	* **Reference:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Name:** *Insufficient Modularization*
	* **Description:** This smell arises when an abstraction exists that has not been completely decomposed, and a further decomposition could reduce its size, implementation complexity, or both
	* **Importance:** NORMAL
	* **Quality Impact:** Modularity, Maintainability
	* **Intervention Action:** PLANNED
	* **Reference:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Name:** *Deep Hierarchy*
	* **Description:** This smell arises when an inheritance hierarchy is 'excessively' deep
	* **Importance:** NORMAL
	* **Quality Impact:** Inheritance
	* **Intervention Action:** PLANNED
	* **Reference:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Name:** *Deficient Encapsulation*
	* **Description:** This smell occurs when the declared accessibility of one or more members of an abstraction is more permissive than actually required
	* **Importance:** NORMAL
	* **Quality Impact:** Encapsulation
	* **Intervention Action:** PLANNED
	* **Reference:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Name:** *Hub-like Modularization*
	* **Description:** This smell arises when an abstraction has dependencies (both incoming and outgoing) with a large number of other abstractions
	* **Importance:** HIGH
	* **Quality Impact:** Maintainability, Coupling
	* **Intervention Action:** PLANNED
	* **Reference:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Name:** *Multifaceted Abstraction*
	* **Description:** This smell arises when an abstraction has more than one responsibility assigned to it
	* **Importance:** NORMAL
	* **Quality Impact:** Cohesion
	* **Intervention Action:** PLANNED
	* **Reference:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

* **Name:** *Wide Hierarchy*
	* **Description:** This smell arises when an inheritance hierarchy is 'too' wide indicating that intermediate types may be missing
	* **Importance:** NORMAL
	* **Quality Impact:** Inheritance
	* **Intervention Action:** PLANNED
	* **Reference:** G. Suryanarayana, G. Samarthyam, T. Sharma, “Refactoring for Software Design Smells: Managing Technical Debt Morgan Kaufmann, 2014.

#### Granularity: Method

* **Name:** *Long Method*
	* **Description:** This smell occurs when a method is too long to understand
	* **Importance:** HIGH
	* **Quality Impact:** Understandability, Maintainability
	* **Intervention Action:** PLANNED
	* **Reference:** M. Fowler, “Refactoring: Improving the Design of Existing Code”. Addison-Wesley, 1999.

* **Name:** *Long Parameter List*
	* **Description:** This smell occurs when a method accepts a long list of parameters
	* **Importance:** LOW
	* **Quality Impact:** Maintainability
	* **Intervention Action:** LOW
	* **Reference:** M. Fowler, “Refactoring: Improving the Design of Existing Code”. Addison-Wesley, 1999.

* **Name:** *Complex Method*
	* **Description:** This smell occurs when a method has high cyclomatic complexity
	* **Importance:** HIGH
	* **Quality Impact:** Understandability, Maintainability, Complexity
	* **Intervention Action:** NORMAL
	* **Reference:** Tushar Sharma, Marios Fragkoulis, and Diomidis Spinellis. * “House of Cards: Code Smells in Open-Source C# Repositories". * in ACM/IEEE International Symposium on Empirical Software Engineering and Measurement (ESEM), 2017.

* **Name:** *Bumpy Road*
	* **Description:** This smell occurs when a method has high nested blocks
	* **Importance:** CRITICAL
	* **Quality Impact:** Understandability, Maintainability, Complexity
	* **Intervention Action:** IMMEDIATE
	* **Reference:** Tornhill, Adam. [The Bumpy Road Code Smell: Measuring Code Complexity by its Shape and Distribution](https://codescene.com/blog/bumpy-road-code-complexity-in-context/), 2023.  

As an example, we can list the smells of *type*, filtering the first 3 classes. It is possible to observe the smells found in each class and their respective impacts on quality.

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


### List of Detected Smell Co-Occurrences

The **DR-Tools Code Health** also detects various types of smell co-occurrences, categorized in *type* and *method*. The co-occurrences can indicate certain design anti-patterns that repeat in the project.  

#### Category: Type

* **Description:** *Big structures in types with modularization problems*
	* **Smells:** God Class, Insufficient Modularization, Hub-like Modularization, Cyclically Dependent Modularization, Multifaceted Abstraction
	* **Quality Impact:** Size, Cohesion, Complexity, Coupling

* **Description:** *Critical!! Structures in types with too many smells*
	* **Smells:** God Class, Insufficient Modularization, Hub-like Modularization, Cyclically Dependent Modularization, Multifaceted Abstraction, Deep Hierarchy
	* **Quality Impact:** Size, Cohesion, Complexity, Coupling

* **Description:** *Complex structures in types*
	* **Smells:** God Class, Insufficient Modularization, Multifaceted Abstraction
	* **Quality Impact:** Size, Complexity

* **Description:** *Deep hierarchy, big and low cohesion structures in types*
	* **Smells:** God Class, Insufficient Modularization, Deep Hierarchy, Multifaceted Abstraction
	* **Quality Impact:** Size, Complexity

* **Description:** *Complex and low cohesion structures in types*
	* **Smells:** Insufficient Modularization, Multifaceted Abstraction
	* **Quality Impact:** Cohesion, Complexity

* **Description:** *Low cohesion and cyclic dependency structures in types*
	* **Smells:** Insufficient Modularization, Cyclically Dependent Modularization
	* **Quality Impact:** Cohesion, Coupling

* **Description:** *Low cohesion and big structures in types*
	* **Smells:** Insufficient Modularization, God Class
	* **Quality Impact:** Cohesion, Size

* **Description:** *Low cohesion and high coupling structures in types*
	* **Smells:** Insufficient Modularization, Hub-like Modularization
	* **Quality Impact:** Cohesion, Coupling

* **Description:** *Low cohesion, high coupling, and big structures in types*
	* **Smells:** God Class, Insufficient Modularization, Hub-like Modularization
	* **Quality Impact:** Cohesion, Coupling, Size

#### Category: Method

* **Description:** *Big structures in methods*
	* **Smells:** Long Method, Long Parameter List
	* **Quality Impact:** Size

* **Description:** *Complex structures in methods*
	* **Smells:** Long Method, Bumpy Road, Complex Method
	* **Quality Impact:** Complexity

* **Description:** *Hard structures to understand in methods*
	* **Smells:** Long Method, Bumpy Road
	* **Quality Impact:** Complexity, Size

* **Description:** *Structures difficult to maintain in methods*
	* **Smells:** Complex Method, Long Method
	* **Quality Impact:** Complexity, Size

* **Description:** *High complexity structures in methods*
	* **Smells:** Complex Method, Bumpy Road
	* **Quality Impact:** Complexity

* **Description:** *Critical!! Structures in methods with too many smells*
	* **Smells:** Complex Method, Bumpy Road, Long Method, Long Parameter List
	* **Quality Impact:** Complexity, Size

* **Description:** *Several parameters and complex structures in methods*
	* **Smells:** Complex Method, Long Parameter List
	* **Quality Impact:** Complexity, Size

It is possible to identify the co-occurrences of smells and their respective detected quantities using the `sco` command.


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

## Ranking and Prioritization

More important than detecting possible problems, represented by [smells](#smells), is providing additional analysis mechanisms. Not all of these problems are equally important or relevant to the software's objectives or even its quality. Developers do not frequently address all the problems, and one possible cause is the lack of a prioritization and filtering method that takes into account quality aspects and the developers' perception at that moment in the project.

The **DR-Tools Code Health** implements a prioritization model that considers criteria such as severity, representativity, impact on quality, and degree of intervention, both individually and collectively, to present the most problematic code elements.

The **severity** of a code element is determined by the sum of the severities of the smells present in the element, considering the weights given to their importance, which can be defined by the developer.

The **representativity** defines how representative the smells present in a code element are compared to other smells at the same granularity. Based on representativity, it is possible to assess which code elements are most affected by smells at a given granularity.

The **impact on quality** is determined by mapping the quality attributes associated with smells to their respective weights, also defined by the developer.

The **intervention** defines the degree of importance in addressing a given code element, considering the perception of the developers, who can customize these weights.

> *"Quality is one of the most important aspects in software development"* (SOMMERVILLE, 2016).
>
> According to Yamashita (YAMASHITA, 2015), *"quality attributes are present in a problem if there is a mapping in the definition of the problem with these attributes."*

The **DR-Tools Code Health** allows analyzing each of these criteria individually and in their respective granularity. These values are presented and normalized on a scale of 1 (less critical) to 10 (more critical).
Additionally, a normalized indicator is provided that aggregates all these criteria, called the *Code Disease Indicator (CDI)*, with values ranging from 1 (least critical) to 100 (most critical).

Furthermore, since code elements are self-contained, we have the *CCDI (Code Disease Indicator Composition)*, which aggregates the CDIs of a lower granularity code element (*e.g.*, methods) with the CDI of a higher granularity (*e.g.*, class), following the same intervals defined for the CDI.

The following options are available for ranking and prioritizing the code issues presented by the **DR-Tools Code Health**.

##### Menu of options for ranking code issues
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

# How to Analyze Code with DR-Tools Code Health?

Detecting code issues reduces maintenance costs if the faults are found in the early stages of software development. When developing the **DR-Tools Code Health**, we considered how it could be used in the development workflow and how to involve developers in this process. 
After all, developers play a crucial role in the process, and their participation in adapting semi-automated techniques makes them more effective.
Furthermore, it is important for tools to be simple, with filtering mechanisms, and integrated into the development workflow, allowing for specific and continuous actions.

## Defining an Analysis Purpose

> *"A significant portion of the money spent on software development is used to understand existing code."* 
> Kent Beck

*Code analysis* is a discipline that every developer should develop and improve to accelerate their understanding of the code, aiming to facilitate the process of maintenance and evolution of the software project. Within the pillars of analysis, we need to monitor the evolution from the perspective of cohesion, coupling, size, and complexity, managing its complexity regardless of the programming paradigm.

To start an analysis, it is important **to define your objective/purpose**.
This objective can be: improving a quality aspect, identifying possible problems in a specific granularity, evaluating recurring issues in the code, improving the design, among others.

Next, **update your local project repository**.
You can start by **analyzing more general aspects of the project**.
Begin by **observing the more general metrics**, checking their distribution.

Take a look at this example:

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

With this simple command (`a --top 5`), we are able **to have an overview at different levels of the project**.
Firstly, a general summary with the number of *namespaces, types*, and *methods*, average values, medians, and standard deviations.
Note that we have an average of 19 *types per namespace*. However, when we look at specific data at the second level, related to *namespaces*, we can see that the 5 listed packages have values higher than the average.

At the *types* level, we can also see numerous important pieces of information, with a focus on the complexity (metric `WMC`) and size (metric `SLOC`) of the classes, as well as information on dependencies (`DEP, I-DEP`), API size (`NPM`), and couplings (`FAN-IN, FAN-OUT`).

Moving on to the *methods* level, we can observe the list of methods for the 5 considered most complex ones. Notice that the first 3 listed methods have the same signature (`sawOpcode`) in distinct classes, which could indicate that a method is defined in a hierarchy.

Here, we can **evaluate different aspects such as size or complexity**. However, let's say you want to consider code understanding issues. A metric that helps identify such problems is `NBD`. We have a standout method with a high `NBD` value. This method is not the largest, but it certainly stands out with code understanding issues. To learn more details about the *thresholds*, you can list them with the `mt` command.

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

In addition to this initial assessment, we can further analyze the code by **looking at how the symptoms (smells) are distributed**. For this, you can use the `ss` command, which provides a summary of the identified smells.

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

With this result, it is possible **to evaluate how the smells are distributed across different levels of granularity** (*namespace, type, method*), which smells appear most frequently, and, most importantly, **the distribution of multiple smells within a single code element**. Looking at the data presented at the *method* level, over 17% of the methods exhibit at least one symptom, and of all the detected smells, over 39% have more than one symptom. This is a strong indicator for investigation, as code elements that have multiple smells are more prone to bugs and changes.

Moving further in the analysis, **we can explore the criteria used**. For example, let's examine the severity of the methods.

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

When **examining severity**, we can see that the list of methods has changed compared to a simple listing of the [metrics](#metrics). This is because severity considers not only the number of [smells](#smells) in the same code element but also their severity level (*e.g.*, when identifying two methods with the same smell, such as *Long Method*, which one is more severe?). The model implemented in **DR-Tools Code Health** computes the ranking and prioritizes based on this aspect. As you can see, the criteria are presented with normalized values ranging from 1 (less critical) to 10 (more critical).

Let's **examine another criterion**, such as quality. Notice that the list of methods has changed once again, giving priority to smells that have a higher association with various quality attributes.

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

Let's **see how the aggregated value of these criteria** looks like with the presentation of the *CDI*. The *CDI* takes into account all the criteria to create a ranking of methods suggested for evaluation by the developer.

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

Note that the *CDI* aggregates the criteria, with normalized values ranging from 1 (less critical) to 100 (more critical).

Now, does **changing the granularity also change our perception of problems**? 
The **DR-Tools Code Health** allows for this type of investigation in the discussed granularities.


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

Similarly, it is also possible to evaluate the criteria at the *type* granularity and assess potential symptoms, as well as check the respective *CDI*.

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

We can also **assess the composition of potential problems at a lower granularity with a higher granularity** using the *CCDI*. In other words, for example, smells identified in methods along with smells identified in the class that contains these methods.


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

Finally, it is also possible to assess what **DR-Tools Code Health** found in terms of impact on quality, importance of the detected problems, and intervention at the respective granularities compared to the customized parameters.
 

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

Just to illustrate one of these investigations, at the *method* granularity, when considering the quality attributes, *maintainability* was the most impacted. However, when considering the weighting assigned by the developers (*i.e.*, considering that the symptoms related to these attributes are more relevant to them), *complexity* was the most impacted.

These customizations are a valuable resource because we can adjust these parameters in different criteria according to the project's context.

Furthermore, **DR-Tools Code Health** can be adopted during [programming activities](#programming-activities) or in [code review](#code-review) sessions, aiming to support decision-making processes.

**DR-Tools Code Health** is a flexible and adaptable tool. With it, you can analyze, cross-reference, and evaluate code data in different ways, as well as customize and parameterize it according to the project's context. The purpose of the tool is not to judge but to support and assist developers in decision-making processes.


## Programming Activities

The programming activities considered range from implementing new features to preventive and perfective maintenance in the code.

> According to Tufano et al. (TUFANO et al., 2017), *"the majority of code with smells (between 60% and 66%) is introduced during code improvement."*

Just as refactorings should be considered as part of your daily activities, a habit, code analysis should also be part of the daily routine of developers.

The use of **DR-Tools Code Health** allows for prioritizing and filtering smells at different granularities, considering quality aspects during programming activities, aiding in the identification of problems. Even before making a commit, its use can help minimize and/or avoid potential problems that would be identified later and already present in the project's repository.


## Code Review

Code review activities can take place individually, in pairs, or even in team meetings. These reviews, when conducted with multiple team members, can bring learning opportunities, especially when evaluating the negative aspects of smells that should not be followed.

> Digkas et al. (DIGKAS et al., 2020) studied the relationship between code quality practices and the cleanliness of new code. They evaluated 57 projects from the Apache ecosystem, totaling 66,661 classes and 56,890 commits. The results suggest that writing clean new code (or at least cleaner code) can be an effective strategy to reduce technical debt density, preventing software degradation over time. The results also suggest that projects that adopt explicit quality improvement policies (e.g., discussions about quality in team meetings) are associated with a higher frequency of new commits with cleaner code. 

With **DR-Tools Code Health**, it is possible to continuously monitor the presence of problems to prevent a critical increase in change activities. The tool can assist the team in conducting technical investigations based on the code and planning their actions, whether immediate, planned, or even disregarding them.

The practical application of **DR-Tools Code Health** can assist in the evaluation and study of critical code problems and, consequently, those that are not, in project sustainability. Furthermore, the tool can help professionals monitor and evolve code problems, systematically assessing the need for intervention.


# Customizations

It is possible to make various types of customizations in **DR-Tools Code Health**.
These customizations are related to the definitions of [smells](#smells), co-occurrences, and weights used in prioritization. Some customizations may involve code changes, while others involve only modifying parameters in the configuration file.

## Customizations through Code

Most developers benefit from the heuristic-based approach because it allows for the construction of customized detections, which are efficient in smell detection. The customization process consists of two steps: 1) formulation of the heuristic and 2) refinement of the general heuristic, including specific metrics, *thresholds*, and logical operators.
The success of detection relies directly on the efficiency of an automated customization process.
There is also a need for generalized detection models that can be easily adapted to new smells and allow for the detection of multiple issues in the same code element.

Thus, the **DR-Tools Code Health** has a high-level structure that supports the definition and identification of smells, rather than solely relying on ASTs or metrics.
This detection support structure is an extension and adaptation of the infrastructure built for the [DR-Tools Suite](http://drtools.site), which already includes code components and OO metrics. The goal is to instantiate the model and prioritize methods through a [Fluent API](https://martinfowler.com/bliki/FluentInterface.html).

##### Example of Smell Definition in Code Health

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
It is also possible to define which smells consistently appear together in code elements. The **DR-Tools Code Health** allows specifying co-occurrences of multiple smells as needed by the team.

##### Example of Smell Co-occurrence Definition in Code Health

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

## Customizations through File Configuration

Other customizations, without the need to alter the code, can be made to adjust parameters of the criteria used in the [prioritization and ranking](#ranking-and-prioritization) of the most problematic code elements. Simply edit the file and adapt the parameters to the context of your project.

##### Snippet from the `drtools-config.properties` configuration file

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
