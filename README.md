# pokemon_simulator

main.py
import sys

from clases.pokemon import Pokemon


pokemons: list[Pokemon] = []


def pedir_entero(mensaje: str, minimo: int = 1) -> int:
    while True:
        try:
            valor = int(input(mensaje))
            if valor < minimo:
                print(f"Ingresa un número igual o mayor que {minimo}.")
                continue
            return valor
        except ValueError:
            print("Entrada no válida. Ingresa un número entero.")


def seleccionar_pokemon() -> Pokemon | None:
    if not pokemons:
        print("Aún no hay Pokémon capturados.")
        return None

    for indice, pokemon in enumerate(pokemons, start=1):
        print(f"{indice}. {pokemon.nombre} ({pokemon.tipo}) - Nivel {pokemon.nivel}")

    seleccion = pedir_entero("Selecciona un Pokémon: ")
    if seleccion > len(pokemons):
        print("No existe un Pokémon con ese número.")
        return None
    return pokemons[seleccion - 1]


def capturar_pokemon() -> None:
    nombre = input("Nombre: ").strip()
    tipo = input("Tipo: ").strip()
    if not nombre or not tipo:
        print("El nombre y el tipo no pueden estar vacíos.")
        return

    ataque = pedir_entero("Ataque: ")
    defensa = pedir_entero("Defensa: ")
    salud = pedir_entero("Salud: ")
    pokemons.append(Pokemon(nombre, tipo, ataque, defensa, salud))


def entrenar_pokemon() -> None:
    pokemon = seleccionar_pokemon()
    if pokemon is None:
        return

    print("Deja la entrada vacía para mejorar ataque, defensa y salud en 1.")
    entrada = input(
        "Mejoras separadas por comas (ataque, defensa, salud; máximo 3): "
    ).strip()
    try:
        mejoras = tuple(int(valor.strip()) for valor in entrada.split(",")) if entrada else ()
        pokemon.entrenar(*mejoras)
    except ValueError as error:
        print(f"No se pudo entrenar: {error}")


def atacar_pokemon() -> None:
    print("Elige el Pokémon atacante:")
    atacante = seleccionar_pokemon()
    if atacante is None:
        return

    print("Elige el objetivo:")
    objetivo = seleccionar_pokemon()
    if objetivo is None:
        return
    if atacante is objetivo:
        print("Un Pokémon no puede atacarse a sí mismo.")
        return

    atacante.atacar(objetivo)


def mostrar_pokemons() -> None:
    if not pokemons:
        print("Aún no hay Pokémon capturados.")
        return
    for pokemon in pokemons:
        pokemon.mostrar_info()


def liberar_pokemon() -> None:
    pokemon = seleccionar_pokemon()
    if pokemon is None:
        return

    pokemons.remove(pokemon)
    print(f"{pokemon.nombre} fue retirado de tu equipo.")
    del pokemon


def main() -> None:
    sys.stdout.reconfigure(encoding="utf-8")
    while True:
        print(
            "\n=== Simulador de entrenamiento Pokémon ===\n"
            "1. Capturar Pokémon 🐉\n"
            "2. Entrenar Pokémon 💪\n"
            "3. Ver información de todos 📋\n"
            "4. Contar Pokémon creados 🧮\n"
            "5. Liberar Pokémon 🕊️\n"
            "6. Atacar con un Pokémon ⚔️\n"
            "0. Salir"
        )
        opcion = input("Elige una opción: ").strip()

        if opcion == "1":
            capturar_pokemon()
        elif opcion == "2":
            entrenar_pokemon()
        elif opcion == "3":
            mostrar_pokemons()
        elif opcion == "4":
            Pokemon.total_pokemons()
        elif opcion == "5":
            liberar_pokemon()
        elif opcion == "6":
            atacar_pokemon()
        elif opcion == "0":
            print("¡Gracias por jugar!")
            break
        else:
            print("Opción no válida.")

            pokemon.py
            class Pokemon:
    """Representa un Pokémon entrenable."""

    _total_creados = 0

    def __init__(
        self,
        nombre: str,
        tipo: str,
        ataque: int,
        defensa: int,
        salud: int,
    ) -> None:
        self.nombre = nombre
        self.tipo = tipo
        self.nivel = 1
        self.ataque = ataque
        self.defensa = defensa
        self.salud = salud
        Pokemon._total_creados += 1
        print(f"¡{self.nombre} ha sido capturado!")

    def __del__(self) -> None:
        nombre = getattr(self, "nombre", "El Pokémon")
        print(f"{nombre} ha sido liberado ✨")

    def entrenar(self, *mejoras: int) -> None:
        """Sube de nivel y aplica mejoras a ataque, defensa y salud."""
        if len(mejoras) > 3:
            raise ValueError("Indica como máximo tres mejoras: ataque, defensa y salud.")
        if any(type(mejora) is not int or mejora < 1 for mejora in mejoras):
            raise ValueError("Cada mejora debe ser un entero mayor que cero.")

        self.nivel += 1
        if not mejoras:
            mejoras = (1, 1, 1)

        atributos = ("ataque", "defensa", "salud")
        for atributo, mejora in zip(atributos, mejoras):
            setattr(self, atributo, getattr(self, atributo) + mejora)

        print(f"{self.nombre} subió al nivel {self.nivel}.")

    def mostrar_info(self) -> None:
        print(
            f"{self.nombre} ({self.tipo}) | Nivel: {self.nivel} | "
            f"Ataque: {self.ataque} | Defensa: {self.defensa} | "
            f"Salud: {self.salud} | Poder total: {self.calcular_poder()}"
        )

    def calcular_poder(self) -> int:
        return self.ataque + self.defensa + self.salud

    def atacar(self, objetivo: "Pokemon") -> int:
        if self.salud <= 0:
            print(f"{self.nombre} no puede atacar porque está debilitado.")
            return 0
        if objetivo.salud <= 0:
            print(f"{objetivo.nombre} ya está debilitado.")
            return 0

        dano = max(1, self.ataque - objetivo.defensa)
        objetivo.salud = max(0, objetivo.salud - dano)
        print(
            f"{self.nombre} atacó a {objetivo.nombre} e hizo {dano} de daño. "
            f"Salud restante: {objetivo.salud}."
        )
        return dano

    @classmethod
    def total_pokemons(cls) -> int:
        """Muestra y devuelve cuántos Pokémon se han creado."""
        print(f"Pokémon creados: {cls._total_creados}")
        return cls._total_creados


if __name__ == "__main__":
    main()
