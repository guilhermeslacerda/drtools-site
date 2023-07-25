![DR-Tools Logo](https://guilhermeslacerda.github.io/drtools-site/images/logo_drtools.png?raw=true)


DR-Tools Code Health - Diagnosis
================================

- [DR-Tools Code Health - Diagnosis](#dr-tools-code-health---diagnosis)
- [Metrics](#metrics)
  - [Summary](#summary)
- [Smells](#smells)
  - [Smell Summary](#smell-summary)
  - [Smell Detected](#smell-detected)
    - [Namespace](#namespace)
    - [Type](#type)
    - [Method](#method)
  - [Co-Occurrences](#co-occurrences)
- [Ranking and Prioritization](#ranking-and-prioritization)
  - [Most Critical Namespaces (Top 10)](#most-critical-namespaces-top-10)
  - [Most Critical Types (Top 20)](#most-critical-types-top-20)
  - [Most Critical Methods (Top 20)](#most-critical-methods-top-20)


# Metrics

## Summary

| Description     | Total      |  Average      | Median        | Standard Deviation    |
|:----------------|-----------:|--------------:|--------------:|----------------------:|
| Namespaces      | 57         |      -        |    -          |  -                    |
| Types           | 1102       |   19,33 (types/namespaces) | 8,00    | 36,29     |
| SLOC            | 130364     |   118,30 (SLOC/types) | 56,00    | 195,00     |
| Methods         | 10002      |   9,08 (methods/types) | 5,00    | 26,14     |
| CYCLO           | 52838      |   47,95 (CYCLO/types) | -    | -     |


# Smells

## Smell Summary

| Granularity     | Smell Detected |  with more than one smell      |
|:----------------|-----------:|------------------------------:|
| Namespaces      | **9** (15,79%) of **57**    |  **4** (44,44% of detected or 7,02% of total)        |
| Types           | **255** (23,14%) of **1102**  |   **101** (39,61% of detected or 9,17% of total)  |
| Methods         | **5248** (52,47%) of **10002**  |   **2561** (48,80% of detected or 25,60% of total)  |

## Smell Detected

### Namespace
| Smell      | # of Instances |  %      |
|:----------------|-----------:|------------------------------:|
Too Large Package/Subsystem  | 8 | 88,89 |
Cyclic Dependency            | 5 | 55,56 |

### Type
| Smell      | # of Instances |  %      |
|:----------------|-----------:|------------------------------:|
| Insufficient Modularization | 227 | 89,02 |
| God Class                   | 45  | 17,65 |
| Cyclically-dependent Modularization | 43 | 16,86 |
| Multifaceted Abstraction    | 41 | 16,08 |
| Deep Hierarchy              | 27  | 10,59 |
| Hub-like Modularization     | 24  | 9,41  |
| Deficient Encapsulation     | 11  | 4,31  |
| Broken Modularization       | 2   | 0,78  |
| Wide Hierarchy              | 1   | 0,39  |

### Method 
| Smell      | # of Instances |  %      |
|:----------------|-----------:|------------------------------:|
| Complex Method   |  4171  | 79,48 |
| Bumpy Road          |  3270 | 62,31 |
| Long Method        |  719  | 13,70 |
| Long Parameter List |  145 | 2,76 |


## Co-Occurrences

**Category: Type**
| Co-Occurrence     | # of Instances |  %      | Impacts on    |
|:----------------|-----------:|-------------:|:---------------|
| Low cohesion and big structures in types (Insufficient Modularization, God Class) |  18 | 7,06 | Cohesion, Size |
| Complex and low cohesion structures in types (Insufficient Modularization, Multifaceted Abstraction) |  16 | 6,27 | Cohesion, Complexity |

**Category: Method**
| Co-Occurrence     | # of Instances |  %      | Impacts on    |
|:----------------|-----------:|-------------:|:---------------|
| High complexity structures in methods (Complex Method, Bumpy Road) | 1817 | 34,62 | Complexity |
| Complex structures in methods (Bumpy Road, Long Method, Complex Method) |  398 | 7,58 | Complexity |

# Ranking and Prioritization

## Most Critical Namespaces (Top 10)

| Namespace     | Severity |  Representativity   | Quality        | Intervention     | CDI           |
|:----------------|-----------:|-------------:|---------------:|-----------------:|--------------:|
| edu.umd.cs.findbugs | 10,000 |  10,000 |  10,000 |   10,000 | **100,000** |
| edu.umd.cs.findbugs.ba | 8,127 |  10,000 |  10,000|   10,000  | **81,273** |
| edu.umd.cs.findbugs.classfile |  2,948 |  10,000 |  10,000|   10,000| **29,478** |
| edu.umd.cs.findbugs.gui2 |  2,083|  10,000|  10,000|   10,000 | **20,825** |


## Most Critical Types (Top 20)

| Type     | Severity |  Representativity   | Quality        | Intervention     | CDI           |
|:----------------|-----------:|-------------:|---------------:|-----------------:|--------------:|
| edu.umd.cs.findbugs.BugInstance | 10,000 |  10,000 |  10,000 |   10,000  |  **100,000** |
| edu.umd.cs.findbugs.ba.AnalysisContext | 6,221 |  8,200 |  8,455|   7,092 | **49,242** |
| edu.umd.cs.findbugs.SortedBugCollection |  4,867 |  8,200|  8,455|  7,785 | **39,649** |


## Most Critical Methods (Top 20)

| Method     | Severity |  Representativity   | Quality        | Intervention     | CDI           |
|:----------------|-----------:|-------------:|---------------:|-----------------:|--------------:|
| edu.umd.cs.findbugs.detect.DumbMethods.sawOpcode(int seen) [Line: 645] | 10,000  |  7,000 |  8,180 |   7,254 |  **74,780** |
| edu.umd.cs.findbugs.detect.FindPuzzlers.sawOpcode(int seen) [Line: 172] | 7,446  |  7,000 |  8,180 |   7,254 |  **55,684** |
| edu.umd.cs.findbugs.detect.UnreadFields.sawOpcode(int seen) [Line: 386] |  5,776 |  7,000 |  8,180 |   7,254 |  **43,195** |
| edu.umd.cs.findbugs.detect.FindRefComparison.checkEqualsComparison(Location location, JavaClass jclass, Method method, MethodGen methodGen, ConstantPoolGen cpg, TypeDataflow typeDataflow) [Line: 1084] | 2,809 |  10,000 |  10,000 |   10,000 | **28,092** |

**Legend:**

*Criteria (severity, representativity, quality, intervention):* Values between 1 (less critical) and 10 (more critical)
*CDI (code disease indicator):* Values between 1 (less critical) and 100 (more critical)

**Explanation:**

<font size="2">

* The **severity** of a code element is determined by the sum of the severities of the smells present in the element, considering the weights given to their importance, which can be defined by the developer.

* The **representativity** defines how representative the smells present in a code element are compared to other smells at the same granularity. Based on representativeness, it is possible to assess which code elements are most affected by smells at a given granularity.

* The **impact on quality** is determined by mapping the quality attributes associated with smells to their respective weights, also defined by the developer.

* The **intervention** defines the degree of importance in addressing a given code element, considering the perception of the developers, who can customize these weights.

The **DR-Tools Code Health** allows analyzing each of these criteria individually and in their respective granularity. These values are presented and normalized on a scale of 1 (less critical) to 10 (more critical).
Additionally, a normalized indicator is provided that aggregates all these criteria, called the *Code Disease Indicator (CDI)*, with values ranging from 1 (least critical) to 100 (most critical).

</font>


------
