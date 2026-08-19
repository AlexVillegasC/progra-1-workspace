# Lab 3 — Herencia y polimorfismo

Guía paso a paso.

En ese punto ya tienes `Usuario.h`, `Usuario.cpp` y un `main` que crea dos objetos `Usuario`.

Meta de esta parte:

1. Crear clases derivadas.
2. Sobrescribir comportamiento con `virtual` y `override`.
3. Recorrer objetos distintos con un puntero común `Usuario*`.
4. Usar polimorfismo para tomar una decisión dentro de un ciclo.

---

## Lo que ya tienes

`Usuario` guarda datos `private`:

- `nombre`
- `apellidos`
- `fechaNacimiento`
- `direccion`
- `correo`

La interfaz pública incluye constructor, getters, setters y:

```cpp
virtual void acceder() const;
```

`virtual` es la base del polimorfismo. Sin esa palabra, C++ siempre llamaría la versión de `Usuario`.

`main` hoy crea dos `Usuario` y llama `acceder()`. Los dos imprimen el mismo mensaje genérico. Eso es normal: todavía no existen tipos distintos.

---

## Los cuatro pilares en este lab

| Pilar | Qué significa aquí |
| --- | --- |
| Encapsulamiento | Los datos son `private`. El resto del programa usa getters y setters. |
| Abstracción | `main` pide `acceder()` o `puedeAcceder()`. No necesita saber cómo se decide por dentro. |
| Herencia | `Administrador`, `Invitado` y `Premium` **son un tipo de** `Usuario`. |
| Polimorfismo | Misma llamada. Distinto comportamiento según el objeto real. |

---

## Paso 1 — Diseñar las clases derivadas

Antes de escribir código, nombra los roles.

Usa estos tres:

- `Administrador` — entra al panel de control.
- `Invitado` — ve contenido limitado.
- `Premium` — ve contenido completo.

Cada uno **es un** `Usuario`. Reutilizan nombre, correo y el resto.

Cada uno necesita su propia versión de `acceder()`.

Si las tres imprimen lo mismo, no hay polimorfismo que mostrar.

**Comprueba:** ¿puedes decir en una frase qué hace `acceder()` en cada clase?

---

## Paso 2 — Crear `Administrador`

Crea `Administrador.h` junto a `Usuario.h`.

```cpp
#pragma once
#include "Usuario.h"

class Administrador : public Usuario
{
public:
    Administrador(std::string nombre,
                  std::string apellidos,
                  std::string fechaNacimiento,
                  std::string direccion,
                  std::string correo);

    void acceder() const override;
};
```

Qué significa cada parte:

- `: public Usuario` — herencia. `Administrador` es un `Usuario`.
- El constructor recibe los mismos cinco datos.
- `override` confirma que sobrescribes `acceder()`.
- `const` forma parte de la firma. Debe coincidir con la base.

Crea `Administrador.cpp`:

```cpp
#include "Administrador.h"
#include <iostream>

Administrador::Administrador(std::string nombre,
                             std::string apellidos,
                             std::string fechaNacimiento,
                             std::string direccion,
                             std::string correo)
    : Usuario(nombre, apellidos, fechaNacimiento, direccion, correo)
{
}

void Administrador::acceder() const
{
    std::cout << "Administrador entra al panel de control.\n";
}
```

`: Usuario(...)` llama al constructor de la clase base. Ahí se inician los datos privados.

En Visual Studio:

1. Clic derecho en el proyecto.
2. **Add → Existing Item**.
3. Agrega el `.h` y el `.cpp`.

**Prueba:** compila. Todavía no hace falta cambiar `main`. Si falta `const` u `override`, el compilador lo dice.

---

## Paso 3 — Crear `Invitado` y `Premium`

Copia el molde de `Administrador`.

Cambia solo:

1. El nombre de la clase.
2. El mensaje de `acceder()`.

Mensajes:

- `Invitado` → `Invitado ve contenido limitado.`
- `Premium` → `Premium ve contenido completo.`

La línea `: Usuario(...)` se queda igual. La base no cambia.

Agrega los cuatro archivos al proyecto.

**Prueba:** compila de nuevo. `main` sigue creando `Usuario`. Aún no verás los mensajes nuevos.

---

## Paso 4 — Ver polimorfismo en `main`

Incluye las tres cabeceras derivadas:

```cpp
#include "../Usuario.h"
#include "../Administrador.h"
#include "../Invitado.h"
#include "../Premium.h"
```

Crea un objeto de cada tipo:

```cpp
Administrador admin("Ana", "Lopez", "12-03-1998", "Calle 1", "ana@correo.com");
Invitado invitado("Carlos", "Perez", "05-07-1995", "Calle 2", "carlos@correo.com");
Premium premium("Luis", "Mora", "01-01-2000", "Calle 3", "luis@correo.com");
```

Guárdalos con un puntero común:

```cpp
Usuario* usuarios[3] = { &admin, &invitado, &premium };
```

`Usuario*` no es el tipo real. Es la referencia común.

Recorre el arreglo:

```cpp
for (int i = 0; i < 3; ++i)
{
    std::cout << usuarios[i]->getNombre() << ": ";
    usuarios[i]->acceder();
}
```

Misma llamada. Tres mensajes.

Eso es polimorfismo dinámico. Necesita tres cosas:

1. Puntero o referencia a la base: `Usuario*`.
2. Método `virtual` en `Usuario`.
3. Firma compatible con `override` en cada derivada.

**Prueba:** ejecuta con `Ctrl + F5`.

Salida esperada en esta etapa:

```text
Ana: Administrador entra al panel de control.
Carlos: Invitado ve contenido limitado.
Luis: Premium ve contenido completo.
```

Si los tres imprimen el mensaje genérico, revisa `virtual` en `Usuario.h`.

---

## Paso 5 — Una decisión dentro del ciclo

El ciclo ya elige *cómo* acceder.

Ahora debe decidir *si* puede acceder.

El error típico es preguntar el tipo:

```cpp
// Evita esto
if (es Administrador) { ... }
else if (es Premium) { ... }
```

Esa cadena crece con cada clase nueva.

La solución: pregunta al objeto.

### 5.1 Agregar el estado `activo`

En `Usuario.h`, suma un dato `private`:

```cpp
bool activo;
```

En el constructor de `Usuario.cpp`, inícialo:

```cpp
this->activo = true;
```

Agrega interfaz:

```cpp
bool getActivo() const;
void setActivo(bool nuevoEstado);
virtual bool puedeAcceder() const;
```

Implementación en `Usuario.cpp`:

```cpp
bool Usuario::getActivo() const
{
    return activo;
}

void Usuario::setActivo(bool nuevoEstado)
{
    activo = nuevoEstado;
}

bool Usuario::puedeAcceder() const
{
    return activo;
}
```

`puedeAcceder()` es `virtual`. Cada clase puede responder distinto.

### 5.2 Sobrescribir la decisión

**Administrador:** siempre puede entrar. El rol lo permite, aunque la cuenta esté inactiva.

```cpp
bool Administrador::puedeAcceder() const
{
    return true;
}
```

**Invitado** y **Premium:** solo si están activos.

```cpp
bool Invitado::puedeAcceder() const
{
    return getActivo();
}

bool Premium::puedeAcceder() const
{
    return getActivo();
}
```

Declara `puedeAcceder() const override;` en cada `.h`.

### 5.3 Usar el `if` en el ciclo

En `main`, deja inactivas a Ana y a Luis:

```cpp
admin.setActivo(false);
premium.setActivo(false);
```

Carlos permanece activo.

El ciclo queda así:

```cpp
for (int i = 0; i < 3; ++i)
{
    std::cout << usuarios[i]->getNombre() << ": ";

    if (usuarios[i]->puedeAcceder())
    {
        usuarios[i]->acceder();
    }
    else
    {
        std::cout << "acceso denegado. Cuenta inactiva.\n";
    }
}
```

Lee el `if` en voz alta:

> Si *este usuario* puede acceder, que acceda a su manera. Si no, niégalo.

El ciclo no nombra `Administrador`, `Invitado` ni `Premium`.

---

## Resultado final

Salida esperada:

```text
Consulta mediante getter: Ana Lopez
Correo actualizado: ana.lopez@correo.com

Ana: Administrador entra al panel de control.
Carlos: Invitado ve contenido limitado.
Luis: acceso denegado. Cuenta inactiva.
```

Por qué Ana entra: `Administrador::puedeAcceder()` devuelve `true`. El `setActivo(false)` no la bloquea.

Por qué Luis no entra: `Premium::puedeAcceder()` consulta `activo`. Está en `false`. El `else` lo detiene.

Por qué Carlos ve contenido limitado: está activo, y su `acceder()` es el de `Invitado`.

---

## `main.cpp` de referencia

Si tu ciclo y tus includes coinciden, el archivo debe verse así:

```cpp
#include "../Usuario.h"
#include "../Administrador.h"
#include "../Invitado.h"
#include "../Premium.h"
#include <iostream>

int main()
{
    Administrador admin("Ana", "Lopez", "12-03-1998", "Calle 1", "ana@correo.com");
    Invitado invitado("Carlos", "Perez", "05-07-1995", "Calle 2", "carlos@correo.com");
    Premium premium("Luis", "Mora", "01-01-2000", "Calle 3", "luis@correo.com");

    admin.setActivo(false);
    premium.setActivo(false);

    std::cout << "Consulta mediante getter: "
              << admin.getNombre() << " "
              << admin.getApellidos() << '\n';

    admin.setCorreo("ana.lopez@correo.com");
    std::cout << "Correo actualizado: " << admin.getCorreo() << "\n\n";

    Usuario* usuarios[3] = { &admin, &invitado, &premium };

    for (int i = 0; i < 3; ++i)
    {
        std::cout << usuarios[i]->getNombre() << ": ";

        if (usuarios[i]->puedeAcceder())
        {
            usuarios[i]->acceder();
        }
        else
        {
            std::cout << "acceso denegado. Cuenta inactiva.\n";
        }
    }

    std::cin.get();
    return 0;
}
```

---

## Autoevaluación

Marca solo si puedes explicarlo en voz alta.

- [ ] Los datos de `Usuario` son `private`.
- [ ] Cada derivada usa `: public Usuario`.
- [ ] El constructor derivado llama `: Usuario(...)`.
- [ ] `acceder()` es `virtual` en la base y `override` en las derivadas.
- [ ] `main` recorre un arreglo de `Usuario*`.
- [ ] El `if` pregunta `puedeAcceder()`, no el nombre de la clase.
- [ ] Agregar otra clase no obliga a reescribir el ciclo.

---

## Preguntas para entregar

1. ¿Qué pasaría si quitas `virtual` de `acceder()`? ¿Qué mensaje verías tres veces?
2. ¿Por qué Ana entra si también llamaste `setActivo(false)`?
3. ¿Cómo agregarías una clase `Moderador` sin cambiar el `for`?
4. ¿Qué pilar protege el setter de `correo` cuando recibe `""`?

Si puedes responder esas cuatro, llegaste al punto de esta guía.
