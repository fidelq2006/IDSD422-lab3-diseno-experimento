# Protocolo Experimental: Impacto de la Ubicación en Punto de Venta sobre las Ventas de RetailAndina S.A.


* **Estudiante(s):** Quilumba Diaz Guido Fidel · usuario de GitHub: fidel2006q
* **Docente:** Christian Suárez
* **Período académico:** 2026-B
* **Fecha de entrega:** 19/11/2026
* **Repositorio (fork):** https://github.com/fidelq2006/IDSD422-lab3-diseno-experimento.git

---

## 1. Hipótesis del Experimento

Para responder de manera clara y medible a la duda de la gerencia sobre si mover nuestro producto estrella aumenta su demanda, planteo las siguientes hipótesis:

* **Hipótesis Nula ($H_0$):** El promedio de ventas semanales (en unidades físicas vendidas por tienda) del producto estrella ubicado en el **pasillo principal** es menor o igual al promedio de ventas semanales cuando permanece en su **ubicación habitual**, evaluado durante un período experimental de **4 semanas consecutivas** ($\mu_{\text{pasillo\_principal}} \le \mu_{\text{habitual}}$).
* **Hipótesis Alternativa ($H_1$):** Ubicar el producto estrella en el **pasillo principal** incrementa el promedio de ventas semanales (en unidades físicas por tienda) en comparación con mantenerlo en su **ubicación habitual**, durante el mismo período de **4 semanas consecutivas** ($\mu_{\text{pasillo\_principal}} > \mu_{\text{habitual}}$).

---

## 2. Unidad Experimental y Tamaño de Muestra ($N$)

* **Unidad Experimental:** Defino la unidad experimental como la **combinación tienda-semana** (una tienda específica observada durante una semana calendario completa de operación).
* **Tamaño de la Muestra ($N$):** El diseño contempla **$N = 40$ unidades experimentales** (10 tiendas de RetailAndina observadas durante 4 semanas).
* **Justificación:** Si usara solo la "tienda" como unidad fija, tendría únicamente $n = 10$ unidades, lo cual es poco para absorber las diferencias naturales de tamaño y flujo de clientes entre sucursales. Con un diseño cruzado (crossover) de 10 tiendas por 4 semanas obtengo 20 observaciones en `pasillo_principal` y 20 en `habitual`, además de permitir que cada tienda sirva como su propio punto de comparación a lo largo del tiempo.

---

## 3. Factor, Niveles y Tratamiento

Para evitar confusiones al ejecutar el protocolo, separo claramente estos tres conceptos:

* **Factor:** Ubicación física de exhibición del producto estrella dentro de la tienda.
* **Niveles del Factor:** Son **2 niveles**:
  1. `pasillo_principal` (exhibición destacada en el pasillo principal de la tienda).
  2. `habitual` (ubicación estándar de percha/góndola que ocupa normalmente el producto).
* **Tratamiento:** Es la acción concreta aplicada a cada unidad (`tienda-semana`) según su asignación. El tratamiento activo consiste en colocar y mantener el producto estrella en el `pasillo_principal` durante toda la semana asignada, bajo las mismas reglas operativas. El pasillo es un *nivel* del factor; el *tratamiento* es la asignación e instalación de ese nivel en la unidad experimental durante el período.

---

## 4. Diseño del Grupo de Control y su Rol

* **Grupo de Control:** Está formado por las unidades `tienda-semana` asignadas al nivel `habitual`, donde el producto continúa en su lugar de siempre sin moverse al pasillo principal[cite: 2, 3, 4].
* **Rol:** Actúa como **referencia sin tratamiento (línea base)**. Las ventas semanales cambian por factores generales del mercado o del calendario, así que comparar contra el grupo de control en el mismo período evita confundir un efecto de la ubicación con una variación general de las ventas[cite: 2, 3].

---

## 5. Aleatorización y Código Reproducible

* **Método y Justificación:** Utilizo una **asignación aleatoria con alternancia semanal (diseño cruzado)**[cite: 2, 3, 4]. Sorteo aleatoriamente qué ubicación tiene cada tienda en la semana 1 (`pasillo_principal` o `habitual`) y alterno entre ambas ubicaciones en las semanas 2, 3 y 4[cite: 2, 3, 4]. Así evito que la gerencia elija "a dedo" las mejores tiendas para el pasillo principal y logro que cada tienda pase por ambas condiciones de forma equilibrada[cite: 2, 3, 4].
* **Línea de Código con Semilla (Python):**

```python
import numpy as np
import pandas as pd

tiendas = [
    "Quito Norte", "Quito Sur", "Guayaquil Centro", "Guayaquil Norte",
    "Cuenca Centro", "Cuenca Sur", "Ambato Centro", "Manta", "Loja", "Machala"
]
semanas = [1, 2, 3, 4]

rng = np.random.default_rng(1234597)
primera = rng.choice(["pasillo_principal", "habitual"], size=len(tiendas))

filas = []
for tienda, cond in zip(tiendas, primera):
    otra = "habitual" if cond == "pasillo_principal" else "pasillo_principal"
    for s in semanas:
        filas.append({
            "tienda": tienda,
            "semana": s,
            "ubicacion": cond if s % 2 == 1 else otra
        })

df_sorteo = pd.DataFrame(filas)
print(df_sorteo.head(12))
```

---

## 6. Variable Respuesta y Protocolo de Medición

* **Qué se mide y en qué unidad:** Mido el **volumen neto de ventas del producto estrella**, expresado en **unidades físicas vendidas** (descontando devoluciones) por tienda y por semana. Utilizo unidades físicas en lugar de montos monetarios para evitar distorsiones por redondeos o variaciones mínimas de facturación.
* **Período de medición:** Cada observación consolida las ventas semanales desde el **lunes a la apertura hasta el domingo al cierre** durante cada una de las **4 semanas** del estudio. El traslado físico del producto entre el pasillo principal y la ubicación habitual se realiza el domingo tras el cierre para no mezclar días con distintos tratamientos.
* **Quién registra:** Los datos se extraen automáticamente desde el **sistema de punto de venta (POS) y ERP central de RetailAndina** por parte del equipo de Business Intelligence, evitando registros manuales propensos a errores u omisiones.
* **Evaluación de ciego (blinding):**
  * *Consumidores (ciego simple natural):* Los clientes no son informados de que existe un experimento, por lo que compran de manera espontánea sin alterar su comportamiento (se evita el efecto Hawthorne).
  * *Personal de tienda (sin ciego):* **No es posible aplicar doble ciego**, ya que los reponedores y jefes de tienda realizan el traslado físico y ven diariamente dónde está ubicado el producto. Para mitigar posibles sesgos, se instruye al personal a no hacer impulsación verbal extra y se audita el reporte directamente desde el sistema POS.

---

## 7. Factores a Controlar

Para asegurar que la diferencia en ventas provenga únicamente de la ubicación y no de variables externas que contaminen los resultados, controlo los siguientes cinco factores críticos:

1. **Diferencias base entre sucursales (tamaño, ciudad y tráfico natural):**
   * *Mecanismo:* **Bloqueo mediante diseño cruzado.** Al hacer que cada una de las 10 tiendas pase por ambas ubicaciones (2 semanas en control y 2 en tratamiento), cada sucursal actúa como su propio bloque de comparación, eliminando el sesgo de que tiendas grandes vendan naturalmente más que las pequeñas.
2. **Estacionalidad semanal (quincenas, fin de mes y feriados):**
   * *Mecanismo:* **Control por balanceo temporal.** En cada una de las 4 semanas coexisten tiendas en `pasillo_principal` y en `habitual` simultáneamente, asegurando que los picos de consumo por quincena afecten por igual a ambos grupos.
3. **Precio de venta y promociones:**
   * *Mecanismo:* **Constancia estricta y registro de incidencias.** Se mantiene fijo el precio de lista y se prohíbe lanzar descuentos o combos sobre el producto estrella durante las 4 semanas. Si llegara a ocurrir alguna promoción local excepcional, se registra en una columna auxiliar (`promocion_simultanea`) para auditar su efecto.
4. **Quiebres de stock (agotamiento de inventario en percha):**
   * *Mecanismo:* **Control operativo mediante stock de seguridad.** Antes de iniciar cada semana se asegura inventario suficiente en bodega y reposición diaria a primera hora, evitando que una ubicación registre menos ventas simplemente porque se quedó sin unidades disponibles.
5. **Material POP y señalética:**
   * *Mecanismo:* **Estandarización visual.** Tanto en el pasillo principal como en la ubicación habitual se utiliza exactamente la misma etiqueta estándar de precio, sin carteles adicionales ni habladores llamativos que confundan el efecto de la *ubicación* con el efecto de la *publicidad*.

---

## 8. Limitaciones Honestas del Diseño

Reconozco con transparencia las siguientes limitaciones reales del diseño experimental frente a la gerencia:

* **Efecto de arrastre (carryover) o aprendizaje del cliente:** Como alterno la ubicación semana a semana dentro de la misma tienda, un cliente frecuente que vio el producto en el pasillo principal en la Semana 1 podría recordarlo y buscarlo activamente en su percha habitual en la Semana 2, acercando artificialmente las ventas de ambos grupos (sesgo conservador).
* **Posible canibalización de otros productos:** El protocolo mide si aumentan las ventas del *producto estrella*, pero no evalúa si ese incremento ocurre a costa de reducir las ventas de productos competidores dentro de la tienda o del artículo que antes ocupaba ese espacio en el pasillo principal.
* **Horizonte temporal limitado a 4 semanas:** Los resultados reflejan el impacto a corto plazo (un mes). No podemos concluir con este estudio si el aumento de ventas se mantendrá estable durante todo el año una vez que pase el efecto novedad.
* **Imposibilidad de ciego para el personal de tienda:** Aunque se instruya no hacer impulsación verbal, los jefes de local y reponedores saben qué semanas el producto está en el pasillo principal y podrían priorizar inconscientemente su reposición frente a la percha habitual.
