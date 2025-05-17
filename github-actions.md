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

