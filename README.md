# Práctica 4 — Modelado y control del sistema endócrino

**Tecnológico Nacional de México · Instituto Tecnológico de Tijuana**  
**Departamento:** Ingeniería Eléctrica y Electrónica  
**Carrera:** Ingeniería Biomédica  
**Asignatura:** Modelado de Sistemas Fisiológicos (IBF-1020)  
**Docente:** Dr. Paul Antonio Valle Trujillo  
**Estudiante:** **Castro Alcántar Daniel Hiram**  
**Número de control:** **23212184**  
**Correo institucional:** **l23212184@tectijuana.edu.mx**  
**Modalidad:** Individual  
**Fecha:** Noviembre de 2026

**Repositorio de la asignatura:** [DrPaulValle/MSF-IBF1020](https://github.com/DrPaulValle/MSF-IBF1020)  
**Documento base:** *Syllabus para Modelado de Sistemas Fisiológicos* (2026), sección 5.6, pp. 96–105.

---

## Contenido

1. [Resumen](#1-resumen)
2. [Competencia y objetivos](#2-competencia-y-objetivos)
3. [Fundamento fisiológico y diagrama BioRender](#3-fundamento-fisiológico-y-diagrama-biorender)
4. [Analogía eléctrica y parámetros](#4-analogía-eléctrica-y-parámetros)
5. [Desarrollo matemático del sistema](#5-desarrollo-matemático-del-sistema)
6. [Funciones de transferencia de control y caso](#6-funciones-de-transferencia-de-control-y-caso)
7. [Representación mediante variables de estado](#7-representación-mediante-variables-de-estado)
8. [Polos, ceros y estabilidad](#8-polos-ceros-y-estabilidad)
9. [Respuesta temporal y error estacionario](#9-respuesta-temporal-y-error-estacionario)
10. [Controlador PID y lazo cerrado](#10-controlador-pid-y-lazo-cerrado)
11. [Implementación en MATLAB, Simulink y Simscape](#11-implementación-en-matlab-simulink-y-simscape)
12. [Implementación en Python](#12-implementación-en-python)
13. [Resultados computacionales](#13-resultados-computacionales)
14. [Resultados experimentales de laboratorio](#14-resultados-experimentales-de-laboratorio)
15. [Discusión de los resultados](#15-discusión-de-los-resultados)
16. [Análisis matemático manuscrito](#16-análisis-matemático-manuscrito)
17. [Archivos del repositorio y reproducibilidad](#17-archivos-del-repositorio-y-reproducibilidad)
18. [Conclusión y referencias](#18-conclusión-y-referencias)

---

## 1. Resumen

En esta práctica se modeló el comportamiento dinámico de un **sistema endócrino simplificado** que representa la secreción, el transporte, el almacenamiento y la respuesta de una señal hormonal. Se utilizó una analogía eléctrica de segundo orden con una resistencia de transporte $R_1$, una capacitancia de almacenamiento $C$ y una rama de respuesta constituida por una inductancia $L$ y una resistencia $R_2$ en serie.

Se analizaron dos condiciones: **control**, que representa una respuesta de referencia, y **caso**, que reproduce una alteración de la capacidad de almacenamiento y de la utilización de la señal. A partir de las leyes de Kirchhoff se obtuvo la ecuación diferencial, la función de transferencia y las ecuaciones de estado. Se calcularon los polos y los errores ante una entrada escalón unitario. Posteriormente se incorporó un **controlador PID** para que la respuesta del caso siguiera la trayectoria del control. Finalmente, se contrastaron las gráficas de MATLAB y Python con una captura del osciloscopio obtenida en laboratorio.

**Palabras clave:** sistema endócrino; secreción hormonal; circuito RLC; función de transferencia; estabilidad; estado estacionario; PID; MATLAB; Simulink; Simscape; Python; BioRender.

## 2. Competencia y objetivos

**Competencia:** modelar procesos fisiológicos mediante analogías eléctricas, ecuaciones diferenciales y funciones de transferencia, así como aplicar estrategias de control clásico para estudiar y modificar su comportamiento dinámico.

**Objetivo general:** analizar y comparar la respuesta dinámica de un modelo endócrino de referencia y un caso alterado, y evaluar un controlador PID que permita aproximar la respuesta del caso a la condición de control.

**Objetivos específicos:**

- Relacionar el proceso de secreción, transporte, almacenamiento y respuesta hormonal con los componentes de un circuito eléctrico.
- Obtener las ecuaciones del circuito mediante leyes de Kirchhoff y transformada de Laplace.
- Determinar las funciones de transferencia para control y caso, sus polos, estabilidad y errores estacionarios.
- Representar el sistema mediante ecuaciones diferenciales y variables de estado.
- Implementar y comparar las respuestas computacionales en MATLAB, Simulink/Simscape y Python.
- Documentar el diseño PID, los resultados y la evidencia del osciloscopio.

## 3. Fundamento fisiológico y diagrama BioRender

El sistema endócrino libera hormonas en respuesta a estímulos neurales, metabólicos o homeostáticos. Después de la secreción, las hormonas se transportan hacia otros compartimentos y pueden almacenarse temporalmente antes de actuar sobre un tejido diana o ser eliminadas. La respuesta del tejido no siempre es inmediata: depende de la disponibilidad de la señal, el transporte y la capacidad de respuesta de los compartimentos involucrados.

El modelo utilizado en esta práctica **no corresponde a una hormona o enfermedad concreta**. Es una analogía fenomenológica con fines académicos para estudiar almacenamiento, disipación y retardo.

![Diagrama BioRender del sistema endócrino, incluyendo la secreción hormonal, el transporte, el compartimento de almacenamiento, el tejido diana y el circuito eléctrico equivalente](imagenes/Biorender.jpeg)

*Figura 1. Esquema fisiológico elaborado en BioRender. Se observa el recorrido desde el estímulo hasta el tejido diana, la correspondencia con los elementos $R_1$, $C$, $L$ y $R_2$ y la comparación de parámetros entre control y caso.*

**Interpretación del diagrama:**

| Elemento fisiológico | Representación | Interpretación del modelo |
|---|---|---|
| Estímulo para la secreción | $V_e(t)$ | Entrada eléctrica que inicia la respuesta del sistema |
| Secreción y transporte | $F_e(t)$ | Flujo asociado con el traslado de la señal hormonal |
| Oposición y pérdidas de transporte | $R_1$ | Disipación efectiva durante el transporte |
| Almacenamiento temporal | $C$ | Capacidad de acumular parte de la señal |
| Inercia de respuesta del tejido diana | $L$ | Oposición dinámica a cambios rápidos |
| Utilización o eliminación | $R_2$ | Proceso resistivo en la rama de respuesta |
| Flujo de respuesta | $F_s(t)$ | Flujo de la rama $L$–$R_2$ |
| Nivel hormonal efectivo | $V_s(t)$ | Voltaje del nodo de salida, normalizado en la analogía |

Los voltajes y las corrientes son **magnitudes del circuito**, no mediciones clínicas directas de concentración hormonal.

## 4. Analogía eléctrica y parámetros

La fuente de entrada $V_e(t)$ está conectada en serie con $R_1$. Después de $R_1$ se encuentra el nodo de salida **$V_s(t)$**. Desde ese nodo parten dos ramas hacia tierra: una con el capacitor $C$ y otra con el inductor $L$ **en serie** con $R_2$. Esta topología corresponde al circuito incluido en el BioRender y descrito en el syllabus.

| Componente | Control | Caso | Unidad |
|---|---:|---:|---|
| $R_1$ | 1 | 1 | kΩ |
| $L$ | 100 | 100 | mH |
| $C$ | **1** | **1000** | µF |
| $R_2$ | **100** | **1** | kΩ |

En unidades del SI:

$$
\begin{aligned}
R_1&=1000\;\Omega,\qquad L=0.1\;\mathrm{H},\\
(C_c,R_{2c})&=(10^{-6}\;\mathrm F,\;100000\;\Omega),\\
(C_a,R_{2a})&=(10^{-3}\;\mathrm F,\;1000\;\Omega).
\end{aligned}
$$

En el caso se incrementa la capacitancia de $1\,\mu\mathrm F$ a $1000\,\mu\mathrm F$ y se reduce $R_2$ de $100\,\mathrm{k}\Omega$ a $1\,\mathrm{k}\Omega$. Por lo tanto, cambian tanto el tiempo de respuesta como el nivel final de la salida.

## 5. Desarrollo matemático del sistema

### 5.1. Definición de variables

- $V_e(t)$: voltaje de entrada (estímulo).
- $V_s(t)$: voltaje de salida en la unión de $R_1$, $C$ y $L$.
- $F_e(t)$: corriente que pasa por $R_1$.
- $F_s(t)$: corriente que circula por la rama $L$–$R_2$.
- $F_C(t)$: corriente por el capacitor.

### 5.2. Ley de corriente de Kirchhoff

En el nodo de salida, la corriente de entrada se divide entre el capacitor y la rama del tejido diana:

$$
\boxed{F_e(t)=F_C(t)+F_s(t)}
$$

Como la corriente del capacitor es $F_C(t)=C\,dV_s(t)/dt$:

$$
\boxed{F_e(t)=C\frac{dV_s(t)}{dt}+F_s(t)} \tag{1}
$$

### 5.3. Relaciones de voltaje

Por la ley de Ohm sobre $R_1$:

$$
\boxed{V_e(t)-V_s(t)=R_1F_e(t)} \tag{2}
$$

Sobre la rama conectada en serie de $L$ y $R_2$:

$$
\boxed{V_s(t)=L\frac{dF_s(t)}{dt}+R_2F_s(t)} \tag{3}
$$

El balance de corriente también permite obtener la forma integro-diferencial:

$$
V_s(t)=\frac{1}{C}\int_0^t\left[F_e(\tau)-F_s(\tau)\right]d\tau,
$$

con $V_s(0)=0$. En consecuencia:

$$
\boxed{V_e(t)=R_1F_e(t)+\frac{1}{C}\int_0^t\left[F_e(\tau)-F_s(\tau)\right]d\tau}. \tag{4}
$$

Estas ecuaciones son equivalentes a las empleadas en el análisis manuscrito.

### 5.4. Transformada de Laplace

Considerando **condiciones iniciales nulas**, las ecuaciones (1)–(3) se expresan como:

$$
\begin{aligned}
F_e(s)&=CsV_s(s)+F_s(s),\\
V_e(s)-V_s(s)&=R_1F_e(s),\\
V_s(s)&=(Ls+R_2)F_s(s).
\end{aligned}
$$

De la tercera ecuación:

$$
F_s(s)=\frac{V_s(s)}{Ls+R_2}.
$$

Sustituyendo en el balance de corriente:

$$
F_e(s)=CsV_s(s)+\frac{V_s(s)}{Ls+R_2}.
$$

Ahora se sustituye en el voltaje de entrada:

$$
V_e(s)-V_s(s)=R_1\left[CsV_s(s)+\frac{V_s(s)}{Ls+R_2}\right].
$$

Factorizando $V_s(s)$:

$$
V_e(s)=V_s(s)\left[1+R_1Cs+\frac{R_1}{Ls+R_2}\right].
$$

Multiplicando ambos miembros por $(Ls+R_2)$:

$$
V_e(s)(Ls+R_2)=V_s(s)\left[(Ls+R_2)+R_1Cs(Ls+R_2)+R_1\right].
$$

Al desarrollar el polinomio:

$$
V_e(s)(Ls+R_2)=V_s(s)\left[CL R_1s^2+(CR_1R_2+L)s+(R_1+R_2)\right].
$$

**Función de transferencia general del sistema endócrino:**

$$
\boxed{G(s)=\frac{V_s(s)}{V_e(s)}=\frac{Ls+R_2}{CL R_1s^2+(CR_1R_2+L)s+(R_1+R_2)}}. \tag{5}
$$

La planta es de **segundo orden** porque tiene dos elementos de almacenamiento independientes: $C$ y $L$.

### 5.5. Ecuación diferencial de segundo orden

Aplicando la transformada inversa a la ecuación (5), bajo el modelo lineal y las condiciones indicadas:

$$
\boxed{R_1CL\frac{d^2V_s}{dt^2}+(R_1CR_2+L)\frac{dV_s}{dt}+(R_1+R_2)V_s=L\frac{dV_e}{dt}+R_2V_e}. \tag{6}
$$

Esto permite obtener la salida directamente en el dominio del tiempo sin depender de un circuito físico.

## 6. Funciones de transferencia de control y caso

### 6.1. Condición control

Con $R_1=1000\,\Omega$, $R_2=100000\,\Omega$, $L=0.1\,\mathrm H$ y $C=10^{-6}\,\mathrm F$:

$$
\begin{aligned}
CL R_1 &= 10^{-6}(0.1)(1000)=0.0001,\\
CR_1R_2+L &=10^{-6}(1000)(100000)+0.1=100.1,\\
R_1+R_2&=101000.
\end{aligned}
$$

$$
\boxed{G_{\mathrm{control}}(s)=\frac{0.1s+100000}{0.0001s^2+100.1s+101000}}. \tag{7}
$$

### 6.2. Condición caso

Con $R_1=1000\,\Omega$, $R_2=1000\,\Omega$, $L=0.1\,\mathrm H$ y $C=10^{-3}\,\mathrm F$:

$$
\begin{aligned}
CL R_1 &= 10^{-3}(0.1)(1000)=0.1,\\
CR_1R_2+L &=10^{-3}(1000)(1000)+0.1=1000.1,\\
R_1+R_2&=2000.
\end{aligned}
$$

$$
\boxed{G_{\mathrm{caso}}(s)=\frac{0.1s+1000}{0.1s^2+1000.1s+2000}}. \tag{8}
$$

**Comparación:** la topología es la misma, pero la modificación de $C$ y $R_2$ produce denominadores, polos y ganancias estáticas diferentes.

## 7. Representación mediante variables de estado

Se seleccionaron las variables asociadas con los elementos de almacenamiento:

$$
x_1(t)=V_s(t),\qquad x_2(t)=F_s(t),\qquad u(t)=V_e(t).
$$

De las ecuaciones (1) y (2):

$$
\dot x_1(t)=-\frac{1}{R_1C}x_1(t)-\frac{1}{C}x_2(t)+\frac{1}{R_1C}u(t).
$$

De la ecuación (3):

$$
\dot x_2(t)=\frac{1}{L}x_1(t)-\frac{R_2}{L}x_2(t).
$$

Por lo tanto, la representación matricial es:

$$
\boxed{
\frac{d}{dt}\begin{bmatrix}x_1\\x_2\end{bmatrix}
=\underbrace{\begin{bmatrix}-\frac{1}{R_1C}&-\frac{1}{C}\\\frac{1}{L}&-\frac{R_2}{L}\end{bmatrix}}_{A}
\begin{bmatrix}x_1\\x_2\end{bmatrix}
+\underbrace{\begin{bmatrix}\frac{1}{R_1C}\\0\end{bmatrix}}_{B}u(t)
},
$$

$$
\boxed{y(t)=\underbrace{\begin{bmatrix}1&0\end{bmatrix}}_{C_s}\begin{bmatrix}x_1\\x_2\end{bmatrix},\qquad D=0.}
$$

Con los parámetros del ejercicio:

$$
A_{\mathrm{control}}=\begin{bmatrix}-1000&-1000000\\10&-1000000\end{bmatrix},\quad
B_{\mathrm{control}}=\begin{bmatrix}1000\\0\end{bmatrix},
$$

$$
A_{\mathrm{caso}}=\begin{bmatrix}-1&-1000\\10&-10000\end{bmatrix},\quad
B_{\mathrm{caso}}=\begin{bmatrix}1\\0\end{bmatrix}.
$$

La matriz $C_s=[1\ \ 0]$ es común a ambas condiciones y representa la lectura del voltaje del nodo.

## 8. Polos, ceros y estabilidad

Los polos se obtienen al resolver el denominador característico:

$$
a s^2+b s+c=0,\qquad
\boxed{s_{1,2}=\frac{-b\pm\sqrt{b^2-4ac}}{2a}},
$$

siendo $a=R_1CL$, $b=R_1CR_2+L$ y $c=R_1+R_2$.

| Condición | Polo 1 ($\mathrm{s}^{-1}$) | Polo 2 ($\mathrm{s}^{-1}$) | Cero ($\mathrm{s}^{-1}$) | Clasificación |
|---|---:|---:|---:|---|
| Control | −999989.9899 | −1010.0101 | −1000000 | Estable, sobreamortiguado |
| Caso | −9998.9998 | −2.0002 | −10000 | Estable, sobreamortiguado |

El cero de cada planta se encuentra en $s=-R_2/L$. Todos los polos tienen parte real negativa y, por lo tanto, ambos sistemas son **asintóticamente estables**. Como los polos de cada sistema son reales, distintos y negativos, las respuestas son sobreamortiguadas. La cercanía de un cero a un polo rápido explica por qué en las gráficas predomina una evolución visual parecida a la de un sistema de primer orden.

**Plano complejo — condición control:**

![Localización de los polos del sistema control](imagenes/Polos_control.png)

**Plano complejo — condición caso:**

![Localización de los polos del sistema caso](imagenes/Polos_caso.png)

En la condición control, la dinámica dominante está asociada al polo $-1010.0101\,\mathrm{s}^{-1}$ (escala temporal aproximada $0.000990\,\mathrm{s}$). En el caso, el polo dominante es $-2.0002\,\mathrm{s}^{-1}$ (escala aproximada $0.500\,\mathrm{s}$), lo que anticipa una respuesta considerablemente más lenta.

## 9. Respuesta temporal y error estacionario

### 9.1. Entrada escalón

Durante las simulaciones se aplicó una entrada **escalón unitario**, no un impulso de Dirac:

$$
V_e(t)=u(t)=\begin{cases}0,&t<0,\\1,&t\ge0.\end{cases}
\qquad\Longrightarrow\qquad V_e(s)=\frac{1}{s}.
$$

Se analizó el intervalo $0\le t\le10\,\mathrm{s}$, con condiciones iniciales nulas.

### 9.2. Teorema del valor final

Para un sistema estable:

$$
V_s(\infty)=\lim_{s\to0}s\,G(s)\frac1s=G(0)=\frac{R_2}{R_1+R_2}.
$$

Para el **control**:

$$
V_{s,\mathrm{control}}(\infty)=\frac{100000}{101000}=\boxed{0.990099\;\mathrm V}.
$$

Para el **caso**:

$$
V_{s,\mathrm{caso}}(\infty)=\frac{1000}{2000}=\boxed{0.500000\;\mathrm V}.
$$

El error de seguimiento se define como $e(t)=V_e(t)-V_s(t)$ para cada **planta en lazo abierto**:

$$
\boxed{e_{ss}=\lim_{t\to\infty}[V_e(t)-V_s(t)]=1-G(0)}.
$$

Así, los errores son:

$$
\boxed{e_{ss,\mathrm{control}}=1-0.990099=0.009901\;\mathrm V},
$$

$$
\boxed{e_{ss,\mathrm{caso}}=1-0.5=0.5\;\mathrm V}.
$$

**Interpretación:** el control se aproxima a $1\,\mathrm V$, mientras que el caso se establece en $0.5\,\mathrm V$. La diferencia entre las dos salidas finales es aproximadamente $0.490099\,\mathrm V$.

### 9.3. Respuesta temporal analítica

La descomposición en fracciones parciales de $V_s(s)=G(s)/s$ permite escribir, para $t\ge0$, las respuestas al escalón (coeficientes redondeados):

$$
\begin{aligned}
V_{s,\mathrm{control}}(t)\approx{}&0.9900990099-0.9900990199e^{-1010.01011t}\\
&+1.00204\times10^{-8}e^{-999989.98989t}.
\end{aligned}
$$

$$
\begin{aligned}
V_{s,\mathrm{caso}}(t)\approx{}&0.5-0.5000000100e^{-2.00020006t}\\
&+1.00060\times10^{-8}e^{-9998.99980t}.
\end{aligned}
$$

Estas expresiones muestran por qué el control alcanza su valor final casi de inmediato en una gráfica de diez segundos, mientras que el caso presenta una transición visible de varios segundos.

## 10. Controlador PID y lazo cerrado

### 10.1. Objetivo y señal de error

El **modelo control** es la referencia deseada y el **modelo caso** es la planta que se busca corregir. La señal de referencia y el error del sistema retroalimentado se expresan como:

$$
r(t)=V_{s,\mathrm{control}}(t),\qquad
\boxed{e(t)=r(t)-V_{s,\mathrm{caso,PID}}(t)}.
$$

Antes de aplicar el controlador, la diferencia entre los dos modelos es $V_{s,\mathrm{control}}(t)-V_{s,\mathrm{caso}}(t)$.

### 10.2. Ganancias utilizadas en Python

Las ganancias de referencia que se documentan aquí son **únicamente las definidas en el código Python suministrado**:

| Ganancia | Valor en el archivo `Castro23212184.py` |
|---|---:|
| $k_P$ | **13209.9564** |
| $k_I$ | **32316525.9285** |
| $k_D$ | **0.18118** |

Para un PID ideal en forma paralela, la ley de control se expresa como:

$$
u(t)=k_Pe(t)+k_I\int_0^te(\tau)d\tau+k_D\frac{de(t)}{dt},
$$

$$
K_{\mathrm{ideal}}(s)=k_P+\frac{k_I}{s}+k_Ds.
$$

### 10.3. Expresión del controlador que implementa el script

El programa Python calcula los componentes equivalentes a partir de $C_r=1\,\mu\mathrm F$:

$$
R_e=\frac{1}{C_rk_I},\qquad R_r=k_PR_e,\qquad C_e=\frac{k_D}{R_r}.
$$

Los valores calculados a partir del script son:

| Parámetro | Resultado calculado |
|---|---:|
| $C_r$ | $1\,\mu\mathrm F$ |
| $R_e$ | $0.0309439\,\Omega$ |
| $R_r$ | $408.7678\,\Omega$ |
| $C_e$ | $443.2345\,\mu\mathrm F$ |

La función implementada en el archivo Python está escrita exactamente con esta estructura:

$$
\boxed{K_{\mathrm{Python}}(s)=\frac{R_eR_rC_eC_rs^2+(R_eC_e+R_rC_r)s+1}{R_eC_rs}}. \tag{9}
$$

Al desarrollar el cociente:

$$
K_{\mathrm{Python}}(s)=R_rC_es+\left(\frac{R_r}{R_e}+\frac{C_e}{C_r}\right)+\frac{1}{R_eC_rs}.
$$

Con las variables calculadas en el mismo script, esto equivale aproximadamente a:

$$
\boxed{K_{\mathrm{Python}}(s)=0.18118s+13653.19088+\frac{32316525.9285}{s}}.
$$

La forma racional implementada incorpora el término adicional $C_e/C_r$ en el coeficiente proporcional efectivo. Se muestra este desarrollo para **describir fielmente el algoritmo utilizado**, sin introducir otro conjunto de ganancias obtenido por separado. Estos componentes son resultados algebraicos del programa; **no se afirma que constituyan valores comerciales seleccionados o un controlador analógico físicamente construido**.

### 10.4. Función de transferencia en lazo cerrado

Con retroalimentación negativa unitaria, la planta del caso controlado es:

$$
\boxed{T_{\mathrm{PID}}(s)=\frac{K_{\mathrm{Python}}(s)G_{\mathrm{caso}}(s)}{1+K_{\mathrm{Python}}(s)G_{\mathrm{caso}}(s)}}. \tag{10}
$$

Reemplazando las funciones:

$$
T_{\mathrm{PID}}(s)=\frac{K_{\mathrm{Python}}(s)(0.1s+1000)}{0.1s^2+1000.1s+2000+K_{\mathrm{Python}}(s)(0.1s+1000)}.
$$

En el script se implementa mediante:

```python
sysPID = ctrl.feedback(ctrl.series(PID, sysCaso), 1, sign=-1)
Tratamiento = closedloop(t, sysPID, Control)
```

La entrada del sistema en lazo cerrado **no es directamente el escalón original**, sino la **respuesta del control** (`Control`), utilizada como trayectoria de referencia. El efecto buscado es disminuir la diferencia entre esa referencia y la salida del caso controlado.

## 11. Implementación en MATLAB, Simulink y Simscape

Se incluyeron los archivos originales del trabajo:

- **[Sistema.slx](codigo/Sistema.slx):** modelo de simulación con las representaciones de control, caso y sistema retroalimentado.
- **[Castro23212184.mlx](codigo/Castro23212184.mlx):** cuaderno computacional de MATLAB.
- **[Castro23212184.pdf](documentos/Castro23212184.pdf):** exportación del cuaderno con código y gráfica.
- **[Gráfica exportada desde MATLAB (PDF)](documentos/casocontrol.matlab.pdf).**

El cuaderno abre el proyecto `Sistema`, establece diez segundos de simulación y utiliza un solucionador adecuado para sistemas con escalas temporales distintas:

```matlab
clc; clear; close all; warning('off','all')
open_system('Sistema')
parameters.StopTime = '10';
parameters.Solver = 'ode15s';
parameters.MaxStep = '1e-3';
x = sim('Sistema',parameters);
plotsignals(x.t,x.Ve,x.Fsx,x.Fsy,x.PID,'Control y caso')
```

Las representaciones de ecuaciones integro-diferenciales, función de transferencia y componentes eléctricos se comparan bajo una entrada común. Las señales de salida del cuaderno se recuperan desde Simulink para generar la figura que se presenta en la sección de resultados.

## 12. Implementación en Python

El archivo **[Castro23212184.py](codigo/Castro23212184.py)** utiliza `numpy`, `python-control` y `matplotlib`. Las funciones de transferencia se construyen a partir de los mismos parámetros $R_1$, $L$, $C$ y $R_2$:

```python
def sis(C, R2, nombre):
    R1, L = 1E3, 100E-3
    num = [L, R2]
    den = [C*L*R1, C*R1*R2+L, R1+R2]
    sys = ctrl.tf(num, den)
    print(f"Función de transferencia {nombre}: {sys}\n")
    return sys

sysControl = sis(1E-6, 100E3, "Control")
sysCaso = sis(1000E-6, 1E3, "Caso")
```

Se establece un vector temporal de $10\,001$ muestras, desde $0$ hasta $10\,\mathrm s$, con $\Delta t=10^{-3}\,\mathrm s$ y entrada $u(t)=1$. El código calcula las respuestas con `forced_response` y construye el sistema retroalimentado mediante `feedback` y `series`.

**Ejecución desde la raíz del repositorio:**

```bash
python -m pip install -r requirements.txt
python codigo/Castro23212184.py
```

Al ejecutarlo, el programa muestra las respuestas y guarda `Control_caso_python4.pdf` en el directorio actual. La versión original de ese archivo se incluye en la carpeta `documentos/`.

## 13. Resultados computacionales

### 13.1. Respuesta obtenida en MATLAB

![Comparación de señales del sistema endócrino obtenida en MATLAB](imagenes/casocontrol_matlab.png)

*Figura 2. Respuesta comparativa exportada del cuaderno de MATLAB: entrada, control, caso y caso con controlador.*  
[**Abrir gráfica MATLAB en PDF**](documentos/casocontrol.matlab.pdf)

La curva del **caso sin controlador** se aproxima gradualmente a $0.5\,\mathrm V$, mientras que las correspondientes a la condición control y a la respuesta controlada se sitúan cerca del valor de referencia. La diferencia temporal es visible principalmente en el caso, cuya respuesta es más lenta.

### 13.2. Respuesta obtenida en Python

![Comparación de señales del sistema endócrino obtenida en Python](imagenes/Control_caso_python4.png)

*Figura 3. Señal escalón de entrada, respuesta del control, respuesta del caso y respuesta controlada con PID.*  
[**Abrir gráfica Python en PDF**](documentos/Control_caso_python4.pdf)

En la figura de Python se aprecia el mismo comportamiento general: el modelo control alcanza rápidamente cerca de $0.99\,\mathrm V$, el caso sin controlador converge a $0.5\,\mathrm V$ y la respuesta controlada se aproxima a la referencia.

### 13.3. Resumen de resultados

| Magnitud | Control | Caso sin PID | Caso con PID |
|---|---:|---:|---|
| Valor final del modelo ante escalón de $1\,\mathrm V$ | $0.990099\,\mathrm V$ (analítico) | $0.500000\,\mathrm V$ (analítico) | Próximo a la referencia del control en las gráficas |
| Estabilidad de la planta en lazo abierto | Estable | Estable | No aplica: existe realimentación |
| Velocidad de respuesta | Muy rápida | Más lenta | Respuesta acelerada en las figuras |
| Error respecto a la entrada en lazo abierto | $0.009901\,\mathrm V$ | $0.500000\,\mathrm V$ | Se evalúa respecto a $V_{s,\mathrm{control}}(t)$, no respecto al escalón directamente |

**Nota de verificación:** las cifras de estado estacionario y los polos proceden del modelo matemático. Las comparaciones del caso controlado describen lo observado en las figuras entregadas; no se reportan porcentajes de mejora ni métricas de tiempo de establecimiento que no estén medidos explícitamente.

## 14. Resultados experimentales de laboratorio

El circuito eléctrico equivalente permite comprobar la respuesta del modelo con componentes físicos, un generador de funciones y un osciloscopio. La entrada escalón se aproxima experimentalmente mediante una onda de pulso de período largo, tal como se realiza habitualmente en esta asignatura.

![Captura de osciloscopio tomada durante la práctica del sistema endócrino](imagenes/Laboratorio.jpg)

*Figura 4. Captura original del osciloscopio OWON proporcionada como evidencia del laboratorio.*

En la captura, la traza verde parte aproximadamente de cero y asciende hasta una meseta cercana a **$0.5\,\mathrm V$**, mientras que otras trazas se mantienen cerca de **$1\,\mathrm V$** tras el escalón. Esto coincide **cualitativamente** con el comportamiento esperado para el caso y para la condición de referencia, respectivamente. Se observan indicaciones de máximo de aproximadamente $980\,\mathrm{mV}$ en el canal 1 y $960\,\mathrm{mV}$ en el canal 2; esos máximos no sustituyen una medición calibrada del estado estacionario de todas las señales.

La captura disponible respalda la observación de las formas de onda, pero no documenta por sí sola todos los montajes ni la implementación física de un controlador PID. Entre las causas de discrepancias entre el circuito ideal y la medición real pueden encontrarse las tolerancias de resistencias y capacitores, la resistencia parásita del inductor, la configuración de la fuente, la conexión de sondas y las características del generador.

## 15. Discusión de los resultados

La diferencia principal entre las dos condiciones se explica por los cambios simultáneos en $C$ y $R_2$. En el modelo de control, la capacitancia pequeña permite una transición muy rápida y la resistencia $R_2$ alta produce una ganancia estática cercana a la unidad. En el caso, la capacitancia es mil veces mayor y la resistencia de salida es mucho menor, por lo que el sistema responde más lentamente y la salida final disminuye a la mitad del escalón aplicado.

Los cálculos de polos son consistentes con las gráficas: ambas condiciones son estables, pero sus escalas temporales son muy distintas. La forma de las respuestas también se entiende al observar que cada función de transferencia tiene un cero próximo a su polo de dinámica rápida. Por este motivo, las curvas visibles parecen de primer orden, aunque la ecuación original sea de segundo orden.

La incorporación del PID en el programa permite que el caso siga una trayectoria muy cercana al control. Las figuras de MATLAB y Python coinciden en la tendencia general y en los niveles finales de las plantas sin control; no se ha calculado un error numérico punto a punto entre herramientas, por lo que no se afirma identidad exacta entre implementaciones.

Desde el punto de vista fisiológico, el ejercicio demuestra cómo cambios en almacenamiento y disipación modifican la disponibilidad de una señal y el tiempo que tarda en responder un compartimento. Sin embargo, **las unidades eléctricas y las ganancias PID no deben interpretarse como concentraciones reales de hormonas, dosis ni tratamientos médicos**.

## 16. Análisis matemático manuscrito

Se conserva el archivo original de **tres páginas** con el circuito dibujado, ecuaciones de Kirchhoff, desarrollo de la función de transferencia, cálculo de polos, errores y bosquejos de respuesta.

[**Ver análisis matemático completo en PDF**](documentos/Analisis_matematico.pdf)

<details>
<summary><strong>Mostrar las tres páginas escaneadas dentro del README</strong></summary>

**Página 1 — Circuito y desarrollo de las ecuaciones**

![Primera página de análisis matemático manuscrito](imagenes/Analisis_matematico_p1.png)

**Página 2 — Función de transferencia, estabilidad y control**

![Segunda página de análisis matemático manuscrito](imagenes/Analisis_matematico_p2.png)

**Página 3 — Errores, bosquejos y modelo por ecuaciones**

![Tercera página de análisis matemático manuscrito](imagenes/Analisis_matematico_p3.png)

</details>

Los pasos algebraicos principales se desarrollan **también en texto y ecuaciones en las secciones 5–10**, de modo que pueden leerse directamente en GitHub sin abrir el PDF manuscrito.

## 17. Archivos del repositorio y reproducibilidad

### 17.1. Estructura

```text
Practica4_Sistema_Endocrino_Castro23212184/
├── README.md
├── CITATION.cff
├── requirements.txt
├── codigo/
│   ├── Castro23212184.py
│   ├── Castro23212184.mlx
│   └── Sistema.slx
├── documentos/
│   ├── Analisis_matematico.pdf
│   ├── Castro23212184.pdf
│   ├── casocontrol.matlab.pdf
│   └── Control_caso_python4.pdf
└── imagenes/
    ├── Biorender.jpeg
    ├── Laboratorio.jpg
    ├── casocontrol_matlab.png
    ├── Control_caso_python4.png
    ├── Analisis_matematico_p1.png
    ├── Analisis_matematico_p2.png
    ├── Analisis_matematico_p3.png
    ├── Polos_control.png
    └── Polos_caso.png
```

Los archivos `.png` de las gráficas y de las páginas manuscritas se incluyen específicamente para que el contenido pueda **verse como imagen dentro de GitHub**. Los PDF originales se mantienen disponibles para abrirlos o descargarlos.

### 17.2. Requisitos

- MATLAB, Simulink y Simscape Electrical para examinar y ejecutar el modelo `.slx` y el cuaderno `.mlx` (según las licencias y versiones disponibles).
- Python 3 con los paquetes indicados en `requirements.txt`.
- Un navegador con soporte de Markdown y ecuaciones matemáticas de GitHub.
- Osciloscopio y generador de funciones para reproducir la etapa experimental.

### 17.3. Procedimiento de reproducción

1. Identificar la topología del circuito y comprobar los valores de $R_1$, $L$, $C$ y $R_2$.
2. Verificar mediante las ecuaciones (5), (7) y (8) las dos funciones de transferencia.
3. Revisar los polos, ceros y el error en estado estacionario.
4. Abrir `codigo/Sistema.slx` en Simulink y ejecutar la simulación con entrada escalón de diez segundos.
5. Abrir el cuaderno `codigo/Castro23212184.mlx` con el modelo disponible en su ruta de trabajo.
6. Ejecutar `codigo/Castro23212184.py` para obtener las respuestas correspondientes a los dos modelos y al caso controlado.
7. Comparar las figuras PDF y las imágenes del repositorio.
8. Contrastar las amplitudes y los transitorios con la captura experimental, considerando las limitaciones del circuito y del instrumental.

## 18. Conclusión y referencias

### Conclusión

En esta práctica pude comprobar cómo un proceso fisiológico puede estudiarse mediante un circuito eléctrico y un modelo matemático. Al cambiar la capacitancia y la resistencia de la rama de respuesta, observé que el caso tarda más en estabilizarse y alcanza un valor menor que el control. Los cálculos de polos y error coincidieron con la tendencia de las gráficas de MATLAB y Python. Además, el controlador PID permitió acercar la respuesta del caso a la del control, mientras que la medición del osciloscopio ayudó a relacionar la simulación con el comportamiento del circuito real.

### Referencias

1. P. A. Valle Trujillo, *Syllabus para Modelado de Sistemas Fisiológicos*, Tecnológico Nacional de México / Instituto Tecnológico de Tijuana, 2026, sección 5.6, pp. 96–105.
2. M. C. K. Khoo, *Physiological Control Systems: Analysis, Simulation, and Estimation*, 2.ª ed., IEEE Press, 2018.
3. F. C. Hoppensteadt y C. S. Peskin, *Modeling and Simulation in Medicine and the Life Sciences*, Springer.
4. J. E. Hall, *Guyton and Hall Textbook of Medical Physiology*, 14.ª ed., Elsevier, 2021.
5. K. Ogata, *Ingeniería de Control Moderna*, 5.ª ed., Pearson, 2010.
6. N. S. Nise, *Control Systems Engineering*, 8.ª ed., Wiley, 2020.
7. [BioRender](https://www.biorender.com/), herramienta empleada en la elaboración del diagrama fisiológico.

---

**Autor:** Castro Alcántar Daniel Hiram · **23212184** · **Ingeniería Biomédica, Instituto Tecnológico de Tijuana**.
