# Gradle
## next generation build toolkit
<br/>
<br/>
#### **Stig Kleppe-Jørgensen**
FINN reise

Note:
Hei, jeg heter Stig, jobber i FINN reise og tenkte jeg skulle gi dere en liten
introduksjon til byggeverktøyet Gradle. Og kom gjerne med spørsmål underveis!
Men før jeg kommer til Gradle, tenkte jeg jeg skulle ta dere med på en liten reise; en tidsreise.

----

<!-- .slide: data-background="img/Funny-Quotes-about-Dinosaurs-10.jpg" data-background-size="50%" -->

Note:
Reisen vår begynner for lenge, lenge siden...vel, det føles i hvert fall slik. Det typiske
java-prosjektet var en enkel applet uten så mye logikk. Disse ble bygget med vanlige shell-script,
eller kanskje make hvis man var heldig. Dvs. enkle prosjekter som heller ikke krevde så mye støtte
i byggeverktøyet.

Men prosjektene vokste raskt og man begynte å få voksesmerter...og så kom ant.

--

<!-- .slide: data-background="img/stoneagenerd.png" data-background-size="75%" -->

Note:
Ant var skrevet i Java og dermed cross-platform, hadde tasks som lavnivå byggesteiner
og brukte xml som script-språk for å sy disse taskene sammen til det en ville gjøre.
Og etterhvert brukte "alle" ant; noe som førte til at det fantes tasks for alt mulig.

Men scriptene var vanskelig å gjenbruke på tvers av prosjekter og de ble fort spaghetti.
Dette gjorde det vanskelig å sette seg inn i byggscript for andre prosjekter.

--

<!-- .slide: data-background="img/flintstones.jpg" data-background-size="90%" -->

Note:
Dermed var det duket for Maven. Her var det mange nyvinninger. Den hadde en underliggende
modell, med pom-en, som var prosjekt-orientert, støttet flere prosjekter i et bygg, hadde
konvensjoner for hvor filer skulle ligge og hvordan avhengigheter skulle fungere.
Den definerte også livssyklusen til et bygg med forskjellige faser, f.eks. clean og
install.

I begynnelsen av et prosjekt var det greit med et rammeverk som Maven, som har veldig
bestemte meninger om hvordan ting skal gjøres. Men så fort en går utenfor rammene blir
alt veldig tungvindt og vanskelig.

--

<!-- .slide: data-background="img/jetsons.jpg" data-background-size="90%" -->

Note:
Og da har vi kommet til Gradle...som...

----

### Gradle &asymp; Ant + Maven + Groovy DSL

* Ant's power and flexibility
* Maven's conventions and dependency management
* Wrapped in a very nice DSL

Note:
Kort fortalt så har Gradle tatt det beste fra ant og maven, blandet sammen med en veldig
rik DSL.
Fleksibiliteten gjør at en ikke låses fast i en måte å gjøre ting på, men kan strukturere
bygget slik en vil. Samtidig har en konvensjoner å støtte seg på slik at ikke hjulet må
finnes opp på nytt.

----

## Very easy to get started

```
$ which java
/usr/bin/java

$ ./gradlew build
```

Note:
Det er veldig lett å komme i gang med eksisterende bygg. Så lenge JDK-en er installert, så
kan man bare kjøre et medfølgende script, gradlew, som laster ned oppgitt versjon av Gradle,
hvis det ikke allerede er gjort, og kjører i gang bygget. Med dette sikrer en også at alle
bruker samme versjon av Gradle for å bygge prosjektet.

Dette er en feature som kalles Gradle Wrapper og er en bootstrap for å komme igang med
Gradle.
Med wrapper trenger en ikke å ha installert Gradle, kan spesifisere en spesifikk versjon,
sjekke inn "all" konfig for prosjektet

----

## Gradle hides complexity

<!-- .slide: data-background="img/complexity.gif" data-background-size="100%" -->

Note:
Gradle gir stor fleksibilitet, men det gjemmes hvis en ikke bruker det.

--

### The simplest build script

<!-- .slide: data-background="img/complexity.gif" data-background-size="100%" -->

```java
apply plugin: 'java'
```

Note:
Mindre kompleksitet ved å følge Gradle's (Maven's) konvensjoner slik at det blir veldig lite konfigurasjon.
Dette er alt som behøves for å kompilere, kjøre tester og bygge jar.

--

### No need to show unused information

<!-- .slide: data-background="img/complexity.gif" data-background-size="100%" -->

Note:
en trenger ikke å vise alt som skjer når en ikke behøver informasjonen. heller vente
med å vise til det er nødvendig.
vise et vanlig bygg, kanskje som feiler på en test?

----

## Rich Groovy DSL
### Declarative
### Build-by-convention

<!-- .slide: data-background="img/dsl.png" data-background-size="100%" -->

Note:
Gradles byggscript implementerter man gjennom en Groovy DSL som gjør det veldig enkelt å få til
alt fra enkle til store
og komplekse bygg. Denne DSL-en er deklarativ, veldig lett å utvide og har mye build-by-convention
innebygd, dvs. en slipper å konfigurere hvis en følger konvensjonene. Dette gjør at byggescriptene
blir små og lette å vedlikeholde.

----

## Extendable task graph

<!-- .slide: data-background="img/dag.png" data-background-size="100%" data-background-repeat="repeat" -->

```java
featureTest.dependsOn startJetty
build.dependsOn featureTest
```

Note:
Alt som gjøres i et bygg er modellert som tasks, f.eks. clean, test, jar og build. Disse blir
lagt inn i bygget av plugins, f.eks. java-pluginen, og satt sammen i en spesifikk rekkefølge.
En vil også typisk lage egne task der en trenger noe utover det som følger med i pluginene.
Rekkefølgen på alle tasker kan enkelt endres på hvis en behøver det.

(Maven har også tasks, men disse er koblet til en fase. Det er fasene som er satt til å kjøre i en
spesiell rekkefølge. Dvs. at det ikke er mulig å si at en Maven-task skal kjøre før en annen uten
å gå via faser. Dette gir ekstra kompleksitet med veldig liten gevinst.)

----

## Easy to reuse build logic
### Composition over inheritance

<!-- .slide: data-background="img/composition.png" data-background-size="100%" -->

```java
apply from: 'http://..../codecheck.gradle'
```

Note:
Typisk når en får litt større prosjekter er det ønskelig å dele bygge-script på tvers av disse.
Gradle gjør dette veldig enkelt med f.eks. å injecte felles konfigurasjon fra et sted inn i alle
eller spesifikke subprosjekter, eller å importere gjenbrukbare script fra en URL/fil.

----

## Easy to write plugins

<!-- .slide: data-background="img/plugins.jpg" data-background-size="100%" -->

```java
apply plugin: GreetingPlugin

class GreetingPlugin implements Plugin<Project> {
    void apply(Project project) {
        project.task('hello') << {
            println "Hello from the GreetingPlugin"
        }
    }
}

build.dependsOn hello
```

Note:
En plugin er egentlig bare en pakke med gjenbrukbar bygge-logikk, noe som gjør de enkle å lage.
Og hvis det allerede finnes en ant task som gjør jobben kan denne gjenbrukes veldig enkelt.

----

## Incremental builds

<!-- .slide: data-background="img/incremental.jpg" data-background-size="100%" -->

A rich and deep API makes complex problems easy to solve

```java
task npmInstall(type: Exec) {
  inputs.file "package.json"
  outputs.dir "node_modules"
  commandLine 'npm', 'install'
}

test.dependsOn npmInstall
```

Note:
Inkrementelle bygg gjør at Gradle bare kjører det som er nødvendig, f.eks. vil den ikke kjøre
tester hvis ikke noe kode er forandret. Dette er en del av APIet og ikke noe hver plugin trenger
å implementere selv.

Her er et eksempel på hvordan incremental build enkelt kan settes opp.
Tasken vil bare kjøre hvis det er gjort endringer på package.json
eller katalogen node_modules ikke finnes.

Her setter en også opp at denne tasken skal kjøres før test kjøres.

----

## Proper multi-project support

<!-- .slide: data-background="img/multiproject.png" data-background-size="100%" -->

Note:
Gradle støtter å bygge deler av et multiprosjekt. Dvs. at samme hvor en står når en kjører igang
et bygg, vil Gradle forsikre at andre prosjekt som gjeldende er avhengig av, vil bygges først.

--

### Typical layout

<!-- .slide: data-background="img/multiproject.png" data-background-size="100%" -->

```
$ ls -1f

example-api/
example-integration/
example-web/
gradle/
  wrapper/
    gradle-wrapper.jar
    gradle-wrapper.properties
build.gradle
gradlew
gradlew.bat
settings.gradle
```

--

### Multi-project setup file

<!-- .slide: data-background="img/multiproject.png" data-background-size="100%" -->

```java
include 'example-web',
        'example-api',
        'example-integration'
```

Note:
i settings.gradle setter en opp hvilke prosjekter som inngår i multiprosjektet.

--

### Common config for all subprojects

<!-- .slide: data-background="img/multiproject.png" data-background-size="100%" -->

```java
allprojects {
  apply plugin: 'maven'

  group = 'no.finn.travel'
  version = '1.0.0-snapshot'
}

subprojects {
  apply plugin: 'java'
  apply from: 'http://.../codecheck.gradle'

  sourcecompatibility = 1.7
  targetcompatibility = 1.7
}
```

--

### Depending on another subproject

<!-- .slide: data-background="img/multiproject.png" data-background-size="100%" -->

```java
dependencies {
    compile project(':example-api')
    ...
}
```

Note:
viser også hvordan en setter opp avhengighet til et annet prosjekt i et multiprosjekt.
som dere ser, er det ikke nødvendig å spesifisere versjonsnummer for interne avhengigheter!
noen her som har slitt med merging av versjonsnummer?

----

## Dependency management
### Everything you need

<!-- .slide: data-background="img/depsmgmt.png" data-background-size="100%" -->

Note:
Når det gjelder dependency management gir Gradle mange muligheter.

--

<!-- .slide: data-background="img/depsmgmt.png" data-background-size="100%" -->

```java
dependencies {
    compile 'org.slf4j:slf4j-api:1.7.5'
    testcompile 'junit:junit:4.11'
}

repositories {
  maven {
    url = 'http://mavenproxy.finntech.no/finntech-release/'
  }
}
```

Note:
et enkelt eksempel på en dependency-seksjon med 1 compile og 1 test compile dependency.
viser også hvordan en setter opp en maven repo.

--

<!-- .slide: data-background="img/depsmgmt.png" data-background-size="100%" -->

```java
configurations {
  compile.transitive = false
  testcompile.transitive = false

  all*.exclude group: 'commons-logging'
}
```

Note:
først setter vi at dependencies i compile og testcompile scope ikke skal resolve deps transitivt.
dette betyr at en må spesifisere selv i scriptet alle deps som din kode bruker. den siste linjen
ekskluderer dependencien for alle scopes, eller configurations som gradle kaller det.

--

<!-- .slide: data-background="img/depsmgmt.png" data-background-size="100%" -->

```java
ext.versions = [
  thrift: '0.9.1'
]

configurations {
  all*.resolutionstrategy {
    force "org.apache.thrift:libthrift:${versions.thrift}"

    eachdependency { details ->
      // always use the glassfish version of the javax.inject library
      if (details.requested.name == 'javax.inject') {
        details.usetarget "org.glassfish.hk2.external:javax.inject:${details.requested.version}"
      }
    }
  }
}
```

Note:
ved å lage en egen strategi kan en f.eks. force versjonsnummer på tvers av alle konfigurasjoner
og subprosjekt. eller bytte ute group-id/artifact-id på dependencies f.eks. i de tilfeller der
det finnes flere versjoner av et api.

her er også et eksempel på hvordan man drar ut versjonsnummer fra dependencien.

----

## A complete example

```java
apply plugin: 'java'

dependencies {
    compile 'org.slf4j:slf4j-api:1.7.5'
    testcompile 'junit:junit:4.11'
}

repositories {
  maven {
    url = 'http://mavenproxy.finntech.no/finntech-release/'
  }
}
```
