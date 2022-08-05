# PowerBI_Proyects
Proyectos orientados a la analitica de datos

---------------CASOS_COVID19_REGIONES_CHILE_2021-2022 (ENERO)----------------------

Tabla Confirmados_Acumulados_Comuna: Producto 1 Covid-19_std.csv

https://raw.githubusercontent.com/MinCiencia/Datos-COVID19/master/output/producto1/Covid-19_std.csv

Tabla TotalesAcumulados_Region: Producto 3 CasosTotalesCumulativo_std.csv

https://raw.githubusercontent.com/MinCiencia/Datos-COVID19/master/output/producto3/CasosTotalesCumulativo_std.csv

Tabla Totales_Categoria_Region: Producto 3 TotalesPorRegion_std.csv

https://raw.githubusercontent.com/MinCiencia/Datos-COVID19/master/output/producto3/TotalesPorRegion_std.csv

Tabla Totales_Nacionales : Producto 5 TotalesNacionales_std.csv

https://raw.githubusercontent.com/MinCiencia/Datos-COVID19/master/output/producto5/TotalesNacionales_std.csv

Totales Total_NuevosCumulativo_Region: Producto 13 CasosNuevosCumulativo_std.csv

https://raw.githubusercontent.com/MinCiencia/Datos-COVID19/master/output/producto13/CasosNuevosCumulativo_std.csv

Tabla Casos_ActivosPorComuna : Producto 19 CasosActivosPorComuna_std.csv

https://raw.githubusercontent.com/MinCiencia/Datos-COVID19/master/output/producto19/CasosActivosPorComuna_std.csv

Tabla Calendario:

Calendario = 
VAR MinYear = YEAR ( MIN('Confirmados_Acumulados_Comuna'[Fecha]) )
VAR MaxYear = YEAR ( TODAY() )
RETURN
ADDCOLUMNS (
    FILTER (
        CALENDARAUTO( ),
        AND ( YEAR ( [Date] ) >= MinYear, YEAR ( [Date] ) <= MaxYear )
    ),
    "Año",  YEAR ( [Date] ),
    "Mes_1", FORMAT ( [Date], "mmmm" ),
    "Mes", MONTH ( [Date] ),
    "Dia", DAY([Date]),
    "Semana", WEEKNUM([Date]),
    "Año-Mes", YEAR ( [Date] )&"-"&IF(MONTH ( [Date] )<10,0&MONTH ( [Date] ),MONTH ( [Date] ))  
    
Tabla de medidas:

Casos Confirmados = [Casos ConfirmadosAcum]-'Tabla Medidas'[Medida]

Casos ConfirmadosAcum = 
    CALCULATE(
       SUM('Confirmados_Acumulados_Comuna'[Casos confirmados]), 
        LASTDATE('Confirmados_Acumulados_Comuna'[Fecha]))

Confirmados por población = [Casos ConfirmadosAcum]/SUM(Comuna_GEO[Poblacion])

Medida = 
VAR SecondLast_ = 
CALCULATE (
    MAX ( 'Confirmados_Acumulados_Comuna'[Fecha] ),
    FILTER ( 'Confirmados_Acumulados_Comuna', 'Confirmados_Acumulados_Comuna'[Fecha] <> MAX ( 'Confirmados_Acumulados_Comuna'[Fecha] ) )
)
RETURN


 // Your measure V2. Best practice is  to not use table name with measures

    CALCULATE ( SUM ('Confirmados_Acumulados_Comuna'[Casos confirmados] ), 'Confirmados_Acumulados_Comuna'[Fecha] = SecondLast_ )
    
Titulo Meses = "Meses de "&SELECTEDVALUE(Calendario[Año])

Total_Nacional = 
    CALCULATE(
       SUM(Totales_Nacionales[Total]),
       LASTDATE(Totales_Nacionales[Fecha]))
        
Total_Nuevos_Reg_Acum = 
    CALCULATE(
       SUM(Total_NuevosCumulativo_Region[Total])/10, 
        LASTDATE(Total_NuevosCumulativo_Region[Fecha]))
        
Total_Region = 
    CALCULATE(
       SUM('Totales_Categoria_Region'[Total]), 
        LASTDATE(Totales_Categoria_Region[Fecha]))
        
Total_Region_Acum = 
    CALCULATE(
       SUM('TotalesAcumulados_Region'[Total])/10, 
        LASTDATE(TotalesAcumulados_Region[Fecha]))
        
 YTD = VAR fec_inicio = EDATE(MIN(Totales_Categoria_Region[Fecha]),-12)
       VAR fec_fin = EDATE(MAX(Totales_Categoria_Region[Fecha]),-12)
       RETURN CALCULATE('Tabla Medidas'[Total_Region],DATESBETWEEN(Calendario[Date],fec_inicio,fec_fin))

