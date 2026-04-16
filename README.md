# Partones

Reconstrucción no paramétrica de funciones de distribución de partones (PDFs) en [0,1] a partir de un conjunto finito de momentos usando métodos de integración numérica (Trapecio, Simpson y Gauss–Legendre). El repositorio contiene el informe en LaTeX y los notebooks en Wolfram Language / Mathematica usados para generar los resultados y las figuras.

## Contenido del repositorio

- Informe/
  - `informePractica.tex`, `finalisimo.tex`, `ref.bib` — Informe académico con la teoría, metodología, resultados y figuras.
  - Subdirectorios `Trapecio/`, `Simpson/`, `Gauss/`, `RMSE/` con las figuras que acompañan el informe.
- `Aproximaciones.nb` — Notebook en Wolfram Language con funciones principales para reconstrucción y visualización.
- `ErroresAproximación.nb` — Notebook con funciones para cálculo y comparación de errores entre métodos.
- `README.md` — (este archivo) descripción del proyecto y guía rápida.

## Resumen del enfoque

Dado un conjunto de momentos discretos
μn = ∫0^1 φ(x) x^{n-1} dx, n = 1..N,
se aproxima la densidad desconocida φ(x) por un polinomio
f(x) = ∑_{i=0}^{N-1} c_i x^i,
determinando los coeficientes c_i imponiendo que los momentos discretizados del polinomio (calculados por cuadratura) coincidan con los momentos conocidos. El sistema lineal resultante se resuelve con mínimos cuadrados (LeastSquares). Para garantizar que la función reconstruida represente una densidad, se fuerza la positividad usando max(0, polinomio).

Las integrales (momentos) se estiman mediante tres reglas de cuadratura:
- Trapecio (nodos equiespaciados)
- Simpson (nodos equiespaciados con pesos de Simpson)
- Gauss–Legendre (nodos y pesos de Gauss)

Se calcula además:
- Error puntual |φ(x) − f(x)| en una malla fina,
- Error máximo (máxima diferencia en la malla),
- RMSE (root mean square error),
- Una estimación del error de cuadratura (por ejemplo comparando Gauss con n y n−1 nodos).

## Archivos clave y funciones (Wolfram Language)

Los notebooks contienen las funciones principales:

- reconstruirBetaConError[metodo_, Nmomentos_, alpha_, beta_, nPuntos_, silent_: False]
  - metodo: "Trapecio" | "Simpson" | "Gauss"
  - Nmomentos: número de momentos a usar
  - alpha, beta: parámetros de la distribución Beta usada como referencia/simulación
  - nPuntos: número de nodos/cuadratura
  - silent (opcional): si False imprime gráficos/ tablas; si True retorna solo los datos
  - Retorna una Association con:
    - "PhiOriginal": función original (distribución Beta)
    - "PhiRec": función reconstruida (evaluables como funciones de x)
    - "Puntos": vector de evaluación
    - "Errores": vector de errores punto a punto
    - "MaxError": error máximo
    - "RMSE": error cuadrático medio
    - "ErrorIntegracion" (cuando aplica): estimación del error de integración

- reconstruirBetaSilent[metodo_, Nmomentos_, alpha_, beta_, nPuntos_]
  - Versión que no imprime gráficos y devuelve la Association con resultados.

- analizarPrecisionPorMomentos[metodo_, alpha_, beta_, nPuntos_, momentoInicial_, momentoFinal_]
  - Mide y grafica la evolución de error máximo y RMSE al aumentar el número de momentos.

- compararMetodos[metodos_, alpha_, beta_, nPuntos_, momentoInicial_, momentoFinal_]
  - Compara Error Máximo, RMSE y Error de Integración entre varios métodos.

## Ejemplos de uso

Abrir los `.nb` en Mathematica o Wolfram Engine y ejecutar las celdas que definen las funciones. Ejemplos de llamada:

```wolfram
reconstruirBetaConError["Gauss", 5, 3, 7, 30]
reconstruirBetaConError["Simpson", 8, 3, 7, 75]
analizarPrecisionPorMomentos["Trapecio", 3, 7, 30, 1, 10]
compararMetodos[{"Trapecio", "Simpson", "Gauss"}, 3, 7, 30, 1, 10]
```


Las funciones devuelven Associations que permiten seguir procesando los resultados (evaluar la función reconstruida en puntos arbitrarios, extraer vectores de error, etc.).
Requisitos
    Wolfram Mathematica o Wolfram Engine.
    Paquetes (incluidos en los notebooks):
        NumericalDifferentialEquationAnalysis` (se utiliza GaussianQuadratureWeights).
    Sistema para compilar LaTeX si se desea regenerar el PDF del informe (pdflatex / latexmk / Overleaf).

## Notas metodológicas y limitaciones

La reconstrucción polinómica directa puede ser sensible a la condicionalidad numérica del sistema (especialmente con muchos momentos). En el notebook se usa LeastSquares para mitigar problemas en sistemas sobredeterminados/indeterminados.
    
Forzar positividad con Max[0, ·] garantiza densidad no negativa pero puede introducir distorsiones en regiones donde el polinomio original toma valores negativos por oscilaciones numéricas.
Para cuadratura Gauss, el cálculo directo de una cota de error requiere derivadas de orden alto; en el código se estima la estabilidad comparando n y n−1 nodos.
Los resultados y figuras presentes en Informe/ fueron generados con los notebooks incluidos; para reproducir las figuras ejecute las celdas correspondientes.

## Reproducibilidad

Abrir Aproximaciones.nb y ErroresAproximación.nb en Mathematica.
Ejecutar las celdas que definen las funciones.
Llamar a las funciones de ejemplo para generar gráficos y datos.
Si desea recompilar el informe en PDF: compilar Informe/informePractica.tex (asegúrese de que las figuras referenciadas existan en las rutas indicadas dentro de Informe/).

