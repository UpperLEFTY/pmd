---
title: PMD Release Notes
permalink: pmd_release_notes.html
keywords: changelog, release notes
---

## ????? - 6.0.0-SNAPSHOT

The PMD team is pleased to announce PMD 6.0.0.

This is a major release.

### Table Of Contents

* [New and noteworthy](#new-and-noteworthy)
    *   [Revamped Apex CPD](#revamped-apex-cpd)
    *   [Java Type Resolution](#java-type-resolution)
    *   [Metrics Framework](#metrics-framework)
    *   [Error Reporting](#error-reporting)
    *   [Java Symbol Table](#java-symbol-table)
    *   [Apex Parser Update](#apex-parser-update)
    *   [New Rules](#new-rules)
    *   [Modified Rules](#modified-rules)
    *   [Deprecated Rules](#deprecated-rules)
    *   [Removed Rules](#removed-rules)
* [Fixed Issues](#fixed-issues)
* [API Changes](#api-changes)
* [External Contributions](#external-contributions)

### New and noteworthy

#### Revamped Apex CPD

We are now using the Apex Jorje Lexer to tokenize Apex code for CPD. This change means:

*   All comments are now ignored for CPD. This is consistent with how other languages such as Java and Groovy work.
*   Tokenization honors the language specification, which improves accuracy.

CPD will therefore have less false positives and false negatives.

#### Java Type Resolution

As part of Google Summer of Code 2017, [Bendegúz Nagy](https://github.com/WinterGrascph)'s work on type resolution for Java continues.
For this release he has extended support for method calls for both instance and static methods.

Method shadowing and overloading are supported, as are varargs. However, the selection of the target method upon the presence
of generics and type inference is still work in progress. Expect it in forecoming releases.

As for fields, the basic support was in place for release 5.8.0, but has now been expanded to support static fields.

#### Metrics Framework

As part of Google Summer of Code 2017, [Clément Fournier](https://github.com/oowekyala) is continuing his work
on the new metrics framework for object-oriented metrics.

There are already a couple of metrics (e.g. ATFD, WMC, Cyclo, LoC) implemented. More metrics are planned.
Based on those metrics, rules like "GodClass" detection can be implemented more easily.

The Metrics framework has been abstracted and is available in `pmd-core` for other languages. With this
PMD release, the metrics framework is supported for both Java and Apex.

#### Error Reporting

A number of improvements on error reporting have taken place, meaning changes to some of the report formats.

##### Processing Errors

Processing errors can now provide not only the message previously included on some reports, but also a full stacktrace.
This will allow better error reports when providing feedback to the PMD team and help in debugging issues.

The report formats providing full stacktrace of errors are:

*   html
*   summaryhtml
*   textcolor
*   vbhtml
*   xml

##### Configuration Errors

For a long time reports have been notified of configuration errors on rules, but they have remained hidden.
On a push to make these more evident to users, and help them get the best results out of PMD, we have started
to include them on the reports.

So far, only reports that include processing errors are showing configuration errors. In other words, the report formats
providing configuration error reporting are:

*   csv
*   html
*   summaryhtml
*   text
*   textcolor
*   vbhtml
*   xml

As we move forward we will be able to detect and report more configuration errors (ie: incomplete `auxclasspath`)
and include them to such reports.

#### New Rules

*   The rule `NcssCount` (ruleset `java-codesize`) replaces the three rules "NcssConstructorCount", "NcssMethodCount",
    and "NcssTypeCount". The new rule uses the metrics framework to achieve the same. It has two properties, to
    define the report level for method and class sizes separately. Constructors and methods are considered the same.

*   The new rule `ForLoopCanBeForeach` (ruleset `java-migration`) helps to identify those for-loops that can
    be safely refactored into for-each-loops available since java 1.5.

#### Modified Rules

*   The rule `UnnecessaryFinalModifier` (ruleset `java-unnecessarycode`) has been revamped to detect more cases.
    It will now flag anonymous class' methods marked as final (can't be overridden, so it's pointless), along with
    final methods overridden / defined within enum instances.

*   The rule `UnnecessaryParentheses` (ruleset `java-controversial`) has been merged into `UselessParentheses`
    (ruleset `java-unnecessary`). The rule covers all scenarios previously covered by either rule.

*   The rule `UncommentedEmptyConstructor` (ruleset `java-design`) will now ignore empty constructors annotated with
    `javax.inject.Inject`.

*   The rule `AbstractClassWithoutAnyMethod` (ruleset `java-design`)  will now ignore classes annotated with
    `com.google.auto.value.AutoValue`.

*   The rule `GodClass` (ruleset `java-design`) has been revamped to use the new metrics framework.

#### Deprecated Rules

*   The rules `NcssConstructorCount`, `NcssMethodCount`, and `NcssTypeCount` (ruleset `java-codesize`) have been
    deprecated. They will be replaced by the new rule `NcssCount` in the same ruleset.

#### Removed Rules

*   The deprecated rule `UseSingleton` has been removed from the ruleset `java-design`. The rule has been renamed
    long time ago to `UseUtilityClass`.

#### Java Symbol Table

A [bug in symbol table](https://github.com/pmd/pmd/pull/549/commits/0958621ca884a8002012fc7738308c8dfc24b97c) prevented
the symbol table analysis to properly match primitive arrays types. The issue [affected the `java-unsedcode/UnusedPrivateMethod`](https://github.com/pmd/pmd/issues/521)
rule, but other rules may now produce improved results as consequence of this fix.

#### Apex Parser Update

The Apex parser version was bumped, from `1.0-sfdc-187` to `1.0-sfdc-224`. This update let us take full advantage
of the latest improvements from Salesforce, but introduces some breaking changes:

*   `BlockStatements` are now created for all control structures, even if no brace is used. We have therefore added
    a `hasCurlyBrace` method to differentiate between both scenarios.
*   New AST node types are available. In particular `CastExpression`, `ConstructorPreamble`, `IllegalStoreExpression`,
    `MethodBlockStatement`, `Modifier`, `MultiStatement`, `NestedExpression`, `NestedStoreExpression`,
    `NewKeyValueObjectExpression` and `StatementExecuted`
*   Some nodes have been removed. Such is the case of `TestNode`, `DottedExpression` and `NewNameValueObjectExpression`
    (replaced by `NewKeyValueObjectExpression`)

All existing rules have been updated to reflect these changes. If you have custom rules, be sure to update them.

### Fixed Issues

*   apex
    *   [#488](https://github.com/pmd/pmd/pull/488): \[apex] Use Apex lexer for CPD
    *   [#489](https://github.com/pmd/pmd/pull/489): \[apex] Update Apex compiler
    *   [#500](https://github.com/pmd/pmd/issues/500): \[apex] Running through CLI shows jorje optimization messages
*   cpp
    *   [#448](https://github.com/pmd/pmd/issues/448): \[cpp] Write custom CharStream to handle continuation characters
*   java
    *   [#1513](https://sourceforge.net/p/pmd/bugs/1513/): \[java] Remove deprecated rule UseSingleton
    *   [#487](https://github.com/pmd/pmd/pull/487): \[java] Fix typeresolution for anonymous extending object
    *   [#496](https://github.com/pmd/pmd/issues/496): \[java] processing error on generics inherited from enclosing class
    *   [#527](https://github.com/pmd/pmd/issues/527): \[java] Lombok getter annotation on enum is not recognized correctly
*   java-comments
    *   [#536](https://github.com/pmd/pmd/issues/536): \[java] CommentDefaultAccessModifierRule ignores constructors
*   java-controversial
    *   [#388](https://github.com/pmd/pmd/issues/388): \[java] controversial.AvoidLiteralsInIfCondition 0.0 false positive
    *   [#408](https://github.com/pmd/pmd/issues/408): \[java] DFA not analyzing asserts
    *   [#537](https://github.com/pmd/pmd/issues/537): \[java] UnnecessaryParentheses fails to detect obvious scenario
*   java-design
    *   [#357](https://github.com/pmd/pmd/issues/357): \[java] UncommentedEmptyConstructor consider annotations on Constructor
    *   [#438](https://github.com/pmd/pmd/issues/438): \[java] Relax AbstractClassWithoutAnyMethod when class is annotated by @AutoValue
    *   [#590](https://github.com/pmd/pmd/issues/590): \[java] False positive on MissingStaticMethodInNonInstantiatableClass
*   java-sunsecure
    *   [#468](https://github.com/pmd/pmd/issues/468): \[java] ArrayIsStoredDirectly false positive
*   java-unusedcode
    *   [#521](https://github.com/pmd/pmd/issues/521): \[java] UnusedPrivateMethod returns false positives with primitive data type in map argument
*   java-unnecessarycode
    *   [#412](https://github.com/pmd/pmd/issues/412): \[java] java-unnecessarycode/UnnecessaryFinalModifier missing cases

### API Changes

*   The class `net.sourceforge.pmd.lang.dfa.NodeType` has been converted to an enum.
    All node types are enum members now instead of int constants. The names for node types are retained.

*   The properties API (rule and report properties) have been revamped to be fully typesafe. This is everything
    around `net.sourceforge.pmd.PropertyDescriptor`.

*   The rule classes `net.sourceforge.pmd.lang.apex.rule.apexunit.ApexUnitTestClassShouldHaveAsserts`
    and `net.sourceforge.pmd.lang.apex.rule.apexunit.ApexUnitTestShouldNotUseSeeAllDataTrue` have been
    renamed to `ApexUnitTestClassShouldHaveAssertsRule` and `ApexUnitTestShouldNotUseSeeAllDataTrueRule`,
    respectively. This is to comply with the naming convention, that each rule class should be suffixed with "Rule".
    This change has no impact on custom rulesets, since the rule names themselves didn't change.

*   The never implemented method `PMD.processFiles(PMDConfiguration, RuleSetFactory, Collection<File>, RuleContext, ProgressMonitor)` along with the interface `ProgressMonitor` has been removed.

*   All APIs deprecated in older versions are now removed. This includes:
    *    `Renderer.getPropertyDefinitions`
    *    `AbstractRenderer.defineProperty(String, String)`
    *    `AbstractRenderer.propertyDefinitions`
    *    `ReportListener`
    *    `Report.addListener(ReportListener)`
    *    `SynchronizedReportListener`
    *    `CPDConfiguration.CPDConfiguration(int, Language, String)`
    *    `CPDConfiguration.getRendererFromString(String)`
    *    `StreamUtil`
    *    `StringUtil.appendXmlEscaped(StringBuilder, String)`
    *    `StringUtil.htmlEncode(String)`


*   Several methods in `net.sourceforge.pmd.util.CollectionUtil` have been deprecated and will be removed in PMD 7.0.0. In particular:
    *    `CollectionUtil.addWithoutDuplicates(T[], T)`
    *    `CollectionUtil.addWithoutDuplicates(T[], T[])`
    *    `CollectionUtil.areSemanticEquals(T[], T[])`
    *    `CollectionUtil.areEqual(Object, Object)`
    *    `CollectionUtil.arraysAreEqual(Object, Object)`
    *    `CollectionUtil.valuesAreTransitivelyEqual(Object[], Object[])`


*   Several methods in `net.sourceforge.pmd.util.StringUtil` have been deprecated and will be removed in PMD 7.0.0. In particular:
    *    `StringUtil.startsWithAny(String, String[])`
    *    `StringUtil.isNotEmpty(String)`
    *    `StringUtil.isEmpty(String)`
    *    `StringUtil.isMissing(String)`
    *    `StringUtil.areSemanticEquals(String, String)`
    *    `StringUtil.replaceString(String, String, String)`
    *    `StringUtil.replaceString(String, char, String)`
    *    `StringUtil.substringsOf(String, char)`
    *    `StringUtil.substringsOf(String, String)`
    *    `StringUtil.asStringOn(StringBuffer, Iterator, String)`
    *    `StringUtil.asStringOn(StringBuilder, Object[], String)`
    *    `StringUtil.lpad(String, int)`

*   The class `net.sourceforge.pmd.lang.java.typeresolution.typedefinition.JavaTypeDefinition` is now abstract, and has been enhanced
    to provide several new methods.

### External Contributions

*   [#420](https://github.com/pmd/pmd/pull/420): \[java] Fix UR anomaly in assert statements - [Clément Fournier](https://github.com/oowekyala)
*   [#482](https://github.com/pmd/pmd/pull/482): \[java] Metrics testing framework + improved capabilities for metrics - [Clément Fournier](https://github.com/oowekyala)
*   [#484](https://github.com/pmd/pmd/pull/484): \[core] Changed linux usage to a more unix like path - [patriksevallius](https://github.com/patriksevallius)
*   [#486](https://github.com/pmd/pmd/pull/486): \[java] Add basic method typeresolution - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#492](https://github.com/pmd/pmd/pull/492): \[java] Typeresolution for overloaded methods - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#495](https://github.com/pmd/pmd/pull/495): \[core] Custom rule reinitialization code - [Clément Fournier](https://github.com/oowekyala)
*   [#479](https://github.com/pmd/pmd/pull/479): \[core] Typesafe and immutable properties - [Clément Fournier](https://github.com/oowekyala)
*   [#499](https://github.com/pmd/pmd/pull/499): \[java] Metrics memoization tests - [Clément Fournier](https://github.com/oowekyala)
*   [#501](https://github.com/pmd/pmd/pull/501): \[java] Add support for most specific vararg method type resolution - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#502](https://github.com/pmd/pmd/pull/502): \[java] Add support for static field type resolution - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#505](https://github.com/pmd/pmd/pull/505): \[java] Followup on metrics - [Clément Fournier](https://github.com/oowekyala)
*   [#506](https://github.com/pmd/pmd/pull/506): \[java] Add reduction rules to type inference - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#511](https://github.com/pmd/pmd/pull/511): \[core] Prepare abstraction of the metrics framework - [Clément Fournier](https://github.com/oowekyala)
*   [#512](https://github.com/pmd/pmd/pull/512): \[java] Add incorporation to type inference - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#513](https://github.com/pmd/pmd/pull/513): \[java] Fix for maximally specific method selection - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#514](https://github.com/pmd/pmd/pull/514): \[java] Add static method type resolution - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#517](https://github.com/pmd/pmd/pull/517): \[doc] Metrics documentation - [Clément Fournier](https://github.com/oowekyala)
*   [#518](https://github.com/pmd/pmd/pull/518): \[core] Properties refactoring: factorized enumerated property - [Clément Fournier](https://github.com/oowekyala)
*   [#523](https://github.com/pmd/pmd/pull/523): \[java] Npath complexity metric and rule - [Clément Fournier](https://github.com/oowekyala)
*   [#524](https://github.com/pmd/pmd/pull/524): \[java] Add support for explicit type arguments with method invocation - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#525](https://github.com/pmd/pmd/pull/525): \[core] Fix line ending and not ignored files issues - [Matias Comercio](https://github.com/MatiasComercio)
*   [#528](https://github.com/pmd/pmd/pull/528): \[core] Fix typo - [Ayoub Kaanich](https://github.com/kayoub5)
*   [#529](https://github.com/pmd/pmd/pull/529): \[java] Abstracted the Java metrics framework - [Clément Fournier](https://github.com/oowekyala)
*   [#530](https://github.com/pmd/pmd/pull/530): \[java] Fix issue #527: Lombok getter annotation on enum is not recognized correctly - [Clément Fournier](https://github.com/oowekyala)
*   [#533](https://github.com/pmd/pmd/pull/533): \[core] improve error message - [Dennis Kieselhorst](https://github.com/deki)
*   [#535](https://github.com/pmd/pmd/pull/535): \[apex] Fix broken Apex visitor adapter - [Clément Fournier](https://github.com/oowekyala)
*   [#542](https://github.com/pmd/pmd/pull/542): \[java] Metrics abstraction - [Clément Fournier](https://github.com/oowekyala)
*   [#545](https://github.com/pmd/pmd/pull/545): \[apex] Apex metrics framework - [Clément Fournier](https://github.com/oowekyala)
*   [#548](https://github.com/pmd/pmd/pull/548): \[java] Metrics documentation - [Clément Fournier](https://github.com/oowekyala)
*   [#550](https://github.com/pmd/pmd/pull/550): \[java] Add basic resolution to type inference - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#553](https://github.com/pmd/pmd/pull/553): \[java] Refactored ParserTst into a static utility class + add getSourceFromClass - [Clément Fournier](https://github.com/oowekyala)
*   [#554](https://github.com/pmd/pmd/pull/554): \[java] Fix #537: UnnecessaryParentheses fails to detect obvious scenario - [Clément Fournier](https://github.com/oowekyala)
*   [#555](https://github.com/pmd/pmd/pull/555): \[java] Changed metrics/CyclomaticComplexityRule to use WMC when reporting classes - [Clément Fournier](https://github.com/oowekyala)
*   [#556](https://github.com/pmd/pmd/pull/556): \[java] Fix #357: UncommentedEmptyConstructor consider annotations on Constructor - [Clément Fournier](https://github.com/oowekyala)
*   [#557](https://github.com/pmd/pmd/pull/557): \[java] Fix NPath metric not counting ternaries correctly - [Clément Fournier](https://github.com/oowekyala)
*   [#563](https://github.com/pmd/pmd/pull/563): \[java] Add support for basic method type inference for strict invocation - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#566](https://github.com/pmd/pmd/pull/566): \[java] New rule in migrating ruleset: ForLoopCanBeForeach - [Clément Fournier](https://github.com/oowekyala)
*   [#567](https://github.com/pmd/pmd/pull/567): \[java] Last API change for metrics (metric options) - [Clément Fournier](https://github.com/oowekyala)
*   [#570](https://github.com/pmd/pmd/pull/570): \[java] Model lower, upper and intersection types - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#573](https://github.com/pmd/pmd/pull/573): \[java] Data class rule - [Clément Fournier](https://github.com/oowekyala)
*   [#576](https://github.com/pmd/pmd/pull/576): \[doc]\[java] Add hint for Guava users in InefficientEmptyStringCheck - [mmoehring](https://github.com/mmoehring)
*   [#578](https://github.com/pmd/pmd/pull/578): \[java] Refactored god class rule - [Clément Fournier](https://github.com/oowekyala)
*   [#579](https://github.com/pmd/pmd/pull/579): \[java] Update parsing to produce upper and lower bounds - [Bendegúz Nagy](https://github.com/WinterGrascph)
*   [#580](https://github.com/pmd/pmd/pull/580): \[core] Add AbstractMetric to topple the class hierarchy of metrics - [Clément Fournier](https://github.com/oowekyala)
*   [#581](https://github.com/pmd/pmd/pull/581): \[java] Relax AbstractClassWithoutAnyMethod when class is annotated by @AutoValue - [Niklas Baudy](https://github.com/vanniktech)
*   [#583](https://github.com/pmd/pmd/pull/583): \[java] Documentation about writing metrics - [Clément Fournier](https://github.com/oowekyala)
*   [#585](https://github.com/pmd/pmd/pull/585): \[java] Moved NcssCountRule to codesize.xml - [Clément Fournier](https://github.com/oowekyala)
*   [#587](https://github.com/pmd/pmd/pull/587): \[core] Properties refactoring: Move static constants of ValueParser to class ValueParserConstants - [Clément Fournier](https://github.com/oowekyala)
*   [#588](https://github.com/pmd/pmd/pull/588): \[java] XPath function to compute metrics - [Clément Fournier](https://github.com/oowekyala)
*   [#598](https://github.com/pmd/pmd/pull/598): \[java] Fix #388: controversial.AvoidLiteralsInIfCondition 0.0 false positive - [Clément Fournier](https://github.com/oowekyala)
*   [#620](https://github.com/pmd/pmd/pull/620): \[core] Moved properties to n.s.pmd.properties - [Clément Fournier](https://github.com/oowekyala)

