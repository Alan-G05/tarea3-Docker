# Tarea 3 - Docker

<h1 style="background-color: #c1c1c1; text-align: center">Docker</h1>

## ¿Qué es Docker?
<div style="text-align: justify">
Docker es una plataforma de software que permite crear, probar e implementar aplicaciones rápidamente mediante contenedores. Un contenedor es una unidad estándar de software que empaqueta el código y todas sus dependencias (como bibliotecas, compiladores y herramientas de graficación) para que la aplicación se ejecute de forma rápida y confiable desde un entorno informático a otro.
</div>

## ¿Qué es un Dockerfile?
<div style="text-align: justify">
Un Dockerfile es un archivo de texto plano que contiene una serie de instrucciones necesarias para generar una imagen que, posteriormente, se convertirá en una sola aplicación utilizada para un determinado propósito.
</div>

## Ventajas de los contenedores
* Modularidad
* Capas y control de versiones de imágenes
* Restauración
* Implementación rápida
* Consistencia multiplataforma

<h1 style="background-color: #c1c1c1; text-align: center">Configuración del Entorno (Ubuntu 22.04)</h1>
<div style="text-align: justify">

**1. Generación del Dockerfile**

Para garantizar que el entorno sea consistente, se utilizará un archivo Dockerfile. Este archivo define una imagen base de Ubuntu 22.04 con todas las dependencias necesarias instaladas (compiladores, Gnuplot, entre otras).
```
# 1. Imagen base estable
FROM ubuntu:22.04

# 2. Configuración de entorno no interactivo
ENV DEBIAN_FRONTEND=noninteractive

# 3. Dependencias gráficas y de compilación
RUN apt-get update && apt-get install -y \
build-essential \
g++ \
gnuplot \
xdg-utils \
libx11-dev \
&& rm -rf /var/lib/apt/lists/*

# 4. Directorio de trabajo genérico
WORKDIR /usr/src/app

# 5. Copiamos el contenido del proyecto
COPY . .

# 6. Variable para el comando por defecto
ARG MAKE_TARGET=all
ENV TARGET=$MAKE_TARGET

# 7. Variable de entorno para X11
ENV XDG_RUNTIME_DIR=/tmp

# 8. Ejecutamos el Makefile
CMD make ${TARGET}
```
**2. Construcción de la imagen**

Desde la terminal (PowerShell o bash) ubicada en la carpeta donde está el archivo Dockerfile (carpeta donde está Docker Desktop.exe), ejecutar el siguiente comando para generar la imagen base:
```
docker build -t ambiente_c_mas_mas .
```

</div>

<h1 style="background-color: #c1c1c1; text-align: center">Proyecto Julia (Conjunto de Julia)</h1>
<div style="text-align: justify">
Este proyecto implementa la generación y visualización del Conjunto de Julia, familia de fractales perteneciente al campo del Análisis Complejo.

El programa está dividido en dos etapas principales:


**1. Cálculo numérico (C++)**
- El archivo main.cpp calcula los valores del conjunto de Julia evaluando la divergencia de puntos en el plano complejo.
- El resultado se guarda en un archivo de texto (julia_set.txt).

**2. Visualización (Gnuplot)**
- El script julia_set.gp toma los datos generados y produce una imagen (julia_set.png) representando el fractal.

## ¿Cómo funciona el programa?
### 1.- Cálculo del fractal
Se recorre una cuadrícula de puntos en el plano (x, y).
Para cada punto, se evalúa una iteración del tipo:

$$z_{n+1} = z_n^2 + c$$

donde:
- c=−0.70176−0.3842i (constante del conjunto de Julia)
- Se calcula cuántas iteraciones tarda en divergir (si lo hace)
- Los datos se guardan como: x, y, iteraciones

Salida: julia_set.txt

### 2.- Generación de la imagen
Usa Gnuplot para leer el archivo .txt y genera una imagen en formato:
- PNG (por defecto)
- PDF o LaTeX (opcional)

Configura:
- Fondo negro
- Sin ejes
- Paleta de colores personalizada

Salida: julia_set.png

### 3.- Automatización con Makefile
El archivo Makefile conecta todo el flujo:

Cálculo → Archivo .txt → Imagen → Apertura automática

## Requisitos
Tener instalado Docker en el equipo donde se ejecutará el software.

## Instrucciones de uso
Al igual que en la actividad anterior, se ejecuta el contenedor desde la carpeta del proyecto. El volumen (-v) permite que el archivo resultados.dat generado dentro del contenedor aparezca en la carpeta local.

`docker run -ti --rm -v "${PWD}:/usr/src/app" ambiente_c_mas_mas`

Este comando invocará el Makefile y ejecutará todo el flujo automáticamente: compilación, cálculo de resultados y generación del archivo de datos.
## Explicación del Makefile
### Variables
- CC=gcc: Define el compilador de C.
- CFLAGS=-Wall: Aplica banderas para mostrar advertencias durante la compilación. 
- PROYECTO=programa y RESULTADOS=resultados.dat: Definen los nombres de los archivos para mantener consistencia y evitar escribirlos manualmente en múltiples lugares.
### Reglas
- all: Es el objetivo por defecto; compila el proyecto y ejecuta el programa.
- Compilación: Convierte automáticamente los archivos .c a .o y enlaza los objetos para generar el ejecutable.
- clean: Elimina todos los archivos generados (.o, .dat y el binario) para mantener el directorio limpio.
</div>