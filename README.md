# Simulación de ventas de café

Vamos a realizar la simulación de ventas de café de una cafetería de la cual se tiene un muestreo limitado.

- Se tiene un muestreo correspondiente al horario de **3:00 a 4:00 pm**
- En ese intervalo se registró la venta de **15 cafés**

Otros datos proporcionados son que existen **2 picos de actividad** los cuales ocurren:
- De **7:30 am a 8:30 am** aproximadamente
- De **6:30 pm a 8:00 pm** que es la hora de cierre

Agregare **1 pico de actividad mas** 
-Justo a medio día **12:00pm**


Con estos datos vamos a proceder haciendo una simulación de una tasa de ventas variable.

# Distribución de Poisson
* La distribución de Poisson es una **distribución discreta de probabilidad** que expresa la probabilidad de un número dado de eventos que ocurren en un intervalo fijo de tiempo, estos eventos pasan con una tasa promedio conocida y de modo independiente un evento de otro. 

# Definición de la distribución de Poisson

En un proceso de Poisson homogéneo (en este caso homogeneo se refiere a una tasa de constante), el número de eventos en un intervalo de longitud **$t$** sigue la distribución:

$$
N(t) \sim \text{Poisson}(\lambda t)
$$

cuya función de masa de probabilidad es:

$$
P(N(t)=k) =
\frac{(\lambda t)^k e^{-\lambda t}}{k!}
$$

# **Sin embargo en este caso la tasa $\lambda$ es variable y podemos modelarla como parte del proceso que no homogeneo de Poisson**

En este caso la tasa de llegada de clientes se modela mediante una función de intensidad dependiente del tiempo:

$$
\lambda(t) = B + \sum_{i=1}^{k}
A_i \exp\left(-\frac{(t-\mu_i)^2}{2\sigma_i^2}\right)
$$

donde:

- $B$ es el nivel base de demanda
- $A_i$ es la intensidad máxima del pico $i$
- $\mu_i$ es la hora central del pico
- $\sigma_i$ controla la duración del pico

# **Importante: Cada uno de los picos se modela mediante un nuevo termino en la sumatoria (en este caso nosotros tenemos 3 picos y corresponderan únicamente 3 terminos).**


En este caso, se consideran tres picos de actividad 8:00 am, 12:00 pm y 6:30 pm, por lo que la función de intensidad (tasa de ventas) queda como:
```math
\lambda(t) = B
+ A_1 \exp\!\left(-\frac{(t-8)^2}{2\sigma_1^2}\right)
+ A_2 \exp\!\left(-\frac{(t-12)^2}{2\sigma_2^2}\right)
+ A_3 \exp\!\left(-\frac{(t-18.5)^2}{2\sigma_3^2}\right)
```

$t$ se mide en horas y $\lambda(t)$ representa cafés por hora.  
El parámetro $B$ se obtiene imponiendo la condición observada a las 15:00 hrs:
```math
\lambda(15\text{ hrs}) = 15\text{ ventas}
```

lo que implica:
```math
B = 15 - \left(
A_1 e^{-\frac{(15-8)^2}{2\sigma_1^2}}
+ A_2 e^{-\frac{(15-12)^2}{2\sigma_2^2}}
+ A_3 e^{-\frac{(15-18.5)^2}{2\sigma_3^2}}
\right)
```

# Simulando mediante Poisson

En un proceso de Poisson no homogéneo, la tasa depende del tiempo y el número de eventos en el intervalo $[a,b]$ satisface:

$$
N(a,b) \sim \text{Poisson}\left(
\int_a^b \lambda(t)\,dt
\right)
$$

Recordemos que Poisson en este caso es:

$$
P(N(t)=k) =
\frac{(\lambda t)^k e^{-\lambda t}}{k!}
$$

La probabilidad de observar $k$ eventos en el intervalo $[a,b]$ es:

$$
P\bigl(N(a,b)=k\bigr) =
\frac{
\left(\int_a^b \lambda(t)\,dt\right)^k
\exp\!\left(-\int_a^b \lambda(t)\,dt\right)
}{k!}
$$

Condicionado a $\lambda(t)$, los eventos son independientes, es decir la compra anterior no afecta a la siguiente.

Esta integral la resolveremos numericamente por el metodo del trapezoide



Para cada hora $h$, el número de cafés vendidos se modela como:

$$
N_h \sim \text{Poisson}(\mu_h),
\quad
\mu_h = \int_h^{h+1} \lambda(t)\,dt
$$

## Integración numérica de $\lambda(t)$

Para calcular la intensidad acumulada en un intervalo de tiempo $[a,b]$,
necesitamos evaluar la integral:

$$
\int_a^b \lambda(t)\,dt
$$

Dado que $\lambda(t)$ es una función dependiente del tiempo y no siempre
admite una solución analítica sencilla, utilizamos un método numérico de
integración: **el método del trapecio**.

Este método aproxima el área bajo la curva dividiendo el intervalo en pequeños
subintervalos y sumando áreas de trapecios.

La aproximación numérica es:

$$
\int_a^b \lambda(t)\,dt \;\approx\; 
\sum_{i=1}^{n-1}
\frac{\lambda(t_i) + \lambda(t_{i+1})}{2}
\,(t_{i+1}-t_i)
$$


# Todo esto lo haremos para ahora ver la planeación de demanda de café 
Sabemos que para producir un café de 16 oz, necesitamos 20g de café molido


Podemos ver que el consumo diario de cafe ronda entorno al promedio del día.

Vamos a calcular el Stock de Seguridad $SS$ 

$SS = P95 - media$


