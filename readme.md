# Proyecto 2: Carrito de Compras de Cursos en Línea

Este proyecto consiste en una aplicación web interactiva desarrollada con HTML5, CSS (Skeleton / Custom CSS) y JavaScript vainilla (ES6+). Permite a los usuarios explorar un catálogo de cursos, agregar elementos al carrito de compras, ajustar la cantidad de los cursos seleccionados, eliminar elementos individuales, vaciar el carrito completo y conservar el estado de la compra utilizando la API de `localStorage`.

---

## 📁 Estructura del Proyecto

Basada en la arquitectura de archivos del repositorio:

```text
CARRITOCOMPRAS/
├── css/
│   ├── custom.css
│   ├── normalize.css
│   └── skeleton.css
├── img/
│   ├── cart.png
│   ├── curso1.jpg
│   ├── curso2.jpg
│   ├── curso3.jpg
│   ├── curso4.jpg
│   ├── curso5.jpg
│   ├── estrellas.png
│   └── logo.jpg
├── js/
│   └── app.js
└── index.html
```

---

## 🚀 Características Principales

1. **Delegación de Eventos (Event Delegation):** Unificación de listeners en contenedores padre (`#lista-cursos` y `#carrito`) para optimizar el rendimiento y la manipulación dinámica del DOM.
2. **Persistencia de Datos:** Integración con `localStorage` para guardar los artículos agregados y mantener la lista recuperada tras recargar la página.
3. **Manejo de Inmutabilidad y Métodos Modernos de JS:** Uso de métodos como `.some()`, `.map()`, `.filter()` y el operador Spread (`...`) para actualizar el estado sin mutaciones accidentales.
4. **Diseño Adaptable:** Uso del framework ligero Skeleton CSS junto con un diseño limpio y limpio.

---

## 🛠️ Explicación del Código JavaScript (`js/app.js`)

### 1. Seleccionadores y Estado
```javascript
const carrito = document.querySelector('#carrito');
const listaCursos = document.querySelector('#lista-cursos');
const contenedorCarrito = document.querySelector('#lista-carrito tbody');
const vaciarCarritoBtn = document.querySelector('#vaciar-carrito'); 
let articulosCarrito = [];
```
Se seleccionan los elementos clave del DOM y se define `articulosCarrito` como un arreglo mutable que guardará los objetos de los cursos seleccionados.

---

### 2. Event Listeners y Carga Inicial
```javascript
cargarEventListeners();

function cargarEventListeners() {
    listaCursos.addEventListener('click', agregarCurso);
    carrito.addEventListener('click', eliminarCurso);
    vaciarCarritoBtn.addEventListener('click', vaciarCarrito);

    document.addEventListener('DOMContentLoaded', () => {
        articulosCarrito = JSON.parse(localStorage.getItem('carrito')) || [];
        carritoHTML();
    });
}
```
* **`DOMContentLoaded`:** Recupera los cursos almacenados previamente en `localStorage`. Si no existe nada, asigna un arreglo vacío `[]` y renderiza el carrito en pantalla.

---

### 3. Captura y Extracción de Datos
```javascript
function agregarCurso(e) {
    e.preventDefault();
    if (e.target.classList.contains('agregar-carrito')) {
        const curso = e.target.parentElement.parentElement;
        leerDatosCurso(curso);
    }
}

function leerDatosCurso(curso) {
    const infoCurso = {
        imagen: curso.querySelector('img').src,
        titulo: curso.querySelector('h4').textContent,
        precio: curso.querySelector('.precio span').textContent,
        id: curso.querySelector('a').getAttribute('data-id'), 
        cantidad: 1
    };

    // Actualiza cantidad o agrega nuevo elemento
    if (articulosCarrito.some(curso => curso.id === infoCurso.id)) { 
        const cursos = articulosCarrito.map(curso => {
            if (curso.id === infoCurso.id) {
                let cantidad = parseInt(curso.cantidad);
                cantidad++;
                curso.cantidad = cantidad;
                return curso;
            } else {
                return curso;
            }
        });
        articulosCarrito = [...cursos];
    } else {
        articulosCarrito = [...articulosCarrito, infoCurso];
    }

    carritoHTML();
}
```
* Extrae la información necesaria (imagen, título, precio e ID) leyendo los nodos hijos de la tarjeta HTML elegida.
* Verifica si el curso ya existía en el carrito usando `.some()`. De ser así, incrementa la propiedad `cantidad`. De lo contrario, lo inserta.

---

### 4. Eliminación y Renderizado
```javascript
function eliminarCurso(e) {
    e.preventDefault();
    if (e.target.classList.contains('borrar-curso')) {
        const curso = e.target.parentElement.parentElement;
        const cursoId = curso.querySelector('a').getAttribute('data-id');
        
        articulosCarrito = articulosCarrito.filter(curso => curso.id !== cursoId);
        carritoHTML();
    }
}

function carritoHTML() {
    vaciarCarrito();

    articulosCarrito.forEach(curso => {
        const row = document.createElement('tr');
        row.innerHTML = `
            <td><img src="${curso.imagen}" width=100></td>
            <td>${curso.titulo}</td>
            <td>${curso.precio}</td>
            <td>${curso.cantidad}</td>
            <td><a href="#" class="borrar-curso" data-id="${curso.id}">X</a></td>
        `;
        contenedorCarrito.appendChild(row);
    });

    sincronizarStorage();
}

function vaciarCarrito() {
    while (contenedorCarrito.firstChild) {
        contenedorCarrito.removeChild(contenedorCarrito.firstChild);
    }
}

function sincronizarStorage() {
    localStorage.setItem('carrito', JSON.stringify(articulosCarrito));
}
```
* **`vaciarCarrito()`:** Limpia eficientemente el contenedor `<tbody>` iterando con un ciclo `while` sobre el primer hijo antes de volver a construir la tabla.
* **`sincronizarStorage()`:** Serializa el arreglo `articulosCarrito` mediante `JSON.stringify` y lo guarda localmente.

---

## ⚡ Instalación y Ejecución

1. Clona o descarga el repositorio localmente.
2. Mantén la estructura de carpetas mostrada (`css/`, `img/`, `js/`).
3. Abre el archivo `index.html` en tu navegador web preferido o ejecuta un servidor local (por ejemplo, con la extensión *Live Server* de VS Code).