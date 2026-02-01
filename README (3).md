# Pricing Experimentation with Synthetic Control  
## Estimación causal del impacto de pricing y elasticidad implícita

Este repositorio contiene un análisis de **inferencia causal aplicada a pricing**, cuyo objetivo es responder a la siguiente pregunta:

> **¿Cuánto cambió el consumo de reservas en los mercados donde se aumentó el precio (+5%, +10%, +15%), comparado con lo que habría ocurrido si no se hubiera cambiado el precio?**

Dado que el pricing es una **policy a nivel de mercado** y no un feature aislado a nivel usuario, el análisis evita A/B testing clásico y utiliza **Synthetic Control Method (SCM)** para construir el contrafactual relevante.

---

## 🧠 Enfoque metodológico

### Unidad de análisis
- **Mercado × día**
- El mercado se define por **origen del usuario**, lo que reduce significativamente problemas de interferencia entre unidades.

### Treatment
- **Policy de pricing (Z)** con tres brazos:  
  `+5%`, `+10%`, `+15%` vs `control`
- El **precio observado (P)** es un **mediador**, no el treatment.

### Outcome primario
- **Bookings diarios** (conteo discreto)

### Método causal
- **Synthetic Control Method (SCM)** a nivel de mercado
- Cada mercado tratado se compara contra un **contrafactual sintético** construido a partir de mercados control
- El análisis se realiza **mercado a mercado**, evitando supuestos TWFE o DiD clásico

---

## 🔗 Marco causal (resumen)

El diseño sigue el siguiente esquema conceptual:

- `Z (policy)` → `P (precio efectivo)` → `Y (bookings)`
- Factores contextuales (estacionalidad, GDP, etc.) afectan a `Y`
- No se ajusta por variables post-treatment (CR, churn, LTV)
- La identificación se apoya en:
  - buen ajuste pre-treatment del SCM
  - ausencia razonable de interferencia entre mercados

---

## 📊 Estimando principal

El estimando central es el **ATT por mercado y brazo**:

\[
ATT = Y_{observado} - Y_{sintético}
\]

Interpretado como el **efecto causal total de la policy de pricing** sobre las reservas, en el corto plazo.

---

## 📈 Elasticidad implícita (reduced-form)

A partir del ATT se construye una **elasticidad implícita causal**, definida como:

\[
Elasticidad \approx \frac{\%\Delta Q_{causal}}{\%\Delta P_{realizado}}
\]

donde:

- `%ΔQ_causal` se calcula respecto al **contrafactual sintético**
- `%ΔP_realizado` se mide sobre el **precio observado pre vs post**, no el nominal del brazo

### ⚠️ Importante
- Esta **no es una elasticidad estructural clásica**
- Es una **elasticidad implícita, local y reduced-form**
- Resume la respuesta de la demanda ante una **policy discreta de pricing**, no ante variaciones marginales continuas

---

## 🧪 Diagnósticos y validación

El análisis incluye:

- Verificación del **first stage** (la policy mueve el precio)
- Evaluación del ajuste pre-treatment mediante **RMSPE**
- Comparación visual entre serie observada y sintética
- Interpretación cautelosa de resultados con peor pre-fit

Resultados con mayor RMSPE deben interpretarse con mayor prudencia.

## 🚫 Qué NO hace este análisis

- No estima elasticidades estructurales de demanda
- No utiliza propensity scores ni DiD
- No ajusta por variables afectadas por el treatment
- No asume linealidad ni continuidad entre brazos

---

## 🧭 Interpretación para toma de decisiones

Los resultados permiten:

- Cuantificar el **trade-off volumen vs precio**
- Comparar políticas discretas de pricing (+5 / +10 / +15)
- Entender **orden de magnitud** de la sensibilidad de la demanda
- Informar decisiones de pricing con **contrafactual explícito**

La elasticidad **no dice qué hacer**, sino **cómo responde la demanda** ante cambios de precio.

---

## 📌 Limitaciones

- Resultados válidos para mercados similares a los analizados
- Posible interferencia residual si el origen del usuario no está perfectamente medido
- Elasticidades locales; extrapolación requiere nuevo análisis

---

## 📬 Contacto

Este repositorio forma parte de un trabajo más amplio sobre **pricing experimentation e inferencia causal aplicada a negocio digital**.

Cualquier comentario técnico o metodológico es bienvenido.

