PAV - P4: reconocimiento y verificación del locutor
===================================================

Obtenga su copia del repositorio de la práctica accediendo a [Práctica 4](https://github.com/albino-pav/P4)
y pulsando sobre el botón `Fork` situado en la esquina superior derecha. A continuación, siga las
instrucciones de la [Práctica 3](https://github.com/albino-pav/P3) para crear una rama con el apellido de
los integrantes del grupo de prácticas, dar de alta al resto de integrantes como colaboradores del proyecto
y crear la copias locales del repositorio.

También debe descomprimir, en el directorio `PAV/P4`, el fichero [db_spk.tgz](https://atenea.upc.edu/pluginfile.php/3008277/mod_assign/introattachment/0/db_spk.tgz?forcedownload=1)
con la base de datos oral que se utilizará en la parte experimental de la práctica.

Como entrega deberá realizar un *pull request* con el contenido de su copia del repositorio. Recuerde
que los ficheros entregados deberán estar en condiciones de ser ejecutados con sólo ejecutar:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~.sh
  make release
  run_spkid mfcc train test classerr verify verifyerr
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A modo de memoria de la práctica, complete, en este mismo documento y usando el formato *markdown*, los
ejercicios indicados.

## Ejercicios.

### Extracción de características.

- Escriba el pipeline principal usado para calcular los coeficientes cepstrales de predicción lineal
  (LP), en el fichero <code>scripts/wav2lp.sh</code>:
  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~.sh

sox $inputfile -t raw - | $X2X +sf | $FRAME -l 400 -p 80 | $WINDOW -l 400 -L 400 |
        $LPC -l 400 -m $lpc_order > $base.lp
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


- Escriba el pipeline principal usado para calcular los coeficientes cepstrales de predicción lineal
  (LPCC), en su fichero <code>scripts/wav2lpcc.sh</code>:
  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~.sh
sox $inputfile -t raw - dither -p12 | $X2X +sf | $FRAME -l 200 -p 40 | $WINDOW -l 200 | 
        $LPC -l 200 -m $lpc_order | $LPC2C -m $lpc_order -M $nceps > $base.cep
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  

- Escriba el pipeline principal usado para calcular los coeficientes cepstrales en escala Mel (MFCC), en
  su fichero <code>scripts/wav2mfcc.sh</code>:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~.sh
sox $inputfile -t raw - dither -p12 | $X2X +sf | $FRAME -l 200 -p 40 | 
        $MFCC -l 200 -m $mfcc_order -s 8000 -n $ncoef > $base.mfcc
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Indique qué parámetros considera adecuados para el cálculo de los coeficientes LPCC y MFCC.

LPCC:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~.sh
EXEC="wav2lpcc 13 10 $db/$filename.wav $w/$FEAT/$filename.$FEAT"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Hemos decidio probando un orden de lpcc de 13 i número de cepstrums 10 consiguiendo un error rate aceptable pero no demasiado bueno. Para el cálculo de los coeficientes MFCC:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~.sh
EXEC="wav2mfcc 13 14 $db/$filename.wav $w/$FEAT/$filename.$FEAT"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

En este caso usamos un orden de mfcc de 13 (el que se usa de forma habitual) y 14 coefs. También con mfcc hemos obtenido un resultado aceptable.

- Inserte una imagen mostrando la dependencia entre los coeficientes 2 y 3 de las tres parametrizaciones
  para una señal de prueba.
  
  Parametrización LP
  
  <img src="img/lpcoef.png.PNG" width="640" align="center">
  
  Parametrización MFCC
  
  <img src="img/mfcccoef.png.PNG" width="640" align="center">
  
  Parametrización LPCC
  
  <img src="img/lpcccoef.png.PNG" width="640" align="center">
  
  + ¿Cuál de ellas le parece que contiene más información?
  
La representación del mel-cepstrum és la que muestra la información más completa, debito a que este oncentra la información del trozo de señal analizado en un numero reducido de muestras y tiene coeficientes más incorrelados que en los demas casos. Por lo tanto,en el momento de la clasificación habrá menos redundancia. La percepción acústica de el sistema auditivo humano funciona diferente según las bandas frecuenciales. El mel cepstrum es el que se usa normalmente para este tipo de aplicaciones, ya que concentra la información en los primeros coeficientes.

  
### Entrenamiento y visualización de los GMM.

- Inserte una gráfica que muestre la función de densidad de probabilidad modelada por el GMM de un locutor
  para sus dos primeros coeficientes de MFCC.
  
  <img src="img/gmmlocutor.png.PNG">
  
- Inserte una gráfica que permita comparar los modelos y poblaciones de dos locutores distintos. Comente el
  resultado obtenido y discuta si el modelado mediante GMM permite diferenciar las señales de uno y otro.
  
  <img src="img/gmmlocutor2.png.PNG">
  
La imagen corresponde a la GMM obtenida de un locutor distinto de la pregunta anterior. Como podemos ver si comparamos las dos gráficas se diferencian en la probabilidad de decisión. La salida se representa con las direcciones de máxima varianza. Una vez obtenidas las características se hace la parametrización a partir de las fdp gaussianas.


### Reconocimiento del locutor.

- Inserte una tabla con la tasa de error obtenida en el reconocimiento de los locutores de la base de datos
  SPEECON usando su mejor sistema de reconocimiento para los parámetros LP, LPCC y MFCC.
  
    |                 | LP   | LPCC | MFCC |
  |------------------|:----:|:----:|:----:|
  | ERROR_RATE      |30,32%|13,76%|15.86%|
    | MISSED       |234/250 |240/250| 227/250 |
  
 
### Verificación del locutor.

- Inserte una tabla con el score obtenido con su mejor sistema de verificación del locutor en la tarea
  de verificación de SPEECON. La tabla debe incluir el umbral óptimo, el número de falsas alarmas y de
  pérdidas, y el score obtenido usando la parametrización que mejor resultado le hubiera dado en la tarea
  de reconocimiento.
  
   |                 | LP   | LPCC | MFCC |
   |------------------|:----:|:----:|:----:|
   | COSTDETECT       |93,6% |97%| 91.2% |
    | MISSED       |234/250 |244/250| 228/250 |

  

 
### Test final y trabajo de ampliación.

- Recuerde adjuntar los ficheros `class_test.log` y `verif_test.log` correspondientes a la evaluación
  ciega final.

- Recuerde, también, enviar a Atenea un fichero en formato zip o tgz con la memoria con el trabajo
  realizado como ampliación, así como los ficheros `class_ampl.log` y/o `verif_ampl.log`, obtenidos como
  resultado del mismo.+