# practica-04a-laboratorio-estructuras-concurrentes — transcripción

Fuente: [practica-04a-laboratorio-estructuras-concurrentes.pdf](practica-04a-laboratorio-estructuras-concurrentes.pdf).

Conversión textual por página. Se conserva el texto disponible, los ejemplos y las diapositivas progresivas. No es código compilable ni una corrección del original. Los diagramas, fórmulas, colores y columnas deben cotejarse en el PDF cuando afecten una conclusión. No se modificó el archivo fuente.



## Página 1

[Ver página del PDF](practica-04a-laboratorio-estructuras-concurrentes.pdf#page=1)

```text
Laboratorio de estructuras concurrentes

                              Programación Concurrente y Paralela


                                         September 9, 2026



1    Introducción

En este laboratorio vamos a estar trabajando sobre distintas estrategias para implementar una
conjunto de valores de tipo int.

Este conjunto se implementará sobre una lista enlazada. Para cada ejercicio se espera que se
implemente la siguiente interfaz:
 public interface ConcurrentIntSet {
     // Agrega un elemento.
     // Retorna true si este fue agregado, false si ya existe en el conjunto.
     boolean add(int value);

     // Quita un elemento del conjunto.
     // Retorna true si el elemento existı́a, false si no existe el elemento.
     boolean remove(int value);

     // Retorna true si la lista contiene el elemento.
     boolean contains(int value);

     // Evita que los ejercicios sin implementar hagan que fallen tests.
     default boolean isImplemented() { return false; }
 }


Los invariantes de la lista siempre deben ser válidos para cualquier combinación de llamadas con-
currentes a los métodos públicos.

Los valores de la lista deben ser insertados en orden. Por comodidad para recorrer la lista siem-
pre se inicializa con dos nodos sentinel con los valores Long.MIN VALUE y Long.MAX VALUE. Estos
acotan todos los valores posibles del conjunto, ya que Long.MIN VALUE < Integer.MIN VALUE <
Integer.MAX VALUE < Long.MAX VALUE.

Para resolver este taller pueden usar su IDE preferida que tenga soporte para Maven. Por ejemplo
IntelliJ o Visual Studio Code con un plugin de Java + Maven. Alternativamente se provee un
makefile para resolver el taller con un editor de texto plano y una versión reciente de JDK instalada.

                                                  1
```

## Página 2

[Ver página del PDF](practica-04a-laboratorio-estructuras-concurrentes.pdf#page=2)

```text
2    Bloquear toda la lista

La solución más sencilla no siempre es la mejor. La forma más sencilla de permitir que una
estructura sea usada de forma concurrente es definir todos los métodos que la utilizan como una
zona de exclusión mutua. Completar la clase CoarseList para seguir esta estrategia. Se recomienda
tomar como referencia la implementación de SequentialListSet.



3    Paseando de lock en lock

Una mejor forma de controlar la concurrencia de una lista es bloquar nodos especı́ficos. El algoritmo
básicamente consiste en mantener en todo momento el lock del nodo actual y el anterior. Completar
la clase FineListSet.

Antes de comenzar a escribir código, piensen en las siguientes preguntas:


    • ¿Por qué hay que tomar primero el lock del predecesor y después el del nodo actual? Piensen
      qué podrı́a pasar si los locks se toman en cualquier órden.

    • Cuando se pasa de un nodo a otro, ¿en qué orden hay que liberar y tomar nuevos locks?

    • ¿Hay que bloquear nodos en el método contains?



4    Sincronización optimista

La idea detrás de este algoritmo es similar al anterior, pero solo tomando locks cuando llegamos
a la arista de la lista que nos interesa. Sin tomar locks en el camino hasta esos nodos. El precio
a pagar por hacer esto es tener que verificar que la relación previo → actual siga siendo válida
después de tomar los locks. Completar la clase OptimisticListSet.

Antes de comenzar a escribir código, piensen en las siguientes preguntas:


    • ¿Qué hace el método edgeExists?

    • ¿Qué condiciones hay que asegurar sobre nodo.next durante el borrado de un nodo para no
      romper otros usuarios concurrentes?

    • ¿Qué hay que hacer si después de tomar los locks nos damos cuenta que edgeExists(prev,
      curr) es false?




                                                 2
```

## Página 3

[Ver página del PDF](practica-04a-laboratorio-estructuras-concurrentes.pdf#page=3)

```text
5    Sincronización lazy

El algoritmo anterior tiene desventajas bastante aparentes. El método contains sigue conteniendo
locks y tenemos que recorrer la lista dos veces para cada operación. Esta es una mejora sobre esas
dos cuestiones. Completar la clase LazyListSet.

Antes de comenzar a escribir código, piensen en las siguientes preguntas:


    • ¿Qué representa el campo marked de la clase Node?
    • Si tanto marked como next son variables atómicas, ¿por qué sigue haciendo falta un lock por
      nodo?
    • ¿Qué se necesita para verificar que la relación previo → actual sigua existiendo?



6    Una lista lock free

La idea principal detrás de este algoritmo es usar la abstracción AtomicMarkableReference<T>
para unificar los campos marked y next.

Para este ejercicio deberı́a alcanzar con estos métodos de la clase AtomicMarkableReference<Node>:
 // Lee solamente la referencia actual.
 Node actual = next.getReference();

 // Lee la referencia y su marca en una sola operación atómica.
 // (La API fea es una limitación de Java de no poder reornar
 // múltiples valores fácilmente)
 boolean[] marca = new boolean[1];
 Node actual = next.get(marca);

 // Actualiza ambos valores solo si coinciden los esperados.
 // Se ejecuta de forma atómica.
 Node referenciaEsperada, referenciaNueva;
 boolean marcaEsperada, marcaNueva;
 boolean actualizado = next.compareAndSet(
     referenciaEsperada, referenciaNueva, marcaEsperada, marcaNueva);


Completar la clase LockFreeListSet sin usar ningún lock . El uso de la abstracción Window es
opcional.

Antes de comenzar a escribir código, piensen en las siguientes preguntas:


    • ¿Qué hace el método compareAndSet? ¿Qué podrı́a ocasionar que este retorne false?
    • ¿En qué se diferencia conceptualmente el tipo AtomicMarkableReference<T> de volatile
      T?

                                                 3
```

## Página 4

[Ver página del PDF](practica-04a-laboratorio-estructuras-concurrentes.pdf#page=4)

```text
• Analizar el método Window find(int value). Luego explicar el rol que cumple la marca en
      el campo next de la clase Node.



7    Experimentación

Para comparar las distintas implementaciones vamos a usar la clase SetPerformanceBenchmark.
El programa ejecuta varias mezclas de operaciones contains, add y remove, usando una cantidad
configurable de hilos y una distribución de claves uniforme o concentrada en una región pequeña
de la lista.

Antes de medir se realizan repeticiones de calentamiento para reducir el ruido introducido por el
JIT y las cachés internas de la JVM. Para cada configuración se mide la latencia de cada operación,
incluyendo los percentiles 90, 95 y 99, y el throughput total. Los resultados se muestran por pantalla
y también se guardan en un archivo CSV para poder analizarlos después.


    • Ejecutar el programa con los parámetros por defecto. ¿Qué resultados se obtienen? ¿Es lo
      que esperaban ver?

    • Cambiar la distribución de generación de claves para ser uniforme. ¿Cómo cambian los
      resultados en comparación a la corrida anterior?

    • Variar el tamaño del espacio de claves, la distribución y en el caso de la clusterizada el tamaño
      del cluster y la probabilidad de acceder una clave en ese clúster. Ejecutar varios experimentos
      y comparar resultados.

    • Para una serie se parámetros fijos, variar la cantidad de threads. ¿Hasta qué punto aumenta
      el throughput? ¿Tiene alguna relación con la cantidad de CPUs de su computadora?



References

[1] Maurice Herlihy y Nir Shavit. The Art of Multiprocessor Programming, segunda edición. Morgan
    Kaufmann, 2020. Capı́tulo 9: “Linked Lists: The Role of Locking”.




                                                    4
```

## Archivos de texto del paquete original

Se incluyen los archivos de código, configuración e instrucciones; los archivos comprimidos anidados se abrieron para recuperar su contenido. No se ejecutaron scripts de instalación. Las plantillas incompletas y la solución provista se conservan tal cual, no se presentan como soluciones elaboradas ni verificadas.

### `laboEstructurasLockFree/.mvn/wrapper/maven-wrapper.properties`

````text
wrapperVersion=3.3.4
distributionType=only-script
distributionUrl=https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.11/apache-maven-3.9.11-bin.zip
````

### `laboEstructurasLockFree/Makefile`

````text
.DEFAULT_GOAL := help

PROJECT_TMP := $(CURDIR)/.tmp
MAVEN_REPOSITORY := $(PROJECT_TMP)/maven-repository
MAVEN_WRAPPER_HOME := $(PROJECT_TMP)/maven-wrapper
MVNW := MAVEN_USER_HOME="$(MAVEN_WRAPPER_HOME)" ./mvnw -Dmaven.repo.local="$(MAVEN_REPOSITORY)"

.PHONY: help test benchmark clean

help:
	@printf '%s\n' \
		'Available commands:' \
		'  make test       Run all tests.' \
		'  make benchmark  Run the classroom performance benchmark.' \
		'  make clean      Remove build files and the project-local Maven cache.'

test:
	@$(MVNW) test

benchmark:
	@$(MVNW) compile
	@java -cp target/classes org.pcp.exactas.set.SetPerformanceBenchmark

clean:
	@rm -rf -- target "$(PROJECT_TMP)"
````

### `laboEstructurasLockFree/mvnw`

````text
#!/bin/sh
# ----------------------------------------------------------------------------
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.
# ----------------------------------------------------------------------------

# ----------------------------------------------------------------------------
# Apache Maven Wrapper startup batch script, version 3.3.4
#
# Required ENV vars:
# ------------------
#   JAVA_HOME - location of a JDK home dir
#
# Optional ENV vars
# -----------------
#   MAVEN_OPTS - parameters passed to the Java VM when running Maven
#     e.g. to debug Maven itself, use
#       set MAVEN_OPTS=-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=y,address=8000
#   MAVEN_SKIP_RC - flag to disable loading of mavenrc files
# ----------------------------------------------------------------------------

if [ -z "$MAVEN_SKIP_RC" ]; then

  if [ -f /usr/local/etc/mavenrc ]; then
    . /usr/local/etc/mavenrc
  fi

  if [ -f /etc/mavenrc ]; then
    . /etc/mavenrc
  fi

  if [ -f "$HOME/.mavenrc" ]; then
    . "$HOME/.mavenrc"
  fi

fi

# OS specific support.  $var _must_ be set to either true or false.
cygwin=false
darwin=false
mingw=false
case "$(uname)" in
CYGWIN*) cygwin=true ;;
MINGW*) mingw=true ;;
Darwin*)
  darwin=true
  # Use /usr/libexec/java_home if available, otherwise fall back to /Library/Java/Home
  # See https://developer.apple.com/library/mac/qa/qa1170/_index.html
  if [ -z "$JAVA_HOME" ]; then
    if [ -x "/usr/libexec/java_home" ]; then
      JAVA_HOME="$(/usr/libexec/java_home)"
      export JAVA_HOME
    else
      JAVA_HOME="/Library/Java/Home"
      export JAVA_HOME
    fi
  fi
  ;;
esac

if [ -z "$JAVA_HOME" ]; then
  if [ -r /etc/gentoo-release ]; then
    JAVA_HOME=$(java-config --jre-home)
  fi
fi

# For Cygwin, ensure paths are in UNIX format before anything is touched
if $cygwin; then
  [ -n "$JAVA_HOME" ] \
    && JAVA_HOME=$(cygpath --unix "$JAVA_HOME")
  [ -n "$CLASSPATH" ] \
    && CLASSPATH=$(cygpath --path --unix "$CLASSPATH")
fi

# For Mingw, ensure paths are in UNIX format before anything is touched
if $mingw; then
  [ -n "$JAVA_HOME" ] && [ -d "$JAVA_HOME" ] \
    && JAVA_HOME="$(
      cd "$JAVA_HOME" || (
        echo "cannot cd into $JAVA_HOME." >&2
        exit 1
      )
      pwd
    )"
fi

if [ -z "$JAVA_HOME" ]; then
  javaExecutable="$(which javac)"
  if [ -n "$javaExecutable" ] && ! [ "$(expr "$javaExecutable" : '\([^ ]*\)')" = "no" ]; then
    # readlink(1) is not available as standard on Solaris 10.
    readLink=$(which readlink)
    if [ ! "$(expr "$readLink" : '\([^ ]*\)')" = "no" ]; then
      if $darwin; then
        javaHome="$(dirname "$javaExecutable")"
        javaExecutable="$(cd "$javaHome" && pwd -P)/javac"
      else
        javaExecutable="$(readlink -f "$javaExecutable")"
      fi
      javaHome="$(dirname "$javaExecutable")"
      javaHome=$(expr "$javaHome" : '\(.*\)/bin')
      JAVA_HOME="$javaHome"
      export JAVA_HOME
    fi
  fi
fi

if [ -z "$JAVACMD" ]; then
  if [ -n "$JAVA_HOME" ]; then
    if [ -x "$JAVA_HOME/jre/sh/java" ]; then
      # IBM's JDK on AIX uses strange locations for the executables
      JAVACMD="$JAVA_HOME/jre/sh/java"
    else
      JAVACMD="$JAVA_HOME/bin/java"
    fi
  else
    JAVACMD="$(
      \unset -f command 2>/dev/null
      \command -v java
    )"
  fi
fi

if [ ! -x "$JAVACMD" ]; then
  echo "Error: JAVA_HOME is not defined correctly." >&2
  echo "  We cannot execute $JAVACMD" >&2
  exit 1
fi

if [ -z "$JAVA_HOME" ]; then
  echo "Warning: JAVA_HOME environment variable is not set." >&2
fi

# traverses directory structure from process work directory to filesystem root
# first directory with .mvn subdirectory is considered project base directory
find_maven_basedir() {
  if [ -z "$1" ]; then
    echo "Path not specified to find_maven_basedir" >&2
    return 1
  fi

  basedir="$1"
  wdir="$1"
  while [ "$wdir" != '/' ]; do
    if [ -d "$wdir"/.mvn ]; then
      basedir=$wdir
      break
    fi
    # workaround for JBEAP-8937 (on Solaris 10/Sparc)
    if [ -d "${wdir}" ]; then
      wdir=$(
        cd "$wdir/.." || exit 1
        pwd
      )
    fi
    # end of workaround
  done
  printf '%s' "$(
    cd "$basedir" || exit 1
    pwd
  )"
}

# concatenates all lines of a file
concat_lines() {
  if [ -f "$1" ]; then
    # Remove \r in case we run on Windows within Git Bash
    # and check out the repository with auto CRLF management
    # enabled. Otherwise, we may read lines that are delimited with
    # \r\n and produce $'-Xarg\r' rather than -Xarg due to word
    # splitting rules.
    tr -s '\r\n' ' ' <"$1"
  fi
}

log() {
  if [ "$MVNW_VERBOSE" = true ]; then
    printf '%s\n' "$1"
  fi
}

BASE_DIR=$(find_maven_basedir "$(dirname "$0")")
if [ -z "$BASE_DIR" ]; then
  exit 1
fi

MAVEN_PROJECTBASEDIR=${MAVEN_BASEDIR:-"$BASE_DIR"}
export MAVEN_PROJECTBASEDIR
log "$MAVEN_PROJECTBASEDIR"

trim() {
  # MWRAPPER-139:
  #   Trims trailing and leading whitespace, carriage returns, tabs, and linefeeds.
  #   Needed for removing poorly interpreted newline sequences when running in more
  #   exotic environments such as mingw bash on Windows.
  printf "%s" "${1}" | tr -d '[:space:]'
}

##########################################################################################
# Extension to allow automatically downloading the maven-wrapper.jar from Maven-central
# This allows using the maven wrapper in projects that prohibit checking in binary data.
##########################################################################################
wrapperJarPath="$MAVEN_PROJECTBASEDIR/.mvn/wrapper/maven-wrapper.jar"
if [ -r "$wrapperJarPath" ]; then
  log "Found $wrapperJarPath"
else
  log "Couldn't find $wrapperJarPath, downloading it ..."

  if [ -n "$MVNW_REPOURL" ]; then
    wrapperUrl="$MVNW_REPOURL/org/apache/maven/wrapper/maven-wrapper/3.3.4/maven-wrapper-3.3.4.jar"
  else
    wrapperUrl="https://repo.maven.apache.org/maven2/org/apache/maven/wrapper/maven-wrapper/3.3.4/maven-wrapper-3.3.4.jar"
  fi
  while IFS="=" read -r key value; do
    case "$key" in wrapperUrl)
      wrapperUrl=$(trim "${value-}")
      break
      ;;
    esac
  done <"$MAVEN_PROJECTBASEDIR/.mvn/wrapper/maven-wrapper.properties"
  log "Downloading from: $wrapperUrl"

  if $cygwin; then
    wrapperJarPath=$(cygpath --path --windows "$wrapperJarPath")
  fi

  if command -v wget >/dev/null; then
    log "Found wget ... using wget"
    [ "$MVNW_VERBOSE" = true ] && QUIET="" || QUIET="--quiet"
    if [ -z "$MVNW_USERNAME" ] || [ -z "$MVNW_PASSWORD" ]; then
      wget ${QUIET:+"$QUIET"} "$wrapperUrl" -O "$wrapperJarPath" || rm -f "$wrapperJarPath"
    else
      wget ${QUIET:+"$QUIET"} --http-user="$MVNW_USERNAME" --http-password="$MVNW_PASSWORD" "$wrapperUrl" -O "$wrapperJarPath" || rm -f "$wrapperJarPath"
    fi
  elif command -v curl >/dev/null; then
    log "Found curl ... using curl"
    [ "$MVNW_VERBOSE" = true ] && QUIET="" || QUIET="--silent"
    if [ -z "$MVNW_USERNAME" ] || [ -z "$MVNW_PASSWORD" ]; then
      curl ${QUIET:+"$QUIET"} -o "$wrapperJarPath" "$wrapperUrl" -f -L || rm -f "$wrapperJarPath"
    else
      curl ${QUIET:+"$QUIET"} --user "$MVNW_USERNAME:$MVNW_PASSWORD" -o "$wrapperJarPath" "$wrapperUrl" -f -L || rm -f "$wrapperJarPath"
    fi
  else
    log "Falling back to using Java to download"
    javaSource="$MAVEN_PROJECTBASEDIR/.mvn/wrapper/MavenWrapperDownloader.java"
    javaClass="$MAVEN_PROJECTBASEDIR/.mvn/wrapper/MavenWrapperDownloader.class"
    # For Cygwin, switch paths to Windows format before running javac
    if $cygwin; then
      javaSource=$(cygpath --path --windows "$javaSource")
      javaClass=$(cygpath --path --windows "$javaClass")
    fi
    if [ -e "$javaSource" ]; then
      if [ ! -e "$javaClass" ]; then
        log " - Compiling MavenWrapperDownloader.java ..."
        ("$JAVA_HOME/bin/javac" "$javaSource")
      fi
      if [ -e "$javaClass" ]; then
        log " - Running MavenWrapperDownloader.java ..."
        ("$JAVA_HOME/bin/java" -cp .mvn/wrapper MavenWrapperDownloader "$wrapperUrl" "$wrapperJarPath") || rm -f "$wrapperJarPath"
      fi
    fi
  fi
fi
##########################################################################################
# End of extension
##########################################################################################

# If specified, validate the SHA-256 sum of the Maven wrapper jar file
wrapperSha256Sum=""
while IFS="=" read -r key value; do
  case "$key" in wrapperSha256Sum)
    wrapperSha256Sum=$(trim "${value-}")
    break
    ;;
  esac
done <"$MAVEN_PROJECTBASEDIR/.mvn/wrapper/maven-wrapper.properties"
if [ -n "$wrapperSha256Sum" ]; then
  wrapperSha256Result=false
  if command -v sha256sum >/dev/null; then
    if echo "$wrapperSha256Sum  $wrapperJarPath" | sha256sum -c - >/dev/null 2>&1; then
      wrapperSha256Result=true
    fi
  elif command -v shasum >/dev/null; then
    if echo "$wrapperSha256Sum  $wrapperJarPath" | shasum -a 256 -c >/dev/null 2>&1; then
      wrapperSha256Result=true
    fi
  else
    echo "Checksum validation was requested but neither 'sha256sum' or 'shasum' are available." >&2
    echo "Please install either command, or disable validation by removing 'wrapperSha256Sum' from your maven-wrapper.properties." >&2
    exit 1
  fi
  if [ $wrapperSha256Result = false ]; then
    echo "Error: Failed to validate Maven wrapper SHA-256, your Maven wrapper might be compromised." >&2
    echo "Investigate or delete $wrapperJarPath to attempt a clean download." >&2
    echo "If you updated your Maven version, you need to update the specified wrapperSha256Sum property." >&2
    exit 1
  fi
fi

MAVEN_OPTS="$(concat_lines "$MAVEN_PROJECTBASEDIR/.mvn/jvm.config") $MAVEN_OPTS"

# For Cygwin, switch paths to Windows format before running java
if $cygwin; then
  [ -n "$JAVA_HOME" ] \
    && JAVA_HOME=$(cygpath --path --windows "$JAVA_HOME")
  [ -n "$CLASSPATH" ] \
    && CLASSPATH=$(cygpath --path --windows "$CLASSPATH")
  [ -n "$MAVEN_PROJECTBASEDIR" ] \
    && MAVEN_PROJECTBASEDIR=$(cygpath --path --windows "$MAVEN_PROJECTBASEDIR")
fi

# Provide a "standardized" way to retrieve the CLI args that will
# work with both Windows and non-Windows executions.
MAVEN_CMD_LINE_ARGS="$MAVEN_CONFIG $*"
export MAVEN_CMD_LINE_ARGS

WRAPPER_LAUNCHER=org.apache.maven.wrapper.MavenWrapperMain

# shellcheck disable=SC2086 # safe args
exec "$JAVACMD" \
  $MAVEN_OPTS \
  $MAVEN_DEBUG_OPTS \
  -classpath "$MAVEN_PROJECTBASEDIR/.mvn/wrapper/maven-wrapper.jar" \
  "-Dmaven.multiModuleProjectDirectory=${MAVEN_PROJECTBASEDIR}" \
  ${WRAPPER_LAUNCHER} $MAVEN_CONFIG "$@"
````

### `laboEstructurasLockFree/mvnw.cmd`

````text
@REM ----------------------------------------------------------------------------
@REM Licensed to the Apache Software Foundation (ASF) under one
@REM or more contributor license agreements.  See the NOTICE file
@REM distributed with this work for additional information
@REM regarding copyright ownership.  The ASF licenses this file
@REM to you under the Apache License, Version 2.0 (the
@REM "License"); you may not use this file except in compliance
@REM with the License.  You may obtain a copy of the License at
@REM
@REM    http://www.apache.org/licenses/LICENSE-2.0
@REM
@REM Unless required by applicable law or agreed to in writing,
@REM software distributed under the License is distributed on an
@REM "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
@REM KIND, either express or implied.  See the License for the
@REM specific language governing permissions and limitations
@REM under the License.
@REM ----------------------------------------------------------------------------

@REM ----------------------------------------------------------------------------
@REM Apache Maven Wrapper startup batch script, version 3.3.4
@REM
@REM Required ENV vars:
@REM JAVA_HOME - location of a JDK home dir
@REM
@REM Optional ENV vars
@REM MAVEN_BATCH_ECHO - set to 'on' to enable the echoing of the batch commands
@REM MAVEN_BATCH_PAUSE - set to 'on' to wait for a keystroke before ending
@REM MAVEN_OPTS - parameters passed to the Java VM when running Maven
@REM     e.g. to debug Maven itself, use
@REM set MAVEN_OPTS=-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=y,address=8000
@REM MAVEN_SKIP_RC - flag to disable loading of mavenrc files
@REM ----------------------------------------------------------------------------

@REM Begin all REM lines with '@' in case MAVEN_BATCH_ECHO is 'on'
@echo off
@REM set title of command window
title %0
@REM enable echoing by setting MAVEN_BATCH_ECHO to 'on'
@if "%MAVEN_BATCH_ECHO%" == "on"  echo %MAVEN_BATCH_ECHO%

@REM set %HOME% to equivalent of $HOME
if "%HOME%" == "" (set "HOME=%HOMEDRIVE%%HOMEPATH%")

@REM Execute a user defined script before this one
if not "%MAVEN_SKIP_RC%" == "" goto skipRcPre
@REM check for pre script, once with legacy .bat ending and once with .cmd ending
if exist "%USERPROFILE%\mavenrc_pre.bat" call "%USERPROFILE%\mavenrc_pre.bat" %*
if exist "%USERPROFILE%\mavenrc_pre.cmd" call "%USERPROFILE%\mavenrc_pre.cmd" %*
:skipRcPre

@setlocal

set ERROR_CODE=0

@REM To isolate internal variables from possible post scripts, we use another setlocal
@setlocal

@REM ==== START VALIDATION ====
if not "%JAVA_HOME%" == "" goto OkJHome

echo. >&2
echo Error: JAVA_HOME not found in your environment. >&2
echo Please set the JAVA_HOME variable in your environment to match the >&2
echo location of your Java installation. >&2
echo. >&2
goto error

:OkJHome
if exist "%JAVA_HOME%\bin\java.exe" goto init

echo. >&2
echo Error: JAVA_HOME is set to an invalid directory. >&2
echo JAVA_HOME = "%JAVA_HOME%" >&2
echo Please set the JAVA_HOME variable in your environment to match the >&2
echo location of your Java installation. >&2
echo. >&2
goto error

@REM ==== END VALIDATION ====

:init

@REM Find the project base dir, i.e. the directory that contains the folder ".mvn".
@REM Fallback to current working directory if not found.

set MAVEN_PROJECTBASEDIR=%MAVEN_BASEDIR%
IF NOT "%MAVEN_PROJECTBASEDIR%"=="" goto endDetectBaseDir

set EXEC_DIR=%CD%
set WDIR=%EXEC_DIR%
:findBaseDir
IF EXIST "%WDIR%"\.mvn goto baseDirFound
cd ..
IF "%WDIR%"=="%CD%" goto baseDirNotFound
set WDIR=%CD%
goto findBaseDir

:baseDirFound
set MAVEN_PROJECTBASEDIR=%WDIR%
cd "%EXEC_DIR%"
goto endDetectBaseDir

:baseDirNotFound
set MAVEN_PROJECTBASEDIR=%EXEC_DIR%
cd "%EXEC_DIR%"

:endDetectBaseDir

IF NOT EXIST "%MAVEN_PROJECTBASEDIR%\.mvn\jvm.config" goto endReadAdditionalConfig

@setlocal EnableExtensions EnableDelayedExpansion
for /F "usebackq delims=" %%a in ("%MAVEN_PROJECTBASEDIR%\.mvn\jvm.config") do set JVM_CONFIG_MAVEN_PROPS=!JVM_CONFIG_MAVEN_PROPS! %%a
@endlocal & set JVM_CONFIG_MAVEN_PROPS=%JVM_CONFIG_MAVEN_PROPS%

:endReadAdditionalConfig

SET MAVEN_JAVA_EXE="%JAVA_HOME%\bin\java.exe"
set WRAPPER_JAR="%MAVEN_PROJECTBASEDIR%\.mvn\wrapper\maven-wrapper.jar"
set WRAPPER_LAUNCHER=org.apache.maven.wrapper.MavenWrapperMain

set WRAPPER_URL="https://repo.maven.apache.org/maven2/org/apache/maven/wrapper/maven-wrapper/3.3.4/maven-wrapper-3.3.4.jar"

FOR /F "usebackq tokens=1,2 delims==" %%A IN ("%MAVEN_PROJECTBASEDIR%\.mvn\wrapper\maven-wrapper.properties") DO (
    IF "%%A"=="wrapperUrl" SET WRAPPER_URL=%%B
)

@REM Extension to allow automatically downloading the maven-wrapper.jar from Maven-central
@REM This allows using the maven wrapper in projects that prohibit checking in binary data.
if exist %WRAPPER_JAR% (
    if "%MVNW_VERBOSE%" == "true" (
        echo Found %WRAPPER_JAR%
    )
) else (
    if not "%MVNW_REPOURL%" == "" (
        SET WRAPPER_URL="%MVNW_REPOURL%/org/apache/maven/wrapper/maven-wrapper/3.3.4/maven-wrapper-3.3.4.jar"
    )
    if "%MVNW_VERBOSE%" == "true" (
        echo Couldn't find %WRAPPER_JAR%, downloading it ...
        echo Downloading from: %WRAPPER_URL%
    )

    powershell -Command "&{"^
		"$webclient = new-object System.Net.WebClient;"^
		"if (-not ([string]::IsNullOrEmpty('%MVNW_USERNAME%') -and [string]::IsNullOrEmpty('%MVNW_PASSWORD%'))) {"^
		"$webclient.Credentials = new-object System.Net.NetworkCredential('%MVNW_USERNAME%', '%MVNW_PASSWORD%');"^
		"}"^
		"[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; $webclient.DownloadFile('%WRAPPER_URL%', '%WRAPPER_JAR%')"^
		"}"
    if "%MVNW_VERBOSE%" == "true" (
        echo Finished downloading %WRAPPER_JAR%
    )
)
@REM End of extension

@REM If specified, validate the SHA-256 sum of the Maven wrapper jar file
SET WRAPPER_SHA_256_SUM=""
FOR /F "usebackq tokens=1,2 delims==" %%A IN ("%MAVEN_PROJECTBASEDIR%\.mvn\wrapper\maven-wrapper.properties") DO (
    IF "%%A"=="wrapperSha256Sum" SET WRAPPER_SHA_256_SUM=%%B
)
IF NOT %WRAPPER_SHA_256_SUM%=="" (
    powershell -Command "&{"^
       "Import-Module $PSHOME\Modules\Microsoft.PowerShell.Utility -Function Get-FileHash;"^
       "$hash = (Get-FileHash \"%WRAPPER_JAR%\" -Algorithm SHA256).Hash.ToLower();"^
       "If('%WRAPPER_SHA_256_SUM%' -ne $hash){"^
       "  Write-Error 'Error: Failed to validate Maven wrapper SHA-256, your Maven wrapper might be compromised.';"^
       "  Write-Error 'Investigate or delete %WRAPPER_JAR% to attempt a clean download.';"^
       "  Write-Error 'If you updated your Maven version, you need to update the specified wrapperSha256Sum property.';"^
       "  exit 1;"^
       "}"^
       "}"
    if ERRORLEVEL 1 goto error
)

@REM Provide a "standardized" way to retrieve the CLI args that will
@REM work with both Windows and non-Windows executions.
set MAVEN_CMD_LINE_ARGS=%*

%MAVEN_JAVA_EXE% ^
  %JVM_CONFIG_MAVEN_PROPS% ^
  %MAVEN_OPTS% ^
  %MAVEN_DEBUG_OPTS% ^
  -classpath %WRAPPER_JAR% ^
  "-Dmaven.multiModuleProjectDirectory=%MAVEN_PROJECTBASEDIR%" ^
  %WRAPPER_LAUNCHER% %MAVEN_CONFIG% %*
if ERRORLEVEL 1 goto error
goto end

:error
set ERROR_CODE=1

:end
@endlocal & set ERROR_CODE=%ERROR_CODE%

if not "%MAVEN_SKIP_RC%"=="" goto skipRcPost
@REM check for post script, once with legacy .bat ending and once with .cmd ending
if exist "%USERPROFILE%\mavenrc_post.bat" call "%USERPROFILE%\mavenrc_post.bat"
if exist "%USERPROFILE%\mavenrc_post.cmd" call "%USERPROFILE%\mavenrc_post.cmd"
:skipRcPost

@REM pause the script if MAVEN_BATCH_PAUSE is set to 'on'
if "%MAVEN_BATCH_PAUSE%"=="on" pause

if "%MAVEN_TERMINATE_CMD%"=="on" exit %ERROR_CODE%

cmd /C exit /B %ERROR_CODE%
````

### `laboEstructurasLockFree/pom.xml`

````xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.pcp.exactas</groupId>
    <artifactId>laboEstructurasLockFree</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.release>21</maven.compiler.release>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <junit.version>5.11.4</junit.version>
        <jmh.version>1.37</jmh.version>
        <stress.enabled>false</stress.enabled>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>${junit.version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.5.2</version>
                <configuration>
                    <systemPropertyVariables>
                        <stress.enabled>${stress.enabled}</stress.enabled>
                    </systemPropertyVariables>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <profiles>
        <profile>
            <id>stress</id>
            <properties>
                <stress.enabled>true</stress.enabled>
            </properties>
        </profile>
        <profile>
            <id>jmh</id>
            <dependencies>
                <dependency>
                    <groupId>org.openjdk.jmh</groupId>
                    <artifactId>jmh-core</artifactId>
                    <version>${jmh.version}</version>
                </dependency>
                <dependency>
                    <groupId>org.openjdk.jmh</groupId>
                    <artifactId>jmh-generator-annprocess</artifactId>
                    <version>${jmh.version}</version>
                </dependency>
            </dependencies>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.codehaus.mojo</groupId>
                        <artifactId>build-helper-maven-plugin</artifactId>
                        <version>3.6.0</version>
                        <executions>
                            <execution>
                                <id>add-jmh-sources</id>
                                <phase>generate-sources</phase>
                                <goals><goal>add-source</goal></goals>
                                <configuration><sources><source>src/jmh/java</source></sources></configuration>
                            </execution>
                        </executions>
                    </plugin>
                    <plugin>
                        <artifactId>maven-compiler-plugin</artifactId>
                        <version>3.13.0</version>
                        <configuration>
                            <annotationProcessorPaths>
                                <path>
                                    <groupId>org.openjdk.jmh</groupId>
                                    <artifactId>jmh-generator-annprocess</artifactId>
                                    <version>${jmh.version}</version>
                                </path>
                            </annotationProcessorPaths>
                        </configuration>
                    </plugin>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-shade-plugin</artifactId>
                        <version>3.5.3</version>
                        <executions>
                            <execution>
                                <phase>package</phase>
                                <goals><goal>shade</goal></goals>
                                <configuration>
                                    <createDependencyReducedPom>false</createDependencyReducedPom>
                                    <transformers>
                                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                            <mainClass>org.openjdk.jmh.Main</mainClass>
                                        </transformer>
                                    </transformers>
                                </configuration>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
        </profile>
    </profiles>

</project>
````

### `laboEstructurasLockFree/src/jmh/java/org/pcp/exactas/set/SetBenchmarks.java`

````java
package org.pcp.exactas.set;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.ThreadLocalRandom;
import org.openjdk.jmh.annotations.Benchmark;
import org.openjdk.jmh.annotations.BenchmarkMode;
import org.openjdk.jmh.annotations.Level;
import org.openjdk.jmh.annotations.Measurement;
import org.openjdk.jmh.annotations.Mode;
import org.openjdk.jmh.annotations.OutputTimeUnit;
import org.openjdk.jmh.annotations.Param;
import org.openjdk.jmh.annotations.Scope;
import org.openjdk.jmh.annotations.Setup;
import org.openjdk.jmh.annotations.State;
import org.openjdk.jmh.annotations.Threads;
import org.openjdk.jmh.annotations.Warmup;

/** JMH workloads for comparing costs and guarantees, not proving progress properties. */
@BenchmarkMode(Mode.Throughput)
@OutputTimeUnit(TimeUnit.SECONDS)
@Warmup(iterations = 2, time = 1)
@Measurement(iterations = 3, time = 1)
public class SetBenchmarks {
    @Benchmark
    @Threads(1)
    public boolean singleThreadBaseline(SingleThreadState state) {
        return state.perform();
    }

    @Benchmark
    public boolean concurrentWorkload(ConcurrentState state) {
        return state.perform();
    }

    @State(Scope.Benchmark)
    public static class SingleThreadState extends BaseState {
        @Param({"SEQUENTIAL", "COARSE", "FINE", "OPTIMISTIC", "LAZY", "LOCK_FREE"})
        public Implementation implementation;

        @Setup(Level.Iteration) public void setup() { initialize(implementation, Workload.READ_HEAVY); }
    }

    @State(Scope.Benchmark)
    public static class ConcurrentState extends BaseState {
        @Param({"COARSE", "FINE", "OPTIMISTIC", "LAZY", "LOCK_FREE"})
        public Implementation implementation;
        @Param({"READ_ONLY", "READ_HEAVY", "MIXED_LOW_CONFLICT", "HOTSPOT", "UPDATE_HEAVY"})
        public Workload workload;

        @Setup(Level.Iteration) public void setup() { initialize(implementation, workload); }
    }

    @State(Scope.Benchmark)
    public abstract static class BaseState {
        @Param({"256", "1024"}) public int listLength;
        private ConcurrentIntSet set;
        private Workload workload;
        private int domain;

        final void initialize(Implementation implementation, Workload workload) {
            this.set = implementation.create();
            this.workload = workload;
            this.domain = listLength * 2;
            for (int key = 0; key < domain; key += 2) set.add(key);
        }

        final boolean perform() {
            int ticket = ThreadLocalRandom.current().nextInt(100);
            int key = chooseKey();
            if (ticket < workload.containsPercent) return set.contains(key);
            if (ticket < workload.containsPercent + workload.addPercent) return set.add(key);
            return set.remove(key);
        }

        private int chooseKey() {
            if (workload == Workload.HOTSPOT && ThreadLocalRandom.current().nextInt(100) < 80) {
                int middle = domain / 2;
                return middle - 16 + ThreadLocalRandom.current().nextInt(32);
            }
            return ThreadLocalRandom.current().nextInt(domain);
        }
    }

    public enum Implementation {
        SEQUENTIAL { ConcurrentIntSet create() { return new SequentialListSet(); } },
        COARSE { ConcurrentIntSet create() { return new CoarseListSet(); } },
        FINE { ConcurrentIntSet create() { return new FineListSet(); } },
        OPTIMISTIC { ConcurrentIntSet create() { return new OptimisticListSet(); } },
        LAZY { ConcurrentIntSet create() { return new LazyListSet(); } },
        LOCK_FREE { ConcurrentIntSet create() { return new LockFreeListSet(); } };
        abstract ConcurrentIntSet create();
    }

    public enum Workload {
        READ_ONLY(100, 0, 0), READ_HEAVY(90, 5, 5), MIXED_LOW_CONFLICT(50, 25, 25),
        HOTSPOT(50, 25, 25), UPDATE_HEAVY(10, 45, 45);
        final int containsPercent, addPercent, removePercent;
        Workload(int containsPercent, int addPercent, int removePercent) {
            this.containsPercent = containsPercent; this.addPercent = addPercent; this.removePercent = removePercent;
        }
    }
}
````

### `laboEstructurasLockFree/src/main/java/org/pcp/exactas/set/CoarseListSet.java`

````java
package org.pcp.exactas.set;

import java.util.concurrent.locks.ReentrantLock;

public class CoarseListSet implements ConcurrentIntSet {
    private final ReentrantLock lock = new ReentrantLock();
    private final Node head = new Node(Long.MIN_VALUE);
    private final Node tail = new Node(Long.MAX_VALUE);

    public CoarseListSet() { head.next = tail; }

    @Override public boolean add(int value) {
        return false;
    }

    @Override public boolean remove(int value) {
        return false;
    }

    @Override public boolean contains(int value) {
        return false;
    }

    @Override public boolean isImplemented() {
        return false;
    }

    private static final class Node {
        final long key;
        Node next;
        Node(long key) { this(key, null); }
        Node(long key, Node next) { this.key=key; this.next=next; } }
}
````

### `laboEstructurasLockFree/src/main/java/org/pcp/exactas/set/ConcurrentIntSet.java`

````java
package org.pcp.exactas.set;

public interface ConcurrentIntSet {
    boolean add(int value);

    boolean remove(int value);

    boolean contains(int value);

    default boolean isImplemented() {
        return false;
    }
}
````

### `laboEstructurasLockFree/src/main/java/org/pcp/exactas/set/FineListSet.java`

````java
package org.pcp.exactas.set;

import java.util.concurrent.locks.ReentrantLock;

public class FineListSet implements ConcurrentIntSet {
    private final Node head = new Node(Long.MIN_VALUE);
    private final Node tail = new Node(Long.MAX_VALUE);

    public FineListSet() { head.next = tail; }

    @Override public boolean add(int value) {
        return false;
    }

    @Override public boolean remove(int value) {
        return false;
    }

    @Override public boolean contains(int value) {
        return false;
    }

    @Override public boolean isImplemented() {
        return false;
    }

    private static final class Node {
        final long key;
        Node next;
        final ReentrantLock lock = new ReentrantLock();
        Node(long key) { this(key, null); } Node(long key, Node next) { this.key=key; this.next=next; }
    }
}
````

### `laboEstructurasLockFree/src/main/java/org/pcp/exactas/set/LazyListSet.java`

````java
package org.pcp.exactas.set;

import java.util.concurrent.locks.ReentrantLock;

public class LazyListSet implements ConcurrentIntSet {
    private final Node head = new Node(Long.MIN_VALUE);
    private final Node tail = new Node(Long.MAX_VALUE);

    public LazyListSet() { head.next = tail; }

    @Override public boolean add(int value) {
        while (true) {
            Node pred = head, curr = head.next;
            // TODO
            return false;
        }
    }

    @Override public boolean remove(int value) {
        while (true) {
            Node pred = head, curr = head.next;
            // TODO
            return false;
        }
    }

    @Override public boolean contains(int value) {
        // TODO
        return false;
    }

    private static boolean edgeExists(Node pred, Node curr) {
        // TODO
        return false;
    }

    @Override public boolean isImplemented() {
        return false;
    }

    private static final class Node {
        final long key;
        volatile Node next;
        volatile boolean marked;
        final ReentrantLock lock = new ReentrantLock();
        Node(long key) { this(key, null); } Node(long key, Node next) { this.key=key; this.next=next; }
    }
}
````

### `laboEstructurasLockFree/src/main/java/org/pcp/exactas/set/LockFreeListSet.java`

````java
package org.pcp.exactas.set;

import java.util.concurrent.atomic.AtomicMarkableReference;

public class LockFreeListSet implements ConcurrentIntSet {
    private final Node head = new Node(Long.MIN_VALUE, null);
    private final Node tail = new Node(Long.MAX_VALUE, null);

    public LockFreeListSet() { head.next.set(tail, false); }

    @Override public boolean add(int value) {
        while (true) {
            Window window = find(value);
            Node pred = window.pred;
            Node curr = window.curr;
            // TODO
            return false;
        }
    }

    @Override public boolean remove(int value) {
        while (true) {
            Window window = find(value);
            Node pred = window.pred;
            Node curr = window.curr;
            // TODO
            return false;
        }
    }

    @Override public boolean contains(int value) {
        // TODO
        return false;
    }

    @Override public boolean isImplemented() { return false; }

    private Window find(int value) {
        boolean[] marked = {false};
        retry:
        while (true) {
            Node pred = head;
            Node curr = pred.next.getReference();
            while (true) {
                Node succ = curr.next.get(marked);
                // Si curr->next está marcado, significa que debo eliminar curr de la lista
                while (marked[0]) {
                    if (!pred.next.compareAndSet(curr, succ, false, false))
                        continue retry;
                    curr = succ;
                    succ = curr.next.get(marked);
                }
                if (curr.key >= value) return new Window(pred, curr);
                pred = curr;
                curr = succ;
            }
        }
    }

    static final class Window {
        final Node pred;
        final Node curr;
        Window(Node pred, Node curr) { this.pred = pred; this.curr = curr; }
    }

    static final class Node {
        final long key;
        final AtomicMarkableReference<Node> next;
        Node(long key, Node next) { this.key = key; this.next = new AtomicMarkableReference<>(next, false); }
    }
}
````

### `laboEstructurasLockFree/src/main/java/org/pcp/exactas/set/OptimisticListSet.java`

````java
package org.pcp.exactas.set;

import java.util.concurrent.locks.ReentrantLock;

public class OptimisticListSet implements ConcurrentIntSet {
    private final Node head = new Node(Long.MIN_VALUE);
    private final Node tail = new Node(Long.MAX_VALUE);

    public OptimisticListSet() { head.next = tail; }

    @Override public boolean add(int value) {
        while (true) {
            Node pred = head, curr = head.next;
            // TODO
            return false;
        }
    }

    @Override public boolean remove(int value) {
        while (true) {
            Node pred = head, curr = head.next;
            // TODO
            return false;
        }
    }

    @Override public boolean contains(int value) {
        while (true) {
            Node pred = head, curr = head.next;
            // TODO
            return false;
        }
    }

    @Override public boolean isImplemented() {
        return false;
    }

    // Dadas referencias a dos nodos pred --> curr, retorna true si sigue existiendo la relación pred --> curr en la lista
    private boolean edgeExists(Node pred, Node curr) {
        Node node = head;
        while (node.key <= pred.key) {
            if (node == pred)
                return pred.next == curr;
            node = node.next;
        }
        return false;
    }

    private static final class Node {
        final long key;
        volatile Node next;
        final ReentrantLock lock = new ReentrantLock();
        Node(long key) { this(key, null); }
        Node(long key, Node next) { this.key=key; this.next=next; }
    }
}
````

### `laboEstructurasLockFree/src/main/java/org/pcp/exactas/set/SequentialListSet.java`

````java
package org.pcp.exactas.set;

// La lista enlazada de toda la vida, muy insegura cuando es usada por más de un thread
public class SequentialListSet implements ConcurrentIntSet {
    private final Node head = new Node(Long.MIN_VALUE);
    private final Node tail = new Node(Long.MAX_VALUE);

    public SequentialListSet() {
        head.next = tail;
    }

    @Override
    public boolean add(int value) {
        Node pred = head;
        Node curr = head.next;
        while (curr.key < value) {
            pred = curr;
            curr = curr.next;
        }
        if (curr.key == value) {
            return false;
        }
        pred.next = new Node(value, curr);
        return true;
    }

    @Override
    public boolean remove(int value) {
        Node pred = head;
        Node curr = head.next;
        while (curr.key < value) {
            pred = curr;
            curr = curr.next;
        }
        if (curr.key != value) {
            return false;
        }
        pred.next = curr.next;
        return true;
    }

    @Override
    public boolean contains(int value) {
        Node curr = head.next;
        while (curr.key < value) {
            curr = curr.next;
        }
        return curr.key == value;
    }

    @Override
    public boolean isImplemented() { return false; }

    private static final class Node {
        final long key;
        Node next;

        Node(long key) { this(key, null); }
        Node(long key, Node next) { this.key = key; this.next = next; }
    }
}
````

### `laboEstructurasLockFree/src/main/java/org/pcp/exactas/set/SetPerformanceBenchmark.java`

````java
package org.pcp.exactas.set;

import java.io.BufferedWriter;
import java.io.PrintWriter;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.Arrays;
import java.util.Locale;
import java.util.SplittableRandom;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.atomic.AtomicReference;
import java.util.function.Supplier;

public final class SetPerformanceBenchmark {

    // CONFIGURACIÓN DEL EXPERIMENTO

    private static final int[] THREAD_COUNTS = {16};
    private static final int KEY_SPACE = 1_024;
    private static final KeyDistribution KEY_DISTRIBUTION = KeyDistribution.CLUSTERED;
    private static final double CLUSTERED_ACCESS_PROBABILITY = 0.95;
    private static final int CLUSTERED_REGION_SIZE = 8;
    private static final int WARMUP_REPETITIONS = 1; // Ayuda a elimiar ruido del JIT y otras caches internas de la JVM
    private static final int MEASURED_REPETITIONS = 6;
    private static final int OPERATIONS_PER_THREAD = 5_000;
    private static final long BASE_SEED = 0x5eed;
    private static final String CSV_FILE = "set-performance-results.csv";

    private SetPerformanceBenchmark() {
    }

    enum KeyDistribution {
        UNIFORM,
        CLUSTERED
    }

    enum Workload {
        READ_ONLY(1.00, 0.00, 0.00),
        READ_HEAVY(0.90, 0.05, 0.05),
        BALANCED(0.50, 0.25, 0.25),
        WRITE_HEAVY(0.10, 0.45, 0.45),
        ALMOST_ALL_WRITES(0.02, 0.49, 0.49);

        final double containsProbability;
        final double addProbability;
        final double removeProbability;

        Workload(double containsProbability, double addProbability, double removeProbability) {
            this.containsProbability = containsProbability;
            this.addProbability = addProbability;
            this.removeProbability = removeProbability;
        }
    }

    enum Operation {
        CONTAINS("contains"), ADD("add"), REMOVE("remove");

        final String csvName;

        Operation(String csvName) {
            this.csvName = csvName;
        }
    }

    enum Implementation {
        SEQUENTIAL("Sequential", "SequentialListSet", SequentialListSet::new, false),
        COARSE("Coarse", "CoarseListSet", CoarseListSet::new, true),
        FINE("Fine", "FineListSet", FineListSet::new, true),
        OPTIMISTIC("Optimistic", "OptimisticListSet", OptimisticListSet::new, true),
        LAZY("Lazy", "LazyListSet", LazyListSet::new, true),
        LOCK_FREE("LockFree", "LockFreeListSet", LockFreeListSet::new, true);

        final String displayName;
        final String csvName;
        final Supplier<ConcurrentIntSet> factory;
        final boolean concurrent;

        Implementation(String displayName, String csvName, Supplier<ConcurrentIntSet> factory,
                       boolean concurrent) {
            this.displayName = displayName;
            this.csvName = csvName;
            this.factory = factory;
            this.concurrent = concurrent;
        }
    }

    record OperationStats(Operation operation, int samples, double successRate, double meanMicros,
                          double stddevMicros, double p90Micros, double p95Micros,
                          double p99Micros) {
    }

    record BenchmarkResult(Implementation implementation, Workload workload, int threadCount,
                           OperationStats contains, OperationStats add, OperationStats remove,
                           double meanThroughput) {
    }

    record RepetitionResult(long wallElapsedNanos, WorkerSamples[] samples) {
    }

    /** Each worker writes only to its own arrays; no measurement bookkeeping is shared. */
    static final class WorkerSamples {
        final long[] containsLatencies = new long[OPERATIONS_PER_THREAD];
        final long[] addLatencies = new long[OPERATIONS_PER_THREAD];
        final long[] removeLatencies = new long[OPERATIONS_PER_THREAD];
        int containsCount;
        int addCount;
        int removeCount;
        long containsTrue;
        long addTrue;
        long removeTrue;

        void record(Operation operation, long elapsedNanos, boolean result) {
            switch (operation) {
                case CONTAINS -> {
                    containsLatencies[containsCount++] = elapsedNanos;
                    if (result) containsTrue++;
                }
                case ADD -> {
                    addLatencies[addCount++] = elapsedNanos;
                    if (result) addTrue++;
                }
                case REMOVE -> {
                    removeLatencies[removeCount++] = elapsedNanos;
                    if (result) removeTrue++;
                }
            }
        }
    }

    /** A tiny primitive-only buffer used only while one configuration is aggregated. */
    static final class LongSamples {
        private long[] values = new long[16];
        private int size;

        void addAll(long[] source, int count) {
            ensureCapacity(size + count);
            System.arraycopy(source, 0, values, size, count);
            size += count;
        }

        int size() {
            return size;
        }

        long[] sortedCopy() {
            long[] copy = Arrays.copyOf(values, size);
            Arrays.sort(copy);
            return copy;
        }

        private void ensureCapacity(int required) {
            if (required <= values.length) return;
            int newCapacity = Math.max(required, values.length * 2);
            values = Arrays.copyOf(values, newCapacity);
        }
    }

    public static void main(String[] args) throws Exception {
        validateConfiguration();
        printStartup();
        Path csvPath = Path.of(CSV_FILE);

        try (BufferedWriter bufferedWriter = Files.newBufferedWriter(csvPath, StandardCharsets.UTF_8);
             PrintWriter csv = new PrintWriter(bufferedWriter)) {
            csv.println("implementation,workload,key_distribution,threads,operation,samples,success_rate,mean_us,"
                    + "stddev_us,p90_us,p95_us,p99_us,throughput_ops_s");

            for (Workload workload : Workload.values()) {
                for (int threadCount : THREAD_COUNTS) {
                    BenchmarkResult[] results = new BenchmarkResult[Implementation.values().length];
                    for (Implementation implementation : Implementation.values()) {
                        if (!implementation.concurrent && threadCount > 1) continue;
                        BenchmarkResult result = runConfiguration(implementation, workload, threadCount);
                        results[implementation.ordinal()] = result;
                        writeCsvRows(csv, result);
                        csv.flush();
                    }
                    printSummary(workload, threadCount, results);
                }
            }
        }

        System.out.println("\nCSV results written to " + csvPath.toAbsolutePath());
    }

    private static void printStartup() {
        System.out.println("Concurrent Set Benchmark");
        System.out.println("Available processors: " + Runtime.getRuntime().availableProcessors());
        System.out.println("Java version: " + System.getProperty("java.version"));
        System.out.println("OS: " + System.getProperty("os.name"));
        System.out.println("Warmup repetitions: " + WARMUP_REPETITIONS);
        System.out.println("Measured repetitions: " + MEASURED_REPETITIONS);
        System.out.println("Operations/thread: " + OPERATIONS_PER_THREAD);
        System.out.println("Key space: " + KEY_SPACE);
        System.out.println("Key distribution: " + KEY_DISTRIBUTION);
        if (KEY_DISTRIBUTION == KeyDistribution.CLUSTERED) {
            int firstClusteredKey = clusteredRegionStart();
            int lastClusteredKey = firstClusteredKey + CLUSTERED_REGION_SIZE - 1;
            System.out.printf(Locale.ROOT, "Clustered keys: %.0f%% in %d..%d%n",
                    CLUSTERED_ACCESS_PROBABILITY * 100, firstClusteredKey, lastClusteredKey);
        }
    }

    private static BenchmarkResult runConfiguration(Implementation implementation, Workload workload,
                                                    int threadCount) throws InterruptedException {
        System.out.printf("%nRunning %s / %d threads / %s ...%n",
                workload, threadCount, implementation.csvName);

        for (int repetition = 0; repetition < WARMUP_REPETITIONS; repetition++) {
            System.out.printf("  warmup %d/%d%n", repetition + 1, WARMUP_REPETITIONS);
            runRepetition(implementation, workload, threadCount, repetition, true, false);
        }

        LongSamples contains = new LongSamples();
        LongSamples add = new LongSamples();
        LongSamples remove = new LongSamples();
        long containsTrue = 0;
        long addTrue = 0;
        long removeTrue = 0;
        double[] throughputs = new double[MEASURED_REPETITIONS];

        for (int repetition = 0; repetition < MEASURED_REPETITIONS; repetition++) {
            System.out.printf("  repetition %d/%d%n", repetition + 1, MEASURED_REPETITIONS);
            RepetitionResult repetitionResult = runRepetition(
                    implementation, workload, threadCount, repetition, false, true);
            throughputs[repetition] = operationsPerRepetition(threadCount)
                    / (repetitionResult.wallElapsedNanos / 1_000_000_000.0);

            for (WorkerSamples samples : repetitionResult.samples) {
                contains.addAll(samples.containsLatencies, samples.containsCount);
                add.addAll(samples.addLatencies, samples.addCount);
                remove.addAll(samples.removeLatencies, samples.removeCount);
                containsTrue += samples.containsTrue;
                addTrue += samples.addTrue;
                removeTrue += samples.removeTrue;
            }
        }

        return new BenchmarkResult(implementation, workload, threadCount,
                computeStats(Operation.CONTAINS, contains, containsTrue),
                computeStats(Operation.ADD, add, addTrue),
                computeStats(Operation.REMOVE, remove, removeTrue), mean(throughputs));
    }

    private static RepetitionResult runRepetition(Implementation implementation, Workload workload,
                                                  int threadCount, int repetition, boolean warmup,
                                                  boolean collectSamples) throws InterruptedException {
        ConcurrentIntSet set = implementation.factory.get();
        prepopulate(set);

        CountDownLatch ready = new CountDownLatch(threadCount);
        CountDownLatch start = new CountDownLatch(1);
        CountDownLatch done = new CountDownLatch(threadCount);
        AtomicReference<Throwable> failure = new AtomicReference<>();
        WorkerSamples[] samples = collectSamples ? new WorkerSamples[threadCount] : null;

        for (int threadId = 0; threadId < threadCount; threadId++) {
            int workerId = threadId;
            WorkerSamples workerSamples = collectSamples ? new WorkerSamples() : null;
            if (collectSamples) samples[workerId] = workerSamples;
            Thread worker = Thread.ofPlatform().name("set-benchmark-" + workerId).unstarted(() -> {
                ready.countDown();
                try {
                    start.await();
                    workerLoop(set, workload, seedFor(workload, threadCount, warmup, repetition, workerId),
                            workerSamples);
                } catch (Throwable throwable) {
                    failure.compareAndSet(null, throwable);
                } finally {
                    done.countDown();
                }
            });
            worker.start();
        }

        ready.await();
        long wallStart = System.nanoTime();
        start.countDown();
        done.await();
        long wallElapsedNanos = System.nanoTime() - wallStart;

        Throwable workerFailure = failure.get();
        if (workerFailure != null) {
            throw new IllegalStateException("Worker failed for " + implementation.csvName + " / "
                    + workload + " / " + threadCount + " threads", workerFailure);
        }
        return new RepetitionResult(wallElapsedNanos, samples);
    }

    private static void workerLoop(ConcurrentIntSet set, Workload workload, long seed,
                                   WorkerSamples samples) {
        SplittableRandom random = new SplittableRandom(seed);
        for (int i = 0; i < OPERATIONS_PER_THREAD; i++) {
            // RNG cost is intentionally outside the timed set operation.
            Operation operation = chooseOperation(random, workload);
            int key = chooseKey(random);

            long start = System.nanoTime();
            boolean result = switch (operation) {
                case CONTAINS -> set.contains(key);
                case ADD -> set.add(key);
                case REMOVE -> set.remove(key);
            };
            long elapsedNanos = System.nanoTime() - start;

            if (samples != null) samples.record(operation, elapsedNanos, result);
        }
    }

    private static Operation chooseOperation(SplittableRandom random, Workload workload) {
        double probability = random.nextDouble();
        if (probability < workload.containsProbability) return Operation.CONTAINS;
        if (probability < workload.containsProbability + workload.addProbability) return Operation.ADD;
        return Operation.REMOVE;
    }

    private static int chooseKey(SplittableRandom random) {
        if (KEY_DISTRIBUTION == KeyDistribution.CLUSTERED
                && random.nextDouble() < CLUSTERED_ACCESS_PROBABILITY) {
            return clusteredRegionStart() + random.nextInt(CLUSTERED_REGION_SIZE);
        }
        return random.nextInt(KEY_SPACE);
    }

    private static int clusteredRegionStart() {
        return (KEY_SPACE - CLUSTERED_REGION_SIZE) / 2;
    }

    private static void validateConfiguration() {
        if (KEY_SPACE <= 0) {
            throw new IllegalStateException("KEY_SPACE must be positive");
        }
        if (CLUSTERED_REGION_SIZE <= 0 || CLUSTERED_REGION_SIZE > KEY_SPACE) {
            throw new IllegalStateException("CLUSTERED_REGION_SIZE must be between 1 and KEY_SPACE");
        }
        if (CLUSTERED_ACCESS_PROBABILITY < 0.0 || CLUSTERED_ACCESS_PROBABILITY > 1.0) {
            throw new IllegalStateException("CLUSTERED_ACCESS_PROBABILITY must be between 0.0 and 1.0");
        }
    }

    private static long seedFor(Workload workload, int threadCount, boolean warmup, int repetition,
                                int threadId) {
        // No implementation identifier appears here: each implementation sees equivalent streams.
        return BASE_SEED
                + workload.ordinal() * 1_000_000_000L
                + threadCount * 10_000_000L
                + (warmup ? 1_000_000L : 0)
                + repetition * 100_000L
                + threadId;
    }

    private static void prepopulate(ConcurrentIntSet set) {
        for (int key = 0; key < KEY_SPACE; key += 2) set.add(key);
        if (!set.contains(0) || set.contains(1)) {
            throw new IllegalStateException("Prepopulation did not create the expected even-key set");
        }
    }

    private static long operationsPerRepetition(int threadCount) {
        return (long) threadCount * OPERATIONS_PER_THREAD;
    }

    private static OperationStats computeStats(Operation operation, LongSamples samples, long trueCount) {
        if (samples.size() == 0) return null;
        double meanNanos = mean(samples.values, samples.size());
        double variance = 0.0;
        for (int i = 0; i < samples.size(); i++) {
            double difference = samples.values[i] - meanNanos;
            variance += difference * difference;
        }
        double stddevNanos = Math.sqrt(variance / samples.size());
        long[] sorted = samples.sortedCopy();
        return new OperationStats(operation, samples.size(), trueCount / (double) samples.size(),
                meanNanos / 1_000.0, stddevNanos / 1_000.0,
                percentile(sorted, 0.90) / 1_000.0,
                percentile(sorted, 0.95) / 1_000.0,
                percentile(sorted, 0.99) / 1_000.0);
    }

    private static double mean(long[] values, int count) {
        double sum = 0.0;
        for (int i = 0; i < count; i++) sum += values[i];
        return sum / count;
    }

    private static double mean(double[] values) {
        double sum = 0.0;
        for (double value : values) sum += value;
        return sum / values.length;
    }

    private static long percentile(long[] sorted, double percentile) {
        int index = (int) Math.ceil(percentile * sorted.length) - 1;
        index = Math.max(0, Math.min(index, sorted.length - 1));
        return sorted[index];
    }

    private static void printSummary(Workload workload, int threadCount, BenchmarkResult[] results) {
        System.out.printf("%n%s / %s - %d threads%n", KEY_DISTRIBUTION, workload, threadCount);
        System.out.printf(Locale.ROOT, "contains=%.0f%%, add=%.0f%%, remove=%.0f%%%n",
                workload.containsProbability * 100, workload.addProbability * 100,
                workload.removeProbability * 100);
        System.out.println("Implementation  Operation  Samples    Success%    Mean us    Std us    P90 us    P95 us    P99 us");
        System.out.println("----------------------------------------------------------------------------------------------------");
        for (BenchmarkResult result : results) {
            if (result == null) continue;
            printStatsRow(result.implementation.displayName, result.contains);
            printStatsRow(result.implementation.displayName, result.add);
            printStatsRow(result.implementation.displayName, result.remove);
        }

        System.out.println("\nImplementation  Throughput ops/s");
        System.out.println("---------------------------------");
        for (BenchmarkResult result : results) {
            if (result != null) {
                System.out.printf(Locale.ROOT, "%-15s %18.0f%n",
                        result.implementation.displayName, result.meanThroughput);
            }
        }
    }

    private static void printStatsRow(String implementation, OperationStats stats) {
        if (stats == null) return; // READ_ONLY deliberately has no add/remove rows.
        System.out.printf(Locale.ROOT,
                "%-15s %-10s %,9d %10.2f %10.3f %9.3f %9.3f %9.3f %9.3f%n",
                implementation, stats.operation.csvName, stats.samples, stats.successRate * 100,
                stats.meanMicros, stats.stddevMicros, stats.p90Micros, stats.p95Micros,
                stats.p99Micros);
    }

    private static void writeCsvRows(PrintWriter csv, BenchmarkResult result) {
        writeCsvRow(csv, result, result.contains);
        writeCsvRow(csv, result, result.add);
        writeCsvRow(csv, result, result.remove);
    }

    private static void writeCsvRow(PrintWriter csv, BenchmarkResult result, OperationStats stats) {
        if (stats == null) return;
        csv.printf(Locale.ROOT, "%s,%s,%s,%d,%s,%d,%.6f,%.6f,%.6f,%.6f,%.6f,%.6f,%.6f%n",
                result.implementation.csvName, result.workload, KEY_DISTRIBUTION, result.threadCount,
                stats.operation.csvName, stats.samples, stats.successRate, stats.meanMicros,
                stats.stddevMicros, stats.p90Micros, stats.p95Micros, stats.p99Micros,
                result.meanThroughput);
    }
}
````

### `laboEstructurasLockFree/src/test/java/org/pcp/exactas/set/ConcurrentListSetTest.java`

````java
package org.pcp.exactas.set;

import static org.junit.jupiter.api.Assertions.*;

import java.time.Duration;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.Callable;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.function.Supplier;
import java.util.stream.Stream;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.MethodSource;

class ConcurrentListSetTest {
    static Stream<Supplier<ConcurrentIntSet>> concurrentImplementations() {
        return Stream.of(CoarseListSet::new, FineListSet::new, OptimisticListSet::new,
                LazyListSet::new, LockFreeListSet::new);
    }

    @ParameterizedTest
    @MethodSource("concurrentImplementations")
    void duplicateInsertionAndRemoval(Supplier<ConcurrentIntSet> factory) {
        ConcurrentIntSet probe = factory.get();
        String implementation = probe.getClass().getSimpleName();
        ImplementationAssumptions.assumeImplemented(probe);
        assertTimeoutPreemptively(Duration.ofSeconds(10), () -> {
            ConcurrentIntSet set = factory.get();
            List<Boolean> adds = simultaneous(8, () -> set.add(42));
            assertEquals(1, adds.stream().filter(Boolean::booleanValue).count(),
                    implementation + ": exactly one concurrent add(42) must succeed; results=" + adds);
            assertTrue(set.contains(42), implementation + ": 42 must be present after the winning add");
            List<Boolean> removes = simultaneous(8, () -> set.remove(42));
            assertEquals(1, removes.stream().filter(Boolean::booleanValue).count(),
                    implementation + ": exactly one concurrent remove(42) must succeed; results=" + removes);
            assertFalse(set.contains(42), implementation + ": 42 must be absent after the winning removal");
        }, implementation + ": duplicate-key races must finish; timeout may indicate deadlock");
    }

    @ParameterizedTest
    @MethodSource("concurrentImplementations")
    void sameWindowAndAdjacentRemoval(Supplier<ConcurrentIntSet> factory) {
        ConcurrentIntSet probe = factory.get();
        String implementation = probe.getClass().getSimpleName();
        ImplementationAssumptions.assumeImplemented(probe);
        assertTimeoutPreemptively(Duration.ofSeconds(10), () -> {
            ConcurrentIntSet set = factory.get();
            set.add(10); set.add(40);
            List<Boolean> inserted = simultaneous(List.of(() -> set.add(20), () -> set.add(30)));
            assertEquals(List.of(true, true), inserted,
                    implementation + ": both same-window insertions must succeed; results=" + inserted);
            assertTrue(set.contains(20), implementation + ": 20 must survive concurrent same-window insertion");
            assertTrue(set.contains(30), implementation + ": 30 must survive concurrent same-window insertion");
            set.add(10); set.add(20); set.add(30);
            List<Boolean> removed = simultaneous(List.of(() -> set.remove(10), () -> set.remove(20)));
            assertEquals(List.of(true, true), removed,
                    implementation + ": both adjacent removals must succeed; results=" + removed);
            assertFalse(set.contains(10), implementation + ": 10 must be absent after adjacent removal");
            assertFalse(set.contains(20), implementation + ": 20 must be absent after adjacent removal");
            assertTrue(set.contains(30), implementation + ": unaffected key 30 must remain present");
        }, implementation + ": same-window insertion and adjacent removal must finish; timeout may indicate deadlock");
    }

    @ParameterizedTest
    @MethodSource("concurrentImplementations")
    void disjointRanges(Supplier<ConcurrentIntSet> factory) {
        ConcurrentIntSet probe = factory.get();
        String implementation = probe.getClass().getSimpleName();
        ImplementationAssumptions.assumeImplemented(probe);
        assertTimeoutPreemptively(Duration.ofSeconds(10), () -> {
            ConcurrentIntSet set = factory.get();
            List<Callable<Boolean>> adds = new ArrayList<>();
            for (int thread = 0; thread < 4; thread++) {
                int start = thread * 100;
                adds.add(() -> { for (int i = start; i < start + 100; i++) if (!set.add(i)) return false; return true; });
            }
            List<Boolean> addResults = simultaneous(adds);
            assertTrue(addResults.stream().allMatch(Boolean::booleanValue),
                    implementation + ": every disjoint-range insertion must succeed; results=" + addResults);
            for (int i = 0; i < 400; i++) assertTrue(set.contains(i), implementation + ": inserted disjoint-range key must be present: " + i);
            List<Callable<Boolean>> removes = new ArrayList<>();
            for (int thread = 0; thread < 4; thread++) {
                int start = thread * 100;
                removes.add(() -> { for (int i = start; i < start + 100; i++) if (!set.remove(i)) return false; return true; });
            }
            List<Boolean> removeResults = simultaneous(removes);
            assertTrue(removeResults.stream().allMatch(Boolean::booleanValue),
                    implementation + ": every disjoint-range removal must succeed; results=" + removeResults);
            for (int i = 0; i < 400; i++) assertFalse(set.contains(i), implementation + ": removed disjoint-range key must be absent: " + i);
        }, implementation + ": disjoint-range operations must finish; timeout may indicate deadlock");
    }

    private static List<Boolean> simultaneous(int threads, Callable<Boolean> work) throws Exception {
        List<Callable<Boolean>> workItems = new ArrayList<>();
        for (int i = 0; i < threads; i++) workItems.add(work);
        return simultaneous(workItems);
    }

    private static List<Boolean> simultaneous(List<Callable<Boolean>> work) throws Exception {
        ExecutorService executor = Executors.newFixedThreadPool(work.size());
        CountDownLatch ready = new CountDownLatch(work.size());
        CountDownLatch start = new CountDownLatch(1);
        try {
            List<Future<Boolean>> futures = new ArrayList<>();
            for (Callable<Boolean> item : work) futures.add(executor.submit(() -> { ready.countDown(); start.await(); return item.call(); }));
            assertTrue(ready.await(5, java.util.concurrent.TimeUnit.SECONDS),
                    "Not all " + work.size() + " workers reached the simultaneous-start barrier");
            start.countDown();
            List<Boolean> results = new ArrayList<>();
            for (Future<Boolean> future : futures) results.add(future.get());
            return results;
        } finally { executor.shutdownNow(); }
    }
}
````

### `laboEstructurasLockFree/src/test/java/org/pcp/exactas/set/ImplementationAssumptions.java`

````java
package org.pcp.exactas.set;

import static org.junit.jupiter.api.Assumptions.assumeTrue;

final class ImplementationAssumptions {
    private ImplementationAssumptions() {
    }

    static void assumeImplemented(ConcurrentIntSet set) {
        assumeTrue(set.isImplemented(),
                () -> set.getClass().getSimpleName() + " is not implemented yet");
    }
}
````

### `laboEstructurasLockFree/src/test/java/org/pcp/exactas/set/LinearizabilityChecker.java`

````java
package org.pcp.exactas.set;

import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

/** Deliberately tiny DFS checker for completed Boolean integer-set operations. */
final class LinearizabilityChecker {
    enum Type { ADD, REMOVE, CONTAINS }
    record Operation(int threadId, long invocationSequence, long responseSequence, Type type, int key, boolean result) { }

    boolean isLinearizable(List<Operation> history) {
        return search(new HashSet<>(), new ArrayList<>(history), history);
    }

    private boolean search(Set<Integer> model, List<Operation> remaining, List<Operation> all) {
        if (remaining.isEmpty()) return true;
        for (int i = 0; i < remaining.size(); i++) {
            Operation candidate = remaining.get(i);
            if (!predecessorsDone(candidate, remaining, all)) continue;
            Set<Integer> nextModel = new HashSet<>(model);
            if (!matches(nextModel, candidate)) continue;
            List<Operation> nextRemaining = new ArrayList<>(remaining);
            nextRemaining.remove(i);
            if (search(nextModel, nextRemaining, all)) return true;
        }
        return false;
    }

    private boolean predecessorsDone(Operation candidate, List<Operation> remaining, List<Operation> all) {
        for (Operation other : all) {
            if (other.responseSequence < candidate.invocationSequence && remaining.contains(other)) return false;
        }
        return true;
    }

    private boolean matches(Set<Integer> model, Operation op) {
        boolean expected = switch (op.type) {
            case ADD -> model.add(op.key);
            case REMOVE -> model.remove(op.key);
            case CONTAINS -> model.contains(op.key);
        };
        return expected == op.result;
    }
}
````

### `laboEstructurasLockFree/src/test/java/org/pcp/exactas/set/LinearizabilityCheckerTest.java`

````java
package org.pcp.exactas.set;

import static org.junit.jupiter.api.Assertions.*;

import java.time.Duration;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicLong;
import java.util.function.Supplier;
import java.util.stream.Stream;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.MethodSource;

class LinearizabilityCheckerTest {
    @Test
    void recognizesKnownHistories() {
        LinearizabilityChecker checker = new LinearizabilityChecker();
        List<LinearizabilityChecker.Operation> linearizable = List.of(
                new LinearizabilityChecker.Operation(0, 1, 2, LinearizabilityChecker.Type.ADD, 1, true),
                new LinearizabilityChecker.Operation(1, 3, 4, LinearizabilityChecker.Type.CONTAINS, 1, true));
        assertTrue(checker.isLinearizable(linearizable),
                "The checker must accept this known linearizable history: " + linearizable);
        List<LinearizabilityChecker.Operation> nonLinearizable = List.of(
                new LinearizabilityChecker.Operation(0, 1, 2, LinearizabilityChecker.Type.ADD, 1, false));
        assertFalse(checker.isLinearizable(nonLinearizable),
                "The checker must reject an add(1) that falsely returned false on an empty set: " + nonLinearizable);
    }

    static Stream<Supplier<ConcurrentIntSet>> implementations() {
        return Stream.of(CoarseListSet::new, FineListSet::new, OptimisticListSet::new,
                LazyListSet::new, LockFreeListSet::new);
    }

    @ParameterizedTest
    @MethodSource("implementations")
    void tinyConcurrentHistoriesAreLinearizable(Supplier<ConcurrentIntSet> factory) {
        ConcurrentIntSet probe = factory.get();
        String implementation = probe.getClass().getSimpleName();
        ImplementationAssumptions.assumeImplemented(probe);
        assertTimeoutPreemptively(Duration.ofSeconds(10), () -> {
            for (int round = 0; round < 10; round++) {
                List<LinearizabilityChecker.Operation> history = runHistory(factory.get(), round);
                int historyRound = round;
                assertTrue(new LinearizabilityChecker().isLinearizable(history),
                        () -> implementation + ": generated history in round " + historyRound + " is not linearizable: " + history);
            }
        }, implementation + ": bounded linearizability histories must finish; timeout may indicate deadlock");
    }

    private static List<LinearizabilityChecker.Operation> runHistory(ConcurrentIntSet set, int round) throws Exception {
        LinearizabilityChecker.Type[][] scripts = {
                {LinearizabilityChecker.Type.ADD, LinearizabilityChecker.Type.REMOVE},
                {LinearizabilityChecker.Type.CONTAINS, LinearizabilityChecker.Type.ADD},
                {LinearizabilityChecker.Type.ADD, LinearizabilityChecker.Type.CONTAINS}
        };
        AtomicLong sequence = new AtomicLong();
        List<LinearizabilityChecker.Operation> history = java.util.Collections.synchronizedList(new ArrayList<>());
        CountDownLatch ready = new CountDownLatch(3);
        CountDownLatch start = new CountDownLatch(1);
        ExecutorService executor = Executors.newFixedThreadPool(3);
        try {
            for (int thread = 0; thread < 3; thread++) {
                final int id = thread;
                executor.submit(() -> {
                    ready.countDown(); start.await();
                    for (int i = 0; i < 2; i++) {
                        LinearizabilityChecker.Type type = scripts[id][i];
                        int key = (id + i + round) % 3;
                        long invocation = sequence.incrementAndGet();
                        boolean result = switch (type) {
                            case ADD -> set.add(key);
                            case REMOVE -> set.remove(key);
                            case CONTAINS -> set.contains(key);
                        };
                        long response = sequence.incrementAndGet();
                        history.add(new LinearizabilityChecker.Operation(id, invocation, response, type, key, result));
                    }
                    return null;
                });
            }
            assertTrue(ready.await(3, TimeUnit.SECONDS),
                    "Not all three history workers reached the simultaneous-start barrier in round " + round);
            start.countDown();
        } finally { executor.shutdown(); }
        assertTrue(executor.awaitTermination(5, TimeUnit.SECONDS),
                "History workers did not terminate in round " + round + "; partial history=" + history);
        return history;
    }
}
````

### `laboEstructurasLockFree/src/test/java/org/pcp/exactas/set/ListSetContractTest.java`

````java
package org.pcp.exactas.set;

import static org.junit.jupiter.api.Assertions.*;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Random;
import java.util.function.Supplier;
import java.util.stream.Stream;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.MethodSource;

class ListSetContractTest {
    static Stream<Supplier<ConcurrentIntSet>> implementations() {
        return Stream.of(SequentialListSet::new, CoarseListSet::new, FineListSet::new,
                OptimisticListSet::new, LazyListSet::new, LockFreeListSet::new);
    }

    @ParameterizedTest
    @MethodSource("implementations")
    void basicContract(Supplier<ConcurrentIntSet> factory) {
        ConcurrentIntSet set = factory.get();
        ImplementationAssumptions.assumeImplemented(set);
        String implementation = name(set);
        assertFalse(set.contains(7), implementation + ": empty set must not contain 7");
        assertTrue(set.add(7), implementation + ": first add(7) must succeed");
        assertFalse(set.add(7), implementation + ": duplicate add(7) must return false");
        assertTrue(set.contains(7), implementation + ": set must contain 7 after successful insertion");
        assertTrue(set.remove(7), implementation + ": remove(7) must succeed for a present key");
        assertFalse(set.remove(7), implementation + ": second remove(7) must return false");
        assertFalse(set.contains(7), implementation + ": set must not contain 7 after removal");
    }

    @ParameterizedTest
    @MethodSource("implementations")
    void insertionOrdersAndPositions(Supplier<ConcurrentIntSet> factory) {
        ConcurrentIntSet set = factory.get();
        ImplementationAssumptions.assumeImplemented(set);
        String implementation = name(set);
        for (int i = 1; i <= 100; i++) assertTrue(set.add(i), implementation + ": ascending add(" + i + ") must succeed");
        for (int i = 200; i > 100; i--) assertTrue(set.add(i), implementation + ": descending add(" + i + ") must succeed");
        List<Integer> randomOrder = new ArrayList<>();
        for (int i = 0; i < 100; i++) randomOrder.add(1_000 + i);
        Collections.shuffle(randomOrder, new Random(8172));
        for (int value : randomOrder) assertTrue(set.add(value), implementation + ": shuffled unique add(" + value + ") must succeed");
        for (int i = 1; i <= 200; i++) assertTrue(set.contains(i), implementation + ": must contain inserted key " + i);
        assertTrue(set.remove(1), implementation + ": removal near the head must succeed");
        assertTrue(set.remove(100), implementation + ": removal in the middle must succeed");
        assertTrue(set.remove(200), implementation + ": removal near the tail must succeed");
        assertFalse(set.contains(1), implementation + ": removed head-side key 1 must be absent");
        assertFalse(set.contains(100), implementation + ": removed middle key 100 must be absent");
        assertFalse(set.contains(200), implementation + ": removed tail-side key 200 must be absent");
    }

    @ParameterizedTest
    @MethodSource("implementations")
    void repeatedAddRemove(Supplier<ConcurrentIntSet> factory) {
        ConcurrentIntSet set = factory.get();
        ImplementationAssumptions.assumeImplemented(set);
        String implementation = name(set);
        for (int i = 0; i < 1_000; i++) {
            assertTrue(set.add(42), implementation + ": iteration " + i + " first add(42) must succeed");
            assertFalse(set.add(42), implementation + ": iteration " + i + " duplicate add(42) must fail");
            assertTrue(set.remove(42), implementation + ": iteration " + i + " first remove(42) must succeed");
            assertFalse(set.remove(42), implementation + ": iteration " + i + " duplicate remove(42) must fail");
        }
    }

    private static String name(ConcurrentIntSet set) { return set.getClass().getSimpleName(); }
}
````

### `laboEstructurasLockFree/src/test/java/org/pcp/exactas/set/StressListSetTest.java`

````java
package org.pcp.exactas.set;

import static org.junit.jupiter.api.Assertions.*;

import java.time.Duration;
import java.util.Random;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import java.util.function.Supplier;
import org.junit.jupiter.api.condition.EnabledIfSystemProperty;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.MethodSource;

class StressListSetTest {
    static java.util.stream.Stream<Supplier<ConcurrentIntSet>> implementations() {
        return java.util.stream.Stream.of(CoarseListSet::new, FineListSet::new, OptimisticListSet::new,
                LazyListSet::new, LockFreeListSet::new);
    }

    @ParameterizedTest
    @MethodSource("implementations")
    void seededMixedWorkloadCompletes(Supplier<ConcurrentIntSet> factory) {
        ConcurrentIntSet probe = factory.get();
        String implementation = probe.getClass().getSimpleName();
        ImplementationAssumptions.assumeImplemented(probe);
        assertTimeoutPreemptively(Duration.ofSeconds(30), () -> {
            long seed = Long.getLong("seed", 19_847L);
            ConcurrentIntSet set = factory.get();
            int threads = 24, perThread = 6_000;
            CountDownLatch ready = new CountDownLatch(threads), start = new CountDownLatch(1);
            ExecutorService executor = Executors.newFixedThreadPool(threads);
            try {
                for (int t = 0; t < threads; t++) {
                    int id = t;
                    executor.submit(() -> {
                        Random random = new Random(seed + id);
                        ready.countDown();
                        start.await();
                        int base = id * 10_000;
                        for (int i = 0; i < perThread; i++) {
                            int key = base + random.nextInt(1_000);
                            switch (random.nextInt(3)) { case 0 -> set.add(key); case 1 -> set.remove(key); default -> set.contains(key); }
                        }
                        return null;
                    });
                }
                assertTrue(ready.await(5, TimeUnit.SECONDS),
                        implementation + ": not all " + threads + " stress workers reached the start barrier; seed=" + seed);
                start.countDown();
            } finally { executor.shutdown(); }
            assertTrue(executor.awaitTermination(20, TimeUnit.SECONDS),
                    implementation + ": stress workers did not terminate; seed=" + seed
                            + ", threads=" + threads + ", operationsPerThread=" + perThread);
        }, implementation + ": seeded mixed workload timed out; seed=" + Long.getLong("seed", 19_847L));
    }
}
````
