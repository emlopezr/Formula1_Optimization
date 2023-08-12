# Optimización del calendario de la Formula 1
- **Materia:** Análisis y Diseño de Algoritmos
- **Profesor:** Julián Moreno Cadavid
- **Institución:** Universidad Nacional de Colombia sede Medellín
- **Semestre:** 2023-1

## Equipo de trabajo
- Emmanuel López Rodríguez - [emlopezr@unal.edu.co](mailto:emlopezr@unal.edu.co)
- Andrés Felipe Aparicio Mestre - [anapariciom@unal.edu.co](mailto:anapariciom@unal.edu.co)
- Maria Paula Duque Muñoz - [maduquem@unal.edu.co](mailto:maduquem@unal.edu.co)
- Johan Madroñero Cuervo - [jmadronero@unal.edu.co](mailto:jmadronero@unal.edu.co)

## Código e informe
Se puede encontrar el código de Python en el archivo [Formula1_Optimization.ipynb](https://github.com/lopezemmanuel/Formula1_Optimization/blob/main/Formula1_Optimization.ipynb). A su vez, se puede encontrar el informe académico detallado del proyecto en el archivo [Formula1_Optimization.pdf](https://github.com/lopezemmanuel/Formula1_Optimization/blob/main/Formula1_Optimization.pdf)

## Planteamiento

![image1](https://github.com/lopezemmanuel/Formula1_Optimization/assets/45053254/e4a0ad69-bd7b-49e3-a5e8-9d08adb8b711)

## Solución
### Preprocesamiento de los datos

Inicialmente se consiguen las coordenadas de todos los circuitos del calendario 2023 de la Formula 1 *(Incluyendo el "Autodromo Internazionale Enzo e Dino Ferrari" en Imola, ya que este año se canceló pero inicialmente hacía parte del calendario)*

- Los datos y el orden de los circuitos se pueden consultar en estos enlaces [Wikipedia](https://en.wikipedia.org/wiki/2023_Formula_One_World_Championship#Calendar) y [Formula1.com](https://www.formula1.com/en/racing/2023.html)

- Se obtuvo la latitud y longitud de cada circuito usando la página [GeoHack](https://geohack.toolforge.org/)

Mediante la fórmula del semiverseno ([Harvesine Formula](https://en.wikipedia.org/wiki/Haversine_formula)) se calculan las distancias entre todos ellos y con ellas se construye un grafo representado en un diccionario de diccinarios.

$$d = 2R \, \arcsin \sqrt{\sin^{2} \frac{\varphi_{1} - \varphi_{2}}{2} + \cos\varphi_{1} \cos\varphi_{2} \sin^{2} \frac{\lambda_{1} - \lambda_{2}}{2}}$$

```
{
    "Sakhir": {
        "Sakhir": Infinity,
        "Jeddah": 1258.42,
        "Melbourne": 12112.65,
        "Baku": 1595.69,
        "Miami": 12185.61,
        "Imola": 4018.42,
        "Montecarlo": 4332.64,
        "Barcelona": 4710.92,
        "Montreal": 10258.89,
        "Spielberg": 3910.83,
        "Silverstone": 5158.07,
        "Budapest": 3629.0,
        "Spa-Francorchamps": 4640.38,
        "Zandvoort": 4805.04,
        "Monza": 4242.25,
        "Marina Bay": 6327.17,
        "Suzuka": 8054.3,
        "Lusail": 112.11,
        "Austin": 12908.76,
        "Mexico City": 13990.04,
        "Sao Paulo": 11813.24,
        "Las Vegas": 12942.72,
        "Abu Dhabi": 446.84
  }
  ...
}

```

### Cálculo de la distancia recorrida actual
```
Total recorrido actual: 132163.47 Km
```

### Solución inical (Algoritmo greedy)
En primer lugar se realiza una aproximación greedy al problema. Por lo que se convierte el grafo en una representación matricial y la solución inicial consiste en elegir por cada fila la distancia más cercana a recorrer en el próximo paso.

```
   inf     1258.42   12112.65  1595.69   12185.61  4018.42   4332.64   4710.92   10258.89  3910.83   5158.07    3629.0   4640.38   4805.04   4242.25   6327.17    8054.3    112.11   12908.76  13990.04  11813.24  12942.72   446.84  
 1258.42     inf     12817.13  2317.67   11605.61  3559.52   3810.51   4087.37   9929.36   3585.07   4815.75   3387.96   4307.68   4515.09   3797.32   7353.78   9293.26   1329.12   12632.6   13574.55  10555.29  13046.96  1615.84  
 12112.65  12817.13    inf     12989.09  15594.44  16086.6   16422.24  16823.36  16741.08  15878.76  16947.24  15546.29  16511.75  16572.56  16287.46  6057.73   8129.71   12000.57  14286.03  13563.72  13063.22  13131.4   11674.78 
 1595.69   2317.67   12989.09    inf     11015.92  3136.08   3484.88    3946.5   8930.46   2890.54   4030.59   2557.24   3545.36   3652.58   3313.73   6946.61   7342.51    1661.5   11489.36  12631.8   12217.45  11372.96  1823.13  
 12185.61  11605.61  15594.44  11015.92    inf     8172.77   7870.82   7536.28   2253.95   8278.96   7043.57   8573.81   7556.52   7408.91   7945.63   16953.17  12224.23  12295.51   1767.7    2064.2   6596.07   3492.79   12600.08 
 4018.42   3559.52   16086.6   3136.08   8172.77     inf      349.66    828.03   6372.16    397.99   1273.43    684.63    803.4    1038.81    237.86   10078.12   9598.9   4129.41   9074.85   10054.55  9611.69   9591.62    4444.1  
 4332.64   3810.51   16422.24  3484.88   7870.82    349.66     inf      485.64   6121.68    690.95   1119.23    1012.7    753.28    985.59    256.51   10425.03  9874.92   4444.16   8824.29   9778.17   9305.99   9413.47   4763.02  
 4710.92   4087.37   16823.36   3946.5   7536.28    828.03    485.64     inf     5891.46   1173.28    1194.5   1498.27   1026.45    1215.2    722.86   10873.33  10323.58  4822.98   8582.42    9487.4   8834.48   9287.99   5148.62  
 10258.89  9929.36   16741.08  8930.46   2253.95   6372.16   6121.68   5891.46     inf     6390.43   5137.16   6644.58   5654.94   5470.17    6134.8   14805.68  10583.98  10362.75  2703.24   3731.52   8159.53   3612.48   10635.83 
 3910.83   3585.07   15878.76  2890.54   8278.96    397.99    690.95   1173.28   6390.43     inf     1254.64    340.01    735.75    930.58    455.69   9834.11   9203.96   4019.63   9083.34   10104.57  9994.29   9494.42   4321.12  
 5158.07   4815.75   16947.24  4030.59   7043.57   1273.43   1119.23    1194.5   5137.16   1254.64     inf     1531.29    519.16    379.97   1039.49   10902.48  9507.07    5265.9   7833.24    8850.1    9522.4    8319.6   5561.33  
  3629.0   3387.96   15546.29  2557.24   8573.81    684.63    1012.7   1498.27   6644.58    340.01   1531.29     inf     1017.62   1176.78    791.06   9497.62   8932.35   3736.24   9326.25   10369.32  10294.49  9664.72   4030.08  
 4640.38   4307.68   16511.75  3545.36   7556.52    803.4     753.28   1026.45   5654.94    735.75    519.16   1017.62     inf      238.6     589.54   10454.26  9366.27   4748.49   8349.21   9369.25   9728.52   8800.37   5045.71  
 4805.04   4515.09   16572.56  3652.58   7408.91   1038.81    985.59    1215.2   5470.17    930.58    379.97   1176.78    238.6      inf      828.04   10524.08  9257.63   4911.88   8157.69   9192.85   9807.17   8577.34    5202.6  
 4242.25   3797.32   16287.46  3313.73   7945.63    237.86    256.51    722.86    6134.8    455.69   1039.49    791.06    589.54    828.04     inf     10260.26   9619.4   4352.89   8837.36    9819.9   9555.17   9359.03   4664.98  
 6327.17   7353.78   6057.73   6946.61   16953.17  10078.12  10425.03  10873.33  14805.68  9834.11   10902.48  9497.62   10454.26  10524.08  10260.26    inf     5035.94   6219.23   15843.03  16612.99  15982.53  14219.52  5882.31  
  8054.3   9293.26   8129.71   7342.51   12224.23   9598.9   9874.92   10323.58  10583.98  9203.96   9507.07   8932.35   9366.27   9257.63    9619.4   5035.94     inf     8003.94   10829.02  11598.54  18737.21  9184.92   7787.84  
  112.11   1329.12   12000.57   1661.5   12295.51  4129.41   4444.16   4822.98   10362.75  4019.63    5265.9   3736.24   4748.49   4911.88   4352.89   6219.23   8003.94     inf     13008.45  14094.18  11883.26  13022.06   337.14  
 12908.76  12632.6   14286.03  11489.36   1767.7   9074.85   8824.29   8582.42   2703.24   9083.34   7833.24   9326.25   8349.21   8157.69   8837.36   15843.03  10829.02  13008.45    inf     1201.68   8085.15   1759.74   13260.62 
 13990.04  13574.55  13563.72  12631.8    2064.2   10054.55  9778.17    9487.4   3731.52   10104.57   8850.1   10369.32  9369.25   9192.85    9819.9   16612.99  11598.54  14094.18  1201.68     inf     7431.29    2433.3   14365.97 
 11813.24  10555.29  13063.22  12217.45  6596.07   9611.69   9305.99   8834.48   8159.53   9994.29    9522.4   10294.49  9728.52   9807.17   9555.17   15982.53  18737.21  11883.26  8085.15   7431.29     inf     9788.14   12148.74 
 12942.72  13046.96  13131.4   11372.96  3492.79   9591.62   9413.47   9287.99   3612.48   9494.42    8319.6   9664.72   8800.37   8577.34   9359.03   14219.52  9184.92   13022.06  1759.74    2433.3   9788.14     inf     13193.06 
  446.84   1615.84   11674.78  1823.13   12600.08   4444.1   4763.02   5148.62   10635.83  4321.12   5561.33   4030.08   5045.71    5202.6   4664.98   5882.31   7787.84    337.14   13260.62  14365.97  12148.74  13193.06    inf
```
Usando el algoritmo Greedy se encuantra esta optimización
```
Total recorrido: 51280.4 Km
Mejora con respecto al actual: 61.2%

Ruta:
1. Sao Paulo, Brazil
2. Miami, United States
3. Austin, United States
4. Mexico City, Mexico
5. Las Vegas, United States
6. Montreal, Canada
7. Silverstone, United Kingdom
8. Zandvoort, Netherlands
9. Spa-Francorchamps, Belgium
10. Monza, Italy
11. Imola, Italy
12. Montecarlo, Monaco
13. Barcelona, Spain
14. Spielberg, Austria
15. Budapest, Hungary
16. Baku, Azerbaijan
17. Sakhir, Bahrain
18. Lusail, Qatar
19. Abu Dhabi, United Arab Emirates
20. Jeddah, Saudi Arabia
21. Marina Bay, Singapore
22. Suzuka, Japan
23. Melbourne, Australia
```

Se observa que esta aproximación disminuye la distancia total recorrida comparado con la actual en un 61.2%, pero esta aproximación no es totalmente óptima, por lo que en los lugares aledaños a los "problemáticos" se opta por calcular la organización de estos nodos usando búsqueda exhaustiva

![image2](https://github.com/lopezemmanuel/Formula1_Optimization/assets/45053254/e5e9eba4-94e5-4ad3-beea-6dafdbfe6c99)

### Mejoramiento de la solución usando búsqueda exhaustiva

#### Nodos 1-6 (América)

En primer lugar, se identifica un potencial de mejoramiento en el recorrido de los nodos del continente americano (Nodos 1-6). Actualmente se recorren de esta manera:

1. Sao Paulo, Brazil
2. Miami, United States
3. Austin, United States
4. Mexico City, Mexico
5. Las Vegas, United States
6. Montreal, Canada

Como en este cruce entran en juego 6 nodos, es posible permitirse usar búsqueda exhaustiva para buscar la mejor solución, ya que hay solo 720 combinaciones posibles.

![image3](https://github.com/lopezemmanuel/Formula1_Optimization/assets/45053254/ddb092b5-40a8-4764-a537-4d7e31fbeae2)

```
Complejidad: 24 posibles soluciones

Total recorrido actual: 15611.23 Km
Total recorrido óptimo: 15234.17 Km
Mejora con respecto al actual: 2.42%

Ruta:
1. Sao Paulo, Brazil
2. Miami, United States
3. Mexico City, Mexico
4. Austin, United States
5. Las Vegas, United States
6. Montreal, Canada
```

#### Nodos 9-14 (Europa)

Este es el punto más crítico ya que al recorrer los nodos 9-14 se crea un cruce, que indica que este sub-recorrido no es óptimo.

9. Spa-Francorchamps, Belgium
10. Monza, Italy
11. Imola, Italy
12. Montecarlo, Monaco
13. Barcelona, Spain
14. Spielberg, Austria

Como en este cruce entran en juego 6 nodos, se puede permitir usar búsqueda exhaustiva para buscar la mejor solución, ya que hay solo 720 combinaciones posibles.

![image4](https://github.com/lopezemmanuel/Formula1_Optimization/assets/45053254/4eed39d5-77c7-4d4b-9c47-9ecf6e3eb37e)

```
Complejidad: 24 posibles soluciones

Total recorrido actual: 2835.98 Km
Total recorrido óptimo: 2404.45 Km
Mejora con respecto al actual: 15.22%

Ruta:
9. Spa-Francorchamps, Belgium
10. Barcelona, Spain
11. Montecarlo, Monaco
12. Monza, Italy
13. Imola, Italy
14. Spielberg, Austria
```

#### Nodos 16-21 (Medio Oriente)

Similar al caso anterior, aunque sin un cruce, notamos potencial de mejora en ese subconjunto del recorrido

16. Baku, Azerbaijan
17. Sakhir, Bahrain
18. Lusail, Qatar
19. Abu Dhabi, United Arab Emirates
20. Jeddah, Saudi Arabia
21. Marina Bay, Singapore

Como en este cruce entran en juego 6 nodos, se puede permitir  usar búsqueda exhaustiva para buscar la mejor solución, ya que hay solo 720 combinaciones posibles.

![image5](https://github.com/lopezemmanuel/Formula1_Optimization/assets/45053254/a0fb1a88-5645-4bb2-8c2b-6aed07f72b5d)

```
Complejidad: 24 posibles soluciones

Total recorrido actual: 11014.56Km
Total recorrido óptimo: 9907.65Km
Mejora con respecto al actual: 10.05%

Ruta:
16. Baku, Azerbaijan
17. Jeddah, Saudi Arabia
18. Sakhir, Bahrain
19. Lusail, Qatar
20. Abu Dhabi, United Arab Emirates
21. Marina Bay, Singapore
```

## Solución final

Aplicando las mejoras encontradas utilizando búsqueda exhaustiva se llega a la conclución de que el recorrido más óptimo para los 23 circuitos del calendario 2023 de la Formula 1 es:

1. Sao Paulo, Brazil
2. Miami, United States
3. Mexico City, Mexico
4. Austin, United States
5. Las Vegas, United States
6. Montreal, Canada
7. Silverstone, United Kingdom
8. Zandvoort, Netherlands
9. Spa-Francorchamps, Belgium
10. Barcelona, Spain
11. Montecarlo, Monaco
12. Monza, Italy
13. Imola, Italy
14. Spielberg, Austria
15. Budapest, Hungary
16. Baku, Azerbaijan
17. Jeddah, Saudi Arabia
18. Sakhir, Bahrain
19. Lusail, Qatar
20. Abu Dhabi, United Arab Emirates
21. Marina Bay, Singapore
22. Suzuka, Japan
23. Melbourne, Australia

![image6](https://github.com/lopezemmanuel/Formula1_Optimization/assets/45053254/43e63694-d10a-4e40-b655-52cd95d9e22e)

```
Total recorrido actual: 132163.47 Km
Total recorrido óptimo: 49364.9 Km
Mejora con respecto al actual: 62.65%
```
