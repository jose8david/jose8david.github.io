---
layout: post
title: Cómo funciona un condensador en los osciladores
---
{: style="color:gray; font-size: 80%; text-align: center;"}

A pesar de los distintos tipos de condensadores que encontramos en el mercado (cerámicos, electrolíticos, con mayor capacidad de almacenaje o tolerancia) todos ellos tienen como principio fundamental la capacidad para almacenar energía eléctrica.

Sin embargo, dependiendo del lugar del circuito en el que se encuentren, su comportamiento será distinto. Hay condensadores que funcionan como un balancín para permitir la conexión entre etapas (cargándose y descargándose continuamente), y otros que actúan como filtros de señal, reduciendo su amplitud.

En esta entrada estudiaremos el comportamiento de los condensadores cuando actúan como un balancín, generando periodos de carga y descarga, y que permitirán la construcción de osciladores, capaces de generar ondas (voltaje alterno), a partir de una fuente de voltaje continua. 

Cuando estamos ante un condensador que actúa como balancín, verás que la corriente pasa por él y genera voltajes variables en los componentes cercanos.

Vamos a aprender a calcular la intensidad pico que recorre este circuito:

![Circuito para el cálculo de la intensidad pico](../img/cap14.png)

1. Calculamos la reactancia capacitiva
   
$$
X_c = \frac{1}{2\pi f C} = \frac{1}{2\pi * 10 * 1\times 10^{-6}}
$$
$$X_c= \frac{1}{6,28\times 10^{-5}}$$
$$X_c = 15,92 K \varOmega$$
   

2. Calculamos la impedancia total del circuito

$$Z_t = R_1+jX_c+R_2$$
$$Z_t= 1.000 \varOmega + j1.592 \varOmega + 1.000 \varOmega $$

3. Calculamos la magnitud de la impendancia total
    $$ |Z_t| = \sqrt{(R_1+R_2)^2 + X_c^2} $$
    $$ |Z_t| = \sqrt{(1K \varOmega+1K \varOmega)^2 + (15,92K \varOmega)^2} $$
    $$ |Z_t| = 16 K\varOmega $$

4. Calculamos la intensidad pico usando la Ley de Ohm

$$I_p = \frac{V_c}{|Z_t|}$$
$$I_p= \frac{5}{16\times 10^{3}}$$
$$I_p= 312 \mu A$$

En este circuito la corriente se retrasa con respecto a la tensión en 90º. Para hacer el cálculo se utiliza la siguiente fórmula:

$$ Desfase(grado) = \frac{\varDelta_t}{T}$$

Donde 
$$ \varDelta_t $$ 
es igual a la diferencia en segundos que tarda en llegar una señal al punto 0 del eje X frente a la otra.

Donde 
$$T$$
es igual al periodo de la señal, es decir, el tiempo que tarda en completar un ciclo una señal. El periodo es igual a 

$$Periodo = \frac{1}{frecuencia}$$


<html>
<head>
    <title>Desfase entre señales</title>
    <script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
</head>
<body>

<div id="calculadora-desfase">
<h3>Calcular el grado de desfase entre señales</h3>

<div id="periodo">
<label for="period">Periodo de la señal</label>
<input type="number" id="period" placeholder="Introduce el periodo de la señal. Este valor es la inversa de la frecuencia" required>
<select id="period-unit">
<option value="1">s</option>
<option value="0.001">ms</option>
<option value="0.000001">us</option>
</select>
</div>

<div id="señal1">
<label for="señal1">Tiempo de corte en señal 1</label>
<input type="number" id="señal1" placeholder="Introduce el tiempo que tarda la señal 1 en cortar el eje X" required>
<select id="señal1-unit">
<option value="1">s</option>
<option value="0.001">ms</option>
<option value="0.000001">us</option>
</select>
</div>

<div id="señal2">

<label for="señal2">Tiempo de corte en señal 2</label>
<input type="number" id="señal2" placeholder="Introduce el tiempo que tarda la señal 2 en cortar el eje X" required>
<select id="señal2-unit">
<option value="1">s</option>
<option value="0.001">ms</option>
<option value="0.000001">us</option>
</select>

</div>

<button onclick="calculadoraFase()">Calcular el desfase</button>

<p id="resultado"></p>
</div>

    <script>
    function calculadoraFase(){

        const periodo = parseFloat(document.getElementById("period").value)*parseFloat(document.getElementById("period-unit").value);
        const señal1 = parseFloat(document.getElementById("señal1").value)*parseFloat(document.getElementById("señal1-unit").value);
        const señal2 = parseFloat(document.getElementById("señal2").value)*parseFloat(document.getElementById("señal2-unit").value);

        const desfase = Math.abs(señal2-señal1);
        const desfaseGrados = (desfase/periodo)*360;

        document.getElementById("resultado").textContent = `El desfase es de ${desfaseGrados.toFixed(2)} grados`
    }     

    </script>
</body>

</html>

<html>

<head>

</head>

<body>

<div id=container>
<h3>Calcular el voltaje del circuito a lo largo del tiempo</h3>

<div id="input">
<label for="time">Indica el tiempo</label>
<input type="number" id="time" placeholder="Introduce el tiempo" required>
</div>

<div id="boton">
<button onclick="calculadoraVoltaje()">Calcular los voltajes</button>
</div>

<div id="resultado">
<p> Voltaje en el nodo A <span id="voltajeA"></span> V</p>
<p> Voltaje en el nodo B <span id="voltajeB"></span> V</p>
<p> Voltaje en el nodo C <span id="voltajeC"></span> V</p>
</div>

<canvas id="voltajeGrafico"></canvas>

</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>
    const Vp= 5;
    const freq = 100;
    const R1= 1000;
    const R2= 1000;
    const C1= 10e-6;

    let voltageChart;

    //Función para calcular el voltaje

    function calculadoraVoltaje(){
        const tiempo = parseFloat(document.getElementById("time").value);
        //Calculo fuente en el tiempo dado
        const omega= 2 * Math.PI * freq; // Velocidad angular
        const Vs = Vp* Math.sin(omega * tiempo);
        //Voltaje en el nodo A
        const VA = Vs;
        //Voltaje en el nodo B
        const XC = 1/(omega*C1); //Reactancia del condensador
        const ZB = Math.sqrt(R1 ** 2 + XC ** 2); // Impedancia en el nodo B
        const VB = VA * (XC/ZB);
        // Voltaje en el nodo C
        const VC = VB * (R2 / (R2+XC));
        // Mostrar valores en pantalla
        document.getElementById("voltajeA").textContent = VA.toFixed(2);
        document.getElementById("voltajeB").textContent = VB.toFixed(2);
        document.getElementById("voltajeC").textContent = VC.toFixed(2);
        //Generar gráfico
        updateChart(time,VA, VB, VC);

        }

        const ctx = document.getElementById('voltajeGrafico').getContext('2d');
        const voltajeGrafico = new Chart(ctx, {
            type:'line',
            data: {
                labels: [],
                datasets: [
                    {
                        label: 'Voltaje en nodo A',
                        borderColor:'red',
                        data: [],
                        fill: false
                    },
                    {
                        label: 'Voltaje en nodo B',
                        borderColor:'blue',
                        data: [],
                        fill: false
                    },
                    {
                        label: 'Voltaje en nodo C',
                        borderColor:'green',
                        data: [],
                        fill: false
                    }
                ]
            },
        options: {
            responsive: true,
            scales : {
                x: {
                    type:'linear',
                    title:{
                        display: true,
                        text: 'Tiempo (s)'
                    }
                },
                y :{
                    title: {
                        display: true,
                        text: 'Voltaje (V)'
                    }
                }
            }
        }

        });
    function updateChart(time, VA, VB, VC) {
        voltageChart.data.labels.push(time);
        voltageChart.data.dataset[0].data.push(VA);
        voltageChart.data.dataset[1].data.push(VB);
        voltageChart.data.dataset[2].data.push(VC);
        voltageChart.update();
    }

    
</script>
</body>

</html>