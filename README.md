# Pricing Experimentation: un enfoque causal
## Estimación del impacto de pricing en negocio digital y análisis de la elasticidad

Este repositorio contiene un análisis de **inferencia causal aplicada a pricing**, cuyo objetivo es responder a la siguiente pregunta:

> **¿Cuánto cambió el consumo de reservas en los mercados donde se aumentó el precio (+5%, +10%, +15%), comparado con lo que habría ocurrido si no se hubiera cambiado el precio?**

Dado que el pricing es una **policy a nivel de mercado** y no una feature aislada nivel usuario, usamos **Synthetic Control Method (SCM)** para construir un contrafactual.

---

## 🧠 Enfoque metodológico

### Unidad de análisis
- **Mercado × día**
- El mercado se define por **origen del usuario**, es decir, no se aplica un análisis que tiene en cuenta destino. Simplifica el análisis y reduce significativamente problemas de interferencia entre usuarios, mercados y demás. Un problema recurrente en este tipo de casos es que usuarios puedan comprar en distintos mercados o que puedan usar incluso VPN, pero se asume que es un porcentaje reducido de usuarios.

### Treatment
- **Policy de pricing (Z)** con tres casos:  
  `+5%`, `+10%`, `+15%` vs `control`
- El **precio observado (P)** es un **mediador**, no el treatment. Es importante esto porque nosotros no accionamos sobre pricing, sino sobre la decisión que tomamos sobre ello.

### Outcome primario
- **Bookings diarios** (conteo discreto). No se utiliza revenue porque Miguel Hernán & Robbins recomiendan utilizar estimandos comportamentales y desde ellos inferir otros como revenue. En este caso me parece lo más prudente.

### Método causal
- **Synthetic Control Method (SCM)** a nivel de mercado
- Cada mercado tratado se compara contra un **contrafactual sintético** construido a partir de mercados control
- El análisis se realiza **mercado a mercado**.
---

## 🔗 Marco teórico

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

Interpretado como el **efecto causal total de la policy de pricing** sobre las reservas.

---

## 📈 Elasticidad implícita (reduced-form)

A partir del ATT se construye una **elasticidad implícita causal**, definida como:

\[
Elasticidad \approx \frac{\%\Delta Q_{causal}}{\%\Delta P_{realizado}}
\]

donde:

- `%ΔQ_causal` se calcula respecto al **contrafactual sintético**
- `%ΔP_realizado` se mide sobre el **precio observado pre vs post**, no el nominal de cada caso.

### ⚠️ Importante
- Esta **no es una elasticidad estructural clásica**
- Es una **elasticidad implícita, local y simplificada**
- Resume la respuesta de la demanda ante una **policy de pricing**.

---

## 🧪 Diagnósticos y validación

El análisis incluye:

- Verificación del **first stage** (la policy mueve el precio)
- Evaluación del ajuste pre-treatment mediante **RMSPE**
- Comparación visual entre serie observada y sintética
- Interpretación de resultados

## 🚫 Qué NO hace este análisis

- No estima elasticidades estructurales de demanda
- No asume linealidad ni interferencia entre los 3 mercados tratados
- Ser una extrapolación al resto de mercados (requeriría otro análisis adicional).

---

## 🧭 Interpretación para toma de decisiones

Los resultados permiten:

- Cuantificar el **trade-off volumen vs precio**, es decir, si quizás generar menores reservas puede salir a cuenta en materia de revenue.
- Comparar políticas de pricing (+5 / +10 / +15)
- Entender **orden de magnitud** de la sensibilidad de la demanda por mercado

La elasticidad **no dice qué hacer**, sino **cómo responde la demanda** ante cambios de precio.

---

## 📌 Limitaciones

- Resultados válidos para mercados similares a los analizados
- Posible interferencia residual si el origen del usuario no está perfectamente trackeado
- Pueden existir Elasticidades locales.

---

## 📬 Contacto

Cualquier comentario técnico o metodológico es bienvenido.

