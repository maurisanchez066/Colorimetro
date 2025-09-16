# Calibración del Sistema

## Configuración Inicial

Para utilizar el programa en tu propio sistema, primero debes reemplazar la calibración con tus propias mediciones.

### 1. Curvas de Intensidad

Reemplaza las curvas de intensidad por tus mediciones para los tres colores:

```python
Curvas_Intensidad_Rojo = {
    "Grados3" : np.array([...
    # Agrega todos tus arrays de mediciones aquí
}
```

**Importante:** 
- Incluye la misma cantidad de arrays como mediciones realizaste por cada color
- Estas mediciones ya deben tener aplicada la corrección del 0

### 2. Manejo de Ángulos Sin Emisión LED

Ten en cuenta las mediciones donde el ángulo gira pero el LED no se enciende. En nuestro caso eran las primeras 3 mediciones, por lo que forzamos el valor 0 en la integral para evitar integrales negativas:

```python
IR = [0, 0, 0] + Integrales_Curvas_Intensidad_Rojo[3:]
IV = [0, 0, 0] + Integrales_Curvas_Intensidad_Verde[3:]
IA = [0, 0, 0] + Integrales_Curvas_Intensidad_Azul[3:]
```

**Ajuste necesario:** Cambia `[3:]` por la cantidad de ángulos para los cuales el LED todavía no se enciende en tu sistema.

### 3. Calibración de Datos

Para la calibración, mantenemos solo el último de los ceros:

```python
IRp = IR[2:]
IVp = IV[2:]
IAp = IA[2:]
```

**Ajuste necesario:** Reemplaza `[2:]` por `"número de ángulos para los cuales no se enciende el LED" - 1`

### 4. Configuración de Ángulos

Reemplaza los ángulos por los que mediste en tu sistema:

```python
# Ángulos tomados en los goniómetros
angulosR = [
    3, 10, 21, 30, 40, 50, 60, 70, 80, 90, 
    100, 110, 120, 130, 140, 150, 160, 170, 180, 
    190, 200, 210, 220, 230, 240, 250, 260, 270, 
    280, 282
]

angulosV = [
    5, 10, 23, 30, 40, 50, 60, 70, 80, 90,
    100, 110, 120, 130, 140, 150, 160, 170, 180,
    190, 200, 210, 220, 230, 240, 250, 260, 270,
    280, 282
]

angulosA = [
    6, 10, 25, 30, 40, 50, 60, 70, 80, 90,
    100, 110, 120, 130, 140, 150, 160, 170, 180,
    190, 200, 210, 220, 230, 240, 250, 260, 270,
    280, 285
]
```

Y también los ángulos recortados:

```python
angulosRp = angulosR[2:]
angulosVp = angulosV[2:]
angulosAp = angulosA[2:]
```

**Ajuste necesario:** Reemplaza `[2:]` por `"número de ángulos para los cuales no se enciende el LED" - 1`

## Modificación de Funciones

### Función intensidades_desde_angulos

**⚠️ Importante:** No toques las funciones a menos que sepas lo que haces. Lo único que deberías adaptar es la función `intensidades_desde_angulos`:

```python
if gR > 21:
    IR = quad(coeffs['R'], gR)
else:
    IR = 0

if gV > 23:
    IV = quad(coeffs['V'], gV)
else:
    IV = 0

if gA > 25:
    IA = quad(coeffs['A'], gA)
else:
    IA = 0
```

**Ajuste necesario:** Los ángulos están condicionados según el mínimo ángulo para el cual el LED emite. Reemplaza `21`, `23` y `25` por tus valores correspondientes.

### Función scales_factors

Debes configurar 3 pares array-integral de la medición de un ángulo determinado, uno para cada color:

```python
scales_factors = (
    Curvas_Intensidad_Rojo_Acotadas["Grados150"] / Integrales_Curvas_Intensidad_Rojo[16],
    Curvas_Intensidad_Verde_Acotadas["Grados160"] / Integrales_Curvas_Intensidad_Verde[17],
    Curvas_Intensidad_Azul_Acotadas["Grados160"] / Integrales_Curvas_Intensidad_Azul[17]
)
```

En nuestro caso utilizamos:
- **Rojo:** ángulo 150°
- **Verde:** ángulo 160°
- **Azul:** ángulo 160°

Estos puntos ajustaban bien a la curva de calibración.

**Nota:** En teoría, como la forma de la curva no cambia (mismo color), deberías poder tomar cualquier ángulo. Reemplaza por los ángulos que consideres más convenientes para tu sistema.

## Ejecución del Programa

### Requisitos

Asegúrate de tener instalado:
- Python
- Matplotlib  
- NumPy

### Uso

Una vez completada la calibración, ejecuta el código desde la terminal:

```bash
python nombre_del_archivo.py
```

El programa te permitirá:
1. Ingresar los ángulos de giro de las perillas de los potenciómetros (Rojo, Verde y Azul)
2. Obtener las coordenadas x e y del color generado

## ⚠️ Advertencia

Si modificaste algo incorrectamente y el programa no funciona, revisa cada uno de los pasos anteriores. Es crucial seguir las instrucciones exactamente como se indican para el correcto funcionamiento del sistema. 

Si tienes más dudas puedes contactarme por mail! mauriciosanchez260602@gmail.com 
