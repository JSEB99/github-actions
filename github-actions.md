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

Actualizando la página de la pr donde estabamos, tendremos lo siguiente:

![pr-action](./images/pr-actions.png)

Donde vemos que se ejecuto el workflow que teniamos programado en el archivo `yaml`.

**¿Cómo hago para que el disparador sea de acuerdo a una rama?**

Tendremos que definir a que rama se le aplicará cada acción:

```yml
on:
  push:
    branches:
      - main # Puede ser una lista
  pull_request:
    branches:
      - main
```

> **Nota**: En este caso se realiza la acción debido a que se realiza un *pull request sobre la rama main*

### Actions

Bloque de código **reutilizable** que realiza una tarea en especifico en un step.

> Agrupar todos los steps que tenemos y utilizarlos en diferentes workflows

GitHub actions tambien dispone de diferentes acciones ya preestablecidas en su marketplace: [Actions marketplace](https://github.com/marketplace?type=actions)

Entonces, como ejemplo usaremos una que ya esta llamada `checkout`, para ello en el marketplace>buscamos la acción>le damos en *use latest version* y copiamos el código:

![checkout-action](./images/checkout-action.png)

Entonces en la parte de los **steps** podremos pegar el código quedando asi:

```YML
other:
    runs-on: ubuntu-latest
    needs: hola

    steps:
    - name: Checkout
      uses: actions/checkout@v4.2.2
```

De esta manera obtenemos ubicarnos en el repositorio, de tal manera que cuando hagamos el *ls* si nos retorne los archivos del repositorio

![checkout-exe](./images/checkout-exe.png)

### Instalación de paquetes

A veces un problema que se puede presentar es el uso de scripts multilinea, para ello podemos usar `|` para generar un string multilinea

```yml
name: Test

on: [push]

jobs:
  test-build:
    runs-on: ubuntu-latest

    steps:
      - name: ls
        run: |
          echo "Hola Mundo" >> hola.txt
          cat hola.txt
```

Entonces cuando hacemos push subimos todo lo agregado al repositorio, de esta manera si generamos scripts en el repositorio podremos usarlos con el workflow

```sh
sudo apt-get update

sudo apt-get install curl
```

Para poder acceder a ese script, primero usaremos `checkout` para poder ver el repositorio y posteriormente usar el script, entonces le daremos permisos de ejecución y procederemos a invocarlo.

```yml
name: Test

on: 
  push:

jobs:
  test-build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4.2.2
      - name: ls
        run: |
          chmod +x script.sh
          ./script.sh
```

Agregando la bandera `-y` a la instalación del curl para aceptar todos los paquetes que se instalan

```sh
sudo apt-get install curl -y
```

### Uso de Actions

Poder configurar nuestro entorno, ejemplo tendremos que instalar node. **Idealmente,** se debería buscar en el marketplace si ya esta esa acción. Entonces, la buscamos [node environment](https://github.com/marketplace/actions/setup-node-js-environment) y procedemos a usarla

```yml
name: Hola mundo

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

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
    - name: Checkout
      uses: actions/checkout@v4.2.2
    - name: LS 
      run: ls -al
    - name: Setup Node.js environment
      uses: actions/setup-node@v4.4.0
    - name: node version
      run: node --version
```

Agregamos node y revisamos si se instalo mediante `node --version`, ademas mediante los pasos podemos agregar un atributo `with` que nos permitira **enviarle parametros** al comando, entonces añadiremos lo siguiente al step del ambiente

```yml
    - name: Setup Node.js environment
      uses: actions/setup-node@v4.4.0
      with:
        node-version: latest
    - name: node version
      run: node --version
    # instalando dependencias
    - name: instalar dependencias
      run: npm install
```

> **Advertencia:** node necesita el *package.json* por lo que da error.

Donde le especificamos que queremos la ultima versión de node, ademas ya con *node instalado* procedemos a instalar la dependencia de **npm**. Ahora procederemos a instalar `php` por ejemplo, para ello agregamos lo siguiente al final

```yml
    - name: PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.3'
    - name: php version
      run: php --version
```

### Actions Reutilizar

Pero como puedo yo utilizar estos bloques de código personalizados, para ello crearemos una carpeta en `.github` crearemos una carpeta llamada `actions` y dentro podemos tener subcarpetas de las acciones y cada una tendra su archivo `yml` Quedando algo asi `.github/actions/my-action/action.yml`, ya en nuestra acción tendremos lo siguiente

```yml
name: "My action"
desciption: "Test my action"

runs:
  using: "composite" # acciones de tipo composite permiten agrupar pasos comunes

  steps:
    - name: Hola Mundo
      run: echo "Hola Mundo"
      shell: bash
```

**importante** usar la directiva `shell: bash` para poder ejecutar acciones de tipo `composite`.

> La acción de tipo composite nos permitira agrupar y reutilizar neustras acciones

Para poder usarla iremos a nuestro yml del workflow quedando de la siguiente manera:

```yml
name: Test

on:
  push:

jobs:
  test-build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4.2.2

      - name: ls
        uses: ./.github/actions/my-action
```

> No es necesario pasarle el archivo ya que el por defecto lo va a buscar

### Parámetros

Como vimos podemos pasarle parametros a la acción, esto se puede hacer tambien con los compostie personalizados, para ello iremos a la acción, donde definiremos la directiva que se llama inputs, le definimos el nombre del parametro *(podemos definir que hace, valores que lleva, etc)*.

Para poder acceder a ellas en el step nos apoyamos en los contextos, **para ello accedemos directamente a los inputs y de ahí al apartado que queramos**

```yml
name: "My action"
desciption: "Test my action"

inputs:
  message:
    description: "The message to print"
    required: true
    default: "Hola" # Valor por defectos

runs:
  using: "composite" # acciones de tipo composite permiten agrupar pasos comunes

  steps:
    - name: Hola Mundo
      run: echo "Hola ${{ inputs.message }}"
      shell: bash
```

En cuanto al workflow, le pasaremos el nombre de la variable con el mensaje

```yml
name: Test

on:
  push:

jobs:
  test-build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4.2.2

      - name: ls
        uses: ./.github/actions/my-action
        with:
          message: "SebDev"
```

### Servicios

De vez en cuando necesitamos instalar servicios para que se ejecute de manera correcta, tales como, *MySQL, Redis, etc*. Para ese tipo de casos, se nos permite utilizar contenedores de `Docker`. Entonces en nuestro workflow

```yml
name: Ejemplo de Composite action

on: [push]

jobs:
  say-hello:
    runs-on: ubuntu-latest

    services:
      mysql: # Nombre de servicio
        image: mysql:5.7 # imagen de mysql (dockerhub)
        env: # Ambiente
          MYSQL_ROOT_PASSWORD: example
        ports:
          - 3306:3306
    # Definir steps sino arroja ERROR
    steps:
      - name: Test
        run: pwd
```

> **Advertencia**: Definir los steps para que no arroje error