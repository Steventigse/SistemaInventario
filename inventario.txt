import json
import os
# ==============================
# Clase Producto
# ==============================

class Producto:
    def __init__(self, id_producto, nombre, cantidad, precio):
        self.__id = id_producto
        self.__nombre = nombre
        self.__cantidad = cantidad
        self.__precio = precio

    # ===== Getters =====
    def get_id(self):
        return self.__id

    def get_nombre(self):
        return self.__nombre

    def get_cantidad(self):
        return self.__cantidad

    def get_precio(self):
        return self.__precio

    # ===== Setters =====
    def set_nombre(self, nombre):
        self.__nombre = nombre

    def set_cantidad(self, cantidad):
        self.__cantidad = cantidad

    def set_precio(self, precio):
        self.__precio = precio

    # Convertir a diccionario (para guardar en archivo)
    def to_dict(self):
        return {
            "id": self.__id,
            "nombre": self.__nombre,
            "cantidad": self.__cantidad,
            "precio": self.__precio
        }
# ==============================
# Clase Inventario
# ==============================

class Inventario:
    def __init__(self):
        # Diccionario para almacenar productos
        # Clave: ID del producto
        # Valor: Objeto Producto
        self.productos = {}

    # Agregar producto
    def agregar_producto(self, producto):
        if producto.get_id() in self.productos:
            print("❌ El producto ya existe.")
        else:
            self.productos[producto.get_id()] = producto
            print("✅ Producto agregado correctamente.")

    # Eliminar producto por ID
    def eliminar_producto(self, id_producto):
        if id_producto in self.productos:
            del self.productos[id_producto]
            print("✅ Producto eliminado.")
        else:
            print("❌ Producto no encontrado.")

    # Actualizar cantidad o precio
    def actualizar_producto(self, id_producto, cantidad=None, precio=None):
        if id_producto in self.productos:
            if cantidad is not None:
                self.productos[id_producto].set_cantidad(cantidad)
            if precio is not None:
                self.productos[id_producto].set_precio(precio)
            print("✅ Producto actualizado.")
        else:
            print("❌ Producto no encontrado.")

    # Buscar por nombre
    def buscar_por_nombre(self, nombre):
        encontrados = [
            producto for producto in self.productos.values()
            if nombre.lower() in producto.get_nombre().lower()
        ]

        if encontrados:
            for producto in encontrados:
                self.mostrar_producto(producto)
        else:
            print("❌ No se encontraron productos.")

    # Mostrar producto individual
    def mostrar_producto(self, producto):
        print(f"ID: {producto.get_id()} | "
              f"Nombre: {producto.get_nombre()} | "
              f"Cantidad: {producto.get_cantidad()} | "
              f"Precio: ${producto.get_precio():.2f}")

    # Mostrar todos los productos
    def mostrar_todos(self):
        if not self.productos:
            print("📦 Inventario vacío.")
        else:
            for producto in self.productos.values():
                self.mostrar_producto(producto)
    def valor_total_inventario(self):
        total = sum(
            producto.get_cantidad() * producto.get_precio()
            for producto in self.productos.values()
            )
            print(f"💰 Valor total del inventario: ${total:.2f}")
    # ==============================
    # Guardar inventario en archivo
    # ==============================
    def guardar_en_archivo(self, archivo="inventario.json"):
        datos = [producto.to_dict() for producto in self.productos.values()]
        
        with open(archivo, "w") as f:
            json.dump(datos, f, indent=4)
        
        print("💾 Inventario guardado correctamente.")

    # ==============================
    # Cargar inventario desde archivo
    # ==============================
    def cargar_desde_archivo(self, archivo="inventario.json"):
        if os.path.exists(archivo):
            with open(archivo, "r") as f:
                datos = json.load(f)
                
                for item in datos:
                    producto = Producto(
                        item["id"],
                        item["nombre"],
                        item["cantidad"],
                        item["precio"]
                    )
                    self.productos[producto.get_id()] = producto
            
            print("📂 Inventario cargado correctamente.")
        else:
            print("⚠ No existe archivo previo. Se creará uno nuevo.")
if __name__ == "__main__":
    inventario = Inventario()
    inventario.cargar_desde_archivo()

    while True:
        print("\n====== SISTEMA DE INVENTARIO ======")
        print("1. Agregar producto")
        print("2. Eliminar producto")
        print("3. Actualizar producto")
        print("4. Buscar producto por nombre")
        print("5. Mostrar todos los productos")
        print("6. Mostrar valor total del inventario")
        print("7. Guardar y salir")

        opcion = input("Seleccione una opción: ")

        if opcion == "1":
            id_producto = input("ID: ")
            nombre = input("Nombre: ")
            cantidad = int(input("Cantidad: "))
            precio = float(input("Precio: "))

            producto = Producto(id_producto, nombre, cantidad, precio)
            inventario.agregar_producto(producto)

        elif opcion == "2":
            id_producto = input("Ingrese ID a eliminar: ")
            inventario.eliminar_producto(id_producto)

        elif opcion == "3":
            id_producto = input("Ingrese ID a actualizar: ")
            cantidad = input("Nueva cantidad (Enter para omitir): ")
            precio = input("Nuevo precio (Enter para omitir): ")

            if cantidad:
                try:
                    cantidad = int(cantidad)
                except ValueError:
                    print("❌ Cantidad inválida.")
                    continue
            else:
                cantidad = None

            if precio:
                try:
                    precio = float(precio)
                except ValueError:
                    print("❌ Precio inválido.")
                    continue
            else:
                precio = None

            inventario.actualizar_producto(id_producto, cantidad, precio)

        elif opcion == "4":
            nombre = input("Ingrese nombre a buscar: ")
            inventario.buscar_por_nombre(nombre)

        elif opcion == "5":
            inventario.mostrar_todos()

        elif opcion == "6":
            inventario.valor_total_inventario()

        elif opcion == "7":
            inventario.guardar_en_archivo()
            print("👋 Saliendo del sistema...")
            break

        else:
            print("❌ Opción inválida.")