"""
Sistema Integral de Gestión de Clientes, Servicios y Reservas
Empresa: Software FJ
Curso: Programación

Para ejecutar:
    python software_fj_unico.py
"""
from abc import ABC, abstractmethod
from datetime import datetime

# ============================================================
# #Estructura base del sistema, clases abstractas, clientes,
# servicios, reservas y listas internas.
# ============================================================


class EntidadSistema(ABC):
    """
    Clase abstracta base para entidades generales del sistema.
    Demuestra abstracción.
    """

    def __init__(self, identificador):
        self._identificador = identificador

    @property
    def identificador(self):
        return self._identificador

    @abstractmethod
    def mostrar_info(self):
        pass


class Cliente(EntidadSistema):
    """
    Clase Cliente con encapsulación y validaciones básicas.
    """

    def __init__(self, identificador, nombre, correo, telefono):
        super().__init__(identificador)
        self._nombre = nombre
        self._correo = correo
        self._telefono = telefono

    @property
    def nombre(self):
        return self._nombre

    @property
    def correo(self):
        return self._correo

    @property
    def telefono(self):
        return self._telefono

    def mostrar_info(self):
        return (
            f"Cliente #{self.identificador} | "
            f"Nombre: {self.nombre} | "
            f"Correo: {self.correo} | "
            f"Teléfono: {self.telefono}"
        )


class Servicio(EntidadSistema):
    """
    Clase abstracta para servicios.
    Las clases hijas implementan cálculo de costo.
    Demuestra abstracción y polimorfismo.
    """

    def __init__(self, identificador, nombre, tarifa_base):
        super().__init__(identificador)
        self._nombre = nombre
        self._tarifa_base = tarifa_base

    @property
    def nombre(self):
        return self._nombre

    @property
    def tarifa_base(self):
        return self._tarifa_base

    @abstractmethod
    def calcular_costo(self, duracion_horas):
        pass

    def mostrar_info(self):
        return (
            f"Servicio #{self.identificador} | "
            f"Nombre: {self.nombre} | "
            f"Tarifa base: ${self.tarifa_base}"
        )


class ReservaSala(Servicio):
    """
    Servicio especializado para reserva de salas.
    """

    def __init__(self, identificador, nombre, tarifa_base, capacidad):
        super().__init__(identificador, nombre, tarifa_base)
        self._capacidad = capacidad

    @property
    def capacidad(self):
        return self._capacidad

    def calcular_costo(self, duracion_horas):
        recargo_capacidad = 20000 if self.capacidad > 20 else 0
        return self.tarifa_base * duracion_horas + recargo_capacidad

    def mostrar_info(self):
        return super().mostrar_info() + f" | Capacidad: {self.capacidad} personas"


class AlquilerEquipo(Servicio):
    """
    Servicio especializado para alquiler de equipos.
    """

    def __init__(self, identificador, nombre, tarifa_base, tipo_equipo):
        super().__init__(identificador, nombre, tarifa_base)
        self._tipo_equipo = tipo_equipo

    @property
    def tipo_equipo(self):
        return self._tipo_equipo

    def calcular_costo(self, duracion_horas):
        seguro = 15000
        return self.tarifa_base * duracion_horas + seguro

    def mostrar_info(self):
        return super().mostrar_info() + f" | Equipo: {self.tipo_equipo}"


class AsesoriaEspecializada(Servicio):
    """
    Servicio especializado para asesorías.
    """

    def __init__(self, identificador, nombre, tarifa_base, area):
        super().__init__(identificador, nombre, tarifa_base)
        self._area = area

    @property
    def area(self):
        return self._area

    def calcular_costo(self, duracion_horas):
        recargo_especialista = 30000
        return self.tarifa_base * duracion_horas + recargo_especialista

    def mostrar_info(self):
        return super().mostrar_info() + f" | Área: {self.area}"


class Reserva:
    """
    Clase que integra cliente, servicio, duración y estado.
    """

    ESTADOS_VALIDOS = ["pendiente", "confirmada", "cancelada"]

    def __init__(self, identificador, cliente, servicio, duracion_horas):
        self._identificador = identificador
        self._cliente = cliente
        self._servicio = servicio
        self._duracion_horas = duracion_horas
        self._estado = "pendiente"
        self._fecha_creacion = datetime.now()

    @property
    def identificador(self):
        return self._identificador

    @property
    def cliente(self):
        return self._cliente

    @property
    def servicio(self):
        return self._servicio

    @property
    def duracion_horas(self):
        return self._duracion_horas

    @property
    def estado(self):
        return self._estado

    @property
    def fecha_creacion(self):
        return self._fecha_creacion

    def confirmar(self):
        self._estado = "confirmada"

    def cancelar(self):
        self._estado = "cancelada"

    def calcular_total(self):
        return self.servicio.calcular_costo(self.duracion_horas)

    def mostrar_info(self):
        return (
            f"Reserva #{self.identificador} | "
            f"Cliente: {self.cliente.nombre} | "
            f"Servicio: {self.servicio.nombre} | "
            f"Duración: {self.duracion_horas} hora(s) | "
            f"Estado: {self.estado} | "
            f"Total: ${self.calcular_total():,.0f}"
        )


class SistemaGestion:
    """
    Clase principal que maneja listas internas.
    No utiliza bases de datos.
    """

    def __init__(self):
        self.clientes = []
        self.servicios = []
        self.reservas = []

    def agregar_cliente(self, cliente):
        self.clientes.append(cliente)

    def agregar_servicio(self, servicio):
        self.servicios.append(servicio)

    def agregar_reserva(self, reserva):
        self.reservas.append(reserva)

    def buscar_cliente(self, identificador):
        for cliente in self.clientes:
            if cliente.identificador == identificador:
                return cliente
        return None

    def buscar_servicio(self, identificador):
        for servicio in self.servicios:
            if servicio.identificador == identificador:
                return servicio
        return None

    def buscar_reserva(self, identificador):
        for reserva in self.reservas:
            if reserva.identificador == identificador:
                return reserva
        return None

    def listar_clientes(self):
        for cliente in self.clientes:
            print(cliente.mostrar_info())

    def listar_servicios(self):
        for servicio in self.servicios:
            print(servicio.mostrar_info())

    def listar_reservas(self):
        for reserva in self.reservas:
            print(reserva.mostrar_info())

# ============================================================
# Excepciones personalizadas, logs, validaciones robustas,
# operaciones completas válidas e inválidas.
# ============================================================
 
 
class ErrorSistema(Exception):
    """
    Excepción base personalizada del sistema.
    """
    pass
 
 
class ErrorValidacion(ErrorSistema):
    """
    Error para datos inválidos.
    """
    pass
 
 
class ErrorNoEncontrado(ErrorSistema):
    """
    Error para entidades no encontradas.
    """
    pass
 
 
class ErrorReserva(ErrorSistema):
    """
    Error para operaciones incorrectas con reservas.
    """
    pass
 
 
class LoggerSistema:
    """
    Registra eventos y errores en archivo de texto.
    """
 
    ARCHIVO_LOG = "logs_software_fj.txt"
 
    @staticmethod
    def registrar(mensaje):
        fecha = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        linea = f"[{fecha}] {mensaje}\n"
 
        with open(LoggerSistema.ARCHIVO_LOG, "a", encoding="utf-8") as archivo:
            archivo.write(linea)
 
 
def validar_texto(valor, campo):
    if not isinstance(valor, str) or not valor.strip():
        raise ErrorValidacion(f"El campo '{campo}' no puede estar vacío.")
 
 
def validar_correo(correo):
    validar_texto(correo, "correo")
    if "@" not in correo or "." not in correo:
        raise ErrorValidacion("El correo electrónico no tiene un formato válido.")
 
 
def validar_telefono(telefono):
    validar_texto(telefono, "teléfono")
    if not telefono.isdigit() or len(telefono) < 7:
        raise ErrorValidacion("El teléfono debe contener solo números y mínimo 7 dígitos.")
 
 
def validar_numero_positivo(valor, campo):
    if not isinstance(valor, (int, float)) or valor <= 0:
        raise ErrorValidacion(f"El campo '{campo}' debe ser un número positivo.")
 
 
def crear_cliente_validado(identificador, nombre, correo, telefono):
    validar_numero_positivo(identificador, "identificador del cliente")
    validar_texto(nombre, "nombre")
    validar_correo(correo)
    validar_telefono(telefono)
 
    cliente = Cliente(identificador, nombre, correo, telefono)
    LoggerSistema.registrar(f"Cliente creado correctamente: {nombre}")
    return cliente
 
 
def crear_servicio_sala(identificador, nombre, tarifa_base, capacidad):
    validar_numero_positivo(identificador, "identificador del servicio")
    validar_texto(nombre, "nombre del servicio")
    validar_numero_positivo(tarifa_base, "tarifa base")
    validar_numero_positivo(capacidad, "capacidad")
 
    servicio = ReservaSala(identificador, nombre, tarifa_base, capacidad)
    LoggerSistema.registrar(f"Servicio de sala creado: {nombre}")
    return servicio
 
 
def crear_servicio_equipo(identificador, nombre, tarifa_base, tipo_equipo):
    validar_numero_positivo(identificador, "identificador del servicio")
    validar_texto(nombre, "nombre del servicio")
    validar_numero_positivo(tarifa_base, "tarifa base")
    validar_texto(tipo_equipo, "tipo de equipo")
 
    servicio = AlquilerEquipo(identificador, nombre, tarifa_base, tipo_equipo)
    LoggerSistema.registrar(f"Servicio de alquiler creado: {nombre}")
    return servicio
 
 
def crear_servicio_asesoria(identificador, nombre, tarifa_base, area):
    validar_numero_positivo(identificador, "identificador del servicio")
    validar_texto(nombre, "nombre del servicio")
    validar_numero_positivo(tarifa_base, "tarifa base")
    validar_texto(area, "área de asesoría")
 
    servicio = AsesoriaEspecializada(identificador, nombre, tarifa_base, area)
    LoggerSistema.registrar(f"Servicio de asesoría creado: {nombre}")
    return servicio
 
 
def crear_reserva_validada(sistema, identificador, id_cliente, id_servicio, duracion_horas):
    validar_numero_positivo(identificador, "identificador de reserva")
    validar_numero_positivo(id_cliente, "identificador de cliente")
    validar_numero_positivo(id_servicio, "identificador de servicio")
    validar_numero_positivo(duracion_horas, "duración de la reserva")
 
    if sistema.buscar_reserva(identificador) is not None:
        raise ErrorReserva(f"Ya existe una reserva con ID {identificador}.")
 
    cliente = sistema.buscar_cliente(id_cliente)
    if cliente is None:
        raise ErrorNoEncontrado(f"No existe un cliente con ID {id_cliente}.")
 
    servicio = sistema.buscar_servicio(id_servicio)
    if servicio is None:
        raise ErrorNoEncontrado(f"No existe un servicio con ID {id_servicio}.")
 
    reserva = Reserva(identificador, cliente, servicio, duracion_horas)
    sistema.agregar_reserva(reserva)
 
    LoggerSistema.registrar(
        f"Reserva creada: ID {identificador}, cliente {cliente.nombre}, servicio {servicio.nombre}"
    )
 
    return reserva
 
 
def confirmar_reserva_validada(sistema, id_reserva):
    reserva = sistema.buscar_reserva(id_reserva)
 
    if reserva is None:
        raise ErrorNoEncontrado(f"No existe una reserva con ID {id_reserva}.")
 
    if reserva.estado == "cancelada":
        raise ErrorReserva("No se puede confirmar una reserva cancelada.")
 
    if reserva.estado == "confirmada":
        raise ErrorReserva("La reserva ya se encuentra confirmada.")
 
    reserva.confirmar()
    LoggerSistema.registrar(f"Reserva confirmada: ID {id_reserva}")
 
 
def cancelar_reserva_validada(sistema, id_reserva):
    reserva = sistema.buscar_reserva(id_reserva)
 
    if reserva is None:
        raise ErrorNoEncontrado(f"No existe una reserva con ID {id_reserva}.")
 
    if reserva.estado == "cancelada":
        raise ErrorReserva("La reserva ya se encuentra cancelada.")
 
    reserva.cancelar()
    LoggerSistema.registrar(f"Reserva cancelada: ID {id_reserva}")
 
 
def ejecutar_operacion(nombre_operacion, funcion):
    """
    Ejecuta operaciones controlando errores para que el sistema no se detenga.
    """
 
    print(f"\n--- {nombre_operacion} ---")
 
    try:
        resultado = funcion()
 
        if resultado is not None:
            if hasattr(resultado, "mostrar_info"):
                print(resultado.mostrar_info())
            else:
                print(resultado)
 
        LoggerSistema.registrar(f"Operación exitosa: {nombre_operacion}")
 
    except ErrorSistema as error:
        print(f"Error controlado: {error}")
        LoggerSistema.registrar(f"Error controlado en '{nombre_operacion}': {error}")
 
    except Exception as error:
        print(f"Error inesperado: {error}")
        LoggerSistema.registrar(f"Error inesperado en '{nombre_operacion}': {error}")
 
 
def main():
    sistema = SistemaGestion()
 
    print("==============================================")
    print(" SISTEMA INTEGRAL SOFTWARE FJ")
    print(" Gestión de Clientes, Servicios y Reservas")
    print("==============================================")
 
    # Operación 1: cliente válido
    ejecutar_operacion(
        "Crear cliente válido 1",
        lambda: sistema.agregar_cliente(
            crear_cliente_validado(1, "Ana Torres", "ana@email.com", "3001234567")
        )
    )
 
    # Operación 2: cliente válido
    ejecutar_operacion(
        "Crear cliente válido 2",
        lambda: sistema.agregar_cliente(
            crear_cliente_validado(2, "Carlos Ríos", "carlos@email.com", "3107654321")
        )
    )
 
    # Operación 3: cliente inválido
    ejecutar_operacion(
        "Intentar crear cliente con correo inválido",
        lambda: sistema.agregar_cliente(
            crear_cliente_validado(3, "Laura Gómez", "correo-invalido", "3201112233")
        )
    )
 
    # Operación 4: servicio sala válido
    ejecutar_operacion(
        "Crear servicio de reserva de sala",
        lambda: sistema.agregar_servicio(
            crear_servicio_sala(101, "Sala Empresarial", 50000, 25)
        )
    )
 
    # Operación 5: servicio equipo válido
    ejecutar_operacion(
        "Crear servicio de alquiler de equipo",
        lambda: sistema.agregar_servicio(
            crear_servicio_equipo(102, "Alquiler Video Beam", 35000, "Video Beam")
        )
    )
 
    # Operación 6: servicio asesoría válido
    ejecutar_operacion(
        "Crear servicio de asesoría especializada",
        lambda: sistema.agregar_servicio(
            crear_servicio_asesoria(103, "Asesoría en Software", 80000, "Desarrollo")
        )
    )
 
    # Operación 7: servicio inválido
    ejecutar_operacion(
        "Intentar crear servicio con tarifa negativa",
        lambda: sistema.agregar_servicio(
            crear_servicio_sala(104, "Sala Inválida", -10000, 10)
        )
    )
 
    # Operación 8: crear reserva válida
    ejecutar_operacion(
        "Crear reserva válida",
        lambda: crear_reserva_validada(sistema, 1001, 1, 101, 3)
    )
 
    # Operación 9: crear otra reserva válida
    ejecutar_operacion(
        "Crear reserva válida con servicio de asesoría",
        lambda: crear_reserva_validada(sistema, 1002, 2, 103, 2)
    )
 
    # Operación 10: reserva con cliente inexistente
    ejecutar_operacion(
        "Intentar crear reserva con cliente inexistente",
        lambda: crear_reserva_validada(sistema, 1003, 99, 101, 2)
    )
 
    # Operación 11: reserva con duración inválida
    ejecutar_operacion(
        "Intentar crear reserva con duración inválida",
        lambda: crear_reserva_validada(sistema, 1004, 1, 102, 0)
    )
 
    # Operación 12: confirmar reserva
    ejecutar_operacion(
        "Confirmar reserva existente",
        lambda: confirmar_reserva_validada(sistema, 1001)
    )
 
    # Operación 13: cancelar reserva
    ejecutar_operacion(
        "Cancelar reserva existente",
        lambda: cancelar_reserva_validada(sistema, 1002)
    )
 
    # Operación 14: intentar confirmar reserva cancelada
    ejecutar_operacion(
        "Intentar confirmar reserva cancelada",
        lambda: confirmar_reserva_validada(sistema, 1002)
    )
 
    # Operación 15: intentar cancelar reserva inexistente
    ejecutar_operacion(
        "Intentar cancelar reserva inexistente",
        lambda: cancelar_reserva_validada(sistema, 9999)
    )
 
    print("\n==============================================")
    print("CLIENTES REGISTRADOS")
    print("==============================================")
    sistema.listar_clientes()
 
    print("\n==============================================")
    print("SERVICIOS REGISTRADOS")
    print("==============================================")
    sistema.listar_servicios()
 
    print("\n==============================================")
    print("RESERVAS REGISTRADAS")
    print("==============================================")
    sistema.listar_reservas()
 
    print("\nEl archivo de logs fue generado como: logs_software_fj.txt")
    print("Programa finalizado sin detenerse ante errores.")
 
 
if __name__ == "__main__":
    main()
