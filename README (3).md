# 🎯 Pricing Experimentation: Un Enfoque Causal


[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/)
[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 📋 Tabla de Contenidos

- [El Problema](#-el-problema)
- [La Pregunta Causal](#-la-pregunta-causal)
- [Metodología](#-metodología)
- [Estructura del Proyecto](#-estructura-del-proyecto)
- [Quick Start](#-quick-start)
- [Resultados Clave](#-resultados-clave)
- [Fundamentos Teóricos](#-fundamentos-teóricos)
- [Limitaciones y Caveats](#-limitaciones-y-caveats)
- [Referencias](#-referencias)

---

## 🔥 El Problema

### ¿Por qué Pricing NO admite A/B Testing Clásico?

En un A/B test tradicional, aleatorizamos **usuarios individuales** a diferentes variantes. Esto **NO funciona para pricing** por razones fundamentales:

| Problema | Descripción | Consecuencia |
|----------|-------------|--------------|
| **Interferencia (SUTVA)** | Los usuarios comparan precios entre sí | El outcome de un usuario depende del tratamiento de otros |
| **Riesgo Reputacional** | Discriminación de precios percibida | Daño a la marca, posibles problemas legales |
| **El precio es una policy** | Afecta la percepción del mercado completo | No es un feature aislado que solo afecta al usuario |

### La Solución: Quasi-Experimentos a Nivel de Mercado

Aleatorizamos **mercados geográficos completos**, no usuarios:
- Cada mercado recibe un único precio
- Comparamos mercados tratados vs. mercados control
- Usamos **Difference-in-Differences (DiD)** para estimar efectos causales

---

## ❓ La Pregunta Causal

> *"¿Cuánto cambió el consumo de reservas en los mercados donde aumentamos el precio +5%/+10%/+15%, comparado con lo que hubiera ocurrido si no hubiéramos cambiado el precio?"*

### El DAG Mínimo

```
    Z (Policy: asignación a brazo)
    │
    ▼
    P (Precio efectivo)
    │
    ▼
    Y (Bookings)
```

**Importante**: El **treatment** es la **policy (Z)**, no el precio (P). El precio es un **mediador**.

### Estimand: ATT por Brazo

Estimamos el **Average Treatment Effect on the Treated (ATT)**:

$$ATT = E[Y(1) - Y(0) | D = 1]$$

Un ATT separado para cada brazo (+5%, +10%, +15%), todos contra el mismo grupo control.

---

## 🔬 Metodología

### Diseño del Experimento

| Componente | Implementación |
|------------|----------------|
| **Unidad de aleatorización** | Mercado geográfico |
| **Brazos de tratamiento** | Control, +5%, +10%, +15% |
| **Staggered rollout** | Waves 1, 2, 3 en diferentes días |
| **Outcome primario** | Bookings diarios |
| **Período de análisis** | ~150 días (pre + post) |

### Asunciones de Identificación

| Asunción | Significado | Validación |
|----------|-------------|------------|
| **Parallel Trends** | Sin tratamiento, tratados y controles evolucionarían igual | Event study pre-treatment |
| **Positivity** | Todos los mercados podrían ser asignados a cualquier brazo | Overlap en covariables |
| **Consistency** | El tratamiento está bien definido | Documentación del +X% |
| **SUTVA** | No hay interferencia entre mercados | Separación geográfica |

### Métodos de Estimación

1. **Difference-in-Differences (DiD) 2x2**
   - Estimación separada por brazo
   - Errores estándar clustered por mercado
   
2. **Synthetic Control** (complementario)
   - Construcción de contrafactual sintético
   - Útil para narrativa por mercado individual

3. **De ATT a Elasticidad**
   ```
   ε = %ΔQ / %ΔP
   ```

---

## 📁 Estructura del Proyecto

```
pricing-experimentation/
│
├── 📓 pricing_experiment_causal_analysis.ipynb  # Notebook principal
├── 📊 pricing_experiment_realistic.csv          # Dataset
├── 📖 README.md                                  # Este archivo
│
└── Secciones del Notebook:
    ├── 1. El Problema (por qué no A/B clásico)
    ├── 2. La Pregunta Causal (DAG, ATT, asunciones)
    ├── 3. El Diseño (múltiples brazos, staggered)
    ├── 4. Los Datos (outcome, confusores, bad controls)
    ├── 5. El Análisis (DiD, Synthetic Control, elasticidad)
    ├── 6. La Validación (placebos, sensibilidad)
    └── 7. La Decisión (interpretación, trade-offs)
```

---

## 🚀 Quick Start

### Opción 1: Google Colab (Recomendado)

1. Abre el notebook en Colab
2. Ejecuta la celda de instalación
3. Sube `pricing_experiment_realistic.csv` cuando se solicite
4. Ejecuta todas las celdas secuencialmente

### Opción 2: Local

```bash
# Clonar o descargar el proyecto
pip install pandas numpy matplotlib seaborn scipy statsmodels scikit-learn

# Ejecutar en Jupyter
jupyter notebook pricing_experiment_causal_analysis.ipynb
```

### Dependencias

```python
pandas>=1.3.0
numpy>=1.21.0
matplotlib>=3.4.0
seaborn>=0.11.0
scipy>=1.7.0
statsmodels>=0.12.0
```

---

## 📈 Resultados Clave

### ATT por Brazo de Tratamiento

| Brazo | ATT (bookings/día) | Efecto (%) | IC 95% | Significativo |
|-------|-------------------|------------|--------|---------------|
| +5%   | +20.8 | +5.1% | [-3.7%, 13.9%] | ❌ No |
| +10%  | -22.8 | -11.1% | [-18.6%, -3.6%] | ✅ Sí*** |
| +15%  | -24.3 | -6.7% | [-20.5%, 7.1%] | ❌ No |

### Elasticidad Estimada

```
ε ≈ -0.18  (Demanda INELÁSTICA)
```

**Interpretación**: Por cada 1% de aumento en precio, la demanda cae ~0.2%.

### Implicaciones de Negocio

- ✅ Demanda **inelástica** → incrementos moderados de precio aumentan revenue
- ⚠️ Solo el brazo +10% es estadísticamente significativo
- 📊 Necesarios más datos para +5% y +15%
- 🔍 Monitorear LTV y churn a largo plazo

---

## 📚 Fundamentos Teóricos

### ¿Por qué Difference-in-Differences?

DiD explota la estructura de **panel** (mercados × tiempo) para identificar efectos causales:

$$Y_{it} = \alpha_i + \lambda_t + \beta \cdot D_{it} + \epsilon_{it}$$

Donde:
- $\alpha_i$ = efecto fijo de mercado (captura diferencias permanentes)
- $\lambda_t$ = efecto fijo de tiempo (captura shocks comunes)
- $D_{it}$ = indicador de tratamiento
- $\beta$ = **ATT** (lo que queremos estimar)

### El Problema del Staggered Adoption

Con **staggered rollout** (tratamiento en diferentes momentos), el TWFE clásico puede dar pesos negativos. Soluciones:

1. **Callaway-Sant'Anna**: Estima ATT(g,t) separadamente
2. **2x2 DiD por brazo**: Nuestra implementación
3. **Definir `post_common`**: Mismo corte temporal para tratados y controles

### De ATT a Elasticidad

```
Elasticidad = (ATT / baseline_bookings) / price_change_pct
            = %ΔQ / %ΔP
```

Tres puntos (+5%, +10%, +15%) permiten:
- Verificar linealidad de la respuesta
- Detectar no-linealidades (¿hay un "cliff"?)
- Interpolar para precio óptimo

---

## ⚠️ Limitaciones y Caveats

### Validez Interna

| Amenaza | Mitigación | Estado |
|---------|------------|--------|
| Parallel trends violado | Event study pre-treatment | ✅ Verificado |
| Anticipación del tratamiento | Diseño sin anuncios previos | ✅ Por diseño |
| Interferencia entre mercados | Separación geográfica | ⚠️ Asumido |

### Validez Externa (Transportability)

La elasticidad estimada aplica a:
- ✅ Mercados con características similares
- ✅ Incrementos en rango +5% a +15%
- ✅ Horizonte ~4 semanas

**NO** necesariamente aplica a:
- ❌ Mercados con GDP muy diferente
- ❌ Incrementos >15% o <5%
- ❌ Efectos a largo plazo (3-6 meses)
- ❌ Temporadas diferentes (alta vs. baja)

### Corto vs. Largo Plazo

```
Corto plazo (este análisis):     Efecto directo sobre demanda
Largo plazo (no medido):         Efectos en lealtad, marca, LTV
```

**Recomendación**: Seguimiento a 3-6 meses para efectos completos.

---

## 🔧 Detalles Técnicos

### Errores Estándar Clustered

```python
model.fit(cov_type='cluster', cov_kwds={'groups': df['market']})
```

Fundamental para:
- Correlación serial dentro de mercados
- Heterocedasticidad entre mercados

### Variable `post_common`

**Error común**: Usar la columna `post` del CSV directamente.

**Problema**: `post=1` solo para tratados después de SU tratamiento. Los controles siempre tienen `post=0`.

**Solución**: Crear `post_common` que marca el período post para TODOS basándose en cuándo empezó el tratamiento:

```python
treatment_start = df[df['treatment_arm'] == arm]['treatment_start_day'].iloc[0]
df_sub['post_common'] = (df_sub['day_index'] >= treatment_start).astype(int)
```

---

## 📖 Referencias

### Papers Fundamentales

1. **Hernán, M. A., & Robins, J. M.** (2020). *Causal Inference: What If*. Chapman & Hall/CRC.
   - [Libro gratuito](https://www.hsph.harvard.edu/miguel-hernan/causal-inference-book/)

2. **Callaway, B., & Sant'Anna, P. H.** (2021). Difference-in-differences with multiple time periods. *Journal of Econometrics*, 225(2), 200-230.
   - [Paper](https://www.sciencedirect.com/science/article/abs/pii/S0304407620303948)

3. **Goodman-Bacon, A.** (2021). Difference-in-differences with variation in treatment timing. *Journal of Econometrics*, 225(2), 254-277.

### Recursos Online

4. **Facure, M.** (2022). *Causal Inference for the Brave and True*.
   - [https://matheusfacure.github.io/python-causality-handbook/](https://matheusfacure.github.io/python-causality-handbook/)

5. **Cunningham, S.** (2021). *Causal Inference: The Mixtape*.
   - [https://mixtape.scunning.com/](https://mixtape.scunning.com/)

### Synthetic Control

6. **Abadie, A., Diamond, A., & Hainmueller, J.** (2010). Synthetic control methods for comparative case studies. *JASA*, 105(490), 493-505.

---

## 🤝 Contribuir

¿Encontraste un error? ¿Tienes una mejora?

1. Fork del repositorio
2. Crea una rama (`git checkout -b feature/mejora`)
3. Commit (`git commit -am 'Añade mejora'`)
4. Push (`git push origin feature/mejora`)
5. Abre un Pull Request

---

## 📄 Licencia

MIT License - ver [LICENSE](LICENSE) para detalles.

---

<p align="center">
  <i>"Correlation is not causation, but with the right design, we can get closer to the truth."</i>
</p>

<p align="center">
  Creado con ❤️ siguiendo principios de inferencia causal rigurosa
</p>
