# Pruebas de merge
En este reposiorio, simulamos la creación de un sitio web entre dos usuarios, los cuales no están muy sincronizados a la hora de trabajar y puede que haya *conflictos* a la hora de combinar (*merge*) los cambios de cada uno de ellos. 

## Merge
Vamos a empezar por lo más básico. Tenemos un repositorio con dos ramas. Tenemos el commit inicial en la rama `main`, y cambiamos a `develop` para seguir desarrollando.

Creamos unos cuantos commits en la rama de desarrollo y por fin terminamos nuestra tarea. Estamos listos para incluir esos cambios en la rama principal. Así es como está ahora mismo el historial: 

![Commits 1](img/readme/tree1.png)

Sin complicarnos, hacemos un *merge* normal y corriente. Todos los cambios de `develop` ahora están en `main`.

```bash
git switch main
git merge develop
```

> `git switch` es un comando añadido en las últimas versiones de git. Si no tenemos git suficientemente actualizado, usaremos el clásico `git checkout`.

El historial ahora está así:

![Commits 2](img/readme/tree2.png)

Ahora `main` y el puntero `HEAD` apuntan al último commit de `develop`.

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
