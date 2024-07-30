---
toc: false
---

<div class="hero">
  <h1>Exploración de datos Covius</h1>
  <h2>Físico-química del agua superficial <br> Monitoreo de piezómetros <br> Censo completo de monos</h2>
</div>

<!-- Configuración de gráficas e inputs-->

```js
// datos
const monos = FileAttachment("data/monos_time.csv").csv({ typed: true });
const piezometros = FileAttachment("data/piezometros_time.csv").csv({
    typed: true,
});
const qa = FileAttachment("data/qa_superficial_time.csv").csv({ typed: true });
```

```js
// Variables de Input
const parametroSuperficialInput = Inputs.select(
    [
        "ph",
        "salinidad",
        "temperatura",
        "conductividad",
        "solidostotalesdisueltosstd",
    ],
    { label: "Seleccionar parámetro" },
);
const parametro_superficial = Generators.input(parametroSuperficialInput);

const agrLugarInput = Inputs.toggle({ label: "Separar lugares", value: true });
const agr_lugar = Generators.input(agrLugarInput);

const parametroPiezometroInput = Inputs.select(
    [
        "ph",
        "salinidad",
        "temperatura",
        "conductividad",
        "conductividad2",
        "agua_intersticial",
        "agua_subterranea",
        "solidos_totales_disueltos",
    ],
    { label: "Seleccionar parámetro" },
);
const parametro_piezometro = Generators.input(parametroPiezometroInput);

const agruparInput = Inputs.toggle({ label: "Separar por Grupo", value: true });
const agrupar = Generators.input(agruparInput);
```

```js
// Configuración de gráficas
// Gráfica Físico-Química
function qa_timeseries(data, { width } = {}) {
    return Plot.plot({
        title: "Físico-química del agua superficial",
        color: {
            legend: true,
        },
        marks: [
            Plot.ruleY([0]),
            Plot.lineY(data, {
                x: "fecha_muestra",
                y: parametro_superficial,
                stroke: agr_lugar ? "lugar" : "grey",
            }),
        ],
    });
}
// Gráfica piezometros
function qaPiezometros(data, { width } = {}) {
    return Plot.plot({
        color: {
            legend: true,
        },
        marks: [
            Plot.ruleY([0]),
            Plot.lineY(data, {
                x: "hora_muestra",
                y: parametro_piezometro,
                stroke: agrupar ? "grupo" : "grey",
            }),
        ],
    });
}

// Gráfica Monos 1
function monosActividad(data, { width } = {}) {
    return Plot.plot({
        title: "Censo de monos por categoría y actividad",
        height: 500,
        marginBottom: 40,
        color: {
            scheme: "Viridis",
            legend: true,
        },
        marks: [
            Plot.barY(data, {
                x: "categoria_mono",
                y: "conteo",
                fill: "actividad",
                sort: "actividad",
            }),
            Plot.ruleY([0]),
        ],
    });
}

// Gráfica de monos 2
function avistamientosArea(data, { width } = {}) {
    return Plot.plot({
      height: 500,
      marginBottom: 40,
      color: {
        scheme: "Accent",
        legend: true
      },
      marks: [
        Plot.areaY(data, {x: "hora_avistamiento", y: "conteo", fill:"categoria_mono", sort: "hora_avistamiento"}),
        Plot.ruleY([0]),
      ]
    })
}
```

# Introducción

Las siguientes gráficas son una muestra de los datos obtenidos de la API de EarthRanger para la instancia de Covius. Las visualizaciones exploran diversos análisis cuantitativos de los datos.

# Calidad del agua superficial

<div class="grid grid-cols-2" style="grid-auto-rows: 504px;">
    <div class="card">
            <span>${parametroSuperficialInput}</span>
            <span>
            ${agrLugarInput}
            </span>
            ${resize((width) => qa_timeseries(qa, {width}))}
    </div>
    <div class="card">
        <h2>Muestra de datos</h2>
        <br>
        ${Inputs.table(qa)}
    </div>

</div>

# Monitoreo de piezómetros

La data está muy sucia.

<div class="grid grid-cols-2" style="grid-auto-rows: 504px;">
    <div class="card">
        <h2>Muestra de datos</h2>
        <br>
        ${Inputs.table(piezometros)}
    </div>
    <div class="card">
            <span>
                ${parametroPiezometroInput}
            </span>
            <span>
                ${agruparInput}
            </span>
            ${resize((width) => qaPiezometros(piezometros, {width}))}
    </div>
</div>

# Censo completo de monos

<div class="grid grid-cols-2" style="grid-auto-rows: 504px;">
    <div class="card">
        ${resize((width) => monosActividad(monos, {width}))}
    </div>
    <div class="card">
        ${resize((width) => avistamientosArea(monos, {width}))}
    </div>

</div>

<style>

.hero {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: var(--sans-serif);
  margin: 4rem 0 8rem;
  text-wrap: balance;
  text-align: center;
}

.hero h1 {
  margin: 1rem 0;
  padding: 1rem 0;
  max-width: none;
  font-size: 14vw;
  font-weight: 900;
  line-height: 1;
  background: linear-gradient(30deg, var(--theme-foreground-focus), currentColor);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.hero h2 {
  margin: 0;
  max-width: 34em;
  font-size: 20px;
  font-style: initial;
  font-weight: 500;
  line-height: 1.5;
  color: var(--theme-foreground-muted);
}

@media (min-width: 640px) {
  .hero h1 {
    font-size: 90px;
  }
}

</style>
