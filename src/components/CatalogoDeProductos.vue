<template>
  <h1>Catálogo de Productos</h1>
  <button @click="cambiarCSS" class="boton">Cambiar tema a {{ tema }}</button>
  <div>
    <input v-model="busqueda" @input="mostrarProductoBusqueda" placeholder="Buscar productos..." class="barraBusqueda" />
    <button @click="ordenarPorNombre" class="boton">Ordenar por Nombre</button>
    <select v-model="categoriaSeleccionada" class="seleccionCategoria">
      <option value="">Todas las categorías</option>
      <option value="electronics">Electrónica</option>
      <option value="jewelery">Joyería</option>
      <option value="men's clothing">Ropa de hombre</option>
      <option value="women's clothing">Ropa de mujer</option>
    </select>
    <button @click="mostrarCesta" class="boton">
      {{ estadoCarrito }} carrito
    </button>

    <div v-if="isVisibleCesta">
      <h2>Artículos en el carrito</h2>
      <ul>
        <li v-for="item in listadoCompra" :key="item.id">
          {{ item.title }} - {{ item.price }} EUR
          <button @click="eliminarProducto(item.title)" class="boton">
            Eliminar
          </button>
        </li>
      </ul>
    </div>

    <table v-if="isVisibleProductos" class="productoTabla">
      <tr v-for="item in listadoAux" :key="item.id">
        <td>
          <img
            :src="item.image"
            alt="Imagen del producto"
            class="productoImagen"
          />
        </td>
        <td>{{ item.title }}</td>
        <td>{{ item.price }} EUR</td>
        <td>{{ item.category }}</td>
        <td>
          <button @click="añadirCompra(item.title)" class="boton">
            Comprar
          </button>
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "CatalogoDeProductos",
  data() {
    return {
      busqueda: "", // Texto de búsqueda en la barra
      listado: [], // Lista original de productos
      listadoAux: [], // Lista filtrada y ordenada
      listadoCompra: [], // Productos añadidos al carrito
      tema: "oscuro", // Tema actual (claro u oscuro)
      isVisibleCesta: false, // Control de visibilidad del carrito
      isVisibleProductos: true, // Control de visibilidad de los productos
      categoriaSeleccionada: "", // Categoría seleccionada
      estadoCarrito: "Ver", // Texto que indica el estado del carrito
    };
  },
  mounted() {
    axios
      .get("https://fakestoreapi.com/products")
      .then((response) => {
        this.listado = response.data;
        this.listadoAux = [...this.listado];
      })
      .catch((error) => {
        console.error("Hubo un error:", error);
      });
  },
  watch: {
    categoriaSeleccionada(nuevaCategoria) {
      if (nuevaCategoria) {
        this.listadoAux = this.listado.filter(
          (producto) => producto.category === nuevaCategoria
        );
      } else {
        this.listadoAux = [...this.listado];
      }
    },
  },
  methods: {
    cambiarCSS() {
      document.body.classList.toggle("oscuro");
      if (this.tema === "oscuro") {
        this.tema = "claro";
      } else {
        this.tema = "oscuro";
      }
    },
    mostrarProductoBusqueda() {
      const busquedaEnMinusculas = this.busqueda.toLowerCase();
      this.listadoAux = this.listado.filter((producto) =>
        producto.title.toLowerCase().includes(busquedaEnMinusculas)
      );
    },
    añadirCompra(articulo) {
      let producto = this.listado.find((item) => item.title === articulo);
      if (producto && !this.listadoCompra.includes(producto)) {
        this.listadoCompra.push(producto);
      }
    },
    eliminarProducto(articulo) {
      const producto = this.listadoCompra.find(
        (item) => item.title === articulo
      );
      if (producto) {
        this.listadoCompra.splice(this.listadoCompra.indexOf(producto), 1);
      }
    },
    mostrarCesta() {
      this.isVisibleProductos = !this.isVisibleProductos; // Alternar visibilidad de productos
      this.isVisibleCesta = !this.isVisibleCesta; // Alternar visibilidad del carrito

      // Actualizar el texto del botón según el estado del carrito
      this.estadoCarrito = this.isVisibleCesta ? "Ocultar" : "Ver";
    },
    ordenarPorNombre() {
      this.listadoAux.sort((a, b) => a.title.localeCompare(b.title));
    },
  },
};
</script>

<style scoped>
body {
  background-color: white;
  color: black;
  font-family: Arial, sans-serif;
  margin: 10px;
  padding: 0;
  overflow-x: hidden;
}

body.oscuro {
  background-color: black;
  color: white;
}

body.oscuro .productoTabla,
body.oscuro .boton,
body.oscuro .seleccionCategoria {
  background-color: rgb(40, 40, 40);
  color: white;
}

body.oscuro .boton:hover {
  background-color: #909091;
  color: white;
}

h1 {
  text-align: center;
  font-size: 32px;
  margin-bottom: 20px;
}

.boton {
  padding: 10px 20px;
  margin: 10px;
  background-color: white;
  color: black;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.5s ease, transform 0.5s ease, color 0.5s ease;
}

.boton:hover {
  background-color: #909091;
  color: white;
  transform: scale(1.1);
}

.productoTabla {
  width: 80%;
  margin: 20px auto;
  border-collapse: collapse;
  background-color: white;
  border-radius: 5px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.productoTabla th,
.productoTabla td {
  padding: 15px;
  text-align: left;
  border-bottom: 1px solid #ccc;
}

.productoTabla th {
  background-color: #f7f7f7;
  font-weight: bold;
}

.productoTabla tr:hover {
  background-color: #a5a5a5;
  transition: background-color 0.5s ease, transform 0.5s ease, color 0.5s ease;
  color: white;
  transform: scale(1.05);
}

.productoImagen {
  width: 150px;
  height: 150px;
  object-fit: cover;
  border-radius: 5px;
  transition: transform 0.5s ease, opacity 2s ease;
}

.productoImagen:hover {
  transform: scale(1.1);
  opacity: 0.8;
  border: 3px solid #3498db;
}

.barraBusqueda {
  width: 100%;
  max-width: 300px;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f7f7f7;
  transition: border-color 0.5s ease, box-shadow 0.5s ease;
}

.barraBusqueda:hover,
.barraBusqueda:focus {
  border-color: #3498db;
  box-shadow: 0 0 5px rgba(52, 152, 219, 0.5);
}

.seleccionCategoria {
  width: 100%;
  max-width: 300px;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f7f7f7;
  transition: border-color 0.5s ease, box-shadow 0.5s ease;
}

.seleccionCategoria:hover,
.seleccionCategoria:focus {
  border-color: #3498db;
  box-shadow: 0 0 5px rgba(52, 152, 219, 0.5);
}

ul {
  list-style-type: none;
  padding: 0;
}

@media (max-width: 768px) {
  .productoTabla {
    display: block;
    width: 100%;
    overflow-x: auto;
  }

  .productoTabla tr {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .productoTabla td {
    display: block;
    text-align: center;
  }
}

@media (max-width: 480px) {
  h1 {
    font-size: 24px;
  }

  .productoImagen {
    max-width: 100px;
  }

  .boton {
    width: 100%;
    font-size: 14px;
    padding: 10px;
  }
}
</style>
