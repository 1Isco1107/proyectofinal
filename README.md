import time
class Cliente:
    def __init__(self, codigo, nombre, direccion, telefono):
        self.codigo = codigo
        self.nombre = nombre
        self.direccion = direccion
        self.telefono = telefono

class ClienteCredito(Cliente):
    contador=2
    def __init__(self, codigo, nombre, direccion, telefono, saldo_credito):
        ClienteCredito.contador += 1
        codigo="CC"+ str(ClienteCredito.contador).zfill(3)
        super().__init__(codigo, nombre, direccion, telefono)
        self.saldo_credito = saldo_credito

class ClienteContado(Cliente):
    contador=2
    def __init__(self, codigo, nombre, direccion, telefono,saldo_contado):
        ClienteContado.contador += 1
        codigo = "CT" + str(ClienteContado.contador).zfill(3)
        super().__init__(codigo, nombre, direccion, telefono)
        self.saldo_contado = saldo_contado

class Producto:
    
    def __init__(self, codigo_pro, detalle, precio_venta, stock):
        self.codigo_pro =codigo_pro
        self.detalle = detalle
        self.precio_venta = precio_venta
        self.stock = stock

class Venta:
    def __init__(self, nro_docto, cod_cli, codigo_pro, cantidad_vendida, precio_venta, fecha):
        self.nro_docto = nro_docto
        self.cod_cli = cod_cli
        self.codigoProd = codigo_pro
        self.cantidad_vendida = cantidad_vendida
        self.precio_venta = precio_venta
        self.fecha = fecha



class SistemaVentas:
    def __init__(self):
        self.clientes_credito = []
        self.clientes_contado = []
        self.productos = {}
        self.ventas = []
        self.contador_productos=4
    #clientes predefinidos credito
        cliente_credito1 = ClienteCredito("CC001", "Juan Pérez", "Calle 123", "555-1234", 500)
        cliente_credito2 = ClienteCredito("CC002", "María Gómez", "Avenida 456", "555-5678", 1000)
        self.clientes_credito.append(cliente_credito1)
        self.clientes_credito.append(cliente_credito2)
    #clientes predefinidos contado
        cliente_contado1 = ClienteContado("CT001", "Pedro Rodríguez", "Carrera 789", "555-9876", 200)
        cliente_contado2 = ClienteContado("CT002", "Laura Silva", "Avenida 654", "555-4321", 300)
        self.clientes_contado.append(cliente_contado1)
        self.clientes_contado.append(cliente_contado2)
    #productos predefinidos
        producto1 = Producto("P001", "Camisa", 25.99, 10)
        producto2 = Producto("P002", "Pantalón", 39.99, 5)
        producto3 = Producto("P003", "Zapatos", 59.99, 3)
        producto4 = Producto("P004", "Sombrero", 12.99, 8)

        self.productos[producto1.codigo_pro] = producto1
        self.productos[producto2.codigo_pro] = producto2
        self.productos[producto3.codigo_pro] = producto3
        self.productos[producto4.codigo_pro] = producto4



    
    def agregar_cliente(self):
        max_intentos = 5  
        intentos = 0  
        while intentos<max_intentos:
            tipo_cliente = input("Ingrese el tipo de cliente (C: Crédito, CT: Contado): ")
            tipo_cliente=tipo_cliente.upper()
            if tipo_cliente=="C" or tipo_cliente=="CT":
                break
            print("tipo de cliente invalido")
            intentos +=1
        if intentos==max_intentos:
            print("numero maximo de intentos,terminando el programa")
            return

        codigo = ""
        if codigo in self.clientes_credito or codigo in self.clientes_contado:
            print("El código de cliente ya existe.")
            return
        nombre = input("Ingrese el nombre del cliente: ")
        direccion = input("Ingrese la dirección del cliente: ")
        telefono = input("Ingrese el teléfono del cliente: ")
        try:
            if not telefono.startswith("+56"):
                raise ValueError("El número de teléfono debe comenzar con '+56'.")
            if len(telefono[3:]) != 9:
                raise ValueError("El número de teléfono debe tener 9 dígitos.")
            
            if tipo_cliente.upper() == "C":
                saldo_credito = float(input("Ingrese el saldo de crédito del cliente: "))
                cliente = ClienteCredito(codigo, nombre, direccion, telefono, saldo_credito)
                self.clientes_credito.append(cliente)
                codigo = cliente.codigo
            elif tipo_cliente.upper() == "CT":
                saldo_contado = float(input("ingrese el saldo del cliente: "))
                cliente = ClienteContado(codigo, nombre, direccion, telefono,saldo_contado)
                self.clientes_contado.append(cliente)
                codigo = cliente.codigo
            else:
                pass
            print("Cliente agregado correctamente.")
            print("Código de cliente:", codigo)
        except ValueError as e:
            print("Error:", e)

    def pagar_deuda(self, monto):
        if monto > 0:
            if monto > self.saldo_credito:
                excedente = monto - self.saldo_credito
                self.saldo_credito += excedente
                print("Deuda pagada correctamente.")
                print(f"El excedente de {excedente} se ha sumado al saldo.")
            else:
                self.saldo_credito -= monto
                print("Deuda pagada correctamente.")
        else:
            print("El monto debe ser mayor a cero.")

    def agregar_producto(self):
        self.contador_productos += 1
        codigo_pro = "P" + str(self.contador_productos).zfill(3)
        if codigo_pro in self.productos:
            print("El código de producto ya existe.")
            return
        detalle = input("Ingrese el detalle del producto: ")
        precio_venta = float(input("Ingrese el precio de venta del producto: "))
        stock = int(input("Ingrese la cantidad en stock del producto: "))
        producto = Producto(codigo_pro, detalle, precio_venta, stock)
        self.productos[codigo_pro] = producto
        print("Producto agregado correctamente.")
        print("Código de producto:", codigo_pro)

    def realizar_venta(self):
        nro_docto = input("Ingrese el número de documento de la venta: ")
        cod_cli = input("Ingrese el código del cliente: ")
        if not self.cliente_existente(cod_cli):
            print("El código de cliente no existe.")
            return

        codigoProd = input("Ingrese el código del producto: ")
        if codigoProd not in self.productos:
            print("El código de producto no existe.")
            return

        cantidad_vendida = int(input("Ingrese la cantidad vendida: "))

        cliente = self.obtener_cliente(cod_cli)
        producto = self.productos[codigoProd]

        if cantidad_vendida > producto.stock:
            print("No hay suficiente stock del producto.")
            return

        precio_venta = producto.precio_venta
        fecha = input("Ingrese la fecha de la venta (DD/MM/AAAA): ")

        if isinstance(cliente, ClienteCredito) and cliente.saldo_credito < precio_venta * cantidad_vendida:
            print("El cliente no tiene saldo suficiente para realizar la compra.")
            return

        venta = Venta(nro_docto, cod_cli, codigoProd, cantidad_vendida, precio_venta, fecha)
        self.ventas.append(venta)
        producto.stock -= cantidad_vendida
        if isinstance(cliente, ClienteCredito):
            cliente.saldo_credito -= precio_venta * cantidad_vendida

        print("Venta registrada correctamente.")

    def cliente_existente(self, codigo):
        for cliente in self.clientes_credito:
            if cliente.codigo == codigo:
                return True
        for cliente in self.clientes_contado:
            if cliente.codigo == codigo:
                return True
        return False

    def obtener_cliente(self, codigo):
        for cliente in self.clientes_credito:
            if cliente.codigo == codigo:
                return cliente
        for cliente in self.clientes_contado:
            if cliente.codigo == codigo:
                return cliente
        return None

    def consultar_saldo_credito(self):
        cod_cli = input("Ingrese el código del cliente: ")
        cliente = self.obtener_cliente(cod_cli)
        if cliente:
            if isinstance(cliente, ClienteCredito):
                print(f"El saldo de crédito del cliente es: {cliente.saldo_credito}")
            else:
                print("El cliente no tiene saldo de crédito.")
        else:
            print("El código de cliente no existe.")

    def pagar_deuda_cliente(self):
        cod_cli = input("Ingrese el código del cliente: ")
        cliente = self.obtener_cliente(cod_cli)
        if cliente:
            if isinstance(cliente, ClienteCredito):
                monto = float(input("Ingrese el monto a pagar: "))
                cliente.pagar_deuda(monto)
            else:
                print("El cliente no tiene saldo de crédito.")
        else:
            print("El código de cliente no existe.")

    def listar_ventas_cliente(self):
        cod_cli = input("Ingrese el código del cliente: ")
        if self.cliente_existente(cod_cli):
            ventas_cliente = [venta for venta in self.ventas if venta.cod_cli == cod_cli]
            if ventas_cliente:
                for venta in ventas_cliente:
                    print(f"NroDocto: {venta.nro_docto}, Producto: {venta.codigoProd}, Cantidad: {venta.cantidad_vendida}, Precio: {venta.precio_venta}, Fecha: {venta.fecha}")
            else:
                print("No hay ventas registradas para este cliente.")
        else:
            print("El código de cliente no existe.")

    def menu_principal(self):
        max_intentosm=3
        intentosm=0
        while True:
            print("\n--- El Boliche de la Esquina ---")
            print("\nOfrece a su querido publico lo siguiente:")
            print("1. Agregar Cliente")
            print("2. Agregar Producto")
            print("3. Realizar Venta")
            print("4. Consultar Saldo Crédito de Cliente")
            print("5. Listar Ventas de Cliente")
            print("6. Pagar Deuda de Cliente ")
            print("7. Salir")

            opcion = input("Ingrese una opción: ")
            if opcion == "1":
                self.agregar_cliente()
            elif opcion == "2":
                self.agregar_producto()
            elif opcion == "3":
                self.realizar_venta()
            elif opcion == "4":
                self.consultar_saldo_credito()
            elif opcion == "5":
                self.listar_ventas_cliente()
            elif opcion == "6":
                self.pagar_deuda_cliente()
            elif opcion == "7":
                break
            else:
                intentosm += 1
                if intentosm >= max_intentosm:
                    print("Número máximo de intentos. Autodestrucción iniciada.")
                    for i in range(5, 0, -1):
                        print(i)
                        time.sleep(1)
                    print("EXPLOTOOOOOOO")
                    print("Encontraste el easter egg")
                    return
                print("Opción inválida. Intente nuevamente.")

sistema_ventas = SistemaVentas()
sistema_ventas.menu_principal()
