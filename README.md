# Taller I — Desafío Strata

Proyecto desarrollado para la asignatura **Taller I** (Ingeniería en Inteligencia Artificial, UNSTA), en el marco de un caso propuesto por la empresa ficticia **Strata Analytics**. El desafío consistió en construir un pipeline de **DataOps** sobre AWS para procesar datos de clientes de una empresa de telecomunicaciones y obtener un set de KPIs de negocio, visualizados en un dashboard de Power BI.

> 📄 Informe grupal: [`docs/INFORME_TALLER_I.pdf`](docs/INFORME_TALLER_I.pdf)
> 
> 📄 Informe individual (Parte II — Dashboard): [`docs/TALLER_I-_PARTE_II-_Barrios.pdf`](docs/TALLER_I-_PARTE_II-_Barrios.pdf)
> 
> 📊 Dashboard: [`DASHBOARD-BARRIOS.pbix`](DASHBOARD-BARRIOS.pbix)

## Equipo (Parte I — pipeline DataOps)

- Barrios, Julieta Luján
- Pannuto, Leandro
- Urday, Nazareno
- Salguero Frías, Patricio
- Parache, Imanol
- Tartarini, Luciano

**Docente a cargo:** Ana Martínez Saucedo

> La **Parte II** (dashboard e interpretación de negocio) es una entrega individual propia (Julieta Luján Barrios).

## Descripción del proyecto

Strata Analytics solicitó, en representación de una empresa de telecomunicaciones (Telco), el análisis del comportamiento de sus clientes a partir de 4 fuentes de datos crudas en `.csv`:

- Información general de clientes (DNA), generada semanalmente.
- Eventos de recargas (semanas del 22 y 29 de abril).
- Eventos de compra de packs (15 al 18 de abril).
- Encuesta de satisfacción (feedback de clientes).

El objetivo fue construir, con servicios de **AWS**, una base de datos limpia y unificada que sirviera de input para el cálculo de los siguientes KPIs:

1. Porcentaje de clientes que recargaron por semana
2. Porcentaje de clientes que compraron un pack por semana
3. Monto promedio de recarga semanal
4. Porcentaje de detractores, neutros y promotores
5. NPS (Net Promoter Score)

## Arquitectura (AWS)

El pipeline de DataOps se implementó íntegramente sobre servicios de AWS:

| Servicio | Rol en el pipeline |
|---|---|
| **S3** | Almacenamiento de los datos crudos (`.csv`) organizados por fuente, y de los datos transformados en formato Parquet |
| **Glue** | ETL: eliminación de duplicados, filas/columnas nulas, selección de identificadores únicos y cruces entre fuentes |
| **Lambda** | Automatización del proceso: un trigger detecta cambios en el bucket de origen y dispara el ETL de Glue automáticamente |
| **Athena** | Consultas SQL sobre las tablas Parquet para calcular cada KPI |

**Flujo general:** Carga de `.csv` en S3 → Lambda detecta el cambio → Glue ejecuta el ETL y guarda el resultado en Parquet → Athena consulta las tablas y calcula los KPIs.

## Resultados obtenidos

| KPI | Valor |
|---|---|
| Total de clientes | 4.999 |
| % de recargas semanales | 19,81% |
| % de clientes que compraron un pack por semana | 20% |
| Monto promedio de recargas semanales | 7,6 |
| % de detractores | 34% |
| % de neutros | 26% |
| % de promotores | 40% |
| NPS | 6% |

El proceso completo (desde la carga de datos hasta la obtención de los KPIs) se ejecuta en aproximadamente 3 minutos.

## Dashboard (Power BI)

A partir de los KPIs calculados en Athena se construyó un dashboard en Power BI (`DASHBOARD-BARRIOS.pbix`) con tres vistas principales:

**Resumen general** — clientes totales, % de recargas, % de compra de packs y % de respuesta a la encuesta, junto con la evolución diaria de recargas y compras en las dos semanas relevadas.
<img width="1255" height="721" alt="Captura de pantalla 2024-11-14 222438" src="https://github.com/user-attachments/assets/3fd371df-4ab5-4466-b261-6d4f0e559346" />


**Recargas** — distribución de clientes que recargan, evolución diaria y montos de recarga más frecuentes.
<img width="1234" height="729" alt="Captura de pantalla 2024-11-14 222455" src="https://github.com/user-attachments/assets/ebe6ddd4-f094-457f-a67e-57d33b683d38" />


**Compras y NPS** — segmentación de clientes en promotores/neutros/detractores, cálculo del NPS y tipos de compra más frecuentes.
<img width="1236" height="711" alt="Captura de pantalla 2024-11-14 222505" src="https://github.com/user-attachments/assets/1ef3545d-21a3-4d8d-99d4-04d5a91b9533" />


### Lectura de negocio

- **Recargas y montos:** 34,49% de los clientes recarga semanalmente, con un monto promedio de 45,5. Los montos más frecuentes podrían orientar campañas para incentivar recargas mayores.
- **Compra de packs:** 40,11% de los clientes compra un pack por semana, lo que permite priorizar qué packs reforzar u ofertar.
- **Interacción del cliente:** solo el 7,74% responde la encuesta de satisfacción, una oportunidad de mejora en la recolección de feedback.
- **Segmentación NPS:** 40% promotores, 34% detractores, 26% neutros — insumo directo para diseñar campañas de fidelización o recuperación de clientes.

## Aprendizajes y desafíos

El equipo destacó como principales desafíos la falta de conocimiento inicial sobre los servicios de AWS, la falta de documentación clara sobre el contenido de las columnas de los `.csv`, y las dificultades iniciales con SQL y con el formato de los *targets* de Glue. Estos se resolvieron mediante reuniones semanales de sincronización y documentación conjunta del proceso.


## Tecnologías utilizadas

- AWS S3, Glue, Lambda, Athena
- Python (scripts de automatización en Lambda)
- SQL (consultas en Athena)
- Power BI (dashboard de KPIs)
