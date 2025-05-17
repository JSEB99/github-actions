# GitHub Actions

> Profesor: Fernando. Contacto: fernando93d@gmail.com

---

## Módulo 1 - Introducción a GitHub Actions

### ¿Qué es CI/CD?

- Integración continua: integrar cambios de manera frecuente
  - Integrar cambios de forma continua
  - Automatización, asegurarse que los cambios no rompan el proyecto *(mediante pruebas unitarias)*
- Despliegue continuo: extensión de **CI**, donde los cambios son desplegados a producción *(entornos productivos)*

#### Herramientas 

- Jenkins *(Open Source)*
- GitHub Actions: permite automatizar flujos de trabajo desde el repositorio
  - Administrado por GitHub
  - Curva de aprendizaje rapida

### Conceptos

1. `Workflows`: Archivo en formato `YAML` que define una serue de acciones que se ejecutan automáticamente en respuesta a eventos
2. `Eventos/Triggers`: Un disparador que inicia un **workflow**
3. `Jobs`: Una unidad de trabajo dentro de un workflow. Un workflow puede tener varios jobs, y cada uno de ellos **puede ejecutarse en paralelo o secuencialmente**
4. `Step`: Una **acción individual** dentro de un job
5. `Actions`: Un **bloque de código reutilizable** que realiza una tarea especificada en un step
6. `Runners`: Un **entorno** donde se ejecutan los jobs

### Intro a YAML

> Reglas claras y directas

- No usa bloques delimitados, **USA IDENTACIONES**

*Ejemplo:*

```YAML
# strings sin comillas o con comillas (ambos sirven)
key: string con espacios sin comillas
# identación
person:
    name: John
    age: 30
    city: Oklahoma
    # Lista
    items:
        - item1
        - item2
        - item3
    # Cada elemento 1 valor
    items2:
        - item1: valor1
        - item2: valor2
        - item3: valor3
# Multilinea
scripts: >
    primera linea
    segunda linea
    tercera linea
# Multilinea v2
scripts: |
    touch hola.txt
    echo "Hola";

val:
    # Si no agregamos un valor (le agrega un val por defecto)
    # Dependera de la convención
    val:
```

Verificar si el `yaml` esta bien: [YAML LINT](https://www.yamllint.com/)

> Depende de la versión de YAML

---

## Módulo 2 - Configuración Básica

### Primer Workflow

```yml
name: Hola mundo

on: [push] # Se ejecute cuando hago push

jobs:
  hola: #Nombre representativo
    runs-on: ubuntu-latest # SO de ejecución

    steps:
    - name: Hola Mundo 
      run: echo "Hola Mundo"
```

Una vez en GitHub aparecera un punto donde si le damos clic:

![pending-success](./images/pending-success.png)

Nos llevara al actions, donde si damos en `details` podremos ver la ejecución de los pasos realizados en actions de acuerdo al `yaml` que configuramos.

### Jobs & Steps

> En la pestana `Actions` en GitHub podremos ver el historial

Dentro en un workflow podremos tener **multiples jobs**, entonces en el anterior añadiremos los siguiente

```yml
name: Hola mundo

on: [push] # Se ejecute cuando hago push

jobs:
  hola: #Nombre representativo
    runs-on: ubuntu-latest # SO de ejecución

    steps:
    - name: Hola Mundo 
      run: echo "Hola Mundo"

  other:
    runs-on: ubuntu-latest
    steps:
    - name: LS
      run: ls -al
```

Si seleccionamos la acción en la pestaña `Actions` veremos lo siguiente:

![actions-jobs](./images/jobs.png)

Donde podremos ver que hace cada job seleccionandolo en la pestaña de la izquierda, ahora procederemos a realizar mas acciones:

```yml
name: Hola mundo

on: [push] # Se ejecute cuando hago push

jobs:
  hola: #Nombre representativo
    runs-on: ubuntu-latest # SO de ejecución

    steps:
    - name: Hola Mundo 
      run: echo "Hola Mundo"

    - name: Touch file
      run: touch hola.txt

    - name: Set text
      run : echo "Hola Mundo" >> hola.txt

    - name: Read file
      run: cat hola.txt
  
  other:
    runs-on: ubuntu-latest
    steps:
    - name: LS 
      run: ls -al
```

Donde añadimos la creación de un archivo, envío de texto al archivo y lectura del archivo. *Entonces subimos al repositorio...* y veremos lo siguiente:

![github-actions-pipeline](./images/github-pipe.png)

> **Nota**: los jobs se ejecutan de manera independiente y paralela, *se pueden generar dependencias para que un job se ejecute despues de otro*

**Generando dependencia**

En el job other agregaremos lo siguiente:

```yml
  other:
    runs-on: ubuntu-latest
    needs: hola # dependencia del job hola, tambien se puede definir asi [hola]
    steps:
    - name: LS
      run: ls -al
```

Por lo que ahora el esquema sería de la siguiente manera:

![actions-dependency](./images/actions-dependency.png)

> **Aviso** aparece cargandose porque aun no terminaba de ejecutarse

### Eventos

Los eventos serán los disparadores *(Triggers)* que van a darle **indicación para ejecutar el workflow**, en el anterior ejemplo usamos el evento `Push`, es decir, que el disparador será cada que se realice un push al repositorio. [Documentación eventos](https://docs.github.com/es/actions/writing-workflows/choosing-when-your-workflow-runs/events-that-trigger-workflows)

Entonces realizamos lo siguiente:
1. nueva rama `pr`
2. nuevo archivo
3. push a la rama
4. pr

Luego si queremos hacer que esto sea de disparador le pondremos `pull_request` de la siguiente forma:

```yml
on: [push, pull_request] # Funciona con ambos
```