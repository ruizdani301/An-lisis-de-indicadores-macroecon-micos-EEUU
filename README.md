config.py	Leer .env (API key, DB URL, ventanas por frecuencia)
models/	Definición de tablas con SQLAlchemy ORM
schemas/	Validación y serialización con Pydantic
clients/	Solo HTTP: llamar a FRED y devolver JSON validado
repositories/	Solo SQL: INSERT/UPDATE/UPSERT/consultas
services/	Lógica: ventana, comparación, orquestación
main.py / scripts/	Punto de entrada (CLI, cron, scheduler)# An-lisis-de-indicadores-macroecon-micos-EEUU
