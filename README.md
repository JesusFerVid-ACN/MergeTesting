# Pruebas de merge
En este reposiorio, simulamos la creación de un sitio web entre dos usuarios, los cuales no están muy sincronizados a la hora de trabajar y puede que haya *conflictos* a la hora de combinar (*merge*) los cambios de cada uno de ellos. 

## Merge
Vamos a empezar por lo más básico. Tenemos un repositorio con dos ramas. Tenemos el commit inicial en la rama `main`, y cambiamos a `develop` para seguir desarrollando.

Creamos unos cuantos commits en la rama de desarrollo y por fin terminamos nuestra tarea. Estamos listos para incluir esos cambios en la rama principal. 

```bash
git merge main develop
```

Sin complicarnos, hacemos un *merge* normal y corriente. Todos los cambios de `develop` ahora están en `main`.