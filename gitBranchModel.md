# Git branch model
Este modelo fue concebido en 2010, ahora hace más de 10 años, y no mucho después de la creación de Git. 
![alt text](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f1/Git-branching-model.pdf/page1-636px-Git-branching-model.pdf.jpg)
## Descentralizado pero centralizado 
La configuración del repositorio que utilizamos y que funciona bien con este modelo de ramificación es la de un repositorio central de "verdad". Tenga en cuenta que este repositorio solo se  considera el central (dado que Git es un DVCS, no existe un repositorio central a nivel técnico). Nos referiremos a este repositorio como origin, ya que este nombre es familiar para todos los usuarios de Git.
![alt text](https://nvie.com/img/centr-decentr@2x.png)

Cada desarrollador tira y empuja al origen. Pero además de las relaciones centralizadas push-pull, cada desarrollador también puede extraer cambios de otros pares para formar sub equipos. Por ejemplo, esto podría ser útil para trabajar junto con dos o más desarrolladores en una nueva característica importante, antes de adelantar originprematuramente el trabajo en progreso . En la figura anterior, hay subgrupos de Alice y Bob, Alice y David, y Clair y David.

Técnicamente, esto no significa nada más que que Alice haya definido un control remoto Git, llamado bob, apuntando al repositorio de Bob, y viceversa.
## Las ramas principales
En esencia, el modelo de desarrollo está muy inspirado en los modelos existentes que existen. El repositorio central tiene dos ramas principales con una vida infinita:

master
develop
La mastersucursal en origindebería ser familiar para todos los usuarios de Git. 
Paralelo a la masterrama, existe otra rama llamada develop.

Consideramos origin/masterque es la rama principal donde el código fuente de 
HEADsiempre refleja un estado listo para producción .

Consideramos origin/developser la rama principal donde el código fuente de HEADsiempre
 refleja un estado con los últimos cambios de desarrollo entregados para la próxima versión. 
 Algunos llamarían a esto la "rama de integración". Aquí es donde se 
 construyen las construcciones nocturnas automáticas.
* master
* develop
La mastersucursal en origindebería ser familiar para  todos los usuarios de Git. Paralelo a la masterrama, existe otra rama llamada develop.

Consideramos origin/masterque es la rama principal donde el código fuente de HEADsiempre refleja un estado listo para producción .

Consideramos origin/developser la rama principal donde el código fuente de HEADsiempre refleja un estado con los últimos cambios de desarrollo entregados para la próxima versión. Algunos llamarían a esto la "rama de integración". Aquí es donde se construyen las construcciones nocturnas automáticas.

Cuando el código fuente en la developrama alcanza un punto estable y está listo para ser lanzado, todos los cambios deben fusionarse de master alguna manera y luego etiquetarse con un número de versión. Cómo se hace esto en detalle se discutirá más adelante.

Por lo tanto, cada vez que los cambios se fusionan nuevamente master, esta es una nueva versión de producción por definición . Tendemos a ser muy estrictos en esto, de modo que, en teoría, podríamos usar un script de enlace Git para construir y desplegar automáticamente nuestro software en nuestros servidores de producción cada vez que se realiza una confirmación master.
![alt text](https://nvie.com/img/main-branches@2x.png)

## Ramas de apoyo 
Junto a las ramas principales mastery develop, nuestro modelo de desarrollo utiliza una variedad de ramas de soporte para ayudar al desarrollo paralelo entre los miembros del equipo, facilitar el seguimiento de las características, prepararse para lanzamientos de producción y ayudar a solucionar rápidamente los problemas de producción en vivo. A diferencia de las ramas principales, estas ramas siempre tienen un tiempo de vida limitado, ya que eventualmente se eliminarán.

Los diferentes tipos de ramas que podemos usar son:

* Ramas de características
* Liberar ramas
* Ramas de revisión
Cada una de estas ramas tiene un propósito específico y están sujetas a reglas estrictas sobre qué ramas pueden ser su rama de origen y qué ramas deben ser sus objetivos de fusión. Los atravesaremos en un minuto.

De ninguna manera estas ramas son "especiales" desde una perspectiva técnica. Los tipos de ramas se clasifican según cómo los usamos . Por supuesto, son simples ramas viejas de Git.

## Ramas de características 

Puede ramificarse de:
develop
Debe fusionarse nuevamente en:
develop
Convención de nomenclatura de rama:
nada, excepto  master, develop, release-*, ohotfix-*
Las ramas de características (o a veces denominadas ramas temáticas) se utilizan para desarrollar nuevas características para la próxima versión futura o futura. Al comenzar el desarrollo de una característica, la versión de destino en la que se incorporará esta característica puede ser desconocida en ese momento. La esencia de una rama de características es que existe mientras la característica esté en desarrollo, pero eventualmente se fusionará nuevamente develop(para agregar definitivamente la nueva característica a la próxima versión) o se descartará (en caso de un experimento decepcionante).

Las ramas de características generalmente existen solo en repositorios de desarrolladores, no en origin.

## Crear una rama de características 
Al comenzar a trabajar en una nueva característica, bifurcarse de la developrama.

``` $ git checkout -b myfeature desarrollo
 Cambiado a una nueva rama "myfeature" 
 ```

## Incorporando una característica terminada en desarrollo 
Las funciones terminadas se pueden combinar en la developrama para agregarlas definitivamente a la próxima versión:
```
 $ git checkout desarrollo
 Cambiado a rama 'desarrollo' 
$ git fusión --no-ff myfeature
 Actualizando ea1b82a..05e9557 
(Resumen de cambios) 
$ git branch -d myfeature
 Rama eliminada myfeature (era 05e9557). 
$ git empuje origen desarrollar 
```
El --no-ffindicador hace que la fusión siempre cree un nuevo objeto de confirmación, incluso si la fusión se puede realizar con un avance rápido. Esto evita perder información sobre la existencia histórica de una rama de características y agrupa todas las confirmaciones que juntas agregaron la característica. Comparar:



En el último caso, es imposible ver en el historial de Git cuáles de los objetos de confirmación juntos han implementado una característica; tendría que leer manualmente todos los mensajes de registro. Revertir una característica completa (es decir, un grupo de confirmaciones) es un verdadero dolor de cabeza en la última situación, mientras que se hace fácilmente si --no-ffse utilizó la bandera.

Sí, creará algunos objetos de confirmación más (vacíos), pero la ganancia es mucho mayor que el costo.

###Liberar ramas 
Puede ramificarse de:
develop
Debe fusionarse nuevamente en:
develop y master
Convención de nomenclatura de rama:
release-*
Las sucursales de lanzamiento apoyan la preparación de una nueva versión de producción. Permiten puntos de última hora de i y cruzan t. Además, permiten pequeñas correcciones de errores y preparan metadatos para un lanzamiento (número de versión, fechas de compilación, etc.). Al hacer todo este trabajo en una rama de lanzamiento, la develop rama se borra para recibir características para la próxima gran versión.

El momento clave para ramificar una nueva rama de lanzamiento developes cuando el desarrollo (casi) refleja el estado deseado de la nueva versión. Al menos todas las características que están destinadas a la versión para ser construida deben fusionarse en developeste momento. Es posible que no todas las funciones destinadas a futuras versiones: deben esperar hasta que se ramifique la rama de la versión.

Es exactamente al comienzo de una rama de lanzamiento que al próximo lanzamiento se le asigna un número de versión, no antes. Hasta ese momento, la develop rama reflejaba los cambios para la "próxima versión", pero no está claro si esa "próxima versión" eventualmente se convertirá en 0.3 o 1.0, hasta que se inicie la rama de la versión. Esa decisión se toma al comienzo de la rama de lanzamiento y se lleva a cabo según las reglas del proyecto sobre el cambio de número de versión.

Creando una rama de lanzamiento 
Las ramas de liberación se crean a partir de la developrama. Por ejemplo, digamos que la versión 1.1.5 es la versión de producción actual y tenemos una gran versión próxima. El estado de developestá listo para la "próxima versión" y hemos decidido que se convertirá en la versión 1.2 (en lugar de 1.1.6 o 2.0). Entonces nos ramificamos y le damos un nombre a la rama de lanzamiento que refleja el nuevo número de versión:
```
$ git checkout -b release-1.2 desarrollo
 Cambiado a una nueva rama "release-1.2" 
$ ./bump-version.sh 1.2
 Archivos modificados con éxito, la versión pasó a 1.2. 
$ git commit -a -m "Número de versión de Bumped a 1.2" 
[versión-1.2 74d9424] Número de versión de Bumped a 1.2 
1 archivos modificados, 1 inserciones (+), 1 eliminaciones (-)
```
Después de crear una nueva rama y cambiar a ella, topamos con el número de versión. Aquí,  bump-version.shhay un script de shell ficticio que cambia algunos archivos en la copia de trabajo para reflejar la nueva versión. (Por supuesto, esto puede ser un cambio manual; el punto es que algunos archivos cambian). Luego, se confirma el número de versión eliminado.

Esta nueva rama puede existir allí por un tiempo, hasta que la versión se pueda implementar definitivamente. Durante ese tiempo, se pueden aplicar correcciones de errores en esta rama (en lugar de en la developrama). Agregar nuevas características grandes aquí está estrictamente prohibido. Deben fusionarse developy, por lo tanto, esperar el próximo gran lanzamiento.

## Finalizando una rama de lanzamiento 
Cuando el estado de la rama de lanzamiento está listo para convertirse en un lanzamiento real, se deben llevar a cabo algunas acciones. Primero, la rama de lanzamiento se fusiona master(ya que cada confirmación masteres un lanzamiento nuevo por definición , recuerde). A continuación, ese compromiso masterdebe etiquetarse para una referencia futura fácil a esta versión histórica. Finalmente, los cambios realizados en la rama de lanzamiento deben fusionarse nuevamente develop, de modo que las versiones futuras también contengan estas correcciones de errores.

Los primeros dos pasos en Git:
```
$ git checkout master
 Cambió a la rama 'master' 
$ git merge --no-ff release-1.2
 Combinación hecha por recursivo. 
(Resumen de cambios) 
$ git tag -a 1.2
```
El lanzamiento ya está hecho y etiquetado para referencia futura.

#### Editar: también puede usar las banderas -so -u <key>para firmar su etiqueta criptográficamente . ####

Sin developembargo, para mantener los cambios realizados en la rama de lanzamiento, necesitamos fusionarlos nuevamente . En Git:

``` $ git checkout desarrollo
 Cambiado a la rama 'desarrollo' 
$ git merge --no-ff release-1.2
 Fusión hecha por recursivo. 
(Resumen de Cambios)
```
Este paso puede conducir a un conflicto de fusión (probablemente incluso, ya que hemos cambiado el número de versión). Si es así, arréglalo y comprométete.

Ahora realmente hemos terminado y la rama de lanzamiento puede eliminarse, ya que ya no la necesitamos:

``` $ git branch -d release-1.2
 Rama eliminada release-1.2 (era ff452fe).
 ```
## Reparar ramas ## 


Puede ramificarse de:
master
Debe fusionarse nuevamente en:
develop y master
Convención de nomenclatura de rama:
hotfix-*
Las ramas de revisión son muy parecidas a las ramas de lanzamiento, ya que también están destinadas a prepararse para un nuevo lanzamiento de producción, aunque no planificado. Surgen de la necesidad de actuar inmediatamente sobre un estado no deseado de una versión de producción en vivo. Cuando un error crítico en una versión de producción debe resolverse de inmediato, una rama de revisión puede separarse de la etiqueta correspondiente en la rama maestra que marca la versión de producción.

La esencia es que el trabajo de los miembros del equipo (en la developsucursal) puede continuar, mientras que otra persona está preparando una solución rápida de producción.

## Creando la rama de revisión 
Las ramas de revisión se crean a partir de la  masterrama. Por ejemplo, digamos que la versión 1.2 es la versión de producción actual que se ejecuta en vivo y causa problemas debido a un error grave. Pero los cambios developaún son inestables. Luego podemos ramificar una rama de revisión y comenzar a solucionar el problema:

``` $ git checkout -b hotfix-1.2.1 master
 Cambiado a una nueva rama "hotfix-1.2.1" 
$ ./bump-version.sh 1.2.1
 Archivos modificados con éxito, la versión pasó a 1.2.1. 
$ git commit -a -m "Número de versión eliminada a 1.2.1" 
[hotfix-1.2.1 41e61bb] Número de versión eliminada a 1.2.1 
1 archivos modificados, 1 inserciones (+), 1 eliminaciones (-)
```
¡No olvides cambiar el número de versión después de ramificarte!

Luego, corrige el error y confirma la corrección en una o más confirmaciones separadas.

``` $ git commit -m "Se 
corrigió un problema 
grave de producción" [hotfix-1.2.1 abbe5d6] Se corrigió un problema grave de producción 5 archivos cambiados, 32 inserciones (+), 17 eliminaciones (-)
```
## Finalizando una rama de revisión 
Cuando termine, la corrección de errores debe volver a fusionarse master, pero también debe volver a fusionarse develop, para garantizar que la corrección de errores también se incluya en la próxima versión. Esto es completamente similar a cómo se terminan las ramas de lanzamiento.

Primero, actualice mastery etiquete el lanzamiento.

``` $ git checkout master
 Cambió a la rama 'master' 
$ git merge --no-ff hotfix-1.2.1
 Fusión hecha por recursivo. 
(Resumen de cambios) 
$ git tag -a 1.2.1
```
 Editar: también puede usar las banderas -so -u <key>para firmar su etiqueta criptográficamente.

A continuación, incluya también la corrección de errores develop:

``` $ git checkout desarrollo
 Cambiado a rama 'desarrollo' 
$ git merge --no-ff hotfix-1.2.1
 Fusión realizada por recursivo. 
(Resumen de Cambios)
``` 
La única excepción a la regla aquí es que,  cuando actualmente existe una rama de lanzamiento, los cambios de revisión deben fusionarse en esa rama de lanzamiento, en lugar dedevelop . Volver a fusionar la corrección de errores en la rama de lanzamiento eventualmente dará como resultado que la corrección de errores también se fusione develop, cuando la rama de lanzamiento haya terminado. (Si el trabajo de developinmediato requiere esta corrección de errores y no puede esperar a que finalice la rama de lanzamiento, también puede fusionar la corrección de errores de forma segura developahora).

Finalmente, elimine la rama temporal:

```
$ git branch -d hotfix-1.2.1
 Rama eliminada hotfix-1.2.1 (era abbe5d6).
 ```