# Pruebas de fusión entre ramas
En este reposiorio, simulamos la creación de un sitio web entre dos usuarios, los cuales no están muy sincronizados a la hora de trabajar y puede que haya *conflictos* a la hora de combinar (*merge*) los cambios de cada uno de ellos. 


## Merge
Vamos a empezar por lo más básico. Tenemos un repositorio con dos ramas. Tenemos el commit inicial en la rama `main`, y cambiamos a `develop` para seguir desarrollando.

Creamos unos cuantos commits en la rama de desarrollo y por fin terminamos nuestra tarea. Estamos listos para incluir esos cambios en la rama principal. Así es como está ahora mismo el historial: 

![Commits 1](img/readme/tree1.png)

Sin complicarnos, hacemos un *merge* normal y corriente. Nos vamos a la rama destino y fusionamos los cambios de la rama origen.

```bash
git switch main
git merge develop
```

> **Note**  
> `git switch` es un comando añadido en las últimas versiones de git. Si no tenemos git suficientemente actualizado, usaremos el clásico `git checkout`.

El historial ahora está así:

![Commits 2](img/readme/tree2.png)

Todos los cambios de `develop` ahora están en `main`. 

### Conflictos
Simulemos una situación de conflicto. De la rama `develop`, vamos a sacar dos ramas:

- `feature/welcome`: En esta rama, se añade un mensaje de bienvenida a la página principal.
- `feature/styles`: En esta, se cambia el color del título.

Supongamos que en cada rama está trabajando un usuario. En un momento dado, el usuario de `feature/welcome` se sale de su jurisdicción y decide que no le gusta el color del título, y pone uno de su preferencia. Esto tendrá consecuencias.

Ambos hacen commit de sus cambios, y el historial se ve así:

![Commits in different branches](img/readme/tree3.png)

Vamos a fusionar los cambios de estilos a `develop`:

![Merge ok](img/readme/mergeok.png)

No hay problema. Los cambios se aplican en la rama destino. Ahora incluyamos los cambios de la otra rama:

![Merge conflict](img/readme/mergeconflict.png)

Tenemos un ***conflicto***. Además, git nos chiva que es el el fichero `css/styles.css`, vamos a ver lo que pasa en VS Code en ese fichero:

![VS Code conflict](img/readme/vscodeconflict.png)

Nos dice que en esa línea, vienen cambios diferentes en ambos lados. Ahora mismo, tiene el valor superior, en verde, pero en la rama entrante, se ha modificado la misma línea con un valor distinto. Debemos elegir si aceptamos el actual, el entrante, ambos o algo diferente.

En nuestro caso, nos quedamos con el cambio que ya tenemos, ya que es el que corresponde a la rama donde estábamos editando los estilos. El usuario trabajando en `feature/welcome` se ha excedido de sus límites y ha modificado un fragmento de código que no le correspondía.

Tras aceptar el cambio correcto, vamos a hacer un `git status`:

![Git status on conflict](img/readme/gitstatusconflict.png)

Nos dice que tenemos *unmerged paths*, es decir, que estamos en medio de un *merge* no finalizado. Nos da varias opciones, o bien abortar la fusión con `git merge --abort` o bien resolver los conflictos haciendo un commit. 

Si miramos más abajo, vemos que en index.html no hay conflictos, está marcado como listo para añadirse al commit, pues las modificaciones en ese archivo no sobreescriben nada en esta rama. Pero css/styles.css sigue marcado como modificado en ambos lados. Como ya lo tenemos resuelto, lo añadimos al stage para marcarlo como tal. De modo que lo que falta por hacer para resolver es:

```bash
git add css/styles.css
git commit -m "Merged branches"
```

Y el historial muestra las ramas correctamente fusionadas:

![Conflict solved](img/readme/conflictsolved.png)

## Rebase
El cambio de base o *rebase* es otra técnica de incorporación de cambios entre ramas, además del merge.

Rebase hace algo opuesto a merge, pero que acaba obteniendo el mismo resultado. En lugar de traer todos los commits de una rama para sí misma, lo que hace es que posiciona todos los commits de la rama actual al final de la rama especificada. 

### Cuando aplicar un rebase
Hemos creado la rama `feature/awesome-things`  a partir de la rama `develop`, en la cual vamos a trabajar, incorporando una serie de características increíbles. Aquí podemos verlas:

![Rebase 1](img/readme/rebase1.png)

En `feature/awesome-things`, hemos aumentado el tamaño del título, y en `develop`, hemos cambiado el texto del título. No hay indicios de conflicto a la vista, pero podemos apreciar que el cambio en `develop` se hizo posteriormente a haber creado la rama secundaria, con lo cual dicho cambio no está incorporado en `feature/awesome-things`.

En este caso, nuestra rama sólo está perdiendo los cambios de un solo commit en `develop`. Sin embargo, si estamos mucho tiempo desarrollando en una rama, en un repositorio en el que colaboran muchos usuarios, es altamente probable que estemos perdiendo muchos cambios, lo cual aumenta la probabilidad de conflictos de fusión, y errores al ejecutar, si algún módulo ha tenido cambios drásticos en su interfaz y/o funcionamiento.

**Conclusión**: se recomienda que nuestras ramas vayan actualizandose con su rama "padre" de vez en cuando. Ahí entra el rebase.

### Como aplicar un rebase
El rebase se aplica exactamente igual que el merge. Debemos situarnos en la rama destino, es decir, en la que queremos incorporar los cambios. Después, hacemos el rebase, así:

```bash
git switch feature/awesome-things
git rebase develop
```

![Rebase 2](img/readme/rebase2.png)

Veamos como ha quedado ahora el historial:

![Rebase 3](img/readme/rebase3.png)

Vemos varias cosas. Lo primero, el historial ahora es lineal, no hay "ramificaciones". Y el commit con el cambio de tamaño está *después* que el de cambio de título.

Desde fuera, parece como si todos los commits de `feature/awesome-things` se hubiesen movido para colocarse delante del úlimo commit de `develop`. Sin embargo, los commits no pueden "moverse", y una pista la tenemos en el hecho de que el commit `Bigger title` ahora tiene un identificador (un *hash*) distinto. Eso se debe a que **NO** es el mismo commit.

Internamente, lo que se ha hecho es **eliminar** todos esos commits y crear otros con las mismas modificaciones, pero partiendo (poniendo su ***base***) desde el final de la rama que queríamos incorporar (la rama origen).

> **Warning**  
> El rebase altera el historial, es decir, elimina commits y genera otros nuevos, con distinto código *hash*. Este tipo de técnicas no son recomendadas si hablamos de commits a los cuales otros usuarios tienen acceso.
> Si un usuario quisiera acceder a uno de esos commits, o generase una rama partiendo de ellos, podría haber problemas si el rebase los elimina.

### Conflictos 
Puesto que estamos igualmente integrando cambios de una rama en otra, puede haber conflictos en el proceso. Vamos a hacer algo así.

Creamos dos ramas: `feature/add-text` y `feature/font-changes `. Añadimos unos cuantos commits en la primera:

![Alt text](img/readme/rebaseconflict1.png)

Cambiamos a la segunda, donde queremos incorporar los cambios de la primera, para ir sincronizados:

![Alt text](img/readme/rebaseconflict2.png)

Hacemos los cambios necesarios y creamos un commit:

![Alt text](img/readme/rebaseconflict3.png)

Se ve genial, todo lineal. Ahora, de vuelta en `feature/add-text`, vemos que alguien se ha vuelto a salir de su campo de acción y ha cambiado la fuente:

![Alt text](img/readme/rebaseconflict4.png)

En `feature/font-changes`, el usuario quiere volver a incorporar los cambios de `feature/add-text`. Empieza el show:

![Alt text](img/readme/rebaseconflict5.png)

Esto es lo que obtenemos al tratar de hacer rebase. No es muy explicativo. Veamos qué muestra `git status`:

![Alt text](img/readme/rebaseconflict6.png)

Esto ya nos aclara un poco, nos da varias opciones. Vamos a optar por continuar el rebase con `git rebase --continue`.

![Alt text](img/readme/rebaseconflict7.png)

De nuevo más información, y ya empieza a parecerse al proceso de resolución de conflictos del merge. Tenemos que resolver manualmente, marcarlos como resueltos añadiendo al stage y haciendo commit.

También podemos saltarnos ese commit o cancelar todo el rebase. Continuemos. En VS Code, tenemos ya esta vista ya familiar:

![Alt text](img/readme/rebaseconflict8.png)

Aquí hay algo interesante. Recordemos que estamos en `feature/font-changes`, queriendo incorporar `feature/add-text`. ¿Y tenemos los cambios hechos en el commit de `feature/font-changes` como *Incoming*? En realidad tiene sentido, recordemos que `rebase` lo que hace es posicionar **nuestra** rama al final de otra. De modo que aceptamos en este caso los cambios entrantes.

Añadimos el cambio al stage y hacemos commit:

![Alt text](img/readme/rebaseconflict9.png)

Por último, tal y como se nos dijo, ejecutamos `git rebase --continue`:

![Alt text](img/readme/rebaseconflict10.png)

Por fin tenemos el conflicto resuelto. El árbol queda así:

![Alt text](img/readme/rebaseconflict11.png)

> **Note**  
> Vemos que `rebase` genera árboles más limpios, pero tratar con los conflictos es mucho más difícil que con `merge`. Y no olvidemos el problema de la eliminación de commits. Nunca se avisa demasiadas veces.

### Peligro
Siguiendo con las mismas ramas, esta vez vamos a provocar algo bastante peor que un conflicto. Resumiendo el proceso anterior, se crearon dos ramas, y una de ellas (`feature/font-changes`) hizo `rebase` de la otra (`feature/add-text`), en dos ocasiones, la segunda con conflictos, pero finalmente resueltos.

De ahí deducimos que ahora mismo el origen de `feature/font-changes` está en algún punto de `feature/add-text`. Vamos a ver cuanto puede el `rebase` complicarnos la vida si no sabemos lo que hacemos.

Siguiendo nuestro proceso de desarrollo, la rama `develop` sigue su avance, incluyendo más commits:



### Merge después de rebase
Analicemos este caso:
```bash
git switch feature/styles
git rebase develop
git switch develop
git merge feature/styles
```

Hemos incorporado los cambios de la rama secundaria en la principal con `git rebase`. A continuación, hemos vuelto a la principal para traernos los cambios de la secundaria con `git merge`. 

Si hacemos un `git branch`, veremos que tenemos dos ramas. Sin embargo, si observamos el grafo, vemos algo curioso. El historial es lineal. No hay ramificaciones. Ese es uno de los motivos por los que algunos usuarios utilizan rebase. Consideran que un historial lineal es más sencillo de entender y gestionar.

## Stash
Para explicar lo que es el stash, maginemos que tenemos unos cambios que enviamos a ser evaluados antes de ser incluídos en una release, pero mientras tanto seguimos desarrollando nuevas funcionalidades.

Pasado un tiempo, nos llaman diciendo que tenemos que modificar nuestro anterior trabajo, y nosotros estamos con una característica a medio desarrollar. No queremos perder el trabajo que tenemos ahora mismo por volver a la rama en la que desarrollabamos antes.

Una solución puede ser un commit. Guardamos nuestros cambios y nos libramos. Pero realmente ¿tiene sentido añadir un commit de algo hecho a medias? Y ¿qué pondríamos en el commit? No es muy agradable ver un mensaje de commit así:

```
Just to save my WIP

This is some unfinished feature, this commit only serves as a checkpoint so I don't lose my work before switching branches.
```

Por ello existe el stash. Un espacio aparte en el que guardar cambios destinados a ser recuperados más tarde. En lugar de ensuciar el historial con un commit que no aporta nada, ponemos los cambios en un limbo, dejando limpio el *working tree*, y recuperamos dichos cambios más tarde.

### Guardar y recuperar cambios
Para guardar los cambios, simplemente usamos el comando `git stash`. 

Para recuperar los cambios, tenemos dos métodos: *pop* y *apply*. 
1. ***Pop***. Recupera los cambios almacenados en el stash y los borra del mismo.
2. ***Apply***. Recupera los cambios del stash sin borrarlos.

Ambos comandos actuarán sobre los últimos cambios almacenados (*LIFO*), porque sí, se puede tener más de un stash, pero no entraremos ahí de momento.

### No todo se guarda
El comando `git stash` **no** guardará cambios que no estén en el *stage* (*unstaged*) ni ficheros ignorados. Hay varias formas de solucionar esto.

1. Añadir los ficheros manualmente y hacer un stash como ya sabemos.
   ```bash
   git add .
   git stash
   ```
2. Usar el flag `-u` o `--include-untracked` para guardar también los cambios que no están en el stage. Con esto seguimos dejando fuera los ficheros ignorados.
   ```bash
   git stash -u
   ```
3. Usar e parámetro `-a` o `--all` para guardar todo, incluso ignorados.
   ```bash
   git stash -a
   ```
