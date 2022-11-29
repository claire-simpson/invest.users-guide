.. _wind-energy:

****************************************
Producci�n de energ�a e�lica en alta mar
****************************************

.. figure:: ./wind_energy/intro_image.png
   :align: right
   :figwidth: 200pt

Resumen
=======
La energ�a e�lica marina est� ganando inter�s en todo el mundo, con 5.400 megavatios (MW) instalados en enero de 2013 y una tasa de crecimiento de alrededor del 25% anual (GWEC, 2013). Los vientos marinos, consistentemente m�s altos, y la proximidad a los centros de carga costeros son dos de las principales razones por las que los promotores de energ�a e�lica miran hacia el mar. El objetivo del modelo de energ�a e�lica marina InVEST es proporcionar mapas espaciales de la disponibilidad de recursos energ�ticos, el potencial de generaci�n de energ�a y (opcionalmente) el valor de la generaci�n de energ�a para permitir que se eval�en las decisiones de ubicaci�n, las compensaciones de uso y una serie de otras cuestiones de planificaci�n del espacio marino. El modelo se ha desarrollado para ofrecer la m�xima flexibilidad a quien lo usa, ya que puede ejecutarse con datos y par�metros por defecto, pero tambi�n puede actualizarse f�cilmente con nueva informaci�n sobre turbinas y cimientos, informaci�n sobre la conexi�n a la red y valores de los par�metros que se ajusten al contexto de quien usa el modelo. Los resultados del modelo incluyen el potencial de energ�a e�lica, la generaci�n de energ�a, las emisiones de carbono compensadas, el valor actual neto y el costo nivelado de la energ�a, todo ello a nivel de parque.

Las referencias revisadas por personal experto para este modelo son http://dx.doi.org/10.1016/j.aquaculture.2014.10.035 para la parte financiera del modelo y http://dx.doi.org/10.1016/j.marpol.2015.09.024 para la parte f�sica.

Introducci�n
============
Este modelo de energ�a e�lica proporciona una interfaz f�cilmente manejable para evaluar la viabilidad de la energ�a e�lica en su regi�n bajo diferentes escenarios de dise�o de parques. Los resultados son mapas rasterizados, cuyos valores puntuales representan el valor agregado de un parque centrado en ese punto. Esto permite un an�lisis detallado de las opciones de emplazamiento a escala fina, aunque tiene el costo de suponer que las condiciones son suficientemente sim�tricas alrededor del punto central para que �ste represente las condiciones medias de todas las turbinas del parque. Dado que quien lo usa puede seleccionar el n�mero de turbinas del parque, y que los mapas rasterizados no dan una indicaci�n del tama�o del parque, el modelo tambi�n genera un pol�gono representativo en un punto de datos e�licos seleccionado al azar que indica el tama�o del parque.

Para ejecutar el modelo, se le pide que suministre informaci�n en la interfaz gr�fica. Esto incluye informaci�n sobre las condiciones de energ�a e�lica, el tipo de turbina, el n�mero de turbinas, la zona de inter�s, etc. Para facilitar la ejecuci�n del modelo, este incluye datos por defecto en tablas `.csv` sobre dos aerogeneradores habituales en alta mar: 3,6 MW y 5,0 MW. Tambi�n se incluyen dos conjuntos de datos sobre la velocidad del viento: uno global y otro que cubre el Atl�ntico Noroeste. Por �ltimo, se incluye una tabla de valores por defecto que se modifican con menos frecuencia y que se utilizan para parametrizar varias partes del modelo, denominada archivo "Global Wind Energy Parameters" (Par�metros globales de la energ�a e�lica). Estos archivos `.csv` son inputs obligatorios y pueden modificarse si se desean valores alternativos editando directamente los archivos con un editor de texto o con Microsoft Excel. Al modificar estos archivos, se recomienda hacer una copia del archivo `.csv` por defecto para no perder los valores originales.


El modelo
=========

Potencial de energ�a e�lica
---------------------------

El modelo de energ�a e�lica estima la densidad de potencia del viento (potencial de energ�a e�lica) para identificar las zonas de alta mar con alto potencial energ�tico. La densidad de potencia e�lica :math:`PD (Wm^{-2}`) en un lugar determinado puede aproximarse como una funci�n de las estad�sticas del viento (Elliott et al., 1986)

.. math:: \frac{1}{2}\rho\sum^c_{j=1}f(V_j)V_j^3
   :label: wind_power_density

donde, :math:`\rho` is mean air density (:math:`kg\,m^{-3}`), :math:`j` es el �ndice de la clase de velocidad del viento, :math:`c` es el n�mero de clases de velocidad del viento, :math:`V_j` es la velocidad del viento de la j� clase (:math:`ms^{-1}`), y :math:`f(V_j)` es la funci�n de densidad de probabilidad de :math:`V_j`. En el an�lisis de los datos del viento se suelen utilizar dos distribuciones de probabilidad: 1) la de Rayleigh y 2) la de Weibull (Manwell et al. 2009). La distribuci�n de Weibull puede representar mejor una mayor variedad de reg�menes de viento (Celik 2003; Manwell et al. 2009), y viene dada como

.. math:: f(V_j) = \frac{k}{\lambda}\left(\frac{V_j}{\lambda}\right)^{k-1}e^{-\left(\frac{V_j}{\lambda}\right)^k}
   :label: weibull_dist

donde, :math:`k` and :math:`\lambda` son los factores de forma y escala, respectivamente. El factor de forma, :math:`k`, determina la forma de la funci�n de densidad de probabilidad de Weibull (:numref:`weibull-fig`). La funci�n de densidad de probabilidad muestra un pico m�s agudo a medida que :math:`k` aumenta, indicando que hay velocidades de viento consistentes alrededor de la velocidad media del viento. Por otro lado, la funci�n se suaviza a medida que k disminuye, lo que indica una mayor variaci�n de la velocidad del viento y una mayor frecuencia de velocidades bajas y altas. El modelo requiere que los inputs de la velocidad del viento se den en t�rminos de los par�metros estimados de Weibull, en lugar de tomar los datos brutos de la velocidad del viento. Para nuestros datos de muestra utilizamos una funci�n de MATLAB, `wblfit`, para estimar :math:`k` y :math:`lambda` a la altura de referencia de la velocidad del viento (altura a la que se observaron o estimaron las velocidades del viento), que devuelve las estimaciones de m�xima verosimilitud de los par�metros de la distribuci�n de Weibull dados los valores de los datos de la serie temporal del viento. Esto se hizo para cada punto de observaci�n de la velocidad del viento. Para m�s detalles de la funci�n `wblfit`, consulte https://kr.mathworks.com/help/stats/wblfit.html. Esto tambi�n se puede realizar en R (ver aqu� el tutorial: https://stats.stackexchange.com/questions/60511/weibull-distribution-parameters-k-and-c-for-wind-speed-data).

.. _weibull-fig:

.. figure:: ./wind_energy/weibull_curves.png

   :align: center

   Ejemplo de funci�n de densidad de probabilidad de Weibull con varios factores de forma (:math:`k`), donde la velocidad media del viento = :math:`6 ms^{-1}` (Manwell et al., 2009).

La densidad de potencia del viento se calcula a la altura del buje :math:`Z` (m) de un aerogenerador (:numref:`weibull-fig`), lo que significa que todas las variables de :eq:`wind_power_density` y :eq:`weibull_dist` deben convertirse al valor apropiado a la altura del buje. La densidad media del aire :math:`\rho` se estim� como :math:`\rho=1,225-(1,194\cdot 10^{-4})Z`, que se aproxima al perfil de la Atm�sfera Est�ndar de Estados Unidos para la densidad del aire (National Oceanic and Atmospheric Administration, 1976). Aplicamos la ley de potencia del perfil del viento para estimar la velocidad del viento (:math:`V`) a la altura del buje :math:`Z` (Elliott et al., 1986).

.. math:: \frac{V}{V_r} = \left(\frac{Z}{Z_r}\right)^\alpha

donde :math:`V` es la velocidad del viento (:math:`ms^{-1}`) a la altura del buje :math:`Z` (m) de un aerogenerador, y :math:`V_{r}` es la velocidad del viento (:math:`ms^{-1}`) a la altura de referencia :math:`Z_r` (m) donde se obtienen los datos del viento. :math:`\alpha` es el exponente de la ley de potencia, que es un coeficiente derivado emp�ricamente y var�a con la estabilidad de la atm�sfera. Para la condici�n de estabilidad neutra, ? es aproximadamente 1/7 (0,143) para las superficies terrestres, que es ampliamente aplicable para ajustar la velocidad del viento en tierra (Elliott et al., 1986). El exponente de la ley de potencia tiene un valor diferente en las superficies oce�nicas. Hsu et al. (1994) encontraron que :math:`\alpha = 0,11\pm0,03` para la superficie del oc�ano en condiciones de estabilidad atmosf�rica casi neutras. El modelo de energ�a e�lica utiliza :math:`\alpha = 0,11` como valor por defecto para ajustar la velocidad del viento en la superficie del oc�ano. El perfil del viento de la capa l�mite atmosf�rica puede aproximarse con mayor precisi�n utilizando la ecuaci�n del perfil del viento logar�tmico que tiene en cuenta la rugosidad de la superficie y la estabilidad atmosf�rica (Manwell et al. 2009).

.. _wind-turbine-fig:

.. figure:: ./wind_energy/wind_turbine.png

   :align: center

   Diagrama esquem�tico de un aerogenerador (https://www.daviddarling.info/encyclopedia/H/AE_hub_height.html)

Los resultados de la densidad de potencia del viento (DP) proporcionan informaci�n sobre la idoneidad de un proyecto de desarrollo de energ�a e�lica en t�rminos de recurso e�lico. Pacific Northwest Laboratories clasific� la densidad de potencia e�lica y la velocidad del viento en siete clases basadas en el atlas e�lico de Estados Unidos (:numref:`wind-power-density-fig`) (Manwell et al. 2009). Las zonas designadas como de clase 4 o superior se consideran adecuadas para la mayor parte del desarrollo de la energ�a e�lica. Las zonas de clase 3 son adecuadas para el desarrollo de la energ�a e�lica si se utilizan turbinas grandes. Las clases 1 y 2 rara vez se consideran zonas adecuadas para el desarrollo de la energ�a e�lica en t�rminos de potencial energ�tico. Los recursos e�licos var�an considerablemente en el espacio y en Europa se ha desarrollado una categorizaci�n m�s detallada de la densidad de potencia del viento para cinco condiciones topogr�ficas, que incluye el terreno protegido, la llanura abierta, la costa del mar, el mar abierto, las colinas y las crestas (:numref:`wind-power-density-fig`) (Manwell et al. 2009). La clasificaci�n del recurso e�lico para la costa mar�tima y el mar abierto puede proporcionar una mejor informaci�n sobre la idoneidad de los proyectos de energ�a e�lica en alta mar.

.. _wind-power-density-fig:

.. figure:: ./wind_energy/wind_power_density.png

   :align: center

   Densidad de potencia del viento (DP) y clases de velocidad del viento basadas en el atlas europeo del viento (Modificado de la Tabla 2.6 en Manwell et al. 2009).
Generaci�n de energ�a
---------------------

La cantidad de energ�a cosechable de un aerogenerador en un lugar concreto depende de las caracter�sticas del aerogenerador y de las condiciones del viento (Pallabazzer 2003; Jafarian y Ranjbar 2010). El modelo de energ�a e�lica cuantifica la energ�a cosechable bas�ndose en la curva de potencia de salida de un aerogenerador y en las estad�sticas de la velocidad del viento. :numref:`power-output-curve-fig` muestra una curva de potencia de salida de un aerogenerador (tipo de control de paso). El aerogenerador comienza a generar potencia a la velocidad de corte del viento (:math:`V_cin`). La potencia de salida aumenta hasta la potencia nominal (Prate) a medida que la velocidad del viento aumenta hasta la velocidad nominal (:math:`V_rate`). El aerogenerador sigue produciendo la m�xima potencia (es decir, Prate) hasta que la velocidad del viento alcanza la velocidad de corte (:math:`V_cout`). Si la velocidad del viento aumenta por encima de la velocidad de corte, el aerogenerador deja de generar energ�a por motivos de seguridad. En la actualidad, hay m�s de 74 parques e�licos marinos en funcionamiento en todo el mundo y la informaci�n tecnol�gica espec�fica de los aerogeneradores de cada parque e�lico est� disponible en LORC Knowledge (2012).

.. _power-output-curve-fig:

.. figure:: ./wind_energy/power_output_curve.png

   :align: center

Curva de potencia de salida (P) de un aerogenerador (tipo de control de paso) en funci�n de la velocidad del viento (V) (Modificada de la Fig.1 de Pallabazzer 2003)
Para proporcionar flexibilidad para una variedad de tipos de turbinas diferentes sin requerir que usted introduzca manualmente una curva de potencia, estimamos la potencia de salida :math:`P` (kW) de un aerogenerador utilizando un enfoque de modelizado polin�mico (Jafarian y Ranjbar 2010):

.. math:: P(V) = \left\{\begin{array}{ll} 0 & V < V_{cin} \mathrm{\ or\ } V>V_{cout}\\
              P_{rate} & V_{rate} < V < V_{cout}\\
	      (V^m - V^m_{in})/(V^m_{rate} - V^m_{in}) & V_{cin} \leq V \leq V_{rate}\\
	      \end{array}\right.

donde, :math:`m` es un exponente de la curva de potencia de salida (normalmente 1 o 2). Utilizando este enfoque, la producci�n de energ�a, O (MWh), generada por un aerogenerador puede calcularse utilizando

.. math:: O = nday\cdot \frac{\rho}{\rho_0} P_{rate}\left(\int^{V_rate}_{V_{cin}} \frac{V^m - V^m_{cin}}{V^m_r-V^m_{cin}} f(V)dV
     + \int^{V_{cout}}_{V_{rate}} f(V) dV\right)(1- lossrate)

donde, :math:`nday` es el n�mero de d�as para la producci�n de energ�a (por ejemplo :math:`nday = 365` d�as para la producci�n anual de energ�a), :math:`\rho_0` es la densidad del aire de la atm�sfera est�ndar (por ejemplo :math:`1.225 kg m^{-3}` para la densidad del aire de la atm�sfera est�ndar de Estados Unidos a nivel del mar), y :math:`lossrate` es un valor decimal que representa las p�rdidas de energ�a debidas a una combinaci�n de tiempo de inactividad, eficiencia de conversi�n de energ�a y p�rdidas en la red el�ctrica (el valor por defecto es 0,05). Todos estos par�metros est�n incluidos en el archivo de par�metros globales `.csv` y pueden ser modificados por usted a partir de sus valores predeterminados. La producci�n total de energ�a del parque es igual a la producci�n individual de las turbinas multiplicada por el n�mero de turbinas, :math:`n`,

.. math:: E = nO

El software InVEST viene con informaci�n t�cnica y financiera por defecto sobre dos tama�os de turbina comunes, las turbinas de 3,6 MW y 5,0 MW. La informaci�n de cada turbina se presenta en archivos `.csv` en el directorio `Input` y es un dato necesario para el modelo. Se pueden utilizar los datos por defecto, editar un archivo o crear uno nuevo para evaluar diferentes tama�os de turbina o actualizar caracter�sticas espec�ficas. Los archivos deben mantener el mismo formato: solo se pueden modificar los valores de los par�metros de forma segura. Se recomienda guardar las ediciones como nuevos archivos `.csv` en lugar de sobrescribir los datos por defecto.

Compensaci�n de carbono
-----------------------

Dado que los aerogeneradores no crean gases de efecto invernadero al generar energ�a, usted podr�a estar interesado en evaluar la cantidad de emisiones de di�xido de carbono que se evitan al construir un parque e�lico frente a una planta de generaci�n de energ�a convencional. Para traducir la energ�a e�lica libre de carbono en una cantidad representativa de emisiones anuales evitadas 
:math:`\mathrm{CO}_2` utilizamos el siguiente factor de conversi�n por defecto :math:`6.8956\cdot 10 ^{-4} \mathrm{metric\ tons\ CO}_2/kWh`
Se obtiene de la EPA (https://www.epa.gov/energy/greenhouse-gases-equivalencies-calculator-calculations-and-references) y se basa en datos de 2007. Consulte su sitio web para conocer las limitaciones de este enfoque. El par�metro es representativo del carbono emitido por la cartera energ�tica de Estados Unidos y puede no ser apropiado para su contexto. Este valor es modificable en el archivo de par�metros globales `.csv`.

Valor de la energ�a
-------------------

El valor de la energ�a e�lica se mide como los ingresos netos descontados antes de impuestos procedentes de la generaci�n de energ�a que obtendr�a un promotor/operador de energ�a e�lica a lo largo de la vida �til prevista de un parque e�lico. El valor actual neto (https://en.wikipedia.org/wiki/Net_present_value) de la energ�a para un parque e�lico determinado es:

.. math:: NPV = \sum^T_{t=1}(R_t-C_t)(1+i)^{-t}

Donde :math:`R_t` son los ingresos brutos recaudados en el a�o :math:`t`, y :math:`C_t` son los costos agregados en el a�o :math:`t`. :math:`T` representa la vida �til prevista de la instalaci�n, y :math:`i` representa el tipo de descuento (https://en.wikipedia.org/wiki/Discount_rate) o costo medio ponderado del capital(WACC, https://en.wikipedia.org/wiki/Weighted_average_cost_of_capital). Tanto :math:`T` cuanto :math:`i` puede ser modificado por usted; :math:`T` pueden encontrarse en el archivo de par�metros globales `.csv` y :math:`i` se introduce en la secci�n de valoraci�n de la interfaz. En el caso de los proyectos financiados tanto con deuda como con capital y en los que existe un riesgo importante asociado al establecimiento y mantenimiento del flujo de ingresos previsto, el WACC es un m�todo m�s apropiado para establecer el valor temporal del dinero. Como este par�metro entra en el c�lculo de la misma manera que lo har�a un tipo de descuento, si lo prefiere puede introducir un tipo de descuento adecuado e interpretar los resultados en consecuencia. No proporcionamos un valor por defecto, pero Levitt et al. (2011) sugieren un valor de WACC de 0,116 basado en un an�lisis exhaustivo de las tasas de descuento espec�ficas del sector y de las diferentes estructuras de deuda/capital en Europa y Estados Unidos.

Los ingresos brutos anuales se calculan multiplicando el precio por kWh, :math:`s`, por la cantidad anual de kWh suministrada a la red por un parque e�lico, :math:`E_t`, por lo que :math:`R_t=sE_t`. Se supone que la energ�a no se recoge en el primer a�o durante la fase de construcci�n.

Los costos pueden dividirse en costos de capital �nicos y costos de explotaci�n y gesti�n permanentes. Durante la fase de construcci�n, los gastos se destinan a las turbinas, los cimientos, el equipo de transmisi�n el�ctrica y otros costos diversos asociados al desarrollo, la adquisici�n y la ingenier�a. Al final de la vida �til de los parques, la empresa debe retirar sus equipos. La informaci�n suministrada por defecto se basa en una amplia revisi�n de publicaciones revisadas por personas expertas, informes del sector y comunicados de prensa. Esta informaci�n se resume a continuaci�n.

Turbinas
^^^^^^^^

Las turbinas y los cimientos se modelizan con costos unitarios. Hemos suministrado datos de costos de turbinas de 3,6 MW y 5,0 MW, as� como de cimientos monopilote y encamisados, aunque usted puede introducir su propia informaci�n espec�fica sobre la turbina o la cimentaci�n. Tenga en cuenta que todos los costos por defecto se indican en d�lares estadounidenses de 2012. Suponiendo un cimiento por turbina, el costo total de las turbinas y los cimientos es simplemente el n�mero de aerogeneradores multiplicado por el costo unitario. La Tabla 1 ofrece un resumen de los costos de las turbinas existentes.

.. csv-table::

  :file: ./wind_energy/turbine_costs.csv

  :header-rows: 1

* Cuadro 1: Costos de las turbinas.*

Cimientos
^^^^^^^^^
Este modelo puede incluir de forma flexible la valoraci�n tanto de dise�os de turbinas basadas en cimientos como de turbinas flotantes. Para ello, se puede introducir la informaci�n sobre los costos unitarios correspondientes al dise�o de su parque. Los resultados est�n limitados por los par�metros de profundidad y distancia que usted puede introducir, por lo que es importante ajustarlos para que reflejen las limitaciones tecnol�gicas apropiadas de su elecci�n de dise�o. Las turbinas con cimientos se limitan a una profundidad de unos 60 metros.

La informaci�n sobre el costo de los cimientos es relativamente dif�cil de conseguir. Los de monopilotes son el tipo m�s com�n y suelen acoplarse a turbinas de 3,6 MW. Ramboll, uno de los principales fabricantes de cimientos, calcula que los de monopilotes con una turbina de 3,6 MW cuestan 2 millones de d�lares por cimiento. Los costos de los monopilotes en Burbo y Rhyl Flats, en el Reino Unido, se cifran en 1,9 millones de d�lares y 2,2 millones de d�lares respectivamente. Los encamisados son m�s robustos que las de los monopilotes y suelen utilizarse con turbinas de 5,0 MW y/o en aguas profundas. Dos comunicados de prensa de Nordsee Ost (Alemania) y Ormonde (Reino Unido) sit�an los costos unitarios de este tipo en 2,74 y 2,43 millones de d�lares respectivamente. Un comunicado de 2012 del Programa Europeo de Energ�a para la Recuperaci�n cifraba el costo de los cimientos por gravedad en aguas profundas (40 metros) de Global Tech 1 (Alemania) en 6,65 millones de d�lares por cimiento.

Todos los cimientos deber�an presentar un costo creciente con la profundidad, ya que los costos de los materiales ser�n necesariamente m�s elevados; sin embargo, esto no se recoge actualmente en este modelo debido a la escasez de datos de costos de proyectos para estimar dicha relaci�n. Jacquemin et al. (2011) utilizaron datos de campo para estimar el peso del cimiento en funci�n de la profundidad del agua; sin embargo, no se facilitan los datos ni las funciones, por lo que es imposible reproducir su trabajo. No obstante, esta fuente proporciona un medio para aproximar los costos de diferentes tecnolog�as de cimentaci�n, incluida la tecnolog�a flotante. Samoteskul et al. (2014) demuestran c�mo se pueden utilizar los datos de Jacquemin et al. (2011) de esta manera.

Transmisi�n de electricidad
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Los equipos de transmisi�n de electricidad son mucho m�s dif�ciles de modelizar a nivel de componentes porque el dise�o �ptimo del sistema de transmisi�n var�a considerablemente seg�n las condiciones locales y el dise�o del parque e�lico. Dependiendo del tama�o del parque y de su distancia a la costa, pueden ser necesarias plataformas en alta mar con transformadores de tensi�n, convertidores y equipos de conmutaci�n. Adem�s, hay un punto cr�tico en el que la distancia de un parque e�lico a la red requiere un cambio de energ�a de corriente alterna (CA) a corriente continua (CC) para superar las p�rdidas de la l�nea que reducen la cantidad de energ�a suministrada. Dada la variaci�n del dise�o en los distintos contextos, utilizamos un enfoque de modelizaci�n descendente para los costos de transmisi�n, a fin de que el modelo pueda utilizarse ampliamente sin necesidad de una modelizaci�n exhaustiva del sistema ni de informaci�n sobre los costos unitarios. Recogimos informaci�n sobre los costos de transmisi�n de electricidad (incluida la instalaci�n) de 20 parques e�licos y la utilizamos para estimar una relaci�n entre los costos totales y las caracter�sticas de los parques. Estos datos se recogieron del proceso de licitaci�n de Ofgem en el Reino Unido (https://www.ofgem.gov.uk/electricity/transmission-networks/offshore-transmission) y se muestran en la Tabla 2.

.. csv-table::

  :file: ./wind_energy/transmission_table.csv

  :header-rows: 1

* Cuadro 2: Infraestructura de transmisi�n de energ�a en alta mar.*

Mediante una regresi�n de m�nimos cuadrados ordinarios, estimamos la siguiente ecuaci�n que relaciona los costos totales de transmisi�n con la capacidad de las explotaciones y la distancia total de los cables de transmisi�n:

.. math:: TransCost = \beta_0 MW + \beta_1 TotCable + \epsilon

Para captar el efecto de las p�rdidas de transmisi�n debidas a la resistencia, lo estimamos por separado para cada tipo de corriente (CA y CC). Como nuestros datos sugieren un umbral cr�tico de m�s de 54,8 km para la transmisi�n de CC, adoptamos 60 km como punto de transici�n. Esto tambi�n es coherente con las cifras publicadas sobre la rentabilidad de la transici�n de la transmisi�n de CA a CC (Carbon Trust, 2008; Umaine, 2011); v�ase el cuadro 3.

.. csv-table::

  :file: ./wind_energy/ac_dc_transmission_cost.csv

  :header-rows: 1

* Cuadro 3, costos de transmisi�n de CA a CC. \*p<.10, \*\*p<.05, \*\*\*p<.01*

Estos resultados proporcionan un modelo predictivo de los costos de transmisi�n en funci�n del tipo de corriente, la capacidad total de la explotaci�n en MW y la longitud total del cable de transmisi�n en km. Para calcular la longitud total del cable de transmisi�n desde cualquier ubicaci�n en alta mar, el modelo requiere cierta informaci�n sobre la red en tierra. Las opciones proporcionadas est�n pensadas para ofrecerle flexibilidad en funci�n de la disponibilidad de datos y de las preguntas habituales de an�lisis. Tiene dos opciones:

 * Crear una tabla `.csv` que incluya los detalles de latitud y longitud de todos los puntos de conexi�n a la red en el �rea de inter�s
 * Utilizar un par�metro fijo para modelizar la ubicaci�n de la red

La opci�n de la tabla lw ofrece la posibilidad de indicar tanto los puntos de aterrizaje en la l�nea de costa como los puntos de conexi�n a la red. Para cada emplazamiento potencial de un parque e�lico (cada p�xel del oc�ano que se ajuste a las dem�s restricciones del modelo y se encuentre en el ADI), el modelo identifica el punto terrestre especificado m�s cercano y calcula la distancia en l�nea recta hasta ese punto. A continuaci�n, encuentra el punto de conexi�n a la red m�s cercano y calcula la distancia en l�nea recta hasta ese punto. La suma de estas dos distancias da como resultado la longitud total de los cables de transmisi�n utilizados en el c�lculo de los costos de transmisi�n de la Tabla 3. Puede omitir los puntos de aterrizaje de la tabla e incluir solo los puntos de la red: en este caso, el modelo simplemente calcula la longitud total del cable de transmisi�n como la distancia en l�nea recta desde cada ubicaci�n potencial del parque e�lico hasta el punto de la red m�s cercano.

La opci�n de par�metro fijo especifica una distancia media hacia el interior a lo largo de toda la costa que representa la distancia prevista que los cables terrestres pueden tener que recorrer para llegar a una conexi�n a la red. Dado que los puntos de conexi�n a la red para las grandes explotaciones son muy oportunos y representan una parte relativamente peque�a de los costos de capital, no es poco realista modelizar la conexi�n a la red de esta manera en ausencia de un esquema detallado de conexi�n a la red. El par�metro incluido por defecto, 5,5 km, es la distancia media del cable terrestre del Reino Unido de la tabla de infraestructuras de transmisi�n anterior.

Adem�s del costo de enviar la energ�a a la costa, los parques e�licos tambi�n necesitan cables que conecten las turbinas entre s�, denominados cables del conjunto. Hemos calculado una relaci�n lineal simple entre los cables del conjunto y el n�mero de turbinas bas�ndonos en los datos que figuran a continuaci�n:

.. csv-table::

  :file: ./wind_energy/example_farms.csv

  :header-rows: 1

* Tabla 4. Cableado de la matriz.*

Los datos anteriores sugieren que se necesitan 0,91 km de cable por aerogenerador a un costo de 260.000 d�lares por km. Esto establece una relaci�n entre el cable de la instalaci�n y los aerogeneradores que permite calcular el  total del cable de la instalaci�n bas�ndose �nicamente en el n�mero de aerogeneradores del parque.

Otros costos
^^^^^^^^^^^^

Hay una serie de costos adicionales asociados a la fase de construcci�n, como los de desarrollo, ingenier�a, adquisici�n y regal�as. AWS Truewind (2010) estima que estos costos ascienden al 2% del total de los gastos de capital; Blanco (2009) indica que podr�an llegar al 8%. Adoptamos su m�todo de utilizar un coeficiente de costos de capital para calcular estos costos y utilizamos el valor medio del 5% como valor por defecto.

La instalaci�n de los cimientos, las turbinas y los equipos de transmisi�n (cables y subestaciones) constituye su propia categor�a de costos. Kaiser y Snyder (2012) adoptan una visi�n global de los costos de instalaci�n y constatan que estos representan aproximadamente el 20% de los gastos de capital en los parques e�licos marinos europeos. En consecuencia, este modelo trata los costos de instalaci�n como un porcentaje fijo de los costos totales de capital y utiliza el valor por defecto sugerido por Kaiser y Snyder (2012).

El desmantelamiento de la instalaci�n al final de su vida �til (:math:`t=T`) entra en el modelo de forma similar a los costos de instalaci�n, en el sentido de que es una fracci�n fija de los gastos de capital. Kaiser y Snyder (2012) sit�an este costo �nico entre el 2,6% y el 3,7% de los gastos iniciales (neto del valor de la chatarra) para el parque e�lico de Cape Wind utilizando un sofisticado modelo de desmantelamiento. El valor por defecto utilizado en este modelo es del 3,7%.

La mayor parte de los costos de un parque e�lico marino est�n relacionados con los costos de capital iniciales; sin embargo, tambi�n hay costos continuos relacionados con el mantenimiento y las operaciones (O&M). Boccard (2010) utiliza una metodolog�a coherente con el resto de nuestra modelizaci�n, calculando el costo anual de O&M como un porcentaje de los costos de capital originales, y sit�a los costos entre el 3 y el 3,5. El valor por defecto utilizado en este modelo es el 3,5%, y puede cambiarse junto con todos los dem�s costos de esta secci�n editando el archivo de par�metros globales `.csv`.

Precios de le energ�a
^^^^^^^^^^^^^^^^^^^^^

Este modelo est� dise�ado para aceptar un precio unitario fijo por un kilovatio hora (kWh) de energ�a durante la vida �til del parque e�lico O una tabla .csv en la que se puede especificar el precio/kWh para cada a�o durante la vida �til del parque e�lico. En algunos lugares, los operadores de parques e�licos reciben una tarifa subvencionada, conocida como tarifa de introducci�n, que les garantiza un precio fijo por su energ�a durante un cierto tiempo. En otros lugares, los operadores de parques e�licos deben negociar con los proveedores de energ�a y las comisiones de servicios p�blicos para conseguir un acuerdo de compra de energ�a. Se trata de contratos que especifican un precio unitario por la energ�a entregada y pueden presentar tarifas variables a lo largo del tiempo, lo que hace que la flexibilidad del cuadro de precios sea esencial.

Costo nivelado de la energ�a
----------------------------

El costo nivelado de la energ�a (https://en.wikipedia.org/wiki/Cost_of_electricity_by_source) (LCOE) es el precio unitario que habr�a que recibir por la energ�a para que el valor actual del proyecto fuera igual a cero. Como tal, da el precio/kWh m�s bajo que un promotor de parques e�licos podr�a recibir antes de considerar que el proyecto no merece la pena. El resultado que ofrece el modelo se expresa en t�rminos de moneda/kWh y se calcula como:

.. math:: LCOE = \frac{\sum^T_{t=1}\frac{O\&M\cdot CAPEX}{(1+i)^t}+\frac{D\cdot CAPEX}{(1+i)^T}+CAPEX}{\sum^T_{t=1}\frac{E_t}{(1+i)^t}}

Donde :math:`CAPEX` es el gasto de capital inicial, :math:`O\&M` es el par�metro de operaciones y gesti�n, :math:`D` es el par�metro de desmantelamiento, :math:`E_t` es la energ�a anual producida en kWh, :math:`i` es la tasa de descuento o WACC, y :math:`t` es el paso de tiempo anual, donde :math:`t={1\ldots T\}`.

Validaci�n
----------

Modelo de costos de capital
^^^^^^^^^^^^^^^^^^^^^^^

Dado que los gastos de capital representan la mayor proporci�n de los costos, y que gran parte de los costos auxiliares son fracciones fijas de los costos de capital, es de vital importancia validar nuestro modelo con los costos declarados de los parques e�licos marinos en todo el mundo. Para ello, recopilamos datos de https://www.4coffshore.com/ y https://www.lorc.dk/offshore-wind-farms-map/statistics sobre los costos de capital declarados y los dise�os de los parques e�licos que est�n en construcci�n o actualmente en funcionamiento. Limitamos la recopilaci�n de datos a los que emplean turbinas de 3,6 MW y 5,0 MW, para los que hemos proporcionado datos por defecto con el modelo InVEST. Los costos de capital declarados por 4Coffshore se inflaron a d�lares estadounidenses de 2012, utilizando la informaci�n de cierre financiero suministrada como base para el momento en que se recopil� la estimaci�n de costos. Para generar predicciones, el dise�o de cada finca se introdujo en el modelo InVEST utilizando los par�metros de costo por defecto adecuados para todos los componentes. La mayor�a de los parques tienen su propio equipo de transmisi�n el�ctrica, aunque algunos parques de aguas profundas est�n empezando a utilizar subestaciones marinas centralizadas que agregan la energ�a para su transporte desde varios parques. Para predecir los costos de transmisi�n el�ctrica de estas fincas, primero fue necesario estimar el costo de toda la subestaci�n en alta mar y luego atribuir un costo de capital prorrateado a cada finca en funci�n de su contribuci�n relativa a la capacidad de energ�a exportada. Por ejemplo, una subestaci�n en alta mar con una capacidad de exportaci�n de 800 MW que est� conectada a la finca A (200 MW) y a la finca B (600 MW) contribuir�a con el 25% de los costos de capital a la finca A y con el 75% a la finca B. Los resultados de nuestra validaci�n muestran una correlaci�n muy fuerte entre las predicciones y los costos de capital declarados para las turbinas de 3,6 MW y 5,0 MW utilizando los datos por defecto (v�ase la figura 5.6).

.. _project-costs-fig:

.. figure:: ./wind_energy/project_costs.png

   :align: center

   Costos de capital previsto versus costos de capital declarado.

Desde que este modelo se public� a principios de 2013, se ha contrastado con otros enfoques de modelizaci�n. A continuaci�n se se�alan como referencia:

1. El modelo InVEST se compar� con las estimaciones del Laboratorio Nacional de Energ�as Renovables (NREL) y de una empresa consultora en un informe de la Universidad de California, Santa B�rbara, que med�a el costo nivelado de la energ�a e�lica en las Bermudas. InVEST se situ� dentro de 3% de la estimaci�n del NREL y 12% de la estimaci�n realizada por la empresa consultora. http://trapdoor.bren.ucsb.edu/research/2014Group_Projects/documents/BermudaWind_Final_Report_2014-05-07.pdf

Limitaciones y simplificaciones
-------------------------------

Producci�n de energ�a
^^^^^^^^^^^^^^^^^^^^^

La calidad de los datos de input del viento determina la precisi�n de los resultados del modelo. Por lo tanto, quienes usan el modelo deben conocer la calidad de los datos de input del viento para interpretar correctamente los resultados del modelo. Los datos de input del viento por defecto son m�s apropiados para aplicaciones a escala global y regional con una resoluci�n espacial de 4 o 60 minutos.

La energ�a e�lica cosechada indica el promedio de la producci�n de energ�a para un per�odo determinado, basado en la curva de potencia de salida de un aerogenerador. Se puede tener en cuenta informaci�n adicional espec�fica de la tecnolog�a, como la disponibilidad del dispositivo, la eficiencia de la conversi�n de potencia y los factores direccionales, aplicando factores de ajuste a la producci�n de energ�a cosechada.

Valoraci�n de la energ�a
^^^^^^^^^^^^^^^^^^^^^^^^

Como demuestra la secci�n de validaci�n, el modelo y los datos por defecto predicen de forma fiable los costos de capital utilizando los insumos suministrados. Los ingresos est�n vinculados a la producci�n de energ�a y a un precio introducido por usted. Es probable que se puedan obtener proyecciones de costos m�s fiables a lo largo del espacio, considerando:

 * Crear una funci�n de costos de cimentaci�n que tenga en cuenta los mayores costos en aguas m�s profundas
 * Que los costos de instalaci�n var�an en funci�n de la geolog�a del fondo

Se trata de caracter�sticas que se est�n estudiando para posteriores actualizaciones del modelo en funci�n de la disponibilidad de datos.

El modelo es id�neo para producir resultados de valoraci�n de turbinas flotantes, pero no fue dise�ado espec�ficamente para esta tarea. Para obtener resultados, debe introducir valores razonables para las restricciones de profundidad y distancia, as� como costos de "cimentaci�n" equivalentes al costo unitario del equipo agregado necesario para hacer flotar una turbina. El modelo de costos de transmisi�n el�ctrica se elabor� utilizando tecnolog�as adecuadas para una profundidad de aproximadamente 60 metros y una distancia de 200 kil�metros de la costa, y probablemente producir� proyecciones de costos menos precisas fuera de esos l�mites.

Necesidades de datos
====================

- :investspec:`wind_energy workspace_dir`

- :investspec:`wind_energy results_suffix`

- :investspec:`wind_energy wind_data_path`

  Columnas:

  - :investspec:`wind_energy wind_data_path.columns.long`
  - :investspec:`wind_energy wind_data_path.columns.lati`
  - :investspec:`wind_energy wind_data_path.columns.lam`
  - :investspec:`wind_energy wind_data_path.columns.k`
  - :investspec:`wind_energy wind_data_path.columns.ref`

- :investspec:`wind_energy aoi_vector_path` Si se proporciona el ADI (�rea de inter�s), recortar� y proyectar� los resultados a la del ADI. Los resultados de distancia dependen del ADI y solo se calcular�n si se proporciona el ADI. Si se proporciona el ADI y se seleccionan los par�metros de distancia, entonces el ADI debe cubrir tambi�n una parte del pol�gono del terreno para calcular las distancias correctamente.

- :investspec:`wind_energy bathymetry_path` Debe cubrir al menos toda la extensi�n de la zona de inter�s y, si no se proporciona ning�n ADI, debe utilizarse el MDE global por defecto.

- :investspec:`wind_energy land_polygon_vector_path` Para que este input sea seleccionable, el ADI debe ser seleccionado. El ADI tambi�n debe cubrir una parte de este pol�gono terrestre para calcular correctamente las distancias. Este pol�gono costero, y el �rea cubierta por el ADI, forman la base para los c�lculos de distancia para la transmisi�n el�ctrica del parque e�lico.

- :investspec:`wind_energy global_wind_parameters_path` Los valores por defecto proporcionados en los datos de muestra se revisan en la secci�n **El modelo** de esta gu�a. Recomendamos que se considere cuidadosamente antes de cambiar estos valores. **Nota:** Los valores monetarios por defecto para estos par�metros (ver Tabla 3) est�n especificados en d�lares americanos. Si utiliza una moneda diferente para los dem�s par�metros de valoraci�n de este modelo ( costo del tipo de fundaci�n, etc.), deber� modificar tambi�n los par�metros globales de energ�a e�lica utilizando un tipo de conversi�n adecuado. 

  Columnas:

  - :investspec:`wind_energy global_wind_parameters_path.rows.air_density`
  - :investspec:`wind_energy global_wind_parameters_path.rows.exponent_power_curve`
  - :investspec:`wind_energy global_wind_parameters_path.rows.decommission_cost`
  - :investspec:`wind_energy global_wind_parameters_path.rows.operation_maintenance_cost`
  - :investspec:`wind_energy global_wind_parameters_path.rows.miscellaneous_capex_cost`
  - :investspec:`wind_energy global_wind_parameters_path.rows.installation_cost`
  - :investspec:`wind_energy global_wind_parameters_path.rows.infield_cable_length`
  - :investspec:`wind_energy global_wind_parameters_path.rows.infield_cable_cost`
  - :investspec:`wind_energy global_wind_parameters_path.rows.mw_coef_ac`
  - :investspec:`wind_energy global_wind_parameters_path.rows.mw_coef_dc`
  - :investspec:`wind_energy global_wind_parameters_path.rows.cable_coef_ac`
  - :investspec:`wind_energy global_wind_parameters_path.rows.cable_coef_dc`
  - :investspec:`wind_energy global_wind_parameters_path.rows.ac_dc_distance_break`
  - :investspec:`wind_energy global_wind_parameters_path.rows.time_period`
  - :investspec:`wind_energy global_wind_parameters_path.rows.carbon_coefficient`
  - :investspec:`wind_energy global_wind_parameters_path.rows.air_density_coefficient`
  - :investspec:`wind_energy global_wind_parameters_path.rows.loss_parameter`

Propiedades de la turbina
-------------------------

- :investspec:`wind_energy turbine_parameters_path` Los datos de muestra incluyen estos par�metros para dos opciones de modelo de turbina, 3,6 MW y 5,0 MW. Puede crear una nueva clase de turbina (o modificar las clases existentes) utilizando las convenciones de formato de archivo existentes y rellenando sus propios par�metros. Se recomienda no sobrescribir los archivos .csv existentes por defecto. Estos archivos se encuentran en el directorio `WindEnergy\input` de los datos de muestra de InVEST y se denominan

  * 3.6 MW: `3_6_turbine.csv`
  * 5.0 MW: `5_0_turbine.csv`

  Columnas:

  - :investspec:`wind_energy turbine_parameters_path.rows.hub_height`
  - :investspec:`wind_energy turbine_parameters_path.rows.cut_in_wspd`
  - :investspec:`wind_energy turbine_parameters_path.rows.rated_wspd`
  - :investspec:`wind_energy turbine_parameters_path.rows.cut_out_wspd`
  - :investspec:`wind_energy turbine_parameters_path.rows.turbine_rated_pwr`
  - :investspec:`wind_energy turbine_parameters_path.rows.turbine_cost`


- :investspec:`wind_energy number_of_turbines`
- :investspec:`wind_energy min_depth`
- :investspec:`wind_energy max_depth`
- :investspec:`wind_energy min_distance`
- :investspec:`wind_energy max_distance`

Valoraci�n
----------

- :investspec:`wind_energy valuation_container`

- :investspec:`wind_energy foundation_cost` El costo de una cimentaci�n depender� del tipo de cimiento seleccionado, que a su vez depende de diversos factores, como la profundidad y la elecci�n de la turbina. Se puede utilizar cualquier moneda, siempre que sea coherente con los diferentes datos de valoraci�n.

- :investspec:`wind_energy discount_rate` El tipo de descuento refleja las preferencias por los beneficios inmediatos frente a los futuros (por ejemplo, �preferir�a un individuo recibir 10 d�lares hoy o 10 d�lares dentro de cinco a�os?)

- :investspec:`wind_energy grid_points_path` Para los c�lculos se utiliza la distancia m�s corta entre los puntos respectivos.

  Columnas:

  - :investspec:`wind_energy grid_points_path.columns.id`
  - :investspec:`wind_energy grid_points_path.columns.type`
  - :investspec:`wind_energy grid_points_path.columns.lati`
  - :investspec:`wind_energy grid_points_path.columns.long`

  Ejemplo:

  .. csv-table::
    :file: ../invest-sample-data/WindEnergy/input/NE_sub_pts_modified.csv
    :header-rows: 1
    :widths: auto


- :investspec:`wind_energy avg_grid_distance`

- :investspec:`wind_energy price_table`

- :investspec:`wind_energy wind_schedule`

  Columnas:

  - :investspec:`wind_energy wind_schedule.columns.year`
  - :investspec:`wind_energy wind_schedule.columns.price` Se puede utilizar cualquier moneda, siempre que sea coherente con los diferentes datos de valoraci�n.

- :investspec:`wind_energy wind_price` Se puede utilizar cualquier moneda, siempre que sea coherente con los diferentes datos de valoraci�n.

- :investspec:`wind_energy rate_change`


Interpretaci�n de los resultados
================================
Todas las resoluciones resultantes se basan en la resoluci�n del r�ster. Cuando la resoluci�n del MDE supera la resoluci�n de las capas de datos e�licos, los valores de los p�xeles se determinan mediante interpolaci�n bilineal.

 * `carbon_emissions_tons.tif`: un archivo r�ster GeoTIFF que representa toneladas de emisiones de carbono compensadas para una finca construida centrada en un p�xel por a�o.

 * `density_W_per_m2.tif`: un archivo r�ster GeoTIFF que representa la densidad de potencia (W/m^2) centrada en un p�xel.

 * `example_size_and_orientation_of_a_possible_wind_farm.shp`: un shapefile de ESRI que representa el l�mite exterior de un parque e�lico de muestra. La posici�n de este pol�gono es aleatoria y pretende darle una idea de la escala del posible parque e�lico.

 * `harvested_energy_MWhr_per_yr.tif`: un archivo r�ster GeoTIFF que representa la energ�a anual cosechada de una finca centrada en ese p�xel.

 * `levelized_cost_price_per_kWh.tif`: un archivo r�ster GeoTIFF que representa el precio unitario de la energ�a que ser�a necesario para que el valor actual de la explotaci�n centrada en ese p�xel fuera igual a cero. Los valores se dan en la unidad de moneda utilizada como input del modelo.

 * `npv.tif`: un archivo r�ster GeoTIFF que representa el valor actual neto de una finca centrada en ese p�xel.

 * `wind_energy_points.shp`: un shapefile ESRI que resume los resultados anteriores para cada punto...


Fuentes de datos
================

Datos de la serie temporal del viento
-------------------------------------
El Servicio Meteorol�gico Nacional de la NOAA proporciona resultados de rean�lisis hindcast para series temporales de viento; https://polar.ncep.noaa.gov/. La resoluci�n espacial de los resultados del modelo oscila entre 4 y 60 minutos, dependiendo de los sistemas de cuadr�culas globales y regionales. Los resultados del modelo se han guardado en intervalos de 3 horas desde 1999 hasta el presente. Los resultados del modelo se han validado con datos de boyas oce�nicas en muchos lugares y proporcionan informaci�n de buena calidad sobre el viento.

Par�metros de las turbinas
--------------------------
LORC proporciona la informaci�n de los par�metros de los aerogeneradores marinos que funcionan actualmente en el mundo. https://www.lorc.dk/offshore-wind-farms-map/list?sortby=InstalledCapacity&sortby2=&sortorder=desc

Validaci�n
----------
Las fuentes de datos se citan en su mayor parte m�s arriba, excepto las cifras que se derivan de los comunicados de prensa. Los comunicados de prensa se encontraron mediante una b�squeda exhaustiva de palabras clave en Google sobre "energ�a e�lica marina" y diversas variantes de ese tema. Todos los costos se registraron e inflaron en su moneda original y se cambiaron a d�lares estadounidenses al tipo de cambio al contado del 30 de marzo de 2012.

Este fichero (https://www.dropbox.com/s/p4l36pbanl334c2/Wind_Sources.zip?dl=0) contiene un archivo de las fuentes citadas para los costos y una hoja de c�lculo que enlaza cada cifra de costo con el correspondiente comunicado de prensa, acta de conferencia, etc.

Referencias
===========

AWS Truewind. 2010. New York�s Offshore Wind Energy Development Potential in the Great Lakes. Feasibility Study for New York State Energy Research and Development Authority.

Blanco, M. 2009. The Economics of Wind Energy. Renewable and Sustainable Energy Reviews, 13, 1372-82. http://dx.doi.org/10.1016/j.rser.2008.09.004

Boccard, N. 2010. Economic Properties of Wind Power: A European Assessment. Energy Policy, 38, 3232-3244. http://dx.doi.org/10.1016/j.enpol.2009.07.033

Carbon Trust. 2008. Offshore Wind Power: Big Challenge, Big Opportunity. Report on behalf of the Government of the United Kingdom.

Celik, A. N. 2003. A statistical analysis of wind power density based on the Weibull and Rayleigh models at the southern of Turkey. Renewable Energy 29:509-604. http://dx.doi.org/10.1016/j.renene.2003.07.002

Elliott, D. L., C. G. Holladay, W. R. Barchet, H. P. Foote y W. F. Sandusky. 1986. Wind energy resource atlas of the United States. DOE/CH 10093-4. Solar Technical Information Program, Richland, Washington.

Global Wind Energy Council (GWEC). 2013. Global Wind Statistics, 2012. Accessed at: http://www.gwec.net/wp-content/uploads/2013/02/GWEC-PRstats-2012_english.pdf

Griffin, R., Buck, B. y Krause, G. 2015a. Private incentives for the emergence of co-production of offshore wind energy and mussel aquaculture. Aquaculture, 346, 80-89. http://dx.doi.org/10.1016/j.aquaculture.2014.10.035

Griffin, R., Chaumont, N., Denu, D., Guerry, A., Kim, C. y Ruckelshaus, M. 2015b. Incorporating the visibility of coastal energy infrastructure into multi-criteria siting decisions. Marine Policy, 62, 218-223. http://dx.doi.org/10.1016/j.marpol.2015.09.024

Hsu, S. A., E. A. Meindl y D. B. Gilhousen. 1994. Determining the power-law wind-profile exponent under near-neutral stability conditions at sea. Journal of applied meteorology 33:757-765. http://dx.doi.org/10.1175/1520-0450(1994)033%3C0757:DTPLWP%3E2.0.CO;2

Jacquemin, J., Butterworth, D., Garret, C., Baldock, N. y A. Henderson. 2011. Inventory of location specific wind energy cost. WP2
Report D2.2. Spatial deployment of offshore wind energy in Europe (Wind-Speed). Garrad Hassan & Partners Ltd. Supported by Intelligent Energy Europe.

Jafarian, M. y A. M. Ranjbar. 2010. Fuzzy modeling techniques and artificial neural networks to estimate annual energy output of a wind turbine. Renewable Energy 35:2008-2014. http://dx.doi.org/10.1016/j.renene.2010.02.001

Kaiser, M. y B. Snyder. 2012. Offshore wind capital cost estimation in the U.S. Outer Continental Shelf: A reference class approach. Marine Policy, 36, 1112-1122. http://dx.doi.org/10.1016/j.marpol.2012.02.001

Levitt, A., Kempton, W., Smith, A., Musial, W. y J. Firestone. 2011. Pricing offshore wind energy. Energy Policy, 39, 6408-6421. http://dx.doi.org/10.1016/j.enpol.2011.07.044

Lorc Knowledge. 2012. List of offshore wind farms. https://www.lorc.dk/offshore-wind-farms-map/list Accessed at December 31, 2012.

Manwell, J. F., J. G. Mcgowan y A. L. Rogers. 2009. Wind energy explained: Theory, design and application. John Wiley & Sons Ltd., West Sussex, Reino Unido.

National Oceanic and Atmospheric Administration. 1976. U. S. Standard Atmosphere. NOAA- S/T76-1562, Washington, DC.

Pallabazzer, R. 2003. Provisional estimation of the energy output of wind generators. Renewable Energy, 29, 413-420. http://dx.doi.org/10.1016/S0960-1481(03)00197-6

Samoteskul, K., Firestone, J., Corbett, J. y J. Callahan. 2014. Changing vessel routes could significantly reduce the cost of future offshore wind projects. Journal of Environmental Management, 141, 146-154. http://dx.doi.org/10.1016/j.jenvman.2014.03.026

UMaine. 2011. Maine deepwater offshore wind report. https://composites.umaine.edu/research/offshore-wind-report/
