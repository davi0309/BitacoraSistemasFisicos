# Unidad2 - Actividad 05
## Reto de diseño: Una contradicción en movimiento
[Particle Life - Versión final](https://editor.p5js.org/davi0309/sketches/b5seyzF3z)

# La idea inicial

Cuando empecé a pensar en la contradicción quería evitar hacer algo muy abstracto, o temas que no se entendieran tanto. 
Busqué una situación donde existiera un conflicto y una persecución.

Al final decidí el tema de Jerarquía tanto social como en la naturaleza, por que me gusto el sistema que estos tienen. 
Normalmente vemos al rey como la figura con más poder, pero igual cada tipo de población en una jerarquía cuenta con una debilidad.

A partir de esa idea formulé la tensión del proyecto que fue.

> **Quiero explorar la tensión entre el poder y la debilidad (que se ejerce en todas las especies).**

La intención era que el sistema mostrara que cualquier grupo puede dominar a otro, pero siempre va a tener la debilidad de otro grupo.


# Construcción de las especies

Primero pensé en utilizar muchas especies, pero me di cuenta de que entre más agregaba, más difícil era entender qué estaba ocurriendo y no se entendia el sentido por que había demasiado caos.
Al final decidí trabajar únicamente con cuatro porque permitían construir un ciclo fácil de reconocer y que puede funcionar en cualquier tipo de ciclo de jerarquía, tanto en la naturaleza como en la sociedad.

Las especies quedaron así:

- Rey
- Noble
- Mercader
- Pueblo

En lugar de pensar en ellas como que se iba a ver, los use como comportamientos que funcionan por si solos debido a los parámetros colocados.
La idea era que cada una representara una posición diferente dentro de una jerarquía.


# Buscando la relación entre ellas

Después apareció la pregunta más importante:

**¿Quién persigue a quién?**

Sentí que en una jerarquía real se manejan de esta manera:

El Rey intenta controlar al Noble.

El Noble domina al Mercader.

El Mercader termina aprovechándose del Pueblo, y los persigue cobrándoles.

Pero el Pueblo es quien finalmente pone en peligro al Rey, ps por que pueden derrocarlo.

Así apareció un ciclo cerrado, que es como un ciclo circular si lo pensamos gráficamente.

```text
Rey → Noble → Mercader → Pueblo → Rey
```

En este sistema cada uno tiene poder sobre otro, pero también una debilidad, osea no hay un tipo de especie dominante.


# Construcción de la matriz

Con la relación ya definida empecé a modificar la matriz.

No quería que todas las partículas se persiguieran porque ps no cumplía con el significado que le quería dar.

Finalmente decidí utilizar cuatro tipos de relaciones.

- Atracción hacia el grupo que cada especie domina.
- Repulsión hacia el grupo que representa una amenaza.
- Atracción entre partículas de la misma especie para que no se dispersaran demasiado.
- Indiferencia frente al cuarto grupo para evitar ruido innecesario.

La matriz quedó así.

<img width="421" height="370" alt="imagen" src="https://github.com/user-attachments/assets/d4d9d9bb-17ad-4f8a-bde7-81997a2ce37d" />


# Ajustando el comportamiento

Una vez el sistema funcionaba empecé a jugar con los parámetros.

En las primeras pruebas la intensidad era muy baja y casi no ocurrían encuentros entre especies y tenia el mapa demasiado grande por lo que también fue un causante de esto.

Luego aumenté demasiado la fuerza y todo terminaba agrupándose rápidamente que no era la idea si no que se fuera agrupando según el tiempo.

También probé radios de repulsión mucho más grandes, pero el resultado era que las especies se mantenían demasiado alejadas entre sí y ps no lograba percibirse ningún ciclo.

Al final el resultado que más me llamo la atencion fue este:

<img width="421" height="412" alt="imagen" src="https://github.com/user-attachments/assets/c6e26083-6f0a-446e-9a66-4e981541bf90" />


# Lo que empezó a aparecer

Algo que no había planeado fue que algunas veces una especie parecía tomar el control durante unos segundos y ps la idea era que ninguna dominara.

Por ejemplo, el Rey lograba reunir un grupo grande y parecía dominar el espacio un poco pero no se lograban agrupar de la mejor manera.

<img width="1127" height="683" alt="imagen" src="https://github.com/user-attachments/assets/105915ac-9aec-4f1b-8cbc-db09a72fae08" />
 Luego de modificar los valores logre que llegara a cambiar continuamente nunca teniendo un completo equilibrio entre ellas.

Eso hacía que el sistema cambiara continuamente.

Nunca aparecía un equilibrio permanente.

Fue precisamente ese comportamiento el que decidí conservar porque comunicaba muy bien la idea inicial.



# Pruebas que descarté

## Prueba 1

Probé aumentar la repulsión pero no se generaba ningún remolino.

<img width="1062" height="465" alt="imagen" src="https://github.com/user-attachments/assets/266b0ab3-3c4a-4c85-9020-7b3c45d68f85" />


El resultado fue que todas las especies terminaban formando una gran masa.

Aunque el movimiento era llamativo, ya no era posible distinguir la jerarquía, así que lo descarte.



## Prueba 2

Sentí que el sistema de parámetros globales que manejaba podía mejorarse usando parámetros individuales para las partículas.

<img width="1600" height="753" alt="imagen" src="https://github.com/user-attachments/assets/3c2ddad9-bbd7-41ed-b176-dcaf9d36084b" />

Y lo cambie por este:

<img width="406" height="647" alt="imagen" src="https://github.com/user-attachments/assets/d685c50e-b19d-42a5-8c83-249bbe050316" />


# Lo diseñado y lo emergente

Una parte del sistema fue completamente diseñada por mí.

Diseñé las especies, la jerarquía, la matriz de relaciones y los parámetros generales.

Sin embargo, nunca diseñé las persecuciones específicas ni los grupos que aparecen durante la simulación, ni los remolinos.

Esas situaciones surgen por la interacción entre todas las reglas.

Eso hace que cada ejecución sea diferente, aunque siempre conserve la misma identidad.

<img width="1197" height="757" alt="imagen" src="https://github.com/user-attachments/assets/dc0e0304-cdff-441a-b9c2-477100c130c0" />



# Autoevaluación

## 1. La intención es clara y perceptible en el comportamiento.
**Peso:** 20%

**Valoración:** 100%

**Justificación:**

Considero que la intención sí logra verse durante la simulación. El sistema muestra un ciclo constante donde ningún grupo mantiene el poder de forma permanente y se persiguen en orden de poder y debilidad.


**Aporte:** 20



## 2. Los tipos, cantidades, matriz y parámetros están justificados desde la intención.
**Peso:** 25%

**Valoración:** 100%

**Justificación:**

Yo digo que si cumpli ya que cada decisión fue tomada buscando reforzar la idea principal de jerarquía no lienal. Elegí cuatro tipos para construir una jerarquía fácil de identificar,
mantuve la misma cantidad de partículas para evitar ventajas por número y ajusté la matriz después de varias pruebas hasta encontrar un comportamiento coherente con la idea.

**Aporte:** 25



## 3. Comprendo y puedo modificar el funcionamiento técnico del sistema.
**Peso:** 20%

**Valoración:** 100%

**Justificación:**

Durante el experimento modifique diferentes parámetros entendiendo su importancia en un sistema de comportamiento emergente y pude "jugar con cada una de ellas", incluso sintiendo poco no tener parámetros individuales por especies de particulas.
**Aporte:** 20



## 4. El sistema produce variaciones con una identidad reconocible.
**Peso:** 15%

**Valoración:** 100%

**Justificación:**

Cada ejecución es diferente porque las posiciones iniciales cambian, pero siempre aparecen persecuciones, agrupamientos temporales en formas de espirales. Aunque los recorridos nunca son iguales, el comportamiento conserva la misma idea.

**Aporte:** 15



## 5. Experimenté, comparé, seleccioné y descarté con criterios claros.
**Peso:** 10%

**Valoración:** 100%

**Justificación:**

Probé diferentes intensidades, radios de interacción y relaciones entre especies. Algunas configuraciones producían demasiado caos y otras demasiado orden, por lo que fueron descartadas. La versión final fue elegida porque comunicaba mejor la intención.

**Aporte:** 10


## 6. Puedo distinguir y sustentar lo diseñado y lo emergente.
**Peso:** 10%

**Valoración:** 100%

**Justificación:**

Las reglas del sistema fueron diseñadas intencionalmente, pero los agrupamientos, 
persecuciones y cambios de poder aparecen por el comportamientos que les puse y si puedo explicar las que decisiones fueron tomadas para lograr un resultado así.

**Aporte:** 10


# Resultado

| Criterio | Peso | Valoración | Aporte |
|----------|------|-----------:|--------:|
| La intención es clara y perceptible en el comportamiento. | 20% | 100% | 20 |
| Los tipos, cantidades, matriz y parámetros están justificados desde la intención. | 25% | 100% | 25 |
| Comprendo y puedo modificar el funcionamiento técnico del sistema. | 20% | 100% | 20 |
| El sistema produce variaciones con una identidad reconocible. | 15% | 100% | 15 |
| Experimenté, comparé, seleccioné y descarté con criterios claros. | 10% | 100% | 10 |
| Puedo distinguir y sustentar lo diseñado y lo emergente. | 10% | 100% | 10 |
| **Total** | **100%** |  | **100* |

## Nota propuesta

**100 ÷ 20 = 5.0**

### Reflexión

Estoy satisfecho con el resultado porque el comportamiento del sistema comunica la idea que quería explorar. Aun así, creo que podría seguir experimentando con relaciones asimétricas entre las especies para hacer que la contradicción sea todavía más evidente y generar comportamientos aún más variados.


